# 了解 Go 中的 init
## 简介

在 Go 中，预定义的 `init()` 函数设置了一段代码，在你的包的任何其他部分之前运行。这段代码将在[包被导入](https://www.digitalocean.com/community/tutorials/importing-packages-in-go)后立即执行，当你需要你的应用程序在一个特定的状态下初始化时，例如你有一个特定的配置或一组资源，你的应用程序需要用它来启动。它也可以在*导入副作用*时使用，这是一种通过导入特定包来设置程序状态的技术。这经常被用于 `register` 一个包和另一个包，以确保程序考虑任务的正确代码。

尽管 `init()` 是一个有用的工具，但它有时会使代码难以阅读，因为难以找到的 `init()` 实例会大大影响代码的运行顺序。正因为如此，对于刚接触 Go 的开发者来说，了解这个函数的方方面面是非常重要的，这样他们在写代码时就能确保以可读的方式使用 `init()`。

在本教程中，你将学习 `init()` 如何用于设置和初始化特定包的变量、一次性计算，以及注册一个包以便与另一个包一起使用。

## 先决条件

对于本文中的一些例子，你将需要：

* 按照 [如何安装 Go 和设置本地编程环境]（https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-go）设置的 Go 工作空间。本教程将使用以下文件结构：
```shell
.
├── bin 
│ 
└── src
    └── github.com
        └── gopherguides
```
## 定义 `init()`

只要你定义一个 `init()` 函数，Go 就会在该包的其他东西之前加载并运行它。为了证明这一点，本节将介绍如何定义一个 `init()` 函数，并展示对包的运行的影响。

首先，让我们以下面这个没有 `init()` 函数的代码为例：

<center>main.go</center>

```go
package main

import "fmt"

var weekday string

func main() {
	fmt.Printf("Today is %s", weekday)
}
```

在这个程序中，我们声明了一个全局[变量](https://www.digitalocean.com/community/tutorials/how-to-use-variables-and-constants-in-go)，叫做 `weekday`。默认情况下，`weekday` 的值是一个空字符串。

让我们运行这段代码：

```shell
go run main.go
```

因为 `weekday` 的值是空的，当我们运行程序时，我们将得到以下输出：

```shell
Today is
```
我们可以通过引入一个 `init()` 函数，将 `weekday` 的值初始化为当前日期，来填补这个空白变量。在 `main.go` 中加入以下高亮行：

<center>main.go</center>

```go
package main

import (
	"fmt"
	"time"
)

var weekday string

func init() {
	weekday = time.Now().Weekday().String()
}

func main() {
	fmt.Printf("Today is %s", weekday)
}
```
在这段代码中，我们导入并使用了 `time` 包来获取当前的星期（`Now().Weekday().String()`），然后使用 `init()` 用这个值来初始化 `weekday`。
现在当我们运行该程序时，它将打印出当前的工作日：

```shell
Today is Monday
```
虽然这说明了 `init()` 是如何工作的，但 `init()` 更典型的使用情况是在导入软件包时使用它。当你在使用软件包之前需要在软件包中进行特定的设置任务时，这就很有用。为了证明这一点，让我们创建一个程序，该程序需要一个特定的初始化，以便包能够如期工作。

## 导入时初始化软件包

首先，我们将写一些代码，从[切片](https://www.digitalocean.com/community/tutorials/understanding-arrays-and-slices-in-go)中选择一个随机的生物并打印出来。然而，我们不会在初始程序中使用 `init()`。这将更好地展示我们的问题，以及 `init()` 将如何解决我们的问题。

在你的 `src/github.com/gopherguides/ `目录中，用以下命令创建一个名为 `creature` 的文件夹。

```shell
mkdir creature
```

在 `creature` 文件夹下，创建一个名为 `creature.go` 的文件：

```shell
nano creature/creature.go
```

在这个文件中，添加以下内容：

```go
package creature

import (
	"math/rand"
)

var creatures = []string{"shark", "jellyfish", "squid", "octopus", "dolphin"}

func Random() string {
	i := rand.Intn(len(creatures))
	return creatures[i]
}
```

这个文件定义了一个叫做 `creatures` 的变量，它有一组初始化为数值的海洋生物。它还有一个[exported](https://www.digitalocean.com/community/tutorials/understanding-package-visibility-in-go#exported-and-unexported-items) `Random` 函数，将从 `creatures` 变量中返回一个随机值。

保存并退出这个文件。

接下来，让我们创建一个 `cmd` 包，我们将用它来编写 `main()` 函数并调用 `creature` 包。

在我们创建 `creature` 文件夹的同一文件层，用以下命令创建一个 `cmd` 文件夹：

```shell
mkdir cmd
```

在 `cmd` 文件夹中，创建一个名为 `main.go` 的文件：

```shell
nano cmd/main.go
```

在文件中添加以下内容：

<center>cmd/main.go</center>

```go
package main

import (
	"fmt"

	"github.com/gopherguides/creature"
)

func main() {
	fmt.Println(creature.Random())
	fmt.Println(creature.Random())
	fmt.Println(creature.Random())
	fmt.Println(creature.Random())
}
```

这里我们导入了 `creature` 包，然后在 `main()` 函数中，使用 `creature.Random() `函数来检索一个随机生物并打印出来四次。

保存并退出 `main.go`。

我们现在已经写好了我们的整个程序。然而，在我们运行这个程序之前，我们还需要创建几个配置文件，以便我们的代码能够正常工作。Go 使用[Go Modules](https://blog.golang.org/using-go-modules)来配置导入资源的软件包依赖性。这些模块是放置在你的包目录中的配置文件，告诉编译器从哪里导入包。虽然对模块的学习超出了本文的范围，但我们只需写几行配置就可以让这个例子在本地运行。

在 `cmd` 目录下，创建一个名为 `go.mod` 的文件：

```shell
nano cmd/go.mod
```

文件打开后，放入以下内容：

<center>cmd/go.mod</center>

```go
module github.com/gopherguides/cmd
replace github.com/gopherguides/creature => ../creature
```

这个文件的第一行告诉编译器，我们创建的 `cmd` 包实际上是 `github.com/gopherguides/cmd`。第二行告诉编译器，`github.com/gopherguides/creature`可以在磁盘上的 `.../creature` 目录下找到。
保存并关闭该文件。接下来，在 `creature` 目录下创建一个 `go.mod` 文件。

```shell
nano creature/go.mod
```

在文件中添加以下一行代码：

<center>creature/go.mod</center>


```go
 module github.com/gopherguides/creature
```
这告诉编译器，我们创建的 `creature` 包实际上是 `github.com/gopherguides/creature` 包。没有这个，`cmd` 包就不知道从哪里导入这个包。
保存并退出该文件。

现在你应该有以下的目录结构和文件布局：

```shell
├── cmd
│   ├── go.mod
│   └── main.go
└── creature
    ├── go.mod
    └── creature.go
```
现在我们已经完成了所有的配置，我们可以用下面的命令运行 `main` 程序：
```shell
go run cmd/main.go
```

这将输出：

```shell
jellyfish
squid
squid
dolphin
```
当我们运行这个程序时，我们收到了四个数值并打印出来。如果我们多次运行这个程序，我们会注意到，我们***总是***得到相同的输出，而不是预期的随机结果。这是因为 `rand` 包创建了伪随机数，对于单一的初始状态会持续产生相同的输出。为了实现更多的随机数，我们可以用 *seed* 包，或者设置一个不断变化的源，这样每次运行程序时的初始状态都会不同。在 Go 中，通常使用当前时间作为 `rand` 包的种子。
由于我们想让 `creature` 包来处理随机功能，所以打开这个文件。

```shell
nano creature/creature.go
```

在 `creature.go` 文件中添加以下高亮行：

<center>creature/creature.go</center>

```go
package creature

import (
	"math/rand"
	"time"
)

var creatures = []string{"shark", "jellyfish", "squid", "octopus", "dolphin"}

func Random() string {
	rand.Seed(time.Now().UnixNano())
	i := rand.Intn(len(creatures))
	return creatures[i]
}
```
在这段代码中，我们导入了 `time` 包，并使用当前时间作为 `Seed()` 的种子。保存并退出该文件。
现在，当我们运行该程序时，我们将得到一个随机的结果：

```shell
go run cmd/main.go
```

```shell
jellyfish
octopus
shark
jellyfish
```
如果你继续反复运行该程序，你将继续得到随机结果。然而，这还不是我们代码的理想实现，因为每次调用 `creature.Random()` 时，也会通过再次调用 `rand.Seed(time.Now().UnixNano()` 来重新播种 `rand` 包。如果内部时钟没有改变，重新播种会增加用相同初始值播种的机会，这将导致随机模式可能的重复，或者会因为让你的程序等待时钟改变而增加 CPU 处理时间。
为了解决这个问题，我们可以使用一个 `init()` 函数。让我们更新 `creature.go` 文件：

```shell
nano creature/creature.go
```

添加以下几行代码：

<center>creature/creature.go</center>

```go
package creature

import (
	"math/rand"
	"time"
)

var creatures = []string{"shark", "jellyfish", "squid", "octopus", "dolphin"}

func init() {
	rand.Seed(time.Now().UnixNano())
}

func Random() string {
	i := rand.Intn(len(creatures))
	return creatures[i]
}
```
添加 `init()` 函数告诉编译器，当 `creature` 包被导入时，它应该运行一次 `init()` 函数，为随机数生成提供一个种子。这确保了我们不会超过必须的时间来运行代码。现在，如果我们运行该程序，我们将继续得到随机结果：

```shell
go run cmd/main.go
```

```shell
dolphin
squid
dolphin
octopus
```
在这一节中，我们已经看到使用 `init()` 可以确保在使用包之前进行适当的计算或初始化。接下来，我们将看到如何在一个包中使用多个 `init()` 语句。

## 多个 `init()` 实例

与只能声明一次的 `main()` 函数不同，`init()` 函数可以在一个包中多次声明。然而，多个 `init()` 会使我们很难知道哪个函数比其他函数有优先权。在本节中，我们将展示如何保持对多个 `init()` 语句的控制。

在大多数情况下，`init()`函数将按照你遇到它们的顺序执行。让我们以下面的代码为例：

main.go

```go
package main

import "fmt"

func init() {
	fmt.Println("First init")
}

func init() {
	fmt.Println("Second init")
}

func init() {
	fmt.Println("Third init")
}

func init() {
	fmt.Println("Fourth init")
}

func main() {}
```

如果我们用以下命令运行该程序：

```shell
go run main.go
```

我们将收到以下输出：

```shell
First init
Second init
Third init
Fourth init
```
注意，每个 `init()` 都是按照编译器遇到它的顺序来运行的。然而，要确定 `init()` 函数的调用顺序可能并不总是那么容易。
让我们看看一个更复杂的包结构，其中我们有多个文件，每个文件都有自己的 `init()` 函数声明。为了说明这一点，我们将创建一个程序，共享一个名为 `message` 的变量并将其打印出来。

删除前面的 `creature` 和 `cmd` 目录及其内容，用下面的目录和文件结构取代它们：

```shell
├── cmd
│   ├── a.go
│   ├── b.go
│   └── main.go
└── message
    └── message.go
```
现在我们来添加每个文件的内容。在 `a.go` 中，添加以下几行：

<center>cmd/a.go</center>

```go
package main

import (
	"fmt"

	"github.com/gopherguides/message"
)

func init() {
	fmt.Println("a ->", message.Message)
}
```

这个文件包含一个 `init() `函数，打印出 `message` 包中 `message.Message` 的值。

接下来，在 `b.go` 中添加以下内容：

<center>cmd/b.go</center>

```go
package main

import (
	"fmt"

	"github.com/gopherguides/message"
)

func init() {
	message.Message = "Hello"
	fmt.Println("b ->", message.Message)
}
```

在 `b.go` 中，我们有一个 `init()` 函数，将 `message.Message` 的值设置为 `Hello` 并打印出来。

接下来，创建 `main.go`，看起来像下面这样：

<center>cmd/main.go</center>

```go
package main

func main() {}
```

这个文件什么也不做，但为程序的运行提供了一个入口点。

最后，创建你的 `message.go` 文件，如下所示：

<center>message/message.go</center>

```go
package message

var Message string
```

我们的 `message` 包声明了导出的 `Message` 变量。

要运行该程序，在 `cmd` 目录下执行以下命令：

```shell
go run *.go
```

因为我们在 `cmd` 文件夹中有多个 Go 文件组成 `main` 包，我们需要告诉编译器，`cmd` 文件夹中所有的 `.go` 文件都应该被编译。使用 `*.go` 告诉编译器加载 `cmd` 文件夹中所有以 `.go` 结尾的文件。如果我们发出 `go run main.go` 的命令，程序将无法编译，因为它看不到 `a.go` 和 `b.go` 文件中的代码。

这将得到以下输出：

```shell
a ->
b -> Hello
```
根据 Go 语言对[包初始化](https://golang.org/ref/spec#Package_initialization)的规范，当一个包中遇到多个文件时，会按字母顺序处理。正因为如此，我们第一次从 `a.go` 中打印出 `message.Message` 时，其值是空白的。在运行 `b.go` 的 `init()` 函数之前，该值没有被初始化。
如果我们把 `a.go` 的文件名改为 `c.go`，我们会得到一个不同的结果：

```shell
b -> Hello
a -> Hello
```

现在编译器先遇到了 `b.go`，因此，当遇到 `c.go` 中的 `init()` 函数时，`message.Message` 的值已经被初始化为 `Hello`。

这种行为可能会在你的代码中产生一个可能的问题。在软件开发中，改变文件名是很常见的，由于 `init()` 的处理方式，改变文件名可能改变 `init()` 的处理顺序。这可能会产生改变你的程序输出的不良后果。为了确保可重复的初始化行为，我们鼓励构建系统以词法文件名的顺序向编译器展示属于同一软件包的多个文件。确保所有 `init()` 函数按顺序加载的一个方法是在一个文件中声明它们。这将防止即使文件名被改变，顺序也不会改变。

除了确保你的 `init()` 函数的顺序不发生变化外，你还应该尽量避免使用*全局变量*来管理包中的状态，即在包中任何地方都可以访问的变量。在前面的程序中， `message.Message` 变量对整个包都是可用的，并保持着程序的状态。由于这种访问，`init()` 语句能够改变该变量并破坏你的程序的可预测性。为了避免这种情况，尽量在受控的空间内处理变量，在允许程序工作的同时，尽可能减少访问。

我们已经看到，你可以在一个包中有多个 `init()` 声明。然而，这样做可能会产生不想要的效果，使你的程序难以阅读或预测。避免多个 `init()` 声明或将它们全部放在一个文件中，将确保当文件被移动或名称被改变时，你的程序的行为不会改变。

接下来，我们将检查 `init()` 是如何被用来导入产生副作用的。

## 使用 `init()` 的副作用

在 Go 中，有时导入一个包并不是为了它的内容，而是为了导入包后产生的副作用。这通常意味着在导入的代码中有一个 `init()` 语句，在其他代码之前执行，允许开发者操纵他们程序开始的状态。这种技术被称为*导入的副作用*。

为副作用而导入的一个常见用例是在你的代码中*注册*功能，这让包知道你的程序需要使用哪部分代码。例如，在 `image` [包](https://golang.org/pkg/image/)中，`image.Decode` 函数在执行前需要知道它要解码的图像格式（`jpg`，`png`，`gif`，等等）。你可以通过首先导入一个有 `init()` 语句副作用的特定程序来完成这个任务。

假设你试图在一个`.png` 文件上使用 `image.Decode`，代码片段如下：

<center>Sample Decoding Snippet</center>

```go
. . .
func decode(reader io.Reader) image.Rectangle {
	m, _, err := image.Decode(reader)
	if err != nil {
		log.Fatal(err)
	}
	return m.Bounds()
}
. . .
```

使用这段代码的程序仍然可以编译，但任何时候我们试图对 `png` 图像进行解码时，都会出现错误。

为了解决这个问题，我们需要首先为 `image.Decode` 注册一个图像格式。幸运的是，`image/png` 包包含以下 `init()` 语句：

<center>image/png/reader.go</center>

```go
func init() {
	image.RegisterFormat("png", pngHeader, Decode, DecodeConfig)
}
```

因此，如果我们将 `image/png` 导入我们的解码片段，那么 `image/png` 中的 `image.RegisterFormat()` 函数将在我们任何代码之前运行：

<center>Sample Decoding Snippet</center>

```go
. . .
import _ "image/png"
. . .

func decode(reader io.Reader) image.Rectangle {
	m, _, err := image.Decode(reader)
	if err != nil {
		log.Fatal(err)
	}
	return m.Bounds()
}
```
这将设置状态并注册我们需要 `image.Decode()` 的 `png` 版本。这个注册将作为导入 `image/png` 的一个副作用发生。
你可能已经注意到了在`image/png`之前的[空白标识符](https://golang.org/ref/spec#Blank_identifier)(`_`) 。 这是有必要的，因为 Go 不允许你导入那些在整个程序中不使用的包。通过包括空白标识符，导入本身的值被丢弃了，所以只有导入的副作用才会出现。这意味着，即使我们在代码中从未调用 `image/png` 包，我们仍然可以导入它的副作用。

当你需要导入一个包的时候，知道它的副作用是很重要的。如果没有适当的注册，你的程序很可能会被编译，但在运行时却不能正常工作。标准库中的包会在其文档中声明需要这种类型的导入。如果你写了一个需要导入副作用的包，你也应该确保你所使用的 `init()` 语句是有文档的，这样导入你的包的用户就能正确使用它。

## 总结

在本教程中，我们了解到 `init()` 函数是在你的包中的其他代码被加载之前加载的，它可以为一个包执行特定的任务，如初始化一个期望的状态。我们还了解到，编译器执行多个 `init()` 语句的顺序取决于编译器加载源文件的顺序。如果你想了解更多关于 `init()` 的信息，请查看官方的[Golang 文档](https://golang.org/doc/effective_go.html#init)，或者阅读[Go 社区中关于该函数的讨论](https://github.com/golang/go/issues/25885)。

你可以通过我们的[如何在 Go 中定义和调用函数](https://www.digitalocean.com/community/tutorials/how-to-define-and-call-functions-in-go)文章阅读更多关于函数的信息，或者探索[整个 Go 中如何编程系列](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go)。
