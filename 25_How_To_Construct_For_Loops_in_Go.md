# How To Construct For Loops in Go
# 如何在 Go 中构造 for 循环
### Introduction
### 介绍

In computer programming, a _loop_ is a code structure that loops around to repeatedly execute a piece of code, often until some condition is met. Using loops in computer programming allows you to automate and repeat similar tasks multiple times. Imagine if you had a list of files that you needed to process, or if you wanted to count the number of lines in an article. You would use a loop in your code to solve these types of problems.
在计算机编程中，_循环_ 是在满足某些条件之前循环重复执行一段代码的代码结构。在计算机编程中使用循环可以让您自动并重复地执行类似的任务。想象一下，如果您有一个需要处理的文件列表，或者您想计算一篇文章的行数。您就可以在代码中使用循环来解决这样的问题。

In Go, a `for` loop implements the repeated execution of code based on a loop counter or loop variable. Unlike other programming languages that have multiple looping constructs such as `while`, `do`, etc., Go only has the `for` loop. This serves to make your code clearer and more readable, since you do not have to worry with multiple strategies to achieve the same looping construct. This enhanced readability and decreased cognitive load during development will also make your code less prone to error than in other languages.
在 Go 中，`for` 循环是基于循环计数器或循环变量实现代码的重复执行。与其他具有多个循环结构（例如 `while` ， `do` 等 ）的编程语言不同，Go 只有 `for` 循环。这有助于使您的代码更清晰和更具可读性，因为您不必担心会有多种策略来实现相同的循环结构。在开发过程中，这种强可读性和低认知负担也将使您的代码比其他语言更不容易出错。

