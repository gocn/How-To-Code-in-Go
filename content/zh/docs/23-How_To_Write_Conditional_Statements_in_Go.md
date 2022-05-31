# 如何在Go中编写条件语句

### 介绍

条件性语句是每一种编程语言的组成部分。通过条件语句，我们可以让代码有时运行，有时不运行，这取决于当时程序的条件。

当我们完全执行程序的每个语句时，我们并没有要求程序评估特定的条件。通过使用条件语句，程序可以确定某些条件是否被满足，然后被告知下一步该做什么。

让我们来看看一些使用条件语句的例子。

- 如果学生的考试成绩超过65%，报告她的成绩通过；如果没有，报告她的成绩不合格。
- 如果他的账户里有钱，就计算利息；如果没有，就收取罚款。
- 如果他们买了10个或更多的橙子，计算5%的折扣；如果他们买的少，就不买。

通过评估条件，并根据是否满足这些条件来分配代码运行，我们就是在写条件代码。

本教程将带你了解在Go编程语言中编写条件语句。

## If 语句

我们将从 `if` 语句开始，它将评估一个语句是真的还是假的，并且只在该语句为真的情况下运行代码。

在一个纯文本编辑器中，打开一个文件，写入以下代码：

```go
package main

import "fmt"

func main() {
	grade := 70

	if grade >= 65 {
		fmt.Println("Passing grade")
	}
}
```

