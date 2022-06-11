# 在 Go 中定义方法

## 简介

[函数]({{< relref "/docs/27-How_To_Define_and_Call_Functions_in_Go.md" >}})允许你将逻辑组织成可重复的程序，每次运行时可以使用不同的参数。在定义函数的过程中，你常常会发现，可能会有多个函数每次对同一块数据进行操作。Go 可以识别这种模式，并允许您定义特殊的函数，称为方法，其目的是对某些特定类型（称为接收器）的实例进行操作。将方法添加到类型中，不仅可以传达数据是什么，还可以传达如何使用这些数据。

## 定义一个方法

定义一个方法的语法与定义一个函数的语法很相似。唯一的区别是在 `func` 关键字后面增加了一个额外的参数，用于指定方法的接收器。接收器是你希望定义的方法的类型声明。下面的例子为一个结构体类型定义了一个方法。

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

如果你运行这段代码，输出将是：

```bash
Output
Sammy says Hello!
```

我们创建了一个名为 `Creature` 的结构，包含字符串类型的 `Name` 和 `Greeting` 字段。这个 `Creature` 结构体有一个定义的方法，即 `Greet`。在接收器声明中，我们将 `Creature` 的实例分配给变量 `c`，以便我们在 `fmt.Printf` 中打印问候信息时可以引用 `Creature` 字段。

在其他编程语言中，方法调用的接收器通常用一个关键字来表示（例如：`this` 或 `self`）。Go 认为接收器和其他变量一样，是一个变量，所以你可以自由地命名。社区对这个参数的首选风格是接收器类型小写版本的第一个字符。在这个例子中，我们使用了 `c`，因为接收器的类型是 `Creature`。

在 `main` 方法中，我们创建了一个 `Creature` 实例，并为其 `Name` 和 `Greeting` 字段进行赋值。我们在这里调用了 `Greet` 方法，用 `.` 连接类型名和方法名，并提供 `Creature` 实例作为第一个参数。

Go 提供了另一种更简洁的方式来调用结构体实例的方法，如本例所示：

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

如果你运行这个，输出将与前面的例子相同：

```bash
Output
Sammy says Hello!
```

这个例子与前一个例子相同，但这次我们使用点号来调用 `Greet` 方法，使用存储在 `sammy` 变量中的 `Creature` 作为接收器，这是对第一个例子中的方法调用的简化。标准库和 Go 社区更喜欢这种风格，以至于你很少看到前面所示的方法调用风格。

下一个例子展示了使用点号比较普遍的一个原因：

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

如果你运行这段代码，输出看起来像这样：

```go
Output
Sammy says Hello!!
Farewell gophers !
Sammy says Hello!!
Farewell gophers !
```

我们修改了前面的例子，引入了另一个名为 `SayGoodbye` 的方法，并将 `Greet` 改为返回一个 `Creature`，这样我们就可以对该实例调用更多的方法。在 `main` 方法中，我们首先使用点号调用 `sammy` 变量上的 `Greet` 和 `SayGoodbye` 方法，然后使用函数式调用方式。

两种风格输出的结果相同，但使用点号的例子更易读。点号调用链路还会告诉我们方法被调用的顺序，而函数式则颠倒了这个顺序。在 `SayGoodbye` 的调用中增加了一个参数，进一步模糊了方法调用的顺序。点号调用的清晰性是 Go 中调用方法的首选风格，无论是在标准库中还是在整个 Go 生态的第三方包中都是如此。

相对于定义对某些值进行操作的方法，为类型定义方法对 Go 编程语言还有其他特殊意义，方法是接口背后的核心概念。

## 接口

当你在 Go 中为任何类型定义方法时，该方法会被添加到该类型的方法集中。方法集是与该类型相关联的方法的集合，并被 Go 编译器用来确定某种类型是否可以分配给具有接口类型的变量。接口类型是一种方法的规范，被编译器用来保证一个类型会实现这些方法。任何具有与接口定义中相同名称、相同参数与相同返回值的方法类型都被称为实现了该接口，并允许被分配给具有该接口类型的变量。下面是标准库中 `fmt.Stringer` 接口的定义。

```go
type Stringer interface {
  String() string
}
```

一个类型要实现 `fmt.Stringer` 接口，需要提供一个返回 `string` 的 `String()` 方法。实现了这个接口，当你把你的类型实例传递给 `fmt` 包中定义的函数时，你的类型就可以完全按照你的意愿被打印出来（有时称为 “pretty-printed”）。下面的例子定义了一个实现了这个接口的类型：

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

当你运行该代码时，你会看到这样的输出：

```bash
Output
ocean contains : sea urchin, lobster, shark
```

这个例子定义了一个名为 `Ocean` 的新结构体类型。`Ocean` 实现了 `fmt.Stringer` 接口，因为 `Ocean` 定义了一个名为 `String` 的方法，该方法不需要参数，返回一个 `string`。在 `main` 方法中，我们定义了一个新的 `Ocean`，并把它传递给一个 `log` 函数，该函数首先接收一个 `string` 来打印，然后是任何实现 `fmt.Stringer` 的参数。Go 编译器允许我们在这里传递 `o`，因为 `Ocean` 实现了 `fmt.Stringer` 所要求的所有方法。在 `log` 中，我们使用 `fmt.Println` ，当它遇到 `fmt.Stringer` 作为其参数之一时，会调用 `Ocean` 的 `String` 方法。

如果 `Ocean` 没有实现 `String()` 方法，Go 会产生一个编译错误，因为 `log` 方法要求一个 `fmt.Stringer` 作为其参数。这个错误看起来像这样：

```bash
Output
src/e4/main.go:24:6: cannot use o (type Ocean) as type fmt.Stringer in argument to log:
        Ocean does not implement fmt.Stringer (missing String method)
```