In this tutorial, you will learn how Go’s `for` loop works, including the three major variations of its use. We’ll start by showing how to create different types of `for` loops, followed by how to loop through [sequential data types in Go](https://www.digitalocean.com/community/tutorials/understanding-arrays-and-slices-in-go). We’ll end by explaining how to use nested loops.
在本教程中，您将了解 Go 中 `for` 循环是如何工作的，包括其使用的三个主要变体。我们将首先展示如何创建不同类型的 `for` 循环，然后介绍如何[在 Go 中遍历顺序数据类型](https://www.digitalocean.com/community/tutorials/understanding-arrays-and-slices-in-go)。最后，我们将解释如何使用嵌套循环。

## Declaring ForClause and Condition Loops
## 声明 ForClause 和 条件循环

In order to account for a variety of use cases, there are three distinct ways to create `for` loops in Go, each with their own capabilities. These are to create a `for` loop with a **Condition**, a **ForClause**, or a **RangeClause**. In this section, we will explain how to declare and use the ForClause and Condition variants.
为了适应各种用例，在 Go 中创建 `for` 循环有三种不同的方法，每种方法都有自己的功能。这些是使用**Condition**、**ForClause**或**RangeClause** 创建 `for` 循环。在本节中，我们将解释如何声明和使用 ForClause 和 Condition 变体。

Let’s look at how we can use a `for` loop with the ForClause first.
让我们先看看如何在 ForClause 中使用 `for` 循环。

A _ForClause loop_ is defined as having an _initial statement_, followed by a _condition_, and then a _post statement_. These are arranged in the following syntax:
_ForClause循环_ 被定义为有一个 _初始语句_，后跟一个 _条件_，然后是一个 _后置语句_。它们按以下语法排列：

```
for [ Initial Statement ] ; [ Condition ] ; [ Post Statement ] {
    [Action]
}
```

To explain what the preceding components do, let’s look at a `for` loop that increments through a specified range of values using the ForClause syntax:
为了解释前面组成元素的作用，让我们看一个使用 ForClause 语法在指定值范围内递增的 `for` 循环：

Let’s break this loop down and identify each part.
让我们分解这个循环并识别每个部分

The first part of the loop is `i := 0`. This is the initial statement:
循环的第一部分是 `i := 0` ，这是初始语句：

```
for i := 0; i < 5; i++ {
	fmt.Println(i)
}
```

It states that we are declaring a variable called `i`, and setting the initial value to `0`.
它表明我们正在声明一个名为 `i` 的变量，并将初始值设置为 `0`。

Next is the condition:
接下来是条件：

```
for i := 0; i < 5; i++ {
	fmt.Println(i)
}
```

In this condition, we stated that while `i` is less than the value of `5`, the loop should continue looping.
在这种情况下，我们声明当 `i` 小于 `5` 时，循环应该继续。

Finally, we have the post statement:
最后，是一个后置语句：

```
for i := 0; i < 5; i++ {
	fmt.Println(i)
}
```

In the post statement, we increment the loop variable `i` up by one each time an iteration occurs using the `i++` [increment](https://golang.org/ref/spec#IncDec_statements) operator.
在后置语句中，循环遍历 `i` 以 `i++` [增量](https://golang.org/ref/spec#IncDec_statements)运算符进行迭代加一。

When we run this program, the output looks like this:
当我们运行这个程序时，输出如下所示：

```
Output
0
1
2
3
4
```

The loop ran 5 times. Initially, it set `i` to `0`, and then checked to see if `i` was less than `5`. Since the value of `i` was less than `5`, the loop executed and the action of `fmt.Println(i)` was executed. After the loop finished, the post statement of `i++` was called, and the value of `i` was incremented by 1.
循环运行了 5 次。最初，设置 `i` 为 `0`，然后检查是否 `i` 小于`5`。由于 `i` 的值小于 `5` ，因此执行了循环并执行了 `fmt.Println(i)` 的动作。循环结束时， 调用 `i++` 语句，使`i` 的值加 1。

**Note:** Keep in mind that in programming we tend to begin at index 0, so that is why although 5 numbers are printed out, they range from 0-4.
**注意：** 请记住，在编程中我们倾向于从索引 0 开始，这就是为什么虽然打印了 5 个数字，但它们的范围是 0-4。

We aren’t limited to starting at 0 or ending at a specified value. We can assign any value to our initial statement, and also stop at any value in our post statement. This allows us to create any desired range to loop through:
我们不限于从 0 开始或以指定值结束。我们可以为我们的初始语句分配任何值，也可以在我们的后置语句中赋予任何值。这允许我们创建任何所需的范围来循环：

```
for i := 20; i < 25; i++ {
	fmt.Println(i)
}
```
Here, the iteration goes from 20 (inclusive) to 25 (exclusive), so the output looks like this:
在这里，迭代会从 20（包括）到 25（不包括），所以输出如下所示：

```
Output
20
21
22
23
24
```

We can also use our post statement to increment at different values. This is similar to `step` in other languages:
我们还可以使用我们的后置语句以不同的值递增。这类似于其他语言中的 `step`：

First, let’s use a post statement with a positive value:
首先，让我们使用正值递增的后置语句：

```
for i := 0; i < 15; i += 3 {
	fmt.Println(i)
}
```

In this case, the `for` loop is set up so that the numbers from 0 to 15 print out, but at an increment of 3, so that only every third number is printed, like so:
在这种情况下，设置 `for` 循环以便打印出从 0 到 15 的数字，但增量为 3，因此每三个数字打印一次，如下所示：

```
Output
0
3
6
9
12
```

We can also use a negative value for our post statement argument to iterate backwards, but we’ll have to adjust our initial statement and condition arguments accordingly:
我们也可以为我们的后置语句使用负值来向后迭代，但我们必须相应地调整我们的初始语句和条件参数：

```
for i := 100; i > 0; i -= 10 {
	fmt.Println(i)
}
```

Here, we set `i` to an initial value of `100`, use the condition of `i < 0` to stop at `0`, and the post statement decrements the value by 10 with the `-=` operator. The loop begins at `100` and ends at `0`, decreasing by 10 with each iteration. We can see this occur in the output:
在这里，我们将 `i` 初始值设置为`100`，使用 `i < 0` 的条件在处停止，并且后置语句使用 `-=` 运算符将值减 10。循环开始于 `100` 并结束于 `0`，每次迭代减少 10。我们可以在输出中看到这种情况：

```
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

You can also exclude the initial statement and the post statement from the `for` syntax, and only use the condition. This is what is known as a _Condition loop_:
您也可以从语法中不使用初始语句和后置语句，而只使用条件。这就是所谓的 _条件循环_：

```
i := 0
for i < 5 {
	fmt.Println(i)
	i++
}
```

This time, we declared the variable `i` separately from the `for` loop in the preceding line of code. The loop only has a condition clause that checks to see if `i` is less than `5`. As long as the condition evaluates to `true`, the loop will continue to iterate.
这一次，我们在前面的代码行中将变量声明为 `i` 与循环分开。该循环只有一个条件子句，用于检查 `i` 是否小于`5`。只要条件为 `true`，循环就会继续迭代。

Sometimes you may not know the number of iterations you will need to complete a certain task. In that case, you can omit all statements, and use the `break` keyword to exit execution:
有时您可能不知道完成某项任务所需的迭代次数。在这种情况下，您可以省略所有语句，并使用 `break` 关键字退出执行：

```
for {
	if someCondition {
		break
	}
	// do action here
}
```

An example of this may be if we are reading from an indeterminately sized structure like a [buffer](https://golang.org/pkg/bytes/#Buffer) and we don’t know when we will be done reading:
这方面的一个例子可能是，如果我们正在从一个不确定大小的结构（如[缓冲区](https://golang.org/pkg/bytes/#Buffer)）中读取，并且我们不知道何时完成读取：
```
// buffer.go
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

In the preceding code, `buf :=bytes.NewBufferString("one\ntwo\nthree\nfour\n")` declares a buffer with some data. Because we don’t know when the buffer will finish reading, we create a `for` loop with no clause. Inside the `for` loop, we use `line, err := buf.ReadString('\n')` to read a line from the buffer and check to see if there was an error reading from the buffer. If there was, we address the error, and [use the `break` keyword to exit the for loop](https://www.digitalocean.com/community/tutorials/using-break-and-continue-statements-when-working-with-loops-in-go). With these `break` points, you do not need to include a condition to stop the loop.
在代码中，`buf :=bytes.NewBufferString("one\ntwo\nthree\nfour\n")` 声明了一个包含一些数据的缓冲区。因为我们不知道缓冲区何时会完成读取，所以我们创建了一个没有子句的 `for` 循环。在 `for` 循环内部，我们使用 `line, err := buf.ReadString('\n')` 从缓冲区读取一行并检查从缓冲区读取是否有错误。如果有，我们解决错误，并[使用 `break` 关键字退出 for 循环](https://www.digitalocean.com/community/tutorials/using-break-and-continue-statements-when-working-with-loops-in-go)。有了`break`，您就不需要使用停止循环的条件。

In this section, we learned how to declare a ForClause loop and use it to iterate through a known range of values. We also learned how to use a Condition loop to iterate until a specific condition was met. Next, we’ll learn how the RangeClause is used for iterating through sequential data types.
在本节中，我们学习了如何声明 ForClause 循环并使用它来迭代已知范围的值。我们还学习了如何使用条件循环进行迭代，直到满足特定条件。接下来，我们将了解 RangeClause 如何用于迭代顺序数据类型。

## Looping Through Sequential Data Types with RangeClause
## 使用 RangeClause 循环遍历顺序数据类型

It is common in Go to use `for` loops to iterate over the elements of sequential or collection data types like [slices, arrays](https://www.digitalocean.com/community/tutorials/understanding-arrays-and-slices-in-go), and [strings](https://www.digitalocean.com/community/tutorials/an-introduction-to-working-with-strings-in-go). To make it easier to do so, we can use a `for` loop with _RangeClause_ syntax. While you can loop through sequential data types using the ForClause syntax, the RangeClause is cleaner and easier to read.
在 Go 中，使用 `for` 循环来迭代连续或集合数据类型（如[切片、数组](https://www.digitalocean.com/community/tutorials/understanding-arrays-and-slices-in-go)和[字符串](https://www.digitalocean.com/community/tutorials/an-introduction-to-working-with-strings-in-go)）的元素是很常见的。为了更容易做到这一点，我们可以使用带有 _RangeClause_ 语法的 `for` 循环。虽然您可以使用 ForClause 语法遍历顺序数据类型，但 RangeClause 更简洁且更易于阅读。

Before we look at using the RangeClause, let’s look at how we can iterate through a slice by using the ForClause syntax:
在我们研究使用 RangeClause 之前，让我们看看如何使用 ForClause 语法遍历切片：


```
package main

import "fmt"

func main() {
	sharks := []string{"hammerhead", "great white", 		"dogfish", "frilled", "bullhead", "requiem"}

	for i := 0; i < len(sharks); i++ {
		fmt.Println(sharks[i])
	}
}
```

Running this will give the following output, printing out each element of the slice:
运行它将给出以下输出，打印出切片的每个元素：

```
Output
hammerhead
great white
dogfish
frilled
bullhead
requiem
```

Now, let’s use the RangeClause to perform the same set of actions:
现在，让我们使用 RangeClause 执行相同的操作：

```
package main

import "fmt"

func main() {
	sharks := []string{"hammerhead", "great white", 		"dogfish", "frilled", "bullhead", "requiem"}

	for i, shark := range sharks {
		fmt.Println(i, shark)
	}
}
```

In this case, we are printing out each item in the list. Though we used the variables `i` and `shark`, we could have called the variable any other [valid variable name](https://www.digitalocean.com/community/tutorials/how-to-use-variables-and-constants-in-go#naming-variables-rules-and-style) and we would get the same output:
在这种情况下，我们打印出列表中的每个项。虽然我们使用了变量 `i` 和 `shark` ，但我们可以将变量称为任何其他[有效的变量名](https://www.digitalocean.com/community/tutorials/how-to-use-variables-and-constants-in-go#naming-variables-rules-and-style)，我们会得到相同的输出：

```
Output
0 hammerhead
1 great white
2 dogfish
3 frilled
4 bullhead
5 requiem
```

When using `range` on a slice, it will always return two values. The first value will be the index that the current iteration of the loop is in, and the second is the value at that index. In this case, for the first iteration, the index was `0`, and the value was `hammerhead`.
在切片上使用 `range` 时，它将始终返回两个值。第一个值将是当前迭代所在的索引，第二个是该索引处的值。在这种情况下，对于第一次迭代，索引是 `0`，值是 `hammerhead`。

Sometimes, we only want the value inside the slice elements, not the index. If we change the preceding code to only print out the value however, we will receive a compile time error:
有时，我们只想要切片元素内的值，而不是索引。但是，如果我们将前面的代码更改为仅打印值，我们将收到编译时错误：


```
package main

import "fmt"

func main() {
	sharks := []string{"hammerhead", "great white", "dogfish", "frilled", "bullhead", "requiem"}

	for i, shark := range sharks {
		fmt.Println(shark)
	}
}
```

```
Output
src/range-error.go:8:6: i declared and not used
```

Because `i` is declared in the `for` loop, but never used, the compiler will respond with the error of `i declared and not used`. This is the same error that you will receive in Go any time you declare a variable and don’t use it.
因为 `i` 在 `for` 循环中声明了，但从未使用过，编译器会报告 `i declared and not used`。每当您声明一个变量并且不使用它时，您都会在 Go 中收到相同的错误。

Because of this, Go has the [blank identifier](https://golang.org/ref/spec#Blank_identifier) which is an underscore (`_`). In a `for` loop, you can use the blank identifier to ignore any value returned from the `range` keyword. In this case, we want to ignore the index, which is the first argument returned.
因此，Go 具有[空白标识符](https://golang.org/ref/spec#Blank_identifier)，即下划线 ( `_` )。在 `for` 循环中，您可以使用空白标识符来忽略从 `range` 关键字返回的任何值。在这种情况下，我们要忽略索引，它是返回的第一个参数。


```
package main

import "fmt"

func main() {
	sharks := []string{"hammerhead", "great white", 		"dogfish", "frilled", "bullhead", "requiem"}

	for _, shark := range sharks {
		fmt.Println(shark)
	}
}
```

```
Output
hammerhead
great white
dogfish
frilled
bullhead
requiem
```

This output shows that the `for` loop iterated through the slice of strings, and printed each item from the slice without the index.
输出显示 `for` 循环遍历字符串切片，并打印切片中每个项，且不会打印索引。

You can also use `range` to add items to a list:
您也可以使用 `range` 将项目添加到列表中：


```
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

```
Output
['hammerhead', 'great white', 'dogfish', 'frilled', 'bullhead', 'requiem', 'shark', 'shark', 'shark', 'shark', 'shark', 'shark']
```

Here, we have added a placeholder string of `"shark"` for each item of the length of the `sharks` slice.
在这里，我们为切片 `"shark"` 添加了切片长度的占位符字符串 `sharks`。

Notice that we didn’t have to use the blank identifier `_` to ignore any of the return values from the `range` operator. Go allows us to leave out the entire declaration portion of the `range` statement if we don’t need to use either of the return values.
请注意，我们不必使用空白标识符 `_` 来忽略 `range` 运算符的任何返回值。如果我们不需要使用任何一个 `range`  的返回值，Go 允许我们省略语句的整个声明部分。

We can also use the `range` operator to fill in values of a slice:
我们也可以使用 `range` 运算符来填充切片的值

```
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

In this example, the slice `integers` is initialized with ten empty values, but the `for` loop sets all the values in the list like so:
在这个例子中，切片 `integers` 初始化了十个空值，但 `for` 循环会设置列表中的所有值，如下所示：

```
Output
[0 0 0 0 0 0 0 0 0 0]
[0 1 2 3 4 5 6 7 8 9]
```

The first time we print the value of the slice `integers`, we see all zeros. Then we iterate through each index and set the value to the current index. Then when we print the value of `integers` a second time, showing that they all now have a value of `0` through `9`.
第一次打印切片 `integers` 的值时，我们看到全为零。然后我们遍历每个索引并将值设置为当前索引。然后当我们第二次打印值`integers` 时，显示它们现在都具有`0` 到 `9` 的值。

We can also use the `range` operator to iterate through each character in a string:
我们也可以使用 `range` 运算符来遍历字符串中的每个字符：

```
package main

import "fmt"

func main() {
	sammy := "Sammy"

	for _, letter := range sammy {
		fmt.Printf("%c\n", letter)
	}
}
```

```
Output
S
a
m
m
y
```

When iterating through a [map](https://www.digitalocean.com/community/tutorials/understanding-maps-in-go), `range` will return both the **key** and the **value**:
当遍历 [map](https://www.digitalocean.com/community/tutorials/understanding-maps-in-go) 时，`range`将返回**键**和**值**：
```
package main

import "fmt"

func main() {
	sammyShark := map[string]string{"name": "Sammy", "animal": "shark", "color": "blue", "location": "ocean"}

	for key, value := range sammyShark {
		fmt.Println(key + ": " + value)
	}
}
```

```
Output
color: blue
location: ocean
name: Sammy
animal: shark
```

**Note:** It is important to note that the order in which a map returns is random. Each time you run this program you may get a different result.
注意：map 返回的顺序是随机的。每次运行此程序时，您可能会得到不同的结果。

Now that we have learned how to iterate over sequential data with `range` `for` loops, let’s look at how to use loops inside of loops.
现在我们已经学会了如何使用 `range` `for` 循环来遍历数据，下面让我们看看如何在循环中使用循环。

## Nested For Loops
## 嵌套 For 循环

Loops can be nested in Go, as they can with other programming languages. _Nesting_ is when we have one construct inside of another. In this case, a nested loop is a loop that occurs within another loop. These can be useful when you would like to have a looped action performed on every element of a data set.
就像其他编程语言一样，在 Go 中循环也是可以嵌套的。_嵌套_ 是当我们在一个结构内又使用了一个结构。在这种情况下，嵌套循环是发生在另一个循环中的循环。当您希望对数据集的每个元素执行循环操作时，这些可能很有用。

Nested loops are structurally similar to [nested `if` statements](https://www.digitalocean.com/community/tutorials/how-to-write-conditional-statements-in-go#nested-if-statements). They are constructed like so:
嵌套循环在结构上类似于[嵌套`if`语句](https://www.digitalocean.com/community/tutorials/how-to-write-conditional-statements-in-go#nested-if-statements)。它的构造如下：

```
for {
    [Action]
    for {
        [Action]  
    }
}
```

The program first encounters the outer loop, executing its first iteration. This first iteration triggers the inner, nested loop, which then runs to completion. Then the program returns back to the top of the outer loop, completing the second iteration and again triggering the nested loop. Again, the nested loop runs to completion, and the program returns back to the top of the outer loop until the sequence is complete or a break or other statement disrupts the process.
程序首先遇到外循环，执行它的第一次迭代。第一次迭代触发内部嵌套循环，然后运行完成。然后程序返回到外部循环的顶部，完成第二次迭代并再次触发嵌套循环。同样，嵌套循环运行到完成，程序返回到外部循环的顶部，直到序列完成或中断或其他语句中断该过程。

Let’s implement a nested `for` loop so we can take a closer look. In this example, the outer loop will iterate through a slice of integers called `numList`, and the inner loop will iterate through a slice of strings called `alphaList`.
让我们实现一个嵌套 `for` 循环，以便我们仔细看看。在这个例子中，外层循环将遍历一个名为 `numList` 的整数切片，而内层循环将遍历一个名为 `alphaList` 的字符串切片。

```
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

When we run this program, we’ll receive the following output:
当我们运行这个程序时，我们将得到以下输出：

```
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

The output illustrates that the program completes the first iteration of the outer loop by printing `1`, which then triggers completion of the inner loop, printing `a`, `b`, `c` consecutively. Once the inner loop has completed, the program returns to the top of the outer loop, prints `2`, then again prints the inner loop in its entirety (`a`, `b`, `c`), etc.
输出说明程序通过打印 `1` 完成了外循环的第一次迭代，然后连续触发内循环打印的完成。内循环完成后，程序返回到外循环的顶部，打印 `2`，然后再次完整打印内循环（`a`, `b`, `c`）等。

Nested `for` loops can be useful for iterating through items within slices composed of slices. In a slice composed of slices, if we use just one `for` loop, the program will output each internal list as an item:
嵌套 `for` 循环可用于遍历由切片组成的切片中的项。在由切片组成的切片中，如果我们只使用一个 `for` 循环，程序会将每个内部列表作为一项输出：

```
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

```
Output
[0 1 2]
[-1 -2 -3]
[9 8 7]
```

In order to access each individual item of the internal slices, we’ll implement a nested `for` loop:
为了访问内部切片的每个单独项，我们将实现一个嵌套`for`循环：
```
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

```
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

When we use a nested `for` loop here, we are able to iterate over the individual items contained in the slices.
当我们在这里使用嵌套 `for` 循环时，我们能够迭代切片中包含的各个项。

## Conclusion
## 结论

In this tutorial we learned how to declare and use `for` loops to solve for repetitive tasks in Go. We also learned the three different variations of a `for` loop and when to use them. To learn more about `for` loops and how to control the flow of them, read [Using Break and Continue Statements When Working with Loops in Go](https://www.digitalocean.com/community/tutorials/using-break-and-continue-statements-when-working-with-loops-in-go).
在本教程中，我们学习了如何声明和使用 `for` 循环来解决 Go 中的重复任务。我们还学习了 `for` 循环的三种不同变体以及何时使用它们。要了解有关 `for` 循环以及如何控制它们的流程的更多信息，请阅读[在 Go 中使用循环时的 Break 和 Continue 语句](https://github.com/gocn/How-To-Code-in-Go/blob/main/26_Using_Break_and_Continue_Statements_When_Working_with_Loops_in_Go.md)。
