

- 原文地址：[How To Use the Flag Package in Go | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-use-the-flag-package-in-go)
- 原文作者：digitalocean
- 本文永久链接：[How-To-Code-in-Go/40-How_To_Use_the_Flag_Package_in_Go.md at main · gocn/How-To-Code-in-Go (github.com)](https://github.com/gocn/How-To-Code-in-Go/blob/main/40-How_To_Use_the_Flag_Package_in_Go.md)
- 译者：[zxmfke](https://github.com/zxmfke)
- 校对：[pseudoyu](https://github.com/pseudoyu)

# 在 Go 里面如何使用 Flag 包

### 简介

命令行工具很少在没有额外配置的情况下开箱即用。好的默认值固然很重要，但有用的工具需要接受用户的配置。在大多数平台上，命令行工具通过接收标志来指定命令的执行。标志是以键值分隔的字符串，加在命令的名称后面。Go 让你通过使用标准库中的 flag 包来制作接受标志的命令行工具。

在本教程中，你将探索使用 flag 包来建立不同种类的命令行工具的各种方法。你将使用一个标志来控制程序输出，引入位置参数，在这里你将混合标志和其他数据，然后实现子命令。

## 用 Flag 来改变程序的行为

使用 flag 包包括三个步骤：首先，定义变量以捕获标志值，然后定义你的 Go 应用程序将使用的标志，最后解析执行时提供给应用程序的标志。`flag`包内的大多数函数都与定义标志和将它们与你定义的变量绑定有关。解析阶段由`Parse()`函数处理。

为了阐述这一点，你将创建一个程序，定义一个 [Boolean](https://www.digitalocean.com/community/tutorials/understanding-boolean-logic-in-go)标志，改变这个标志将会把信息打印到标准输出上。如果提供一个`-color`标志，程序会用蓝色来打印消息。如果没有这个标志，则打印消息不会有颜色。

创建一个叫`boolean.go`的文件：

```bash
nano boolean.go
```

添加如下代码到文件里面来创建程序：

```go
package main

import (
	"flag"
	"fmt"
)

type Color string

const (
	ColorBlack  Color = "\u001b[30m"
	ColorRed          = "\u001b[31m"
	ColorGreen        = "\u001b[32m"
	ColorYellow       = "\u001b[33m"
	ColorBlue         = "\u001b[34m"
	ColorReset        = "\u001b[0m"
)

func colorize(color Color, message string) {
	fmt.Println(string(color), message, string(ColorReset))
}

func main() {
	useColor := flag.Bool("color", false, "display colorized output")
	flag.Parse()

	if *useColor {
		colorize(ColorBlue, "Hello, DigitalOcean!")
		return
	}
	fmt.Println("Hello, DigitalOcean!")
}
```

这个例子使用[ANSI 逃逸序列](https://en.wikipedia.org/wiki/ANSI_escape_code)来指示终端显示彩色输出。这些是专门的 character 序列，所以为它们定义一个新的类型是有意义的(L8)。在这个例子中，我们称该类型为`color`，并将该类型定义为`string`。然后我们定义了一个调色板，在后面的 `const` 块中使用。定义在`const`块之后的`colorize`函数接受`Color`常量其中之一和一个`string`，用于对信息进行着色。然后它指示终端改变颜色，首先打印所要求的颜色的转义序列，然后打印信息，最后要求终端通过打印特殊的颜色重置序列来重置其颜色。

在`main`中，我们使用`flag.Bool`函数来定义一个名为`color`的 Boolean 标志。这个函数的第二个参数，`false`，在没有提供这个标志的情况下，设置这个标志的默认值。与你可能有的期望相反，将其设置为`true`并不会颠倒行为，如提供一个标志会导致它变成 false。因此，这个参数的值在布尔标志下几乎总是`false`。

最后一个参数是一个可以作为使用信息打印出来的文档 string。从这个函数返回的值是一个指向`bool`的指针。下一行的`flag.Parse`函数使用这个指针，然后根据用户传入的标志，设置`bool`变量。 然后我们就可以通过取消引用这个指针来检查这个`bool`指针的值。更多关于指针变量的信息可以在[指针教程](https://www.digitalocean.com/community/conceptual_articles/understanding-pointers-in-go)找到。使用这个 Boolean，我们就可以在设置`-color`标志时调用`colorize`，而在没有这个标志时调用`fmt.Println`变量。

保存文件，并在未传入没有任何标志的情况下运行该程序：

```bash
go run boolean.go
```

你将会看到如下输出：

```text
Output
Hello, DigitalOcean!
```

现在带上`-color`标志再跑一遍程序：

```bash
go run boolean.go -color
```

输出文本会是一样的，只不过这时候颜色时蓝色的。

标志不是传递给命令的唯一参数。你也能发送文件名或其他数据。

## 使用位置参数

通常情况下，命令会接受一些参数，这些参数作为命令的重点对象。例如，打印文件第一行的`head`命令经常被以`head example.txt`调用。文件`example.txt`是调用`head`命令时的一个位置参数。

`Parse()`函数将一直解析它所遇到的标志，直到它检测到一个非标志参数。`flag`包通过`Args()`和`Arg()`函数使这些参数可用。

为了阐述这一点，你将重新实现一个简化的`head`命令，它显示一个给定文件的前几行：

创建一个新的文件称为`head.go`，然后添加如下代码：

```go
package main

import (
	"bufio"
	"flag"
	"fmt"
	"io"
	"os"
)

func main() {
	var count int
	flag.IntVar(&count, "n", 5, "number of lines to read from the file")
	flag.Parse()

	var in io.Reader
	if filename := flag.Arg(0); filename != "" {
		f, err := os.Open(filename)
		if err != nil {
			fmt.Println("error opening file: err:", err)
			os.Exit(1)
		}
		defer f.Close()

		in = f
	} else {
		in = os.Stdin
	}

	buf := bufio.NewScanner(in)

	for i := 0; i < count; i++ {
		if !buf.Scan() {
			break
		}
		fmt.Println(buf.Text())
	}

	if err := buf.Err(); err != nil {
		fmt.Fprintln(os.Stderr, "error reading: err:", err)
	}
}
```

首先，我们定义了一个`count`变量，用来保存程序应该从文件中读取的行数。然后，我们使用`flag.IntVar`定义`-n`标志，模拟原始`head`程序的行为。 这个函数允许我们将自己的[pointer](https://www.digitalocean.com/community/conceptual_articles/understanding-pointers-in-go)传递给一个变量，与没有`Var`后缀的标志函数相反。除了这个区别之外，`flag.IntVar`的其他参数与`flag.Int`对应的参数相同：标志名称、默认值和描述。 和前面的例子一样，我们随后调用`flag.Parse()`来处理用户的输入。

下一节读取文件。我们首先定义一个`io.Reader`变量，该变量将被设置为用户请求的文件，或传递给程序的标准输入。在`if`语句中，我们使用`flag.Arg`函数来访问所有标志之后的第一个位置参数。如果用户提供了文件名，这个位置参数会被设置。否则，它将为空 string(`""`)。当文件名提供时，我们使用`os.Open`函数来打开该文件，并将我们之前定义的`io.Reader`设置为该文件。否则，我们使用`os.stdin`来读取标准输入。

最后一节使用一个用`bufio.NewScanner`创建的`*bufio.Scanner`从`io.Reader`变量`in`中读取行数据。我们使用[`for`loop](https://www.digitalocean.com/community/tutorials/how-to-construct-for-loops-in-go)遍历到 count 的值，如果用`buf.Scan`扫描该行结果为`false`，则调用`break`，表示行数少于用户要求的数量。

运行这个程序，用`head.go`作为文件参数，显示你刚才写的文件的内容：

```bash
go run head.go -- head.go
```

`--`分隔符是一个被`flag`包识别的特殊标志，它表示后面没有更多的 flag 参数。当你运行这个命令时，你会收到以下输出：

```text
Output
package main

import (
        "bufio"
        "flag"
```

使用你定义的`-n`标志来调整输出的数量：

```bash
go run head.go -n 1 head.go
```

这只输出包的声明：

```text
Output
package main
```

最后，当程序检测到没有提供位置参数时，它从标准输入中读取输入，就像`head`一样。试着运行这个命令：

```bash
echo "fish\nlobsters\nsharks\nminnows" | go run head.go -n 3
```

你将会看到如下输出：

```text
Output
fish
lobsters
sharks
```

到目前为止，你所看到的`flag`函数的行为仅限于检查整个命令的调用。你并不总是想要这种行为，特别是当你在编写一个支持子命令的命令行工具时。

## 用 FlagSet 来实现子命令

现代的命令行应用程序经常实现 "子命令"，将一套工具捆绑在一个命令之下。使用这种模式的最著名的工具是`git`。 当检查像`git init`这样的命令时，`git`是命令，`init`是 git 的子命令。子命令的一个显著特点是，每个子命令可以有自己的标志集合。

Go 应用程序可以使用`flag.(*FlagSet)`类型支持具有自己的标志集的子命令。为了阐述这一点，创建一个程序，使用两个具有不同标志的子命令来实现一个命令。

创建一个名为`subcommand.go`的新文件，并在该文件中添加以下内容：

```go
package main

import (
	"errors"
	"flag"
	"fmt"
	"os"
)

func NewGreetCommand() *GreetCommand {
	gc := &GreetCommand{
		fs: flag.NewFlagSet("greet", flag.ContinueOnError),
	}

	gc.fs.StringVar(&gc.name, "name", "World", "name of the person to be greeted")

	return gc
}

type GreetCommand struct {
	fs *flag.FlagSet

	name string
}

func (g *GreetCommand) Name() string {
	return g.fs.Name()
}

func (g *GreetCommand) Init(args []string) error {
	return g.fs.Parse(args)
}

func (g *GreetCommand) Run() error {
	fmt.Println("Hello", g.name, "!")
	return nil
}

type Runner interface {
	Init([]string) error
	Run() error
	Name() string
}

func root(args []string) error {
	if len(args) < 1 {
		return errors.New("You must pass a sub-command")
	}

	cmds := []Runner{
		NewGreetCommand(),
	}

	subcommand := os.Args[1]

	for _, cmd := range cmds {
		if cmd.Name() == subcommand {
			cmd.Init(os.Args[2:])
			return cmd.Run()
		}
	}

	return fmt.Errorf("Unknown subcommand: %s", subcommand)
}

func main() {
	if err := root(os.Args[1:]); err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
}
```

这个程序分为几个部分：`main`函数，`root`函数，以及实现子命令的各个函数。`main`函数处理从命令返回的错误。如果任何函数返回[错误](https://www.digitalocean.com/community/tutorials/handling-errors-in-go)，`if`语句将捕捉到它，打印出错误，程序将以`1`的状态码退出，向操作系统的其他部分表明发生了错误。在`main`中，我们将程序被调用的所有参数传递给`root`。我们通过先将`os.Args`切片来删除第一个参数，也就是程序的名称（在前面的例子中是`./subcommand`）。

`root`函数定义了`[]Runner`，所有的子命令都会在这里定义。`Runner`是一个子命令的 [interface](https://www.digitalocean.com/community/tutorials/how-to-use-interfaces-in-go) ，允许`root`使用`Name()`获取子命令的名称，并将其与变量`subcommand`内容进行比较。一旦在遍历`cmds`变量后找到了正确的子命令，我们就用其余的参数初始化子命令，并调用该命令的`Run()`方法。

我们只定义了一个子命令，尽管这个框架很容易让我们创建其他子命令。`GreetCommand`是使用`NewGreetCommand`实例化的，在这里我们使用`flag.NewFlagSet`创建一个新的`*flag.FlagSet`。`flag.NewFlagSet`需要两个参数：一个标志集的名称，和一个报告解析错误的策略。用`flag.(*FlagSet).Name`方法获取`*flag.FlagSet`的名称。我们在`(*GreetCommand).Name()`方法中使用这个方法，所以子命令的名字与我们给`*flag.FlagSet`的名字一致。 `NewGreetCommand`也用了类似于以前的例子的方式定义了一个`-name`标志，但它改为从`*GreetCommand`的`*flag.FlagSet`字段中调用这个方法，`gc.fs`。当`root`调用`*GreetCommand`的`Init()`方法时，我们将传入的参数传递给`*flag.FlagSet`字段的`Parse`方法。

如果你构建这个程序，然后运行它，就会更容易看到子命令。建立该程序：

```bash
go build subcommand.go
```

现在运行该程序，没有参数：

```bash
./subcommand
```

你会看到如下输出：

```text
Output
You must pass a sub-command
```

现在用`greet`子命令运行该程序。

```bash
./subcommand greet
```

这会输出如下内容：

```text
Output
Hello World !
```

现在使用`-name`标志和`greet`来指定一个名字：

```bash
./subcommand greet -name Sammy
```

你会看到程序给出的这个输出：

```text
Output
Hello Sammy !
```

这个例子说明了在 Go 中如何构建大型命令行应用程序的一些原则。 `FlagSets`的设计是为了给开发者提供更多的控制权，使其能够通过 flag 解析逻辑，分析`flag`的位置和处理方式。

## 总结

标记使你的应用程序在更多情景下更有用，因为它们让你的用户控制程序的执行方式。给用户提供有用的默认值很重要，但你应该让他们有机会覆盖那些不适合他们情况的设置。你已经看到，`flag`包提供了灵活的选择，向你的用户展示配置选项。你可以选择一些简单的标志，或者建立一套可扩展的子命令。 无论是哪种情况，在过去长久历史沉淀的风格下，使用`flag`包都可以帮助你按照灵活的、可编写脚本的命令行工具。

