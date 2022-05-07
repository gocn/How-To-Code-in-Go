# How To Construct For Loops in Go
### Introduction

In computer programming, a _loop_ is a code structure that loops around to repeatedly execute a piece of code, often until some condition is met. Using loops in computer programming allows you to automate and repeat similar tasks multiple times. Imagine if you had a list of files that you needed to process, or if you wanted to count the number of lines in an article. You would use a loop in your code to solve these types of problems.

In Go, a `for` loop implements the repeated execution of code based on a loop counter or loop variable. Unlike other programming languages that have multiple looping constructs such as `while`, `do`, etc., Go only has the `for` loop. This serves to make your code clearer and more readable, since you do not have to worry with multiple strategies to achieve the same looping construct. This enhanced readability and decreased cognitive load during development will also make your code less prone to error than in other languages.

In this tutorial, you will learn how Go’s `for` loop works, including the three major variations of its use. We’ll start by showing how to create different types of `for` loops, followed by how to loop through [sequential data types in Go](https://www.digitalocean.com/community/tutorials/understanding-arrays-and-slices-in-go). We’ll end by explaining how to use nested loops.

## Declaring ForClause and Condition Loops

In order to account for a variety of use cases, there are three distinct ways to create `for` loops in Go, each with their own capabilities. These are to create a `for` loop with a **Condition**, a **ForClause**, or a **RangeClause**. In this section, we will explain how to declare and use the ForClause and Condition variants.

Let’s look at how we can use a `for` loop with the ForClause first.

A _ForClause loop_ is defined as having an _initial statement_, followed by a _condition_, and then a _post statement_. These are arranged in the following syntax:

```
for [ Initial Statement ] ; [ Condition ] ; [ Post Statement ] {
    [Action]
}
```

To explain what the preceding components do, let’s look at a `for` loop that increments through a specified range of values using the ForClause syntax:

Let’s break this loop down and identify each part.

The first part of the loop is `i := 0`. This is the initial statement:

```
for i := 0; i < 5; i++ {
fmt.Println(i)
}
```

It states that we are declaring a variable called `i`, and setting the initial value to `0`.

Next is the condition:

```
for i := 0; i < 5; i++ {
fmt.Println(i)
}
```

In this condition, we stated that while `i` is less than the value of `5`, the loop should continue looping.

Finally, we have the post statement:

```
for i := 0; i < 5; i++ {
fmt.Println(i)
}
```

In the post statement, we increment the loop variable `i` up by one each time an iteration occurs using the `i++` [increment](https://golang.org/ref/spec#IncDec_statements) operator.

When we run this program, the output looks like this:

```
Output0
1
2
3
4
```

The loop ran 5 times. Initially, it set `i` to `0`, and then checked to see if `i` was less than `5`. Since the value of `i` was less than `5`, the loop executed and the action of `fmt.Println(i)` was executed. After the loop finished, the post statement of `i++` was called, and the value of `i` was incremented by 1.

**Note:** Keep in mind that in programming we tend to begin at index 0, so that is why although 5 numbers are printed out, they range from 0-4.

We aren’t limited to starting at 0 or ending at a specified value. We can assign any value to our initial statement, and also stop at any value in our post statement. This allows us to create any desired range to loop through:

Here, the iteration goes from 20 (inclusive) to 25 (exclusive), so the output looks like this:

```
Output20
21
22
23
24
```

We can also use our post statement to increment at different values. This is similar to `step` in other languages:

First, let’s use a post statement with a positive value:

```
for i := 0; i < 15; i += 3 {
fmt.Println(i)
}
```

In this case, the `for` loop is set up so that the numbers from 0 to 15 print out, but at an increment of 3, so that only every third number is printed, like so:

```
Output0
3
6
9
12
```

We can also use a negative value for our post statement argument to iterate backwards, but we’ll have to adjust our initial statement and condition arguments accordingly:

Here, we set `i` to an initial value of `100`, use the condition of `i < 0` to stop at `0`, and the post statement decrements the value by 10 with the `-=` operator. The loop begins at `100` and ends at `0`, decreasing by 10 with each iteration. We can see this occur in the output:

```
Output100
90
80
70
60
50
40
30
20
10
```

You can also exclude the initial statement and the post statement from the `for` syntax, and only use the condition. This is what is known as a _Condition loop_:

```
i := 0
for i < 5 {
fmt.Println(i)
i++
}
```

This time, we declared the variable `i` separately from the `for` loop in the preceding line of code. The loop only has a condition clause that checks to see if `i` is less than `5`. As long as the condition evaluates to `true`, the loop will continue to iterate.

Sometimes you may not know the number of iterations you will need to complete a certain task. In that case, you can omit all statements, and use the `break` keyword to exit execution:

```
for {
if someCondition {
break
}
// do action here
}
```

An example of this may be if we are reading from an indeterminately sized structure like a [buffer](https://golang.org/pkg/bytes/#Buffer) and we don’t know when we will be done reading:

buffer.go

In the preceding code, `buf :=bytes.NewBufferString("one\ntwo\nthree\nfour\n")` declares a buffer with some data. Because we don’t know when the buffer will finish reading, we create a `for` loop with no clause. Inside the `for` loop, we use `line, err := buf.ReadString('\n')` to read a line from the buffer and check to see if there was an error reading from the buffer. If there was, we address the error, and [use the `break` keyword to exit the for loop](https://www.digitalocean.com/community/tutorials/using-break-and-continue-statements-when-working-with-loops-in-go). With these `break` points, you do not need to include a condition to stop the loop.

In this section, we learned how to declare a ForClause loop and use it to iterate through a known range of values. We also learned how to use a Condition loop to iterate until a specific condition was met. Next, we’ll learn how the RangeClause is used for iterating through sequential data types.

## Looping Through Sequential Data Types with RangeClause

It is common in Go to use `for` loops to iterate over the elements of sequential or collection data types like [slices, arrays](https://www.digitalocean.com/community/tutorials/understanding-arrays-and-slices-in-go), and [strings](https://www.digitalocean.com/community/tutorials/an-introduction-to-working-with-strings-in-go). To make it easier to do so, we can use a `for` loop with _RangeClause_ syntax. While you can loop through sequential data types using the ForClause syntax, the RangeClause is cleaner and easier to read.

Before we look at using the RangeClause, let’s look at how we can iterate through a slice by using the ForClause syntax:

main.go

```
package main

import "fmt"

func main() {
sharks := []string{"hammerhead", "great white", "dogfish", "frilled", "bullhead", "requiem"}

for i := 0; i < len(sharks); i++ {
fmt.Println(sharks[i])
}
}
```

Running this will give the following output, printing out each element of the slice:

```
Outputhammerhead
great white
dogfish
frilled
bullhead
requiem
```

Now, let’s use the RangeClause to perform the same set of actions:

main.go

```
package main

import "fmt"

func main() {
sharks := []string{"hammerhead", "great white", "dogfish", "frilled", "bullhead", "requiem"}

for i, shark := range sharks {
fmt.Println(i, shark)
}
}
```

In this case, we are printing out each item in the list. Though we used the variables `i` and `shark`, we could have called the variable any other [valid variable name](https://www.digitalocean.com/community/tutorials/how-to-use-variables-and-constants-in-go#naming-variables-rules-and-style) and we would get the same output:

```
Output0 hammerhead
1 great white
2 dogfish
3 frilled
4 bullhead
5 requiem
```

When using `range` on a slice, it will always return two values. The first value will be the index that the current iteration of the loop is in, and the second is the value at that index. In this case, for the first iteration, the index was `0`, and the value was `hammerhead`.

Sometimes, we only want the value inside the slice elements, not the index. If we change the preceding code to only print out the value however, we will receive a compile time error:

main.go

```
package main

import "fmt"

func main() {
sharks := []string{"hammerhead", "great white", "dogfish", "frilled", "bullhead", "requiem"}

for i, shark := range sharks {
fmt.Println(shark)
}
}
```

```
Outputsrc/range-error.go:8:6: i declared and not used
```

Because `i` is declared in the `for` loop, but never used, the compiler will respond with the error of `i declared and not used`. This is the same error that you will receive in Go any time you declare a variable and don’t use it.

Because of this, Go has the [blank identifier](https://golang.org/ref/spec#Blank_identifier) which is an underscore (`_`). In a `for` loop, you can use the blank identifier to ignore any value returned from the `range` keyword. In this case, we want to ignore the index, which is the first argument returned.

main.go

```
package main

import "fmt"

func main() {
sharks := []string{"hammerhead", "great white", "dogfish", "frilled", "bullhead", "requiem"}

for _, shark := range sharks {
fmt.Println(shark)
}
}
```

```
Outputhammerhead
great white
dogfish
frilled
bullhead
requiem
```

This output shows that the `for` loop iterated through the slice of strings, and printed each item from the slice without the index.

You can also use `range` to add items to a list:

main.go

```
package main

import "fmt"

func main() {
sharks := []string{"hammerhead", "great white", "dogfish", "frilled", "bullhead", "requiem"}

for range sharks {
sharks = append(sharks, "shark")
}

fmt.Printf("%q\n", sharks)
}
```

```
Output['hammerhead', 'great white', 'dogfish', 'frilled', 'bullhead', 'requiem', 'shark', 'shark', 'shark', 'shark', 'shark', 'shark']
```

Here, we have added a placeholder string of `"shark"` for each item of the length of the `sharks` slice.

Notice that we didn’t have to use the blank identifier `_` to ignore any of the return values from the `range` operator. Go allows us to leave out the entire declaration portion of the `range` statement if we don’t need to use either of the return values.

We can also use the `range` operator to fill in values of a slice:

main.go

```
package main

import "fmt"

func main() {
integers := make([]int, 10)
fmt.Println(integers)

for i := range integers {
integers[i] = i
}

fmt.Println(integers)
}
```

In this example, the slice `integers` is initialized with ten empty values, but the `for` loop sets all the values in the list like so:

```
Output[0 0 0 0 0 0 0 0 0 0]
[0 1 2 3 4 5 6 7 8 9]
```

The first time we print the value of the slice `integers`, we see all zeros. Then we iterate through each index and set the value to the current index. Then when we print the value of `integers` a second time, showing that they all now have a value of `0` through `9`.

We can also use the `range` operator to iterate through each character in a string:

main.go

```
OutputS
a
m
m
y
```

When iterating through a [map](https://www.digitalocean.com/community/tutorials/understanding-maps-in-go), `range` will return both the **key** and the **value**:

main.go

```
Outputcolor: blue
location: ocean
name: Sammy
animal: shark
```

**Note:** It is important to note that the order in which a map returns is random. Each time you run this program you may get a different result.

Now that we have learned how to iterate over sequential data with `range` `for` loops, let’s look at how to use loops inside of loops.

## Nested For Loops

Loops can be nested in Go, as they can with other programming languages. _Nesting_ is when we have one construct inside of another. In this case, a nested loop is a loop that occurs within another loop. These can be useful when you would like to have a looped action performed on every element of a data set.

Nested loops are structurally similar to [nested `if` statements](https://www.digitalocean.com/community/tutorials/how-to-write-conditional-statements-in-go#nested-if-statements). They are constructed like so:

```
for {
    [Action]
    for {
        [Action]  
    }
}
```

The program first encounters the outer loop, executing its first iteration. This first iteration triggers the inner, nested loop, which then runs to completion. Then the program returns back to the top of the outer loop, completing the second iteration and again triggering the nested loop. Again, the nested loop runs to completion, and the program returns back to the top of the outer loop until the sequence is complete or a break or other statement disrupts the process.

Let’s implement a nested `for` loop so we can take a closer look. In this example, the outer loop will iterate through a slice of integers called `numList`, and the inner loop will iterate through a slice of strings called `alphaList`.

main.go

When we run this program, we’ll receive the following output:

```
Output1
a
b
c
2
a
b
c
3
a
b
c
```

The output illustrates that the program completes the first iteration of the outer loop by printing `1`, which then triggers completion of the inner loop, printing `a`, `b`, `c` consecutively. Once the inner loop has completed, the program returns to the top of the outer loop, prints `2`, then again prints the inner loop in its entirety (`a`, `b`, `c`), etc.

Nested `for` loops can be useful for iterating through items within slices composed of slices. In a slice composed of slices, if we use just one `for` loop, the program will output each internal list as an item:

main.go

```
Output[0 1 2]
[-1 -2 -3]
[9 8 7]
```

In order to access each individual item of the internal slices, we’ll implement a nested `for` loop:

main.go

```
Output0
1
2
-1
-2
-3
9
8
7
```

When we use a nested `for` loop here, we are able to iterate over the individual items contained in the slices.

## Conclusion

In this tutorial we learned how to declare and use `for` loops to solve for repetitive tasks in Go. We also learned the three different variations of a `for` loop and when to use them. To learn more about `for` loops and how to control the flow of them, read [Using Break and Continue Statements When Working with Loops in Go](https://www.digitalocean.com/community/tutorials/using-break-and-continue-statements-when-working-with-loops-in-go).
