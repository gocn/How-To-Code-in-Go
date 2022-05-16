# 理解 GOPATH

### 介绍

本文将带领你了解什么是 `GOPATH`，它是如何工作的，以及如何设置它。这是设置 Go 开发环境以及理解 Go 如何查找、安装和构建源文件的关键步骤。在本文中，我们将使用 `GOPATH` 来指代我们将要讨论的文件夹结构的概念。我们将使用 `$GOPATH` 来指代 Go 用来查找文件夹结构的环境变量。

[Go 工作区](https://golang.org/doc/code.html#Workspaces) 是 Go 管理源码文件、编译的二进制文件和用于后续更快编译的缓存对象。虽然可能有多个空间，但只有一个 Go 工作区是典型的，也是被建议的使用方式。`GOPATH` 充当工作区的根文件夹。

## 设置 `$GOPATH` 环境变量

`$GOPATH` 环境变量列出了 Go 用来寻找 Go 工作区的地方。

默认情况下，Go 假设 `GOPATH` 位于 `$HOME/go`，其中 `$HOME` 是电脑上上我们帐户的根目录。我们可以通过设置 `$GOPATH` 环境变量来修改它。为了进一步的研究，请参考[在 Linux 中阅读和设置环境变量](https://www.digitalocean.com/community/tutorials/how-to-read-and-set-environmental-and-shell-variables-on-a-linux-vps)的教程。

想要了解更多关于设置 `$GOPATH` 变量的信息，可以参考 Go [文档](https://golang.org/doc/code.html#Workspaces)。

此外，[本系列教程](https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-go) 简单介绍了安装 Go 和设置 Go 开发环境的方法。

## `$GOPATH` 不是 `$GOROOT`

`$GOROOT` 是 Go 的代码、编译器和工具所在的地方ーー这**不是**我们的源代码。`$GOROOT` 通常类似于 `/usr/local/go`。我们的 `$GOPATH` 通常类似于 `$HOME/go`。

虽然我们不再需要专门设置 `$GOROOT` 变量，但它仍然在旧材料中被引用。

现在，让我们讨论一下 Go Workspace 的结构。

## Go 工作区剖析

在一个 Go Workspace 或者 `GOPATH` 中，有三个目录: `bin`、 `pkg` 和 `src`。这些目录中的每一个对于 Go 工具链都有特殊的意义。

```
.
├── bin
├── pkg
└── src
  └── github.com/foo/bar
    └── bar.go
```

让我们来看看每个目录。

`$GOPATH/bin` 目录是 Go 放置 `go install` 编译的二进制文件的地方。我们的操作系统使用 `$PATH` 环境变量在没有完整路径的情况下找到二进制应用程序并执行。建议将此目录添加到全局 `$PATH` 变量中。

例如，如果我们不在 `$PATH` 中添加 `$GOPATH/bin` 来执行一个程序，我们需要运行:

```
$ $GOPATH/bin/myapp
```

当 `$GOPATH/bin` 被添加到 `$PATH` 时，我们可以像这样进行同样的调用:

```
$ myapp
```

`$GOPATH/pkg` 目录是 Go 存储预编译目标文件的地方，以加速程序的后续编译。通常，大多数开发人员不需要访问这个目录。如果遇到编译问题，可以安全地删除该目录，然后 Go 将重新生成该目录。

在 `src` 目录是我们放置所有的 `.go` 文件，或源代码的地方。这不应与 Go 工具使用的源代码混淆，后者位于 `$GOROOT` 中。在编写 Go 应用程序、包和库时，我们将把这些文件放在 `$GOPATH/src/path/to/code` 下。

## 包是什么？

Go 代码是以包的形式组织的。包表示磁盘上单个目录中的所有文件。一个目录只能包含来自同一包的某些文件。包与所有用户编写的 Go 源文件一起存储在 `$GOPATH/src` 目录下。我们可以通过导入不同的软件包来理解软件包解析。

如果我们的代码是 `$GOPATH/src/blue/red`，那么它的包名应该是 `red`。

```
import "blue/red"
```

`red` 包的导入声明如下:

存储在源代码仓库中的软件包，如 GitHub 和 BitBucket，将仓库的完整位置作为导入路径的一部分。

例如，我们可以使用下面的导入路径来导入 [https://github.com/gobuffalo/buffalo](https://github.com/gobuffalo/buffalo) 的源代码:

```
import "github.com/gobuffalo/buffalo"
```

因此，这个源代码应该位于磁盘上的下列位置:

```
$GOPATH/src/github.com/gobuffalo/buffalo
```

## 结论

在这篇文章中，我们讨论了 `GOPATH` 作为一个文件夹的集合，Go 期望我们的源代码保存在里面，以及这些文件夹是什么，它们包含什么。我们讨论了如何通过设置 `$GOPATH` 环境变量，将默认的 `$HOME/go` 位置改为用户选择的位置。最后，我们讨论了 Go 如何在该文件夹结构中搜索包。

在 Go 1.11中引入的 [Go Modules](https://github.com/golang/Go/wiki/Modules)旨在取代 Go Workspaces 和 `GOPATH`。虽然建议开始使用模块，但是有些环境(如公司环境)，可能还没有准备好使用模块。

`GOPATH` 是 Go 设置中比较棘手的一个方面，但是一旦设置好了，我们通常会忘记它。
