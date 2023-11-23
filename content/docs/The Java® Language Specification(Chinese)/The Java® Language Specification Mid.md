---
title: "The Java® Language Specification Mid"
description: ""
summary: ""
date: 2023-09-07T16:13:18+02:00
lastmod: 2023-09-07T16:13:18+02:00
draft: false
images: []
menu:
  docs:
    parent: ""
    identifier: "java-ee514306871548sd6e68deg33593ad"
weight: 900
toc: true
---





## 6. 命名

1. **Declared Entity（声明的实体）：** 这是指在程序中声明的各种实体，如包、类、接口、成员（类、接口、字段或方法）、类型参数、形式参数、异常参数或局部变量。
2. **Scope（作用域）：** 每个引入名称的声明都有一个作用域，即在程序文本中可以通过简单名称引用该声明实体的部分。作用域规定了在哪些部分可以使用特定的名称。
3. **Simple Name和Qualified Name（简单名称和限定名称）：** 简单名称由单个标识符组成，而限定名称由由"."符号分隔的一系列标识符组成。限定名称用于引用包或引用类型的成员。
4. **Access Control（访问控制）：** 访问控制是指在类、接口、方法或字段声明中指定的规则，用于控制何时允许访问成员。访问控制和作用域是不同的概念。访问控制规定了通过限定名称访问声明实体时的可见范围。
5. **Fully Qualified and Canonical Names（完全限定和规范名称）：** 完全限定名是指包含了实体的所有限定信息的名称，而规范名称则是指用于唯一标识实体的简化形式的名称。
6. **Name Resolution（名称解析）：** 在确定名称的含义时，上下文是用于消除具有相同名称的包、类型、变量和方法之间歧义的关键因素





### 6.1 声明

**声明介绍了程序中的实体，并包括一个标识符（identifier）**，用于通过名称引用该实体。标识符在引入类、接口或类型参数时有一些约束，以避免与特定上下文关键字冲突。

**声明的实体包括：**

- 模块（Module）: 在模块声明中引入。
- 包（Package）: 在包声明中引入。
- 导入的类或接口：在单类型导入或导入类型的通配符声明中引入。
- 导入的静态成员：在静态导入或导入静态成员的通配符声明中引入。
- 类、枚举、记录的声明：通过普通类声明、枚举声明或记录声明引入。
- 接口的声明：通过普通接口声明或注解接口声明引入。
- 泛型类型参数：在泛型类、接口、方法或构造方法的声明中引入。
- 引用类型的成员：成员类、成员接口、字段、方法等。
- 方法、构造方法：通过普通方法或构造方法声明引入。



**非泛型上下文：** 指在特定情境下，泛型信息不重要，可以将引用C作为类或接口C。例如，在模块声明、导入声明、构造方法声明等情况下。

**非泛型上下文的例子：**

- 在模块声明的 `uses` 或 `provides` 指令中
- 在单一类型导入声明中
- 在单个静态导入声明的左侧
- 在静态按需导入的左侧声明
- 在类或接口声明的子句中
- 在构造函数声明的左侧
- 在注释中签名后
- 在类文本中的左侧
- 在限定表达式中的左侧
- 在限定的超类字段中的左侧
- 在限定方法调用中的左侧
- 在方法引用的左侧
- 在后缀表达式或 `try-with-resources` 语句中的限定表达式名称中
- 在方法或构造函数的子句中
- 在异常参数声明



**Naming Conventions（命名约定）：**

- **包名和模块名：**
  - 建议使用唯一的包名，通过逆转互联网域名形成。
  - 模块名应该与其主要导出包的名称相对应。
- **类和接口名：**
  - 类名应该是描述性的名词，使用混合大小写。
  - 接口名应该短而描述性，也使用混合大小写。
- **类型变量名：**
  - 类型变量名应该是简洁而富有表现力的，避免小写字母。
  - 常用的类型变量名有E（元素类型）、K（键类型）、V（值类型）、T（通用类型）等。
- **方法名：**
  - 方法名应该是动词或动词短语，使用混合大小写。
- **字段名：**
  - 非最终字段名应该使用混合大小写，第一个字母小写。
- **常量名：**
  - 接口中的常量名应全大写，用下划线分隔单词。
- **局部变量和参数名：**
  - 应该是简短而有意义的，可以是首字母缩写、缩写或助记词汇。



**Unique Package and Module Names（唯一包名和模块名）：**

- 建议的生成唯一包名的步骤：
  - 拥有（或隶属于）互联网域名，例如 oracle.com。
  - 逆转域名以生成前缀，例如 com.oracle。
  - 使用组织内部的约定进一步管理包名的其余部分。



>以上内容目的是向读者解释在Java中如何声明实体，以及在命名中遵循的一些规范。
>
>单字符局部变量或参数名称 应避免使用，但临时变量和循环变量除外，或者 其中变量具有未区分的值 类型。传统的单字符名称是：
>
>- `b`对于一个`byte`
>- `c`对于一个`char`
>- `d`对于一个`double`
>- `e`对于一个`Exception`
>- `f`对于一个`float`
>- `i`,`j` ,`k` 和 int`
>- `l`对于一个`long`
>- `o`对于一个`Object`
>- `s`对于一个`String`
>- `v`对于任意值 某种类型
>
>局部变量或参数名称，包括 只有两个或三个小写字母不应与 初始国家/地区代码和域名是 唯一的包名称。



### 6.2. 名称和标识符

1. 名称和标识符

- **名称（Name）**：用于引用程序中声明的实体的标识符。分为简单名称和限定名称。
- **简单名称（Simple Name）**：单一的标识符。
- **限定名称（Qualified Name）**：由名称、"."标记和标识符组成。

```java
// Simple Name
int age;

// Qualified Name
java.util.List<String> names;

```

2. 名称的上下文

- 在解析名称的含义时，上下文是重要的。上下文的规则区分了名称必须指代的上下文，如包、类、接口、类型参数、变量、方法等。

```java
package com.example;

