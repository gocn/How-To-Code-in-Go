# 如何在 Go 中构造 for 循环
### 介绍

在计算机编程中，_循环_ 是在满足某些条件之前循环重复执行一段代码的代码结构。在计算机编程中使用循环可以让您自动并重复地执行类似的任务。想象一下，如果您有一个需要处理的文件列表，或者您想计算一篇文章的行数。您就可以在代码中使用循环来解决这样的问题。

在 Go 中，`for` 循环是基于循环计数器或循环变量实现代码的重复执行。与其他具有多个循环结构（例如 `while` ， `do` 等 ）的编程语言不同，Go 只有 `for` 循环。这有助于使您的代码更清晰和更具可读性，因为您不必担心会有多种策略来实现相同的循环结构。在开发过程中，这种强可读性和低认知负担也将使您的代码比其他语言更不容易出错。

在本教程中，您将了解 Go 中 `for` 循环是如何工作的，包括其使用的三个主要变体。我们将首先展示如何创建不同类型的 `for` 循环，然后介绍如何[在 Go 中遍历顺序数据类型](https://www.digitalocean.com/community/tutorials/understanding-arrays-and-slices-in-go)。最后，我们将解释如何使用嵌套循环。

## 声明 ForClause 和 Condition 循环

为了适应各种用例，在 Go 中创建 `for` 循环有三种不同的方法，每种方法都有自己的功能。这些是使用 **Condition**、**ForClause** 或 **RangeClause** 创建 `for` 循环。在本节中，我们将解释如何声明和使用 ForClause 和 Condition 变体。

让我们先看看如何在 ForClause 中使用 `for` 循环。

_ForClause循环_ 被定义为有一个 _初始语句_，后跟一个 _条件_，然后是一个 _后置语句_。它们按以下语法排列：

```go
for [ Initial Statement ] ; [ Condition ] ; [ Post Statement ] {
    [Action]
}
```

为了解释前面组成元素的作用，让我们看一个使用 ForClause 语法在指定值范围内递增的 `for` 循环：

```go
for i := 0; i < 5; i++ {
	fmt.Println(i)
}
```

让我们分解这个循环并识别每个部分

循环的第一部分是 `i := 0` ，这是初始语句：

```go
for i := 0; i < 5; i++ {
	fmt.Println(i)
}
```

它表明我们正在声明一个名为 `i` 的变量，并将初始值设置为 `0`。

接下来是条件：

```go
for i := 0; i < 5; i++ {
	fmt.Println(i)
}
```

在这种情况下，我们声明当 `i` 小于 `5` 时，循环应该继续。

最后，是一个后置语句：

```go
for i := 0; i < 5; i++ {
	fmt.Println(i)
}
```

在后置语句中，循环遍历 `i` 以 `i++` [增量](https://golang.org/ref/spec#IncDec_statements)运算符进行迭代加一。

当我们运行这个程序时，输出如下所示：

```shell
Output
0
1
2
3
4
```

循环运行了 5 次。最初，设置 `i` 为 `0`，然后检查是否 `i` 小于`5`。由于 `i` 的值小于 `5` ，因此执行了循环并执行了 `fmt.Println(i)` 的动作。循环结束时， 调用 `i++` 语句，使 `i` 的值加 1。

**注意：** 请记住，在编程中我们倾向于从索引 0 开始，这就是为什么虽然打印了 5 个数字，但它们的范围是 0-4。

我们不会限制必须从 0 开始或者以某个特定值结束。我们可以为我们的初始语句分配任何值，也可以在我们的后置语句中赋予任何值。这允许我们创建任何所需的范围来循环：

```go
for i := 20; i < 25; i++ {
	fmt.Println(i)
}
```
在这里，迭代会从 20（包括）到 25（不包括），所以输出如下所示：

```shell
Output
20
21
22
23
24
```

我们还可以使用我们的后置语句以不同的值递增。这类似于其他语言中的 `step`：

首先，让我们使用正值递增的后置语句：

```go
for i := 0; i < 15; i += 3 {
	fmt.Println(i)
}
```

在这种情况下，设置 `for` 循环以便打印出从 0 到 15 的数字，但增量为 3，因此每三个数字打印一次，如下所示：

```shell
Output
0
3
6
9
12
```

我们也可以为我们的后置语句使用负值来向后迭代，但我们必须相应地调整我们的初始语句和条件参数：

```go
for i := 100; i > 0; i -= 10 {
	fmt.Println(i)
}
```

在这里，我们将 `i` 初始值设置为`100`，使用 `i < 0` 的条件在处停止，并且后置语句使用 `-=` 运算符将值减 10。循环开始于 `100` 并结束于 `0`，每次迭代减少 10。我们可以在输出中看到这种情况：

```shell
Output
100
90
80
70
60
50
40
30
20
10
```

您也可以从语法中不使用初始语句和后置语句，而只使用条件。这就是所谓的 _Condition循环_：

```go
i := 0
for i < 5 {
	fmt.Println(i)
	i++
}
```

这一次，我们在前面的代码行中将变量声明为 `i` 与循环分开。该循环只有一个条件子句，用于检查 `i` 是否小于`5`。只要条件为 `true`，循环就会继续迭代。

有时您可能不知道完成某项任务所需的迭代次数。在这种情况下，您可以省略所有语句，并使用 `break` 关键字退出执行：

```go
for {
	if someCondition {
		break
	}
	// do action here
}
```

这方面的一个例子可能是，如果我们正在从一个不确定大小的结构（如[缓冲区](https://golang.org/pkg/bytes/#Buffer)）中读取，并且我们不知道何时完成读取：
```go
package main

import (
	"bytes"
	"fmt"
	"io"
)

func main() {
	buf := bytes.NewBufferString("one\ntwo\nthree\nfour\n")

	for {
		line, err := buf.ReadString('\n')
		if err != nil {
			if err == io.EOF {

				fmt.Print(line)
				break
			}
			fmt.Println(err)
			break
		}
		fmt.Print(line)
	}
}
```

在前面的代码中，`buf :=bytes.NewBufferString("one\ntwo\nthree\nfour\n")` 声明了一个包含一些数据的缓冲区。因为我们不知道缓冲区何时会完成读取，所以我们创建了一个没有子句的 `for` 循环。在 `for` 循环内部，我们使用 `line, err := buf.ReadString('\n')` 从缓冲区读取一行并检查从缓冲区读取是否有错误。如果有，我们解决错误，并[使用 `break` 关键字退出 for 循环](https://www.digitalocean.com/community/tutorials/using-break-and-continue-statements-when-working-with-loops-in-go)。有了`break`，您就不需要使用停止循环的条件。

在本节中，我们学习了如何声明 ForClause 循环并使用它来迭代已知范围的值。我们还学习了如何使用 Condition循环进行迭代，直到满足特定条件。接下来，我们将了解 RangeClause 如何用于迭代顺序数据类型。

## 使用 RangeClause 循环遍历顺序数据类型

在 Go 中，使用 `for` 循环来迭代连续或集合数据类型（如[切片、数组](https://www.digitalocean.com/community/tutorials/understanding-arrays-and-slices-in-go)和[字符串](https://www.digitalocean.com/community/tutorials/an-introduction-to-working-with-strings-in-go)）的元素是很常见的。为了更容易做到这一点，我们可以使用带有 _RangeClause_ 语法的 `for` 循环。虽然您可以使用 ForClause 语法遍历顺序数据类型，但 RangeClause 更简洁且更易于阅读。

在我们研究使用 RangeClause 之前，让我们看看如何使用 ForClause 语法遍历切片：


```go
package main

import "fmt"

func main() {
	sharks := []string{"hammerhead", "great white", 		"dogfish", "frilled", "bullhead", "requiem"}

	for i := 0; i < len(sharks); i++ {
		fmt.Println(sharks[i])
	}
}
```

运行它将给出以下输出，打印出切片的每个元素：

```shell
Output
hammerhead
great white
dogfish
frilled
bullhead
requiem
```

现在，让我们使用 RangeClause 执行相同的操作：

```go
package main

import "fmt"

func main() {
	sharks := []string{"hammerhead", "great white", 		"dogfish", "frilled", "bullhead", "requiem"}

	for i, shark := range sharks {
		fmt.Println(i, shark)
	}
}
```

在这种情况下，我们打印出列表中的每个项。虽然我们使用了变量 `i` 和 `shark` ，但我们可以将变量称为任何其他[有效的变量名](https://www.digitalocean.com/community/tutorials/how-to-use-variables-and-constants-in-go#naming-variables-rules-and-style)，我们会得到相同的输出：

```shell
Output
0 hammerhead
1 great white
2 dogfish
3 frilled
4 bullhead
5 requiem
```

在切片上使用 `range` 时，它将始终返回两个值。第一个值将是当前迭代所在的索引，第二个是该索引处的值。在这个示例中，对于第一次迭代，索引是 `0`，值是 `hammerhead`。

有时，我们只想要切片元素内的值，而不是索引。但是，如果我们将前面的代码更改为仅打印值，我们将收到编译时错误：


```go
package main

import "fmt"

func main() {
	sharks := []string{"hammerhead", "great white", "dogfish", "frilled", "bullhead", "requiem"}

	for i, shark := range sharks {
		fmt.Println(shark)
	}
}
```

```shell
Output
src/range-error.go:8:6: i declared and not used
```

因为 `i` 在 `for` 循环中声明了，但从未使用过，编译器会报告 `i declared and not used`。每当您声明一个变量并且不使用它时，您都会在 Go 中收到相同的错误。

因此，Go 具有[空白标识符](https://golang.org/ref/spec#Blank_identifier)，即下划线 ( `_` )。在 `for` 循环中，您可以使用空白标识符来忽略从 `range` 关键字返回的任何值。在这种情况下，我们要忽略索引，它是返回的第一个参数。


```go
package main

import "fmt"

func main() {
	sharks := []string{"hammerhead", "great white", 		"dogfish", "frilled", "bullhead", "requiem"}

	for _, shark := range sharks {
		fmt.Println(shark)
	}
}
```

```shell
Output
hammerhead
great white
dogfish
frilled
bullhead
requiem
```

输出显示 `for` 循环遍历字符串切片，并打印切片中每个项，且不会打印索引。

您也可以使用 `range` 将项目添加到列表中：


```go
package main

import "fmt"

func main() {
	sharks := []string{"hammerhead", "great white", 		"dogfish", "frilled", "bullhead", "requiem"}

	for range sharks {
		sharks = append(sharks, "shark")
	}

	fmt.Printf("%q\n", sharks)
}
```

```shell
Output
['hammerhead', 'great white', 'dogfish', 'frilled', 'bullhead', 'requiem', 'shark', 'shark', 'shark', 'shark', 'shark', 'shark']
```

在这里，我们为切片 `"shark"` 添加了切片长度的占位符字符串 `sharks`。

请注意，我们不必使用空白标识符 `_` 来忽略 `range` 运算符的任何返回值。如果我们不需要使用任何一个 `range`  的返回值，Go 允许我们省略语句的整个声明部分。

我们也可以使用 `range` 运算符来填充切片的值

```go
package main

import "fmt"

func main() {
	integers := make([]int, 10)
	fmt.Println(integers)

	for i := range integers {
		integers[i] = i
	}

	fmt.Println(integers)
}
```

在这个例子中，切片 `integers` 初始化了十个空值，但 `for` 循环会设置列表中的所有值，如下所示：

```shell
Output
[0 0 0 0 0 0 0 0 0 0]
[0 1 2 3 4 5 6 7 8 9]
```

第一次打印切片 `integers` 的值时，我们看到全为零。然后我们遍历每个索引并将值设置为当前索引。然后当我们第二次打印值`integers` 时，显示它们现在都具有`0` 到 `9` 的值。

我们也可以使用 `range` 运算符来遍历字符串中的每个字符：

```go
package main

import "fmt"

func main() {
	sammy := "Sammy"

	for _, letter := range sammy {
		fmt.Printf("%c\n", letter)
	}
}
```

```shell
Output
S
a
m
m
y
```

当遍历 [map](https://www.digitalocean.com/community/tutorials/understanding-maps-in-go) 时，`range`将返回**键**和**值**：
```go
package main

import "fmt"

func main() {
	sammyShark := map[string]string{"name": "Sammy", "animal": "shark", "color": "blue", "location": "ocean"}

	for key, value := range sammyShark {
		fmt.Println(key + ": " + value)
	}
}
```

```shell
Output
color: blue
location: ocean
name: Sammy
animal: shark
```

注意：map 返回的顺序是随机的。每次运行此程序时，您可能会得到不同的结果。

现在我们已经学会了如何使用 `range` `for` 循环来遍历数据，下面让我们看看如何在循环中使用循环。

## 嵌套 For 循环

就像其他编程语言一样，在 Go 中循环也是可以嵌套的。_嵌套_ 是当我们在一个结构内又使用了一个结构。在这种情况下，嵌套循环是发生在另一个循环中的循环。当您希望对数据集的每个元素执行循环操作时，这些可能很有用。

嵌套循环在结构上类似于[嵌套`if`语句](https://www.digitalocean.com/community/tutorials/how-to-write-conditional-statements-in-go#nested-if-statements)。它的构造如下：

```go
for {
    [Action]
    for {
        [Action]  
    }
}
```

程序首先遇到外循环，执行它的第一次迭代。第一次迭代触发内部嵌套循环，然后运行完成。然后程序返回到外部循环的顶部，完成第二次迭代并再次触发嵌套循环。同样，嵌套循环运行到完成，程序返回到外部循环的顶部，直到序列完成或中断或其他语句中断该过程。

让我们实现一个嵌套 `for` 循环，以便我们仔细看看。在这个例子中，外层循环将遍历一个名为 `numList` 的整数切片，而内层循环将遍历一个名为 `alphaList` 的字符串切片。

```go
package main

import "fmt"

func main() {
	numList := []int{1, 2, 3}
	alphaList := []string{"a", "b", "c"}

	for _, i := range numList {
		fmt.Println(i)
		for _, letter := range alphaList {
			fmt.Println(letter)
		}
	}
}
```

当我们运行这个程序时，我们将得到以下输出：

```shell
Output
1
a
b
c
2
a
b
c
3
a
b
c
```

输出说明程序通过打印 `1` 完成了外循环的第一次迭代，然后连续触发内循环打印的完成。内循环完成后，程序返回到外循环的顶部，打印 `2`，然后再次完整打印内循环（`a`, `b`, `c`）等。

嵌套 `for` 循环可用于遍历由切片组成的切片中的项。在由切片组成的切片中，如果我们只使用一个 `for` 循环，程序会将每个内部列表作为一项输出：

```go
package main

import "fmt"

func main() {
	ints := [][]int{
		[]int{0, 1, 2},
		[]int{-1, -2, -3},
		[]int{9, 8, 7},
	}

	for _, i := range ints {
		fmt.Println(i)
	}
}
```

```shell
Output
[0 1 2]
[-1 -2 -3]
[9 8 7]
```

为了访问内部切片的每个单独项，我们将实现一个嵌套`for`循环：
```go
package main

import "fmt"

func main() {
	ints := [][]int{
		[]int{0, 1, 2},
		[]int{-1, -2, -3},
		[]int{9, 8, 7},
	}

	for _, i := range ints {
		for _, j := range i {
			fmt.Println(j)
		}
	}
}
```

```shell
Output
0
1
2
-1
-2
-3
9
8
7
```

当我们在这里使用嵌套 `for` 循环时，我们能够迭代切片中包含的各个项。

## 结论

在本教程中，我们学习了如何声明和使用 `for` 循环来解决 Go 中的重复任务。我们还学习了 `for` 循环的三种不同变体以及何时使用它们。要了解有关 `for` 循环以及如何控制它们的流程的更多信息，请阅读[在 Go 中使用循环时的 Break 和 Continue 语句](https://github.com/gocn/How-To-Code-in-Go/blob/main/26_Using_Break_and_Continue_Statements_When_Working_with_Loops_in_Go.md)。
