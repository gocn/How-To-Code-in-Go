# How To Install Go and Set Up a Local Programming Environment on Windows 10

## Introduction

[Go](https://golang.org/) is a programming language that was born out of frustration at Google. Developers continually had to pick a language that executed efficiently but took a long time to compile, or to pick a language that was easy to program but ran inefficiently in production. Go was designed to have all three available at the same time: fast compilation, ease of programming, and efficient execution in production.

While Go is a versatile programming language that can be used for many different programming projects, it’s particularly well suited for networking/distributed systems programs, and has earned a reputation as “the language of the cloud”. It focuses on helping the modern programmer do more with a strong set of tooling, removing debates over formatting by making the format part of the language specification, as well as making deployment easy by compiling to a single binary. Go is easy to learn, with a very small set of keywords, which makes it a great choice for beginners and experienced developers alike.

This tutorial will guide you through installing Go on your local Windows 10 machine and setting up a programming environment via the command line.

## Prerequisites

You will need a Windows 10 machine with administrative access that is connected to the internet.

## Step 1 — Opening and Configuring PowerShell

You’ll be completing most of the installation and setup on a command-line interface, which is a non-graphical way to interact with your computer. That is, instead of clicking on buttons, you’ll be typing in text and receiving feedback from your computer through text as well. The command line, also known as a _shell_, can help you modify and automate many of the tasks you do on a computer every day, and is an essential tool for software developers.

[PowerShell](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/powershell) is a program from Microsoft that provides a command-line shell interface. Administrative tasks are performed by running _cmdlets_, pronounced _command-lets_, which are specialized classes of the [.NET](https://dotnet.microsoft.com/) software framework that can carry out operations. Open-sourced in August 2016, PowerShell is now available across platforms, for both Windows and UNIX systems (including Mac and Linux).

To find Windows PowerShell, you can right-click on the **Start** menu icon on the lower left-hand corner of your screen. When the menu pops up, click on **Search**, and then type `PowerShell` into the search bar. When you are presented with options, right-click on **Windows PowerShell** from the Desktop app. For the purposes of this tutorial, select **Run as Administrator**. When you are prompted with a dialog box that asks **Do you want to allow this app to make changes to your PC?** click on **Yes**.

Once you do this, you’ll see a text-based interface that has a string of words that looks like this:

![Windows 10 PowerShell](https://assets.digitalocean.com/articles/eng_python/Win10SetUp/Win10SetUp.png)

Switch out of the system folder by typing the following command:

You’ll then be in a home directory such as `PS C:\Users\sammy`.

To continue with the installation process, you must first set up permissions through PowerShell. Configured to run in the most secure mode by default, there are a few levels of permissions that you can set up as an administrator:

-   **Restricted** is the default execution policy. Under this mode you will not be able to run scripts, and PowerShell will work only as an interactive shell.
-   **AllSigned** will enable you to run all scripts and configuration files that are signed by a trusted publisher, meaning that you could potentially open your machine up to the risk of running malicious scripts that happen to be signed by a trusted publisher.
-   **RemoteSigned** will let you run scripts and configuration files downloaded from the internet signed by trusted publishers, again opening your machine up to vulnerabilities if these trusted scripts are actually malicious.
-   **Unrestricted** will run all scripts and configuration files downloaded from the internet as soon as you confirm that you understand that the file was downloaded from the internet. In this case no digital signature is required, so you could be opening your machine up to the risk of running unsigned and potentially malicious scripts downloaded from the internet.

In this tutorial you will use the `RemoteSigned` execution policy to set the permissions for the current user. This will allow the PowerShell to accept trusted scripts without making the permissions as broad as they would be with an `Unrestricted` permission. Enter the following in PowerShell:

PowerShell will then prompt you to provide an execution policy. Enter the following to use `RemoteSigned`:

Once you press `ENTER`, you’ll be asked to confirm the change to the execution policy. Type the letter `y` to allow the changes to take effect. You can confirm that this worked by asking for the current permissions across the machine:

You should receive output that looks something like this:

```
Output        Scope ExecutionPolicy
        ----- ---------------
MachinePolicy       Undefined
   UserPolicy       Undefined
      Process       Undefined
  CurrentUser    RemoteSigned
 LocalMachine       Undefined
```

This confirms that the current user can run trusted scripts downloaded from the internet. You can now move on to downloading the files we will need to set up our Go programming environment.

## Step 2 — Installing the Package Manager Chocolatey

A _package manager_ is a collection of software tools that work to automate installation processes. This includes the initial installation, upgrading and configuring of software, and removing software as needed. They keep software installations in a central location and can maintain all software packages on the system in formats that are commonly used.

[Chocolatey](https://chocolatey.org/) is a command-line package manager built for Windows that works like `apt-get` does on Linux. Available in an open-source version, Chocolatey will help you quickly install applications and tools. You will be using it to download what you need for your development environment.

Before installing the script, read it to confirm that you are happy with the changes it will make to your machine. To do this, use the .NET scripting framework to download and display the Chocolatey script within the terminal window.

Start by creating a WebClient object called `$script` that shares internet connection settings with Internet Explorer:

Take a look at the available options by piping the `$script` object with `|` to the `Get-Member` class:

This will return all members (properties and methods) of this WebClient object:

```
 . . .
[secondary_label Snippet of Output]
DownloadFileAsync         Method     void DownloadFileAsync(uri address, string fileName), void DownloadFileAsync(ur...
DownloadFileTaskAsync     Method     System.Threading.Tasks.Task DownloadFileTaskAsync(string address, string fileNa...
DownloadString            Method     string DownloadString(string address), string DownloadString(uri address) #method we will use
DownloadStringAsync       Method     void DownloadStringAsync(uri address), void DownloadStringAsync(uri address, Sy...
DownloadStringTaskAsync   Method     System.Threading.Tasks.Task[string] DownloadStringTaskAsync(string address), Sy…
 . . .
```

Looking over the output, you can identify the `DownloadString` method used to display the script and signature in the PowerShell window. Use this method to inspect the script:

After inspecting the script, install Chocolatey by typing the following into PowerShell:

The cmdlet `iwr`, or `Invoke-WebRequest`, allows you to extract data from the web. This will pass the script to `iex`, or the `Invoke-Expression` cmdlet, which will execute the contents of the script and run the installation for the Chocolatey package manager.

Allow PowerShell to install Chocolatey. Once it is fully installed, you can begin installing additional tools with the `choco` command.

If you need to upgrade Chocolatey at any time in the future, run the following command:

With the package manager installed, you can install the rest of what you need for the Go programming environment.

## Step 3 — Installing the Text Editor Nano (Optional)

In this step, you are going to install _nano_, a text editor that uses a command-line interface. You can use nano to write programs directly within PowerShell. This is not a compulsory step, as you can also use a text editor with a graphical user interface such as Notepad. This tutorial recommends using nano, as it will help accustom you to using PowerShell.

Use Chocolatey to install nano:

The `-y` flag automatically confirms that you want to run the script without being prompted for confirmation.

Once nano is installed, you can use the `nano` command to create new text files. You will use it later in this tutorial to write your first Go program.

## Step 4 — Installing Go

Just like you did with nano in the previous step, you will use Chocolatey to install Go:

**Note:** Because **go** is such a small word, it has become common to use `golang` as a term for installing packages and when searching the internet for Go-related articles. The term _Golang_ was born from the domain for Go, which is `golang.org`.

PowerShell will now install Go, generating output within PowerShell during that process. Once the install is completed, you should see the following output:

```
OutputEnvironment Vars (like PATH) have changed. Close/reopen your shell to
see the changes (or in powershell/cmd.exe just type `refreshenv`).
The install of golang was successful.
 Software installed as 'msi', install location is likely default.

Chocolatey installed 1/1 packages.
See the log for details (C:\ProgramData\chocolatey\logs\chocolatey.log).
```

With the installation finished, you’ll now confirm that Go is installed. To see the changes, close and re-open PowerShell as an Administrator, then check the version of Go available on your local machine:

You’ll receive output similar to the following:

```
Outputgo version go1.12.1 windows/amd643.7.0
```

Once Go is installed, you can set up a workspace for your development projects.

## Step 5 — Creating Your Go Workspace

Now that you have Chocolatey, nano, and Go installed, you can create your programming workspace.

The Go workspace will contain two directories at its root:

-   `src`: The directory that contains Go source files. A source file is a file that you write using the Go programming language. Source files are used by the Go compiler to create an executable binary file.
-   `bin`: The directory that contains executables built and installed by the Go tools. Executables are binary files that run on your system and execute tasks. These are typically the programs compiled by your source code or another downloaded Go source code.

The `src` subdirectory may contain multiple version control repositories (such as [Git](https://git-scm.com/), [Mercurial](https://www.mercurial-scm.org/), and [Bazaar](http://bazaar.canonical.com/)). You will see directories like `github.com` or `golang.org` when your program imports third party libraries. If you are using a code repository like `github.com`, you will also put your projects and source files under that directory. This allows for a canonical import of code in your project. _Canonical_ imports are imports that reference a fully qualified package, such as `github.com/digitalocean/godo`.

Here is what a typical workspace may look like:

```
.
├── bin
│   ├── buffalo                                      # command executable
│   ├── dlv                                          # command executable
│   └── packr                                        # command executable
└── src
    └── github.com
        └── digitalocean
            └── godo
                ├── .git                            # Git repository metadata
                ├── account.go                      # package source
                ├── account_test.go                 # test source
                ├── ...
                ├── timestamp.go
                ├── timestamp_test.go
                └── util
                    ├── droplet.go
                    └── droplet_test.go
```

The default directory for the Go workspace as of 1.8 is your user’s home directory with a `go` subdirectory, or `$HOME/go`. If you are using an earlier version of Go than 1.8, it is still considered best practice to use the `$HOME/go` location for your workspace

Issue the following command to navigate to the `$HOME` directory:

Next, create the directory structure for your Go workspace:

This will ensure the following directory structure is now in place:

```
└── $HOME
    └── go
        ├── bin
        └── src
```

Prior to Go 1.8, it was required to set a local environment variable called `$GOPATH`. While it is no longer explicitly required to do so, it is still considered a good practice as many third party tools still depend on this variable being set.

Since you used Chocolatey for the installation, this environment variable should already be set. You can verify this with the following command:

You should see the following output, with your username in place of `sammy`:

```
OutputC:\Users\sammy\go
```

When Go compiles and installs tools, it will put them in the `$GOPATH/bin` directory. For convenience, it’s common to add the workspace’s `bin` subdirectory to your `$PATH`. You can do this using the `setx` command in PowerShell:

This will now allow you to run any programs you compile or download via the Go tools anywhere on your system.

Now that you have the root of the workspace created and your `$GOPATH` environment variable set, you will create your future projects with the following directory structure. This example assumes you are using [github.com](https://www.github.com/) as your repository:

```
$GOPATH/src/github.com/username/project
```

If you were working on the [https://github.com/digitalocean/godo](https://github.com/digitalocean/godo) project, you would put it in the following directory:

```
$GOPATH/src/github.com/digitalocean/godo
```

Structuring your projects in this manner will make projects available with the `go get` tool. It will also help readability later.

You can verify this by using the `go get` command to fetch the `godo` library:

**Note:** If you don’t have `git` installed, Windows will open a dialog box asking if you want to install it. Click **Yes** to continue and follow the installation instructions.

You can see it successfully downloaded the `godo` package by listing the directory:

You will receive output similar to this:

```
Output    Directory: C:\Users\sammy\go\src\github.com\digitalocean\godo


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        4/10/2019   2:59 PM                util
-a----        4/10/2019   2:59 PM              9 .gitignore
-a----        4/10/2019   2:59 PM             69 .travis.yml
-a----        4/10/2019   2:59 PM           1592 account.go
-a----        4/10/2019   2:59 PM           1679 account_test.go
-rw-r--r--  1 sammy  staff   2892 Apr  5 15:56 CHANGELOG.md
-rw-r--r--  1 sammy  staff   1851 Apr  5 15:56 CONTRIBUTING.md
.
.
.
-a----        4/10/2019   2:59 PM           5076 vpcs.go
-a----        4/10/2019   2:59 PM           4309 vpcs_test.go
```

In this step, you created a Go workspace and configured the necessary environment variables. In the next step you will test the workspace with some code.

## Step 6 — Creating a Simple Program

Now that you have the Go workspace set up, create a simple “Hello, World!” program. This will make sure that your workspace is configured properly, and also gives you the opportunity to become more familiar with Go. Because you are creating a single Go source file, and not an actual project, you don’t need to be in your workspace to do this.

From your home directory, open up a command-line text editor, such as nano, and create a new file:

Once the text file opens up in nano, type out your program:

hello.go

Exit `nano` by pressing the `CTRL` and `X` keys. When prompted to save the file, press `Y` and then `ENTER`.

This code will use the `fmt` package and call the `Println` function with `Hello, World!` as the argument. This will cause the phrase `Hello, World!` to print out to the terminal when the program is run.

Once you exit out of `nano` and return to your shell, run the program:

The hello.go program that you just created should cause PowerShell to produce the following output:

```
OutputHello, World!
```

In this step, you used a basic program to verify that your Go workspace is properly configured.

## Conclusion

Congratulations! At this point you have a Go programming workspace set up on your local Windows machine and can begin a coding project!