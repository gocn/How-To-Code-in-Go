# How To Write Your First Program in Go

## Introduction

The “Hello, World!” program is a classic and time-honored tradition in computer programming. It’s a simple and complete first program for beginners, and it’s a good way to make sure your environment is properly configured.

This tutorial will walk you through creating this program in Go. However, to make the program more interesting, you’ll modify the traditional “Hello, World!” program so that it asks the user for their name. You’ll then use the name in the greeting. When you’re done with the tutorial, you’ll have a program that looks like this when you run it:

```
OutputPlease enter your name.
Sammy
Hello, Sammy! I'm Go!
```

## Prerequisites

Before you begin this tutorial, you will need a [local Go development environment](https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-go) set up on your computer. You can set this up by following one of these tutorials:

-   [How to Install Go and Set Up a Local Programming Environment on macOS](https://www.digitalocean.com/community/tutorials/how-to-install-go-and-set-up-a-local-programming-environment-on-macos)
-   [How to Install Go and Set Up a Local Programming Environment on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-install-go-and-set-up-a-local-programming-environment-on-ubuntu-18-04)
-   [How to Install Go and Set Up a Local Programming Environment on Windows 10](https://www.digitalocean.com/community/tutorials/how-to-install-go-and-set-up-a-local-programming-environment-on-windows-10)

## Step 1 — Writing the Basic “Hello, World!” Program

To write the “Hello, World!” program, open up a command-line text editor such as `nano` and create a new file:

Once the text file opens up in the terminal window, you’ll type out your program:

hello.go

Let’s break down the different components of the code.

`package` is a Go _keyword_ that defines which code bundle this file belongs to. There can be only one package per folder, and each `.go` file has to declare the same package name at the top of its file. In this example, the code belongs to the `main` package.

`import` is a Go _keyword_ that tells the Go compiler which other packages you want to use in this file. Here you import the `fmt` package that comes with the standard library. The `fmt` package provides formatting and printing functions that can be useful when developing.

`fmt.Println` is a Go _function_, found in the `fmt` package, that tells the computer to print some text to the screen.

You follow the `fmt.Println` function by a sequence of characters, like `"Hello, World!"`, enclosed in quotation marks. Any characters that are inside of quotation marks are called a _string_. The `fmt.Println` function will print this string to the screen when the program runs.

Save and exit `nano` by typing `CTRL + X`, when prompted to save the file, press `Y`.

Now you can try your program.

## Step 2 — Running a Go Program

With your “Hello, World!” program written, you’re ready to run the program. You’ll use the `go` command, followed by the name of the file you just created.

The program will execute and display this output:

```
OutputHello, World!
```

Let’s explore what actually happened.

Go programs need to compile before they run. When you call `go run` with the name of a file, in this case `hello.go`, the `go` command will _compile_ the application and then run the resulting binary. For programs written in _compiled_ programming languages, a compiler will take the source code of a program and generate another type of lower-level code (such as machine code) to produce an executable program.

Go applications require a `main` package and exactly **one** `main()` function that serves as the entry point for the application. The `main` function takes no arguments and returns no values. Instead it tells the Go compiler that the package should be compiled as an executable package.

Once compiled, the code executes by entering the `main()` function in the `main` package. It executes the line `fmt.Println("Hello, World!")` by _calling_ the `fmt.Println` function. The string value of `Hello, World!` is then _passed_ to the function. In this example, the string `Hello, World!` is also called an _argument_ since it is a value that is passed to a method.

The quotes that are on either side of `Hello, World!` are not printed to the screen because you use them to tell Go where your string begins and ends.

In this step, you’ve created a working “Hello, World!” program with Go. In the next step, you will explore how to make the program more interactive.

## Step 3 — Prompting for User Input

Every time you run your program, it produces the same output. In this step, you can add to your program to prompt the user for their name. You’ll then use their name in the output.

Instead of modifying your existing program, create a new program called `greeting.go` with the `nano` editor:

First, add this code, which prompts the user to enter their name:

greeting.go

Once again, you use the `fmt.Println` function to print some text to the screen.

Now add the highlighted line to store the user’s input:

greeting.go

```go
package main

import (
"fmt"
)

func main() {
  fmt.Println("Please enter your name.")
  var name string
}
```

The `var name string` line will create a new variable using the `var` _keyword_. You name the variable `name`, and it will be of type `string`.

Then, add the highlighted line to capture the user’s input:

greeting.go

```
package main

import (
"fmt"
)

func main() {
  fmt.Println("Please enter your name.")
  var name string
  fmt.Scanln(&name)
}
```

The `fmt.Scanln` method tells the computer to wait for input from the keyboard ending with a new line or (`\n`), character. This pauses the program, allowing the user to enter any text they want. The program will continue when the user presses the `ENTER` key on their keyboard. All of the keystrokes, including the `ENTER` keystroke, are then captured and converted to a string of characters.

You want to use those characters in your program’s output, so you save those characters by _writing_ them into the string _variable_ called `name`. Go stores that string in your computer’s memory until the program finishes running.

Finally, add the following highlighted line in your program to print the output:

greeting.go

```
package main

import (
"fmt"
)

func main() {
  fmt.Println("Please enter your name.")
  var name string
  fmt.Scanln(&name)
  fmt.Printf("Hi, %s! I'm Go!", name)
}
```

This time, instead of using the `fmt.Println` method again, you’re using `fmt.Printf`. The `fmt.Printf` function takes a string, and using special printing _verbs_, (`%s`), it injects the value of `name` into the string. You do this because Go does not support _string interpolation_, which would let you take the value assigned to a variable and place it inside of a string.

Save and exit `nano` by pressing `CTRL + X`, and press `Y` when prompted to save the file.

Now run the program. You’ll be prompted for your name, so enter it and press `ENTER`. The output might not be exactly what you expect:

```
OutputPlease enter your name.
Sammy
Hi, Sammy
! I'm Go!
```

Instead of `Hi, Sammy! I'm Go!`, there’s a line break right after the name.

The program captured **all** of our keystrokes, including the `ENTER` key that we pressed to tell the program to continue. In a string, pressing the `ENTER` key creates a special character that creates a new line. The program’s output is doing exactly what you told it to do; it’s displaying the text you entered, including that new line. It’s not what you expected the output to be, but you can fix it with additional functions.

Open the `greeting.go` file in your editor:

Locate this line in your program:

greeting.go

Add the following line right after it:

greeting.go

```
name = strings.TrimSpace(name)
```

This uses the `TrimSpace` function, from Go’s standard library `strings` package, on the string that you captured with `fmt.Scanln`. The `strings.TrimSpace` function removes any space characters, including new lines, from the start and end of a string. In this case, it removes the newline character at the end of the string created when you pressed `ENTER`.

To use the `strings` package you need to import it at the top of the program.

Locate these lines in your program:

greeting.go

Add the following line to import the `strings` package:

greeting.go

```
import (
"fmt"
"strings"
)
```

Your program will now contain the following:

greeting.go

Save and exit `nano`. Press `CTRL + X`, then press `Y` when prompted to save the file.

Run the program again:

This time, after you enter your name and press `ENTER`, you get the expected output:

```
OutputPlease enter your name.
Sammy
Hi, Sammy! I'm Go!
```

You now have a Go program that takes input from a user and prints it back to the screen.

## Conclusion

In this tutorial, you wrote a “Hello, World!” program that takes input from a user, processes the results, and displays the output. Now that you have a basic program to work with, try to expand your program further. For example, ask for the user’s favorite color, and have the program say that its favorite color is red. You might even try to use this same technique to create a simple Mad-Lib program.