在这段代码中，我们有一个变量`grade`，并给它一个整数值`70`。然后我们使用`if`语句来评估变量`grade`是否大于或等于（`>=`）`65`。如果它确实满足这个条件，我们告诉程序打印出[字符串](https://www.digitalocean.com/community/tutorials/an-introduction-to-working-with-strings-in-go) `Passing grade`。

将程序保存为`grade.go`，并在[终端窗口](https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-go)中用`go run grade.go`命令运行它。

在这种情况下，70分的成绩*符合大于或等于65分的条件，因此，一旦你运行该程序，你将收到以下输出：

```Output
Passing grade
```

现在让我们改变这个程序的结果，把`grade`变量的值改为`60`：

```go
package main

import "fmt"

func main() {
	grade := 60

	if grade >= 65 {
		fmt.Println("Passing grade")
	}
}
```

当我们保存并运行*这个*代码时，我们不会收到任何输出，因为条件*没有得到满足，我们也没有告诉程序执行另一条语句。

再举一个例子，让我们计算一个银行账户余额是否低于0。让我们创建一个名为`account.go`的文件，并编写以下程序：

```go
package main

import "fmt"

func main() {
	balance := -5

	if balance < 0 {
		fmt.Println("Balance is below 0, add funds now or you will be charged a penalty.")
	}
}
```

当我们用`go run account.go`运行该程序时，我们会收到以下输出：

```Output
Balance is below 0, add funds now or you will be charged a penalty.
```

在程序中，我们将变量`balance`初始化为`5`，即小于0。由于`balance`符合`if`语句的条件（`balance<0`），一旦我们保存并运行代码，我们将收到字符串的输出。同样，如果我们把余额改为0或一个正数，我们将不会收到任何输出。
## Else 语句

我们很可能希望程序在 `if`语句评估为假时也能有所作为。在我们的成绩例子中，我们希望输出成绩是合格还是不合格。

要做到这一点，我们将在上面的成绩条件中添加一个 `else` 语句，其结构如下：

```go
package main

import "fmt"

func main() {
	grade := 60

	if grade >= 65 {
		fmt.Println("Passing grade")
	} else {
		fmt.Println("Failing grade")
	}
}
```

由于成绩变量的值是`60`，`if`语句评估为假，所以程序不会打印出`Passing grade`。接下来的 `else` 语句告诉程序无论如何都要做一些事情。

当我们保存并运行该程序时，我们将收到以下输出：

```Output
Failing grade
```

如果我们重写程序，给成绩一个`65`或更高的值，我们将收到`Passing grade`的输出。

为了给银行账户的例子增加一个 `else` 语句，我们这样改写代码:

```go
package main

import "fmt"

func main() {
	balance := 522

	if balance < 0 {
		fmt.Println("Balance is below 0, add funds now or you will be charged a penalty.")
	} else {
		fmt.Println("Your balance is 0 or above.")
	}
}
```

```Output
Your balance is 0 or above.
```

在这里，我们把`balance`变量的值改为正数，这样`else`语句就会打印出来。为了让第一个`if`语句打印出来，我们可以把这个值改写成一个负数。

通过将`if`语句和`else`语句结合起来，你就构建了一个由两部分组成的条件语句，无论`if`条件是否满足，都会告诉计算机执行某些代码。

## Else if 语句

到目前为止，我们已经为条件语句提出了一个[布尔](https://www.digitalocean.com/community/tutorials/understanding-boolean-logic-in-go)选项，每个`if`语句的评估结果为真或假。在许多情况下，我们会希望一个程序能评估出两个以上的可能结果。为此，我们将使用**else if**语句，在Go中写成`else if`。`else if`或else if语句看起来和`if`语句一样，将评估另一个条件。

在银行账户程序中，我们可能希望在三种不同的情况下有三个离散的输出。

- 余额低于0
- 余额等于0
- 余额高于0

`else if`语句将被放在 `if` 语句和 `else` 语句之间，如下所示：

```go
package main

import "fmt"

func main() {
	balance := 522

	if balance < 0 {
		fmt.Println("Balance is below 0, add funds now or you will be charged a penalty.")
	} else if balance == 0 {
		fmt.Println("Balance is equal to 0, add funds soon.")
	} else {
		fmt.Println("Your balance is 0 or above.")
	}
}
```

现在，一旦我们运行该程序，有三种可能的输出：

- 如果变量`余额`等于`0`，我们将收到`else if`语句的输出（`余额等于0，尽快添加资金。）
- 如果变量`balance`被设置为一个正数，我们将收到`else`语句的输出（`你的余额为0或以上`）。
- 如果变量`balance`被设置为一个负数，输出将是`if`语句的字符串（`余额低于0，现在添加资金，否则将被收取罚款`）。

如果我们想有三个以上的可能性呢？我们可以通过在代码中写一个以上的`else if`语句来实现。

在`grade.go`程序中，让我们重写代码，以便有几个字母等级对应于数字等级的范围。

- 90分或以上相当于A级
- 80-89相当于B级
- 70-79相当于C级
- 65-69相当于D级
- 64分或以下相当于F级

要运行这段代码，我们将需要一个`if`语句，三个`else if`语句，以及一个处理所有失败情况的`else`语句。

让我们重写前面的例子中的代码，让字符串打印出每个字母等级。我们可以保持我们的`else`语句不变。

```go
package main

import "fmt"

func main() {
	grade := 60

	if grade >= 90 {
		fmt.Println("A grade")
	} else if grade >= 80 {
		fmt.Println("B grade")
	} else if grade >= 70 {
		fmt.Println("C grade")
	} else if grade >= 65 {
		fmt.Println("D grade")
	} else {
		fmt.Println("Failing grade")
	}
}
```

由于`else if`语句将按顺序评估，我们可以保持我们的语句相当基本。这个程序正在完成以下步骤。

1. 如果成绩大于90，程序将打印 "A级"，如果成绩小于90，程序将继续下一个语句...。
2. 如果成绩大于或等于80，程序将打印 "B级"，如果成绩在79或以下，程序将继续下一个语句......
3. 如果成绩大于或等于70，程序将打印 "C级"，如果成绩是69或更少，程序将继续下一个语句......
4. 如果成绩大于或等于65，程序将打印 "D级"，如果成绩是64或更少，程序将继续下一个语句......
5. 程序将打印 "成绩不合格"，因为上述所有的条件都没有满足。

## 嵌套的If语句

一旦你对 `if`, `else if`, 和 `else`语句感到满意，你就可以转到嵌套条件语句。我们可以使用嵌套的`if`语句来处理这样的情况：如果第一个条件执行为真，我们想检查第二个条件。为此，我们可以在另一个if-else 语句中设置一个if-else 语句。让我们来看看嵌套的`if`语句的语法。

```go
if statement1 { // outer if statement
	fmt.Println("true")

	if nested_statement { // nested if statement
		fmt.Println("yes")
	} else { // nested else statement
		fmt.Println("no")
	}

} else { // outer else statement
	fmt.Println("false")
}
```

这段代码可以产生一些可能的输出。

- 如果`statement1`评估为真，程序将评估`nested_statement`是否也评估为真。如果这两种情况都是真的，那么输出将是：

> ```Output
> true
> yes
> ```

- 然而，如果`statement1`评估为真，但`nested_statement`评估为假，那么输出将是：

> ```Output
> true
> no
> ```

- 而如果`statement1`评估为false，嵌套的if-else语句将不会运行，所以`else`语句将单独运行，输出结果为：

> ```Output
> false
> ```

我们也可以在代码中嵌套多个`if`语句：

```go
if statement1 { // outer if
	fmt.Println("hello world")

	if nested_statement1 { // first nested if
		fmt.Println("yes")

	} else if nested_statement2 { // first nested else if
		fmt.Println("maybe")

	} else { // first nested else
		fmt.Println("no")
	}

} else if statement2 { // outer else if
	fmt.Println("hello galaxy")

	if nested_statement3 { // second nested if
		fmt.Println("yes")
	} else if nested_statement4 { // second nested else if
		fmt.Println("maybe")
	} else { // second nested else
		fmt.Println("no")
	}

} else { // outer else
	statement("hello universe")
}
```

在这段代码中，除了 `else if` 语句外，每个 `if` 语句内都有一个嵌套的 `if` 语句。这将使每个条件内有更多的选项。

让我们用`grade.go`程序来看一个嵌套`if`语句的例子。可以首先检查一个成绩是否合格（大于或等于65%），然后评估数字成绩应该相当于哪个字母等级。如果成绩不合格，我们就不需要运行字母等级，而可以让程序报告该成绩不合格。修改后的代码和嵌套的 `if` 语句看起来是这样的：

```go
package main

import "fmt"

func main() {
	grade := 92
	if grade >= 65 {
		fmt.Print("Passing grade of: ")

		if grade >= 90 {
			fmt.Println("A")

		} else if grade >= 80 {
			fmt.Println("B")

		} else if grade >= 70 {
			fmt.Println("C")

		} else if grade >= 65 {
			fmt.Println("D")
		}

	} else {
		fmt.Println("Failing grade")
	}
}
```

如果我们在运行代码时将变量`grade`设置为整数值`92`，那么第一个条件就得到了满足，程序将打印出`Passing grade of:`。接下来，它将检查成绩是否大于或等于90，由于这个条件也被满足，它将打印出`A`。

如果我们在运行代码时将`grade`变量设置为`60`，那么第一个条件就没有得到满足，所以程序将跳过嵌套的`if`语句，向下移动到`else`语句，程序将打印出`Failing grade`。

当然，我们可以在此基础上增加更多的选项，并使用第二层嵌套的if语句。也许我们想对A+、A和A-的成绩分别进行评估。我们可以这样做，首先检查成绩是否合格，然后检查成绩是否在90分或以上，然后检查成绩是否在96分以上为A+：

```go
...
if grade >= 65 {
	fmt.Print("Passing grade of: ")

	if grade >= 90 {
		if grade > 96 {
			fmt.Println("A+")

		} else if grade > 93 && grade <= 96 {
			fmt.Println("A")

		} else {
			fmt.Println("A-")
		}
...
```

在这段代码中，对于设置为96的 `grade` 变量，程序将运行以下程序。

1. 检查该等级是否大于或等于65（真）。
2. 打印出 `Passing grade of:`
3. 检查成绩是否大于或等于90（真）。
4. 检查成绩是否大于96（假）。
5. 检查等级是否大于93，同时小于或等于96（真）。
6. 打印 "A"。
7. 离开这些嵌套的条件语句，继续执行剩余的代码

因此，成绩为96的程序的输出看起来是这样的：

```Output
Passing grade of: A
```

嵌套的`if`语句可以提供机会，在你的代码中添加几个特定级别的条件。

## 总结

通过使用像 `if` 语句这样的条件语句，你将对你的程序执行内容有更大的控制。条件性语句告诉程序要评估是否满足某个条件。如果满足条件，它将执行特定的代码，但如果不满足条件，程序将继续执行其他代码。

要继续练习条件语句，请尝试使用不同的[运算符](https://www.digitalocean.com/community/tutorials/how-to-do-math-in-go-with-operators)来获得对条件语句的更多熟悉。