# 理解 Go 中包的可见性

## 介绍

当创建一个[Go 中的包]({{< relref "/docs/21-How_To_Write_Packages_in_Go.md" >}})时，最终的目标通常是让其他开发者可以使用这个包，无论是高阶包还是整个程序。通过[导入包]({{< relref "/docs/20-Importing_Packages_in_Go_DigitalOcean.md" >}})，你的这段代码可以作为其他更复杂的工具的构建模块。然而，只有某些包是可以导入的。这是由包的可见性决定的。

这里的*可见性*是指一个包或其他构造可以被引用的文件空间。例如，如果我们在一个函数中定义一个变量，那么这个变量的可见性（范围）只在定义它的那个函数中。同样，如果你在一个包中定义了一个变量，你可以让它只在该包中可见，或允许它在包外也可见。

在编写符合人体工程学的代码时，仔细控制包的可见性是很重要的，特别是在考虑到将来可能要对你的包进行修改时。如果你需要修复一个错误，提高性能，或改变功能，你会希望以一种不会破坏使用你的包的人的代码的方式进行改变。尽量减少破坏性修改的一个方法是只允许访问你的包中需要正常使用的部分。通过限制访问，你可以在内部对包进行修改，而减少影响其他开发者使用你的包的机会。

在这篇文章中，将学习如何控制包的可见性，以及如何保护代码中只应在包内使用的部分。为了做到这一点，我们将创建一个基本的记录器来记录和调试信息，使用具有不同程度的项目可见性的包。

## 前提条件

要遵循本文中的示例，你将需要：

- 按照[如何安装 Go 并设置本地编程环境]({{< relref "/docs/01-How_To_Install_Go_and_Set_Up_a_Local Programming_Environment_on_Ubuntu_18.04_DigitalOcean.md" >}})设置的 Go 工作区。 本教程将使用以下文件结构：

```text
.
├── bin 
│ 
└── src
    └── github.com
        └── gopherguides
```

## 可导出与不可导出

不同于其他程序语言，如 Java 和[Python](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-python-3)使用*访问修饰符*如`public`、`private`或`protected`来指定范围不同，Go 通过其声明方式来决定一个项目是否`exported`和`unxported`。在这种情况下，导出一个项目会使它在当前包之外是 "可见的"。如果它没有被导出，它只能在它被定义的包内可见和使用。

这种外部可见性是通过将声明的项目的第一个字母大写来控制的。所有以大写字母开头的声明，如 "类型"、"变量"、"常量"、"函数"等，在当前包外是可见的。

让我们看看下面的代码，仔细注意一下大写字母。

```go
package greet

import "fmt"

var Greeting string

func Hello(name string) string {
	return fmt.Sprintf(Greeting, name)
}
```

这段代码声明它是在`greet`包中。然后声明了两个符号，一个叫做 `Greeting` 的变量和一个叫做 `Hello` 的函数。因为它们都以大写字母开头，所以它们都被 "可导出" 的，可供任何外部程序使用。如前所述，精心设计一个限制访问的包将允许更好的 API 设计，并使内部更新你的包更容易，而不会破坏任何依赖此包的代码。

## 定义包的可见性

为了仔细看看包的可见性在程序中是如何工作的，让我们创建一个`logging`包，记住哪些信息我们希望包外可见，哪些我们不希望它可见。这个日志包将负责把我们程序的任何信息记录到控制台。它还将查看我们在什么*级别*上进行的日志记录，一个级别描述了日志的类型，它将是三种状态之一：`信息`、`警告`或`错误`。

首先，在你的 `src` 目录下，创建一个名为 `logging` 的目录来放置日志文件：

```bash
mkdir logging
```

进入目录：

```bash
cd logging
```
然后，使用 nano 这样的编辑器，创建一个名为`logging.go`的文件：

```bash
nano logging.go
```

在刚刚创建的`logging.go`文件中写入以下代码：

```go
package logging

import (
	"fmt"
	"time"
)

var debug bool

func Debug(b bool) {
	debug = b
}

func Log(statement string) {
	if !debug {
		return
	}

	fmt.Printf("%s %s\n", time.Now().Format(time.RFC3339), statement)
}
```

