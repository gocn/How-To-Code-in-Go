# How To Run Multiple Functions Concurrently in Go

## Introduction

One of the popular features of the Go language is its first-class support for [_concurrency_](https://en.wikipedia.org/wiki/Concurrency_(computer_science)), or the ability of a program to do multiple things at once. Being able to run code concurrently is becoming a larger part of programming as computers move from running a single stream of code faster to running more streams of code simultaneously. To run programs faster, a programmer needs to design their programs to run concurrently, so that each concurrent part of the program can be run independently of the others. Two features in Go, [goroutines](https://golangdocs.com/goroutines-in-golang) and [channels](https://golangdocs.com/channels-in-golang), make concurrency easier when used together. Goroutines solve the difficulty of setting up and running concurrent code in a program, and channels solve the difficulty of safely communicating between the code running concurrently.

In this tutorial, you will explore both goroutines and channels. First, you will create a program that uses goroutines to run multiple functions at once. Then you will add channels to that program to communicate between the running goroutines. Finally, you’ll add more goroutines to the program to simulate a program running with multiple worker goroutines.

## Prerequisites

To follow this tutorial, you will need:

-   Go version 1.16 or greater installed. To set this up, follow the [How To Install Go](https://www.digitalocean.com/community/tutorials/how-to-install-go-on-ubuntu-20-04) tutorial for your operating system.
-   Familiarity with Go functions, which you can find in the [How to Define and Call Functions in Go](https://www.digitalocean.com/community/tutorials/how-to-define-and-call-functions-in-go) tutorial.

## Running Functions at the Same Time with Goroutines

In a modern computer, the processor, or [CPU](https://en.wikipedia.org/wiki/Central_processing_unit), is designed to run as many streams of code as possible at the same time. These processors have one or more “cores,” each capable of running one stream of code at the same time. So, the more cores a program can use simultaneously, the faster the program will run. However, in order for programs to take advantage of the speed increase that [multiple cores](https://en.wikipedia.org/wiki/Multi-core_processor) provide, programs need to be able to be split into multiple streams of code. Splitting a program into parts can be one of the more challenging things to do in programming, but Go was designed to make this easier.

One way Go does this is with a feature called _goroutines_. A goroutine is a special type of function that can run while other goroutines are also running. When a program is designed to run multiple streams of code at once, the program is designed to run [_concurrently_](https://en.wikipedia.org/wiki/Concurrency_(computer_science)). Typically, when a function is called, it will finish running completely before the code after it continues to run. This is known as running in the “foreground” because it prevents your program from doing anything else before it finishes. With a goroutine, the function call will continue running the next code right away while the goroutine runs in the “background”. Code is considered running in the background when it doesn’t prevent other code from running before it finishes.

The power goroutines provide is that each goroutine can run on a processor core at the same time. If your computer has four processor cores and your program has four goroutines, all four goroutines can run simultaneously. When multiple streams of code are running at the same time on different cores like this, it’s called running in [_parallel_](https://en.wikipedia.org/wiki/Parallel_computing).

To visualize the difference between concurrency and parallelism, consider the following diagram. When a processor runs a function, it doesn’t always run it from beginning to completion all at once. Sometimes the operating system will interleave other functions, goroutines, or other programs on a CPU core when a function is waiting for something else to happen, such as reading a file. The diagram shows how a program designed for concurrency can run on a single core as well as multiple cores. It also shows how more segments of a goroutine can fit into the same timeframe (9 vertical segments, as seen in the diagram) when running in parallel than when running on a single core.

![Diagram split into two columns, labeled Concurrency and Parallelism. The Concurrency column has a single tall rectangle, labeled CPU core, divided into stacked sections of varying colors signifying different functions. The Parallelism column has two similar tall rectangles, both labeled CPU core, with each stacked section signifying different functions, except it only shows goroutine1 running on the left core and goroutine2 running on the right core.](https://assets.digitalocean.com/articles/68067/diagram2.png)

The left column in the diagram, labeled “Concurrency”, shows how a program designed around concurrency could run on a single CPU core by running part of `goroutine1`, then another function, goroutine, or program, then `goroutine2`, then `goroutine1` again, and so on. To a user, this would seem like the program is running all the functions or goroutines at the same time, even though they’re actually being run in small parts one after the other.

The column on the right of the diagram, labeled “Parallelism”, shows how that same program could run in parallel on a processor with two CPU cores. The first CPU core shows `goroutine1` running interspersed with other functions, goroutines, or programs, while the second CPU core shows `goroutine2` running with other functions or goroutines on that core. Sometimes both `goroutine1` and `goroutine2` are running at the same time as each other, just on different CPU cores.

This diagram also shows another of Go’s powerful traits, [_scalability_](https://en.wikipedia.org/wiki/Scalability). A program is scalable when it can run on anything from a small computer with a few processor cores to a large server with dozens of cores, and take advantage of those additional resources. The diagram shows that by using goroutines, your concurrent program is capable of running on a single CPU core, but as more CPU cores are added, more goroutines can be run in parallel to speed up the program.

To get started with your new concurrent program, create a `multifunc` directory in the location of your choosing. You may already have a directory for your projects, but in this tutorial, you’ll create a directory called `projects`. You can create the `projects` directory either through an IDE or via the command line.

If you’re using the command line, begin by making the `projects` directory and navigating to it:

```shell
mkdir projects
cd projects
```

From the `projects` directory, use the `mkdir` command to create the program’s directory (`multifunc`) and then navigate into it:

```shell
mkdir multifunc
cd multifunc
```

Once you’re in the `multifunc` directory, open a file named `main.go` using `nano`, or your favorite editor:

```shell
nano main.go
```

Paste or type the following code in the `main.go` file to get started.

projects/multifunc/main.go

```go
package main

import (
	"fmt"
)

func generateNumbers(total int) {
	for idx := 1; idx <= total; idx++ {
		fmt.Printf("Generating number %d\n", idx)
	}
}

func printNumbers() {
	for idx := 1; idx <= 3; idx++ {
		fmt.Printf("Printing number %d\n", idx)
	}
}

func main() {
	printNumbers()
	generateNumbers(3)
}
```

This initial program defines two functions, `generateNumbers` and `printNumbers`, then runs those functions in the `main` function. The `generateNumbers` function takes the amount of numbers to “generate” as a parameter, in this case one through three, and then prints each of those numbers to the screen. The `printNumbers` function doesn’t take any parameters yet, but it will also print out the numbers one through three.

Once you’ve saved the `main.go` file, run it using `go run` to see the output:

```shell
go run main.go
```

The output will look similar to this:

```
Output
Printing number 1
Printing number 2
Printing number 3
Generating number 1
Generating number 2
Generating number 3
```

You’ll see the functions run one after the other, with `printNumbers` running first and `generateNumbers` running second.

Now, imagine that `printNumbers` and `generateNumbers` each takes three seconds to run. When running _synchronously_, or one after the other like the last example, your program would take six seconds to run. First, `printNumbers` would run for three seconds, and then `generateNumbers` would run for three seconds. In your program, however, these two functions are independent of the other because they don’t rely on data from the other to run. You can take advantage of this to speed up this hypothetical program by running the functions concurrently using goroutines. When both functions are running concurrently the program could, in theory, run in half the time. If both the `printNumbers` and the `generateNumbers` functions take three seconds to run and both start at exactly the same time, the program could finish in three seconds. (The actual speed could vary due to outside factors, though, such as how many cores the computer has or how many other programs are running on the computer at the same time.)

Running a function concurrently as a goroutine is similar to running a function synchronously. To run a function as a goroutine (as opposed to a standard synchronous function), you only need to add the `go` keyword before the function call.

However, for the program to run the goroutines concurrently, you’ll need to make one additional change. You’ll need to add a way for your program to wait until both goroutines have finished running. If you don’t wait for your goroutines to finish and your `main` function completes, the goroutines could potentially never run, or only part of them could run and not complete running.

To wait for the functions to finish, you’ll use a [`WaitGroup`](https://pkg.go.dev/sync#WaitGroup) from Go’s [`sync`](https://pkg.go.dev/sync) package. The `sync` package contains “synchronization primitives”, such as `WaitGroup`, that are designed to synchronize various parts of a program. In your case, the synchronization keeps track of when both functions have finished running so you can exit the program.

The `WaitGroup` primitive works by counting how many things it needs to wait for using the `Add`, `Done`, and `Wait` functions. The `Add` function increases the count by the number provided to the function, and `Done` decreases the count by one. The `Wait` function can then be used to wait until the count reaches zero, meaning that `Done` has been called enough times to offset the calls to `Add`. Once the count reaches zero, the `Wait` function will return and the program will continue running.

Next, update the code in your `main.go` file to run both of your functions as goroutines using the `go` keyword, and add a `sync.WaitGroup` to the program:

projects/multifunc/main.go

```go
package main

import (
	"fmt"
	"sync"
)

func generateNumbers(total int, wg *sync.WaitGroup) {
	defer wg.Done()

	for idx := 1; idx <= total; idx++ {
		fmt.Printf("Generating number %d\n", idx)
	}
}

func printNumbers(wg *sync.WaitGroup) {
	defer wg.Done()

	for idx := 1; idx <= 3; idx++ {
		fmt.Printf("Printing number %d\n", idx)
	}
}

func main() {
	var wg sync.WaitGroup

	wg.Add(2)
	go printNumbers(&wg)
	go generateNumbers(3, &wg)

	fmt.Println("Waiting for goroutines to finish...")
	wg.Wait()
	fmt.Println("Done!")
}
```

After declaring the `WaitGroup`, it will need to know how many things to wait for. Including a `wg.Add(2)` in the `main` function before starting the goroutines will tell `wg` to wait for two `Done` calls before considering the group finished. If this isn’t done before the goroutines are started, it’s possible things will happen out of order or the code may panic because the `wg` doesn’t know it should be waiting for any `Done` calls.

Each function will then use `defer` to call `Done` to decrease the count by one after the function finishes running. The `main` function is also updated to include a call to `Wait` on the `WaitGroup`, so the `main` function will wait until both functions call `Done` to continue running and exit the program.

After saving your `main.go` file, run it using `go run` like you did before:

```shell
go run main.go
```

The output will look similar to this:

```
Output
Printing number 1
Waiting for goroutines to finish...
Generating number 1
Generating number 2
Generating number 3
Printing number 2
Printing number 3
Done!
```

Your output may differ from what is printed here, and it’s even likely to change every time you run the program. With both functions running concurrently, the output depends on how much time Go and your operating system give for each function to run. Sometimes there is enough time to run each function completely and you’ll see both functions print their entire sequences uninterrupted. Other times, you’ll see the text interspersed like the output above.

An experiment you can try is removing the `wg.Wait()` call in the `main` function and running the program a few times with `go run` again. Depending on your computer, you may see some output from the `generateNumbers` and `printNumbers` functions, but it’s also likely you won’t see any output from them at all. When you remove the call to `Wait`, the program will no longer wait for both functions to finish running before it continues. Since the `main` function ends soon after the `Wait` function, there’s a good chance that your program will reach the end of the `main` function and exit before the goroutines finish running. When this happens, you’ll see a few numbers printed out, but you won’t see all three from each function.

In this section, you created a program that uses the `go` keyword to run two goroutines concurrently and print a sequence of numbers. You also used a `sync.WaitGroup` to make your program wait for those goroutines to finish before exiting the program.

You may have noticed that the `generateNumbers` and `printNumbers` functions do not have return values. In Go, goroutines aren’t able to return values like a standard function would. You can still use the `go` keyword to call a function that returns values, but those return values will be thrown out and you won’t be able to access them. So, what do you do when you need data from one goroutine in another goroutine if you can’t return values? The solution is to use a Go feature called “channels”, which allow you to send data from one goroutine to another.

## Communicating Safely Between Goroutines with Channels

One of the more difficult parts of concurrent programming is communicating safely between different parts of the program that are running simultaneously. If you’re not careful, you might run into problems that are only possible with concurrent programs. For example, a [_data race_](https://en.wikipedia.org/wiki/Race_condition) can happen when two parts of a program are running concurrently, and one part tries to update a variable while the other part is trying to read it at the same time. When this happens, the reading or writing can happen out of order, leading to one or both parts of the program using the wrong value. The name “data race” comes from both parts of the program “racing” each other to access the data.

While it’s still possible to run into concurrency issues like data races in Go, the language is designed to make it easier to avoid them. In addition to goroutines, channels are another feature that makes concurrency safer and easier to use. A channel can be thought of like a pipe between two or more different goroutines that data can be sent through. One goroutine puts data into one end of the pipe and another goroutine gets that same data out. The difficult part of making sure the data gets from one to the other safely is handled for you.

Creating a channel in Go is similar to how you would create a [slice](https://www.digitalocean.com/community/tutorials/understanding-arrays-and-slices-in-go), using the built-in `make()` function. The type declaration for a channel uses the `chan` keyword followed by the [type of data](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go) you want to send on the channel. For example, to create a channel for sending `int` values, you would use the type `chan int`. If you wanted a channel for sending `[]byte` vaules, it would be `chan []byte`, like so:

```go
bytesChan := make(chan []byte)
```

Once a channel is created, you can send or receive data on the channel by using the arrow-looking `<-` operator. The position of the `<-` operator in relation to the channel variable determines whether you’re reading from or writing to the channel.

To write to a channel, begin with the channel variable, followed by the `<-` operator, then the value you want to write to the channel:

```go
intChan := make(chan int)
intChan <- 10
```

To read a value from a channel, begin with the variable you want to put the value into, either `=` or `:=` to assign a value to the variable, followed by the `<-` operator, and then the channel you want to read from:

```go
intChan := make(chan int)
intVar := <- intChan
```

To keep these two operations straight, it can be helpful to remember that the `<-` arrow always points to the left (as opposed to `->`), and the arrow points to where the value is going. In the case of writing to a channel, the arrow points the value to the channel. When reading from a channel, the arrow points the channel to the variable.

Like a slice, a channel can also be read using the `range` keyword in a [`for` loop](https://www.digitalocean.com/community/tutorials/how-to-construct-for-loops-in-go). When a channel is read using the `range` keyword, each iteration of the loop will read the next value from the channel and put it into the loop variable. It will then continue reading from the channel until the channel is closed or the `for` loop is exited in other ways, such as a `break`:

```go
intChan := make(chan int)
for num := range intChan {
	// Use the value of num received from the channel
	if num < 1 {
		break
	}
}
```

In some cases, you may want only to allow a function to read from or write to a channel, but not both. To do this, you would add the `<-` operator onto the `chan` type declaration. Similar to reading and writing from a channel, the channel type uses the `<-` arrow to allow variables to constrain a channel to only reading, only writing, or both reading and writing. For example, to define a read-only channel of `int` values, the type declaration would be `<-chan int`:

```go
func readChannel(ch <-chan int) {
	// ch is read-only
}
```

If you wanted the channel to be write-only, you would declare it as `chan<- int`:

```go
func writeChannel(ch chan<- int) {
	// ch is write-only
}
```

Notice that the arrow is pointing out of the channel for reading, and pointing into the channel for writing. If the declaration doesn’t have an arrow, as in the case of `chan int`, the channel can be used for both reading and writing.

Finally, once a channel is no longer being used it can be closed using the built-in `close()` function. This step is essential because when channels are created and then left unused many times in a program, it can lead to what’s known as a [_memory leak_](https://en.wikipedia.org/wiki/Memory_leak). A memory leak is when a program creates something that uses up memory on a computer, but doesn’t release that memory back to the computer once it’s done using it. This leads to the program slowly (or sometimes not so slowly) using up more memory over time, like a water leak. When a channel is created with `make()`, some of the computer’s memory is used up for the channel, then when `close()` is called on the channel, that memory is given back to the computer to be used for something else.

Now, update the `main.go` file in your program to use a `chan int` channel to communicate between your goroutines. The `generateNumbers` function will generate numbers and write them to the channel while the `printNumbers` function will read those numbers from the channel and print them to the screen. In the `main` function, you’ll create a new channel to pass as a parameter to each of the other functions, then use `close()` on the channel to close it because it will no longer be used. The `generateNumbers` function should also not be a goroutine any more because once that function is done running, the program will have finished generating all the numbers it needs to. This way, the `close()` function is only called on the channel before both functions have finished running.

projects/multifunc/main.go

```go
package main

import (
	"fmt"
	"sync"
)

func generateNumbers(total int, ch chan<- int, wg *sync.WaitGroup) {
	defer wg.Done()

	for idx := 1; idx <= total; idx++ {
		fmt.Printf("sending %d to channel\n", idx)
		ch <- idx
	}
}

func printNumbers(ch <-chan int, wg *sync.WaitGroup) {
	defer wg.Done()

	for num := range ch {
		fmt.Printf("read %d from channel\n", num)
	}
}

func main() {
	var wg sync.WaitGroup
	numberChan := make(chan int)

	wg.Add(2)
	go printNumbers(numberChan, &wg)

	generateNumbers(3, numberChan, &wg)

	close(numberChan)

	fmt.Println("Waiting for goroutines to finish...")
	wg.Wait()
	fmt.Println("Done!")
}
```

In the parameters for `generateNumbers` and `printNumbers`, you’ll see that the `chan` types are using the read-only and write-only types. Since `generateNumbers` only needs to be able to write numbers to the channel, it’s a write-only type with the `<-` arrow pointing into the channel. `printNumbers` only needs to be able to read numbers from the channel, so it’s a read-only type with the `<-` arrow pointing away from the channel.

Even though these types could be a `chan int`, which would allow both reading and writing, it can be helpful to constrain them to only what the function needs to avoid accidentally causing your program to stop running from something known as a [_deadlock_](https://en.wikipedia.org/wiki/Deadlock). A deadlock can happen when one part of a program is waiting for another part of the program to do something, but that other part of the program is also waiting for the first part of the program to finish. Since both parts of the program are waiting on each other, the program will never continue running, almost like when two gears seize.

The deadlock can happen due to the way channel communication works in Go. When part of a program is writing to a channel, it will wait until another part of the program reads from that channel before continuing on. Similarly, if a program is reading from a channel it will wait until another part of the program writes to that channel before it continues. A part of a program waiting on something else to happen is said to be [_blocking_](https://en.wikipedia.org/wiki/Blocking_(computing)) because it’s blocked from continuing until something else happens. Channels block when they are being written to or read from. So if you have a function where you’re expecting to write to a channel but accidentally read from the channel instead, your program may enter a deadlock because the channel will never be written to. Ensuring this never happens is one reason to use a `chan<- int` or a `<-chan int` instead of just a `chan int`.

One other important aspect of the updated code is using `close()` to close the channel once it’s done being written to by `generateNumbers`. In this program, `close()` causes the `for ... range` loop in `printNumbers` to exit. Since using `range` to read from a channel continues until the channel it’s reading from is closed, if `close` isn’t called on `numberChan` then `printNumbers` will never finish. If `printNumbers` never finishes, the `WaitGroup`’s `Done` method will never be called by the `defer` when `printNumbers` exits. If the `Done` method is never called from `printNumbers`, the program itself will never exit because the `WaitGroup`’s `Wait` method in the `main` function will never continue. This is another example of a deadlock because the `main` function is waiting on something that will never happen.

Now, run your updated code using the `go run` command on `main.go` again.

```shell
go run main.go
```

Your output may vary slightly from what’s shown below, but overall it should be similar:

```
Output
sending 1 to channel
sending 2 to channel
read 1 from channel
read 2 from channel
sending 3 to channel
Waiting for functions to finish...
read 3 from channel
Done!
```

The output from the program shows that the `generateNumbers` function is generating the numbers one through three while writing them to the channel shared with `printNumbers`. Once `printNumbers` receives the number, it then prints it to the screen. After `generateNumbers` has generated all three numbers it will exit, allowing the `main` function to close the channel and wait until `printNumbers` is finished. Once `printNumbers` finishes printing out the last number, it calls `Done` on the `WaitGroup` and the program exits. Similar to previous outputs, the exact output you see will depend on various outside factors, such as when the operating system or Go runtime choose to run specific goroutines, but it should be relatively close.

The benefit of designing your programs using goroutines and channels is that once you’ve designed your program to be split up, you can scale it up to more goroutines. Since `generateNumbers` is just writing to a channel, it doesn’t matter how many other things are reading from that channel. It will just send numbers to anything that reads the channel. You can take advantage of this by running more than one `printNumbers` goroutine, so each of them will read from the same channel and handle the data concurrently.

Now that your program is using channels to communicate, open the `main.go` file again and update your program so it starts multiple `printNumbers` goroutines. You will need to tweak the call to `wg.Add` so it adds one for every goroutine you start. You don’t need to worry about adding one to the `WaitGroup` for the call to `generateNumbers` any more because the program won’t continue without finishing the whole function, unlike when you were running it as a goroutine. To ensure it doesn’t reduce the `WaitGroup` count when it finishes, you should remove the `defer wg.Done()` line from the function. Next, adding the number of the goroutine to `printNumbers` makes it easier to see how the channel is read by each of them. Increasing the amount of numbers being generated is also a good idea so that it’s easier to see the numbers being spread out:

projects/multifunc/main.go

```go
...

func generateNumbers(total int, ch chan<- int, wg *sync.WaitGroup) {
	for idx := 1; idx <= total; idx++ {
		fmt.Printf("sending %d to channel\n", idx)
		ch <- idx
	}
}

func printNumbers(idx int, ch <-chan int, wg *sync.WaitGroup) {
	defer wg.Done()

	for num := range ch {
		fmt.Printf("%d: read %d from channel\n", idx, num)
	}
}

func main() {
	var wg sync.WaitGroup
	numberChan := make(chan int)

	for idx := 1; idx <= 3; idx++ {
		wg.Add(1)
		go printNumbers(idx, numberChan, &wg)
	}

	generateNumbers(5, numberChan, &wg)

	close(numberChan)

	fmt.Println("Waiting for goroutines to finish...")
	wg.Wait()
	fmt.Println("Done!")
}
```

Once your `main.go` is updated, you can run your program again using `go run` with `main.go`. Your program should start three `printNumbers` goroutines before continuing on to generating numbers. Your program should also now generate five numbers instead of three to make it easier to see the numbers spread out among each of the three `printNumbers` goroutines:

```shell
go run main.go
```

The ouput may look similar to this (although your output might vary quite a bit):

```
Outputsending 1 to channel
sending 2 to channel
sending 3 to channel
3: read 2 from channel
1: read 1 from channel
sending 4 to channel
sending 5 to channel
3: read 4 from channel
1: read 5 from channel
Waiting for goroutines to finish...
2: read 3 from channel
Done!
```

When you look at your program output this time, there’s a good chance it will vary greatly from the output you see above. Since there are three `printNumbers` goroutines running, there’s an element of chance determining which one receives a specific number. When one `printNumbers` goroutine receives a number, it spends a small amount of time printing that number to the screen, while another goroutine reads the next number from the channel and does the same thing. When a goroutine has finished its work of printing the number and is ready to read another number, it will go back and read the channel again to print the next one. If there are no more numbers to be read from the channel, it will start to block until the next number can be read. Once `generateNumbers` has finished and `close()` is called on the channel, all three of the `printNumbers` goroutines will finish their `range` loops and exit. When all three goroutines have exited and called `Done` on the `WaitGroup`, the `WaitGroup`’s count will reach zero and the program will exit. You can also experiment with increasing or decreasing the amount of goroutines or numbers being generated to see how that affects the output.

When using goroutines, avoid starting too many. In theory, a program could have hundreds or even thousands of goroutines. However, depending on the computer the program is running on, it could actually be slower to have a higher number of goroutines. With a high number of goroutines, there’s a chance it could run into [_resource starvation_](https://en.wikipedia.org/wiki/Starvation_(computer_science)). Every time Go runs part of a goroutine, it requires a little bit of extra time to start running again, in addition to the time needed to run the code in the next function. Due to the extra time it takes, it’s possible for the computer to take longer to switch between running each goroutine than to actually run the goroutine itself. When this happens, it’s called resource starvation because the program and its goroutines aren’t getting the resources they need to run, or are getting very few. In these cases, it may be faster to lower the number of parts in the program running concurrently because it will lower the time it takes to switch between them, and give more time to running the program itself. Remembering how many cores the program is running on can be a good starting point for deciding how many goroutines you want to use.

Using a combination of goroutines and channels makes it possible to create very powerful programs capable of scaling from running on small desktop computers up to massive servers. As you saw in this section, channels can be used to communicate between as few as a couple of goroutines to potentially thousands of goroutines with minimal changes. If you take this into consideration when writing your programs, you’ll be able to take advantage of the concurrency available in Go to provide your users a better overall experience.

## Conclusion

In this tutorial, you created a program using the `go` keyword to start concurrently-running goroutines that printed out numbers as they run. Once that program was running, you created a new channel of `int` values using `make(chan int)`, then used the channel to generate numbers in one goroutine and send them to another goroutine to be printed to the screen. Finally, you started multiple “printing” goroutines at the same time as an example of how channels and goroutines can be used to speed up your programs on multi-core computers.

If you’re interested in learning more about concurrency in Go, the [Effective Go](https://golang.org/doc/effective_go#concurrency) document created by the Go team goes into much more detail. The [Concurrency is not parallelism](https://go.dev/blog/waza-talk) Go blog post is also an interesting follow-up about the relationship between concurrency and parallelism, two terms that are sometimes mistakenly conflated to mean the same thing.

This tutorial is also part of the [DigitalOcean](https://www.digitalocean.com/) [How to Code in Go](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go) series. The series covers a number of Go topics, from installing Go for the first time to how to use the language itself.
