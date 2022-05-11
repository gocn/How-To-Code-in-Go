# Defining Methods in Go

## Introduction

[Functions](https://www.digitalocean.com/community/tutorials/how-to-define-and-call-functions-in-go) allow you to organize logic into repeatable procedures that can use different arguments each time they run. In the course of defining functions, you’ll often find that multiple functions might operate on the same piece of data each time. Go recognizes this pattern and allows you to define special functions, called methods, whose purpose is to operate on instances of some specific type, called a receiver. Adding methods to types allows you to communicate not only what the data is, but also how that data should be used.

## Defining a Method

The syntax for defining a method is similar to the syntax for defining a function. The only difference is the addition of an extra parameter after the `func` keyword for specifying the receiver of the method. The receiver is a declaration of the type that you wish to define the method on. The following example defines a method on a struct type:

```go
package main

import "fmt"

type Creature struct {
	Name     string
	Greeting string
}

func (c Creature) Greet() {
	fmt.Printf("%s says %s", c.Name, c.Greeting)
}

func main() {
	sammy := Creature{
		Name:     "Sammy",
		Greeting: "Hello!",
	}
	Creature.Greet(sammy)
}
```

If you run this code, the output will be:

```bash
Output
Sammy says Hello!
```

We created a struct called `Creature` with `string` fields for a `Name` and a `Greeting`. This `Creature` has a single method defined, `Greet`. Within the receiver declaration, we assigned the instance of `Creature` to the variable `c` so that we could refer to the fields of the `Creature` as we assemble the greeting message in `fmt.Printf`.

In other languages, the receiver of method invocations is typically referred to by a keyword (e.g. `this` or `self`). Go considers the receiver to be a variable like any other, so you’re free to name it whatever you like. The style preferred by the community for this parameter is a lower-case version of the first character of the receiver type. In this example, we used `c` because the receiver type was `Creature`.

Within the body of `main`, we created an instance of `Creature` and specified values for its `Name` and `Greeting` fields. We invoked the `Greet` method here by joining the name of the type and the name of the method with a `.` and supplying the instance of `Creature` as the first argument.

Go provides another, more convenient, way of calling methods on instances of a struct as shown in this example:

```go
package main

import "fmt"

type Creature struct {
	Name     string
	Greeting string
}

func (c Creature) Greet() {
	fmt.Printf("%s says %s", c.Name, c.Greeting)
}

func main() {
	sammy := Creature{
		Name:     "Sammy",
		Greeting: "Hello!",
	}
	sammy.Greet()
}
```

If you run this, the output will be the same as the previous example:

```bash
Output
Sammy says Hello!
```

This example is identical to the previous one, but this time we have used dot notation to invoke the `Greet` method using the `Creature` stored in the `sammy` variable as the receiver. This is a shorthand notation for the function invocation in the first example. The standard library and the Go community prefers this style so much that you will rarely see the function invocation style shown earlier.

The next example shows one reason why dot notation is more prevalent:

```go
package main

import "fmt"

type Creature struct {
	Name     string
	Greeting string
}

func (c Creature) Greet() Creature {
	fmt.Printf("%s says %s!\n", c.Name, c.Greeting)
	return c
}

func (c Creature) SayGoodbye(name string) {
	fmt.Println("Farewell", name, "!")
}

func main() {
	sammy := Creature{
		Name:     "Sammy",
		Greeting: "Hello!",
	}
	sammy.Greet().SayGoodbye("gophers")

	Creature.SayGoodbye(Creature.Greet(sammy), "gophers")
}
```

If you run this code, the output looks like this:

```go
Output
Sammy says Hello!!
Farewell gophers !
Sammy says Hello!!
Farewell gophers !
```

We’ve modified the earlier examples to introduce another method called `SayGoodbye` and also changed `Greet` to return a `Creature` so that we can invoke further methods on that instance. In the body of `main` we call the methods `Greet` and `SayGoodbye` on the `sammy` variable first using dot notation and then using the functional invocation style.

Both styles output the same results, but the example using dot notation is far more readable. The chain of dots also tells us the sequence in which methods will be invoked, where the functional style inverts this sequence. The addition of a parameter to the `SayGoodbye` call further obscures the order of method calls. The clarity of dot notation is the reason that it is the preferred style for invoking methods in Go, both in the standard library and among the third-party packages you will find throughout the Go ecosystem.

Defining methods on types, as opposed to defining functions that operate on some value, have other special significance to the Go programming language. Methods are the core concept behind interfaces.

## Interfaces

When you define a method on any type in Go, that method is added to the type’s method set. The method set is the collection of functions associated with that type as methods and used by the Go compiler to determine whether some type can be assigned to a variable with an interface type. An interface type is a specification of methods used by the compiler to guarantee that a type provides implementations for those methods. Any type that has methods with the same name, same parameters, and same return values as those found in an interface’s definition are said to implement that interface and are allowed to be assigned to variables with that interface’s type. The following is the definition of the `fmt.Stringer` interface from the standard library:

```go
type Stringer interface {
  String() string
}
```

For a type to implement the `fmt.Stringer` interface, it needs to provide a `String()` method that returns a `string`. Implementing this interface will allow your type to be printed exactly as you wish (sometimes called “pretty-printed”) when you pass instances of your type to functions defined in the `fmt` package. The following example defines a type that implements this interface:

```go
package main

import (
	"fmt"
	"strings"
)

type Ocean struct {
	Creatures []string
}

func (o Ocean) String() string {
	return strings.Join(o.Creatures, ", ")
}

func log(header string, s fmt.Stringer) {
	fmt.Println(header, ":", s)
}

func main() {
	o := Ocean{
		Creatures: []string{
			"sea urchin",
			"lobster",
			"shark",
		},
	}
	log("ocean contains", o)
}
```

When you run the code, you’ll see this output:

```bash
Output
ocean contains : sea urchin, lobster, shark
```

This example defines a new struct type called `Ocean`. `Ocean` is said to implement the `fmt.Stringer` interface because `Ocean` defines a method called `String`, which takes no parameters and returns a `string`. In `main`, we defined a new `Ocean` and passed it to a `log` function, which takes a `string` to print out first, followed by anything that implements `fmt.Stringer`. The Go compiler allows us to pass `o` here because `Ocean` implements all of the methods requested by `fmt.Stringer`. Within `log`, we use `fmt.Println`, which calls the `String` method of `Ocean` when it encounters a `fmt.Stringer` as one of its parameters.

If `Ocean` did not provide a `String()` method, Go would produce a compilation error, because the `log` method requests a `fmt.Stringer` as its argument. The error looks like this:

```bash
Output
src/e4/main.go:24:6: cannot use o (type Ocean) as type fmt.Stringer in argument to log:
        Ocean does not implement fmt.Stringer (missing String method)
```

Go will also make sure that the `String()` method provided exactly matches the one requested by the `fmt.Stringer` interface. If it does not, it will produce an error that looks like this:

```bash
Output
src/e4/main.go:26:6: cannot use o (type Ocean) as type fmt.Stringer in argument to log:
        Ocean does not implement fmt.Stringer (wrong type for String method)
                have String()
                want String() string
```

In the examples so far, we have defined methods on the value receiver. That is, if we use the functional invocation of methods, the first parameter, referring to the type the method was defined on, will be a value of that type, rather than a [pointer](https://www.digitalocean.com/community/conceptual_articles/understanding-pointers-in-go). Consequently, any modifications we make to the instance provided to the method will be discarded when the method completes execution, because the value received is a copy of the data. It’s also possible to define methods on the pointer receiver to a type.

## Pointer Receivers

The syntax for defining methods on the pointer receiver is nearly identical to defining methods on the value receiver. The difference is prefixing the name of the type in the receiver declaration with an asterisk (`*`). The following example defines a method on the pointer receiver to a type:

```go
package main

import "fmt"

type Boat struct {
	Name string

	occupants []string
}

func (b *Boat) AddOccupant(name string) *Boat {
	b.occupants = append(b.occupants, name)
	return b
}

func (b Boat) Manifest() {
	fmt.Println("The", b.Name, "has the following occupants:")
	for _, n := range b.occupants {
		fmt.Println("\t", n)
	}
}

func main() {
	b := &Boat{
		Name: "S.S. DigitalOcean",
	}

	b.AddOccupant("Sammy the Shark")
	b.AddOccupant("Larry the Lobster")

	b.Manifest()
}
```

You’ll see the following output when you run this example:

``bash
Output
The S.S. DigitalOcean has the following occupants:
	 Sammy the Shark
	 Larry the Lobster
```

This example defined a `Boat` type with a `Name` and `occupants`. We want to force code in other packages to only add occupants with the `AddOccupant` method, so we’ve made the `occupants` field unexported by lowercasing the first letter of the field name. We also want to make sure that calling `AddOccupant` will cause the instance of `Boat` to be modified, which is why we defined `AddOccupant` on the pointer receiver. Pointers act as a reference to a specific instance of a type rather than a copy of that type. Knowing that `AddOccupant` will be invoked using a pointer to `Boat` guarantees that any modifications will persist.

Within `main`, we define a new variable, `b`, which will hold a pointer to a `Boat` (`*Boat`). We invoke the `AddOccupant` method twice on this instance to add two passengers. The `Manifest` method is defined on the `Boat` value, because in its definition, the receiver is specified as (`b Boat`). In `main`, we are still able to call `Manifest` because Go is able to automatically dereference the pointer to obtain the `Boat` value. `b.Manifest()` here is equivalent to `(*b).Manifest()`.

Whether a method is defined on a pointer receiver or on a value receiver has important implications when trying to assign values to variables that are interface types.

## Pointer Receivers and Interfaces

When you assign a value to a variable with an interface type, the Go compiler will examine the method set of the type being assigned to ensure that it has the methods the interface expects. The method sets for the pointer receiver and the value receiver are different because methods that receive a pointer can modify their receiver where those that receive a value cannot.

The following example demonstrates defining two methods: one on a type’s pointer receiver and on its value receiver. However, only the pointer receiver will be able to satisfy the interface also defined in this example:

```go
package main

import "fmt"

type Submersible interface {
	Dive()
}

type Shark struct {
	Name string

	isUnderwater bool
}

func (s Shark) String() string {
	if s.isUnderwater {
		return fmt.Sprintf("%s is underwater", s.Name)
	}
	return fmt.Sprintf("%s is on the surface", s.Name)
}

func (s *Shark) Dive() {
	s.isUnderwater = true
}

func submerge(s Submersible) {
	s.Dive()
}

func main() {
	s := &Shark{
		Name: "Sammy",
	}

	fmt.Println(s)

	submerge(s)

	fmt.Println(s)
}
```

When you run the code, you’ll see this output:

```bash
Output
Sammy is on the surface
Sammy is underwater
```

This example defined an interface called `Submersible` that expects types having a `Dive()` method. We then defined a `Shark` type with a `Name` field and an `isUnderwater` method to keep track of the state of the `Shark`. We defined a `Dive()` method on the pointer receiver to `Shark` which modified `isUnderwater` to `true`. We also defined the `String()` method of the value receiver so that it could cleanly print the state of the `Shark` using `fmt.Println` by using the `fmt.Stringer` interface accepted by `fmt.Println` that we looked at earlier. We also used a function `submerge` that takes a `Submersible` parameter.

Using the `Submersible` interface rather than a `*Shark` allows the `submerge` function to depend only on the behavior provided by a type. This makes the `submerge` function more reusable because you wouldn’t have to write new `submerge` functions for a `Submarine`, a `Whale`, or any other future aquatic inhabitants we haven’t thought of yet. As long as they define a `Dive()` method, they can be used with the `submerge` function.

Within `main` we defined a variable s that is a pointer to a `Shark` and immediately printed `s` with `fmt.Println`. This shows the first part of the output, `Sammy is on the surface`. We passed `s` to `submerge` and then called `fmt.Println` again with `s` as its argument to see the second part of the output printed, `Sammy is underwater`.

If we changed `s` to be a `Shark` rather than a `*Shark`, the Go compiler would produce the error:

```bash
Output
cannot use s (type Shark) as type Submersible in argument to submerge:
	Shark does not implement Submersible (Dive method has pointer receiver)
```

The Go compiler helpfully tells us that `Shark` does have a `Dive` method, it’s just defined on the pointer receiver. When you see this message in your own code, the fix is to pass a pointer to the interface type by using the `&` operator before the variable where the value type is assigned.

## Conclusion

Declaring methods in Go is ultimately no different than defining functions that receive different types of variables. The same rules of [working with pointers](https://www.digitalocean.com/community/conceptual_articles/understanding-pointers-in-go) apply. Go provides some conveniences for this extremely common function definition and collects these into sets of methods that can be reasoned about by interface types. Using methods effectively will allow you to work with interfaces in your code to improve testability and leaves better organization behind for future readers of your code.

If you’d like to learn more about the Go programming language in general, check out our [How To Code in Go series](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go).