Robust code needs to react correctly to unexpected circumstances like bad user input, faulty network connections, and failing disks. _Error handling_ is the process of identifying when your program is in an unexpected state, and taking steps to record diagnostic information for later debugging.

Unlike other languages that require developers to handle errors with specialized syntax, errors in Go are values with the type `error` returned from functions like any other value. To handle errors in Go, we must examine these errors that functions could return, decide if an error has occurred, and take proper action to protect data and tell users or operators that the error occurred.

## Creating Errors

Before we can handle errors, we need to create some first. The standard library provides two built-in functions to create errors: `errors.New` and `fmt.Errorf`. Both of these functions allow you to specify a custom error message that you can later present to your users.

`errors.New` takes a single argument—an error message as a string that you can customize to alert your users what went wrong.

Try running the following example to see an error created by `errors.New` printed to standard output:

``` shell
OutputSammy says: barnacles
```

We used the `errors.New` function from the standard library to create a new error message with the string `"barnacles"` as the error message. We’ve followed convention here by using lowercase for the error message as the [Go Programming Language Style Guide](https://github.com/golang/go/wiki/CodeReviewComments#error-strings) suggests.

Finally, we used the `fmt.Println` function to combine our error message with `"Sammy says:"`.

The `fmt.Errorf` function allows you to dynamically build an error message. Its first argument is a string containing your error message with placeholder values such as `%s` for a string and `%d` for an integer. `fmt.Errorf` interpolates the arguments that follow this formatting string into those placeholders in order:

``` shell
OutputAn error happened: Error occurred at: 2019-07-11 16:52:42.532621 -0400 EDT m=+0.000137103
```

We used the `fmt.Errorf` function to build an error message that would include the current time. The formatting string we provided to `fmt.Errorf` contains the `%v` formatting directive that tells `fmt.Errorf` to use the default formatting for the first argument provided after the formatting string. That argument will be the current time, provided by the `time.Now` function from the standard library. Similarly to the earlier example, we combine our error message with a short prefix and print the result to standard output using the `fmt.Println` function.

## Handling Errors

Typically you wouldn’t see an error created like this to be used immediately for no other purpose, as in the previous example. In practice, it’s far more common to create an error and return it from a function when something goes wrong. Callers of that function will then use an `if` statement to see if the error was present or `nil`—an uninitialized value.

This next example includes a function that always returns an error. Notice when you run the program that it produces the same output as the previous example even though a function is returning the error this time. Declaring an error in a different location does not change the error’s message.

``` shell
OutputAn error occurred: barnacles
```

Here we define a function called `boom()` that returns a single `error` that we construct using `errors.New`. We then call this function and capture the error with the line `err := boom()`. Once we assign this error, we check to see if it was present with the `if err != nil` conditional. Here the conditional will always evaluate to `true`, since we are always returning an `error` from `boom()`.

This won’t always be the case, so it’s good practice to have logic handling cases where an error is not present (`nil`) and cases where the error is present. When the error is present, we use `fmt.Println` to print our error along with a prefix as we have done in earlier examples. Finally, we use a `return` statement to skip the execution of `fmt.Println("Anchors away!")`, since that should only execute when no error occurred.

**Note:** The `if err != nil` construction shown in the last example is the workhorse of error handling in the Go programming language. Wherever a function could produce an error, it’s important to use an `if` statement to check whether one occurred. In this way, idiomatic Go code naturally has its [“happy path”](https://en.wikipedia.org/wiki/Happy_path) logic at the first indent level, and all the “sad path” logic at the second indent level.

If statements have an optional assignment clause that can be used to help condense calling a function and handling its errors.

Run the next program to see the same output as our earlier example, but this time using a compound `if` statement to reduce some boilerplate:

``` shell
OutputAn error occurred: barnacles
```

As before, we have a function, `boom()`, that always returns an error. We assign the error returned from `boom()` to `err` as the first part of the `if` statement. In the second part of the `if` statement, following the semicolon, that `err` variable is then available. We check to see if the error was present and print our error with a short prefix string as we’ve done previously.

In this section, we learned how to handle functions that only return an error. These functions are common, but it’s also important to be able to handle errors from functions that can return multiple values.

## Returning Errors Alongside Values

Functions that return a single error value are often those that effect some stateful change, like inserting rows to a database. It’s also common to write functions that return a value if they completed successfully along with a potential error if that function failed. Go permits functions to return more than one result, which can be used to simultaneously return a value and an error type.

To create a function that returns more than one value, we list the types of each returned value inside parentheses in the signature for the function. For example, a `capitalize` function that returns a `string` and an `error` would be declared using `func capitalize(name string) (string, error) {}`. The `(string, error)` part tells the Go compiler that this function will return a `string` and an `error`, in that order.

Run the following program to see the output from a function that returns both a `string` and an `error`:

``` shell
OutputCapitalized name: SAMMY
```

We define `capitalize()` as a function that takes a string (the name to be capitalized) and returns a string and an error value. In `main()`, we call `capitalize()` and assign the two values returned from the function to the `name` and `err` variables by separating them with commas on the left-hand side of the `:=` operator. After this, we perform our `if err != nil` check as in earlier examples, printing the error to standard output using `fmt.Println` if the error was present. If no error was present, we print `Capitalized name: SAMMY`.

Try changing the string `"sammy"` in `name, err := capitalize("sammy")` to the empty string `("")` and you’ll receive the error `Could not capitalize: no name provided` instead.

The `capitalize` function will return an error when callers of the function provide an empty string for the `name` parameter. When the `name` parameter is not the empty string, `capitalize()` uses `strings.ToTitle` to capitalize the `name` parameter and returns `nil` for the error value.

There are some subtle conventions that this example follows that is typical of Go code, yet not enforced by the Go compiler. When a function returns multiple values, including an error, convention requests that we return the `error` as the last item. When returning an `error` from a function with multiple return values, idiomatic Go code also will set each non-error value to a _zero value_. Zero values are, for example, an empty string for strings, `0` for integers, an empty struct for struct types, and `nil` for interface and pointer types, to name a few. We cover zero values in more detail in our [tutorial on variables and constants](https://www.digitalocean.com/community/tutorials/how-to-use-variables-and-constants-in-go#zero-values).

### Reducing boilerplate

Adhering to these conventions can become tedious in situations where there are many values to return from a function. We can use an [_anonymous function_](https://en.wikipedia.org/wiki/Anonymous_function) to help reduce the boilerplate. Anonymous functions are procedures assigned to variables. In contrast to the functions we have defined in earlier examples, they are only available within the functions where you declare them—this makes them perfect to act as short pieces of reusable helper logic.

The following program modifies the last example to include the length of the name that we’re capitalizing. Since it has three values to return, handling errors could become cumbersome without an anonymous function to assist us:

``` shell
OutputCapitalized name: SAMMY, length: 5
```

Within `main()`, we now capture the three returned arguments from `capitalize` as `name`, `size`, and `err`, respectively. We then check to see if `capitalize` returned an `error` by checking if the `err` variable was not equal to `nil`. This is important to do before attempting to use any of the other values returned by `capitalize`, because the anonymous function, `handle`, could set those to zero values. Since no error occurred because we provided the string `"sammy"`, we print out the capitalized name and its length.

Once again, you can try changing `"sammy"` to the empty string `("")` to see the error case printed (`An error occurred: no name provided`).

Within `capitalize`, we define the `handle` variable as an anonymous function. It takes a single error and returns identical values in the same order as the return values of `capitalize`. `handle` sets those values to zero values and forwards the `error` passed as its argument as the final return value. Using this, we can then return any errors encountered in `capitalize` by using the `return` statement in front of the call to `handle` with the `error` as its parameter.

Remember that `capitalize` must return three values all the time, since that’s how we defined the function. Sometimes we don’t want to deal with all the values that a function could return. Fortunately, we have some flexibility in how we can use these values on the assignment side.

## Handling Errors from Multi-Return Functions

When a function returns many values, Go requires us to assign each to a variable. In the last example, we do this by providing names for the two values returned from the `capitalize` function. These names should be separated by commas and appear on the left-hand side of the `:=` operator. The first value returned from `capitalize` will be assigned to the `name` variable, and the second value (the `error`) will be assigned to the variable `err`. Occasionally, we’re only interested in the error value. You can discard any unwanted values that functions return using the special `_` variable name.

In the following program, we’ve modified our first example involving the `capitalize` function to produce an error by passing in the empty string `("")`. Try running this program to see how we’re able to examine just the error by discarding the first returned value with the `_` variable:

``` shell
OutputCould not capitalize: no name provided
```

Within the `main()` function this time, we assign the capitalized name (the `string` returned first) to the underscore variable (`_`). At the same time, we assign the `error` returned by `capitalize` to the `err` variable. We then check if the error was present in the `if err != nil` conditional. Since we have hard-coded an empty string as an argument to `capitalize` in the line `_, err := capitalize("")`, this conditional will always evaluate to `true`. This produces the output `"Could not capitalize: no name provided"` printed by the call to the `fmt.Println` function within the body of the `if` statement. The `return` after this will skip the `fmt.Println("Success!")`.

## Conclusion

We’ve seen many ways to create errors using the standard library and how to build functions that return errors in an idiomatic way. In this tutorial, we’ve managed to successfully create various errors using the standard library `errors.New` and `fmt.Errorf` functions. In future tutorials, we’ll look at how to create our own custom error types to convey richer information to users.
