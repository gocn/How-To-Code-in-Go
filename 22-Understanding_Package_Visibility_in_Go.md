# Understanding Package Visibility in Go

### Introduction

When creating a [package in Go](https://www.digitalocean.com/community/tutorials/how-to-write-packages-in-go), the end goal is usually to make the package accessible for other developers to use, either in higher order packages or whole programs. By [importing the package](https://www.digitalocean.com/community/tutorials/importing-packages-in-go), your piece of code can serve as the building block for other, more complex tools. However, only certain packages are available for importing. This is determined by the visibility of the package.

*Visibility* in this context means the file space from which a package or other construct can be referenced. For example, if we define a variable in a function, the visibility (scope) of that variable is only within the function in which it was defined. Similarly, if you define a variable in a package, you can make it visible to just that package, or allow it to be visible outside the package as well.

Carefully controlling package visibility is important when writing ergonomic code, especially when accounting for future changes that you may want to make to your package. If you need to fix a bug, improve performance, or change functionality, you’ll want to make the change in a way that won’t break the code of anyone using your package. One way to minimize breaking changes is to allow access only to the parts of your package that are needed for it to be used properly. By limiting access, you can make changes internally to your package with less of a chance of affecting how other developers are using your package.

In this article, you will learn how to control package visibility, as well as how to protect parts of your code that should only be used inside your package. To do this, we will create a basic logger to log and debug messages, using packages with varying degrees of item visibility.

## Prerequisites

To follow the examples in this article, you will need:

- A Go workspace set up by following [How To Install Go and Set Up a Local Programming Environment](https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-go). This tutorial will use the following file structure:

```
.
├── bin 
│ 
└── src
    └── github.com
        └── gopherguides
```

## Exported and Unexported Items

Unlike other program languages like Java and [Python](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-python-3) that use *access modifiers* such as `public`, `private`, or `protected` to specify scope, Go determines if an item is `exported` and `unexported` through how it is declared. Exporting an item in this case makes it `visible` outside the current package. If it’s not exported, it is only visible and usable from within the package it was defined.

This external visibility is controlled by capitalizing the first letter of the item declared. All declarations, such as `Types`, `Variables`, `Constants`, `Functions`, etc., that start with a capital letter are visible outside the current package.

Let’s look at the following code, paying careful attention to capitalization:

greet.go

```
package greet

import "fmt"

var Greeting string

func Hello(name string) string {
	return fmt.Sprintf(Greeting, name)
}
```

This code declares that it is in the `greet` package. It then declares two symbols, a variable called `Greeting`, and a function called `Hello`. Because they both start with a capital letter, they are both `exported` and available to any outside program. As stated earlier, crafting a package that limits access will allow for better API design and make it easier to update your package internally without breaking anyone’s code that is depending on your package.

## Defining Package Visibility

To give a closer look at how package visibility works in a program, let’s create a `logging` package, keeping in mind what we want to make visible outside our package and what we won’t make visible. This logging package will be responsible for logging any of our program messages to the console. It will also look at what *level* we are logging at. A level describes the type of log, and is going to be one of three statuses: `info`, `warning`, or `error`.

First, within your `src` directory, let’s create a directory called `logging` to put our logging files in:

```bash
mkdir logging
```


Move into that directory next:

```bash
cd logging
```


Then, using an editor like nano, create a file called `logging.go`:

```bash
nano logging.go
```


Place the following code in the `logging.go` file we just created:

logging/logging.go

```go
package logging

import (
	"fmt"
	"time"
)

var debug bool

func Debug(b bool) {
	debug = b
}

func Log(statement string) {
	if !debug {
		return
	}

	fmt.Printf("%s %s\n", time.Now().Format(time.RFC3339), statement)
}
```


The first line of this code declared a package called `logging`. In this package, there are two `exported` functions: `Debug` and `Log`. These functions can be called by any other package that imports the `logging` package. There is also a private variable called `debug`. This variable is only accessible from within the `logging` package. It is important to note that while the function `Debug` and the variable `debug` both have the same spelling, the function is capitalized and the variable is not. This makes them distinct declarations with different scopes.

Save and quit the file.

To use this package in other areas of our code, we can [`import` it into a new package](https://www.digitalocean.com/community/tutorials/importing-packages-in-go). We’ll create this new package, but we’ll need a new directory to store those source files in first.

Let’s move out of the `logging` directory, create a new directory called `cmd`, and move into that new directory:

```bash
cd ..
mkdir cmd
cd cmd
```


Create a file called `main.go` in the `cmd` directory we just created:

```bash
nano main.go
```


Now we can add the following code:

cmd/main.go

```go
package main

import "github.com/gopherguides/logging"

func main() {
	logging.Debug(true)

	logging.Log("This is a debug statement...")
}
```


We now have our entire program written. However, before we can run this program, we’ll need to also create a couple of configuration files for our code to work properly. Go uses [Go Modules](https://blog.golang.org/using-go-modules) to configure package dependencies for importing resources. Go modules are configuration files placed in your package directory that tell the compiler where to import packages from. While learning about modules is beyond the scope of this article, we can write just a couple lines of configuration to make this example work locally.

Open the following `go.mod` file in the `cmd` directory:

```bash
nano go.mod
```


Then place the following contents in the file:

go.mod

```
module github.com/gopherguides/cmd

replace github.com/gopherguides/logging => ../logging
```

The first line of this file tells the compiler that the `cmd` package has a file path of `github.com/gopherguides/cmd`. The second line tells the compiler that the package `github.com/gopherguides/logging` can be found locally on disk in the `../logging` directory.

We’ll also need a `go.mod` file for our `logging` package. Let’s move back into the `logging` directory and create a `go.mod` file:

```bash
cd ../logging
nano go.mod
```


Add the following contents to the file:

go.mod

```
module github.com/gopherguides/logging
```

This tells the compiler that the `logging` package we created is actually the `github.com/gopherguides/logging` package. This makes it possible to import the package in our `main` package with the following line that we wrote earlier:

cmd/main.go

```
package main

import "github.com/gopherguides/logging"

func main() {
	logging.Debug(true)

	logging.Log("This is a debug statement...")
}
```

You should now have the following directory structure and file layout:

```
├── cmd
│   ├── go.mod
│   └── main.go
└── logging
    ├── go.mod
    └── logging.go
```

Now that we have all the configuration completed, we can run the `main` program from the `cmd` package with the following commands:

```bash
cd ../cmd
go run main.go
```


You will get output similar to the following:

```
Output2019-08-28T11:36:09-05:00 This is a debug statement...
```

The program will print out the current time in RFC 3339 format followed by whatever statement we sent to the logger. [RFC 3339](https://tools.ietf.org/html/rfc3339) is a time format that was designed to represent time on the internet and is commonly used in log files.

Because the `Debug` and `Log` functions are exported from the logging package, we can use them in our `main` package. However, the `debug` variable in the `logging` package is not exported. Trying to reference an unexported declaration will result in a compile-time error.

Add the following highlighted line to `main.go`:

cmd/main.go

```
package main

import "github.com/gopherguides/logging"

func main() {
	logging.Debug(true)

	logging.Log("This is a debug statement...")

	fmt.Println(logging.debug)
}
```

Save and run the file. You will receive an error similar to the following:

```
Output. . .
./main.go:10:14: cannot refer to unexported name logging.debug
```

Now that we have seen how `exported` and `unexported` items in packages behave, we will next look at how `fields` and `methods` can be exported from `structs`.

## Visibility Within Structs

While the visibility scheme in the logger we built in the last section may work for simple programs, it shares too much state to be useful from within multiple packages. This is because the exported variables are accessible to multiple packages that could modify the variables into contradictory states. Allowing the state of your package to be changed in this way makes it hard to predict how your program will behave. With the current design, for example, one package could set the `Debug` variable to `true`, and another could set it to `false` in the same instance. This would create a problem since both packages that are importing the `logging` package are affected.

We can make the logger isolated by creating a struct and then hanging methods off of it. This will allow us to create an `instance` of a logger to be used independently in each package that consumes it.

Change the `logging` package to the following to refactor the code and isolate the logger:

logging/logging.go

```go
package logging

import (
	"fmt"
	"time"
)

type Logger struct {
	timeFormat string
	debug      bool
}

func New(timeFormat string, debug bool) *Logger {
	return &Logger{
		timeFormat: timeFormat,
		debug:      debug,
	}
}

func (l *Logger) Log(s string) {
	if !l.debug {
		return
	}
	fmt.Printf("%s %s\n", time.Now().Format(l.timeFormat), s)
}
```


In this code, we created a `Logger` struct. This struct will house our unexported state, including the time format to print out and the `debug` variable setting of `true` or `false`. The `New` function sets the initial state to create the logger with, such as the time format and debug state. It then stores the values we gave it internally to the unexported variables `timeFormat` and `debug`. We also created a method called `Log` on the `Logger` type that takes a statement we want to print out. Within the `Log` method is a reference to its local method variable `l` to get access back to its internal fields such as `l.timeFormat` and `l.debug`.

This approach will allow us to create a `Logger` in many different packages and use it independently of how the other packages are using it.

To use it in another package, let’s alter `cmd/main.go` to look like the following:

cmd/main.go

```go
package main

import (
	"time"

	"github.com/gopherguides/logging"
)

func main() {
	logger := logging.New(time.RFC3339, true)

	logger.Log("This is a debug statement...")
}
```


Running this program will give you the following output:

```
Output2019-08-28T11:56:49-05:00 This is a debug statement...
```

In this code, we created an instance of the logger by calling the exported function `New`. We stored the reference to this instance in the `logger` variable. We can now call `logging.Log` to print out statements.

If we try to reference an unexported field from the `Logger` such as the `timeFormat` field, we will receive a compile-time error. Try adding the following highlighted line and running `cmd/main.go`:

cmd/main.go

```
package main

import (
	"time"

	"github.com/gopherguides/logging"
)

func main() {
	logger := logging.New(time.RFC3339, true)

	logger.Log("This is a debug statement...")

	fmt.Println(logger.timeFormat)
}
```

This will give the following error:

```
Output. . .
cmd/main.go:14:20: logger.timeFormat undefined (cannot refer to unexported field or method timeFormat)
```

The compiler recognizes that `logger.timeFormat` is not exported, and therefore can’t be retrieved from the `logging` package.

## Visibility Within Methods

In the same way as struct fields, methods can also be exported or unexported.

To illustrate this, let’s add *leveled* logging to our logger. Leveled logging is a means of categorizing your logs so that you can search your logs for specific types of events. The levels we will put into our logger are:

- The `info` level, which represents information type events that inform the user of an action, such as `Program started`, or `Email sent`. These help us debug and track parts of our program to see if expected behavior is happening.
- The `warning` level. These types of events identify when something unexpected is happening that is not an error, like `Email failed to send, retrying`. They help us see parts of our program that aren’t going as smoothly as we expected them to.
- The `error` level, which means the program encountered a problem, like `File not found`. This will often result in the program’s operation failing.

You may also desire to turn on and off certain levels of logging, especially if your program isn’t performing as expected and you’d like to debug the program. We’ll add this functionality by changing the program so that when `debug` is set to `true`, it will print all levels of messages. Otherwise, if it’s `false`, it will only print error messages.

Add leveled logging by making the following changes to `logging/logging.go`:

logging/logging.go

```go
package logging

import (
	"fmt"
	"strings"
	"time"
)

type Logger struct {
	timeFormat string
	debug      bool
}

func New(timeFormat string, debug bool) *Logger {
	return &Logger{
		timeFormat: timeFormat,
		debug:      debug,
	}
}

func (l *Logger) Log(level string, s string) {
	level = strings.ToLower(level)
	switch level {
	case "info", "warning":
		if l.debug {
			l.write(level, s)
		}
	default:
		l.write(level, s)
	}
}

func (l *Logger) write(level string, s string) {
	fmt.Printf("[%s] %s %s\n", level, time.Now().Format(l.timeFormat), s)
}
```


In this example, we introduced a new argument to the `Log` method. We can now pass in the `level` of the log message. The `Log` method determines what level of message it is. If it’s an `info` or `warning` message, and the `debug` field is `true`, then it writes the message. Otherwise it ignores the message. If it is any other level, like `error`, it will write out the message regardless.

Most of the logic for determining if the message is printed out exists in the `Log` method. We also introduced an unexported method called `write`. The `write` method is what actually outputs the log message.

We can now use this leveled logging in our other package by changing `cmd/main.go` to look like the following:

cmd/main.go

```go
package main

import (
	"time"

	"github.com/gopherguides/logging"
)

func main() {
	logger := logging.New(time.RFC3339, true)

	logger.Log("info", "starting up service")
	logger.Log("warning", "no tasks found")
	logger.Log("error", "exiting: no work performed")

}
```


Running this will give you:

```
Output[info] 2019-09-23T20:53:38Z starting up service
[warning] 2019-09-23T20:53:38Z no tasks found
[error] 2019-09-23T20:53:38Z exiting: no work performed
```

In this example, `cmd/main.go` successfully used the exported `Log` method.

We can now pass in the `level` of each message by switching `debug` to `false`:

main.go

```go
package main

import (
	"time"

	"github.com/gopherguides/logging"
)

func main() {
	logger := logging.New(time.RFC3339, false)

	logger.Log("info", "starting up service")
	logger.Log("warning", "no tasks found")
	logger.Log("error", "exiting: no work performed")

}
```


Now we will see that only the `error` level messages print:

```
Output[error] 2019-08-28T13:58:52-05:00 exiting: no work performed
```

If we try to call the `write` method from outside the `logging` package, we will receive a compile-time error:

main.go

```
package main

import (
	"time"

	"github.com/gopherguides/logging"
)

func main() {
	logger := logging.New(time.RFC3339, true)

	logger.Log("info", "starting up service")
	logger.Log("warning", "no tasks found")
	logger.Log("error", "exiting: no work performed")

	logger.write("error", "log this message...")
}
Outputcmd/main.go:16:8: logger.write undefined (cannot refer to unexported field or method logging.(*Logger).write)
```

When the compiler sees that you are trying to reference something from another package that starts with a lowercase letter, it knows that it is not exported, and therefore throws a compiler error.

The logger in this tutorial illustrates how we can write code that only exposes the parts we want other packages to consume. Because we control what parts of the package are visible outside the package, we are now able to make future changes without affecting any code that depends on our package. For example, if we wanted to only turn off `info` level messages when `debug` is false, you could make this change without affecting any other part of your API. We could also safely make changes to the log message to include more information, such as the directory the program was running from.

## Conclusion

This article showed how to share code between packages while also protecting the implementation details of your package. This allows you to export a simple API that will seldom change for backwards compatibility, but will allow for changes privately in your package as needed to make it work better in the future. This is considered a best practice when creating packages and their corresponding APIs.

To learn more about packages in Go, check out our [Importing Packages in Go](https://www.digitalocean.com/community/tutorials/importing-packages-in-go) and [How To Write Packages in Go](https://www.digitalocean.com/community/tutorials/how-to-write-packages-in-go) articles, or explore our entire [How To Code in Go series](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go).