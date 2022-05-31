# How To Use Variables and Constants in Go

*Variables* are an important programming concept to master. They are symbols that stand in for a value you’re using in a program.

This tutorial will cover some variable basics and best practices for using them within the Go programs you create.

## Understanding Variables

In technical terms, a variable is assigning a storage location to a value that is tied to a symbolic name or identifier. We use the variable name to reference that stored value within a computer program.

We can think of a variable as a label that has a name on it, which you tie onto a value.

![Variables in Go](https://assets.digitalocean.com/articles/go_variables/variable1.png)

Let’s say we have an integer, `1032049348`, and we want to store it in a variable rather than continuously retype the long number over and over again. To achieve this, we can use a name that’s easy to remember, like the variable `i`. To store a value in a variable, we use the following syntax:

```go
i := 1032049348
```

We can think of this variable like a label that is tied to the value.

![Go Variable Example](https://assets.digitalocean.com/articles/go_variables/variable2.png)

The label has the variable name `i` written on it, and is tied to the integer value `1032049348`.

The phrase `i := 1032049348` is a declaration and assignment statement that consists of a few parts:

- the variable name (`i`)
- the short variable declaration assignment (`:=`)
- the value that is being tied to the variable name (`1032049348`)
- the data type inferred by Go (`int`)

We’ll see later how to explicitly set the type in the next section.

Together, these parts make up the statement that sets the variable `i` equal to the value of the integer `1032049348`.

As soon as we set a variable equal to a value, we *initialize* or create that variable. Once we have done that, we are ready to use the variable instead of the value.

Once we’ve set `i` equal to the value of `1032049348`, we can use `i` in the place of the integer, so let’s print it out:

```go
package main

import "fmt"

func main() {
	i := 1032049348
	fmt.Println(i)
}
```

```go
Output
1032049348
```

We can also quickly and easily do math by using variables. With `i := 1032049348`, we can subtract the integer value `813` with the following syntax:

```go
fmt.Println(i - 813)
```

```go
Output
1032048535
```

In this example, Go does the math for us, subtracting 813 from the variable `i` to return the sum `1032048535`.

Speaking of math, variables can be set equal to the result of a math equation. You can also add two numbers together and store the value of the sum into the variable `x`:

```go
x := 76 + 145
```

You may have noticed that this example looks similar to algebra. In the same way that we use letters and other symbols to represent numbers and quantities within formulas and equations, variables are symbolic names that represent the value of a data type. For correct Go syntax, you’ll need to make sure that your variable is on the left side of any equations.

Let’s go ahead and print `x`:

```go
package main

import "fmt"

func main() {
	x := 76 + 145
	fmt.Println(x)
}
```

```go
Output
221
```

Go returned the value `221` because the variable `x` was set equal to the sum of `76` and `145`.

Variables can represent any data type, not just integers:

```go
s := "Hello, World!"
f := 45.06
b := 5 > 9 // A Boolean value will return either true or false
array := [4]string{"item_1", "item_2", "item_3", "item_4"}
slice := []string{"one", "two", "three"}
m := map[string]string{"letter": "g", "number": "seven", "symbol": "&"}
```

If you print any of these variables, Go will return what that variable is equivalent to. Let’s work with the assignment statement for the string `slice` data type:

```go
package main

import "fmt"

func main() {
	slice := []string{"one", "two", "three"}
	fmt.Println(slice)
}
```

```go
Output
[one two three]
```

We assigned the slice value of `[]string{"one", "two", "three"}` to the variable `slice`, and then used the `fmt.Println` function to print out that value by calling `slice`.

Variables work by carving out a little area of memory within your computer that accepts specified values that are then associated with that space.

## Declaring Variables

In Go, there are several ways to declare a variable, and in some cases, more than one way to declare the exact same variable and value.

We can declare a variable called `i` of data type `int` without initialization. This means we will declare a space to put a value, but not give it an initial value:

```go
var i int
```

This creates a variable declared as `i` of data type `int`.

We can initialize the value by using the equal (`=`) operator, like in the following example:

```go
var i int = 1
```

In Go, both of these forms of declaration are called *long variable declarations*.

We can also use *short variable declaration*:

```go
i := 1
```

In this case, we have a variable called `i`, and a data type of `int`. When we don’t specify a data type, Go will infer the data type.

With the three ways to declare variables, the Go community has adopted the following idioms:

- Only use long form, `var i int`, when you’re not initializing the variable.
- Use short form, `i := 1`, when declaring and initializing.
- If you did not desire Go to infer your data type, but you still want to use short variable declaration, you can wrap your value in your desired type, with the following syntax:

```go
i := int64(1)
```

It’s not considered idiomatic in Go to use the long variable declaration form when we’re initializing the value:

```go
var i int = 1
```

It’s good practice to follow how the Go community typically declares variables so that others can seamlessly read your programs.

## Zero Values

All built-in types have a zero value. Any allocated variable is usable even if it never has a value assigned. We can see the zero values for the following types:

```go
package main

import "fmt"

func main() {
	var a int
	var b string
	var c float64
	var d bool

	fmt.Printf("var a %T = %+v\n", a, a)
	fmt.Printf("var b %T = %q\n", b, b)
	fmt.Printf("var c %T = %+v\n", c, c)
	fmt.Printf("var d %T = %+v\n\n", d, d)
}
```

```go
Output
var a int =  0
var b string = ""
var c float64 = 0
var d bool = false
```

We used the `%T` verb in the `fmt.Printf` statement. This tells the function to print the `data type` for the variable.

In Go, because all values have a `zero` value, we can’t have `undefined` values like some other languages. For instance, a [`boolean`](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#booleans) in some languages could be `undefined`, `true`, or `false`, which allows for `three` states to the variable. In Go, we can’t have more than `two` states for a boolean value.

## Naming Variables: Rules and Style

The naming of variables is quite flexible, but there are some rules to keep in mind:

- Variable names must only be one word (as in no spaces).
- Variable names must be made up of only letters, numbers, and underscores (`_`).
- Variable names cannot begin with a number.

Following these rules, let’s look at both valid and invalid variable names:

| Valid      | Invalid     | Why Invalid                  |
| ---------- | ----------- | ---------------------------- |
| `userName` | `user-name` | Hyphens are not permitted    |
| `name4`    | `4name`     | Cannot begin with a number   |
| `user`     | `$user`     | Cannot use symbols           |
| `userName` | `user name` | Cannot be more than one word |

Furthermore, keep in mind when naming variables that they are case sensitive. These names `userName`, `USERNAME`, `UserName`, and `uSERnAME` are all completely different variables. It’s best practice to avoid using similar variable names within a program to ensure that both you and your collaborators—current and future—can keep your variables straight.

While variables are case sensitive, the case of the first letter of a variable has special meaning in Go. If a variable starts with an uppercase letter, then that variable is accessible outside the package it was declared in (or `exported`). If a variable starts with a lowercase letter, then it is only available within the package it is declared in.

```go
var Email string
var password string
```

`Email` starts with an uppercase letter and can be accessed by other packages. `password` starts with a lowercase letter, and is only accessible inside the package it is declared in.

It is common in Go to use very terse (or short) variable names. Given the choice between using `userName` and `user` for a variable, it would be idiomatic to choose `user`.

Scope also plays a role in the terseness of the variable name. The rule is that the smaller the scope the variable exists in, the smaller the variable name:

```go
names := []string{"Mary", "John", "Bob", "Anna"}
for i, n := range names {
	fmt.Printf("index: %d = %q\n", i, n)
}
```

We use the variable `names` in a larger scope, so it would be common to give it a more meaningful name to help remember what it means in the program. However, we use the `i` and `n` variables immediately in the next line of code, and then do not use them again… Because of this, it won’t confuse someone reading the code about where the variables are used, or what they mean.

Next, let’s cover some notes about variable style. The style is to use `MixedCaps` or `mixedCaps` rather than underscores for multi-word names.

| Conventional Style | Unconventional Style | Why Unconventional                       |
| ------------------ | -------------------- | ---------------------------------------- |
| `userName`         | `user_name`          | Underscores are not conventional         |
| `i`                | `index`              | prefer `i` over `index` as it is shorter |
| `serveHTTP`        | `serveHttp`          | acronyms should be capitalized           |

The most important thing about style is to be consistent, and that the team you work on agrees to the style.

## Reassigning Variables

As the word “variable” implies, we can change Go variables readily. This means that we can connect a different value with a previously assigned variable through reassignment. Being able to reassign is useful because throughout the course of a program we may need to accept user-generated values into already initialized variables. We may also need to change the assignment to something previously defined.

Knowing that we can readily reassign a variable can be useful when working on a large program that someone else wrote, and it isn’t clear what variables are already defined.

Let’s assign the value of `76` to a variable called `i` of type `int`, then assign it a new value of `42`:

```go
package main

import "fmt"

func main() {
	i := 76
	fmt.Println(i)

	i = 42
	fmt.Println(i)
}
```

```go
Output
76
42
```

This example shows that we can first assign the variable `i` with the value of an integer, and then reassign the variable `i` assigning it this time with the value of `42`.

**Note:** When you declare **and** initialize a variable, you can use `:=`, however, when you want to simply change the value of an already declared variable, you only need to use the equal operator (`=`).

Because Go is a `typed` language, we can’t assign one type to another. For instance, we can’t assign the value `"Sammy"` to a variable of type `int`:

```go
i := 72
i = "Sammy"
```

Trying to assign different types to each other will result in a compile-time error:

```go
Output
cannot use "Sammy" (type string) as type int in assignment
```

Go will not allow us to use a variable name more than once:

```go
var s string
var s string
```

```go
Output
s redeclared in this block
```

If we try to use short variable declaration more than once for the same variable name we’ll also receive a compilation error. This can happen by mistake, so understanding what the error message means is helpful:

```go
i := 5
i := 10
```

```go
Output
no new variables on left side of :=
```

Similarly to variable declaration, giving consideration to the naming of your variables will improve the readability of your program for you, and others, when you revisit it in the future.

## Multiple Assignment

Go also allows us to assign several values to several variables within the same line. Each of these values can be of a different data type:

```go
j, k, l := "shark", 2.05, 15
fmt.Println(j)
fmt.Println(k)
fmt.Println(l)
```

```go
Output
shark
2.05
15
```

In this example, the variable `j` was assigned to the string `"shark"`, the variable `k` was assigned to the float `2.05`, and the variable `l` was assigned to the integer `15`.

This approach to assigning multiple variables to multiple values in one line can keep the number of lines in your code down. However, it’s important to not compromise readability for fewer lines of code.

## Global and Local Variables

When using variables within a program, it is important to keep *variable scope* in mind. A variable’s scope refers to the particular places it is accessible from within the code of a given program. This is to say that not all variables are accessible from all parts of a given program—some variables will be global and some will be local.

Global variables exist outside of functions. Local variables exist within functions.

Let’s take a look at global and local variables in action:

```go
package main

import "fmt"


var g = "global"

func printLocal() {
	l := "local"
	fmt.Println(l)
}

func main() {
	printLocal()
	fmt.Println(g)
}
```

```go
Output
local
global
```

Here we use `var g = "global"` to create a global variable outside of the function. Then we define the function `printLocal()`. Inside of the function a local variable called `l` is assigned and then printed out. The program ends by calling `printLocal()` and then printing the global variable `g`.

Because `g` is a global variable, we can refer to it in `printLocal()`. Let’s modify the previous program to do that:

```go
package main

import "fmt"


var g = "global"

func printLocal() {
	l := "local"
	fmt.Println(l)
	fmt.Println(g)
}

func main() {
	printLocal()
	fmt.Println(g)
}
```

```go
Output
local
global
global
```

We start by declaring a global variable `g`, `var g = "global"`. In the `main` function, we call the function `printLocal`, which declares a local variable `l` and prints it out, `fmt.Println(l)`. Then, `printLocal` prints out the global variable `g`, `fmt.Println(g)`. Even though `g` wasn’t defined within `printLocal`, it could still be accessed because it was declared in a global scope. Finally, the `main` function prints out `g` as well.

Now let’s try to call the local variable outside of the function:

```go
package main

import "fmt"

var g = "global"

func printLocal() {
	l := "local"
	fmt.Println(l)
}

func main() {
	fmt.Println(l)
}
```

```go
Output
undefined: l
```

We can’t use a local variable outside of the function it is assigned in. If you try to do so, you’ll receive a `undefined` error when you compile.

Let’s look at another example where we use the same variable name for a global variable and a local variable:

```go
package main

import "fmt"

var num1 = 5

func printNumbers() {
	num1 := 10
	num2 := 7  

	fmt.Println(num1)
	fmt.Println(num2)
}

func main() {
	printNumbers()
	fmt.Println(num1)
}
```

```go
Output
10
7
5
```

In this program, we declared the `num1` variable twice. First, we declared `num1` at the global scope, `var num1 = 5`, and again within the local scope of the `printNumbers` function, `num1 := 10`. When we print `num1` from the `main` program, we see the value of `5` printed out. This is because `main` only sees the global variable declaration. However, when we print out `num1` from the `printNumbers` function, it sees the local declaration, and will print out the value of `10`. Even though `printNumbers` creates a new variable called `num1` and assigned it a value of `10`, it does not affect the global instance of `num1` with the value of `5`.

When working with variables, you also need to consider what parts of your program will need access to each variables; adopting a global or local variable accordingly. Across Go programs, you’ll find that local variables are typically more common.

## Constants

Constants are like variables, except they can’t be modified once they have been declared. Constants are useful for defining a value that will be used more than once in your program, but shouldn’t be able to change.

For instance, if we wanted to declare the tax rate for a shopping cart system, we could use a constant and then calculate tax in different areas of our program. At some point in the future, if the tax rate changes, we only have to change that value in one spot in our program. If we used a variable, it is possible that we might accidentally change the value somewhere in our program, which would result in an improper calculation.

To declare a constant, we can use the following syntax:

```go
const shark = "Sammy"
fmt.Println(shark)
```

```go
Output
Sammy
```

If we try to modify a constant after it was declared, we’ll get a compile-time error:

```go
Output
cannot assign to shark
```

Constants can be `untyped`. This can be useful when working with numbers such as integer-type data. If the constant is `untyped`, it is explicitly converted, where `typed` constants are not. Let’s see how we can use constants:

```go
package main

import "fmt"

const (
	year     = 365
	leapYear = int32(366)
)

func main() {
	hours := 24
	minutes := int32(60)
	fmt.Println(hours * year)    
	fmt.Println(minutes * year)   
	fmt.Println(minutes * leapYear)
}
```

```go
Output
8760
21900
21960
```

If you declare a constant with a type, it will be that exact type. Here when we declare the constant `leapYear`, we define it as data type `int32`. Therefore it is a `typed` constant, which means it can only operate with `int32` data types. The `year` constant we declare with no type, so it is considered `untyped`. Because of this, you can use it with any integer data type.

When `hours` was defined, it *inferred* that it was of type `int` because we did not explicitly give it a type, `hours := 24`. When we declared `minutes`, we explicitly declared it as an `int32`, `minutes := int32(60)`.

Now let’s walk through each calculation and why it works:

```go
hours * year
```

In this case, `hours` is an `int`, and `years` is *untyped*. When the program compiles, it explicitly converts `years` to an `int`, which allows the multiplication operation to succeed.

```go
minutes * year
```

In this case, `minutes` is an `int32`, and `year` is *untyped*. When the program compiles, it explicitly converts `years` to an `int32`, which allows the multiplication operation to succeed.

```go
minutes * leapYear
```

In this case, `minutes` is an `int32`, and `leapYear` is a *typed* constant of `int32`. There is nothing for the compiler to do this time as both variables are already of the same type.

If we try to multiply two types that are `typed` and not compatible, the program will not compile:

```go
fmt.Println(hours * leapYear)
```

```go
Output
invalid operation: hours * leapYear (mismatched types int and int32)
```

In this case, `hours` was inferred as an `int`, and `leapYear` was explicitly declared as an `int32`. Because Go is a typed language, an `int` and an `int32` are not compatible for mathematical operations. To multiply them, you would need [to convert one to a `int32` or an `int`](https://www.digitalocean.com/community/tutorials/how-to-convert-data-types-in-go#converting-number-types).

## Conclusion

In this tutorial we reviewed some of the common use cases of variables within Go. Variables are an important building block of programming, serving as symbols that stand in for the value of a data type we use in a program.