public class MyClass {
    public void myMethod() {
        // 上下文中使用名称
        String message = "Hello, World!";
        System.out.println(message);
    }
}

```

**3. 标识符的使用**

- **声明中的标识符**：在声明（§6.1）中，标识符用于指定声明实体的名称。
- **标签（Label）**：在带标签的语句（§14.7）和break、continue语句中使用标识符。
- **字段访问表达式（Field Access Expressions）**：在字段访问表达式中，标识符表示表达式前的对象的成员。
- **方法调用表达式（Method Invocation Expressions）**：在一些方法调用表达式中，标识符表示要为表达式前的对象调用的方法。
- **方法引用表达式（Method Reference Expressions）**：在一些方法引用表达式中，标识符表示要引用的对象的方法。
- **限定类实例创建表达式（Qualified Class Instance Creation Expressions）**：在限定类实例创建表达式中，标识符表示前导new token之前表达式的编译时类型的成员。
- **注解元素值对（Annotation Element-Value Pairs）**：在注解的元素-值对中，标识符表示相应注解接口的元素。



>为什么一些表达式使用标识符而不是简单名称?
>
>在Java中，简单表达式名称的解析是基于词法环境（即，必须在变量声明的作用域内）。而对于字段访问、限定方法调用、方法引用和限定类实例创建等表达式，它们引用的成员名称不在词法环境中。
>
>Java中采用标识符而不是简单名称表示某些成员，是为了与词法环境以及访问控制机制的适用范围相匹配，确保语法的一致性和可靠性。



### 6.3.声明的作用域

在Java中，声明的作用域定义了程序中声明的实体可以使用**简单名称引用**的区域。



1. **顶层包:**
   - 可观察的顶层包的作用域包括与其唯一可见的模块相关的所有可观察编译单元。
2. **导入声明:**
   - 单类型导入或按需类型导入声明导入的类或接口的作用域仅限于模块声明和编译单元中的类和接口声明。
   - 单静态导入或按需静态导入声明导入的成员的作用域类似，包括模块声明和相关编译单元中的类和接口声明。
3. **顶层类或接口:**
   - 顶层类或接口的作用域包括同一包中的所有类和接口声明。
4. **类或接口声明:**
   - 类或接口声明的作用域包括类或接口的整个主体，包括嵌套声明。
   - 对于记录类，作用域还包括记录声明的头部。
5. **形式参数:**
   - 方法、构造函数或Lambda表达式的形式参数的作用域是相应方法、构造函数或Lambda表达式的整个主体。
6. **类型参数:**
   - 类型参数的作用域包括类声明的类型参数部分、类声明的超类类型或超接口类型的类型参数部分以及类主体。
   - 对于记录类，作用域还包括记录声明的头部。
   - 接口的类型参数的作用域是接口声明的类型参数部分和接口主体。
   - 方法的类型参数的作用域是方法的整个声明，包括类型参数部分但不包括方法修饰符。
   - 构造函数的类型参数的作用域是构造函数的整个声明，包括类型参数部分但不包括构造函数修饰符。
7. **局部变量声明:**
   - 在块中声明的局部变量的作用域是该块的其余部分。
   - 在for语句中，作用域包括ForInit部分、Expression、ForUpdate和包含的Statement。
   - 在增强for语句中，作用域是包含的Statement。
   - 在try-with-resources语句中，作用域是从声明开始，覆盖资源规范和与try-with-resources语句相关的整个try块。
8. **异常处理程序:**
   - 在try语句的catch子句中声明的异常处理程序的参数的作用域是与catch相关联的整个块。



```java
package points;

class Point {
    int x, y;
    PointList list;
    Point next;
}

class PointList {
    Point first;
}

```

`Point`类中对`PointList`的使用是有效的，因为`PointList`的作用域包括`Point`和`PointList`，以及包中其他编译单元的其他类或接口声明。



```java
class Test1 {
    static int x;
    public static void main(String[] args) {
        int x = x;
    }
}

```

*下程序会导致编译时错误 因为局部变量的初始化是 在地方申报范围内 变量，但局部 变量尚无值，也不能 使用。该字段的值为 （已分配 初始化时），但是其中一个x 因为它被本地  遮蔽 变量。*

```java
class Test2 {
    static int x;
    public static void main(String[] args) {
        int x = (x = 2) * 2;
        System.out.println(x);
    }
}

```

此程序有效，并输出`4`。局部变量`x`在使用之前已被明确赋值。



```java
class PatternExample {
    public static void main(String[] args) {
        System.out.print("2+1=");
        int two = 2, three = two + 1;
        System.out.println(three);
    }
}

