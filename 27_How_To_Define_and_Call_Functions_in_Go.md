### Introduction

A _function_ is a section of code that, once defined, can be reused. Functions are used to make your code easier to understand by breaking it into small, understandable tasks that can be used more than once throughout your program.

Go ships with a powerful standard library that has many predefined functions. Ones that you are probably already familiar with from the [fmt](https://golang.org/pkg/fmt/) package are:

-   `fmt.Println()` which will print objects to standard out (most likely your terminal).
-   `fmt.Printf()` which will allow you to format your printed output.

Function names include parentheses and may include parameters.

In this tutorial, we’ll go over how to define your own functions to use in your coding projects.

## Defining a Function

Let’s start with turning the classic [“Hello, World!” program](https://www.digitalocean.com/community/tutorials/how-to-write-your-first-program-in-go) into a function.

We’ll create a new text file in our text editor of choice, and call the program `hello.go`. Then, we’ll define the function.

A function is defined by using the `func` keyword. This is then followed by a name of your choosing and a set of parentheses that hold any parameters the function will take (they can be empty). The lines of function code are enclosed in curly brackets `{}`.

In this case, we’ll define a function named `hello()`:

hello.go

This sets up the initial statement for creating a function.

From here, we’ll add a second line to provide the instructions for what the function does. In this case, we’ll be printing `Hello, World!` to the console:

hello.go

Our function is now fully defined, but if we run the program at this point, nothing will happen since we didn’t call the function.

So, inside of our `main()` function block, let’s call the function with `hello()`:

hello.go

Now, let’s run the program:

You’ll receive the following output:

```
OutputHello, World!
```

Notice that we also introduced a function called `main()`. The `main()` function is a special function that tells the compiler that this is where the program should **start**. For any program that you want to be _executable_ (a program that can be run from the command line), you will need a `main()` function. The `main()` function must appear only once, be in the `main()` _package_, and receive and return no arguments. This allows for [program execution](https://golang.org/ref/spec#Program_execution) in any Go program. As per the following example:

main.go

Functions can be more complicated than the `hello()` function we defined. We can use [`for` loops](https://www.digitalocean.com/community/tutorials/how-to-construct-for-loops-in-go), [conditional statements](https://www.digitalocean.com/community/tutorials/how-to-write-conditional-statements-in-go), and more within our function block.

For example, the following function uses a conditional statement to check if the input for the `name` variable contains a vowel, then uses a `for` loop to iterate over the letters in the `name` string.

names.go

The `names()` function we define here sets up a `name` variable with input, and then sets up a conditional statement within a `for` loop. This shows how code can be organized within a function definition. However, depending on what we intend with our program and how we want to set up our code, we may want to define the conditional statement and the `for` loop as two separate functions.

Defining functions within a program makes our code modular and reusable so that we can call the same functions without rewriting them.

## Working with Parameters

So far we have looked at functions with empty parentheses that do not take arguments, but we can define parameters in function definitions within their parentheses.

A _parameter_ is a named entity in a function definition, specifying an argument that the function can accept. In Go, you must specify the [data type](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go) for each parameter.

Let’s create a program that repeats a word a specified number of times. It will take a `string` parameter called `word` and an `int` parameter called `reps` for the number of times to repeat the word.

repeat.go

We passed the value `Sammy` in for the `word` parameter, and `5` for the `reps` parameter. These values correspond with each parameter in the order they were given. The `repeat` function has a `for` loop that will iterate the number of times specified by the `reps` parameter. For each iteration, the value of the `word` parameter is printed.

Here is the output of the program:

```
OutputSammySammySammySammySammy
```

If you have a set of parameters that are all the same value, you can omit specifying the type each time. Let’s create a small program that takes in parameters `x`, `y`, and `z` that are all `int` values. We’ll create a function that adds the parameters together in different configurations. The sums of these will be printed by the function. Then we’ll call the function and pass numbers into the function.

add\_numbers.go

When we created the function signature for `addNumbers`, we did not need to specify the type each time, but only at the end.

We passed the number `1` in for the `x` parameter, `2` in for the `y` parameter, and `3` in for the `z` parameter. These values correspond with each parameter in the order they are given.

The program is doing the following math based on the values we passed to the parameters:

```
a = 1 + 2
b = 1 + 3
c = 2 + 3
```

The function also prints `a`, `b`, and `c`, and based on this math we would expect `a` to be equal to `3`, `b` to be `4`, and `c` to be `5`. Let’s run the program:

```
Output3 4 5
```

When we pass `1`, `2`, and `3` as parameters to the `addNumbers()` function, we receive the expected output.

Parameters are arguments that are typically defined as variables within function definitions. They can be assigned values when you run the method, passing the arguments into the function.

## Returning a Value

You can pass a parameter value into a function, and a function can also produce a value.

A function can produce a value with the `return` statement, which will exit a function and _optionally_ pass an expression back to the caller. The return data type must be specified as well.

So far, we have used the `fmt.Println()` statement instead of the `return` statement in our functions. Let’s create a program that instead of printing will return a variable.

In a new text file called `double.go`, we’ll create a program that doubles the parameter `x` and returns the variable `y`. We issue a call to print the `result` variable, which is formed by running the `double()` function with `3` passed into it:

double.go

We can run the program and see the output:

```
Output6
```

The integer `6` is returned as output, which is what we would expect by multiplying `3` by `2`.

If a function specifies a return, you must provide a return as part of the code. If you do not, you will receive a compilation error.

We can demonstrate this by commenting out the line with the return statement:

double.go

Now, let’s run the program again:

```
Output./double.go:13:1: missing return at end of function
```

Without using the `return` statement here, the program cannot compile.

Functions exit immediately when they hit a `return` statement, even if they are not at the end of the function:

return\_loop.go

Here we iterate through a `for` loop, and tell the loop to run `25` iterations. However, inside the `for` loop, we have a conditional `if` statement that checks to see if the value of `i` is equal to `5`. If it is, we issue a `return` statement. Because we are in the `loopFive` function, any `return` at any point in the function will exit the function. As a result, we never get to the last line in this function to print the statement `This line will not execute.`.

Using the `return` statement within the `for` loop ends the function, so the line that is outside of the loop will not run. If, instead, we had used a [`break` statement](https://www.digitalocean.com/community/tutorials/how-to-use-break-and-continue-statements-when-working-with-loops-in-go#break-statement), only the loop would have exited at that time, and the last `fmt.Println()` line would run.

The `return` statement exits a function, and may return a value if specified in the function signature.

## Returning Multiple Values

More than one return value can be specified for a function. Let’s examine the `repeat.go` program and make it return two values. The first will be the repeated value and the second will be an error if the `reps` parameter is not a value greater than `0`:

repeat.go

The first thing the `repeat` function does is check to see if the `reps` argument is a valid value. Any value that is not greater than `0` will cause an error. Since we passed in the value of `-1`, this branch of code will execute. Notice that when we return from the function, we have to provide both the `string` and `error` return values. Because the provided arguments resulted in an error, we will pass back a blank string for the first return value, and the error for the second return value.

In the `main()` function, we can receive both return values by declaring two new variables, `value` and `err`. Because there could be an error in the return, we want to check to see if we received an error before continuing on with our program. In this example, we did receive an error. We print out the error and `return` out of the `main()` function to exit the program.

If there was not an error, we would print out the return value of the function.

**Note:** It is considered best practice to only return two or three values. Additionally, you should always return all errors as the last return value from a function.

Running the program will result in the following output:

```
Outputinvalid value of -1 provided for reps. value must be greater than 0.
```

In this section we reviewed how we can use the `return` statement to return multiple values from a function.

## Conclusion

Functions are code blocks of instructions that perform actions within a program, helping to make our code reusable and modular.

To learn more about how to make your code more modular, you can read our guide on [How To Write Packages in Go](https://www.digitalocean.com/community/tutorials/how-to-write-packages-in-go).