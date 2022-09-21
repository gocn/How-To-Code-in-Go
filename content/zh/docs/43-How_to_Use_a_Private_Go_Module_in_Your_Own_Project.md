# 如何在自己的项目中使用私有的Go模块

## 简介

Go 的生态系统的一个优势是大量的模块是开源的。由于它们是开源的，所以可以自由访问、检查、使用和学习。然而，有时出于各种原因，有必要制作一个私有的 Go 模块，例如在公司内部保留专有的商业逻辑。

在本教程中，你将发布一个私有 Go 模块，设置访问私有模块的认证，并在项目中使用一个私有 Go 模块。

## 前期准备

- 安装1.16 或更高版本的 Go。如何安装 Go ，请根据你的操作系统遵循 [如何安装Go](https://gocn.github.io/How-To-Code-in-Go/docs/01-How_To_Install_Go_and_Set_Up_a_Local-Programming_Environment_on_Ubuntu_18.04_DigitalOcean/) 教程。
- 对分发 Go 模块的理解，可以在 [如何分发 Go 模块](https://www.digitalocean.com/community/tutorials/how-to-distribute-go-modules) 教程中找到。
- 熟悉Git，你可以参考 [How To Use Git: A Reference Guide](https://www.digitalocean.com/community/cheatsheets/how-to-use-git-a-reference-guide) 。
- 一个名为 `mysecret` 的空的 GitHub 私有仓库，用于发布你的私有模块。要开始使用，请遵循 [GitHub 创建仓库的文档](https://docs.github.com/en/get-started/quickstart/create-a-repo)。
- 一个可以从你的仓库读取的[GitHub 个人访问令牌](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token)。你将用它来允许 Go 访问你的私有仓库。

## 分发一个私有模块

与许多编程语言不同，Go 从仓库而不是中央包服务器发布模块。这种方法的一个好处是，发布私有模块与发布公共模块非常相似。Go 的私有模块不需要一个完全独立的私有包服务器，而是通过一个私有源代码库发布。由于大多数源代码托管选项都支持开箱即用，所以不需要再建立一个额外的私有服务器。

为了使用私有模块，你需要访问一个私有 Go 模块。在本节中，你将创建并发布一个私有模块，你可以在以后的教程中使用，从另一个 Go 程序中访问一个私有模块。

要创建新的私有 Go 模块，首先要克隆它所在的私有 GitHub 仓库。作为先决条件的一部分，你在 GitHub 账户中创建了一个名为 `mysecret` 的私有空仓库，这就是你将用于私有模块的仓库。这个仓库可以在你的电脑上的任何地方克隆，但许多开发者倾向于为他们的项目建立一个目录。在本教程中，你将使用一个名为 `projects` 的目录。

创建 `projects` 目录并进入：

```
mkdir projects
cd projects
```

在`projects`目录下，运行`git clone`将你的私人 `mysecret` 仓库克隆到你的电脑上：

```
git clone git@github.com:your_github_username/mysecret.git
```

Git 会确认它已经克隆了你的模块，你可能会得到一个警告，说你克隆了一个空的仓库，但这并不值得担心：

```
Output
Cloning into 'mysecret'...
warning: You appear to have cloned an empty repository.
```

接下来，使用 `cd` 进入你克隆的 `mysecret` 目录，并使用 `go mod init` ，加上你的私有仓库的名称，创建一个新的Go模块：

```
cd mysecret
go mod init github.com/your_github_username/mysecret
```

现在你的模块已经创建，现在是时候添加一个你可以从另一个项目中使用的函数了。使用 `nano`，或你喜欢的文本编辑器，打开一个与你的仓库同名的文件，如 `mysecret.go`。这个名字并不重要，可以是任何东西，但使用与仓库相同的名字可以使你在使用新模块时，更容易确定先看哪个文件。

```
nano mysecret.go
```

在 `mysecret.go` 文件中，用与你的仓库相同的名字命名软件包，然后添加一个 `SecretProcess` 函数，在调用时打印 `Running the secret process!` 这一行：

projects/mysecret/mysecret.go

```go
package mysecret

import "fmt"

func SecretProcess() {
	fmt.Println("Running the secret process!")
}
```

现在你已经创建了你的私人模块，你将把它发布到你的私人资源库，供其他人使用。由于你的私人资源库最初只允许你访问，你能够控制谁可以访问你的私人模块。你可能会限制自己的访问，但你也可以让朋友或同事访问。

由于私有和公共 Go 模块都是源码库，发布私有 Go 模块的过程与发布公共模块的过程相同。要发布你的新模块，使用`git add`命令在当前目录下进行修改，然后使用 `git commit` 命令将这些修改提交到你的本地存储库：

```
git add .
git commit -m "Initial private module implementation"
```

你会看到 Git 确认你的初始提交已经成功，以及提交中包含的文件的摘要：

```
Output
[main (root-commit) bda059d] Initial private module implementation
 2 files changed, 10 insertions(+)
 create mode 100644 go.mod
 create mode 100644 mysecret.go
```

现在唯一剩下的部分就是把你的改动移到你的 GitHub 仓库里。与公共模块类似，使用 `git push` 命令来发布你的代码：

```
git push
```

然后，Git 会推送你的修改，让任何能访问你的私有仓库的人都能看到这些修改：

```
git push origin main
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (4/4), 404 bytes | 404.00 KiB/s, done.
Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:your_github_username/mysecret.git
 * [new branch]      main -> main
```

与公开的 Go 模块一样，你也可以为你的私有 Go 模块添加版本。[如何发布Go模块](https://www.digitalocean.com/community/tutorials/how-to-distribute-go-modules)教程中的[发布新模块版本](https://www.digitalocean.com/community/tutorials/how-to-distribute-go-modules#publishing-a-new-module-version)部分包含了如何做到这一点的信息。

在这一节中，你创建了一个带有 `SecretProcess` 函数的新模块，并将其发布到你的私人 `mysecret` GitHub 仓库，使其成为一个私人 Go 模块。为了从其他 Go 程序中访问这个模块，你需要配置 Go，使它知道如何访问这个模块。

## 配置 Go 访问私有模块

虽然 Go 模块通常从其源代码库中分发，但 Go 团队也运行一些[中央 Go 模块服务](https://proxy.golang.org/)，以确保模块在原始仓库发生意外时继续存在。默认情况下，Go 被配置为使用这些服务，但当你试图下载一个私有模块时，它们可能会引起问题，因为它们无法访问这些模块。要告诉 Go 某些导入路径是私有的，它不应该尝试使用中央 Go 服务，你可以使用 `GOPRIVATE` 环境变量。`GOPRIVATE` 环境变量是一个以逗号分隔的导入路径前缀列表，当遇到这种情况时，Go 工具将尝试直接访问它们，而不是通过中央服务。一个这样的例子是你刚刚创建的私有模块。

为了使用私有模块，你将通过在 `GOPRIVATE` 变量中设置来告诉 Go 哪个路径是私有的。在设置 `GOPRIVATE` 变量值时，你可以做几个选择。一个选择是将 `GOPRIVATE` 设置为 `github.com`。但这可能不是你想要的，因为这将告诉 Go 不为任何托管在`github.com`上的模块使用中央服务，包括不是你的模块。

下一个选择是将 `GOPRIVATE` 设置为只有你自己的用户路径，如`github.com/your_github_username`。这就解决了把所有 GitHub 视为私有的问题，但在某些时候，你可能会有你创建的公共模块，你想通过 Go 模块镜像下载。这样做不会造成任何问题，是一个完全合理的选择，但你也可以选择更具体的方式。

最具体的选择是将 `GOPRIVATE` 设置为与你的模块的路径完全匹配，例如，`github.com/your_github_username/mysecret`  。这就解决了前面两个选项的问题，但也带来了一个问题，即你需要将你的每个私有仓库单独添加到 `GOPRIVATE` 中，如图。

```
GOPRIVATE=github.com/your_github_username/mysecret,github.com/your_github_username/othersecret
```

选择最适合你自己的方案是一个根据自身情况权衡利弊的问题。

由于你现在只有一个私有模块，我们将使用完整的仓库名称作为值。要在当前终端设置 `GOPRIVATE=github.com/your_github_username/mysecret` 环境变量，使用 export 命令：

```
export GOPRIVATE=github.com/your_github_username/mysecret
```

如果你想仔细检查它是否被设置，你可以使用 `env` 命令和 `grep` 来检查 `GOPRIVATE` 名称：

```
env | grep GOPRIVATE
```

```
Output
GOPRIVATE=github.com/your_github_username/mysecret
```

尽管 Go 现在知道你的模块是私有的，但还不能完全使用该模块。如果你试图将你的私有模块 `go get` 到另一个模块中，你可能会看到一个类似的错误。

```
go get github.com/your_github_username/mysecret
```

```
Output
go get: module github.com/your_github_username/mysecret: git ls-remote -q origin in /Users/your_github_username/go/pkg/mod/cache/vcs/2f8c...b9ea: exit status 128:
fatal: could not read Username for 'https://github.com': terminal prompts disabled
Confirm the import path was entered correctly.
If this is a private repository, see https://golang.org/doc/faq#git_https for additional information.
```

这条错误信息说 Go 试图下载你的模块，但它遇到了它仍然无法访问的东西。由于 Git 被用来下载模块，它通常会要求你输入凭证。然而，在这种情况下，Go 正在为你调用 Git，无法提示你。在这一点上，要访问你的模块，你需要提供一种方法，让 Git 在不需要你立即输入的情况下检索你的凭证。

## 为 HTTPS 提供私有模块凭证

告诉 Git 如何代表你登录的一种方法是 `.netrc` 文件。`.netrc` 文件位于用户的主目录中，包含了各种主机名称以及这些主机的登录凭证。它被许多工具广泛使用，包括 Git。

默认情况下，当 `go get` 试图下载一个模块时，它将首先尝试使用 HTTPS。然而，正如前面的例子所示，它无法提示你的用户名和密码。为了给 Git 提供你的凭证，你需要在你的主目录下有一个 `.netrc`，其中包括 `github.com`。

要在Linux、MacOS或 Windows Subsystem for Linux（WSL）上创建一个`.netrc`文件，在你的主目录（`~/`）中打开`.netrc`文件，这样你就可以编辑它：

```
nano ~/.netrc
```

接下来，在该文件中创建一个新条目。`machine` 值应该是你要设置证书的主机名，本例中是 `github.com`。`login` 值应该是你的 GitHub用户名。最后，`password` 值应该是你创建的[GitHub个人访问令牌](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token)。

~/.netrc

```
machine github.com
login your_github_username
password your_github_access_token
```

如果你愿意，你也可以把整个条目放在文件中的一行:

~/.netrc

```
machine github.com login your_github_username password your_github_access_token
```

**注意：**如果你使用 [Bitbucket](https://bitbucket.org/) 作为你的源代码托管，你可能还需要在 `bitbucket.org` 之外添加第二个条目 `api.bitbucket.org`。在过去，Bitbucket 为多种类型的版本控制提供托管服务，所以 Go 会在尝试下载之前使用 API 来检查仓库的类型。虽然现在已经不是这样了，但 API 检查仍然存在。如果你遇到这个问题，一个错误信息的例子可能是这样的：

```
go get bitbucket.org/your_github_username/mysecret: reading https://api.bitbucket.org/2.0/repositories/your_bitbucket_username/protocol?fields=scm: 403 Forbidden
server response: Access denied. You must have write or admin access.
```

如果你在试图下载一个私有模块时看到 `403 Forbidden` 错误，请仔细检查 Go 试图连接的主机名。它可能表示另一个主机名，如 `api.bitbucket.org`，你需要添加到你的 `.netrc` 文件中。

现在你的环境被设置为使用 HTTPS 认证来下载你的私有模块。尽管 HTTPS 是 Go 和 Git 尝试下载模块的默认方式，但也可以告诉 Git 使用 SSH 来代替。使用 SSH 而不是 HTTPS 很有用，这样你就可以使用推送私有模块时使用的相同 SSH 密钥。如果你不愿意创建个人访问令牌，也可以在设置 [CI/CD](https://www.digitalocean.com/community/tags/ci-cd) 环境时使用 [deploy keys](https://docs.github.com/en/developers/overview/managing-deploy-keys)。

## 为SSH提供私有模块凭证

为了使用 SSH 密钥作为私有 Go 模块的认证方式而不是 HTTPS，Git 提供了一个名为 `insteadOf` 的配置选项。`insteadOf` 选项表示你说：“与其”使用 `https://github.com/` 作为所有 Git 请求的 URL，你更愿意使用 `ssh://git@github.com/`。

在Linux、MacOS 和 WSL 上，这个配置存在于 `.gitconfig` 文件中。你可能已经很熟悉这个文件了，因为你的提交邮件地址和名字也是在这里配置的。要编辑该文件，请使用 `nano`，或你喜欢的文本编辑器，并打开你的主目录中的 `~/.gitconfig` 文件：

```
nano ~/.gitconfig
```

一旦你打开这个文件，编辑它的 `url` 部分为 `ssh://git@github.com/` ，如下面的例子：

~/.gitconfig

```
[user]
email = your_github_username@example.com
name = Sammy the Shark

[url "ssh://git@github.com/"]
insteadOf = https://github.com/
```

`url` 部分相对于 `user` 部分的顺序并不重要，如果文件中除了你刚刚添加的 `url` 部分外没有其他内容，你也不需要担心。`email` 和 `name` 字段在 `user` 部分的顺序也不重要。

这个新的部分告诉 Git，你使用的任何以 `https://github.com/` 开头的 URL 应该用 `ssh://git@github.com/` 代替前缀。由于 Go 默认使用 HTTPS，这也会影响到你的 `go get` 命令。以你的私有模块为例，这意味着 Go 将 `github.com/your_github_username/mysecret` 导入路径变成 `https://github.com/your_github_username/mysecret` 的URL。当 Git 遇到这个 URL 时，它将看到这个 URL 与 `insteadOf` 引用的 `https://github.com/` 前缀相匹配，并将生成的 URL 变成 `ssh://git@github.com/your_github_username/mysecret`。

只要`ssh://git@`的 URL 对该主机也有效，这个模式也可以用于 GitHub 以外的域名。

在本节中，你通过更新 `.gitconfig` 文件并添加 `url` 部分，配置了 Git 使用 SSH 下载 Go 模块。现在你的私有模块的认证已经设置好了，你可以访问它，在你的Go程序中使用。

## 使用一个私有模块

在前面的章节中，您将 Go 配置为通过 HTTPS、SSH 或可能同时访问您的私有 Go 模块。现在 Go 可以访问你的私有模块，它的使用与你过去可能使用的任何公共模块相似。在本节中，你将创建一个使用你的私有模块的新 Go 模块。

在你用于项目的目录中，如 `projects`，用 `mkdir` 命令为新项目创建一个名为 `myproject` 的目录:

```
mkdir myproject
```

一旦目录创建完毕，使用 `cd` 进入该目录，并根据你的项目所在的仓库 URL，使用 `go mod init` 为你的项目初始化一个新的 Go 模块，例如 `github.com/your_github_username/myproject`。如果你不打算将你的项目推送到任何其他仓库，模块名称可以是 `myproject`，或任何其他名称，但使用完整的 URL 是很好的做法，因为大多数共享的模块都需要它们。

```
Output
go: creating new go.mod: module github.com/your_github_username/myproject
```

现在，用`nano`或你喜欢的文本编辑器打开`main.go`，创建你项目的第一个代码文件：

```
nano main.go
```

在该文件中，设置初始的 `main` 函数，你将从该函数中调用你的私有模块：

projects/myproject/main.go

```go
package main

import "fmt"

func main() {
	fmt.Println("My new project!")
}
```

现在运行你的项目确保一切都设置正确，你可以使用 `go run` 命令并向它提供 `main.go` 文件：

```
go run main.go
```

```
Output
My new project!
```

接下来，使用 `go get ` 将你的私有模块作为你的新项目的一个依赖项加入，就像你对一个公共模块的做法一样：

```
go get github.com/your_github_username/mysecret
```

然后 `go` 工具将下载你的私有模块的代码，并使用与你的最新提交哈希值和提交时间相匹配的版本字符串，将其添加为依赖关系：

```
Output
go: downloading github.com/your_github_username/mysecret v0.0.0-20210920195630-bda059d63fa2
go get: added github.com/your_github_username/mysecret v0.0.0-20210920195630-bda059d63fa2
```

最后，再次打开 `main.go` 文件，更新代码，在 `main` 函数中添加对你的私有模块的 `SecretProcess` 函数的调用。你还需要更新 `import` 语句，把你的 `github.com/your_github_username/mysecret` 私有模块也作为一个导入文件。

projects/myproject/main.go

```go
package main

import (
	"fmt"

	"github.com/your_github_username/mysecret"
)

func main() {
	fmt.Println("My new project!")
	mysecret.SecretProcess()
}
```

要看到最终的项目与你的私有模块一起运行，再次使用 `go run` 命令，同时提供 `main.go` 文件作为参数：

```
go run main.go
```

你会看到原始代码中的 `My new project!` 一行，但现在你也会看到你导入的 `mysecret ` 模块中的 `Running the secret process!` 一行：

```
Output
My new project!
Running the secret process!
```

在这一节中，你用 `go init` 创建了一个新的 Go 模块来访问你之前发布的私有模块。一旦你创建了这个模块，你就使用 `go get`来下载你的私有模块，就像你使用公共 Go 模块一样。最后，你使用 `go run` 来编译和运行使用私有模块的 Go 程序。

## 总结

在本教程中，你创建并发布了一个私有 Go 模块。你还设置了 HTTPS 和 SSH 认证来访问你的私有 Go 模块。最后，你在一个新项目中使用了你的私有模块。

关于 Go 模块的更多信息，Go 项目有[一系列博文](https://blog.golang.org/using-go-modules)详细介绍了 Go 工具如何与模块互动和理解模块。Go 项目在[Go模块参考](https://golang.org/ref/mod)中也有关于 Go 模块的非常详细的技术参考。

除了 `GOPRIVATE` 环境变量外，还有更多的变量可以在处理私有 Go 模块时使用。它们可以在[Go Modules Reference](https://golang.org/ref/mod)的[Private Modules](https://golang.org/ref/mod#private-modules)部分详细了解。

如果你有兴趣更详细地了解`.netrc ` 文件，[GNU网站上的".netrc"](https://www.gnu.org/software/inetutils/manual/html_node/The-_002enetrc-file.html) 包括一个所有可用关键字的列表。 [`git-config`文档](https://git-scm.com/docs/git-config#Documentation/git-config.txt-urlltbasegtinsteadOf)还包括更多关于你使用的 `insteadOf` 配置选项如何工作以及其他可用选项的信息。

本教程也是[DigitalOcean](https://www.digitalocean.com/) [How to Code in Go](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go)系列的一部分。该系列涵盖了许多 Go 的主题，从首次安装 Go 到如何使用语言本身。