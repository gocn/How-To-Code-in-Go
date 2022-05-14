

# Using ldflags to Set Version Information for Go Applications

### Introduction

When deploying applications into a production environment, building binaries with version information and other metadata will improve your monitoring, logging, and debugging processes by adding identifying information to help track your builds over time. This version information can often include highly dynamic data, such as build time, the machine or user building the binary, the [Version Control System (VCS)](https://www.atlassian.com/git/tutorials/what-is-version-control) commit ID it was built against, and more. Because these values are constantly changing, coding this data directly into the source code and modifying it before every new build is tedious and prone to error: Source files can move around and [variables/constants](https://www.digitalocean.com/community/tutorials/how-to-use-variables-and-constants-in-go) may switch files throughout development, breaking the build process.

One way to solve this in Go is to use `-ldflags` with the `go build` command to insert dynamic information into the binary at build time, without the need for source code modification. In this flag, `ld` stands for [*linker*](https://en.wikipedia.org/wiki/Linker_(computing)), the program that links together the different pieces of the compiled source code into the final binary. `ldflags`, then, stands for *linker flags*. It is called this because it passes a flag to the underlying Go toolchain linker, [`cmd/link`](https://golang.org/cmd/link), that allows you to change the values of imported packages at build time from the command line.

In this tutorial, you will use `-ldflags` to change the value of variables at build time and introduce your own dynamic information into a binary, using a sample application that prints version information to the screen.

## Prerequisites

To follow the example in this article, you will need:

- A Go workspace set up by following [How To Install Go and Set Up a Local Programming Environment](https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-go).

## Building Your Sample Application

Before you can use `ldflags` to introduce dynamic data, you first need an application to insert the information into. In this step, you will make this application, which will at this stage only print static versioning information. Let’s create that application now.

In your `src` directory, make a directory named after your application. This tutorial will use the application name `app`:

```bash
mkdir app
```

Change your working directory to this folder:

```bash
cd app
```

Next, using the text editor of your choice, create the entry point of your program, `main.go`:

```bash
nano main.go
```

Now, make your application print out version information by adding the following contents:

app/main.go

```go
package main

import (
	"fmt"
)

var Version = "development"

func main() {
	fmt.Println("Version:\t", Version)
}
```

Inside of the `main()` function, you declared the `Version` variable, then printed the [string](https://www.digitalocean.com/community/tutorials/an-introduction-to-working-with-strings-in-go) `Version:`, followed by a tab character, `\t`, and then the declared variable.

At this point, the variable `Version` is defined as `development`, which will be the default version for this app. Later on, you will change this value to be an official version number, arranged according to [semantic versioning format](https://semver.org/).

Save and exit the file. Once this is done, build and run the application to confirm that it prints the correct version:

```bash
go build
./app
```

You will see the following output:

```bash
OutputVersion:	 development
```

You now have an application that prints default version information, but you do not yet have a way to pass in current version information at build time. In the next step, you will use `-ldflags` and `go build` to solve this problem.

## Using `ldflags` with `go build`

As mentioned before, `ldflags` stands for *linker flags*, and is used to pass in flags to the underlying linker in the Go toolchain. This works according to the following syntax:

```bash
go build -ldflags="-flag"
```

In this example, we passed in `flag` to the underlying `go tool link` command that runs as a part of `go build`. This command uses double quotes around the contents passed to `ldflags` to avoid breaking characters in it, or characters that the command line might interpret as something other than what we want. From here, you could pass in [many different `link` flags](https://golang.org/cmd/link/). For the purposes of this tutorial, we will use the `-X` flag to write information into the variable at link time, followed by the [package](https://www.digitalocean.com/community/tutorials/importing-packages-in-go) path to the variable and its new value:

```bash
go build -ldflags="-X 'package_path.variable_name=new_value'"
```

Inside the quotes, there is now the `-X` option and a [key-value pair](https://www.digitalocean.com/community/tutorials/understanding-maps-in-go#keys-and-values) that represents the variable to be changed and its new value. The `.` character separates the package path and the variable name, and single quotes are used to avoid breaking characters in the key-value pair.

To replace the `Version` variable in your example application, use the syntax in the last command block to pass in a new value and build the new binary:

```bash
go build -ldflags="-X 'main.Version=v1.0.0'"
```

In this command, `main` is the package path of the `Version` variable, since this variable is in the `main.go` file. `Version` is the variable that you are writing to, and `v1.0.0` is the new value.

In order to use `ldflags`, the value you want to change must exist and be a package level variable of type `string`. This variable can be either exported or unexported. The value cannot be a `const` or have its value set by the result of a function call. Fortunately, `Version` fits all of these requirements: It was already declared as a variable in the `main.go` file, and the current value (`development`) and the desired value (`v1.0.0`) are both strings.

Once your new `app` binary is built, run the application:

```bash
./app
```

You will receive the following output:

```bash
OutputVersion:	 v1.0.0
```

Using `-ldflags`, you have succesfully changed the `Version` variable from `development` to `v1.0.0`.

You have now modified a `string` variable inside of a simple application at build time. Using `ldflags`, you can embed version details, licensing information, and more into a binary ready for distribution, using only the command line.

In this example, the variable you changed was in the `main` program, reducing the difficulty of determining the path name. But sometimes the path to these variables is more complicated to find. In the next step, you will write values to variables in sub-packages to demonstrate the best way to determine more complex package paths.

## Targeting Sub-Package Variables

In the last section, you manipulated the `Version` variable, which was at the top-level package of the application. But this is not always the case. Often it is more practical to place these variables in another package, since `main` is not an importable package. To simulate this in your example application, you will create a new sub-package, `app/build`, that will store information about the time the binary was built and the name of the user that issued the build command.

To add a new sub-package, first add a new directory to your project named `build`:

```bash
mkdir -p build
```

Then create a new file named `build.go` to hold the new variables:

```bash
nano build/build.go
```

In your text editor, add new variables for `Time` and `User`:

app/build/build.go

```go
package build

var Time string

var User string
```

The `Time` variable will hold a string representation of the time when the binary was built. The `User` variable will hold the name of the user who built the binary. Since these two variables will always have values, you don’t need to initialize these variables with default values like you did for `Version`.

Save and exit the file.

Next, open `main.go` to add these variables to your application:

```bash
nano main.go
```

Inside of `main.go`, add the following highlighted lines:

main.go

```
package main

import (
	"app/build"
	"fmt"
)

var Version = "development"

func main() {
	fmt.Println("Version:\t", Version)
	fmt.Println("build.Time:\t", build.Time)
	fmt.Println("build.User:\t", build.User)
}
```

In these lines, you first imported the `app/build` package, then printed `build.Time` and `build.User` in the same way you printed `Version`.

Save the file, then exit from your text editor.

Next, to target these variables with `ldflags`, you could use the import path `app/build` followed by `.User` or `.Time`, since you already know the import path. However, to simulate a more complex situation in which the path to the variable is not evident, let’s instead use the `nm` command in the Go tool chain.

The `go tool nm` command will output the *symbols* involved in a given executable, object file, or archive. In this case, a symbol refers to an object in the code, such as a defined or imported variable or function. By generating a symbol table with `nm` and using `grep` to search for a variable, you can quickly find information about its path.

**Note:** The `nm` command will not help you find the path of your variable if the package name has any non-[ASCII](https://en.wikipedia.org/wiki/ASCII) characters, or a `"` or `%` character, as that is a limitation of the tool itself.

To use this command, first build the binary for `app`:

```bash
go build
```

Now that `app` is built, point the `nm` tool at it and search through the output:

```bash
go tool nm ./app | grep app
```

When run, the `nm` tool will output a lot of data. Because of this, the preceding command used `|` to pipe the output to the `grep` command, which then searched for terms that had the top-level `app` in the title.

You will receive output similar to this:

```
Output  55d2c0 D app/build.Time
  55d2d0 D app/build.User
  4069a0 T runtime.appendIntStr
  462580 T strconv.appendEscapedRune
. . .
```

In this case, the first two lines of the result set contain the paths to the two variables you are looking for: `app/build.Time` and `app/build.User`.

Now that you know the paths, build the application again, this time changing `Version`, `User`, and `Time` at build time. To do this, pass multiple `-X` flags to `-ldflags`:

```bash
go build -v -ldflags="-X 'main.Version=v1.0.0' -X 'app/build.User=$(id -u -n)' -X 'app/build.Time=$(date)'"
```

Here you passed in the `id -u -n` Bash command to list the current user, and the `date` command to list the current date.

Once the executable is built, run the program:

```bash
./app
```

This command, when run on a Unix system, will generate similar output to the following:

```
OutputVersion:	 v1.0.0
build.Time:	 Fri Oct  4 19:49:19 UTC 2019
build.User:	 sammy
```

Now you have a binary that contains versioning and build information that can provide vital assistance in production when resolving issues.

## Conclusion

This tutorial showed how, when applied correctly, `ldflags` can be a powerful tool for injecting valuable information into binaries at build time. This way, you can control feature flags, environment information, versioning information, and more without introducing changes to your source code. By adding `ldflags` to your current build workflow you can maximize the benefits of Go’s self-contained binary distribution format.