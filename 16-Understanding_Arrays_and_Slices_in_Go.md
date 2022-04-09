# Understanding Arrays and Slices in Go

## Introduction

In Go, _arrays_ and _slices_ are [data structures](https://en.wikipedia.org/wiki/Data_structure) that consist of an ordered sequence of elements. These data collections are great to use when you want to work with many related values. They enable you to keep data together that belongs together, condense your code, and perform the same methods and operations on multiple values at once.

Although arrays and slices in Go are both ordered sequences of elements, there are significant differences between the two. An _array_ in Go is a [data structure](https://en.wikipedia.org/wiki/Data_structure) that consists of an ordered sequence of elements that has its capacity defined at creation time. Once an array has allocated its size, the size can no longer be changed. A _slice_, on the other hand, is a variable length version of an array, providing more flexibility for developers using these data structures. Slices constitute what you would think of as arrays in other languages.

Given these differences, there are specific situations when you would use one over the other. If you are new to Go, determining when to use them can be confusing: Although the versatility of slices make them a more appropriate choice in most situations, there are specific instances in which arrays can optimize the performance of your program.

This article will cover arrays and slices in detail, which will provide you with the necessary information to make the appropriate choice when choosing between these data types. Furthermore, you’ll review the most common ways to declare and work with both arrays and slices. The tutorial will first provide a description of arrays and how to manipulate them, followed by an explanation of slices and how they differ.

## Arrays

Arrays are collection data structures with a set number of elements. Because the size of an array is static, the data structure only needs to allocate memory once, as opposed to a variable length data structure that must dynamically allocate memory so that it can become larger or smaller in the future. Although the fixed length of arrays can make them somewhat rigid to work with, the one-time memory allocation can increase the speed and performance of your program. Because of this, developers typically use arrays when optimizing programs in instances where the data structure will never need a variable amount of elements.

## Defining an Array

Arrays are defined by declaring the size of the array in brackets `[ ]`, followed by the data type of the elements. An array in Go must have all its elements be the same [data type](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go). After the data type, you can declare the individual values of the array elements in curly brackets `{ }`.

The following is the general schema for declaring an array:

```go
[capacity]data_type{element_values}
```

**Note:** It is important to remember that every declaration of a new array creates a distinct type. So, although `[2]int` and `[3]int` both have integer elements, their differing lengths make their data types incompatible.

If you do not declare the values of the array’s elements, the default is zero-valued, which means that the elements of the array will be empty. For integers, this is represented by `0`, and for strings this is represented by an empty string.

For example, the following array `numbers` has three integer elements that do not yet have a value:

```go
var numbers [3]int
```

If you printed `numbers`, you would receive the following output:

```go
Output
[0 0 0]
```

If you would like to assign the values of the elements when you create the array, place the values in curly brackets. An array of strings with set values looks like this:

```go
[4]string{"blue coral", "staghorn coral", "pillar coral", "elkhorn coral"}
```

You can store an array in a variable and print it out:

```go
coral := [4]string{"blue coral", "staghorn coral", "pillar coral", "elkhorn coral"}
fmt.Println(coral)
```

Running a program with the preceding lines would give you the following output:

```go
Output
[blue coral staghorn coral pillar coral elkhorn coral]
```

Notice that there is no delineation between the elements in the array when it is printed, making it difficult to tell where one element ends and another begins. Because of this, it is sometimes helpful to use the `fmt.Printf` function instead, which can format strings before printing them to the screen. Provide the `%q` verb with this command to instruct the function to put quotation marks around the values:

```go
fmt.Printf("%q\n", coral)
```

This will result in the following:

```go
Output
["blue coral" "staghorn coral" "pillar coral" "elkhorn coral"]
```

Now each item is quoted. The `\n` verb instructs to the formatter to add a line return at the end.

With a general idea of how to declare arrays and what they consist of, you can now move on to learning how to specify elements in an array with an index number.

## Indexing Arrays (and Slices)

Each element in an array (and also a slice) can be called individually through indexing. Each element corresponds to an index number, which is an `int` value starting from the index number `0` and counting up.

We will use an array in the following examples, but you could use a slice as well, since they are identical in how you index both of them.

For the array `coral`, the index breakdown looks like this:

“blue coral” “staghorn coral” “pillar coral” “elkhorn coral”

0 1 2 3

The first element, the string `"blue coral"`, starts at index `0`, and the slice ends at index `3` with the element `"elkhorn coral"`.

Because each element in a slice or array has a corresponding index number, we’re able to access and manipulate them in the same ways we can with other sequential data types.

Now we can call a discrete element of the slice by referring to its index number:

```go
fmt.Println(coral[1])
```

```go
Output
staghorn coral
```

The index numbers for this slice range from `0-3`, as shown in the previous table. So to call any of the elements individually, we would refer to the index numbers like this:

```go
coral[0] = "blue coral"
coral[1] = "staghorn coral"
coral[2] = "pillar coral"
coral[3] = "elkhorn coral"
```

If we call the array `coral` with an index number of any that is greater than `3`, it will be out of range as it will not be valid:

```go
fmt.Println(coral[18])
```

```go
Output
panic: runtime error: index out of range
```

When indexing an array or slice, you must always use a positive number. Unlike some languages that let you index backwards with a negative number, doing that in Go will result in an error:

```go
fmt.Println(coral[-1])
```

```go
Output
invalid array index -1 (index must be non-negative)
```

We can concatenate string elements in an array or slice with other strings using the `+` operator:

```go
fmt.Println("Sammy loves " + coral[0])
```

```go
Output
Sammy loves blue coral    
```

We were able to concatenate the string element at index number `0` with the string `"Sammy loves "`.

With index numbers that correspond to elements within an array or a slice, we’re able to access each element discretely and work with those elements. To demonstrate this, we will next look at how to modify an element at a certain index.

## Modifying Elements

We can use indexing to change elements within an array or slice by setting an index numbered element equal to a different value. This gives us greater control over the data in our slices and arrays, and will allow us to programmatically manipulate individual elements.

If we want to change the string value of the element at index `1` of the array `coral` from `"staghorn coral"` to `"foliose coral"`, we can do so like this:

```go
coral[1] = "foliose coral"
```

Now when we print `coral`, the array will be different:

```go
fmt.Printf("%q\n", coral)
```

```go
Output
["blue coral" "foliose coral" "pillar coral" "elkhorn coral"]
```

Now that you know how to manipulate individual elements of an array or a slice, let’s look at a couple of functions that will give you more flexibility when working with collection data types.

## Counting Elements with `len()`

In Go, `len()` is a built-in function made to help you work with arrays and slices. Like with strings, you can calculate the length of an array or slice by using `len()` and passing in the array or slice as a parameter.

For example, to find how many elements are in the `coral` array, you would use:

```go
len(coral)
```

If you print out the length for the array `coral`, you’ll receive the following output:

```go
Output
4
```

This gives the length of the array `4` in the `int` data type, which is correct because the array `coral` has four items:

```go
coral := [4]string{"blue coral", "foliose coral", "pillar coral", "elkhorn coral"}    
```

If you create an array of integers with more elements, you could use the `len()` function on this as well:

```go
numbers := [13]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12}
fmt.Println(len(numbers))
```

This would result in the following output:

```go
Output
13
``` 

Although these example arrays have relatively few items, the `len()` function is especially useful when determining how many elements are in very large arrays.

Next, we will cover how to add an element to a collection data type, and demonstrate how, because of the fixed length of arrays, appending these static data types will result in an error.

## Appending Elements with `append()`

`append()` is a built-in method in Go that adds elements to a collection data type. However, this method will not work when used with an array. As mentioned before, the primary way in which arrays are different from slices is that the size of an array cannot be modified. This means that while you can change the values of elements in an array, you can’t make the array larger or smaller after it has been defined.

Let’s consider your `coral` array:

```go
coral := [4]string{"blue coral", "foliose coral", "pillar coral", "elkhorn coral"}   
```

Say you want to add the item `"black coral"` to this array. If you try to use the `append()` function with the array by typing:

```go
coral = append(coral, "black coral")
```

You will receive an error as your output:

```go
Output
first argument to append must be slice; have [4]string
```

To fix this, let’s learn more about the slice data type, how to define a slice, and how to convert from an array to a slice.

## Slices

A _slice_ is a data type in Go that is a _mutable_, or changeable, ordered sequence of elements. Since the size of a slice is variable, there is a lot more flexibility when using them; when working with data collections that may need to expand or contract in the future, using a slice will ensure that your code does not run into errors when trying to manipulate the length of the collection. In most cases, this mutability is worth the possible memory re-allocation sometimes required by slices when compared to arrays. When you need to store a lot of elements or iterate over elements and you want to be able to readily modify those elements, you’ll likely want to work with the slice data type.

### Defining a Slice

Slices are defined by declaring the data type preceded by an empty set of square brackets (`[]`) and a list of elements between curly brackets (`{}`). You’ll notice that, as opposed to arrays that require an `int` in between the brackets to declare a specific length, a slice has nothing between the brackets, representing its variable length.

Let’s create a slice that contains elements of the string data type:

```go
seaCreatures := []string{"shark", "cuttlefish", "squid", "mantis shrimp", "anemone"}
```

When we print out the slice, we can see the elements that are in the slice:

```go
fmt.Printf("%q\n", seaCreatures)
```

This will result in the following:

```go
Output
["shark" "cuttlefish" "squid" "mantis shrimp" "anemone"]
```

If you would like to create a slice of a certain length without populating the elements of the collection yet, you can use the built-in `make()` function:

```go
oceans := make([]string, 3)
```

If you printed this slice, you would get:

```go
Output
["" "" ""]
```

If you want to pre-allocate the memory for a certain capacity, you can pass in a third argument to `make()`:

```go
oceans := make([]string, 3, 5)
```

This would make a zeroed slice with a length of `3` and a pre-allocated capacity of `5` elements.

You now know how to declare a slice. However, this does not yet solve the error we had with the `coral` array earlier. To use the `append()` function with `coral`, you will first have to learn how to slice out sections of an array.

## Slicing Arrays into Slices

By using index numbers to determine beginning and endpoints, you can call a subsection of the values within an array. This is called _slicing_ the array, and you can do this by creating a range of index numbers separated by a colon, in the form of `[first_index:second_index]`. It is important to note however, that when slicing an array, the result is a slice, not an array.

Let’s say you would like to just print the middle items of the `coral` array, without the first and last element. You can do this by creating a slice starting at index `1` and ending just before index `3`:

```go
fmt.Println(coral[1:3])
```

Running a program with this line would yield the following:

```go
Output
[foliose coral pillar coral]
```

When creating a slice, as in `[1:3]`, the first number is where the slice starts (inclusive), and the second number is the sum of the first number and the total number of elements you would like to retrieve:

```go
array[starting_index : (starting_index + length_of_slice)]
```

In this instance, you called the second element (or index 1) as the starting point, and called two elements in total. This is how the calculation would look:

```go
array[1 : (1 + 2)]
```

Which is how you arrived at this notation:

```go
coral[1:3]
```

If you want to set the beginning or end of the array as a starting or end point of the slice, you can omit one of the numbers in the `array[first_index:second_index]` syntax. For example, if you want to print the first three items of the array `coral` — which would be `"blue coral"`, `"foliose coral"`, and `"pillar coral"` — you can do so by typing:

```go
fmt.Println(coral[:3])
```

This will print:

```go
Output
[blue coral foliose coral pillar coral]
```

This printed the beginning of the array, stopping right before index `3`.

To include all the items at the end of an array, you would reverse the syntax:

```go
fmt.Println(coral[1:])
```

This would give the following slice:

```go
Output
[foliose coral pillar coral elkhorn coral]
```

This section discussed calling individual parts of an array by slicing out subsections. Next, you’ll learn how to use slicing to convert entire arrays into slices.

## Converting from an Array to a Slice

If you create an array and decide that you need it to have a variable length, you can convert it to a slice. To convert an array to a slice, use the slicing process you learned in the **Slicing Arrays into Slices** step of this tutorial, except this time select the entire slice by omitting both of the index numbers that would determine the endpoints:

```go
coral[:]
```

Keep in mind that you can’t convert the variable `coral` to a slice itself, since once a variable is defined in Go, its type can’t be changed. To work around this, you can copy the entire contents of the array into a new variable as a slice:

```go
coralSlice := coral[:]
```

If you printed `coralSlice`, you would receive the following output:

```go
Output
[blue coral foliose coral pillar coral elkhorn coral]
```

Now, try to add the `black coral` element like in the array section, using `append()` with the newly converted slice:

```go
coralSlice = append(coralSlice, "black coral")
fmt.Printf("%q\n", coralSlice)
```

This will output the slice with the added element:

```go
Output
["blue coral" "foliose coral" "pillar coral" "elkhorn coral" "black coral"]
```

We can also add more than one element in a single `append()` statement:

```go
coralSlice = append(coralSlice, "antipathes", "leptopsammia")
```

```go
Output
["blue coral" "foliose coral" "pillar coral" "elkhorn coral" "black coral" "antipathes" "leptopsammia"]
```

To combine two slices together, you can use `append()`, but you must expand the second argument to append using the `...` expansion syntax:

```go
moreCoral := []string{"massive coral", "soft coral"}
coralSlice = append(coralSlice, moreCoral...)
```

```go
Output
["blue coral" "foliose coral" "pillar coral" "elkhorn coral" "black coral" "antipathes" "leptopsammia" "massive coral" "soft coral"]    
```

Now that you have learned how to append an element to your slice, we will take a look at how to remove one.

### Removing an Element from a Slice

Unlike other languages, Go does not provide any built-in functions to remove an element from a slice. Items need to be removed from a slice by slicing them out.

To remove an element, you must slice out the items before that element, slice out the items after that element, then append these two new slices together without the element that you wanted to remove.

If `i` is the index of the element to be removed, then the format of this process would look like the following:

```go
slice = append(slice[:i], slice[i+1:]...)
```

From `coralSlice`, let’s remove the item `"elkhorn coral"`. This item is located at the index position of `3`.

```go
coralSlice := []string{"blue coral", "foliose coral", "pillar coral", "elkhorn coral", "black coral", "antipathes", "leptopsammia", "massive coral", "soft coral"}
    
coralSlice = append(coralSlice[:3], coralSlice[4:]...)
    
fmt.Printf("%q\n", coralSlice)
```

```go
Output["blue coral" "foliose coral" "pillar coral" "black coral" "antipathes" "leptopsammia" "massive coral" "soft coral"]    
```

Now the element at index position `3`, the string `"elkhorn coral"`, is no longer in our slice `coralSlice`.

We can also delete a range with the same approach. Say we wanted to remove not only the item `"elkhorn coral"`, but `"black coral"` and `"antipathes"` as well. We can use a range in the expression to accomplish this:

```go
coralSlice := []string{"blue coral", "foliose coral", "pillar coral", "elkhorn coral", "black coral", "antipathes", "leptopsammia", "massive coral", "soft coral"}
    
coralSlice = append(coralSlice[:3], coralSlice[6:]...)
    
fmt.Printf("%q\n", coralSlice)
```

This code will take out index `3`, `4`, and `5` from the slice:

```go
Output["blue coral" "foliose coral" "pillar coral" "leptopsammia" "massive coral" "soft coral"]
```

Now that you know how to add and remove elements from a slice, let’s look at how to measure the amount of data a slice can hold at any given time.

## Measuring the Capacity of a Slice with `cap()`

Since slices have a variable length, the `len()`method is not the best option to determine the size of this data type. Instead, you can use the `cap()` function to learn the capacity of a slice. This will show you how many elements a slice can hold, which is determined by how much memory has already been allocated for the slice.

**Note:** Because the length and capacity of an array are always the same, the `cap()` function will not work on arrays.

A common use for `cap()` is to create a slice with a preset number of elements and then fill in those elements programmatically. This avoids potential unnecessary allocations that could occur by using `append()` to add elements beyond the capacity currently allocated for.

Let’s take the scenario where we want to make a list of numbers, `0` through `3`. We can use `append()` in a loop to do so, or we can pre-allocate the slice first and use `cap()` to loop through to fill the values.

First, we can look at using `append()`:

```go
numbers := []int{}
for i := 0; i < 4; i++ {
    numbers = append(numbers, i)
}
fmt.Println(numbers)
```

```go
Output
[0 1 2 3]
```

In this example, we created a slice, and then created a `for` loop that would iterate four times. Each iteration appended the current value of the loop variable `i` into the index of the `numbers` slice. However, this could lead to unnecessary memory allocations that could slow down your program. When adding to an empty slice, each time you make a call to append, the program checks the capacity of the slice. If the added element makes the slice exceed this capacity, the program will allocate additional memory to account for it. This creates additional overhead in your program and can result in a slower execution.

Now let’s populate the slice without using `append()` by pre-allocating a certain length/capacity:

```go
numbers := make([]int, 4)
for i := 0; i < cap(numbers); i++ {
    numbers[i] = i
}
    
fmt.Println(numbers)
```

```go
Output
[0 1 2 3]
```

In this example, we used `make()` to create a slice and had it pre-allocate `4` elements. We then used the `cap()` function in the loop to iterate through each zeroed element, filling each until it reached the pre-allocated capacity. In each loop, we placed the current value of the loop variable `i` into the index of the `numbers` slice.

While the `append()` and the `cap()` strategies are both functionally equivalent, the `cap()` example avoids any additional memory allocations that would have been needed using the `append()` function.

## Constructing Multidimensional Slices

You can also define slices that consist of other slices as elements, with each bracketed list enclosed inside the larger brackets of the parent slice. Collections of slices like these are called _multidimensional slices_. These can be thought of as depicting multidimensional coordinates; for example, a collection of five slices that are each six elements long could represent a two-dimensional grid with a horizontal length of five and a vertical height of six.

Let’s examine the following multidimensional slice:

```go
seaNames := [][]string{{"shark", "octopus", "squid", "mantis shrimp"}, {"Sammy", "Jesse", "Drew", "Jamie"}}    
```

To access an element within this slice, we will have to use multiple indices, one for each dimension of the construct:

```go
fmt.Println(seaNames[1][0])
fmt.Println(seaNames[0][0])    
```

In the preceding code, we first identify the element at index `0` of the slice at index `1`, then we indicate the element at index `0` of the slice at index `0`. This will yield the following:

```go
OutputSammy
shark
```

The following are the index values for the rest of the individual elements:

```go
seaNames[0][0] = "shark"
seaNames[0][1] = "octopus"
seaNames[0][2] = "squid"
seaNames[0][3] = "mantis shrimp"
    
seaNames[1][0] = "Sammy"
seaNames[1][1] = "Jesse"
seaNames[1][2] = "Drew"
seaNames[1][3] = "Jamie"
```

When working with multidimensional slices, it is important to keep in mind that you’ll need to refer to more than one index number in order to access specific elements within the relevant nested slice.

## Conclusion

In this tutorial, you learned the foundations of working with arrays and slices in Go. You went through multiple exercises to demonstrate how arrays are fixed in length, whereas slices are variable in length, and discovered how this difference affects the situational uses of these data structures.

To continue studying data structures in Go, check out our article on [Understanding Maps in Go](https://www.digitalocean.com/community/tutorials/understanding-maps-in-go), or explore the entire [How To Code in Go](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go) series.