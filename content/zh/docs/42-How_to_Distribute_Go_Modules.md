# 如何分发 Go 模块

## 简介

许多现代编程语言允许开发人员分发现成的库，供他人在其程序中使用，Go 也不例外。虽然有些语言使用中央存储库来安装这些库，但 Go 从用于创建库的同一版本控制存储库中分发这些库。Go 还使用了一个称为[语义版本管理](https://semver.org/)的版本管理系统，向用户显示何时以及何种修改。这可以帮助用户知道一个模块的较新版本是否安全，可以快速升级到新版本，并有助于确保他们的软件继续与该模块一起工作下去。

在本教程中，你将创建并发布一个新的模块，学习使用语义版本管理，并发布模块的语义版本。

## 前期准备

-   安装1.16 或更高版本的 Go。如何安装 Go ，请根据你的操作系统遵循 [如何安装Go](https://www.digitalocean.com/community/tutorials/how-to-install-go-on-ubuntu-20-04) 教程。
-   理解 Go 的模块，你可以参考[如何使用Go模块](https://www.digitalocean.com/community/tutorials/how-to-use-go-modules)的教程。
- 熟悉Git，你可以参考 [How To Use Git: A Reference Guide](https://www.digitalocean.com/community/cheatsheets/how-to-use-git-a-reference-guide) 。
- 一个空的 GitHub 公共仓库，名为`pubmodule`，用于你发布的模块。开始使用前，请遵循 [GitHub 创建仓库的文档](https://docs.github.com/en/get-started/quickstart/create-a-repo)。

## 创建一个要发布的模块

与许多其他编程语言不同，Go 模块直接从其所在的源代码库中发布，而不是从独立的软件包库中发布。这使用户更容易找到他们代码中引用的模块，也使模块维护者更容易发布他们模块的新版本。在本节中，你将创建一个新的模块，然后将其发布给其他用户使用。

为了开始创建你的模块，作为先决条件的一部分，你将在你创建的空仓库上使用 `git clone` 下载初始仓库。这个版本库可以在你的电脑上的任何地方克隆，但许多开发者倾向于为他们的项目设置一个目录。在本教程中，你将使用一个名为 `projects` 的目录。

创建 `projects` 目录并进入：

```
mkdir projects
cd projects
```

在 `projects` 目录下，运行 `git clone` 将你的版本库克隆到你的电脑上：

```
git clone git@github.com:your_github_username/pubmodule.git
```

克隆模块将下载你的空模块到你的 `projects` 目录下的 `pubmodule` 目录。你可能会得到一个警告，说你克隆了一个空的版本库，但这并不值得担心：

```
Output
Cloning into 'pubmodule'...
warning: You appear to have cloned an empty repository.
```

接下来，进入到你下载到目录：

```
cd pubmodule
```

一旦你进入了模块目录，你将使用 `go mod init` 来创建你的新模块，并将版本库的位置作为模块名称传入。确保模块名称与版本库的位置一致是很重要的，因为当你的模块在其他项目中使用时，`go` 工具会找到下载的位置。

```
go mod init github.com/your_github_username/pubmodule
```

Go 会确认你的模块已经创建，让你知道它已经创建了 `go.mod` 文件:

```
Output
go: creating new go.mod: module github.com/your_github_username/pubmodule
```

最后，使用你喜欢的文本编辑器，如 `nano` ，创建并打开一个与你的版本库同名的文件：`pubmodule.go`。

```
nano pubmodule.go
```

这个文件的名字可以是任何东西，但使用与软件包相同的名字，可以使你在处理一个不熟悉的软件包时更容易知道从哪里开始。不过，包的名字本身应该与你的版本库名称相同。这样，当有人从你的包中引用一个方法或类型时，就会与版本库相匹配，如 `pubmodule.MyFunction` 。这将使他们更容易知道这个包来自哪里，以备他们以后需要参考。

接下来，给你的包添加一个 `Hello` 方法，它将返回字符串 `Hello, You!` 。这将是任何导入你的包的人都可以使用的功能。

projects/pubmodule/pubmodule.go

```go
package pubmodule

func Hello() string {
  return "Hello, You!"
}
```

你现在使用 `go mod init` 创建了一个新模块，模块名称与你的远程仓库（`github.com/your_github_username/pubmodule`）一致。你还为你的模块添加了一个名为 `pubmodule.go` 的文件，其中有一个名为 `Hello` 的函数，模块的用户可以调用。接下来，你将发布你的模块，使其对其他人可用。

## 发布模块

一旦你创建了一个本地模块，并准备将其提供给其他用户，就该发布你的模块了。由于 Go 模块是从它们所存放的代码库中发布的，你要把你的代码提交到你的本地Git仓库，然后推送到你的仓库`github.com/your_github_username/pubmodule`。

在提交代码到本地 Git 仓库之前，最好先确定不会提交任何你不期望提交的文件，这些文件在你推送代码到 GitHub 时就会公开发布。在 `pubmodule` 目录下使用 `git status` 命令会显示所有将要提交的文件和修改：

```
git status
```

输出结果将类似于这样:

```
Output
On branch main

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
go.mod
pubmodule.go
```

你应该看到 `go mod init ` 命令创建的 `go.mod ` 文件，以及你创建 `Hello ` 函数的 `pubmodule.go ` 文件。根据你创建版本库的方式，你可能有一个与此输出不同的分支名称。最常见的是，名字是 `main ` 或 `master`。

当你确定只有你要找的文件时，你就可以用 `git add` 将文件阶段性提交，用 `git commit` 将它们提交到版本库:

```
git add .
git commit -m "Initial Commit"
```

输出结果将类似于这样：

```
Output
[main (root-commit) 931071d] Initial Commit
 2 files changed, 8 insertions(+)
 create mode 100644 go.mod
 create mode 100644 pubmodule.go
```

Finally, use the `git push` command to push your module to the GitHub repository:

最后，使用 `git push` 命令将你的模块推送到 GitHub 仓库：

```
git push
```

输出结果将类似于这样：

```
Output
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (4/4), 367 bytes | 367.00 KiB/s, done.
Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:your_github_username/pubmodule.git
 * [new branch]      main -> main
```

运行 `git push` 命令后，你的模块将被推送到你的仓库，可以供其他人使用了。如果你没有发布任何版本，Go 将使用你的仓库的默认分支中的代码作为你的模块的代码。你的默认分支是否被命名为 `main`、`master ` 或其他名称并不重要，重要的是你的版本库的默认分支被设置为什么。

在这一节中，你把你创建的本地 Go 模块发布到 GitHub 仓库，让其他人可以使用。虽然你现在有了一个发布的模块，但维护公共模块的另一个部分是确保使用你模块的用户可以使用它的稳定版本。你很可能想在未来对你的模块进行修改和添加功能，但如果你在模块中不使用版本而进行这些修改，你可能会意外地破坏使用你的模块的人的代码。为了解决这个问题，当你在开发中达到一个新的里程碑时，你可以向你的模块添加版本。不过在添加新的版本时，一定要选择一个有意义的版本号，这样你的用户就能知道他们是否可以马上升级，是否安全。

## 语义版本管理

一个有意义的版本号可以让您的用户了解他们所交互的公共接口或API发生了多大的变化。Go 通过一种被称为 [semantic versioning](https://semver.org/) ，或简称为 `SemVer` 的版本方案来传达这些变化。(语义版本管理使用版本字符串来传达关于代码变化的意义，这就是语义版本管理的名字来源)。Go 的模块系统遵循 SemVer 来确定哪些版本比你当前使用的版本要新，以及某个模块的较新版本是否可以自动升级为安全版本。

语义版本管理为版本字符串中的每个数字赋予了意义。SemVer 中的典型版本包含三个主要数字：主版本、次版本和补丁版本。这些数字中的每一个都与". "组合在一起，形成版本，例如 `1.2.3` 。这些数字的顺序是：主要版本在前，次要版本在后，补丁版本在最后。这样，当看一个版本时，你可以看到哪个版本是较新的，因为特定位置的数字比以前的版本要高。例如，`2.2.3 ` 版本比 `1.2.3` 版本要新，因为主要版本更高。同样地，版本 `1.4.3`比 `1.2.10` 新，因为次要版本更高。尽管在补丁版本中 `10` 比 `3` 高，但次要版本 `4` 比 `2` 高，所以该版本优先。当版本字符串中的一个数字增加时，所有跟在它后面的其他版本部分都会重置为`0`。例如，增加次要版本 `1.3.10` 会导致 `1.4.0` ，增加主要版本 `2.4.1` 会导致 `3.0.0` 。

使用这些规则可以让 Go 在运行 `go get` 时决定使用哪个版本的模块。举个例子，假设你有一个项目使用 `1.4.3`版本的模块，`github.com/your_github_username/pubmodule`。如果你依赖 `pubmodule` 是稳定的，你可能只想自动升级补丁版本（即 `.3` ）。如果你运行命令 `go get -u=patch github.com/your_github_username/pubmodule` ，Go 会看到你想升级模块的补丁版本，并且只会寻找以`1.4`作为版本主要和次要部分的新版本。

在创建你的模块的新版本时，重要的是要考虑你的模块的公共 API 是如何变化的。语义版本字符串的每一部分都向你和你的用户传达了 API 变化的范围。这些类型的变化通常分为三个不同的类别，与版本的每个组成部分排成一列。最小的变化会增加补丁版本，中等规模的变化会增加次要版本，而最大的变化则会增加主要版本。使用这些类别来决定增加哪个版本号将帮助你避免破坏你自己的代码和其他依赖你的模块的人的代码。

## 主要版本号

SemVer 版本中的第一个数字是主版本号（`1.4.3` ）。主版本号是模块发布新版本时，需要考虑的最重要数字。一个主要的版本变化是用来表示你的公共 API 向后不兼容的变化。一个向后不兼容的变化是指你的模块中的任何变化，如果他们在没有做任何其他改变的情况下升级，会导致别人的程序崩溃。破坏可能意味着任何事情，包括因为函数名称改变而无法构建，或者因为库的工作方式改变而导致同一方法返回 `v1`而不是 `1`。但这只适用于你的公共 API，也就是说，任何出口的类型或方法，别人都可以使用。如果这个版本只包括你的库的用户不会注意到的改进，它不需要一个主要的版本变化。记住哪些变化符合这个类别的方法是，任何被认为是 "更新 "或 "删除 "的东西都是一个主要的版本增加。

**注意：**与 SemVer 中其他类型的数字不同，主要版本 `0` 有一个额外的特殊意义。主版本 `0` 被认为是 "开发中 "的版本。任何主版本为 `0` 的 SemVer 都不被认为是稳定的，任何东西都可能在 API 中发生变化。当你创建一个新模块时，最好从主版本 `0` 开始，只更新次版本和补丁版本，直到你完成模块的初步开发。一旦你的模块的公共 API完成了变化，并被认为对你的用户来说是稳定的，那么就可以从 `1.0.0` 版本开始。

以下面的代码为例，说明一个主要的版本变化可能是什么样子的。你有一个叫做 `UserAddress` 的函数，目前接受一个`string` 作为参数，并返回一个`string`：

```go
func UserAddress(username string) string {
	// return user address as a string
}
```

虽然该函数目前返回一个 `字符串`，但你可能认为如果该函数返回一个 `结构体`，如 `*Address`，对你和你的用户来说会更好。这样你就可以包括已经拆开的额外数据，如邮政编码：

```go
type Address struct {
	Address    string
	PostalCode string
}

func UserAddress(username string) *Address {
	// return user address and postal code struct
}
```

这将是一个主要版本变化的例子，因为它需要你的用户对他们自己的代码进行修改才能使用它。如果你决定完全删除`UserAddress` 也是如此，因为你的用户需要更新他们的代码来使用这个替换。

另一个主要版本变化的例子是给 `UserAddress` 函数添加一个新的参数，即使它仍然返回一个`字符串`：

```go
func UserAddress(username string, uppercase bool) string {
	// return user address as a string, uppercase if bool is true
}
```

由于这一变化也需要你的用户更新他们的代码，如果他们使用 `UserAddress` 函数的话，这也需要一个主要的版本增加。

不过，并不是所有你对你的代码所做的改变都会如此剧烈。有时你会对你的公共 API 进行修改，增加新的函数或值，但不改变任何现有的。

## 次要版本号

SemVer 版本中的第二个数字是次要版本号（`1.4.3`）。次要版本的变化是用来表示你的公共 API 向后兼容的变化。向后兼容的变化是指任何不影响目前使用你的模块的代码或项目的变化。与主要版本号类似，这只影响你的公共 API。一个记住哪些变化符合这个类别的方法是，可能是任何被认为是 "增加"，但不是 "更新 "的东西。

使用主要版本号的同一个例子，设想你有一个名为 `UserAddress` 的方法，返回一个 `字符串`：

```go
func UserAddress(username string) string {
	// return user address as a string
}
```

不过这一次，你没有更新`UserAddress`以返回 `*Address` ，而是决定添加一个全新的方法，名为`UserAddressDetail`：

```go
type Address struct {
	Address    string
	PostalCode string
}

func UserAddress(username string) string {
	// return user address as a string
}

func UserAddressDetail(username string) *Address {
	// return user address and postal code struct
}
```

添加这个新的`UserAddressDetail`功能不需要你的用户在更新到这个版本的模块时进行修改，所以这将被视为一个小的版本号增加。他们可以继续使用 `UserAddress`，如果他们愿意包括 `UserAddressDetail`  的额外信息，只需要更新他们的代码。

不过，公共 API 的变化可能不是你发布新版本模块的唯一时机。漏洞是软件开发中不可避免的一部分，补丁版本号是为了掩盖这些漏洞。

## 补丁版本号

补丁版本号是 SemVer 版本中的最后一个数字（`1.4.3`）。补丁版本的变化是任何**不影响**模块的**公共 API **的变化。不影响模块的公共 API 的变化往往是诸如错误修复或安全修复。再次使用前面例子中的 `UserAddress ` 函数，假设你发布的模块在函数返回的 `字符串` 中缺少部分地址。如果你发布一个新版本的模块来修复这个错误，它只会增加补丁的版本。这个版本不会包括对用户如何使用 `UserAddress` 公共 API 的任何改变，只包括返回数据的正确性。

正如你在本节中所看到的，谨慎地选择新的版本号是赢得用户信任的一个重要方法。使用语义版本号可以向用户展示更新到新版本所需的工作量，而且你不会意外地用一个破坏他们程序的更新来给他们带来惊喜。在考虑了你对你的模块所做的修改，并确定了要使用的下一个版本号之后，你就可以发布新的版本，让你的用户使用。

## 发布一个新的模块版本

在你发布新版本的模块之前，你需要用你计划进行的改动来更新你的模块。如果没有任何改动，你将无法确定要增加语义版本的哪一部分。对于本教程中的模块，你将增加一个新的`Goodbye`方法来补充`Hello` 方法，然后你将发布这个新版本供用户使用。

首先，打开`pubmodule.go`文件，将新的 `Goodbye` 方法添加到你的公共API：

pubmodule/pubmodule.go

```go
package pubmodule

func Hello() string {
  return "Hello, You!"
}

func Goodbye() string {
  return "Goodbye for now!"
}
```

一旦你保存了你的改动，你可以通过运行 `git status` 来检查哪些改动会被提交：

```
git status
```

输出结果将与此类似，显示你的模块中唯一的变化是你在 `pubmodule.go` 中添加的方法。

```
Output
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
modified:   pubmodule.go

no changes added to commit (use "git add" and/or "git commit -a")
```

接下来，将修改添加到暂存文件中，并通过 `git add` 和 `git commit` 将修改提交到本地仓库：

```
git add .
git commit -m "Add Goodbye method"
```

输出结果将与此类似：

```
Output
[main 3235010] Add Goodbye method
 1 file changed, 4 insertions(+)
```

提交修改后，你需要把它们推送到你的 GitHub 仓库。在一个较大的软件项目中，或者在与其他开发者合作的项目中，这一步骤通常会略有不同。当开发一个新功能时，一个开发者会创建一个 Git 分支，将修改放入其中，直到新功能稳定并准备发布。一旦如此，另一个开发者就会审查该分支中的修改，以增加第二双眼睛，发现第一个开发者可能错过的问题。一旦审查完成，该分支就会被合并到默认分支（如`master`或`main`）。在两次发布之间，默认分支将积累这些类型的变化，直到发布新版本的时候。

由于你的模块没有经历这个过程，推送你所做的修改到版本库将模拟修改的积累：

```
git push
```

输出结果将类似于这样：

```
Output
numerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 369 bytes | 369.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:your_github_username/pubmodule.git
   931071d..3235010  main -> main
```

输出显示新代码已经准备好供用户在默认分支中使用。

到此为止，你所做的一切都与最初发布模块时一样。然而，现在发布新版本的一个重要部分出现了：选择一个新的版本号。

如果你看一下你对模块所做的改动，对公共 API 的唯一改动（或者说是任何改动）是给你的模块添加了 `Goodbye` 方法。由于用户可以从只有 `Hello` 功能的上一个版本中进行更新，而不需要在自己身上做任何改动，因此这种改动将是一种向后兼容的改动。在语义版本管理中，对公共 API 的向后兼容的改变意味着增加次要版本号。不过，这是你的模块所发布的第一个版本，所以没有以前的版本可以增加。如果你认为 `0.0.0` 是 "无版本"，那么增加次要版本将导致你的模块的下一个版本为`0.1.0`。

现在你有了一个版本号来发布你的模块，你可以用它和 Git 标签配对来发布一个新的版本。当开发者使用 Git 来记录他们的源代码时，即使是 Go 以外的语言，一个常见的惯例是使用 Git 的标签来记录某个特定版本的代码被发布。这样，如果他们需要对旧版本进行修改，就可以使用该标签。由于 Go 已经从源码库中下载了模块，它通过使用这些相同的版本标签来利用这种做法。

要使用这些标签发布你自己模块的新版本，你可以用 `git tag` 命令来标记你要发布的代码。作为 `git tag`命令的一个参数，你还需要提供版本标签。要创建版本标签，以前缀 `v` 开始，代表版本，然后紧接着添加你的SemVer。就你的模块而言，你的最终版本标签将是`v0.1.0` 。现在，运行 `git tag` 来给你的模块加上版本标签：

```
git tag v0.1.0
```

一旦版本标签被添加到本地，你仍然需要将该标签推送到你的 GitHub 仓库，你可以使用 `git push` 和 `origin` 来完成。

```
git push origin v0.1.0
```

在 `git push` 命令成功后，你会看到一个新的标签，`v0.1.0`，已经被创建：

```
Output
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:your_github_username/pubmodule.git
 * [new tag]         v0.1.0 -> v0.1.0
```

上面的输出显示你的标签已经被推送，你的 GitHub 仓库有一个新的 `v0.1.0` 标签供你的模块用户参考。

现在你已经用`git tag`发布了新版本的模块，每当用户运行`go get`获取你的模块的最新版本时，它将不再基于默认分支的最新提交哈希值下载版本。一旦一个模块有了发布的版本，`go` 工具将开始使用这些版本来决定更新模块的最佳方式。与语义上的版本控制相搭配，这使你能够迭代和改进你的模块，同时也为你的用户提供了一致和稳定的体验。

## 总结

在本教程中，你创建了一个公共 Go 模块，并将其发布到 GitHub 仓库中，以便其他人可以使用。你还使用语义版本学来确定你的模块的最佳版本号。最后，你扩展了你的模块的功能，并使用语义版本管理，发布了新的版本，相信你再也不会破坏依赖它的程序了。

如果你想了解有关语义版本管理的更多信息，包括如何为你的版本添加数字以外的信息，[Semantic Versioning website](https://semver.org/) 有很详细的介绍。Go 文档中也有一个 [module version numbering](https://golang.org/doc/modules/version-numbers) 页面，解释了 Go 如何具体使用SemVer。

关于 Go 模块的更多信息，Go 项目有[一系列博文](https://blog.golang.org/using-go-modules)，详细介绍了 Go 工具如何与模块互动和理解模块。Go 项目还在[Go模块参考](https://golang.org/ref/mod)中为 Go 模块提供了非常详细的技术参考。

本教程也是[DigitalOcean](https://www.digitalocean.com/) [How to Code in Go](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go)系列的一部分。该系列涵盖了许多 Go 主题，从首次安装 Go 到如何使用该语言本身。