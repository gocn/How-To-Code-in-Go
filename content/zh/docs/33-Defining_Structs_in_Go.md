# 在 Go 中定义结构体

## 简介

围绕具体的细节建立抽象，是编程语言能给开发者的最大工具。结构体使我们可以谈论 `Address` 而不是通过描述 `Street`, `City`, 或 `PostalCode` 字符串来进行推断。它们作为[文档](https://gocn.github.io/How-To-Code-in-Go/docs/06-How_To_Write_Comments_in_Go)的一个自然纽带，致力于告诉未来的开发者（包括我们自己）哪些数据对我们的 Go 程序是重要的，以及未来的代码应该如何正确使用这些数据。结构体可以用几种不同的方式来定义和使用。在本教程中，我们将会逐一看下这些技术。

## 定义结构体

结构体的工作方式类似于你可能正在使用的纸质表格，例如用来报税的表单。纸质表格可能有文本信息的字段，比如你的名字和姓氏。除了文本字段外，表单可能还有复选框来表示布尔值，如“已婚”或“单身”，或表示出生日期的日期字段。同样，结构体将不同数据收集在一起，并通过不同的字段名组织它们。当你用一个新的结构体初始化一个变量时，就好像你影印了一张表格并准备填写。

要创建一个新的结构体，你必须首先给 Go 定义一个蓝图来描述结构体所包含的字段。这个结构定义通常以关键字 `type` 开始，紧跟着结构体的名称。随后，使用 `struct` 关键字，后面跟着一对大括号 `{}`，在这里声明结构体将包含的字段。一旦你定义了结构体，就可以声明使用该结构体定义的变量。本例定义了一个结构并使用它。

```go
package main

import "fmt"

type Creature struct {
	Name string
}

func main() {
	c := Creature{
		Name: "Sammy the Shark",
	}
	fmt.Println(c.Name)
}
```

当你运行这段代码时，会看到这样的输出：

```bash
output
Sammy the Shark
```

在这个例子中，我们首先定义了一个 `Creature` 结构体，包含一个字符串类型的 `Name` 字段。在 `main` 方法中，我们通过在 `Creature` 类型名称后添加一对大括号来创建一个 `Creature` 实例，然后为该实例的字段设定值。`c` 实例的 `Name` 字段将被设置为 “Sammy the Shark”。在 `fmt.Println` 方法的调用中，我们通过在实例变量后加点号与我们想访问的字段名来检索实例的字段值。例如，`c.Name` 在本例中返回 `Name` 字段值。

当你声明一个新的结构体实例时，通常会列举字段名和它们的值，就像上一个例子。此外，如果每个字段的值都会在结构的实例化过程中提供，也可以省略字段名，如本例。

```go
package main

import "fmt"

type Creature struct {
	Name string
	Type string
}

func main() {
	c := Creature{"Sammy", "Shark"}
	fmt.Println(c.Name, "the", c.Type)
}
```

输出结果与上一个例子相同：

```bash
output
Sammy the Shark
```

我们为 `Creature` 增加了一个额外的字段，用字符串类型来追踪生物 `Type` 。当在 `main` 方法中实例化 `Creature` 时，我们选择使用较短的实例化方式，即按顺序为每个字段提供值，并省略其字段名。在 `Creature{"Sammy", "Shark"}` 的声明中，因为 `Name` 在类型声明中首先出现，然后为`Type`，所以`Name` 字段取值为 `Sammy`，`Type` 字段取值为 `Shark`。

这种较短的声明方式有一些缺点，导致 Go 社区在大多数情况下都倾向于采用较长的方式。使用简短声明时，你必须为结构体中的每个字段提供值，而不能省略你并不关心的字段。这很快就会导致包含很多字段的结构体短声明变得混乱。出于这个原因，简短声明通常用于字段少的结构体。

到目前为止，例子中的字段名都是以大写字母开头。这并不仅是风格上的偏好，而是在字段名中使用大写或小写字母会影响到你的字段名是否能被其他包中运行的代码所访问。

## 结构体字段导出

结构体的字段遵循与 Go 编程语言中其他标识符相同的导出规则。如果字段名以大写字母开头，则该字段可被定义该结构体的包之外的代码读写。如果字段以小写字母开头，则只有该结构体包内的代码才可以读写该字段。这个例子定义了可导出和不可导出的字段：

```go
package main

import "fmt"

type Creature struct {
	Name string
	Type string

	password string
}

func main() {
	c := Creature{
		Name: "Sammy",
		Type: "Shark",

		password: "secret",
	}
	fmt.Println(c.Name, "the", c.Type)
	fmt.Println("Password is", c.password)
}
```

这将输出：

```bash
output
Sammy the Shark
Password is secret
```

我们在之前的例子中添加了一个额外的字段，`secret`。`secret` 是一个未导出的字符串类型字段，这意味着任何试图实例化 `Creature` 的其他包将无法访问或设置其 `secret` 字段。在同一个包内，我们能够访问这些字段，正如本例所做的那样。由于 `main` 方法也在 `main` 包中，它能够引用 `c.password` 并检索所存储的值。在结构中拥有未导出的字段是很常见的，对它们的访问由导出的方法来进行配置。

## 内联结构体

除了定义一个新的类型来表示一个结构体外，你还可以定义一个内联结构。在为结构类型想一个新的名称会造成浪费的情况下，这些即时创建的结构定义(不需要命名的 struct)会非常有用。例如，测试经常使用一个结构体来定义构成一个特定测试案例的所有参数。当该结构只会在一个地方使用时，想出 `CreatureNamePrintingTestCase` 这样的新名字会很麻烦。

内联结构定义出现在变量赋值的右侧。你必须立即使用一对额外的大括号进行实例化并为定义的每个字段赋值。下面的例子显示了一个内联结构定义：

```go
package main

import "fmt"

func main() {
	c := struct {
		Name string
		Type string
	}{
		Name: "Sammy",
		Type: "Shark",
	}
	fmt.Println(c.Name, "the", c.Type)
}
```

这个例子的输出结果将是：

```bash
output
Sammy the Shark
```

本例没有使用 `type` 关键字定义一个新的类型来描述我们的结构体，而是通过将 `struct` 定义放在短赋值运算符 `:=` 之后定义一个内联结构。我们像之前的例子一样定义了结构体的字段，但是必须立即提供一对大括号和每个字段将赋的值。现在我们可以和以前完全一样使用这个结构体，用点符号来访问字段名。内联结构最常用于测试过程中声明，因为经常会需要用到一次性结构体来定义包含特定测试案例的数据和预期测试结果。

## 总结

结构体是开发者为组织信息而定义的各种各样的数据的集合。大多数程序都要处理大量的数据，如果没有结构体，就很难记住哪些 `string` 或 `int` 变量相关，哪些无关。下一次，当你发现自己在组织一些变量时，问问自己，也许这些变量用 `struct` 来分组会更好。这些变量可能一直都在描述更高层级的概念。
