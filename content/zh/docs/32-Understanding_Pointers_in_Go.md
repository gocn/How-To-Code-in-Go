# 了解 Go 中的指针

## 简介

当你用 Go 编写软件时，你会编写函数和方法。你将数据作为 *参数* 传递给这些函数。有时，函数会需要一个数据的本地拷贝，你希望原始数据保持不变。例如，如果你是一家银行，你有一个函数可以根据用户选择的储蓄计划来显示他们的余额变化，你不想在客户选择计划之前改变他们的实际余额，而只想用它来做计算。这被称为 *按值传递*，因为你是在向函数发送变量的值，而不是变量本身。

其他时候，你可能希望函数能够改变原始变量中的数据。例如，当银行客户向其账户存款时，你希望存款函数能够访问实际的余额，而不是一个副本。在这种情况下，你不需要向函数发送实际数据， 而只需要告诉函数数据在内存中的位置。一个叫做 *指针* 的数据类型持有数据的内存地址，但不是数据本身。内存地址告诉函数在哪里可以找到数据，而不是数据的值。你可以把指针传给函数而不是实际的数据，然后函数就可以在原地改变原始变量的值。这被称为 *通过引用传递*，因为变量的值并没有传递给函数，而是传递了它指向的位置。

在这篇文章中，你将创建并使用指针来分享对一个变量的内存空间的访问。

## 定义和使用指针

当你使用一个指向变量的指针时，有几个不同的语法元素你需要了解。第一个是与号（`&`）的使用。如果你在一个变量名称前面加一个与号，你就说明你想获得 *地址*，或者说是该变量的一个指针。第二个语法元素是使用星号（`*`）或 *引用* 操作符。当你声明一个指针变量时，你在变量名后面加上指针指向的变量类型，前面加一个`*`，像这样：

```go
var myPointer *int32 = &someint
```

这将创建 `myPointer` 作为一个指向 `int32` 变量的指针，并以 `someint` 的地址初始化该指针。指针实际上并不包含一个 `int32`，而只是一个地址。

让我们来看看一个指向 `string` 的指针。下面的代码既声明了一个字符串的值，又声明了一个指向字符串的指针：

<center>main.go</center>

```go
package main

import "fmt"

func main() {
	var creature string = "shark"
	var pointer *string = &creature

	fmt.Println("creature =", creature)
	fmt.Println("pointer =", pointer)
}

```


用以下命令运行该程序：

```shell
go run main.go
```

当你运行程序时，它将打印出变量的值，以及该变量的存储地址（指针地址）。内存地址是一个十六进制的数字，并不是为了让人看懂。在实践中，你可能永远不会输出内存地址来查看它。我们给你看是为了说明问题。因为每个程序运行时都是在自己的内存空间中创建的，所以每次运行时指针的值都会不同，也会与下面显示的输出不同：

```shell
creature = shark
pointer = 0xc0000721e0
```
我们定义的第一个变量名为 `creature`，并将其设置为一个 `string`，其值为 `shark` 。然后我们创建了另一个名为 `pointer` 的变量。这一次，我们将 `pointer` 变量的值设置为 `creature` 变量的地址。我们通过使用与号（`&`）符号将一个值的地址存储在一个变量中。这意味着 `pointer` 变量存储的是 `creature` 变量的 **地址** ，而不是实际值。
这就是为什么当我们打印出 `pointer` 的值时，我们收到的值是 `0xc0000721e0` ，这是 `creature` 变量目前在计算机内存中的地址。

如果你想打印出 `pointer` 变量所指向的变量的值，你需要 *解引用* 该变量。下面的代码使用 `*` 操作符来解除对 `pointer` 变量的引用并检索其值。

<center>main.go</center>

```go
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

如果你运行这段代码，你会看到以下输出：

```shell
creature = shark
pointer = 0xc000010200
*pointer = shark
```
我们添加的最后一行现在解除了对 `pointer` 变量的引用，并打印出了存储在该地址的值。
如果你想修改存储在 `pointer` 变量位置的值，你也可以使用解除引用操作：

<center>main.go</center>

```go
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

运行这段代码可以看到输出：

```shell
creature = shark
pointer = 0xc000094040
*pointer = shark
*pointer = jellyfish
```
我们通过在变量名称前使用星号（`*`）来设置 `pointer` 变量所指的值，然后提供一个 `jellyfish` 的新值。正如你所看到的，当我们打印解引用的值时，它现在被设置为 `jellyfish` 。
你可能没有意识到，但实际上我们也改变了 `creature` 变量的值。这是因为 `pointer` 变量实际上是指向 `creature` 变量的地址。这意味着如果我们改变了 `pointer` 变量所指向的值，同时我们也会改变 `creature` 变量的值。

<center>main.go</center>