```

在此程序中，模式变量声明的作用域是通过考虑成功模式匹配后模式变量绝对匹配的程序点确定的。

>理解声明的作用域对于正确访问变量并避免Java程序中的命名冲突至关重要。



#### 6.3.1. 表达式中模式变量的作用域

在Java中，模式变量是与模式匹配相关的新特性。

模式变量的作用域规则与布尔表达式类型有关，其中只有一些特定类型的布尔表达式会引入模式变量，并确定这些变量的确切匹配位置。如果一个表达式不是条件与表达式、条件或表达式、逻辑补充表达式、条件表达式、instanceof 表达式、switch 表达式或带括号的表达式，则不适用作用域规则。

>**下面内容讨论的都是在模式变量的基础上进行的！**
>
>例如：
>
>```java
>if (obj instanceof String s) {
>    // 在这里，模式变量 s 被引入，并且在 if 条件成立的情况下肯定已经匹配成功。
>    // 所以说，由 obj 引入的模式变量 s 在这里肯定匹配。
>    // 你可以在这里使用变量 s，因为它已经成功匹配了。
>}
>
>```
>
>

##### 6.3.1.1. 条件与运算符 `&&`

条件与运算符 `&&`的规则如下：

- 当 `a` 为 `true` 时，由 `a` 引入的模式变量在 `b` 也必须也true。
- 当 `a && b` 为 `true` 时，模式变量由以下两种情况之一引入即为 `true`：
  1. 它由 `a` 为 `true` 时引入。
  2. 它由 `b` 为 `true` 时引入。
- 需要注意，对于 `a && b` 为 `false` 的情况，没有引入模式变量的规则。这是因为在编译时无法确定哪个操作数将评估为 `false`。
- 如果满足以下条件之一，将导致编译时错误：
  1. 模式变量既由 `a` 为 `true` 时引入，又由 `b` 为 `true` 时引入。
  2. 模式变量既由 `a` 为 `false` 时引入，又由 `b` 为 `false` 时引入。

这两个错误情况排除了 `&&` 运算符的两个操作数声明相同名称的模式变量的可能性。例如，考虑问题表达式 `(a instanceof String s) && (b instanceof String s)`。第一个错误情况涵盖了整个表达式评估为 `true` 的情况，其中（如果代码是合法的）需要初始化两个名为 `s` 的模式变量声明，因为左操作数和右操作数都评估为 `true`。由于在程序的其余部分无法区分两个名为 `s` 的变量，因此整个表达式被认为是错误的。第二个错误情况涵盖了整个表达式评估为 `false` 的相反情况。



##### 6.3.1.2. 条件或运算符 `||`

条件或运算符 `||`（§15.24）的规则如下：

- 当 `a` 为 `false` 时，由 `a` 引入的模式变量在 `b` 也必须为false。
- 当 `a || b` 为 `false` 时，模式变量由以下两种情况之一引入即为 `false`：
  1. 它由 `a` 为 `false` 时引入。
  2. 它由 `b` 为 `false` 时引入。
- 需要注意，对于 `a || b` 为 `true` 的情况，没有引入模式变量的规则。这是因为在编译时无法确定哪个操作数将评估为 `true`。
- 如果满足以下条件之一，将导致编译时错误：
  1. 模式变量既由 `a` 为 `true` 时引入，又由 `b` 为 `true` 时引入。
  2. 模式变量既由 `a` 为 `false` 时引入，又由 `b` 为 `false` 时引入。

这两个错误情况排除了 `||` 运算符的两个操作数声明相同名称的模式变量的可能性，类似于 `&&` 运算符的情况。



##### 6.3.1.3. 逻辑补充运算符 `!`

逻辑补充运算符 `!`（§15.15.6）的规则如下：

- 当 `!a` 为 `true` 时，由 `a` 为 `false` 时引入的模式变量肯定匹配。
- 当 `!a` 为 `false` 时，由 `a` 为 `true` 时引入的模式变量肯定匹配。



##### 6.3.1.4. 条件运算符 `? :`

条件表达式 `a ? b : c`（§15.25）的规则如下：

- 当 `a` 为 `true` 时，由 `a` 引入的模式变量在 `b` 为。
- 当 `a` 为 `false` 时，由 `a` 引入的模式变量在 `c` 处肯定匹配。
- 需要注意，在编译时无法确定 `a` 的操作数将评估为 `true` 或 `false`，因此没有规则引入模式变量。
- 如果满足以下条件之一，将导致编译时错误：
  1. 模式变量既由 `a` 为 `true` 时引入，又由 `c` 为 `true` 时引入。
  2. 模式变量既由 `a` 为 `true` 时引入，又由 `c` 为 `false` 时引入。
  3. 模式变量既由 `a` 为 `false` 时引入，又由 `b` 为 `true` 时引入。
  4. 模式变量既由 `a` 为 `false` 时引入，又由 `b` 为 `false` 时引入。
  5. 模式变量既由 `b` 为 `true` 时引入，又由 `c` 为 `true` 时引入。
  6. 模式变量既由 `b` 为 `false` 时引入，又由 `c` 为 `false` 时引入。

这些错误情况类似于 `&&` 和 `||` 运算符的错误情况，排除了 `? :` 运算符的两个操作数声明相同名称的模式变量的可能性。

##### 6.3.1.5. `instanceof` 表达式

`instanceof` 表达式用于检查对象是否是某个类的实例。在模式匹配中，`instanceof` 表达式还可以用于引入模式变量。以下是关于具有模式操作数的 `instanceof` 表达式的规则：

- 当 `a instanceof p` 为 `true` 时，由 `a instanceof p` 引入的模式变量在模式 `p` 中声明时必须匹配。
- 不允许模式变量遮蔽另一个局部变量。

```java
class Person {
    String name;

    Person(String name) {
        this.name = name;
    }
}

class Employee extends Person {
    int employeeId;

    Employee(String name, int employeeId) {
        super(name);
        this.employeeId = employeeId;
    }
}

public class InstanceofOperatorExample {
    public static void main(String[] args) {
        Person person = new Employee("John", 123);

        // 模式变量引入规则：在 a instanceof p 为 true 时，由 a instanceof p 引入的模式变量
        // 在模式 p 中声明时必须匹配。
        if (person instanceof Employee employee) {
            System.out.println("Employee ID: " + employee.employeeId);
        } else {
            System.out.println("Not an employee");
        }

        // 模式变量不允许遮蔽另一个局部变量。
        // 错误示例：
        // int employee = 42; // 编译时错误
    }
}
```

>`person instanceof Employee employee` 表达式在为 `true` 时，引入了模式变量 `employee`，该模式变量与 `Employee` 类型的对象匹配。这使得我们能够在条件为 `true` 时安全地使用模式变量，而不需要强制类型转换。同时，要注意模式变量不允许遮蔽其他局部变量。



##### 6.3.1.6. `switch` 表达式

1. **`switch` 表达式的 `switch` 规则：**
   - 由 `switch` 标签引入的模式变量在关联的 `switch` 规则表达式、`switch` 规则块或 `switch` 规则 throw 语句中必定匹配。
2. **`switch` 表达式的 `switch` 标签语句组规则：**
   - 由 `switch` 标签引入的模式变量在关联的 `switch` 标签语句组中的所有语句中必定匹配。
3. **`switch` 表达式的语句组中的规则：**
   - 由 `switch` 标签引入的模式变量在语句组中的每个语句中都必定匹配。
   - 如果在语句组中的语句 `S` 中引入了模式变量，那么该模式变量在 `S` 后面的所有语句中也必定匹配。

```java
public class SwitchExpressionExample {
    public static void main(String[] args) {
        Object obj = "Hello";

        // switch 表达式的 switch 规则
        int result = switch (obj) {
            case String s -> {
                // 模式变量 s 在这里必定匹配
                System.out.println("String length: " + s.length());
                yield 1;
            }
            case Integer i -> {
                // 这里不允许使用模式变量 i，因为它没有在这个 case 中引入
                yield 2;
            }
            default -> {
                // 默认情况
                System.out.println("Default case");
                yield 0;
            }
        };

        System.out.println("Result: " + result);
    }
}

