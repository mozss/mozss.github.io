---
title: nio的实践
tags:
  - java-nio
categories:
  - code
  - code-java
abbrlink: 62876
date: 2020-11-01 09:26:30
---

<!--more-->

# Java NIO 实践

Java NIO（New IO）是Java SE1.4后新的IO API。NIO提供了与传统的IO API（Java IO）相比，更快速，更灵活的IO操作。

## NIO与IO的差异

### IO的缺陷

IO的读写是阻塞操作，即在读写数据的过程中，线程会被一直阻塞，无法进行其他操作。因此，IO的吞吐量受限，无法支持高并发的情况。

### NIO的优点

NIO使用了非阻塞IO来提高系统的IO性能。非阻塞IO的特点是在读写数据时，线程不会一直被阻塞，而是可以进行其他操作。这样，你可以使用一个线程来处理多个IO连接，提高了系统的吞吐量。

NIO的另一个优点是它的IO操作是面向缓冲区的。这就意味着数据是从缓冲区读取和写入的，而不是一个字节一个字节的读写。这减少了内存的拷贝次数，从而提高了系统的IO性能。

## NIO的核心组件

### 缓冲区 Buffer

在NIO中，所有的数据都是从缓冲区读取或写入的。Buffer是一个字节数组或其他基本类型数组的容器。

Buffer的主要属性有：

- capacity: 缓冲区的容量
- position: 下一个要读或写的位置
- limit: 缓冲区的限制
- mark: 缓冲区的标记

Buffer的常用类型有：ByteBuffer、CharBuffer、ShortBuffer、IntBuffer、LongBuffer、FloatBuffer、DoubleBuffer。

### 通道 Channel

在NIO中，所有的IO操作都是通过通道(Channel)来完成的。通道是一个双向的数据传输通道，可以完成读和写的操作。

通道的主要实现类有：FileChannel、DatagramChannel、SocketChannel和ServerSocketChannel。

### 选择器 Selector

Selector是NIO的多路复用器，它可以监控多个通道的IO事件，例如连接、读取和写入事件，使得一个线程可以同时处理多个IO连接。

## NIO的实践

在本次实践中，将使用NIO实现一个简单的服务端和客户端通信的功能。

### 服务端

服务端的功能是监听客户端的请求，并返回当前系统时间给客户端。

```java
public class NIOServer {

    public static void main(String[] args) throws IOException {
        // 打开ServerSocketChannel
        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        // 绑定到指定端口
        serverSocketChannel.socket().bind(new InetSocketAddress(8080));
        // 配置为非阻塞模式
        serverSocketChannel.configureBlocking(false);

        // 创建一个Selector
        Selector selector = Selector.open();
        // 将ServerSocketChannel注册到Selector，并监听连接事件
        serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);

        while (true) {
            // 阻塞等待IO事件
            selector.select();

            // 获取所有发生的IO事件
            Set<SelectionKey> selectionKeys = selector.selectedKeys();
            Iterator<SelectionKey> iterator = selectionKeys.iterator();

            while (iterator.hasNext()) {
                SelectionKey key = iterator.next();
                // 将已处理的事件移除
                iterator.remove();

                if (key.isAcceptable()) {
                    // 有新连接事件
                    ServerSocketChannel channel = (ServerSocketChannel) key.channel();
                    // 获取SocketChannel通道
                    SocketChannel socketChannel = channel.accept();
                    // 配置为非阻塞模式
                    socketChannel.configureBlocking(false);
                    // 将SocketChannel注册到Selector，并监听读取事件
                    socketChannel.register(selector, SelectionKey.OP_READ);
                } else if (key.isReadable()) {
                    // 有可读事件
                    SocketChannel socketChannel = (SocketChannel) key.channel();
                    ByteBuffer buffer = ByteBuffer.allocate(1024);
                    // 读取数据到缓冲区
                    socketChannel.read(buffer);

                    buffer.flip();// 切换为读取模式
                    byte[] bytes = buffer.array();
                    String request = new String(bytes).trim();
                    System.out.printf("request: %s\n", request);

                    // 处理请求
                    byte[] responseBytes = new Date().toString().getBytes();
                    ByteBuffer responseBuffer = ByteBuffer.wrap(responseBytes);
                    // 返回响应数据
                    socketChannel.write(responseBuffer);
                }
            }
        }
    }
}
```

### 客户端

客户端的功能是连接服务端，并向服务端发送请求，获取服务器返回的时间信息。

```java
public class NIOClient {

    public static void main(String[] args) throws IOException {
        SocketChannel socketChannel = SocketChannel.open();
        socketChannel.connect(new InetSocketAddress("localhost", 8080));
        socketChannel.configureBlocking(false);

        ByteBuffer buffer = ByteBuffer.wrap("Hello, server!".getBytes());
        // 向服务端发送请求
        socketChannel.write(buffer);
        System.out.println("Send request to server success.");

        ByteBuffer responseBuffer = ByteBuffer.allocate(1024);
        // 读取服务端的响应数据
        socketChannel.read(responseBuffer);

        responseBuffer.flip();// 切换为读取模式
        byte[] bytes = responseBuffer.array();
        String response = new String(bytes).trim();

        System.out.println("Server response: " + response);

        socketChannel.close();
    }
}
```

## 总结

通过这次实践，我们学会了如何使用Java NIO实现一个简单的服务端和客户端通信的功能。NIO的非阻塞IO模式和多路复用机制使得它能够支持更高效的IO操作，适用于高并发的系统场景。