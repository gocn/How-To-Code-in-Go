# Understanding Boolean Logic in Go

The Boolean data type (`bool`) can be one of two values, either **true** or **false**. Booleans are used in programming to make comparisons and to control the flow of the program.

Booleans represent the truth values that are associated with the logic branch of mathematics, which informs algorithms in computer science. Named for the mathematician George Boole, the word Boolean always begins with a capitalized `B`.

The data type in Go for Boolean is `bool`, all lowercase. The values `true` and `false` will always be with a lowercase `t` and `f` respectively, as they are special values in Go.

This tutorial will cover the basics you’ll need to understand how the `bool` data type works, including Boolean comparison, logical operators, and truth tables.

## Comparison Operators

In programming, _comparison operators_ are used to compare values and evaluate down to a single Boolean value of either true or false.

The table below shows Boolean comparison operators.

Operator What it means  

== Equal to  

!= Not equal to  

< Less than  

\> Greater than  

<= Less than or equal to  

\>= Greater than or equal to

To understand how these operators work, let’s assign two integers to two variables in a Go program:

```go
x := 5
y := 8
```

In this example, since `x` has the value of `5`, it is less than `y` which has the value of `8`.

Using those two variables and their associated values, let’s go through the operators from the preceding table. In this program, you’ll ask Go to print out whether each comparison operator evaluates to either true or false. To help better understand this output, you’ll have Go also print a string to show you what it’s evaluating:

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

Following mathematical logic, Go has evaluated the following from the expressions:

*   Is 5 (`x`) equal to 8 (`y`)? **false**
*   Is 5 not equal to 8? **true**
*   Is 5 less than 8? **true**
*   Is 5 greater than 8? **false**
*   Is 5 less than or equal to 8? **true**
*   Is 5 not less than or equal to 8? **false**

Although integers were used here, you could substitute them with float values.

Strings can also be used with Boolean operators. They are case-sensitive unless you use an additional string method.

You can look at how strings are compared in practice:

```go
Sammy := "Sammy"
sammy := "sammy"

fmt.Println("Sammy == sammy: ", Sammy == sammy)
```

```go
Output
Sammy == sammy:  false
```

The string `Sammy` is not equal to the string `sammy`, because they are not exactly the same; one starts with an uppercase `S` and the other with a lowercase `s`. But, if you add another variable that is assigned the value of `Sammy`, then they will evaluate to equal:

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

You can also use the other comparison operators including `>` and `<` to compare two strings. Go will compare these strings lexicographically using the ASCII values of the characters.

You can also evaluate Boolean values with comparison operators:

```go
t := true
f := false
    
fmt.Println("t != f: ", t != f)
```

```go
Output
t != f:  true
```    

The preceding code block evaluated that `true` is not equal to `false`.

Note the difference between the two operators `=` and `==`.

```go
x = y   // Sets x equal to y
x == y  // Evaluates whether x is equal to y
```

The first `=` is the assignment operator, which will set one value equal to another. The second, `==`, is a comparison operator and will evaluate whether two values are equal.

## Logical Operators

There are two logical operators that are used to compare values. They evaluate expressions down to Boolean values, returning either `true` or `false`. These operators are `&&`, `||`, and `!`, and are defined in the list below:

*   **&&** (`x && y`) is the `and` operator. It is true if both statements are true.
*   **||** (`x || y`) is the `or` operator. It is true if at least one statement is true.
*   **!** (`!x`) is the `not` operator. It is true only if the statement is false.

Logical operators are typically used to evaluate whether two or more expressions are true or not true. For example, they can be used to determine if the grade is passing and that the student is registered in the course, and if both cases are true, then the student will be assigned a grade in the system. Another example would be to determine whether a user is a valid active customer of an online shop based on whether they have store credit or have made a purchase in the past 6 months.

To understand how logical operators work, let’s evaluate three expressions:

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

In the first case, `fmt.Println((9 > 7) && (2 < 4))`, both `9 > 7` and `2 < 4` needed to evaluate to true since the `and` operator was used.

