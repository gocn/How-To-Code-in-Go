- 原文地址：[How To Use Interfaces in Go | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-use-interfaces-in-go)
- 原文作者：digitalocean
- 本文永久链接：
- 译者：[zxmfke](https://github.com/zxmfke)
- 校对：

# How To Use Interfaces in Go

## Introduction

Writing flexible, reusable, and modular code is vital for developing versatile programs. Working in this way ensures code is easier to maintain by avoiding the need to make the same change in multiple places. How you accomplish this varies from language to language. For instance, [*inheritance*](https://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming)) is a common approach that is used in languages such as Java, C++, C#, and more.

Developers can also attain those same design goals through [*composition*](https://en.wikipedia.org/wiki/Object_composition). Composition is a way to combine objects or data types into more complex ones. This is the approach that Go uses to promote code reuse, modularity, and flexibility. Interfaces in Go provide a method of organizing complex compositions, and learning how to use them will allow you to create common, reusable code.

In this article, we will learn how to compose custom types that have common behaviors, which will allow us to reuse our code. We’ll also learn how to implement interfaces for our own custom types that will satisfy interfaces defined from another package.

## Defining a Behavior

One of the core implementations of composition is the use of interfaces. An interface defines a behavior of a type. One of the most commonly used interfaces in the Go standard library is the [`fmt.Stringer`](https://golang.org/pkg/fmt/#Stringer) interface:

```go
type Stringer interface {
    String() string
}
```

The first line of code defines a `type` called `Stringer`. It then states that it is an `interface`. Just like defining a struct, Go uses curly braces (`{}`) to surround the definition of the interface. In comparison to defining structs, we only define the interface’s *behavior*; that is, “what can this type do”.

In the case of the `Stringer` interface, the only behavior is the `String()` method. The method takes no arguments and returns a string.

Next, let’s look at some code that has the `fmt.Stringer` behavior:

main.go

```go
package main

import "fmt"

type Article struct {
	Title string
	Author string
}

func (a Article) String() string {
	return fmt.Sprintf("The %q article was written by %s.", a.Title, a.Author)
}

func main() {
	a := Article{
		Title: "Understanding Interfaces in Go",
		Author: "Sammy Shark",
	}
	fmt.Println(a.String())
}
```

The first thing we do is create a new type called `Article`. This type has a `Title` and an `Author` field and both are of the string [data type](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go):

main.go

```go
...
type Article struct {
	Title string
	Author string
}
...
```

Next, we define a [`method`](https://www.digitalocean.com/community/tutorials/defining-methods-in-go) called `String` on the `Article` type. The `String` method will return a string that represents the `Article` type:

main.go

```go
...
func (a Article) String() string {
	return fmt.Sprintf("The %q article was written by %s.", a.Title, a.Author)
}
...
```

Then, in our `main` [function](https://www.digitalocean.com/community/tutorials/how-to-define-and-call-functions-in-go), we create an instance of the `Article` type and assign it to the [variable](https://www.digitalocean.com/community/tutorials/how-to-use-variables-and-constants-in-go) called `a`. We provide the values of `"Understanding Interfaces in Go"` for the `Title` field, and `"Sammy Shark"` for the `Author` field:

main.go

```go
...
a := Article{
	Title: "Understanding Interfaces in Go",
	Author: "Sammy Shark",
}
...
```

Then, we print out the result of the `String` method by calling `fmt.Println` and passing in the result of the `a.String()` method call:

main.go

```go
...
fmt.Println(a.String())
```

After running the program you’ll see the following output:

```
OutputThe "Understanding Interfaces in Go" article was written by Sammy Shark.
```

So far, we haven’t used an interface, but we did create a type that had a behavior. That behavior matched the `fmt.Stringer` interface. Next, let’s see how we can use that behavior to make our code more reusable.

## Defining an Interface

Now that we have our type defined with the desired behavior, we can look at how to use that behavior.

Before we do that, however, let’s look at what we would need to do if we wanted to call the `String` method from the `Article` type in a function:

main.go

```
package main

import "fmt"

type Article struct {
	Title string
	Author string
}

func (a Article) String() string {
	return fmt.Sprintf("The %q article was written by %s.", a.Title, a.Author)
}

func main() {
	a := Article{
		Title: "Understanding Interfaces in Go",
		Author: "Sammy Shark",
	}
	Print(a)
}

func Print(a Article) {
	fmt.Println(a.String())
}
```

In this code we add a new function called `Print` that takes an `Article` as an argument. Notice that the only thing the `Print` function does is call the `String` method. Because of this, we could instead define an interface to pass to the function:

main.go

```
package main

import "fmt"

type Article struct {
	Title string
	Author string
}

func (a Article) String() string {
	return fmt.Sprintf("The %q article was written by %s.", a.Title, a.Author)
}

type Stringer interface {
	String() string
}

func main() {
	a := Article{
		Title: "Understanding Interfaces in Go",
		Author: "Sammy Shark",
	}
	Print(a)
}

func Print(s Stringer) {
	fmt.Println(s.String())
}
```

Here we created an interface called `Stringer`:

main.go

```go
...
type Stringer interface {
	String() string
}
...
```

Copy

The `Stringer` interface has only one method, called `String()` that returns a `string`. A [method](https://www.digitalocean.com/community/tutorials/defining-methods-in-go) is a special function that is scoped to a specific type in Go. Unlike a function, a method can only be called from the instance of the type it was defined on.

We then update the signature of the `Print` method to take a `Stringer`, and not a concrete type of `Article`. Because the compiler knows that a `Stringer` interface defines the `String` method, it will only accept types that also have the `String` method.

Now we can use the `Print` method with anything that satisfies the `Stringer` interface. Let’s create another type to demonstrate this:

main.go

```
package main

import "fmt"

type Article struct {
	Title  string
	Author string
}

func (a Article) String() string {
	return fmt.Sprintf("The %q article was written by %s.", a.Title, a.Author)
}

type Book struct {
	Title  string
	Author string
	Pages  int
}

func (b Book) String() string {
	return fmt.Sprintf("The %q book was written by %s.", b.Title, b.Author)
}

type Stringer interface {
	String() string
}

func main() {
	a := Article{
		Title:  "Understanding Interfaces in Go",
		Author: "Sammy Shark",
	}
	Print(a)

	b := Book{
		Title:  "All About Go",
		Author: "Jenny Dolphin",
		Pages:  25,
	}
	Print(b)
}

func Print(s Stringer) {
	fmt.Println(s.String())
}
```

We now add a second type called `Book`. It also has the `String` method defined. This means it also satisfies the `Stringer` interface. Because of this, we can also send it to our `Print` function:

```
OutputThe "Understanding Interfaces in Go" article was written by Sammy Shark.
The "All About Go" book was written by Jenny Dolphin. It has 25 pages.
```

So far, we have demonstrated how to use just a single interface. However, an interface can have more than one behavior defined. Next, we’ll see how we can make our interfaces more versatile by declaring more methods.

## Multiple Behaviors in an Interface

One of the core tenants of writing Go code is to write small, concise types and compose them up to larger, more complex types. The same is true when composing interfaces. To see how we build up an interface, we’ll first start by defining only one interface. We’ll define two shapes, a `Circle` and `Square`, and they will both define a method called `Area`. This method will return the geometric area of their respective shapes:

main.go

```go
package main

import (
	"fmt"
	"math"
)

type Circle struct {
	Radius float64
}

func (c Circle) Area() float64 {
	return math.Pi * math.Pow(c.Radius, 2)
}

type Square struct {
	Width  float64
	Height float64
}

func (s Square) Area() float64 {
	return s.Width * s.Height
}

type Sizer interface {
	Area() float64
}

func main() {
	c := Circle{Radius: 10}
	s := Square{Height: 10, Width: 5}

	l := Less(c, s)
	fmt.Printf("%+v is the smallest\n", l)
}

func Less(s1, s2 Sizer) Sizer {
	if s1.Area() < s2.Area() {
		return s1
	}
	return s2
}
```

Because each type declares the `Area` method, we can create an interface that defines that behavior. We create the following `Sizer` interface:

main.go

```go
...
type Sizer interface {
	Area() float64
}
...
```

We then define a function called `Less` that takes two `Sizer` and returns the smallest one:

main.go

```go
...
func Less(s1, s2 Sizer) Sizer {
	if s1.Area() < s2.Area() {
		return s1
	}
	return s2
}
...
```

Notice that we not only accept both arguments as the type `Sizer`, but we also return the result as a `Sizer` as well. This means that we no longer return a `Square` or a `Circle`, but the interface of `Sizer`.

Finally, we print out what had the smallest area:

```
Output{Width:5 Height:10} is the smallest
```

Next, let’s add another behavior to each type. This time we’ll add the `String()` method that returns a string. This will satisfy the `fmt.Stringer` interface:

main.go

```
package main

import (
	"fmt"
	"math"
)

type Circle struct {
	Radius float64
}

func (c Circle) Area() float64 {
	return math.Pi * math.Pow(c.Radius, 2)
}

func (c Circle) String() string {
	return fmt.Sprintf("Circle {Radius: %.2f}", c.Radius)
}

type Square struct {
	Width  float64
	Height float64
}

func (s Square) Area() float64 {
	return s.Width * s.Height
}

func (s Square) String() string {
	return fmt.Sprintf("Square {Width: %.2f, Height: %.2f}", s.Width, s.Height)
}

type Sizer interface {
	Area() float64
}

type Shaper interface {
	Sizer
	fmt.Stringer
}

func main() {
	c := Circle{Radius: 10}
	PrintArea(c)

	s := Square{Height: 10, Width: 5}
	PrintArea(s)

	l := Less(c, s)
	fmt.Printf("%v is the smallest\n", l)

}

func Less(s1, s2 Sizer) Sizer {
	if s1.Area() < s2.Area() {
		return s1
	}
	return s2
}

func PrintArea(s Shaper) {
	fmt.Printf("area of %s is %.2f\n", s.String(), s.Area())
}
```

Because both the `Circle` and the `Square` type implement both the `Area` and `String` methods, we can now create another interface to describe that wider set of behavior. To do this, we’ll create an interface called `Shaper`. We’ll compose this of the `Sizer` interface and the `fmt.Stringer` interface:

main.go

```go
...
type Shaper interface {
	Sizer
	fmt.Stringer
}
...
```

**Note:** It is considered idiomatic to try to name your interface by ending in `er`, such as `fmt.Stringer`, `io.Writer`, etc. This is why we named our interface `Shaper`, and not `Shape`.

Now we can create a function called `PrintArea` that takes a `Shaper` as an argument. This means that we can call both methods on the passed in value for both the `Area` and `String` method:

main.go

```go
...
func PrintArea(s Shaper) {
	fmt.Printf("area of %s is %.2f\n", s.String(), s.Area())
}
```

If we run the program, we will receive the following output:

```
Outputarea of Circle {Radius: 10.00} is 314.16
area of Square {Width: 5.00, Height: 10.00} is 50.00
Square {Width: 5.00, Height: 10.00} is the smallest
```

We have now seen how we can create smaller interfaces and build them up into larger ones as needed. While we could have started with the larger interface and passed it to all of our functions, it is considered best practice to send only the smallest interface to a function that is needed. This typically results in clearer code, as anything that accepts a specific smaller interface only intends to work with that defined behavior.

For example, if we passed `Shaper` to the `Less` function, we may assume that it is going to call both the `Area` and `String` methods. However, since we only intend to call the `Area` method, it makes the `Less` function clear as we know that we can only call the `Area` method of any argument passed to it.

## Conclusion

We have seen how creating smaller interfaces and building them up to larger ones allows us to share only what we need to a function or method. We also learned that we can compose our interfaces from other interfaces, including those defined from other packages, not just our packages.

If you’d like to learn more about the Go programming language, check out the entire [How To Code in Go series](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go).