```go
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

输出看起来像这样：

```shell
creature = shark
pointer = 0xc000010200
*pointer = shark
*pointer = jellyfish
creature = jellyfish
```
虽然这段代码说明了指针的工作原理，但这并不是你在 Go 中使用指针的典型方式。更常见的是在定义函数参数和返回值时使用它们，或者在定义自定义类型的方法时使用它们。让我们看看如何在函数中使用指针来共享对一个变量的访问。
同样，请记住，我们正在打印 `pointer` 的值，是为了说明它是一个指针。在实践中，你不会使用指针的值，除了引用底层的值来检索或更新该值之外。

## 函数指针接收器

当你写一个函数时，你可以定义参数，以 *值* 或 *引用* 的方式传递。通过 *值* 传递意味着该值的副本被发送到函数中，并且在该函数中对该参数的任何改变 *只* 在该函数中影响该变量，而不是从哪里传递。然而，如果你通过 *引用* 传递，意味着你传递了一个指向该参数的指针，你可以在函数中改变该值，也可以改变传递进来的原始变量的值。你可以在我们的[如何在 Go 中定义和调用函数](https://gocn.github.io/How-To-Code-in-Go/docs/32-Understanding_Pointers_in_Go)中阅读更多关于如何定义函数的信息。

什么时候传递一个指针，什么时候发送一个值，都取决于你是否希望这个值发生变化。如果你不希望数值改变，就把它作为一个值来发送。如果你希望你传递给你的变量的函数能够改变它，那么你就把它作为一个指针传递。

为了看到区别，让我们先看看一个通过 `值` 传递参数的函数：

<center>main.go</center>

```go
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

输出看起来像这样：

```shell
1) {Species:shark}
2) {Species:jellyfish}
3) {Species:shark}
```
首先我们创建了一个名为 `Creature` 的自定义类型。它有一个名为 `Species` 的字段，它是一个字符串。在 `main` 函数中，我们创建了一个名为`Creature` 的新类型实例，并将`Species` 字段设置为`shark` 。然后我们打印出变量，以显示存储在 `creature` 变量中的当前值。
接下来，我们调用 `changeCreature`，并传入 `creature` 变量的副本。

`changeCreature` 被定义为接受一个名为 `creature` 的参数，并且它是我们之前定义的 `Creature` 类型的函数。然后我们将`Species` 字段的值改为 `jellyfish` 并打印出来。注意在 `changeCreature` 函数中，`Species` 的值现在是 `jellyfish`，并且打印出 `2) {Species:jellyfish}`。这是因为我们被允许在我们的函数范围内改变这个值。

然而，当 `main` 函数的最后一行打印出 `creature` 的值时，`Species` 的值仍然是 `shark` 。值没有变化的原因是我们通过 *值* 传递变量。这意味着在内存中创建了一个值的副本，并传递给 `changeCreature` 函数。这允许我们有一个函数，可以根据需要对传入的任何参数进行修改，但不会影响函数之外的任何变量。

接下来，让我们改变 `changeCreature` 函数，使其通过 *引用* 接受一个参数。我们可以通过使用星号（`*`）操作符将类型从 `Creature` 改为指针来做到这一点。我们现在传递的不是一个 `Creature`，而是一个指向 `Creature` 的指针，或者是一个 `*Creature`。在前面的例子中，`creature` 是一个 `struct`，它的 `Species` 值为 `shark`。`*creature` 是一个指针，不是一个结构体，所以它的值是一个内存位置，这就是我们传递给 `changeCreature()` 真正的东西。

<center>main.go</center>

```go
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

运行这段代码可以看到以下输出：

```shell
1) {Species:shark}
2) &{Species:jellyfish}
3) {Species:jellyfish}
```
注意，现在当我们在 `changeCreature` 函数中把 `Species` 的值改为 `jellyfish` 时，它也改变了 `main` 函数中定义的原始值。这是因为我们通过 *引用* 传递了 `creature` 变量，它允许访问内存里的原始值并可以根据需要改变它。
因此，如果你想让一个函数能够改变一个值，你需要通过引用来传递它。要通过引用传递，你就需要传递变量的指针，而不是变量本身。

然而，有时你可能没有为一个指针定义一个实际的值。在这些情况下，有可能在程序中出现[恐慌](https://gocn.github.io/How-To-Code-in-Go/docs/19-Handling_Panics_in_Go _DigitalOcean)。让我们来看看这种情况是如何发生的，以及如何对这种潜在的问题进行规划。

## 空指针

Go 中的所有变量都有一个[零值](https://gocn.github.io/How-To-Code-in-Go/docs/11-How_To_Use_Variables_and_Constants_in_Go/#%E9%9B%B6%E5%80%BC)。即使对指针来说也是如此。如果你声明了一个类型的指针，但是没有赋值，那么零值将是 `nil`。`nil` 是一种表示变量 "没有被初始化" 的方式。

在下面的程序中，我们定义了一个指向 `Creature` 类型的指针，但是我们从来没有实例化过 `Creature` 的实际实例，也没有将它的地址分配给 `creature` 指针变量。该值将是 `nil`，因此我们不能引用任何定义在 `Creature` 类型上的字段或方法：

<center>main.go</center>

```go
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

