# How To Convert Data Types in Go

### Introduction

In Go, data types are used to classify one particular type of data, determining the values that you can assign to the type and the operations you can perform on it. When programming, there are times when you will need to convert values between types in order to manipulate values in a different way. For example, you may need to concatenate numeric values with strings, or represent decimal places in numbers that were initialized as integer values. User-generated data is often automatically assigned the string data type, even if it consists of numbers; in order to perform mathematical operations in this input, you would have to convert the string to a numeric data type.

Since Go is a statically typed language, [data types are bound to variables](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#declaring-data-types-for-variables) rather than values. This means that, if you define a variable as an `int`, it can only be an `int`; you can’t assign a `string` to it without converting the data type of the variable. The static nature of data types in Go places even more importance on learning the ways to convert them.

This tutorial will guide you through converting numbers and strings, as well as provide examples to help familiarize yourself with different use cases.

## Converting Number Types

Go has several numeric types to choose from. Primarily they break out into two general types: [integers](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#integers) and [floating-point numbers](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#floating-point-numbers).

There are many situations in which you may want to convert between numeric types. Converting between [different sizes of numeric types](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#sizes-of-numeric-types) can help optimize performance for specific kinds of system architecture. If you have an integer from another part of your code and want to do division on it, you may want to convert the integer to a float to preserve the precision of the operation. Additionally, working with time durations usually involves integer conversion. To address these situations, Go has built-in *type conversions* for most numeric types.

### Converting Between Integer Types

Go has many integer data types to pick from. When to use one over the other is typically more about [performance](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#picking-numeric-data-types); however, there will be times when you will need to convert from one integer type to another. For example, Go sometimes automatically generates numeric values as `int`, which may not match your input value. If your input value were `int64`, you would not be able to use the `int` and the `int64` numbers in the same mathematical expression until you converted their data types to match.

Assume that you have an `int8` and you need to convert it to an `int32`. You can do this by wrapping it in the `int32()` type conversion:

```go
var index int8 = 15

var bigIndex int32

bigIndex = int32(index)

fmt.Println(bigIndex)
```

```go
Output
15
```

This code block defines `index` as an `int8` data type and `bigIndex` as an `int32` data type. To store the value of `index` in `bigIndex`, it converts the data type to an `int32`. This is done by wrapping the `int32()` conversion around the `index` variable.

To verify your data types, you could use the `fmt.Printf` statement and the `%T` verb with the following syntax:

```go
fmt.Printf("index data type:    %T\n", index)
fmt.Printf("bigIndex data type: %T\n", bigIndex)
```

```go
Output
index data type:    int8
bigIndex data type: int32
```

Since this uses the `%T` verb, the print statement outputs the type for the variable, and not the actual value of the variable. This way, you can confirm the converted data type.

You can also convert from a larger bit-size integer to a smaller bit-size integer:

```go
var big int64 = 64

var little int8

little = int8(big)

fmt.Println(little)
```

```go
Output
64
```

Keep in mind that when converting integers you could potentially exceed the maximum value of the data type and [*wraparound*](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#overflow-vs-wraparound):

```go
var big int64 = 129
var little = int8(big)
fmt.Println(little)
```

```go
Output
-127
```

A wraparound happens when the value is converted to a data type that is too small to hold it. In the preceding example, the 8-bit data type `int8` did not have enough space to hold the 64-bit variable `big`. Care should always be taken when converting from a larger number data type to a smaller number data type so that you do not truncate the data by accident.

### Converting Integers to Floats

Converting integers to floats in Go is similar to converting one integer type to another. You can use the built-in type conversions by wrapping `float64()` or `float32()` around the integer you are converting:

```go
var x int64 = 57

var y float64 = float64(x)

fmt.Printf("%.2f\n", y)
```

```go
Output
57.00
```

This code declares a variable `x` of type `int64` and initializes its value to `57`.

```go
var x int64 = 57
```

Wrapping the `float64()` conversion around `x` will convert the value of `57` to a float value of `57.00`.

```go
var y float64 = float64(x)
```

The `%.2f` print verb tells `fmt.Printf` to format the float with two decimals.

You can also use this process on a variable. The following code declares `f` as equal to `57`, and then prints out the new float:

```go
var f float64 = 57
fmt.Printf("%.2f\n", f)
```

```go
Output
57.00
```

By using either `float32()` or `float64()`, you can convert integers to floats. Next, you will learn how to convert floats to integers.

### Converting Floats to Integers

Go can convert floats to integers, but the program will lose the precision of the float.

Wrapping floats in `int()`, or one of its architecture-independent data types, works similarly to when you used it to convert from one integer type to another. You can add a floating-point number inside of the parentheses to convert it to an integer:

```go
var f float64 = 390.8
var i int = int(f)

fmt.Printf("f = %.2f\n", f)
fmt.Printf("i = %d\n", i)
```

```go
Output
f = 390.80
i = 390
```

This syntax would convert the float `390.8` to the integer `390`, dropping the decimal place.

You can also use this with variables. The following code declares `b` as equal to `125.0` and `c` as equal to `390.8`, then prints them out as integers. Short variable declaration (`:=`) shortens up the syntax:

```go
b := 125.0
c := 390.8

fmt.Println(int(b))
fmt.Println(int(c))
```

```go
Output125
390
```

When converting floats to integers with the `int()` type, Go cuts off the decimal and remaining numbers of a float to create an integer. Note that, even though you may want to round 390.8 up to 391, Go will not do this through the `int()` type. Instead, it will drop the decimal.

### Numbers Converted Through Division

When dividing integer types in Go the result will also be an integer type, with the *modulus*, or remainder, dropped:

```go
a := 5 / 2
fmt.Println(a)
```

```go
Output
2
```

If, when dividing, any of the number types are a float, then all of the types will automatically be declared as a float:

```go
	a := 5.0 / 2
	fmt.Println(a)
```

```go
Output
2.5
```

This divides the float `5.0` by the integer `2`, and the answer `2.5` is a float that retains the decimal precision.

In this section, you have converted between different number data types, including differing sizes of integers and floating-point numbers. Next, you will learn how to convert between numbers and strings.

## Converting with Strings

A *string* is a sequence of one or more characters (letters, numbers, or symbols). Strings are a common form of data in computer programs, and you may need to convert strings to numbers or numbers to strings fairly often, especially when you are taking in user-generated data.

### Converting Numbers to Strings

You can convert numbers to strings by using the `strconv.Itoa` method from the `strconv` package in the Go standard libary. If you pass either a number or a variable into the parentheses of the method, that numeric value will be converted into a string value.

First, let’s look at converting integers. To convert the integer `12` to a string value, you can pass `12` into the `strconv.Itoa` method:

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	a := strconv.Itoa(12)
	fmt.Printf("%q\n", a)
}
```

When running this program, you’ll receive the following output:

```go
Output
"12"
```

The quotes around the number 12 signify that the number is no longer an integer but is now a string value.

You used the `:=` assignment operator to both declare a new variable with the name of `a` and assign the value returned from the `strconv.Itoa()` function. In this case, you assigned the value `12` to your variable. You also used the `%q` verb in the `fmt.Printf` function, which tells the function to quote the string provided.

With variables you can begin to see how practical it can be to convert integers to strings. Say you want to keep track of a user’s daily programming progress and are inputting how many lines of code they write at a time. You would like to show this feedback to the user and will be printing out string and integer values at the same time:

```go
package main

import (
	"fmt"
)

func main() {
	user := "Sammy"
	lines := 50

	fmt.Println("Congratulations, " + user + "! You just wrote " + lines + " lines of code.")
}
```

When you run this code, you’ll receive the following error:

```go
Output
invalid operation: ("Congratulations, " + user + "! You just wrote ") + lines (mismatched types string and int)
```

You’re not able to concatenate strings and integers in Go, so you’ll have to convert the variable `lines` to be a string value:

```
package main

import (
	"fmt"
	"strconv"
)

func main() {
	user := "Sammy"
	lines := 50

	fmt.Println("Congratulations, " + user + "! You just wrote " + strconv.Itoa(lines) + " lines of code.")
}
```

Now, when you run the code, you’ll receive the following output that congratulates your user on their progress:

```go
Output
Congratulations, Sammy! You just wrote 50 lines of code.
```

If you are looking to convert a float to a string rather than an integer to a string, you follow similar steps and format. When you pass a float into the `fmt.Sprint` method, from the `fmt` package in the Go standard library, a string value of the float will be returned. You can use either the float value itself or a variable:

```go
package main

import (
	"fmt"
)

func main() {
	fmt.Println(fmt.Sprint(421.034))

	f := 5524.53
	fmt.Println(fmt.Sprint(f))
}
```

```go
Output
421.034
5524.53
```

You can test to make sure it’s right by concatenating with a string:

```go
package main

import (
	"fmt"
)

func main() {
	f := 5524.53
	fmt.Println("Sammy has " + fmt.Sprint(f) + " points.")
}
```

```go
Output
Sammy has 5524.53 points.
```

You can be sure your float was properly converted to a string because the concatenation was performed without error.

### Converting Strings to Numbers

Strings can be converted to numbers by using the `strconv` package in the Go standard library. The `strconv` package has functions for converting both integer and float number types. This is a very common operation when accepting input from the user. For example, if you had a program that asked for a person’s age, when they type the response in, it is captured as a `string`. You would then need to convert it to an `int` to do any math with it.

If your string does not have decimal places, you’ll most likely want to convert it to an integer by using the `strconv.Atoi` function. If you know you will use the number as a float, you would use `strconv.ParseFloat`.

Let’s use the example of the user Sammy keeping track of lines of code written each day. You may want to manipulate those values with math to provide more interesting feedback for the user, but those values are currently stored in strings:

```go
package main

import (
	"fmt"
)

func main() {
	lines_yesterday := "50"
	lines_today := "108"

	lines_more := lines_today - lines_yesterday

	fmt.Println(lines_more)
}
```

```go
Output
invalid operation: lines_today - lines_yesterday (operator - not defined on string)
```

Because the two numeric values were stored in strings, you received an error. The operand `-` for subtraction is not a valid operand for two string values.

Modify the code to include the `strconv.Atoi()` method that will convert the strings to integers, which will allow you to do math with values that were originally strings. Because there is a potential to fail when converting a string to an integer, you have to check for any errors. You can use an `if` statement to check if your conversion was successful.

```
package main

import (
	"fmt"
	"log"
	"strconv"
)

func main() {
	lines_yesterday := "50"
	lines_today := "108"

	yesterday, err := strconv.Atoi(lines_yesterday)
	if err != nil {
		log.Fatal(err)
	}

	today, err := strconv.Atoi(lines_today)
	if err != nil {
		log.Fatal(err)
	}
	lines_more := today - yesterday

	fmt.Println(lines_more)
}
```

Because it is possible for a string to not be a number, the `strconv.Atoi()` method will return both the converted type, as well as a potential error. When converting from `lines_yesterday` with the `strconv.Atoi` function, you have to check the `err` return value to ensure that the value was converted. If the `err` is not `nil`, it means that `strconv.Atoi` was unable to successfully convert the string value to an integer. In this example, you used an `if` statement to check for the error, and if an error was returned, you used `log.Fatal` to log the error and exit the program.

When you run the preceding code, you will get:

```go
Output
58
```

Now try to convert a string that is not a number:

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	a := "not a number"
	b, err := strconv.Atoi(a)
	fmt.Println(b)
	fmt.Println(err)
}
```

You will get the following error:

```go
Output
0
strconv.Atoi: parsing "not a number": invalid syntax
```

Because `b` was declared, but `strconv.Atoi` failed to make a conversion, a value was never assigned to `b`. Notice that `b` has the value of `0`. This is because Go has default values, referred to as zero values in Go. `strconv.Atoi` provides an error describing why it failed to convert the string as well.

### Converting Strings and Bytes

Strings in Go are stored as a slice of bytes. In Go, you can convert between a slice of bytes and a string by wrapping it in the corresponding conversions of `[]byte()` and `string()`:

```go
package main

import (
	"fmt"
)

func main() {
	a := "my string"

	b := []byte(a)

	c := string(b)

	fmt.Println(a)

	fmt.Println(b)

	fmt.Println(c)
}
```

Here you have stored a string value in `a`, then converted it to a slice of bytes `b`, then converted the slice of bytes back to a string as `c`. You then print `a`, `b`, and `c` to the screen:

```go
Output
my string
[109 121 32 115 116 114 105 110 103]
my string
```

The first line of output is the original string `my string`. The second line printed out is the byte slice that makes up the original string. The third line shows that the byte slice can be safely converted back into a string and printed back out.

## Conclusion

This Go tutorial demonstrated how to convert several of the important native data types to other data types, primarily through built-in methods. Being able to convert data types in Go will allow you to do things like accept user input and do math across different number types. Later on, when you are using Go to write programs that accept data from many different sources like databases and APIs, you will use these conversion methods to ensure you can act on your data. You will also be able to optimize storage by converting data to smaller data types.

If you would like a deeper analysis of data types in Go, check out our [Understanding Data Types in Go](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go) article.