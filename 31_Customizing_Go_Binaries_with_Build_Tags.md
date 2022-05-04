# 用构建标签定制 Go 二进制文件

## 简介

在 Go 中，*构建标签* 或 *构建标签* 是添加到一段代码中的标识符，它决定了该文件在 `build` 过程中何时应被包含在一个包中。这允许你从同一源代码中构建不同版本的 Go 应用程序，并以快速和有组织的方式在它们之间进行切换。许多开发者使用构建标签来改善构建跨平台兼容的应用程序的工作流程，例如需要修改代码以考虑不同操作系统之间的差异的程序。构建标签还可用于[集成测试](https://en.wikipedia.org/wiki/Integration_testing)，允许你在集成代码和带有[Mock 服务](https://en.wikipedia.org/wiki/Mock_object)的代码之间快速切换，并用于应用程序内不同级别的功能集。

让我们以不同的客户功能集的问题为例。在编写一些应用程序时，你可能想控制在二进制文件中包括哪些功能，例如一个提供**免费**、**专业**和**企业**级别的应用程序。当客户在这些应用程序中增加他们的订阅级别时，更多的功能将被解锁并可用。为了解决这个问题，你可以维护独立的项目，并试图通过使用 `import` 语句来保持它们的同步性。虽然这种方法可行，但随着时间的推移，它将变得乏味和容易出错。另一种方法是使用构建标签。

在本文中，您将使用 Go 中的构建标签来生成不同的可执行二进制文件，这些文件提供了一个示例应用程序的免费、专业和企业功能集。每一个都有不同的功能集，其中免费版本是默认的。

## 先决条件

要遵循本文的例子，你将需要。

* 按照 [如何安装 Go 和设置本地编程环境](https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-go)设置的 Go 工作区。

## 构建免费版本

让我们从构建应用程序的免费版本开始，因为当运行 `go build` 而没有任何构建标签时，它将是默认的。稍后，我们将使用构建标签来有选择地将其他部分添加到我们的程序中。

在 `src` 目录下，用你的应用程序的名字创建一个文件夹。本教程将使用`app`：

```shell
mkdir app
```

移动到这个文件夹中：

```shell
cd app
```

接下来，在你选择的文本编辑器中建立一个新的文本文件，名为 `main.go`：

```go
nano main.go
```

现在，我们将定义该应用程序的免费版本。在以下内容中加入`main.go`：

<center>main.go</center>

```go
package main

import "fmt"

var features = []string{
  "Free Feature #1",
  "Free Feature #2",
}

func main() {
  for _, f := range features {
    fmt.Println(">", f)
  }
}
```
在这个文件中，我们创建了一个程序，声明了一个名为 `features` 的[切片](https://www.digitalocean.com/community/tutorials/understanding-arrays-and-slices-in-go#slices)，它容纳了两个[字符串](https://www.digitalocean.com/community/tutorials/an-introduction-to-working-with-strings-in-go)，代表我们 Free 应用程序的特征。应用程序中的 `main()` 函数使用一个 `for` [循环](https://www.digitalocean.com/community/tutorials/how-to-construct-for-loops-in-go#looping-through-sequential-data-types-with-rangeclause) `range` 通过  `features` 切片，并将所有可用的功能打印到屏幕上。

保存并退出该文件。现在这个文件已经保存了，在文章的其余部分，我们将不再需要编辑它。相反，我们将使用构建标签来改变我们将从中构建的二进制文件的功能特性。

构建和运行程序：

```shell
go build
./app
```

你将收到以下输出：

```shell
> Free Feature #1
> Free Feature #2
```
该程序已经打印出我们的两个免费功能，完成了我们应用程序的免费版本。
到目前为止，你创建了一个具有非常基本功能集的应用程序。接下来，你将建立一种方法，在构建时向应用程序添加更多的功能。

## 用 `go build` 添加专业功能

到目前为止，我们避免了对 `main.go` 的修改，模拟了一个常见的生产环境，在这个环境中，需要在不改变和可能破坏主代码的情况下增加代码。由于我们不能编辑 `main.go` 文件，我们需要使用另一种机制，使用构建标签将更多的功能注入到 `features` 切片中。

让我们创建一个名为 `pro.go` 的新文件，该文件将使用 `init()` 函数将更多的特特性追加到 `features` 切片中：

```shell
nano pro.go
```

一旦编辑器打开了该文件，添加以下几行：

<center>pro.go</center>

```go
package main

func init() {
  features = append(features,
    "Pro Feature #1",
    "Pro Feature #2",
  )
}
```

在这段代码中，我们使用 `init()` 在我们应用程序的 `main()` 函数前运行代码，然后使用 `append()` 将专业功能添加到 `features`切片中。保存并退出该文件。

编译并运行应用程序，使用 `go build`:

```shell
go build
```

由于现在我们的当前目录中有两个文件（ `pro.go` 和 `main.go` ），`go build`将从这两个文件创建一个二进制文件。执行这个二进制文件：

```shell
./app
```

这将为你提供以下功能组合：

```shell
> Free Feature #1
> Free Feature #2
> Pro Feature #1
> Pro Feature #2
```
该应用程序现在同时包括专业版和免费版的功能。然而，这并不可取：由于版本之间没有区别，免费版现在包括了本应只有专业版才有的功能。为了解决这个问题，你可以加入更多的代码来管理应用程序的不同层级，或者你可以使用构建标签来告诉 Go 工具链哪些`.go`文件需要构建，哪些需要忽略。让我们在下一步中添加构建标签。
## 添加构建标签

你现在可以使用构建标签来区分你的应用程序的专业版本和免费版本。

让我们先来看看构建标签是什么样子的：

```go
// +build tag_name
```

将这行代码作为软件包的第一行，并将 `tag_name` 替换为你的构建标签的名称，你将把这个软件包标记为可以选择性地包含在最终二进制文件中的代码。让我们来看看这个操作，在 `pro.go` 文件中添加一个构建标签，告诉 `go build` 命令忽略它，除非指定标签。在你的文本编辑器中打开该文件：

```shell
nano pro.go
```

然后添加以下突出显示的一行：

<center>pro.go</center>

```go
// +build pro

package main

func init() {
  features = append(features,
    "Pro Feature #1",
    "Pro Feature #2",
  )
}
```
在 `pro.go` 文件的顶部，我们添加了 `// +build pro`，后面是一个空白的换行。这个尾部的换行是必须的，否则 Go 会将其解释为一个注释。构建标签的声明也必须在 `.go` 文件的最顶端。任何东西，甚至是评论，都不能在构建标签之上。
`+build` 声明告诉 `go build` 命令，这不是一个注释，而是一个构建标签。第二部分是 `pro` 标签。通过在 `pro.go` 文件的顶部添加这个标签，`go build` 命令现在将只包括有 `pro` 标签的 `pro.go` 文件。

编译并再次运行应用程序：

```shell
go build
./app
```

你将收到以下输出：

```shell
> Free Feature #1
> Free Feature #2
```
由于 `pro.go` 文件需要有 `pro` 标签，所以该文件被忽略，应用程序在没有标签的情况下进行编译。
当运行 `go build` 命令时，我们可以使用 `-tags` 标志，通过添加标签本身作为参数，有条件地将代码纳入编译后的源代码中。让我们对 `pro` 标签这样做：

```go
go build -tags pro
```

这将输出以下内容：

```shell
> Free Feature #1
> Free Feature #2
> Pro Feature #1
> Pro Feature #2
```
现在，我们只有在使用 `pro` 构建标签构建应用程序时才能获得额外的功能。
如果只有两个版本，这很好，但当你加入更多标签时，事情就变得复杂了。为了在下一步添加我们应用程序的企业版，我们将使用多个构建标签，用布尔逻辑连接起来。

## 构建标签布尔逻辑

当一个 Go 包中有多个构建标签时，这些标签会使用[布尔逻辑](https://www.digitalocean.com/community/tutorials/understanding-boolean-logic-in-go)相互作用。为了证明这一点，我们将同时使用 `pro` 标签和 `enterprise` 标签来添加我们应用程序的企业级。

为了建立一个企业级二进制文件，我们将需要包括默认功能、专业级功能和一套新的企业级功能。首先，打开一个编辑器并创建一个新文件，`enterprise.go`，它将添加新的企业级功能：

```shell
nano enterprise.go
```

`enterprise.go` 的内容将看起来与 `pro.go`几乎相同，但将包含新的功能。在该文件中添加以下几行：

<center>enterprise.go</center>

```go
package main

func init() {
  features = append(features,
    "Enterprise Feature #1",
    "Enterprise Feature #2",
  )
}
```

保存并退出该文件。

目前 `enterprise.go` 文件没有任何构建标签，正如你在添加 `pro.go` 时了解到的，这意味着这些功能将在执行 `go build` 时被添加到免费版本中。对于 `pro.go`，你在文件的顶部添加了 `// +build pro` 和一个换行符，以告诉 `go build` 只有在使用 `tags pro` 时才应包含它。在这种情况下，你只需要一个构建标记就能达到目的。然而，当添加新的企业版功能时，你首先必须同时拥有专业版功能。

让我们先在 `enterprise.go` 中添加对 `pro` 构建标签的支持。用你的文本编辑器打开该文件：

```shell
nano enterprise.go
```

接下来，在 `package main` 声明前添加构建标签，并确保在构建标签后包含一个换行符：

<center>enterprise.go</center>

```go
// +build pro

package main

func init() {
  features = append(features,
    "Enterprise Feature #1",
    "Enterprise Feature #2",
  )
}
```
保存并退出该文件。
编译并运行没有任何标签的应用程序：

```shell
go build
./app
```

你将收到以下输出：

```shell
> Free Feature #1
> Free Feature #2
```
企业版的功能在免费版中不再显示。现在让我们添加 `pro` 构建标签，再次构建并运行该应用程序：
```shell
go build -tags pro
./app
```

你将收到以下输出：

```shell
> Free Feature #1
> Free Feature #2
> Enterprise Feature #1
> Enterprise Feature #2
> Pro Feature #1
> Pro Feature #2
```
这仍然不完全是我们需要的。现在，当我们试图构建专业版时，企业版的功能出现了。为了解决这个问题，我们需要使用另一个构建标签。但与 `pro` 标签不同的是，我们现在需要确保 `pro` 和 `enterprise` 功能都是可用的。
Go 构建系统通过允许在构建标签系统中使用一些基本的布尔逻辑来说明这种情况。

让我们再次打开 `enterprise.go`:

```shell
nano enterprise.go
```

在`pro`标签的同一行添加另一个构建标签，`enterprise`:

<center>enterprise.go</center>

```go
// +build pro enterprise

package main

func init() {
  features = append(features,
    "Enterprise Feature #1",
    "Enterprise Feature #2",
  )
}
```
保存并关闭该文件。
现在让我们用新的 `enterprise` 构建标签来编译和运行该应用程序。

```shell
go build -tags enterprise
./app
```

这将得到以下信息：

```shell
> Free Feature #1
> Free Feature #2
> Enterprise Feature #1
> Enterprise Feature #2
```
现在我们已经失去了专业功能。这是因为当我们在 `.go` 文件中的同一行放置多个构建标签时，`go build` 会将它们解释为使用 `OR` 逻辑。 加上 `// +build pro enterprise`一行，如果存在 `pro` 构建标签**或** `enterprise` 构建标签，`enterprise.go` 文件将被构建。 我们需要正确设置构建标签，要求**两个同时**，并使用 `AND` 逻辑来代替。
如果我们不把这两个标签放在同一行，而是把它们放在不同的行上，那么 `go build` 将使用 `AND` 逻辑来解释这些标签。

再次打开 `enterprise.go`，让我们把构建标签分成多行。

<center>enterprise.go</center>

```go
// +build pro
// +build enterprise

package main

func init() {
  features = append(features,
    "Enterprise Feature #1",
    "Enterprise Feature #2",
  )
}
```
现在用新的 `enterprise` 构建标签编译和运行应用程序：
```shell
go build -tags enterprise
./app
```

你将收到以下输出：

```shell
> Free Feature #1
> Free Feature #2
```
仍然没有达到目的。因为 `AND` 语句需要两个元素都被认为是 `true`，我们需要同时使用 `pro` 和 `enterprise` 构建标签。
让我们再试一次：

```shell
go build -tags "enterprise pro"
./app
```

你将收到以下输出：

```shell
> Free Feature #1
> Free Feature #2
> Enterprise Feature #1
> Enterprise Feature #2
> Pro Feature #1
> Pro Feature #2
```
现在，我们的应用程序可以从同一个源树中以多种方式构建，并相应地解锁应用程序的功能。
在这个例子中，我们使用了一个新的 `// +build` 标签来表示 `AND` 逻辑，但也有其他方法可以用构建标签表示布尔逻辑。下表是构建标签的其他语法格式的一些例子，以及它们的布尔等价物：

|构建标签语法|构建标签示例|布尔声明|
|:----|:----|:----|
|空格分隔的元素|// +build pro enterprise|pro OR enterprise|
|逗号分隔的元素|// +build pro,enterprise|pro AND enterprise|
|感叹号元素|// +build !pro|NOT pro|

## 总结

在本教程中，你使用构建标签来控制你的哪些代码被编译到二进制中。首先，你声明了构建标签并使用它们与 `go build`，然后你用布尔逻辑组合了多个标签。然后，你建立了一个程序，代表了免费版、专业版和企业版的不同功能集，显示了构建标签对项目的强大控制能力。

如果你想了解更多关于构建标签的信息，请看一下[Golang 的相关文档](https://golang.org/pkg/go/build/#hdr-Build_Constraints)，或者继续探索我们的[如何在 Go 中编码系列](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go)。

 

