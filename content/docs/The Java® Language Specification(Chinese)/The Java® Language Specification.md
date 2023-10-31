---
title: "The Java® Language Specification"
description: ""
summary: ""
date: 2023-09-07T16:13:18+02:00
lastmod: 2023-09-07T16:13:18+02:00
draft: false
images: []
menu:
  docs:
    parent: ""
    identifier: "java-ee514306871548sd6e68dea3359133ad"
weight: 900
toc: true
---



## 一、简介

## 1.1. 规范的组织

Java® 编程语言是一种 通用的、并发的、基于类的、面向对象的语言。Java 编程语言是**强类型**和**静态类型**

- 强类型：强类型语言是指在编程时要求严格定义数据类型，不允许不同数据类型之间的隐式类型转换

```java
int num = 42;
String text = 42; // 这里会报错，需要进行类型转换
```

- 静态类型：静态类型语言是指在编译时进行类型检查，变量的数据类型必须在声明时明确定义，编译器可以根据这些类型信息进行类型检查，从而提前捕获潜在的类型错误。

```java
num = 42; // 这里会报错，需要定义类型
text = "Hello world"; // 这里会报错，需要定义类型
```

>***简单理解：***
>
> 强类型意味着不同类型的变量无法进行隐式转换
>
> 静态类型意味着需要变量定义类型才能使用



## 1.2. 示例程序

创建 oneAndOne 文件：

```java
public class oneAndOne {
    public static void main(String[] args) {
        for (int i = 0; i < args.length; i++)
            System.out.print(i == 0 ? args[i] : " " + args[i]);
        System.out.println();
    }
}

```

执行命令：

```
javac oneAndOne.java
java oneAndOne  Hello, world.
```

输出：

```java
Hello, world.
```



## 1.3. 符号

1. **引用Java SE平台API**：文本提到，在整个规范中，会引用Java SE平台API中的类和接口。当使用单个标识符（例如“N”）引用类或接口时，意图是引用`java.lang`包中名为“N”的类或接口。这是Java核心类的默认包。
2. **规范名称**：当引用来自`java.lang`之外的包中的类或接口时，规范使用规范名称，这是一个包括包名的完全限定名称。例如，它可能引用一个类为`com.example.MyClass`而不仅仅是“MyClass”。

> ***简单理解：***
>
> 当在Java规范中使用单个标识符引用类或接口时，通常是指这个类或接口来自Java核心包的默认包，也就是`java.lang`包。
>
> 如果要引用来自其他包的类或接口，规范要求您提供完全限定名称，即包括包名的类或接口名称。这是为了确保唯一性，因为不同的包可能会包含具有相同名称的类或接口。





2.语法
2.1. 上下文无关语法
2.2. 词汇语法
2.3. 句法语法
2.4. 语法符号



