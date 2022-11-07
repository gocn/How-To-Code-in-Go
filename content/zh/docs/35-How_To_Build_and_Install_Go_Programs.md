# 如何构建和安装 Go 程序

## 简介

到目前为止，在我们的 [How To Code in Go 系列](https://gocn.github.io/How-To-Code-in-Go/)中，你已经使用了[`go run`](https://gocn.github.io/How-To-Code-in-Go/docs/04-How_To_Write_Your_First_Program_in_Go_DigitalOcean/#%E7%AC%AC%E4%BA%8C%E6%AD%A5--%E8%BF%90%E8%A1%8C-go-%E7%A8%8B%E5%BA%8F) 命令来自动编译你的源代码并生成可执行文件。虽然这个命令对于在命令行上测试你的代码很有用，但是分发或部署你的应用程序则需要将你的代码构建成一个可共享的二进制可执行文件，或者一个包含机器字节码的单一文件来运行你的应用程序。要做到这一点，你可以使用 Go 工具链来构建和安装你的程序。

在 Go 中，将源代码转译成二进制可执行文件的过程被称为构建。一旦这个可执行文件被构建，它将不仅包含你的应用程序，还包含在目标平台上执行二进制文件所需的所有支持代码。这意味着 Go 二进制文件不需要 Go 工具链等系统依赖就可以在新系统上运行。将这些可执行文件放在自己系统的可执行文件路径中，就可以在系统的任何地方运行程序，这与把程序安装到你的操作系统上是一样的。

在本教程中，你将使用 Go 工具链来运行、构建和安装一个示例 `Hello, World!` 程序，让你有效地使用、分发和部署未来的应用程序。

## 前置条件

要遵循本文的例子，你将需要：

- 按照[如何安装 Go 与设置本地编程环境](https://gocn.github.io/How-To-Code-in-Go/docs/01-How_To_Install_Go_and_Set_Up_a_Local Programming_Environment_on_Ubuntu_18.04_DigitalOcean)设置的 Go 工作区。

## 第 1 步 - 设置和运行 Go 二进制文件

首先，创建一个应用程序，作为演示 Go 工具链的例子。要做到这一点，你将使用[如何用 Go 写第一个程序](https://gocn.github.io/How-To-Code-in-Go/docs/04-How_To_Write_Your_First_Program_in_Go_DigitalOcean)教程中的经典程序 "Hello, World!"。

在你的 `src` 目录下创建一个名为 `greeter` 的目录：

```bash
mkdir greeter
```

接下来，进入新创建的目录，在你选择的文本编辑器中创建 `main.go` 文件：

```bash
cd greeter
nano main.go
```

打开文件，添加以下内容：

```go
# src/greeter/main.go

package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

当程序运行时，这个程序将在控制台打印 `Hello, World!` 这句话，然后程序将成功退出。

保存并退出该文件。

要测试这个程序，使用 `go run` 命令，就像你在之前的教程中做的那样：

```bash
go run main.go
```

你会收到以下输出：

```bash
Output
Hello, World!
```

如前文所述，`go run` 命令将你的源文件构建成可执行的二进制文件，然后运行编译后的程序。然而，本教程的目的是以一种可以随意分享和分发的方式来构建二进制文件。要做到这一点，你将在下一步使用 `go build` 命令。

## 第 2 步 -- 创建 Go 模块，建立 Go 二进制文件

Go 程序和库是围绕着模块的核心概念建立的。一个模块包含了你的程序所使用的库的信息，以及要使用这些库的什么版本。

为了告诉 Go 这是一个 Go 模块，你需要使用 go mod 命令[创建一个 Go 模块](https://www.digitalocean.com/community/tutorials/how-to-use-go-modules)：

```bash
go mod init greeter
```

这将创建 `go.mod` 文件，其中包含模块的名称和用于构建模块的 Go 的版本。

```bash
Output
go: creating new go.mod: module greeter
go: to add module requirements and sums:
	go mod tidy
```

Go 会提示你运行 `go mod tidy`，以便在将来该模块的需求发生变化时更新它，现在运行它不会有额外的效果。

## 第 3 步 - 用 `go build` 构建 Go 二进制文件

使用 `go build`，你可以为我们的示例 Go 应用程序生成可执行的二进制文件，以便在任何你想要的地方分发和部署该程序。

在 `main.go` 尝试一下，在你的 `greeter` 目录下，运行以下命令：

```bash
go build
```

如果你没有给这个命令提供参数，`go build` 将自动编译你当前目录下的 `main.go` 程序。该命令将包括该目录中所有的 `*.go` 文件。它还将编译所有支持代码，以便能够在任何具有相同系统结构的计算机上执行二进制文件，无论该系统是否有 `.go` 源文件，甚至是否安装了 Go。

在这种情况下，你把你的 `greeter` 程序构建在一个可执行文件中，并被添加到你的当前目录中。通过运行 `ls` 命令来检查：

```bash
ls
```

如果你运行的是 macOS 或 Linux 系统，你会发现一个以你构建程序的目录命名的新可执行文件：

```bash
Output
greeter  main.go  go.mod
```

```plaintext
注：在Windows上，你的可执行文件将是 greeter.exe。
```

默认情况下，`go build` 将为当前[平台和架构](https://gocn.github.io/How-To-Code-in-Go/docs/38-Building_Go_Applications_for_Different_Operating_Systems_and_Architectures/#goos%E5%92%8Cgoarch%E5%8F%AF%E8%83%BD%E6%94%AF%E6%8C%81%E7%9A%84%E5%B9%B3%E5%8F%B0)生成一个可执行文件。例如，如果在 `linux/386` 系统上构建，可执行文件将与任何其他 `linux/386` 系统兼容，即使并没有安装 Go。Go 支持为其他平台和架构进行构建，你可以在我们的[为不同操作系统和架构构建 Go 应用程序](https://gocn.github.io/How-To-Code-in-Go/docs/38-Building_Go_Applications_for_Different_Operating_Systems_and_Architectures)文章中了解更多信息。

现在，你已经创建了你的可执行文件，运行它以确保二进制文件已被正确构建。在 macOS 或 Linux 上，运行以下命令：

```bash
./greeter
```

在 Windows 上，运行：

```bash
greeter.exe
```

二进制文件的输出将与你用 `go run` 运行程序时的输出一致：

```bash
Output
Hello, World!
```

现在你已经创建了一个单一的可执行二进制文件，它不仅包含你的程序，还包含运行该二进制文件所需的所有系统代码。因为这个文件将始终运行同一个程序，你现在可以将这个程序分发到新的系统中，或者将其部署到服务器上。

在下一节中，本教程将解释二进制文件是如何命名的，以及你如何改变它，以便你能更好地控制程序的构建过程。

## 第 4 步 - 改变二进制名称

现在你知道了如何生成可执行文件，下一步则是确定 Go 如何为二进制文件选择一个名字，并为你的项目定制这个名字。

当你运行 `go build` 时，默认情况下 Go 会自动决定生成的可执行文件的名称。它通过使用你之前创建的模块来实现这一目的。当运行 `go mod init greeter` 命令时，它创建了名为 `greeter` 的模块，这就是为什么生成的二进制文件被命名为 `greeter`。

让我们仔细看看这个模块方法。如果你的项目中有一个 `go.mod` 文件，其中有一个 `module` 声明，像下面这样：

```go
# go.mod
module github.com/sammy/shark
```

那么生成的可执行文件默认名称将是 `shark`。

在需要特定命名规则的更复杂的程序中，这些默认值并不总是你命名二进制文件的最佳选择。在这些情况下，最好用 `-o` 标识来定制你的输出。

为了测试这一点，把你在上一节中制作的可执行文件的名称改为 `hello`，并把它放在一个名为 `bin` 的子文件夹中。你不需要创建这个文件夹，Go 会在构建过程中自行完成。

运行以下带有 `-o` 标志的 `go build` 命令：

```bash
go build -o bin/hello
```

`-o` 标志使 Go 将命令的输出与你选择的任何参数相匹配。在本例中，结果是在一个名为 `bin` 的子文件夹中产生一个名为 `hello` 的新可执行文件。

要测试新的可执行文件，请切换到新目录并运行二进制文件:

```bash
cd bin
./hello
```

你将收到以下输出:

```bash
Output
Hello, World!
```

现在你可以自定义你的可执行文件名称，以适应项目需要，这完成了我们对如何在 Go 中构建二进制文件的调研。但是使用 `go build`，你仍然只限于在当前目录下运行你的二进制文件。为了在系统的任何地方使用新建立的可执行文件，你可以用 `go install` 来安装它们。

## 第 5 步 - 用 `go install` 安装 Go 程序

到目前为止，在这篇文章中，我们已经讨论了如何从我们的 `.go` 源文件中生成可执行二进制文件。这些可执行文件有助于分发、部署和测试，但它们还不能在其源文件目录之外执行。如果你想在 shell 脚本或其他工作流程中使用你的程序，这将是一个问题。为了使这些程序更容易使用，你可以把它们安装到你的系统中，并从任何地方访问它们。

为了理解这一点的含义，你将使用 `go install` 命令来安装你的示例程序。

`go install` 命令与 `go build` 几乎相同，但它不是将可执行文件留在当前目录或由 `o` 标志编译到指定的目录中，而是将可执行文件放到 `$GOPATH/bin` 目录中。

要找到你的 `$GOPATH` 目录的位置，请运行以下命令：

```bash
go env GOPATH
```

你收到的输出会有所不同，但默认是你的 `$HOME` 目录下的 `go` 目录：

```bash
Output
$HOME/go
```

由于 `go install` 会将生成的可执行文件放入 `$GOPATH` 的一个子目录，名为 `bin`，这个目录必须被添加到 `$PATH` 环境变量中。这在先决条件文章[如何安装 Go 和设置本地编程环境](https://gocn.github.io/How-To-Code-in-Go/docs/01-How_To_Install_Go_and_Set_Up_a_Local Programming_Environment_on_Ubuntu_18.04_DigitalOcean)的**创建 Go 工作空间**步骤中有所涉及。

设置好 `$GOPATH/bin` 目录后，切回你的 `greeter` 目录：

```bash
cd ..
```

现在运行安装命令：

```bash
go install
```

这将构建你的二进制文件并将其放在 `$GOPATH/bin` 中。要测试这一点，请运行以下程序：

```bash
ls $GOPATH/bin
```

这将列出 `$GOPATH/bin` 的内容：

```bash
Output
greeter
```

```plaintext
注：`go install` 命令不支持 `-o` 标记，所以它将使用前面描述的默认名称来命名可执行文件。
```

安装好二进制文件后，测试一下程序是否能从其源目录外运行，切回你的主目录：

```bash
cd $HOME
```

使用以下方法来运行该程序：

```bash
greeter
```

这将产生以下结果：

```bash
Output
Hello, World!
```

现在，你可以把你编写的程序安装到你的系统中，让你在任何地方、任何时候都能使用它们。

## 总结

在本教程中，你演示了 Go 工具链是如何从源代码中轻松构建可执行二进制文件的。这些二进制文件可以分发到其他系统上运行，甚至是那些没有 Go 工具链和环境的系统。你还使用 `go install` 自动构建并将程序作为可执行文件安装在系统的 `$PATH` 中。有了 `go build` 和 `go install`，你现在可以随意分享和使用你的应用程序。

现在你了解了 `go build` 的基础知识，你可以通过[用 Build 标签定制 Go 二进制文件](https://gocn.github.io/How-To-Code-in-Go/docs/31-Customizing_Go_Binaries_with_Build_Tags)教程来探索如何制作模块化的源代码，或者通过[为不同的操作系统和架构构建 Go 应用程序](https://gocn.github.io/How-To-Code-in-Go/docs/38-Building_Go_Applications_for_Different_Operating_Systems_and_Architectures)来探索如何为不同的平台构建。如果你想了解更多关于 Go 编程语言的信息，请查看整个[How To Code in Go 系列](https://gocn.github.io/How-To-Code-in-Go/)。
