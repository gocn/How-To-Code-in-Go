# 如何在 Go 中格式化字符串

由于字符串通常由书面文本组成，因此在许多情况下，我们可能希望更好的定制字符串的展示形式，以便通过定时、换行和缩进使其更易于阅读。

在本教程中，我们将介绍一些使用 Go 字符串的方法，以确保所有输出文本的格式正确。

## 字符串文字

我们先来看看  *字符串文字*  和  *字符串值* 的区别。 字符串文字是我们在计算机程序的源代码中看到的，包括引号。 当我们调用 `fmt.Println` 函数并运行程序时，我们会看到一个字符串值。

在“Hello, World!” 程序中，字符串文字是 `"Hello, World!"` 而字符串值是 `Hello, World!` 不带引号。 字符串值是我们在运行 Go 程序时在终端窗口中看到的输出。

但是某些字符串值可能需要包含引号，例如当我们引用某个资源时。由于字符串文字和字符串值不等价，因此通常需要为字符串文字添加额外的转换格式，以确保字符串值按照我们想要的方式显示。

## 引号

因为我们可以在 Go 中使用反引号 (`` `) 或双引号 (`"`)，所以我们很容易在反引号里使用双引号来括住字符串：

```go
`Sammy says, "Hello!"`
```

或者，要使用反引号，你可以将字符串括在双引号中：

```go
"Sammy likes the `fmt` package for formatting strings.."
```

在组合反引号和双引号的方式中，我们可以控制字符串中引号和反引号的显示方式。

这里有个重点，在 Go 中使用反引号会创建一个 `raw` 字符串文字，而使用双引号会创建一个 `interpreted` 字符串文字。要了解有关差异的更多信息，请阅读 [Go 中处理字符串的介绍](https://gocn.github.io/How-To-Code-in-Go/docs/08-An_Introduction_to_Working_with_Strings_in_Go) 教程。

## 转义字符

格式化字符串的另一种方法是使用*转义字符*。转义字符用于告诉代码后面的字符具有特殊含义。转义字符都以反斜杠 (`\`) 开头，并结合字符串中的另一个字符以某种方式格式化给定的字符串。

以下是几个常见转义字符的列表：

| 转义字符 | 如何格式化         |
| -------- | ------------------ |
| \\       | 反斜杠             |
| \"       | 双引号             |
| \n       | 换行               |
| \t       | 制表符（水平缩进） |

让我们使用转义字符将引号添加到上面的引号示例中，但这次将使用双引号来表示字符串：

```go
fmt.Println("Sammy says, \"Hello!\"")
```

```text
output
Sammy says, "Hello!"
```

我们可以通过转义字符 `\"` 来转义掉双引号，从而实现双引号中还能嵌套使用双引号。

我们可以使用 `\n` 转义字符来换行，而无需使用 enter 或 return ：

```go
fmt.Println("This string\nspans multiple\nlines.")
```

```text
output
This string
spans multiple
lines.
```

我们也可以组合转义字符。 打印一个多行字符串且每行包含一个制表符，例如：

```go
fmt.Println("1.\tShark\n2.\tShrimp\n10.\tSquid")
```

```go
Output
1.      Shark
2.      Shrimp
10.     Squid
```

`\t` 转义字符提供的水平缩进确保在前面示例中的第二列内对齐，使输出结果非常易读。

转义字符用于向可能难以或不可能实现的字符串添加额外的格式。 如果没有转义字符，你将无法构造字符串 `Sammy says, "I like to use the `fmt` package"`。

## 多行

在多行上打印字符串可以使文本更具可读性。 通过多行，可以将字符串分组为干净有序的文本，格式化为字母，或用于维护诗歌或歌曲歌词的换行符。

要创建跨越多行的字符串，使用反引号将字符串括起来。 请记住，虽然这么写会保留换行，但它也创建了一个 `raw` 字符串文字。

```go
`
This string is on 
multiple lines
within three single 
quotes on either side.
`
```

如果你打印这个，你会注意到有一个前空行和后空行：

```go
Output

This string is on 
multiple lines
within three single 
quotes on either side.

```

为避免这种情况，您需要将第一行紧跟在反引号之后，并以反引号结束最后一行。

```go
`This string is on 
multiple lines
within three single 
quotes on either side.`
```

如果你需要创建解释字符串文字，可以使用双引号和 `+` 运算符来完成，但你需要插入自己的换行符。

```go
"This string is on\n" +
"multiple lines\n" +
"within three single\n" +
"quotes on either side."
```

虽然反引号可以更轻松地打印和阅读冗长的文本，但如果你需要解释字符串文字，则需要使用双引号。

## 原始字符串文字

如果我们不想在字符串中使用特殊格式怎么办？ 例如，我们可能需要比较或评估故意使用反斜杠的计算机代码字符串，因此我们不希望 Go 将其用作转义字符。

**raw** 字符串文字告诉 Go 忽略字符串中的所有格式，包括转义字符。

我们通过在字符串周围使用反引号来创建一个原始字符串：

```go
fmt.Println(`Sammy says,\"The balloon\'s color is red.\"`)
```

```go
Output
Sammy says,\"The balloon\'s color is red.\"
```

通过在给定字符串周围使用反引号来构造原始字符串，我们可以保留反斜杠和其他用作转义字符的字符。

## 结论

本教程通过使用字符串介绍了几种在 Go 中格式化文本的方法。 通过使用转义字符或原始字符串等技术，我们能够确保程序的字符串正确的呈现在屏幕上，以便用户最终能够轻松阅读所有输出文本。
