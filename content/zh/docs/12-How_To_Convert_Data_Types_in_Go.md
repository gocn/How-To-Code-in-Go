# 如何在 Go 中转换数据类型

### 介绍

在 Go 中，数据类型用于对一种特定类型的数据进行分类，确定可以分配给该类型的值以及可以对其执行的操作。在编程时，有时你需要在类型之间转换值以便以不同的方式操作值。例如，你可能需要将数值与字符串连接起来，或者在初始化为整数值的数字中表示小数位。用户生成的数据通常会自动分配为字符串数据类型，即使它由数字组成； 为了在此输入中执行数学运算，你必须将字符串转换为数字数据类型。

由于 Go 是一种静态类型语言，[数据类型绑定到变量](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#declaring-data-types-for-variables ) 而不是值。这意味着，如果你将变量定义为 `int`，它只能是 `int`； 如果不转换变量的数据类型，就无法将一个 `字符串` 赋值给它。Go 中数据类型的静态特性使得学习转换它们的方法更加重要。

本教程将指导你完成数字和字符串的转换，并提供示例以帮助你熟悉不同的用例。

## 数字类型转换

Go 有多种数字类型可供选择。它们主要分为两种类型：[integers](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#integers) 和 [floating-point numbers](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#floating-point-numbers)。

在许多情况下，你可能希望在数字类型之间进行转换。在 [不同大小的数字类型](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#sizes-of-numeric-types) 之间进行转换有助于优化特定场景下的性能。如果你有来自代码另一部分的整数并想要对其进行除法，你可能需要将整数转换为浮点数以保持操作的精度。此外，时间相关的操作通常涉及整数转换。为了解决这些情况，Go 为大多数数字类型提供了内置的*类型转换*。

### 整数类型之间的转换

Go 有许多整数数据类型可供选择。何时使用另一种通常更多要考虑的是[性能](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#picking-numeric-data-types)； 但是，有时你需要从一种整数类型转换为另一种整数类型。例如，Go 有时会自动生成数值为 `int`，这可能与你的输入值不匹配。如果你的输入值为 `int64`，则在将它们的数据类型转换为匹配之前，你将无法在同一数学表达式中使用 `int` 和 `int64` 。

假设你有一个 `int8` 并且需要将其转换为 `int32`。你可以通过 `int32()` 类型转换来做到这一点：

```go
var index int8 = 15

var bigIndex int32

bigIndex = int32(index)

fmt.Println(bigIndex)
```

```go
Output
15
```

此代码块将 `index` 定义为 `int8` 数据类型，将 `bigIndex` 定义为 `int32` 数据类型。要将 `index` 的值存储在 `bigIndex` 中，它将数据类型转换为 `int32`。这是通过 `int32()` 包装 `index` 变量来完成的。

要验证你的数据类型，你可以使用 `fmt.Printf` 语句和 `%T` ，语法如下：

```go
fmt.Printf("index data type:    %T\n", index)
fmt.Printf("bigIndex data type: %T\n", bigIndex)
```

```go
Output
index data type:    int8
bigIndex data type: int32
```

由于使用了 `%T` ，打印语句会输出变量的类型，而不是变量的实际值。这样，你可以确认转换后的数据类型。

你还可以从较大整数类型转换为较小整数类型：

```go
var big int64 = 64

var little int8

little = int8(big)

fmt.Println(little)
```

```go
Output
64
```

Keep in mind that when converting integers you could potentially exceed the maximum value of the data type and [*wraparound*](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#overflow-vs-wraparound):请记住，在转换整数时，你可能会超过数据类型的最大值和 [*wraparound*](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#overflow-vs-wraparound)：

```go
var big int64 = 129
var little = int8(big)
fmt.Println(little)
```

```go
Output
-127
```

当值转换为太小而无法容纳它的数据类型时，就会发生出。在前面的示例中，8 位的数据类型 `int8` 没有足够的空间来容纳 64 位的变量 `big` 。从较大数据类型转换为较小数据类型时应始终小心，以免意外截断数据。

### 将整数转换为浮点数

在 Go 中将整数转换为浮点数类似于将一种整数类型转换为另一种。你可以通过给要转换的整数包装 `float64()` 或 `float32()` 来使用内置类型转换：

```go
var x int64 = 57

var y float64 = float64(x)

fmt.Printf("%.2f\n", y)
```

```go
Output
57.00
```

这段代码声明了一个类型为 `int64` 的变量 `x` 并将其值初始化为 `57` 。

```go
var x int64 = 57
```

对 `x` 使用 `float64()` 会将 `57` 转换为 `57.00` 的浮点值。

```go
var y float64 = float64(x)
```

`%.2f` 告诉 `fmt.Printf` 用两位小数格式化浮点数。

你也可以对变量这么操作。以下代码将 `f` 声明为 `57`，然后打印出新的浮点数：

```go
var f float64 = 57
fmt.Printf("%.2f\n", f)
```

```go
Output
57.00
```

通过使用 `float32()` 或 `float64()`，你可以将整数转换为浮点数。接下来，你将学习如何将浮点数转换为整数。

### 将浮点数转换为整数

Go 可以将浮点数转换为整数，但程序会丢失浮点数的精度。

使用 `int()` 或其他非浮点数据类型包装浮点数，其工作方式与整数类型转换类似。你可以在括号内添加一个浮点数以将其转换为整数：

```go
var f float64 = 390.8
var i int = int(f)

fmt.Printf("f = %.2f\n", f)
fmt.Printf("i = %d\n", i)
```

```go
Output
f = 390.80
i = 390
```

此语法会将浮点数 `390.8` 转换为整数 `390`，去掉小数位。

你也可以将其与变量一起使用。以下代码声明 `b` 等于 `125.0` 和 `c` 等于 `390.8`，然后将它们打印为整数。短变量声明（`:=`）缩短了语法：

```go
b := 125.0
c := 390.8

fmt.Println(int(b))
fmt.Println(int(c))
```

```go
Output
125
390
```

当使用 `int()` 类型将浮点数转换为整数时，Go 会截断浮点数的小数并保留剩余数字部分来创建整数。请注意，即使你可能希望将 390.8 向上取整到 391，Go 也不会通过 `int()` 类型执行此操作。相反，它会去掉小数点。

### 除法中的类型转换

在 Go 中除以整数类型时，结果也将是整数类型，去掉了 *模数* 或余数：

```go
a := 5 / 2
fmt.Println(a)
```

```go
Output
2
```

在做除法操作时，如果有数字类型是浮点数的话，那么所有类型都将自动声明为浮点数：

```go
	a := 5.0 / 2
	fmt.Println(a)
```

```go
Output
2.5
```

上例将浮点数 `5.0` 除以整数 `2` ，答案 `2.5` 是一个保留小数精度的浮点数。

在本节中，你已经在不同的数字数据类型之间进行了转换，包括不同大小的整数和浮点数。接下来，你将学习如何在数字和字符串之间进行转换。

## 字符串转换

*字符串* 是一个或多个字符（字母、数字或符号）组成的序列。字符串是计算机程序中一种常见的数据形式，你可能需要经常将字符串转换为数字或将数字转换为字符串，尤其是在你接收用户生成的数据时。

### 将数字转换为字符串

你可以使用 Go 标准库中 `strconv` 包中的 `strconv.Itoa` 方法将数字转换为字符串。如果将数字或变量传递给该方法，则该数字值将被转换为字符串。

首先，让我们看看转换整数。要将整数 `12` 转换为字符串，可以将 `12` 传递给 `strconv.Itoa` 方法：

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	a := strconv.Itoa(12)
	fmt.Printf("%q\n", a)
}
```

运行此程序时，你将收到以下输出：

```go
Output
"12"
```

数字 `12` 周围的引号表示该数字不再是整数，而是字符串。

你使用 `:=` 赋值运算符来声明一个名为 `a` 的新变量，并将 `strconv.Itoa()` 函数返回的值赋值给它。此时值 `12` 被分配给了变量。你通过在 `fmt.Printf` 函数中使用 `%q` 来告诉函数引用提供的字符串参数。

通过使用变量，你更能了解将整数转换为字符串的实际情况。假设你想跟踪用户的日常编程进度，并输入他们一次编写的代码行数。你希望将此反馈显示给用户，并将同时打印出字符串和整数值：

```go
package main

import (
	"fmt"
)

func main() {
	user := "Sammy"
	lines := 50

	fmt.Println("Congratulations, " + user + "! You just wrote " + lines + " lines of code.")
}
```

运行此代码时，你将收到以下错误：

```go
Output
invalid operation: ("Congratulations, " + user + "! You just wrote ") + lines (mismatched types string and int)
```

你不能在 Go 中拼接字符串和整数，所以你必须将变量 `lines` 转换为字符串：

```
package main

import (
	"fmt"
	"strconv"
)

func main() {
	user := "Sammy"
	lines := 50

	fmt.Println("Congratulations, " + user + "! You just wrote " + strconv.Itoa(lines) + " lines of code.")
}
```

现在，当你运行代码时，你将收到以下输出，祝贺你的用户取得了进展：

```go
Output
Congratulations, Sammy! You just wrote 50 lines of code.
```

如果你希望将浮点数转换为字符串而不是将整数转换为字符串，请遵循类似的步骤和格式。当你将浮点数传递给 `fmt.Sprint` 方法时，从 Go 标准库中的 `fmt` 包中，将返回浮点数的字符串值。你可以使用浮点值本身或变量：

```go
package main

import (
	"fmt"
)

func main() {
	fmt.Println(fmt.Sprint(421.034))

	f := 5524.53
	fmt.Println(fmt.Sprint(f))
}
```

```go
Output
421.034
5524.53
```

你可以通过拼接字符串来测试以确保它是正确的：

```go
package main

import (
	"fmt"
)

func main() {
	f := 5524.53
	fmt.Println("Sammy has " + fmt.Sprint(f) + " points.")
}
```

```go
Output
Sammy has 5524.53 points.
```

你可以确定你的浮点数已正确转换为字符串，因为拼接执行时没有报错。

### 将字符串转换为数字

可以使用 Go 标准库中的 `strconv` 包将字符串转换为数字。`strconv` 包具有转换整数和浮点数类型的功能。这是接受用户输入时非常常见的操作。例如，如果你有一个程序询问一个人的年龄，当他们输入值时，它会被捕获为 `字符串` 。然后，你需要将其转换为 `int` 以对其进行任何数学运算。

如果你的字符串没有小数位，你很可能希望使用 strconv.Atoi 函数将其转换为整数。如果你知道你将使用数字作为浮点数，你会使用`strconv.ParseFloat`。

让我们使用用户 Sammy 跟踪每天编写的代码行的示例。你可能希望使用运算符来操作这些值，以便为用户提供更有趣的反馈，但这些值当前存储在字符串中：

```go
package main

import (
	"fmt"
)

func main() {
	lines_yesterday := "50"
	lines_today := "108"

	lines_more := lines_today - lines_yesterday

	fmt.Println(lines_more)
}
```

```go
Output
invalid operation: lines_today - lines_yesterday (operator - not defined on string)
```

因为这两个数值存储在字符串中，所以你收到报错。减法运算符 `-` 不能对两个字符串执行。

修改代码来增加 `strconv.Atoi()` 方法，该方法能将字符串转换为整数，这将允许你对最初是字符串的值进行数学运算。因为在将字符串转换为整数时可能会失败，所以你必须检查是否有任何报错。你可以使用 `if` 语句来检查你的转换是否成功。

```
package main

import (
	"fmt"
	"log"
	"strconv"
)

func main() {
	lines_yesterday := "50"
	lines_today := "108"

	yesterday, err := strconv.Atoi(lines_yesterday)
	if err != nil {
		log.Fatal(err)
	}

	today, err := strconv.Atoi(lines_today)
	if err != nil {
		log.Fatal(err)
	}
	lines_more := today - yesterday

	fmt.Println(lines_more)
}
```

因为字符串可能不是数字，所以 strconv.Atoi() 方法将返回转换后的类型以及潜在的错误。当使用 `strconv.Atoi` 函数从 `lines_yesterday` 转换时，你必须检查 `err` 返回值以确保该值已被转换。如果 `err` 不是 `nil`，则意味着 `strconv.Atoi` 无法成功将字符串值转换为整数。在这个例子中，你使用了一个 `if` 语句来检查错误，如果返回了一个错误，你使用了 `log.Fatal` 来记录错误并退出程序。

当你运行前面的代码时，你会得到：

```go
Output
58
```

现在尝试转换一个不是数字的字符串：

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	a := "not a number"
	b, err := strconv.Atoi(a)
	fmt.Println(b)
	fmt.Println(err)
}
```

你将收到以下报错：

```go
Output
0
strconv.Atoi: parsing "not a number": invalid syntax
```

因为声明了 `b`，但 `strconv.Atoi` 未能进行转换，所以 `b` 没能被赋值。请注意，`b` 的值为 `0` 。这是因为 Go 有默认值，在 Go 中称为零值。`strconv.Atoi` 返回了一个错误，描述了为什么它无法转换字符串。

### 转换字符串和字节

Go 中的字符串存储为字节切片。在 Go 中，你可以通过将其包装在 `[]byte()` 和 `string()` 的相应转换中来在字节切片和字符串之间进行转换：

```go
package main

import (
	"fmt"
)

func main() {
	a := "my string"

	b := []byte(a)

	c := string(b)

	fmt.Println(a)

	fmt.Println(b)

	fmt.Println(c)
}
```

在这里，你将字符串存储在 `a` 中，然后将其转换为字节切片`b`，然后将字节切片转换回字符串并赋值给`c`。最后打印出来：

```go
Output
my string
[109 121 32 115 116 114 105 110 103]
my string
```

输出的第一行是原始字符串 `my string`。打印出来的第二行是组成原始字符串的字节切片。第三行显示字节切片可以安全地转换回字符串并打印出来。

## 结论

本 Go 教程主要演示了如何通过内置方法将几种重要的数据类型转换为其他数据类型。能够在 Go 中转换数据类型将允许你执行诸如接受用户输入和跨不同数字类型进行数学运算之类的事情。稍后，当你使用 Go 编写接受来自许多不同来源（如数据库和 API）的数据的程序时，你将使用这些转换方法来确保你可以对数据进行操作。你还可以通过将数据转换为更小的数据类型来优化存储。

如果你想深入分析 Go 中的数据类型，请查看我们的 [了解 Go 中的数据类型](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go) 文章。
