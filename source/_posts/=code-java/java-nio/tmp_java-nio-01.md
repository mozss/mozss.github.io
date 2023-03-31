---
title: nio的入门
tags:
  - java-nio
categories:
  - code
  - code-java
abbrlink: 62876
date: 2020-11-01 01:36:08
---

<!--more-->

# Java NIO 入门指南

Java NIO (Non-blocking I/O) 是 Java 平台中的一种 I/O 模型，允许使用非阻塞 I/O 方式进行高效的 I/O 操作。与传统的 I/O 模型相比，Java NIO 提供了更加底层的控制，更灵活的选择器和更高效的缓冲区管理，从而实现更高效的 I/O 操作。

## Java NIO 的核心组件

Java NIO 的核心组件包括以下几个部分：

- 缓冲区 (Buffer): NIO 中的所有 I/O 操作都通过缓冲区进行，缓冲区是对数据对象的封装。

- 通道 (Channel): NIO 中数据的读写操作是通过通道进行的，和传统的流不同，通道是可以双向的。

- 选择器 (Selector): 选择器是用于检测通道是否准备好读或写的 I/O 操作。多个通道可以注册到同一个选择器中，因此只需要一个线程就可以处理多个通道。

## NIO 缓冲区

在 NIO 中所有数据的读写操作都是通过缓冲区来完成的。缓冲区有以下几种类型：

- ByteBuffer: 字节缓冲区

- CharBuffer: 字符缓冲区

- ShortBuffer: 短整型缓冲区

- IntBuffer: 整型缓冲区

- LongBuffer: 长整型缓冲区

- FloatBuffer: 浮点型缓冲区

- DoubleBuffer: 双精度型缓冲区

### 创建缓冲区

在 NIO 中缓冲区的创建一般有两种方式：

```java
// 创建一个 ByteBuffer 缓冲区，默认容量为 1024 字节
ByteBuffer buffer = ByteBuffer.allocate(1024);

// 创建一个 ByteBuffer 缓冲区，容量为 1024 字节，限制为 512 字节
ByteBuffer buffer2 = ByteBuffer.allocate(1024).limit(512);
```

### 缓冲区的属性

在 NIO 中缓冲区的属性有以下几种：

- Capacity: 缓冲区的容量

- Position: 缓冲区当前的位置，下一个要读或写的位置

- Limit: 缓冲区的限制，不能读写超过这个位置

- Mark: 缓冲区的标记，用于记录当前位置

### 缓冲区的读写操作

在 NIO 中缓冲区的读写操作都是通过方法来实现的，常见的操作有：

```java
// 写入数据到缓冲区
buffer.put((byte) 1);
buffer.putInt(10);

// 从缓冲区读取数据
byte b = buffer.get();
int i = buffer.getInt();
```

### 缓冲区的切换和复制

缓冲区之间的切换和复制是非常常见的操作，可以使用以下方法实现：

```java
// 切换到读模式
buffer.flip();

// 切换到写模式
buffer.clear();

// 切换到读模式并标记当前位置
buffer.mark();

// 重置到标记位置
buffer.reset();

// 复制原缓冲区
buffer.copy();

// 复制子缓冲区
buffer.slice();
```

## NIO 通道

在 NIO 中数据的读写操作都是通过通道来完成的。通道是一个双向的数据通路，可以以单独的 I/O 操作来读取和写入数据。

### 创建通道

在 NIO 中可以通过以下几种方式来创建通道：

```java
// 创建一个文件输入通道
FileChannel inChannel = new FileInputStream("path/to/file").getChannel();

// 创建一个文件输出通道
FileChannel outChannel = new FileOutputStream("path/to/file").getChannel();

// 创建一个网络输入通道
SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1", 8080));

// 创建一个网络输出通道
SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1", 8080));
```

### 通道的读写操作

在 NIO 中数据的读写操作都是通过通道来完成的，通道的读写操作一般使用缓冲区来进行：

```java
// 写入数据到通道
channel.write(ByteBuffer.wrap("Hello, World!".getBytes()));

// 从通道读取数据
ByteBuffer buffer = ByteBuffer.allocate(1024);
int bytesRead = channel.read(buffer);
```

### 通道的类型

在 NIO 中通道的类型分为以下几种：

- FileChannel: 文件通道，用于读写文件数据

- DatagramChannel: 数据报通道，用于 UDP 连接

- SocketChannel: 套接字通道，用于 TCP 连接

- ServerSocketChannel: 服务套接字通道，用于监听客户端连接

## NIO 选择器

在 NIO 中选择器是用于检测通道是否准备好读或写的 I/O 操作。选择器允许将多个通道注册到同一个选择器中，同一个线程就可以同时处理多个通道的 I/O 操作，从而提高 I/O 操作的效率。

### 创建选择器

在 NIO 中可以通过以下方法来创建选择器：

```java
Selector selector = Selector.open();
```

### 注册通道

在 NIO 中可以通过以下方法将通道注册到选择器中：

```java
channel.register(selector, SelectionKey.OP_READ);
```

### 选择器的工作方式

选择器一般使用循环来进行工作：

```java
// 阻塞等待一个或多个通道准备好读或写
int select = selector.select();

// 获取选择器中已经准备好的通道
Set<SelectionKey> selectedKeys = selector.selectedKeys();

for (SelectionKey key : selectedKeys) {
    if (key.isReadable()) {
        // 处理通道的读操作
    } else if (key.isWritable()) {
        // 处理通道的写操作
    }
}
```

## 总结

Java NIO 提供了底层的 I/O 操作，允许使用非阻塞的方式进行高效的数据读写。熟练掌握 Java NIO 可以大大提高代码的性能和效率，建议对 Java NIO 进行深入学习并应用到实际项目中。