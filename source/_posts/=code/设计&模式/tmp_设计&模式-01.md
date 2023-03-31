---
title: 责任链
tags:
  - java
categories:
  - code
  - code-java
abbrlink: 62876
date: 2019-11-01 01:16:20
---

<!--more-->

人类的责任感和责任心是非常重要的，有时候我们需要把一些任务交给另外一个人完成，但是我们不能确定是哪一个人能够承担这个任务。那么，我们就需要使用“责任链模式”（Chain of Responsibility Pattern）来解决这个问题。

责任链模式是一种行为型模式，它为多个对象（接收者）提供了解决请求的方法，但是客户端只需要知道第一个对象，它会自动把请求传递给下一个对象，直到有一个对象能够处理请求为止。这个过程就像是一条链，所以称之为责任链模式。

我们来看一个例子，假设我们需要处理用户在网站上的注册请求。首先，我们需要验证用户的用户名和密码是否符合要求。如果通过验证，就要提交给下一个处理者来检查用户的邮箱和电话号码是否正确。如果还是通过了，就会提交给下一个处理者检查用户的公司和职位信息是否完整。最后，如果全部通过了，就把用户信息保存到数据库中。

下面是责任链模式的Java代码：

```java
// 抽象处理者，定义处理请求的接口
public abstract class Handler {
    protected Handler successor; // 维持一个后继对象

    public void setSuccessor(Handler successor) {
        this.successor = successor;
    }

    public abstract void handleRequest(User user); // 处理请求的抽象方法
}

// 具体处理者1，处理用户名和密码验证请求
public class UserNameAndPasswordHandler extends Handler {
    @Override
    public void handleRequest(User user) {
        if (user.getName() != null && user.getPassword() != null) {
            System.out.println("用户名和密码验证通过");
            if (successor != null) {
                successor.handleRequest(user); // 传递给下一个处理者
            }
        } else {
            System.out.println("用户名或密码未填写");
        }
    }
}

// 具体处理者2，处理用户邮箱和电话验证请求
public class EmailAndPhoneHandler extends Handler {
    @Override
    public void handleRequest(User user) {
        if (user.getEmail() != null && user.getPhone() != null) {
            System.out.println("用户邮箱和电话验证通过");
            if (successor != null) {
                successor.handleRequest(user); // 传递给下一个处理者
            }
        } else {
            System.out.println("用户邮箱或电话未填写");
        }
    }
}

// 具体处理者3，处理用户公司和职位信息验证请求
public class CompanyAndPositionHandler extends Handler {
    @Override
    public void handleRequest(User user) {
        if (user.getCompany() != null && user.getPosition() != null) {
            System.out.println("用户公司和职位信息验证通过");
            // 不需要传递给下一个处理者了，直接保存到数据库
            System.out.println("用户信息已保存到数据库");
        } else {
            System.out.println("用户公司或职位信息未填写");
        }
    }
}

// 用户类
public class User {
    private String name;
    private String password;
    private String email;
    private String phone;
    private String company;
    private String position;

    // 省略getter和setter方法
}

// 客户端类
public class Client {
    public static void main(String[] args) {
        // 创建处理者对象
        Handler handler1 = new UserNameAndPasswordHandler();
        Handler handler2 = new EmailAndPhoneHandler();
        Handler handler3 = new CompanyAndPositionHandler();

        // 构建责任链
        handler1.setSuccessor(handler2);
        handler2.setSuccessor(handler3);

        // 创建用户对象
        User user = new User();
        user.setName("Tom");
        user.setPassword("123456");
        user.setEmail("tom@abc.com");
        user.setPhone("12345678901");
        user.setCompany("ABC公司");
        user.setPosition("程序员");

        // 处理请求
        handler1.handleRequest(user);
    }
}
```

小编觉得，责任链模式其实类似于我们生活中的“小环节串连起来变成大环节”，让我们在面对各种任务和问题时更加游刃有余和从容不迫。希望大家能够掌握这种使用广泛的设计模式，用好责任感和责任心，成为一个更好的人。

