# 如何在 Go 中使用变量和常量

*变量*  是一个需要掌握的重要编程概念。它们是代表你在程序中使用的值的符号。

本教程将介绍一些变量基础知识和在你创建的 Go 程序中使用它们的最佳实践。

## 理解变量

用术语来说，变量将存储位置分配给与符号名称或标识符相关的值。我们使用变量名来引用计算机程序中存储的值。

我们可以将变量视为带有名称的标签，您可以将其绑定到值上。

![Variables in Go](https://assets.digitalocean.com/articles/go_variables/variable1.png)

假设我们有一个整数 `1032049348` ，我们希望将它存储在一个变量中，而不是一遍又一遍地不断地重新输入长数字。为了达到这个目的，我们可以使用一个容易记住的名字，比如变量 `i`。要将值存储在变量中，我们使用以下语法：

```go
i := 1032049348
```

我们可以把这个变量想象成一个与值绑定的标签。

![Go Variable Example](https://assets.digitalocean.com/articles/go_variables/variable2.png)

标签上写有变量名 `i` ，并与整数值 `1032049348` 相关联。

`i := 1032049348` 是一个声明和赋值语句，由几个部分组成：

- 变量名 (`i`)
- 变量声明赋值 (`:=`)
- 与变量名绑定的值（`1032049348`）
- Go 推断的数据类型 (`int`)

稍后我们将在下一节中看到如何显式设置类型。

这些部分共同构成了将变量 `i` 设置为整数 `1032049348` 的值的语句。

一旦我们设置一个等于某个值的变量，变量就会被*初始化*或创建出来。一旦我们这样做了，我们就可以使用变量而不是值了。

一旦我们将 `i` 设置为 `1032049348` ，我们可以使用 `i` 代替整数，所以让我们打印出来：

```go
package main

import "fmt"

func main() {
	i := 1032049348
	fmt.Println(i)
}
```

```go
Output
1032049348
```

我们还可以通过使用变量快速轻松地进行数学运算。使用`i := 1032049348`，我们可以使用以下语法减去整数值`813`：

```go
fmt.Println(i - 813)
```

```go
Output
1032048535
```

在这个例子中，Go 为我们做数学运算，从变量 i 中减去 813 以返回总和 1032048535。

说到数学，变量可以设置为某个数学方程的结果。你还可以将两个数字相加并将总和的值存储到变量 `x` 中：

```go
x := 76 + 145
```

你可能已经注意到这个例子看起来和代数很像。就像我们在公式和方程式中使用字母和其他符号来表示数字和数量一样，变量是表示数据类型值的符号名称。对于正确的 Go 语法，你需要确保你的变量位于任何等式的左侧。

让我们继续打印 `x`：

```go
package main

import "fmt"

func main() {
	x := 76 + 145
	fmt.Println(x)
}
```

```go
Output
221
```

Go 返回值 `221` 因为变量 `x` 被设置为等于 `76` 和 `145` 的总和。

变量可以表示任何数据类型，而不仅仅是整数：

```go
s := "Hello, World!"
f := 45.06
b := 5 > 9 // A Boolean value will return either true or false
array := [4]string{"item_1", "item_2", "item_3", "item_4"}
slice := []string{"one", "two", "three"}
m := map[string]string{"letter": "g", "number": "seven", "symbol": "&"}
```

如果你打印这些变量中的任何一个，Go 将返回该变量等价的内容。让我们使用 `slice` 数据类型的赋值语句：

```go
package main

import "fmt"

func main() {
	slice := []string{"one", "two", "three"}
	fmt.Println(slice)
}
```

```go
Output
[one two three]
```

我们将 `[]string{"one", "two", "three"}` 的切片值分配给变量 `slice`，然后使用 `fmt.Println` 函数通过调用 `slice` 打印出该值。

变量通过在你的计算机中开辟一小块内存区域来工作，该区域接受指定的值，然后与该空间相关联。

## 声明变量

在 Go 中，有多种声明变量的方法，在某些情况下，声明完全相同的变量和值的方法不止一种。

我们可以在不初始化的情况下声明一个数据类型为 `int` 的名为 `i` 的变量。这意味着我们将声明一个空间来放置一个值，但不给它一个初始值：

```go
var i int
```

这将创建一个数据类型为 `int` 的 `i` 的变量。

我们可以使用等于 (`=`) 运算符来初始化值，如下例所示：

```go
var i int = 1
```

在 Go 中，这两种声明形式都称为*长变量声明*。

我们也可以使用*短变量声明*：

```go
i := 1
```

在这种情况下，我们有一个名为 `i` 的变量和一个 `int` 数据类型。当我们不指定数据类型时，Go 会推断数据类型。

对于声明变量的三种方式，Go 社区采用了以下习惯用法：

- 仅在不初始化变量时使用长格式，`var i int`。
- 在声明和初始化时使用缩写形式，`i := 1`。
- 如果你不希望 Go 推断数据类型，但仍想使用短变量声明，则可以使用以下语法将值包装为所需类型：

```go
i := int64(1)
```

当我们初始化值时，长变量声明形式在 Go 中是不常用的：

```go
var i int = 1
```

遵循 Go 社区关于如何声明变量的方式，以便其他人可以无缝阅读你的程序。

## 零值

所有内置类型的默认值都为零值。任何分配的变量都是可用的，即使它从未分配过值。我们可以看到以下类型的零值：

```go
package main

import "fmt"

func main() {
	var a int
	var b string
	var c float64
	var d bool

	fmt.Printf("var a %T = %+v\n", a, a)
	fmt.Printf("var b %T = %q\n", b, b)
	fmt.Printf("var c %T = %+v\n", c, c)
	fmt.Printf("var d %T = %+v\n\n", d, d)
}
```

```go
Output
var a int =  0
var b string = ""
var c float64 = 0
var d bool = false
```

我们在 `fmt.Printf` 语句中使用了 `%T` 。这告诉函数打印变量的 `数据类型` 。

在 Go 中，因为所有值都有一个 `零` 值，所以我们不能像其他一些语言那样有“未定义”值。例如，某些语言中的 [`boolean`](https://gocn.github.io/How-To-Code-in-Go/docs/07-Understanding_Data_Types_in_Go/#%E5%B8%83%E5%B0%94%E5%80%BC) 可能是 `undefined`、`true` 或 `false`，它允许变量有`三种`状态。在 Go 中，一个布尔值的状态不能超过 `两个` 。

## 命名变量：规则和样式

变量的命名相当灵活，但有一些规则需要牢记：

- 变量名只能是一个单词（如没有空格）。
- 变量名称必须仅由字母、数字和下划线 (`_`) 组成。
- 变量名不能以数字开头。

按照这些规则，让我们看看有效和无效的变量名：

| Valid有效  | Invalid无效 | 无效原因         |
| ---------- | ----------- | ---------------- |
| `userName` | `user-name` | 不允许使用连字符 |
| `name4`    | `4name`     | 不能以数字开头   |
| `user`     | `$user`     | 不能使用符号     |
| `userName` | `user name` | 不能超过一个单词 |

此外，在命名变量时请记住它们区分大小写。这些名称 `userName`、`USERNAME`、`UserName` 和 `uSERNAME` 都是完全不同的变量。最好避免在程序中使用相似的变量名称，以确保你和你的合作者（当前和未来）都能保持变量的正确性。

虽然变量区分大小写，但变量首字母的大小写在 Go 中具有特殊含义。如果变量以大写字母开头，则该变量可以在声明它的包（或 `exporter` ）之外访问。如果变量以小写字母开头，则它仅在声明它的包中可用。

```go
var Email string
var password string
```

`Email` 以大写字母开头，可以被其他包访问。`password` 以小写字母开头，只能在声明它的包内访问。

在 Go 中使用非常简洁（或短）的变量名是很常见的。变量 `userName` 和 `user` 之间的选择，通常会选择 `user` 。

作用域在变量名称的简洁性方面也发挥了作用。规则是变量存在的作用域越小，变量名越小：

```go
names := []string{"Mary", "John", "Bob", "Anna"}
for i, n := range names {
	fmt.Printf("index: %d = %q\n", i, n)
}
```

我们在更大的作用域内使用变量 `names`，因此通常给它一个更有意义的名称以帮助记住它在程序中的含义。但是，我们在下一行代码中立即使用 `i` 和 `n` 变量，然后不再使用它们……因此，它不会让阅读代码的人混淆变量的使用位置，或者它们代表的意思。

接下来，让我们介绍一些关于变量样式的注意事项。使用 `MixedCaps` 或 `mixedCaps` 而不是下划线作为多词名称。

| 常用风格    | 非常用风格  | 为什么不常用                           |
| ----------- | ----------- | -------------------------------------- |
| `userName`  | `user_name` | 下划线不常用                           |
| `i`         | `index`     | 更倾向用 `i` 而不是 `index` 因为它更短 |
| `serveHTTP` | `serveHttp` | 专业词缩写应该用大写                   |

风格最重要的是要保持一致，并且你所在的团队同意这种风格。

## 重新分配变量

正如 `变量` 一词所暗示的那样，我们可以轻松地更改 Go 变量。这意味着我们可以通过重新分配将不同的值与先前分配的变量联系起来。能够重新分配变量是很有用的，因为在整个程序过程中，我们可能需要将用户生成的值赋给已经初始化的变量中。我们可能还需要将分配更改为先前定义的内容。

在处理其他人编写的大型程序时，知道变量可以被重新分配对我们是很有帮助的，特别是当我们不清楚哪些变量已经被定义了。

让我们将 `76` 的值分配给一个名为 `i` 的类型为 `int` 的变量，然后为它分配一个新的值 `42`：

```go
package main

import "fmt"

func main() {
	i := 76
	fmt.Println(i)

	i = 42
	fmt.Println(i)
}
```

```go
Output
76
42
```

这个例子表明我们可以先给变量 `i` 赋值一个整数，然后再重新赋值变量 `i` ，这次赋值为 `42`。

**注意：**当你声明**和**初始化一个变量时，你可以使用`:=`，但是，当你想简单地改变一个已经声明的变量的值时，你只需要使用等号运算符 (`=`)。

因为 Go 是一种静态强类型语言，我们不能将一种类型分配给另一种类型。例如，我们不能将值 `"Sammy"` 分配给 `int` 类型的变量：

```go
i := 72
i = "Sammy"
```

尝试相互分配不同的类型将导致编译时错误：

```go
Output
cannot use "Sammy" (type string) as type int in assignment
```

Go 不允许我们多次声明相同变量名：

```go
var s string
var s string
```

```go
Output
s redeclared in this block
```

如果我们尝试对同一个变量名多次使用短变量声明，我们也会收到编译错误。这可能会发生错误，因此了解错误消息的含义是有帮助的：

```go
i := 5
i := 10
```

```go
Output
no new variables on left side of :=
```

与变量声明类似，考虑变量的命名将提高你和其他人在将来维护程序的可读性。

## 多重赋值

Go 还允许我们为同一行中的多个变量分配多个值。这些值中的每一个都可以是不同的数据类型：

```go
j, k, l := "shark", 2.05, 15
fmt.Println(j)
fmt.Println(k)
fmt.Println(l)
```

```go
Output
shark
2.05
15
```

在这个例子中，变量 `j` 被赋值给字符串 `shark`，变量 `k` 被赋值给浮点数 `2.05`，变量 `l` 被赋值给整数 `15` 。

这种在一行中将多个变量分配给多个值的方法可以减少代码中的行数。但是，重要的是不要因为更少的代码行而损害可读性。

## 全局和局部变量

在程序中使用变量时，牢记*变量作用域*很重要。变量的作用域是指可以从给定程序的代码中访问的特定位置。也就是说，并非所有变量都可以从给定程序的所有部分访问——有些变量是全局变量，有些变量是局部变量。

全局变量存在于函数之外。局部变量存在于函数中。

让我们看一下全局和局部变量的作用：

```go
package main

import "fmt"


var g = "global"

func printLocal() {
	l := "local"
	fmt.Println(l)
}

func main() {
	printLocal()
	fmt.Println(g)
}
```

```go
Output
local
global
```

这里我们使用 `var g = "global"` 在函数外部创建一个全局变量。然后我们定义函数`printLocal()`。在函数内部，分配了一个名为 `l` 的局部变量，然后打印出来。程序先调用 `printLocal()` ，然后打印全局变量 `g`。

因为 `g` 是一个全局变量，我们可以在 `printLocal()` 中引用它。让我们修改之前的程序来做到这一点：

```go
package main

import "fmt"


var g = "global"

func printLocal() {
	l := "local"
	fmt.Println(l)
	fmt.Println(g)
}

func main() {
	printLocal()
	fmt.Println(g)
}
```

```go
Output
local
global
global
```

我们首先声明一个全局变量 `g` ，`var g = "global"`。在 `main` 函数中，我们调用函数`printLocal`，它声明了一个局部变量`l`并打印出来，`fmt.Println(l)`。然后，`printLocal` 打印出全局变量`g`，`fmt.Println(g)`。即使 `g` 没有在 `printLocal` 中定义，它仍然可以被访问，因为它是在全局作用域内声明的。最后，`main` 函数也打印出 `g`。

现在让我们尝试在函数外部调用局部变量：

```go
package main

import "fmt"

var g = "global"

func printLocal() {
	l := "local"
	fmt.Println(l)
}

func main() {
	fmt.Println(l)
}
```

```go
Output
undefined: l
```

我们不能在分配它的函数之外使用局部变量。如果你尝试这样做，编译时会收到 `未定义` 错误。

让我们看另一个例子，我们对全局变量和局部变量使用相同的变量名：

```go
package main

import "fmt"

var num1 = 5

func printNumbers() {
	num1 := 10
	num2 := 7  

	fmt.Println(num1)
	fmt.Println(num2)
}

func main() {
	printNumbers()
	fmt.Println(num1)
}
```

```go
Output
10
7
5
```

在这个程序中，我们两次声明了 `num1` 变量。首先，我们在全局作用域内声明了`num1`，`var num1 = 5`，然后再次在 `printNumbers` 函数的本地作用域内声明 `num1 := 10`。当我们从 `main` 程序中打印 `num1` 时，我们看到 `5` 的值被打印出来了。这是因为 `main` 只看到全局变量声明。但是，当我们从 `printNumbers` 函数中打印出 `num1` 时，它会看到本地声明，并会打印出 `10` 的值。尽管 `printNumbers` 创建了一个名为 `num1` 的新变量并为其分配了 `10` 的值，但它不会影响值为 `5` 的 `num1` 的全局实例。

使用变量时，你还需要考虑程序的哪些部分需要访问每个变量； 相应地采用全局或局部变量。在 Go 程序中，你会发现局部变量通常更常见。

## 常量

常量就像变量一样，除了它们一旦被声明就不能被修改。常量对于定义在程序中多次使用但不能更改的值很有用。

例如，如果我们想声明购物车系统的税率，我们可以使用一个常数，然后在程序的不同区域计算税收。在未来的某个时候，如果税率发生变化，我们只需在程序中的一个位置更改该值。如果我们使用了一个变量，我们可能会在程序中不小心更改某个值，从而导致计算不正确。

要声明一个常量，我们可以使用以下语法：

```go
const shark = "Sammy"
fmt.Println(shark)
```

```go
Output
Sammy
```

如果我们在声明后尝试修改常量，我们会得到一个编译时错误：

```go
Output
cannot assign to shark
```

常量可以是 `untyped` 。这在处理整数类型数据等数字时很有用。如果常量是 `untyped`，它会被显式转换，而 `typed` 常量则不是。让我们看看如何使用常量：

```go
package main

import "fmt"

const (
	year     = 365
	leapYear = int32(366)
)

func main() {
	hours := 24
	minutes := int32(60)
	fmt.Println(hours * year)    
	fmt.Println(minutes * year)   
	fmt.Println(minutes * leapYear)
}
```

```go
Output
8760
21900
21960
```

如果你用一个类型声明一个常量，它将是那个确切的类型。在这里，当我们声明常量 `leapYear` 时，我们将其定义为数据类型 `int32`。因此它是一个 `typed` 常量，这意味着它只能对 `int32` 数据类型进行操作。我们声明的 `year` 常量没有类型，所以它被认为是 `untyped`。因此，你可以将它与任何整数数据类型一起使用。

当`hours`被定义时，它*推断*它是`int`类型，因为我们没有明确地给它一个类型，`hours := 24`。当我们声明 `minutes` 时，我们明确地将其声明为 `int32`，`minutes := int32(60)`。

现在让我们来看看每个计算以及它的工作原理：

```go
hours * year
```

在这种情况下，`hours` 是 `int`，而 `years` 是 *untyped*。当程序编译时，它显式地将 `years` 转换为 `int`，这使得乘法运算成功。

```go
minutes * year
```

在这种情况下，`minutes` 是 `int32`，而 `year` 是 *untyped*。当程序编译时，它显式地将 `years` 转换为 `int32`，从而允许乘法运算成功。

```go
minutes * leapYear
```

在这种情况下，`minutes` 是 `int32`，而 `leapYear` 是 `int32` 的 *typed* 常量。这次编译器无需执行任何操作，因为这两个变量已经属于同一类型。

如果我们尝试将两种类型不兼容的类型相乘，程序将无法编译：

```go
fmt.Println(hours * leapYear)
```

```go
Output
invalid operation: hours * leapYear (mismatched types int and int32)
```

在这种情况下，`hours` 被推断为 `int`，而 `leapYear` 被显式声明为 `int32`。因为 Go 是一种强类型语言，所以 `int` 和 `int32` 不兼容数学运算。要将它们相乘，你需要 [将其中一个转换为 `int32` 或 `int`](https://gocn.github.io/How-To-Code-in-Go/docs/12-How_To_Convert_Data_Types_in_Go/#%E6%95%B0%E5%AD%97%E7%B1%BB%E5%9E%8B%E8%BD%AC%E6%8D%A2)。

## 结论

在本教程中，我们回顾了 Go 中变量的一些常见用例。变量是编程的重要组成部分，充当我们在程序中使用的数据类型的值的代表。