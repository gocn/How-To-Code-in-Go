# How To Use Struct Tags in Go

## Introduction

Structures, or structs, are used to collect multiple pieces of information together in one unit. These [collections of information](https://www.digitalocean.com/community/tutorials/defining-structs-in-go) are used to describe higher-level concepts, such as an `Address` composed of a `Street`, `City`, `State`, and `PostalCode`. When you read this information from systems such as databases, or APIs, you can use struct tags to control how this information is assigned to the fields of a struct. Struct tags are small pieces of metadata attached to fields of a struct that provide instructions to other Go code that works with the struct.

## What Does a Struct Tag Look Like?

Go struct tags are annotations that appear after the type in a Go struct declaration. Each tag is composed of short strings associated with some corresponding value.

A struct tag looks like this, with the tag offset with backtick ` characters:

```go
type User struct {
	Name string `example:"name"`
}
```

Other Go code is then capable of examining these structs and extracting the values assigned to specific keys it requests. Struct tags have no effect on the operation of your code without some other code that examines them.

Try this example to see what struct tags look like, and that without code from another package, they will have no effect.

```go
package main

import "fmt"

type User struct {
	Name string `example:"name"`
}

func (u *User) String() string {
	return fmt.Sprintf("Hi! My name is %s", u.Name)
}

func main() {
	u := &User{
		Name: "Sammy",
	}

	fmt.Println(u)
}
```

This will output:

```bash
Output
Hi! My name is Sammy
```

This example defines a `User` type with a `Name` field. The `Name` field has been given a struct tag of `example:"name"`. We would refer to this specific tag in conversation as the “example struct tag” because it uses the word “example” as its key. The `example` struct tag has the value `"name"` for the `Name` field. On the `User` type, we also define the `String()` method required by the `fmt.Stringer` interface. This will be called automatically when we pass the type to `fmt.Println` and gives us a chance to produce a nicely formatted version of our struct.

Within the body of `main`, we create a new instance of our `User` type and pass it to `fmt.Println`. Even though the struct had a struct tag present, we see that it has no effect on the operation of this Go code. It will behave exactly the same if the struct tag were not present.

To use struct tags to accomplish something, other Go code must be written to examine structs at runtime. The standard library has packages that use struct tags as part of their operation. The most popular of these is the `encoding/json` package.

## Encoding JSON

JavaScript Object Notation (JSON) is a textual format for encoding collections of data organized under different string keys. It’s commonly used to communicate data between different programs as the format is simple enough that libraries exist to decode it in many different languages. The following is an example of JSON:

```json
{
  "language": "Go",
  "mascot": "Gopher"
}
```

This JSON object contains two keys, `language` and `mascot`. Following these keys are the associated values. Here the `language` key has a value of `Go` and `mascot` is assigned the value `Gopher`.

The JSON encoder in the standard library makes use of struct tags as annotations indicating to the encoder how you would like to name your fields in the JSON output. These JSON encoding and decoding mechanisms can be found in the `encoding/json` [package](https://godoc.org/encoding/json).

Try this example to see how JSON is encoded without struct tags:

```go
package main

import (
	"encoding/json"
	"fmt"
	"log"
	"os"
	"time"
)

type User struct {
	Name          string
	Password      string
	PreferredFish []string
	CreatedAt     time.Time
}

func main() {
	u := &User{
		Name:      "Sammy the Shark",
		Password:  "fisharegreat",
		CreatedAt: time.Now(),
	}

	out, err := json.MarshalIndent(u, "", "  ")
	if err != nil {
		log.Println(err)
		os.Exit(1)
	}

	fmt.Println(string(out))
}
```

This will print the following output:

```bash
Output
{
  "Name": "Sammy the Shark",
  "Password": "fisharegreat",
  "CreatedAt": "2019-09-23T15:50:01.203059-04:00"
}
```

We defined a struct describing a user with fields including their name, password, and the time the user was created. Within the `main` function, we create an instance of this user by supplying values for all fields except `PreferredFish` (Sammy likes all fish). We then passed the instance of `User` to the `json.MarshalIndent` function. This is used so we can more easily see the JSON output without using an external formatting tool. This call could be replaced with `json.Marshal(u)` to receive JSON without any additional whitespace. The two additional arguments to `json.MarshalIndent` control the prefix to the output (which we have omitted with the empty string), and the characters to use for indenting, which here are two space characters. Any errors produced from `json.MarshalIndent` are logged and the program terminates using `os.Exit(1)`. Finally, we cast the `[]byte` returned from `json.MarshalIndent` to a `string` and hand the resulting string to `fmt.Println` for printing on the terminal.

The fields of the struct appear exactly as we named them. This is not the typical JSON style that you may expect, which uses camel casing for names of fields. You’ll change the names of the field to follow camel case style in this next example. As you’ll see when you run this example, this won’t work because the desired field names conflict with Go’s rules about exported field names.

```go
package main

import (
	"encoding/json"
	"fmt"
	"log"
	"os"
	"time"
)

type User struct {
	name          string
	password      string
	preferredFish []string
	createdAt     time.Time
}

func main() {
	u := &User{
		name:      "Sammy the Shark",
		password:  "fisharegreat",
		createdAt: time.Now(),
	}

	out, err := json.MarshalIndent(u, "", "  ")
	if err != nil {
		log.Println(err)
		os.Exit(1)
	}

	fmt.Println(string(out))
}
```

This will present the following output:

```bash
Output
{}
```

In this version, we’ve altered the names of the fields to be camel cased. Now `Name` is `name`, `Password` is `password`, and finally `CreatedAt` is `createdAt`. Within the body of `main` we’ve changed the instantiation of our struct to use these new names. We then pass the struct to the `json.MarshalIndent` function as before. The output, this time is an empty JSON object, `{}`.

Camel casing fields properly requires that the first character be lower-cased. While JSON doesn’t care how you name your fields, Go does, as it indicates the visibility of the field outside of the package. Since the `encoding/json` package is a separate package from the `main` package we’re using, we must uppercase the first character in order to make it visible to `encoding/json`. It would seem that we’re at an impasse, and we need some way to convey to the JSON encoder what we would like this field to be named.

## Using Struct Tags to Control Encoding

You can modify the previous example to have exported fields that are properly encoded with camel-cased field names by annotating each field with a struct tag. The struct tag that `encoding/json` recognizes has a key of `json` and a value that controls the output. By placing the camel-cased version of the field names as the value to the `json` key, the encoder will use that name instead. This example fixes the previous two attempts:

```go
package main

import (
	"encoding/json"
	"fmt"
	"log"
	"os"
	"time"
)

type User struct {
	Name          string    `json:"name"`
	Password      string    `json:"password"`
	PreferredFish []string  `json:"preferredFish"`
	CreatedAt     time.Time `json:"createdAt"`
}

func main() {
	u := &User{
		Name:      "Sammy the Shark",
		Password:  "fisharegreat",
		CreatedAt: time.Now(),
	}

	out, err := json.MarshalIndent(u, "", "  ")
	if err != nil {
		log.Println(err)
		os.Exit(1)
	}

	fmt.Println(string(out))
}
```

This will output:

```bash
Output
{
  "name": "Sammy the Shark",
  "password": "fisharegreat",
  "preferredFish": null,
  "createdAt": "2019-09-23T18:16:17.57739-04:00"
}
```

We’ve changed the field names back to be visible to other packages by capitalizing the first letters of their names. However, this time we’ve added struct tags in the form of `json:"name"`, where `"name"` was the name we wanted `json.MarshalIndent` to use when printing our struct as JSON.

We’ve now successfully formatted our JSON correctly. Notice, however, that the fields for some values were printed even though we did not set those values. The JSON encoder can eliminate these fields as well, if you like.

## Removing Empty JSON Fields

Most commonly, we want to suppress outputting fields that are unset in JSON. Since all types in Go have a “zero value,” some default value that they are set to, the `encoding/json` package needs additional information to be able to tell that some field should be considered unset when it assumes this zero value. Within the value part of any `json` struct tag, you can suffix the desired name of your field with `,omitempty` to tell the JSON encoder to suppress the output of this field when the field is set to the zero value. The following example fixes the previous examples to no longer output empty fields:

```go
package main

import (
	"encoding/json"
	"fmt"
	"log"
	"os"
	"time"
)

type User struct {
	Name          string    `json:"name"`
	Password      string    `json:"password"`
	PreferredFish []string  `json:"preferredFish,omitempty"`
	CreatedAt     time.Time `json:"createdAt"`
}

func main() {
	u := &User{
		Name:      "Sammy the Shark",
		Password:  "fisharegreat",
		CreatedAt: time.Now(),
	}

	out, err := json.MarshalIndent(u, "", "  ")
	if err != nil {
		log.Println(err)
		os.Exit(1)
	}

	fmt.Println(string(out))
}
```

This example will output:

```bash
Output
{
  "name": "Sammy the Shark",
  "password": "fisharegreat",
  "createdAt": "2019-09-23T18:21:53.863846-04:00"
}
```

We’ve modified the previous examples so that the `PreferredFish` field now has the struct tag `json:"preferredFish,omitempty"`. The presence of the `,omitempty` augmentation causes the JSON encoder to skip that field, since we decided to leave it unset. This had the value `null` in our previous examples’ outputs.

This output is looking much better, but we’re still printing out the user’s password. The `encoding/json` package provides another way for us to ignore private fields entirely.

## Ignoring Private Fields

Some fields must be exported from structs so that other packages can correctly interact with the type. However, the nature of these fields may be sensitive, so in these circumstances, we would like the JSON encoder to ignore the field entirely—even when it is set. This is done using the special value `-` as the value argument to a `json:` struct tag.

This example fixes the issue of exposing the user’s password.

```go
package main

import (
	"encoding/json"
	"fmt"
	"log"
	"os"
	"time"
)

type User struct {
	Name      string    `json:"name"`
	Password  string    `json:"-"`
	CreatedAt time.Time `json:"createdAt"`
}

func main() {
	u := &User{
		Name:      "Sammy the Shark",
		Password:  "fisharegreat",
		CreatedAt: time.Now(),
	}

	out, err := json.MarshalIndent(u, "", "  ")
	if err != nil {
		log.Println(err)
		os.Exit(1)
	}

	fmt.Println(string(out))
}
```

When you run this example, you’ll see this output:

```bash
Output
{
  "name": "Sammy the Shark",
  "createdAt": "2019-09-23T16:08:21.124481-04:00"
}
```

The only thing we’ve changed in this example from previous ones is that the password field now uses the special `"-"` value for its `json:` struct tag. We see that in the output from this example that the `password` field is no longer present.

These features of the `encoding/json` package, `,omitempty` and `"-"`, are not standards. What a package decides to do with values of a struct tag depends on its implementation. Because the `encoding/json` package is part of the standard library, other packages have also implemented these features in the same way as a matter of convention. However, it’s important to read the documentation for any third-party package that uses struct tags to learn what is supported and what is not.

## Conclusion

Struct tags offer a powerful means to augment the functionality of code that works with your structs. Many standard library and third-party packages offer ways to customize their operation through the use of struct tags. Using them effectively in your code provides both this customization behavior and succinctly documents how these fields are used to future developers.