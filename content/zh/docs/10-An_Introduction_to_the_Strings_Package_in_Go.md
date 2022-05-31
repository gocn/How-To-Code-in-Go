# An Introduction to the Strings Package in Go

### Introduction

Go’s [`string`](https://golang.org/pkg/strings/) package has several functions available to work with the [string data type](https://www.digitalocean.com/community/tutorials/an-introduction-to-working-with-strings-in-go). These functions let us easily modify and manipulate strings. We can think of functions as being actions that we perform on elements of our code. Built-in functions are those that are defined in the Go programming language and are readily available for us to use.

In this tutorial, we’ll review several different functions that we can use to work with strings in Go.

## Making Strings Uppercase and Lowercase

The functions `strings.ToUpper` and `strings.ToLower` will return a string with all the letters of an original string converted to uppercase or lowercase letters. Because strings are immutable data types, the returned string will be a new string. Any characters in the string that are not letters will not be changed.

To convert the string `"Sammy Shark"` to be all uppercase, you would use the `strings.ToUpper` function:

```go
ss := "Sammy Shark"
fmt.Println(strings.ToUpper(ss))
```

```go
Output
SAMMY SHARK
```

To convert to lowercase:

```go
fmt.Println(strings.ToLower(ss))
```

```go
Output
sammy shark
```

Since you are using the `strings` package, you first need to import it into a program. To convert the string to uppercase and lowercase the entire program would be as follows:

```
package main

import (
	"fmt"
	"strings"
)

func main() {
	ss := "Sammy Shark"
	fmt.Println(strings.ToUpper(ss))
	fmt.Println(strings.ToLower(ss))
}
```

The `strings.ToUpper` and `strings.ToLower` functions make it easier to evaluate and compare strings by making case consistent throughout. For example, if a user writes their name all lowercase, we can still determine whether their name is in our database by checking it against an all uppercase version.

## String Search Functions

The `strings` package has a number of functions that help determine if a string contains a specific sequence of characters.

| Function            | Use                                      |
| ------------------- | ---------------------------------------- |
| `strings.HasPrefix` | Searches the string from the beginning   |
| `strings.HasSuffix` | Searches the string from the end         |
| `strings.Contains`  | Searches anywhere in the string          |
| `strings.Count`     | Counts how many times the string appears |

The `strings.HasPrefix` and `strings.HasSuffix` allow you to check to see if a string starts or ends with a specific set of characters.

For example, to check to see if the string `"Sammy Shark"` starts with `Sammy` and ends with `Shark`:

```go
ss := "Sammy Shark"
fmt.Println(strings.HasPrefix(ss, "Sammy"))
fmt.Println(strings.HasSuffix(ss, "Shark"))
```

```go
Output
true
true
```

You would use the `strings.Contains` function to check if `"Sammy Shark"` contains the sequence `Sh`:

```go
fmt.Println(strings.Contains(ss, "Sh"))
```

```go
Output
true
```

Finally, to see how many times the letter `S` appears in the phrase `Sammy Shark`:

```go
fmt.Println(strings.Count(ss, "S"))
```

```go
Output
2
```

**Note:** All strings in Go are case sensitive. This means that `Sammy` is not the same as `sammy`.

Using a lowercase `s` to get a count from `Sammy Shark` is not the same as using uppercase `S`:

```go
fmt.Println(strings.Count(ss, "s"))
```

```go
Output
0
```

Because `S` is different than `s`, the count returned will be `0`.

String functions are useful when you want to compare or search strings in your program.

## Determining String Length

The built-in function `len()` returns the number of characters in a string. This function is useful for when you need to enforce minimum or maximum password lengths, or to truncate larger strings to be within certain limits for use as abbreviations.

To demonstrate this function, we’ll find the length of a sentence-long string:

```go
import (
	"fmt"
	"strings"
)

func main() {
        openSource := "Sammy contributes to open source."
        fmt.Println(len(openSource))
}
```

```go
Output
33
```

We set the variable `openSource` equal to the string `"Sammy contributes to open source."` and then passed that variable to the `len()` function with `len(openSource)`. Finally we passed the function into the `fmt.Println()` function so that we could see the program’s output on the screen…

Keep in mind that the `len()` function will count any character bound by double quotation marks—including letters, numbers, whitespace characters, and symbols.

## Functions for String Manipulation

The `strings.Join`, `strings.Split`, and `strings.ReplaceAll` functions are a few additional ways to manipulate strings in Go.

The `strings.Join` function is useful for combining a slice of strings into a new single string.

To create a comma-separated string from a slice of strings, we would use this function as per the following:

```go
fmt.Println(strings.Join([]string{"sharks", "crustaceans", "plankton"}, ","))
```

```go
Output
sharks,crustaceans,plankton
```

If we want to add a comma and a space between string values in our new string, we can simply rewrite our expression with a whitespace after the comma: `strings.Join([]string{"sharks", "crustaceans", "plankton"}, ", ")`.

Just as we can join strings together, we can also split strings up. To do this, we can use the `strings.Split` function and split on the spaces:

```go
balloon := "Sammy has a balloon."
s := strings.Split(balloon, " ")
fmt.Println(s)
```

```go
Output
[Sammy has a balloon]
```

The output is a slice of strings. Since `strings.Println` was used, it is hard to tell what the output is by looking at it. To see that it is indeed a slice of strings, use the `fmt.Printf` function with the `%q` verb to quote the strings:

```go
fmt.Printf("%q", s)
```

```go
Output
["Sammy" "has" "a" "balloon."]
```

Another useful function in addition to `strings.Split` is `strings.Fields`. The difference is that `strings.Fields` will ignore all whitespace, and will only split out the actual `fields` in a string:

```go
data := "  username password     email  date"
fields := strings.Fields(data)
fmt.Printf("%q", fields)
```

```go
Output
["username" "password" "email" "date"]
```

The `strings.ReplaceAll` function can take an original string and return an updated string with some replacement.

Let’s say that the balloon that Sammy had is lost. Since Sammy no longer has this balloon, we would change the substring `"has"` from the original string `balloon` to `"had"` in a new string:

```go
fmt.Println(strings.ReplaceAll(balloon, "has", "had"))
```

Within the parentheses, first is `balloon` the variable that stores the original string; the second substring `"has"` is what we would want to replace, and the third substring `"had"` is what we would replace that second substring with. Our output would look like this when we incorporate this into a program:

```go
Output
Sammy had a balloon.
```

Using the string function `strings.Join`, `strings.Split`, and `strings.ReplaceAll` will provide you with greater control to manipulate strings in Go.

## Conclusion

This tutorial went through some of the common string package functions for the string data type that you can use to work with and manipulate strings in your Go programs.

You can learn more about other data types in [Understanding Data Types](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go) and read more about strings in [An Introduction to Working with Strings](https://www.digitalocean.com/community/tutorials/an-introduction-to-working-with-strings-in-go).

------