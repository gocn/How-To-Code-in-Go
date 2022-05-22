# 了解 Go 中的布尔逻辑

布尔数据类型（`bool`）可以是两个值中的一个，即**真**或**假**。布尔型数据在编程中被用来进行比较和控制程序的流程。

Boolean 在 Go 中的数据类型是`bool`，全部小写。值 `true` 和 `false` 总是分别用小写的 `t` 和 `f`，因为它们是 Go 中的特殊值。

本教程将涵盖你需要了解 `bool` 数据类型如何工作的基础知识，包括布尔比较、逻辑运算符和真值表。

## 比较运算符

在编程中，比较运算符被用来比较数值，并计算为一个单一的布尔值，即真或假。

下表展示了布尔比较运算符。

运算符的含义

`==` 等于

`!=` 不等于

`<` 少于

`>` 大于 

`<=` 少于等于  

`>=` 大于等于


为了了解这些运算符的工作原理，我们在 Go 程序中把两个整数分配给两个变量：

```go
x := 5
y := 8
```

在这个例子中，由于 `x` 的值是`5`，`y` 的值是 `8`，所以 `x` 小于 `y`。

使用这两个变量和它们的相关值，让我们回忆一下之前的运算符。在这个程序中，你用 Go 打印出每个比较运算符的值是真还是假。为了帮助更好地理解这个输出，Go 打印一个字符串来显示它正在计算的内容。

```go
package main
    
import "fmt"
    
func main() {
    x := 5
    y := 8
    
    fmt.Println("x == y:", x == y)
    fmt.Println("x != y:", x != y)
    fmt.Println("x < y:", x < y)
    fmt.Println("x > y:", x > y)
    fmt.Println("x <= y:", x <= y)
    fmt.Println("x >= y:", x >= y)
}
```

```go
Output
x == y: false
x != y: true
x < y: true
x > y: false
x <= y: true
x >= y: false
```

遵循数学逻辑，Go 从表达式中计算了以下内容：

* 5（`x`）等于 8（`y`）吗？**假**
* 5 不等于 8 吗？**真**
* 5 小于 8 吗？**真** 
* 5 是否大于 8？**假***
* 5 是否小于或等于 8？**真** 
* 5 不小于或等于 8 吗？**假***

虽然这里使用的是整数，但你可以用浮点数来代替。

字符串也可以和布尔运算符一起使用。它们是区分大小写的，除非你使用一个额外的字符串方法。

你可以看一下字符串在实践中是如何比较的：

```go
Sammy := "Sammy"
sammy := "sammy"

fmt.Println("Sammy == sammy: ", Sammy == sammy)
```

```go
Output
Sammy == sammy:  false
```

字符串 `Sammy` 不等于字符串 `sammy`，因为它们不完全相同；一个以大写字母 `S` 开头，另一个以小写字母 `s` 开头。但是，如果你添加了另一个变量，该变量被分配了 `Sammy` 的值，那么它们的值将相等。

```go
Sammy := "Sammy"
sammy := "sammy"
alsoSammy := "Sammy"
    
fmt.Println("Sammy == sammy: ", Sammy == sammy)
fmt.Println("Sammy == alsoSammy", Sammy == alsoSammy)
```

```go
Output
Sammy == sammy:  false
Sammy == alsoSammy true
```

你还可以使用其他比较运算符，包括 `>` 和 `<` 来比较两个字符串。Go 将使用字符的 ASCII 值对这些字符串进行按字母顺序的比较。

你也可以用比较运算符计算布尔值：

```go
t := true
f := false
    
fmt.Println("t != f: ", t != f)
```

```go
Output
t != f:  true
```    

前面的代码块计算了 `true` 不等于 `false`。

注意两个运算符 `=` 和 `==` 之间的区别。

```go
x = y   // Sets x equal to y
x == y  // Evaluates whether x is equal to y
```

第一个 `=` 是赋值运算符，它将设置一个值等于另一个值。第二个 `==` 是一个比较运算符，将评估两个值是否相等。


## 逻辑运算符

有两个逻辑运算符被用来比较数值。它们将表达式评估为布尔值，返回 `true` 或 `false`。这些运算符是 `&&`，`||`，和 `！`，下面的列表中为定义：

**&&** (`x && y`) 是 `and` 运算符。如果两个语句都是真，它就是真。  
**||** (`x || y`) 是 `or` 运算符。如果至少有一个语句是真，它就是真。  
**!** (`!x`)是 `not` 运算符。只有当语句为假时，它才为真。  

逻辑运算符通常用于计算两个或多个表达式是真还是假。例如，它们可以用来确定成绩是否合格，以及学生是否在课程中注册，如果这两种情况都是真，那么该学生将在系统中被分配一个成绩。另一个例子是根据用户是否有商店信用或在过去6个月内有购买行为，来确定用户是否是一个网上商店的有效活跃客户。

