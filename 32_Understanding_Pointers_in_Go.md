# Understanding Pointers in Go

### Introduction

When you write software in Go you’ll be writing functions and methods. You pass data to these functions as *arguments*. Sometimes, the function needs a local copy of the data, and you want the original to remain unchanged. For example, if you’re a bank, and you have a function that shows the user the changes to their balance depending on the savings plan they choose, you don’t want to change the customer’s actual balance before they choose a plan; you just want to use it in calculations. This is called *passing by value*, because you’re sending the value of the variable to the function, but not the variable itself.

Other times, you may want the function to be able to alter the data in the original variable. For instance, when the bank customer makes a deposit to their account, you want the deposit function to be able to access the actual balance, not a copy. In this case, you don’t need to send the actual data to the function; you just need to tell the function where the data is located in memory. A data type called a *pointer* holds the memory address of the data, but not the data itself. The memory address tells the function where to find the data, but not the value of the data. You can pass the pointer to the function instead of the data, and the function can then alter the original variable in place. This is called *passing by reference*, because the value of the variable isn’t passed to the function, just its location.

In this article, you will create and use pointers to share access to the memory space for a variable.

## Defining and Using Pointers

When you use a pointer to a variable, there are a couple of different syntax elements that you need to understand. The first one is the use of the ampersand (`&`). If you place an ampersand in front of a variable name, you are stating that you want to get the *address*, or a pointer to that variable. The second syntax element is the use of the asterisk (`*`) or *dereferencing* operator. When you declare a pointer variable, you follow the variable name with the type of the variable that the pointer points to, prefixed with an `*`, like this:

```plain
var myPointer *int32 = &someint
```

This creates `myPointer` as a pointer to an `int32` variable, and initializes the pointer with the address of `someint`. The pointer doesn’t actually contain an `int32`, just the address of one.

Let’s take a look at a pointer to a `string`. The following code declares both a value of a string, and a pointer to a string:

main.go

```plain
package main

import "fmt"

func main() {
	var creature string = "shark"
	var pointer *string = &creature

	fmt.Println("creature =", creature)
	fmt.Println("pointer =", pointer)
}

```


Run the program with the following command:

```plain
go run main.go
```

When you run the program, it will print out the value of the variable, as well as the address of where the variable is stored (the pointer address). The memory address is a hexadecimal number, and not meant to be human-readable. In practice, you’ll probably never output a memory address to look at it. We’re showing you for illustrative purposes. Because each program is created in its own memory space when it is run, the value of the pointer will be different each time you run it, and will be different than the output shown here:

```plain
Output
creature = shark
pointer = 0xc0000721e0
```
The first variable we defined we named `creature`, and set it equal to a `string` with the value of `shark`. We then created another variable named `pointer`. This time, we set the value of the `pointer` variable to the address of the `creature` variable. We store the address of a value in a variable by using the ampersand (`&`) symbol. This means that the `pointer` variable is storing the **address** of the `creature` variable, not the actual value.
This is why when we printed out the value of `pointer`, we received the value of `0xc0000721e0`, which is the address of where the `creature` variable is currently stored in computer memory.

If you want to print out the value of the variable being pointed at from the `pointer` variable, you need to *dereference* that variable. The following code uses the `*` operator to dereference the `pointer` variable and retrieve its value:

main.go

```plain

package main

import "fmt"

func main() {
	var creature string = "shark"
	var pointer *string = &creature

	fmt.Println("creature =", creature)
	fmt.Println("pointer =", pointer)

	fmt.Println("*pointer =", *pointer)
}
```

If you run this code, you’ll see the following output:

```plain
Output
creature = shark
pointer = 0xc000010200
*pointer = shark
```
The last line we added now dereferences the `pointer` variable, and prints out the value that is stored at that address.
If you want to modify the value stored at the `pointer` variable’s location, you can use the dereference operator as well:

main.go

```plain
package main

import "fmt"

func main() {
	var creature string = "shark"
	var pointer *string = &creature

	fmt.Println("creature =", creature)
	fmt.Println("pointer =", pointer)

	fmt.Println("*pointer =", *pointer)

	*pointer = "jellyfish"
	fmt.Println("*pointer =", *pointer)
}

```


