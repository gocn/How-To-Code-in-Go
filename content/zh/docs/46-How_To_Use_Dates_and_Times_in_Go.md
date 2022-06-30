# How To Use Dates and Times in Go

## Introduction

Software is designed to make it easier to get work done, and for many people, that includes interacting with dates and times. Date and time values show up everywhere in modern software. For example, keeping track of when a car needs service and letting the owner know, keeping track of changes in a database to create an audit log, or just comparing one time to another to determine how long a process took. Therefore, retrieving the current time, manipulating time values to extract information from them, and displaying them to users in an easy-to-understand format are essential properties of an application.

In this tutorial, you will create a Go program to get the current local time of your computer, then print it to the screen in a format that is easier for people to read. Next, you will interpret a string to extract the date and time information. You will also translate the date and time values between two time zones, as well as add or subtract time values to determine the interval between two times.

## Prerequisites

To follow this tutorial, you will need:

-   Go version 1.16 or greater installed. To set this up, follow the [How To Install Go](https://www.digitalocean.com/community/tutorials/how-to-install-go-on-ubuntu-20-04) tutorial for your operating system.

## Getting the Current Time

In this section, you will get the current time using Go’s `time` package. The `time` package in Go’s standard library provides a variety of date- and time-related functions, and can be used to represent a specific point in time using the `time.Time` type. In addition to a time and date, it can also hold information about the time zone the represented date and time are in.

To begin creating a program to explore the `time` package, you’ll need to create a directory for the files. This directory can be created anywhere you’d like on your computer, but many developers tend to have a directory for their projects. In this tutorial, you’ll use a directory named `projects`.

Make the `projects` directory and navigate to it:

```shell
mkdir projects
cd projects
```

From the `projects` directory, run `mkdir` to create a `datetime` directory and then use `cd` to navigate to it:

```shell
mkdir datetime
cd datetime
```

Once you have your project directory created, open a `main.go` file using `nano`, or your preferred editor:

```shell
nano main.go
```

In the `main.go` file, add a `main` function that will get the current time and print it out:

projects/datetime/main.go

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	currentTime := time.Now()
	fmt.Println("The time is", currentTime)
}
```

In this program, the `time.Now` function from the `time` package is used to get the current local time as a `time.Time` value, and then stores it in the `currentTime` variable. Once it’s stored in the variable, the `fmt.Println` function prints `currentTime` to the screen using `time.Time`’s default string output format.

Run the program using `go run` with the `main.go` file:

```shell
go run main.go
```

The output showing your current date and time will look similar to this:

```
OutputThe time is 2021-08-15 14:30:45.0000001 -0500 CDT m=+0.000066626
```

The output will show your current date and time, which will differ from the example. Additionally, the time zone you see (`-0500 CDT` in this output) will likely be different, since `time.Now()` returns the time in the local time zone.

You may also notice an `m=` value in your output. This value is the [_monotonic clock_](https://pkg.go.dev/time#hdr-Monotonic_Clocks), and is used internally by Go when measuring differences in time. The monotonic clock is designed to compensate for any potential changes to the date and time of a computer’s system clock while a program is running. By using the monotonic clock, a `time.Now` value compared to a `time.Now` value five minutes later will still end up with the correct result (a five-minute interval), even if the system clock for the computer is changed an hour forward or backward during that five-minute interval. You don’t need to understand it thoroughly for the code or examples in this tutorial, but if you’d like to learn more about monotonic clocks and how Go uses them, the [Monotonic Clocks](https://pkg.go.dev/time#hdr-Monotonic_Clocks) section in the `time` package documentation provides additional details.

Now, while you do have the current time displayed, it might not be useful for users. It may not be the format you’re looking for, or it may include more parts of the date or time than you want to display.

Fortunately, the `time.Time` type includes various methods to get specific parts of the date or time you want. For example, if you only wanted to know the year portion of the `currentTime` variable, you could use the `Year` method, or get the current hour using the `Hour` method.

Open your `main.go` file again and add a few of the `time.Time` methods to your output to see what they produce:

projects/datetime/main.go

```go
...

