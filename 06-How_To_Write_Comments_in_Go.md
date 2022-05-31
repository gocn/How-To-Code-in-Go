# 如何在 Go 中写注释

### 介绍 

注释是存在于计算机程序中，被编译器和解释器忽略的代码行。在程序中包含注释使得代码对于人类来说更具可读性，因为它提供了一些关于程序的每个部分正在做什么的信息或解释。

根据你程序的目的，注释可以作为你自己的笔记或者提醒，或者它们可以是为了其他程序员能够理解你的代码在做什么而写的。

一般来说，在写或者更新程序的时候写评论是个不错的主意，因为以后很容易忘记你的思维过程，而且以后写的评论长期来看可能没那么有用。

### 注释语法

Go 中的注释以一组向前斜杠（`//`）开始，一直到行尾。在前向斜杠集合之后有一个空白符号是惯用的方式。

一般来说，评论看起来是这样的:

```
// This is a comment
```

注释不会执行，因此在运行程序时没有注释的指示。注释在源代码中供人阅读，而不是供计算机执行。

在一个 “Hello, World!” 的程序中，注释可能如下所示：

```go
package main

import (
	"fmt"
)

func main() {
	// Print “Hello, World!” to console
	fmt.Println("Hello, World!")
}
```

在一个迭代切片的 `for` 循环时，注释可能如下所示：

```go
package main

import (
	"fmt"
)

func main() {
	// Define sharks variable as a slice of strings
	sharks := []string{"hammerhead", "great white", "dogfish", "frilled", "bullhead", "requiem"}

	// For loop that iterates over sharks list and prints each string item
	for _, shark := range sharks {
		fmt.Println(shark)
	}
}
```

注释应该与注释代码的缩进相同。也就是说，一个没有缩进的函数定义将有一个没有缩进的注释，并且下面的每个缩进级别中，注释与所注释的代码对齐。

比如，下面是 `main` 函数如何注释，以及各个缩进级别的代码和注释：

```go
 package main

import "fmt"

const favColor string = "blue"

func main() {
	var guess string
	// Create an input loop
	for {
		// Ask the user to guess my favorite color
		fmt.Println("Guess my favorite color:")
		// Try to read a line of input from the user. Print out the error 0
		if _, err := fmt.Scanln(&guess); err != nil {
			fmt.Printf("%s\n", err)
			return
		}
		// Did they guess the correct color?
		if favColor == guess {
			// They guessed it!
			fmt.Printf("%q is my favorite color!\n", favColor)
			return
		}
		// Wrong! Have them guess again.
		fmt.Printf("Sorry, %q is not my favorite color. Guess again.\n", guess)
	}
}
```

编写注释是为了帮助程序员，无论是最初的程序员还是在项目中使用或合作的其他人。如果注释不能与代码库一起适当地维护和更新，那么对于编写与代码相矛盾或将与将来的代码相矛盾的注释，不如不包含注释。

在给代码注释时，你应该回答代码背后的 _原因_，而不是 _什么_ 或 _怎么_ 回事。除非代码特别复杂，否则查看代码通常可以回答是 _什么_ 或者 _怎么_ 的问题，这就是为什么注释通常是围绕 _为什么_ 的原因。

### 注释块

注释块可以用来解释更复杂的代码或那些读者可能并不熟悉的代码。

你可以用两种方式在 Go 中创建注释块。第一种方法是使用一组双向前斜杠，并在每行中重复它们。

```go
// First line of a block comment
// Second line of a block comment
```

第二种是使用开始标记（`/*`）和结束标记（`*/`）。为了记录代码，惯例方式是使用 `//` 语法。你只能使用 `/* ... */` 语法进行调试，我们将在本文后面讨论这个问题。

```go
/*
Everything here
will be considered
a block comment
*/
```

在这个例子中，注释块定义了 `MustGet()` 函数中发生的事情：

