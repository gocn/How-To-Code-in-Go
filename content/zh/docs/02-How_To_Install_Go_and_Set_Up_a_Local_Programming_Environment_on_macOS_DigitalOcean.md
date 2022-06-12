# 如何在 macOS 上安装 Go 和设置本地编程环境

## Go 语言简介

[Go](https://golang.org/) 是一门在 Google 备受挫折后而诞生的语言。开发者不得不频繁地在两门语言中选择，要么选择一门执行效率高但是编译时间长的语言，要么选择一种易于编程但在生产中运行效率低下的语言。 Go 被设计为同时提供所有这三个功能：快速编译、易于编程和生产中的高效执行。

虽然 Go 是一门通用的编程语言，可用于许多不同类型的编程项目。但它特别适合网络/分布式系统项目，赢得了“云语言”的美誉。Go  语言专注于通过一组强大的工具来帮助现代程序员完成更多的工作，通过使格式成为语言规范的一部分来消除对格式的争论，以及通过编译为单个二进制文件来简化部署。 Go 易于学习，关键字非常少，这使其成为不论是初学者还是经验丰富的开发人员的不二之选。

本教程将指导你通过命令行来安装 Go 和配置 Go 编程环境。本教程特别针对 Ubuntu 18.04 的安装过程，但是对于其他 Debian Linux 发行版也同样适用。

## 安装前提

你需要一台拥有管理员权限并且能够连接上网的 macOS 的电脑。

## 第一步 — 打开终端

我们将会在终端上完成我们大部分的安装和设置，这是一种与计算机交互的非图形化方式。也就是说，你输入的是文本，然后也是通过文本得到计算机的反馈，而不是点击按钮。命令行，也就是我们熟知的 _shell_ 或者 _终端_ ，可以帮助你修改或自动化很多你每天执行在计算机上的任务，这是软件开发人员必备的工具。

macOS 终端是一个可以访问命令行界面的应用程序。就像其他应用程序那样，你可以通过进入 Finder ，导航到 Applications 文件夹，最后进入 Utilities 文件夹找到它。然后就像打开其他应用程序一样双击终端将其打开。或者，你可以通过按住`CMD` 和 `SPACE` 键来启动 Spotlight，然后在出现的框中输入终端来查找它。

![macOS Terminal](https://assets.digitalocean.com/articles/eng_python/OSXSetUp/MacOSXSetUp.png)

尽管有很多终端命令需要学习，但是这些命令可以让你做更强大的事情。 文章[Linux 终端简介](https://www.digitalocean.com/community/tutorials/an-introduction-to-the-linux-terminal) 可以让你更好地了解 Linux 终端，它和 macOS 终端类似。

现在你已经打开了终端，你可以下载并安装 [Xcode](https://developer.apple.com/xcode/)，这是安装 Go 语言所需的开发者工具包。

## 第二步 — 安装 Xcode

Xcode 是一个`集成开发环境`(IDE)，由 macOS 软件开发工具组成。你可以在终端窗口中输入以下命令来检查 Xcode 是否已安装。

```shell
xcode-select -p
```

下面的输出表明 Xcode 已经安装了：

```text
output
/Library/Developer/CommandLineTools
```

如果收到错误信息，请在你的网络浏览器中安装[来自 App Store 的 Xcode]([https://itunes.apple.com/us/app/xcode/id497799835?mt=12&ign-mpt=uo%3D2](https://itunes.apple.com/us/app/xcode/id497799835?mt=12&ign-mpt=uo%3D2))  并接受默认选项。

 安装完 Xcode 后，返回终端窗口。接下来，你需要安装 Xcode 的单独命令行工具应用，可以通过输入以下命令：

```shell
xcode-select --install
```

至此，Xcode 及其命令行工具应用已安装完毕，我们准备安装包管理器 Homebrew。

## 第三步 — 安装和设置 Homebrew

尽管 macOS 终端具有 Linux 终端和其他 Unix 系统的许多功能，但它没有提供包管理器的最佳实践。 **包管理器**是一组软件工具集，用于安装过程的自动化，包括初始软件的安装、升级和配置，以及根据需要删除软件。 他们将安装包保存在一个中心位置，并且可以以常用格式维护系统上的所有软件包。 [**Homebrew**](https://brew.sh/) 为 macOS 提供了一个免费的开源软件包管理系统，简化了 macOS 上的软件安装。

要安装 Homebrew ，请在终端窗口输入以下命令：

```shell
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Homebrew 是用 Ruby 开发的，因此它会修改你计算机的 Ruby 路径。`curl` 命令从指定的 URL 中提取脚本。该脚本解释了它将要做什么，然后会暂停下来提示你确认。这给了你大量反馈，关于该脚本对你的系统将要做什么并让你有机会验证该过程。

如果你需要输入密码，请注意你的输入内容不会显示在终端窗口中，但会被记录下来。 输入密码后，只需按 `返回` 键即可。 否则，当系统提示你确认安装时，按字母 `y` 表示“是”。

让我们来看看与  `curl` 命令相关的一些标志：

-   The `-f` or `--fail` flag tells the Terminal window to give no HTML document output on server errors.
-   `-f` 或者 `--fail` 标志告诉终端窗口在服务器错误时不提供 HTML 文档输出。
-   The `-s` or `--silent` flag mutes `curl` so that it does not show the progress meter, and combined with the `-S` or `--show-error` flag it will ensure that `curl` shows an error message if it fails.
-    `-s` 或者 `--silent` 标志给`curl`静音使其不显示进度表，并且结合 `-S`  或者 `--show-error` 标志来确保 `curl` 在失败时显示错误消息。
-   The `-L` or `--location` flag will tell `curl` to redo the request to a new place if the server reports that the requested page has moved to a different location.
-   `-L`  或者  `--location` 标志告诉  `curl` 如果服务器报告请求的页面已移动到不同的位置，请在新位置重试请求。

安装过程完成后，我们将把 Homebrew 目录放在 `PATH` 环境变量的顶部。 这将确保 Homebrew 安装命令将会被 macOS 自动选择的工具调用，这些工具可能与我们正在创建的开发环境背道而驰。

你应该使用命令行文本编辑器  **nano** 使用  `nano`  命令创建或者打开 `~/.bash_profile` 文件：

```shell
nano ~/.bash_profile
```

在终端窗口中打开文件后，输入以下内容：

```shell
export PATH=/usr/local/bin:$PATH
```

要保存更改，请按住 `CTRL` 键和字母 `o` ，并在出现提示时按 `RETURN` 键。 现在你可以通过按住 `CTRL` 键和字母 `x` 来退出 nano。

在终端中执行以下命令来激活这些更改：

```shell
source ~/.bash_profile
```

完成此操作后，你对 `PATH` 环境变量所做的更改将生效。

可以通过输入以下命令来确保 Homebrew 已成功安装：

```shell
brew doctor
```

如果此时不需要更新，终端将输出以下内容：

```text
output
Your system is ready to brew.
```

否则，你可能会收到提示你运行另一个命令（例如 `brew update`）的警告，以确保你的 Homebrew 的版本是最新的。

一旦 Homebrew 准备好了，就开始安装 Go 了。

## 第四步 — 安装 Go

你可以通过  `brew search`  命令来使用 Homebrew 搜索所有可用的软件包。 出于本教程的目的，你将搜索与 Go 相关的包或模块：

```shell
brew search golang
```

**注意**：本教程不使用 `brew search go`，因为它返回的结果太多。 因为 `go` 是一个很小的词，并且会匹配很多包，所以使用 `golang` 作为搜索词变得很普遍。 这也是在互联网上搜索 Go 相关文章时的常见做法。 　`Golang`  一词源于 Go 的域，即 `golang.org`。

终端会输出一个你可以安装的列表：

```text
output
golang golang-migrate
```

Go 将是列表中的一项。继续安装它：

```shell
brew install golang
```

终端窗口将为你显示有关 Go 安装过程的反馈。 安装完成可能需要几分钟时间。

要检查你安装 Go 的版本，输入以下命令：

```shell
go version
```

将会输出当前安装的 Go 语言的特定版本，默认情况下它是可用 Go 版本中的最新、最稳定的。

以后要更新 Go，你可以运行以下命令先更新 Homebrew，然后再更新 Go。 现在不必这样做，因为你刚刚安装了最新版本：

```shell
brew update
brew upgrade golang
```

`brew update` 将更新 Homebrew 本身，确保你拥有要安装的软件包的最新信息。 `brew upgrade golang` 会将 `golang` 包更新到最新版本。

保持 Go 一直是最新版本是一种很好的做法。

在你的计算机上安装 Go 后，你现在可以为你的 Go 项目创建工作区。

## 第五步 — 创建你的 Go 工作区

现在你已经安装了 Xcode、Homebrew 和 Go，可以接着创建你的编程工作区。

Go 语言的工作区在其根目录下包含两个目录：

-   `src`: 该目录包含 Go 的源文件。所谓源文件就是你用 Go 编程语言写的文件。源文件被 Go 编译器构建成可执行的二进制文件。
-   `bin`: 该目录包含了 Go 工具构建和安装的可执行文件。可执行文件就是运行在你系统上并执行任务的二进制文件。通常是你的源码或者是其他下载的 Go 源代码编译的程序。

`src` 子目录可能包含多个版本控制仓库（例如 [Git](https://git-scm.com/), [Mercurial](https://www.mercurial-scm.org/) 和 [Bazaar](http://bazaar.canonical.com/)）。当你引入第三方库的时候，你可以看到类似  `github.com`, `golang.org` 或其他目录，如果你使用的是 `github.com` 之类的代码仓库，你还将把项目和源文件放在该目录下。 我们将在此步骤的后面部分探讨这个概念。这允许你在你的项目中规范导入代码。 规范导入就是引用完全限定包的导入，例如 `github.com/digitalocean/godo` 。

下面是典型的工作区目录结构：

```shell
.
├── bin
│   ├── buffalo                                      # command executable
│   ├── dlv                                          # command executable
│   └── packr                                        # command executable
└── src
    └── github.com
        └── digitalocean
            └── godo
                ├── .git                            # Git reposistory metadata
                ├── account.go                      # package source
                ├── account_test.go                 # test source
                ├── ...
                ├── timestamp.go
                ├── timestamp_test.go
                └── util
                    ├── droplet.go
                    └── droplet_test.go
```

从 1.8 开始，Go 工作区的默认目录是用户的 home 目录，并带有 `go` 子目录，或者是 `$HOME/go` 目录。 如果你使用的是早于 1.8 的 Go 版本，目前认为最佳做法是为你的工作区使用 `$HOME/go` 位置。

使用下面命令为你的 Go 工作区创建目录结构：

```shell
mkdir -p $HOME/go/{bin,src}
```

`-p`  选项是告诉  `mkdir`  在目录中创建所有的上级目录，尽管他们可能不存在。使用 `{bin,src}` 为 `mkdir` 创建一组参数，并告诉它创建 `bin` 目录和 `src` 目录。

以上命令将确保下面的目录结构各就各位：

```shell
└── $HOME
    └── go
        ├── bin
        └── src
```

在 Go 1.8 之前，需要设置一个名为 `$GOPATH` 的本地环境变量。 `$GOPATH` 告诉编译器在哪里可以找到导入的第三方源代码，同样包括任何你写的本地源代码。 虽然不再明确要求它，但它仍然被认为是一种很好的做法，因为许多第三方工具仍然依赖于设置的这个变量。

你可以通过将全局变量添加到你的 `~/.profile` 中来设置你的 `$GOPATH`。 你可能想根据你的 shell 配置将其添加到 `.zshrc` 或 `.bashrc` 文件中。

首先，使用 `nano` 或你喜欢的文本编辑器打开 `~/.profile`：

```shell
nano ~/.bash_profile
```

通过下面的命令设置你的  `$GOPATH` ：

```shell
~/.bash\_profile
export GOPATH=$HOME/go
```

当 Go 编译和安装工具时，会将他们放在  `$GOPATH/bin`  目录。为方便起见，通常将工作区的 `/bin` 子目录添加到 `~/.profile` 中的 `PATH` 中：

```shell
~/.bash\_profile
export PATH=$PATH:$GOPATH/bin
```

现在，你的 `~/.bash_profile` 中应该有以下条目：

```shell
~/.bash\_profile
export GOPATH=$HOME/go
export PATH=$PATH:$GOPATH/bin
```

这将允许你在系统上的任何位置运行通过 Go 工具编译或下载的任何程序。

为了更新你的 shell 配置，请使用下面的命令来加载全局变量:

```shell
. ~/.bash_profile
```

您可以通过使用 `echo` 命令并检查其输出，来验证你的 `$PATH` 是否已更新：

```shell
echo $PATH
```

你将看到你的  `$GOPATH/bin` 显示在你的主目录中。如果你以  `sammy` 身份登录，你将在路径中看到 `/Users/sammy/go/bin`。

```text
output
/Users/sammy/go/bin:/usr/local/sbin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
```

现在你已经创建了工作区的根目录并设置了 `$GOPATH` 环境变量，你可以根据以下目录结构创建你未来的项目。 此示例假设你使用 `github.com` 作为仓库：

```shell
$GOPATH/src/github.com/username/project
```

例如，如果你正在开发  [`https://github.com/digitalocean/godo`](https://github.com/digitalocean/godo)  项目，它将存储在以下目录中：

```shell
$GOPATH/src/github.com/digitalocean/godo
```

该项目结构使项目可以通过 `go get` 工具使用。它也有助于以后的可读性。 你可以通过使用 `go get` 命令并获取 `godo` 库来验证这一点：

```shell
go get github.com/digitalocean/godo
```

We can see it successfully downloaded the `godo` package by listing the directory:

你可以通过列出目录来检查下看看是否成功下载了 `godo`包：

```shell
ls -l $GOPATH/src/github.com/digitalocean/godo
```

你应该看到类似下面这样的输出：

```shell
Output
-rw-r--r--  1 sammy  staff   2892 Apr  5 15:56 CHANGELOG.md
-rw-r--r--  1 sammy  staff   1851 Apr  5 15:56 CONTRIBUTING.md
.
.
.
-rw-r--r--  1 sammy  staff   4893 Apr  5 15:56 vpcs.go
-rw-r--r--  1 sammy  staff   4091 Apr  5 15:56 vpcs_test.go
```

在这一步中，你创建了一个 Go 工作区并且配置了必要的环境变量。下一步你将使用一些代码来测试下工作区。

## 第六步 — 创建一个简单的程序

现在你已经设置了工作区，来创建一个  “Hello, World!” 程序吧。这可以检验工作区配置是否正确，并且给你一个更加熟悉 Go 的机会。

因为我们创建的是单个 Go 源文件，而不是实际项目，所以我们不需要在工作区中执行此操作。

在你的 home 目录，打开一个命令行文本编辑器，例如 `nano`，然后创建一个新文件：

```shell
nano hello.go
```

在终端中打开文本文件后，输入你的程序代码：

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello, World!")
}
```

按 `CTRL` 和 `X` 键退出 `nano`。 当提示保存文件时，按 `Y`就行。

该代码使用了  `fmt` 包并且使用  `Hello, World!`  作为参数调用了  `Println` 函数。这将导致短语 `Hello, World!` 在程序运行时打印到终端上。

退出 `nano` 返回 shell 后，运行程序：

```shell
go run hello.go
```

`hello.go` 程序会使终端产生以下输出：

```shell
Output
Hello, World!
```

在此步骤中，你使用了一个简单小程序来验证是否正确配置了 Go 工作区。

## 总结

恭喜！至此，你已经在 macOS 机器上设置了 Go 编程工作区，可以开始写项目了！