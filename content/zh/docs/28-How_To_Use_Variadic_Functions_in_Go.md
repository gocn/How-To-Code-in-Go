# 如何在 Go 中使用可变参数函数

## 介绍

_可变参数函数_ 是可以接受零个、一个或多个值作为单个参数的函数。虽然可变参数函数并不常见，但它们能够使您的代码更清晰、更具可读性。

可变参数函数其实很常见。最常见的是[`fmt`](https://golang.org/pkg/fmt)包中的 `Println`。

```go
func Println(a ...interface{}) (n int, err error)
```

参数前面带有一组省略号 ( `...` )的[函数]({{< relref "/docs/27-How_To_Define_and_Call_Functions_in_Go.md" >}})被视为可变参数函数。省略号表示提供的参数可以是零个、一个或多个。对于`fmt.Println`包，它声明参数`a`是可变参数。

让我们创建一个使用 `fmt.Println` 函数并传入零个、一个或多个值的程序：

```go
package main

import "fmt"

func main() {
	fmt.Println()
	fmt.Println("one")
	fmt.Println("one", "two")
	fmt.Println("one", "two", "three")
}
```

第一次调用 `fmt.Println` 时，我们不传递任何参数。第二次调用时，我们只传入一个参数，值为 `one`。 然后我们传递 `one` 和 `two`，最后是 `one`，`two` 和 `three` 三个值。

让我们使用以下命令运行程序：

```shell
$ go run print.go
```

我们将看到以下输出：

```shell
Output
one
one two
one two three
```

输出的第一行为空。这是因为我们在第一次调用 `fmt.Println` 时没有传递任何参数。第二次打印了 `one` 。然后打印 `one` 和 `two`，最后打印 `one`，`two` 和 `three`。

现在我们已经了解了如何调用可变参数函数，让我们看看如何定义自己的可变参数函数。

## 定义可变参数函数

我们可以通过在参数前面使用省略号 ( `...` ) 来定义可变参数函数。让我们创建一个程序，当人们的名字被发送到函数时会进行问候：

```go
package main

import "fmt"

func main() {
	sayHello()
	sayHello("Sammy")
	sayHello("Sammy", "Jessica", "Drew", "Jamie")
}

func sayHello(names ...string) {
	for _, n := range names {
		fmt.Printf("Hello %s\n", n)
	}
}
```

我们创建了一个 `sayHello` 函数，它只接受一个名为`names`，该参数是可变参数，因为我们在数据类型之前放置了一个省略号 (`...`)： `...string`。这告诉 Go 这个函数可以接受零个、一个或多个参数。

`sayHello` 函数将 `names` 参数作为 [`slice`](https://gocn.github.io/How-To-Code-in-Go/docs/16-Understanding_Arrays_and_Slices_in_Go/#%E5%88%87%E7%89%87) 接收。由于数据类型是 [`string`](https://gocn.github.io/How-To-Code-in-Go/docs/07-Understanding_Data_Types_in_Go/#%E5%AD%97%E7%AC%A6%E4%B8%B2)，因此 `names` 可以将参数视为字符串切片 ( `[]string` ) 。我们可以使用[`range`](https://gocn.github.io/How-To-Code-in-Go/docs/25-How_To_Construct_For_Loops_in_Go/#%E4%BD%BF%E7%94%A8-rangeclause-%E5%BE%AA%E7%8E%AF%E9%81%8D%E5%8E%86%E9%A1%BA%E5%BA%8F%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B)运算符创建一个循环并遍历字符串切片。

如果我们运行程序，我们将得到以下输出：

```shell
Output
Hello Sammy
Hello Sammy
Hello Jessica
Hello Drew
Hello Jamie
```

请注意，我们第一次调用 `sayHello` 什么也没打印。这是因为可变参数是一个空的字符串切片。 由于我们会遍历切片，因此没有任何内容可以迭代，并且 `fmt.Printf` 永远不会被调用。

让我们修改程序以检测接收到值的情况：

```go
package main

import "fmt"

func main() {
	sayHello()
	sayHello("Sammy")
	sayHello("Sammy", "Jessica", "Drew", "Jamie")
}

func sayHello(names ...string) {
	if len(names) == 0 {
		fmt.Println("nobody to greet")
		return
	}
	for _, n := range names {
		fmt.Printf("Hello %s\n", n)
	}
}
```

现在，通过使用[`if`语句](https://gocn.github.io/How-To-Code-in-Go/docs/23-How_To_Write_Conditional_Statements_in_Go/#if-%E8%AF%AD%E5%8F%A5)，如果没有传递任何值，则 `names` 长度将为 `0`，我们将打印出`nobody to greet`：

```shell
Output
nobody to greet
Hello Sammy
Hello Sammy
Hello Jessica
Hello Drew
Hello Jamie
```

使用可变参数可以使您的代码更具可读性。让我们创建一个函数，将单词通过指定的分隔符连接在一起。我们将首先创建没有可变参数函数的程序来展示它的读取方式：

```go
package main

import "fmt"

func main() {
	var line string

	line = join(",", []string{"Sammy", "Jessica", "Drew", "Jamie"})
	fmt.Println(line)

	line = join(",", []string{"Sammy", "Jessica"})
	fmt.Println(line)

	line = join(",", []string{"Sammy"})
	fmt.Println(line)
}

func join(del string, values []string) string {
	var line string
	for i, v := range values {
		line = line + v
		if i != len(values)-1 {
			line = line + del
		}
	}
	return line
}
```

在这个程序中，我们将逗号 (`,`) 作为分隔符传递给 `join` 函数，再传递一个字符串切片。输出如下：

```shell
Output
Sammy,Jessica,Drew,Jamie
Sammy,Jessica
Sammy
```

因为该函数将字符串切片作为 `values` 参数，所以当我们调用 `join` 函数时，我们必须将所有单词包装在一个切片中。这会使代码难以阅读。

现在，让我们编写相同的函数，但我们将使用可变参数函数：

```go
package main

import "fmt"

func main() {
	var line string

	line = join(",", "Sammy", "Jessica", "Drew", "Jamie")
	fmt.Println(line)

	line = join(",", "Sammy", "Jessica")
	fmt.Println(line)

	line = join(",", "Sammy")
	fmt.Println(line)
}

func join(del string, values ...string) string {
	var line string
	for i, v := range values {
		line = line + v
		if i != len(values)-1 {
			line = line + del
		}
	}
	return line
}
```

如果我们运行该程序，可以看到我们得到了与之前的程序相同的输出：

```shell
Output
Sammy,Jessica,Drew,Jamie
Sammy,Jessica
Sammy
```

虽然这两个版本的 `join` 函数执行完全相同的操作，但可变参数函数的版本在调用时更容易阅读。

## 可变参数顺序

一个函数中只能有一个可变参数，并且它必须是函数定义中的最后一个参数。将可变参数放在最后位置以外的任何顺序在函数定义会导致编译错误：

```go
package main

import "fmt"

func main() {
	var line string

	line = join(",", "Sammy", "Jessica", "Drew", "Jamie")
	fmt.Println(line)

	line = join(",", "Sammy", "Jessica")
	fmt.Println(line)

	line = join(",", "Sammy")
	fmt.Println(line)
}

func join(values ...string, del string) string {
	var line string
	for i, v := range values {
		line = line + v
		if i != len(values)-1 {
			line = line + del
		}
	}
	return line
}
```

这次我们把 `values` 参数放在 `join` 函数的首位时，将导致以下编译错误：

```shell
Output
./join_error.go:18:11: syntax error: cannot use ... with non-final parameter values
```

定义任何可变参数函数时，只有最后一个参数可以是可变参数。

## 分解参数

到目前为止，我们已经看到我们可以将零个、一个或多个值传递给可变参数函数。但是，有时我们有一个切片并且我们希望将它们发送到可变参数函数。

让我们用上一节中的 `join` 函数尝试一下，看看会发生什么：

```go
package main

import "fmt"

func main() {
	var line string

	names := []string{"Sammy", "Jessica", "Drew", "Jamie"}

	line = join(",", names)
	fmt.Println(line)
	}

func join(del string, values ...string) string {
	var line string
	for i, v := range values {
		line = line + v
		if i != len(values)-1 {
			line = line + del
		}
	}
	return line
}
```

如果我们运行这个程序，我们会收到一个编译错误：

```shell
Output
./join-error.go:10:14: cannot use names (type []string) as type string in argument to join
```

即使可变参数函数将 `values ...string` 参数转换为字符串切片 `[]string`，我们也不能将字符串切片作为参数传递。这是因为编译器需要的是字符串的离散参数。

为了解决这个问题，我们可以通过在 _切片_ 后面加上一组省略号 (`...`) 来分解切片，将其转换为离散参数，然后传递给可变参数函数：


```go
package main

import "fmt"

func main() {
	var line string

	names := []string{"Sammy", "Jessica", "Drew", "Jamie"}

	line = join(",", names...)
	fmt.Println(line)
}

func join(del string, values ...string) string {
	var line string
	for i, v := range values {
		line = line + v
		if i != len(values)-1 {
			line = line + del
		}
	}
	return line
}
```

这一次，当我们调用 `join` 函数时，我们通过附加省略号 (`...`)来分解 `names` 切片。

这样程序就会按照预期运行：

```shell
Output
Sammy,Jessica,Drew,Jamie
```

要注意，我们仍然可以传递零个、一个或多个参数，以及我们分解的切片。以下是我们迄今为止看到的所有变体的代码：

```go
package main

import "fmt"

func main() {
	var line string

	line = join(",", []string{"Sammy", "Jessica", "Drew", "Jamie"}...)
	fmt.Println(line)

	line = join(",", "Sammy", "Jessica", "Drew", "Jamie")
	fmt.Println(line)

	line = join(",", "Sammy", "Jessica")
	fmt.Println(line)

	line = join(",", "Sammy")
	fmt.Println(line)

}

func join(del string, values ...string) string {
	var line string
	for i, v := range values {
		line = line + v
		if i != len(values)-1 {
			line = line + del
		}
	}
	return line
}
```

```shell
Output
Sammy,Jessica,Drew,Jamie
Sammy,Jessica,Drew,Jamie
Sammy,Jessica
Sammy
```

现在，我们知道了如何将零个、一个或多个参数以及我们分解的切片传递给可变参数函数。

## 结论

在本教程中，我们了解了可变参数函数如何使您的代码更简洁。虽然您并不总是需要使用它们，但您可能会发现它们很有用：

-   如果你创建了一个临时切片，只是为了传递给函数。
-   当输入参数的数量未知或调用时会发生变化。
-   使您的代码更具可读性。

要了解有关创建和调用函数的更多信息，您可以阅读[如何在 Go 中定义和调用函数]({{< relref "/docs/27-How_To_Define_and_Call_Functions_in_Go.md" >}})。