```go
// MustGet will retrieve a url and return the body of the page.
// If Get encounters any errors, it will panic.
func MustGet(url string) string {
	resp, err := http.Get(url)
	if err != nil {
		panic(err)
	}

	// don't forget to close the body
	defer resp.Body.Close()
	var body []byte
	if body, err = ioutil.ReadAll(resp.Body); err != nil {
		panic(err)
	}
	return string(body)
}
```

在 Go 中导出函数的开头经常会看到注释块; 这些注释也是生成代码文档的元素。当操作不那么直观，而需要完全解释时，也会使用注释块。除了记录函数之外，你应该尽量避免对代码进行过度注释，并相信其他程序员能够理解 Go，除非你是为特定的用户编写代码。

### 内联注释

内联注释出现在语句的同一行，位于代码本身之后。像其他注释一样，它们以一组向前斜杠开始。同样，不需要在前向斜杠后面加空格，但是惯例是这样做的。

一般来说，内联注释是这样的:

```go
[code]  // Inline comment about the code
```

内联注释应该谨慎使用，但是对于解释代码中棘手或不明显的部分来说可能是有效的。如果你认为自己将来可能不记得正在编写的代码中的某一行，或者你正在与一个你认识的人合作，而这个人可能并不熟悉代码的所有方面，那么它们也会很有用。

例如，如果你在你的 Go 程序中没有使用大量的数学，你或者你的合作者可能不知道下面的代码创建了一个复杂的数字，所以你可能需要包含一个关于这个的内联注释:

```go
z := x % 2  // Get the modulus of x
```

你也可以使用行内注释来解释做某事背后的原因，或者提供一些额外的信息，比如:

```go
x := 8  // Initialize x with an arbitrary number
```

你应该只在必要的时候，并且当它们可以为阅读程序的人提供有用的指导时，使用内联注释。

### 注释测试代码

除了使用注释作为记录代码的方式之外，还可以使用开始标记（`/*`）和结束标记（`*/`）来创建块注释。这允许你在测试或调试当前正在创建的程序时注释掉不想执行的代码。也就是说，当你在实现新代码后遇到错误时，你可能希望对其中的一些代码行进行注释，以查看是否能够解决这个确切的问题。

Using the `/*` and `*/` tags can also allow you to try alternatives while you’re determining how to set up your code. You can also use block comments to comment out code that is failing while you continue to work on other parts of your code.
在决定如何设置代码时，使用 `/*` 和 `*/` 标记还可以让你尝试其他选择。你也可以使用注释块来注释失败的代码，同时继续处理代码的其他部分。

```go
// Function to add two numbers
func addTwoNumbers(x, y int) int {
	sum := x + y
	return sum
}

// Function to multiply two numbers
func multiplyTwoNumbers(x, y int) int {
	product := x * y
	return product
}

func main() {
	/*
		In this example, we're commenting out the addTwoNumbers
		function because it is failing, therefore preventing it from executing.
		Only the multiplyTwoNumbers function will run

		a := addTwoNumbers(3, 5)
		fmt.Println(a)

	*/

	m := multiplyTwoNumbers(5, 9)
	fmt.Println(m)
}
```

**注意**: 注释掉代码只能用于测试目的。不要在最终的程序中保留注释掉的代码片段。

使用 `/*` 和 `*/` 标记注释代码可以让你尝试不同的编程方法，并通过系统地注释和运行程序的各个部分来帮助你找到错误的源代码。

### 结论

在你的 Go 程序中使用注释有助于使你的程序对于人类来说更具可读性，包括未来的你自己。添加适当相关和有用的注释可以使其他人更容易地与你合作编程项目，并使代码的价值更加明显。

在 Go 中正确注释代码还可以让你用到 [Godoc](https://godoc.org/golang.org/x/tools/cmd/godoc) 工具。Godoc 是一个从代码中提取注释并为 Go 程序生成文档的工具。
