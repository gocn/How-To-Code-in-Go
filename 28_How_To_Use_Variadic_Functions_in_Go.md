### Introduction

A _variadic function_ is a function that accepts zero, one, or more values as a single argument. While variadic functions are not the common case, they can be used to make your code cleaner and more readable.

Variadic functions are more common than they seem. The most common one is the `Println` function from the [`fmt`](https://golang.org/pkg/fmt) package.

A [function](https://www.digitalocean.com/community/tutorials/how-to-define-and-call-functions-in-go) with a parameter that is preceded with a set of ellipses (`...`) is considered a variadic function. The ellipsis means that the parameter provided can be zero, one, or more values. For the `fmt.Println` package, it is stating that the parameter `a` is variadic.

Let’s create a program that uses the `fmt.Println` function and pass in zero, one, or more values:

print.go

The first time we call `fmt.Println`, we don’t pass any arguments. The second time we call `fmt.Println` we pass in only a single argument, with the value of `one`. Then we pass `one` and `two`, and finally `one`, `two`, and `three`.

Let’s run the program with the following command:

We’ll see the following output:

```
Output
one
one two
one two three
```

The first line of the output is blank. This is because we didn’t pass any arguments the first time `fmt.Println` was called. The second time the value of `one` was printed. Then `one` and `two`, and finally `one`, `two`, and `three`.

Now that we have seen how to call a variadic function, let’s look at how we can define our own variadic function.

## Defining a Variadic Function

We can define a variadic function by using an ellipsis (`...`) in front of the argument. Let’s create a program that greets people when their names are sent to the function:

hello.go

We created a `sayHello` function that takes only a single parameter called `names`. The parameter is variadic, as we put an ellipsis (`...`) before the data type: `...string`. This tells Go that the function can accept zero, one, or many arguments.

The `sayHello` function receives the `names` parameter as a [`slice`](https://www.digitalocean.com/community/tutorials/understanding-arrays-and-slices-in-go#slices). Since the data type is a [`string`](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#strings), the `names` parameter can be treated just like a slice of strings (`[]string`) inside the function body. We can create a loop with the [`range`](https://www.digitalocean.com/community/tutorials/how-to-construct-for-loops-in-go#looping-through-sequential-data-types-with-rangeclause) operator and iterate through the slice of strings.

If we run the program, we will get the following output:

```
OutputHello Sammy
Hello Sammy
Hello Jessica
Hello Drew
Hello Jamie
```

Notice that nothing printed for the first time we called `sayHello`. This is because the variadic parameter was an empty `slice` of `string`. Since we are looping through the slice, there is nothing to iterate through, and `fmt.Printf` is never called.

Let’s modify the program to detect that no values were sent in:

hello.go

Now, by using an [`if` statement](https://www.digitalocean.com/community/tutorials/how-to-write-conditional-statements-in-go#if-statements), if no values are passed, the length of `names` will be `0`, and we will print out `nobody to greet`:

```
Outputnobody to greet
Hello Sammy
Hello Sammy
Hello Jessica
Hello Drew
Hello Jamie
```

Using a variadic parameter can make your code more readable. Let’s create a function that joins words together with a specified delimiter. We’ll create this program without a variadic function first to show how it would read:

join.go

In this program, we are passing a comma (`,`) as the delimiter to the `join` function. Then we are passing a slice of values to join. Here is the output:

```
OutputSammy,Jessica,Drew,Jamie
Sammy,Jessica
Sammy
```

Because the function takes a slice of string as the `values` parameter, we had to wrap all of our words in a slice when we called the `join` function. This can make the code difficult to read.

Now, let’s write the same function, but we’ll use a variadic function:

join.go

If we run the program, we can see that we get the same output as the previous program:

```
OutputSammy,Jessica,Drew,Jamie
Sammy,Jessica
Sammy
```

While both versions of the `join` function do the exact same thing programmatically, the variadic version of the function is much easier to read when it is being called.

## Variadic Argument Order

You can only have one variadic parameter in a function, and it must be the last parameter defined in the function. Defining parameters in a variadic function in any order other than the last parameter will result in a compilation error:

join.go

This time we put the `values` parameter first in the `join` function. This will cause the following compilation error:

```
Output./join_error.go:18:11: syntax error: cannot use ... with non-final parameter values
```

When defining any variadic function, only the last parameter can be variadic.

## Exploding Arguments

So far, we have seen that we can pass zero, one, or more values to a variadic function. However, there will be occasions when we have a slice of values and we want to send them to a variadic function.

Let’s look at our `join` function from the last section to see what happens:

join.go

```
package main

import "fmt"

func main() {
var line string

names := []string{"Sammy", "Jessica", "Drew", "Jamie"}

line = join(",", names)
fmt.Println(line)
}

func join(del string, values ...string) string {
var line string
for i, v := range values {
line = line + v
if i != len(values)-1 {
line = line + del
}
}
return line
}
```

If we run this program, we will receive a compilation error:

```
Output./join-error.go:10:14: cannot use names (type []string) as type string in argument to join
```

Even though the variadic function will convert the parameter of `values ...string` to a slice of strings `[]string`, we can’t pass a slice of strings as the argument. This is because the compiler expects discrete arguments of strings.

To work around this, we can _explode_ a slice by suffixing it with a set of ellipses (`...`) and turning it into discrete arguments that will be passed to a variadic function:

join.go

```
package main

import "fmt"

func main() {
var line string

names := []string{"Sammy", "Jessica", "Drew", "Jamie"}

line = join(",", names...)
fmt.Println(line)
}

func join(del string, values ...string) string {
var line string
for i, v := range values {
line = line + v
if i != len(values)-1 {
line = line + del
}
}
return line
}
```

This time, when we called the `join` function, we exploded the `names` slice by appending ellipses (`...`).

This allows the program to now run as expected:

```
OutputSammy,Jessica,Drew,Jamie
```

It’s important to note that we can still pass a zero, one, or more arguments, as well as a slice that we explode. Here is the code passing all the variations that we have seen so far:

join.go

```
OutputSammy,Jessica,Drew,Jamie
Sammy,Jessica,Drew,Jamie
Sammy,Jessica
Sammy
```

We now know how to pass zero, one, or many arguments, as well as a slice that we explode, to a variadic function.

## Conclusion

In this tutorial, we have seen how variadic functions can make your code cleaner. While you won’t always need to use them, you may find them useful:

-   If you find that you’re creating a temporary slice just to pass to a function.
-   When the number of input params are unknown or will vary when called.
-   To make your code more readable.

To learn more about creating and calling functions, you can read [How to Define and Call Functions in Go](https://www.digitalocean.com/community/tutorials/how-to-define-and-call-functions-in-go).