```



##### 6.3.1.7. 括号表达式

括号表达式 `(a)` 在模式匹配中引入模式变量的规则如下：

- 当 `(a)` 为 `true` 时，由 `(a)` 引入的模式变量在 `a` 为 `true` 时必定匹配。
- 当 `(a)` 为 `false` 时，由 `(a)` 引入的模式变量在 `a` 为 `false` 时必定匹配。

```java
public class ParenthesizedExpressionExample {
    public static void main(String[] args) {
        boolean condition = true;

        // 括号表达式引入模式变量的规则：
        // 当括号表达式为 true 时，引入的模式变量在关联的条件为 true 时必定匹配。
        // 当括号表达式为 false 时，引入的模式变量在关联的条件为 false 时必定匹配。
        int result = (condition) ? 42 : 0;

        System.out.println("Result: " + result);
    }
}

```



#### 6.3.2. 语句中模式变量的作用域

在语句中，仅有一些特定类型的语句在确定模式变量的作用域方面起着重要的作用。



**1. if、while、do、for 语句中的模式变量作用域：**

在 `if`、`while`、`do` 或 `for` 语句中，如果包含引入模式变量的表达式，那么在某些情况下，这些变量的作用域可以包括语句的子语句。

例如，在以下的 if-then-else 语句中，模式变量 `s` 的作用域包括一个子语句但不包括另一个子语句：

```java
Object o = ...;
if (o instanceof String s) {
    // s 在这个子语句中的作用域内；不需要对 o 进行强制类型转换
    System.out.println(s.replace('*', '_'));
} else {
    // s 在这个子语句中的作用域外（因此，会出现错误）
    System.out.println(s); // 编译时错误
}

```

**2. 语句本身引入模式变量：**

在某些情况下，语句本身而非语句内的表达式可以引入模式变量。由语句引入的模式变量在包含块内的后续语句中都是可见的。

例如，在以下方法中，模式变量 `s` 的作用域包括 `if` 语句后的方法体：

```java
void test(Object o) {
    if (!(o instanceof String s)) {
        throw new IllegalArgumentException();
    }
    // 只有在模式匹配成功的情况下才能到达这里
    // 因此，s 在块的其余部分中是可见的
    System.out.println(s.repeat(5));
    // ...
}

```



##### 6.3.2.1. 块语句

在块语句中，特定规则适用于不是 switch block（`switch` 语句块）的块（§14.2）中包含的块语句 `S`：

- 由 `S` 引入的模式变量在块中的所有后续语句中必定匹配。

```java
public class BlockStatementExample {
    public static void main(String[] args) {
        boolean condition = true;

        if (condition) {
            // 模式变量 s 在这里被引入
            String s = "Hello, World!";
            System.out.println(s);

            // 模式变量 s 在这个块的后续语句中仍然可见
            System.out.println(s.length());
        }

        // 在这里，模式变量 s 不再可见
        // System.out.println(s); // 编译时错误
    }
}

```

>`if` 语句的块引入了模式变量 `s`。在该块内，`s` 在后续语句中仍然可见，但是在块外，`s` 不再可见。这符合规则，即由块语句引入的模式变量在块的后续语句中仍然可见。





##### 6.3.2.2. if Statements

在 `if` 语句中，模式变量的作用域规则如下：

1. **if (e) S 语句规则：**

- 当 `e` 为 `true` 时，由 `e` 引入的模式变量在语句 `S` 中必定匹配。
- 当 if (e) S语句引入模式变量时，必须满足两个条件：
  1. 它由 `e` 为 `false` 时引入。
  2. `S` 不能正常完成。

**2. if (e) S else T 语句规则：**

- 当 `e` 为 `true` 时，由 `e` 引入的模式变量在语句 `S` 中必定匹配。
- 当 `e` 为 `false` 时，由 `e` 引入的模式变量在语句 `T` 中必定匹配。
- if (e) S else T语句引入模式变量时，满足以下条件之一：
  1. 它由 `e` 为 `true` 时引入，且 `S` 可以正常完成，而 `T` 不能正常完成。
  2. 它由 `e` 为 `false` 时引入，且 `S` 不能正常完成，而 `T` 可以正常完成。

这些规则强调了模式变量作用域的流程性质。例如，在以下语句中：

```java
if (e instanceof String s) {
    counter += s.length();
} else {
    System.out.println(e);  // s 不在作用域内
}

```

模式变量 `s` 由 `instanceof` 表达式引入，在第一个语句（`then` 块中的赋值语句）中是可见的，但在第二个语句（`else` 块中的表达式语句）中不可见。

结合对布尔表达式的处理，模式变量的作用域对利用熟悉的布尔逻辑等价性进行的代码重构是鲁棒的。例如，前面的代码可以重写为：

```java
if (!(e instanceof String s)) {
    System.out.println(e);  // s 不在作用域内
} else {
    counter += s.length();
}

