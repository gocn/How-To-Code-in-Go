# 如何在 Windows 10 上安装 Go 和设置本地编程环境

## Go 语言简介

[Go](https://golang.org/) 是一门在 Google 备受挫折后而诞生的语言。开发者不得不频繁地在两门语言中选择，要么选择一门执行效率高但是编译时间长的语言，要么选择一种易于编程但在生产中运行效率低下的语言。 Go 被设计为同时提供所有这三个功能：快速编译、易于编程和生产中的高效执行。

虽然 Go 是一门通用的编程语言，可用于许多不同类型的编程项目。但它特别适合网络/分布式系统项目，赢得了“云语言”的美誉。Go  语言专注于通过一组强大的工具来帮助现代程序员完成更多的工作，通过使格式成为语言规范的一部分来消除对格式的争论，以及通过编译为单个二进制文件来简化部署。 Go 易于学习，关键字非常少，这使其成为不论是初学者还是经验丰富的开发人员的不二之选。

本教程将指导你通过命令行来安装 Go 和配置 Go 编程环境。本教程特别针对 Ubuntu 18.04 的安装过程，但是对于其他 Debian Linux 发行版也同样适用。

## 安装前提

你需要一台拥有管理员权限并且能够连接上网的  Windows 10 的电脑。

## 第一步 — 打开并配置 PowerShell

我们将会在终端上完成我们大部分的安装和设置，这是一种与计算机交互的非图形化方式。也就是说，你输入的是文本，然后也是通过文本得到计算机的反馈，而不是点击按钮。命令行，也就是我们熟知的 _shell_ 或者 _终端_ ，可以帮助你修改或自动化很多你每天执行在计算机上的任务，这是软件开发人员必备的工具。

[PowerShell](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/powershell) 是微软提供的一个命令行 shell 界面的程序。通过 `cmdlets` （发音为 `command-lets` ）来执行管理任务，它们是 [.NET](https://dotnet.microsoft.com/) 软件框架可以执行操作的专用类。PowerShell 于 2016 年 8 月开源，现在可跨平台使用，适用于 Windows 和 UNIX 系统（包括 Mac 和 Linux）。

如何找到 Windows PowerShell 呢，你可以右击屏幕左下角的**开始**按钮，弹出菜单后，点击**搜索**，然后在搜索栏中输入 `PowerShell` 。当你看到选项时，右击桌面应用程序中的 **Windows PowerShell**。 出于本教程的目的，选择**以管理员身份运行**。 当系统提示你出现一个对话框，询问**是否允许此应用程序对您的 PC 进行更改？** 单击**是**。

完成此操作后，你将看到一个基于文本的界面，其中包含如下所示的字符串：

![Windows 10 PowerShell](https://assets.digitalocean.com/articles/eng_python/Win10SetUp/Win10SetUp.png)

输入以下命令退出系统文件夹：

```shell
cd ~
```

然后你会进入到主目录中，比如 `PS C:\Users\sammy`

要继续安装过程，你必须通过 PowerShell 设置权限。默认情况下以最安全的模式运行，作为管理员你可以设置以下几个级别的权限：

-   **Restricted** 是默认执行策略。 在这种模式下，你将无法运行脚本，并且 PowerShell 只能作为交互式 shell 工作。
-   **AllSigned** 将使你能够运行由受信任的发布者签名的所有脚本和配置文件，这意味着可能让计算机面临着运行恶意脚本的风险，这脚本碰巧由受信任发布者签名。
-   **RemoteSigned** 将允许你运行由受信任的发布者签名的从 Internet 下载的脚本和配置文件，如果这些受信任的脚本实际上是恶意的，则再次使你的计算机暴露在漏洞面前。.
-   **Unrestricted** 将运行从 Internet 下载的所有脚本和配置文件，只要你确认你知晓该文件是从 Internet 下载的。 在这种情况下，不需要数字签名，因此你可能会让你计算机面临运行从 Internet 下载的未签名和潜在恶意脚本的风险。

在本教程中，你将使用 `RemoteSigned` 执行策略来设置当前用户的权限。 这将允许 PowerShell 接受受信任的脚本，而不会使权限像 `无限制` 权限那样广泛。 在 PowerShell 中输入以下内容：

```shell
Set-ExecutionPolicy -Scope CurrentUser
```

然后 PowerShell 将提示你为你提供一个可执行策略。 输入以下内容来使用`RemoteSigned` 策略：

```
RemoteSigned
```

 一旦你按了回车键，系统会要求你确认对执行政策的更改。 输入字母 `y` 以使更改生效。 你可以通过询问机器上的当前权限来确认这是否生效：

```
Get-ExecutionPolicy -List
```

你应该会收到如下所示的输出：

```shell
Output        
        Scope ExecutionPolicy
        ----- ---------------
MachinePolicy       Undefined
   UserPolicy       Undefined
      Process       Undefined
  CurrentUser    RemoteSigned
 LocalMachine       Undefined
```

这表明当前用户可以运行从 Internet 下载的受信任脚本。 你现在可以继续下载设置 Go 编程环境所需的文件。

## 第二步 — 安装包管理器 Chocolatey

所谓包管理器就是一组软件工具的集合，这些工具使得安装过程自动化。这包括软件的初始安装、升级和配置，以及根据需要删除软件。 他们将软件安装保存在一个中央位置，并且可以以常用格式维护系统上的所有软件包。

[Chocolatey](https://chocolatey.org/) 是一个为 Windows 构建的命令行包管理器，其工作方式与 Linux 上的 `apt-get` 类似。 Chocolatey 提供开源版本，可帮助你快速安装应用程序和工具。 你可以用它来下载开发环境所需的内容。

在安装脚本之前，请阅读一下脚本并确认它对你的机器所做的更改是否令你感到满意。 为此，请使用 .NET 脚本框架下载 Chocolatey 脚本并将其显示在终端窗口中。

首先创建一个名为 `$script` 的 WebClient 对象，该对象与 Internet Explorer 共享 Internet 连接设置：

```shell
$script = New-Object Net.WebClient
```

通过将带有 `|` 的 `$script` 对象传递给 `Get-Member` 类来查看可用选项：

```shell
$script | Get-Member
```

这将返回此 WebClient 对象的所有成员（属性和方法）：

```shell
 . . .
[secondary_label Snippet of Output]
DownloadFileAsync         Method     void DownloadFileAsync(uri address, string fileName), void DownloadFileAsync(ur...
DownloadFileTaskAsync     Method     System.Threading.Tasks.Task DownloadFileTaskAsync(string address, string fileNa...
DownloadString            Method     string DownloadString(string address), string DownloadString(uri address) #method we will use
DownloadStringAsync       Method     void DownloadStringAsync(uri address), void DownloadStringAsync(uri address, Sy...
DownloadStringTaskAsync   Method     System.Threading.Tasks.Task[string] DownloadStringTaskAsync(string address), Sy…
 . . .
```

查看输出内容，你可以识别出用于在 PowerShell 窗口中显示脚本和签名的 `DownloadString` 方法。 使用此方法检查脚本：

```shell
$script.DownloadString("https://chocolatey.org/install.ps1")
```

检查脚本后，通过在 PowerShell 中输入以下内容来安装 Chocolatey：

```shell
iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
```

cmdlet `iwr` 或 `Invoke-WebRequest` 允许你从 Web 中提取数据。 然后将脚本传递给 `iex` 或 `Invoke-Expression` cmdlet，后者将执行脚本的内容并开始 Chocolatey 包管理器的安装。

允许 PowerShell 安装 Chocolatey。 等到安装完成后，你可以使用 `choco` 命令开始安装其他工具。

如果你以后需要随时升级 Chocolatey，请运行以下命令：

```shell
choco upgrade chocolatey
```

安装包管理器后，你可以安装 Go 编程环境所需的其余部分。

## 第三步 — 安装文本编辑器 Nano (可选)

在这一步中，你将安装 _nano_，这是一个使用命令行界面的文本编辑器。 你可以使用 nano 直接在 PowerShell 中编写程序。 这不是强制性步骤，因为你也可以使用带有图形用户界面的文本编辑器，例如 Notepad 。 本教程推荐使用 nano，因为它将帮助你习惯使用 PowerShell。

使用 Chocolatey 安装 nano：

```shell
choco install -y nano
```

`-y` 标志会自动确认你要运行脚本，而不会提示你确认。

安装 nano 后，你可以使用 `nano` 命令创建新的文本文件。 你将在本教程后面使用它来编写你的第一个 Go 程序。

## 第四步 — 安装 Go

就像你在上一步中安装 nano 一样，你将使用 Chocolatey 安装 Go：

```shell
choco install -y golang
```

**注意：** 因为 `go` 是一个很小的词，所以使用 `golang` 作为安装包的术语， 这也是在互联网上搜索 Go 相关文章时的常见做法。 `Golang`  一词源于 Go 的域，即 `golang.org`

PowerShell 安装 Go 的过程中，会在 PowerShell 中生成输出内容。 安装完成后，你应该会看到以下输出：

```powershell
Output
Environment Vars (like PATH) have changed. Close/reopen your shell to
see the changes (or in powershell/cmd.exe just type `refreshenv`).
The install of golang was successful.
 Software installed as 'msi', install location is likely default.

Chocolatey installed 1/1 packages.
See the log for details (C:\ProgramData\chocolatey\logs\chocolatey.log).
```

安装完成后，你可以确认下 Go 是否已安装。 要查看更改，请以管理员身份关闭并重新打开 PowerShell，然后检查本地计算机上可用的 Go 版本：

```
go version
```

你将得到类似以下内容的输出：

```
Output
go version go1.12.1 windows/amd643.7.0
```

安装 Go 后，你可以为开发项目设置工作区。

## 第五步 — 创建你的 Go 工作区

现在你已经安装了 Chocolatey、nano 和 Go，可以接着创建你的编程工作区。

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

使用下面命令导航到 `$HOME` 目录：

```
cd $HOME
```

接下来，为你的 Go 工作区创建目录结构：

```
mkdir go/bin, go/src
```

以上命令将确保下面的目录结构各就各位：

```
└── $HOME
    └── go
        ├── bin
        └── src
```

在 Go 1.8 之前，需要设置一个名为 `$GOPATH` 的本地环境变量。 `$GOPATH` 告诉编译器在哪里可以找到导入的第三方源代码，同样包括任何你写的本地源代码。 虽然不再明确要求它，但它仍然被认为是一种很好的做法，因为许多第三方工具仍然依赖于设置的这个变量。

由于你使用 Chocolatey 进行安装，因此应该已经设置了此环境变量。 你可以使用以下命令验证这一点：

```shell
$env:GOPATH
```

你应该会看到以下输出，其中  `sammy` 代替了你的用户名：

```
Output
C:\Users\sammy\go
```

当 Go 编译和安装工具时，会将他们放在  `$GOPATH/bin`  目录。为方便起见，通常将工作区的 `/bin` 子目录添加到 `~/.profile` 中的 `PATH` 中。你可以使用 PowerShell 中的 `setx` 命令执行此操作：

```shell
setx PATH "$($env:path);$GOPATH\bin"
```

这将允许你在系统上的任何位置运行通过 Go 工具编译或下载的任何程序。

现在你已经创建了工作区的根目录并设置了 `$GOPATH` 环境变量，你可以根据以下目录结构创建你未来的项目。 此示例假设你使用 `github.com` 作为仓库：

```
$GOPATH/src/github.com/username/project
```

例如，如果你正在开发  [`https://github.com/digitalocean/godo`](https://github.com/digitalocean/godo)  项目，它将存储在以下目录中：

```
$GOPATH/src/github.com/digitalocean/godo
```

该项目结构使项目可以通过 `go get` 工具使用。它也有助于以后的可读性。 你可以通过使用 `go get` 命令并获取 `godo` 库来验证这一点：

```
go get github.com/digitalocean/godo
```

**注意：** 如果你没有安装 `git`，Windows 会打开一个对话框询问你是否要安装它。 单击 **Yes** 继续并按照安装说明进行操作。

你可以通过列出目录来检查下看看是否成功下载了 `godo`包：

```
ls $env:GOPATH/src/github.com/digitalocean/godo
```

你应该看到类似下面这样的输出：

```
Output    
    Directory: C:\Users\sammy\go\src\github.com\digitalocean\godo


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        4/10/2019   2:59 PM                util
-a----        4/10/2019   2:59 PM              9 .gitignore
-a----        4/10/2019   2:59 PM             69 .travis.yml
-a----        4/10/2019   2:59 PM           1592 account.go
-a----        4/10/2019   2:59 PM           1679 account_test.go
-rw-r--r--  1 sammy  staff   2892 Apr  5 15:56 CHANGELOG.md
-rw-r--r--  1 sammy  staff   1851 Apr  5 15:56 CONTRIBUTING.md
.
.
.
-a----        4/10/2019   2:59 PM           5076 vpcs.go
-a----        4/10/2019   2:59 PM           4309 vpcs_test.go
```

在这一步中，你创建了一个 Go 工作区并且配置了必要的环境变量。下一步你将使用一些代码来测试下工作区。

## 第六步 — 创建一个简单的程序

现在你已经设置了工作区，来创建一个  “Hello, World!” 程序吧。这可以检验工作区配置是否正确，并且给你一个更加熟悉 Go 的机会。因为我们创建的是单个 Go 源文件，而不是实际项目，所以我们不需要在工作区中执行此操作。

在你的 home 目录，打开一个命令行文本编辑器，例如 `nano`，然后创建一个新文件：

```
nano hello.go
```

在 nano 中打开文本文件后，输入你的程序代码：

```go
hello.go
package main

import "fmt"

func main() {
	fmt.Println("Hello, World!")
}
```

按 `CTRL` 和 `X` 键退出 `nano`。 当提示保存文件时，按 `Y`，然后按 `ENTER` 退出。

该代码使用了  `fmt` 包并且使用  `Hello, World!`  作为参数调用了  `Println` 函数。这将导致短语 `Hello, World!` 在程序运行时打印到终端上。

退出 `nano` 返回 shell 之后，运行程序：

```
go run hello.go
```

你刚刚创建的 hello.go 程序应该会在 PowerShell 产生以下输出：

```
Output
Hello, World!
```

在此步骤中，你使用了一个简单小程序来验证是否正确配置了 Go 工作区。

## 总结

恭喜！至此，你已经在 Ubuntu 机器上设置了 Go 编程工作区，可以开始写项目了！