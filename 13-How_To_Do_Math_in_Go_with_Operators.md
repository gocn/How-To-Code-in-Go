# How To Do Math in Go with Operators

## Introduction

Numbers are common in programming. They are used to represent things such as: screen-size dimensions, geographic locations, money and points, the amount of time that passes in a video, positions of game avatars, colors through assigning numeric codes, and so on.

Effectively performing mathematical operations in programming is an important skill to develop because of how frequently you’ll work with numbers. Though a high-level understanding of mathematics can certainly help you become a better programmer, it is not a prerequisite. If you don’t have a background in mathematics, try to think of math as a tool to accomplish what you would like to achieve, and as a way to improve your logical thinking.

We’ll be working with two of Go’s most used numeric [data types](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go), _integers_ and _floats_:

*   [Integers](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#integers) are whole numbers that can be positive, negative, or 0 (…, `-1`, `0`, `1`, …).
*   [Floats](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#floating-point-numbers) are real numbers that contain a decimal point, like `9.0` or `-2.25`…

This tutorial will review operators that we can use with number data types in Go.

## Operators

An _operator_ is a symbol or function that indicates an operation. For example, in math the plus sign or `+` is the operator that indicates addition.

In Go, we will see some familiar operators that are brought over from math. However, other operators we will use are specific to computer programming.

Here is a quick reference table of math-related operators in Go. We’ll be covering all of the following operations in this tutorial.

Operation What it returns  

`x + y` Sum of `x` and `y`  

`x - y` Difference of `x` and `y`  

`-x` Changed sign of `x`  

`+x` Identity of `x`  

`x * y` Product of `x` and `y`  

`x / y` Quotient of `x` and `y`  

`x % y` Remainder of `x` / `y`  

We’ll also be covering compound assignment operators, including `+=` and `*=`, that combine an arithmetic operator with the `=` operator.

## Addition and Subtraction

In Go, addition and subtraction operators perform just as they do in mathematics. In fact, you can use the Go programming language as a calculator.

Let’s look at some examples, starting with integers:

```go
fmt.Println(1 + 5)
```

```go
Output
6
```
    
Instead of passing integers directly into the `fmt.Println` statement, we can initialize variables to stand for integer values by using syntax like the following:

```go
a := 88
b := 103
    
fmt.Println(a + b)
```

```go
Output
191
```

Because integers can be both positive and negative numbers (and 0 too), we can add a negative number with a positive number:

```go
c := -36
d := 25
    
fmt.Println(c + d)
```
    
```go
Output
-11
```

Addition will behave similarly with floats:

```go
e := 5.5
f := 2.5
    
fmt.Println(e + f)
```

```go
Output
8
```

Because we added two floats together, Go returned a float value with a decimal place. However, since the decimal place is zero in this case, `fmt.Println` dropped the decimal formatting. To properly format the output, we can use `fmt.Printf` and the verb `%.2f`, which will format to two decimal places, like this example:

```go
fmt.Printf("%.2f", e + f)
```

```go
Output
8.00
``` 

The syntax for subtraction is the same as for addition, except we change our operator from the plus sign (`+`) to the minus sign (`-`):

```go
g := 75.67
h := 32.0
    
fmt.Println(g - h)
```
    
```go
Output
43.67
``` 

In Go, we can only use operators on the same _data types_. We can’t add an `int` and a [`float64`](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#sizes-of-numeric-types):

```go
i := 7
j := 7.0
fmt.Println(i + j)
```

```go
Output
i + j (mismatched types int and float64)
```

Trying to use operators on data types that are not the same will result in a compiler error.

## Unary Arithmetic Operations

A unary mathematical expression consists of only one component or element. In Go we can use the plus and minus signs as a single element paired with a value to: return the value’s identity (`+`), or change the sign of the value (`-`).

Though not commonly used, the plus sign indicates the identity of the value. We can use the plus sign with positive values:

```go
i := 3.3
fmt.Println(+i)
```

```go
Output
3.3
```

When we use the plus sign with a negative value, it will also return the identity of that value, and in this case it would be a negative value:

```go
j := -19
fmt.Println(+j)
```

```go
Output
-19
```

With a negative value the plus sign returns the same negative value.

The minus sign, however, changes the sign of a value. So, when we pass a positive value we’ll find that the minus sign before the value will return a negative value:

```go
k := 3.3
fmt.Println(-k)
```

```go
Output
-3.3
```

Alternatively, when we use the minus sign unary operator with a negative value, a positive value will be returned:

```go
j := -19
fmt.Println(-j)
```

```go
Output
19
```

The unary arithmetic operations indicated by the plus sign and minus sign will return either the value’s identity in the case of `+i`, or the opposite sign of the value as in `-i`.

## Multiplication and Division

Like addition and subtraction, multiplication and division will look very similar to how they do in mathematics. The sign we’ll use in Go for multiplication is `*` and the sign we’ll use for division is `/`.

Here’s an example of doing multiplication in Go with two float values:

```go
k := 100.2
l := 10.2
    
fmt.Println(k * l)
```

```go
Output
1022.04
```

In Go, division has different characteristics depending on the numeric type we’re dividing.

If we’re dividing integers, Go’s `/` operator performs floor division, where for the quotient **x** the number returned is the largest integer less than or equal to **x**.

If you run the following example of dividing `80 / 6`, you’ll receive `13` as the output and the data type will be `int`:

```go
package main
    
import (
    "fmt"
)
    
func main() {
    m := 80
    n := 6
    
    fmt.Println(m / n)
}
```

```go
Output
13
``` 

If the desired output is a float, you have to explicitly convert the values before dividing.

You can do this by wrapping your desired float type of `float32()` or `float64()` around your values:

```go
package main
    
import (
    "fmt"
)
    
func main() {
    s := 80
    t := 6
    r := float64(s) / float64(t)
    fmt.Println(r)
}
```

```go
Output
13.333333333333334
```    

## Modulo

The `%` operator is the _modulo_, which returns the remainder rather than the quotient after division. This is useful for finding numbers that are multiples of the same number.

Let’s look at an example of the modulo:

```go
o := 85
p := 15
    
fmt.Println(o % p)
```

```go
Output
10
```

To break this down, `85` divided by `15` returns the quotient of `5` with a remainder of `10`. Our program returns the value `10` here, because the modulo operator returns the remainder of a division expression.

To do modulus math with `float64` data types, you’ll use the `Mod` function from the `math` package:

```go
package main
    
import (
    "fmt"
    "math"
)
    
func main() {
    q := 36.0
    r := 8.0
    s := math.Mod(q, r)

    fmt.Println(s)
}
```

```go
Output
4
```    

## Operator Precedence

In Go, as in mathematics, we need to keep in mind that operators will be evaluated in order of precedence, not from left to right or right to left.

If we look at the following mathematical expression:

```go
u = 10 + 10 * 5
```

We may read it left to right, but multiplication will be done first, so if we were to print `u`, we would receive the following value:

```go
Output
60
```

This is because `10 * 5` evaluates to `50`, and then we add `10` to return `60` as the final result.

If instead we would like to add the value `10` to `10`, then multiply that sum by `5`, we use parentheses in Go just like we would in math:

```go
u := (10 + 10) * 5
fmt.Println(u)
```

```go
Output
100
```    

One way to remember the order of operation is through the acronym **PEMDAS**:

Order Letter Stands for  

1 P Parentheses

2 E Exponent

3 M Multiplication

4 D Division

5 A Addition

6 S Subtraction

You may be familiar with another acronym for the order of operations, such as **BEDMAS** or **BODMAS**. Whatever acronym works best for you, try to keep it in mind when performing math operations in Go so that the results that you expect are returned.

## Assignment Operators

The most common assignment operator is one you have already used: the equals sign `=`. The `=` assignment operator assigns the value on the right to a variable on the left. For example, `v = 23` assigns the value of the integer `23` to the variable `v`.

When programming, it is common to use compound assignment operators that perform an operation on a variable’s value and then assign the resulting new value to that variable. These compound operators combine an arithmetic operator with the `=` operator. Therefore, for addition we’ll combine `+` with `=` to get the compound operator `+=`. Let’s see what that looks like:


```go
w := 5
w += 1
fmt.Println(w)
```

```go
Output
6
```

First, we set the variable `w` equal to the value of `5`, then we use the `+=` compound assignment operator to add the right number to the value of the left variable, and then assign the result to `w`.

Compound assignment operators are used frequently in the case of `for` loops, which you’ll use when you want to repeat a process several times:

```go
package main
    
import "fmt"
    
func main() {

    values := []int{0, 1, 2, 3, 4, 5, 6}
    
    for _, x := range values {

        w := x

        w *= 2

        fmt.Println(w)
    }
 }
```

```go
Output0
2
4
6
8
10
12
```

By using a `for` loop to iterate over the slice called `values`, you were able to automate the process of the `*=` operator that multiplied the variable `w` by the number `2` and then assigned the result back into the variable `w`.

Go has a compound assignment operator for each of the arithmetic operators discussed in this tutorial.

To add then assign the value:

```go
y += 1
```

To subtract then assign the value:

```go
y -= 1
```    

To multiply then assign then value:

```go
y *= 2
```

To divide then assign the value:

```go
y /= 3
```

To return the remainder then assign the value:

```go
y %= 3
```

Compound assignment operators can be useful when things need to be incrementally increased or decreased, or when you need to automate certain processes in your program.

## Conclusion

This tutorial covered many of the operators you’ll use with the integer and float numeric data types. You can learn more about different data types in [Understanding Data Types in Go](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go) and [How To Convert Data Types](https://www.digitalocean.com/community/tutorials/how-to-convert-data-types-in-go).