```

甚至可以如下重写，尽管不一定推荐使用两次 `!` 运算符：

```java
if (!!(e instanceof String s)) {
    counter += s.length();
} else {
    System.out.println(e);  // s 不在作用域内
}
```



##### 6.3.2.3. while Statements

在 `while` 语句中，模式变量的作用域规则如下：

**while (e) S 语句规则：**

- 当 `e` 为 `true` 时，由 `e` 引入的模式变量在语句 `S` 中必定匹配。
- 当 while (e) S语句引入模式变量时，必须满足两个条件：
  1. 它由 `e` 为 `false` 时引入。
  2. `S` 不包含可达的 `break` 语句，其中 `while` 语句是 `break` 的目标（§14.15）。

```java
public class WhileStatementExample {
    public static void main(String[] args) {
        int[] numbers = {1, 2, 3, 4, 5};
        int sum = 0;
        int i = 0;

        while (i < numbers.length) {
            int current = numbers[i];
            sum += current;

            System.out.println("Current number: " + current);
            System.out.println("Current sum: " + sum);

            i++;
        }
    }
}

```

>`while` 语句引入了模式变量 `current`，该变量在 `while` 语句的循环体中是可见的。这符合模式变量在 `while` 语句中的作用域规则。值得注意的是，循环体中没有包含 `break` 语句，因此模式变量的作用域延伸到整个循环。



##### 6.3.2.4. do Statements

在 `do` 语句中，模式变量的作用域规则如下：

- do S while (e)

   语句引入模式变量，当且仅当满足以下两个条件：

  1. 它由 `e` 为 `false` 时引入。
  2. `S` 不包含可达的 `break` 语句，其中 `do` 语句是 `break` 的目标。

下面是一个简单的 Java 代码示例，演示了在 `do` 语句中引入模式变量的情况：

```java
public class DoStatementExample {
    public static void main(String[] args) {
        int[] numbers = {1, 2, 3, 4, 5};
        int sum = 0;
        int i = 0;

        do {
            int current = numbers[i];
            sum += current;

            System.out.println("Current number: " + current);
            System.out.println("Current sum: " + sum);

            i++;
        } while (i < numbers.length);
    }
}

```

##### 6.3.2.5. for Statements

对于基本的 `for` 语句，模式变量的作用域规则如下：

1. **对于基本的 for 语句：**
   - 当条件表达式为 `true` 时，由条件表达式引入的模式变量在增量部分和包含的语句中必定匹配。
   - 一个模式变量由基本的 for 语句引入，当且仅当满足以下两个条件：
     - 它由条件表达式为 `false` 时引入。
     - 包含的语句 `S` 不包含可达的 `break` 语句，其中基本的 for 语句是 `break` 的目标。
2. **对于增强的 for 语句：**
   - 增强的 for 语句被定义为通过转换为基本的 for 语句实现的，因此对于增强的 for 语句不需要提供特殊的规则。

在基本的 `for` 语句中引入模式变量的情况：

```java
public class ForStatementExample {
    public static void main(String[] args) {
        int[] numbers = {1, 2, 3, 4, 5};
        int sum = 0;

        // 基本的 for 语句引入模式变量 i
        for (int i = 0; i < numbers.length; i++) {
            int current = numbers[i];
            sum += current;

            System.out.println("Current number: " + current);
            System.out.println("Current sum: " + sum);
        }
    }
}

```



##### 6.3.2.6. switch Statements

对于 `switch` 语句，模式变量的作用域规则如下：

1. **对于包含 switch 规则的 switch 语句：**
   - 由 `switch` 标签引入的模式变量在关联的 `switch` 规则表达式、`switch` 规则块或 `switch` 规则 throw 语句中必定匹配。
2. **对于包含 switch 标签语句组的 switch 语句：**
   - 由 `switch` 标签引入的模式变量在关联的 `switch` 标签语句组中的所有语句中必定匹配。
   - 由 `switch` 标签引入的模式变量在语句组中的每个语句 `S` 中都必定匹配，如果有的话，在 `S` 后面的所有语句中也必定匹配。

```java
public class SwitchStatementExample {
    public static void main(String[] args) {
        Object obj = "Hello";

        // switch 语句的 switch 规则
        switch (obj) {
            case String s -> {
                // 模式变量 s 在这里必定匹配
                System.out.println("String length: " + s.length());
            }
            case Integer i -> {
                // 这里不允许使用模式变量 i，因为它没有在这个 case 中引入
                System.out.println("Integer value: " + i);
            }
            default -> {
                // 默认情况
                System.out.println("Default case");
            }
        }
    }
}

```



##### 6.3.2.7. Labeled Statements

对于带标签的语句，模式变量的作用域规则如下：

- 由带标签的语句 L: S引入的模式变量，当且仅当满足以下两个条件：
  1. 它由语句 `S` 引入。
  2. `S` 不包含可达的 `break` 语句，其中带标签的语句是 `break` 的目标（§14.15）。

```java
public class LabeledStatementExample {
    public static void main(String[] args) {
        int[][] matrix = {
            {1, 2, 3},
            {4, 5, 6},
            {7, 8, 9}
        };

        // 带标签的语句
        outerLoop:
        for (int[] row : matrix) {
            for (int num : row) {
                if (num == 5) {
                    System.out.println("Found 5!");
                    break outerLoop; // 使用标签进行循环跳出
                }
            }
        }
    }
}

