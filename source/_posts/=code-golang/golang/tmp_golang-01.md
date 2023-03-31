---
title: golang的使用
tags:
  - golang
categories:
  - code
  - code-golang
abbrlink: 62876
date: 2022-06-10 01:16:20
---
[![ppnXW4g.png](https://s1.ax1x.com/2023/03/10/ppnXW4g.png)](https://imgse.com/i/ppnXW4g)

<!--more-->

# Golang学习技巧

---

## 编写规范

感觉这条总是出现在各类编程语言技巧文章里，但是 ~大部分~ 所有都非常实用啊！
遵循规范+好的习惯，让代码可读性强、易于维护、扩展性好。以下是go中几个经典的规范：

### 符号命名

- 私有变量或函数：以小写字母开头，单词之间使用下划线 _ 连接
- 公有变量或函数：以大写字母开头，单词之间使用驼峰命名法

### 代码结构

- 将package和所在文件命名一致（名称要能体现功能）
- import时将标准库放在前面，第三方库放在后面
- init()函数必要时可以使用

在编写代码时遵守命名规范、代码结构规范，可以提高可读性、可维护性、易扩展性。

## Golang使用技巧

### 指针、值传递

go中可以传递指针，也可以传递值（int、string等）。
如果是传递指针的话，被调用的函数可以在原变量上修改，否则不行。如：

```go
func main() {
    var num int = 0
    fmt.Printf("%d", add_num(&num))
}

func add_num(num *int) int {
    *num = 1
    return *num
}
```
这样在main函数中就会打印出1了。

### defer关键字

go中有一个非常优秀的关键字: defer。这个关键字可以“注册”一个函数，等当前函数执行完毕后再去执行。
常常用于解决资源占用释放问题，比如是打开文件后后忘记关闭等问题，代码如下：

```go
func foo(filename string) error {
    f, err := os.Open(filename)
    if err != nil { return err }

    // 在函数执行完毕后关闭文件，无论函数结果如何都会执行
    defer f.Close()
    // ... 此处省略其他逻辑处理
    return nil
}
```

### 并发处理

go极其擅长并发处理，这是它设计之初就考虑到的。
goroutine是go的轻量级线程，可以用来并发执行程序。
示例代码如下：
```go
func main() {
    go do_job() // 新开一个线程执行函数，不阻塞主函数
    // ... 此处省略其他逻辑处理
}

func do_job() {
    // ... 此处省略一些逻辑处理
}
```
这样main函数会继续执行其他操作，而do_job则会在另一线程中并发执行。

### 反射

反射是了解go程序的一种方法。它的作用是在运行时检查程序的结构、变量、接口等。
以下是一个简单的反射示例：

```go
func main() {
    var str string = "hello"
    var value reflect.Value = reflect.ValueOf(str)
    fmt.Printf("type: %v, kind:%v, value:%v", value.Type(), value.Kind(), value.Interface())
}
```

在这个例子中，可以通过`reflect.Value`的函数获得变量的类型、种类、值（字符串类型的“hello”）等相关信息。

---