这段代码的第一行声明了一个名为 `logging` 的包。在这个包中，有两个 "导出 "的函数。`Debug`和`Log`。这些函数可以被任何其他导入`logging`的包所调用。还有一个名为`debug`的私有变量。这个变量只能从`logging`包内访问。值得注意的是，虽然函数`Debug`和变量`debug`的拼写相同，但函数是大写的，变量不是。这使得它们成为具有不同作用域的不同声明。

保存并退出该文件。

为了在我们代码的其他地方使用这个包，我们可以[`import`它到一个新的包]({{< relref "/docs/20-Importing_Packages_in_Go_DigitalOcean.md" >}})。我们将创建这个新的包，但需要一个新的目录来首先存储这些源文件。

让我们离开`logging`目录，创建一个名为`cmd`的新目录，然后进入这个新目录：

```bash
cd ..
mkdir cmd
cd cmd
```

在刚刚创建的`cmd`目录下创建一个名为`main.go`的文件：

```bash
nano main.go
```

现在我们可以添加以下代码：

```go
package main

import "github.com/gopherguides/logging"

func main() {
	logging.Debug(true)

	logging.Log("This is a debug statement...")
}
```

现在整个程序已经写好了。然而，在运行这个程序之前，我们还需要创建几个配置文件，以便我们的代码能够正常工作。Go 使用[Go 模块](https://blog.golang.org/using-go-modules)来配置导入资源的软件包依赖性。Go 模块是放置在你的包目录中的配置文件，它告诉编译器从哪里导入包。虽然对模块的学习超出了本文的范围，但我们可以只写几行配置来使这个例子在本地工作。

在`cmd`目录下打开以下`go.mod`文件：

```bash
nano go.mod
```

然后在文件中放置以下内容：

```go
module github.com/gopherguides/cmd

replace github.com/gopherguides/logging => ../logging
```

这个文件的第一行告诉编译器，`cmd`包的文件路径是`github.com/gopherguides/cmd`。第二行告诉编译器，`github.com/gopherguides/logging`包可以在磁盘上的`.../logging`目录下找到。

我们还需要一个`go.mod`文件用于我们的`logging`包。让我们回到`logging`目录中，创建一个`go.mod`文件。

```bash
cd ../logging
nano go.mod
```

在文件中加入以下内容：

```go
module github.com/gopherguides/logging
```

这告诉编译器，我们创建的`logging`包实际上是`github.com/gopherguides/logging`包。这使得在 `main` 包中导入该包成为可能，之前写了以下这一行：

```go
package main

import "github.com/gopherguides/logging"

func main() {
	logging.Debug(true)

	logging.Log("This is a debug statement...")
}
```

你现在应该有以下目录结构和文件布局：

```text
├── cmd
│   ├── go.mod
│   └── main.go
└── logging
    ├── go.mod
    └── logging.go
```

现在我们已经完成了所有的配置，可以用以下命令运行`cmd`包中的`main`程序：

```bash
cd ../cmd
go run main.go
```

你将得到类似以下的输出：

```Output
2019-08-28T11:36:09-05:00 This is a debug statement...
```

该程序将以 RFC 3339 格式打印出当前时间，后面是我们发送给记录器的任何语句。[RFC 3339](https://tools.ietf.org/html/rfc3339)是一种时间格式，被设计用来表示互联网上的时间，通常用于日志文件。

因为`Debug`和`Log`函数是从日志包中导出的，我们可以在`main`包中使用它们。然而，`logging`包中的`debug`变量没有被导出。试图引用一个未导出的声明将导致一个编译时错误。

在`main.go`中添加错误操作的一行`fmt.Println(logging.debug)`：

```go
package main

import "github.com/gopherguides/logging"

func main() {
	logging.Debug(true)

	logging.Log("This is a debug statement...")

	fmt.Println(logging.debug)
}
```

保存并运行该文件，你将收到一个类似于以下的错误：

```Output
. . .
./main.go:10:14: cannot refer to unexported name logging.debug
```

现在我们已经了解了包中的 `exported` 和 `unexported` 项的行为，接下来我们将看看如何从 `structs` 中导出 `fields` 和 `methods`。

## 结构内的可见性

虽然在上一节中构建的记录器中的可见性方案可能对简单的程序有效，但它分享了太多的状态，在多个包中都是有用的。这是因为导出的变量可以被多个包所访问，这些包可以将变量修改成相互矛盾的状态。允许你的包的状态以这种方式被改变，使得你很难预测你的程序将如何表现。例如，在目前的设计中，一个包可以将`Debug`变量设置为`true`，而另一个包可以在同一实例中将其设置为`false`。这将产生一个问题，因为导入`logging`包的两个包都会受到影响。

我们可以通过创建一个结构，然后把方法挂在它上面，使日志记录器隔离。这将允许我们创建一个日志记录器的`instance`实例，在每个使用它的包中独立使用。

将`logging`包改为以下内容，以重构代码并隔离记录器：

```go
package logging

import (
	"fmt"
	"time"
)

type Logger struct {
	timeFormat string
	debug      bool
}

func New(timeFormat string, debug bool) *Logger {
	return &Logger{
		timeFormat: timeFormat,
		debug:      debug,
	}
}

func (l *Logger) Log(s string) {
	if !l.debug {
		return
	}
	fmt.Printf("%s %s\n", time.Now().Format(l.timeFormat), s)
}
```

在这段代码中，我们创建了一个`Logger`结构。这个结构将存放未导出的状态，包括要打印出来的时间格式和`debug`变量设置为`true`或`false`。`New`函数设置初始状态来创建记录器，例如时间格式和调试状态。然后，它将内部给它的值存储到未导出的变量`timeFormat`和`debug`中。我们还在`Logger`类型上创建了一个名为`Log`的方法，该方法接收我们想要打印出来的语句。在`Log`方法内有一个对其本地方法变量`l`的引用，以获得对其内部字段的访问，如`l.timeFormat`和`l.debug`。

这种方法将允许在许多不同的包中创建一个`Logger`，并独立于其他包的使用方式而使用它。

为了在其他软件包中使用它，让我们把`cmd/main.go`改成下面的样子：

```go
package main

import (
	"time"

	"github.com/gopherguides/logging"
)

func main() {
	logger := logging.New(time.RFC3339, true)

	logger.Log("This is a debug statement...")
}
```

运行这个程序将给你带来以下输出：

```text
output
2019-08-28T11:56:49-05:00 This is a debug statement...
```

在这段代码中，我们通过调用导出的函数`New`创建了一个记录器的实例。将这个实例的引用存储在`logger`变量中。现在可以调用`logging.Log`来打印出语句。

如果试图从`logger`中引用一个未导出的字段，如`timeFormat`字段，将收到一个编译时错误。尝试添加以下高亮行，并运行`cmd/main.go`。

```go
package main

import (
	"time"

	"github.com/gopherguides/logging"
)

func main() {
	logger := logging.New(time.RFC3339, true)

	logger.Log("This is a debug statement...")

	fmt.Println(logger.timeFormat)
}
```

这将给出如下错误信息：

```Output
. . .
cmd/main.go:14:20: logger.timeFormat undefined (cannot refer to unexported field or method timeFormat)
```

编译器认识到`logger.timeFormat`没有被导出，因此不能从`logging`包中检索到。

## 方法中的可见性

与结构字段相同，方法也可以被导出或未导出。

为了说明这一点，让我们为日志器添加*级别*的日志记录。分级日志是一种对日志进行分类的方法，这样就可以在日志中搜索特定类型的事件。我们将在记录器中加入的级别是。

- `info`级别，代表信息类型的事件，通知用户一个动作，如 "程序开始"，或 "电子邮件发送"。这些帮助我们调试和跟踪我们程序的一部分，看看是否有预期的行为发生。
- `warning` 级别。这些类型的事件可以识别出一些不属于错误的意外情况，如 "邮件发送失败，重试"。它们帮助我们看到我们的程序中没有像我们预期的那样顺利进行的部分。
- `error`级别，意味着程序遇到了问题，如 "未找到文件"。这往往会导致程序的运行失败。

你也可能希望打开和关闭某些级别的日志记录，特别是当你的程序没有按照预期执行，你想调试程序的时候。我们将通过改变程序来增加这个功能，当`debug`被设置为`true`时，它将打印所有级别的信息。否则，如果它是`false`，它将只打印错误信息。

通过对`logging/logging.go`进行以下修改来增加分级日志：


```go
package logging

import (
	"fmt"
	"strings"
	"time"
)

type Logger struct {
	timeFormat string
	debug      bool
}

func New(timeFormat string, debug bool) *Logger {
	return &Logger{
		timeFormat: timeFormat,
		debug:      debug,
	}
}

func (l *Logger) Log(level string, s string) {
	level = strings.ToLower(level)
	switch level {
	case "info", "warning":
		if l.debug {
			l.write(level, s)
		}
	default:
		l.write(level, s)
	}
}

func (l *Logger) write(level string, s string) {
	fmt.Printf("[%s] %s %s\n", level, time.Now().Format(l.timeFormat), s)
}
```

在这个例子中，我们为`Log`方法引入了一个新的参数。我们现在可以传入日志信息的`级别`。`Log`方法决定了它是什么级别的消息。如果是 `info` 或 `warning` 消息，并且 `debug` 字段是 `true`,，那么它就会写下该消息。否则，它将忽略该消息。如果是其他级别的信息，比如 `error`，它将写出该信息。

大多数确定消息是否被打印出来的逻辑存在于`Log`方法中。我们还引入了一个未导出的方法，叫做 `write`。`write`方法是实际输出日志信息的方法。

现在我们可以在其他软件包中使用这种分级日志，方法是将`cmd/main.go`改成下面的样子：

```go
package main

import (
	"time"

	"github.com/gopherguides/logging"
)

func main() {
	logger := logging.New(time.RFC3339, true)

	logger.Log("info", "starting up service")
	logger.Log("warning", "no tasks found")
	logger.Log("error", "exiting: no work performed")

}
```

运行这个将返回：
```Output
[info] 2019-09-23T20:53:38Z starting up service
[warning] 2019-09-23T20:53:38Z no tasks found
[error] 2019-09-23T20:53:38Z exiting: no work performed
```

在这个例子中，`cmd/main.go`成功使用了导出的`Log`方法。

现在我们可以通过将`debug`切换为`false`来传递每个消息的`level'：

```go
package main

import (
	"time"

	"github.com/gopherguides/logging"
)

func main() {
	logger := logging.New(time.RFC3339, false)

	logger.Log("info", "starting up service")
	logger.Log("warning", "no tasks found")
	logger.Log("error", "exiting: no work performed")

}
```

现在我们将看到，只有 `error` 级别的信息会被打印出来：

```Output
[error] 2019-08-28T13:58:52-05:00 exiting: no work performed
```

如果我们试图从`logging`包之外调用`write`方法，我们将收到一个编译时错误：

```go
package main

import (
	"time"

	"github.com/gopherguides/logging"
)

func main() {
	logger := logging.New(time.RFC3339, true)

	logger.Log("info", "starting up service")
	logger.Log("warning", "no tasks found")
	logger.Log("error", "exiting: no work performed")

	logger.write("error", "log this message...")
}
```
```Output
cmd/main.go:16:8: logger.write undefined (cannot refer to unexported field or method logging.(*Logger).write)
```

当编译器看到你试图引用另一个包中以小写字母开头的东西时，它知道这个东西没有被导出，因此抛出一个编译器错误。

本教程中的记录器说明了如何编写代码，只暴露出希望其他包消费的部分。因为我们控制了包的哪些部分在包外是可见的，所以现在能够在未来进行修改而不影响任何依赖包的代码。例如，如果想只在`debug`为 false 时关闭`info`级别的消息，你可以在不影响你的 API 的任何其他部分的情况下做出这个改变。我们也可以安全地对日志信息进行修改，以包括更多的信息，如程序运行的目录。

## 总结

这篇文章展示了如何在包之间共享代码，同时也保护你的包的实现细节。这允许你输出一个简单的 API，为了向后兼容而很少改变，但允许在你的包中根据需要私下改变，使其在未来更好地工作。这被认为是创建包和它们相应的 API 时的最佳做法。

要了解更多关于 Go 中的包，请查看我们的[在 Go 中导入包]({{< relref "/docs/20-Importing_Packages_in_Go_DigitalOcean.md" >}})和[如何在 Go 中编写包]({{< relref "/docs/21-How_To_Write_Packages_in_Go.md" >}})文章，或者探索我们整个[如何在 Go 中编码系列](https://gocn.github.io/How-To-Code-in-Go/)。