```

>带标签的语句 `outerLoop` 用于标识外部循环。`break outerLoop;` 语句用于在找到数字 `5` 时跳出外部循环。在这种情况下，由内层循环引入的模式变量在外层循环的 `break` 语句处是可见的。带标签的语句使得在外部循环的任何位置都可以安全地使用模式变量。



##### 6.3.3. Scope for Pattern Variables in Case Labels

在 `case` 标签中，模式变量的作用域规则如下：

- 模式变量可以通过带有 `case` 模式的 `case` 标签引入，可以是模式本身，也可以是一个守卫中的模式，对应的模式变量在关联的 `switch` 表达式（§6.3.1.6）或 `switch` 语句（§6.3.2.6）的相关部分中是可见的。

以下规则适用于 `case` 标签：

1. 如果 `case` 模式 `p` 包含模式变量的声明，则该模式变量由带有 `case` 模式的 `case` 标签引入。
2. 如果在带有守卫的 `case` 标签中，守卫的 `case` 模式包含模式变量的声明，则该模式变量在关联的守卫中必定匹配。
3. 如果在带有守卫的 `case` 标签中引入模式变量，它是由关联的守卫在为 `true` 时引入的）。

```java
public class CaseLabelExample {
    public static void main(String[] args) {
        Object obj = "Hello";

        // switch 语句的 case 标签
        switch (obj) {
            case String s -> {
                // 模式变量 s 在这里必定匹配
                System.out.println("String length: " + s.length());
            }
            case Integer i when i > 0 -> {
                // 模式变量 i 在关联的守卫中必定匹配
                System.out.println("Positive Integer: " + i);
            }
            default -> {
                // 默认情况
                System.out.println("Default case");
            }
        }
    }
}

```

>带有守卫的 `case` 标签是指在 `switch` 语句中的某个 `case` 标签中除了模式匹配之外，还包含了一个额外的条件，即守卫条件（guard condition）。守卫条件是一个布尔表达式，它必须为 `true` 才能执行关联的 `case` 语句块。
>
>```java
>case SomePatternType x when guardCondition -> {
>    // 执行语句块
>}
>
>```
>
>该特性在Java 21以上支持使用



仔细学习这个知识点，然后对我进行深入和扩展教学







### 6.4. Shadowing and Obscuring

- 局部变量、形式参数、异常参数、局部类，或局部接口只能使用简单名称，而非限定名称进行引用。

- 在局部变量声明、形式参数声明、异常参数声明、局部类声明、或局部接口声明的作用域内，某些声明是不允许的，因为仅使用简单名称无法区分这些声明的实体。

  - 例如，如果一个方法的形式参数的名称在方法体内被重新声明为局部变量的名称，那么局部变量会遮蔽形式参数，这将导致无法引用形式参数，这是一个不良的结果。

    ```java
    public class ShadowingExample {
    
        // 方法的形式参数为 value
        static void exampleMethod(int value) {
            // 在方法体内重新声明局部变量 value
            int value = 10;  // 这会导致编译时错误，因为局部变量遮蔽了形式参数
            System.out.println("Local variable value: " + value);
        }
    
        public static void main(String[] args) {
            // 调用方法并传递一个值
            exampleMethod(5);
        }
    }
    
    ```

    

- **编译时错误规则**：

  - 如果形式参数的名称被用于在方法、构造函数或Lambda表达式的主体中声明新变量，除非新变量是在方法、构造函数或Lambda表达式内包含的类或接口声明中声明的，否则会导致编译时错误。

  - 如果局部变量 `v` 的名称被用于在 `v` 的作用域内声明新变量，除非新变量是在 `v` 的作用域内出现的类或接口声明中声明的，否则会导致编译时错误。

    ```java
    class LocalVariableShadowingExample {
    
        public static void main(String[] args) {
            int v = 5;
    
            // 下面的声明会导致编译时错误，因为局部变量 v 被重新声明了
            int v = 10;
            System.out.println("Local variable v: " + v);
        }
    }
    
    ```

    

  - 如果异常参数的名称被用于在 `catch` 子句的块中声明新变量，除非新变量是在 `catch` 子句的块内包含的类或接口声明中声明的，否则会导致编译时错误。

    ```java
    class ExceptionParameterShadowingExample {
    
        public static void main(String[] args) {
            try {
                // ...
            } catch (Exception e) {
                // 下面的声明会导致编译时错误，因为异常参数 e 被重新声明了
                Exception e = new Exception("New Exception");
                // 处理异常
            }
        }
    }
    
    ```

    

  - 如果局部类或接口 `C` 的名称被用于在 `C` 的作用域内声明新的局部类或接口，除非新的局部类或接口是在 `C` 的作用域内出现的类或接口声明中声明的，否则会导致编译时错误。

    ```java
    class LocalClassShadowingExample {
    
        public static void main(String[] args) {
            class C {
                // ...
            }
    
            // 下面的声明会导致编译时错误，因为局部类 C 被重新声明了
            class C {
                // ...
            }
        }
    }
    
    ```

>这些规则允许在变量、局部类或局部接口的作用域内发生在其内的嵌套类或接口声明中的重新声明；这样的嵌套类或接口声明可以是局部类或接口声明，也可以是匿名类声明。因此，形式参数、局部变量、局部类或局部接口的声明可能会在嵌套在方法、构造函数或Lambda表达式内的类或接口声明中被遮蔽；异常参数的声明可能会在嵌套在 `catch` 子句块内的类或接口声明中被遮蔽。

- **名称冲突处理**：

  - 有两种设计方案用于处理由Lambda表达式中的lambda参数和其他在lambda表达式中声明的变量引起的名称冲突。

    - 一种是模仿类声明：像局部类一样，lambda表达式引入了新的“级别”，并且表达式外部的所有变量名都可以被重新声明。

      ```java
      static int externalVariable = 10;
      
          public static void main(String[] args) {
              int localVariable = 5;
      
              // 模仿类声明的策略，lambda表达式引入新的“级别”，外部的变量名可以被重新声明
              MyFunctionalInterface myLambda = (param) -> {
                  int externalVariable = 20;  // 外部变量被重新声明
                  System.out.println("Param: " + param);
                  System.out.println("Local variable: " + localVariable);
                  System.out.println("External variable: " + externalVariable);
              };
      
              myLambda.myMethod(7);
          }
      
          interface MyFunctionalInterface {
              void myMethod(int param);
          }
      ```

      

    - 另一种是“本地”策略：像 `catch` 子句、`for` 循环和块一样，lambda表达式在与封闭上下文相同的“级别”上运行，表达式外部的局部变量不能被遮蔽。

      ```java
      class LambdaExpressionLocalStrategy {
      
          static int externalVariable = 10;
      
          public static void main(String[] args) {
              int localVariable = 5;
      
              // “本地”策略，lambda表达式在与封闭上下文相同的“级别”上运行，外部的局部变量不能被遮蔽
              MyFunctionalInterface myLambda = (param) -> {
                  // 下面的声明会导致编译时错误，因为外部的局部变量 localVariable 不能被遮蔽
                  int localVariable = 20;
                  System.out.println("Param: " + param);
                  System.out.println("Local variable: " + localVariable);  // 无法访问外部的局部变量
                  System.out.println("External variable: " + externalVariable);
              };
      
              myLambda.myMethod(7);
          }
      
          interface MyFunctionalInterface {
              void myMethod(int param);
          }
      }
      
      ```

      

>上述规则使用了“本地”策略；没有特殊规定允许在lambda表达式中声明的变量遮蔽在封闭方法中声明的变量。

**示例 6.4-1. 尝试遮蔽局部变量**

由于将标识符声明为方法、构造函数或初始化块的局部变量不能出现在具有相同名称的参数或局部变量的作用域内，因此以下程序会导致编译时错误：

```java
class Test1 {
    public static void main(String[] args) {
        int i;
        for (int i = 0; i < 10; i++)
            System.out.println(i);
    }
}
```

这种限制有助于检测一些其他非常难以发现的错误。类似地，对局部变量由于成员变量的阴影化的限制被认为是不切实际的，因为在超类中添加成员可能会导致子类必须重新命名局部变量。相关的考虑使得对由嵌套类中的成员对局部变量的阴影化或由嵌套类中声明的局部变量对局部类的阴影化的限制同样不吸引人。

因此，以下程序可以无错误编译：

```java
class Test2 {
    public static void main(String[] args) {
        int i;
        class Local {
            {
                for (int i = 0; i < 10; i++)
                    System.out.println(i);
            }
        }
        new Local();
    }
}
```

另一方面，具有相同名称的局部变量可以在两个不相交的块或for语句中声明，其中一个不包含另一个：

```java
class Test3 {
    public static void main(String[] args) {
        for (int i = 0; i < 10; i++)
            System.out.print(i + " ");
        for (int i = 10; i > 0; i--)
            System.out.print(i + " ");
        System.out.println();
    }
}
```

这个程序可以无错误编译，执行时产生输出：

```
0 1 2 3 4 5 6 7 8 9 10 9 8 7 6 5 4 3 2 1
```

这种风格在模式匹配中也很常见，其中重复的模式经常使用相同的名称：

```java
class Point {
    int x, y;
    Point(int x, int y) { this.x = x; this.y = y; }
}

