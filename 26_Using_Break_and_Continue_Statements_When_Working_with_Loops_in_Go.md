# Using Break and Continue Statements When Working with Loops in Go
# 在循环中使用 Break 和 Continue
### Introduction
### 介绍

Using **for loops** in Go allow you to automate and repeat tasks in an efficient manner.
在 Go 中使用 **for 循环**可以让您以有效的方式自动化重复任务。

Learning how to control the operation and flow of loops will allow for customized logic in your program. You can control your loops with the `break` and `continue` statements.
学习如何控制循环的操作和流程将允许在您的程序中自定义逻辑。您可以使用 `break` 和 `continue` 语句控制循环

## Break Statement
## Break 语句

In Go, the `break` statement terminates execution of the current loop. A `break` is almost always paired with a [conditional `if` statement](https://www.digitalocean.com/community/tutorials/how-to-write-conditional-statements-in-go).
在 Go 中， `break` 语句终止当前循环的执行。`break`几乎总是与[条件`if`语句](https://www.digitalocean.com/community/tutorials/how-to-write-conditional-statements-in-go)配对。

Let’s look at an example that uses the `break` statement in a `for` loop:
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

This small program creates a `for` loop that will iterate while `i` is less than `10`.
这个小程序创建了一个 `for`循环，该循环在当 `i` 小于 `10` 时迭代。

Within the `for` loop, there is an `if` statement. The `if` statement tests the condition of `i` to see if the value is less than `5`. If the value of `i` is not equal to `5`, the loop continues and prints out the value of `i`. If the value of `i` is equal to `5`, the loop will execute the `break` statement, print that it is `Breaking out of loop`, and stop executing the loop. At the end of the program we print out `Exiting program` to signify that we have exited the loop.
在 `for` 循环中，有一个`if`语句。该 `if`语句会检查 `i` 的值是否小于 `5`。如果 `i` 的值不等于 `5`，则循环继续并打印出 `i` 的值。如果 `i` 的值等于 `5`，则循环将执行 `break` 语句，打印 `Breaking out of loop`，并停止循环。在程序结束时，我们打印出 `Exiting program` 表示我们已经退出了循环。

When we run this code, our output will be the following:
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

This shows that once the integer `i` is evaluated as equivalent to 5, the loop breaks, as the program is told to do so with the `break` statement.
这表明，一旦整数 `i` 被检查为等于 5，循环就会中断，因为程序使用 `break` 语句来这样做。

### Nested Loops
### 嵌套循环

It is important to remember that the `break` statement will only stop the execution of the inner most loop it is called in. If you have a nested set of loops, you will need a break for each loop if desired.
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

In this program, we have two loops. While both loops iterate 5 times, each has a conditional `if` statement with a `break` statement. The outer loop will break if the value of `outer` equals `3`. The inner loop will break if the value of `inner` is `2`.
在这个程序中，我们有两个循环。虽然两个循环都迭代 5 次，但每个循环都有一个带有 `break` 语句的 `if` 条件语句。`outer` 如果等于 `3`，外部循环将中断。如果 `inner` 值为 `2` ，内部循环将中断。

If we run the program, we can see the output:
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

Notice that each time the inner loop breaks, the outer loop does not break. This is because `break` will only break the inner most loop it is called from.
请注意，每次内循环中断时，外循环都不会中断。这是因为`break` 只会中断调用它的最内层循环。

We have seen how using `break` will stop the execution of a loop. Next, let’s look at how we can continue the iteration of a loop.
我们已经看到 `break` 是如何停止循环的。接下来，让我们看看 `continue` 如何继续循环的。

## Continue Statement
## Continue 语句

The `continue` statement is used when you want to skip the remaining portion of the loop, and return to the top of the loop and continue a new iteration.
当您想要跳过循环的剩余部分并返回循环顶部继续新的迭代时，使用 `continue` 语句。

As with the `break` statement, the `continue` statement is commonly used with a conditional `if` statement.
与 `break` 语句一样，`continue` 语句通常与  `if`  条件语句一起使用。

Using the same `for` loop program as in the preceding [Break Statement](https://www.digitalocean.com/community/tutorials/how-to-use-break-and-continue-statements-when-working-with-loops-in-go#break-statement) section, we’ll use a `continue` statement rather than a `break` statement:
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

The difference in using the `continue` statement rather than a `break` statement is that our code will continue despite the disruption when the variable `i` is evaluated as equivalent to `5`. Let’s look at our output:
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

Here we see that the line `The value of i is 5` never occurs in the output, but the loop continues after that point to print lines for the numbers 6-10 before leaving the loop.
在这里，我们看到 `The value of i is 5` 没有出现在输出中，但循环在该点之后继续打印数字 6-10 的行，然后结束循环。

You can use the `continue` statement to avoid deeply nested conditional code, or to optimize a loop by eliminating frequently occurring cases that you would like to reject.
您可以使用 `continue` 语句来避免深度嵌套的条件代码，或者通过消除您想要拒绝的一些频繁发生的情况来优化循环。

The `continue` statement causes a program to skip certain factors that come up within a loop, but then continue through the rest of the loop.
`continue` 语句能够让程序跳过循环中出现的某些情况，然后继续循环的其余部分。

## Conclusion
## 结论

The `break` and `continue` statements in Go will allow you to use `for` loops more effectively in your code.
Go 中的 `break` and `continue` 语句将允许您在代码中更高效地使用 `for` 循环。