func main() {
	currentTime := time.Now()
	fmt.Println("The time is", currentTime)
	
	fmt.Println("The year is", currentTime.Year())
	fmt.Println("The month is", currentTime.Month())
	fmt.Println("The day is", currentTime.Day())
	fmt.Println("The hour is", currentTime.Hour())
	fmt.Println("The minute is", currentTime.Hour())
	fmt.Println("The second is", currentTime.Second())
}
```

Next, run your program again using `go run`:

```shell
go run main.go
```

The output will look similar to this:

```
OutputThe time is 2021-08-15 14:30:45.0000001 -0500 CDT m=+0.000066626
The year is 2021
The month is August
The day is 15
The hour is 14
The minute is 14
The second is 45
```

Like the previous output, your current date and time will be different from the example, but the format should be similar. This time in the output, you’ll see the full date and time printed as it was before, but you also have a list of the year, month, day of the month, hour, minute, and second. Note that instead of the month printing as a number (like it does in the full date), it appears as the English string `August`. This is because the `Month` method returns the month as a `time.Month` type instead of just a number, and that type is designed to print out the full English name when it’s printed as a `string`.

Now, update the `main.go` file in your program again and replace the various function outputs with a single function call to `fmt.Printf`, so you can print the current date and time in a format that’s closer to what you may want to display to a user:

projects/datetime/main.go

```go
...

func main() {
	currentTime := time.Now()
	fmt.Println("The time is", currentTime)
	
	fmt.Printf("%d-%d-%d %d:%d:%d\n",
		currentTime.Year(),
		currentTime.Month(),
		currentTime.Day(),
		currentTime.Hour(),
		currentTime.Hour(),
		currentTime.Second())
}
```

Once you’ve saved your updates to the `main.go` file, run it using the `go run` command as you did before:

```shell
go run main.go
```

The output will look similar to this:

```
OutputThe time is 2021-08-15 14:30:45.0000001 -0500 CDT m=+0.000066626
2021-8-15 14:14:45
```

This time your output may be much closer to what you’d like, but there are still a few things that could be tweaked about the output. The month is now showing up in number format again, because the `fmt.Printf` format used `%d` to tell the `time.Month` type it should use a number and not a `string`, but it’s only showing up as a single digit. If you want to display two digits, you could change the `fmt.Printf` format to say that, but what if you also wanted to show a 12-hour time instead of a 24-hour time as shown in the output above? Using the `fmt.Printf` method, you would have to do your own math to calculate it. Printing dates and times using `fmt.Printf` is possible, but as you can see, it can eventually become cumbersome. Doing it this way, you could either end up with a large number of lines for each part you want to display, or you’d need to do a number of your own calculations to determine what to display.

In this section, you created a new program to get the current time using `time.Now`. Once you had the current time, you then used various functions, such as `Year` and `Hour` on the `time.Time` type to print out information about the current time. It did start to become a lot of work to display it in a custom format, though. To make this type of common work easier, many programming languages, Go included, provide a special way to format dates and times, similar to how `fmt.Printf` can be used to format a string.

## Printing and Formatting Specific Dates

In addition to the `Year`, `Hour`, and other data-related methods the `time.Time` type provides, it also provides a method called `Format`. The `Format` method allows you to provide a layout `string`, similar to how you would provide `fmt.Printf` or `fmt.Sprintf` a format, that will tell the `Format` method how you’d like the date and time printed out. In this section, you will replicate the time output you added in the last section, but in a much more concise manner using the `Format` method.

Before you use the `Format` method, though, it will be easier to see how `Format` affects the output of a date and time if it’s not changing every time you run the program. Up until now, you were getting the current time using `time.Now`, so each time you ran it a different number would show up. The Go `time` package provides another useful function, the `time.Date` function, which will allow you to specify a specific date and time for the `time.Time` to represent.

To begin using `time.Date` instead of `time.Now` in your program, open the `main.go` file again and update it to replace `time.Now` with `time.Date`:

projects/datetime/main.go

```go
...

