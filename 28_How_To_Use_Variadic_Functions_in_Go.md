# How To Use Variadic Functions in Go
# 如何在 Go 中使用可变参数函数
### Introduction
### 介绍

A _variadic function_ is a function that accepts zero, one, or more values as a single argument. While variadic functions are not the common case, they can be used to make your code cleaner and more readable.
_可变参数函数_ 是可以接受零个、一个或多个值作为单个参数的函数。虽然可变参数函数并不常见，但它们能够使您的代码更清晰、更具可读性。

Variadic functions are more common than they seem. The most common one is the `Println` function from the [`fmt`](https://golang.org/pkg/fmt) package.
可变参数函数其实很常见。最常见的是[`fmt`](https://golang.org/pkg/fmt)包中的 `Println`。

```go
func Println(a ...interface{}) (n int, err error)
```

A [function](https://www.digitalocean.com/community/tutorials/how-to-define-and-call-functions-in-go) with a parameter that is preceded with a set of ellipses (`...`) is considered a variadic function. The ellipsis means that the parameter provided can be zero, one, or more values. For the `fmt.Println` package, it is stating that the parameter `a` is variadic.
参数前面带有一组省略号 ( `...` )的[函数](https://www.digitalocean.com/community/tutorials/how-to-define-and-call-functions-in-go)被视为可变参数函数。省略号表示提供的参数可以是零个、一个或多个。对于`fmt.Println`包，它声明参数`a`是可变参数。

Let’s create a program that uses the `fmt.Println` function and pass in zero, one, or more values:
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

The first time we call `fmt.Println`, we don’t pass any arguments. The second time we call `fmt.Println` we pass in only a single argument, with the value of `one`. Then we pass `one` and `two`, and finally `one`, `two`, and `three`.
第一次调用 `fmt.Println` 时，我们不传递任何参数。第二次调用时，我们只传入一个参数，值为 `one`。 然后我们传递 `one` 和 `two`，最后是 `one`，`two` 和 `three` 三个值。

Let’s run the program with the following command:
让我们使用以下命令运行程序：

```shell
$ go run print.go
```

We’ll see the following output:
我们将看到以下输出：

```
Output
one
one two
one two three
```

The first line of the output is blank. This is because we didn’t pass any arguments the first time `fmt.Println` was called. The second time the value of `one` was printed. Then `one` and `two`, and finally `one`, `two`, and `three`.
输出的第一行为空。这是因为我们在第一次调用 `fmt.Println` 时没有传递任何参数。第二次打印了 `one` 。然后打印 `one` 和 `two`，最后打印 `one`，`two` 和 `three`。

Now that we have seen how to call a variadic function, let’s look at how we can define our own variadic function.
现在我们已经了解了如何调用可变参数函数，让我们看看如何定义自己的可变参数函数。

## Defining a Variadic Function
## 定义可变参数函数

We can define a variadic function by using an ellipsis (`...`) in front of the argument. Let’s create a program that greets people when their names are sent to the function:
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

We created a `sayHello` function that takes only a single parameter called `names`. The parameter is variadic, as we put an ellipsis (`...`) before the data type: `...string`. This tells Go that the function can accept zero, one, or many arguments.
我们创建了一个 `sayHello` 函数，它只接受一个名为`names`，该参数是可变参数，因为我们在数据类型之前放置了一个省略号 (`...`)： `...string`。这告诉 Go 这个函数可以接受零个、一个或多个参数。

The `sayHello` function receives the `names` parameter as a [`slice`](https://www.digitalocean.com/community/tutorials/understanding-arrays-and-slices-in-go#slices). Since the data type is a [`string`](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#strings), the `names` parameter can be treated just like a slice of strings (`[]string`) inside the function body. We can create a loop with the [`range`](https://www.digitalocean.com/community/tutorials/how-to-construct-for-loops-in-go#looping-through-sequential-data-types-with-rangeclause) operator and iterate through the slice of strings.
`sayHello` 函数将 `names` 参数作为 [`slice`](https://www.digitalocean.com/community/tutorials/understanding-arrays-and-slices-in-go#slices) 接收。由于数据类型是 [`string`](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#strings)，因此 `names` 可以将参数视为字符串切片 ( `[]string` ) 。我们可以使用[`range`](https://www.digitalocean.com/community/tutorials/how-to-construct-for-loops-in-go#looping-through-sequential-data-types-with-rangeclause)运算符创建一个循环并遍历字符串切片。

If we run the program, we will get the following output:
如果我们运行程序，我们将得到以下输出：

```shell
Output
Hello Sammy
Hello Sammy
Hello Jessica
Hello Drew
Hello Jamie
```

Notice that nothing printed for the first time we called `sayHello`. This is because the variadic parameter was an empty `slice` of `string`. Since we are looping through the slice, there is nothing to iterate through, and `fmt.Printf` is never called.
请注意，我们第一次调用 `sayHello` 什么也没打印。这是因为可变参数是一个空的字符串切片。 由于我们会遍历切片，因此没有任何内容可以迭代，并且 `fmt.Printf` 永远不会被调用。

Let’s modify the program to detect that no values were sent in:
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

Now, by using an [`if` statement](https://www.digitalocean.com/community/tutorials/how-to-write-conditional-statements-in-go#if-statements), if no values are passed, the length of `names` will be `0`, and we will print out `nobody to greet`:
现在，通过使用[`if`语句](https://www.digitalocean.com/community/tutorials/how-to-write-conditional-statements-in-go#if-statements)，如果没有传递任何值，则 `names` 长度将为 `0`，我们将打印出`nobody to greet`：

```shell
Output
nobody to greet
Hello Sammy
Hello Sammy
Hello Jessica
Hello Drew
Hello Jamie
```

Using a variadic parameter can make your code more readable. Let’s create a function that joins words together with a specified delimiter. We’ll create this program without a variadic function first to show how it would read:
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

In this program, we are passing a comma (`,`) as the delimiter to the `join` function. Then we are passing a slice of values to join. Here is the output:
在这个程序中，我们将逗号 (`,`) 作为分隔符传递给 `join` 函数，再传递一个字符串切片。输出如下：

```shell
Output
Sammy,Jessica,Drew,Jamie
Sammy,Jessica
Sammy
```

Because the function takes a slice of string as the `values` parameter, we had to wrap all of our words in a slice when we called the `join` function. This can make the code difficult to read.
因为该函数将字符串切片作为 `values` 参数，所以当我们调用 `join` 函数时，我们必须将所有单词包装在一个切片中。这会使代码难以阅读。

Now, let’s write the same function, but we’ll use a variadic function:
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

If we run the program, we can see that we get the same output as the previous program:
如果我们运行该程序，可以看到我们得到了与之前的程序相同的输出：

```shell
Output
Sammy,Jessica,Drew,Jamie
Sammy,Jessica
Sammy
```

While both versions of the `join` function do the exact same thing programmatically, the variadic version of the function is much easier to read when it is being called.
虽然这两个版本的 `join` 函数执行完全相同的操作，但可变参数函数的版本在调用时更容易阅读。

## Variadic Argument Order
## 可变参数顺序

You can only have one variadic parameter in a function, and it must be the last parameter defined in the function. Defining parameters in a variadic function in any order other than the last parameter will result in a compilation error:
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

This time we put the `values` parameter first in the `join` function. This will cause the following compilation error:
这次我们把 `values` 参数放在 `join` 函数的首位时，将导致以下编译错误：

```shell
Output
./join_error.go:18:11: syntax error: cannot use ... with non-final parameter values
```

When defining any variadic function, only the last parameter can be variadic.
定义任何可变参数函数时，只有最后一个参数可以是可变参数。

## Exploding Arguments
## 分解参数

So far, we have seen that we can pass zero, one, or more values to a variadic function. However, there will be occasions when we have a slice of values and we want to send them to a variadic function.
到目前为止，我们已经看到我们可以将零个、一个或多个值传递给可变参数函数。但是，有时我们有一个切片并且我们希望将它们发送到可变参数函数。

Let’s look at our `join` function from the last section to see what happens:
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

If we run this program, we will receive a compilation error:
如果我们运行这个程序，我们会收到一个编译错误：

```shell
Output
./join-error.go:10:14: cannot use names (type []string) as type string in argument to join
```

Even though the variadic function will convert the parameter of `values ...string` to a slice of strings `[]string`, we can’t pass a slice of strings as the argument. This is because the compiler expects discrete arguments of strings.
即使可变参数函数将 `values ...string` 参数转换为字符串切片 `[]string`，我们也不能将字符串切片作为参数传递。这是因为编译器需要的是字符串的离散参数。

To work around this, we can _explode_ a slice by suffixing it with a set of ellipses (`...`) and turning it into discrete arguments that will be passed to a variadic function:
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

This time, when we called the `join` function, we exploded the `names` slice by appending ellipses (`...`).
这一次，当我们调用 `join` 函数时，我们通过附加省略号 (`...`)来分解 `names` 切片。

This allows the program to now run as expected:
这样程序就会按照预期运行：

```shell
Output
Sammy,Jessica,Drew,Jamie
```

It’s important to note that we can still pass a zero, one, or more arguments, as well as a slice that we explode. Here is the code passing all the variations that we have seen so far:
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

We now know how to pass zero, one, or many arguments, as well as a slice that we explode, to a variadic function.
现在，我们知道了如何将零个、一个或多个参数以及我们分解的切片传递给可变参数函数。

## Conclusion
## 结论

In this tutorial, we have seen how variadic functions can make your code cleaner. While you won’t always need to use them, you may find them useful:
在本教程中，我们了解了可变参数函数如何使您的代码更简洁。虽然您并不总是需要使用它们，但您可能会发现它们很有用：

-   If you find that you’re creating a temporary slice just to pass to a function.
-   如果你创建一个临时切片，只是为了传递给函数。
-   When the number of input params are unknown or will vary when called.
-   当输入参数的数量未知或调用时会发生变化。
-   To make your code more readable.
-   使您的代码更具可读性。

To learn more about creating and calling functions, you can read [How to Define and Call Functions in Go](https://www.digitalocean.com/community/tutorials/how-to-define-and-call-functions-in-go).
要了解有关创建和调用函数的更多信息，您可以阅读[如何在 Go 中定义和调用函数](https://github.com/gocn/How-To-Code-in-Go/blob/main/27_How_To_Define_and_Call_Functions_in_Go.md)。