输出看起来像这样：

```shell
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
当我们运行程序时，它打印出了 `creature` 变量的值，该值是 `<nil>`。然后我们调用 `changeCreature` 函数，当该函数试图设置 `Species` 字段的值时，它 *panics*(恐慌) 了。这是因为实际上没有创建 `creature` 变量的实例。正因为如此，程序没有地方可以实际存储这个值，所以程序就恐慌了。
在 Go 中很常见的是，如果你以指针的形式接收一个参数，在对它进行任何操作之前，你要检查它是否为 `nil`，以防止程序恐慌。

这是检查 `nil` 的一种常见方法：

```go
if someVariable == nil {
	// print an error or return from the method or fuction
}
```

实际上，你想确保你没有一个 `nil` 指针被传入你的函数或方法。如果有的话，你可能只想返回，或者返回一个错误，以表明一个无效的参数被传递到函数或方法中。下面的代码演示了对 `nil` 的检查：

<center>main.go</center>

```go
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

我们在 `changeCreature` 中添加了一个检查，看 `creature` 参数的值是否为 `nil`。如果是，我们打印出 "creature is nil"，并返回函数。否则，我们继续并改变 `Species` 字段的值。如果我们运行该程序，我们现在将得到以下输出：

```shell
1) <nil>
creature is nil
3) <nil>
```
请注意，虽然我们仍然为 `creature` 变量设置了 `nil` 值，但我们不再恐慌，因为我们正在检查这种情况。
最后，如果我们创建一个 `Creature` 类型的实例，并将其赋值给 `creature` 变量，程序现在将按照预期改变值：

<center>main.go</center>

```go
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

现在我们有了一个 `Creature` 类型的实例，程序将运行，我们将得到以下预期输出：

```shell
1) &{Species:shark}
2) &{Species:jellyfish}
3) &{Species:jellyfish}
```
当你在使用指针时，程序有可能会出现恐慌。为了避免恐慌，你应该在试图访问任何字段或定义在其上的方法之前，检查一个指针值是否为 `nil`。
接下来，让我们看看使用指针和值是如何影响在一个类型上定义方法的。

## 方法指针接收器

Go 中的 *接收器* 是指在方法声明中定义的参数。看一下下面的代码：

```go
type Creature struct {
	Species string
}

func (c Creature) String() string {
	return c.Species
}
```

这个方法的接收器是 `c Creature`。它说明 `c` 的实例属于 `Creature` 类型，你将通过该实例变量引用该类型。

方法跟函数一样，也是根据你送入的参数是指针还是值而有不同的行为。最大的区别是，如果你用一个值接收器定义一个方法，你就不能对该方法所定义的那个类型的实例进行修改。

有的时候，你希望你的方法能够更新你所使用的变量的实例。为了实现这一点，你会想让接收器成为一个指针。

让我们给我们的 `Creature` 类型添加一个 `Reset` 方法，将 `Species`字段设置为一个空字符串：

<center>main.go</center>

```go
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

如果我们运行该程序，我们将得到以下输出：

```shell
1) {Species:shark}
2) {Species:shark}
```
注意到即使在 `Reset` 方法中我们将 `Species` 的值设置为空字符串，当我们在 `main` 函数中打印出 `creature` 变量的值时，该值仍然被设置为 `shark` 。这是因为我们定义的 `Reset` 方法有一个 `值` 接收器。这意味着该方法只能访问 `creature` 变量的 *副本*。
如果我们想在方法中修改 `creature` 变量的实例，我们需要将它们定义为有一个 `指针` 接收器：

<center>main.go</center>

```go
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

注意，我们现在在定义 `Reset` 方法时，在 `Creature` 类型前面添加了一个星号（`*`）。这意味着传递给 `Reset` 方法的 `Creature` 实例现在是一个指针，因此当我们进行修改时，将影响到该变量的原始实例。

```shell
1) {Species:shark}
2) {Species:}
```
现在 `Reset` 方法已经改变了 `Species` 字段的值。
## 总结

将一个函数或方法定义为通过 *值* 或通过 *引用*，将影响你的程序的哪些部分能够对其他部分进行修改。控制该变量何时能被改变，将使你能写出更健壮和可预测的软件。现在你已经了解了指针，你也可以看到它们是如何在接口中使用的。

 