func main() {
	theTime := time.Date(2021, 8, 15, 14, 30, 45, 100, time.Local)
	fmt.Println("The time is", theTime)
}
```

The parameters to the `time.Date` function include the year, month, day of the month, hour, minute, and second of the date and time you want a `time.Time` for. The first of the last two parameters accounts for nanoseconds, and the last parameter is the time zone to create the time for. Using time zones themselves are covered later in this tutorial.

After saving your updates, run your program using `go run`:

```shell
go run main.go
```

The output will look similar to this:

```
OutputThe time is 2021-08-15 14:30:45.0000001 -0500 CDT
```

The output you see should now be much closer to the output above because you’re using a specific local date and time instead of the current one when you run the program. (Depending on the time zone your computer is set to, the time zone may show up differently, though.) The output format still looks similar to what you saw before because it’s still using the default `time.Time` format. Now that you have a standard date and time to work with, you can use it to start tweaking how the time is formatted when displayed using the `Format` method.

### Customizing Date Strings Using the `Format` Method

Many other programming languages include a similar way to format dates and times to be displayed, but the way Go constructs the layout of those formats may be slightly different than what you’re used to if you’ve used them in other languages. In other languages, the date formatting uses a style similar to how `Printf` works in Go, with a `%` character followed by a letter representing the part of the date or time to insert. For example, a 4-digit year might be represented by `%Y`. In Go, though, these parts of a date or time are represented by characters that represent a specific date. To include a 4-digit year in a Go date format, you would actually include `2006` in the string itself. The benefit of this type of layout is that what you see in the code actually represents what you’ll see in the output. When you’re able to see a representation of your output, it makes it easier to double-check that your format matches what you’re looking for, and also makes it easier for other developers to understand the output of a program without running the program first.

The specific date Go uses for date and time layouts in string formatting is `01/02 03:04:05PM '06 -0700`. If you look at each component of the date and time, you’ll see that they increase by one for each part. The month comes first at `01`, followed by the day of the month at `02`, then the hour at `03`, the minute at `04`, the second at `05`, the year at `06` (or `2006`) and, finally, the time zone at `07`. Remembering this order will make it easier to create date and time formats in the future. Examples of the options available for formatting can also be found in Go’s [documentation of the `time` package](https://pkg.go.dev/time#pkg-constants).

Now, use this new `Format` method to replicate and clean up the date format you printed in the last section. It required several lines and function calls to display, and using the `Format` method should make it much easier and cleaner to duplicate.

Open the `main.go` file and add a new `fmt.Println` call and pass it `theTime` with a date formatted using the `Format` method:

projects/datetime/main.go

```go
...

func main() {
	theTime := time.Date(2021, 8, 15, 14, 30, 45, 100, time.Local)
	fmt.Println("The time is", theTime)

	fmt.Println(theTime.Format("2006-1-2 15:4:5"))
}
```

If you look at the layout being used for the format, you’ll see it’s using the same time from above to specify how the date and time should be formatted (January 2, 2006). One thing to notice is that the hour uses `15` instead of `03` like the previous example. This shows that you’d like the hour displayed in 24-hour format instead of 12-hour format.

To see the output from this new format, save your program and run it using `go run`:

```shell
go run main.go
```

The output will look similar to this:

```
OutputThe time is 2021-08-15 14:30:45.0000001 -0500 CDT
2021-8-15 14:30:45
```

The output you see now will be similar to the output from the end of the last section, but it was much simpler to accomplish. All you needed to include was a single line of code and a layout string. The `Format` function does the rest for you.

Depending on the date or time you’re displaying, using a variable-length format like the one you ended up with when printing out numbers directly can potentially be hard to read for yourself, your users, or other code trying to read the value. Using `1` for the month format would result in March being displayed as `3`, while October would use two characters and show up as `10`. Now, open `main.go` and add an additional line to your program with another, more structured layout. In this layout, include a `0` prefix on the components and update the hour to use a 12-hour format:

projects/datetime/main.go

```go
...

func main() {
	theTime := time.Date(2021, 8, 15, 14, 30, 45, 100, time.Local)
	fmt.Println("The time is", theTime)

	fmt.Println(theTime.Format("2006-1-2 15:4:5"))
	fmt.Println(theTime.Format("2006-01-02 03:04:05 pm"))
}
```

After saving your code, run the program again using `go run`:

```shell
go run main.go
```

The output will look similar to this:

```
OutputThe time is 2021-08-15 14:30:45.0000001 -0500 CDT
2021-8-15 14:30:45
2021-08-15 02:30:45 pm
```

