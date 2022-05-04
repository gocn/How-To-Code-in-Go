# Understanding init in Go
## Introduction

In Go, the predefined `init()` function sets off a piece of code to run before any other part of your package. This code will execute as soon as the [package is imported](https://www.digitalocean.com/community/tutorials/importing-packages-in-go), and can be used when you need your application to initialize in a specific state, such as when you have a specific configuration or set of resources with which your application needs to start. It is also used when *importing a side effect*, a technique used to set the state of a program by importing a specific package. This is often used to `register` one package with another to make sure that the program is considering the correct code for the task.

Although `init()` is a useful tool, it can sometimes make code difficult to read, since a hard-to-find `init()` instance will greatly affect the order in which the code is run. Because of this, it is important for developers who are new to Go to understand the facets of this function, so that they can make sure to use `init()` in a legible manner when writing code.

In this tutorial, you’ll learn how `init()` is used for the setup and initialization of specific package variables, one time computations, and the registration of a package for use with another package.

## Prerequisites

For some of the examples in this article, you will need:

* A Go workspace set up by following [How To Install Go and Set Up a Local Programming Environment](https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-go). This tutorial will use the following file structure:
```bash
.
├── bin 
│ 
└── src
    └── github.com
        └── gopherguides
```
## Declaring `init()`

Any time you declare an `init()` function, Go will load and run it prior to anything else in that package. To demonstrate this, this section will walk through how to define an `init()` function and show the effects on how the package runs.

Let’s first take the following as an example of code without the `init()` function:


```go
package main

import "fmt"

var weekday string

func main() {
	fmt.Printf("Today is %s", weekday)
}
```

In this program, we declared a global [variable](https://www.digitalocean.com/community/tutorials/how-to-use-variables-and-constants-in-go) called `weekday`. By default, the value of `weekday` is an empty string.

Let’s run this code:

```go
go run main.go
```

Because the value of `weekday` is blank, when we run the program, we will get the following output:

```bash
Output
Today is
```
We can fill in the blank variable by introducing an `init()` function that initializes the value of `weekday` to the current day. Add in the following highlighted lines to `main.go`:

```go
package main

import (
	"fmt"
	"time"
)

var weekday string

func init() {
	weekday = time.Now().Weekday().String()
}

func main() {
	fmt.Printf("Today is %s", weekday)
}
```
In this code, we imported and used the `time` package to get the current day of the week (`Now().Weekday().String()`), then used `init()` to initialize `weekday` with that value.
Now when we run the program, it will print out the current weekday:

```bash
Output
Today is Monday
```
While this illustrates how `init()` works, a much more typical use case for `init()` is to use it when importing a package. This can be useful when you need to do specific setup tasks in a package before you want the package to be used. To demonstrate this, let’s create a program that will require a specific initialization for the package to work as intended.
## Initializing Packages on Import

First, we will write some code that selects a random creature from a [slice](https://www.digitalocean.com/community/tutorials/understanding-arrays-and-slices-in-go) and prints it out. However, we won’t use `init()` in our initial program. This will better show the problem we have, and how `init()` will solve our problem.

From within your `src/github.com/gopherguides/` directory, create a folder called `creature` with the following command:

```bash
mkdir creature
```

Inside the `creature` folder, create a file called `creature.go`:

```bash
nano creature/creature.go
```

In this file, add the following contents:

```go
package creature

import (
	"math/rand"
)

var creatures = []string{"shark", "jellyfish", "squid", "octopus", "dolphin"}

func Random() string {
	i := rand.Intn(len(creatures))
	return creatures[i]
}
```

This file defines a variable called `creatures` that has a set of sea creatures initialized as values. It also has an [exported](https://www.digitalocean.com/community/tutorials/understanding-package-visibility-in-go#exported-and-unexported-items) `Random` function that will return a random value from the `creatures` variable.

Save and quit this file.

Next, let’s create a `cmd` package that we will use to write our `main()` function and call the `creature` package.

At the same file level from which we created the `creature` folder, create a `cmd` folder with the following command:

```bash
mkdir cmd
```

Inside the `cmd` folder, create a file called `main.go`:

```bash
nano cmd/main.go
```

Add the following contents to the file:

cmd/main.go

```go
package main

import (
	"fmt"

	"github.com/gopherguides/creature"
)

func main() {
	fmt.Println(creature.Random())
	fmt.Println(creature.Random())
	fmt.Println(creature.Random())
	fmt.Println(creature.Random())
}
```

Here we imported the `creature` package, and then in the `main()` function, used the `creature.Random()` function to retrieve a random creature and print it out four times.

Save and quit `main.go`.

We now have our entire program written. However, before we can run this program, we’ll need to also create a couple of configuration files for our code to work properly. Go uses [Go Modules](https://blog.golang.org/using-go-modules) to configure package dependencies for importing resources. These modules are configuration files placed in your package directory that tell the compiler where to import packages from. While learning about modules is beyond the scope of this article, we can write just a couple lines of configuration to make this example work locally.

In the `cmd` directory, create a file named `go.mod`:

```bash
nano cmd/go.mod
```

Once the file is open, place in the following contents:

cmd/go.mod

```go
module github.com/gopherguides/cmd
 replace github.com/gopherguides/creature => ../creature
```
The first line of this file tells the compiler that the `cmd` package we created is in fact `github.com/gopherguides/cmd`. The second line tells the compiler that `github.com/gopherguides/creature` can be found locally on disk in the `../creature` directory.
Save and close the file. Next, create a `go.mod` file in the `creature` directory:

```bash
nano creature/go.mod
```

Add the following line of code to the file:

creature/go.mod

```go
 module github.com/gopherguides/creature
```
This tells the compiler that the `creature` package we created is actually the `github.com/gopherguides/creature` package. Without this, the `cmd` package would not know where to import this package from.
Save and quit the file.

You should now have the following directory structure and file layout:

```bash
├── cmd
│   ├── go.mod
│   └── main.go
└── creature
    ├── go.mod
    └── creature.go
```
Now that we have all the configuration completed, we can run the `main` program with the following command:
```bash
go run cmd/main.go
```

This will give:

```bash
Output
jellyfish
squid
squid
dolphin
```
When we ran this program, we received four values and printed them out. If we run the program multiple times, we will notice that we **always** get the same output, rather than a random result as expected. This is because the `rand` package creates pseudorandom numbers that will consistently generate the same output for a single initial state. To achieve a more random number, we can *seed* the package, or set a changing source so that the initial state is different every time we run the program. In Go, it is common to use the current time to seed the `rand` package.
Since we want the `creature` package to handle the random functionality, open up this file:

```bash
nano creature/creature.go
```

Add the following highlighted lines to the `creature.go` file:

creature/creature.go

```go
package creature

import (
	"math/rand"
	"time"
)

var creatures = []string{"shark", "jellyfish", "squid", "octopus", "dolphin"}

func Random() string {
	rand.Seed(time.Now().UnixNano())
	i := rand.Intn(len(creatures))
	return creatures[i]
}
```
In this code, we imported the `time` package and used `Seed()` to seed the current time. Save and exit the file.
Now, when we run the program we will get a random result:

```bash
go run cmd/main.go
```

```bash
Output
jellyfish
octopus
shark
jellyfish
```
If you continue to run the program over and over, you will continue to get random results. However, this is not yet an ideal implementation of our code, because every time `creature.Random()` is called, it also re-seeds the `rand` package by calling `rand.Seed(time.Now().UnixNano())` again. Re-seeding will increase the chance of seeding with the same initial value if the internal clock has not changed, which will cause possible repetitions of the random pattern, or will increase CPU processing time by having your program wait for the clock to change.
To fix this, we can use an `init()` function. Let’s update the `creature.go` file:

```bash
nano creature/creature.go
```

Add the following lines of code:

creature/creature.go

```go
package creature

import (
	"math/rand"
	"time"
)

var creatures = []string{"shark", "jellyfish", "squid", "octopus", "dolphin"}

func init() {
	rand.Seed(time.Now().UnixNano())
}

func Random() string {
	i := rand.Intn(len(creatures))
	return creatures[i]
}
```
Adding the `init()` function tells the compiler that when the `creature` package is imported, it should run the `init()` function once, providing a single seed for the random number generation. This ensures that we don’t run code more than we have to. Now if we run the program, we will continue to get random results:
```bash
go run cmd/main.go
```

```bash
Output
dolphin
squid
dolphin
octopus
```
In this section, we have seen how using `init()` can ensure that the appropriate calculations or initializations are performed prior to the package being used. Next, we will see how to use multiple `init()` statements in a package.
## Multiple Instances of `init()`

Unlike the `main()` function that can only be declared once, the `init()` function can be declared multiple times throughout a package. However, multiple `init()`s can make it difficult to know which one has priority over the others. In this section, we will show how to maintain control over multiple `init()` statements.

In most cases, `init()` functions will execute in the order that you encounter them. Let’s take the following code as an example:

main.go

```go
package main

import "fmt"

func init() {
	fmt.Println("First init")
}

func init() {
	fmt.Println("Second init")
}

func init() {
	fmt.Println("Third init")
}

func init() {
	fmt.Println("Fourth init")
}

func main() {}
```

If we run the program with the following command:

```bash
go run main.go
```

We’ll receive the following output:

```bash
Output
First init
Second init
Third init
Fourth init
```
Notice that each `init()` is run in the order in which the compiler encounters it. However, it may not always be so easy to determine the order in which the `init()` function will be called.
Let’s look at a more complicated package structure in which we have multiple files each with their own `init()` function declared in them. To illustrate this, we will create a program that shares a variable called `message` and prints it out.

Delete the `creature` and `cmd` directories and their contents from the earlier section, and replace them with the following directories and file structure:

```bash
├── cmd
│   ├── a.go
│   ├── b.go
│   └── main.go
└── message
    └── message.go
```
Now let’s add the contents of each file. In `a.go`, add the following lines:
cmd/a.go

```go
package main

import (
	"fmt"

	"github.com/gopherguides/message"
)

func init() {
	fmt.Println("a ->", message.Message)
}
```

This file contains a single `init()` function that prints out the value of `message.Message` from the `message` package.

Next, add the following contents to `b.go`:

cmd/b.go

```go
package main

import (
	"fmt"

	"github.com/gopherguides/message"
)

func init() {
	message.Message = "Hello"
	fmt.Println("b ->", message.Message)
}
```

In `b.go`, we have a single `init()` function that set’s the value of `message.Message` to `Hello` and prints it out.

Next, create `main.go` to look like the following:

cmd/main.go

```go
package main

func main() {}
```

This file does nothing, but provides an entry point for the program to run.

Finally, create your `message.go` file like the following:

message/message.go

```go
package message

var Message string
```

Our `message` package declares the exported `Message` variable.

To run the program, execute the following command from the `cmd` directory:

```go
go run *.go
```

Because we have multiple Go files in the `cmd` folder that make up the `main` package, we need to tell the compiler that all the `.go` files in the `cmd` folder should be compiled. Using `*.go` tells the compiler to load all the files in the `cmd` folder that end in `.go`. If we issued the command of `go run main.go`, the program would fail to compile as it would not see the code in the `a.go` and `b.go` files.

This will give the following output:

```go
Output
a ->
b -> Hello
```
Per the Go language specification for [Package Initialization](https://golang.org/ref/spec#Package_initialization), when multiple files are encountered in a package, they are processed alphabetically. Because of this, the first time we printed out `message.Message` from `a.go`, the value was blank. The value wasn’t initialized until the `init()` function from `b.go` had been run.
If we were to change the file name of `a.go` to `c.go`, we would get a different result:

```go
Output
b -> Hello
a -> Hello
```

Now the compiler encounters `b.go` first, and as such, the value of `message.Message` is already initialized with `Hello` when the `init()` function in `c.go` is encountered.

This behavior could create a possible problem in your code. It is common in software development to change file names, and because of how `init()` is processed, changing file names may change the order in which `init()` is processed. This could have the undesired effect of changing your program’s output. To ensure reproducible initialization behavior, build systems are encouraged to present multiple files belonging to the same package in lexical file name order to a compiler. One way to ensure that all `init()` functions are loaded in order is to declare them all in one file. This will prevent the order from changing even if file names are changed.

In addition to ensuring the order of your `init()` functions does not change, you should also try to avoid managing state in your package by using *global variables*, i.e., variables that are accessible from anywhere in the package. In the preceding program, the `message.Message` variable was available to the entire package and maintained the state of the program. Because of this access, the `init()` statements were able to change the variable and destablize the predictability of your program. To avoid this, try to work with variables in controlled spaces that have as little access as possible while still allowing the program to work.

We have seen that you can have multiple `init()` declarations in a single package. However, doing so may create undesired effects and make your program hard to read or predict. Avoiding multiple `init()` statements or keeping them all in one file will ensure that the behavior of your program does not change when files are moved around or names are changed.

Next, we will examine how `init()` is used to import with side effects.

## Using `init()` for Side Effects

In Go, it is sometimes desirable to import a package not for its content, but for the side effects that occur upon importing the package. This often means that there is an `init()` statement in the imported code that executes before any of the other code, allowing for the developer to manipulate the state in which their program is starting. This technique is called *importing for a side effect*.

A common use case for importing for side effects is to *register* functionality in your code, which lets a package know what part of the code your program needs to use. In the `image`[ package](https://golang.org/pkg/image/), for example, the `image.Decode` function needs to know which format of image it is trying to decode (`jpg`, `png`, `gif`, etc.) before it can execute. You can accomplish this by first importing a specific program that has an `init()` statement side effect.

Let’s say you are trying to use `image.Decode` on a `.png` file with the following code snippet:

Sample Decoding Snippet

```go
. . .
func decode(reader io.Reader) image.Rectangle {
	m, _, err := image.Decode(reader)
	if err != nil {
		log.Fatal(err)
	}
	return m.Bounds()
}
. . .
```

A program with this code will still compile, but any time we try to decode a `png` image, we will get an error.

To fix this, we would need to first register an image format for `image.Decode`. Luckily, the `image/png` package contains the following `init()` statement:

image/png/reader.go

```go
func init() {
	image.RegisterFormat("png", pngHeader, Decode, DecodeConfig)
}
```

Therefore, if we import `image/png` into our decoding snippet, then the `image.RegisterFormat()` function in `image/png` will run before any of our code:

Sample Decoding Snippet

```go
. . .
import _ "image/png"
. . .

func decode(reader io.Reader) image.Rectangle {
	m, _, err := image.Decode(reader)
	if err != nil {
		log.Fatal(err)
	}
	return m.Bounds()
}
```
This will set the state and register that we require the `png` version of `image.Decode()`. This registration will happen as a side effect of importing `image/png`.
You may have noticed the [blank identifier](https://golang.org/ref/spec#Blank_identifier) (`_`) before `"image/png"`. This is needed because Go does not allow you to import packages that are not used throughout the program. By including the blank identifier, the value of the import itself is discarded, so that only the side effect of the import comes through. This means that, even though we never call the `image/png` package in our code, we can still import it for the side effect.

It is important to know when you need to import a package for its side effect. Without the proper registration, it is likely that your program will compile, but not work properly when it is run. The packages in the standard library will declare the need for this type of import in their documentation. If you write a package that requires importing for side effect, you should also make sure the `init()` statement you are using is documented so users that import your package will be able to use it properly.

## Conclusion

In this tutorial we learned that the `init()` function loads before the rest of the code in your package is loaded, and that it can perform specific tasks for a package like initializing a desired state. We also learned that the order in which the compiler executes multiple `init()` statements is dependent on the order in which the compiler loads the source files. If you would like to learn more about `init()`, check out the official [Golang documentation](https://golang.org/doc/effective_go.html#init), or read through [the discussion in the Go community about the function](https://github.com/golang/go/issues/25885).

You can read more about functions with our [How To Define and Call Functions in Go](https://www.digitalocean.com/community/tutorials/how-to-define-and-call-functions-in-go) article, or explore [the entire How To Code in Go series](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go). 
