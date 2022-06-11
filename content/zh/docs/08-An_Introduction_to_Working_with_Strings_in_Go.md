# Go 中处理字符串的介绍

_字符串_是由一个或多个字符(字母、数字、符号)组成的序列，这些字符可以是常量，也可以是变量。字符串由 [Unicode](https://unicode.org) 组成，是不可变的序列，这意味着它们是不变的。

因为文本是我们日常生活中使用的常见数据形式，所以字符串数据类型是编程中一个非常重要的基石。

本 Go 教程将介绍如何创建和打印字符串，如何连接和复制字符串，以及如何在变量中存储字符串。

## 字符串文字

在 Go 中，字符串存在于反引号 `` ` ``（有时叫做反勾号）或双引号 `"` 中。根据使用的引号不同，字符串将具有不同的特征。

使用反引号，如 `` ` `` ```bar``` `` ` ``，将创建一个_原始_字符串。在原始字符串中，除了反引号之外，任何字符都可以出现在引号之间。下面是一个原始字符串的例子:

```
`Say "hello" to Go!`
```

反斜杠在原始字符串中没有特殊含义。例如，`\n` 表示的是实际字符，以反斜杠 `\` 和字母 `n` 的形式出现。不像解释的字符串文字，`\n` 会插入一个实际的新行。

原始字符串也可用于创建多行字符串:

```
`Go is expressive, concise, clean, and efficient.
Its concurrency mechanisms make it easy to write programs
that get the most out of multi-core and networked machines,
while its novel type system enables flexible and modular
program construction. Go compiles quickly to machine code
yet has the convenience of garbage collection and the power
of run-time reflection. It's a fast, statically typed,
compiled language that feels like a dynamically typed,
interpreted language.`
```

解释字符串是双引号之间的字符序列，如 `"bar"` 中所示。在引号中，除了换行符和非转义双引号之外，可以出现任何字符。

```go
"Say \"hello\" to Go!"
```

您几乎总是使用解释字符串，因为它们允许使用转义字符。

现在你已经了解了 Go 中字符串是如何格式化的，接下来让我们看看如何在程序中打印字符串。

## 打印字符串

你可以使用系统库中的 `fmt` 包并调用 `Println()` 函数来打印字符串：

```go
fmt.Println("Let's print out this string.")
```

```
Output
Let's print out this string.
```

当你使用系统库时需要 `import` 它们，因此一个简单打印字符串的程序如下所示：

```go
package main

import "fmt"

func main() {
	fmt.Println("Let's print out this string.")
}
```

## 字符串拼接

_拼接_意味着把字符串收尾连接起来，创建一个新的字符串。你可以使用 `+` 号连接字符串。注意当你处理数字时，`+` 将是一个加和的操作符，但在用于字符串时是一个连接符。

让我们通过一个 `fmt.Println()` 声明语句把 `"Sammy"` 和 `"Shark"` 字符串连接到一起：

```go
fmt.Println("Sammy" + "Shark")
```

```
Output
SammyShark
```

如果希望两个字符串之间有空格，只需在字符串中包含空格即可。在这个例子中，在 `Sammy` 之后的引号中添加空格:

```go
fmt.Println("Sammy " + "Shark")
```

```
Output
Sammy Shark
```

不能在两种不同的数据类型之间使用 `+` 运算符。例如，你不能将字符串和整数连接在一起。如果你试着写下面的代码:

```go
fmt.Println("Sammy" + 27)
```

你将会收到下面的错误：

```
Output
cannot convert "Sammy" (type untyped string) to type int
invalid operation: "Sammy" + 27 (mismatched types string and int)
```

如果希望创建字符串 `"Sammy27"`，可以将数字 `27` 放在引号中（`"27"`）中 ，这样它就不再是一个整数，而是一个字符串。在处理邮政编码或电话号码时，将数字转换为字符串以进行连接非常有用。例如，你不希望在国家代码和地区代码之间执行添加操作，但是您希望它们可以放在一起。

当通过连接将两个或多个字符串组合在一起时，就创建了一个可以在整个程序中使用的新字符串。

## 在变量中保存字符串 

**[变量]({{< relref "/docs/11-How_To_Use_Variables_and_Constants_in_Go.md" >}})**是在程序中可以用来保存数据的符号。你可以将它们看作是一个可以在其中填充一些数据或值的空盒子。字符串是数据，因此你可以使用它们来填充变量。将字符串声明为变量可以使得在 Go 程序中处理字符串更加容易。

要在变量中存储字符串，只需将一个变量分配给字符串。在下面的例子中，`s` 被声明为变量:

```go
s := "Sammy likes declaring strings."
```

> **注意**: 如果你熟悉其他的编程语言，你可以把变量写成 `sammy`。但是，Go 倾向于使用较短的变量名。在这种情况下，选择 `s` 作为变量名被认为更适合编写 Go 的样式。

现在你有了设置为特定字符串的变量 `s`，你可以像下面的代码一样打印变量：

```go
fmt.Println(s)
```

你将获得下面的输出：

```
Output
Sammy likes declaring strings.
```

通过使用变量来替代字符串，你不必每次都要重新键入字符串，从而使您在程序中处理和操作字符串更加简单。

## 结论

本教程介绍了使用 Go 编程语言处理字符串数据类型的基本知识。创建并打印字符串、连接和复制字符串以及将字符串存储在变量中，它们将为你提供在 Go 程序中使用字符串的基础知识。
