# 如何在 Go 中编写包

一个包由同一目录下的 Go 文件组成的，并且在文件开头有相同的包声明。你可以从包中加入额外的功能，使你的程序更加复杂。有些包可以通过 Go 标准库获得，因此在安装 Go 时就已经安装了。其他的可以用 Go 的`go get`命令来安装。你也可以通过在同一目录下创建 Go 文件来建立你自己的 Go 包，你可以通过使用必要的包声明来分享代码。

本教程将指导你如何编写 Go 包，以便在其他编程文件中使用。
## 前提条件

- 按照[如何安装和设置Go的本地编程环境]({{< relref "/docs/01-How_To_Install_Go_and_Set_Up_a_Local Programming_Environment_on_Ubuntu_18.04_DigitalOcean.md" >}})系列教程中的一个教程设置 Go 编程环境。按照本地编程环境教程中的步骤5创建你的 Go 工作区。要遵循本文的例子和命名规则，请阅读第一节「编写和导入软件包」。
- 为了加深你对 GOPATH 的了解，请阅读文章[了解 GOPATH]({{< relref "/docs/05-Understanding_the_GOPATH.md" >}})。

## 编写和导入软件包

编写包就像编写任何其他 Go 文件一样，包可以包含函数、[类型]({{< relref "/docs/07-Understanding_Data_Types_in_Go.md" >}})和[变量](https://www.digitalocean.com/community/tutorials/how-to-use-variables-and-constants-in-go#understanding-variables)的定义，然后可以在其他 Go 程序中使用。

在我们创建一个新的包之前，我们需要进入我们的 Go 工作区。这通常是在我们的`gopath`下。对于这个例子，本教程中我们将把包称为`greet`。为了做到这一点，在我们的项目空间下的`gopath`中创建了一个名为`greet`的目录。当使用 Github 作为代码库，组织名称为`gopherguides`，想在此组织下创建`greet`包，那么我们的目录会是这样的：

```
└── $GOPATH
    └── src
        └── github.com
            └── gopherguides
```

`greet`目录在`gopherguides`目录中：

```
└── $GOPATH
    └── src
        └── github.com
            └── gopherguides
                └── greet
```

最后，我们可以添加我们目录中的第一个文件。通常的做法是，包中的 `主要`或 `入口` 文件是以目录名来命名的。在这种情况下，将在`greet`目录下创建一个名为`greet.go`的文件：

```
└── $GOPATH
    └── src
        └── github.com
            └── gopherguides
                └── greet
                    └── greet.go
```

创建了文件后，我们就可以开始编写我们想要重复使用或在不同项目中共享的代码。在本例中，我们将创建一个打印出 `Hello World`的 `Hello` 的函数。

在文本编辑器中打开 `greet.go` 文件，增加如下代码：

```go
package greet

import "fmt"

func Hello() {
	fmt.Println("Hello, World!")
}
```

让我们把这个文件分解一下，每个文件中第一行需要是所处的`包`名称。因为你在`greet`包里，所以通过使用`package`关键字，后面加包的名称：

```go
package greet
```

这将告诉编译器把文件中的所有内容作为`greet`包的一部分。

接下来，你用 `import` 语句声明你需要使用的任何其他包。在这个文件中你只使用一个包，`fmt`包：

```go
import "fmt"
```

最后，你创建函数`Hello`，它将使用`fmt`包来打印出`Hello, World!`。

```go
func Hello() {
	fmt.Println("Hello, World!")
}
```

现在已经编写了`greet`包，可以在你创建的任何其他包中使用它。让我们创建一个新的包，在其中使用`greet`包。

接下来创建一个名为`example`的包，这意味着需要一个名为`example`的目录。在`gopherguides`中创建这个包，所以目录结构看起来像这样：

```
└── $GOPATH
    └── src
        └── github.com
            └── gopherguides
                    └── example
```

现在你有了新包的目录，可以创建入口文件。因为这将是一个可执行的程序，最好的做法是将入口文件命名为`main.go`：

```
└── $GOPATH
    └── src
        └── github.com
            └── gopherguides
                └── example
                    └── main.go
```

在文本编辑器中，打开`main.go`，添加以下代码来调用`greet`包：

```go
package main

import "github.com/gopherguides/greet"

func main() {
	greet.Hello()
}
```

因为正在导入一个包，通过用点符号来调用指定包的函数。*点符号*是指在使用的包的名称和想使用的包中资源之间加一个句号`.`。例如，在`greet`包中，有`Hello`函数作为一个资源。如果想调用该资源，可以使用 `greet.Hello()` 的形式。

现在，可以打开终端，在命令行上运行该程序：

```bash
go run main.go
```

完成后，你将收到以下输出：

```Output
Hello, World!
```

为了解如何在包中使用变量，让我们在`greet.go`文件中添加一个变量定义：

```go
package greet

import "fmt"

var Shark = "Sammy"

func Hello() {
	fmt.Println("Hello, World!")
}
```

接下来，打开`main.go`文件，添加以下高亮行，在`fmt.Println()`函数中调用`greet.go`中的变量:

```go
package main

import (
	"fmt"

	"github.com/gopherguides/greet"
)

func main() {
	greet.Hello()

	fmt.Println(greet.Shark)
}
```

再次运行此程序：

```bash
go run main.go
```

你会收到以下输出：

```Output
Hello, World!
Sammy
```

最后，让我们也在`greet.go`文件中定义一个类型。创建一个带有 `name` 和 `color`字段的 `Octopus` 类型，以及一个在调用时将打印出字段的函数：

```go
package greet

import "fmt"

var Shark = "Sammy"

type Octopus struct {
	Name  string
	Color string
}

func (o Octopus) String() string {
	return fmt.Sprintf("The octopus's name is %q and is the color %s.", o.Name, o.Color)
}

func Hello() {
	fmt.Println("Hello, World!")
}
```

打开`main.go`，在文件的末尾创建一个该类型的实例:

```go
package main

import (
	"fmt"

	"github.com/gopherguides/greet"
)

func main() {
	greet.Hello()

	fmt.Println(greet.Shark)

	oct := greet.Octopus{
		Name:  "Jesse",
		Color: "orange",
	}

	fmt.Println(oct.String())
}
```

一旦你用`oct := greet.Octopus`创建了一个`Octopus`类型的实例，就可以在`main.go`文件的命名空间中访问该类型的函数和字段。这使得在最后一行直接写`oct.String()`，而不用调用`greet`。同样的，也可以在不引用`greet`包的名字的情况下调用`oct.Color`等类型字段。

`Octopus`类型上的`String`方法使用`fmt.Sprintf`函数来输出一段文本，并将结果即一个字符串，`返回`给调用者（在这里是指主程序）。

当你运行该程序时，你会收到以下输出：

```bash
go run main.go
```

```Output
Hello, World!
Sammy
The octopus's name is "Jesse" and is the color orange.
```

通过在`Octopus`上创建`String`方法，你现在有一个可重复使用的方法来打印出自定义类型的信息。如果想在将来改变这个方法的行为，只需要编辑这一个方法。

## 可导出代码

你可能已经注意到，调用的`greet.go`文件中所有的声明都是大写的。Go 没有像其他语言那样有`public`、`private`或`protected`修饰符的概念。外部可见性是由大写字母控制的。以大写字母开头的类型、变量、函数等等，在当前包之外是可以公开使用的。一个在其包外可见的符号被认为是 `可导出` 的。

如果你给`Octopus`添加了一个名为`reset`的新方法，可以在`greet`包内调用它，但是不能在`main.go`文件中调用，因为调用者在`greet`包之外：

```go
package greet

import "fmt"

var Shark = "Sammy"

type Octopus struct {
	Name  string
	Color string
}

func (o Octopus) String() string {
	return fmt.Sprintf("The octopus's name is %q and is the color %s.", o.Name, o.Color)
}

func (o *Octopus) reset() {
	o.Name = ""
	o.Color = ""
}

func Hello() {
	fmt.Println("Hello, World!")
}
```

如果你试图从`main.go`文件中调用`reset`：

```go
package main

import (
	"fmt"

	"github.com/gopherguides/greet"
)

func main() {
	greet.Hello()

	fmt.Println(greet.Shark)

	oct := greet.Octopus{
		Name:  "Jesse",
		Color: "orange",
	}

	fmt.Println(oct.String())

	oct.reset()
}
```

你会收到以下编译错误：

```Output
oct.reset undefined (cannot refer to unexported field or method greet.Octopus.reset)
```

要从 `Octopus` 中`导出` `reset` 功能，请将`reset` 中的`R` 大写：

```go
package greet

import "fmt"

var Shark = "Sammy"

type Octopus struct {
	Name  string
	Color string
}

func (o Octopus) String() string {
	return fmt.Sprintf("The octopus's name is %q and is the color %s.", o.Name, o.Color)
}

func (o *Octopus) Reset() {
	o.Name = ""
	o.Color = ""
}

func Hello() {
	fmt.Println("Hello, World!")
}
```

如此一来，可以从其他包中调用`Reset'而不会得到错误：

```go
package main

import (
	"fmt"

	"github.com/gopherguides/greet"
)

func main() {
	greet.Hello()

	fmt.Println(greet.Shark)

	oct := greet.Octopus{
		Name:  "Jesse",
		Color: "orange",
	}

	fmt.Println(oct.String())

	oct.Reset()

	fmt.Println(oct.String())
}
```

现在，如果你运行这个程序：

```bash
go run main.go
```

你将收到以下输出：

```Output
Hello, World!
Sammy
The octopus's name is "Jesse" and is the color orange
The octopus's name is "" and is the color .
```

通过调用`Reset`，清除了`Name`和`Color`字段中的所有信息。当调用`String`方法时，`Name`和`Color`打印为空，因为这些字段现在是空的。

## 总结

编写Go包与编写其他Go文件是一样的，但把它放在另一个目录中可以隔离代码，以便在其他地方重复使用。本教程介绍了如何在包中编写定义，演示了如何在另一个Go文件中使用这些定义，并解释了控制包是否可访问的选项。