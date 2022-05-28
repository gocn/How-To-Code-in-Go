### 介绍

有时，你的代码需要当前程序的基础上增加更多的功能。在这些情况下，你可以使用软件包来丰富你的程序。在 Go 中, 一个包表示磁盘上单个目录中的所有文件。包可以定义可以在其他 Go 文件或包中引用的函数、类型和接口。

本教程将带你来完成软件包的安装, 导入和重命名。

## 标准库包

标准库是 Go 附带的一组软件包。这些软件包包含许多用于编写现代软件的基本模块。例如， [`fmt`](https://golang.org/pkg/fmt) 软件包包含用于格式和打印字符串的基本功能。 [`net/http`](https://golang.org/pkg/net/http/) 软件包包含允许开发人员创建 Web 服务，通过`HTTP` 协议发送和检索数据的功能，等等。

为了利用软件包中的功能，你需要使用 `import` 语句访问软件包。`import` 语句由 `import` 关键字以及软件包的名称组成。

例如，在 GO 程序中 `random.go` 文件。你可以导入 `math/rand` 包来生成随机数：

``` go
import "math/rand"
```

当我们导入一个包时，我们把它在当前程序中作为一个单独的名称空间来使用。这意味着我们必须像 `package.function` 调用其中的函数。

实际上，`math/rand` 软件包的功能看起来像这些示例：

- `rand.Int()` 调用函数返回随机整数。
- `rand.Intn()` 调用函数将随机元素从 `0` 返回到所提供的指定数字。

让我们创建一个 `for` 循环，以显示我们如何在随机过程中调用 `math/rand` 软件包的函数。

random.go

``` go
package main

import "math/rand"

func main() {
  for i := 0; i < 10; i++ {
    println(rand.Intn(25))
  }
}
```

该程序首先在第三行中导入 `math/rand` 软件包，然后移至将运行10次的循环中。在循环中，程序将打印一个在 `0` 到 `25` 范围内的随机整数。其中, 整数 `25` 是作为其参数传递给 `rand.Intn()`。

当我们使用 `go run random.go` 来运行程序时，我们将收到 10 个随机整数作为输出。因为这些是随机的，所以每次运行程序时，你都可能会获得不同的整数。输出看起来像这样：

``` shell
# Output
6
12
22
9
6
18
0
15
6
0
```

整数永远不会低于 0 或 24 以上。

当需要导入多个包时，你可以使用 `()` 来创建一个块。通过使用块，可以避免在每行上重复 `import` 关键字。这将使你的代码看起来更整洁

random.go

``` go
import (
  "fmt"
  "math/rand"
)

```

为了利用新增的软件包，我们现在可以格式化输出并打印出循环中每次迭代生成的随机数：

random.go

``` go
package main

import (
  "fmt"
  "math/rand"
)

func main() {
  for i := 0; i < 10; i++ {
    fmt.Printf("%d) %d\n", i, rand.Intn(25))
  }
}

```

现在，当我们运行程序时，我们将收到看起来像这样的输出：

``` shell
# Output
0) 6
1) 12
2) 22
3) 9
4) 6
5) 18
6) 0
7) 15
8) 6
9) 0
```

在本节中，我们学会了如何导入软件包并使用它们来编写更复杂的程序。到目前为止，我们只使用了标准库中的软件包。接下来，让我们看看如何安装和使用其他开发人员编写的软件包。

## 安装软件包

虽然标准库包含了许多出色且有用的软件包，但它们的设计是通用的，本质上不是特定的。这使开发者可以根据自己的特定需求在标准库之上构建自己的软件包。

GO 工具链带有 `go get` 命令。此命令使你可以将第三方软件包安装到本地开发环境中，并且将这些软件包应用到你的程序中。

使用 `go get` 来安装第三方软件包时，通常可以通过其规范路径引用软件包。这个路径也可能是通往公共项目的途径，该项目托管在诸如 GitHub 之类的代码存储库中。因此，如果要导入 [`flect`](https://github.com/gobuffalo/flect) 软件包，则将使用完整的规范路径：

``` shell
go get github.com/gobuffalo/flect
```

在这种情况下，使用 `go get` 工具将在 GitHub 上找到软件包，并将其安装到你的 [`$Gopath`](https://www.digitalocean.com/community/tutorials/understanding-the-gopath) 中。

对于此示例，代码将安装在此目录中：

``` shell
$GOPATH/src/github.com/gobuffalo/flect
```

原始作者通常会更新软件包，以解决  bug 或添加新功能。发生这种情况时，你可能需要使用该软件包的最新版本来利用新功能或已解决的 bug。要更新软件包，你可以使用 `go get` 命令使用 `-u` 标志：

``` shell
go get -u github.com/gobuffalo/flect
```

如果在本地找不到该软件包，此命令也将安装该软件包。如果已经安装了它，Go 将尝试将软件包更新为最新版本。

`go get` 命令始终检索可用的包装的最新版本。但是，可能还会对该软件包的之前的版本进行更新，这些版本仍然比你使用的更新，并且对你的程序中的更新非常有用。要检索包装的特定版本，你需要使用一个软件包管理工具，例如[Go Modules](https://github.com/golang/go/wiki/Modules)。

从 GO 1.11 开始，使用 Go Modules 来管理要导入的软件包的哪个版本。软件包管理的主题超出了本文的范围，但是你可以在[on the Go Modules GitHub page](https://github.com/golang/go/wiki/Modules)上阅读有关它的更多信息。

## 使用别名的方式导入软件包

如果你的本地软件包已经命名为与你正在使用的第三方软件包相同的包名时，则可能需要更改软件包名称。当发生这种情况时，使别名导入的方式是处理软件包名冲突的最佳方法。你可以通过将 `alias` 名称放在导入的软件包的前面来修改包装及其功能的名称及其功能。

该声明的结构看起来像这样：

``` go
import another_name "package"
```

在此示例中，在 `random.go` 程序文件中修改 `fmt` 软件包的名称。我们将 `fmt` 的包名称更改为 `f`，以缩写它。我们的修改程序看起来像这样：

random.go

``` go
package main

import (
 f "fmt"
  "math/rand"
)

func main() {
  for i := 0; i < 10; i++ {
    f.Printf("%d) %d\n", i, rand.Intn(25))
  }
}
```

在程序中，我们现在将 `Printf` 函数称为 `f.Printf`，而不是 `fmt.Printf`。

虽然其他语言喜欢以别名的方式命名包以便于在程序中更加容易使用，但 GO 却不是。例如，与 `fmt` 软件包与 `f` 相反， [style guide](https://github.com/golang/go/wiki/CodeReviewComments#import-dot) 更加倾向于一致。

在重命名导入包以避免命名冲突时，你应该重命名本地导入的软件包或特定的项目中导入的包。例如，如果你有一个名为 `Strings` 的本地软件包，并且还需要导入称为 `strings` 的系统软件包，你应该重命名本地软件包而不是系统软件包。只要有可能，最好避免完全命名冲突。

在本节中，我们了解了如何以别名的方式导入软件包以避免与我们计划中的其他导入冲突。重要的是要记住，程序的可读性和清晰度很重要，因此你只能使用别名使代码更可读或何时需要避免命名冲突。

## 格式化导入

通过格式化导入，你可以将软件包分为特定的顺序，以使你的代码更加一致。此外，当惟一改变的是导入的排序顺序时，这将防止发生随机提交。由于格式化导入将防止随机提交，因此这将防止不必要的代码混乱和混淆代码审查。

大多数编辑器将自动为你格式化导入，或者让你配置编辑器以使用 [goimports](https://godoc.org/golang.org/x/tools/cmd/goimports) 工具。在编辑器中使用 `goimports` 被认为是标准实践，因为尝试手动维护导入的排序顺序可能是乏味的，而且容易出错。此外，如果进行了任何样式更改，则将更新 `goimports` 以反映这些样式更改。这样可以确保你和任何在代码上工作的人都将在你的 import 块中具有一致的样式。

这是格式化之前的示例导入块可能的样子：

``` go
import (
  "fmt"
  "os"
  "github.com/digital/ocean/godo"
  "github.com/sammy/foo"
  "math/rand"
  "github.com/sammy/bar"
)

```

运行 `goimport` 工具(或使用已安装它的大多数编辑器，保存文件将为你运行)，现在你将具有以下格式：

``` go
import (
  "fmt"
  "math/rand"
  "os"

  "github.com/sammy/foo"
  "github.com/sammy/bar"

  "github.com/digital/ocean/godo"
)
```

请注意，它首先将所有标准库软件包分组，然后将第三方软件包与空白行分组。这使得更容易阅读和了解正在使用哪些软件包。

在本节中，我们了解到，使用 `goimports` 将保持我们所有导入块的正确格式，并防止在处理相同文件在开发人员之间产生不必要的代码混乱。

## 总结

当我们导入软件包时，我们可以调用未内置的功能。有些软件包是随着 GO 安装的标准库的一部分，有些软件包将通过 `go get` 来安装。

使用软件包可以使我们在利用现有代码时使程序更加健壮和强大。我们还可以为自己和其他程序员 [创建自己的软件包](https://www.digitalocean.com/community/tutorials/how-to-write-packages-in-go)，以便将来使用。
