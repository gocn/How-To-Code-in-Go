### Introduction

This article will walk you through understanding what the `GOPATH` is, how it works, and how to set it up. This is a crucial step for setting up a Go development environment, as well as understanding how Go finds, installs, and builds source files. In this article we will use `GOPATH` when referring to the concept of the folder structure we will be discussing. We will use `$GOPATH` to refer to the environment variable that Go uses to find the folder structure.

A [Go Workspace](https://golang.org/doc/code.html#Workspaces) is how Go manages our source files, compiled binaries, and cached objects used for faster compilation later. It is typical, and also advised, to have only one Go Workspace, though it is possible to have multiple spaces. The `GOPATH` acts as the root folder of a workspace.

## Setting the `$GOPATH` Environment Variable

The `$GOPATH` environment variable lists places for Go to look for Go Workspaces.

By default, Go assumes our `GOPATH` location is at `$HOME/go`, where `$HOME` is the root directory of our user account on our computer. We can change this by setting the `$GOPATH` environment variable. For further study, follow this tutorial on [reading and setting environment variables in Linux](https://www.digitalocean.com/community/tutorials/how-to-read-and-set-environmental-and-shell-variables-on-a-linux-vps).

For more information on setting the `$GOPATH` variable, see the Go [documentation](https://golang.org/doc/code.html#Workspaces).

Furthermore, this [series](https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-go) walks through installing Go and setting up a Go development environment.

## `$GOPATH` Is Not `$GOROOT`

The `$GOROOT` is where Go’s code, compiler, and tooling lives — this is **not** our source code. The `$GOROOT` is usually something like `/usr/local/go`. Our `$GOPATH` is usually something like `$HOME/go`.

While we don’t need to specifically set up the `$GOROOT` variable anymore, it is still referenced in older materials.

Now, let’s discuss the structure of the Go Workspace.

## Anatomy of the Go Workspace

Inside of a Go Workspace, or `GOPATH`, there are three directories: `bin`, `pkg`, and `src`. Each of these directories has special meaning to the Go tool chain.

Let’s take a look at each of these directories.

The `$GOPATH/bin` directory is where Go places binaries that `go install` compiles. Our operating system uses the `$PATH` environment variable to find binary applications that can execute without a full path. It is recommended to add this directory to our global `$PATH` variable.

For example, if we don’t add `$GOPATH/bin` to `$PATH` to execute a program from there, we would need to run:

When `$GOPATH/bin` is added to `$PATH` we can make the same call like such:

The `$GOPATH/pkg` directory is where Go stores pre-compiled object files to speed up subsequent compiling of programs. Typically, most developers won’t need to access this directory. If you experience issues with compilation, you can safely delete this directory and Go will then rebuild it.

The `src` directory is where all of our `.go` files, or source code, must be located. This shouldn’t be confused with the source code the Go tooling uses, which is located at the `$GOROOT`. As we write Go applications, packages, and libraries, we will place these files under `$GOPATH/src/path/to/code`.

## What Are Packages?

Go code is organized in packages. A package represents all the files in a single directory on disk. One directory can contain only certain files from the same package. Packages are stored, with all user-written Go source files, under the `$GOPATH/src` directory. We can understand package resolution by importing different packages.

If our code lives at `$GOPATH/src/blue/red` then its package name should be `red`.

The import statement for the `red` package would be:

Packages that live in source code repositories, like GitHub and BitBucket, have the full location of the repository as part of their import path.

For example, we would import the source code at [https://github.com/gobuffalo/buffalo](https://github.com/gobuffalo/buffalo) using the following import path:

Therefore, this source code would be in the following location on disk:

```
$GOPATH/src/github.com/gobuffalo/buffalo
```

## Conclusion

In this article we discussed the `GOPATH` as a set of folder’s that Go expects our source code to live within, as well as what those folders are, and what they contain. We discussed how to change that location from the default of `$HOME/go` to the user’s choice by setting the `$GOPATH` environment variable. Finally, we discussed how Go searches for packages within that folder structure.

Introduced in Go 1.11, [Go Modules](https://github.com/golang/go/wiki/Modules) aim to replace Go Workspaces and the `GOPATH`. While it is recommended to start using modules, some environments, such as corporate environments, may not be ready to use modules.

The `GOPATH` is one of the trickier aspects of setting up Go, but once it is set up, we can usually forget about it.