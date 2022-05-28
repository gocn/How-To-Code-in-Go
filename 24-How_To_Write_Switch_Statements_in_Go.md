# How To Write Switch Statements in Go

### Introduction

[Conditional statements](https://www.digitalocean.com/community/tutorials/how-to-write-conditional-statements-in-go) give programmers the ability to direct their programs to take some action if a condition is true and another action if the condition is false. Frequently, we want to compare some [variable](https://www.digitalocean.com/community/tutorials/how-to-use-variables-and-constants-in-go#understanding-variables) against multiple possible values, taking different actions in each circumstance. It’s possible to make this work using [`if` statements](https://www.digitalocean.com/community/tutorials/how-to-write-conditional-statements-in-go#if-statements) alone. Writing software, however, is not only about making things work but also communicating your intention to your future self and other developers. `switch` is an alternative conditional statement useful for communicating actions taken by your Go programs when presented with different options.

Everything we can write with the switch statement can also be written with `if` statements. In this tutorial, we’ll look at a few examples of what the switch statement can do, the `if` statements it replaces, and where it’s most appropriately applied.

## Structure of Switch Statements

Switch is commonly used to describe the actions taken by a program when a variable is assigned specific values. The following example demonstrates how we would accomplish this using `if` statements:

```go
package main

import "fmt"

func main() {
	flavors := []string{"chocolate", "vanilla", "strawberry", "banana"}

	for _, flav := range flavors {
		if flav == "strawberry" {
			fmt.Println(flav, "is my favorite!")
			continue
		}

		if flav == "vanilla" {
			fmt.Println(flav, "is great!")
			continue
		}

		if flav == "chocolate" {
			fmt.Println(flav, "is great!")
			continue
		}

		fmt.Println("I've never tried", flav, "before")
	}
}
```


This will generate the following output:

```
Outputchocolate is great!
vanilla is great!
strawberry is my favorite!
I've never tried banana before
```

Within `main`, we define a [slice](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#slices) of ice-cream flavors. We then use a [`for loop`](https://www.digitalocean.com/community/tutorials/how-to-construct-for-loops-in-go) to iterate through them. We use three `if` statements to print out different messages indicating preferences for different ice-cream flavors. Each `if` statement must use the `continue` statement to stop execution of the `for` loop so that the default message at the end is not printed for the preferred ice-cream flavors.

As we add new ice-cream preferences, we have to keep adding `if` statements to handle the new cases. Duplicated messages, as in the case of `"vanilla"` and `"chocolate"`, must have duplicated `if` statements. To future readers of our code (ourselves included), the repetitive nature of the `if` statements obscures the important part of what they are doing—comparing the variable against multiple values and taking different actions. Also, our fallback message is set apart from the conditionals, making it appear unrelated. The `switch` statement can help us organize this logic better.

The `switch` statement begins with the `switch` keyword and is followed, in its most basic form, with some variable to perform comparisons against. This is followed by a pair of curly braces (`{}`) where multiple *case clauses* can appear. Case clauses describe the actions your Go program should take when the variable provided to the switch statement equals the value referenced by the case clause. The following example converts the previous example to use a `switch` instead of multiple `if` statements:

```go
package main

import "fmt"

func main() {
	flavors := []string{"chocolate", "vanilla", "strawberry", "banana"}

	for _, flav := range flavors {
		switch flav {
		case "strawberry":
			fmt.Println(flav, "is my favorite!")
		case "vanilla", "chocolate":
			fmt.Println(flav, "is great!")
		default:
			fmt.Println("I've never tried", flav, "before")
		}
	}
}
```


The output is the same as before:

```
Outputchocolate is great!
vanilla is great!
strawberry is my favorite!
I've never tried banana before
```

We’ve once again defined a slice of ice-cream flavors in `main` and used the `range` statement to iterate over each flavor. This time, however, we’ve used a `switch` statement that will examine the `flav` variable. We use two `case` clauses to indicate preferences. We no longer need `continue` statements as only one `case` clause will be executed by the `switch` statement. We’re also able to combine the duplicated logic of the `"chocolate"` and `"vanilla"` conditionals by separating each with a comma in the declaration of the `case` clause. The `default` clause serves as our catch-all clause. It will run for any flavors that we haven’t accounted for in the body of the `switch` statement. In this case, `"banana"` will cause `default` to execute, printing the message `I've never tried banana before`.

This simplified form of `switch` statements addresses the most common use for them: comparing a variable against multiple alternatives. It also provides conveniences for us where we want to take the same action for multiple different values and some other action when none of the listed conditions are met by using the provided `default` keyword.

When this simplified form of `switch` proves too limiting, we can use a more general form of `switch` statement.

## General Switch Statements

`switch` statements are useful for grouping collections of more complicated conditionals to show that they are somehow related. This is most commonly used when comparing some variable against a range of values, rather than specific values as in the earlier example. The following example implements a guessing game using `if` statements that could benefit from a `switch` statement:

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	rand.Seed(time.Now().UnixNano())
	target := rand.Intn(100)

	for {
		var guess int
		fmt.Print("Enter a guess: ")
		_, err := fmt.Scanf("%d", &guess)
		if err != nil {
			fmt.Println("Invalid guess: err:", err)
			continue
		}

		if guess > target {
			fmt.Println("Too high!")
			continue
		}

		if guess < target {
			fmt.Println("Too low!")
			continue
		}

		fmt.Println("You win!")
		break
	}
}
```


The output will vary depending on the random number selected and how well you play the game. Here is the output from one example session:

```
OutputEnter a guess: 10
Too low!
Enter a guess: 15
Too low!
Enter a guess: 18
Too high!
Enter a guess: 17
You win!
```

Our guessing game needs a random number to compare guesses against, so we use the `rand.Intn` function from the `math/rand` package. To make sure we get different values for `target` each time we play the game, we use `rand.Seed` to randomize the random number generator based on the current time. The argument `100` to `rand.Intn` will give us a number in the range 0–100. We then use a `for` loop to begin collecting guesses from the player.

The `fmt.Scanf` function gives us a means to read user input into a variable of our choosing. It takes a format string verb that converts the user’s input into the type we expect. `%d` here means we expect an `int`, and we pass the address of the `guess` variable so that `fmt.Scanf` is able to set that variable. After [handling any parsing errors](https://www.digitalocean.com/community/tutorials/handling-errors-in-go) we then use two `if` statements to compare the user’s guess to the `target` value. The `string` that they return, along with `bool`, controls the message displayed to the player and whether the game will exit.

These `if` statements obscure the fact that the range of values that the variable is being compared against are all related in some way. It can also be difficult, at a glance, to tell if we missed some part of the range. The next example refactors the previous example to use a `switch` statement instead:

```go
package main

import (
	"fmt"
	"math/rand"
)

func main() {
	target := rand.Intn(100)

	for {
		var guess int
		fmt.Print("Enter a guess: ")
		_, err := fmt.Scanf("%d", &guess)
		if err != nil {
			fmt.Println("Invalid guess: err:", err)
			continue
		}

		switch {
		case guess > target:
			fmt.Println("Too high!")
		case guess < target:
			fmt.Println("Too low!")
		default:
			fmt.Println("You win!")
			return
		}
	}
}
```


This will generate output similar to the following:

```
OutputEnter a guess: 25
Too low!
Enter a guess: 28
Too high!
Enter a guess: 27
You win!
```

In this version of the guessing game, we’ve replaced the block of `if` statements with a `switch` statement. We omit the expression argument to `switch` because we are only interested in using `switch` to collect conditionals together. Each `case` clause contains a different expression comparing `guess` against `target`. Similar to the first time we replaced `if` statements with `switch`, we no longer need `continue` statements since only one `case` clause will be executed. Finally, the `default` clause handles the case where `guess == target` since we have covered all other possible values with the other two `case` clauses.

In the examples that we’ve seen so far, exactly one case statement will be executed. Occasionally, you may wish to combine the behaviors of multiple `case` clauses. `switch` statements provide another keyword for achieving this behavior.

## Fallthrough

Sometimes you will want to reuse the code that another `case` clause contains. In these cases, it’s possible to ask Go to run the body of the next `case` clause listed using the `fallthrough` keyword. This next example modifies our earlier ice cream flavor example to more accurately reflect our enthusiasm for strawberry ice cream:

```go
package main

import "fmt"

func main() {
	flavors := []string{"chocolate", "vanilla", "strawberry", "banana"}

	for _, flav := range flavors {
		switch flav {
		case "strawberry":
			fmt.Println(flav, "is my favorite!")
			fallthrough
		case "vanilla", "chocolate":
			fmt.Println(flav, "is great!")
		default:
			fmt.Println("I've never tried", flav, "before")
		}
	}
}
```


We will see this output:

```
Outputchocolate is great!
vanilla is great!
strawberry is my favorite!
strawberry is great!
I've never tried banana before
```

As we’ve seen previously, we define a slice of `string` to represent flavors and iterate through this using a `for` loop. The `switch` statement here is identical to the one we’ve seen before, but with the addition of the `fallthrough` keyword at the end of the `case` clause for `"strawberry"`. This will cause Go to run the body of `case "strawberry":`, first printing out the string `strawberry is my favorite!`. When it encounters `fallthrough` it will run the body of the next `case` clause. This will cause the body of `case "vanilla", "chocolate":` to run, printing `strawberry is great!`.

The `fallthrough` keyword is not used often by Go developers. Usually, the code reuse realized by using `fallthrough` can be better obtained by defining a function with the common code. For these reasons, using `fallthrough` is generally discouraged.

## Conclusion

`switch` statements help us convey to other developers reading our code that a set of comparisons are somehow related to each other. They make it much easier to add different behavior when a new case is added in the future and make it possible to ensure that anything we forgot is handled properly as well with `default` clauses. The next time you find yourself writing multiple `if` statements that all involve the same variable, try rewriting it with a `switch` statement—you’ll find it easier to rework when it comes time to consider some other alternative value.

If you’d like to learn more about the Go programming language, check out the entire [How To Code in Go series](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go).