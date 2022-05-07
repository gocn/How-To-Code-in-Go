# 在循环中使用 Break 和 Continue
### 介绍

在 Go 中使用 **for 循环**可以让您以有效的方式自动化重复任务。

学习如何控制循环的操作和流程将允许在您的程序中自定义逻辑。您可以使用 `break` 和 `continue` 语句控制循环

## Break 语句

在 Go 中， `break` 语句终止当前循环的执行。`break`几乎总是与[条件`if`语句](https://www.digitalocean.com/community/tutorials/how-to-write-conditional-statements-in-go)配对。

让我们看一个在循环中使用`break`语句的示例：

```
package main

import "fmt"

func main() {
	for i := 0; i < 10; i++ {
		if i == 5 {
			fmt.Println("Breaking out of loop")
			break // break here
		}
		fmt.Println("The value of i is", i)
	}
	fmt.Println("Exiting program")
}
```

这个小程序创建了一个 `for`循环，该循环在当 `i` 小于 `10` 时迭代。

在 `for` 循环中，有一个`if`语句。该 `if`语句会检查 `i` 的值是否小于 `5`。如果 `i` 的值不等于 `5`，则循环继续并打印出 `i` 的值。如果 `i` 的值等于 `5`，则循环将执行 `break` 语句，打印 `Breaking out of loop`，并停止循环。在程序结束时，我们打印出 `Exiting program` 表示我们已经退出了循环。

当我们运行此代码时，输出将如下所示：

```
Output
The value of i is 0
The value of i is 1
The value of i is 2
The value of i is 3
The value of i is 4
Breaking out of loop
Exiting program
```

这表明，一旦整数 `i` 被检查为等于 5，循环就会中断，因为程序使用 `break` 语句来这样做。

### 嵌套循环

要记住，`break` 语句只会停止调用它的最内层循环的执行。如果您有一组嵌套循环，如果需要，您将需要为每个循环设置break。

```
package main

import "fmt"

func main() {
	for outer := 0; outer < 5; outer++ {
		if outer == 3 {
			fmt.Println("Breaking out of outer loop")
			break // break here
		}
		fmt.Println("The value of outer is", outer)
		for inner := 0; inner < 5; inner++ {
			if inner == 2 {
				fmt.Println("Breaking out of inner loop")
				break // break here
			}
			fmt.Println("The value of inner is", inner)
		}
	}
	fmt.Println("Exiting program")
}
```

在这个程序中，我们有两个循环。虽然两个循环都迭代 5 次，但每个循环都有一个带有 `break` 语句的 `if` 条件语句。`outer` 如果等于 `3`，外部循环将中断。如果 `inner` 值为 `2` ，内部循环将中断。

如果我们运行程序，可以看到输出：

```
Output
The value of outer is 0
The value of inner is 0
The value of inner is 1
Breaking out of inner loop
The value of outer is 1
The value of inner is 0
The value of inner is 1
Breaking out of inner loop
The value of outer is 2
The value of inner is 0
The value of inner is 1
Breaking out of inner loop
Breaking out of outer loop
Exiting program
```

请注意，每次内循环中断时，外循环都不会中断。这是因为`break` 只会中断调用它的最内层循环。

我们已经看到 `break` 是如何停止循环的。接下来，让我们看看 `continue` 如何继续循环的。

## Continue 语句

当您想要跳过循环的剩余部分并返回循环顶部继续新的迭代时，使用 `continue` 语句。

与 `break` 语句一样，`continue` 语句通常与  `if`  条件语句一起使用。

使用与前面的[Break 语句](https://www.digitalocean.com/community/tutorials/how-to-use-break-and-continue-statements-when-working-with-loops-in-go#break-statement)部分相同的 `for` 循环程序，我们将使用 `continue` 语句而不是 `break` 语句：

```
package main

import "fmt"

func main() {
	for i := 0; i < 10; i++ {
		if i == 5 {
			fmt.Println("Continuing loop")
			continue // break here
		}
		fmt.Println("The value of i is", i)
	}
	fmt.Println("Exiting program")
}
```

使用 `continue` 语句而不是 `break` 语句的区别在于，当变量 `i` 等于 `5` 时，尽管中断了，我们的代码仍将继续执行。让我们看看我们的输出：

```
Output
The value of i is 0
The value of i is 1
The value of i is 2
The value of i is 3
The value of i is 4
Continuing loop
The value of i is 6
The value of i is 7
The value of i is 8
The value of i is 9
Exiting program
```

在这里，我们看到 `The value of i is 5` 没有出现在输出中，但循环在该点之后继续打印数字 6-10 的行，然后结束循环。

您可以使用 `continue` 语句来避免深度嵌套的条件代码，或者通过消除您想要拒绝的一些频繁发生的情况来优化循环。

`continue` 语句能够让程序跳过循环中出现的某些情况，然后继续循环的其余部分。

## 结论

Go 中的 `break` and `continue` 语句将允许您在代码中更高效地使用 `for` 循环。