Go 还将确保提供的 `String()` 方法与 `fmt.Stringer` 接口所要求的方法完全一致。如果不匹配，就会产生一个类似这样的错误：

```bash
Output
src/e4/main.go:26:6: cannot use o (type Ocean) as type fmt.Stringer in argument to log:
        Ocean does not implement fmt.Stringer (wrong type for String method)
                have String()
                want String() string
```

在到目前为止的例子中，我们已经在值接收器上定义了方法。也就是说，如果我们使用方法的功能调用，第一个参数（指的是方法所定义的类型）将是一个该类型的值，而不是一个[指针]({{< relref "/docs/32-Understanding_Pointers_in_Go.md" >}})。因此，我们对方法实例所做的任何修改都会在方法执行完毕后被丢弃，因为收到的值是数据的副本。此外，我们也可以在一个类型的指针接收器上定义方法。

## 指针接收器

在指针接收器上定义方法的语法与在值接收器上定义方法的语法几乎相同。不同的是在接收器声明中用星号（`*`）作为类型名称的前缀。下面的例子在指针接收器上定义了一个类型的方法：

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

当你运行这个例子时，你会看到以下输出：

```bash
Output
The S.S. DigitalOcean has the following occupants:
	 Sammy the Shark
	 Larry the Lobster
```

这个例子定义了一个包含 `Name` 和 `occupants` 的 `Boat` 类型。我们想规定其他包中的代码只用 `AddOccupant` 方法来添加乘员，所以我们通过小写字段名的第一个字母使 `occupants` 字段不被导出。我们还想确保调用 `AddOccupant` 会导致 `Boat` 实例被修改，这就是为什么我们通过指针接收器定义 `AddOccupant`。指针作为一个类型的特定实例的引用，而不是该类型的副本。`AddOccupant` 将使用 `Boat` 类型的指针调用，可以保证任何修改都是持久的。

在 `main` 方法中，我们定义了一个新的变量 `b`，它将持有一个指向 `Boat`（`*Boat`）的指针。我们在这个实例上调用了两次 `AddOccupant` 方法来增加两名乘客。`Manifest` 方法是在`Boat` 值上定义的，因为在其定义中，接收器被指定为（`b Boat`）。在 `main` 方法中，我们仍然能够调用 `Manifest`，因为 Go 能够自动解引用指针以获得 `Boat` 值。`b.Manifest()`在这里等同于 `(*b).Manifest()`。

当试图为接口类型的变量赋值时，一个方法是定义在一个指针接收器上还是定义在一个值接收器上有重要的影响。

## 指针接收器和接口

当你为一个接口类型的变量赋值时，Go 编译器会检查被赋值类型的方法集，以确保它实现了所有接口方法。指针接收器和值接收器的方法集是不同的，因为接收指针的方法可以修改其接收器，而接收值的方法则不能。

下面的例子演示了定义两个方法：一个在一个类型的指针接收器上，一个在它的值接收器上。然而，只有指针接收器能够满足本例中也定义的接口：

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

当你运行该代码时，你会看到这样的输出：

```bash
Output
Sammy is on the surface
Sammy is underwater
```

这个例子定义了一个叫做 `Submersible` 的接口，它要求类型实现一个 `Dive()` 方法。然后我们定义了一个包含 `Name` 字段 和 `isUnderwater` 方法的 `Shark` 类型来跟踪 `Shark` 的状态。我们在 `Shark` 的指针接收器上定义了一个 `Dive()` 方法，将 `isUnderwater` 修改为 `true`。我们还定义了值接收器的 `String()` 方法，这样它就可以使用 `fmt.Println` 干净利落地打印出 `Shark` 的状态，方法使用我们之前看过的 `fmt.Println` 所接收的 `fmt.Stringer` 接口。我们还使用了一个函数 `submerge`，它接受一个 `Submersible` 参数。

使用 `Submersible` 接口而不是 `*Shark` 允许 `submerge` 方法只依赖于一个类型所提供的行为。这使得 `submerge` 方法更容易重用，因为你不必为 `Submarine`、`Whale` 或任何其他我们还没有想到的未来水生居民编写新的 `submerge` 方法。只要它们定义了一个 `Dive()` 方法，就可以和 `submerge` 方法一起使用。

在 `main` 方法中，我们定义了一个变量 `s`，它是一个指向 `Shark` 的指针，并立即用 `fmt.Println` 打印了 `s`。这展示了输出的第一部分，`Sammy is on the surface`。我们把 `s` 传给`submerge`，然后再次调用 `fmt.Println`，以 `s` 为参数，看到输出的第二部分，`Sammy is underwater`。

如果我们把 `s` 改成 `Shark`而不是 `*Shark`，Go 编译器会产生错误：

```bash
Output
cannot use s (type Shark) as type Submersible in argument to submerge:
	Shark does not implement Submersible (Dive method has pointer receiver)
```

Go 编译器很好心地告诉我们，`Shark` 确实有一个 `Dive` 方法，它只在指针接收器上定义。当你在自己的代码中看到这条信息时，解决方法是在分配值类型的变量前使用 `&` 操作符，传递一个指向接口类型的指针。

## 总结

在 Go 中声明方法与定义接收不同类型变量的函数本质上没有区别。同样，[使用指针]({{< relref "/docs/32-Understanding_Pointers_in_Go.md" >}})规则也适用。Go 为这种极其常见的函数定义提供了一些便利，并将这些方法收集到可以通过接口类型进行要求的方法集中。有效地使用方法可以让你在代码中使用接口来提高可测试性，并为你的代码的未来读者留下更好的结构。

如果你想了解更多关于 Go 编程语言的一般信息，请查看我们的 [How To Code in Go 系列](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go)。