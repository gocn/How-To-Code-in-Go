A _string_ is a sequence of one or more characters (letters, numbers, symbols) that can be either a constant or a variable. Made up of [Unicode](https://unicode.org/), strings are immutable sequences, meaning they are unchanging.

Because text is such a common form of data that we use in everyday life, the string data type is a very important building block of programming.

This Go tutorial will go over how to create and print strings, how to concatenate and replicate strings, and how to store strings in variables.

## String Literals

In Go, strings exist within either back quotes `` ` `` (sometimes referred to as back ticks) or double quotes `"`. Depending on which quotes you use, the string will have different characteristics.

Using back quotes, as in `` ` ```bar``` ` ``, will create a _raw_ string literal. In a raw string literal, any character may appear between quotes, with the exception of back quotes. Here’s an example of a raw string literal:

```
`Say "hello" to Go!`
```

Backslashes have no special meaning inside of raw string literals. For instance, `\n` will appear as the actual characters, backslash `\` and letter `n`. Unlike interpreted string literals, in which `\n` would insert an actual new line.

Raw string literals may also be used to create multi-line strings:

```
`Go is expressive, concise, clean, and efficient.
Its concurrency mechanisms make it easy to write programs
that get the most out of multi-core and networked machines,
while its novel type system enables flexible and modular
program construction. Go compiles quickly to machine code
yet has the convenience of garbage collection and the power
of run-time reflection. It's a fast, statically typed,
compiled language that feels like a dynamically typed,
interpreted language.`
```

Interpreted string literals are character sequences between double quotes, as in `"bar"`. Within the quotes, any character may appear with the exception of newline and unescaped double quotes.

You will almost always use interpreted string literals because they allow for escape characters within them.

Now that you understand how strings are formatted in Go, let’s take a look at how you can print strings in programs.

## Printing Strings

You can print out strings by using the `fmt` package from the system library and calling the `Println()` function:

```
OutputLet's print out this string.
```

You have to `import` system packages when you use them, so a simple program to print out a string would look like this:

## String Concatenation

_Concatenation_ means joining strings together, end-to-end, to create a new string. You can concatenate strings with the `+` operator. Keep in mind that when you work with numbers, `+` will be an operator for addition, but when used with strings it is a joining operator.

Let’s combine the string literals `"Sammy"` and `"Shark"` together with concatenation through a `fmt.Println()` statement:

```
OutputSammyShark
```

If you would like a whitespace between the two strings, you can simply include the whitespace within a string. In this example, add the whitespace within the quotes after `Sammy`:

```
OutputSammy Shark
```

The `+` operator can not be used between two different data types. As an example, you can’t concatenate strings and integers together. If you were to try to write the following:

You will receive the following errors:

```
Outputcannot convert "Sammy" (type untyped string) to type int
invalid operation: "Sammy" + 27 (mismatched types string and int)
```

If you wanted to create the string `"Sammy27"`, you could do so by putting the number `27` in quotes (`"27"`) so that it is no longer an integer but is instead a string. Converting numbers to strings for concatenation can be useful when dealing with zip codes or phone numbers. For example, you wouldn’t want to perform addition between a country code and an area code, but you do want them to stay together.

When you combine two or more strings through concatenation, you are creating a new string that you can use throughout your program.

## Storing Strings in Variables

**[Variables](https://www.digitalocean.com/community/tutorials/how-to-use-variables-in-go)** are symbols that you can use to store data in a program. You can think of them as an empty box that you fill with some data or value. Strings are data, so you can use them to fill up a variable. Declaring strings as variables can make it easier to work with strings throughout your Go programs.

To store a string inside a variable, simply assign a variable to a string. In this case, declare `s` as your variable:

**Note:** If you’re familiar with other programming languages, you may have written the variable as `sammy`. Go, however, favors shorter variable names. Choosing `s` for the variable name in this case would be considered more appropriate for the style in which Go is written.

Now that you have the variable `s` set to that particular string, you can print the variable like so:

You will then receive the following output:

```
OutputSammy likes declaring strings.
```

By using variables to stand in for strings, you do not have to retype a string each time you want to use it, making it more simple for you to work with and manipulate strings within your programs.

## Conclusion

This tutorial went over the basics of working with the string data type in the Go programming language. Creating and printing strings, concatenating and replicating strings, and storing strings in variables will provide you with the fundamentals to use strings in your Go programs.