class Test4 {
    static void test(Object a, Object b, Object c) {
        if (a instanceof Point p) {
            System.out.println("a is a point ("+p.x+","+p.y+")");
        }
        if (b instanceof Point p){
            System.out.println("b is a point ("+p.x+","+p.y+")");
        } else if (c instanceof Point p) {
            System.out.println("c is a point ("+p.x+","+p.y+")");
        }
    }

    public static void main(String[] args) {
        Point p = new Point(2,3);
        Point q = new Point(4,5);
        Point r = new Point(6,7);
        test(p, q, r);
    }
}
```

然而，模式变量不被允许遮蔽局部变量，包括其他模式变量，因此以下程序会导致两个编译时错误：

```java
class Point {
    int x, y;
    Point(int x, int y) { this.x = x; this.y = y; }
}

class Test5 {
    static void test(Object a, Object b, Object c) {
        if (a instanceof Point p) {
            System.out.println("a is a point ("+p.x+","+p.y+")");
	    
            if (b instanceof Point p) {  // 编译时错误
                System.out.println("b is a point ("+p.x+","+p.y+")"); 
            }
        }
    }

    public static void main(String[] args) {
        Point p = new Point(2,3);
        Point q = new Point(4,5);
        Point r = new Point(6,7);
        test(p, q, r);

        if (new Object() instanceof Point q)  // 编译时错误
            System.out.println("I get your point");
    }
}
```





#### **6.4.1. 遮蔽**

某些声明可能在其范围的一部分被另一个同名声明所遮蔽，在这种情况下，简单名称无法用于引用已声明的实体。

```java
public class ShadowingExample {
    // 类级别的变量声明
    static int x = 10;

    public static void main(String[] args) {
        // 方法级别的变量声明，遮蔽了类级别的变量
        int x = 5;

        System.out.println("Local variable x: " + x); // 引用方法级别的变量
        System.out.println("Class variable x: " + ShadowingExample.x); // 引用类级别的变量
    }
}

