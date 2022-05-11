# Defining Structs in Go

## Introduction

Building abstractions around concrete details is the greatest tool that a programming language can give to a developer. Structs allow Go developers to describe the world in which a Go program operates. Instead of reasoning about strings describing a `Street`, `City`, or a `PostalCode`, structs allow us to instead talk about an `Address`. They serve as a natural nexus for [documentation](https://www.digitalocean.com/community/tutorials/how-to-write-comments-in-go) in our efforts to tell future developers (ourselves included) what data is important to our Go programs and how future code should use that data appropriately. Structs can be defined and used in a few different ways. In this tutorial, we’ll take a look at each of these techniques.

## Defining Structs

Structs work like paper forms that you might use, for example, to file your taxes. Paper forms might have fields for textual pieces of information like your first and last names. Besides text fields, forms might have checkboxes to indicate Boolean values such as “married” or “single,” or date fields for birth date. Similarly, structs collect different pieces of data together and organize them under different field names. When you initialize a variable with a new struct, it’s as though you’ve photocopied a form and made it ready to fill out.

To create a new struct, you must first give Go a blueprint that describes the fields the struct contains. This struct definition usually begins with the keyword `type` followed by the name of the struct. After this, use the `struct` keyword followed by a pair of braces `{}` where you declare the fields the struct will contain. Once you have defined the struct, you are then able to declare variables that use this struct definition. This example defines a struct and uses it:

```go
package main

import "fmt"

type Creature struct {
	Name string
}

func main() {
	c := Creature{
		Name: "Sammy the Shark",
	}
	fmt.Println(c.Name)
}
```

When you run this code, you will see this output:

```bash
output
Sammy the Shark
```

We first define a `Creature` struct in this example, containing a `Name` field of type `string`. Within the body of `main`, we create an instance of `Creature` by placing a pair of braces after the name of the type, `Creature`, and then specifying values for that instance’s fields. The instance in `c` will have its `Name` field set to “Sammy the Shark”. Within the `fmt.Println` function invocation, we retrieve the values of the instance’s field by placing a period after the variable where the instance was created, followed by the name of the field we would like to access. For example, `c.Name` in this case returns the `Name` field.

When you declare a new instance of a struct, you generally enumerate the field names with their values, as in the last example. Alternatively, if every field value will be provided during the instantiation of a struct, you can omit the field names, like in this example:

```go
package main

import "fmt"

type Creature struct {
	Name string
	Type string
}

func main() {
	c := Creature{"Sammy", "Shark"}
	fmt.Println(c.Name, "the", c.Type)
}
```

The output is the same as the last example:

```bash
output
Sammy the Shark
```

We’ve added an extra field to `Creature` to track the `Type` of creature as a `string`. When instantiating `Creature` within the body of `main`, we’ve opted to use the shorter instantiation form by providing values for each field in order and omitting their field names. In the declaration `Creature{"Sammy", "Shark"}`, the `Name` field takes the value `Sammy` and the `Type` field takes the value `Shark` because `Name` appears first in the type declaration, followed by `Type`.

This shorter declaration form has a few drawbacks that have led the Go community to prefer the longer form in most circumstances. You must provide values for each field in the struct when using the short declaration—you can’t skip fields you don’t care about. This quickly causes short declarations for structs with many fields to become confusing. For this reason, declaring structs using the short form is typically used with structs that have few fields.

The field names in the examples so far have all begun with capital letters. This is more significant than a stylistic preference. The use of capital or lowercase letters for field names affects whether your field names will be accessible to code running in other packages.

## Struct Field Exporting

Fields of a struct follow the same exporting rules as other identifiers within the Go programming language. If a field name begins with a capital letter, it will be readable and writeable by code outside of the package where the struct was defined. If the field begins with a lowercase letter, only code within that struct’s package will be able to read and write that field. This example defines fields that are exported and those that are not:

```go
package main

import "fmt"

type Creature struct {
	Name string
	Type string

	password string
}

func main() {
	c := Creature{
		Name: "Sammy",
		Type: "Shark",

		password: "secret",
	}
	fmt.Println(c.Name, "the", c.Type)
	fmt.Println("Password is", c.password)
}
```

This will output:

```bash
output
Sammy the Shark
Password is secret
```

We added an additional field to our previous examples, `secret`. `secret` is an unexported `string` field, which means that any other package that attempts to instantiate a `Creature` will not be able to access or set its `secret` field. Within the same package, we are able to access these fields, as this example has done. Since `main` is also in the `main` package, it’s able to reference `c.password` and retrieve the value stored there. It’s common to have unexported fields in structs with access to them mediated by exported methods.

## Inline Structs

In addition to defining a new type to represent a struct, you can also define an inline struct. These on-the-fly struct definitions are useful in situations where inventing new names for struct types would be wasted effort. For example, tests often use a struct to define all the parameters that make up a particular test case. It would be cumbersome to come up with new names like `CreatureNamePrintingTestCase` when that struct is used in only one place.

Inline struct definitions appear on the right-hand side of a variable assignment. You must provide an instantiation of them immediately after by providing an additional pair of braces with values for each of the fields you define. The example that follows shows an inline struct definition:

```go
package main

import "fmt"

func main() {
	c := struct {
		Name string
		Type string
	}{
		Name: "Sammy",
		Type: "Shark",
	}
	fmt.Println(c.Name, "the", c.Type)
}
```

The output from this example will be:

```bash
output
Sammy the Shark
```

Rather than defining a new type describing our struct with the `type` keyword, this example defines an inline struct by placing the `struct` definition immediately following the short-assignment operator, `:=`. We define the fields of the struct as in previous examples, but then we must immediately supply another pair of braces and the values that each field will assume. Using this struct is now exactly the same as before—we can refer to field names using dot notation. The most common place you will see inline structs declared is during tests, as frequently one-off structs are defined to contain data and expectations for a particular test case.

## Conclusion

Structs are collections of heterogenous data defined by programmers to organize information. Most programs deal with enormous volumes of data, and without structs, it would become difficult to remember which `string` or `int` variables belonged together or which were different. The next time that you find yourself juggling groups of variables, ask yourself if perhaps those variables would be better grouped together using a `struct`. Those variables may have been describing some higher-level concept all along.