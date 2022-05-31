# How To Format Strings in Go

As strings are often made up of written text, there are many instances when we may want to have greater control over how strings look to make them more readable for humans through punctuation, line breaks, and indentation.

In this tutorial, we’ll go over some of the ways we can work with Go strings to make sure that all output text is formatted correctly.

## String Literals

Let’s first differentiate between a *string literal* and a *string value*. A string literal is what we see in the source code of a computer program, including the quotation marks. A string value is what we see when we call the `fmt.Println` function and run the program.

In the “Hello, World!” program, the string literal is `"Hello, World!"` while the string value is `Hello, World!` without the quotation marks. The string value is what we see as the output in a terminal window when we run a Go program.

But some string values may need to include quotation marks, like when we are quoting a source. Because string literals and string values are not equivalent, it is often necessary to add additional formatting to string literals to ensure that string values are displayed the way in which we intend.

## Quotes

Because we can use back quotes (`` `) or double quotes (`"`) within Go, it is simple to embed quotes within a string by using double quotes within a string enclosed by back quotes:

```go
`Sammy says, "Hello!"`
```

Or, to use a back quote, you can enclose the string in double quotes:

```go
"Sammy likes the `fmt` package for formatting strings.."
```

In the way we combine back quotes and double quotes, we can control the display of quotation marks and back quotes within our strings.

It’s important to remember that using back quotes in Go creates a `raw` string literal, and using double quotes creates an `interpreted` string literal. To learn more about the difference, read the [An Introduction to Working with Strings in Go](https://www.digitalocean.com/community/tutorials/an-introduction-to-working-with-strings-in-go) tutorial.

## Escape Characters

Another way to format strings is to use an *escape character*. Escape characters are used to tell the code that the following character has a special meaning. Escape characters all start with the backslash key (`\`) combined with another character within a string to format the given string a certain way.

Here is a list of several of the common escape characters:

| Escape Character | How it formats               |
| ---------------- | ---------------------------- |
| \\               | Backslash                    |
| \"               | Double Quote                 |
| \n               | Line Break                   |
| \t               | Tab (horizontal indentation) |

Let’s use an escape character to add the quotation marks to the example on quotation marks above, but this time we’ll use double quotes to denote the string:

```go
fmt.Println("Sammy says, \"Hello!\"")
```

```
Output
Sammy says, "Hello!"
```

By using the escape character `\"` we are able to use double quotes to enclose a string that includes text quoted between double quotes.

We can use the `\n` escape character to break lines without hitting the enter or return key:

```go
fmt.Println("This string\nspans multiple\nlines.")
```

```
Output
This string
spans multiple
lines.
```

We can combine escape characters, too. Let’s print a multi-line string and include tab spacing for an itemized list, for example:

```go
fmt.Println("1.\tShark\n2.\tShrimp\n10.\tSquid")
```

```go
Output
1.      Shark
2.      Shrimp
10.     Squid
```

The horizontal indentation provided with the `\t` escape character ensures alignment within the second column in the preceding example, making the output extremely readable for humans.

Escape characters are used to add additional formatting to strings that may be difficult or impossible to achieve. Without escape characters, you would not be able to construct the string `Sammy says, "I like to use the `fmt` package"`.

## Multiple Lines

Printing strings on multiple lines can make text more readable to humans. With multiple lines, strings can be grouped into clean and orderly text, formatted as a letter, or used to maintain the linebreaks of a poem or song lyrics.

To create strings that span multiple lines, back quotes are used to enclose the string. Keep in mind that while this will preserve the line returns, it is also creating a `raw` string literal.

```go
`
This string is on 
multiple lines
within three single 
quotes on either side.
`
```

You will notice if you print this that there is a leading and trailing return:

```go
Output

This string is on 
multiple lines
within three single 
quotes on either side.

```

To avoid this, you need to put the first line immediately following the back quote and end the last with the back quote.

```go
`This string is on 
multiple lines
within three single 
quotes on either side.`
```

If you need to create an interpreted string literal, this can be done with double quotes and the `+` operator, but you will need to insert your own line breaks.

```go
"This string is on\n" +
"multiple lines\n" +
"within three single\n" +
"quotes on either side."
```

While back quotes can make it easier to print and read lengthy text, if you need an interpreted string literal, you will need to use double quotes.

## Raw String Literals

What if we don’t want special formatting within our strings? For example, we may need to compare or evaluate strings of computer code that use the backslash on purpose, so we won’t want Go to use it as an escape character.

A **raw** string literal tells Go to ignore all formatting within a string, including escape characters.

We create a raw string by using back quotes around the string:

```go
fmt.Println(`Sammy says,\"The balloon\'s color is red.\"`)
```

```go
Output
Sammy says,\"The balloon\'s color is red.\"
```

By constructing a raw string by using back quotes around a given string, we can retain backslashes and other characters that are used as escape characters.

## Conclusion

This tutorial went over several ways to format text in Go through working with strings. By using techniques such as escape characters or raw strings, we are able to ensure that the strings of our program are rendered correctly on-screen so that the end user is able to easily read all of the output text.