```

>在这个例子中，类 `ShadowingExample` 中有一个类级别的变量 `x`，而在 `main` 方法中又声明了一个同名的方法级别的变量 `x`。方法级别的变量遮蔽了类级别的变量，因此在方法中，简单名称 `x` 引用的是方法级别的变量，而要引用类级别的变量，需要使用类名限定，即 `ShadowingExample.x`。这种现象被称为"遮蔽"。

遮蔽与隐藏是不同的，后者仅适用于本应被继承但由于子类中的声明而未继承的成员。遮蔽还与混淆不同。

- 名为n的类型的 声明d会隐藏在整个d范围内d出现的范围内的任何其他名为n的类型的声明。
- 名为n的字段或形式参数的 声明d在整个d范围内，会影响d出现时范围内名为n的任何其他变量的声明。
- 名为n的局部变量或异常参数的 声明d在整个d范围内，(a) 在d出现的范围内的任何其他名为n的字段的声明，以及 (b) 任何其他名为n的变量在d出现的位置范围内，但未在声明 d 的最内层类中声明*。*
- 名为n的方法的 声明d会隐藏任何其他名为n的方法的声明，这些方法位于d在整个d范围内出现的封闭范围内。
- 包声明永远不会遮蔽任何其他声明。
- 按需类型导入声明永远不会导致任何其他声明被隐藏。
- 按需静态导入声明永远不会导致任何其他声明被隐藏。
- 略【理解有点难度，超越作者水平。具体内容请查阅官方文档】

**示例 6.4.1-1. 通过局部变量声明遮蔽字段声明**

```java
class Test {
    static int x = 1;
    public static void main(String[] args) {
        int x = 0;
        System.out.print("x=" + x);
        System.out.println(", Test.x=" + Test.x);
    }
}
```

该程序输出：

```
x=0, Test.x=1
```

这个程序声明了：

- 一个类 `Test`
- 一个类（静态）变量 `x` 是类 `Test` 的成员
- 一个类方法 `main` 是类 `Test` 的成员
- `main` 方法的参数 `args`
- `main` 方法的局部变量 `x`

由于类变量 `x` 的范围包括整个类的主体，类变量 `x` 通常应在方法 `main` 的整个主体中可用。然而，在这个例子中，类变量 `x` 在方法 `main` 的主体内被局部变量 `x` 的声明遮蔽。

这意味着：

- 在打印的调用中的表达式 `x` 引用（表示）局部变量 `x` 的值。
- println 的调用使用了合格名称Test.x，它使用类类型名称 `Test` 来访问类变量 `x`，因为此时类变量 `Test.x` 的声明已经被遮蔽，并且不能通过其简单名称引用。
- 关键字 `this` 也可以用于访问被遮蔽的字段 `x`，使用形式 `this.x`。事实上，在构造函数中（§8.8）中，这种惯用法通常出现：

```java
class Pair {
    Object first, second;
    public Pair(Object first, Object second) {
        this.first = first;
        this.second = second;
    }
}
```

在这里，构造函数使用与要初始化的字段相同的名称作为参数。这比为参数发明不同的名称更简单，在这种风格化的上下文中并不太令人困扰。然而，一般来说，在局部变量与字段具有相同名称的情况下是不良风格的。

**示例 6.4.1-2. 通过类型声明遮蔽另一个类型声明**

```java
import java.util.*;

class Vector {
    int[] val = { 1 , 2 };
}

class Test {
    public static void main(String[] args) {
        Vector v = new Vector();
        System.out.println(v.val[0]);
    }
}
```

该程序编译并打印：

```diff
1
```

它使用了此处声明的类 `Vector`，而不是可能被按需导入的通用类 `java.util.Vector`。





#### 6.4.2. 遮蔽

在某些情境中，一个简单的名称可能会被解释为**变量、类型或包的名称**。在这些情况下，规则指定将优先选择变量而不是类型，并且将优先选择类型而不是包。因此，有时可能无法通过其简单名称引用类型或包，即使其声明在范围内且未被遮蔽。我们称这样的声明为被遮蔽。

>变量>类型>包

遮蔽与阴影和隐藏是不同的。

模块名称与变量、类型或包的名称之间不存在遮蔽；因此，模块可以与变量、类型和包共享名称，尽管不一定建议将模块命名为其包含的包。

果确实发生了遮蔽，以下是一些建议，可以避免它

**当表达式中出现包名称时：**

- 如果包名称被字段声明遮蔽，那么导入声明通常可以用来使该包中声明的类型名称可用。
- 如果包名称被参数或局部变量的声明遮蔽，那么可以更改参数或局部变量的名称而不影响其他代码。

包名称的第一个组件通常不容易被误认为是类型名称，因为类型名称通常以单个大写字母开头。（Java编程语言实际上不依赖大小写区分来确定名称是包名称还是类型名称。）

涉及类和接口类型名称的遮蔽很少见。字段、参数和局部变量的名称通常不会遮蔽类型名称，因为它们惯例上以小写字母开头，而类型名称惯例上以大写字母开头。

**涉及字段名称的遮蔽很少见，然而：**

- 如果字段名称遮蔽了包名称，那么导入声明通常可以用来使该包中声明的类型名称可用。
- 如果字段名称遮蔽了类型名称，那么可以使用类型的完全限定名称，除非类型名称表示本地类或接口（§14.3）。

**字段名称不能遮蔽方法名称:**

如果字段名称被参数或局部变量的声明遮蔽，那么可以更改参数或局部变量的名称而不影响其他代码。

**涉及常量名称的遮蔽很少见：**

- 常量名称通常不包含小写字母，因此它们通常不会遮蔽包或类型的名称，也不会遮蔽字段，其名称通常至少包含一个小写字母。
- 常量名称不能遮蔽方法名称，因为它们在语法上有所区别。

>总结：
>
>1. **名称冲突与遮蔽（Obscuring）：**
>   - 避免在不同上下文中使用相同的名称，以防止变量、类型或包之间的冲突。
>   - 注意命名约定（§6.1）以减少遮蔽，避免使用与包、类型或变量相似的名称。
>2. **导入声明（Import Declarations）：**
>   - 使用导入声明（§7.5）来处理在字段声明中遮蔽的包名，以便在代码中使用该包中声明的类型名称。
>3. **包名与类型名的区分：**
>   - 注意包名和类型名的首字母约定，通常类型名以大写字母开头，而包名以小写字母开头。这有助于在代码中清晰地区分它们。
>4. **类和接口类型名称的遮蔽：**
>   - 遮蔽类和接口类型名称的情况相对较少，但要注意字段、参数和局部变量的命名规范，以避免与类型名称冲突。
>5. **方法名与其他名称的区分：**
>   - 方法名不能遮蔽或被其他名称遮蔽，这提供了一种清晰的语法区分。注意在方法命名中保持清晰和有意义。
>6. **字段名的使用：**
>   - 避免字段名与包名或类型名发生遮蔽，可使用导入声明解决这些冲突。
>   - 如果字段名与类型名发生遮蔽，可以使用类型的完全限定名称，除非类型名称表示本地类或接口。
>7. **常量名的使用：**
>   - 常量名通常不包含小写字母，因此不太容易与包或类型的名称发生遮蔽。
>   - 常量名与方法名在语法上有明显的区别，因此不会发生遮蔽









仔细学习这个知识点，然后对我进行深入和扩展教学
