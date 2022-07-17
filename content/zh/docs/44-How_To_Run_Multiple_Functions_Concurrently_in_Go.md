# 如何在 Go 中并发运行多个函数

## 介绍

Go 受欢迎的特性之一是它对 [_并发_](https://en.wikipedia.org/wiki/Concurrency_(computer_science)) 的一流且原生的支持，或者说能立即让程序同时做多件事情。随着计算机从快速地运行单道程序，逐渐转变到现在的倾向于同时运行多个程序，能够并发地运行代码在现代编程中的重要程度越来越高。为了更快地运行程序，程序员通常需要将程序设计成并发运行的，因而程序的每个可并发的部分都能独立地运行。同时使用 Go 的两个特性 [_协程(goroutines)_](https://golangdocs.com/goroutines-in-golang) 和 [_通道(channels)_](https://golangdocs.com/channels-in-golang), 将会让并发变得更容易。协程解决了创建与运行并发代码的难度，而通道解决了并发运行的协程之间的安全通信问题。

在这个教程中，你将探索协程与通道的知识。首先，你将创建一个使用协程来同时运行多个函数的程序。然后，你将在程序中加入通道，以实现运行着的协程之间的通信。最后你将在程序中加入更多的协程，以模拟一个运行着多个工作协程的程序。

> 注： 下文的 goroutines 指 Go 的协程这一特性，goroutine 指创建的某个协程（并发运行的函数）本身。（若都翻译成协程，将会混淆二者，为方便读者理解，故不译）。

## 前置条件

为了更顺畅地阅读本教程，你需要：

* Go 版本 >= 1.16。你可以按照 [如何安装 Go 和设置本地编程环境]({{< relref "/docs/01-How_To_Install_Go_and_Set_Up_a_Local Programming_Environment_on_Ubuntu_18.04_DigitalOcean.md" >}}) 来安装 Go。
* 熟悉 Go 的 [函数]({{< relref "/docs/27-How_To_Define_and_Call_Functions_in_Go.md" >}}) 相关知识。

## 使用协程同时运行函数

在现代计算机中，处理器或 [CPU](https://en.wikipedia.org/wiki/Central_processing_unit) 被设计成在同一时间运行尽可能多的代码流。这些处理器拥有一个或更多的“核”，每个核都能同时运行一个代码流。因此，一个程序可以同时使用的内核越多，程序的运行速度就越快。然而，为了使程序能够利用 [多核](https://en.wikipedia.org/wiki/Multi-core_processor) 提供的速度提升，程序需要能够被分割成多个代码流。将一个程序分割成若干部分可能是编程中具有挑战性的事情之一，但 Go 的设计使之更容易。

Go 实现这一功能的其中一个方式是使用 *goroutines*。goroutine 是一种特殊类型的函数，它可以在其他 goroutine 运行时同时运行。当一个程序被设计成同时运行多个代码流时，这个程序即被设计成  [_并发_](https://en.wikipedia.org/wiki/Concurrency_(computer_science)) 运行。通常，当一个函数被调用时，调用方接下来的代码，只有当被调用的函数执行完毕后才会开始执行。这被称为“前台”运行，因为它可以当前代码运行结束前执行其他代码。对于一个协程，调用它的函数将继续执行接下来的代码，而 goroutine 本身则会在“后台”运行。当代码在完成之前不妨碍其他代码的运行时，它就被认为是在后台运行。

goroutines 提供的能力是，每个 goroutine 可以同时在一个处理器核心上运行。如果你的计算机有四个处理器核心，且你的程序有四个 goroutine，那么所有四个 goroutine 都可以同时运行。当多个代码流像这样在不同的内核上同时运行时，就叫做以 [*并行*](https://en.wikipedia.org/wiki/Parallel_computing) 方式运行。

为了直观地了解并发和并行之间的区别，请看下面的图表。当一个处理器运行一个函数时，它并不总是一次性地从开始运行到完成。有时，当一个函数在等待其他事情发生时，如读取文件，操作系统会在 CPU 核心上交错地运行其他函数、goroutine 或其他程序。该图展示了为并发而设计的程序如何在单核以及多核上运行。它还展示了一个 goroutine 在并行运行时比在单核上运行时可以在相同的时间区间内容纳更多的代码段（如图所示，9个垂直的代码段）。

> 译者注：左侧为并发，右侧为并行。下图由上至下为时间轴。如左图，并发即为交错地快速地在多个 goroutine 间切换，因为切换足够快，在用户看来像同时运行了紫红绿三种 goroutine，但其实同一时间只有一个 goroutine 在运行；右图，并行为同一时间真的在运行多个 goroutine，横向地观察右图，可以看到每个时间窗口（每一行）都有两个 goroutine 同时在运行。

![Diagram split into two columns, labeled Concurrency and Parallelism. The Concurrency column has a single tall rectangle, labeled CPU core, divided into stacked sections of varying colors signifying different functions. The Parallelism column has two similar tall rectangles, both labeled CPU core, with each stacked section signifying different functions, except it only shows goroutine1 running on the left core and goroutine2 running on the right core.](https://assets.digitalocean.com/articles/68067/diagram2.png)

图中左栏标有 "并发"，显示了围绕并发设计的程序如何在单个 CPU 核上运行，先运行部分 "goroutine1"，然后是另一个函数、goroutine 或程序，然后是 "goroutine2"，再是 "goroutine1"，如此循环。对用户来说，这似乎是程序在同时运行所有的函数或 goroutine，尽管它们实际上是在一个接一个的小部分中运行。

图中右边一栏标有 "并行"，显示了同一个程序如何在一个有两个CPU核的处理器上并行运行。第一个CPU核显示 `goroutine1` 与其他函数、goroutine 或程序穿插运行，而第二个 CPU 核显示 `goroutine2` 与该核的其他函数或 goroutine 运行。有时 `goroutine1` 和 `goroutine2` 同时运行，只是在不同的 CPU 核上。

这张图还展示了 Go 的另一个强大特性，[_高可拓展性_](https://en.wikipedia.org/wiki/Scalability)。当一个程序可以在从有几个处理器核心的小型计算机，切换到在有几十个核心的大型服务器上运行，并利用这些额外的资源时，它就是可扩展的。图中显示，通过使用 goroutines，你的并发程序能够在单个 CPU 核上运行，但随着更多 CPU 核的加入，更多的 goroutine 可以并行运行，以加快程序运行速度。

为了开始写你的新的并发程序，请选择一个位置，创建一个 `multifunc` 目录。你可能已经有了一个项目目录，但在本教程中，你将创建一个名为 `projects` 的目录。你可以通过 IDE 或命令行来创建此目录。

如果你正在使用命令行工具，可以创建 `projects` 目录并切换至此目录，从此开始你的并发编程之旅：

```shell
mkdir projects
cd projects
```

在 `projects` 目录下, 使用 `mkdir` 命令来创建项目文件夹 `multifunc` 并切换至此目录：

```shell
mkdir multifunc
cd multifunc
```

当你已经进入 `multifunc` 目录时, 使用 `nano` 或者你喜欢的编辑器打开（创建） `main.go` ：

```shell
nano main.go
```

粘贴或手动输入以下代码至 `main.go` ：

> projects/multifunc/main.go

```go
package main

import (
	"fmt"
)

func generateNumbers(total int) {
	for idx := 1; idx <= total; idx++ {
		fmt.Printf("Generating number %d\n", idx)
	}
}

func printNumbers() {
	for idx := 1; idx <= 3; idx++ {
		fmt.Printf("Printing number %d\n", idx)
	}
}

func main() {
	printNumbers()
	generateNumbers(3)
}
```

这个初始的程序定义了两个函数，`generateNumbers` 和 `printNumbers`，然后在 `main` 函数中运行这两个函数。`generateNumbers` 函数把要“生成”的数字的个数作为一个参数，然后把每个数字打印到屏幕上（本例遍历了1-3）。`printNumbers` 函数没有任何参数，但它也会打印出1到3的数字。

一旦你保存了 `main.go` 文件，就可以用 `go run` 来运行它：

```shell
go run main.go
```

输出看起来像这样：

```
Output
Printing number 1
Printing number 2
Printing number 3
Generating number 1
Generating number 2
Generating number 3
```

你会看到函数一个接一个地运行，先运行 `printNumbers` ，再运行 `generateNumbers`。

现在，想象一下，`printNumbers` 和 `generateNumbers` 分别需要3秒钟的时间来运行。_同步_ 运行时，或者像上面的例子那样一个接一个地运行，你的程序将运行6秒。首先，`printNumbers` 运行3秒，然后 `generateNumbers` 运行3秒。然而，在你的程序中，这两个函数是相互独立的，因为它们不依赖另一个函数的数据来运行。你可以利用它们的互不干扰的特性，使用 goroutines 并发运行这些函数，以加速这个假想的程序。理论上，当两个函数并发运行时，运行时间将减半。如果 `printNumbers` 和 `generateNumbers` 这两个函数都需要3秒的时间，且同时启动，那么程序可以在3秒内运行完毕。(实际速度可能会因为外部因素而变化，如计算机有多少个内核或有多少其他程序同时在计算机上运行)。

> 译者注：_同步_ 为计算机术语，与 _异步_ 相对，指为了保持多程序间的数据一致性而采取的机制。这里的语境，可以简单理解为：前一个函数执行完毕后才能执行下一个函数，即顺序地无交叠地执行。

作为 goroutine 并发地运行一个函数与同步地运行一个函数相似。要想让函数以 goroutine 方式运行（相对于标准的同步函数），你只需要在函数调用前添加 `go` 关键字。

然而，为了使程序并发地运行多个 goroutine, 你需要做一些修改。你需要为你的程序添加一种方法，让它等待两个 goroutine 都运行完毕。如果你不等待所有 goroutine 运行完毕，那么当你的 `main` 函数运行完毕的时候，这两个 goroutine 可能永远都不会运行，或者只运行了一部分而没有运行完毕。

为了等待函数运行完毕，你可以使用 Go 的 [`sync`](https://pkg.go.dev/sync) 包中的 [`WaitGroup`](https://pkg.go.dev/sync#WaitGroup)。`sync` 包包含了 "同步原语"，比如 `WaitGroup`，它们被设计用来同步程序的各个部分。在本例中，同步可以跟踪两个函数何时完成运行，以便你可以退出程序。

`WaitGroup` 原语的工作方式是使用 `Add`、`Done` 和 `Wait` 函数计算它需要等待的"事情"的数量。`Add` 函数把传入的增量累加到该计数值，`Done` 将计数减一。`Wait` 函数可以用来等待（阻塞）后续程序，直到计数变为零，这意味着 `Done` 被调用的次数足以抵消对 `Add` 的调用。一旦计数达到零，`Wait` 函数将 return，程序将继续运行。

接下来，更新 `main.go` 文件中的代码，使用 `go` 关键字将两个函数作为 goroutine 运行，并在程序中添加一个 `sync.WaitGroup`。

> projects/multifunc/main.go

```go
package main

import (
	"fmt"
	"sync"
)

func generateNumbers(total int, wg *sync.WaitGroup) {
	defer wg.Done()

	for idx := 1; idx <= total; idx++ {
		fmt.Printf("Generating number %d\n", idx)
	}
}

func printNumbers(wg *sync.WaitGroup) {
	defer wg.Done()

	for idx := 1; idx <= 3; idx++ {
		fmt.Printf("Printing number %d\n", idx)
	}
}

func main() {
	var wg sync.WaitGroup

	wg.Add(2)
	go printNumbers(&wg)
	go generateNumbers(3, &wg)

	fmt.Println("Waiting for goroutines to finish...")
	wg.Wait()
	fmt.Println("Done!")
}
```

在声明了 `WaitGroup` 之后，它需要知道要等待多少"事情"。在启动 goroutine 之前，在 `main` 函数中加入`wg.Add(2)` ，告诉 `wg` 需要等待两个 `Done` 调用才能继续执行 `wg.Wait` 的后续代码。如果未在 goroutine 启动前执行 `wg.Add`，程序执行顺序会不符合预期，甚至代码可能会 panic，因为 `wg` 不知道它应该等待多少 `Done` 调用。

然后，每个函数会通过 `defer` 来调用 `Done`，在函数运行结束后将计数值减一。`main` 函数也被更新了，包括在 `WaitGroup` 类型变量上的对 `Wait` 方法的调用，所以 `main` 函数将等待（被阻塞），直到两个函数都调用 `Done` 后，再继续运行，并退出程序。

在保存了 `main.go` 文件后，像之前那样用 `go run` 来运行程序：

```shell
go run main.go
```

输出看起来像这样：

```
Output
Printing number 1
Waiting for goroutines to finish...
Generating number 1
Generating number 2
Generating number 3
Printing number 2
Printing number 3
Done!
```

你的输出可能和教程中的输出不同，甚至，当你每次运行程序的时候，输出很有可能都不一样。因为两个函数在被并发地执行，实际输出完全取决于 Go 和操作系统给了每个函数多少时间来运行。有的时候，这个时间足够每个函数运行完毕，所以你会看到两个函数完整打印了整个数字序列，且不被另外一个函数所打断。有时，你可能会看到类似上面的相互交织的输出。

你可以尝试做一个实验，删除 `main` 函数中的 `wg.Wait()` 调用，然后用 `go run` 再运行几遍程序。取决于你的电脑，你可能会看到 `generateNumbers` 和 `printNumbers` 函数输出了一些东西，但也可能根本看不到任何输出。当你删除对 `Wait` 的调用时，程序将不再等待两个函数运行完毕后再继续运行。由于 `main` 函数将在 `Wait` 函数后很快运行完毕，你的程序很有可能在所有的 goroutine 完成运行之前就执行到 `main` 函数的末尾并退出。当这种情况发生时，你会看到一些数字被打印出来，但看不到每个函数打印完毕全部的三个数字。

在本节中，你创建了一个程序，使用 `go` 关键字来同时运行两个 goroutine 并打印一串数字。你还使用了一个 `sync.WaitGroup` 来使程序在退出前等待这些 goroutine 完成。

你可能已经注意到，`generateNumbers` 和 `printNumbers` 函数没有返回值。在 Go 中，goroutine 并不能像标准函数那样返回某些值。你仍然可以使用 `go` 关键字来调用一个具有返回值的函数，但是这些返回值会被丢弃，你无法获取到它们。那么，当你在一个 goroutine 中需要使用另一个 goroutine 的数据时，如果不能返回值，该怎么办呢？解决办法是使用 Go 的另一个特性，即 "通道"，它允许你从一个 goroutine 向另一个 goroutine 发送数据。

## 使用通道在协程间安全地通信

并发编程的难题之一，是在同时运行的程序的不同部分之间安全地通信。如果你不小心，可能会遇到只有在并发程序中才会出现的问题。例如，当程序的两个部分并发运行时，一个部分试图更新一个变量（写），而另一个部分同时试图读取它（读），就会发生 [_数据竞争_](https://en.wikipedia.org/wiki/Race_condition)。当这种情况发生时，读或写操作可能会发生错乱，导致程序的一个或两个部分使用了错误的值。"数据竞争"这一名称来自于程序的两部分互相"竞争"地访问数据。

虽然在 Go 中仍有可能遇到像数据竞争这样的并发问题，但该语言的底层设计使其更容易避免这些问题。除了 goroutines 之外，通道（channel）是另一个使并发更安全和更容易使用的特性。通道可以被看作是两个或多个不同的 goroutine 之间的管道，数据可以通过它来发送。一个 goroutine 将数据放入管道的一端，另一个 goroutine 将同样的数据取出。确保数据安全地从一端到达另一端是困难的，但这部分 Go 已经替你解决了。

在 Go 中创建一个通道类似于创建一个 [切片]({{< relref "/docs/16-Understanding_Arrays_and_Slices_in_Go.md" >}})，需要使用内置的 `make()` 函数。通道的类型声明由两部分组成： `chan` 关键字在前，后面是你想在通道上发送的 [数据类型]({{< relref "/docs/07-Understanding_Data_Types_in_Go.md" >}})。例如，要创建一个用于发送 `int`类型值的通道，你可以使用 `chan int` 类型。如果你想要一个发送 `[]byte` 类型数据的通道，就用 `chan []byte`，像这样：

```go
bytesChan := make(chan []byte)
```

一旦一个通道被创建，你可以通过使用箭头状的 `<-` 操作符在通道上发送或接收数据。`<-` 操作符相对于通道变量的位置决定了你是在读取还是写入通道。

要向通道中写入数据，通道变量在前，`<-` 操作符在中间，最后是要写入通道的值：

```go
intChan := make(chan int)
intChan <- 10
```

要从通道中读取数据，存放读取内容的变量在前，接着是赋值操作符（`=` 或 `:=`）和  `<-`，最后是被读取的通道：

```go
intChan := make(chan int)
intVar := <- intChan
```

为了更直观、容易地弄清这两种操作，可以记住 `<-` 箭头总是向左（相对于`->`），并指向了数据的去向。向通道写入数据时，箭头指向通道；从通道中读取数据时，箭头指向保存数据的变量。

像切片那样，也可以在 [`for`循环]({{< relref "/docs/25-How_To_Construct_For_Loops_in_Go.md" >}}) 中使用 `range` 关键字来读取通道。当使用 `range` 关键字读取一个通道时，循环的每次迭代都从通道中读取下一个值，并将其赋值给循环变量。然后，它将继续从通道中读取数据，直到通道关闭或以其他方式退出 `for` 循环，如`break`：

```go
intChan := make(chan int)
for num := range intChan {
	// Use the value of num received from the channel
	if num < 1 {
		break
	}
}
```

在某些情况下，你可能只想让一个函数从通道中只读或只写数据，而不是同时读取和写入。要做到这一点，你可以在 `chan` 类型声明中添加 `<-` 操作符。与从通道中读写类似，通道类型使用 `<-` 箭头，将通道变量限制在只读、只写或同时读写。例如，要定义一个只读的 `int` 值的通道，类型声明是 `<-chan int`。

```go
func readChannel(ch <-chan int) {
	// ch is read-only
}
```

如果你想要只写通道，使用 `chan<- int` 声明：

```go
func writeChannel(ch chan<- int) {
	// ch is write-only
}
```

注意，当箭头指向通道外时是只读，指向通道时为只写。如果类型声明中没有使用箭头，例如 `chan int`，那么这个通道可读写。

最后，一旦某个通道不再被使用，可以使用内置的 `close()` 函数来关闭它。这一步非常重要，因为当通道被创建后却未被使用，多次后，会导致 [_内存泄漏_](https://en.wikipedia.org/wiki/Memory_leak)。内存泄漏是指程序创建的东西占用了计算机的内存，但在使用完后却没有将内存释放回计算机。这导致程序随着时间的推移慢慢地（有时不那么慢）占用更多的内存，就像漏水一样。当用 `make()` 创建一个通道时，计算机的一些内存分配给该通道，然后当 `close()` 在该通道上被调用时，这些内存会被还给计算机，可再被用于其他用途。

现在，更新你程序中的 `main.go` 文件，使用 `chan int` 类型的通道在你的 goroutine 之间进行通信。`generateNumbers` 函数将生成数字并写入通道，而 `printNumbers` 函数将从通道中读取这些数字并打印到屏幕上。在 `main` 函数中，你将创建一个新的通道，并将其作为参数传递给另外两个函数。接着，调用 `close()` 来关闭通道，因为后面不再使用这个通道了。`generateNumbers` 函数也不应该再是一个 goroutine，因为一旦该函数运行完毕，程序将已经生成完了所有需要的数字。这样，`close()` 函数只会在两个函数都运行完成之前在通道上被调用。

> projects/multifunc/main.go

```go
package main

import (
	"fmt"
	"sync"
)

func generateNumbers(total int, ch chan<- int, wg *sync.WaitGroup) {
	defer wg.Done()

	for idx := 1; idx <= total; idx++ {
		fmt.Printf("sending %d to channel\n", idx)
		ch <- idx
	}
}

func printNumbers(ch <-chan int, wg *sync.WaitGroup) {
	defer wg.Done()

	for num := range ch {
		fmt.Printf("read %d from channel\n", num)
	}
}

func main() {
	var wg sync.WaitGroup
	numberChan := make(chan int)

	wg.Add(2)
	go printNumbers(numberChan, &wg)

	generateNumbers(3, numberChan, &wg)

	close(numberChan)

	fmt.Println("Waiting for goroutines to finish...")
	wg.Wait()
	fmt.Println("Done!")
}
```

在 `generateNumbers` 和 `printNumbers` 的形参中，你会看到 `chan` 类型使用的是只读和只写的类型。因为 `generateNumbers` 只需要能够将数字写入通道，所以它是只写类型，`<-` 箭头指向通道。`printNumbers` 只需要能够从通道中读取数字，所以它是只读类型，`<-` 箭头指向远离通道的方向。

尽管这两个地方的类型可以是 `chan int`，即允许读和写，但将它们限制在函数需要的范围内是有用的，以避免意外地导致你的程序因所谓的 [_死锁_](https://en.wikipedia.org/wiki/Deadlock) 而停止运行。当程序的 A 部分在等待 B 部分做某事，但 B 部分也在等待程序的 A 部分完成时，就会发生死锁。由于程序的两部分都在互相等待，程序将永远不会继续运行，几乎就像两个齿轮卡住一样。

死锁的发生由 Go 中通道通信的工作方式导致。当一个程序的一部分正在向一个通道写入数据时，它将等待（被阻塞），直到程序的另一部分从该通道中读出该数据，然后继续运行。同样地，如果一个程序正在从一个通道中读出数据，它将等待程序另一部分将数据写到该通道后再继续。程序中等待其他事情发生的部分被称为[_阻塞_](https://en.wikipedia.org/wiki/Blocking_(computing))，因为它在其他事情发生之前被阻止继续运行。当通道被写入或读出时，它们就会被阻塞。因此，如果你有一个函数，期望写入一个通道，但不小心从该通道读出，你的程序可能会进入死锁，因为该通道将永远不会被写入。使用 `chan<- int` 或 `<-chan int` 而不是仅仅使用 `chan int` 的原因之一，是为了确保这种情况不会发生。

更新后的代码的另一个重要的地方是，一旦使用 `generateNumbers` 向通道写入数据完毕后，使用 `close()` 来关闭通道。

* 在这个程序中，`close()` 使得 `printNumbers` 中的 `for ... range` 循环终止。
* 程序使用 `range` 从一个通道读取数据，直到它所读取的通道被关闭。所以，如果 `close` 没有在 `numberChan` 上被调用，那么 `printNumbers` 将永远不会结束。
* 如果 `printNumbers` 无法执行完毕，`WaitGroup` 的 `Done` 方法就不会在 `printNumbers` 退出时被 `defer` 调用。
* 如果 `printNumbers` 从未调用 `Done` 方法，程序本身将永远不会退出，因为 `main` 函数中 `WaitGroup` 的 `Wait` 方法将永远被阻塞，无法继续往下运行。

这是另一个死锁的例子，因为 `main` 函数在等待永远不会发生的事情。

现在，使用 `go run` 再次运行被更新的代码：

```shell
go run main.go
```

你得到的输出可能与下面展示的略有不同，但总体上是相似的：

```
Output
sending 1 to channel
sending 2 to channel
read 1 from channel
read 2 from channel
sending 3 to channel
Waiting for functions to finish...
read 3 from channel
Done!
```

程序的输出显示，`generateNumbers` 函数正在生成1到3的数字，同时将它们写入与 `printNumbers` 共享的通道。`printNumbers` 一收到数字，就会将其打印到屏幕上。在 `generateNumbers` 生成了所有的三个数字后，它将会退出，允许 `main` 函数关闭通道并等待 `printNumbers` 执行完毕。一旦 `printNumbers` 完成最后一个数字的打印，它就会在 `WaitGroup` 上调用 `Done`，随后程序退出。与之前的输出类似，你看到的实际输出取决于各种外部因素，比如操作系统或 Go 运行时选择运行的特定 goroutine，但应该比较接近。

使用 goroutines 和通道来设计程序的好处是，一旦你把程序设计成可分割的，你就可以把它扩展到更多的 goroutine。因为 `generateNumbers` 只是把数据写入至一个通道上，不管有多少其他的东西在从这个通道上读取数据都没问题，它将只是向任何读取该通道的东西发送数据。你可以通过运行多个 `printNumbers`  goroutine 来利用这个优势，这样每个 `printNumbers` 都会从同一个通道读取数据并且并发地处理数据。

现在你的程序正在使用通道进行通信，再次打开 `main.go` 文件，更新程序，使其启动多个 `printNumbers` goroutine。你需要调整对`wg.Add` 的调用方式，以便在启动每个 goroutine 时都能自动增加一个计数值。你不需要在调用 `generateNumbers`时将 `WaitGroup` 的计数值加一，因为程序在没有完成整个数字生成函数之前都不会继续运行（之前给计数值加一，是因为不加一会导致后面的程序直接运行完毕），这与先前将 `generateNumbers` 作为一个 goroutine 运行时不同。为了确保它在完成时不会减少 `WaitGroup` 的计数值，你应该从该函数中删除 `defer wg.Done()` 这一行。接着，在 `printNumbers` 中加入 goroutine 的编号，以更容易地观察每个通道的读取情况。增加被生成的数字的个数也是一个好主意，这样更容易看到数字被分散至各个 goroutine 中。

> projects/multifunc/main.go

```go
...

func generateNumbers(total int, ch chan<- int, wg *sync.WaitGroup) {
	for idx := 1; idx <= total; idx++ {
		fmt.Printf("sending %d to channel\n", idx)
		ch <- idx
	}
}

func printNumbers(idx int, ch <-chan int, wg *sync.WaitGroup) {
	defer wg.Done()

	for num := range ch {
		fmt.Printf("%d: read %d from channel\n", idx, num)
	}
}

func main() {
	var wg sync.WaitGroup
	numberChan := make(chan int)

	for idx := 1; idx <= 3; idx++ {
		wg.Add(1)
		go printNumbers(idx, numberChan, &wg)
	}

	generateNumbers(5, numberChan, &wg)

	close(numberChan)

	fmt.Println("Waiting for goroutines to finish...")
	wg.Wait()
	fmt.Println("Done!")
}
```

当 `main.go` 被更新后，你可以再次使用 `go run` 运行它。在继续运行数字生成函数之前，你的程序会启动三个 `printNumbers` goroutine。你的程序现在应该生成5个数字，而不是3个，以便更容易看到分布在3个 `printNumbers` goroutine 中的数字。

```shell
go run main.go
```

输出可能类似于这样（尽管你的输出结果可能大不相同）：

```
Outputsending 1 to channel
sending 2 to channel
sending 3 to channel
3: read 2 from channel
1: read 1 from channel
sending 4 to channel
sending 5 to channel
3: read 4 from channel
1: read 5 from channel
Waiting for goroutines to finish...
2: read 3 from channel
Done!
```

你这次的程序输出，很可能与上面的输出有很大差别。因为有三个 `printNumbers` goroutine 在运行，偶然的因素决定了哪一个 goroutine 收到哪个特定的数字。

* 当一个 `printNumbers` goroutine 收到一个数字时，它会花费少量的时间将该数字打印到屏幕上，而另一个 goroutine 从通道中读取下一个数字并做同样的事情。
* 当一个 goroutine 完成了打印数字的工作并准备读取另一个数字时，它将再次读取通道以打印下一个数字。
* 如果没有更多的数字要从通道中读取，它将被阻塞，直到可以读取下一个数字。
* 一旦 `generateNumbers ` 执行完毕，并且 `close()` 被调用，所有三个 `printNumbers` goroutine 将完成其 `range` 循环并退出。
* 当所有三个 goroutine 都退出并在 `WaitGroup` 上调用 `Done` 时，`WaitGroup` 的计数将达到零，程序将退出。

你也可以尝试增加或减少生成的 goroutine 或数字的个数，看看这对输出有什么影响。

当使用 goroutines 时，要避免启动太多 goroutine。理论上，一个程序可以有数百甚至数千个 goroutine。然而，取决于运行程序的计算机，启动更多的 goroutine 实际运行速度可能会更慢。在大量的 goroutine 被启动的情况下，有可能遇到 [_资源饥饿_](https://en.wikipedia.org/wiki/Starvation_(computer_science))。每次 Go 运行 goroutine 的一部分时，除了运行下一个函数的代码所需的时间外，它还需要一些额外的时间来重新开始运行。由于需要额外的时间，计算机在运行每个 goroutine 之间切换的时间有可能比实际运行 goroutine 本身的时间还长。当这种情况发生时，它被称为资源饥饿，因为程序和它的 goroutine 们没有得到它们运行所需的资源，或者得到的资源非常少。在这种情况下，降低程序的并发数可能会更快，因为这将降低在 goroutine 之间切换的时间，而将更多时间用于运行程序本身。记住，你可以把运行程序的机器的 CPU 核心的数量，当作决定使用多少个 goroutine 的首要考量因素。

使用 goroutines 和通道的组合，使创建非常强大的程序成为可能，并快速地将运行于小型桌面计算机上的程序，扩展到大型服务器。正如你在这一节中所看到的，通道可以用来在少数几个 goroutine 和潜在的数千个 goroutine 之间进行通信，并且只需要做最小的改动。如果你在编写程序时考虑到了这一点，你就能利用 Go 的可靠的并发机制，为你的用户提供更好的体验。

## 结论

在本教程中，你创建了一个程序，使用 `go` 关键字来启动并发运行的 goroutine，在它们运行时打印出数字。程序一运行，你就用 `make(chan int)` 创建了一个新的 `int` 值通道，然后用这个通道在一个 goroutine 中生成数字，并将它们发送到另一个 goroutine 中以打印到屏幕上。最后，你同时启动了多个 "打印 " goroutine，并将其作为一个例子，来说明如何利用通道和 goroutine 在多核计算机上加速你的程序。

如果你有兴趣了解更多关于 Go 编程的知识，可以看看由 Go 团队创建的更为详细的 [Effective Go](https://golang.org/doc/effective_go#concurrency) 文档。[并发不是并行](https://go.dev/blog/waza-talk) 这一 Go 博客也是一篇有趣的后续文章，讲述了并发和并行之间的关系，这两个术语有时被错误地混为一谈。
