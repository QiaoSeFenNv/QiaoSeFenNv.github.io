---
title: "The Java® Language Specification Front"
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

### 1.1. 规范的组织

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



### 1.2. 示例程序

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



### 1.3. 符号

1. **引用Java SE平台API**：文本提到，在整个规范中，会引用Java SE平台API中的类和接口。当使用单个标识符（例如“N”）引用类或接口时，意图是引用`java.lang`包中名为“N”的类或接口。这是Java核心类的默认包。
2. **规范名称**：当引用来自`java.lang`之外的包中的类或接口时，规范使用规范名称，这是一个包括包名的完全限定名称。例如，它可能引用一个类为`com.example.MyClass`而不仅仅是“MyClass”。

> ***简单理解：***
>
> 当在Java规范中使用单个标识符引用类或接口时，通常是指这个类或接口来自Java核心包的默认包，也就是`java.lang`包。
>
> 如果要引用来自其他包的类或接口，规范要求提供完全限定名称，即包括包名的类或接口名称。这是为了确保唯一性，因为不同的包可能会包含具有相同名称的类或接口。





## 2.语法

### 2.1. 上下文无关语法

```
上下文无关语法由一个数字组成的制作。每部作品都有一个摘要 称为非终端的符号 它的左侧，以及一个或多个序列 非终端和终端符号作为 它的右手边。对于每种语法， 终端符号是从指定的字母表中提取的。

从一个句子开始，由一个可区分的句子组成 非终端，称为目标符号，给定 上下文无关语法指定了一种语言，即 可能由重复产生的终端符号序列 将序列中的任何非终端替换为 非终端是左侧的生产。
```

在该规范中，上下文无关语法表示一种形式文法。用于描述语言的结构。通常它由一系列**生产式**组成。CFG 用于描述各种编程语言、自然语言和其他形式语言的语法结构。

>生产式一般就是我们所说的表达式：左侧通常是一个**非终结符**（nonterminal），而右侧是一个包含**终结符**（terminal）和非终结符的符号序列

终结符是文法中的基本符号，它们是最终的语言元素，例如编程语言中的关键字、标识符、常数等。

非终结符则是抽象符号，它们可以被替换为其他符号的序列，用于构建语言的结构。



### 2.2. 词汇语法

**词法结构**：词法结构指的是编程语言中的字符和符号如何被解释为词法单元（tokens）。在Java编程语言中，词法结构定义了如何将Unicode字符序列转换为一系列输入元素。

**输入元素**：词法结构中的产生式描述了如何将Unicode字符序列翻译成**输入元素**。输入元素是编程语言的基本组成部分，它们组成了语法结构的终结符。在Java编程语言中，输入元素经过去除空白和注释后，形成了语法结构的终结符，这些被称为**标记**（tokens）。

1. **标记（Tokens）**：标记是编程语言中的基本单元，它们是已识别和分析的输入元素。在Java编程语言中，**标记包括标识符、关键字、字面值、分隔符和操作符**。这些标记是编程语言中构建程序的构建块。
2. **标识符**：标识符是变量名、方法名或类名等程序中自定义的名称。它们用于标识程序中的各种实体。
3. **关键字**：关键字是Java编程语言中具有特殊含义的保留字，例如 `if`、`for`、`while` 等。它们用于定义控制结构和语言关键部分。
4. **字面值**：字面值是编程语言中的常量，例如字符串、数字、布尔值等。它们表示数据的固定值。
5. **分隔符**：分隔符是用于分隔不同程序元素的符号，例如分号 `;` 用于结束语句。
6. **操作符**：操作符是用于执行操作的符号，例如加法运算符 `+`。



## 3.词汇结构

### 3.1. 统一码

程序是使用 Unicode 字符集编写。Java 编程语言以 16 位代码单元序列表示文本， 使用 UTF-16 编码。

>为了只使用16位单元表示完整的字符范围，Unicode标准定义了一种编码称为UTF-16。

Unicode的合法代码点范围现在是从U+0000到U+10FFFF，使用十六进制的U+n表示法。

ASCII（ANSI X3.4）是美国信息交换标准代码。Unicode UTF-16编码的前128个字符就是ASCII字符。

>Java语言中除了注释（§3.7）、标识符（§3.8）以及字符字面量、字符串字面量和文本块（§3.10.4, §3.10.5, §3.10.6）的内容外，程序中的所有输入元素（§3.5）仅由ASCII字符（或Unicode转义符（§3.3），这些转义符会转换为ASCII字符）组成

#### ⚠ 扩展

##### 什么是国际化，为什么它很重要？

为了确保最终用户可以在世界各地使用他们的母语无缝地工作、协作、交流等，软件开发人员在所有软件和服务中部署了 Unicode 体系结构和标准。

国际化（又名“i18n”）是针对不同文化、地区或语言的目标受众设计和开发的产品。软件国际化意味着可以支持不同地区和语言的人类期望与应用程序交互的方式，包括文本、数字、日期等的呈现，以及用户界面的布局。

Unicode 建立了基础层（字符集、区域设置数据、算法），使设计能够同时处理所有语言和区域要求的代码成为可能，同时最大限度地减少对较低级别细节和怪癖干扰该设计的需要。

##### 什么是Unicode？

