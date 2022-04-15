# How To Install Go and Set Up a Local Programming Environment on macOS

## Introduction

[Go](https://golang.org/) is a programming language that was born out of frustration at Google. Developers continually had to pick a language that executed efficiently but took a long time to compile, or to pick a language that was easy to program but ran inefficiently in production. Go was designed to have all three available at the same time: fast compilation, ease of programming, and efficient execution in production.

While Go is a versatile programming language that can be used for many different programming projects, it’s particularly well suited for networking/distributed systems programs, and has earned a reputation as “the language of the cloud.” It focuses on helping the modern programmer do more with a strong set of tooling, removing debates over formatting by making the format part of the language specification, as well as making deployment easy by compiling to a single binary. Go is easy to learn, with a very small set of keywords, which makes it a great choice for beginners and experienced developers alike.

This tutorial will guide you through installing Go on your local macOS machine and setting up a programming workspace via the command line.

## Prerequisites

You will need a macOS computer with administrative access that is connected to the internet.

## Step 1 — Opening Terminal

We’ll be completing most of our installation and setup on the command line, which is a non-graphical way to interact with your computer. That is, instead of clicking on buttons, you’ll be typing in text and receiving feedback from your computer through text as well. The command line, also known as a shell, can help you modify and automate many of the tasks you do on a computer every day, and is an essential tool for software developers.

The macOS Terminal is an application you can use to access the command line interface. Like any other application, you can find it by going into Finder, navigating to the Applications folder, and then into the Utilities folder. From here, double-click the Terminal like any other application to open it up. Alternatively, you can use Spotlight by holding down the `CMD` and `SPACE` keys to find Terminal by typing it out in the box that appears.

![macOS Terminal](https://assets.digitalocean.com/articles/eng_python/OSXSetUp/MacOSXSetUp.png)

There are many more Terminal commands to learn that can enable you to do more powerful things. The article “\[An Introduction to the Linux Terminal\] ([https://www.digitalocean.com/community/tutorials/an-introduction-to-the-linux-terminal](https://www.digitalocean.com/community/tutorials/an-introduction-to-the-linux-terminal))” can get you better oriented with the Linux Terminal, which is similar to the macOS Terminal.

Now that you have opened up Terminal, you can download and install [Xcode](https://developer.apple.com/xcode/), a package of developer tools that you will need in order to install Go.

## Step 2 — Installing Xcode

Xcode is an _integrated development environment_ (IDE) that is comprised of software development tools for macOS. You can check if Xcode is already installed by typing the following in the Terminal window:

```shell
xcode-select -p
```

The following output means that Xcode is installed:

```
Output
/Library/Developer/CommandLineTools
```

If you received an error, then in your web browser install \[Xcode from the App Store\] ([https://itunes.apple.com/us/app/xcode/id497799835?mt=12&ign-mpt=uo%3D2](https://itunes.apple.com/us/app/xcode/id497799835?mt=12&ign-mpt=uo%3D2)) and accept the default options.

Once Xcode is installed, return to your Terminal window. Next, you’ll need to install Xcode’s separate Command Line Tools app, which you can do by typing:

```shell
xcode-select --install
```

At this point, Xcode and its Command Line Tools app are fully installed, and we are ready to install the package manager Homebrew.

## Step 3 — Installing and Setting Up Homebrew

While the macOS Terminal has a lot of the functionality of Linux Terminals and other Unix systems, it does not ship with a package manager that accommodates best practices. A **package manager** is a collection of software tools that work to automate installation processes that include initial software installation, upgrading and configuring of software, and removing software as needed. They keep installations in a central location and can maintain all software packages on the system in formats that are commonly used. [**Homebrew**](https://brew.sh/) provides macOS with a free and open source software package managing system that simplifies the installation of software on macOS.

To install Homebrew, type this into your Terminal window:

```shell
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Homebrew is made with Ruby, so it will be modifying your computer’s Ruby path. The `curl` command pulls a script from the specified URL. This script will explain what it will do and then pauses the process to prompt you to confirm. This provides you with a lot of feedback on what the script is going to be doing to your system and gives you the opportunity to verify the process.

If you need to enter your password, note that your keystrokes will not display in the Terminal window but they will be recorded. Simply press the `return` key once you’ve entered your password. Otherwise press the letter `y` for “yes” whenever you are prompted to confirm the installation.

Let’s walk through the flags that are associated with the `curl` command:

-   The `-f` or `--fail` flag tells the Terminal window to give no HTML document output on server errors.
-   The `-s` or `--silent` flag mutes `curl` so that it does not show the progress meter, and combined with the `-S` or `--show-error` flag it will ensure that `curl` shows an error message if it fails.
-   The `-L` or `--location` flag will tell `curl` to redo the request to a new place if the server reports that the requested page has moved to a different location.

Once the installation process is complete, we’ll put the Homebrew directory at the top of the `PATH` environment variable. This will ensure that Homebrew installations will be called over the tools that macOS may select automatically that could run counter to the development environment we’re creating.

You should create or open the `~/.bash_profile` file with the command-line text editor **nano** using the `nano` command:

```shell
nano ~/.bash_profile
```

Once the file opens up in the Terminal window, write the following:

```shell
export PATH=/usr/local/bin:$PATH
```

To save your changes, hold down the `CTRL` key and the letter `o`, and when prompted press the `RETURN` key. Now you can exit nano by holding the `CTRL` key and the letter `x`.

Activate these changes by executing the following in Terminal:

```shell
source ~/.bash_profile
```

Once you have done this, the changes you have made to the `PATH` environment variable will go into effect.

You can make sure that Homebrew was successfully installed by typing:

```shell
brew doctor
```

If no updates are required at this time, the Terminal output will read:

```
Output
Your system is ready to brew.
```

Otherwise, you may get a warning to run another command such as `brew update` to ensure that your installation of Homebrew is up to date.

Once Homebrew is ready, you can install Go.

## Step 4 — Installing Go

You can use Homebrew to search for all available packages with the `brew search` command. For the purpose of this tutorial, you will search for Go-related packages or modules:

```shell
brew search golang
```

**Note**: This tutorial does not use `brew search go` as it returns too many results. Because `go` is such a small word and would match many packages, it has become common to use `golang` as the search term. This is common practice when searching the internet for Go-related articles as well. The term _Golang_ was born from the domain for Go, which is `golang.org`.

The Terminal will output a list of what you can install:

```
Output
golanggolang-migrate
```

Go will be among the items on the list. Go ahead and install it:

```shell
brew install golang
```

The Terminal window will give you feedback regarding the installation process of Go. It may take a few minutes before installation is complete.

To check the version of Go that you installed, type the following:

```shell
go version
```

This will output the specific version of Go that is currently installed, which will by default be the most up-to-date, stable version of Go that is available.

In the future, to update Go, you can run the following commands to first update Homebrew and then update Go. You don’t have to do this now, as you just installed the latest version:

```shell
brew update
brew upgrade golang
```

`brew update` will update the formulae for Homebrew itself, ensuring you have the latest information for packages you want to install. `brew upgrade golang` will update the `golang` package to the latest release of the package.

It is good practice to ensure that your version of Go is up-to-date.

With Go installed on your computer, you are now ready to create a workspace for your Go projects.

## Step 5 — Creating Your Go Workspace

Now that you have Xcode, Homebrew, and Go installed, you can go on to create your programming workspace.

The Go workspace will contain two directories at its root:

-   `src`: The directory that contains Go source files. A source file is a file that you write using the Go programming language. Source files are used by the Go compiler to create an executable binary file.
-   `bin`: The directory that contains executables built and installed by the Go tools. Executables are binary files that run on your system and execute tasks. These are typically the programs compiled by your source code or another downloaded Go source code.

The `src` subdirectory may contain multiple version control repositories (such as [Git](https://git-scm.com/), [Mercurial](https://www.mercurial-scm.org/), and [Bazaar](http://bazaar.canonical.com/)). You will see directories like `github.com` or `golang.org` when your program imports third party libraries. If you are using a code repository like `github.com`, you will also put your projects and source files under that directory. This allows for a canonical import of code in your project. _Canonical_ imports are imports that reference a fully qualified package, such as `github.com/digitalocean/godo`.

Here is what a typical workspace may look like:

```shell
.
├── bin
│   ├── buffalo                                      # command executable
│   ├── dlv                                          # command executable
│   └── packr                                        # command executable
└── src
    └── github.com
        └── digitalocean
            └── godo
                ├── .git                            # Git reposistory metadata
                ├── account.go                      # package source
                ├── account_test.go                 # test source
                ├── ...
                ├── timestamp.go
                ├── timestamp_test.go
                └── util
                    ├── droplet.go
                    └── droplet_test.go
```

The default directory for the Go workspace as of 1.8 is your user’s home directory with a `go` subdirectory, or `$HOME/go`. If you are using a version of Go earlier than 1.8, it is considered best practice to still use the `$HOME/go` location for your workspace.

Issue the following command to create the directory structure for your Go workspace:

```shell
mkdir -p $HOME/go/{bin,src}
```

The `-p` option tells `mkdir` to create all `parents` in the directory, even if they don’t currently exist. Using `{bin,src}` creates a set of arguments to `mkdir` and tells it to create both the `bin` directory and the `src` directory.

This will ensure the following directory structure is now in place:

```shell
└── $HOME
    └── go
        ├── bin
        └── src
```

Prior to Go 1.8, it was required to set a local environment variable called `$GOPATH`. While it is no longer explicitly required to do so, it is still considered a good practice as many third party tools still depend on this variable being set.

You can set your `$GOPATH` by adding it to your `~/.bash_profile`.

First, open `~/.bash_profile` with `nano` or your preferred text editor:

```shell
nano ~/.bash_profile
```

Set your `$GOPATH` by adding the following to the file:

```shell
~/.bash\_profile
export GOPATH=$HOME/go
```

When Go compiles and installs tools, it will put them in the `$GOPATH/bin` directory. For convenience, it’s common to add the workspace’s `/bin` subdirectory to your `PATH` in your `~/.bash_profile`:

```shell
~/.bash\_profile
export PATH=$PATH:$GOPATH/bin
```

You should now have the following entries in your `~/.bash_profile`:

```shell
~/.bash\_profile
export GOPATH=$HOME/go
export PATH=$PATH:$GOPATH/bin
```

This will now allow you to run any programs you compile or download via the Go tools anywhere on your system.

To update your shell, issue the following command to load the global variables you just created:

```shell
. ~/.bash_profile
```

You can verify your `$PATH` is updated by using the `echo` command and inspecting the output:

```shell
echo $PATH
```

You should see your `$GOPATH/bin` which will show up in your home directory. If you were logged in as `sammy`, you would see `/Users/sammy/go/bin` in the path.

```
Output
/Users/sammy/go/bin:/usr/local/sbin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
```

Now that you have the root of the workspace created and your `$GOPATH` environment variable set, you will create your future projects with the following directory structure. This example assumes you are using [github.com](https://www.github.com/) as your repository:

```shell
$GOPATH/src/github.com/username/project
```

If you were working on the [https://github.com/digitalocean/godo](https://github.com/digitalocean/godo) project, you would put it in the following directory:

```shell
$GOPATH/src/github.com/digitalocean/godo
```

Structuring your projects in this manner will make projects available with the `go get` tool. It will also help readability later.

You can verify this by using the `go get` command to fetch the `godo` library:

```shell
go get github.com/digitalocean/godo
```

We can see it successfully downloaded the `godo` package by listing the directory:

```shell
ls -l $GOPATH/src/github.com/digitalocean/godo
```

You will receive output similar to this:

```shell
Output-rw-r--r--  1 sammy  staff   2892 Apr  5 15:56 CHANGELOG.md
-rw-r--r--  1 sammy  staff   1851 Apr  5 15:56 CONTRIBUTING.md
.
.
.
-rw-r--r--  1 sammy  staff   4893 Apr  5 15:56 vpcs.go
-rw-r--r--  1 sammy  staff   4091 Apr  5 15:56 vpcs_test.go
```

In this step, you created a Go workspace and configured the necessary environment variables. In the next step you will test the workspace with some code.

## Step 6 — Creating a Simple Program

Now that you have your Go workspace set up, it’s time to create a simple “Hello, World!” program. This will make sure that your workspace is working and gives you the opportunity to become more familiar with Go.

Because you are creating a single Go source file, and not an actual project, you don’t need to be in your workspace to do this.

From your home directory, open up a command-line text editor, such as `nano`, and create a new file:

```shell
nano hello.go
```

Once the text file opens up in Terminal, type out your program:

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello, World!")
}
```

Exit nano by typing the `control` and `x` keys, and when prompted to save the file press `y`.

This code will use the `fmt` package and call the `Println` function with `Hello, World!` as the argument. This will cause the phrase `Hello, World!` to print out to the terminal when the program is run.

Once you exit out of `nano` and return to your shell, run the program:

```shell
go run hello.go
```

The `hello.go` program that you just created will cause Terminal to produce the following output:

```shell
Output
Hello, World!
```

In this step, you used a basic program to verify that your Go workspace is properly configured.

## Conclusion

Congratulations! At this point you have a Go programming workspace set up on your local macOS machine and can begin a coding project!