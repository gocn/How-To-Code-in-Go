# Understanding Maps in Go

Most modern programming languages have the concept of a _dictionary_ or a _hash_ type. These types are commonly used to store data in pairs with a **key** that maps to a **value**.

In Go, the _map_ data type is what most programmers would think of as the _dictionary_ type. It maps keys to values, making key-value pairs that are a useful way to store data in Go. A map is constructed by using the keyword `map` followed by the key data type in square brackets `[ ]`, followed by the value data type. The key-value pairs are then placed inside curly braces on either side { }:

```go
map[key]value{}
```

You typically use maps in Go to hold related data, such as the information contained in an ID. A map with data looks like this:

```go
map[string]string{"name": "Sammy", "animal": "shark", "color": "blue", "location": "ocean"}
```

In addition to the curly braces, there are also colons throughout the map that connect the key-value pairs. The words to the left of the colons are the keys. Keys can be any _comparable_ type in Go, like `strings`, `ints`, and so on.

The keys in the example map are:

*   `"name"`
*   `"animal"`
*   `"color"`
*   `"location"`

The words to the right of the colons are the values. Values can be any data type. The values in the example map are:

*   `"Sammy"`
*   `"shark"`
*   `"blue"`
*   `"ocean"`

Like the other data types, you can store the map inside a variable, and print it out:

```go
sammy := map[string]string{"name": "Sammy", "animal": "shark", "color": "blue", "location": "ocean"}
fmt.Println(sammy)

```

This would be your output:

```go
Output
map[animal:shark color:blue location:ocean name:Sammy]
```

The order of the key-value pairs may have shifted. In Go, the map data type is unordered. Regardless of the order, the key-value pairs will remain intact, enabling you to access data based on their relational meaning.

## Accessing Map Items

You can call the values of a map by referencing the related keys. Since maps offer key-value pairs for storing data, they can be important and useful items in your Go program.

If you want to isolate Sammy’s username, you can do so by calling `sammy["name"]`; the variable holding your map and the related key. Let’s print that out:

```go
fmt.Println(sammy["name"])
```

And receive the value as output:

```go
Output
Sammy
```    

Maps behave like a database; instead of calling an integer to get a particular index value as you would with a slice, you assign a value to a key and call that key to get its related value.

By invoking the key `"name"` you receive the value of that key, which is `"Sammy"`.

Similarly you can call the remaining values in the `sammy` map using the same format:

```go
fmt.Println(sammy["animal"])
// returns shark
    
fmt.Println(sammy["color"])
// returns blue
    
fmt.Println(sammy["location"])
// returns ocean
```

By making use of the key-value pairs in map data types, you can reference keys to retrieve values.

## Keys and Values

Unlike some programming languages, Go does not have any _convenience_ functions to list out the keys or values of a map. An example of this would be Python’s `.keys()` method for dictionaries. It does, however, allow for iteration by using the `range` operator:

```go
for key, value := range sammy {
    fmt.Printf("%q is the key for the value %q\n", key, value)
}
```

When ranging through a map in Go, it’ll return two values. The first value will be the key, and the second value will be the value. Go will create these variables with the correct data type. In this case, the map key was a `string` so `key` will also be a string. The `value` is also a string:

```go
Output
animal" is the key for the value "shark"
"color" is the key for the value "blue"
"location" is the key for the value "ocean"
"name" is the key for the value "Sammy"
```

To get a list of just the keys, you can use the range operator again. You can declare just one variable to only access the keys:

```go
keys := []string{}
    
for key := range sammy {
    keys = append(keys, key)
}
fmt.Printf("%q", keys)
```

The program begins by declaring a slice to store your keys in.

The output will show only the keys of your map:

```go
Output
["color" "location" "name" "animal"]
```

