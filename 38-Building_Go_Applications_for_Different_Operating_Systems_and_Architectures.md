



# Building Go Applications for Different Operating Systems and Architectures

In software development, it is important to consider the [operating system](https://en.wikipedia.org/wiki/Operating_system) and underlying processor [architecture](https://en.wikipedia.org/wiki/Microarchitecture) that you would like to compile your binary for. Since it is often slow or impossible to run a binary on a different OS/architecture platform, it is a common practice to build your final binary for many different platforms to maximize your program’s audience. However, this can be difficult when the platform you are using for development is different from the platform you want to deploy your program to. In the past, for example, developing a program on Windows and deploying it to a Linux or a macOS machine would involve setting up build machines for each of the environments you wanted binaries for. You’d also need to keep your tooling in sync, in addition to other considerations that would add cost and make collaborative testing and distribution more difficult.

Go solves this problem by building support for multiple platforms directly into the `go build` tool, as well as the rest of the Go toolchain. By using [environment variables](https://www.digitalocean.com/community/tutorials/how-to-read-and-set-environmental-and-shell-variables-on-a-linux-vps) and [build tags](https://www.digitalocean.com/community/tutorials/customizing-go-binaries-with-build-tags), you can control which OS and architecture your final binary is built for, in addition to putting together a workflow that can quickly toggle the inclusion of platform-dependent code without changing your codebase.

In this tutorial, you will put together a sample application that joins [strings](https://www.digitalocean.com/community/tutorials/an-introduction-to-working-with-strings-in-go) together into a filepath, create and selectively include platform-dependent snippets, and build binaries for multiple operating systems and system architectures on your own system, showing you how to use this powerful capability of the Go programming language.

## Prerequisites

To follow the example in this article, you will need:

- A Go workspace set up by following [How To Install Go and Set Up a Local Programming Environment](https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-go).

## Possible Platforms for `GOOS` and `GOARCH`

Before showing how to control the build process to build binaries for different platforms, let’s first inspect what kinds of platforms Go is capable of building for, and how Go references these platforms using the environment variables `GOOS` and `GOARCH`.

The Go tooling has a command that can print a list of the possible platforms that Go can build on. This list can change with each new Go release, so the combinations discussed here might not be the same on another version of Go. At the time of writing this tutorial, the current Go release is [`1.13`](https://golang.org/doc/go1.13).

To find this list of possible platforms, run the following:

```bash
go tool dist list
```

You will receive an output similar to the following:

```
Outputaix/ppc64        freebsd/amd64   linux/mipsle   openbsd/386
android/386      freebsd/arm     linux/ppc64    openbsd/amd64
android/amd64    illumos/amd64   linux/ppc64le  openbsd/arm
android/arm      js/wasm         linux/s390x    openbsd/arm64
android/arm64    linux/386       nacl/386       plan9/386
darwin/386       linux/amd64     nacl/amd64p32  plan9/amd64
darwin/amd64     linux/arm       nacl/arm       plan9/arm
darwin/arm       linux/arm64     netbsd/386     solaris/amd64
darwin/arm64     linux/mips      netbsd/amd64   windows/386
dragonfly/amd64  linux/mips64    netbsd/arm     windows/amd64
freebsd/386      linux/mips64le  netbsd/arm64   windows/arm
```

This output is a set of key-value pairs separated by a `/`. The first part of the combination, before the `/`, is the operating system. In Go, these operating systems are possible values for the environment variable `GOOS`, pronounced “goose”, which stands for **Go Operating System**. The second part, after the `/`, is the architecture. As before, these are all possible values for an environment variable: `GOARCH`. This is pronounced “gore-ch”, and stands for **Go Architecture**.

Let’s break down one of these combinations to understand what it means and how it works, using `linux/386` as an example. The key-value pair starts with the `GOOS`, which in this example would be `linux`, referring to the [Linux OS](https://www.digitalocean.com/community/tutorial_series/getting-started-with-linux). The `GOARCH` here would be `386`, which stands for the [Intel 80386 microprocessor](https://en.wikipedia.org/wiki/Intel_80386).

There are many platforms available with the `go build` command, but a majority of the time you’ll end up using `linux` , `windows`, or `darwin` as a value for `GOOS`. These cover the big three OS platforms: [Linux](https://en.wikipedia.org/wiki/Linux), [Windows](https://en.wikipedia.org/wiki/Microsoft_Windows), and [macOS](https://en.wikipedia.org/wiki/MacOS), which is based on the [Darwin operating system](https://en.wikipedia.org/wiki/Darwin_(operating_system)) and is thus called `darwin`. However, Go can also cover less mainstream platforms like `nacl`, which represents [Google’s Native Client](https://developer.chrome.com/native-client).

When you run a command like `go build`, Go uses the current platform’s `GOOS` and `GOARCH` to determine how to build the binary. To find out what combination your platform is, you can use the `go env` command and pass `GOOS` and `GOARCH` as arguments:

```bash
go env GOOS GOARCH
```

In testing this example, we ran this command on macOS on a machine with an [AMD64 architecture](https://en.wikipedia.org/wiki/X86-64), so we will receive the following output:

```
Outputdarwin
amd64
```

Here the output of the command tells us that our system has `GOOS=darwin` and `GOARCH=amd64`.

You now know what the `GOOS` and `GOARCH` are in Go, as well as their possible values. Next, you will put together a program to use as an example of how to use these environment variables and build tags to build binaries for other platforms.

## Write a Platform-Dependent Program with `filepath.Join()`

Before you start building binaries for other platforms, let’s build an example program. A good sample for this purpose is the `Join` function in the [`path/filepath`](https://godoc.org/path/filepath) package in the Go standard library. This function takes a number of strings and returns one string that is joined together with the correct filepath separator.

This is a good example program because the operation of the program depends on which OS it is running on. On Windows, the path separator is a backslash, `\`, while Unix-based systems use a forward slash, `/`.

Let’s start with building an application that uses `filepath.Join()`, and later, you’ll write your own implementation of the `Join()` function that customizes the code to the platform-specific binaries.

First, create a folder in your `src` directory with the name of your app:

```bash
mkdir app
```

Move into that directory:

```bash
cd app
```

Next, create a new file in your text editor of choice named `main.go`. For this tutorial, we will use Nano:

```bash
nano main.go
```

Once the file is open, add the following code:

src/app/main.go

```go
package main

import (
  "fmt"
  "path/filepath"
)

func main() {
  s := filepath.Join("a", "b", "c")
  fmt.Println(s)
}
```

The `main()` function in this file uses `filepath.Join()` to concatenate three [strings](https://www.digitalocean.com/community/tutorials/an-introduction-to-working-with-strings-in-go) together with the correct, platform-dependent path separator.

Save and exit the file, then run the program:

```bash
go run main.go
```

When running this program, you will receive different output depending on which platform you are using. On Windows, you will see the strings separated by `\`:

```
Outputa\b\c
```

On Unix systems like macOS and Linux, you will receive the following:

```
Outputa/b/c
```

This shows that, because of the different filesystem protocols used on these operating systems, the program will have to build different code for the different platforms. But since it already uses a different file separator depending on the OS, we know that `filepath.Join()` already accounts for the difference in platform. This is because the Go tool chain automatically detects your machine’s `GOOS` and `GOARCH` and uses this information to use the code snippet with the right [build tags](https://www.digitalocean.com/community/tutorials/customizing-go-binaries-with-build-tags) and file separator.

Let’s consider where the `filepath.Join()` function gets its separator from. Run the following command to inspect the relevant snippet from Go’s standard library:

```bash
less /usr/local/go/src/os/path_unix.go
```

This will display the contents of `path_unix.go`. Look for the following part of the file:

/usr/local/go/os/path_unix.go

```go
. . .
// +build aix darwin dragonfly freebsd js,wasm linux nacl netbsd openbsd solaris

package os

const (
  PathSeparator     = '/' // OS-specific path separator
  PathListSeparator = ':' // OS-specific path list separator
)
. . .
```

This section defines the `PathSeparator` for all of the varieties of Unix-like systems that Go supports. Notice all of the build tags at the top, which are each one of the possible Unix `GOOS` platforms associated with Unix. When the `GOOS` matches these terms, your program will yield the Unix-styled filepath separator.

Press `q` to return to the command line.

Next, open the file that defines the behavior of `filepath.Join()` when used on Windows:

```bash
less /usr/local/go/src/os/path_windows.go
```

You will see the following:

/usr/local/go/src/os/path_windows.go

```go
. . .
package os

const (
        PathSeparator     = '\\' // OS-specific path separator
        PathListSeparator = ';'  // OS-specific path list separator
)
. . .
```

Although the value of `PathSeparator` is `\\` here, the code will render the single backslash (`\`) needed for Windows filepaths, since the first backslash is only needed as an escape character.

Notice that, unlike the Unix file, there are no build tags at the top. This is because `GOOS` and `GOARCH` can also be passed to `go build` by adding an underscore (`_`) and the environment variable value as a suffix to the filename, something we will go into more in the section [**Using GOOS and GOARCH File Name Suffixes**](https://www.digitalocean.com/community/tutorials/building-go-applications-for-different-operating-systems-and-architectures#using-goos-and-goarch-filename-suffixes). Here, the `_windows` part of `path_windows.go` makes the file act as if it had the build tag `// +build windows` at the top of the file. Because of this, when your program is run on Windows, it will use the constants of `PathSeparator` and `PathListSeparator` from the `path_windows.go` code snippet.

To return to the command line, quit `less` by pressing `q`.

In this step, you built a program that showed how Go converts the `GOOS` and `GOARCH` automatically into build tags. With this in mind, you can now update your program and write your own implementation of `filepath.Join()`, using build tags to manually set the correct `PathSeparator` for Windows and Unix platforms.

## Implementing a Platform-Specific Function

Now that you know how Go’s standard library implements platform-specific code, you can use build tags to do this in your own `app` program. To do this, you will write your own implementation of `filepath.Join()`.

Open up your `main.go` file:

```bash
nano main.go
```

Replace the contents of `main.go` with the following, using your own function called `Join()`:

src/app/main.go

```go
package main

import (
  "fmt"
  "strings"
)

func Join(parts ...string) string {
  return strings.Join(parts, PathSeparator)
}

func main() {
  s := Join("a", "b", "c")
  fmt.Println(s)
}
```

The `Join` function takes a number of `parts` and joins them together using the [`strings.Join()`](https://godoc.org/strings.Join) method from the [`strings` package](https://www.digitalocean.com/community/tutorials/an-introduction-to-the-strings-package-in-go) to concatenate the `parts` together using the `PathSeparator`.

You haven’t defined the `PathSeparator` yet, so do that now in another file. Save and quit `main.go`, open your favorite editor, and create a new file named `path.go`:

```
nano path.go
```

Define the `PathSeparator` and set it equal to the Unix filepath separator, `/`:

src/app/path.go

```go
package main

const PathSeparator = "/"
```

Compile and run the application:

```bash
go build
./app
```

You’ll receive the following output:

```
Outputa/b/c
```

This runs successfully to get a Unix-style filepath. But this isn’t yet what we want: the output is always `a/b/c`, regardless of what platform it runs on. To add in the functionality to create Windows-style filepaths, you will need to add a Windows version of the `PathSeparator` and tell the `go build` command which version to use. In the next section, you will use [build tags](https://www.digitalocean.com/community/tutorials/customizing-go-binaries-with-build-tags) to accomplish this.

## Using `GOOS` or `GOARCH` Build Tags

To account for Windows platforms, you will now create an alternate file to `path.go` and use build tags to make sure the code snippets only run when `GOOS` and `GOARCH` are the appropriate platform.

But first, add a build tag to `path.go` to tell it to build for everything except for Windows. Open up the file:

```bash
nano path.go
```

Add the following highlighted build tag to the file:

src/app/path.go

```
// +build !windows

package main

const PathSeparator = "/"
```

Go build tags allow for inverting, meaning that you can instruct Go to build this file for any platform except for Windows. To invert a build tag, place a `!` before the tag.

Save and exit the file.

Now, if you were to run this program on Windows, you would get the following error:

```
Output./main.go:9:29: undefined: PathSeparator
```

In this case, Go would not be able to include `path.go` to define the variable `PathSeparator`.

Now that you have ensured that `path.go` will not run when `GOOS` is Windows, add a new file, `windows.go`:

```bash
nano windows.go
```

In `windows.go`, define the Windows `PathSeparator`, as well as a build tag to let the `go build` command know it is the Windows implementation:

src/app/windows.go

```go
// +build windows

package main

const PathSeparator = "\\"
```

Save the file and exit from the text editor. The application can now compile one way for Windows and another for all other platforms.

While the binaries will now build correctly for their platforms, there are further changes you must make in order to compile for a platform that you do not have access to. To do this, you will alter your local `GOOS` and `GOARCH` environment variables in the next step.

## Using Your Local `GOOS` and `GOARCH` Environment Variables

Earlier, you ran the `go env GOOS GOARCH` command to find out what OS and architecture you were working on. When you ran the `go env` command, it looked for the two environment variables `GOOS` and `GOARCH`; if found, their values would be used, but if not found, then Go would set them with the information for the current platform. This means that you can change `GOOS` or `GOARCH` so that they do not default to your local OS and architecture.

The `go build` command behaves in a similar manner to the `go env` command. You can set either the `GOOS` or `GOARCH` environment variables to build for a different platform using `go build`.

If you are not using a Windows system, build a `windows` binary of `app` by setting the `GOOS` environment variable to `windows` when running the `go build` command:

```bash
GOOS=windows go build
```

Now list the files in your current directory:

```bash
ls
```

The output of listing the directory shows there is now an `app.exe` Windows executable in the project directory:

```
Outputapp  app.exe  main.go  path.go  windows.go
```

Using the `file` command, you can get more information about this file, confirming its build:

```bash
file app.exe
```

You will receive:

```
Outputapp.exe: PE32+ executable (console) x86-64 (stripped to external PDB), for MS Windows
```

You can also set one, or both environment variables at build time. Run the following:

```bash
GOOS=linux GOARCH=ppc64 go build
```

Your `app` executable will now be replaced by a file for a different architecture. Run the `file` command on this binary:

```bash
file app
```

You will receive output like the following:

```
app: ELF 64-bit MSB executable, 64-bit PowerPC or cisco 7500, version 1 (SYSV), statically linked, not stripped
```

By setting your local `GOOS` and `GOARCH` environment variables, you can now build binaries for any of Go’s compatible platforms without a complicated configuration or setup. Next, you will use filename conventions to keep your files neatly organized and build for specific platforms automatically wihout build tags.

## Using `GOOS` and `GOARCH` Filename Suffixes

As you saw earlier, the Go standard library makes heavy use of build tags to simplify code by separating out different platform implementations into different files. When you opened the `os/path_unix.go` file, there was a build tag that listed all of the possible combinations that are considered Unix-like platforms. The `os/path_windows.go` file, however, contained no build tags, because the suffix on the filename sufficed to tell Go which platform the file was meant for.

Let’s look at the syntax of this feature. When naming a `.go` file, you can add `GOOS` and `GOARCH` as suffixes to the file’s name in that order, separating the values by underscores (`_`). If you had a Go file named `filename.go`, you could specify the OS and architecture by changing the filename to `filename_GOOS_GOARCH.go`. For example, if you wished to compile it for Windows with 64-bit [ARM architecture](https://en.wikipedia.org/wiki/ARM_architecture), you would make the name of the file `filename_windows_arm64.go`. This naming convention helps keep code neatly organized.

Update your program to use the filename suffixes instead of build tags. First, rename the `path.go` and `windows.go` file to use the convention used in the `os` package:

```bash
mv path.go path_unix.go
mv windows.go path_windows.go
```

With the two filenames changed, you can remove the build tag you added to `path_windows.go`:

```bash
nano path_windows.go
```

Remove `// +build windows` so that your file looks like this:

path_windows.go

```go
package main

const PathSeparator = "\\"
```

Save and exit from the file.

Because `unix` is not a valid `GOOS`, the `_unix.go` suffix has no meaning to the Go compiler. It does, however, convey the intended purpose of the file. Like the `os/path_unix.go` file, your `path_unix.go` file still needs to use build tags, so keep that file unchanged.

By using filename conventions, you removed uneeded build tags from your source code and made the filesystem cleaner and clearer.

## Conclusion

The ability to generate binaries for multiple platforms that require no dependencies is a powerful feature of the Go toolchain. In this tutorial, you used this capability by adding build tags and filename suffixes to mark certain code snippets to only compile for certain architectures. You created your own platorm-dependent program, then manipulated the `GOOS` and `GOARCH` environment variables to generate binaries for platforms beyond your current platform. This is a valuable skill, because it is a common practice to have a continuous integration process that automatically runs through these environment variables to build binaries for all platforms.

For further study on `go build`, check out our [Customizing Go Binaries with Build Tags](https://www.digitalocean.com/community/tutorials/customizing-go-binaries-with-build-tags) tutorial. If you’d like to learn more about the Go programming language in general, check out the entire [How To Code in Go series](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go).