---
title: 代理
tags:
  - java
categories:
  - code
  - code-java
abbrlink: 62876
date: 2019-11-07 01:16:20
---

<!--more-->
# 代理模式：当你无法自己处理事情时，找个代理

众所周知，程序里面很多时候需要处理一些繁琐的事情，比如访问一个远程服务、打印一张大图等等，这些任务可能占用大量的内存和时间，导致程序出现卡顿甚至崩溃的情况。如果你是一个懒人，你会选择怎么做？

没错，找个代理人！代理人可以替你完成这些事情，你只需要告诉他你的需求，让他去完成就好了。在程序里也是一样，我们可以使用代理模式来处理这些繁琐的事情。

## 代理模式是什么？

代理模式是一种结构型设计模式，它允许我们为其他对象提供一个代理，以控制对这个对象的访问。代理对象通常充当客户端和目标对象之间的中介，它可以隐藏目标对象的复杂性并且可以在不改变目标对象的情况下，为客户端添加额外的功能。

## 代理模式怎么用？

我们先来看一个例子：假设你需要访问一个远程服务获取一些数据，但是这个服务需要认证才能使用。你不想把认证信息暴露给客户端，也不想让客户端直接访问服务，怎么办？

首先，我们定义一个接口`Service`，用来让代理和目标对象实现：

```java
public interface Service {
    void getData();
}
```

然后，我们定义一个目标对象实现这个接口，用来访问远程服务：

```java
public class RealService implements Service {
    private String authCode;

    public RealService(String authCode) {
        this.authCode = authCode;
    }

    @Override
    public void getData() {
        // 访问远程服务获取数据
        System.out.println("获取数据");
    }
}
```

接下来，我们定义一个代理类实现这个接口，并在构造函数中传入目标对象和认证信息。代理类的`getData()`方法里面先进行身份认证，然后再调用目标对象的`getData()`方法获取数据：

```java
public class ProxyService implements Service {
    private final Service realService;
    private final String authCode;

    public ProxyService(Service realService, String authCode) {
        this.realService = realService;
        this.authCode = authCode;
    }

    @Override
    public void getData() {
        // 进行身份认证
        System.out.println("进行身份认证");

        // 调用目标对象的方法获取数据
        realService.getData();
    }
}
```

最后，我们就可以在客户端中创建代理对象，而不是直接访问远程服务：

```java
public class Client {
    public static void main(String[] args) {
        // 创建代理对象
        Service service = new ProxyService(new RealService("auth"), "auth");

        // 使用代理对象获取数据
        service.getData();
    }
}
```

这样，我们就利用代理模式来实现了目标对象的访问控制，客户端无需知道目标对象的实现细节和认证信息，而且可以在代理对象中添加额外的功能，比如缓存数据、限流等。

## 总结

代理模式是一种非常常用的设计模式，它能够让我们在不改变目标对象的情况下，对目标对象进行访问控制和功能扩展。在实际开发中，我们可以使用代理模式来优化程序的性能和安全性，减少系统的负担，提高用户体验，让程序变得更加健壮。