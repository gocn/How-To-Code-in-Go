# 如何在 Go 中编写 Switch 语句

### 介绍

[条件语句](https://www.digitalocean.com/community/tutorials/how-to-write-conditional-statements-in-go)使程序员有能力指导他们的程序在某个条件为真时采取某些行动，在条件为假时采取另一种行动。经常，我们想把一些[变量](https://www.digitalocean.com/community/tutorials/how-to-use-variables-and-constants-in-go#understanding-variables)与多个可能的值进行比较，在每种情况下采取不同的行动。仅仅使用[`if`语句](https://www.digitalocean.com/community/tutorials/how-to-write-conditional-statements-in-go#if-statements)就可以做到这一点。然而，编写软件不仅是为了让事情顺利进行，也是为了向未来的自己和其他开发者传达你的意图。`switch`是一个替代性的条件语句，对于传达你的 Go 程序在遇到不同选项时采取的行动很有用。

我们可以用 switch 语句编写的所有内容也可以用`if`语句编写。在本教程中，我们将看几个例子，看看 switch 语句能做什么，它所取代的`if`语句，以及它最合适的应用场合。

## Switch 语句的结构

Switch 通常用于描述当一个变量被分配到特定值时程序所采取的行动。下面的例子演示了我们如何使用 `if` 语句来完成这个任务。

```go
package main

import "fmt"

func main() {
	flavors := []string{"chocolate", "vanilla", "strawberry", "banana"}

	for _, flav := range flavors {
		if flav == "strawberry" {
			fmt.Println(flav, "is my favorite!")
			continue
		}

		if flav == "vanilla" {
			fmt.Println(flav, "is great!")
			continue
		}

		if flav == "chocolate" {
			fmt.Println(flav, "is great!")
			continue
		}

		fmt.Println("I've never tried", flav, "before")
	}
}
```

这将输出如下信息：

```Output
chocolate is great!
vanilla is great!
strawberry is my favorite!
I've never tried banana before
```

在`main`中，我们定义了一个[slice](https://www.digitalocean.com/community/tutorials/understanding-data-types-in-go#slices)的冰激凌口味。然后我们使用一个[`for loop`](https://www.digitalocean.com/community/tutorials/how-to-construct-for-loops-in-go)来迭代它们。我们使用三个`if`语句来打印不同的信息，表明对不同冰淇淋口味的偏好。每个`if`语句必须使用`continue`语句来停止`for`循环的执行，这样就不会在最后打印出首选冰淇淋口味的默认信息。

当我们添加新的偏好时，我们必须不断添加`if`语句来处理新的情况。重复的信息，如 "香草"和 "巧克力"的情况，必须有重复的`if`语句。对于我们代码的未来读者（包括我们自己）来说，`if`语句的重复性掩盖了它们所做的重要部分--将变量与多个值进行比较并采取不同的行动。另外，我们的回退信息与条件语句分开，使得它看起来不相关。转换器 "语句可以帮助我们更好地组织这个逻辑。

`switch` 语句以 `switch` 关键字开始，在其最基本的形式下，后面是一些要进行比较的变量。之后是一对大括号（`{}`），其中可以出现多个*case 子句*。case 子句描述了当提供给 switch 语句的变量等于 case 子句所引用的值时，Go 程序应该采取的行动。下面的例子将先前的例子转换为使用一个`switch`而不是多个`if`语句：

```go
package main

import "fmt"

func main() {
	flavors := []string{"chocolate", "vanilla", "strawberry", "banana"}

	for _, flav := range flavors {
		switch flav {
		case "strawberry":
			fmt.Println(flav, "is my favorite!")
		case "vanilla", "chocolate":
			fmt.Println(flav, "is great!")
		default:
			fmt.Println("I've never tried", flav, "before")
		}
	}
}
```

输出与之前相同：

```Output
chocolate is great!
vanilla is great!
strawberry is my favorite!
I've never tried banana before
```

我们再次在`main`中定义了一片冰淇淋的口味，并使用`range`语句来遍历每个口味。但是这一次，我们使用了一个`switch`语句来检查`flav`变量。我们使用两个`case'子句来表示偏好。我们不再需要`继续'语句，因为只有一个`case`子句将被`switch`语句执行。我们还可以将"巧克力"和 "香草"条件的重复逻辑结合起来，在 `case`子句的声明中用逗号将其分开。`default`子句是我们的万能子句。它将对我们在 `switch` 语句中没有考虑到的任何口味运行。在这种情况下，"香蕉"将导致 `default` 的执行，打印出 "I've never tried banana before"的信息。

这种简化形式的`switch`语句解决了它们最常见的用途：将一个变量与多个替代品进行比较。它还为我们提供了便利，当我们想对多个不同的值采取相同的行动，以及在没有满足所列的条件时，通过使用所提供的`default`关键字采取一些其他行动。

当这种简化的`switch`形式被证明太有局限性时，我们可以使用一种更通用的`switch`语句形式。

## 通常的 Switch 语句

`switch`语句对于将更复杂的条件集合在一起以显示它们之间有某种联系是很有用的。这在将某些变量与一定范围的值进行比较时最常用，而不是像前面的例子中的特定值。下面的例子使用`if`语句实现了一个猜谜游戏，可以从`switch`语句中受益：

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	rand.Seed(time.Now().UnixNano())
	target := rand.Intn(100)

	for {
		var guess int
		fmt.Print("Enter a guess: ")
		_, err := fmt.Scanf("%d", &guess)
		if err != nil {
			fmt.Println("Invalid guess: err:", err)
			continue
		}

		if guess > target {
			fmt.Println("Too high!")
			continue
		}

		if guess < target {
			fmt.Println("Too low!")
			continue
		}

		fmt.Println("You win!")
		break
	}
}
```

输出将取决于所选择的随机数和你玩游戏的程度。下面是一个例子会话的输出：
```Output
Enter a guess: 10
Too low!
Enter a guess: 15
Too low!
Enter a guess: 18
Too high!
Enter a guess: 17
You win!
```

我们的猜谜游戏需要一个随机数来比较猜测的结果，所以我们使用`math/rand`包中的`rand.Intn`函数。为了确保我们每次玩游戏都能得到不同的`target`值，我们使用`rand.Seed`来根据当前时间随机化随机数发生器。`rand.Intn`的参数`100`将给我们一个0-100范围内的数字。然后我们使用`for`循环来开始收集玩家的猜测。

`fmt.Scanf`函数为我们提供了一种方法来读取用户的输入到我们选择的变量中。它接受一个格式化的字符串动词，将用户的输入转换为我们期望的类型。这里的`%d`意味着我们期望一个 `int`，我们传递 `guess` 变量的地址，这样 `fmt.Scanf` 就能够设置该变量。在[处理任何解析错误](https://www.digitalocean.com/community/tutorials/handling-errors-in-go)之后，我们使用两个`if`语句来比较用户的猜测和`target`值。它们返回的`string`和`bool`一起控制显示给玩家的信息，以及游戏是否会退出。

这些 `if` 语句掩盖了一个事实，即变量被比较的数值范围都有某种联系。一眼就能看出我们是否遗漏了该范围的某些部分，这也是很困难的。下一个例子重构了前面的例子，用`switch`语句代替：

```go
package main

import (
	"fmt"
	"math/rand"
)

func main() {
	target := rand.Intn(100)

	for {
		var guess int
		fmt.Print("Enter a guess: ")
		_, err := fmt.Scanf("%d", &guess)
		if err != nil {
			fmt.Println("Invalid guess: err:", err)
			continue
		}

		switch {
		case guess > target:
			fmt.Println("Too high!")
		case guess < target:
			fmt.Println("Too low!")
		default:
			fmt.Println("You win!")
			return
		}
	}
}
```

这将产生类似以下的输出:

```Output
Enter a guess: 25
Too low!
Enter a guess: 28
Too high!
Enter a guess: 27
You win!
```

在这个版本的猜谜游戏中，我们用一个`switch`语句代替了`if`语句块。我们省略了`switch`的表达式参数，因为我们只对使用`switch`来收集条件语句感兴趣。每个`case`子句包含一个不同的表达式，将`guess`与`target`进行比较。与第一次用`switch`代替`if`语句类似，我们不再需要`continue`语句，因为只有一个`case`子句会被执行。最后，`default`子句处理`guess == target`的情况，因为我们已经用另外两个`case`子句覆盖了所有其他可能的值。

在我们目前看到的例子中，正好有一个 case 语句将被执行。偶尔，你可能希望结合多个`case`子句的行为。`switch`语句提供了另一个实现这种行为的关键字。

## Fallthrough

有时你想重复使用另一个 `case` 子句包含的代码。在这种情况下，可以使用 `fallthrough` 关键字要求Go运行下一个 `case` 子句的主体。下面这个例子修改了我们之前的冰淇淋口味的例子，以更准确地反映我们对草莓冰淇淋的热情：

```go
package main

import "fmt"

func main() {
	flavors := []string{"chocolate", "vanilla", "strawberry", "banana"}

	for _, flav := range flavors {
		switch flav {
		case "strawberry":
			fmt.Println(flav, "is my favorite!")
			fallthrough
		case "vanilla", "chocolate":
			fmt.Println(flav, "is great!")
		default:
			fmt.Println("I've never tried", flav, "before")
		}
	}
}
```

将得到如下输出：

```Output
chocolate is great!
vanilla is great!
strawberry is my favorite!
strawberry is great!
I've never tried banana before
```

正如我们之前看到的，我们定义了一个 `string` 片段来表示口味，并使用 `for` 循环来迭代。这里的 `switch` 语句与我们之前看到的语句相同，但是在 `case` 子句的末尾添加了 `fallthrough` 关键字，即 `strawberry`。这将使 Go 运行`case "strawberry":`的主体，首先打印出字符串`strawberry is my favorite!`。当它遇到`fallthrough`时，它将运行下一个`case`子句的主体。这将导致`case "vanilla", "chocolate":`的主体运行，打印出`strawberry is great!`。

Go 开发人员不经常使用`fallthrough`关键字。通常情况下，通过使用`fallthrough`实现的代码重用，可以通过定义一个具有公共代码的函数来更好地获得。由于这些原因，一般不鼓励使用`fallthrough`。

## 总结

`switch`语句帮助我们向阅读代码的其他开发者传达出彼此有某种联系。使我们在将来添加新的情况时更容易添加不同的行为，并有可能确保任何忘记的事情也能通过`default`子句得到正确处理。下次你发现自己写的多个`if`语句都涉及同一个变量时，试着用`switch`语句重写它--你会发现当需要考虑其他值时，它将更容易重写。

如果你想了解更多关于 Go 编程语言的信息，请查看整个[How To Code in Go 系列](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go)