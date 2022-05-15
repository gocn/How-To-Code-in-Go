### Introduction

There will be times when your code needs additional functionality outside of your current program. In these cases, you can use packages to make your program more sophisticated. A package represents all the files in a single directory on disk. Packages can define functions, types, and interfaces that you can reference in other Go files or packages.

This tutorial will walk you through installing, importing, and aliasing packages.

## Standard Library Packages

The standard library that ships with Go is a set of packages. These packages contain many of the fundamental building blocks to write modern software. For instance, the [`fmt`](https://golang.org/pkg/fmt) package contains basic functions for formatting and printing strings. The [`net/http`](https://golang.org/pkg/net/http/) package contains functions that allow a developer to create web services, send and retrieve data over the `http` protocol, and more.

To make use of the functions in a package, you need to access the package with an `import` statement. An `import` statement is made up of the `import` keyword along with the name of the package.

As an example, in the Go program file `random.go` you can import the `math/rand` package to generate random numbers in this manner:

random.go

``` go
import "math/rand"
```

When we import a package, we are making it available in our current program as a separate namespace. This means that we will have to refer to the function in _dot notation_, as in `package.function`.

In practice, a function from the `math/rand` package could look like these examples:

- `rand.Int()` which calls the function to return a random integer.
- `rand.Intn()` which calls the function to return a random element from `0` up to the specified number provided.

Let’s create a `for` loop to show how we will call a function of the `math/rand` package within our `random.go` program:

random.go

``` go
package main

import "math/rand"

func main() {
  for i := 0; i < 10; i++ {
    println(rand.Intn(25))
  }
}
```

This program first imports the `math/rand` package on the third line, then moves into a `for` loop which that will run 10 times. Within the loop, the program will print a random integer within the range of `0` up to `25`. The integer `25` is passed to `rand.Intn()` as its parameter.

When we run the program with `go run random.go`, we’ll receive 10 random integers as output. Because these are random, you’ll likely get different integers each time you run the program. The output will look something like this:

``` shell
# Output
6
12
22
9
6
18
0
15
6
0
```

The integers will never go below 0 or above 24.

When importing more than one package, you can use the `()` to create a block. By using a block you can avoid repeating the `import` keyword on every line. This will make your code look cleaner:

random.go

``` go
import (
  "fmt"
  "math/rand"
)

```

To make use of the additional package, we can now format the output and print out the iteration that each random number was generated on during the loop:

random.go

``` go
package main

import (
  "fmt"
  "math/rand"
)

func main() {
  for i := 0; i < 10; i++ {
    fmt.Printf("%d) %d\n", i, rand.Intn(25))
  }
}

```

Now, when we run our program, we’ll receive output that looks like this:

``` shell
# Output
0) 6
1) 12
2) 22
3) 9
4) 6
5) 18
6) 0
7) 15
8) 6
9) 0
```

In this section, we learned how to import packages and use them to write a more sophisticated program. So far, we have only used packages from the standard library. Next, let’s see how to install and use packages that are written by other developers.

## Installing Packages

While the standard library ships with many great and useful packages, they are intentionally designed to be _general purpose_ and not specific in nature. This allows developers to build their own packages on top of the standard library for their own specific needs.

The Go tool chain ships with the `go get` command. This command allows you to install third party packages to your local development environment and use them in your program.

When using `go get` to install third party packages, it is common for a package to be referenced by its canonical path. That path can also be a path to a public project that is hosted in a code repository such as GitHub. As such, if you want to import the [`flect`](https://github.com/gobuffalo/flect) package, you would use the full canonical path:

``` shell
go get github.com/gobuffalo/flect
```

The `go get` tool will find the package, on GitHub in this case, and install it into your [`$GOPATH`](https://www.digitalocean.com/community/tutorials/understanding-the-gopath).

For this example the code would be installed in this directory:

``` shell
$GOPATH/src/github.com/gobuffalo/flect
```

Packages are often being updated by the original authors to address bugs or add new features. When this happens, you may want to use the latest version of that package to take advantage of the new features or resolved bug. To update a package, you can use the `-u` flag with the `go get` command:

``` shell
go get -u github.com/gobuffalo/flect
```

This command will also have Go install the package if it is not found locally. If it is already installed, Go will attempt to update the package to the latest version.

The `go get` command always retrieves the latest version of the package available. However, there may be updates to previous versions of the package that are still newer than you are using, and would be useful to update in your program. To retrieve that specific version of the package, you would need to use a **Package Management** tool, such as [Go Modules](https://github.com/golang/go/wiki/Modules).

As of Go 1.11, Go Modules are used to manage what version of the package you want imported. The topic of package management is beyond the scope of this article, but you can read more about it [on the Go Modules GitHub page](https://github.com/golang/go/wiki/Modules).

## Aliasing Imported Packages

You may want to change a package name if you have a local package already named the same as a third party package you are using. When this happens, aliasing your import is the best way to handle the collision. You can modify the names of packages and their functions within Go by putting an `alias` name in front of the imported package.

The construction of this statement looks like this:

``` go
import another_name "package"
```

In this example, modify the name of the `fmt` package in the `random.go` program file. We’ll change the package name of `fmt` to `f` in order to abbreviate it. Our modified program will look like this:

random.go

``` go
package main

import (
 f "fmt"
  "math/rand"
)

func main() {
  for i := 0; i < 10; i++ {
    f.Printf("%d) %d\n", i, rand.Intn(25))
  }
}
```

Within the program, we now refer to the `Printf` function as `f.Printf` rather than `fmt.Printf`.

While other languages favor aliasing a package for ease of use later in the program, Go does not. For instance, aliasing the `fmt` package to `f` would not be consistent with the [style guide](https://github.com/golang/go/wiki/CodeReviewComments#import-dot).

When renaming imports to avoid a name collision, you should aim to rename the most local or project specific import. For instance, if you had a _local_ package called `strings`, and you also needed to import the _system_ package called `strings`, you would favor renaming your local package over the system package. Whenever possible, it’s best to avoid name collision altogether.

In this section, we learned how we can alias an import to avoid colliding with another import in our program. It is important to remember that readability and clarity of your program is important, so you should only use aliasing to make the code more readable or when you need to avoid a naming collision.

## Formatting Imports

By formatting imports, you can sort the packages into a specific order that will make your code more consistent. Additionally, this will prevent random commits from taking place when the only thing that changes is the sort order of the imports. Since formatting imports will prevent random commits, this will prevent unnecessary code churn and confusing code reviews.

Most editors will format imports for you automatically, or will let you configure your editor to use [`goimports`](https://godoc.org/golang.org/x/tools/cmd/goimports). It is considered standard practice to use `goimports` in your editor, as trying to manually maintain the sort order of your imports can be tedious and prone to errors. Additionally, if any style changes are made, `goimports` will be updated to reflect those style changes. This ensures that you, and anyone that works on your code, will have consistent styling in your import blocks.

Here is what an example import block may look like before formatting:

``` go
import (
  "fmt"
  "os"
  "github.com/digital/ocean/godo"
  "github.com/sammy/foo"
  "math/rand"
  "github.com/sammy/bar"
)

```

Running the `goimport` tool (or with most editors that have it installed, saving the file will run it for you), you will now have the following format:

``` go
import (
  "fmt"
  "math/rand"
  "os"

  "github.com/sammy/foo"
  "github.com/sammy/bar"

  "github.com/digital/ocean/godo"
)
```

Notice that it groups all standard library packages first and then groups third party packages together with blank lines. This makes is easier to read and understand what packages are being used.

In this section we learned that using `goimports` will keep all of our import blocks properly formatted, and prevent unnecessary code churn between developers working on the same files.

## Conclusion

When we import packages we’re able to call functions that are not built in to Go. Some packages are part of the standard library that installs with Go, and some we will install through `go get`.

Making use of packages allows us to make our programs more robust and powerful as we’re leveraging existing code. We can also [create our own packages](https://www.digitalocean.com/community/tutorials/how-to-write-packages-in-go) for ourselves and for other programmers to use in future programs.