Again, the keys are not sorted. If you want to sort them, you use the `sort.Strings` function from the [`sort`](https://golang.org/pkg/sort) package:

```go
sort.Strings(keys)
```

With this function, you’ll receive the following output:

```go
Output
["animal" "color" "location" "name"]
```

You can use the same pattern to retrieve just the values in a map. In the next example, you pre-allocate the slice to avoid allocations, thus making the program more efficient:

```go
sammy := map[string]string{"name": "Sammy", "animal": "shark", "color": "blue", "location": "ocean"}
    
items := make([]string, len(sammy))
    
var i int
    
for _, v := range sammy {
    items[i] = v
    i++
}
fmt.Printf("%q", items)
```

First you declare a slice to store your keys in; since you know how many items you need, you can avoid potential memory allocations by defining the slice at the exact same size. You then declare your index variable. As you don’t want the key, you use the `_` operator, when starting your loop, to ignore the key’s value. Your output would be the following:

```go
Output
["ocean" "Sammy" "shark" "blue"]
```

To determine the number of items in a map, you can use the built-in `len` function:

```go
sammy := map[string]string{"name": "Sammy", "animal": "shark", "color": "blue", "location": "ocean"}
fmt.Println(len(sammy))
```

The output displays the number of items in your map:

```go
Output
4
```

Even though Go doesn’t ship with convenience functions to get keys and values, it only takes a few lines of code to retrieve the keys and values when needed.

## Checking Existence

Maps in Go will return the zero value for the value type of the map when the requested key is missing. Because of this, you need an alternative way to differentiate a stored zero, versus a missing key.

Let’s look up a value in a map that you know doesn’t exist and look at the value returned:

```go
counts := map[string]int{}
fmt.Println(counts["sammy"])
```

You’ll see the following output:

```go
Output
0
```

Even though the key `sammy` was not in the map, Go still returned the value of `0`. This is because the value data type is an `int`, and because Go has a zero value for all variables, it returns the zero value of `0`.

In many cases, this is undesirable and would lead to a bug in your program. When looking up the value in a map, Go can return a second, _optional_ value. This second value is a `bool` and will be `true` if the key was found, or `false` if the key was not found. In Go, this is referred to as the `ok` idiom. Even though you could name the variable that captures the second argument anything you want, in Go, you always name it `ok`:

```go
count, ok := counts["sammy"]
```

If the key `sammy` exists in the `counts` map, then `ok` will be `true`. Otherwise `ok` will be false.

You can use the `ok` variable to decide what to do in your program:

```go
if ok {
    fmt.Printf("Sammy has a count of %d\n", count)
} else {
    fmt.Println("Sammy was not found")
}
```

This would result in the following output:

```go
Output
Sammy was not found
```

In Go, you can combine variable declaration and conditional checking with an if/else block. This allows you to use a single statement for this check:

```go
if count, ok := counts["sammy"]; ok {
    fmt.Printf("Sammy has a count of %d\n", count)
} else {
    fmt.Println("Sammy was not found")
}
```

When retrieving a value from a map in Go, it’s always good practice to check for its existence as well to avoid bugs in your program.

## Modifying Maps

Maps are a mutable data structure, so you can modify them. Let’s look at adding and deleting map items in this section.

### Adding and Changing Map Items

Without using a method or function, you can add key-value pairs to maps. You do this using the maps variable name, followed by the key value in square brackets `[ ]`, and using the equal `=` operator to set a new value:

```go
map[key] = value
```

In practice, you can see this work by adding a key-value pair to a map called `usernames`:

```go
usernames := map[string]string{"Sammy": "sammy-shark", "Jamie": "mantisshrimp54"}
    
usernames["Drew"] = "squidly"
fmt.Println(usernames)
```

The output will display the new `Drew:squidly` key-value pair in the map:

```go
Output
map[Drew:squidly Jamie:mantisshrimp54 Sammy:sammy-shark]
```

Because maps are returned unordered, this pair may occur anywhere in the map output. If you use the `usernames` map later in your program file, it will include the additional key-value pair.

You can also use this syntax for modifying the value assigned to a key. In this case, you reference an existing key and pass a different value to it.

Consider a map called `followers` that tracks followers of users on a given network. The user `"drew"` had a bump in followers today, so you need to update the integer value passed to the `"drew"` key. You’ll use the `Println()` function to check that the map was modified:

```go
followers := map[string]int{"drew": 305, "mary": 428, "cindy": 918}
followers["drew"] = 342
fmt.Println(followers)
```

Your output will show the updated value for `drew`:

```go
Output
map[cindy:918 drew:342 mary:428]
```

You see that the number of followers jumped from the integer value of `305` to `342`.

You can use this method for adding key-value pairs to maps with user input. Let’s write a quick program called `usernames.go` that runs on the command line and allows input from the user to add more names and associated usernames:

usernames.go

```go
package main
    
import (
    "fmt"
    "strings"
)
    
func main() {
    usernames := map[string]string{"Sammy": "sammy-shark", "Jamie": "mantisshrimp54"}
    
    for {
        fmt.Println("Enter a name:")
    
        var name string
        _, err := fmt.Scanln(&name)
    
        if err != nil {
            panic(err)
        }
    
        name = strings.TrimSpace(name)
    
        if u, ok := usernames[name]; ok {
            fmt.Printf("%q is the username of %q\n", u, name)
            continue
        }
    
        fmt.Printf("I don't have %v's username, what is it?\n", name)
    
        var username string
        _, err = fmt.Scanln(&username)
    
        if err != nil {
            panic(err)
        }
    
        username = strings.TrimSpace(username)
    
        usernames[name] = username
    
        fmt.Println("Data updated.")
    }
}
```

In `usernames.go` you first define the original map. You then set up a loop to iterate over the names. You request your user to enter a name and declare a variable to store it in. Next, you check to see if you had an error; if so, the program will exit with a _panic_. Because `Scanln` captures the entire input, including the carriage return, you need to remove any space from the input; you do this with the `strings.TrimSpace` function.

The `if` block checks whether the name is present in the map and prints feedback. If the name is present it then continues back to the top of the loop. If the name is not in the map, it provides feedback to the user and then will ask for a new username for the associated name. The program checks again to see if there is an error. With no error, it trims off the carriage return, assigns the username value to the name key, and then prints feedback that the data was updated.

Let’s run the program on the command line:

```go
go run usernames.go
```

You’ll see the following output:

```go
Output
Enter a name:
Sammy
"sammy-shark" is the username of "Sammy"
Enter a name:
Jesse
I don't have Jesse's username, what is it?
JOctopus
Data updated.
Enter a name:
```

When you’re done testing, press `CTRL + C` to escape the program.

This shows how you can modify maps interactively. With this particular program, as soon as you exit the program with `CTRL + C` you’ll lose all your data unless you implement a way to handle reading and writing files.

To summarize, you can add items to maps or modify values with the `map[key] = value` syntax.

### Deleting Map Items

Just as you can add key-value pairs and change values within the map data type, you can also delete items within a map.

To remove a key-value pair from a map, you can use the built-in function `delete()`. The first argument is the map you are deleting from. The second argument is the key you are deleting:

```go
delete(map, key)
```

Let’s define a map of permissions:

```go
permissions := map[int]string{1: "read", 2: "write", 4: "delete", 8: "create", 16:"modify"}
```

You no longer need the `modify` permission, so you’ll remove it from your map. Then you’ll print out the map to confirm it was removed:

```go
permissions := map[int]string{1: "read", 2: "write", 4: "delete", 8: "create", 16: "modify"}
delete(permissions, 16)
fmt.Println(permissions)
```

The output will confirm the deletion:

```go
Output
map[1:read 2:write 4:delete 8:create]
```

The line `delete(permissions, 16)` removes the key-value pair `16:"modify"` from the `permissions` map.

If you would like to clear a map of all of its values, you can do so by setting it equal to an empty map of the same type. This will create a new empty map to use, and the old map will be cleared from memory by the garbage collector.

Let’s remove all the items within the `permissions` map:

```go
permissions = map[int]string{}
fmt.Println(permissions)
```

The output shows that you now have an empty map devoid of key-value pairs:

```go
Output
map[]
```

Because maps are mutable data types, they can be added to, modified, and have items removed and cleared.

## Conclusion

This tutorial explored the map data structure in Go. Maps are made up of key-value pairs and provide a way to store data without relying on indexing. This allows us to retrieve values based on their meaning and relation to other data types.