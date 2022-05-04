# Customizing Go Binaries with Build Tags

### Introduction

In Go, a *build tag*, or a build constraint, is an identifier added to a piece of code that determines when the file should be included in a package during the `build` process. This allows you to build different versions of your Go application from the same source code and to toggle between them in a fast and organized manner. Many developers use build tags to improve the workflow of building cross-platform compatible applications, such as programs that require code changes to account for variances between different operating systems. Build tags are also used for [integration testing](https://en.wikipedia.org/wiki/Integration_testing), allowing you to quickly switch between the integrated code and the code with a [mock service or stub](https://en.wikipedia.org/wiki/Mock_object), and for differing levels of feature sets within an application.

Let’s take the problem of differing customer feature sets as an example. When writing some applications, you may want to control which features to include in the binary, such as an application that offers **Free**, **Pro**, and **Enterprise** levels. As the customer increases their subscription level in these applications, more features become unlocked and available. To solve this problem, you could maintain separate projects and try to keep them in sync with each other through the use of `import` statements. While this approach would work, over time it would become tedious and error prone. An alternative approach would be to use build tags.

In this article, you will use build tags in Go to generate different executable binaries that offer Free, Pro, and Enterprise feature sets of a sample application. Each will have a different set of features available, with the Free version being the default.

## Prerequisites

To follow the example in this article, you will need:

* A Go workspace set up by following [How To Install Go and Set Up a Local Programming Environment](https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-go).
## Building the Free Version

Let’s start by building the Free version of the application, as it will be the default when running `go build` without any build tags. Later on, we will use build tags to selectively add other parts to our program.

In the `src` directory, create a folder with the name of your application. This tutorial will use `app`:

```plain
mkdirapp
```

Move into this folder:

```plain
cdapp
```

Next, make a new text file in your text editor of choice named `main.go`:

```plain
nano main.go
```

Now, we’ll define the Free version of the application. Add in the following contents to `main.go`:

main.go

```plain
package main

import "fmt"

var features = []string{
  "Free Feature #1",
  "Free Feature #2",
}

func main() {
  for _, f := range features {
    fmt.Println(">", f)
  }
}
```

In this file, we created a program that declares a [slice](https://www.digitalocean.com/community/tutorials/understanding-arrays-and-slices-in-go#slices) named `features`, which holds two [strings](https://www.digitalocean.com/community/tutorials/an-introduction-to-working-with-strings-in-go) that represent the features of our Free application. The `main()` function in the application uses a `for`[ loop to ](https://www.digitalocean.com/community/tutorials/how-to-construct-for-loops-in-go#looping-through-sequential-data-types-with-rangeclause)`range` through the `features` slice and print all of the features available to the screen.

Save and exit the file. Now that this file is saved, we will no longer have to edit it for the rest of the article. Instead we will use build tags to change the features of the binaries we will build from it.

Build and run the program:

```plain
go build
./app
```

You’ll receive the following output:

```plain
Output
> Free Feature #1
> Free Feature #2
```
The program has printed out our two free features, completing the Free version of our app.
So far, you created an application that has a very basic feature set. Next, you will build a way to add more features into the application at build time.

## Adding the Pro Features With `go build`

We have so far avoided making changes to `main.go`, simulating a common production environment in which code needs to be added without changing and possibly breaking the main code. Since we can’t edit the `main.go` file, we’ll need to use another mechanism for injecting more features into the `features` slice using build tags.

Let’s create a new file called `pro.go` that will use an `init()` function to append more features to the `features` slice:

```plain
nano pro.go
```

Once the editor has opened the file, add the following lines:

pro.go

```plain
package main

func init() {
  features = append(features,
    "Pro Feature #1",
    "Pro Feature #2",
  )
}
```

In this code, we used `init()` to run code before the `main()` function of our application, followed by `append()` to add the Pro features to the `features` slice. Save and exit the file.

Compile and run the application using `go build`:

```plain
go build
```

Since there are now two files in our current directory (`pro.go` and `main.go`), `go build` will create a binary from both of them. Execute this binary:

```plain
./app
```

This will give you the following feature set:

```plain
Output
> Free Feature #1
> Free Feature #2
> Pro Feature #1
> Pro Feature #2
```
The application now includes both the Pro and the Free features. However, this is not desirable: since there is no distinction between versions, the Free version now includes the features that are supposed to be only available in the Pro version. To fix this, you could include more code to manage the different tiers of the application, or you could use build tags to tell the Go tool chain which `.go` files to build and which to ignore. Let’s add build tags in the next step.
## Adding Build Tags

You can now use build tags to distinguish the Pro version of your application from the Free version.

Let’s start by examining what a build tag looks like:

```plain
// +build tag_name
```

By putting this line of code as the first line of your package and replacing `tag_name` with the name of your build tag, you will tag this package as code that can be selectively included in the final binary. Let’s see this in action by adding a build tag to the `pro.go` file to tell the `go build` command to ignore it unless the tag is specified. Open up the file in your text editor:

```plain
nano pro.go
```

Then add the following highlighted line:

pro.go

```plain
// +build pro

package main

func init() {
  features = append(features,
    "Pro Feature #1",
    "Pro Feature #2",
  )
}
```
At the top of the `pro.go` file, we added `// +build pro` followed by a blank newline. This trailing newline is required, otherwise Go interprets this as a comment. Build tag declarations must also be at the very top of a `.go` file. Nothing, not even comments, can be above build tags.
The `+build` declaration tells the `go build` command that this isn’t a comment, but instead is a build tag. The second part is the `pro` tag. By adding this tag at the top of the `pro.go` file, the `go build` command will now only include the `pro.go` file with the `pro` tag is present.

Compile and run the application again:

```plain
go build
./app
```

You’ll receive the following output:

```plain
Output
> Free Feature #1
> Free Feature #2
```
Since the `pro.go` file requires a `pro` tag to be present, the file is ignored and the application compiles without it.
When running the `go build` command, we can use the `-tags` flag to conditionally include code in the compiled source by adding the tag itself as an argument. Let’s do this for the `pro` tag:

```plain
go build -tags pro
```

This will output the following:

```plain
Output
> Free Feature #1
> Free Feature #2
> Pro Feature #1
> Pro Feature #2
```
Now we only get the extra features when we build the application using the `pro` build tag.
This is fine if there are only two versions, but things get complicated when you add in more tags. To add in the Enterprise version of our app in the next step, we will use multiple build tags joined together with Boolean logic.

## Build Tag Boolean Logic

When there are multiple build tags in a Go package, the tags interact with each other using [Boolean logic](https://www.digitalocean.com/community/tutorials/understanding-boolean-logic-in-go). To demonstrate this, we will add the Enterprise level of our application using both the `pro` tag and the `enterprise` tag.

In order to build an Enterprise binary, we will need to include both the default features, the Pro level features, and a new set of features for Enterprise. First, open an editor and create a new file, `enterprise.go`, that will add the new Enterprise features:

```plain
nano enterprise.go
```

The contents of `enterprise.go` will look almost identical to `pro.go` but will contain new features. Add the following lines to the file:

enterprise.go

```plain
package main

func init() {
  features = append(features,
    "Enterprise Feature #1",
    "Enterprise Feature #2",
  )
}
```

Save and exit the file.

Currently the `enterprise.go` file does not have any build tags, and as you learned when you added `pro.go`, this means that these features will be added to the Free version when executing `go.build`. For `pro.go`, you added `// +build pro` and a newline to the top of the file to tell `go build` that it should only be included when `-tags pro` is used. In this situation, you only needed one build tag to accomplish the goal. When adding the new Enterprise features, however, you first must also have the Pro features.

Let’s add support for the `pro` build tag to `enterprise.go` first. Open the file with your text editor:

```plain
nano enterprise.go
```

Next add the build tag before the `package main` declaration and make sure to include a newline after the build tag:

enterprise.go

```plain
// +build pro

package main

func init() {
  features = append(features,
    "Enterprise Feature #1",
    "Enterprise Feature #2",
  )
}
```
Save and exit the file.
Compile and run the application without any tags:

```plain
go build
./app
```

You’ll receive the following output:

```plain
Output
> Free Feature #1
> Free Feature #2
```
The Enterprise features no longer show up in the Free version. Now let’s add the `pro` build tag and build and run the application again:
```plain
go build -tags pro
./app
```

You’ll receive the following output:

```plain
Output
> Free Feature #1
> Free Feature #2
> Enterprise Feature #1
> Enterprise Feature #2
> Pro Feature #1
> Pro Feature #2
```
This is still not exactly what we need: The Enterprise features now show up when we try to build the Pro version. To solve this, we need to use another build tag. Unlike the `pro` tag, however, we need to now make sure both the `pro` and `enterprise` features are available.
The Go build system accounts for this situation by allowing the use of some basic Boolean logic in the build tags system.

Let’s open `enterprise.go` again:

```plain
nano enterprise.go
```

Add another build tag, `enterprise`, on the same line as the `pro` tag:

enterprise.go

```plain
// +build pro enterprise

package main

func init() {
  features = append(features,
    "Enterprise Feature #1",
    "Enterprise Feature #2",
  )
}
```
Save and close the file.
Now let’s compile and run the application with the new `enterprise` build tag.

```plain
go build -tags enterprise
./app
```

This will give the following:

```plain
Output
> Free Feature #1
> Free Feature #2
> Enterprise Feature #1
> Enterprise Feature #2
```
Now we have lost the Pro features. This is because when we put multiple build tags on the same line in a `.go` file, `go build` interprets them as using `OR` logic. With the addition of the line `// +build pro enterprise`, the `enterprise.go` file will be built if **either** the `pro` build tag or the `enterprise` build tag is present. We need to set up the build tags correctly to require **both** and use `AND` logic instead.
Instead of putting both tags on the same line, if we put them on separate lines, then `go build` will interpret those tags using `AND` logic.

Open `enterprise.go` once again and let’s separate the build tags onto multiple lines.

enterprise.go

```plain
// +build pro
// +build enterprise

package main

func init() {
  features = append(features,
    "Enterprise Feature #1",
    "Enterprise Feature #2",
  )
}
```
Now compile and run the application with the new `enterprise` build tag.
```plain
go build -tags enterprise
./app
```

You’ll receive the following output:

```plain
Output
> Free Feature #1
> Free Feature #2
```
Still not quite there: Because an `AND` statement requires both elements to be considered `true`, we need to use both `pro` and `enterprise` build tags.
Let’s try again:

```plain
go build -tags "enterprise pro"
./app
```

You’ll receive the following output:

```plain
Output
> Free Feature #1
> Free Feature #2
> Enterprise Feature #1
> Enterprise Feature #2
> Pro Feature #1
> Pro Feature #2
```
Now our application can be built from the same source tree in multiple ways unlocking the features of the application accordingly.
In this example, we used a new `// +build` tag to signify `AND` logic, but there are alternative ways to represent Boolean logic with build tags. The following table holds some examples of other syntactic formatting for build tags, along with their Boolean equivalent:

|Build Tag Syntax|Build Tag Sample|Boolean Statement|
|:----|:----|:----|
|Space-separated elements|// +build pro enterprise|pro OR enterprise|
|Comma-separated elements|// +build pro,enterprise|pro AND enterprise|
|Exclamation point elements|// +build !pro|NOT pro|

## Conclusion

In this tutorial, you used build tags to allow you to control which of your code got compiled into the binary. First, you declared build tags and used them with `go build`, then you combined multiple tags with Boolean logic. You then built a program that represented the different feature sets of a Free, Pro, and Enterprise version, showing the powerful level of control that build tags can give you over your project.

If you’d like to learn more about build tags, take a look at the [Golang documentation on the subject](https://golang.org/pkg/go/build/#hdr-Build_Constraints), or continue to explore our [How To Code in Go series](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go).

 