Unicode 联盟是软件和服务国际化的首要标准组织，包括所有现代计算系统的文本编码。该联盟通过提供核心库、软件算法和结构化数据来支持 [Unicode 标准的](https://www.unicode.org/versions/latest)国际化。

Unicode 标准是指表示**所有自然语言字符的标准字符集**。Unicode 最多可以编码**大约 110 万个字符**，使其能够在一个通用标准中支持世界上所有的语言和文字。

所有现代操作系统、计算环境、编程语言和应用程序都支持 Unicode 标准的核心。由于 Unicode 标准以统一的方式指定了所有语言的字符处理，因此它简化并启用了对区域设置敏感的 i18n 算法，否则这是不可能的，例如：

- 排序和字符串搜索/匹配
- 单词/句子边界检测和换行
- 从右到左的文本区域边界分析
- 数字、时间、列表和复数消息的格式

Unicode 数据和库还支持更多功能。更高级别的计算应用程序依靠这些功能来实现其目标。

##### 如何使用unicode

- 字符串表示：在大多数编程语言中，字符串通常使用 Unicode 来表示文本。可以直接在代码中使用字符，例如 `char`、`String`、`wchar_t`（在某些语言中），这些字符变量将使用 Unicode 编码。
- 转义序列：可以在字符串中使用 Unicode 转义序列来插入特定字符。在大多数编程语言中，Unicode 转义序列以 `\u` 开头，后跟 4 个十六进制数字来表示字符的 Unicode 代码点。例如，`\u0041` 表示字符 'A'。
- Unicode 字符库：Unicode 字符库通常包含了各种字符的详细信息，包括它们的名称、编码、属性等。可以使用这些信息来操作和分析文本。

```java
String omega = "\u03A9";
System.out.println(omega); // 输出：Ω
```

将 Unicode 转义序列 `\u03A9` 转换为 U+10FFFF 格式

1. 首先，将 `\u` 转义序列移除。
2. 获取转义序列中的十六进制数字（在这种情况下是 `03A9`）。
3. 将这些十六进制数字转换为十进制。
4. 最后，将转换后的十进制数表示为 U+ 后跟八位十六进制数字。

```
移除 \u，得到 03A9。

将 03A9 转换为十进制，得到 937。

最后，将它表示为 U+ 后跟八位十六进制数字，即 U+00000937。

所以，\u03A9 转换为 U+10FFFF 格式后是 U+00000937。
```

>在现代大多数编程语言中，字符串通常使用 Unicode 编码来表示文本。这意味着无论使用哪种编程语言编写代码，字符串变量通常会在内部以 Unicode 编码表示文本字符。
>
>这种做法使编程语言能够支持多语言和国际化，因为 Unicode 具有广泛的字符覆盖范围，可以表示各种不同语言的字符和符号。因此，开发人员可以编写能够处理和显示不同语言的应用程序，而不必担心字符编码的复杂性。

[Unicode官方网站](https://www.unicode.org/)

### 3.2. 词汇翻译

将原始的 Unicode 字符流转化成一系列标记（tokens）的过程:

**Unicode 转义的翻译**：

- 第一步是将原始的 Unicode 字符流中的 Unicode 转义序列（如 `\u1234`）翻译为对应的 Unicode 字符。
- 这种翻译使得任何程序都可以用只包含 ASCII 字符的方式来表示。
- 例如，`\u1234` 被翻译为相应的 Unicode 字符。

**Unicode 流到输入字符和行终止符的翻译**：

- 第二步是将第一步中得到的 Unicode 字符流翻译为输入字符和行终止符。这是为了更好地处理文本的格式化和结构。
- 输入字符和行终止符是编程语言中的基本构建块，用于构建程序代码。

**输入字符和行终止符到输入元素的翻译**：

- 第三步是将第二步中得到的输入字符和行终止符翻译为输入元素，这些元素在去除空格和注释后构成了编程语言的标记。
- 输入元素是语法规则中的终结符，它们构成了编程语言的基本结构。

>考虑以下原始的 Unicode 字符流：
>
>```
>This is an example: \u20AC \u0024
>```
>
>这个字符流包含了一些文本和 Unicode 转义序列。我们将按照上述步骤将其转化为输入元素。
>
>1. **Unicode 转义的翻译**：
>
>   - 首先，我们将 Unicode 转义序列 `\u20AC` 翻译为相应的 Unicode 字符，即欧元符号 €。同样，`\u0024` 被翻译为美元符号 $。
>
>2. **Unicode 流到输入字符和行终止符的翻译**：
>
>   - 此步骤将我们得到的 Unicode 字符流转化为输入字符和行终止符。在这个例子中，原始字符流没有显式的行终止符，因此这个步骤主要涉及字符的翻译。
>
>   - 我们得到以下输入字符序列：
>
>     ```vbnet
>     This is an example: € $
>     ```
>
>3. **输入字符和行终止符到输入元素的翻译**：
>
>   - 最后一步是将输入字符和行终止符转化为输入元素，这些元素构成了编程语言的标记。
>
>   - 在这个示例中，没有注释和额外的空格，所以输入元素将保留原始的输入字符，因此以下是输入元素序列：
>
>     ```vbnet
>     This
>     is
>     an
>     example:
>     €
>     $
>     ```

### 3.3. Unicode 转义

**Unicode 转义的转化规则**：

- 当处理带有 `\` 字符的输入时，以避免歧义，Java 编译器根据前一个字符的情况来决定是否继续处理 Unicode 转义。
  - 如果前一个字符是从一个 Unicode 转义序列中翻译而来，那么 `\` 字符后的 `u` 可以开始另一个 Unicode 转义。
  - 否则，Java 编译器将考虑连续出现的 `\` 字符的数量，如果数量是偶数，则 `\` 字符后的 `u` 可以开始一个新的 Unicode 转义；如果数量是奇数，则 `\` 字符后的 `u` 不会开始一个 Unicode 转义。
  - 如果一个可用于开始 Unicode 转义的 `\` 后不是 `u`，则它将被视为原始输入字符。

>简单解释：就是意味着在编程语言中只有'\u3014'，单个\后面跟u，才会被转义。其他都会被认为原始文本

### 3.4. 线路终止符

Java 编译器在处理 Unicode 输入字符时如何识别行终止符？

**行终止符**：

- 行终止符是一种特殊字符，用于表示行的结束。在 Java 编程语言中，行终止符可以有以下形式：
  - ASCII LF 字符，也被称为 "newline"（换行符）。
  - ASCII CR 字符，也被称为 "return"（回车符）。
  - ASCII CR 字符后跟 ASCII LF 字符（CR LF 组合）。

>**行分割**：
>
>- Java 编译器使用行终止符来将 Unicode 输入字符序列分割成多行。每当编译器遇到行终止符时，它将认为当前行的结束，并开始下一行。
>- 行终止符可以是 CR、LF 或 CR LF 的组合。两个字符 CR 立即跟随 LF 被视为一个行终止符，而不是两个独立的行终止符。

### 3.5. 注释

1. **传统注释（Traditional Comment）**：
   - 传统注释以 `/*` 开头，以 `*/` 结尾。在传统注释中，所有从 `/*` 到 `*/` 之间的文本都被忽略，这类似于C和C++中的注释形式。
2. **行尾注释（End-of-Line Comment）**：
   - 行尾注释以 `//` 开头，一直延续到该行的末尾。在行尾注释中，从 `//` 到行尾的文本都被忽略，这类似于C++中的注释形式。

>- 注释不支持嵌套。也就是说，在一个注释内部不能包含另一个注释。
>- 在传统注释中，`/*` 和 `*/` 本身没有特殊含义，它们只是用于标识注释的开始和结束。
>- 在行尾注释中，`//` 也没有特殊含义，它仅表示注释的开始。
>- 注释不会出现在字符文字（character literals）、字符串文字（string literals）或文本块（text blocks）中。

### 3.6. 标识符

Java编程语言中的标识符规则：

1. **标识符（Identifier）**：

   - 标识符是由Java字母（Java letters）和Java数字（Java digits）组成的无限长度序列，其中第一个字符必须是Java字母。

2. **标识符字符（IdentifierChars）**：

   - 标识符字符由Java字母后跟零个或多个Java字母或数字组成。

3. **Java字母（JavaLetter）**：

   - Java字母是指那些通过 `Character.isJavaIdentifierStart(int)` 方法返回true的Unicode字符。

     ```java
     System.out.println(Character.isJavaIdentifierStart(0x123));
     ```

4. **Java字母或数字（JavaLetterOrDigit）**：

   - Java字母或数字是指那些通过 `Character.isJavaIdentifierPart(int)` 方法返回true的Unicode字符。

>在Java中，Java字母包括ASCII大写字母A-Z（\u0041-\u005a）、小写字母a-z（\u0061-\u007a），以及出于历史原因的ASCII美元符号$（\u0024）和下划线_（\u005f）。美元符号通常只在自动生成的源代码或罕见情况下用于访问遗留系统中预先存在的名称。下划线可以用于由两个或更多字符组成的标识符，但由于它是一个关键字，不能用作一个字符的标识符。

### 3.9. 关键词

Java语言保留51字符序列，由`ASCII`字符组成作为关键字，不可标识符。

**关键字：**

```F#
abstract   continue   for          new         switch
assert     default    if           package     synchronized
boolean    do         goto         private     this
break      double     implements   protected   throw
byte       else       import       public      throws
case       enum       instanceof   return      transient
catch      extends    int          short       try
char       final      interface    static      void
class      finally    long         strictfp    volatile
const      float      native       super       while
_ (underscore)
```

**上下文关键字：**

```F#
exports      opens      requires     uses   yield
module       permits    sealed       var
non-sealed   provides   to           when
open         record     transitive   with
```

>关键字`const`和`goto`虽然保留，但当前未在Java中使用。这是为了帮助Java编译器在出现这些C++关键字的情况下提供更好的错误消息。
>
>关键字`strictfp`已过时，不应在新代码中使用

在将输入字符转化为输入元素（Input Elements）时，上下文关键字是如何被识别的？

**上下文关键字在特定语法上下文中被识别为终结符**，只有当以下两个条件都满足时，才将字符序列减少为上下文关键字：

- 该字符序列在合适的语法文法上下文中被识别为终结符。
- 该字符序列前后没有紧邻JavaLetterOrDigit字符（Java字母、Java数字）。

### 3.10. 文字
#### 3.10.1. 整数文字

1. 十进制整数字面值（DecimalIntegerLiteral）

   十进制整数字面值包括单个数字0，或一个非零数字后面跟随零个或多个数字，数字之间可以用下划线分隔。

2. 十六进制整数字面值（HexIntegerLiteral）

   十六进制整数字面值以`0x`或`0X`开头，后面跟随一或多个十六进制数字，数字之间可以用下划线分隔。十六进制数字由0到9和字母a到f（大小写不敏感）组成。

   ```F#
   0x15 = (十进制)21
   ```

3. 八进制整数字面值（OctalIntegerLiteral）

   八进制整数字面值以`0`开头，后面跟随一个或多个八进制数字，数字之间可以用下划线分隔。

   ```
   016 = (十进制)14
   ```

4. 二进制整数字面值（BinaryIntegerLiteral）

   二进制整数字面值以`0b`或`0B`开头，后面跟随一个或多个二进制数字0或1，数字之间可以用下划线分隔。

   ```
   0b111 = (十进制)7
   ```

类型的最大正十六进制、八进制和二进制文本 - 每个文本都表示十进制 值 （2^(31-1)） - 分别是：

- `十进制 2147483647`

- `0x7fff_ffff`,
- `0177_7777_7777`, 和
- `0b0111_1111_1111_1111_1111_1111_1111_1111`

最负的十六进制、八进制和二进制文本类型（每个文本都表示十进制值 （-231））是 分别：

- `十进制 -2147483648`

- `0x8000_0000`,
- `0200_0000_0000`, 和
- `0b1000_0000_0000_0000_0000_0000_0000_0000`



>**超出所表示的方位？**
>
>对于十进制来说：
>
>对于`int`类型，它的上限是2147483647，下限是-2147483648。如果一个整数值大于2147483647或小于-2147483648，那么它会发生整数溢出，溢出后的值会从最小值或最大值重新开始计算。
>
>对于`long`类型，它的上限是9223372036854775807，下限是-9223372036854775808。如果一个`long`整数值大于9223372036854775807或小于-9223372036854775808，同样会发生整数溢出，溢出后的值会从最小值或最大值重新开始计算。
>
>1. **十六进制（Hexadecimal）：**
>   - `int`类型的最大值为0x7fffffff，最小值为0x80000000。如果一个十六进制整数值大于0x7fffffff或小于0x80000000，它会发生整数溢出，重新从最小值或最大值开始计算。
>2. **八进制（Octal）：**
>   - `int`类型的最大值为017777777777，最小值为020000000000。如果一个八进制整数值大于017777777777或小于020000000000，同样会发生整数溢出，重新从最小值或最大值开始计算。
>3. **二进制（Binary）：**
>   - `int`类型的最大值为0b01111111111111111111111111111111，最小值为0b10000000000000000000000000000000。如果一个二进制整数值大于0b01111111111111111111111111111111或小于0b10000000000000000000000000000000，同样会发生整数溢出，重新从最小值或最大值开始计算

#### 3.10.2. 浮点文字

*浮点文字*包含以下部分： 整数部分、小数点或十六进制点（由 ASCII 句点字符）、分数部分、指数和类型后缀。

点文字可以用十进制（以 10 为基数）表示，或者 十六进制（以 16 为基数）。

1. 浮点字面值类型：
   - 如果一个浮点字面值后缀为ASCII字母F或f，则它的类型是float。
   - 否则，默认类型是double，也可以选择在后缀中添加D或d。
2. 浮点数类型：
   - float 类型对应 IEEE 754 binary32 浮点格式。
   - double 类型对应 IEEE 754 binary64 浮点格式。
3. float 类型的最大和最小正常数：
   - 最大正常数为 `(2 - 2^(-23)) * 2^127`，最短的十进制字面值是 `3.4028235e38f`，十六进制字面值是 `0x1.fffffeP+127f`。
   - 最小正常非零数为 `2^(-149)`，最短的十进制字面值是 `1.4e-45f`，两个十六进制字面值分别是 `0x0.000002P-126f` 和 `0x1.0P-149f`。
4. double 类型的最大和最小正常数：
   - 最大正常数为 `(2 - 2^(-52)) * 2^1023`，最短的十进制字面值是 `1.7976931348623157e308`，十六进制字面值是 `0x1.f_ffff_ffff_ffffP+1023`。
   - 最小正常非零数为 `2^(-1074)`，最短的十进制字面值是 `4.9e-324`，两个十六进制字面值分别是 `0x0.0_0000_0000_0001P-1022` 和 `0x1.0P-1074`。

如果非零浮点字面值太大或者太小，使得在转换为其内部表示时会成为IEEE 754无穷大、无穷小，那么将会产生编译时错误。

- Java提供了一些预定义的常量，用于表示特殊的浮点数值，包括正无穷大（POSITIVE_INFINITY）、负无穷大（NEGATIVE_INFINITY）以及NaN（不是一个数字）。
- 正无穷大和负无穷大用来表示浮点数中的正无穷大和负无穷大，通常出现在一些数学运算中，如除以零。
- NaN 用来表示浮点数运算中的无效结果，例如对负数开平方根或零除以零。NaN表示一个特殊的非数值状态。

>浮点数可以使用十进制表示为什么原因大家都懂，对于人来说比较好理解和处理。
>
>十六进制？是因为浮点数由整数、小数构成。表示的长度很长。而十六进制是最适合的，它由足够的空间表示。【十六进制中的每个数字（0-9 和 A-F）对应于4位二进制（0000-1111）。这种对应关系使得在内部表示和处理浮点数时更为方便，因为它们可以直接映射到二进制位】
>
>1. 十进制数 3.14 的单精度浮点表示：
>   - 十六进制表示：0x4048f5c3
>   - 解释：单精度浮点数通常包括32位，可以分为符号位、指数位和尾数位。0x4048f5c3中的0x表示十六进制，4048f5c3表示二进制位。符号位为0，指数位为10000000，尾数位为10011000111101011100011。
>2. 十进制数 -0.5 的双精度浮点表示：
>   - 十六进制表示：0xbfc0000000000000
>   - 解释：双精度浮点数通常包括64位，也包含符号位、指数位和尾数位。0xbfc0000000000000中的0xb表示十六进制，fc0000000000000表示二进制位。符号位为1，指数位为01111001111，尾数位为0000000000000000000000000000000000000000000000000。
>
>IEEE 754是一种广泛使用的标准，用于表示浮点数（包括单精度和双精度格式）在计算机系统中的二进制编码。
>
>1. 符号位（Sign Bit）：最高位（最左边的位）用于表示浮点数的符号，0表示正数，1表示负数。
>2. 指数位（Exponent Bits）：接下来的一些位用于表示指数，控制浮点数的幂。这部分位通常包括一个偏移值（一般为127），以便表示正数和负数指数。单精度浮点数通常使用8位来表示指数，而双精度浮点数使用11位。
>3. 尾数位（Significand or Mantissa Bits）：剩下的位用于表示尾数，即浮点数的小数部分。这部分位表示浮点数的精度和有效数字。
>4. 规范化：IEEE 754要求浮点数的表示进行规范化，这意味着尾数的最高位通常为1，因此浮点数的值总是以1.xxx形式的二进制数表示，其中xxx代表尾数的其余部分。
>5. 偏移码（Bias）：IEEE 754中使用一个偏移值来表示指数，使得正数和负数指数都可以表示。对于单精度浮点数，偏移码通常是127，对于双精度浮点数，通常是1023。
>6. 特殊值：IEEE 754定义了特殊的浮点数值，如正无穷大、负无穷大、NaN（非数字）等。这些特殊值可以用于处理异常情况和边界条件。
>7. 舍入规则：IEEE 754还定义了浮点数的舍入规则，以确定在进行浮点数运算时如何处理舍入误差。
>
>```java
>//TODO 后续补充相关计算方式，或者读者自己查阅相关文档
>```
>
>```java
>		// 单精度浮点数表示
>		float singlePrecision = -7.75f; // 在数字后面添加"f"表示单精度浮点数
>        int singleBits = Float.floatToIntBits(singlePrecision);
>        String singleBitsStr = Integer.toBinaryString(singleBits);
>        System.out.println("单精度IEEE 754表示：" + singleBitsStr);
>//单精度IEEE 754表示：11000000111110000000000000000000
>        // 双精度浮点数表示
>        double doublePrecision = -7.75;
>        long doubleBits = Double.doubleToLongBits(doublePrecision);
>        String doubleBitsStr = Long.toBinaryString(doubleBits);
>        System.out.println("双精度IEEE 754表示：" + doubleBitsStr);
>//双精度IEEE 754表示：1100000000011111000000000000000000000000000000000000000000000000
>```

#### 3.10.3. 布尔文字

1. 布尔类型：
   - 布尔类型是Java中的一种基本数据类型，它只有两个可能的值，即true和false。
2. 布尔字面值：
   - 布尔字面值是用于表示布尔类型的值的方式。
   - 在Java中，只有两个合法的布尔字面值：true和false。
   - 这些字面值是由ASCII字母组成，不区分大小写，可以写作"true"或"false"。
3. 类型：
   - 布尔字面值的类型始终是boolean类型，这是Java语言规范中规定的。
   - 这意味着，无论你在代码中使用true还是false，它们都属于boolean类型，不需要显式地指定类型。

#### 3.10.4. 字符文字

1. 字符字面值的表示：
   - 字符字面值可以用字符或转义序列表示，它们需要被封装在ASCII单引号中，即`'`。
   - 例如，'a'、'%'、'\t'、'\'、'''、'\u03a9' 等都是字符字面值的示例。
2. 字符类型：
   - 字符字面值的类型始终是char，这是Java语言规范中规定的。
   - 无论字符字面值表示的是一个字符还是一个转义序列，它们都属于char类型。
3. 字符字面值的内容：
   - 字符字面值的内容是单个字符或一个转义序列，这些位于单引号`'`之间。
   - 在字符字面值内部，只能包含一个字符或一个合法的转义序列。
4. 编译时错误：
   - 如果字符字面值的内容后面不是单引号`'`，将会导致编译时错误。
   - 如果在单引号`'`之间存在换行符（line terminator），也会导致编译时错误。
5. Unicode转义序列：
   - Java允许在字符字面值中使用Unicode转义序列，如'\u03a9'和'\uFFFF'。
   - 这些转义序列在编译时会被解释为相应的Unicode字符。
6. 字符字面值的范围：
   - 字符字面值只能表示UTF-16代码单元，范围从'\u0000'到'\uffff'。
7. 处理特殊字符：
   - 不能直接使用换行符（LF）或回车符（CR）作为字符字面值，因为它们会被转义处理。
   - 换行符应该使用'\n'的转义序列，回车符应该使用'\r'的转义序列。
8. 在Java中，字符字面值始终表示一个字符，不像C和C++中可能表示多个字符。

#### 3.10.5. 字符串文字

*字符串文本*由零个或多个组成 用双引号括起来的字符。换行符等字符 可以用转义序列表示。

- 字符串字面值的类型始终是String，这是Java语言规范中规定的。

- 字符串字面值的内容是从开头的双引号`"`到相匹配的闭合双引号`"`之间的字符序列。
- 在字符串字面值中，转义序列会被解释为相应的字符。
- 所有相同内容的字符串字面值引用相同的String实例，**这是通过字符串字面值的"interning"实现的**。
- 字符串字面值以及常量表达式的值都会在编译时进行计算和共享，以减少内存使用。

```java
package testPackage;
class Test {
    public static void main(String[] args) {
        String hello = "Hello", lo = "lo";
        System.out.println(hello == "Hello");
        System.out.println(Other.hello == hello);
        System.out.println(other.Other.hello == hello);
        System.out.println(hello == ("Hel"+"lo"));
        System.out.println(hello == ("Hel"+lo));
        System.out.println(hello == ("Hel"+lo).intern());
    }
}
class Other { static String hello = "Hello"; }
```

```wiki
true
true
true
true
false
true
```

>- 同一类和包中的字符串文本 表示对同一对象的引用 （[第 4.3.1 节](https://docs.oracle.com/javase/specs/jls/se21/html/jls-4.html#jls-4.3.1)）。`String`
>- 同一类中不同类中的字符串文字 package 表示对同一对象的引用。`String`
>- 不同类中的字符串文字 不同的包同样表示对同一对象的引用。`String`
>- 从常量表达式连接的字符串 （[§15.29](https://docs.oracle.com/javase/specs/jls/se21/html/jls-15.html#jls-15.29)） 在编译时计算，并且 然后将它们视为字面意思。
>- **在运行时通过串联计算的字符串 是新创建的，因此是不同的。**
>- 显式嵌套计算的结果 string 是与任何预先存在的字符串相同的对象 具有相同内容的文字。`String`



#### 3.10.6. 文本块

*文本块*由零个或多个字符组成 由打开和关闭分隔符括起来。

```Java
class Test {
   public static void main(String[] args) {
        // The six characters w i n t e r
        String season = """
                        winter""";

        // The seven characters w i n t e r LF
        String period = """
                        winter
                        """;

        // The ten characters H i , SP " B o b " LF
        String greeting = """
                          Hi, "Bob"
                          """;

        // The eleven characters H i , LF SP " B o b " LF
        String salutation = """
                            Hi,
                             "Bob"
                            """;

        // The empty string (zero length)
        String empty = """
                       """;

        // The two characters " LF
        String quote = """
                       "
                       """;

        // The two characters \ LF
        String backslash = """
                           \\
                           """;
    }
}
```

>文本块的类型始终为String类型。并且开始分隔符是以`"""`开始，由一个**换行符**（必须存在）、多个控制、制表符、表单源字符以及末尾的`"""`
>
>使用转义序列 和 分别表示换行符和双引号字符， 允许在文本块中使用
>
>使用该特性需要 Java Level15 以上

文本块总是引用*类的同一*实例。这是因为 `文本块`- 或者更一般地说，是常量值的字符串表达式。

```java
        System.out.println("ab" + """
                        \tcde
                          """);
//输出 ab	cde
        String cde = """
             abcde""".substring(2);
//输出 cde
        String math = """
              1+1 equals \
              """ + String.valueOf(2);
//输出 1+1 equals 2
```

#### 3.10.7. 转义序列

在字符文本、字符串文本和文本块中,*转义序列*允许表示一些非图形字符，而无需使用Unicode转义。

```F#
\b：退格字符（Unicode \u0008）
\s：空格字符（Unicode \u0020）
\t：水平制表符（Unicode \u0009）
\n：换行字符（Unicode \u000a）
\f：换行符字符（Unicode \u000c）
\r：回车符字符（Unicode \u000d）
"：双引号字符（Unicode \u0022）
'：单引号字符（Unicode \u0027）
\：反斜杠字符
```

```java
String backspace = "This is a backspace: \b";
String space = "This is a space: \s";
String tab = "This is a tab: \t";
String newline = "This is a newline: \n";
String formFeed = "This is a form feed: \f";
String carriageReturn = "This is a carriage return: \r";
String doubleQuote = "This is a double quote: \"";
String singleQuote = "This is a single quote: '";
String backslash = "This is a backslash: \\";
String octalEscape = "This is an octal escape: \101";
String multilineText = """
    This is a long piece of text that spans multiple lines and is \
    much easier to read thanks to the line continuation escape.
    """;
```

>1. 八进制转义序列：
>   - 八进制转义序列由反斜杠后面跟着一个或多个八进制数字组成，用于表示特定的字符。
>   - 八进制数字范围从0到7。
>   - 八进制转义序列的范围为\000到\377（对应Unicode \u0000 到 \u00ff）。
>2. 行继续转义：
>   - 行继续转义序列用于表示多行文本块的行继续，以使文本块更易读。
>   - 行继续转义序列由反斜杠和换行符组成。
>3. 行继续转义序列可以出现在文本块中，但不能出现在字符文本或字符串文本中，因为每个都不允许LineTerminator（行终止符）。



#### 3.10.8. 空文字

- NullLiteral（null 字面量）的语法非常简单，只需使用关键字 `null` 即可。

- null 字面量永远属于 null 类型，这是 Java 中的一个引用类型。

- 当您将一个变量或表达式初始化为 `null` 时，它表示这个变量或表达式不引用任何对象。

- null 字面量通常用于初始化引用变量，以表示它们不指向任何对象。例如：

  ```java
  String str = null; // 初始化 str 为 null
  ```

- 当您尝试使用一个空引用来访问对象的成员（例如方法或字段），将会引发 `NullPointerException` 异常，因此在使用引用之前，通常需要检查它是否为 null。

- null 字面量在编程中用于表示缺少对象或尚未分配对象的情况，以便编写更灵活的代码。

- null 字面量不同于空字符串 `""` 或数字 `0`，它们分别表示空字符串和零值。

- 在 Java 中，null 是一个关键字，不是标识符，因此不能用作变量名或方法名。



#### 3.11. 分隔符

由 ASCII 字符组成的 12 个标记是 *分隔符*（标点符号）。

分隔符：

（其中之一）

```
(   )   {   }   [   ]   ;   ,   .   ...   @   ::
```



#### 3.12. 操作符

38个操作符， 由 ASCII 字符组成的*是运算符*。

操作符：

（其中之一）

```
=   >   <   !   ~   ?   :   ->
==  >=  <=  !=  &&  ||  ++  --
+   -   *   /   &   |   ^   %   <<   >>   >>>
+=  -=  *=  /=  &=  |=  ^=  %=  <<=  >>=  >>>=
```



## 4.类型、值和变量

Java 编程语言是 *一种静态类型语言*，这意味着 每个变量和每个表达式都有一个已知的类型 编译时。

>这意味着在编译时每个变量和表达式都必须具有已知的类型。
>
>每个变量都需要显式地声明其数据类型，编译器会检查类型是否匹配，并在编译时进行类型检查。

### 4.1. 类型和值的种类
3578931
有 Java 编程语言中的两种类型：

- 原始类型：包括整数类型（如int、byte、short、long）、浮点类型（如float、double）、字符类型（char）和布尔类型（boolean）。原始类型的变量存储的是实际的数据值。
- 引用类型：包括类（class）、接口（interface）、数组（array）、枚举（enum）等。引用类型的变量存储的是对对象的引用，而不是对象本身。对象通常存储在堆内存中，而引用存储在栈内存中。
- **null类型** 是一个特殊的类型，通常用于表示一个引用类型的变量没有引用任何对象。在Java中，任何引用类型的变量都可以赋予null值，表示它不引用任何对象。

### 4.2. 原始类型和值

#### 4.2.1. 整数类型和值

- 对于`byte`：范围为-128到127，包括边界值。`byte`是一个8位数据类型。
- 对于`short`：范围为-32768到32767，包括边界值。`short`是一个16位数据类型。
- 对于`int`：范围为-2147483648到2147483647，包括边界值。`int`是一个32位数据类型。
- 对于`long`：范围为-9223372036854775808到9223372036854775807，包括边界值。`long`是一个64位数据类型。
- 对于`char`：范围为'\u0000'到'\uffff'，包括边界值，对应整数值从0到65535。`char`表示一个16位Unicode字符。

#### 4.2.2. 整数运算

1. 比较操作符，产生布尔结果：
   - `<`、`<=`、`>`、`>=` 用于数字比较。
   - `==` 和 `!=` 用于数字相等比较。
2. 数值操作符，产生 int 或 long 结果：
   - 一元加法和减法操作符：`+` 和 `-`。
   - 乘法操作符：`*`、`/`、`%`。
   - 加法操作符：`+`、`-`。
   - 自增操作符：`++`（前缀和后缀）。
   - 自减操作符：`--`（前缀和后缀）。
   - 带符号和无符号位移操作符：`<<`、`>>`、`>>>`。
   - 按位非操作符：`~`。
   - 整数位运算操作符：`&`、`^` 和 `|`。
   - 条件操作符：`? :`。
3. 这些操作符的行为由操作数的类型决定：
   - 如果任何操作数是 long 类型，操作将以64位精度执行，结果将是 long 类型。
   - 如果一个或两个操作数不是 long 类型，数值提升将使它们扩展为 int。
   - 整数操作符没有溢出或下溢指示。
4. 整数操作符可以在以下条件下抛出异常：
   - 如果需要对 null 引用进行拆箱转换，将会抛出 NullPointerException。
   - 如果整数除法操作符 `/` 和求余操作符 `%` 的右操作数为零，则可能抛出 ArithmeticException。
   - 如果需要进行装箱转换，但没有足够的内存可用来执行转换，可能会出现 OutOfMemoryError。

```java
class Test {
    public static void main(String[] args) {
        int i = 1000000;
        System.out.println(i * i);
        long l = i;
        System.out.println(l * l);
        System.out.println(20296 / (l - i));
    }
}
//-727379968
//1000000000000
//Exception in thread "main" java.lang.ArithmeticException: / by zero
//	at com.qiaose.fourAndOne.four.main(four.java:10)
```



#### 4.2.3. 浮点类型和值

1. Java中有两种浮点类型：`float`和`double`，分别对应32位和64位IEEE 754浮点格式。从Java SE 15开始，采用2019年版的IEEE 754标准。

2. 浮点类型包括正数、负数、零、正无穷大、负无穷大和特殊的NaN（Not-a-Number）值。NaN用于表示某些无效操作，例如将0.0除以0.0。在Java中，NaN值可以用`Float.NaN`和`Double.NaN`常量表示。

   ```java
   double result = 0.0 / 0.0;
   System.out.println(result); // 输出 NaN
   ```

3. IEEE 754标准允许每个NaN值具有多个不同的NaN位模式，但Java通常将它们处理为单一规范值。

   >IEEE 754标准允许每种二进制32和二进制64浮点格式中的NaN值具有多个不同的NaN位模式。但是，Java SE平台通常将给定浮点类型的NaN值视为单一的规范值，因此本规范通常将任意NaN值视为规范值。

4. 正零和负零相等，所以表达式0.0==-0.0的结果为true，表达式0.0>-0.0的结果为false。其他操作可以区分正零和负零；例如，1.0/0.0的值为正无穷大，而1.0/-0.0的值为负无穷大。

   NaN是无序的，因此：

   - 数值比较运算符<、<=、>和>=如果其中一个或两个操作数为NaN，则返回false（§15.20.1）。
   - 特别是，如果x或y为NaN，则(x<y) == !(x>=y)为false。
   - 等号运算符==如果其中一个操作数为NaN，则返回false。
   - 不等号运算符!=如果其中一个操作数为NaN，则返回true（§15.21.1）。
   - 特别是，x!=x为true当且仅当x为NaN。

#### 4.2.4. 浮点运算

1. 如果二元运算符的至少一个操作数是浮点类型，那么该操作将是浮点操作，即使另一个操作数是整数也一样。
2. 如果至少一个操作数是double类型的数值运算符，那么操作将使用64位浮点算术执行，操作的结果将是double类型的值。如果另一个操作数不是double，则会通过数值提升（numeric promotion）将其转换为double类型。
3. 至少一个操作数是float类型，操作将使用32位浮点算术执行，操作的结果将是float类型的值。如果另一个操作数不是float，则会通过数值提升将其转换为float类型。
4. 任何浮点类型的值都可以转换为任何数值类型。不允许浮点类型与布尔类型之间进行强制类型转换。
5. 浮点运算符可能会因以下原因抛出异常：
   - 任何浮点运算符如果需要对null引用进行拆箱转换（unboxing conversion）时，可能会抛出NullPointerException。
   - 自增和自减运算符++和--在需要进行装箱转换（boxing conversion）时，如果内存不足以执行转换，则可能会抛出OutOfMemoryError。

```java
rint("overflow produces infinity: ");
        System.out.println(d + "*10==" + d*10);
        // An example of gradual underflow:
        d = 1e-305 * Math.PI;
        System.out.print("gradual underflow: " + d + "\n   ");
        for (int i = 0; i < 4; i++)
            System.out.print(" " + (d /= 100000));
        System.out.println();
        // An example of NaN:
        System.out.print("0.0/0.0 is Not-a-Number: ");
        d = 0.0/0.0;
        System.out.println(d);
        // An example of inexact results and rounding:
        System.out.print("inexact results with float:");
        for (int i = 0; i < 100; i++) {
            float z = 1.0f / i;
            if (z * i != 1.0f)
                System.out.print(" " + i);
        }
        System.out.println();
        // Another example of inexact results and rounding:
        System.out.print("inexact results with double:");
        for (int i = 0; i < 100; i++) {
            double z = 1.0 / i;
            if (z * i != 1.0)
                System.out.print(" " + i);
        }
        System.out.println();
        // An example of cast to integer rounding:
        System.out.print("cast to int rounds toward 0: ");
        d = 12345.6;
        System.out.println((int)d + " " + (int)(-d));
    }
}
```

结果：

```java
overflow produces infinity: 1.0E308*10==Infinity
gradual underflow: 3.141592653589793E-305
    3.1415926535898E-310 3.141592653E-315 3.142E-320 0.0
0.0/0.0 is Not-a-Number: NaN
inexact results with float: 0 41 47 55 61 82 83 94 97
inexact results with double: 0 49 98
cast to int rounds toward 0: 12345 -12345
```

#### 4.2.5. 类型boolean和布尔值

- 布尔运算符有关于关系运算符（==，!=），逻辑非运算符（!），逻辑运算符（&，|，^），条件与或运算符（&&，||），条件运算符（? :)，和字符串连接运算符（+）。

- 值可以通过字符串转换（String.valueOf）转换为布尔值。

- 值可以通过类型转换（强制转换）转换为布尔类型。

>1. 字符串转换：使用`String.valueOf`或者字符串的比较操作等方法将值转换为布尔类型时，需要确保字符串的内容符合布尔值的语义。通常，字符串"true"（不区分大小写）会被转换为`true`，而字符串"false"会被转换为`false`。其他字符串内容可能会引发`IllegalArgumentException`或返回`false`。
>2. 类型转换：使用强制类型转换将值转换为布尔类型时，需要确保值的类型与布尔类型兼容。通常，非零整数值会被转换为`true`，而零会被转换为`false`。引用类型（对象引用）通常会被转换为`true`，除非引用为`null`，此时会被转换为`false`。确保转换的值在布尔上下文中具有明确的含义。



### 4.3. 引用类型和值

- 引用类型分为四种种类：类类型（Class Types），接口类型（Interface Types），类型变量（Type Variables），数组类型（Array Types）。

- 如果在类或接口类型中出现类型参数，那么它就是一个参数化类型（Parameterized Type）。参数化类型是一种带有类型参数的类或接口类型。

  ```java
  class List<T> {
      private T[] elements;

      public List(int size) {
          elements = (T[]) new Object[size];
      }

      public void add(T element) {
          // 添加元素的逻辑
      }

      public T get(int index) {
          // 获取元素的逻辑
          return elements[index];
      }
  }

  ```

  >在上面的代码中，`List<T>` 是一个参数化类型，`<T>` 表示类型参数。这意味着你可以在创建 `List` 实例时指定具体的数据类型，例如 `List<Integer>` 或 `List<String>`。这使得 `List` 可以根据需要存储不同类型的元素，而无需为每种数据类型创建不同的类。

- 标识符在类或接口类型中可以被分类为包名（Package Name）或类型名（Type Name）。如果一个类或接口类型以T.id的形式出现（可以后跟类型参数），**那么id必须是T的可访问成员类型的简单名称**。成员类型可以是嵌套在T中的类或接口类型。这个类或接口类型表示T的成员类型。

  ```java
  class Outer {
      class Inner {
          void innerMethod() {
              System.out.println("Inner method");
          }
      }
  }

  public class Main {
      public static void main(String[] args) {
          Outer outer = new Outer();
          Outer.Inner inner = outer.new Inner();
          inner.innerMethod();
      }
  }
  ```



#### 4.3.1. 对象

在Java中，对象是类实例或数组的实例。

在Java中，可以使用以下操作符处理引用到对象的引用:

- **Field access:** 通过限定名（qualified name）或字段访问表达式（field access expression），可以访问对象的字段（类似成员变量）
- **Method invocation:** 可以调用对象的方法，这是通过使用点号（.）来访问方法和传递参数
- **Cast operator:** 可以使用强制类型转换操作符来将引用从一种类型转换为另一种类型，比如从子类引用到父类引用
- **String concatenation operator:** 可以使用+操作符来将字符串和引用连接起来。如果其中一个操作数是String，另一个操作数将隐式地通过调用被引用对象的`toString`方法转换为String（如果引用或`toString`的结果是null，将使用字符串"null"），然后生成一个新的String，它是两个字符串的连接
- **Instanceof operator:** 通过`instanceof`操作符，可以检查对象是否是特定类型的实例
- **Reference equality operators:** 使用`==`和`!=`操作符可以检查两个引用是否引用同一个对象（不过一般不推荐，通过我们会重写equals方法来进行两个对象的判断）
- **Conditional operator:** 使用`? :`条件运算符来根据条件选择不同的值

>可以有多个引用指向同一个对象。如果两个变量包含对同一对象的引用，那么可以使用一个变量的引用修改对象的状态，然后可以通过另一个变量中的引用观察到已更改的状态。



#### 4.3.2. Object

Object类中定义的主要方法包括：

1. **clone方法:** 用于创建对象的副本。通过调用该方法，可以复制一个对象，以便获得一个具有相同状态的新对象。
2. **equals方法:** 定义了对象的相等性概念，它基于值而不是引用进行比较。通过覆盖这个方法，可以自定义两个对象何时被认为是相等的。
3. **finalize方法:** 在对象销毁之前（垃圾回收时）运行。它允许在对象被销毁之前执行一些清理或资源释放操作。
4. **getClass方法:** 用于获取表示对象所属类的Class对象。每个引用类型都有一个对应的Class对象，它可以用于获取类的完全限定名称、成员信息、直接超类信息以及它实现的任何接口信息。
5. **hashCode方法:** 通常与equals方法一起使用，对于在像java.util.HashMap这样的哈希表中查找对象非常有用。hashCode方法用于返回对象的哈希码，以便在哈希表中进行快速查找。
6. **wait, notify和notifyAll方法:** 用于多线程编程中的并发控制。这些方法允许线程在等待某些条件成立时进入休眠状态，并在其他线程满足这些条件时唤醒它们。
7. **toString方法:** 返回对象的字符串表示。通常，这个方法会返回一个包含对象状态信息的字符串，以便于调试和日志记录。

#### 4.3.3. String

**String类**表示Unicode码点的序列，它的特点包括：

- String对象的值是**常量**，一旦创建，它的值不能被修改。这意味着字符串是不可变的。
- 字符串字面值（String literals）和文本块（text blocks）都是对String类实例的引用。这些实例用于存储字符串的内容。
- 字符串拼接操作符+（string concatenation operator +）用于将多个字符串连接在一起，创建一个新的String对象。如果拼接操作的结果不是常量表达式，它将隐式创建一个新的String对象。



#### 4.3.4. 当引用类型相同时

如果出现以下情况，则两个引用类型*是相同的编译时类型* 它们在与 相同的模块， 并且它们具有相同的二进制名称， 并且它们的类型参数（如果有）是相同的，应用此 递归定义。

当两种引用类型相同时，有时称它们为 *同一类*或*相同 接口*。

在运行是，即使是有着相同二进制名称的多个引用类型。但是，由于由不同的类装入器同时加载时，这些类型可能或 不能表示相同的类型声明。即使两种这样的类型确实如此 表示相同的类型声明，它们被认为是不同的。

如果存在一下情况，我们则认为它们时相同的运行时类型：

- 它们都是类或两种接口类型，由 相同的类装入器，并具有相同的二进制名称
- 它们都是数组类型，它们的组件类型是 相同的运行时类型



### 4.4. 类型变量

*类型变量*是使用的非限定标识符 作为类、接口、方法和构造函数体中的类型。

>当你创建一个泛型类、接口、方法或构造函数时，你可以定义一个类型参数（类型变量），并在这些泛型实体的内部使用它，以表示未知的类型。

**示例 1: 泛型类**

```java
class Box<T> {
    private T value;

    public Box(T value) {
        this.value = value;
    }

    public T getValue() {
        return value;
    }
}

public class Main {
    public static void main(String[] args) {
        Box<Integer> intBox = new Box<>(42);
        Box<String> strBox = new Box<>("Hello, Java!");

        System.out.println(intBox.getValue()); // 输出: 42
        System.out.println(strBox.getValue()); // 输出: Hello, Java!
    }
}
```

在这个示例中，`Box` 类使用类型参数 `T` 来表示一个通用的盒子，可以存储不同类型的值。

**示例 2: 泛型方法**

```java
class MathUtils {
    public static <T extends Number> double add(T a, T b) {
        return a.doubleValue() + b.doubleValue();
    }
}

public class Main {
    public static void main(String[] args) {
        int sumInt = MathUtils.add(3, 5);
        double sumDouble = MathUtils.add(2.5, 4.7);

        System.out.println(sumInt);    // 输出: 8.0
        System.out.println(sumDouble); // 输出: 7.2
    }
}
```

在这个示例中，`MathUtils` 类中的 `add` 方法使用类型参数 `T`，并通过限制 `T` 必须是 `Number` 或其子类来确保输入的参数是数字类型。

Java中的类型参数和界限（bounds）的使用：

```java
package TypeVarMembers;

class C {
    public    void mCPublic()    {}
    protected void mCProtected() {}
              void mCPackage()   {}
    private   void mCPrivate()   {}
}

interface I {
    void mI();
}

class CT extends C implements I {
    public void mI() {}
}

class Test {
    <T extends C & I> void test(T t) {
        t.mI();           // OK
        t.mCPublic();     // OK
        t.mCProtected();  // OK
        t.mCPackage();    // OK
        t.mCPrivate();    // Compile-time error
    }
}
```

>1. `C` 类定义了四个不同的方法，分别具有不同的访问修饰符（public、protected、package-private、private）。
>2. `I` 接口定义了一个名为 `mI` 的抽象方法。
>3. `CT` 类继承自 `C` 类并实现了 `I` 接口，因此它继承了 `C` 类的方法和实现了 `I` 接口的方法。
>4. 在 `Test` 类中，有一个泛型方法 `test`，它接受一个类型参数 `T`，并且要求 `T` 必须是 `C` 类和 `I` 接口的子类型，即 `T extends C & I`。
>5. 在 `test` 方法中，可以调用 `t.mI()`，因为 `T` 必须是 `I` 接口的子类型，所以 `mI` 方法是有效的。
>6. 同样，可以调用 `t.mCPublic()`、`t.mCProtected()` 和 `t.mCPackage()` 方法，因为这些方法是从 `C` 类继承而来，而 `T` 必须是 `C` 类的子类型。
>7. 但是，不能调用 `t.mCPrivate()` 方法，因为这个方法是 `C` 类的私有方法，而私有方法在子类中不可见，因此会导致编译时错误。



### 4.5. 参数化类型

Java中的参数化类型（Parameterized Types）和泛型（Generics）的概念:

1. **参数化类型是什么？**

   在Java中，参数化类型是泛型类或接口的实例，它们具有一个特定的参数化，其中参数是类型。通常，参数化类型的形式为 `C<T1, ..., Tn>`，其中 `C` 是泛型类或接口的名称，而 `<T1, ..., Tn>` 是一组类型参数，它们表示泛型类或接口的特定参数化。

2. 泛型类或接口可以定义类型参数，如 `F1, ..., Fn`，并为这些类型参数指定界限（bounds），如 `B1, ..., Bn`。每个类型参数 `Ti` 可以是所有在相应界限中列出的类型的子类型。

>**示例**
>
>下面是一些示例来说明上述概念：
>
>- 合法的参数化类型：
>  - `Seq<String>`：Seq 是一个泛型类，参数化为 String 类型。
>  - `Seq<Seq<String>>`：Seq 参数化为 Seq<String> 类型。
>  - `Seq<String>.Zipper<Integer>`：Seq 参数化为 String，而 Zipper 参数化为 Integer。
>  - `Pair<String, Integer>`：Pair 是一个泛型类，参数化为 String 和 Integer 类型。
>- 不合法的参数化类型：
>  - `Seq<int>`：不能使用原始数据类型作为类型参数。
>  - `Pair<String>`：参数数量不匹配。
>  - `Pair<String, String, String>`：参数数量不匹配。
>- 嵌套的参数化类型示例：
>  - 如果非泛型类 `C` 包含具有一个类型参数的泛型成员类 `D`，那么 `C.D<Object>` 是一个参数化类型。
>  - 如果泛型类 `C` 具有一个类型参数，并且包含非泛型成员类 `D`，那么成员类类型 `C<String>.D` 也是一个参数化类型。

#### 4.5.1. 参数化类型的类型参数

1. **Type Arguments of Parameterized Types（参数化类型的类型参数）**：

   在Java中，参数化类型如 `C<T>` 可以包含类型参数 `T`，它表示特定的类型。这些类型参数可以是引用类型，也可以是通配符。通常，类型参数用于定制参数化类型的行为，从而实现更灵活和通用的代码。

2. **TypeArguments（类型参数列表）**：

   参数化类型的类型参数位于尖括号内，形式为 `<TypeArgumentList>`。类型参数之间使用逗号 `,` 分隔。

3. **TypeArgument（类型参数）**：

   类型参数可以是引用类型（ReferenceType）或通配符（Wildcard）。

4. **Wildcard（通配符）**：

   通配符是一种特殊的类型参数，通常在我们只需要对类型参数有部分了解的情况下使用。通配符可以帮助我们处理更广泛的类型范围，而不需要关心具体的类型。

5. **WildcardBounds（通配符边界）**：

   通配符可以具有边界，使用 `extends` 和 `super` 关键字。`extends` 表示通配符的上限，即它必须是某个类型的子类型。`super` 表示通配符的下限，即它必须是某个类型的父类型。

>**示例**：
>
>- 类型参数的示例：
>  - `List<Integer>`：这是一个参数化类型，参数为 `Integer` 类型。
>  - `Pair<String, Double>`：这是另一个参数化类型，参数为 `String` 和 `Double` 类型。
>- 通配符的示例：
>  - `List<?>`：这是一个通配符，表示我们对列表的元素类型不做特定要求。
>  - `List<? extends Number>`：这是带有上限边界的通配符，表示元素类型必须是 `Number` 的子类型。

***通配符：***

通配符是用于泛型类型的一种特殊语法，它可以用于表示泛型类型的类型参数，同时可以设置上限（extends）和下限（super）。

**通配符的上限和下限**：

- 通配符可以具有上限，使用 `? extends B` 表示，其中 `B` 是上限类型。这表示通配符的类型参数必须是 `B` 类型或其子类型。

  ```java
  public static double sumOfList(List<? extends Number> list) {
      double sum = 0.0;
      for (Number n : list) {
          sum += n.doubleValue();
      }
      return sum;
  }
  ```

  >上界通配符用于在方法中读取泛型集合的值，因为你知道它们至少是 `T` 类型的。

- 通配符还可以具有下限，使用 `? super B` 表示，其中 `B` 是下限类型。这表示通配符的类型参数必须是 `B` 类型或其超类型。

  ```java
  public static void addNumbers(List<? super Integer> list) {
      list.add(42);
  }
  ```

  >下界通配符用于在方法中写入泛型集合的值，因为你知道它们至少是 `T` 类型的。

**通配符的背景**：

通配符在Java中是一种受限制的存在类型（existential types）的形式。它们可以用于处理泛型类型的子类型关系和限制。



如何使用无界通配符（Unbounded Wildcards）来创建更通用的方法:

```java
import java.util.ArrayList;
import java.util.Collection;

class Test {
    static void printCollection(Collection<?> c) {
                                // a wildcard collection
        for (Object o : c) {
            System.out.println(o);
        }
    }

    public static void main(String[] args) {
        Collection<String> cs = new ArrayList<String>();
        cs.add("hello");
        cs.add("world");
        printCollection(cs);
    }
}
```

>`printCollection` 方法：这是一个静态方法，它接受一个参数 `Collection<?> c`，这个参数使用了无界通配符 `<?>`。这意味着该方法可以接受任何类型的集合，而不仅仅是特定类型的集合。



如何使用有界通配符（Bounded Wildcards）来创建更通用的方法:

```java
public static <T extends Comparable<T>> T findMax(List<T> list) {
    if (list.isEmpty()) {
        throw new IllegalArgumentException("List is empty");
    }

    T max = list.get(0);
    for (T item : list) {
        if (item.compareTo(max) > 0) {
            max = item;
        }
    }

    return max;
}
//我们使用上界通配符 `T extends Comparable<T>`，它要求传入的泛型列表中的元素必须实现了 `Comparable` 接口。这使得 `findMax` 方法能够比较元素并找到最大值，而不仅仅是任何类型的元素。这样的方法更通用，因为它不仅适用于特定类型，而且适用于任何实现了 `Comparable` 接口的类型。


public static void copyElements(List<? super Integer> dest, List<Integer> source) {
    for (Integer item : source) {
        dest.add(item);
    }
}
//我们使用下界通配符 `? super Integer`，这允许 `dest` 列表接受 `Integer` 类型及其父类型的元素。这使得方法更通用，因为它可以将 `source` 列表的元素复制到不同类型的目标列表中，只要目标列表的元素类型是 `Integer` 的父类型。

```

### 4.6. 类型擦除

ava中的类型擦除（Type Erasure）是一种编译器技术，用于处理泛型类型（parameterized types）和泛型方法（generic methods）。该概念的主要目的是**允许Java在编译时进行类型检查，而在运行时使用通用的、非泛型的代码，以保持与旧版本Java代码的向后兼容性**。

类型擦除的规则如下：

1. 对于参数化类型（泛型类型），如`List<String>`，编译器会将其类型擦除为原始类型（raw type），即`List`。这意味着在运行时，编译器不会保留有关类型参数的信息。
2. 对于嵌套类型T.C，它的擦除是将外层类型T的擦除类型与内部类型C保持在一起，即|T|.C。这确保了嵌套类型在类型擦除后仍然保持嵌套结构。
3. 对于数组类型T[]，它的擦除是将数组元素类型T的擦除类型与数组维度保持在一起，即|T|[]。这意味着数组类型的维度信息在类型擦除后仍然存在。
4. 对于类型变量，编译器会将其擦除为其左边界（bound）的类型。例如，如果有一个类型变量`<T extends Number>`，则擦除后的类型为`Number`。
5. 对于除上述情况之外的其他类型，擦除后的类型与原始类型相同，不进行任何修改。这包括基本数据类型（如int、double）、非泛型类和接口等。

```java
import java.util.List;
import java.util.ArrayList;

public class TypeErasureExample {
    public static void main(String[] args) {
        // 示例1: 参数化类型擦除
        List<String> stringList = new ArrayList<>();
        List<Integer> intList = new ArrayList<>();

        // 编译时，类型参数被擦除，变成原始类型List
        System.out.println(stringList.getClass().getName()); // 输出：java.util.ArrayList
        System.out.println(intList.getClass().getName());    // 输出：java.util.ArrayList

        // 示例2: 嵌套类型擦除
        Outer.Inner inner = new Outer.Inner();

        // 编译时，嵌套类型被擦除，成为原始类型
        System.out.println(inner.getClass().getName()); // 输出：TypeErasureExample$Outer$Inner

        // 示例3: 数组类型擦除
        String[] strArray = new String[5];
        Integer[] intArray = new Integer[5];

        // 编译时，数组元素类型被擦除，保留维度
        System.out.println(strArray.getClass().getName()); // 输出：[Ljava.lang.String;
        System.out.println(intArray.getClass().getName()); // 输出：[Ljava.lang.Integer;

        // 示例4: 类型变量的擦除
        GenericClass<Integer> intGeneric = new GenericClass<>(5);
        GenericClass<String> strGeneric = new GenericClass<>("Hello");

        // 编译时，类型变量T的擦除是其左边界的擦除类型
        System.out.println(intGeneric.getValue().getClass().getName()); // 输出：java.lang.Integer
        System.out.println(strGeneric.getValue().getClass().getName()); // 输出：java.lang.String
    }

    static class Outer {
        static class Inner {
        }
    }

    static class GenericClass<T> {
        private T value;

        public GenericClass(T value) {
            this.value = value;
        }

        public T getValue() {
            return value;
        }
    }
}

```

>请注意，编译时会进行类型擦除，因此在运行时无法获取类型参数的信息，而只能获取原始类型信息。



### 4.7. Reifiable类型

Reifiable Types（可具体化类型）是Java编程语言中的一个概念，它指的是在运行时可以完全保留类型信息的一类类型。在Java中，由于编译时类型信息的擦除，不是所有类型都能在运行时获得完整的信息。因此，Reifiable Types是那些在运行时仍然能够完整表达自身类型的类型。

以下是定义Reifiable Types的规则：

1. **非泛型类或接口类型声明**：任何引用非泛型类或接口类型声明的类型都是可具体化的。这意味着普通的类或接口类型在运行时是具体化的。
2. **所有类型参数都是无界通配符的参数化类型**：如果一个类型是参数化类型，并且它的所有类型参数都是无界通配符（unbounded wildcards），则该类型是可具体化的。无界通配符通常表示为`<?>`。
3. **原始类型（Raw Type）**：原始类型是可具体化的。原始类型是指未指定类型参数的泛型类型，例如`List`而不是`List<String>`。
4. **基本数据类型**：基本数据类型，如`int`、`double`，都是可具体化的。
5. **数组类型**：数组类型也是可具体化的，但要求其元素类型也是可具体化的。
6. **嵌套类型**：对于嵌套类型，如果每个嵌套部分都是可具体化的，那么整个嵌套类型也是可具体化的。例如，如果有一个泛型类X<T>中包含泛型成员类Y<U>，那么类型X<?>.Y<?>是可具体化的，因为X<?>可具体化且Y<?>可具体化。

>需要注意的是，交集类型（Intersection Type）不是可具体化的。这意味着包含多个泛型类型的交集类型在运行时无法完整表达自身类型信息。
>
>Java选择将某些类型视为可具体化，而将其他类型视为不可具体化，是出于对现有代码兼容性的考虑。这个决策确保了旧版本Java代码可以在新版本Java中继续运行，而不会引入不必要的复杂性和依赖关系。



### 4.8. 原始类型

Raw Types（原始类型）是 Java 泛型系统中的一个概念，用于与非泛型的旧代码进行交互，以确保向后兼容性。

**Raw Type的定义**：Raw Type可以是以下之一：

- 通过采用泛型类或接口声明的名称，但不附带类型参数列表形成的引用类型。
- 数组类型，其中元素类型是Raw Type。
- 原始类型（非泛型的类或接口）的内部成员类的名称，但不从超类或超接口继承而来。

**使用Raw Type的局限**：Raw Type的使用仅作为向后兼容性的妥协，主要用于与旧代码互操作。强烈不建议在引入泛型后的新代码中使用Raw Type。未来版本的Java可能会禁止使用Raw Type

```java
 public void main(String[] args) {
        // 示例1: Raw Type的定义
        List<String> genericList = new ArrayList<>();
        List rawList = genericList; // 使用Raw Type

        // 示例2: 访问Raw Type成员的警告
        rawList.add(42); // 无类型安全检查，会引发 unchecked 警告
        String firstElement = (String) rawList.get(0); // 需要进行强制类型转换

        // 示例3: Raw Type与继承
        RawInheritedClass rawInherited = new RawInheritedClass();
        List<String> stringList = rawInherited.myStrings(); // 调用继承的方法
        List<Number> numberList = rawInherited.myNumbers(); // 无需类型转换

        // 示例4: Raw Type与泛型库的交互
        RawLibraryConsumer consumer = new RawLibraryConsumer();
        List<String> result = consumer.consumeRawType(); // 从泛型库获取数据


    }

    class RawInheritedClass extends NonGeneric {
        List<String> myStrings() { return new ArrayList<>(); }
    }

    class NonGeneric {
        List<Number> myNumbers() { return new ArrayList<>(); }
    }

    class RawLibraryConsumer {
        List consumeRawType() {
            List rawList = new ArrayList();
            rawList.add("Hello");
            rawList.add(42); // 无类型安全检查，会引发 unchecked 警告
            return rawList;
        }
    }
```

>因此在创建对象的时候，我们通常会建议带上它对应的类型。例如：
>
>```java
>        List<String> genericList = new ArrayList<>();
>        List<String>  rawList = genericList; // 使用Raw Type
>        rawList.add(42); // 保证编译器显示错误提醒
>```
>
>



### 4.9. 交叉类型

交集类型是 Java 泛型系统中的一个概念，表示一个类型可以同时具有多个不同的类型。交集类型采用形式 `T1 & ... & Tn`，其中 `Ti` 是不同的类型。交集类型可以从类型参数的界限（bounds）中派生，也可以通过强制类型转换（cast expressions）产生。它们还在捕获转换（capture conversion）和最小上限计算（least upper bound computation）的过程中出现。

**交集类型的特点：**

- 交集类型的值是同时满足所有 `Ti` 类型要求的对象。
- 交集类型可以由类型参数的界限、强制类型转换、捕获转换和最小上限计算等方式派生。



以下是关于 Java 中的交叉类型的一些示例和概念：

**示例 1：使用通配符（Wildcard）**

```java
public static <T extends Number & Comparable<T>> T findMax(T[] array) {
    T max = array[0];
    for (T item : array) {
        if (item.compareTo(max) > 0) {
            max = item;
        }
    }
    return max;
}
```

在这个示例中，我们使用通配符 `T extends Number & Comparable<T>`，表示类型 `T` 必须同时是 `Number` 和 `Comparable<T>` 的子类型，从而引入了交叉类型的概念。

**示例 2：使用接口多继承**

Java 中不支持类的多继承，但可以通过接口来实现多继承的效果。在某种程度上，这也可以看作是交叉类型的一种实现方式。例如：

```java
interface Printable {
    void print();
}

interface Readable {
    void read();
}

class Document implements Printable, Readable {
    public void print() {
        // 实现打印逻辑
    }

    public void read() {
        // 实现读取逻辑
    }
}
```

在这个示例中，`Document` 类实现了两个接口 `Printable` 和 `Readable`，从而具有了两种不同类型的行为。



### 4.10. 子类型化

子类型关系涉及到类型之间的继承和兼容性。

**子类型关系基本概念：**

- 子类型关系是一种二元关系，用于描述类型之间的继承和兼容性关系。
- 子类型关系分为直接子类型关系和直接超类型关系。

**直接超类型关系：**

直接超类型关系表示一个类型可以被另一个类型直接继承。这个关系可以通过 `S >1 T` 表示，其中 `S` 是超类型，`T` 是子类型。

直接超类型关系是通过一系列规则定义的，这些规则确定了哪些类型可以被认为是另一个类型的直接超类型。

**子类型关系：**

子类型关系表示一个类型可以被另一个类型所包容。这个关系可以通过 `T <: S` 表示，其中 `T` 是子类型，`S` 是超类型。

子类型关系是通过反射和传递闭包得到的，它包括所有直接超类型关系，以及更多的继承关系。

>请注意，子类型关系不会扩展到参数化类型。也就是说，`T <: S` 不意味着 `C<T> <: C<S>`。这是因为泛型类型之间的子类型关系通常不成立，除非它们具有特定的子类型关系。
>

**子类型和超类型关系：**

```java
class Animal { }

class Mammal extends Animal { }

class Dog extends Mammal { }

public class SubtypingExample {
    public static void main(String[] args) {
        Animal animal = new Animal();
        Mammal mammal = new Mammal();
        Dog dog = new Dog();

        // 子类型关系
        Mammal subType = dog; // Dog 是 Mammal 的子类型
        Animal subType2 = dog; // Dog 是 Animal 的子类型

        // 超类型关系
        Dog superType = mammal; // Mammal 是 Dog 的超类型
        Mammal superType2 = animal; // Animal 是 Mammal 的超类型
    }
}

```

**Proper Subtype：**

```java
class Fruit { }

class Apple extends Fruit { }

class Banana extends Fruit { }

public class ProperSubtypeExample {
    public static void main(String[] args) {
        Apple apple = new Apple();
        Banana banana = new Banana();

        // Proper Subtype
        Fruit properSubtype = apple; // Apple 是 Fruit 的 Proper Subtype
    }
}

```

>其实，这些内容简单说就是父类可以转为子类；而子类转换父类需要强制转换。



#### 4.10.1. 原始类型之间的子类型化

这 以下规则定义了 基元类型：

- `double` `>1` `float`
- `float` `>1` `long`
- `long` `>1` `int`
- `int` `>1` `char`
- `int` `>1` `short`
- `short` `>1` `byte`

>1. `double` 是 `float` 的直接超类型，表示 `double` 可以包容 `float`。
>2. `float` 是 `long` 的直接超类型，表示 `float` 可以包容 `long`。
>3. `long` 是 `int` 的直接超类型，表示 `long` 可以包容 `int`。
>4. `int` 是 `char` 的直接超类型，表示 `int` 可以包容 `char`。
>5. `int` 也是 `short` 的直接超类型，表示 `int` 可以包容 `short`。
>6. `short` 是 `byte` 的直接超类型，表示 `short` 可以包容 `byte`。



#### 4.10.2. 类和接口类型之间的子类型化

对于非泛型类或接口 `C`，其直接超类型包括：

- `C` 的直接父类（如果 `C` 是一个类）。
- `C` 的直接父接口（如果 `C` 是一个接口）。
- 类型 `Object`（如果 `C` 是一个接口并且没有直接父接口）。

```java
class Animal {}
interface Bird {}

class Dog extends Animal {} // 直接超类型为 Animal
class Parrot implements Bird {} // 直接超类型为 Bird
class Cat implements Bird {} // 直接超类型为 Bird

```

对于泛型类或接口 `C`，其原始类型的直接超类型包括：

- `C` 的直接父类的擦除（erasure）类型（如果 `C` 是一个类）。
- `C` 的直接父接口的擦除类型。
- 类型 `Object`（如果 `C` 是一个接口并且没有直接父接口）。

```java
class Box<T> {}

class IntegerBox extends Box<Integer> {} // 原始类型的直接超类型是 Box

```

对于参数化类型 `C<T1, ..., Tn>`，其中 `T1, ..., Tn` 是类型参数，其直接超类型包括：

- 对于类 `C`，将 `T1, ..., Tn` 分别替换为对应的直接父类的类型参数。
- 将 `T1, ..., Tn` 分别替换为对应的直接父接口的类型参数。
- 交叉类型 `C<S1, ..., Sn>`，其中 `Si` 包含了 `Ti`。
- 类型 `Object`（如果 `C` 是一个接口并且没有直接父接口）。
- 原始类型 `C`。

```java
class Box<T> {}

class StringBox extends Box<String> {} // 直接超类型是 Box<String>
class NumberBox extends Box<Number> {} // 直接超类型是 Box<Number>

```

对于参数化类型 `C<R1, ..., Rn>`，其中至少有一个 `Ri` 是通配符类型参数（wildcard type argument），其直接超类型是将 `C<R1, ..., Rn>` 应用捕获转换（capture conversion）后得到的参数化类型 `C<X1, ..., Xn>` 的直接超类型。捕获转换是一种类型转换，用于处理通配符类型参数。

```java
class Box<T> {}

class WildcardBox extends Box<?> {} // 直接超类型是 Box<?>

```

对于类型变量（type variable），其直接超类型是其边界（bound）中列出的类型。类型变量是一个特殊的类型，通常在泛型中使用。类型变量的直接超类型还包括其下界（lower bound），这是边界之一。

```java
class Box<T> {
    // 类型变量的直接超类型是 T 的上界
    T item;

    Box(T item) {
        this.item = item;
    }
}

```

对于空类型（null type），其直接超类型是除了空类型本身以外的所有引用类型。空类型用于表示 `null` 值。

```java
class Example {
    public static void main(String[] args) {
        String text = "Hello, World!";
        Object obj = null; // 空类型的直接超类型是 Object
    }
}

```





#### 4.10.3. 数组类型之间的子类型化

1. 如果 `S` 和 `T` 都是引用类型，那么 `S[] >T[]` 当且仅当 `S >T`。这表示数组类型 `S[]` 是数组类型 `T[]` 的超类型，如果元素类型 `S` 是元素类型 `T` 的超类型。这是 Java 中的协变性规则。
2. 对于基本数据类型 `P`，有以下规则：
   - `Object > P[]`：任何基本数据类型数组都是 `Object` 数组的子类型。
   - `Cloneable > P[]`：任何基本数据类型数组都是 `Cloneable` 数组的子类型。
   - `java.io.Serializable > P[]`：任何基本数据类型数组都是 `java.io.Serializable` 数组的子类型。
3. 对于引用类型 `S`，以下规则适用：
   - `Object > Object[]`：`Object` 类型是任何对象数组类型的超类型。
   - `Cloneable > Object[]`：`Cloneable` 类型是任何对象数组类型的超类型。
   - `java.io.Serializable >1 Object[]`：`java.io.Serializable` 类型是任何对象数组类型的超类型。

>规则允许在 Java 中进行数组类型的转换和多态性操作。例如，您可以将任何对象数组赋给 `Object[]`，因为 `Object` 是所有对象类型的超类型。同样，您可以将任何基本数据类型数组赋给相应的包装类数组，因为包装类类型是 `Object` 的子类型。



### 4.11. 变量

变量是程序中的存储位置，用于存储数据。每个变量都有一个类型，也称为编译时类型，可以是基本类型（Primitive Type）或引用类型（Reference Type）。

每个变量类型都有默认值，如果变量未经显式初始化，它将具有其默认值。这确保了在访问变量之前具有某种初始值。

在 Java 中，所有对变量的赋值都受到赋值兼容性检查的限制，这通常在编译时进行。这意味着被赋予变量的值必须与变量的类型兼容。

在某些情况下，涉及到数组的赋值可能需要在运行时进行检查以确保类型的兼容性。


#### 4.11.1. 原始类型变量

一个 基元类型的变量始终成立 该确切基元的基元值 类型。

>例如：基本数据类型int、long 等等这些类型就是它自己本身

#### 4.11.2. 引用类型变量

引用类型变量可以持有对类类型或接口类型的实例的引用。它们可以存储以下内容：

- 空引用（null）
- 类型为T或T的子类的实例的引用，如果T是一个类类型。
- 实现了接口的类的实例的引用，如果变量的类型是该接口类型。

1. **堆污染**：堆污染是一种情况，其中变量引用的实际对象不匹配其声明的参数化类型。这通常发生在以下情况下：

  - 进行操作，涉及到未检查的警告。
  - 数组变量的不可恢复元素类型与其超类型或非泛型接口类型存在别名。

   堆污染会在运行时导致异常，如 `ClassCastException`，因为在运行时无法检测到参数化类型的信息。

   ```java
   import java.util.ArrayList;
   import java.util.List;

   public class HeapPollutionExample {
       public static void main(String[] args) {
           List<Integer> integerList = new ArrayList<>();
           List rawList = integerList; // 编译时警告
           rawList.add("String"); // 堆污染
           Integer number = integerList.get(0); // 运行时 ClassCastException
       }
   }

   ```



2. **类型擦除**：Java 中的类型参数会在编译时被擦除，这意味着运行时无法识别参数化类型。这是导致堆污染的一部分原因。

3. **编译时警告**：当进行某些操作涉及到堆污染时，编译器会生成未检查的警告。这些警告是提醒程序员注意潜在的堆污染问题的重要指示。

4. **不安全别名**：对于引用类型，使用 `Object[]` 作为通用超类型可能导致别名问题，从而导致堆污染。

5. **编程实践**：好的编程实践包括确保在涉及堆污染的情况下程序仍然正确，即使可能会生成编译时警告。程序员应该采取措施来防止堆污染。

6. **SafeVarargs 注解**：`@SafeVarargs` 注解可以用于方法参数，用于禁止编译时产生堆污染的警告。

   ```java
   import java.util.ArrayList;
   import java.util.List;

   public class SafeVarargsExample {
       @SafeVarargs
       public static void addToList(List<Integer>... lists) {
           for (List<Integer> list : lists) {
               list.add(42);
           }
       }

       public static void main(String[] args) {
           List<Integer> list1 = new ArrayList<>();
           List<Integer> list2 = new ArrayList<>();
           addToList(list1, list2);
           System.out.println(list1); // [42]
           System.out.println(list2); // [42]
       }
   }

   ```





#### 4.12.3. 变量的种类

1. **类变量（Class Variable）：**

   - 使用 `static` 关键字在类声明中声明的字段。

   - 类变量在其类或接口被加载时创建，并初始化为默认值。

   - 当类或接口被卸载时，类变量会失效。

     ```java
     public class MyClass {
         public static int classVariable; // 类变量

         public static void main(String[] args) {
             MyClass.classVariable = 10; // 访问和修改类变量
             System.out.println(MyClass.classVariable);
         }
     }

     ```



2. **实例变量（Instance Variable）：**

   - 在类声明中不使用 `static` 关键字声明的字段。

   - 对于每个新创建的类T的对象，都会创建一个新的实例变量 `a` 并初始化为默认值。

   - 当对象不再被引用，且对象的终结化已经完成后，实例变量失效。

     ```java
     public class MyClass {
         public int instanceVariable; // 实例变量

         public MyClass() {
             this.instanceVariable = 20; // 实例变量在构造函数中初始化
         }
     }

     ```

3. **数组组件（Array Component）：**

   - 创建数组对象时，数组组件被创建并初始化为默认值。

   - 当数组不再被引用时，数组组件失效。

     ```java
     public class MyClass {
         public int[] array = new int[5]; // 数组声明

         public MyClass() {
             array[0] = 1; // 初始化数组的第一个元素
         }
     }

     ```



4. **方法参数（Method Parameter）：**

   - 指传递给方法的参数值。

   - 每次方法调用时，会为每个方法参数创建一个新的参数变量，并用调用时提供的参数值初始化。

   - 方法参数在方法体执行完毕后失效。

     ```java
     public class MyClass {
         public void method(int parameter) { // 方法参数
             System.out.println(parameter);
         }
     }

     ```



5. **构造器参数（Constructor Parameter）：**

   - 指传递给构造器的参数值。

   - 每次创建类实例或显式调用构造器时，会为每个构造器参数创建一个新的参数变量，并用提供的参数值初始化。

   - 构造器参数在构造器体执行完毕后失效。

     ```java
     public class MyClass {
         public int instanceVariable;

         public MyClass(int constructorParameter) { // 构造器参数
             this.instanceVariable = constructorParameter;
         }
     }

     ```



6. **Lambda 参数（Lambda Parameter）：**

   - 指传递给 lambda 表达式体的参数值。

   - 每次调用由 lambda 体实现的方法时，会为 lambda 表达式的每个参数创建一个新的参数变量，并用方法调用的参数值初始化。

   - Lambda 参数在 lambda 表达式体执行完毕后失效。

     ```java
     import java.util.function.Consumer;

     public class MyClass {
         public static void main(String[] args) {
             Consumer<Integer> lambda = (lambdaParameter) -> { // Lambda 参数
                 System.out.println(lambdaParameter);
             };
             lambda.accept(30);
         }
     }

     ```



7. **异常参数（Exception Parameter）：**

   - 在 catch 子句中捕获异常时创建。

   - 异常参数用与异常关联的实际对象初始化。

   - 当 catch 子句关联的块执行完毕后，异常参数失效。

     ```java
     public class MyClass {
         public static void main(String[] args) {
             try {
                 int result = 10 / 0;
             } catch (ArithmeticException exceptionParameter) { // 异常参数
                 System.out.println(exceptionParameter.getMessage());
             }
         }
     }

     ```



8. **局部变量（Local Variable）：**

   - 在语句中声明的变量。

   - 局部变量在控制流进入最接近的封闭块时创建，并在声明中有初始化器时初始化。

   - 局部变量在其声明不再在作用域内时失效。

     ```java
     public class MyClass {
         public void method() {
             int localVar = 40; // 局部变量
             System.out.println(localVar);
         }
     }

     ```



#### 4.12.4. final变量

在Java中，`final` 变量表示一旦给定了值，就不允许再改变。这是Java中实现不可变性的一种机制。

**Final 变量：**

- `final` 变量只能被赋值一次。
- `final` 变量一旦被赋值后，就不能再指向另一个值。
- 如果 `final` 变量引用了一个对象，那么这个引用不能改变，但是对象内部的状态可以改变（除非该对象自身是不可变的）。

**空白 Final 变量：**

- 没有初始化器的 `final` 变量称为“空白 final”变量。
- 空白 final 变量必须在类的构造函数中初始化。

**常量变量：**

- 使用编译时常量表达式初始化的 `final` 变量称为“常量变量”。
- 常量变量在使用时由编译器内联，以提高效率。

**实质上 Final：**

- 即使没有声明为 `final`，如果一个变量在初始化后没有改变过，那么它也可以被认为是实质上的 final。
- 在方法中只被赋值一次且之后不再改变的局部变量被视为实质上的 final

**Final 变量的影响：**

- 在接口内声明的变量隐式地是 `final` 的。
- 在 try-with-resources 语句和多重捕获块中，资源和变量也隐式地是 `final` 的。



#### 4.12.5. 变量的初始值

- **基本数据类型的默认值：**
  - `byte`：默认值是0，即 `(byte)0`。
  - `short`：默认值是0，即 `(short)0`。
  - `int`：默认值是0，即 `0`。
  - `long`：默认值是0，即 `0L`。
  - `float`：默认值是正零，即 `0.0f`。
  - `double`：默认值是正零，即 `0.0d`。
  - `char`：默认值是空字符，即 `'\u0000'`。
  - `boolean`：默认值是 `false`。
- **引用类型的默认值：**
  - 所有引用类型的默认值是 `null`。
- **方法参数：**
  - 方法参数被调用者提供的实际参数值初始化。
- **构造器参数：**
  - 构造器参数通过类实例创建表达式或显式构造器调用提供的实际参数值初始化。
- **异常参数：**
  - 异常参数由代表异常的抛出对象初始化。
- **局部变量：**
  - 语句声明的局部变量在使用前必须显式赋值，可以通过初始化或赋值操作实现。
  - 局部变量的赋值必须遵守明确赋值（Definite Assignment）的规则。
- **模式变量：**
  - 模式声明的局部变量通过模式匹配过程隐式初始化

```java
class Point {
    static int npoints; // 类变量
    int x, y;           // 实例变量
    Point root;         // 引用类型实例变量
}

class Test {
    public static void main(String[] args) {
        System.out.println("npoints=" + Point.npoints); // 打印类变量
        Point p = new Point();                         // 创建Point类的实例
        System.out.println("p.x=" + p.x + ", p.y=" + p.y); // 打印实例变量
        System.out.println("p.root=" + p.root);            // 打印引用类型实例变量
    }
}
//输出
//npoints=0
//p.x=0, p.y=0
//p.root=null
```

>- 类变量 `npoints` 被默认初始化为0。
>- 实例变量 `x` 和 `y` 在创建 `Point` 类的新实例时被默认初始化为0。
>- 引用类型实例变量 `root` 被默认初始化为 `null`。
>
>当类 `Point` 被准备时，类变量 `npoints` 被默认初始化，以及当新的 `Point` 实例化时，实例变量 `x`、`y` 和引用类型 `root` 的默认初始化



#### 4.12.6. 类型、类和接口

在Java中，每个变量和表达式都有一个在编译时可以确定的类型，它可以是基本类型或引用类型。

引用类型包括类类型和接口类型。这些类型是通过类型声明引入的，这包括类声明和接口声明。我们通常使用“类型”这个词来指代一个类或接口。

**类型、类和接口的基本概念：**

- **类型（Type）：** 在Java中，类型是变量或表达式的属性，它决定了变量可以持有哪些值或者表达式可以产生哪些值。
- **类（Class）：** Java中的每个对象都属于某个特定的类，即对象创建时所指定的类。
- **接口（Interface）：** 接口定义了一个可以被多个类实现的方法协议，但接口本身不能被实例化。



**编译时类型与运行时类型：**

- **编译时类型（Compile-time Type）：** 是变量声明时的类型，或者是表达式计算结果的类型，它限定了变量在运行时可以持有的值或表达式在运行时可以产生的值。
- **运行时类型（Run-time Type）：** 指的是变量或表达式在运行时引用的对象的实际类型。



**类型的兼容性：**

- 一个引用类型的变量可以引用任何兼容的类型的对象，这意味着对象的类必须是变量声明类型的类或其子类。
- 尽管变量或表达式可以有一个是接口的编译时类型，但实际上不存在接口的实例。一个接口类型的变量可以引用任何实现了该接口的类的对象。



**类加载器和类型的唯一性：**

- 在运行时，类和接口是通过类加载器加载到Java虚拟机中的。每个类加载器定义了自己的类和接口集合，因此可能存在两个类加载器加载了相同的类或接口定义，但在运行时产生了不同的类或接口。
- 类加载器的这种行为可能导致在编译时正确的代码，在链接时因为类加载器的不一致而失败



**泛型和类型擦除：**

- 类型变量和类型参数在运行时是不具体化的。这意味着，在运行时，相同的类或接口代表了编译时的多个参数化类型。所有给定泛型类型的编译时参数化都共享单一的运行时表示。
- 在某些条件下，一个参数化类型的变量可能引用了一个并不是该参数化类型的对象，这种情况称为堆污染。



```java
// 定义一个可着色的接口
interface Colorable {
    void setColor(int r, int g, int b);
}

// 定义一个点类
class Point {
    int x, y;

    Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}

// 定义一个有颜色的点类，它扩展了点类并实现了可着色的接口
class ColoredPoint extends Point implements Colorable {
    int r, g, b;

    ColoredPoint(int x, int y, int r, int g, int b) {
        super(x, y);
        this.r = r;
        this.g = g;
        this.b = b;
    }

    // 实现setColor方法
    public void setColor(int rv, int gv, int bv) {
        r = rv;
        g = gv;
        b = bv;
    }

    // 输出颜色信息的方法
    void printColor() {
        System.out.println("Color: " + r + ", " + g + ", " + b);
    }
}

// 测试类
public class Test {
    public static void main(String[] args) {
        Point p = new Point(1, 2); // p是Point类型的实例
        ColoredPoint cp = new ColoredPoint(1, 2, 255, 0, 0); // cp是ColoredPoint类型的实例

        p = cp; // p现在引用了ColoredPoint对象，这是多态的体现
        // p.printColor(); // 这是非法的，因为Point类型不包含printColor方法

        Colorable c = cp; // c是Colorable接口类型的实例
        c.setColor(0, 255, 0); // cp的颜色被改变

        if (p instanceof ColoredPoint) {
            ((ColoredPoint) p).printColor(); // p被转型为ColoredPoint类型，并调用printColor方法
        }

        System.out.println("p.x=" + p.x + ", p.y=" + p.y); // p.x和p.y是从Point类继承来的
    }
}

```





## 5.转换和上下文

在Java编程语言中，表达式的类型是在编译时决定的。每个表达式要么不产生结果，要么具有可以推断出的类型。当表达式出现在大多数上下文中时，它必须与该上下文中预期的类型兼容；这个预期的类型被称为目标类型。为了方便，表达式与其上下文的兼容性通常通过两种方式来促进：

1. **多态表达式（Poly Expressions）**：某些表达式，称为多态表达式，其推断类型可以受到目标类型的影响。相同的表达式在不同的上下文中可以有不同的类型。
2. **隐式转换（Implicit Conversion）**：在表达式的类型被推断出后，有时可以执行从表达式的类型到目标类型的隐式转换



如果这两种策略都无法产生适当的类型，则会发生编译时错误。

**转换（Conversion）的分类：** 在Java中，可能的转换分为几个宽泛的类别：

- 身份转换（Identity Conversions）
- 基本类型的扩宽转换（Widening Primitive Conversions）
- 基本类型的缩小转换（Narrowing Primitive Conversions）
- 引用类型的扩宽转换（Widening Reference Conversions）
- 引用类型的缩小转换（Narrowing Reference Conversions）
- 装箱转换（Boxing Conversions）
- 拆箱转换（Unboxing Conversions）
- 未检查的转换（Unchecked Conversions）
- 捕获转换（Capture Conversions）
- 字符串转换（String Conversions）



**转换上下文（Conversion Contexts）：** 有六种转换上下文，其中多态表达式可能受到上下文的影响，或可能发生隐式转换。每种上下文都有不同的多态表达式类型规则，并且允许上述类别中的某些转换而不允许其他转换。这些上下文包括：

- 赋值上下文（Assignment Contexts）
- 严格调用上下文（Strict Invocation Contexts）
- 宽松调用上下文（Loose Invocation Contexts）
- 字符串上下文（String Contexts）
- 类型转换上下文（Casting Contexts）
- 数值上下文（Numeric Contexts）



```java
class Test {
    public static void main(String[] args) {
        // Casting conversion (5.5) of a float literal to
        // type int. Without the cast operator, this would
        // be a compile-time error, because this is a
        // narrowing conversion (5.1.3):
        int i = (int)12.5f;

        // String conversion (5.4) of i's int value:
        System.out.println("(int)12.5f==" + i);

        // Assignment conversion (5.2) of i's value to type
        // float. This is a widening conversion (5.1.2):
        float f = i;

        // String conversion of f's float value:
        System.out.println("after float widening: " + f);

        // Numeric promotion (5.6) of i's value to type
        // float. This is a binary numeric promotion.
        // After promotion, the operation is float*float:
        System.out.print(f);
        f = f * i;

        // Two string conversions of i and f:
        System.out.println("*" + i + "==" + f);

        // Invocation conversion (5.3) of f's value
        // to type double, needed because the method Math.sin
        // accepts only a double argument:
        double d = Math.sin(f);

        // Two string conversions of f and d:
        System.out.println("Math.sin(" + f + ")==" + d);
    }
}

```

>```css
>(int)12.5f==12
>after float widening: 12.0
>12.0*12==144.0
>Math.sin(144.0)==-0.49102159389846934
>```
>
>在上面的程序中，我们可以看到不同类型转换的例子：
>
>- **类型转换（Casting Conversion）**：将浮点数字面量12.5f转换为int类型，这是一个缩小转换。
>- **字符串转换（String Conversion）**：将int值转换为String类型，可以与字符串直接进行连接操作。
>- **赋值转换（Assignment Conversion）**：将int值赋给float变量，这是一个扩宽转换。
>- **数值提升（Numeric Promotion）**：在进行数值运算时，int类型被提升为float类型，以匹配浮点数乘法运算的要求。
>- **调用转换（Invocation Conversion）**：调用`Math.sin`方法时，将float值转换为double类型，因为`Math.sin`方法接受double类型的参数。

### 5.1. 转换种类

#### 5.1.1. 身份转换

身份转换是最简单的一种类型转换，它允许类型转换为其自身。这可能听起来很琐碎，但它有两个实际的结果：

1. **表达式拥有期望类型**：它允许表达式本来就具有所需的类型，因此，可以简单地说，每个表达式都要经过转换，即使是琐碎的身份转换。
2. **程序中的冗余转换**：它暗示允许程序包含冗余的强制类型转换操作符，以增加清晰度。

```java
int a = 10;
int b = (int) a; // 身份转换，将a转换为int类型
```

#### 5.1.2. 扩大原语转换

在Java中，当我们从一种较小的基本类型转换到一种较大的基本类型时，会发生扩宽原始类型转换（Widening Primitive Conversion）。这种转换被认为是安全的，因为目标类型比源类型能够表示更大范围的数值。

1. `byte` 可以转换为 `short`, `int`, `long`, `float`, 或 `double`
2. `short` 可以转换为 `int`, `long`, `float`, 或 `double`
3. `char` 可以转换为 `int`, `long`, `float`, 或 `double`
4. `int` 可以转换为 `long`, `float`, 或 `double`
5. `long` 可以转换为 `float` 或 `double`
6. `float` 可以转换为 `double`

>这些转换在下列情况下不会丢失关于数值整体大小的信息：
>
>- 从一个整数类型到另一个整数类型。
>- 从 `byte`, `short`, 或 `char` 到一个浮点类型。
>- 从 `int` 到 `double`。
>
>但是从 `int` 到 `float`，或从 `long` 到 `float`，或从 `long` 到 `double` 的转换可能会导致精度损失。

#### 5.1.3. 缩小原始转换

缩小原始类型转换（Narrowing Primitive Conversion）是指将一个较大的基本数据类型转换为较小的类型。在Java中，缩小转换是不安全的，因为它可能会导致信息丢失，包括数值的整体大小、精度和范围。

1. `short` 转换为 `byte` 或 `char`
2. `char` 转换为 `byte` 或 `short`
3. `int` 转换为 `byte`, `short`, 或 `char`
4. `long` 转换为 `byte`, `short`, `char`, 或 `int`
5. `float` 转换为 `byte`, `short`, `char`, `int`, 或 `long`
6. `double` 转换为 `byte`, `short`, `char`, `int`, `long`, 或 `float`



- 对于整数类型，缩小转换简单地丢弃除了最低n位之外的所有位，这里n是目标类型T的位数。这可能导致结果的符号与输入值的符号不同。
- 对于 `char` 到整数类型的缩小转换，同样简单地丢弃除了最低n位之外的所有位。
- 对于浮点数到整数类型的缩小转换，首先根据IEEE 754的规则将浮点数舍入为整数值，然后再进行整数类型的缩小转换。
- 对于 `double` 到 `float` 的缩小转换，根据IEEE 754规则进行，可能会导致精度损失和范围损失。

```java
class Test {
    public static void main(String[] args) {
        // 缩小从int到short丢失高位:
        System.out.println("(short)0x12345678==0x" + Integer.toHexString((short)0x12345678));
        // 对于超出byte范围的int值，符号和大小都会改变:
        System.out.println("(byte)255==" + (byte)255);
        // 太大的float值转换为int时，得到最大的int值:
        System.out.println("(int)1e20f==" + (int)1e20f);
        // NaN转换为int得到零:
        System.out.println("(int)NaN==" + (int)Float.NaN);
        // 太大的double值转换为float得到无穷大:
        System.out.println("(float)-1e100==" + (float)-1e100);
        // 太小的double值转换为float得到零:
        System.out.println("(float)1e-50==" + (float)1e-50);
    }
}
```



#### 5.1.4. 扩大和缩小原始转换

在Java中，某些类型转换可能同时涉及扩宽和缩小原始类型转换。这类转换不太常见，因为它们组合了两个通常相反的操作：先扩大类型的范围，然后再缩小它。最典型的例子是从`byte`类型转换到`char`类型。

`byte` 到 `char` 的转换步骤

1. **扩宽转换（Widening）**：首先，`byte`类型通过扩宽原始类型转换变成了`int`类型。`byte`类型是一个8位的有符号整数，而`int`类型是一个32位的有符号整数。在这个过程中，`byte`的值被符号扩展，这样负数会保持为负，正数会保持为正。
2. **缩小转换（Narrowing）**：其次，得到的`int`类型再通过缩小原始类型转换）变成了`char`类型。`char`类型是一个16位的无符号整数。由于`char`无法表示负数，所以这个转换可能会导致信息的丢失。

```java
public class Test {
    public static void main(String[] args) {
        byte b = 65; // ASCII码表中65对应大写字母'A'
        char c = (char) (b & 0xff); // byte到char的转换
        System.out.println(c); // 输出: A
    }
}

```

>在这个例子中，`byte`值`65`首先被转换为`int`值`65`，然后这个`int`值被转换为`char`值`'A'`。这个转换过程是安全的，因为`byte`的值在`char`的有效范围内。
>
>- **信息丢失**：由于`byte`是有符号的，而`char`是无符号的，这种转换可能会导致信息丢失。例如，一个负的`byte`值在转换过程中会变成一个完全不同的`char`值。
>- **显示转换**：在这种转换中，通常需要显式的类型转换操作符，因为直接赋值会导致编译错误。
>- **位操作**：有时可能需要使用位操作来确保`byte`到`char`的转换不会因为符号扩展而出错，如上例中使用`& 0xff`。
>
>你可以在需要时正确地处理涉及不同范围和符号性质的原始数据类型之间的转换。这对于数据处理和数值操作尤其重要，尤其是在处理诸如文件I/O、网络通信或低级数据操作时。



#### 5.1.5. 扩大参考转换

在Java中，当我们有一个引用类型S，并且想要将它转换为另一个引用类型T时，如果S是T的子类型，那么存在一种扩宽引用转换（Widening Reference Conversion）。这是面向对象编程中多态特性的一个基本组成部分。它允许我们将子类的对象引用赋给父类的引用变量。

扩宽引用转换的特点：

1. **安全性**：扩宽引用转换是类型安全的，因为它们只在类型层次结构中向上转换。也就是说，你可以将一个子类的引用转换为任何它继承的类或实现的接口的引用。
2. **编译时校验**：这种转换在编译时可以被证明是正确的，不需要运行时检查。
3. **无异常**：因为这种转换在编译时可以保证类型安全，所以在运行时不会抛出异常。
4. **null类型**：`null`不是一个引用类型，所以不能说存在从`null`类型到引用类型的扩宽转换。但是，许多转换上下文允许将`null`值赋给任何引用类型。

```java
class Animal {
}

class Dog extends Animal {
}

public class Test {
    public static void main(String[] args) {
        Dog d = new Dog(); // d是Dog类型
        Animal a = d; // 将Dog类型的引用赋值给Animal类型的引用，发生扩宽引用转换
        System.out.println(a instanceof Animal); // 输出为true，因为d是Animal的一个实例
        System.out.println(a instanceof Dog); // 同样输出为true
    }
}

```



#### 5.1.6. 缩小参考转换范围

缩小引用转换（Narrowing Reference Conversion）是指将某个引用类型S的表达式视为不同引用类型T的表达式的过程，其中S不是T的子类型。不同于扩宽引用转换，缩小引用转换并不要求两个类型直接相关。然而，**如果可以静态证明没有任何值同时属于这两种类型，那么某些类型对之间的转换是被禁止的。**

缩小引用转换在实际编程中不是很常见，因为它涉及到将更一般类型的引用转换为更具体类型的引用，这通常是不安全的。但是，它在某些情况下是必要的，特别是在向下转型时。

```java
class Animal {
}

class Dog extends Animal {
}

public class Test {
    public static void main(String[] args) {
        Animal a = new Animal(); // a是Animal类型
        Dog d = (Dog) a; // 尝试将Animal类型转换为Dog类型，发生缩小引用转换
    }
}

```

>这段代码试图将类型为`Animal`的引用转换为`Dog`类型，这是一种缩小引用转换。因为`Animal`不是`Dog`的子类型，所以这里需要一个显式的类型转换。如果`a`实际上不是`Dog`的实例，运行这段代码将抛出`ClassCastException`。
>
>- **instanceof运算符**：在尝试缩小引用转换之前，使用`instanceof`运算符来检查对象是否是特定类型的实例，这是一个好习惯，可以避免运行时异常。



##### 5.1.6.1. 允许的缩小引用转换

**缩小引用转换（Narrowing Reference Conversion）允许将一种特定的引用类型S转换为另一种不是其子类型的引用类型T**。但这种转换不是无条件允许的，它遵循一系列规则以确保类型转换的可能性和安全性。

允许从引用类型S到引用类型T的缩小引用转换必须满足以下所有条件：

1. S不是T的子类型。
2. 如果存在参数化类型X是T的超类型，参数化类型Y是S的超类型，且X和Y的类型擦除是相同的，则X和Y不能被证明是完全不同的（这意味着它们不能有完全不同的类型参数）。
3. 下列情况之一适用：
   - S和T都是类或接口类型，S命名的类或接口与T命名的类或接口不相交。
   - S是`Object`类类型，或者是`java.io.Serializable`或`Cloneable`接口类型（数组实现的唯一接口），而T是数组类型。
   - S是数组类型`SC[]`，T是数组类型`TC[]`，且存在从`SC`到`TC`的缩小引用转换。
   - S是类型变量，并且存在从S的上界到T的缩小引用转换。
   - T是类型变量，并且存在从S到T的上界的扩宽或缩小引用转换。
   - S是交集类型`S1 & ... & Sn`，对于所有i（1 ≤ i ≤ n），存在从`Si`到T的扩宽或缩小引用转换。
   - T是交集类型`T1 & ... & Tn`，对于所有i（1 ≤ i ≤ n），存在从S到`Ti`的扩宽或缩小引用转换。

>***类或接口的不相交性（Disjointness）***
>
>一个类或接口被认为与另一个类或接口不相交，如果可以静态确定它们没有共同的实例（除了null值）。不相交的规则包括：
>
>- 如果一个类是final的或者sealed的并且所有允许的直接子类都与某个接口不相交，那么这个类就与该接口不相交。
>- 如果一个接口是sealed的并且与某个类的所有允许的直接子类或子接口都不相交，那么这个接口就与该类不相交。

```java
final class FinalClass {}

interface SomeInterface {}

class SomeClass implements SomeInterface {}

public class Test {
    public static void main(String[] args) {
        SomeClass sc = new SomeClass(); // sc是SomeClass的实例
        FinalClass fc = new FinalClass(); // fc是FinalClass的实例

        // 正确的扩宽引用转换
        SomeInterface si = sc;

        // 编译错误：不能将FinalClass类型转换为SomeInterface
        // 因为FinalClass是final的，且没有实现SomeInterface
        // SomeInterface si2 = (SomeInterface) fc;

        // 正确的缩小引用转换
        // sc是SomeClass实例，SomeClass实现了SomeInterface
        SomeClass sc2 = (SomeClass) si;
    }
}

```

>`SomeClass`实现了`SomeInterface`接口，因此我们可以将`SomeClass`的对象引用赋给`SomeInterface`类型的变量（这是一个扩宽引用转换）。然而，尽管`FinalClass`和`SomeInterface`都是引用类型，但`FinalClass`没有实现`SomeInterface`且是一个final类，因此不存在从`FinalClass`到`SomeInterface`的缩小引用转换。



##### 5.1.6.2. 检查和未检查的缩小参考转换

**Checked 缩小引用转换：**

- Java虚拟机会完全验证类型的正确性。
- 不会在编译时生成警告。

**Unchecked 缩小引用转换：**

- Java虚拟机无法完全验证类型的正确性，可能导致堆污染（heap pollution）。
- 在编译时，如果是未检查的缩小引用转换，会生成一个未检查的警告，除非使用 `@SuppressWarnings` 进行抑制。

**Unchecked 缩小引用转换的情况:**

1. 从类型 S 缩小引用转换到参数化类或接口类型 T 时，该转换是 unchecked 的，除非以下至少有一项成立：

   - T 的所有类型参数都是无界通配符。
   - T 是 S 的子类型，并且 S 没有子类型 X，X 的类型参数不包含在 T 的类型参数中。

   ```java
   import java.util.List;

   public class UncheckedConversionExample1 {
       public static void main(String[] args) {
           List<?> list = getList();

           // Unchecked narrowing reference conversion
           List<String> stringList = (List<String>) list; // 编译器提示转换警告⚠
       }

       static List<?> getList() {
           return List.of("One", "Two", "Three");
       }
   }

   ```



2. 从类型 S 缩小引用转换到类型变量 T 时是 unchecked 的。

   ```java
   public class UncheckedConversionExample2 {
       public static void main(String[] args) {
           Object obj = "Hello";

           // Unchecked narrowing reference conversion
           String str = (String) obj; // Warning generated
       }
   }

   ```



3. 从类型 S 缩小引用转换到交集类型 T1 & ... & Tn 时，如果存在 Ti（1 ≤ i ≤ n），使得 S 不是 Ti 的子类型且从 S 到 Ti 的缩小引用转换是 unchecked 的，那么整个引用转换也是 unchecked 的。

   ```java
   public class UncheckedConversionExample3 {
       public static void main(String[] args) {
           Object obj = "Hello";

           // Unchecked narrowing reference conversion
           CharSequence charSequence = (CharSequence) obj; // Warning generated
       }
   }
   //Object 类型的引用被转换为 CharSequence 类型。如果存在 Ti 使得 S 不是 Ti 的子类型，而从 S 到 Ti 的缩小引用转换是 unchecked 的，那么整个引用转换也是 unchecked 的。这种转换会生成一个未检查的警告。
   ```





##### 5.1.6.3. 检查的窄化引用转换

所有检查的窄化引用转换在运行时都需要有效性检查。主要用于未参数化的类和接口类型。

- 如果转换在概念上是"上转型"，则转换是完全不检查的，因为在Java虚拟机的非泛型类型系统中，这种转换是合法的，不需要运行时测试。

  ```java
  import java.util.ArrayList;
  import java.util.Collection;

  public class UncheckedConversionExample {
      public static void main(String[] args) {
          ArrayList<String> stringList = new ArrayList<>();
          Collection<?> collection = stringList; // Completely unchecked
      }
  }
  //ArrayList<String> 被转换为 Collection<?>，是完全不检查的，因为 ArrayList 是 Collection 的子类型。
  ```



- 如果转换在概念上是"下转型"，则转换是部分不检查的，即使在Java虚拟机的非泛型类型系统中，也需要运行时检查以测试转换涉及的（原始）类型的兼容性。

  ```java
  import java.util.ArrayList;
  import java.util.Collection;

  public class PartiallyUncheckedConversionExample {
      public static void main(String[] args) {
          Collection<?> collection = new ArrayList<>();
          ArrayList<String> stringList = (ArrayList<String>) collection; // Partially unchecked
      }
  }
  //Collection<?> 被转换为 ArrayList<String>，是部分不检查的，因为 Collection 不是 ArrayList 的子类型。
  ```

**在检查的或部分不检查的窄化引用转换的运行时有效性检查中，主要包括以下规则：**

- 如果运行时值为 `null`，则转换是允许的。

  ```java
  public class RuntimeCheckExample1 {
      public static void main(String[] args) {
          // Null 可以转换任意类型并不会报错,但是转换
          Object obj = (Integer) null;
          Object obj = (String) null;
          //但是并不包括
          String obj = (Object) null;
      }
  }

  ```



- 否则，R是对象引用的类，T是正在转换为的类型的擦除形式。然后：

  - 如果 R是普通类（不是数组类）：

    - 如果 `T` 是类类型，则 `R` 必须是与 `T` 相同的类或 `T` 的子类，否则抛出 `ClassCastException`。

      ```java
      public class RuntimeCheckExample2 {
          public static void main(String[] args) {
              Object obj = "Hello";

              // Valid narrowing reference conversion
              String str = (String) obj;

              // Invalid narrowing reference conversion, ClassCastException will be thrown
              Integer num = (Integer) obj;
          }
      }
      ```

    - 如果 `T` 是接口类型，则 `R` 必须实现接口 `T`，否则抛出 `ClassCastException`。

      ```java
      interface MyInterface {}

      public class RuntimeCheckExample3 implements MyInterface {
          public static void main(String[] args) {
              MyInterface myInterface = new RuntimeCheckExample3();

              // Valid narrowing reference conversion
              RuntimeCheckExample3 obj = (RuntimeCheckExample3) myInterface;

              // Invalid narrowing reference conversion, ClassCastException will be thrown
              RuntimeCheckExample2 obj2 = (RuntimeCheckExample2) myInterface;
          }
      }

      ```

    - 如果 `T` 是数组类型，则抛出 `ClassCastException`。

      ```java
      public class RuntimeCheckExample4 {
          public static void main(String[] args) {
              Object obj = new String[]{"One", "Two", "Three"};

              // 有效的窄化引用转换
              String[] strArray = (String[]) obj;

              // 无效的窄化引用转换，将抛出ClassCastException
              Integer[] intArray = (Integer[]) obj;
          }
      }

      ```

  - 如果 R是接口：

    - 如果 `T` 是类类型，则 `T` 必须是 `Object`，否则抛出 `ClassCastException`。
    - 如果 `T` 是接口类型，则 `R` 必须是与 `T` 相同的接口或 `T` 的子接口，否则抛出 `ClassCastException`。
    - 如果 `T` 是数组类型，则抛出 `ClassCastException`。

  - 如果 R是表示数组类型 RC[]的类：

    - 如果 `T` 是类类型，则 `T` 必须是 `Object`，否则抛出 `ClassCastException`。
    - 如果 `T` 是接口类型，则 `T` 必须是 `java.io.Serializable` 或 `Cloneable`（数组唯一实现的接口），否则抛出 `ClassCastException`。
    - 如果 `T` 是数组类型 `TC[]`，则抛出 `ClassCastException`，除非 `TC` 和 `RC` 是相同的原始类型，或者 `TC` 和 `RC` 是引用类型并且通过这些运行时规则的递归应用是允许的。





#### 5.1.7. 装箱转换

装箱转换将原始类型的表达式视为相应引用类型的表达式。具体而言，以下九种转换被称为装箱转换：

1. 从 `boolean` 类型到 `Boolean` 类型
2. 从 `byte` 类型到 `Byte` 类型
3. 从 `short` 类型到 `Short` 类型
4. 从 `char` 类型到 `Character` 类型
5. 从 `int` 类型到 `Integer` 类型
6. 从 `long` 类型到 `Long` 类型
7. 从 `float` 类型到 `Float` 类型
8. 从 `double` 类型到 `Double` 类型
9. 从 `null` 类型到 `null` 类型

条件运算符会对其操作数的类型应用装箱转换，并在进一步的计算中使用结果。

在运行时，装箱转换的过程如下：

- 如果 `p` 是 `boolean` 类型的值，那么装箱转换将 `p` 转换为一个引用 `r`，其类和类型为 `Boolean`，满足 `r.booleanValue() == p`。

```java
public class BoxingConversionExample1 {
    public static void main(String[] args) {
        boolean p = true;
        Boolean r = p; // Boxing conversion
        System.out.println(r.booleanValue()); // Output: true
    }
}
```

- 如果 `p` 是 `byte` 类型的值，那么装箱转换将 `p` 转换为一个引用 `r`，其类和类型为 `Byte`，满足 `r.byteValue() == p`。

```java
public class BoxingConversionExample2 {
    public static void main(String[] args) {
        byte p = 42;
        Byte r = p; // Boxing conversion
        System.out.println(r.byteValue()); // Output: 42
    }
}
```

- 如果 `p` 是 `char` 类型的值，那么装箱转换将 `p` 转换为一个引用 `r`，其类和类型为 `Character`，满足 `r.charValue() == p`。

```java
public class BoxingConversionExample3 {
    public static void main(String[] args) {
        char p = 'A';
        Character r = p; // Boxing conversion
        System.out.println(r.charValue()); // Output: A
    }
}
```

- 如果 `p` 是 `short` 类型的值，那么装箱转换将 `p` 转换为一个引用 `r`，其类和类型为 `Short`，满足 `r.shortValue() == p`。

```java
public class BoxingConversionExample4 {
    public static void main(String[] args) {
        short p = 123;
        Short r = p; // Boxing conversion
        System.out.println(r.shortValue()); // Output: 123
    }
}
```

- 如果 `p` 是 `int` 类型的值，那么装箱转换将 `p` 转换为一个引用 `r`，其类和类型为 `Integer`，满足 `r.intValue() == p`。

```java
public class BoxingConversionExample5 {
    public static void main(String[] args) {
        int p = 456;
        Integer r = p; // Boxing conversion
        System.out.println(r.intValue()); // Output: 456
    }
}
```

- 如果 `p` 是 `long` 类型的值，那么装箱转换将 `p` 转换为一个引用 `r`，其类和类型为 `Long`，满足 `r.longValue() == p`。

```java
public class BoxingConversionExample6 {
    public static void main(String[] args) {
        long p = 789L;
        Long r = p; // Boxing conversion
        System.out.println(r.longValue()); // Output: 789
    }
}
```

- 如果 `p` 是 `float` 类型的值：

  - 如果 `p` 不是 `NaN`，则装箱转换将 `p` 转换为一个引用 `r`，其类和类型为 `Float`，满足 `r.floatValue()` 的结果为 `p`。

  - 否则，装箱转换将 `p` 转换为一个引用 `r`，其类和类型为 `Float`，满足 `r.isNaN()` 的结果为 `true`。

```java
public class BoxingConversionExample8 {
    public static void main(String[] args) {
        double p = 2.718;

        // Valid boxing conversion
        Double r = p;
        System.out.println(r.doubleValue()); // Output: 2.718

        // Invalid boxing conversion with NaN
        Double nanValue = Double.NaN;
        System.out.println(nanValue.isNaN()); // Output: true
    }
}
```

- 如果 `p` 是其他类型的值，装箱转换等效于标识转换（identity conversion）

```java
public class BoxingConversionExample9 {
    public static void main(String[] args) {
        // Example with other type (String)
        String p = "Hello";
        String r = p; // Boxing conversion (identity conversion)
        System.out.println(r); // Output: Hello
    }
}

```

>如果被装箱的值 `p` 是常量表达式的结果，且其类型为 `boolean`、`byte`、`char`、`short`、`int` 或 `long`，且结果是 `true`、`false`、在 '\u0000' 到 '\u007f' 范围内的字符，或在 -128 到 127 范围内的整数，则任何两个装箱转换的结果 `a` 和 `b` 都满足 `a == b`。
>
>这保证在大多数常见情况下，行为将是期望的行为，而不会对性能造成不必要的影响，特别是在小型设备上。
>
>需要注意，装箱转换可能导致 `OutOfMemoryError`，如果需要分配一个新的 `Boolean`、`Byte`、`Character`、`Short`、`Integer`、`Long`、`Float` 或 `Double` 包装类的实例，而可用的存储空间不足。



#### 5.1.8. 拆箱转换

拆箱转换将引用类型的表达式视为相应原始类型的表达式。具体而言，以下八种转换被称为拆箱转换：

1. 从 `Boolean` 类型到 `boolean` 类型
2. 从 `Byte` 类型到 `byte` 类型
3. 从 `Short` 类型到 `short` 类型
4. 从 `Character` 类型到 `char` 类型
5. 从 `Integer` 类型到 `int` 类型
6. 从 `Long` 类型到 `long` 类型
7. 从 `Float` 类型到 `float` 类型
8. 从 `Double` 类型到 `double` 类型

在运行时，拆箱转换的过程如下：

- 如果 `r` 是类型为 `Boolean` 的引用，则拆箱转换将 `r` 转换为 `r.booleanValue()`。

```java
public class UnboxingConversionExample1 {
    public static void main(String[] args) {
        Boolean r = true;
        boolean p = r; // Unboxing conversion
        System.out.println(p); // Output: true
    }
}
```

- 如果 `r` 是类型为 `Byte` 的引用，则拆箱转换将 `r` 转换为 `r.byteValue()`。

```java
public class UnboxingConversionExample2 {
    public static void main(String[] args) {
        Byte r = 42;
        byte p = r; // Unboxing conversion
        System.out.println(p); // Output: 42
    }
}
```

- 如果 `r` 是类型为 `Character` 的引用，则拆箱转换将 `r` 转换为 `r.charValue()`。

```java
public class UnboxingConversionExample3 {
    public static void main(String[] args) {
        Character r = 'A';
        char p = r; // Unboxing conversion
        System.out.println(p); // Output: A
    }
}
```

- 如果 `r` 是类型为 `Short` 的引用，则拆箱转换将 `r` 转换为 `r.shortValue()`。

```java
public class UnboxingConversionExample4 {
    public static void main(String[] args) {
        Short r = 123;
        short p = r; // Unboxing conversion
        System.out.println(p); // Output: 123
    }
}
```

- 如果 `r` 是类型为 `Integer` 的引用，则拆箱转换将 `r` 转换为 `r.intValue()`。

```java
public class UnboxingConversionExample5 {
    public static void main(String[] args) {
        Integer r = 456;
        int p = r; // Unboxing conversion
        System.out.println(p); // Output: 456
    }
}
```

- 如果 `r` 是类型为 `Long` 的引用，则拆箱转换将 `r` 转换为 `r.longValue()`。

```java
public class UnboxingConversionExample6 {
    public static void main(String[] args) {
        Long r = 789L;
        long p = r; // Unboxing conversion
        System.out.println(p); // Output: 789
    }
}
```

- 如果 `r` 是类型为 `Float` 的引用，则拆箱转换将 `r` 转换为 `r.floatValue()`。

```java
public class UnboxingConversionExample7 {
    public static void main(String[] args) {
        Float r = 3.14f;
        float p = r; // Unboxing conversion
        System.out.println(p); // Output: 3.14
    }
}
```

- 如果 `r` 是类型为 `Double` 的引用，则拆箱转换将 `r` 转换为 `r.doubleValue()`。

```java
public class UnboxingConversionExample8 {
    public static void main(String[] args) {
        Double r = 2.718;
        double p = r; // Unboxing conversion
        System.out.println(p); // Output: 2.718
    }
}
```

>- 如果 `r` 是 `null`，拆箱转换将抛出 `NullPointerException`。
>- 可以将一个类型转换为数值类型，如果它是数值类型，或者它是引用类型，并可以通过拆箱转换转换为数值类型。
>- 可以将一个类型转换为整数类型，如果它是整数类型，或者它是引用类型，并可以通过拆箱转换转换为整数类型。



#### 5.1.9. 未经检查的转换

在泛型类型声明中，假设 G 代表一个具有 n 个类型参数的泛型类型声明。

- 从原始类或接口类型 G 转换为形式为 G<T1,...,Tn> 的任何参数化类型存在无检查转换。
- 从原始数组类型 G[]k 转换为形式为 G<T1,...,Tn>[]k 的任何数组类型存在无检查转换。其中 []k 表示 k 维数组。

>无检查转换用于实现遗留代码与已进行泛型转换的库的平滑互操作性。在引入泛型类型之前编写的遗留代码（legacy code）与已进行泛型化处理的库（我们称之为泛型化）之间的互操作是一个典型的应用场景。

让我们通过一些简单的代码示例来理解无检查转换的概念：

1. 从原始类型 `Box` 转换为参数化类型 `Box<String>`：

```java
class Box<T> {
    private T value;

    public Box(T value) {
        this.value = value;
    }

    public T getValue() {
        return value;
    }
}

public class UncheckedConversionExample {
    public static void main(String[] args) {
        // Unchecked conversion from raw type Box to parameterized type Box<String>
        Box<String> stringBox = new Box("Hello");
        System.out.println(stringBox.getValue()); // Output: Hello
    }
}
```

在这个例子中，`Box` 是一个泛型类，我们使用无检查转换将原始类型 `Box` 转换为参数化类型 `Box<String>`。

1. 从原始数组类型 `RawType[]` 转换为参数化数组类型 `ParameterizedType<String>[]`：

```java
public class UncheckedConversionArrayExample {
    public static void main(String[] args) {
        // Unchecked conversion from raw array type RawType[] to parameterized array type ParameterizedType<String>[]
        String[] stringArray = new String[]{"One", "Two", "Three"};
        Object rawArray = stringArray; // Raw array type
        String[] newArray = (String[]) rawArray; // Unchecked conversion to parameterized array type
        System.out.println(Arrays.toString(newArray)); // Output: [One, Two, Three]
    }
}
```

在这个例子中，我们使用无检查转换将原始数组类型 `RawType[]` 转换为参数化数组类型 `ParameterizedType<String>[]`。



#### 5.1.10. 捕捉转换

在泛型类型声明中，假设 G 代表一个具有 n 个类型参数 A1,...,An 和相应边界 U1,...,Un 的泛型类型声明。

1. **通配符类型参数 `?`：**
   - 如果 `Ti` 是形如 `?` 的通配符类型参数，那么对应的 `Si` 是一个新的类型变量。
   - `Si` 的上界（最大允许的类型）为 `Ui[A1:=S1,...,An:=Sn]`，表示在 `Ui` 的基础上，用 `S1` 替代 `A1`、`S2` 替代 `A2`，以此类推。
   - `Si` 的下界为 `null` 类型。
2. **通配符类型参数 `? extends Bi`：**
   - 如果 `Ti` 是形如 `? extends Bi` 的通配符类型参数，那么对应的 `Si` 也是一个新的类型变量。
   - `Si` 的上界为 `glb(Bi, Ui[A1:=S1,...,An:=Sn])`，表示 `Bi` 和 `Ui` 之间的最大下界。
   - `Si` 的下界为 `null` 类型。
   - 如果任意两个类 `Vi` 和 `Vj`，其中一个不是另一个的子类，那么这是一个编译时错误。
3. **通配符类型参数 `? super Bi`：**
   - 如果 `Ti` 是形如 `? super Bi` 的通配符类型参数，那么对应的 `Si` 仍然是一个新的类型变量。
   - `Si` 的上界为 `Ui[A1:=S1,...,An:=Sn]`。
   - `Si` 的下界为 `Bi`。
4. **其他情况：**
   - 如果 `Ti` 不是通配符类型参数，那么 `Si` 就等于 `Ti`。

>让我们通过理解 `java.util.Collections.reverse()` 方法的实现来了解捕获转换的动机：
>
>```java
>public static void reverse(List<?> list);
>```
>
>这个方法反转提供的列表。它适用于任何类型的列表，因此使用通配符类型 `List<?>` 作为形式参数的类型是完全合适的。
>
>考虑如何实现 `reverse()`：
>
>```java
>public static void reverse(List<?> list) { rev(list); }
>private static <T> void rev(List<T> list) {
>    List<T> tmp = new ArrayList<T>(list);
>    for (int i = 0; i < list.size(); i++) {
>        list.set(i, tmp.get(list.size() - i - 1));
>    }
>}
>```
>
>实现需要复制列表，从副本中提取元素并将其插入原始列表。为了以类型安全的方式执行此操作，我们需要为传入列表的元素类型（即 List<?> 的类型）命名为 T。我们在私有服务方法 `rev()` 中执行此操作。一般来说，`List<?>` 是一个未知类型的列表。**对于任何类型 T，它都不是 List<T> 的子类型。允许这样的子类型关系将是不安全的。**
>
>如果不提供一些特殊的处理，我们可以看到从 `reverse()` 调用到 `rev()` 将被禁止。如果是这种情况，`reverse()` 的作者将被迫将其签名编写为：
>
>```java
>public static <T> void reverse(List<T> list)
>
>```
>
>这是不可取的，因为它向调用者暴露了实现信息。更糟糕的是，API 的设计者可能会推断使用通配符的签名是 API 的调用者所需的，只有在稍后才意识到这样的签名会排除类型安全的实现。
>
>从 `reverse()` 到 `rev()` 的调用实际上是无害的，但无法基于 List<?> 和 List<T> 之间的通用子类型关系来证明。这个调用是无害的，因为传入的参数无疑是某种类型的列表（尽管是未知的类型）。如果我们可以在类型变量 X 中捕获这个未知类型，我们就可以推断 T 是 X。这就是捕获转换的实质。



#### 5.1.11. 字符串转换

**即将任何类型都转换为字符串：**

**原始类型到字符串的转换：**

- 对于原始类型 T的值 x，首先将其转换为一个引用值，就好像将其作为参数传递给适当的类实例创建表达式（§15.9）一样：
  - 如果 `T` 是 `boolean`，则使用 `new Boolean(x)`。
  - 如果 `T` 是 `char`，则使用 `new Character(x)`。
  - 如果 `T` 是 `byte`、`short` 或 `int`，则使用 `new Integer(x)`。
  - 如果 `T` 是 `long`，则使用 `new Long(x)`。
  - 如果 `T` 是 `float`，则使用 `new Float(x)`。
  - 如果 `T` 是 `double`，则使用 `new Double(x)`。
- **引用值到字符串的转换：**
  - 对于这个引用值，它会被转换为字符串。
  - 如果引用是 `null`，它被转换为字符串 "null"。否则，转换的过程就好像通过调用被引用对象的 `toString` 方法来完成一样，不带任何参数。
  - 如果调用 `toString` 方法的结果是 `null`，则使用字符串 "null" 代替，或者出现null指针异常
- **关于 `toString` 方法：**
  - `toString` 方法由基础类 `Object`（§4.3.2）定义。
  - 许多类都重写了这个方法，特别是 `Boolean`、`Character`、`Integer`、`Long`、`Float`、`Double` 和 `String`。

```java
        String nu = null;
        System.out.println(nu); // null 字符粗
        System.out.println(nu.toString()); // null异常
```



#### 5.1.12. 禁止转换

1. **禁止转换：**

   - 任何没有在Java语言规范中明确允许的转换都是被禁止的。规范中详细列出了允许的转换，包括原始类型之间、原始类型到引用类型、引用类型之间、以及其他各种类型之间的转换规则。
   - 禁止转换的目的是为了确保类型转换的安全性和一致性。Java的类型系统旨在减少在编译时和运行时可能发生的错误。

2. **示例：**

   - 假设有一个整数类型的变量 `intNum`，如果试图将其直接赋值给布尔类型的变量，这种转换将是被禁止的：

     ```java
     int intNum = 42;
     boolean boolValue = intNum; // 编译错误，禁止从int到boolean的直接转换
     ```

   - 同样，如果试图将一个字符串赋值给一个整数变量，也会得到编译时错误：

     ```java
     String stringValue = "123";
     int intValue = stringValue; // 编译错误，禁止从String到int的直接转换
     ```

   - 在这些情况下，需要使用适当的类型转换操作符或者其他手段来实现正确的类型转换。



### 5.2. **赋值上下文**

赋值上下文（Assignment Contexts），它规定了在将一个表达式的值赋给一个变量时，需要进行的类型转换。

赋值上下文允许使用以下类型转换：

- 标识转换（identity conversion）
- 扩展原始类型转换（widening primitive conversion）
- 扩展引用类型转换（widening reference conversion）
- 扩展引用类型转换后接拆箱转换（widening reference conversion followed by unboxing conversion）
- 扩展引用类型转换后接拆箱转换，然后接扩展原始类型转换（widening reference conversion followed by unboxing conversion, then followed by widening primitive conversion）
- 装箱转换（boxing conversion）
- 装箱转换后接扩展引用类型转换（boxing conversion followed by widening reference conversion）
- 拆箱转换（unboxing conversion）
- 拆箱转换后接扩展原始类型转换（unboxing conversion followed by widening primitive conversion）
- 如果表达式是 byte、short、char 或 int 类型的常量表达式（constant expression），并且其值在目标变量的类型范围内，那么可以使用窄化原始类型转换。
- 如果目标变量是 Byte、Short 或 Character 类型的，并且表达式的值在 byte、short 或 char 类型的范围内，可以使用窄化原始类型转换后接装箱转换。

一个赋值上下文的示例，包括基本类型和引用类型的情况：

```java
class Test {
    public static void main(String[] args) {
        short s = 12;      // 窄化转换为 short
        float f = s;       // 扩展转换为 float
        System.out.println("f=" + f);
        char c = '\u0123';
        long l = c;        // 扩展转换为 long
        System.out.println("l=0x" + Long.toString(l,16));
        f = 1.23f;
        double d = f;      // 扩展转换为 double
        System.out.println("d=" + d);
    }
}

```

赋值上下文的引用类型示例：

```java
class Point { int x, y; }
class Point3D extends Point { int z; }
interface Colorable { void setColor(int color); }

class ColoredPoint extends Point implements Colorable {
    int color;
    public void setColor(int color) { this.color = color; }
}

class Test {
    public static void main(String[] args) {
        // 类类型的变量赋值：
        Point p = new Point();
        p = new Point3D();  // OK，因为Point3D是Point的子类
        Point3D p3d = p;    // 错误，需要进行强制转换

        // 对象类型的变量赋值：
        Object o = p;        // OK，任何对象都可以赋值给Object
        int[] a = new int[3];
        Object o2 = a;        // OK，数组可以赋值给Object

        // 接口类型的变量赋值：
        ColoredPoint cp = new ColoredPoint();
        Colorable c = cp;     // OK，ColoredPoint实现了Colorable

        // 数组类型的变量赋值：
        byte[] b = new byte[4];
        a = b;               // 错误，不同原始类型的数组不能直接赋值
        Point3D[] p3da = new Point3D[3];
        Point[] pa = p3da;    // OK，由于Point3D可以赋值给Point
        p3da = pa;            // 错误，需要进行强制转换
    }
}

```



### 5.3. 调用上下文

调用上下文（Invocation Contexts），它规定了在**方法或构造函数**调用中，将实际参数值赋给相应的形式参数时需要进行的类型转换。

调用上下文分为严格调用上下文和宽松调用上下文。

**严格调用上下文：**

- 严格调用上下文允许以下类型转换：
  - 标识转换（identity conversion）
  - 扩展原始类型转换（widening primitive conversion）
  - 扩展引用类型转换（widening reference conversion）
- 宽松调用上下文允许以下类型转换：
  - 标识转换（identity conversion）
  - 扩展原始类型转换（widening primitive conversion）
  - 扩展引用类型转换（widening reference conversion）
  - 扩展引用类型转换后接拆箱转换（widening reference conversion followed by unboxing conversion）
  - 扩展引用类型转换后接拆箱转换，然后接扩展原始类型转换（widening reference conversion followed by unboxing conversion, then followed by widening primitive conversion）
  - 装箱转换（boxing conversion）
  - 装箱转换后接扩展引用类型转换（boxing conversion followed by widening reference conversion）
  - 拆箱转换（unboxing conversion）
  - 拆箱转换后接扩展原始类型转换（unboxing conversion followed by widening primitive conversion）

>在调用上下文中可能发生的异常包括：
>
>- ClassCastException：如果在应用了类型转换后，结果值是一个不是对应形式参数类型擦除后的类型的子类或子接口的对象。
>- OutOfMemoryError：由于装箱转换导致的内存不足。
>- NullPointerException：由于在 null 引用上进行拆箱转换导致的空指针异常。



### 5.4. 字符串上下文

`String Contexts` 是指与二进制 `+` 运算符一起使用的上下文，其中一个操作数不是 `String` 类型，而另一个操作数是 `String` 类型。这种情况下，会发生字符串转换（String Conversion）。

```java
public class StringContextExample {
    public static void main(String[] args) {
        int number = 42;
        String result = "The answer is: " + number;
        System.out.println(result);
    }
}

```

### 5.5. 转换上下文

强制转换*上下文允许强制转换*的操作数 表达式。

如果表达式是基元类型，则强制转换上下文 允许使用以下选项之一：

- 标识转换
- 不断扩大的原始转换
- 缩小基元转换
- 变宽和变窄的原始转换
- 装箱转换
- 装箱转换后加宽参考转换

如果表达式是引用类型，则为强制转换上下文 允许使用以下选项之一：

- 标识转换
- 加宽参考转换
- 加宽参考转换，然后进行拆箱转换
- 加宽参考转换，然后进行拆箱转换， 然后是扩大的原始转换
- 缩小参考转换
- 缩小参考转换，然后进行拆箱转换
- 拆箱转换
- 拆箱转换，然后是加宽基元转换

如果表达式具有 null 类型，则可以强制转换表达式 添加到任何引用类型。

**强转为基础类型：**

| 转换目标 \ 转换源 | byte       | short      | char       | int        | long       | float      | double     | boolean    |
| ----------------- | ---------- | ---------- | ---------- | ---------- | ---------- | ---------- | ---------- | ---------- |
| byte              | 身份       | 扩大       | 变宽和变窄 | 扩大       | 扩大       | 扩大       | 扩大       | 不允许转换 |
| short             | 缩小       | 身份       | 缩小       | 扩大       | 扩大       | 扩大       | 扩大       | 不允许转换 |
| char              | 缩小       | 缩小       | 身份       | 扩大       | 扩大       | 扩大       | 扩大       | 不允许转换 |
| int               | 缩小       | 缩小       | 缩小       | 身份       | 扩大       | 扩大       | 扩大       | 不允许转换 |
| long              | 缩小       | 缩小       | 缩小       | 缩小       | 身份       | 扩大       | 扩大       | 不允许转换 |
| float             | 缩小       | 缩小       | 缩小       | 缩小       | 缩小       | 身份       | 扩大       | 不允许转换 |
| double            | 缩小       | 缩小       | 缩小       | 缩小       | 缩小       | 缩小       | 身份       | 不允许转换 |
| boolean           | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 身份       |
| Byte              | 拆箱       | 拆箱,扩大  | 不允许转换 | 拆箱,扩大  | 拆箱,扩大  | 拆箱,扩大  | 拆箱,扩大  | 不允许转换 |
| Short             | 不允许转换 | 拆箱       | 不允许转换 | 拆箱,扩大  | 拆箱,扩大  | 拆箱,扩大  | 拆箱,扩大  | 不允许转换 |
| Character         | 不允许转换 | 不允许转换 | 拆箱       | 拆箱,扩大  | 拆箱,扩大  | 拆箱,扩大  | 拆箱,扩大  | 不允许转换 |
| Integer           | 不允许转换 | 不允许转换 | 不允许转换 | 拆箱       | 拆箱,扩大  | 拆箱,扩大  | 拆箱,扩大  | 不允许转换 |
| Long              | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 拆箱       | 拆箱,扩大  | 拆箱,扩大  | 不允许转换 |
| Float             | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 拆箱       | 拆箱,扩大  | 不允许转换 |
| Double            | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 拆箱       | 不允许转换 |
| Boolean           | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 拆箱       |
| Object            | 缩小,拆箱  | 缩小,拆箱  | 缩小,拆箱  | 缩小,拆箱  | 缩小,拆箱  | 缩小,拆箱  | 缩小,拆箱  | 缩小,拆箱  |

**转为引用类型**

| 转换目标 \ 转换源 | `Byte`     | `Short`    | `Character` | `Integer`  | `Long`     | `Float`    | `Double`   | `Boolean`          | `Object` |
| ----------------- | ---------- | ---------- | ----------- | ---------- | ---------- | ---------- | ---------- | ------------------ | -------- |
| `byte`            | 拆箱       | 不允许转换 | 不允许转换  | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 拆箱,扩大,扩大参考 |          |
| `short`           | 不允许转换 | 拆箱       | 不允许转换  | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 拆箱,扩大,扩大参考 |          |
| `char`            | 不允许转换 | 不允许转换 | 拆箱        | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 拆箱,扩大,扩大参考 |          |
| `int`             | 不允许转换 | 不允许转换 | 不允许转换  | 拆箱       | 不允许转换 | 不允许转换 | 不允许转换 | 拆箱,扩大,扩大参考 |          |
| `long`            | 不允许转换 | 不允许转换 | 不允许转换  | 不允许转换 | 拆箱       | 不允许转换 | 不允许转换 | 拆箱,扩大,扩大参考 |          |
| `float`           | 不允许转换 | 不允许转换 | 不允许转换  | 不允许转换 | 不允许转换 | 拆箱       | 不允许转换 | 拆箱,扩大,扩大参考 |          |
| `double`          | 不允许转换 | 不允许转换 | 不允许转换  | 不允许转换 | 不允许转换 | 不允许转换 | 拆箱       | 拆箱,扩大,扩大参考 |          |
| `boolean`         | 不允许转换 | 不允许转换 | 不允许转换  | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 拆箱,扩大,扩大参考 |          |
| `Byte`            | 身份       | 不允许转换 | 不允许转换  | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 扩大参考           |          |
| `Short`           | 不允许转换 | 身份       | 不允许转换  | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 扩大参考           |          |
| `Character`       | 不允许转换 | 不允许转换 | 身份        | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 扩大参考           |          |
| `Integer`         | 不允许转换 | 不允许转换 | 不允许转换  | 身份       | 不允许转换 | 不允许转换 | 不允许转换 | 扩大参考           |          |
| `Long`            | 不允许转换 | 不允许转换 | 不允许转换  | 不允许转换 | 身份       | 不允许转换 | 不允许转换 | 扩大参考           |          |
| `Float`           | 不允许转换 | 不允许转换 | 不允许转换  | 不允许转换 | 不允许转换 | 身份       | 不允许转换 | 扩大参考           |          |
| `Double`          | 不允许转换 | 不允许转换 | 不允许转换  | 不允许转换 | 不允许转换 | 不允许转换 | 身份       | 扩大参考           |          |
| `Boolean`         | 不允许转换 | 不允许转换 | 不允许转换  | 不允许转换 | 不允许转换 | 不允许转换 | 不允许转换 | 扩大参考           |          |
| `Object`          | 缩小,拆箱  | 缩小,拆箱  | 缩小,拆箱   | 缩小,拆箱  | 缩小,拆箱  | 缩小,拆箱  | 缩小,拆箱  | 身份,扩大参考      |          |



### 5.6. 数字上下文

数字上下文适用于算术运算符、数组创建和访问表达式、条件表达式以及switch表达式的结果表达式的操作数。

1. 一元加法运算符+、一元减法运算符-或位取反运算符~的操作数。
2. 乘法运算符*、除法运算符/或取模运算符%的操作数。
3. 数字类型的加法或减法运算符+或-的操作数。
4. 移位运算符<<、>>或>>>的操作数。
5. 数值比较运算符<、<=、>或>=的操作数。
6. 数值相等运算符==或!=的操作数。
7. 整数位运算符&、^或|的操作数。

数字推广决定了数字上下文中所有表达式的推广类型。推广类型被选择为使每个表达式都可以转换为推广类型，对于算术运算，推广类型的值是定义的。数字推广的规则如下：

1. 如果任何表达式是引用类型，则将其进行拆箱转换。
2. 然后，根据以下规则对一些表达式应用拓宽基元转换和缩小基元转换：
   - 如果任何表达式的类型是double，则推广类型为double，其他不是double类型的表达式将进行拓宽基元转换为double。
   - 否则，如果任何表达式的类型是float，则推广类型为float，其他不是float类型的表达式将进行拓宽基元转换为float。
   - 否则，如果任何表达式的类型是long，则推广类型为long，其他不是long类型的表达式将进行拓宽基元转换为long。
   - 否则，如果没有任何表达式的类型是double、float或long，则根据上下文的类型确定推广类型：
     - 在数字算术上下文或数字数组上下文中，推广类型为int，并且所有不是int类型的表达式将进行拓宽基元转换为int。
     - 在数字选择上下文中，根据以下规则选择推广类型：
       - 如果任何表达式的类型是int且不是常量表达式，则推广类型为int，并且所有不是int类型的表达式将进行拓宽基元转换为int。
       - 否则，如果任何表达式的类型是short，而每个其他表达式既是short类型又是byte类型或具有在short类型中可表示的值的int类型的常量表达式，则推广类型为short，并且byte表达式将进行拓宽基元转换为short，int表达式将进行缩小基元转换为short。
       - 否则，如果任何表达式的类型是byte，而每个其他表达式既是byte类型，又是具有在byte类型中可表示的值的int类型的常量表达式，则推广类型为byte，并且int表达式将进行缩小基元转换为byte。
       - 否则，如果任何表达式的类型是char，而每个其他表达式既是char类型，又是具有在char类型中可表示的值的int类型的常量表达式，则推广类型为char，并且int表达式将进行缩小基元转换为char。
       - 否则，推广类型为int，并且所有不是int类型的表达式将进行拓宽基元转换为int。

```java
class NumericPromotionExample {
    public static void main(String[] args) {
        // Unary Numeric Promotion
        byte b = 2;
        int[] a = new int[b];  // Dimension expression promotion
        char c = '\u0001';
        a[c] = 1;              // Index expression promotion
        a[0] = -c;             // Unary - promotion
        System.out.println("a: " + a[0] + "," + a[1]);

        b = -1;
        int i = ~b;            // Bitwise complement promotion
        System.out.println("~0x" + Integer.toHexString(b)
                + "==0x" + Integer.toHexString(i));

        i = b << 4L;           // Shift promotion (left operand)
        System.out.println("0x" + Integer.toHexString(b)
                + "<<4L==0x" + Integer.toHexString(i));

        // Binary Numeric Promotion
        int j = 0;
        float f = 1.0f;
        double d = 2.0;

        // First int*float is promoted to float*float, then
        // float==double is promoted to double==double:
        if (j * f == d) System.out.println("oops");

        // A char&byte is promoted to int&int:
        byte byteValue = 0x1f;
        char charValue = 'G';
        int control = charValue & byteValue;
        System.out.println(Integer.toHexString(control));

        // Here int:float is promoted to float:float:
        f = (byteValue == 0) ? j : 4.0f;
        System.out.println(1.0 / f);
    }
}

```

>```java
>a: -1,1
>~0xffffffff==0x0
>0xffffffff<<4L==0xfffffff0
>7
>0.25
>```






