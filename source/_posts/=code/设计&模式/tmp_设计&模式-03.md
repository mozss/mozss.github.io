---
title: 策略
tags:
  - java
categories:
  - code
  - code-java
abbrlink: 62876
date: 2019-12-13 09:16:20
---

<!--more-->

# 策略模式：简单而有效的解决方案

策略模式是软件开发中最常用的模式之一，它可以帮助开发者分离算法，并使其可以在运行时互换。

Java开发者可以使用策略模式来轻松实现复杂的业务逻辑。例如，一个应用可以根据用户输入的金额来决定选择何种支付方式，这就可以用策略模式来实现：

```java
//定义支付策略的抽象类
public abstract class PaymentStrategy {
    public abstract void pay(int amount);
}

//实现支付策略的具体类
public class CreditCardStrategy extends PaymentStrategy {
    public void pay(int amount) {
        System.out.println("使用信用卡支付" + amount + "元");
    }
}

public class CashStrategy extends PaymentStrategy {
    public void pay(int amount) {
        System.out.println("使用现金支付" + amount + "元");
    }
}

//定义一个类来控制这一过程
public class PaymentContext {
    private PaymentStrategy paymentStrategy;
    
    public PaymentContext(PaymentStrategy paymentStrategy) {
        this.paymentStrategy = paymentStrategy;
    }
    
    public void pay(int amount) {
        paymentStrategy.pay(amount);
    }
}

//使用策略模式
public class Main {
 
    public static void main(String[] args) {
        PaymentContext paymentContext = null;
        int amount = 1000;
        //选择使用信用卡支付
        paymentContext = new PaymentContext(new CreditCardStrategy());
        paymentContext.pay(amount);
 
        //选择使用现金支付
        paymentContext = new PaymentContext(new CashStrategy());
        paymentContext.pay(amount);
    }
}
```

从上面的代码可以看出，策略模式是一种简单而有效的解决方案，它能够使代码变得容易维护，因为算法的变化不会影响到已有的代码。