

# How To Use the Flag Package in Go

### Introduction

Command-line utilities are rarely useful out of the box without additional configuration. Good defaults are important, but useful utilities need to accept configuration from users. On most platforms, command-line utilities accept flags to customize the command’s execution. Flags are key-value delimited strings added after the name of the command. Go lets you craft command-line utilities that accept flags by using the `flag` package from the standard library.

In this tutorial you’ll explore various ways to use the `flag` package to build different kinds of command-line utilities. You’ll use a flag to control program output, introduce positional arguments where you mix flags and other data, and then implement sub-commands.

## Using a Flag to Change a Program’s Behavior

Using the `flag` package involves three steps: First, [define variables](https://www.digitalocean.com/community/tutorials/how-to-use-variables-and-constants-in-go) to capture flag values, then define the flags your Go application will use, and finally, parse the flags provided to the application upon execution. Most of the functions within the `flag` package are concerned with defining flags and binding them to variables that you have defined. The parsing phase is handled by the `Parse()` function.

To illustrate, you’ll create a program that defines a [Boolean](https://www.digitalocean.com/community/tutorials/understanding-boolean-logic-in-go) flag that changes the message that will be printed to standard output. If there’s a `-color` flag provided, the program will print a message in blue. If no flag is provided, the message will be printed without any color.

Create a new file called `boolean.go`:

```bash
nano boolean.go
```

Add the following code to the file to create the program:

boolean.go

```go
package main

import (
	"flag"
	"fmt"
)

type Color string

const (
	ColorBlack  Color = "\u001b[30m"
	ColorRed          = "\u001b[31m"
	ColorGreen        = "\u001b[32m"
	ColorYellow       = "\u001b[33m"
	ColorBlue         = "\u001b[34m"
	ColorReset        = "\u001b[0m"
)

func colorize(color Color, message string) {
	fmt.Println(string(color), message, string(ColorReset))
}

func main() {
	useColor := flag.Bool("color", false, "display colorized output")
	flag.Parse()

	if *useColor {
		colorize(ColorBlue, "Hello, DigitalOcean!")
		return
	}
	fmt.Println("Hello, DigitalOcean!")
}
```

This example uses [ANSI Escape Sequences](https://en.wikipedia.org/wiki/ANSI_escape_code) to instruct the terminal to display colorized output. These are specialized sequences of characters, so it makes sense to define a new type for them. In this example, we’ve called that type `Color`, and defined the type as a `string`. We then define a palette of colors to use in the `const` block that follows. The `colorize` function defined after the `const` block accepts one of these `Color` constants and a `string` variable for the message to colorize. It then instructs the terminal to change color by first printing the escape sequence for the color requested, then prints the message, and finally requests that the terminal reset its color by printing the special color reset sequence.

Within `main`, we use the `flag.Bool` function to define a Boolean flag called `color`. The second parameter to this function, `false`, sets the default value for this flag when it is not provided. Contrary to expectations you may have, setting this to `true` does not invert the behavior such that providing a flag will cause it to become false. Consequently, the value of this parameter is almost always `false` with Boolean flags.

The final parameter is a string of documentation that can be printed as a usage message. The value returned from this function is a pointer to a `bool`. The `flag.Parse` function on the next line uses this pointer to set the `bool` variable based on the flags passed in by the user. We are then able to check the value of this `bool` pointer by dereferencing the pointer. More information about pointer variables can be found in the [tutorial on pointers](https://www.digitalocean.com/community/conceptual_articles/understanding-pointers-in-go). Using this Boolean value, we can then call `colorize` when the `-color` flag is set, and call the `fmt.Println` variable when the flag is absent.

Save the file and run the program without any flags:

```bash
go run boolean.go
```

You’ll see the following output:

```
OutputHello, DigitalOcean!
```

Now run this program again with the `-color` flag:

```bash
go run boolean.go -color
```

The output will be the same text, but this time in the color blue.

Flags are not the only values passed to commands. You might also send file names or other data.

## Working with Positional Arguments

Typically commands will take a number of arguments that act as the subject of the command’s focus. For example, the `head` command, which prints the first lines of a file, is often invoked as `head example.txt`. The file `example.txt` is a positional argument in the invocation of the `head` command.

The `Parse()` function will continue to parse flags that it encounters until it detects a non-flag argument. The `flag` package makes these available through the `Args()` and `Arg()` functions.

To illustrate this, you’ll build a simplified re-implementation of the `head` command, which displays the first several lines of a given file:

Create a new file called `head.go` and add the following code:

head.go

```go
package main

import (
	"bufio"
	"flag"
	"fmt"
	"io"
	"os"
)

func main() {
	var count int
	flag.IntVar(&count, "n", 5, "number of lines to read from the file")
	flag.Parse()

	var in io.Reader
	if filename := flag.Arg(0); filename != "" {
		f, err := os.Open(filename)
		if err != nil {
			fmt.Println("error opening file: err:", err)
			os.Exit(1)
		}
		defer f.Close()

		in = f
	} else {
		in = os.Stdin
	}

	buf := bufio.NewScanner(in)

	for i := 0; i < count; i++ {
		if !buf.Scan() {
			break
		}
		fmt.Println(buf.Text())
	}

	if err := buf.Err(); err != nil {
		fmt.Fprintln(os.Stderr, "error reading: err:", err)
	}
}
```

First, we define a `count` variable to hold the number of lines the program should read from the file. We then define the `-n` flag using `flag.IntVar`, mirroring the behavior of the original `head` program. This function allows us to pass our own [pointer](https://www.digitalocean.com/community/conceptual_articles/understanding-pointers-in-go) to a variable in contrast to the `flag` functions that do not have the `Var` suffix. Apart from this difference, the rest of the parameters to `flag.IntVar` follow its `flag.Int` counterpart: the flag name, a default value, and a description. As in the previous example, we then call `flag.Parse()` to process the user’s input.

The next section reads the file. We first define an `io.Reader` variable that will either be set to the file requested by the user, or standard input passed to the program. Within the `if` statement, we use the `flag.Arg` function to access the first positional argument after all flags. If the user supplied a file name, this will be set. Otherwise, it will be the empty string (`""`). When a filename is present, we use the `os.Open` function to open that file and set the `io.Reader` we defined before to that file. Otherwise, we use `os.Stdin` to read from standard input.

The final section uses a `*bufio.Scanner` created with `bufio.NewScanner` to read lines from the `io.Reader` variable `in`. We iterate up to the value of `count` using a [`for` loop](https://www.digitalocean.com/community/tutorials/how-to-construct-for-loops-in-go), calling `break` if scanning the line with `buf.Scan` produces a `false` value, indicating that the number of lines is less than the number requested by the user.

Run this program and display the contents of the file you just wrote by using `head.go` as the file argument:

```bash
go run head.go -- head.go
```

The `--` separator is a special flag recognized by the `flag` package which indicates that no more flag arguments follow. When you run this command, you receive the following output:

```
Outputpackage main

import (
        "bufio"
        "flag"
```

Use the `-n` flag you defined to adjust the amount of output:

```bash
go run head.go -n 1 head.go
```

This outputs only the package statement:

```
Outputpackage main
```

Finally, when the program detects that no positional arguments were supplied, it reads input from standard input, just like `head`. Try running this command:

```bash
echo "fish\nlobsters\nsharks\nminnows" | go run head.go -n 3
```

You’ll see the output:

```
Outputfish
lobsters
sharks
```

The behavior of the `flag` functions you’ve seen so far has been limited to examining the entire command invocation. You don’t always want this behavior, especially if you’re writing a command line tool that supports sub-commands.

## Using FlagSet to Implement Sub-commands

Modern command-line applications often implement “sub-commands” to bundle a suite of tools under a single command. The most well-known tool that uses this pattern is `git`. When examining a command like `git init`, `git` is the command and `init` is the sub-command of `git`. One notable feature of sub-commands is that each sub-command can have its own collection of flags.

Go applications can support sub-commands with their own set of flags using the `flag.(*FlagSet)` type. To illustrate this, create a program that implements a command using two sub-commands with different flags.

Create a new file called `subcommand.go` and add the following content to the file:

```go
package main

import (
	"errors"
	"flag"
	"fmt"
	"os"
)

func NewGreetCommand() *GreetCommand {
	gc := &GreetCommand{
		fs: flag.NewFlagSet("greet", flag.ContinueOnError),
	}

	gc.fs.StringVar(&gc.name, "name", "World", "name of the person to be greeted")

	return gc
}

type GreetCommand struct {
	fs *flag.FlagSet

	name string
}

func (g *GreetCommand) Name() string {
	return g.fs.Name()
}

func (g *GreetCommand) Init(args []string) error {
	return g.fs.Parse(args)
}

func (g *GreetCommand) Run() error {
	fmt.Println("Hello", g.name, "!")
	return nil
}

type Runner interface {
	Init([]string) error
	Run() error
	Name() string
}

func root(args []string) error {
	if len(args) < 1 {
		return errors.New("You must pass a sub-command")
	}

	cmds := []Runner{
		NewGreetCommand(),
	}

	subcommand := os.Args[1]

	for _, cmd := range cmds {
		if cmd.Name() == subcommand {
			cmd.Init(os.Args[2:])
			return cmd.Run()
		}
	}

	return fmt.Errorf("Unknown subcommand: %s", subcommand)
}

func main() {
	if err := root(os.Args[1:]); err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
}
```

This program is divided into a few parts: the `main` function, the `root` function, and the individual functions to implement the sub-command. The `main` function handles errors returned from commands. If any function returns an [error](https://www.digitalocean.com/community/tutorials/handling-errors-in-go), the `if` statement will catch it, print the error, and the program will exit with a status code of `1`, indicating that an error occurred to the rest of the operating system. Within `main`, we pass all of the arguments the program was invoked with to `root`. We remove the first argument, which is the name of the program (in the previous examples `./subcommand`) by slicing `os.Args` first.

The `root` function defines `[]Runner`, where all sub-commands would be defined. `Runner` is an [interface](https://www.digitalocean.com/community/tutorials/how-to-use-interfaces-in-go) for sub-commands that allows `root` to retrieve the name of the sub-command using `Name()` and compare it against the contents `subcommand` variable. Once the correct sub-command is located after iterating through the `cmds` variable we initialize the sub-command with the rest of the arguments and invoke that command’s `Run()` method.

We only define one sub-command, though this framework would easily allow us to create others. The `GreetCommand` is instantiated using `NewGreetCommand` where we create a new `*flag.FlagSet` using `flag.NewFlagSet`. `flag.NewFlagSet` takes two arguments: a name for the flag set, and a strategy for reporting parsing errors. The `*flag.FlagSet`’s name is accessible using the `flag.(*FlagSet).Name` method. We use this in the `(*GreetCommand).Name()` method so the name of the sub-command matches the name we gave to the `*flag.FlagSet`. `NewGreetCommand` also defines a `-name` flag in a similar way to previous examples, but it instead calls this as a method off the `*flag.FlagSet` field of the `*GreetCommand`, `gc.fs`. When `root` calls the `Init()` method of the `*GreetCommand`, we pass the arguments provided to the `Parse` method of the `*flag.FlagSet` field.

It will be easier to see sub-commands if you build this program and then run it. Build the program:

```bash
go build subcommand.go
```

Now run the program with no arguments:

```bash
./subcommand
```

You’ll see this output:

```
OutputYou must pass a sub-command
```

Now run the program with the `greet` sub-command:

```bash
./subcommand greet
```

This produces the following output:

```
OutputHello World !
```

Now use the `-name` flag with `greet` to specify a name:

```bash
./subcommand greet -name Sammy
```

You’ll see this output from the program:

```
OutputHello Sammy !
```

This example illustrates some principles behind how larger command line applications could be structured in Go. `FlagSet`s are designed to give developers more control over where and how flags are processed by the flag parsing logic.

## Conclusion

Flags make your applications more useful in more contexts because they give your users control over how the programs execute. It’s important to give users useful defaults, but you should give them the opportunity to override settings that don’t work for their situation. You’ve seen that the `flag` package offers flexible choices to present configuration options to your users. You can choose a few simple flags, or build an extensible suite of sub-commands. In either case, using the `flag` package will help you build utilities in the style of the long history of flexible and scriptable command line tools.