In the second case, `fmt.Println((8 == 8) || (6 != 6))`, since `8 == 8` evaluated to true, it did not make a difference that `6 != 6` evaluates to false because the `or` operator was used. If you had used the `and` operator, this would evaluate to false.

In the third case, `fmt.Println(!(3 <= 1))`, the `not` operator negates the false value that `3 <=1` returns.

Let’s substitute floats for integers and aim for false evaluations:

```go
fmt.Println((-0.2 > 1.4) && (0.8 < 3.1))  // One original expression is false
fmt.Println((7.5 == 8.9) || (9.2 != 9.2)) // Both original expressions are false
fmt.Println(!(-5.7 <= 0.3))               // The original expression is true
```

In this example:

*   `and` must have at least one false expression evaluate to false.
*   `or` must have both expressions evaluate to false.
*   `!` must have its inner expression be true for the new expression to evaluate to false.

If these results seem unclear to you, go through some [truth tables](#truth-tables) for further clarification.

You can also write compound statements using `&&`, `||`, and `!`:

```go
!((-0.2 > 1.4) && ((0.8 < 3.1) || (0.1 == 0.1)))
```

Take a look at the inner-most expression first: `(0.8 < 3.1) || (0.1 == 0.1)`. This expression evaluates to `true` because both mathematical statements are `true`.

Next, Go takes the returned value `true` and combines it with the next inner expression: `(-0.2 > 1.4) && (true)`. This example returns `false` because the mathematical statement `-0.2 > 1.4` is false, and (`false`) and (`true`) returns `false`.

Finally, we have the outer expression: `!(false)`, which evaluates to `true`, so the final returned value if we print this statement out is:

```go
Output
true
``` 

The logical operators `&&`, `||`, and `!` evaluate expressions and return Boolean values.

## Truth Tables

There is a lot to learn about the logic branch of mathematics, but you can selectively learn some of it to improve your algorithmic thinking when programming.

The following are truth tables for the comparison operator `==`, and each of the logic operators `&&`, `||` and `!`. While you may be able to reason them out, it can also be helpful to memorize them as that can make your programming decision-making process quicker.

`==` (equal) Truth Table

x == y Returns

true == true true

true == false false

false == true false

false == false true

`&&` (and) Truth Table

x and y Returns

true and true true

true and false false

false and true false

false and false false

`||` (or) Truth Table

x or y Returns

true or true true

true or false true

false or true true

false or false false

`!` (not) Truth Table

| not | x | Returns | | — | — | — | — | | not | true | false | | not | false | true |

Truth tables are common mathematical tables used in logic, and are useful to keep in mind when constructing algorithms (instructions) in computer programming.

## Using Boolean Operators for Flow Control

To control the stream and outcomes of a program in the form of flow control statements, you can use a _condition_ followed by a _clause_.

A _condition_ evaluates down to a Boolean value of true or false, presenting a point where a decision is made in the program. That is, a condition would tell you if something evaluates to true or false.

The _clause_ is the block of code that follows the _condition_ and dictates the outcome of the program. That is, it is the “do this” part of the construction “If `x` is `true`, then do this.”

The code block below shows an example of comparison operators working in tandem with conditional statements to control the flow of a Go program:

```go
if grade >= 65 {                 // Condition
    fmt.Println("Passing grade") // Clause
} else {
    fmt.Println("Failing grade")
}
```

This program will evaluate whether each student’s grade is passing or failing. In the case of a student with a grade of `83`, the first statement will evaluate to `true`, and the print statement of `Passing grade` will be triggered. In the case of a student with a grade of `59`, the first statement will evaluate to `false`, so the program will move on to execute the print statement tied to the else expression: `Failing grade`.

Boolean operators present conditions that can be used to decide the eventual outcome of a program through flow control statements.

## Conclusion

This tutorial went through comparison and logical operators belonging to the Boolean type, as well as truth tables and using Booleans for program flow control.