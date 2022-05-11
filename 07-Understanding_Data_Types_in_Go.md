### Introduction

Data types specify the kinds of values that particular variables will store when you are writing a program. The data type also determines what operations can be performed on the data.

In this article, we will go over the important data types native to Go. This is not an exhaustive investigation of data types, but will help you become familiar with what options you have available to you in Go. Understanding some basic data types will enable you to write clearer code that performs efficiently.

## Background

One way to think about data types is to consider the different types of data that we use in the real world. An example of data in the real world are numbers: we may use whole numbers (0, 1, 2, …), integers (…, -1, 0, 1, …), and irrational numbers (π), for example.

Usually, in math, we can combine numbers from different types, and get some kind of an answer. We may want to add 5 to π, for example:

```
5 + π
```

We can either keep the equation as the answer to account for the irrational number, or round π to a number with an abbreviated number of decimal places, and then add the numbers together:

```
5 + π = 5 + 3.14 = 8.14 
```

But, if we start to try to evaluate numbers with another data type, such as words, things start to make less sense. How would we solve for the following equation?

```
shark + 8
```

For computers, each data type is quite different—like words and numbers. As a result we have to be careful about how we use varying data types to assign values and how we manipulate them through operations.

## Integers

Like in math, _integers_ in computer programming are whole numbers that can be positive, negative, or 0 (…, -1, 0, 1, …). In Go, an integer is known as an `int`. As with other programming languages, you should not use commas in numbers of four digits or more, so when you write 1,000 in your program, write it as `1000`.

We can print out an integer in a simple way like this:

```
Output-459
```

Or, we can declare a variable, which in this case is a symbol of the number we are using or manipulating, like so:

```
Output-459
```

We can do math with integers in Go, too. In the following code block, we will use the `:=` assignment operator to declare and instantiate the variable `sum`:

```
Output48
```

As the output shows, the mathematical operator `-` subtracted the integer `68` from `116`, resulting in `48`. You’ll learn more about variable declaration in the **Declaring Data Types for Variables** section.

Integers can be used in many ways within Go programs. As you continue to learn about Go, you’ll have a lot of opportunities to work with integers and build upon your knowledge of this data type.

## Floating-Point Numbers

