# 如何在 Go 使用 interface

## 简述

编写灵活的、可重复使用的、模块化的代码对于开发多功能的程序至关重要。以这种方式开发，可以避免在多个地方做同样的修改，从而确保代码更容易维护。如何完成这个目标，不同语言有不同的实现方法来完成这个目标。例如，[*继承*](https://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming))是一种常见的方法，在 Java、C++、C#等语言中都有使用。

开发者们也可以通过[*组合*](https://en.wikipedia.org/wiki/Object_composition)实现这个设计目标。组合是一个将多个对象和数据类型组合到一个复杂的结构体中的方式。这个是 Go 用来促进代码复用，模块化和灵活性的方法。在 Go 中 intrerface 提供了一个方法用于构建复杂的组合，学习使用它们，将会使你创建通用的可重复使用的代码。

在这篇文章中，我们将会学习如何构建那些有相同行为的自定义类型，用于复用代码。 我们还将学习如何为我们自己的自定义类型实现 interface，以满足在另一个包中定义的接口。

## 定义一个行为

组合实现的核心之一是使用 interface。一个 interface 定义一个类型的行为。Go 标准库中最常用的 interface 之一是[`fmt.Stringer`](https://golang.org/pkg/fmt/#Stringer) 接口：

```go
type Stringer interface {
    String() string
}
```

第一行代码定义一个`type`叫`Stringer`。然后表明它是一个`interface`。就好像定义一个结构体，Go 使用大括号(`{}`)来囊括 interface 的定义。跟结构体的定义相比，我们只定义`interface`的*行为*，就是“这个类型可以做什么”

对这个`Stringer`接口的例子来说，唯一的行为就是`String()`这个方法。这个方法没有参数。

接着，让我们看一些代码，这些代码有`fmt.Stringer`的行为：

```go
package main

import "fmt"

type Article struct {
	Title string
	Author string
}

func (a Article) String() string {
	return fmt.Sprintf("The %q article was written by %s.", a.Title, a.Author)
}

func main() {
	a := Article{
		Title: "Understanding Interfaces in Go",
		Author: "Sammy Shark",
	}
	fmt.Println(a.String())
}
```

第一件事是我们创建了一个新的类型叫做`Article`。这个类型有一个`Title`和一个`Author`字段，两个都是 string 的 [数据类型](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go):

```go
...
type Article struct {
	Title string
	Author string
}
...
```

接着，我们为 Article 类型定义了一个叫做 String 的 [`方法`](https://www.digitalocean.com/community/tutorials/defining-methods-in-go)。`String`方法将会返回一个用于表示`Article`类型的字符串：

```go
...
func (a Article) String() string {
	return fmt.Sprintf("The %q article was written by %s.", a.Title, a.Author)
}
...
```

然后，在我们的`main`[function](https://www.digitalocean.com/community/tutorials/how-to-define-and-call-functions-in-go)里，我们创建一个`Article`类型的实例，并且将它赋值给一个[变量](https://www.digitalocean.com/community/tutorials/how-to-use-variables-and-constants-in-go)叫`a`。我们给`Title`字段设置了一个值，为`"理解Go中的Interfaces"`，给`Author`字段赋值`"Sammy Shark"`：

```go
...
a := Article{
	Title: "Understanding Interfaces in Go",
	Author: "Sammy Shark",
}
...
```

紧接着，我们通过调用`fmt.Println`并传入调用`a.String()`后的结果，打印出`String`方法的结果：

```go
...
fmt.Println(a.String())
```

随后运行程序，你会发现如下输出：

```text
Output
The "Understanding Interfaces in Go" article was written by Sammy Shark.
```

至此，我们还没有使用 interface，但是我们创建了一个具备一个行为的类型。这个行为匹配`fmt.Stringer`接口。随后，让我们看看如何利用这种行为来使我们的代码更容易重复使用。

## 定义一个 interface

现在，我们已经用所需的行为定义了我们的类型，我们可以看看如何使用该行为。

然而，在这之前，让我们看看如果我们想在一个函数中从`Article`类型中调用`String`方法，我们需要做什么：

```go
package main

import "fmt"

type Article struct {
	Title string
	Author string
}

func (a Article) String() string {
	return fmt.Sprintf("The %q article was written by %s.", a.Title, a.Author)
}

func main() {
	a := Article{
		Title: "Understanding Interfaces in Go",
		Author: "Sammy Shark",
	}
	Print(a)
}

func Print(a Article) {
	fmt.Println(a.String())
}
```

这段代码中，我们添加了一个名为`Print`的新函数，该函数接收一个`Article`作为参数。请注意，`Print`函数唯一做的事情是调用`String`方法。正因为如此，我们则可以定义一个接口来传递给函数。

```go
package main

import "fmt"

type Article struct {
	Title string
	Author string
}

func (a Article) String() string {
	return fmt.Sprintf("The %q article was written by %s.", a.Title, a.Author)
}

type Stringer interface {
	String() string
}

func main() {
	a := Article{
		Title: "Understanding Interfaces in Go",
		Author: "Sammy Shark",
	}
	Print(a)
}

func Print(s Stringer) {
	fmt.Println(s.String())
}
```

这里我们创建了一个 interface 叫做`Stringer`：

```go
...
type Stringer interface {
	String() string
}
...
```

`Stringer`interface 只有一个方法，叫做`String()`，返回一个`string`。[method](https://www.digitalocean.com/community/tutorials/defining-methods-in-go)是一个特殊的函数，在 Go 中被限定于一个特殊类型。不像函数，一个方法只能从它所定义的类型的实例中被调用。

然后我们更新`Print`方法的签名来接收一个`Stringer`，而不是一个`Article`的具体类型。因为编译器知道`Stringer`接口定义了`String`方法，所以它只接收也有`String`方法的类型。

现在我们可以对任何满足`Stringer`接口的东西使用`Print`方法。让我们创建另一个类型来证明这一点：

```go
package main

import "fmt"

type Article struct {
	Title  string
	Author string
}

func (a Article) String() string {
	return fmt.Sprintf("The %q article was written by %s.", a.Title, a.Author)
}

type Book struct {
	Title  string
	Author string
	Pages  int
}

func (b Book) String() string {
	return fmt.Sprintf("The %q book was written by %s.", b.Title, b.Author)
}

type Stringer interface {
	String() string
}

func main() {
	a := Article{
		Title:  "Understanding Interfaces in Go",
		Author: "Sammy Shark",
	}
	Print(a)

	b := Book{
		Title:  "All About Go",
		Author: "Jenny Dolphin",
		Pages:  25,
	}
	Print(b)
}

func Print(s Stringer) {
	fmt.Println(s.String())
}
```

现在，我们添加了第二个类型叫`Book`。它同样也有定义`String`方法。这表示它也满足`Stringer`接口。因此，我们也可以传递它到`Print`函数：

```text
Output
The "Understanding Interfaces in Go" article was written by Sammy Shark.
The "All About Go" book was written by Jenny Dolphin. It has 25 pages.
```

到目前为止，我们已经演示了如何只使用一个 interface。然而，一个 interface 可以有不止一个行为的定义。接下来，我们将看到如何通过声明更多的方法来使我们的 interface 更加通用。

## 多行为 interface

编写 Go 代码的核心原则之一是编写小而简洁的类型，并将它们组成更大，更复杂的类型。组合 interface 也是一样的。为了了解我们是如何建立一个 interface 的，我们先从只定义一个 interface 开始。我们将会定义 2 个形状，一个`Circle`和一个`Square`，然后他们都会定义一个方法叫`Area`。这个方法会返回它们对应形状的几何面积：

```go
package main

import (
	"fmt"
	"math"
)

type Circle struct {
	Radius float64
}

func (c Circle) Area() float64 {
	return math.Pi * math.Pow(c.Radius, 2)
}

type Square struct {
	Width  float64
	Height float64
}

func (s Square) Area() float64 {
	return s.Width * s.Height
}

type Sizer interface {
	Area() float64
}

func main() {
	c := Circle{Radius: 10}
	s := Square{Height: 10, Width: 5}

	l := Less(c, s)
	fmt.Printf("%+v is the smallest\n", l)
}

func Less(s1, s2 Sizer) Sizer {
	if s1.Area() < s2.Area() {
		return s1
	}
	return s2
}
```

因为每个类型都定义了`Area`方法，我们可以创建一个 interface 来定义这个行为。我们创建如下的`Sizer`interface:

```go
...
type Sizer interface {
	Area() float64
}
...
```

然后定义一个函数叫做`Less`，传入 2 个`Sizer`并返回最小的那一个：

```go
...
func Less(s1, s2 Sizer) Sizer {
	if s1.Area() < s2.Area() {
		return s1
	}
	return s2
}
...
```

注意到我们不仅接收 2 个都为`Sizer`的类型，而且返回的结果也用`Sizer`。这意味着我们不再返回一个`Square`或者一个`Circle`，而是`Sizer`interface。

最后，我们打印出哪一个是最小的面积：

```text
Output
{Width:5 Height:10} is the smallest
```

接着，让我们给每个类型添加另一个行为。这次我们添加`String()`方法，返回一个 string。这个满足`fmt.Stringer`interface:

```go
package main

import (
	"fmt"
	"math"
)

type Circle struct {
	Radius float64
}

func (c Circle) Area() float64 {
	return math.Pi * math.Pow(c.Radius, 2)
}

func (c Circle) String() string {
	return fmt.Sprintf("Circle {Radius: %.2f}", c.Radius)
}

type Square struct {
	Width  float64
	Height float64
}

func (s Square) Area() float64 {
	return s.Width * s.Height
}

func (s Square) String() string {
	return fmt.Sprintf("Square {Width: %.2f, Height: %.2f}", s.Width, s.Height)
}

type Sizer interface {
	Area() float64
}

type Shaper interface {
	Sizer
	fmt.Stringer
}

func main() {
	c := Circle{Radius: 10}
	PrintArea(c)

	s := Square{Height: 10, Width: 5}
	PrintArea(s)

	l := Less(c, s)
	fmt.Printf("%v is the smallest\n", l)

}

func Less(s1, s2 Sizer) Sizer {
	if s1.Area() < s2.Area() {
		return s1
	}
	return s2
}

func PrintArea(s Shaper) {
	fmt.Printf("area of %s is %.2f\n", s.String(), s.Area())
}
```

因为`Circle`和`Square`类型都同时实现了`Area`和`String`方法，我们现在可以创建另一个 interface 来描述这些更广泛的行为。为了实现这个，我们创建了一个 interface 叫做`Shaper`。这个`Shaper`将由`Sizer`interface 和`fmt.Stringer`interface 组成：

```go
...
type Shaper interface {
	Sizer
	fmt.Stringer
}
...
```

**注意：**基于习惯，尝试以`er`结尾来给你的 interface 命名，例如`fmt.Stringer`，`io.Writer`等等。这也是为什么我们用`Shaper`来命名我们的 interface，而不是`Shape`。

现在我们可以创建一个名为`PrintArea`的函数，该函数以`Shaper`为参数。这意味着我们可以对传入的值调用`Area`和`String`这两个方法：

```go
...
func PrintArea(s Shaper) {
	fmt.Printf("area of %s is %.2f\n", s.String(), s.Area())
}
```

如果我们运行程序，将会收到如下输出：

```text
Output
area of Circle {Radius: 10.00} is 314.16
area of Square {Width: 5.00, Height: 10.00} is 50.00
Square {Width: 5.00, Height: 10.00} is the smallest
```

我们现在已经看到了我们如何创建较小的 interface，并根据需要将它们建立成较大的 interface。虽然我们可以从较大的 interface 开始，并将其传递给我们所有的函数，但最好的做法是只将最小的 interface 发送给需要的函数。这通常会使代码更加清晰，因为任何接收特定的较小的 interface 的东西都只打算执行其定义的行为。

例如，如果我们将`Shaper`传递给`Less`函数，我们可能会认为它要同时调用`Area`和`String`方法。然而，由于我们只打算调用`Area`方法，这使得`Less`函数很清楚，因为我们知道我们只能调用传递给它的任何参数的`Area`方法。

## 总结

我们已经看到，创建较小的 interface 并将其构建为较大的 interface，可以让我们只分享我们需要的函数或方法。我们还了解到，我们可以从其他 interface 中组成我们的 interface，包括从其他包中定义的 interface，而不仅仅是我们的包。
