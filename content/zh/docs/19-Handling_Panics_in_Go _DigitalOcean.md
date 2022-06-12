# 在 Go 中处理恐慌

## 介绍

程序遇到的错误分为两个广泛的类别：程序员已经预料到的错误和程序员没有预料到的错误。我们在前两篇关于 [错误处理]({{< relref "/docs/12-How_To_Convert_Data_Types_in_Go.md" >}}) 的文章中介绍过的 `error` 接口主要用于处理我们在编写 Go 程序时可能遇到的错误。`error` 接口甚至允许我们去确认在调用一个函数时发生罕见性错误的可能性，因此我们可以在这些情况下进行适当的响应。

Panics 属于第二类错误，这些错误是程序员意料之外的。这些意料之外的错误导致一个 GO 程序自发终止并退出运行。常见的错误通常是造成 panic 的原因。在本教程中，我们将研究哪些常见操作可以引起 panic ，我们还将看到避免这些 panic 的方法。我们还将使用 [`defer`]({{< relref "/docs/29-Understanding_defer_in_Go.md" >}}) 语句与 `recover` 函数一起捕获 panic，以免 panic 有机会意外终止我们正在运行的 GO 程序。

## 了解 panics

GO 中的某些操作会自动返回 panic 并停止程序的运行。常见的操作包括索引超出 [数组](https://gocn.github.io/How-To-Code-in-Go/docs/16-Understanding_Arrays_and_Slices_in_Go/#%E6%95%B0%E7%BB%84) 的容量，执行类型的断言，空指针上的调用方法，错误地使用互斥锁以及尝试使用已经关闭的 chanel 等等。这些情况中的大多数是由于编程时犯错而导致的，再加上编译器在编译程序时没有检测到这些错误。

由于 panic 包含了有助于解决问题的细节，所以开发者通常会使用 panic 来标记在开发过程中犯了一个错误。

## 由于越界引发的 panic

当你尝试访问超出切片长度或数组容量之外的索引时，GO 运行时会产生 panic。

下面的示例是尝试使用内置的 `len` 函数返回的切片的长度, 然后访问切片的最后一个元素时常见错误。尝试运行此代码以了解为什么这可能会引起 panic：

``` go
package main

import (
	"fmt"
)

func main() {
	names := []string{
		"lobster",
		"sea urchin",
		"sea cucumber",
	}
	fmt.Println("My favorite sea creature is:", names[len(names)])
}

```

这将会有有以下输出：

``` shell
# Output
panic: runtime error: index out of range [3] with length 3

goroutine 1 [running]:
main.main()
# 备注这一块信息可能会有不一样的输出
/tmp/sandbox879828148/prog.go:13 +0x20
```

panic 输出的名称提供了一个提示：`panic: runtime error: index out of range`。我们用三个海洋生物创建了一个切片。然后，我们尝试通过使用内置的 `len` 函数将切片的长度作为索引来获取切片的最后一个元素。请记住，切片和数组的第一个元素的下标都是 0; 因此，第一个元素的索引是 0，此切片中的最后一个元素在索引2。由于我们尝试在第三个索引，3 时，因此切片中没有元素要返回并且超出了切片的边界。运行时别无选择，只能终止和退出，因为我们要求它做一些不可能的事情。Go 在编译过程中也无法证明此代码将尝试执行此操作，因此编译器无法捕获到这种操作。

> 还请注意，后续代码还没被执行。这是因为 panic 是一个完全阻止执行你的 GO 程序的事件。其中产生的消息中包含多个有助于诊断 panic 的原因。

## 剖析 panic

panics 由指示 panic 的原因和一个 [堆栈跟踪](https://en.wikipedia.org/wiki/Stack_trace) 信息组成，这些可帮助你在代码中找到 panic 的位置。

任何 panic 的第一部分都是消息。它始终将以字符串 `panic:` 开始, 紧接着是引发 panic 的具体原因的字符串。在上一个练习中有一个 panic 的消息：

``` shell
panic: runtime error: index out of range [3] with length 3
```

紧接着 `panic:` 的是 `runtime error:` 这告诉我们这个 panic 是由语言的运行时引起的。这个 panic 告诉我们, 我们尝试使用下标 `[3]`已经超出了切片的长度 `3` 了。

消息后面的是堆栈跟踪。堆栈跟踪形成一个映射，我们可以根据映射信息，以准确地定位生成 panic 时正在执行的代码所在的行，和代码的调用链关系。

``` shell
goroutine 1 [running]:
main.main()
/tmp/sandbox879828148/prog.go:13 +0x20
```

上一个示例的堆栈跟踪表示，我们的程序从 `/tmp/sandbox879828148/prog.go` 文件的第 13 行中生成了 panic。这些信息还告诉我们 panic 在 `main` 包中的 `main()` 函数产生。

堆栈跟踪分为单独的块 - 对于你程序中的每个 [goroutine](https://tour.golang.org/concurrency/1)一个块。每个 GO 程序的执行都是通过一个或多个 goroutines 来完成的，它们可以独立并同时执行 GO 代码的一部分。每个块从标头 `goroutine x [state]:` (其中 x: 表示 goroutine 的 id, [state] 表示 goroutine 当前的状态)开头。标头给出了 goroutine 的 ID 号，以及发生 panic 时所处的状态。标头后，堆栈跟踪显示了发生 panic 时程序执行的函数，以及执行函数所在的文件名和行号。

上一个示例中的 panic 是通过对切片的越界访问而产生的。当使用空指针去调用方法时，也可以生成 panic。

## Nil Receivers

## nil 指针调用方法

GO 编程语言在运行时具有指向计算机内存中存在的某种类型的特定实例的指针。指针可以是 `nil` 值, 这表明他们没有指向任何东西。当我们尝试在零指针上调用方法时，GO 运行时会产生 panic。同样，当调用方法时，是接口类型的变量也会产生 panic。要查看这些情况下产生的 panic，请尝试以下示例：

``` go
package main

import (
	"fmt"
)

type Shark struct {
	Name string
}

func (s *Shark) SayHello() {
	fmt.Println("Hi! My name is", s.Name)
}

func main() {
	s := &Shark{"Sammy"}
	s = nil
	s.SayHello()
}

```

The panics produced will look like this:

由此产生的 panic 将是这样的:

``` shell
# Output
panic: runtime error: invalid memory address or nil pointer dereference
[signal SIGSEGV: segmentation violation code=0xffffffff addr=0x0 pc=0xdfeba]

goroutine 1 [running]:
main.(*Shark).SayHello(...)
/tmp/sandbox160713813/prog.go:12
main.main()
/tmp/sandbox160713813/prog.go:18 +0x1a
```

在此示例中，我们定义了一个称为 `Shark` 的结构体。`Shark` 在其指针接收器上定义了一个叫做 `Sayhello` 的方法，这个方法将在被调用时在标准输出中打印出问候信息。在我们的 `main` 函数主体中，我们创建了 `Shark` 结构体的新实例，并使用 `&` 操作符取变量的指针并将指针分配给 `S` 变量。然后，我们使用语句 `s = nil` 将 `s` 变量重新赋值为 `nil`。最后，我们尝试在变量 `s` 上调用 `SayHello` 方法。我们没有收到 `Sammy` 的友好消息，而是收到 panic，因为我们试图访问无效的内存地址。因为 `s` 变量为 `nil`，所以当调用 `SayHello` 函数时，它试图访问 `*Shark` 类型上的 `Name` 字段。因为这是一个指针接收者，并且在这种情况下的接收者是 `nil` 的，所以无法解引用零值指针而引起的 panic。

虽然我们在本例中显式地将 `s` 设置为`nil`，但实际上，这种情况却不明显。当你看到有关解引用 `nil` 指针而引发的 panic 时，请确保你已正确分配了你可能创建的任何指针变量。

> 备注, 通过使用指针作为接收者时, 使用零值取调用时没有不会发生 panic 的, 真正发生 panic 的时, 解引用 `nil` 指针。

``` go
	// 这种定义时, 使用零值的 `* Shark` 对象去调用 SayHello 方法是没有问题的
func (s *Shark) SayHello() {
	if s == nil {
		return 
	}
	fmt.Println("Hi! My name is", s.Name)
}
```

解引用 `nil` 指针和越界访问产生的 panic 是两种在运行时产生的 panic 常见的场景。也可以使用内置函数手动产生 panic。

## 使用内置的 `panic` 函数

我们还可以使用内置的 `panic` 函数来产生自己的 panic。它使用单个字符串作为参数，这是 panic 产生的信息。一般这条消息比重写 error 代码中的消息简单得多。此外，我们可以在我们自己的软件包中使用它向开发者指出，他们在使用包装代码时可能犯了一个错误。但是，最佳实践就是尝试在我们提供的软件包中将 `error` 值返回给开发者。

运行此代码以查看从 `main` 函数调用 `foo` 函数产生的 panic：

``` go
package main

func main() {
	foo()
}

func foo() {
	panic("oh no!")
}
```

产生的 panic 输出看起来像：

``` shell
# Output
panic: oh no!

goroutine 1 [running]:
main.foo(...)
/tmp/sandbox494710869/prog.go:8
main.main()
/tmp/sandbox494710869/prog.go:4 +0x40
```

在这里，我们定义了一个 `foo` 函数，里面会使用 `"oh no!"` 这个字符串调用 `panic` 这个内置函数。`foo` 函数由我们的 `main` 函数调用。请注意输出如何输出 `panic: oh no!` 和堆栈跟踪, 在堆栈跟踪中展示一个 goroutine 和两行堆栈跟踪: 一行是 `main()` 函数，另一行是 `foo()` 函数。

我们已经看到，panic 产生时似乎终止了我们的程序的运行。当需要正确关闭的开放资源时, 这可能会产生一些问题。GO 提供了一种机制，即使在 panic 的情况下，也可以始终执行一些代码。

## derfer 函数

你的程序即使在运行时处理 panic 也必须能够正确清理的资源。GO 允许使用 defer 来调用延迟执行函数，直到调用它的函数完成时才会执行。延迟函数即使在出现 panic 的情况下也会运行，并被用作一种安全机制，用来防范 panic 的混乱本质。通过调用普通一样调用函数, 使用关键字 `defer` 作为调用整个函数调用语句的前缀，比如像调用 `defer sayHello()` 一样。运行此示例以查看即使产生 panic 时也可以打印消息：

``` go
package main

import "fmt"

func main() {
	defer func() {
		fmt.Println("hello from the deferred function!")
	}()

	panic("oh no!")

}

```

此示例产生的输出看起来像：

``` shell
# Output
hello from the deferred function!
panic: oh no!

goroutine 1 [running]:
main.main()
/Users/gopherguides/learn/src/github.com/gopherguides/learn//handle-panics/src/main.go:10 +0x55
```

在此示例的 `main()` 函数中，我们首先使用 `defer` 调用到打印消息 `"hello from the deferred function!"` 的匿名函数。然后，`main` 函数立即使用 `panic` 函数产生 panic。在此程序的输出中，我们首先看到执行递延函数并打印其消息。在此之后是，我们在 `main` 中产生 panic 消息。

延迟函数提供了防范 panic 的保护。在递延函数中，GO 提供另一个内置函数来阻止 panic 终止 GO 程序的机会。

## 处理 panic

go 内置的 `recover` 函数提供了一个恢复 panic 的机制。这个函数通过拦截函数的调用栈并且阻止程序的意外退出。它具有严格的使用规则，但是在编写应用代码时非常有用。

因为 `recover` 是内置包的一部分, 所以我们可以在不导包的情况下使用这个函数:

``` go
package main

import (
	"fmt"
	"log"
)

func main() {
	divideByZero()
	fmt.Println("we survived dividing by zero!")

}

func divideByZero() {
	defer func() {
		if err := recover(); err != nil {
			log.Println("panic occurred:", err)
		}
	}()
	fmt.Println(divide(1, 0))
}

func divide(a, b int) int {
	return a / b
}

```

此示例将输出：

``` shell
# Output
2009/11/10 23:00:00 panic occurred: runtime error: integer divide by zero
we survived dividing by zero!
```

在此示例中，我们在 `main` 函数调用了我们定义的 `DivideByZero` 函数。在 `DivideByZero` 中，我们使用 `defer` 关键字调用匿名函数。这个匿名函数负责处理在 `divideByZero` 中出现的任何 panic。在匿名函数中, 我们调用内置的 `recover` 函数并且将错误信息赋值给 `err`, 如果 `DivideByZero`感处于 panic 状态，那么 `err` 将会被设置值，否则为 `nil`。通过将 `err` 与 `nil` 进行比较，我们可以检测到是否发生了 panic，在这种情况下，我们处理 `panic` 就像处理其他错误一样, 使用 `log.Println` 函数记录了 panic。

在延迟执行匿名函数之后, 我们调用了另外一个我们定义的另一个函数, 并且尝试使用 `fmt.Println` 打印这个函数的返回值。所提供的参数将导致除法执行除数为零的操作，这将引起 panic。

在此示例的输出中，我们首先从匿名函数中恢复 panic 的日志消息，接下来是 `we survived dividing by zero!` 的消息。我们真的做到了这一点，这要归功于内置的 `recover` 函数, 它成功阻止有可能终止 GO 程序运行的灾难性 panic。

从 `recover()` 函数中返回的 `err` 值正是调用 `panic` 的值。因此，在没有发生 panic 时，确保 `err` 值仅为 `nil` 至关重要。

## 使用 `recover` 检测 panic

`recover` 函数依赖于错误的值来确定是否发生了 panic。因为 `panic` 函数的参数是空接口，所以它可以是任何类型。任何接口类型 (包括空接口) 的零值为 `nil`。必须注意避免使用 `nil`作为 `panic` 的参数，如本示例所证明的：

``` go
package main

import (
	"fmt"
	"log"
)

func main() {
	divideByZero()
	fmt.Println("we survived dividing by zero!")

}

func divideByZero() {
	defer func() {
		if err := recover(); err != nil {
			log.Println("panic occurred:", err)
		}
	}()
	fmt.Println(divide(1, 0))
}

func divide(a, b int) int {
	if b == 0 {
		panic(nil)
	}
	return a / b
}

```

这将输出：

``` shell
# Output
we survived dividing by zero!
```

此示例与以前的示例相同，该示例涉及 `recover` 并进行一些小的修改。已更改了 `divide` 函数判断  `b` 是否为 `0`。如果是, 它将使用带有 `nil` 作为参数调用 `panic` 函数来产生 panic。这次的输出不包括 `defer` 调用匿名函数的日志消息，即使通过 `Divide` 创建了 panic，也会出现 panic。这种沉默行为是为什么确保调用 `panic` 的参数不是 `nil` 很重要的原因。

## Conclusion

## 总结

我们已经看到了多种可以在 GO 中造成 panic 的方法，以及如何使用恢复的内置的 `recover` 来恢复它们。虽然您不一定会自己使用 `panic`，但适当的 panic 的恢复机制是使 Go 代码达到生产级别应用程序的重要步骤。
