# 用 ldflags 设置 Go 应用程序的版本信息

## 简介

当把应用程序部署到生产环境中时，用版本信息和其他元数据构建二进制文件将改善你的监控、日志和调试过程，增加识别信息来帮助跟踪随着时间推移后，应用程序的构建信息。这种版本信息通常包括高度动态的数据，如构建时间、构建二进制文件的机器或用户、[版本控制系统（VCS）](https://www.atlassian.com/git/tutorials/what-is-version-control)的提交 ID，等其他更多信息。因为这些值是不断变化的，将这些数据直接编码到源代码中，并在每次新的构建之前进行修改，是很繁琐的，而且容易出错：源文件可能会移动，[变量/常量]({{< relref "/docs/11-How_To_Use_Variables_and_Constants_in_Go.md" >}})在整个开发过程中可能会随着切换文件而改动，打断构建过程。

在 Go 中解决这个问题的一个方法是在使用`go build`命令时加上`-ldflags`，在构建时将动态信息插入二进制文件中，而不需要修改源代码。在这个标志中，`ld`代表[*linker*](https://en.wikipedia.org/wiki/Linker_(computing))，这个程序将编译后的源代码的不同部分连接成最终的二进制文件。`ldflags`就代表*linker 的标志*。之所以这样说，是因为它向底层的 Go 工具链 linker[`cmd/link`](https://golang.org/cmd/link)传递了一个标志，允许你在构建时从命令行中改变导入的包的值。

在本教程中，你将使用`-ldflags`在构建时改变变量的值，并将你自己的动态信息加入二进制，用一个将版本信息打印到屏幕上的应用程序作为示例应用程序。

## 前期准备

为了接下去在文章中的例子，你需要：

- 按照[如何安装 Go 和设置本地编程环境]({{< relref "/docs/01-How_To_Install_Go_and_Set_Up_a_Local Programming_Environment_on_Ubuntu_18.04_DigitalOcean.md" >}})设置 Go 的 workspace。

## 构建你的范例应用程序

在使用`ldflags`加入动态数据之前，你首先需要一个应用程序来插入信息。在这一步，你将制作这个应用程序，在这个阶段，它将只打印静态的版本信息。现在让我们来创建这个应用程序。

在你的`src`目录下，建立一个以你的应用程序命名的目录。本教程将使用叫`app`的应用程序：

```bash
mkdir app
```

跳转你的目录到这个文件夹：

```bash
cd app
```

然后，使用你喜欢的文本编辑器，在`main.go`创建你的程序的 entry point：

```bash
nano main.go
```

现在，通过加入如下内容到你的程序内，来打印出版本信息：

```go
package main

import (
	"fmt"
)

var Version = "development"

func main() {
	fmt.Println("Version:\t", Version)
}
```

在`main()`函数内，你宣告了`Version`变量，然后打印[string]({{< relref "/docs/08-An_Introduction_to_Working_with_Strings_in_Go.md" >}})类型的`Version`：紧跟着 tab 的字符，`\t`，然后是声明的变量。

现在，参数`Version`被定义为`development`，将作为 app 的默认版本。稍后，你将会修改这个值来符合官方版本编号，根据[semantic versioning format](https://semver.org/)来定义。

保存并退出该文件。完成后，构建并运行该应用程序，来确认它打印的是正确的版本：

```bash
go build
./app
```

你将会看到如下输出：

```bash
Output
Version:	 development
```

你现在有一个打印默认版本信息的应用程序，但你还没有办法在构建时传入当前版本信息。在下一步，你将使用`-ldflags`和`go build`来解决这个问题。

## 在 `go build`中使用`ldflags`的方法

在前面提到的，`ldflags`代表*linker 标志*，用于向 Go 工具链中的底层 linker 传递标志。这是按以下语法进行的：

```bash
go build -ldflags="-flag"
```

在这个例子中，我们向作为`go build`的一部分运行的`go tool link`命令传递了`flag`。这个命令在传递给`ldflags`的内容周围使用双引号，以避免其中字符串被分开，或者被命令行翻译为与我们想要的不同的字符。从这里，你可以传入[许多不同的`linker`标志](https://golang.org/cmd/link/)。为了本教程中的目的，我们将使用`-X`标志在链接时将信息写入变量，跟着的是参数的[package]({{< relref "/docs/20-Importing_Packages_in_Go_DigitalOcean.md" >}})路径和它的新值：

```bash
go build -ldflags="-X 'package_path.variable_name=new_value'"
```

在引号内，现在有`X`选项和一个[键值对](https://gocn.github.io/How-To-Code-in-Go/docs/15-Understanding_Maps_in_Go/#%E9%94%AE%E5%92%8C%E5%80%BC)，代表要改变的变量和它的新值。`.`字符将包路径和变量名称分开，单引号用于避免键值对被断开。

要在你的示例程序中替换`Version`变量，使用最后一个命令块中的语法，传入一个新的值并建立新的二进制。

```bash
go build -ldflags="-X 'main.Version=v1.0.0'"
```

在这个命令中，`main`是`Version`变量的包路径，因为这个变量在`main.go`文件中。`Version`是你要写入的变量，`v1.0.0`是新的值。

为了使用`ldflags`，你想改变的值必须存在，并且是一个`string`类型的包级变量。这个变量可以是对外导出的也可以不是。变量的值不可以是`const`或者是需要通过调用函数后得到的结果赋值的。幸运的是，`Version`满足了所有的要求：它已经在`main.go`文件中被声明为一个变量，而且当前值(`development`)和期望值(`v1.0.0`)都是字符串。

一旦你的新`app`二进制文件构建起来，运行应用程序：

```bash
./app
```

你将会收到如下输出：

```bash
Output
Version:	 v1.0.0
```

通过`-ldflags`，你成功地把`Version`变量的值从`development`改成`v1.0.0`。

现在你已经在一个简单的应用程序构建时修改了一个`string`变量。使用`ldflags`，你可以在二进制文件中嵌入版本细节、许可信息等，只需使用命令行就可以发布。

在这个例子中，你改变的变量在`main`程序中，减少了确定路径名称的难度。但有时这些变量的路径寻找起来比较复杂。在下一步中，你将给子包中的变量赋值，来阐述确定更复杂的包路径的最佳方法。

## 锁定子包变量

在上一节中，你操作了`Version`变量，它位于应用程序的顶层包。但这不是常见的案例。通常情况下，将这些变量放在另一个包中更为实际，因为`main`不是一个可导入的包。为了在你的示例程序中模拟这一点，你将创建一个新的子包，`app/build`，它将存储关于二进制文件被构建的时间和发出构建命令的用户名称的信息。

要添加一个新的子包，首先在你的项目中添加一个名为`build'的新目录：

```bash
mkdir -p build
```

然后创建一个名为`build.go`的新文件来保存新的变量：

```bash
nano build/build.go
```

在你的文本编辑器中，添加`Time`和`User`这两个新变量

```go
package build

var Time string

var User string
```

`Time`变量将保存二进制文件建立的时间的字符串表示。`User`变量将保存构建二进制文件的用户名称。由于这两个变量总是有值，你不需要像对`Version`那样用默认值初始化这些变量。

保存并退出文件。

然后，打开`main.go`文件添加这些变量到你的应用程序中：

```bash
nano main.go
```

在`main.go`中，添加如下高亮代码：

```go
package main

import (
	"app/build"
	"fmt"
)

var Version = "development"

func main() {
	fmt.Println("Version:\t", Version)
	fmt.Println("build.Time:\t", build.Time)
	fmt.Println("build.User:\t", build.User)
}
```

在这些代码里，你第一次引用`app/build`包，然后用打印`Version`的方式打印`build.Time`和`build.User`。

保存文件，然后从你的文本编辑器退出。

接下来，为了用`ldflags`锁定这些变量，你可以使用导入路径`app/build`，然后是`.User`或`.Time`，因为你已经知道导入的路径。 然而，为了模拟一种更复杂的情况，即不知道变量的导入路径，让我们改用 Go 工具链中的`nm`命令。

`go tool nm`命令将输出在给定的可执行文件、对象文件或存档中涉及的符号。在这种情况下，符号指的是代码中的一个对象，例如一个定义的或导入的变量或函数。通过使用`nm`生成一个符号表，并使用`grep`搜索一个变量，你可以快速找到其路径信息。

注意：如果软件包名称中有任何非[ASCII](https://en.wikipedia.org/wiki/ASCII)字符，或者有`"`或`%`字符，`nm`命令将不能帮助你找到变量的路径，因为这是工具本身的限制。

要使用这个命令，首先要为`app`构建二进制文件：

```bash
go build
```

现在`app`已经构建好了，将`nm`工具指向它，并在输出中搜索：

```bash
go tool nm ./app | grep app
```

当运行时，`nm`工具将输出大量的数据。因为如此，前面的命令使用`|`将输出的数据输送给`grep`命令，然后搜索标题中带有一级`app`的数据。

你将会收到类似如下的输出：

```text
Output  
  55d2c0 D app/build.Time
  55d2d0 D app/build.User
  4069a0 T runtime.appendIntStr
  462580 T strconv.appendEscapedRune
. . .
```

在这种情况下，结果集的前两行包含你要找的两个变量的路径。`app/build.Time`和`app/build.User`。

现在你知道了路径，再次构建应用程序，这次在构建时改变`版本`、`用户`和`时间`。要做到这一点，需要向`-ldflags`传递多个`-X`标志：

```bash
go build -v -ldflags="-X 'main.Version=v1.0.0' -X 'app/build.User=$(id -u -n)' -X 'app/build.Time=$(date)'"
```

这里你传入了`id -u -n` Bash 命令来列出当前用户，以及`date`命令来列出当前日期。

构建好了可执行文件，运行该程序：

```bash
./app
```

该命令在 Unix 系统上运行时，将产生与下面类似的输出：

```text
Output
Version:	 v1.0.0
build.Time:	 Fri Oct  4 19:49:19 UTC 2019
build.User:	 sammy
```

现在你有一个包含版本和构建信息的二进制文件，在生产中解决问题时可以提供重要帮助。

## 总结

这个教程展示了，如果应用得当，`ldflags`可以成为一个强大的工具，在构建时向二进制文件注入有价值的信息。这样，你可以控制功能标志、环境信息、版本信息等等，而不需要对你的源代码进行修改。通过添加`ldflags`到你当前的构建工作流程中，你可以最大限度地发挥 Go 自成一体的二进制的发布格式的优势。
