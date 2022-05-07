### Introduction

Using **for loops** in Go allow you to automate and repeat tasks in an efficient manner.

Learning how to control the operation and flow of loops will allow for customized logic in your program. You can control your loops with the `break` and `continue` statements.

## Break Statement

In Go, the `break` statement terminates execution of the current loop. A `break` is almost always paired with a [conditional `if` statement](https://www.digitalocean.com/community/tutorials/how-to-write-conditional-statements-in-go).

Let’s look at an example that uses the `break` statement in a `for` loop:

break.go

This small program creates a `for` loop that will iterate while `i` is less than `10`.

Within the `for` loop, there is an `if` statement. The `if` statement tests the condition of `i` to see if the value is less than `5`. If the value of `i` is not equal to `5`, the loop continues and prints out the value of `i`. If the value of `i` is equal to `5`, the loop will execute the `break` statement, print that it is `Breaking out of loop`, and stop executing the loop. At the end of the program we print out `Exiting program` to signify that we have exited the loop.

When we run this code, our output will be the following:

```
OutputThe value of i is 0
The value of i is 1
The value of i is 2
The value of i is 3
The value of i is 4
Breaking out of loop
Exiting program
```

This shows that once the integer `i` is evaluated as equivalent to 5, the loop breaks, as the program is told to do so with the `break` statement.

### Nested Loops

It is important to remember that the `break` statement will only stop the execution of the inner most loop it is called in. If you have a nested set of loops, you will need a break for each loop if desired.

nested.go

In this program, we have two loops. While both loops iterate 5 times, each has a conditional `if` statement with a `break` statement. The outer loop will break if the value of `outer` equals `3`. The inner loop will break if the value of `inner` is `2`.

If we run the program, we can see the output:

```
OutputThe value of outer is 0
The value of inner is 0
The value of inner is 1
Breaking out of inner loop
The value of outer is 1
The value of inner is 0
The value of inner is 1
Breaking out of inner loop
The value of outer is 2
The value of inner is 0
The value of inner is 1
Breaking out of inner loop
Breaking out of outer loop
Exiting program
```

Notice that each time the inner loop breaks, the outer loop does not break. This is because `break` will only break the inner most loop it is called from.

We have seen how using `break` will stop the execution of a loop. Next, let’s look at how we can continue the iteration of a loop.

## Continue Statement

The `continue` statement is used when you want to skip the remaining portion of the loop, and return to the top of the loop and continue a new iteration.

As with the `break` statement, the `continue` statement is commonly used with a conditional `if` statement.

Using the same `for` loop program as in the preceding [Break Statement](https://www.digitalocean.com/community/tutorials/how-to-use-break-and-continue-statements-when-working-with-loops-in-go#break-statement) section, we’ll use a `continue` statement rather than a `break` statement:

continue.go

The difference in using the `continue` statement rather than a `break` statement is that our code will continue despite the disruption when the variable `i` is evaluated as equivalent to `5`. Let’s look at our output:

```
OutputThe value of i is 0
The value of i is 1
The value of i is 2
The value of i is 3
The value of i is 4
Continuing loop
The value of i is 6
The value of i is 7
The value of i is 8
The value of i is 9
Exiting program
```

Here we see that the line `The value of i is 5` never occurs in the output, but the loop continues after that point to print lines for the numbers 6-10 before leaving the loop.

You can use the `continue` statement to avoid deeply nested conditional code, or to optimize a loop by eliminating frequently occurring cases that you would like to reject.

The `continue` statement causes a program to skip certain factors that come up within a loop, but then continue through the rest of the loop.

## Conclusion

The `break` and `continue` statements in Go will allow you to use `for` loops more effectively in your code.