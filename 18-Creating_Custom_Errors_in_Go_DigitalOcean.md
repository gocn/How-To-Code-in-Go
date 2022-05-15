### Introduction

Go provides two methods to create errors in the standard library, [`errors.New` and `fmt.Errorf`](https://www.digitalocean.com/community/tutorials/handling-errors-in-go#creating-errors). When communicating more complicated error information to your users, or to your future self when debugging, sometimes these two mechanisms are not enough to adequately capture and report what has happened. To convey this more complex error information and attain more functionality, we can implement the standard library interface type, [`error`](https://golang.org/pkg/builtin/#error).

The syntax for this would be as follows:

``` go
type error interface {
  Error() string
}

```

The [`builtin`](https://golang.org/pkg/builtin/) package defines `error` as an interface with a single `Error()` method that returns an error message as a string. By implementing this method, we can transform any type we define into an error of our own.

Let’s try running the following example to see an implementation of the `error` interface:

``` go
package main

import (
	"fmt"
	"os"
)

type MyError struct{}

func (m *MyError) Error() string {
	return "boom"
}

func sayHello() (string, error) {
	return "", &MyError{}
}

func main() {
	s, err := sayHello()
	if err != nil {
		fmt.Println("unexpected error: err:", err)
		os.Exit(1)
	}
	fmt.Println("The string:", s)
}

```

We’ll see the following output:

``` shell
# Output
unexpected error: err: boom
exit status 1
```

Here we’ve created a new empty struct type, `MyError`, and defined the `Error()` method on it. The `Error()` method returns the string `"boom"`.

Within `main()`, we call the function `sayHello` that returns an empty string and a new instance of `MyError`. Since `sayHello` will always return an error, the `fmt.Println` invocation within the body of the if statement in `main()` will always execute. We then use `fmt.Println` to print the short prefix string `"unexpected error:"` along with the instance of `MyError` held within the `err` variable.

Notice that we don’t have to directly call `Error()`, since the `fmt` package is able to automatically detect that this is an implementation of `error`. It calls `Error()` [transparently](https://en.wikipedia.org/wiki/Transparency_(human%E2%80%93computer_interaction)) to get the string `"boom"` and concatenates it with the prefix string `"unexpected error: err:"`.

## Collecting Detailed Information in a Custom Error

Sometimes a custom error is the cleanest way to capture detailed error information. For example, let’s say we want to capture the status code for errors produced by an HTTP request; run the following program to see an implementation of `error` that allows us to cleanly capture that information:

``` go
package main

import (
	"errors"
	"fmt"
	"os"
)

type RequestError struct {
	StatusCode int

	Err error
}

func (r *RequestError) Error() string {
	return fmt.Sprintf("status %d: err %v", r.StatusCode, r.Err)
}

func doRequest() error {
	return &RequestError{
		StatusCode: 503,
		Err:        errors.New("unavailable"),
	}
}

func main() {
	err := doRequest()
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
	fmt.Println("success!")
}

```

We will see the following output:

``` shell
# Output
status 503: err unavailable
exit status 1
```

In this example, we create a new instance of `RequestError` and provide the status code and an error using the `errors.New` function from the standard library. We then print this using `fmt.Println` as in previous examples.

Within the `Error()` method of `RequestError`, we use the `fmt.Sprintf` function to construct a string using the information provided when the error was created.

## Type Assertions and Custom Errors

The `error` interface exposes only one method, but we may need to access the other methods of `error` implementations to handle an error properly. For example, we may have several custom implementations of `error` that are temporary and can be retried—denoted by the presence of a `Temporary()` method.

Interfaces provide a narrow view into the wider set of methods provided by types, so we must use a _type assertion_ to change the methods that view is displaying, or to remove it entirely.

The following example augments the `RequestError` shown previously to have a `Temporary()` method which will indicate whether or not callers should retry the request:

``` go
package main

import (
	"errors"
	"fmt"
	"net/http"
	"os"
)

type RequestError struct {
	StatusCode int

	Err error
}

func (r *RequestError) Error() string {
	return r.Err.Error()
}

func (r *RequestError) Temporary() bool {
	return r.StatusCode == http.StatusServiceUnavailable // 503
}

func doRequest() error {
	return &RequestError{
		StatusCode: 503,
		Err:        errors.New("unavailable"),
	}
}

func main() {
	err := doRequest()
	if err != nil {
		fmt.Println(err)
		re, ok := err.(*RequestError)
		if ok {
			if re.Temporary() {
				fmt.Println("This request can be tried again")
			} else {
				fmt.Println("This request cannot be tried again")
			}
		}
		os.Exit(1)
	}

	fmt.Println("success!")
}

```
We will see the following output:

``` shell
# Output
unavailable
This request can be tried again
exit status 1
```

Within `main()`, we call `doRequest()` which returns an `error` interface to us. We first print the error message returned by the `Error()` method. Next, we attempt to expose all methods from `RequestError` by using the type assertion `re, ok := err.(*RequestError)`. If the type assertion succeeded, we then use the `Temporary()` method to see if this error is a temporary error. Since the `StatusCode` set by `doRequest()` is `503`, which matches `http.StatusServiceUnavailable`, this returns `true` and causes `"This request can be tried again"` to be printed. In practice, we would instead make another request rather than printing a message.

## Wrapping Errors

Commonly, an error will be generated from something outside of your program such as: a database, a network connection, etc. The error messages provided from these errors don’t help anyone find the origin of the error. Wrapping errors with extra information at the beginning of an error message would provide some needed context for successful debugging.

The following example demonstrates how we can attach some contextual information to an otherwise cryptic `error` returned from some other function:

``` go
package main

import (
	"errors"
	"fmt"
)

type WrappedError struct {
	Context string
	Err     error
}

func (w *WrappedError) Error() string {
	return fmt.Sprintf("%s: %v", w.Context, w.Err)
}

func Wrap(err error, info string) *WrappedError {
	return &WrappedError{
		Context: info,
		Err:     err,
	}
}

func main() {
	err := errors.New("boom!")
	err = Wrap(err, "main")

	fmt.Println(err)
}

```

We will see the following output:

``` shell
# Output
main: boom!
```

`WrappedError` is a struct with two fields: a context message as a `string`, and an `error` that this `WrappedError` is providing more information about. When the `Error()` method is invoked, we again use `fmt.Sprintf` to print the context message, then the `error` (`fmt.Sprintf` knows to implicitly call the `Error()` method as well).

Within `main()`, we create an error using `errors.New`, and then we wrap that error using the `Wrap` function we defined. This allows us to indicate that this `error` was generated in `"main"`. Also, since our `WrappedError` is also an `error`, we could wrap other `WrappedError`s—this would allow us to see a chain to help us track down the source of the error. With a little help from the standard library, we can even embed complete stack traces in our errors.

## Conclusion

Since the `error` interface is only a single method, we’ve seen that we have great flexibility in providing different types of errors for different situations. This can encompass everything from communicating multiple pieces of information as part of an error all the way to implementing [exponential backoff](https://en.wikipedia.org/wiki/Exponential_backoff). While the error handling mechanisms in Go might on the surface seem simplistic, we can achieve quite rich handling using these custom errors to handle both common and uncommon situations.

Go has another mechanism to communicate unexpected behavior, panics. In our next article in the error handling series, we will examine panics—what they are and how to handle them.
