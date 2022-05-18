

- 原文地址：[Building Go Applications for Different Operating Systems and Architectures | DigitalOcean](https://www.digitalocean.com/community/tutorials/building-go-applications-for-different-operating-systems-and-architectures)
- 原文作者：digitalocean
- 本文永久链接：
- 译者：[zxmfke](https://github.com/zxmfke)
- 校对：[pseudoyu](https://github.com/pseudoyu)

# 在不同的操作系统和架构编译 Go 应用

在软件开发中，重要的是要考虑你想为之编译二进制的[操作系统](https://en.wikipedia.org/wiki/Operating_system)和底层处理器[架构](https://en.wikipedia.org/wiki/Microarchitecture)。因为在不同的操作系统/架构平台上运行一个二进制文件通常很慢或不可能，所以通常的做法是为许多不同的平台编译你最终的二进制文件，以最大化你的程序的受众。然而，这通常是很困难的，当你开发软件的平台和你想要部署的平台不是同一个的时候。例如，在过去，在 Windows 上开发一个程序并将其部署到 Linux 或 macOS 机器上，需要为每一个你想要的二进制文件的环境设置构建机器。你还需要保持你的工具同步，此外还有其他考虑因素，这些因素会增加成本，使协作测试和分布式更加困难。

Go 通过在`go build`工具中直接建立对多平台的支持，以及 Go 工具链的其他部分解决了这个问题。通过使用[环境变量](https://www.digitalocean.com/community/tutorials/how-to-read-and-set-environmental-and-shell-variables-on-a-linux-vps)和[构建标签](https://www.digitalocean.com/community/tutorials/customizing-go-binaries-with-build-tags)，你可以控制你最终的二进制文件是为哪个操作系统和架构构建的，此外还可以把一个工作流程放在一起，在不改变你的代码库的情况下快速切换对平台依赖的代码。

在本教程中，你将把一个将[strings](https://www.digitalocean.com/community/tutorials/an-introduction-to-working-with-strings-in-go)连接成文件路径的示例应用程序放在一起，创建并有选择地包括与平台有关的片段，并在你自己的系统上为多个操作系统和系统架构构建二进制文件，向你展示如何使用 Go 编程语言的这一强大能力。

## 前期准备

为了跟随本文的例子，你将需要：

- 按照[如何安装 Go 和设置本地程序环境](https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-go)设置的 Go 的 workspace

## `GOOS`和`GOARCH`可能支持的平台

在展示如何控制构建过程为不同的平台构建二进制文件之前，让我们先了解一下 Go 能够为哪些类型的平台进行构建，以及 Go 如何使用环境变量`GOOS`和`GOARCH`关联这些平台。

Go 工具有一个命令，可以打印出 Go 可以构建的平台的列表。这个列表会随着每一个新的 Go 版本而改变，所以这里讨论的组合在另一个版本的 Go 中可能不一样。当下写这个教程的时候，Go release 版本是 [`1.13`](https://golang.org/doc/go1.13).

为了找到适用的平台，执行如下命令：

```bash
go tool dist list
```

你将会收到如下相似的输出：

```text
Outputaix/ppc64        freebsd/amd64   linux/mipsle   openbsd/386
android/386      freebsd/arm     linux/ppc64    openbsd/amd64
android/amd64    illumos/amd64   linux/ppc64le  openbsd/arm
android/arm      js/wasm         linux/s390x    openbsd/arm64
android/arm64    linux/386       nacl/386       plan9/386
darwin/386       linux/amd64     nacl/amd64p32  plan9/amd64
darwin/amd64     linux/arm       nacl/arm       plan9/arm
darwin/arm       linux/arm64     netbsd/386     solaris/amd64
darwin/arm64     linux/mips      netbsd/amd64   windows/386
dragonfly/amd64  linux/mips64    netbsd/arm     windows/amd64
freebsd/386      linux/mips64le  netbsd/arm64   windows/arm
```

输出是一些以`/`分割的键值对。键值对的第一个部分，在`/`之前的是操作系统。在 Go 里面，这些操作系统会是环境变量`GOOS`的值，发音像“goose”，代表**Go Operation System**。第二部分，在`/`之后的，是架构。如前所述，这些都是环境变量`GOARCH`可能的值。这个发音"gore-ch"，代表**Go Architecture**。

让我们以`linux/386`为例，对其中的一个组合进行分解，了解它的含义和工作原理。键值对以`GOOS`开始，在这个例子中是`linux`，指的是[Linux 操作系统](https://www.digitalocean.com/community/tutorial_series/getting-started-with-linux)。这里的`GOARCH`应该是`386`，它代表[英特尔 80386 微处理器](https://en.wikipedia.org/wiki/Intel_80386)。

有许多平台可以使用`go build`命令，但大多数情况下，你最终会使用`linux` , `windows`或`darwin`作为 GOOS 的值。这些涵盖了三大操作系统平台： [Linux](https://en.wikipedia.org/wiki/Linux)、[Windows](https://en.wikipedia.org/wiki/Microsoft_Windows)和[macOS](https://en.wikipedia.org/wiki/MacOS)，后者是基于[Darwin Operating system](https://en.wikipedia.org/wiki/Darwin_(operating_system))的，因此被称为`darwin`。然而，Go 也可以覆盖不太主流的平台，如`nacl`，它代表了[谷歌的本地客户端](https://developer.chrome.com/native-client)。

当你运行`go build`这样的命令时，Go 使用当前平台的`GOOS`和`GOARCH`来决定如何构建二进制文件。要想知道你的平台是什么组合，你可以使用`go env`命令，并将`GOOS`和`GOARCH`作为参数：

```bash
go env GOOS GOARCH
```

在测试这个例子时，我们在一台[AMD64 架构](https://en.wikipedia.org/wiki/X86-64)的机器上的 macOS 上运行这个命令，所以我们将收到以下输出：

```text
Output
darwin
amd64
```

这个命令的输出告诉我们系统的 GOOS 是 darwin，GOARCH 是 amd64。

你现在知道了 Go 中的`GOOS`和`GOARCH`是什么，以及它们的可能值。接下来，你将编写一个程序，作为如何使用这些环境变量和构建标签为其他平台构建二进制文件的例子。

## 用`filepath.Join`编写一个平台依赖的应用程序

在你开始构建其他平台的二进制前，让我们先构建一个范例程序。出于这个目的，一个好的例子可以用 Go 标准库中的[`path/filepath`](https://godoc.org/path/filepath)包内的`Join`函数。这个函数以多个 string 为传参，并返回一个用正确文件路径分隔符拼接的 string。

这是一个很好的范例程序，因为该程序的运行取决于它在哪个操作系统上运行。在 Windows 上，路径分隔符是反斜杠，`\`，而基于 Unix 的系统使用正斜杠，`/`。

让我们从构建一个使用`filepath.Join()`的应用程序开始，稍后，你将编写你自己的`Join()`函数的实现，将代码定制为特定平台的二进制文件。

首先，在你的`src`目录下创建一个文件夹，用你的应用程序的名字命名：

```bash
mkdir app
```

进入目录：

```bash
cd app
```

接下来，在你选择的文本编辑器中创建一个名为`main.go`的新文件。在本教程中，我们将使用 Nano。

```bash
nano main.go
```

文件打开后，添加如下代码：

```go
package main

import (
  "fmt"
  "path/filepath"
)

func main() {
  s := filepath.Join("a", "b", "c")
  fmt.Println(s)
}
```

在这个文件的`main()`函数用`filepath.Join()`将三个[strings](https://www.digitalocean.com/community/tutorials/an-introduction-to-working-with-strings-in-go)用正确的，平台依赖的路径分隔符连接起来。

保存并退出文件，然后运行程序：

```bash
go run main.go
```

当运行这个程序时，你将收到不同的输出，这取决于你所使用的平台。在 Windows 上，你会看到用`\`分隔的字符串。

```text
Outputa\b\c
```

在 MacOS 和 Linux 等 Unix 系统上，你将收到以下内容。

```text
Output
a/b/c
```

这表明，由于这些操作系统使用的文件系统协议不同，程序将不得不为不同的平台构建不同的代码。但由于它已经根据操作系统使用了不同的文件分隔符，所有我们知道`filepath.Join()`已经考虑了平台的差异。这是因为 Go 工具链会自动检测你的机器的`GOOS`和`GOARCH`，并使用这些信息来使用具有正确[构建标签](https://www.digitalocean.com/community/tutorials/customizing-go-binaries-with-build-tags)和文件分隔符的代码片段。

让我们思考一下`filepath.Join()`函数的分隔符是从哪里来的。运行以下命令来查看 Go 标准库中的相关片段：

```bash
less /usr/local/go/src/os/path_unix.go
```

这将显示`path_unix.go`的内容。寻找该文件的如下部分：

```go
. . .
// +build aix darwin dragonfly freebsd js,wasm linux nacl netbsd openbsd solaris

package os

const (
  PathSeparator     = '/' // OS-specific path separator
  PathListSeparator = ':' // OS-specific path list separator
)
. . .
```

这一段为 Go 为支持的所有类 Unix 系统定义了 `PathSeparator`。 注意顶部的所有构建标签，它们是与 Unix 相关的每一个可能的 Unix `GOOS`平台。当`GOOS`与这些名词匹配时，你的程序将产生 Unix 风格的文件路径分隔符。

按`q`返回到命令行。

接下来，打开定义在 Windows 上使用`filepath.Join()`时的行为的文件。

```bash
less /usr/local/go/src/os/path_windows.go
```

你会看到如下内容：

```go
. . .
package os

const (
        PathSeparator     = '\\' // OS-specific path separator
        PathListSeparator = ';'  // OS-specific path list separator
)
. . .
```

虽然`PathSeparator`的值在这里是`\\`，但代码将呈现 Windows 文件路径所需的单一反斜杠（`\`），因为第一个反斜杠只需要作为转义字符。

请注意，与 Unix 文件不同，它的顶部没有构建标签。这是因为`GOOS`和`GOARCH`可以通过在文件后缀加上分隔符和环境变量的值来作为参数传递给`go build`，这个我们将会在[使用 GOOS 和 GOARCH 文件后缀名](https://www.digitalocean.com/community/tutorials/building-go-applications-for-different-operating-systems-and-architectures#using-goos-and-goarch-filename-suffixes)做更多的研究。这里，`path_windows.go`的`_windows`部分使文件的行为就像它在文件的顶部有 build 标签`//+build windows`。因为这个，但你程序在 windows 上运行时，它将使用`path_windows.go`代码片段中的`PathSeparator`和`PathListSeparator`常量。

To return to the command line, quit `less` by pressing `q`.

要返回到命令行，按`q`键退出`less`。

在这一步，你建立了一个程序，展示了 Go 如何将`GOOS`和`GOARCH`自动转换为构建标签。考虑到这一点，你现在可以更新你的程序，编写你自己的`filepath.Join()`的实现，使用构建标签为 Windows 和 Unix 平台手动设置正确的`PathSeparator`。

## 实现一个平台特定函数

现在你已经知道 Go 的标准库是如何实现特定平台的代码的，你可以使用构建标签在你自己的`应用`程序中做到这一点。要做到这一点，你将编写你自己的`filepath.Join()`的实现。

打开你的`main.go`文件：

```bash
nano main.go
```

用你自己的函数`Join()`替换`main.go`的内容，如下：

```go
package main

import (
  "fmt"
  "strings"
)

func Join(parts ...string) string {
  return strings.Join(parts, PathSeparator)
}

func main() {
  s := Join("a", "b", "c")
  fmt.Println(s)
}
```

Join 函数接收若干`parts`，并使用[strings 包](https://www.digitalocean.com/community/tutorials/an-introduction-to-the-strings-package-in-go)中的[`strings.Join()`](https://godoc.org/strings.Join)方法将它们连接起来，使用`PathSeparator`将各部分连接起来。

你还没有定义`PathSeparator`，所以现在在另一个文件中做。保存并退出`main.go`，打开你喜欢的编辑器，创建一个名为`path.go`的新文件。

```text
nano path.go
```

定义`PathSeparator`，并将其设置为 Unix 文件路径分隔符，`/`。

```go
package main

const PathSeparator = "/"
```

编译并运行该应用程序：

```bash
go build
./app
```

你将会收到如下输出：

```text
Outputa/b/c
```

这样运行成功，得到一个 Unix 风格的文件路径。但这还不是我们想要的：无论在什么平台上运行，输出总是 a/b/c。为了添加创建 Windows 风格文件路径的功能，你需要添加一个 Windows 版本的`PathSeparator`，并告诉`go build`命令使用哪个版本。在下一节中，你将使用[构建标签](https://www.digitalocean.com/community/tutorials/customizing-go-binaries-with-build-tags)来完成这个任务。

## 使用`GOOS`或`GOARCH`构建标签

为了考虑到 Windows 平台，你现在将创建一个替代文件到`path.go`，并使用构建标签来确保代码片段只在`GOOS`和`GOARCH`是合适的平台时运行。

但首先，在`path.go`中添加一个构建标签，告诉它除 Windows 之外的所有东西都可以进行构建。打开该文件：

```bash
nano path.go
```

加入如下高亮构建标签到文件：

```go
// +build !windows

package main

const PathSeparator = "/"
```

Go 构建标签允许反转，也就是说，你可以指示 Go 为除 Windows 之外的任何平台构建此文件。 要反转一个构建标签，请在标签前加上一个`!`。

保存并退出文件。

现在，如果你要在 Windows 上运行这个程序，你会得到以下错误：

```text
Output./main.go:9:29: undefined: PathSeparator
```

在这种情况下，Go 将无法通过引入`path.go`来定义变量`PathSeparator`。
 
现在你已经确保当`GOOS`是 Windows 时，`path.go`不会运行，添加一个新的文件，`windows.go`：

```bash
nano windows.go
```

在`windows.go`中，定义 Windows 的`PathSeparator`，以及一个构建标签让`go build`命令知道它是 Windows 的实现：

```go
// +build windows

package main

const PathSeparator = "\\"
```

保存文件并从文本编辑器中退出。该应用程序现在可以以一种方式为 Windows 编译，另一种方式为所有其他平台编译。

虽然现在二进制文件可以在其他平台正确编译，但你必须做进一步的修改，以便为你无法访问的平台进行编译。要做到这一点，你将在下一步改变你的本地`GOOS`和`GOARCH`环境变量。

## 使用你本地`GOOS`和`GOARCH`环境变量

在前面，你通过执行`go env GOOS GOACH`指令来找到你正在工作的平台是哪个操作系统和架构。当你执行`go env`指令时，它会去找 2 个环境变量`GOOS`和`GOARCH`；如果找到，他们就会使用环境变量，如果找不到，Go 就会用当前平台的信息来设置它们。这意味着你可以改变`GOOS`或`GOARCH`，所以它们不是根据你的操作系统和架构默认设置的。

`go build`命令的行为方式与`go env`命令类似。你可以设置`GOOS`或`GOARCH`的环境变量，用`go build`为不同的平台进行编译。

如果你没有使用`Windows`系统，可以在运行`go build`命令时将`GOOS`环境变量设置为`window`s，从而构建`应用程序`windows 下的二进制版本：

```bash
GOOS=windows go build
```

现在列出你当前目录中的文件：

```bash
ls
```

列出目录文件的输出项显示在项目目录中现在有一个`app.exe`的 Windows 可执行文件：

```text
Outputapp  app.exe  main.go  path.go  windows.go
```

使用`file`命令，你可以得到关于这个文件的更多信息，确认它的构建构建信息：

```bash
file app.exe
```

你将会看到如下信息：

```text
Outputapp.exe: PE32+ executable (console) x86-64 (stripped to external PDB), for MS Windows
```

你也可以在构建时设置一个，或两个环境变量。运行如下命令：

```bash
GOOS=linux GOARCH=ppc64 go build
```

你的`应用程序`的可执行文件现在将被一个不同架构的文件所取代。在这个二进制文件上运行`file`命令：

```bash
file app
```

你将会收到类似如下的信息：

```text
app: ELF 64-bit MSB executable, 64-bit PowerPC or cisco 7500, version 1 (SYSV), statically linked, not stripped
```

通过设置本地的 `GOOS` 和 `GOARCH` 环境变量，你将可以为任何兼容 Go 的平台构建二进制文件，而无需复杂的配置或设置。接下来，你将使用文件名的约定来保持你的文件整齐，并自动为特定平台构建，而不需要构建标签。

## 使用`GOOS`和`GOARCH`文件名后缀

正如你之前看到的，Go 标准库大量使用构建标签，通过将不同的平台实现分离到不同的文件中来简化代码。当你打开`os/path_unix.go`文件时，有一个构建标签，列出了所有被认为是类 Unix 平台的可能组合。然而，`os/path_windows.go`文件不包含任何构建标签，因为文件名的后缀就足以告诉 Go 该文件是为哪个平台准备的。

让我们来看看这个功能的语法。命名`.go`文件时，可以在文件名中按顺序添加`GOOS`和`GOARCH`作为后缀，用下划线(`_`)来分开这两个值。如果你有一个名为`filename.go`的 Go 文件，你可以通过将文件名改为 `filename_GOOS_GOARCH.go`来指定操作系统和架构。举个例子，如果你希望将其编译为 64 位[ARM 架构](https://en.wikipedia.org/wiki/ARM_architecture)的 Windows，你会将文件名定为`filename_windows_arm64.go`。这种命名方式有助于保持代码的整齐性。

使用文件名后缀而非构建标签来更新你的程序。首先，重新命名`path.go`和`windows.go`文件，使用`os`包中使用的惯例：

```bash
mv path.go path_unix.go
mv windows.go path_windows.go
```

改变了这两个文件名后，你可以删除你添加到`path_windows.go`的构建标签：

```bash
nano path_windows.go
```

移除`// +build windows`，所以你的文件会看起来像这样：

```go
package main

const PathSeparator = "\\"
```

保存并退出文件。

因为`unix`不是一个有效的`GOOS`，`_unix.go`后缀对 Go 编译器没有任何意义。然而，它确实传达了文件的预期目的。 和`os/path_unix.go`文件一样，你的`path_unix.go`文件仍然需要使用构建标签，所以保持该文件不变。

通过使用文件名惯例，你从你的源代码中删除了不需要的构建标签，并使文件系统更干净、更清晰。

## 总结

为多个平台生成不需要依赖的二进制文件的能力是 Go 工具链的一个强大功能。在本教程中，你通过添加构建标签和文件名后缀来使用这种能力，以标记某些代码片段，使其只针对某些架构进行编译。你创建了你自己的平台依赖的程序，然后操纵`GOOS`和`GOARCH`环境变量，为你当前平台以外的平台生成二进制文件。这是一项有价值的技能，因为有一个持续集成过程，自动运行这些环境变量，为所有平台构建二进制文件，这是一个常见的做法。