Run this code to see the output:

```plain
Output
creature = shark
pointer = 0xc000094040
*pointer = shark
*pointer = jellyfish
```
We set the value the `pointer` variable refers to by using the asterisk (`*`) in front of the variable name, and then providing a new value of `jellyfish`. As you can see, when we print the dereferenced value, it is now set to `jellyfish`.
You may not have realized it, but we actually changed the value of the `creature` variable as well. This is because the `pointer` variable is actually pointing at the `creature` variable’s address. This means that if we change the value pointed at from the `pointer` variable, we also change the value of the `creature` variable.

main.go

```plain
package main

import "fmt"

func main() {
	var creature string = "shark"
	var pointer *string = &creature

	fmt.Println("creature =", creature)
	fmt.Println("pointer =", pointer)

	fmt.Println("*pointer =", *pointer)

	*pointer = "jellyfish"
	fmt.Println("*pointer =", *pointer)

	fmt.Println("creature =", creature)
}
```

The output looks like this:

```plain
Output
creature = shark
pointer = 0xc000010200
*pointer = shark
*pointer = jellyfish
creature = jellyfish
```
Although this code illustrates how a pointer works, this is not the typical way in which you would use pointers in Go. It is more common to use them when defining function arguments and return values, or using them when defining methods on custom types. Let’s look at how you would use pointers with functions to share access to a variable.
Again, keep in mind that we are printing the value of `pointer` to illustrate that it is a pointer. In practice, you wouldn’t use the value of a pointer, other than to reference the underlying value to retrieve or update that value.

## Function Pointer Receivers

