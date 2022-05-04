# 如何在 Ubuntu 18.04 上安装 Go 和设置本地编程环境

## Go 语言简介

[Go](https://golang.org/) 是一门在 Google 备受挫折后而诞生的语言。开发者不得不频繁地在两门语言中选择，要么选择一门执行效率高但是编译时间长的语言，要么选择一种易于编程但在生产中运行效率低下的语言。 Go 被设计为同时提供所有这三个功能：快速编译、易于编程和生产中的高效执行。

虽然 Go 是一门通用的编程语言，可用于许多不同类型的编程项目。但它特别适合网络/分布式系统项目，赢得了“云语言”的美誉。Go  语言专注于通过一组强大的工具来帮助现代程序员完成更多的工作，通过使格式成为语言规范的一部分来消除对格式的争论，以及通过编译为单个二进制文件来简化部署。 Go 易于学习，关键字非常少，这使其成为不论是初学者还是经验丰富的开发人员的不二之选。

本教程将指导你通过命令行来安装 Go 和配置 Go 编程环境。本教程特别针对 Ubuntu 18.04 的安装过程，但是对于其他 Debian Linux 发行版也同样适用。

## 安装前提

你需要一台安装了  Ubuntu 18.04 的电脑或者虚拟机，并且有对该计算机的管理员访问权限和网络连接。 您可以通过 [Ubuntu 18.04 版本页面](http://releases.ubuntu.com/releases/18.04/) 下载此操作系统。

## 第一步 — 安装 Go

在这一步，你通过 [Go 官方下载页面](https://golang.org/dl/)下载最新版本来安装 Go。

为此，你需要找到最新二进制版本压缩包的 URL 。你还要注意旁边列出的 SHA256 哈希值，因为你将用它来[验证下载的文件](https://www.digitalocean.com/community/tutorials/how-to-verify-downloaded-files)。

你将通过命令行来完成安装和设置，这是一种与计算机交互的非图形化方式。也就是说，你输入的是文本，然后也是通过文本得到计算机的反馈，而不是点击按钮。

命令行，也就是我们熟知的 _shell_ 或者 _终端_ ，可以帮助你修改或自动化很多你每天执行在计算机上的任务，这是软件开发人员必备的工具。尽管有很多终端命令需要学习，但是这些命令可以让你做更强大的事情。有关命令行的更多信息，请查看 [Linux 终端简介](https://www.digitalocean.com/community/tutorials/an-introduction-to-the-linux-terminal) 教程。

在  Ubuntu 18.04 上，你可以通过点击屏幕左上角的 Ubuntu 图标并在搜索栏中输入`terminal`来找到终端程序。点击终端程序图标来打开终端。或者你可以在键盘上同时按住的“CTRL”、“ALT”和“T”键来自动打开终端程序。

![Ubuntu Terminal](https://assets.digitalocean.com/articles/eng_python/UbuntuDebianSetUp/UbuntuSetUp.png)

终端打开之后，你可以手动安装 Go 二进制。虽然可以通过包管理工具，比如 `apt-get`，但是通过手动安装可以帮助你理解一个有效的 Go 工作区系统里面任何必要的配置信息的修改。

下载 Go 之前，确保你在  home (`~`) 目录：

```shell
cd ~
```


根据从官方 Go 下载页面复制的压缩包 URL，使用 `curl` 命令拉取下载：

  ```shell
curl -LO https://dl.google.com/go/go1.12.1.linux-amd64.tar.gz
  ```

接下来，使用 `sha256sum` 命令来校验压缩包：

```shell
sha256sum go1.12.1.linux-amd64.tar.gz
```

运行上面命令显示的哈希值应该和下载页面的哈希值一致，如果不一致的话，那么这个压缩包就不是一个有效文件，需要重新下载。

```shell
Output
2a3fdabf665496a0db5f41ec6af7a9b15a49fbe71a85a50ca38b1f13a103aeec  go1.12.1.linux-amd64.tar.gz
```

接下来，解压下载的文件并将其安装到系统所需位置。一般都是放在 `/usr/local` 目录下面：

```shell
sudo tar -xvf go1.12.1.linux-amd64.tar.gz -C /usr/local
```

那么在 `/usr/local` 目录下面有一个  `go` 目录。

**注意**：尽管  `/usr/local/go`  是官方推荐的位置，但有些用户可能更喜欢或需要不同的路径。

在这一步中，你在  Ubuntu 18.04 机器上下载并安装了 Go 。接下来你将配置 Go 的工作区。

## 第二步 — 创建你的 Go 工作区

安装完 Go 之后，你可以创建你的编码工作区。Go 语言的工作区在其根目录下包含两个目录：

-   `src`: 该目录包含 Go 的源文件。所谓源文件就是你用 Go 编程语言写的文件。源文件被 Go 编译器构建成可执行的二进制文件。
-   `bin`: 该目录包含了 Go 工具构建和安装的可执行文件。可执行文件就是运行在你系统上并执行任务的二进制文件。通常是你的源码或者是其他下载的 Go 源代码编译的程序。

`src` 子目录可能包含多个版本控制仓库（例如 [Git](https://git-scm.com/), [Mercurial](https://www.mercurial-scm.org/) 和 [Bazaar](http://bazaar.canonical.com/)）。这允许你在你的项目中规范导入代码。 规范导入就是引用完全限定包的导入，例如 `github.com/digitalocean/godo` 。

当你引入第三方库的时候，你可以看到类似  `github.com`, `golang.org` 或其他目录，如果你使用的是 `github.com` 之类的代码仓库，你还将把项目和源文件放在该目录下。 我们将在此步骤的后面部分探讨这个概念。

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
                ├── .git                            # Git repository metadata
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
nano ~/.profile
```

通过下面的命令设置你的  `$GOPATH` ：

```shell
~/.profile
export GOPATH=$HOME/go
```

当 Go 编译和安装工具时，会将他们放在  `$GOPATH/bin`  目录。为方便起见，通常将工作区的 `/bin` 子目录添加到 `~/.profile` 中的 `PATH` 中：

```shell
~/.profile
export PATH=$PATH:$GOPATH/bin
```

这将允许你在系统上的任何位置运行通过 Go 工具编译或下载的任何程序。

最后，你需要将 `go` 二进制文件添加到 `PATH` 中。 你可以通过在行尾添加 `/usr/local/go/bin` 来实现：

```shell
~/.profile
export PATH=$PATH:$GOPATH/bin:/usr/local/go/bin
```

将 `/usr/local/go/bin` 添加到 `$PATH` 中，可以使所有 Go 工具都可以在系统上的任何位置使用。

为了更新你的 shell 配置，请使用下面的命令来加载全局变量:

```shell
. ~/.profile
```

您可以通过使用 `echo` 命令并检查其输出，来验证你的 `$PATH` 是否已更新：

```shell
echo $PATH
```

你将看到你的  `$GOPATH/bin` 显示在你的主目录中。如果你以  `root` 身份登录，你将在路径中看到 `/root/go/bin`。

```shell
Output
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/root/go/bin:/usr/local/go/bin
```

你还会看到  `/usr/local/go/bin` 的 Go 工具的路径：

```shell
Output
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/root/go/bin:/usr/local/go/bin
```

通过检查 Go 的当前版本来验证安装：

```shell
go version
```

我们应该得到类似下面的输出：

```shell
Output
go version go1.12.1 linux/amd64
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

这将下载 `godo` 库的内容并在你的计算机上创建 `$GOPATH/src/github.com/digitalocean/godo` 目录。
你可以通过列出目录来检查下看看是否成功下载了 `godo`包：

```shell
ll $GOPATH/src/github.com/digitalocean/godo
```

你应该看到类似下面这样的输出：

```shell
>>>>>>> main
Outputdrwxr-xr-x 4 root root  4096 Apr  5 00:43 ./
drwxr-xr-x 3 root root  4096 Apr  5 00:43 ../
drwxr-xr-x 8 root root  4096 Apr  5 00:43 .git/
-rwxr-xr-x 1 root root     8 Apr  5 00:43 .gitignore*
-rw-r--r-- 1 root root    61 Apr  5 00:43 .travis.yml
-rw-r--r-- 1 root root  2808 Apr  5 00:43 CHANGELOG.md
-rw-r--r-- 1 root root  1851 Apr  5 00:43 CONTRIBUTING.md
.
.
.
-rw-r--r-- 1 root root  4893 Apr  5 00:43 vpcs.go
-rw-r--r-- 1 root root  4091 Apr  5 00:43 vpcs_test.go
```

在这一步中，你创建了一个 Go 工作区并且配置了必要的环境变量。下一步你将使用一些代码来测试下工作区。

## 第三步 — 创建一个简单的程序

现在你已经设置了工作区，来创建一个  “Hello, World!” 程序吧。这可以检验工作区配置是否正确，并且给你一个更加熟悉 Go 的机会。因为我们创建的是单个 Go 源文件，而不是实际项目，所以我们不需要在工作区中执行此操作。

在你的 home 目录，打开一个命令行文本编辑器，例如 `nano`，然后创建一个新文件：

```shell
nano hello.go
```

在新文件里写下你的程序：

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello, World!")
}
```

该代码使用了  `fmt` 包并且使用  `Hello, World!`  作为参数调用了  `Println` 函数。这将导致短语 `Hello, World!` 在程序运行时打印到终端上。

按 `CTRL` 和 `X` 键退出 `nano`。 当提示保存文件时，按 `Y`，然后按 `ENTER` 退出。

退出 `nano` 返回 shell 之后，运行程序：

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

恭喜！至此，你已经在 Ubuntu 机器上设置了 Go 编程工作区，可以开始写项目了！