为了理解逻辑运算符的工作原理，我们来评估三个表达式：

```go
fmt.Println((9 > 7) && (2 < 4))   // Both original expressions are true
fmt.Println((8 == 8) || (6 != 6)) // One original expression is true
fmt.Println(!(3 <= 1))            // The original expression is false
```

```go
Output
true
true
true
```

在第一种情况下，`fmt.Println((9 > 7) && (2 < 4))`，`9 > 7` 和 `2 < 4` 都计算为真，因为使用了`and` 运算符。

在第二种情况下，`fmt.Println((8 == 8) || (6 != 6))`，由于`8 == 8` 为真，`6 != 6` 为假，因为使用了 `or` 运算符所以结果为真。如果你使用的是 `and` 运算符，那么这个结果将是假。

在第三种情况下，`fmt.Println(!(3 <= 1))`，`not` 运算符否定了 `3 <=1` 返回的错误值。

让我们用浮点数代替整数，并以假为目标：

```go
fmt.Println((-0.2 > 1.4) && (0.8 < 3.1))  // One original expression is false
fmt.Println((7.5 == 8.9) || (9.2 != 9.2)) // Both original expressions are false
fmt.Println(!(-5.7 <= 0.3))               // The original expression is true
```

在这个例子中：

* `and` 必须至少有一个假则计算为假。
* `or` 必须两个表达式都为假则计算为假。
* `!` 必须使其内部表达式为真，新表达式才为假。

如果这些结果对你来说不清楚，可以通过一些[真值表](#truth-tables)来进一步澄清。

你也可以用 `&&`、`||` 和 `！` 来写复合语句：

```go
!((-0.2 > 1.4) && ((0.8 < 3.1) || (0.1 == 0.1)))
```

先看一下最里面的表达式：`(0.8 < 3.1) || (0.1 == 0.1)`。这个表达式计算为 `true`，因为两个数学语句都是 `true`。

接下来，Go 将返回值 `true` 与下一个内部表达式相结合：`(-0.2 > 1.4) && (true)`。这个例子返回`false`，因为数学语句 `-0.2 > 1.4` 是假，而（`false`）和（`true`）返回 `false`。

最后，我们有一个外层表达式：`！(false)`，它的值是 `true`，所以如果我们把这个语句打印出来，最后的返回值是：

```go
Output
true
``` 

逻辑运算符 `&&`、`||` 和 `！` 表达式用于计算并返回布尔值。

## 真值表

关于数学的逻辑分支，有很多东西需要学习，但你可以有选择地学习一些，以提高你编程时的算法思维。

下面是比较运算符 `==`，以及每个逻辑运算符 `&&`，`||` 和 `！` 的真值表。虽然你可能能够推理出它们，但记住它们也是有帮助的，因为这可以使你的编程决策过程更快。

`==` (equal) 真值表

x == y 返回

true == true true

true == false false

false == true false

false == false true

`&&` (and) 真值表

x and y 返回

true and true true

true and false false

false and true false

false and false false

`||` (or) 真值表

x or y 返回

true or true true

true or false true

false or true true

false or false false

`!` (not) 真值表

not x Returns

not true false

not false true


真值表是逻辑学中常用的数学表，在构建计算机编程中的算法（指令）时，真值表是很有用的，可以牢记在心。

## 使用布尔运算符进行流程控制

为了以流程控制语句的形式控制程序的流程和结果，你可以使用一个 _condition_ 和一个 _clause_ 。

一个 _condition_ 计算出一个布尔值的真或假，提出了一个在程序中做出决定的点。也就是说，一个条件会告诉你某个东西的值是真还是假。

条件是跟在条件后面的代码块，它决定了程序的结果。也就是说，它是 "如果 `x` 是 `true`，那就往下执行"。

下面的代码块显示了一个比较运算符与条件语句协同工作的例子，以控制 Go 程序的流程：

```go
if grade >= 65 {                 // Condition
    fmt.Println("Passing grade") // Clause
} else {
    fmt.Println("Failing grade")
}
```

这个程序将评估每个学生的成绩是合格还是不合格。如果一个学生的成绩是 `83`，第一条语句为 `true`，并且将触发 `Passing grade` 的打印语句。如果学生的成绩是 `59`，第一条语句为 `false`，所以程序将继续执行与 else 表达式相关的打印语句：`Failing grade`。

布尔运算符提出的条件，可以通过流程控制语句来决定程序的最终结果。

## 总结

本教程介绍了属于布尔类型的比较和逻辑运算符，以及真值表和使用布尔类型进行程序流程控制。