When you write a function, you can define arguments to be passed ether by *value*, or by *reference*. Passing by *value* means that a copy of that value is sent to the function, and any changes to that argument within that function *only* effect that variable within that function, and not where it was passed from. However, if you pass by *reference*, meaning you pass a pointer to that argument, you can change the value from within the function, and also change the value of the original variable that was passed in. You can read more about how to define functions in our [How To Define and Call Functions in Go](https://www.digitalocean.com/community/conceptual_articles/understanding-pointers-in-go).

Deciding when to pass a pointer as opposed when to send a value is all about knowing if you want the value to change or not. If you don’t want the value to change, send it as a value. If you want the function you are passing your variable to be able to change it, then you would pass it as a pointer.

To see the difference, let’s first look at a function that is passing in an argument by `value`:

main.go

```plain
package main

import "fmt"

type Creature struct {
	Species string
}

func main() {
	var creature Creature = Creature{Species: "shark"}

	fmt.Printf("1) %+v\n", creature)
	changeCreature(creature)
	fmt.Printf("3) %+v\n", creature)
}

func changeCreature(creature Creature) {
	creature.Species = "jellyfish"
	fmt.Printf("2) %+v\n", creature)
}

```


The output looks like this:

```plain
Output
1) {Species:shark}
2) {Species:jellyfish}
3) {Species:shark}
```
First we created a custom type named `Creature`. It has one field named `Species`, which is a string. In the `main` function, we created an instance of our new type named `creature` and set the `Species` field to `shark`. We then printed out the variable to show the current value stored within the `creature` variable.
Next, we call `changeCreature` and pass in a copy of the `creature` variable.

The function `changeCreature` is defined as taking one argument named `creature`, and it is of type `Creature` that we defined earlier. We then change the value of the `Species` field to `jellyfish` and print it out. Notice that within the `changeCreature` function, the value of `Species` is now `jellyfish`, and it prints out `2) {Species:jellyfish}`. This is because we are allowed to change the value within our function scope.

However, when the last line of the `main` function prints the value of `creature`, the value of `Species` is still `shark`. The reason that the value didn’t change is because we passed the variable by *value*. This means that a copy of the value was created in memory, and passed to the `changeCreature` function. This allows us to have a function that can make changes to any arguments passed in as needed, but will not affect any of those variables outside of the function.

Next, let’s change the `changeCreature` function to take an argument by *reference*. We can do this by changing the type from `creature` to a pointer by using the asterisk (`*`) operator. Instead of passing a `creature`, we’re now passing a pointer to a `creature`, or a `*creature`. In the previous example, `creature` is a `struct` that has a `Species` value of `shark`. `*creature` is a pointer, not a struct, so its value is a memory location, and that’s what we pass to `changeCreature()`.

main.go

```plain
package main

import "fmt"

type Creature struct {
	Species string
}

func main() {
	var creature Creature = Creature{Species: "shark"}

	fmt.Printf("1) %+v\n", creature)
	changeCreature(&creature)
	fmt.Printf("3) %+v\n", creature)
}

func changeCreature(creature *Creature) {
	creature.Species = "jellyfish"
	fmt.Printf("2) %+v\n", creature)
}
```

Run this code to see the following output:

```plain
Output
1) {Species:shark}
2) &{Species:jellyfish}
3) {Species:jellyfish}
```
Notice that now when we change the value of `Species` to `jellyfish` in the `changeCreature` function, it changes the original value defined in the `main` function as well. This is because we passed the `creature` variable by *reference*, which allows access to the original value and can change it as needed.
Therefore, if you want a function to be able to change a value, you need to pass it by reference. To pass by reference, you pass the pointer to the variable, and not the variable itself.

However, sometimes you may not have an actual value defined for a pointer. In those cases, it is possible to have a [panic](https://www.digitalocean.com/community/tutorials/handling-panics-in-go) in the program. Let’s look at how that happens and how to plan for that potential problem.

## Nil Pointers

All variables in Go have a [zero value](https://www.digitalocean.com/community/tutorials/how-to-use-variables-and-constants-in-go#zero-values). This is true even for a pointer. If you declare a pointer to a type, but assign no value, the zero value will be `nil`. `nil` is a way to say that “nothing has been initialized” for the variable.

In the following program, we are defining a pointer to a `Creature` type, but we are never instantiating that actual instance of a `Creature` and assigning the address of it to the `creature` pointer variable. The value will be `nil` and we can’t reference any of the fields or methods that would be defined on the `Creature` type:

main.go

```plain
package main

import "fmt"

type Creature struct {
	Species string
}

func main() {
	var creature *Creature

	fmt.Printf("1) %+v\n", creature)
	changeCreature(creature)
	fmt.Printf("3) %+v\n", creature)
}

func changeCreature(creature *Creature) {
	creature.Species = "jellyfish"
	fmt.Printf("2) %+v\n", creature)
}
```

The output looks like this:

```plain
Output
1) <nil>
panic: runtime error: invalid memory address or nil pointer dereference
[signal SIGSEGV: segmentation violation code=0x1 addr=0x8 pc=0x109ac86]

goroutine 1 [running]:
main.changeCreature(0x0)
        /Users/corylanou/projects/learn/src/github.com/gopherguides/learn/_training/digital-ocean/pointers/src/nil.go:18 +0x26
	main.main()
	        /Users/corylanou/projects/learn/src/github.com/gopherguides/learn/_training/digital-ocean/pointers/src/nil.go:13 +0x98
		exit status 2
```
When we run the program, it printed out the value of the `creature` variable, and the value is `<nil>`. We then call the `changeCreature` function, and when that function tries to set the value of the `Species` field, it *panics*. This is because there is no instance of the variable actually created. Because of this, the program has no where to actually store the value, so the program panics.
It is common in Go that if you are receiving an argument as a pointer, you check to see if it was nil or not before performing any operations on it to prevent the program from panicking.

This is a common approach for checking for `nil`:

```plain
if someVariable == nil {
	// print an error or return from the method or fuction
}
```

Effectively you want to make sure you don’t have a `nil` pointer that was passed into your function or method. If you do, you’ll likely just want to return, or return an error to show that an invalid argument was passed to the function or method. The following code demonstrates checking for `nil`:

main.go

```plain
package main

import "fmt"

type Creature struct {
	Species string
}

func main() {
	var creature *Creature

	fmt.Printf("1) %+v\n", creature)
	changeCreature(creature)
	fmt.Printf("3) %+v\n", creature)
}

func changeCreature(creature *Creature) {
	if creature == nil {
		fmt.Println("creature is nil")
		return
	}

	creature.Species = "jellyfish"
	fmt.Printf("2) %+v\n", creature)
}
```

We added a check in the `changeCreature` to see if the value of the `creature` argument was `nil`. If it was, we print out “creature is nil”, and return out of the function. Otherwise, we continue and change the value of the `Species` field. If we run the program, we will now get the following output:

```plain
Output
1) <nil>
creature is nil
3) <nil>
```
Notice that while we still had a `nil` value for the `creature` variable, we are no longer panicking because we are checking for that scenario.
Finally, if we create an instance of the `Creature` type and assign it to the `creature` variable, the program will now change the value as expected:

main.go

```plain
package main

import "fmt"

type Creature struct {
	Species string
}

func main() {
	var creature *Creature
	creature = &Creature{Species: "shark"}

	fmt.Printf("1) %+v\n", creature)
	changeCreature(creature)
	fmt.Printf("3) %+v\n", creature)
}

func changeCreature(creature *Creature) {
	if creature == nil {
		fmt.Println("creature is nil")
		return
	}

	creature.Species = "jellyfish"
	fmt.Printf("2) %+v\n", creature)
}
```

Now that we have an instance of the `Creature` type, the program will run and we will get the following expected output:

```plain
Output
1) &{Species:shark}
2) &{Species:jellyfish}
3) &{Species:jellyfish}
```
When you are working with pointers, there is a potential for the program to panic. To avoid panicking, you should check to see if a pointer value is `nil` prior to trying to access any of the fields or methods defined on it.
Next, let’s look at how using pointers and values affects defining methods on a type.

## Method Pointer Receivers

A *receiver* in go is the argument that is defined in a method declaration. Take a look at the following code:

```plain
type Creature struct {
	Species string
}

func (c Creature) String() string {
	return c.Species
}
```

The receiver in this method is `c Creature`. It is stating that the instance of `c` is of type `Creature` and you will reference that type via that instance variable.

Just like the behavior of functions is different based on whether you send in an argument as a pointer or a value, methods also have different behavior. The big difference is that if you define a method with a value receiver, you are not able to make changes to the instance of that type that the method was defined on.

There will be times that you would like your method to be able to update the instance of the variable that you are using. To allow for this, you would want to make the receiver a pointer.

Let’s add a `Reset` method to our `Creature` type that will set the `Species` field to an empty string:

main.go

```plain
package main

import "fmt"

type Creature struct {
	Species string
}

func (c Creature) Reset() {
	c.Species = ""
}

func main() {
	var creature Creature = Creature{Species: "shark"}

	fmt.Printf("1) %+v\n", creature)
	creature.Reset()
	fmt.Printf("2) %+v\n", creature)
}
```

If we run the program, we will get the following output:

```plain
Output
1) {Species:shark}
2) {Species:shark}
```
Notice that even though in the `Reset` method we set the value of `Species` to an empty string, that when we print out the value of our `creature` variable in the `main` function, the value is still set to `shark`. This is because we defined the `Reset` method has having a `value` receiver. This means that the method will only have access to a *copy* of the `creature` variable.
If we want to be able to modify the instance of the `creature` variable in the methods, we need to define them as having a `pointer` receiver:

main.go

```plain
package main

import "fmt"

type Creature struct {
	Species string
}

func (c *Creature) Reset() {
	c.Species = ""
}

func main() {
	var creature Creature = Creature{Species: "shark"}

	fmt.Printf("1) %+v\n", creature)
	creature.Reset()
	fmt.Printf("2) %+v\n", creature)
}
```

Notice that we now added an asterisk (`*`) in front of the `Creature` type in when we defined the `Reset` method. This means that the instance of `Creature` that is passed to the `Reset` method is now a pointer, and as such when we make changes it will affect the original instance of that variables.

```plain
Output
1) {Species:shark}
2) {Species:}
```
The `Reset` method has now changed the value of the `Species` field.
## Conclusion

Defining a function or method as a pass by *value* or pass by *reference* will affect what parts of your program are able to make changes to other parts. Controlling when that variable can be changed will allow you to write more robust and predictable software. Now that you have learned about pointers, you can see how they are used in interfaces as well.

 

