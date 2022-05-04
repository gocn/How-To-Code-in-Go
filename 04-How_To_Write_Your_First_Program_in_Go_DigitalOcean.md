# 如何用 Go 编写你的第一个程序

## 前言

“Hello, World!” 程序是计算机编程中的经典且历史悠久的传统。 对于初学者来说，这是一个简单而完整的第一个程序，它是一个确保你的环境配置正确的好方法。

本教程将引导你在 Go 中创建此程序。 但是，为了使程序更有趣，你将修改传统的 “Hello, World!” 程序，以便它可以询问用户的姓名。 然后，你将在回复中使用该姓名。 完成本教程后，你将拥有一个运行起来如下所示的程序：

```
Output
Please enter your name.
Sammy
Hello, Sammy! I'm Go!
```

## 安装前提

在开始本教程之前，你需要一个本地的 Go 开发环境。你可以按照下面其中一个教程在你的计算机上进行设置：

-   [如何在 macOS 上安装 Go 和设置本地编程环境](https://github.com/gocn/How-To-Code-in-Go/blob/main/02-How_To_Install_Go_and_Set_Up_a_Local_Programming_Environment_on_macOS_DigitalOcean.md)
-   [如何在 Ubuntu 18.04 上安装 Go 和设置本地编程环境](https://github.com/gocn/How-To-Code-in-Go/blob/main/01-How_To_Install_Go_and_Set_Up_a_Local%20Programming_Environment_on_Ubuntu_18.04_DigitalOcean.md)
-   [如何在 Windows 10 上安装 Go 和设置本地编程环境](https://github.com/gocn/How-To-Code-in-Go/blob/main/03-How_To_Install_Go_and_Set_Up_a_Local_Programming_Environment_on_Windows_10_DigitalOcean.md)

## 第一步 — 编写最基本的 “Hello, World!” 程序

为了编写 Hello, World!” 程序，请打开一个命令行文本编辑器，例如 `nano`，然后创建一个新文件：

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

让我们分解下代码的各个部分。

`package` 是一个 Go 关键字，它定义了这个文件属于哪个代码包。 每个文件夹只能有一个包，并且文件夹中每个 `.go` 文件必须在其文件顶部声明相同的包名。 在这个例子中，代码属于 `main` 包。

`import` 是一个 Go 关键字，它告诉 Go 编译器你想在这个文件中使用哪些其他包。 在这里，你导入标准库附带的 `fmt` 包。 `fmt` 包提供了在开发时很有用的格式化和打印功能。

`fmt.Println` 是一个 Go 函数，位于 `fmt` 包中，它告诉计算机将一些文本打印到屏幕上。

在 `fmt.Println` 函数后面跟着一系列字符，例如 `"Hello, World!"`，用引号括起来。 引号内的任何字符都称为 _string_。 `fmt.Println` 函数会在程序运行时将此字符串打印到屏幕上。

按 `CTRL` 和 `X` 键退出 `nano`。 当提示保存文件时，按 `Y`，然后按 `ENTER` 退出。

现在你可以试试你的程序了。

## 第二步 — 运行 Go 程序

你的 “Hello, World!” 程序写好之后，你就可以运行程序了。你可以用 `go`  命令然后后面跟着你刚刚创建的文件名。

```
go run hello.go
```

程序执行后会显示以下输出：

```
Output
Hello, World!
```

让我们来探索下实际发生了什么。

Go 程序在运行之前必须要先编译。当你使用文件名调用 `go run` 时，在本例中为 `hello.go`，`go` 命令将 _编译_ 应用程序，然后运行生成的二进制文件。 对于用 _编译型_ 编程语言编写的程序，编译器将获取程序的源代码并生成另一种类型的低级代码（例如机器代码）来生成可执行程序。

Go 应用程序需要一个 `main` 包和一个确切的 **唯一** `main()` 函数，作为应用程序的入口点。 `main` 函数不接受任何参数并且不返回任何值。 相反，它告诉 Go 编译器应该将包编译为可执行包。

编译后，代码通过在 `main` 包中输入 `main()` 函数来执行。 它通过_调用_ `fmt.Println` 函数来执行`fmt.Println("Hello, World!")` 行。 `Hello, World!` 的字符串值被传递给函数。 在此示例中，字符串 `Hello, World!` 也称为 _参数_，因为它是传递给方法的值。

`Hello, World!` 两边的引号不会打印到屏幕上，因为你用它们告诉 Go 你的字符串在哪里开始和结束。

在这一步中，你已经用 Go 编程创建了一个有效的“Hello, World！” 。 在下一步中，你将探索如何使程序更具交互性。

## 第三步 — 提示用户输入

每次运行程序时，它都会产生相同的输出。 在此步骤中，你可以把提示用户输入他们的姓名添加到程序中。 然后，你将在输出中使用他们的名字。

不要修改现有程序，而是使用 `nano` 编辑器创建一个名为 `greeting.go` 的新程序：

```
nano greeting.go
```

首先，添加这段代码，提示用户输入他们的姓名：

```go
greeting.go
package main

import (
	"fmt"
)

func main() {
  fmt.Println("Please enter your name.")
}
```

再一次，你使用 `fmt.Println` 函数将一些文本打印到屏幕上。

现在添加下面高亮行代码来存储用户的输入：

```
greeting.go
package main

import (
"fmt"
)

func main() {
  fmt.Println("Please enter your name.")
  var name string
}
```

`var name string` 行将使用 `var` _关键字_ 创建一个新变量。 你将变量命名为 “name” ，它的类型为 “string” 。

然后添加下面高亮行代码来捕捉用户的输入：

```
greeting.go
package main

import (
"fmt"
)

func main() {
  fmt.Println("Please enter your name.")
  var name string
  fmt.Scanln(&name)
}
```

`fmt.Scanln` 方法告诉计算机等待以换行符或 (`\n`) 字符结尾的键盘输入。 该方法会暂停程序，允许用户输入他们想要的任何文本。 当用户按下键盘上的 `ENTER` 键时，程序将继续。 然后捕获所有点击，包括 `ENTER` ，然后将其转换为字符串。

你想在程序中使用这些字符输出，因此你通过将这些字符 _写入_ 到被称为 `name` 的字符串 _变量_ 中来保存这些字符。 Go 将该字符串存储在计算机的内存中，直到程序完成运行。

最后，在程序中添加以下高亮行来打印输出：

```
greeting.go
package main

import (
"fmt"
)

func main() {
  fmt.Println("Please enter your name.")
  var name string
  fmt.Scanln(&name)
  fmt.Printf("Hi, %s! I'm Go!", name)
}
```

这一次，你使用的是 `fmt.Printf`，而不是再次使用 `fmt.Println` 方法。 `fmt.Printf` 函数接受一个字符串，并使用特殊的打印 _占位符_, (`%s`)，将 `name` 的值注入到字符串中。 你这样做是因为 Go 不支持 _字符串插值_ ，它可以让你获取分配给变量的值并将其插入在字符串中。

按 `CTRL` 和 `X` 键退出 `nano`。 当提示保存文件时，按 `Y`。

现在运行程序。 系统将提示你输入你的姓名，因此请输入并按 `ENTER`。 输出可能不完全符合你的预期：

```
Output
Please enter your name.
Sammy
Hi, Sammy
! I'm Go!
```

而不是 `Hi, Sammy! I'm Go!`，名字后面有一个换行符。

该程序捕获了我们 **所有** 的点击，包括我们按下以告诉程序继续的 `ENTER` 键。 在字符串中，按 `ENTER` 键会创建一个特殊字符，该字符会创建一个新行。 该程序的输出完全按照你的要求执行； 它正在显示你输入的文本，包括新行。 这不是你期望的输出，但你可以使用其他功能对其进行修复。

在你的编辑器里面打开 `greeting.go`

```
nano greeting.go
```

在你的程序里定位到下面这一行：

```
greeting.go
...
fmt.Scanln(&name)
...
```

在其后面增加下面一行：

```
greeting.go
name = strings.TrimSpace(name)
```

上面使用了 Go 标准库 `strings` 包中的 `TrimSpace` 函数，来处理你使用 `fmt.Scanln` 捕获的字符串。 `strings.TrimSpace` 函数从字符串的开头和结尾删除任何空格字符，包括换行符。 在这种情况下，它会删除你按下 `ENTER` 时创建的字符串末尾的换行符。

要使用 `strings` 包，你需要在程序顶部导入它。

在你的程序找到下面这几行代码：

```
greeting.go
import (
	"fmt"
)
```

增加下面几行来导入  `strings` 包：

```
greeting.go
import (
"fmt"
"strings"
)
```

你的程序现在包含了以下代码：

```go
greeting.go
package main

import (
	"fmt"
	"strings"
)

func main() {
	fmt.Println("Please enter your name.")
	var name string
	fmt.Scanln(&name)
	name = strings.TrimSpace(name)
	fmt.Printf("Hi, %s! I'm Go!", name)
}
```

按 `CTRL` 和 `X` 键退出 `nano`。 当提示保存文件时，按 `Y`。

再次运行程序：

```
go run greeting.go
```

这一次，在你输入你的名字并按 `ENTER` 后，你会得到预期的输出：

```
Output
Please enter your name.
Sammy
Hi, Sammy! I'm Go!
```

你现在有一个 Go 程序，它接受用户的输入并将其打印回屏幕。

## 总结

在本教程中，你编写了 一个 “Hello, World！” 程序，可以从用户那里获取输入、处理结果并显示输出。 既然你有一个基本程序可以使用，请尝试进一步扩展你的程序。 例如，询问用户最喜欢的颜色，让程序说它最喜欢的颜色是红色。 你甚至可以尝试使用相同的技术来创建一个简单的 Mad-Lib 程序。