You’ll see that by adding a `0` prefix to the numbers in the layout string, the `8` for the month in the new output becomes `08` to match the layout. The hour, which is now in the 12-hour format, also has its own `0` prefix. In the end, though, what you end up seeing in the output mirrors the format you see in the code, so it’s easier to tweak the format if you need to.

Many times, formatted dates are intended to be interpreted by other programs, and it can become a burden to re-create those formats every time you want to use them. In some cases, you can use a pre-defined format.

### Using a Pre-Defined Format

There are many commonly used date formats, such as timestamps for log messages, and re-creating those every time you want to use them could end up being a hassle. For some of these cases, the `time` package includes pre-defined formats you can use.

One of the formats available and used often is the one defined in [RFC 3339](https://datatracker.ietf.org/doc/html/rfc3339). An _[RFC](https://en.wikipedia.org/wiki/Request_for_Comments)_ is a document used to define how standards on the internet work, and other RFCs can then build upon each other. There is an RFC that defines how HTTP works ([RFC 2616](https://datatracker.ietf.org/doc/html/rfc2616)), for example, and others that build on top of that to further define HTTP. So, in the case of RFC 3339, the RFC defines a standard format to use for timestamps on the internet. The format is well-known and well-supported around the internet, so chances of seeing it elsewhere are high.

Each of the pre-defined time formats in the `time` package are represented by a `const string` named after the format they represent. The RFC 3339 format happens to have two formats available, one called `time.RFC3339` and another called `time.RFC3339Nano`. The difference between the formats is that the `time.RFC3339Nano` version includes nanoseconds in the format.

Now, open your `main.go` file again and update your program to use the `time.RFC3339Nano` format for its output:

projects/datetime/main.go

```go
...

func main() {
	theTime := time.Date(2021, 8, 15, 14, 30, 45, 100, time.Local)
	fmt.Println("The time is", theTime)
	
	fmt.Println(theTime.Format(time.RFC3339Nano))
}
```

Since the pre-defined formats are `string` values with the desired format, you just need to replace the format you’d usually use with one of them.

To see the output, run your program with `go run` again:

```shell
go run main.go
```

The output will look similar to this:

```
OutputThe time is 2021-08-15 14:30:45.0000001 -0500 CDT
2021-08-15T14:30:45.0000001-05:00
```

The RFC 3339 format is good to use if you need to save a time value as a `string` somewhere. It can be read by many other programming languages and applications, and is about as compact as a date and time can be in a flexible `string` format.

In this section, you updated your program to use the `Format` method to print out a date and time. Using this flexible layout also allowed your code to look similar to the final output. Lastly, you used one of the pre-defined layout strings to print out a date and time using a well-supported format. In the next section, you’ll update your program to convert that same `string` value back into a `time.Time` value that you can work with.

## Parsing Dates and Times in Strings

Often when developing applications, you’ll come across dates represented as `string` values that you’ll need to interpret in some way. Sometimes, you’ll need to know the date portion of the value, other times you might need to know the time portion, and yet others you might need the whole value. In addition to using the `Format` method to create `string` values from `time.Time` values, the Go `time` package provides a `time.Parse` function to convert a `string` into a `time.Time` value. The `time.Parse` function works similar to the `Format` method, in that it takes the expected date and time layout as well as the `string` value as parameters.

Now, open the `main.go` file in your program and update it to use the `time.Parse` function to parse a `timeString` into a `time.Time` variable:

projects/datetime/main.go

```go
...

func main() {
	timeString := "2021-08-15 02:30:45"
	theTime, err := time.Parse("2006-01-02 03:04:05", timeString)
	if err != nil {
		fmt.Println("Could not parse time:", err)
	}
	fmt.Println("The time is", theTime)
	
	fmt.Println(theTime.Format(time.RFC3339Nano))
}
```

Unlike the `Format` method, the `time.Parse` method also returns a potential `error` value in case the `string` value passed in does not match the layout provided as the first parameter. If you look at the layout used, you’ll see that the layout given to `time.Parse` uses the same `1` for month, `2` for day of month, etc, that is used in the `Format` method.

After saving your updates, you can run your updated program using `go run`:

```shell
go run main.go
```

The output will look similar to this:

```
OutputThe time is 2021-08-15 02:30:45 +0000 UTC
2021-08-15T02:30:45Z
```

There are a couple of things to notice in this output. One is that the time zone being parsed from `timeString` is using the default time zone, which is a `+0` offset and known as [Coordinated Universal Time](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) (UTC). Since neither the time value nor the layout includes the time zone, the `time.Parse` function doesn’t know which time zone to associate it with. If you need it at some point, though, the `time` package does include a [`time.ParseInLocation`](https://pkg.go.dev/time#ParseInLocation) function so you can provide the location to use. The other part to notice is in the RFC 3339 output. The output is using the `time.RFC3339Nano` layout, but the output doesn’t include any nanoseconds. This is because the `time.Parse` function is not parsing any nanoseconds, so the value is set to the default of `0`. When nanoseconds are `0`, the `time.RFC3339Nano` format will not include nanoseconds in the output.

The `time.Parse` method can also use any of the pre-defined time layouts provided in the `time` package when parsing a `string` value. To see this in practice, open your `main.go` file and update the `timeString` value to match the output from `time.RFC3339Nano` earlier, and update the `time.Parse` parameter to match:

projects/datetime/main.go

```go
...

func main() {
	timeString := "2021-08-15T14:30:45.0000001-05:00"
	theTime, err := time.Parse(time.RFC3339Nano, timeString)
	if err != nil {
		fmt.Println("Could not parse time:", err)
	}
	fmt.Println("The time is", theTime)
	
	fmt.Println(theTime.Format(time.RFC3339Nano))
}
```

Once you have the code updated, you can save your program and run it again using `go run`:

```shell
go run main.go
```

The output will look similar to this:

```
OutputThe time is 2021-08-15 14:30:45.0000001 -0500 CDT
2021-08-15T14:30:45.0000001-05:00
```

This time, the output from the `Format` method shows that `time.Parse` was able to parse both the time zone and the nanoseconds from `timeString`.

In this section, you used the `time.Parse` function to parse an arbitrarily formatted date and time string value as well as a pre-defined layout. Thus far, though, you haven’t interacted with the various time zones you’ve seen. Another set of features Go provides for the `time.Time` type is the ability to convert between different time zones, as you’ll see in the next section.

## Working with Time Zones

When developing an application with users across the world, or even across only a few time zones, a common practice is to store dates and times using [Coordinated Universal Time](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) (UTC) and then convert to a user’s local time when required. This allows the data to be stored in a consistent format and makes calculations between them easier, since conversion is needed only when displaying the date and time to a user.

In earlier sections of this tutorial, you created a program that worked primarily on times based in your own local time zone. To save your `time.Time` values as UTC, you’ll first need to convert them to UTC. You will do this using the `UTC` method, which will return a copy of the time you call it on, but in UTC.

**Note:** This section converts times between your computer’s local time zone and UTC. If the computer you’re using has the local time zone set to one that matches UTC, you’ll notice that converting between UTC and back again won’t show a difference in the times.

Now, open your `main.go` file to update your program to use the `UTC` method on `theTime` to return the UTC version of the time:

projects/datetime/main.go

```shell
...

func main() {
	theTime := time.Date(2021, 8, 15, 14, 30, 45, 100, time.Local)
	fmt.Println("The time is", theTime)
	fmt.Println(theTime.Format(time.RFC3339Nano))
	
	utcTime := theTime.UTC()
	fmt.Println("The UTC time is", utcTime)
	fmt.Println(utcTime.Format(time.RFC3339Nano))
}
```

This time your program creates `theTime` as a `time.Time` value in your local time zone, prints it out in two different formats, then uses the `UTC` method to translate that time from your local time to UTC time.

To see the output from the program, you can run it using `go run`:

```shell
go run main.go
```

The output will look similar to this:

```
OutputThe time is 2021-08-15 14:30:45.0000001 -0500 CDT
2021-08-15T14:30:45.0000001-05:00
The UTC time is 2021-08-15 19:30:45.0000001 +0000 UTC
2021-08-15T19:30:45.0000001Z
```

Your output will vary depending on your local time zone, but in the output above, you’ll see that the first time that’s printed out is in `CDT` (North American Central Daylight Time), which is `-5` hours from UTC. Once the `UTC` method is called and the UTC time prints, you can see the hour in the time goes from `14` to `19`, because converting the time to UTC added five hours.

It’s also possible to convert a UTC time back to a local time using the `Local` method on a `time.Time` in the same way. Open your `main.go` file again and update it to add a call to the `Local` method on `utcTime` to convert it back to your local time zone:

projects/datetime/main.go

```go
...

func main() {
	theTime := time.Date(2021, 8, 15, 14, 30, 45, 100, time.Local)
	fmt.Println("The time is", theTime)
	fmt.Println(theTime.Format(time.RFC3339Nano))
	
	utcTime := theTime.UTC()
	fmt.Println("The UTC time is", utcTime)
	fmt.Println(utcTime.Format(time.RFC3339Nano))

	localTime := utcTime.Local()
	fmt.Println("The Local time is", localTime)
	fmt.Println(localTime.Format(time.RFC3339Nano))
}
```

Once your file is saved, run your program using `go run`:

```shell
go run main.go
```

The output will look similar to this:

```
OutputThe time is 2021-08-15 14:30:45.0000001 -0500 CDT
2021-08-15T14:30:45.0000001-05:00
The UTC time is 2021-08-15 19:30:45.0000001 +0000 UTC
2021-08-15T19:30:45.0000001Z
The Local time is 2021-08-15 14:30:45.0000001 -0500 CDT
2021-08-15T14:30:45.0000001-05:00
```

You’ll see that the UTC time has been converted back to your local time zone. In the output above, the UTC conversion back to CDT means that five hours has been subtracted from the UTC, changing the hour from `19` to `14`.

In this section, you updated your program to convert dates and times between your local time zone and the standard UTC time zone using the `UTC` method, then back again using the `Local` method.

Once you have your date and time values available, there are a number of additional features the Go `time` package provides that can be useful in your applications. One of these features is determining whether a given time is before or after another.

## Comparing Two Times

Comparing two dates with each other can be deceptively difficult at times, due to all the variables that need to be considered when comparing them. For example, needing to take time zones into account, or the fact that months have a different number of days from each other. The `time` package provides a few methods to make this easier.

The `time` package provides two methods to make these comparisons easier: the `Before` and `After` methods, available on the `time.Time` type. These methods both accept a single time value and return either `true` or `false` depending on whether the time they’re being called on is before or after the time being provided.

To see an example of these functions in action, open your `main.go` file and update it to include two different dates, then use `Before` and `After` to compare those dates to see the output:

projects/datetime/main.go

```go
...

func main() {
	firstTime := time.Date(2021, 8, 15, 14, 30, 45, 100, time.UTC)
	fmt.Println("The first time is", firstTime)

	secondTime := time.Date(2021, 12, 25, 16, 40, 55, 200, time.UTC)
	fmt.Println("The second time is", secondTime)

	fmt.Println("First time before second?", firstTime.Before(secondTime))
	fmt.Println("First time after second?", firstTime.After(secondTime))

	fmt.Println("Second time before first?", secondTime.Before(firstTime))
	fmt.Println("Second time after first?", secondTime.After(firstTime))
}
```

Once you have your file updated, save it and run it using `go run`:

```shell
go run main.go
```

The output will look similar to this:

```
OutputThe first time is 2021-08-15 14:30:45.0000001 +0000 UTC
The second time is 2021-12-25 16:40:55.0000002 +0000 UTC
First time before second? true
First time after second? false
Second time before first? false
Second time after first? true
```

Since the code is using explicit dates in the UTC time zone, your output should match the output above. You’ll see that when using the `Before` method on `firstTime` and providing it `secondTime` to compare to, it’s saying that it’s `true` that `2021-08-15` is before `2021-12-25`. When using the `After` method of `firstTime` and providing `secondTime`, it says it’s `false` that `2021-08-15` is after `2021-12-25`. Changing the order to call the methods on `secondTime` shows the opposite results, as expected.

Another way to compare two dates and times with the `time` package is the `Sub` method. The `Sub` method will subtract one date from another and return a value using a new type, the `time.Duration`. Unlike a `time.Time` value, which represents an absolute point in time, a `time.Duration` value represents a difference in time. For example, “in one hour” would be a duration because it means something different based on the current time of day, but “at noon” represents a specific, absolute time. Go uses the `time.Duration` type in a number of places, such as when you’d want to define how long a function should wait before returning an error or here, where you need to know how much time is between one time and another.

Now, update your `main.go` file to use the `Sub` method on the `firstTime` and `secondTime` values and print out the results:

projects/datetime/main.go

```go
...

func main() {
	firstTime := time.Date(2021, 8, 15, 14, 30, 45, 100, time.UTC)
	fmt.Println("The first time is", firstTime)

	secondTime := time.Date(2021, 12, 25, 16, 40, 55, 200, time.UTC)
	fmt.Println("The second time is", secondTime)

	fmt.Println("Duration between first and second time is", firstTime.Sub(secondTime))
	fmt.Println("Duration between second and first time is", secondTime.Sub(firstTime))
```

Save your file and then run it using `go run`:

```shell
go run main.go
```

The output will look similar to this:

```
OutputThe first time is 2021-08-15 14:30:45.0000001 +0000 UTC
The second time is 2021-12-25 16:40:55.0000002 +0000 UTC
Duration between first and second time is -3170h10m10.0000001s
Duration between second and first time is 3170h10m10.0000001s
```

The output above says there are 3,170 hours, 10 minutes, 10 seconds, and 100 nanoseconds between the two dates, and there are a few things to note about the output. The first is that the duration between the first and second times is a negative value. This says the second time is after the first, and would be similar to if you subtracted `5` from `0` and got `-5`. The second thing to note is that a duration’s largest unit of measure is an hour, so it doesn’t break it down into days or months. Since the number of days in a month is not consistent and a “day” could have a different meaning during a switch for daylight saving time, an hour is the most accurate measurement that wouldn’t fluctuate.

In this section, you updated your program to compare two times using three different methods. First, you used the `Before` and `After` methods to determine if a time is before or after another time, then you used `Sub` to see how long it is between two times. Getting the duration between two times isn’t the only way the `time` package uses `time.Duration`, though. You are also able to use it to add or remove time from a `time.Time` value, as you’ll see in the next section.

## Adding or Subtracting Times

When writing an application, one common operation using dates and times is determining a past or future time based on another time. It could be used for functionality, such as determining when a subscription will come up for renewal next, or if it’s been a certain amount of time since some value was checked. Either way, the Go `time` package provides a way to handle it. To find another date based on a date you already know, though, you’ll need to be able to define your own `time.Duration` values.

Creating a `time.Duration` value is similar to how you might write a duration on paper, just with multiplication for the unit of time. For example, to create a `time.Duration` to represent an hour, you would define it using the `time.Hour` value multiplied by the number of hours you want the `time.Duration` to represent:

```go
oneHour := 1 * time.Hour
twoHours := 2 * time.Hour
tenHours := 10 * time.Hour
```

Declaring smaller time units is handled in a similar way, except using `time.Minute`, `time.Second`, and so on:

```go
tenMinutes := 10 * time.Minute
fiveSeconds := 5 * time.Second
```

One duration can also be added to another duration to get the sum of the durations. To see this in action, open your `main.go` file and update it to declare a `toAdd` duration variable and add different durations to it:

projects/datetime/main.go

```go
...

func main() {
	toAdd := 1 * time.Hour
	fmt.Println("1:", toAdd)

	toAdd += 1 * time.Minute
	fmt.Println("2:", toAdd)

	toAdd += 1 * time.Second
	fmt.Println("3:", toAdd)
}
```

Once you have your updates finished, save the file and run your program using `go run`:

```shell
go run main.go
```

The output will look similar to this:

```
Output1: 1h0m0s
2: 1h1m0s
3: 1h1m1s
```

When you look at the output, you’ll se the first duration printed is for one hour, corresponding to the `1 * time.Hour` in your code. Next, you added `1 * time.Minute` to the `toAdd` value, which shows up as the one hour, one minute value. Finally, you added `1 * time.Second` to `toAdd`, which resulted in a `time.Duration` value of one hour, one minute and one second.

It’s also possible to combine adding the durations together in one statement, or subtracting a duration from another:

```go
oneHourOneMinute := 1 * time.Hour + 1 * time.Minute
tenMinutes := 1 * time.Hour - 50 * time.Minute
```

Next, open your `main.go` file and update your program to use a combination like this to subtract one minute and one second from `toAdd`:

```go
...

func main() {
	
	...
	
	toAdd += 1 * time.Second
	fmt.Println("3:", toAdd)
	
	toAdd -= 1*time.Minute + 1*time.Second
	fmt.Println("4:", toAdd)
}
```

projects/datetime/main.go

After saving your code, you can run the program using `go run`:

```shell
go run main.go
```

The output will look similar to this:

```
Output1: 1h0m0s
2: 1h1m0s
3: 1h1m1s
4: 1h0m0s
```

The new fourth line added to the output shows the new code you included to subtract the sum of `1 * time.Minute` and `1 * time.Second` worked correctly, resulting in the original one-hour value again.

Using these durations paired with the `Add` method of the `time.Time` type allows you to take a time value you already have and determine the time at some other arbitrary point before or after that time. To see an example of this running, open your `main.go` file and update it to set the `toAdd` value to 24 hours, or `24 * time.Hour`. Then, use the `Add` method on a `time.Time` value to see what the time would be 24 hours after that point:

projects/datetime/main.go

```go
...

func main() {
	theTime := time.Date(2021, 8, 15, 14, 30, 45, 100, time.UTC)
	fmt.Println("The time is", theTime)

	toAdd := 24 * time.Hour
	fmt.Println("Adding", toAdd)

	newTime := theTime.Add(toAdd)
	fmt.Println("The new time is", newTime)
}
```

After saving your file, run your program using `go run`:

```shell
go run main.go
```

The output will look similar to this:

```
OutputThe time is 2021-08-15 14:30:45.0000001 +0000 UTC
Adding 24h0m0s
The new time is 2021-08-16 14:30:45.0000001 +0000 UTC
```

Looking at the output, you’ll see that adding 24 hours to `2021-08-15` results in the new date of `2021-08-16`.

To subtract time, you would also use the `Add` method, which is slightly counter-intuitive. Since the `Sub` method is used to get the difference in time between two dates, you subtract time from the `time.Time` value by using `Add` with a negative value.

Once again, to see this running in your program, open your `main.go` file and update it to change the 24-hour `toAdd` value to a negative value:

projects/datetime/main.go

```go
...

func main() {
	theTime := time.Date(2021, 8, 15, 14, 30, 45, 100, time.UTC)
	fmt.Println("The time is", theTime)

	toAdd := -24 * time.Hour
	fmt.Println("Adding", toAdd)

	newTime := theTime.Add(toAdd)
	fmt.Println("The new time is", newTime)
}
```

After saving your file, run your program one more time using `go run`:

```shell
go run main.go
```

The output will look similar to this:

```
OutputThe time is 2021-08-15 14:30:45.0000001 +0000 UTC
Adding -24h0m0s
The new time is 2021-08-14 14:30:45.0000001 +0000 UTC
```

This time in the output you’ll see that instead of adding 24 hours to the original time, the new date is 24 hours before the original time.

In this section, you used `time.Hour`, `time.Minute`, and `time.Second` to create `time.Duration` values of varying degrees. You also used the `time.Duration` values with the `Add` method to get a new `time.Time` value both before and after the original value. By combining `time.Now`, the `Add` method, and `Before` or `After` methods, you’ll have access to powerful date and time functionality for your applications.

## Conclusion

In this tutorial, you used `time.Now` to retrieve a `time.Time` value for the current local time on your computer, then used the `Year`, `Month`, `Hour`, and other methods to access specific information about that time. Then, you used the `Format` method to print the time using a custom format and a pre-defined format. Next, you used the `time.Parse` function to interpret a `string` value with a time in it and extract the time value from it. Once you had the time value, you used the `UTC` and `Local` methods to translate the time between UTC and your local time zone. After that, you used the `Sub` method to get the duration between two different times before using the `Add` method to find the time relative to a different time value.

Using the various functions and methods described in this tutorial will go a long way in your applications, but the Go [`time`](https://pkg.go.dev/time) package also includes a number of other features if you’re interested.

This tutorial is also part of the [DigitalOcean](https://www.digitalocean.com/) [How to Code in Go](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go) series. The series covers a number of Go topics, from installing Go for the first time to how to use the language itself.
