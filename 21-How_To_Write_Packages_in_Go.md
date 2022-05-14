# How To Write Packages in Go

A package is made up of Go files that live in the same directory and have the same package statement at the beginning. You can include additional functionality from packages to make your programs more sophisticated. Some packages are available through the Go Standard Library and are therefore installed with your Go installation. Others can be installed with Go’s `go get` command. You can also build your own Go packages by creating Go files in the same directory across which you want to share code by using the necessary package statement.

This tutorial will guide you through writing Go packages for use within other programming files.

## Prerequisites

- Set up a Go programming environment following one of the tutorials from the [How To Install and Set Up a Local Programming Environment for Go](https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-go) series. Create your Go Workspace following Step 5 in the Local Programming Environment tutorials. To follow the example and naming conventions in this article, read the first section Writing and Importing Packages.
- To deepen your knowledge of the GOPATH, read our article [Understanding the GOPATH](https://www.digitalocean.com/community/tutorials/understanding-the-gopath).

## Writing and Importing Packages

Writing a package is just like writing any other Go file. Packages can contain definitions of functions, [types](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go), and [variables](https://www.digitalocean.com/community/tutorials/how-to-use-variables-and-constants-in-go#understanding-variables) that can then be used in other Go programs.

Before we create a new package, we need to be in our Go workspace. This is typically under our `gopath`. For the example, in this tutorial we will call the package `greet`. To do this, we’ve created a directory called `greet` in our `gopath` under our project space. If our organization were `gopherguides`, and we wanted to create the `greet` package under the organization while using Github as our code repository, then our directory would look like this:

```
└── $GOPATH
    └── src
        └── github.com
            └── gopherguides
```

The `greet` directory is within the `gopherguides` directory:

```
└── $GOPATH
    └── src
        └── github.com
            └── gopherguides
                └── greet
```

Finally, we can add the first file in our directory. It is considered common practice that the `primary` or `entry point` file in a package is named after the name of the directory. In this case, we would create a file called `greet.go` inside the `greet` directory:

```
└── $GOPATH
    └── src
        └── github.com
            └── gopherguides
                └── greet
                    └── greet.go
```

With the file created, we can begin to write our code that we want to reuse or share across projects. In this case, we will create a function called `Hello` that prints out `Hello World`.

Open your `greet.go` file in your text editor and add the following code:

greet.go

```go
package greet

import "fmt"

func Hello() {
	fmt.Println("Hello, World!")
}
```


Let’s break this first file down. The first line of each file needs the name of the `package` that you are working in. Since you’re in the `greet` package, you use the `package` keyword followed by the name of the package:

```go
package greet
```


This will tell the compiler to treat everything in the file as being part of the `greet` package.

Next you declare any other packages you need to use with the `import` statement. You’re only using one in this file—the `fmt` package:

```go
import "fmt"
```


Lastly, you create the function `Hello`. It will use the `fmt` package to print out `Hello, World!`:

```go
func Hello() {
	fmt.Println("Hello, World!")
}
```


Now that you’ve written the `greet` package, you can use it in any other package you create. Let’s create a new package in which you’ll use your `greet` package.

You’re going to create a package called `example`, which means you need a directory called `example`. Create this package in your `gopherguides` organization, so the directory structure looks like so:

```
└── $GOPATH
    └── src
        └── github.com
            └── gopherguides
                    └── example
```

Now that you have your directory for your new package, you can create the entry point file. Because this is going to be an executable program, it is considered best practice to name the entry point file `main.go`:

```
└── $GOPATH
    └── src
        └── github.com
            └── gopherguides
                └── example
                    └── main.go
```

In your text editor, open `main.go` and add the following code to call the `greet` package:

main.go

```go
package main

import "github.com/gopherguides/greet"

func main() {
	greet.Hello()
}
```


Because you’re importing a package, you need to call the function by referencing the package name in dot notation. *Dot notation* is the practice of putting a period `.` between the name of the package you are using and the resource within that package that you want to use. For instance, in your `greet` package, you have the `Hello` function as a resource. If you want to call that resource, you use the dot notation of `greet.Hello()`.

Now, you can open your terminal and run the program on the command line:

```bash
go run main.go
```


When you do, you’ll receive the following output:

```
OutputHello, World!
```

To see how you can use variables in a package, let’s add a variable definition in your `greet.go` file:

greet.go

```
package greet

import "fmt"

var Shark = "Sammy"

func Hello() {
	fmt.Println("Hello, World!")
}
```

Next, open your `main.go` file and add the following highlighted line to call the variable from `greet.go` in a `fmt.Println()` function:

main.go

```
package main

import (
	"fmt"

	"github.com/gopherguides/greet"
)

func main() {
	greet.Hello()

	fmt.Println(greet.Shark)
}
```

Once you run the program again:

```bash
go run main.go
```


You’ll receive the following output:

```
OutputHello, World!
Sammy
```

Finally, let’s also define a type in the `greet.go` file. You’ll create the type `Octopus` with `name` and `color` fields, and a function that will print out the fields when called:

greet.go

```
package greet

import "fmt"

var Shark = "Sammy"

type Octopus struct {
	Name  string
	Color string
}

func (o Octopus) String() string {
	return fmt.Sprintf("The octopus's name is %q and is the color %s.", o.Name, o.Color)
}

func Hello() {
	fmt.Println("Hello, World!")
}
```

Open `main.go` to create an instance of that type at the end of the file:

main.go

```
package main

import (
	"fmt"

	"github.com/gopherguides/greet"
)

func main() {
	greet.Hello()

	fmt.Println(greet.Shark)

	oct := greet.Octopus{
		Name:  "Jesse",
		Color: "orange",
	}

	fmt.Println(oct.String())
}
```

Once you’ve created an instance of `Octopus` type with `oct := greet.Octopus`, you can access the functions and fields of the type within the `main.go` file’s namespace. This lets you write `oct.String()` on the last line without invoking `greet`. You could also, for example, call one of the types fields such as `oct.Color` without referencing the name of the `greet` package.

The `String` method on the `Octopus` type uses the `fmt.Sprintf` function to create a sentence, and `returns` the result, a string, to the caller (in this case, your main program).

When you run the program, you’ll receive the following output:

```bash
go run main.go
```


```
OutputHello, World!
Sammy
The octopus's name is "Jesse" and is the color orange.
```

By creating the `String` method on `Octopus`, you now have a reusable way to print out information about your custom type. If you want to change the behavior of this method in the future, you only have to edit this one method.

## Exported Code

You may have noticed that all of the declarations in the `greet.go` file you called were capitalized. Go does not have the concept of `public`, `private`, or `protected` modifiers like other languages do. External visibility is controlled by capitalization. Types, variables, functions, and so on, that start with a capital letter are available, publicly, outside the current package. A symbol that is visible outside its package is considered to be `exported`.

If you add a new method to `Octopus` called `reset`, you can call it from within the `greet` package, but not from your `main.go` file, which is outside the `greet` package:

greet.go

```
package greet

import "fmt"

var Shark = "Sammy"

type Octopus struct {
	Name  string
	Color string
}

func (o Octopus) String() string {
	return fmt.Sprintf("The octopus's name is %q and is the color %s.", o.Name, o.Color)
}

func (o *Octopus) reset() {
	o.Name = ""
	o.Color = ""
}

func Hello() {
	fmt.Println("Hello, World!")
}
```

If you try to call `reset` from the `main.go` file:

main.go

```
package main

import (
	"fmt"

	"github.com/gopherguides/greet"
)

func main() {
	greet.Hello()

	fmt.Println(greet.Shark)

	oct := greet.Octopus{
		Name:  "Jesse",
		Color: "orange",
	}

	fmt.Println(oct.String())

	oct.reset()
}
```

You’ll receive the following compilation error:

```
Outputoct.reset undefined (cannot refer to unexported field or method greet.Octopus.reset)
```

To `export` the `reset` functionality from `Octopus`, capitalize the `R` in `reset`:

greet.go

```
package greet

import "fmt"

var Shark = "Sammy"

type Octopus struct {
	Name  string
	Color string
}

func (o Octopus) String() string {
	return fmt.Sprintf("The octopus's name is %q and is the color %s.", o.Name, o.Color)
}

func (o *Octopus) Reset() {
	o.Name = ""
	o.Color = ""
}

func Hello() {
	fmt.Println("Hello, World!")
}
```

As a result you can call `Reset` from your other package without getting an error:

main.go

```
package main

import (
	"fmt"

	"github.com/gopherguides/greet"
)

func main() {
	greet.Hello()

	fmt.Println(greet.Shark)

	oct := greet.Octopus{
		Name:  "Jesse",
		Color: "orange",
	}

	fmt.Println(oct.String())

	oct.Reset()

	fmt.Println(oct.String())
}
```

Now if you run the program:

```bash
go run main.go
```


You will receive the following output:

```
OutputHello, World!
Sammy
The octopus's name is "Jesse" and is the color orange
The octopus's name is "" and is the color .
```

By calling `Reset`, you cleared out all the information in the `Name` and `Color` fields. When you call the `String` method, it will print nothing where `Name` and `Color` normally appear because the fields are now empty.

## Conclusion

Writing a Go package is the same as writing any other Go file, but placing it in another directory allows you to isolate the code to be reused elsewhere. This tutorial covered how to write definitions within a package, demonstrated how to make use of those definitions within another Go programming file, and explained the options for where to keep the package in order to access it.