A _floating-point number_ or a _float_ is used to represent [_real numbers_](https://en.wikipedia.org/wiki/Real_number) that cannot be expressed as integers. Real numbers include all rational and irrational numbers, and because of this, floating-point numbers can contain a fractional part, such as 9.0 or -116.42. For the purposes of thinking of a float in a Go program, it is a number that contains a decimal point.

Like we did with integers, we can print out a floating-point number in a simple way like this:

```
Output-459.67
```

We can also declare a variable that stands in for a float, like so:

```
Output-459.67
```

Just like with integers, we can do math with floats in Go, too:

```
Output929.24
```

With integers and floating-point numbers, it is important to keep in mind that 3 ≠ 3.0, as 3 refers to an integer while 3.0 refers to a float.

## Sizes of Numeric Types

In addition to the distinction between integers and floats, Go has two types of numeric data that are distinguished by the static or dynamic nature of their sizes. The first type is an _architecture-independent_ type, which means that the size of the data in bits does not change, regardless of the machine that the code is running on.

Most system architectures today are either 32 bit or 64 bit. For instance, you may be developing for a modern Windows laptop, on which the operating system runs on a 64-bit architecture. However, if you are developing for a device like a fitness watch, you may be working with a 32-bit architecture. If you use an architecture-independent type like `int32`, regardless of the architecture you compile for, the type will have a constant size.

The second type is an _implementation-specific_ type. In this type, the bit size can vary based on the architecture the program is built on. For instance, if we use the `int` type, when Go compiles for a 32-bit architecture, the size of the data type will be 32 bits. If the program is compiled for a 64-bit architecture, the variable will be 64 bits in size.

In addition to data types having different sizes, types like integers also come in two basic types: _signed_ and _unsigned_. An `int8` is a signed integer, and can have a value from -128 to 127. A `uint8` is an unsigned integer, and can only have a positive value of 0 to 255.

The ranges are based on the bit size. For binary data, 8 bits can represent a total of 256 different values. Because an `int` type needs to support both positive and negative values, an 8-bit integer (`int8`) will have a range of -128 to 127, for a total of 256 unique possible values.

Go has the following architecture-independent integer types:

```
uint8       unsigned  8-bit integers (0 to 255)
uint16      unsigned 16-bit integers (0 to 65535)
uint32      unsigned 32-bit integers (0 to 4294967295)
uint64      unsigned 64-bit integers (0 to 18446744073709551615)
int8        signed  8-bit integers (-128 to 127)
int16       signed 16-bit integers (-32768 to 32767)
int32       signed 32-bit integers (-2147483648 to 2147483647)
int64       signed 64-bit integers (-9223372036854775808 to 9223372036854775807)
```

Floats and complex numbers also come in varying sizes:

```
float32     IEEE-754 32-bit floating-point numbers
float64     IEEE-754 64-bit floating-point numbers
complex64   complex numbers with float32 real and imaginary parts
complex128  complex numbers with float64 real and imaginary parts
```

There are also a couple of alias number types, which assign useful names to specific data types:

```
byte        alias for uint8
rune        alias for int32
```

The purpose of the `byte` alias is to make it clear when your program is using bytes as a common computing measurement in character string elements, as opposed to small integers unrelated to the byte data measurement. Even though `byte` and `uint8` are identical once the program is compiled, `byte` is often used to represent character data in numeric form, whereas `uint8` is intended to be a number in your program.

The `rune` alias is a bit different. Where `byte` and `uint8` are exactly the same data, a `rune` can be a single byte or four bytes, a range determined by `int32`. A `rune` is used to represent a Unicode character, whereas only ASCII characters can be represented solely by an `int32` data type.

In addition, Go has the following implementation-specific types:

```
uint     unsigned, either 32 or 64 bits
int      signed, either 32 or 64 bits
uintptr  unsigned integer large enough to store the uninterpreted bits of a pointer value 
```

Implementation-specific types will have their size defined by the architecture the program is compiled for.

### Picking Numeric Data Types

Picking the correct size usually has more to do with performance for the target architecture you are programming for than the size of the data you are working with. However, without needing to know the specific ramifications of performance for your program, you can follow some of these basic guidelines when first starting out.

As discussed earlier in this article, there are architecture-independent types, and implementation-specific types. For integer data, it’s common in Go to use the implementation types like `int` or `uint` instead of `int64` or `uint64`. This will typically result in the fastest processing speed for your target architecture. For instance, if you use an `int64` and compile to a 32-bit architecture, it will take at least twice as much time to process those values as it takes additional CPU cycles to move the data across the architecture. If you used an `int` instead, the program would define it as a 32-bit size for a 32-bit architecture, and would be significantly faster to process.

If you know you won’t exceed a specific size range, then picking an architecture-independent type can both increase speed and decrease memory usage. For example, if you know your data won’t exceed the value of `100`, and will only be a positive number, then choosing a `uint8` would make your program more efficient as it will require less memory.

Now that we have looked at some of the possible ranges for numeric data types, let’s look at what will happen if we exceed those ranges in our program.

### Overflow vs. Wraparound

Go has the potential to both _overflow_ a number and _wraparound_ a number when you try to store a value larger than the data type was designed to store, depending on if the value is calculated at compile time or at runtime. A compile time error happens when the program finds an error as it tries to build the program. A runtime error happens after the program is compiled, while it is actually executing.

In the following example, we set `maxUint32` to its maximum value:

It will compile and run with the following result:

If we add `1` to the value at runtime, it will wraparound to `0`:

On the other hand, let’s change the program to add `1` to the variable when we assign it, before compile time:

At compile time, if the compiler can determine a value will be too large to hold in the data type specified, it will throw an `overflow` error. This means that the value calculated is too large for the data type you specified.

Because the compiler can determine it will overflow the value, it will now throw an error:

```
Outputprog.go:6:36: constant 4294967296 overflows uint32
```

Understanding the boundaries of your data will help you avoid potential bugs in your program in the future.

Now that we have covered numeric types, let’s look at how to store boolean values.

## Booleans

The _boolean_ data type can be one of two values, either `true` or `false`, and is defined as `bool` when declaring it as a data type. Booleans are used to represent the truth values that are associated with the logic branch of mathematics, which informs algorithms in computer science.

The values `true` and `false` will always be with a lowercase `t` and `f` respectively, as they are pre-declared identifiers in Go.

Many operations in math give us answers that evaluate to either true or false:

-   greater than
    -   500 > 100 true
    -   1 > 5 false
-   less than
    -   200 < 400 true
    -   4 < 2 false
-   equal
    -   5 = 5 true
    -   500 = 400 false

Like with numbers, we can store a boolean value in a variable:

We can then print the boolean value with a call to the `fmt.Println()` function:

Since `5` is not greater than `8`, we will receive the following output:

```
Outputfalse
```

As you write more programs in Go, you will become more familiar with how booleans work and how different functions and operations evaluating to either `true` or `false` can change the course of the program.

## Strings

A string is a sequence of one or more characters (letters, numbers, symbols) that can be either a constant or a variable. Strings exist within either back quotes `` ` `` or double quotes `"` in Go and have different characteristics depending on which quotes you use.

If you use the back quotes, you are creating a _raw_ string literal. If you use the double quotes, you are creating an _interpreted_ string literal.

### Raw String Literals

Raw string literals are character sequences between back quotes, often called back ticks. Within the quotes, any character will appear just as it is displayed between the back quotes, except for the back quote character itself.

```
OutputSay "hello" to Go!
```

Usually, backslashes are used to represent special characters in strings. For example, in an interpreted string, `\n` would represent a new line in a string. However, backslashes have no special meaning inside of raw string literals:

Because the backslash has no special meaning in a string literal, it will actually print out the value of `\n` instead of making a new line:

```
OutputSay "hello" to Go!\n
```

Raw string literals may also be used to create multiline strings:

```
OutputThis string is on 
multiple lines
within a single back 
quote on either side.
```

In the preceding code blocks, the new lines were carried over literally from input to output.

### Interpreted String Literals

Interpreted string literals are character sequences between double quotes, as in `"bar"`. Within the quotes, any character may appear except newline and unescaped double quotes. To show double quotes in an interpreted string, you can use the backslash as an escape character, like so:

```
OutputSay "hello" to Go!
```

You will almost always use interpreted string literals because they allow for escape characters within them. For more on working with strings, check out [An Introduction to Working with Strings in Go](https://www.digitalocean.com/community/tutorials/an-introduction-to-working-with-strings-in-go).

### Strings with UTF-8 Characters

UTF-8 is an encoding scheme used to encode variable width characters into one to four bytes. Go supports UTF-8 characters out of the box, without any special setup, libaries, or packages. Roman characters such as the letter `A` can be represented by an ASCII value such as the number 65. However, with special characters such as an international character of `世`, UTF-8 would be required. Go uses the `rune` alias type for UTF-8 data.

You can use the `range` keyword in a `for` loop to index through any string in Go, even a UTF-8 string. `for` loops and `range` will be covered in more depth later in the series; for now, it’s important to know that we can use this to count the bytes in a given string:

In the above code block, we declared the variable `a` and assigned the value of `Hello, 世界` to it. The text assigned has UTF-8 characters in it.

We then used a standard `for` loop as well as the `range` keyword. In Go, the `range` keyword will index through a string returning one character at a time, as well as the byte index the character is at in the string.

Using the `fmt.Printf` function, we provided a format string of `%d: %s\n`. `%d` is the print verb for a digit (in this case an integer), and `%s` is the print verb for a string. We then provided the values of `i`, which is the current index of the `for` loop, and `c`, which is the current character in the `for` loop.

Finally, we printed the entire length of the variable `a` by using the builtin `len` function.

Earlier, we mentioned that a rune is an alias for `int32` and can be made up of one to four bytes. The `世` character takes three bytes to define and the index moves accordingly when ranging through the UTF-8 string. This is the reason that `i` is not sequential when it is printed out.

```
Output0: H
1: e
2: l
3: l
4: o
5: ,
6:
7: 世
10: 界
length of 'Hello, 世界':  13
```

As you can see, the length is longer than the number of times it took to range over the string.

You won’t always be working with UTF-8 strings, but when you are, you’ll now understand why they are runes and not a single `int32`.

## Declaring Data Types for Variables

Now that you know about the different primitive data types, we will go over how to assign these types to variables in Go.

In Go, we can define a variable with the keyword `var` followed by the name of the variable and the data type desired.

In the following example we will declare a variable called `pi` of type `float64`.

The keyword `var` is the first thing declared:

Followed by the name of our variable, `pi`:

And finally the data type `float64`:

We can optionally specify an initial value as well, such as `3.14`:

Go is a _statically typed_ language. Statically typed means that each statement in the program is checked at compile time. It also means that the data type is bound to the variable, whereas in dynamically linked languages, the data type is bound to the value.

For example, in Go, the type is declared when declaring a variable:

Each of these variables could be a different data type if you declared them differently.

This is different from a language like PHP, where the data type is associated to the value:

In the preceding code block, the first `$s` is a string because it is assigned the value `"sammy"`, and the second is an integer because it has the value `123`.

Next, let’s look at more complex data types like arrays.

## Arrays

An _array_ is an ordered sequence of elements. The capacity of an array is defined at creation time. Once an array has allocated its size, the size can no longer be changed. Because the size of an array is static, it means that it only allocates memory once. This makes arrays somewhat rigid to work with, but increases performance of your program. Because of this, arrays are typically used when optimizing programs. _Slices_, covered next, are more flexible, and constitute what you would think of as arrays in other languages.

Arrays are defined by declaring the size of the array, then the data type with the values defined between curly brackets `{ }`.

An array of strings looks like this:

We can store an array in a variable and print it out:

```
Output[blue coral staghorn coral pillar coral]
```

As mentioned before, slices are similar to arrays, but are much more flexible. Let’s take a look at this mutable data type.

## Slices

A _slice_ is an ordered sequence of elements that can change in length. Slices can increase their size dynamically. When you add new items to a slice, if the slice does not have enough memory to store the new items, it will request more memory from the system as needed. Because a slice can be expanded to add more elements when needed, they are more commonly used than arrays.

Slices are defined by declaring the data type preceded by an opening and closing square bracket `[]` and having values between curly brackets `{ }` .

A slice of integers looks like this:

A slice of floats looks like this:

A slice of strings looks like this:

Let’s define our slice of strings as `seaCreatures`:

We can print them out by calling the variable:

The output will look exactly like the list that we created:

```
Output[shark cuttlefish squid mantis shrimp]
```

We can use the `append` keyword to add an item to our slice. The following command will add the string value of `seahorse` to the slice:

You can verify it was added by printing it out:

```
Output[shark cuttlefish squid mantis shrimp seahorse]
```

As you can see, if you need to manage an unknown size of elements, a slice will be much more versatile than an array.

## Maps

The _map_ is Go’s built-in hash or dictionary type. Maps use _keys_ and _values_ as a pair to store data. This is useful in programming to quickly look up values by an index, or in this case, a key. For instance, you may want to keep a map of users, indexed by their user ID. The key would be the user ID, and the user object would be the value. A map is constructed by using the keyword `map` followed by the key data type in square brackets `[ ]`, followed by the value data type and the key value pairs in curly braces.

```
map[key]value{}
```

Typically used to hold data that are related, such as the information contained in an ID, a map looks like this:

You will notice that in addition to the curly braces, there are also colons throughout the map. The words to the left of the colons are the keys. Keys can be any _comparable_ type in Go. Comparable types are primitive types like `strings`, `ints`, etc. A primitive type is defined by the language, and not built from combining any other types. While they can be user-defined types, it’s considered best practice to keep them simple to avoid programming errors. The keys in the dictionary above are: `name`, `animal`, `color`, and `location`.

The words to the right of the colons are the values. Values can be comprised of any data type. The values in the dictionary above are: `Sammy`, `shark`, `blue`, and `ocean`.

Let’s store the map inside a variable and print it out:

```
Outputmap[animal:shark color:blue location:ocean name:Sammy]
```

If we want to isolate Sammy’s color, we can do so by calling `sammy["color"]`. Let’s print that out:

```
Outputblue
```

As maps offer key-value pairs for storing data, they can be important elements in your Go program.

## Conclusion

At this point, you should have a better understanding of some of the major data types that are available for you to use in Go. Each of these data types will become important as you develop programming projects in the Go language.

Once you have a solid grasp of data types available to you in Go, you can learn [How To Convert Data Types](https://www.digitalocean.com/community/tutorials/how-to-convert-data-types-in-go) in order to change your data types according to the situation.