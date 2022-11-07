# 如何在 Go 中定义并调用函数

## 介绍

_函数_是一段一旦定义，就可以重用的代码。函数的作用在于可以通过将在整个程序中多次使用的代码分解为更小、更可理解的任务，从而使您的代码更易于理解。

Go 附带了强大的标准库，其中包含许多预定义的函数。您可能已经熟悉了[fmt](https://golang.org/pkg/fmt/)包：

-   `fmt.Println()` 会将对象打印到标准输出（最可能在您的终端）。
-   `fmt.Printf()` 允许您将输出格式化打印。

函数名称包括括号，并且可能包括参数。

在本教程中，我们将介绍如何定义您自己的函数以在您的项目中使用。

## 定义一个函数

让我们从经典的[“Hello, World!”程序](https://gocn.github.io/How-To-Code-in-Go/docs/04-How_To_Write_Your_First_Program_in_Go_DigitalOcean)开始理解函数。

我们将在一个文本编辑器中创建一个新的文本文件，然后调用程序 `hello.go`。然后，我们将在里面定义函数。

Go 中使用 `func` 关键字来定义函数。然后是您选择的名称和一组括号，其中包含函数将采用的任何参数（它们可以为空）。函数代码行用大括号 `{}` 括起来。

在这种情况下，我们将定义一个名为 `hello()` 的函数：

```go
func hello() {}
```

这就是用于创建函数的初始语句。

基于此，我们将添加第二行来提供函数功能的说明。我们将打印 `Hello, World!` 到控制台：

```go
func hello() {
	fmt.Println("Hello, World!")
}
```

现在我们的函数已经完全定义好了，但是如果我们此时运行程序，什么都不会发生，因为我们没有调用函数。

因此，在我们的 `main()` 代码块中，来调用 `hello()` 函数：

```go
package main

import "fmt"

func main() {
	hello()
}

func hello() {
	fmt.Println("Hello, World!")
}
```

现在，让我们运行程序：

```shell
$ go run hello.go
```

您将收到以下输出：

```shell
Output
Hello, World!
```

请注意，我们还引入了一个名为 `main()` 的函数。`main()` 函数是一个特殊的函数，它告诉编译器程序应该从这里**开始**。对于 _可执行_ 的任何程序（可以从命令行运行的程序），都需要一个 `main()` 函数。`main()` 函数只能在 `main()` _包_ 中出现一次，并且不接收和返回任何参数。在任何 Go 程序中[程序执行](https://golang.org/ref/spec#Program_execution)都是这样的。根据以下示例：

```go
package main

import "fmt"

func main() {
	fmt.Println("this is the main section of the program")
}
```

函数可以比我们定义的 `hello()` 函数更复杂。我们可以在函数中使用[`for`循环](https://gocn.github.io/How-To-Code-in-Go/docs/25-How_To_Construct_For_Loops_in_Go)、[条件语句](https://gocn.github.io/How-To-Code-in-Go/docs/23-How_To_Write_Conditional_Statements_in_Go)等。

例如，以下函数使用条件语句检查 `name` 变量的输入是否包含元音，并使用 `for` 循环遍历 `name` 字符串中的字母。

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	names()
}

func names() {
	fmt.Println("Enter your name:")

	var name string
	fmt.Scanln(&name)
	// Check whether name has a vowel
	for _, v := range strings.ToLower(name) {
		if v == 'a' || v == 'e' || v == 'i' || v == 'o' || v == 'u' {
			fmt.Println("Your name contains a vowel.")
			return
		}
	}
	fmt.Println("Your name does not contain a vowel.")
}
```

我们在这里定义的 `names()` 函数设置一个带有输入的变量 `name`，然后在一个 `for` 循环中设置一个条件语句。这显示了如何在函数定义中组织代码。但是，根据我们对程序的意图以及我们对代码的安排，我们可能希望将条件语句和循环定义为两个独立的函数。

在程序中定义函数可使我们的代码更模块化和可重用，这样我们就可以调用相同的函数而无需重写它们。

## 使用参数

到目前为止，我们已经研究了带有空括号且不带参数的函数，但我们是可以在函数定义中的括号内定义参数的。

_参数_ 是函数定义中的命名实体，指定函数可以接受的参数。在 Go 中，您必须为每个参数指定[数据类型](https://gocn.github.io/How-To-Code-in-Go/docs/07-Understanding_Data_Types_in_Go)。

让我们创建一个将单词重复指定次数的程序。它将接受一个 `string` 类型的 `word` 参数和一个用于重复单词的次数的 `int` 类型参数 `reps`。

```go
package main

import "fmt"

func main() {
	repeat("Sammy", 5)
}

func repeat(word string, reps int) {
	for i := 0; i < reps; i++ {
		fmt.Print(word)
	}
}
```

我们分别为 `word` 参数和 `reps` 参数传递了`Sammy` 和 `5` 值。这些值按照给定的顺序与每个参数相对应。`repeat` 函数有一个 `for` 循环，将循环参数 `reps` 指定的次数。对于每次循环，都会打印参数 `word`  的值。

这是程序的输出：

```shell
Output
SammySammySammySammySammy
```

如果你有一组参数都是相同的值，你可以不用每次指定类型。让我们创建一个小程序，它接受都是 `int` 值的参数 `x`,  `y`, 和 `z` 这些。我们将创建一个函数，函数将打印他们的总和。下面我们将调用该函数并将数字传递给该函数。

```go
package main

import "fmt"

func main() {
	addNumbers(1, 2, 3)
}

func addNumbers(x, y, z int) {
	a := x + y
	b := x + z
	c := y + z
	fmt.Println(a, b, c)
}
```

当我们创建 `addNumbers` 的函数签名时，我们不需要每次都指定类型，而只需要在最后指定。

我们将数字 `1` 传入参数 `x`，`2` 传入参数 `y`，`3` 传入参数 `z`。这些值按照给定的顺序与每个参数对应。

该程序会根据我们传递参数的值进行以下数学运算：

```shell
a = 1 + 2
b = 1 + 3
c = 2 + 3
```

该函数会打印 `a`，`b`， `c` 的值。基于这个数学运算，我们期望 `a` 等于 `3`， `b` 等于 `4`，`c` 等于 `5`。让我们运行程序：

```shell
$ go run add_numbers.go
```

```shell
Output
3 4 5
```

当我们将 `1`、`2` 和 `3` 作为参数传递给 `addNumbers()` 函数时，我们会收到预期的输出。

在函数定义中的参数通常是作为变量使用，当您运行方法时，会将参数传递给函数，并为它们赋值。

## 返回值

您可以将参数值传递给函数，一个函数也可以产生值。

函数可以通过 `return` 语句生成一个值，`return` 语句将退出函数并 _可选地_ 将表达式传递回调用者。返回的数据类型必须是指定过的。

到目前为止，我们在函数中使用了`fmt.Println()` 语句而不是 `return` 语句。让我们创建一个程序，返回一个变量。

在一个名为 `double.go` 的新文本文件中，我们将创建一个将参数 `x` 加倍并返回变量 `y` 的程序。我们将 `3` 作为`double()` 函数的参数，然后打印 `result` 的值。

```go
package main

import "fmt"

func main() {
	result := double(3)
	fmt.Println(result)
}

func double(x int) int {
	y := x * 2
	return y
}
```

我们可以运行程序并查看输出：
```shell
$ go run double.go
```

```shell
Output
6
```

整数 `6` 将作为输出返回，这正是所期望的 `3` 乘以 `2`的结果。

如果函数指定了返回值，则必须在代码中提供返回值。否则，将收到编译错误。

我们可以通过用 return 语句注释掉这一行来证明这一点：

```go
package main

import "fmt"

func main() {
	result := double(3)
	fmt.Println(result)
}

func double(x int) int {
	y := x * 2
	// return y
}
```

现在，让我们再次运行程序：
```shell
$ go run double.go
```

```shell
Output
./double.go:13:1: missing return at end of function
```

如果不使用此处的 `return` 语句，程序将无法编译。

函数在遇到 `return` 语句时立即退出，即使它们不在函数末尾：

```go
package main

import "fmt"

func main() {
	loopFive()
}

func loopFive() {
	for i := 0; i < 25; i++ {
		fmt.Print(i)
		if i == 5 {
			// Stop function at i == 5
			return
		}
	}
	fmt.Println("This line will not execute.")
}
```

这里我们设置一个 `for` 循环，循环运行 `25` 次。但是，在 `for` 循环内部，我们有一个条件语句来检查 `i` 的值是否等于 `5`。如果等于，我们将 `return` 进行返回。因为我们在 `loopFive` 函数中，所以函数中的任何一个 `return` 都会退出函数。所以，我们永远不会到达该函数的最后一行来打印 `This line will not execute.`语句。

在 `for` 循环内使用了 `return` 语句来结束函数，因此循环外的行将不会运行。相反，如果我们使用了[`break`语句](https://gocn.github.io/How-To-Code-in-Go/docs/26-Using_Break_and_Continue_Statements_When_Working_with_Loops_in_Go/#break-%E8%AF%AD%E5%8F%A5)，那么此时只有循环会退出，最后 `fmt.Println()` 一行会被运行。

`return` 语句能够退出一个函数，并且如果在函数签名中指定，则会返回一个值。

## 返回多个值

一个函数可以指定多个返回值。让我们编写 `repeat.go` 程序并让它返回两个值。第一个返回值是得到的最终重复值，第二个返回值在参数 `reps` 小于等于 `0` 时会得到一个错误。

```go

package main

import "fmt"

func main() {
	val, err := repeat("Sammy", -1)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(val)
}

func repeat(word string, reps int) (string, error) {
	if reps <= 0 {
		return "", fmt.Errorf("invalid value of %d provided for reps. value must be greater than 0.", reps)
	}
	var value string
	for i := 0; i < reps; i++ {
		value = value + word
	}
	return value, nil
}
```

`repeat` 函数首先检查 `reps` 参数是否为有效值。任何不大于 `0` 的值都会导致错误。由于我们传入了 `-1`，因此该代码分支将执行。请注意，当我们从函数返回时，我们必须同时提供 `string` 和 `error` 的返回值。因为提供的参数导致了错误，我们将为第一个返回值传回一个空白字符串，为第二个返回值传回错误。

在 `main()` 函数中，我们可以通过声明两个新变量来接收两个返回值，`value` 和 `err`。因为返回中可能有错误，我们想在继续程序之前检查是否收到错误。在这个例子中，我们确实收到了一个错误。我们打印出错误并 `return` 返回退出`main()` 函数以退出程序。

如果没有错误，我们将打印出函数的返回值。

**注意：** 最好只返回两个或三个值。此外，您应该始终将错误作为函数的最后一个返回值返回。

运行程序将产生以下输出：

```text
output
invalid value of -1 provided for reps. value must be greater than 0.
```

在本节中，我们回顾了如何使用 `return` 语句从函数返回多个值。

## 结论

函数是在程序中执行操作指令的代码块，有助于使我们的代码更好地可重用和模块化。

要了解有关如何使您的代码更模块化的更多信息，您可以阅读我们关于[如何在 Go 中编写包](https://gocn.github.io/How-To-Code-in-Go/docs/21-How_To_Write_Packages_in_Go)的指南。
