# How to Add Extra Information to Errors in Go

## Introduction

When a function in Go fails, the function will return a value using the `error` interface to allow the caller to handle that failure. In many cases, developers will use the [`fmt.Errorf`](https://pkg.go.dev/fmt#Errorf) function in the [`fmt`](https://pkg.go.dev/fmt) package to return these values. Prior to Go 1.13, though, a downside of using this function is that you would lose information about any errors that may have caused the error to be returned. To solve this, developers would either use packages to provide a way to “wrap” errors inside other errors or create custom errors by implementing the `Error() string` method on one of their `struct` error types. Sometimes it can be tedious to create these `struct` types if you have a number of errors that don’t need to be handled explicitly by the callers, though, so in Go 1.13, the language added features to make it easier to handle these cases.

One feature is the ability to wrap errors using the `fmt.Errorf` function with an `error` value that can be unwrapped later to access the wrapped errors. This builds the error-wrapping functionality into the Go standard library, so there’s no longer any need to use a third-party library.

Additionally, the functions [`errors.Is`](https://pkg.go.dev/errors#Is) and [`errors.As`](https://pkg.go.dev/errors#As) make it easier to determine if a specific error is wrapped anywhere inside a given error, and will also give you access to that specific error directly without needing to unwrap all the errors yourself.

In this tutorial, you’ll create a program that uses these functions to include additional information in errors returned from your functions, and then create your own custom error `struct` that supports the wrapping and unwrapping functionality.

## Prerequisites

To follow this tutorial, you will need:

-   Go version 1.13 or greater installed. To set this up, follow the [How To Install Go](https://www.digitalocean.com/community/tutorials/how-to-install-go-on-ubuntu-20-04) tutorial for your operating system.
-   (Optional) Reading [Handling Errors in Go](https://www.digitalocean.com/community/tutorials/handling-errors-in-go) may be helpful in this tutorial for a more in-depth explanation of error handling, but this tutorial will also cover some of the same topics at a higher level.
-   (Optional) This tutorial expands upon the [Creating Custom Errors in Go](https://www.digitalocean.com/community/tutorials/creating-custom-errors-in-go) tutorial with features added to Go since the original tutorial. Reading the previous tutorial is helpful, but is not strictly required.

## Returning and Handling Errors in Go

When an error occurs in a program, it’s good practice to handle those errors so your users never see them — but to handle the errors, you need to know about them first. In Go, you can handle errors in your program by returning information about the error from your functions using a special `interface` type, the `error` interface. Using the `error` interface allows any Go type to be returned as an `error` value as long as that type has an `Error() string` method defined. The Go standard library provides functionality to create `error`s for these return values, such as the [`fmt.Errorf`](https://pkg.go.dev/fmt#Errorf) function.

In this section, you’ll create a program with a function that uses `fmt.Errorf` to return an error, and you will also add an error handler to check for the errors that the function could return. (If you’d like more information on handling errors in Go, please see the tutorial, [Handling Errors in Go](https://www.digitalocean.com/community/tutorials/handling-errors-in-go).)

Many developers have a directory to keep current projects. In this tutorial, you’ll use a directory named `projects`.

To begin, make the `projects` directory and navigate to it:

```shell
mkdir projects
cd projects
```

From the `projects` directory, create a new `errtutorial` directory to keep the new program in:

```shell
mkdir errtutorial
```

Next, navigate into the new directory with the `cd` command:

```shell
cd errtutorial
```

Once you’re in the `errtutorial` directory, use the `go mod init` command to create a new module named `errtutorial`:

```shell
go mod init errtutorial
```

After creating the Go module, open a file named `main.go` in the `errtutorial` directory using `nano`, or your favorite editor:

```shell
nano main.go
```

Next, you will write a program. The program will loop over the numbers `1` through `3` and try to determine if those numbers are valid or not using a function called `validateValue`. If the number is determined to be invalid, the program will use the `fmt.Errorf` function to generate an `error` value that is returned from the function. The `fmt.Errorf` function allows you to create an `error` value where the error message is the message you provide to the function. It works similarly to `fmt.Printf`, but instead of printing the message to the screen it returns it as an `error` instead.

Then, in the `main` function, the error value will be checked to see if it’s a `nil` value or not. If it is a `nil` value, the function succeeded and the `valid!` message is printed. If it’s not, the error received is printed instead.

To begin your program, add the following code into your `main.go` file:

projects/errtutorial/main.go

```go
package main

import (
	"fmt"
)

func validateValue(number int) error {
	if number == 1 {
		return fmt.Errorf("that's odd")
	} else if number == 2 {
		return fmt.Errorf("uh oh")
	}
	return nil
}

func main() {
	for num := 1; num <= 3; num++ {
		fmt.Printf("validating %d... ", num)
		err := validateValue(num)
		if err != nil {
			fmt.Println("there was an error:", err)
		} else {
			fmt.Println("valid!")
		}
	}
}
```

The `validateValue` function from the program takes a number and then returns an `error` based on whether it was determined to be a valid value or not. In this program, the number `1` is invalid and returns the error `that's odd`. The number `2` is invalid and returns the error `uh oh`. The `validateValue` function uses the `fmt.Errorf` function to generate the `error` value being returned. The `fmt.Errorf` function is convenient for returning errors because it allows you to format an error message using formatting similar to `fmt.Printf` or `fmt.Sprintf` without needing to then pass that `string` to `errors.New`.

In the `main` function, the `for` loop will start by iterating over each number from `1` to `3` and will store the value in the `num` variable. Inside the loop body, a call to `fmt.Printf` will print the number the program is currently validating. Then, it will call the `validateValue` function and pass in `num`, the current number being validated, and store the error result in the `err` variable. Lastly, if `err` is not `nil` it means an error occured during validation and the error message is printed using `fmt.Println`. The `else` clause of the error check will print `"valid!"` when an error wasn’t encountered.

After saving your changes, run your program using the `go run` command with `main.go` as the argument from the `errtutorial` directory:

```shell
go run main.go
```

The output from running the program will show that validation was run for each number and number `1` and number `2` returned their appropriate errors:

```
Outputvalidating 1... there was an error: that's odd
validating 2... there was an error: uh oh
validating 3... valid!
```

When you look at the output from the program, you’ll see the program tried to validate all three numbers. The first time it says the `validateValue` function returned the `that's odd` error, which would be expected for the value of `1`. The next value, `2`, also shows it returned an error, but it was the `uh oh` error this time . Finally, the `3` value returns `nil` for the error value, meaning there wasn’t an error and the number is valid. The way the `validateValue` function is written, the `nil` error value would be returned for any values that aren’t either `1` or `2`.

In this section, you used `fmt.Errorf` to create `error` values you returned from a function. You also added an error handler to print out the error message when any `error` is returned from the function. At times, though, it can be useful to know what an error means, not just that an error occurred. In the next section, you’ll learn to customize error handling for specific cases.

## Handling Specific Errors Using Sentinel Errors

When you receive an `error` value from a function, the most basic error handling is to check if the `error` value is `nil` or not. This will tell you if the function had an error, but sometimes you may want to customize error handling for a specific error case. For example, imagine you have code connecting to a remote server, and the only error information you get back is “you had an error”. You may wish to tell whether the error was because the server was unavailable or if your connection credentials were invalid. If you knew the error meant a user’s credentials were wrong, you might want to let the user know right away. But if the error means the server was unavailable, you may want to try reconnecting a few times before letting the user know. Determining the difference between these errors allows you to write more robust and user-friendly programs.

One way you could check for a particular type of error might be using the `Error` method on an `error` type to get the message from the error and compare that value to the type of error you’re looking for. Imagine that in your program, you want to show a message other than `there was an error: uh oh` when the error value is `uh oh`. One approach to handling this case would be to check the value returned from the `Error` method, like so:

```go
if err.Error() == "uh oh" {
	// Handle 'uh oh' error.
	fmt.Println("oh no!")
}
```

Checking the string value of `err.Error()` to see if it’s the value `uh oh`, as in the code above, would work in this case. But the code would not work if the `uh oh` error `string` is slightly different elsewhere in the program. Checking errors this way can also lead to significant updates to code if the error’s message itself needs to be updated because every place the error is checked would need to be updated. Take the following code, for example:

```go
func giveMeError() error {
	return fmt.Errorf("uh h")
}

err := giveMeError()
if err.Error() == "uh h" {
	// "uh h" error code
}
```

In this code, the error message includes a typo and is missing the `o` in `uh oh`. If this is noticed and fixed at some point, but only after adding this error checking in several places, all those places will need to have their checks updated to `err.Error() == "uh oh"`. If one is missed, which could be easy because it’s only a single character change, the expected custom error handler will not run because it’s expecting `uh h` and not `uh oh`.

In cases like these, where you may want to handle a specific error differently than others, it’s common to create a variable whose purpose is to hold an error value. This way, the code can check against that variable instead of a string. Typically, these variables begin with either `err` or `Err` in their names to signify they’re errors. If the error is only meant to be used within the package it’s defined in, you would want to use the `err` prefix. If the error is meant to be used elsewhere, you would instead use the `Err` prefix to make it an exported value, similar to a function or a `struct`.

Now, let’s say you were using one of these error values in the typo example from before:

```go
var errUhOh = fmt.Errorf("uh h")

func giveMeError() error {
	return errUhOh
}

err := giveMeError()
if err == errUhOh {
	// "uh oh" error code
}
```

In this example, the variable `errUhOh` is defined as the error value for an “uh oh” error (even though it’s misspelled). The `giveMeError` function returns the value of `errUhOh` because it wants to let the caller know that an “uh oh” error happened. Then, the error handling code compares the `err` value returned from `giveMeError` against `errUhOh` to see if an “uh oh” error is the one that happened. Even if the typo is found and fixed, all the code would still be working because the error check is checking against the value of `errUhOh`, and the value of `errUhOh` is the fixed version of the error value that `giveMeError` is returning.

An error value that is intended to be checked and compared in this way is known as a _sentinel error_. A sentinel error is an error that’s designed to be a unique value that can always be compared against for a specific meaning. The `errUhOh` value above will always have the same meaning, that an “uh oh” error occurred, so a program can rely on comparing an error to `errUhOh` to determine whether that error occurred.

The Go standard library also defines a number of sentinel errors that are available when developing Go programs. One example would be the [`sql.ErrNoRows`](https://pkg.go.dev/database/sql#pkg-variables) error. The `sql.ErrNoRows` error is returned when a database query doesn’t return any results, so that error can be handled differently from a connection error. Since it’s a sentinel error, it can be compared against in error-checking code to know when a query doesn’t return any rows, and the program can handle that differently than other errors.

Generally, when creating a sentinel error value, the [`errors.New`](https://pkg.go.dev/errors#New) function from the [`errors`](https://pkg.go.dev/errors) package is used instead of the `fmt.Errorf` function you’ve been using thus far. Using `errors.New` instead of `fmt.Errorf` does not make any foundational changes to how the error works, though, and both functions could be used interchangeably most of the time. The biggest difference between the two is the `errors.New` function will only create an error with a static message and the `fmt.Errorf` function allows formatting the string with values, similar to `fmt.Printf` or `fmt.Sprintf`. Since sentinel errors are fundamental errors with values that don’t change, it’s common to use `errors.New` to create them.

Now, update your program to use a sentinel error for the “uh oh” error instead of `fmt.Errorf`.

First, open the `main.go` file to add the new `errUhOh` sentinel error and update the program to use it. The `validateValue` function is updated to return the sentinel error instead of using `fmt.Errorf`. The `main` function is updated to check for the `errUhOh` sentinel error and print `oh no!` when it encounters it instead of the `there was an error:` message it shows for other errors.

projects/errtutorial/main.go

```go
package main

import (
	"errors"
	"fmt"
)

var (
	errUhOh = errors.New("uh oh")
)

func validateValue(number int) error {
	if number == 1 {
		return fmt.Errorf("that's odd")
	} else if number == 2 {
		return errUhOh
	}
	return nil
}

func main() {
	for num := 1; num <= 3; num++ {
		fmt.Printf("validating %d... ", num)
		err := validateValue(num)
		if err == errUhOh {
			fmt.Println("oh no!")
		} else if err != nil {
			fmt.Println("there was an error:", err)
		} else {
			fmt.Println("valid!")
		}
	}
}
```

Now, save your code and use `go run` to run your program again:

```shell
go run main.go
```

This time the output will show the generic error output for the `1` value, but it uses the custom `oh no!` message when it sees the `errUhOh` error returned from `validateValue` for `2`:

```
Outputvalidating 1... there was an error: that's odd
validating 2... oh no!
validating 3... valid!
```

Using sentinel errors inside your error checking makes it easier to handle special error cases. For example, they can help determine whether the file you’re reading is failing because you’ve reached the end of the file, which is signified by the [`io.EOF`](https://pkg.go.dev/io#pkg-variables) sentinel error, or if it’s failing for some other reason.

In this section, you created a Go program that uses a sentinel error using `errors.New` to signify when a specific type of error occurred. Over time as your program grows, though, you may get to the point where you’d like more information included in your error than just the `uh oh` error value. This error value doesn’t give any context on where the error happened or why it happened, and it can be hard to track down specifics of the error in larger programs. To aid in troubleshooting and to cut down the time for debugging, you can make use of error wrapping to include the specifics you need.

## Wrapping and Unwrapping Errors

Wrapping errors means taking one error value and putting another error value inside it, like a wrapped gift. Similar to a wrapped gift, though, you need to unwrap it to know what’s inside. Wrapping an error allows you to include additional information about where the error came from or how it happened without losing the original error value, since it’s inside the wrapper.

Before Go 1.13, it was possible to wrap errors since you could create custom error values that included the original error. But you would either have to create your own wrappers or use a library that already did the work for you. In Go 1.13, though, Go added support for wrapping and unwrapping errors as part of the standard library by adding the [`errors.Unwrap`](https://pkg.go.dev/errors#Unwrap) function and the `%w` verb for the `fmt.Errorf` function. In this section, you’ll update your program to use the `%w` verb to wrap errors with more information, and you’ll then use `errors.Unwrap` to retrieve the wrapped information.

### Wrapping Errors with `fmt.Errorf`

The first feature to examine when wrapping and unwrapping errors is an addition to the existing `fmt.Errorf` function. In the past, `fmt.Errorf` was used to create formatted error messages with additional information using verbs such as `%s` for strings and `%v` for generic values. Go 1.13 added a new verb with a special case, the `%w` verb. When the `%w` verb is included in a format string and an `error` is provided for the value, the error returned from `fmt.Errorf` will include the value of the `error` wrapped in the error being created.

Now, open the `main.go` file and update it to include a new function called `runValidation`. This function will take the number currently being validated and run any validation needed on that number. In this case, it only needs to run the `validateValue` function. If it encounters an error validating the value it will wrap the error using `fmt.Errorf` and the `%w` verb to show there was a `run error` that occurred, then return that new error. You should also update the `main` function so instead of calling `validateValue` directly it calls `runValidation` instead:

projects/errtutorial/main.go

```go
...

var (
	errUhOh = errors.New("uh oh")
)

func runValidation(number int) error {
	err := validateValue(number)
	if err != nil {
		return fmt.Errorf("run error: %w", err)
	}
	return nil
}

...

func main() {
	for num := 1; num <= 3; num++ {
		fmt.Printf("validating %d... ", num)
		err := runValidation(num)
		if err == errUhOh {
			fmt.Println("oh no!")
		} else if err != nil {
			fmt.Println("there was an error:", err)
		} else {
			fmt.Println("valid!")
		}
	}
}
```

Once you’ve saved your updates, run the updated program using `go run`:

```shell
go run main.go
```

The output will look similar to this:

```
Output
validating 1... there was an error: run error: that's odd
validating 2... there was an error: run error: uh oh
validating 3... valid!
```

There are a few things to look at in this output. First, you’ll see the error message being printed for the value `1` now includes `run error: that's odd` in the error message. This shows the error was wrapped by `runValidation`’s `fmt.Errorf` and that the value of the error being wrapped, `that's odd`, is included in the error message.

Next, though, there’s a problem. The special error handling that was added for the `errUhOh` error isn’t running. If you look at the line validating the `2` input, you’ll see it shows the default error message of `there was an error: run error: uh oh` instead of the expected `oh no!` message. You know the `validateValue` function is still returning the `uh oh` error because you can see it at the end of the wrapped error, but the error detection of `errUhOh` is no longer working. This happens because the error being returned by `runValidation` is no longer `errUhOh`, it’s the wrapped error created by `fmt.Errorf`. When the `if` statement tries to compare the `err` variable to `errUhOh`, it returns false because `err` isn’t equal to `errUhOh` any more, it’s equal to the error that’s _wrapping_ `errUhOh`. To fix the `errUhOh` error checking, you’ll need to retrieve the error from inside the wrapper, using the `errors.Unwrap` function.

### Unwrapping Errors with `errors.Unwrap`

In addition to the `%w` verb being added in Go 1.13, a few new functions were added to the Go [`errors`](https://pkg.go.dev/errors) package. One of these, the `errors.Unwrap` function, takes an `error` as a parameter and, if the error passed in is an error wrapper, it will return the wrapped `error`. If the `error` provided isn’t a wrapper, the function will return `nil`.

Now, open the `main.go` file again and, using `errors.Unwrap`, update the `errUhOh` error check to handle the case where `errUhOh` is wrapped inside an error wrapper:

projects/errtutorial/main.go

```go
func main() {
	for num := 1; num <= 3; num++ {
		fmt.Printf("validating %d... ", num)
		err := runValidation(num)
		if err == errUhOh || errors.Unwrap(err) == errUhOh {
			fmt.Println("oh no!")
		} else if err != nil {
			fmt.Println("there was an error:", err)
		} else {
			fmt.Println("valid!")
		}
	}
}
```

After saving the edits, run the program again:

```shell
go run main.go
```

The output will look similar to this:

```
Outputvalidating 1... there was an error: run error: that's odd
validating 2... oh no!
validating 3... valid!
```

Now, in the output, you’ll see the `oh no!` error handling for the `2` input value is back. The additional `errors.Unwrap` function call you added to the `if` statement allows it to detect `errUhOh` both when `err` itself is an `errUhOh` value as well as if `err` is an error that is directly wrapping `errUhOh`.

In this section, you used the `%w` verb added to `fmt.Errorf` to wrap the `errUhOh` error inside another error and give it additional information. Then, you used `errors.Unwrap` to access the `errorUhOh` error that is wrapped inside another error. Including errors inside other errors as `string` values is OK for humans reading error messages, but sometimes you’ll want to include additional information with the error wrapper to aid the program in handling the error, such as the status code in an HTTP request error. When this happens, you can create a new custom error to return.

## Custom Wrapped Errors

Since Go’s only rule for the `error` interface is that it includes an `Error` method, it’s possible to turn many Go types into a custom error. One way is by defining a `struct` type with extra information about the error, and then also including an `Error` method.

For a validation error, it may be useful to know which value actually caused the error. Next, let’s create a new `ValueError` struct that contains a field for the `Value` that caused the error and an `Err` field that contains the actual validation error. Custom error types commonly use the `Error` suffix on the end of the type name to signify it’s a type that conforms to the `error` interface.

Open your `main.go` file and add the new `ValueError` error struct, as well as a `newValueError` function to create instances of the error. You will also need to create a method called `Error` for `ValueError` so the struct will be considered an `error`. This `Error` method should return the value you want to be displayed whenever the error is converted to a string. In this case, it will use `fmt.Sprintf` to return a string that shows `value error:` and then the wrapped error. Also, update the `validateValue` function so instead of returning just the basic error, it uses the `newValueError` function to return a custom error:

projects/errtutorial/main.go

```go
...

var (
	errUhOh = fmt.Errorf("uh oh")
)

type ValueError struct {
	Value int
	Err   error
}

func newValueError(value int, err error) *ValueError {
	return &ValueError{
		Value: value,
		Err:   err,
	}
}

func (ve *ValueError) Error() string {
	return fmt.Sprintf("value error: %s", ve.Err)
}

...

func validateValue(number int) error {
	if number == 1 {
		return newValueError(number, fmt.Errorf("that's odd"))
	} else if number == 2 {
		return newValueError(number, errUhOh)
	}
	return nil
}

...
```

Once your updates are saved, run the program again with `go run`:

```shell
go run main.go
```

The output will look similar to this:

```
Outputvalidating 1... there was an error: run error: value error: that's odd
validating 2... there was an error: run error: value error: uh oh
validating 3... valid!
```

You’ll see that the output now shows the errors are wrapped inside of `ValueError` by the `value error:` before them in the output. However, the `uh oh` error detection is broken again because `errUhOh` is now inside two layers of wrappers, `ValueError` and the `fmt.Errorf` wrapper from `runValidation`. The code code only uses `errors.Unwrap` once on the error, so this results in the first `errors.Unwrap(err)` now only returning a `*ValueError` and not `errUhOh`.

One way to fix this would be to update the `errUhOh` check to add an additional error check that calls `errors.Unwrap()` twice to unwrap both layers. To add this, open your `main.go` file and update your `main` function to include this change:

projects/errtutorial/main.go

```shell
...

func main() {
	for num := 1; num <= 3; num++ {
		fmt.Printf("validating %d... ", num)
		err := runValidation(num)
		if err == errUhOh ||
			errors.Unwrap(err) == errUhOh ||
			errors.Unwrap(errors.Unwrap(err)) == errUhOh {
			fmt.Println("oh no!")
		} else if err != nil {
			fmt.Println("there was an error:", err)
		} else {
			fmt.Println("valid!")
		}
	}
}
```

Now, save your `main.go` file and use `go run` to run your program again:

```shell
go run main.go
```

The output will look similar to this:

```
Outputvalidating 1... there was an error: run error: value error: that's odd
validating 2... there was an error: run error: value error: uh oh
validating 3... valid!
```

You’ll see that, uh oh, the `errUhOh` special error handling is still not working. The line validating the `2` input where we’d expect to see the special error handling `oh no!` output still shows the default `there was an error: run error: ...` error output. This happens because the `errors.Unwrap` function doesn’t know how to unwrap the `ValueError` custom error type. In order for a custom error to be unwrapped, it needs to have its own `Unwrap` method that returns the inner error as an `error` value. When creating errors using `fmt.Errorf` with the `%w` verb earlier, Go was actually creating an error for you that already has an `Unwrap` method added, so you didn’t need to do it yourself. Now that you’re using your own custom function, though, you need to add your own.

To finally fix the `errUhOh` error case, open `main.go` and add an `Unwrap` method to `ValueError` that returns `Err`, the field the inner wrapped error is stored in:

projects/errtutorial/main.go

```shell
...

func (ve *ValueError) Error() string {
	return fmt.Sprintf("value error: %s", ve.Err)
}

func (ve *ValueError) Unwrap() error {
	return ve.Err
}

...
```

Then, once you’ve saved the new `Unwrap` method, run your program:

```shell
go run main.go
```

The output will look similar to this:

```
Outputvalidating 1... there was an error: run error: value error: that's odd
validating 2... oh no!
validating 3... valid!
```

The output shows the `oh no!` error handling for the `errUhOh` error is working again because `errors.Unwrap` is now able to also unwrap `ValueError`.

In this section you created a new, custom `ValueError` error to provide yourself or your users with information about the validation process as part of the error message. You also added support for error unwrapping to your `ValueError` so `errors.Unwrap` can be used to access the wrapped error.

The error handling is getting a bit clunky and hard to maintain, though. Every time there’s a new layer of wrapping you’ve had to add another `errors.Unwrap` to the error checking to handle it. Thankfully, the `errors.Is` and `errors.As` functions in the `errors` package are available to make working with wrapped errors easier.

## Working with Wrapped Errors

If you needed to add a new `errors.Unwrap` function call for every potential layer of error wrapping your program had, it would get very long and difficult to maintain. For this reason, two additional functions were also added to the `errors` package in the Go 1.13 release. Both of these functions make it easier to work with errors by allowing you to interact with errors no matter how deeply they’re wrapped inside other errors. The `errors.Is` function allows you to check if a specific sentinel error value is anywhere inside a wrapped error. The `errors.As` function allows you to get a reference to a certain type of error anywhere inside a wrapped error.

### Checking an Error Value with `errors.Is`

Using `errors.Is` to check for a specific error makes the `errUhOh` special error handling much shorter because it handles all the nested error unwrapping you were doing manually. The function takes two `error` parameters, the first being the error you actually received and the second parameter being the error you want to check against.

To clean up the `errUhOh` error handling, open your `main.go` file and update the `errUhOh` check in the `main` function to use `errors.Is` instead:

projects/errtutorial/main.go

```go
...

func main() {
	for num := 1; num <= 3; num++ {
		fmt.Printf("validating %d... ", num)
		err := runValidation(num)
		if errors.Is(err, errUhOh) {
			fmt.Println("oh no!")
		} else if err != nil {
			fmt.Println("there was an error:", err)
		} else {
			fmt.Println("valid!")
		}
	}
}
```

Then, save your code and run the program again using `go run`:

```shell
go run main.go
```

The output will look similar to this:

```
Outputvalidating 1... there was an error: run error: value error: that's odd
validating 2... oh no!
validating 3... valid!
```

The output shows the `oh no!` error message, which means that even though there’s only one error check for `errUhOh`, it will still be found in the error chain. `errors.Is` takes advantage of an error type’s `Unwrap` method to keep digging deeper into a chain of errors until it either finds the error value you’re looking for, a sentinel error, or encounters an `Unwrap` method that returns a `nil` value.

Using `errors.Is` is the recommended way to check for specific errors now that error wrapping exists as a feature in Go. Not only can it be used for your own error values, but it can also be used for other error values such as the `sql.ErrNoRows` error mentioned earlier in this tutorial.

### Retrieving an Error Type with `errors.As`

The last function added to the `errors` package in Go 1.13 is the `errors.As` function. This function is used when you want to get a reference to a certain type of error to interact with it in more detail. For example, the `ValueError` custom error you added earlier gives access to the actual value being validated in the `Value` field of the error, but you can only access it if you have a reference to that error first. This is where `errors.As` comes in. You can give `errors.As` an error, similar to `errors.Is`, and a variable for a type of error. It will then go through the error chain to see if any of the wrapped errors match the type provided. If one matches, the variable passed in for the error type will be set with the error `errors.As` found, and the function will return `true`. If no error types match, it will return `false`.

Using `errors.As` you can now take advantage of the `ValueError` type to show additional error information in your error handler. Open your `main.go` file one last time and update the `main` function to add a new error handling case for `ValueError`\-type errors that prints out `value error`, the invalid number, and the validation error:

projects/errtutorial/main.go

```shell
...

func main() {
	for num := 1; num <= 3; num++ {
		fmt.Printf("validating %d... ", num)
		err := runValidation(num)

		var valueErr *ValueError
		if errors.Is(err, errUhOh) {
			fmt.Println("oh no!")
		} else if errors.As(err, &valueErr) {
			fmt.Printf("value error (%d): %v\n", valueErr.Value, valueErr.Err)
		} else if err != nil {
			fmt.Println("there was an error:", err)
		} else {
			fmt.Println("valid!")
		}
	}
}
```

In the code above, you declared a new `valueErr` variable and used `errors.As` to get a reference to the `ValueError` if it’s wrapped inside the `err` value. By getting access to the error as a `ValueError`, you’re then able to access any additional fields the type provides, such as the actual value that failed validation. This could be helpful if the validation logic happens deeper inside the program and you don’t normally have access to the values to give users hints on where something might have gone wrong. Another example of where this could be helpful is if you’re doing network programming and run into a [`net.DNSError`](https://pkg.go.dev/net#DNSError). By getting a reference to the error, you are able to see if the error was the result of not being able to connect, or if the error was caused by being able to connect, but your resource was not found. Once you know this, you can handle the error in different ways.

To see `errors.As` in action, save your file and run the program using `go run`:

```shell
go run main.go
```

The output will look similar to this:

```
Outputvalidating 1... value error (1): that's odd
validating 2... oh no!
validating 3... valid!
```

This time in the output you won’t see the default `there was an error: ...` message, because all the errors are being handled by other error handlers. The output for validating `1` shows that the `errors.As` error check returned `true` because the `value error ...` error message is being displayed. Since the `errors.As` function returned true, the `valueErr` variable is set to be a `ValueError` and can be used to print out the value that failed validation by accessing `valueErr.Value`.

For the `2` value, the output also shows that even though the `errUhOh` is also wrapped inside a `ValueError` wrapper, the `oh no!` special error handler is still executed. This is because the special error handler using `errors.Is` for `errUhOh` comes first in the collection of `if` statements handling the errors. Since this handler returns `true` before the `errors.As` even runs, the special `oh no!` handler is the one executed. If the `errors.As` in your code came before the `errors.Is`, the `oh no!` error message would become the same `value error ...` as the `1` value, except in this case it would print `value error (2): uh oh`.

In this section, you updated your program to use the `errors.Is` function to remove a lot of additional calls to `errors.Unwrap` and make your error handling code more robust and future-proof. You also used the `errors.As` function to check if any of the wrapped errors is a `ValueError`, and then used fields on the value if one was found.

## Conclusion

In this tutorial, you wrapped an error using the `%w` format verb and unwrapped an error using `errors.Unwrap`. You also created a custom error type that supports `errors.Unwrap` in your own code. Finally, you used your custom error type to explore the new helper functions `errors.Is` and `errors.As`.

Using these new error functions makes it easier to include deeper information about the errors you create or work with. It also future proofs your code to ensure your error checking continues to work even if errors become deeply nested going forward.

If you’d like to find more details about how to use the new error features, the Go blog has a post about [Working with Errors in Go 1.13](https://go.dev/blog/go1.13-errors). The documentation for the [`errors` package](https://pkg.go.dev/errors) package also includes more information.

This tutorial is also part of the [DigitalOcean](https://www.digitalocean.com/) [How to Code in Go](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go) series. The series covers a number of Go topics, from installing Go for the first time to how to use the language itself.
