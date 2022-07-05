# 如何使用 Go 模块

## 简介

在 1.13 版本，Go 的作者增加了一种管理 Go 项目依赖库的新方法，称之为 [Go 模块](https://golang.org/ref/mod)。Go 模块的加入是为了满足日益增长的需求，使开发者更容易维护其依赖的各种版本，同时也为开发者在计算机上组织项目的方式上，增加了更多的灵活性。Go 模块通常由一个项目或库组成，并包含一系列的 Go 包，然后一起发布。Go 模块解决了原始系统 [`GOPATH`](https://golang.org/doc/gopath_code) 的许多问题，它允许用户将项目代码放在他们选择的目录中，并为每个模块指定依赖的版本。

在本教程中，你将创建你自己的公共 Go module，并为你的新模块添加一个包。此外，你还将把别人的公共模块添加到你自己的项目中，以及把该模块的一个特定版本添加到你的项目中。

## 前期准备

要遵循本教程，你将需要。

- 安装1.16 或更高版本的 Go。如何安装 Go ，请根据你的操作系统遵循 [如何安装Go](https://www.digitalocean.com/community/tutorials/how-to-install-go-on-ubuntu-20-04) 教程。
- 熟悉用 Go 编写软件包。要了解更多，请遵循 [如何用 Go 编写包](https://www.digitalocean.com/community/tutorials/how-to-write-packages-in-go) 教程。

## 创建一个新的模块

乍看之下，Go模块与 [Go包](https://www.digitalocean.com/community/tutorials/how-to-write-packages-in-go) 有些相似。一个模块有许多实现包的功能的 Go 代码文件，但它在根部还有两个额外的重要文件：`go.mod` 文件和 `go.sum` 文件。这些文件包含了 `go` 工具用来跟踪你的模块配置的信息，通常由工具维护，所以你不需要维护。

首先要做的是决定模块所处的目录。随着 Go 模块的引入，Go 项目有可能位于文件系统的任何地方，而不仅仅是 Go 定义的特定目录。你可能已经有了一个存放项目的目录，但在本教程中，你将创建一个名为 `projects` 的目录，新模块将被称为 `mymodule`。你可以通过 IDE 或者命令行来创建 `projects` 目录。

如果你使用的是命令行，首先新建 `projects`目录并进入其中：

~~~
mkdir projects
cd projects
~~~

接下来，你将创建模块目录本身。通常，模块的顶层目录名称与模块名称相同，这使事情更容易跟踪。在你的 `projects` 目录下，运行以下命令来创建 `mymodule` 目录：

~~~
mkdir mymodule
~~~

一旦你创建了模块目录，目录结构将看起来像这样：

```
└── projects
    └── mymodule
```

下一步是在 `mymodule` 目录下创建一个 `go.mod` 文件来定义 Go 模块本身。要做到这一点，你要使用 `go` 工具的 `mod init` 命令，并向它提供模块的名称，在这个例子中是 `mymodule`。现在通过在 `mymodule` 目录下运行 `go mod init` 来创建模块，并向它提供模块的名称 `mymodule`：

~~~
go mod init mymodule
~~~

该命令在创建模块时将返回以下输出：

```
Output
go: creating new go.mod: module mymodule
```

随着模块的创建，你的目录结构现在看起来会是这样：

```
└── projects
    └── mymodule
        └── go.mod
```

现在你已经创建了一个模块，让我们看一下 `go.mod` 文件的内容，看看 `go mod init` 命令做了什么。

## 了解 `go.mod` 文件

当你用 `go` 工具运行命令时，`go.mod` 文件是一个非常重要的部分。它是包含模块名称和你自己模块所依赖的其他模块的版本的文件。它还可以包含其他指令，如 [`replace`](https://golang.org/ref/mod#go-mod-file-replace)，这对同时进行多个模块的开发很有帮助。

在 `mymodule` 目录下，用 `nano` 或你喜欢的文本编辑器打开 `go.mod` 文件：

~~~
nano go.mod
~~~

内容将看起来与此类似，这并不重要：

projects/mymodule/go.mod

~~~
module mymodule

go 1.16
~~~

第一行，`module` 指令，告诉 Go 你的模块名称，这样当它在包中寻找 `import` 路径时，它就不会在其他地方寻找 `mymodule`。`mymodule` 的值来自你传递给 `go mod init` 的参数：

~~~
module mymodule
~~~

文件中的另外一行，即 `go` 指令，告诉 Go 模块所针对的语言版本。在本例中，由于该模块是用 Go 1.16 创建的，所以 `go` 指令显示为 `1.16` ：

```
go 1.16
```

随着更多的信息被添加到模块中，这个文件将被扩展，但现在看看它是个好主意，看看它是如何随着依赖关系的进一步添加而变化的。

现在你已经用 `go mod init` 创建了一个 Go 模块，并查看了初始 `go.mod` 文件的内容，但你的模块还没有做任何事情。现在是时候让你的模块更进一步，添加一些代码了。

## 在你的模块中添加Go代码

为了确保模块被正确创建，并添加代码以便你能运行你的第一个 Go 模块，你将在 `mymodule` 目录下创建一个 `main.go` 文件。`main.go` 文件在 Go 程序中通常用来表示程序的起始点。该文件的名称并不像里面的 `main` 函数那么重要，但两者的匹配使其更容易被找到。在本教程中，`main` 函数在运行时将打印出 `Hello, Modules!`。

要创建这个文件，用 `nano` 或你喜欢的文本编辑器打开 `main.go` 文件：

```
nano main.go
```

在 `main.go` 文件中，添加以下代码来定义你的 `main` 包，导入 `fmt` 包，然后在 `main` 函数中打印出 `Hello, Modules!`信息：

projects/mymodule/main.go

~~~go
package main

import "fmt"

func main() {
	fmt.Println("Hello, Modules!")
}
~~~

在 Go 中，每个目录都被视为自己的包，每个文件都有自己的 `package` 声明行。在你刚刚创建的 `main.go` 文件中，`包` 被命名为 `main`。通常情况下，你可以以任何方式命名包，但 `main` 包在 Go 中是很特别的。当 Go 看到一个包被命名为 `main ` 时，它知道这个包应该被视为二进制文件，应该被编译成可执行文件，而不是一个旨在用于其他程序的库。

在定义了  `包` 之后，`import` 声明说要导入 [`fmt`](https://pkg.go.dev/fmt) 包，所以你可以使用它的 `Println` 函数将  `"Hello, Modules!"` 信息打印到屏幕上。

最后，定义了 `main` 函数。`main` 函数是 Go 的另一个特例，与`main`包有关。当 Go 看到一个名为 `main` 的包内有一个名为 `main` 的函数时，它知道 `main` 函数是它应该运行的第一个函数。这被称为程序的入口点。

一旦你创建了 `main.go` 文件，该模块的目录结构将类似于这样：

```
└── projects
    └── mymodule
        └── go.mod
        └── main.go
```

如果你熟悉使用 Go 和 [`GOPATH`](https://www.digitalocean.com/community/tutorials/understanding-the-gopath) ，运行模块中的代码就类似于从 `GOPATH` 中的一个目录中进行。（如果你不熟悉 `GOPATH` 也不用担心，因为使用模块可以代替它的使用。）

在 Go 中运行可执行程序有两种常见的方法：用 `go build` 构建二进制文件或用 `go run` 运行文件。在本教程中，你将使用 `go run` 直接运行模块，而不是构建二进制文件，后者必须单独运行。

用 `go run` 运行你创建的 `main.go` 文件：

~~~
go run main.go
~~~

运行该命令将打印代码中定义的 `Hello, Modules!` 文本：

```
Output
Hello, Modules!
```

在这一节中，你为你的模块添加了一个 `main.go` 文件，其中的初始 `main` 函数打印出 `Hello, Modules!`。在这一点上，你的程序还没有从 Go 模块中获益—它可以是你电脑上任何地方的一个文件，用 `go run` 来运行。Go 模块的第一个真正好处是能够在任何目录下为你的项目添加依赖，而不仅仅是 `GOPATH` 目录结构。你还可以向你的模块添加包。在下一节中，你将通过在模块中创建一个额外的包来扩展你的模块。

## 为你的模块添加一个包

类似于标准的 Go 包，一个模块可以包含任何数量的包和子包，也可以完全不包含。在这个例子中，你将在 `mymodule` 目录下创建一个名为 `mypackage` 的包。

通过在 `mymodule` 目录下运行 `mkdir` 命令并加上 `mypackage` 参数来创建这个新包：

```
mkdir mypackage
```

这将创建新的目录 `mypackage` 作为 `mymodule` 目录的一个子包：

```
└── projects
    └── mymodule
        └── mypackage
        └── main.go
        └── go.mod
```

使用 `cd` 命令将当前目录改为新的 `mypackage` 目录，然后使用 `nano` ，或你喜欢的文本编辑器，创建一个 `mypackage.go` 文件。这个文件可以有任何名字，但使用与软件包相同的名字可以更容易找到软件包的主文件：

```
cd mypackage
nano mypackage.go
```

在 `mypackage.go` 文件中，添加一个名为 `PrintHello` 的函数，当被调用时，将打印 `Hello, Modules! This is mypackage speaking!` ：

projects/mymodule/mypackage/mypackage.go

```go
package mypackage

import "fmt"

func PrintHello() {
	fmt.Println("Hello, Modules! This is mypackage speaking!")
}
```

由于你希望 `PrintHello` 函数可以从另一个包中使用，函数名称中的大写字母 `P` 很重要。大写字母意味着该函数是导出的，对任何外部程序都是可用的。关于 Go 中包的可见性的更多信息，[Understanding Package Visibility in Go](https://www.digitalocean.com/community/tutorials/understanding-package-visibility-in-go) 包括更多细节。

现在你已经创建了带有导出函数的 `mypackage` 包，你将需要从 `mymodule` 包中 `导入` 它来使用它。这与你导入其他包的方法类似，比如之前的 `fmt` 包，只是这次你要在导入路径的开头加入你的模块名称。从 `mymodule` 目录下打开你的 `main.go` 文件，通过添加下面高亮的行来添加对 `PrintHello` 的调用：

projects/mymodule/main.go

```go
package main

import (
	"fmt"

	"mymodule/mypackage"
)

func main() {
	fmt.Println("Hello, Modules!")

	mypackage.PrintHello()
}
```

如果你仔细看一下 `import` 语句，你会看到新的导入以 `mymodule` 开始，也就是你在 `go.mod` 文件中设置的模块名称。后面是路径分隔符和你要导入的包，本例中是`mypackage`：

```
"mymodule/mypackage"
```

在未来，如果你在 `mypackage` 内添加包，你也会以类似的方式将它们添加到导入路径的末尾。例如，如果你在 `mypackage` 内有另一个叫 `extrapackage` 的包，你对该包的导入路径将是 `mymodule/mypackage/extrapackage`。

像以前一样，用`go run` 和 `main.go` 从 `mymodule` 目录中运行你的更新模块：

```
go run main.go
```

当你再次运行该模块时，你会看到先前的 "Hello, Modules!"信息，以及新的 `mypackage` 的 `PrintHello` 函数打印的新信息：

```
Output
Hello, Modules!
Hello, Modules! This is mypackage speaking!
```

你现在通过创建一个名为 `mypackage` 的目录和 `PrintHello` 函数，为你的初始模块添加了一个新包。随着你的模块功能的扩展，在你的模块中开始使用其他人的模块可能会很有用。在下一节中，你将添加一个远程模块作为你的模块的依赖。

## 添加一个远程模块作为依赖

Go 模块是通过版本控制库（通常是Git库）发布的。当你想添加一个新的模块作为自己的依赖时，你会使用仓库的路径作为你想使用的模块的引用方式。当 Go 看到这些模块的导入路径时，它可以根据这个仓库的路径推断出在哪里可以远程找到它。

在这个例子中，你要在你的模块中加入对 [`github.com/spf13/cobra`](https://github.com/spf13/cobra) 库的依赖。Cobra是一个用于创建控制台应用程序的流行库，但我们不会在本教程中讨论这个问题。

与你创建 `mymodule` 模块时类似，你将再次使用 `go` 工具。然而，这一次，你将在 `mymodule` 目录下运行 `go get` 命令。运行 `go get` 并提供你想添加的模块。在这种情况下，你会得到 `github.com/spf13/cobra` ：

```
go get github.com/spf13/cobra
```

当你运行这个命令时，`go` 工具将从你指定的路径查找 Cobra 仓库，并通过查看仓库的分支和标签来确定哪个版本的 Cobra 是最新的。然后，它将下载该版本，并通过在 `go.mod` 文件中添加模块名称和版本来跟踪它所选择的版本，以供将来参考。

现在，打开 `mymodule` 目录下的 `go.mod` 文件，看看当你添加新的依赖时，`go` 工具如何更新 `go.mod` 文件。下面的例子可能会有变化，这取决于当前已经发布的 Cobra 版本或你使用的 Go 工具的版本，但整体的变化结构应该是相似的：

projects/mymodule/go.mod

```
module mymodule

go 1.16

require (
	github.com/inconshreveable/mousetrap v1.0.0 // indirect
	github.com/spf13/cobra v1.2.1 // indirect
	github.com/spf13/pflag v1.0.5 // indirect
)
```

增加了一个使用  `require`  指令的新部分。这个指令告诉 Go 你想要哪个模块，比如    `github.com/spf13/cobra`，以及你添加的模块的版本。有时 `require` 指令也会包括一个`//间接`注释。这个注释表示，在添加 `require` 指令时，该模块没有在该模块的任何源文件中被直接引用。文件中还增加了一些额外的 `require` 行。这些行是 Cobra 所依赖的其他模块，Go 工具认为这些模块也应该被引用。

你可能还注意到，在运行 `go run ` 命令后，在 `mymodule` 目录下创建了一个新的文件，`go.sum` 。这是 Go 模块的另一个重要文件，包含了 Go 用来记录依赖关系的具体哈希值和版本的信息。这确保了依赖关系的一致性，即使它们被安装在不同的机器上。

一旦你下载了依赖关系，你要用一些最小的 Cobra 代码更新你的 `main.go` 文件，以使用新的依赖关系。用下面的Cobra 代码更新 `mymodule` 目录下的 `main.go` 文件，以使用新的依赖性：

projects/mymodule/main.go

```go
package main

import (
	"fmt"
    
	"github.com/spf13/cobra"

	"mymodule/mypackage"
)

func main() {
	cmd := &cobra.Command{
		Run: func(cmd *cobra.Command, args []string) {
			fmt.Println("Hello, Modules!")

			mypackage.PrintHello()
		},
	}

	fmt.Println("Calling cmd.Execute()!")
	cmd.Execute()
}
```

这段代码创建了一个 `cobra.Command` 结构，其中的 `Run` 函数包含你现有的 "Hello" 语句，然后将通过调用 `cmd.Execute()` 来执行。现在，运行更新后的代码：

```
go run main.go
```

你会看到下面的输出，它看起来与你之前看到的相似。不过这一次，它使用了你的新的依赖关系，如 `Calling cmd.Execute()!` 一行所示：

```
Output
Calling cmd.Execute()!
Hello, Modules!
Hello, Modules! This is mypackage speaking!
```

使用 `go get` 来添加远程依赖的最新版本，例如这里的 `github.com/sp13/cobra` ，使你的依赖更容易保持最新的错误修复。然而，有时你可能更愿意使用一个特定版本的模块、一个版本库标签或一个版本库分支。在下一节中，你将使用 `go get` 来引用这些版本，当你想要这个选项时。

## 使用一个模块的特定版本

由于 Go 模块是从版本控制库中发布的，它们可以使用版本控制功能，如标签、分支、甚至 commits 。你可以在你的依赖关系中使用`@`符号在模块路径的末尾加上你想使用的版本来引用这些。早些时候，当你安装最新版本的Cobra 时，你正在利用这种能力，但你不需要在命令中明确添加它。`go` 工具知道，如果没有用`@`提供特定的版本，它应该使用特殊的版本 `latest` 。`latest` 版本实际上并不在版本库中，就像 `my-tag` 或 `my-branch` 可能是一样。它作为一个辅助工具内置于  `go`  工具中，所以你不需要自己去搜索最新的版本。

例如，当你最初添加你的依赖关系时，你也可以使用下面的命令获得同样的结果：

```
go get github.com/spf13/cobra@latest
```

现在，想象有一个你使用的模块，目前正在开发中。在这个例子中，称它为 `your_domain/sammy/awesome` 。这个 `awesome` 模块正在增加一个新的功能，工作在一个叫做 `new-feature` 的分支中进行。要把这个分支作为你自己的模块的依赖项，你可以向 `go get` 提供模块的路径，后面是`@`符号，再后面是分支的名称：

```
go get your_domain/sammy/awesome@new-feature
```

运行这个命令会使 `go` 连接到 `your_domain/sammy/awesome` 仓库，下载 `new-feature` 分支的当前最新提交，并将该信息添加到 `go.mod` 文件。

不过，分支并不是唯一可以使用`@`选项的方式。这个语法可以用于标签，甚至是版本库的特定提交。例如，有时你正在使用的库的最新版本可能有一个坏的提交。在这种情况下，引用破损提交之前的提交可能会很有用。

以你的模块所依赖的 Cobra 为例，假设你需要引用 `github.com/spf13/cobra` 的 `07445ea` 提交，因为它有一些你需要的修改，而你因为某些原因不能使用其他版本。在这种情况下，你可以在`@`符号后面提供提交哈希值，就像对分支或标签一样。在你的 `mymodule`目 录下运行`go get`命令，输入模块和版本来下载新版本：

```
go get github.com/spf13/cobra@07445ea
```

如果你再次打开你模块的 `go.mod` 文件，你会看到 `go get` 已经更新了 `github.com/spf13/cobra` 的`require`行，引用你指定的提交：

projects/mymodule/go.mod

```go
module mymodule

go 1.16

require (
	github.com/inconshreveable/mousetrap v1.0.0 // indirect
	github.com/spf13/cobra v1.1.2-0.20210209210842-07445ea179fc // indirect
	github.com/spf13/pflag v1.0.5 // indirect
)
```

由于提交是一个特定的时间点，与标签或分支不同，Go在 `require` 指令中包含了额外的信息，以确保它在未来使用正确的版本。如果你仔细看一下版本，你会发现它确实包括你提供的提交散列。`v1.1.2-0.20210209210842-07445ea179fc`.

Go 模块也使用这个功能来支持发布不同版本的模块。当 Go 模块发布新版本时，一个新的标签会被添加到版本库中，并以版本号作为标签。如果你想使用一个特定的版本，你可以查看版本库中的标签列表，找到你要找的版本。如果你已经知道版本，你可能不需要在标签中搜索，因为版本标签的命名是一致的。

再以 Cobra 为例，假设你想使用 Cobra 1.1.1 版本。你可以查看 Cobra 仓库，发现它有一个名为  `v1.1.1` 的标签，还有其他标签。要使用这个标记的版本，你可以在 `go get` 命令中使用 `@` 符号，就像你使用一个非版本标记或分支。现在，通过运行以 `v1.1.1` 为版本的 `go get` 命令，更新你的模块以使用 Cobra 1.1.1：

```
go get github.com/spf13/cobra@v1.1.1
```

现在，如果你打开你的模块的 `go.mod` 文件，你会看到 `go get` 已经更新了  `github.com/spf13/cobra` 的`require` 行，引用你提供的版本：

projects/mymodule/go.mod

```go
module mymodule

go 1.16

require (
	github.com/inconshreveable/mousetrap v1.0.0 // indirect
	github.com/spf13/cobra v1.1.1 // indirect
	github.com/spf13/pflag v1.0.5 // indirect
)
```

最后，如果你正在使用一个特定版本的库，例如 `07445ea` 提交或早期的 `v1.1.1` ，但你确定你宁愿开始使用最新版本，可以通过使用特殊的 `latest` 版本来实现。要将你的模块更新到最新版本的 Cobra，请再次运行 `go get`，输入模块路径和 `latest` 版本：

```
go get github.com/spf13/cobra@latest
```

一旦这个命令完成，`go.mod` 文件将更新为你引用特定版本的 Cobra 之前的样子。根据你的 Go 版本和当前最新的Cobra 版本，你的输出可能会略有不同，但你仍然应该看到 `require` 部分的 `github.com/spf13/cobra` 行再次更新为最新版本：

```
module mymodule

go 1.16

require (
	github.com/inconshreveable/mousetrap v1.0.0 // indirect
	github.com/spf13/cobra v1.2.1 // indirect
	github.com/spf13/pflag v1.0.5 // indirect
)
```

`go get` 命令是一个强大的工具，你可以用它来管理 `go.mod` 文件中的依赖关系，而不需要手动编辑它。正如你在本节中看到的，在模块名称中使用 `@` 字符可以让你使用模块的特定版本，从发布版本到特定的版本库提交。它甚至可以用来回到你的依赖的 `latest` 版本。使用这些选项的组合将使你能够确保你的程序在未来的稳定性。

## 总结

在本教程中，你创建了一个带有子包的 Go 模块，并在你的模块中使用该包。你还将另一个模块作为依赖关系添加到你的模块中，并探索了如何以各种方式引用模块版本。

关于 Go 模块的更多信息，Go 项目有[一系列博文](https://blog.golang.org/using-go-modules)介绍 Go 工具如何与模块互动和理解模块。Go 项目还在 [Go模块参考](https://golang.org/ref/mod) 中为 Go 模块提供了非常详细的技术参考。

本教程也是 [DigitalOcean](https://www.digitalocean.com/)[How to Code in Go](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go) 系列教程的一部分。该系列涵盖了许多 Go 主题，从首次安装 Go 到如何使用语言本身。