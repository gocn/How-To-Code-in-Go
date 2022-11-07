# 如何在 Go 中给错误添加额外信息

## 介绍

当 Go 的函数运行失败时，通常会使用 `error` 接口返回一个值，以使调用者能够处理该失败。在很多情况下，开发者会使用 [`fmt`](https://pkg.go.dev/fmt#Errorf) 包中的[`fmt.Errorf`](https://pkg.go.dev/fmt#Errorf) 函数来返回这些值。不过在 Go 1.13 之前，使用此函数的一个缺点是，你会丢失可能导致函数返回错误的其他错误的信息。为了解决这个问题，开发者要么使用某些第三方库来提供一种"包装"其他错误的方法，要么通过创建结构体类型并实现 `Error() string` 方法来创建自定义错误类型。如果有大量的错误不需要被调用者明确处理，那么在有些时候创建这些结构体类型会很无趣且繁琐。因此，Go 在1.13版中新增了一些特性，以更容易处理这些情况。

其中一个特性是使用 `fmt.Errorf` 函数，并传入 `error` 值，来包装错误，后续可以解除包装来访问被包装的实际错误。这将错误包装的特性内置在了 Go 标准库中，无需使用第三方库。

此外，函数 [`errors.Is`](https://pkg.go.dev/errors#Is) 和 [`errors.As`](https://pkg.go.dev/errors#As) 可以更容易地判断某个特定的错误是否被包装在给定的错误中，也让你能够直接访问那个特定的错误而不需要自己解开所有的错误。

在本教程中，你将创建一个程序，用这些函数来让你函数返回的错误附带上额外信息。然后创建你自己的自定义错误结构体，支持包装和解包功能。

## 先决条件

为了更顺畅地阅读本教程，你需要：

* Go 版本 >= 1.13。你可以按照 [如何安装 Go 和设置本地编程环境](https://gocn.github.io/How-To-Code-in-Go/docs/01-How_To_Install_Go_and_Set_Up_a_Local Programming_Environment_on_Ubuntu_18.04_DigitalOcean) 来安装 Go。
* （可选）阅读 [Go 的错误处理](https://gocn.github.io/How-To-Code-in-Go/docs/17-Handling_Errors_in_Go_DigitalOcean) 有助于加深对本教程的理解，但本教程也在更高的层次上覆盖了此文章的部分内容。
* （可选）本教程在 [在 Go 中创建自定义错误](https://gocn.github.io/How-To-Code-in-Go/docs/18-Creating_Custom_Errors_in_Go_DigitalOcean) 的基础上，拓展介绍了 Go 加入的新特性。阅读之前的教程有助于理解本教程，但不是必须的。

## 在用 Go 中返回和处理错误

当程序出现错误时，最佳实践方式是处理这些错误，这样你的用户就不会看到这些错误。但要处理这些错误，你首先需要了解错误本身。在 Go 中，你可以通过使用 `error` 接口从函数中返回相关错误信息，来处理程序中的错误。使用 `error` 接口可以使任何 Go 类型作为 `error` 值返回，只要该类型定义了 `Error() string` 方法。Go 标准库提供了为这些返回值创建 `error` 的功能，例如 [`fmt.Errorf`](https://pkg.go.dev/fmt#Errorf) 函数。

在本节中，你将创建一个程序，用 `fmt.Errorf` 来返回错误的函数 ，同时你也会添加一个错误处理器来检查这些错误是否能被该函数返回。(如果你想了解更多关于在 Go 中处理错误的信息，请看教程：[Go 的错误处理](https://gocn.github.io/How-To-Code-in-Go/docs/17-Handling_Errors_in_Go_DigitalOcean) 。)

许多开发者可能已经有了存放项目的文件夹，但在本教程中，我们使用 `projects` 文件夹。

创建 `projects` 目录并切换至此目录：

```shell
mkdir projects
cd projects
```

在 `projects` 目录下, 创建新文件夹 `errtutorial` 以存放新程序：

```shell
mkdir errtutorial
```

接着，使用 `cd` 命令进入此文件夹：

```shell
cd errtutorial
```

进入 `errtutorial` 目录后，请使用 `go mod init` 命令，创建一个名为 `errtutorial` 的新模块：

```shell
go mod init errtutorial
```

然后，在 `errtutorial` 目录下用 `nano` 或你喜欢的编辑器打开一个名为 `main.go` 的文件：

```shell
nano main.go
```

接下来，你将写一个程序。该程序将循环处理数字 `1` 到 `3` ，并尝试使用 `validateValue` 函数来判断这些数字是否有效。如果数字被确定为无效，程序将使用 `fmt.Errorf` 函数生成一个 `error` 值，并返回。你可以使用 `fmt.Errorf` 函数创建一个`error` 值，其中的错误信息是你提供给函数的信息。它的工作原理与 `fmt.Printf` 类似，但它不是将信息打印到屏幕上，而是将其作为 `error` 返回。

然后，我们将在 `main` 函数中检查错误值，看它是否为 `nil`。如果是 `nil` ，函数就运行成功了，`valid!` 信息会被打印出来。否则，就会打印收到的错误信息。

在开始编程前，请将以下代码粘贴至 `main.go` 中。

> projects/errtutorial/main.go

```go
package main

import (
	"fmt"
)

func validateValue(number int) error {
	if number == 1 {
		return fmt.Errorf("that's odd")
	} else if number == 2 {
		return fmt.Errorf("uh oh")
	}
	return nil
}

func main() {
	for num := 1; num <= 3; num++ {
		fmt.Printf("validating %d... ", num)
		err := validateValue(num)
		if err != nil {
			fmt.Println("there was an error:", err)
		} else {
			fmt.Println("valid!")
		}
	}
}
```

程序中的 `validateValue` 函数接收一个数字，然后判断它是否为有效值，若不是，返回一个 `error`。在本程序中，数字 `1` 是无效的，并返回错误 `that's odd（那是奇数）`。数字 `2` 也是无效的，并返回错误 `uh oh`。`validateValue` 函数使用 `fmt.Errorf` 函数来生成将被返回的 `error` 值。用 `fmt.Errorf` 函数来返回错误很方便，因为它能让你使用类似于 `fmt.Printf` 或 `fmt.Sprintf` 的方式来格式化错误信息，而不需要再将错误字符串传递给 `errors.New`。

* 在 `main` 函数中，`for` 循环将迭代从 `1` 至 `3` 的每个数字，并将值存储在 `num` 变量中。
* 在循环体中，调用 `fmt.Printf` 打印程序当前正在验证的数字。
* 接着，它将调用 `validateValue` 函数并把 `num` （即当前正在验证的数字）作为参数传入 ，并将错误结果存储在 `err` 变量中。
* 最后，如果 `err` 不是 `nil`，就意味着在验证过程中发生了错误，继而使用 `fmt.Println` 打印错误信息。若无错误，错误检查的 `else` 子句将打印 `valid`。

保存程序，使用 `go run` 命令，以 `main.go` 为参数，在 `errtutorial` 目录下运行你的程序：

```shell
go run main.go
```

程序的输出结果将显示，每个数字都进行了验证，数字 `1` 和数字 `2` 都返回了相应的错误。

```
Output
validating 1... there was an error: that's odd
validating 2... there was an error: uh oh
validating 3... valid!
```

当你看程序的输出时，你会注意到程序试图验证所有的三个数字。

* 第一次它说 `validateValue` 函数返回了 `that's odd` 错误，符合对数字 `1` 的预期。
* 下一个值是 `2`，也显示它返回了一个错误，但这次是 `uh oh` 错误。
* 最后，值 `3` 的错误值为 `nil`，意味着没有错误，即数字是有效的。按照 `validateValue` 函数的写法，任何不是 `1` 或 `2` 的值都会返回 `nil` 错误值。

在这一节中，你使用 `fmt.Errorf` 创建了一个 `error` 值，并从函数中返回。你还写了一个错误处理器，当函数返回任何 `error`时，打印出错误信息。但有时，知道错误的具体含义，而不仅仅是 "有错误发生了"，是非常有用的。在下一节中，你将学习如何为特定情况自定义错误处理。

## 使用哨兵错误处理特定错误

当你从一个函数接收到一个 `error` 值时，最基本的错误处理方式是检查 `error` 值是否为 `nil`。这将告诉你该函数是否有错误，但有时你可能想为特定的错误情况自定义错误处理。例如，想象一下，你有个连接到远程服务器的程序，而你得到的唯一错误信息是 "你有一个错误"。你可能希望知道这个错误是由于服务器不可用，还是连接凭证无效导致的。如果你知道这个错误意味着用户的凭证是错误的，你就可以马上告诉用户；如果该错误意味着服务器不可用，你可能想先尝试重新连接几次再告诉用户。分清这些错误之间的区别可以让你写出更健壮和用户友好的程序。

为了判断特定类型的错误，一种可行的方式是在 `error` 类型的变量上调用 `Error` 方法来获取具体的错误信息，然后与你预期的错误类型信息进行比对。想象一下，当错误值是 `uh oh` 时，你想显示一个除了 `there was an error: uh oh` 以外的消息。处理这种情况的一种方法是检查 `Error` 方法返回的值，像这样：

```go
if err.Error() == "uh oh" {
	// Handle 'uh oh' error.
	fmt.Println("oh no!")
}
```

检查 `err.Error()` 返回的字符串值，看看是否为 `uh oh`，就像上面的代码那样，在这种情况下是可行的。但是，如果 `uh oh` 错误字符串在程序中的其他地方略有不同，那么这段代码就无法正常运行。如果错误信息本身需要更新，这种方式检查错误也会导致代码的重大变动，因为每一个检查错误的地方都需要更新。以下面的代码为例：

```go
func giveMeError() error {
	return fmt.Errorf("uh h")
}

err := giveMeError()
if err.Error() == "uh h" {
	// "uh h" error code
}
```

在这段代码中，错误信息里有一个错别字， `uh oh` 少了个 `o`。如果这个问题在某个时候被注意到并被修复，但只是在几个地方添加了这个错误检查后，所有这些地方都需要将错误检查更新为 `err.Error() == "uh oh"`。 但很容易漏掉一个，因为它只变动了一个字符，预期的自定义错误处理程序将不会正常运行，因为它期望的是 `uh h` 而不是 `uh oh`。

在这样的情况下，你可能会想用另外的方式来处理一个特定的错误。通常会创建一个变量，以保存一个错误值。这样，代码就可以根据这个变量而不是某个字符串进行检查。通常，这些变量的名称以 `err` 或 `Err` 开头，来表示它们是错误。如果错误只在它所定义的包内使用，你可以使用 `err` 前缀；如果错误要在其他地方使用，你可以使用 `Err` 前缀，像导出函数和结构体那样，把它变成一个导出值。

现在，我们假设你使用了之前的错别字例子中的某个错误：

```go
var errUhOh = fmt.Errorf("uh h")

func giveMeError() error {
	return errUhOh
}

err := giveMeError()
if err == errUhOh {
	// "uh oh" error code
}
```

在这个例子中，变量 `errUhOh` 被定义为 "uh oh" 错误的错误值（尽管它被拼错了）。`giveMeError` 函数返回 `errUhOh` 的值，因为它想让调用者知道发生了一个 "uh oh" 错误。然后，错误处理代码比较 `giveMeError` 返回的 `err` 值与 `errUhOh`，看是否发生了 "uh oh"错误。即使发现并修复了错别字，所有的代码仍然可以正常运行，因为错误检查是针对 `errUhOh` 进行的，而 `errUhOh` 的值是 `giveMeError` 返回的错误值的固定版本。

一个以这种方式检查和比较的错误值被称为 _哨兵错误_。一个哨兵错误是被设计成唯一的、并在每次比较中都代表了同一个特定含义的值。上面的`errUhOh` 值总是有相同的含义，即，如果发生了一个"uh oh"错误，程序可以依靠将错误与 `errUhOh` 比较来确定是否发生了该错误。

Go 标准库也定义了一些哨兵错误，在开发 Go 程序时可以使用，如 [`sql.ErrNoRows `](https://pkg.go.dev/database/sql#pkg-variables) 错误。`sql.ErrNoRows` 错误用来表示数据库查询没有返回任何结果，因此，就能把它和连接错误区分开来。由于它是一个哨兵错误，它可以在错误检查代码中进行比较，以知道什么时候查询没有返回任何数据行，并且程序可以以不同于其他错误的方式处理这个错误。

一般情况下，我们用 [`errors`](https://pkg.go.dev/errors) 包里的 `errors.New` 函数来创建一个前哨错误值，而不是你一直使用的 `fmt.Errorf` 函数。使用 `errors.New` 代替 `fmt.Errorf` 并不会对错误的运作方式产生任何根本性的改变，尽管如此，这两个函数在大多数时候都可以互换使用。两者最大的区别是 `errors.New` 函数只会创建一个带有静态信息的错误，而 `fmt.Errorf` 函数可以格式化字符串，类似于 `fmt.Printf` 或 `fmt.Sprintf` 。由于哨兵错误是基础错误，其值不会改变，所以通常使用 `errors.New` 来创建它们。

现在，更新你的程序，把 "uh oh" 错误改造成哨兵错误，而不是 `fmt.Errorf`。

首先，打开 `main.go` 文件，添加新的 `errUhOh` 哨兵错误并更新程序。更新 `validateValue` 函数以返回哨兵错误，而不是使用 `fmt.Errorf`。更新 `main` 函数，以检查 `errUhOh` 哨兵错误，并在遇到它时打印 `oh no!`，而不是像显示其他错误那样显示 `there was an error:` 。

> projects/errtutorial/main.go

```go
package main

import (
	"errors"
	"fmt"
)

var (
	errUhOh = errors.New("uh oh")
)

func validateValue(number int) error {
	if number == 1 {
		return fmt.Errorf("that's odd")
	} else if number == 2 {
		return errUhOh
	}
	return nil
}

func main() {
	for num := 1; num <= 3; num++ {
		fmt.Printf("validating %d... ", num)
		err := validateValue(num)
		if err == errUhOh {
			fmt.Println("oh no!")
		} else if err != nil {
			fmt.Println("there was an error:", err)
		} else {
			fmt.Println("valid!")
		}
	}
}
```

保持代码并使用 `go run` 再次运行程序：

```shell
go run main.go
```

这次输出将为数字 `1` 显示通用的错误描述，但是检测到数字 `2` 从 `validateValue` 中返回的 `errUhOh` 错误时，将显示自定义的 `oh no!` 。

```
Output
validating 1... there was an error: that's odd
validating 2... oh no!
validating 3... valid!
```

在你的错误检查中使用哨兵错误可以更容易地处理特殊的错误情况。例如，它们可以帮助你判断正在读取的文件是因为已经到达了文件的末尾（由 [`io.EOF`](https://pkg.go.dev/io#pkg-variables) 哨兵错误表示）而失败，还是因为其他原因而失败。

在本节中，你创建了一个 Go 程序，使用 `errors.New` 来表示特定类型的错误发生时的哨兵错误。随着时间的推移，随着程序的发展，你可能会希望在错误中包含更多的信息，而不仅仅是 `uh oh` 错误值。目前的这个错误值没有提供任何关于错误发生地点或原因的背景信息，导致在大型程序中很难追踪到错误的具体细节。为了帮助排除故障和减少调试时间，你可以利用错误包装来包含你需要的细节。

## 包装和解包错误

包装错误是指将一个错误值放在另一个错误值里面，就像一个包装好的礼物。不过，与包装好的礼物类似，你需要解开包装才能知道里面是什么。包装一个错误可以让你在不丢失原始错误值的前提下加入错误来源或如何发生的额外信息，因为它就在包装好的错误中。

在 Go 1.13 之前，由于你可以创建包含原始错误的自定义错误值，所以也可以对错误进行包装。但是你必须创建自己的包装器，或者使用已经替你做了这些工作的库。Go 在1.13中通过添加 [`errors.Unwrap`](https://pkg.go.dev/errors#Unwrap) 函数和 `fmt.Errorf` 函数的 `%w` 动词，增加了对包装和解包错误的支持，并将其作为标准库的一部分。在这一节中，你将更新程序，使用  `%w`  动词来包装带有更多信息的错误，并用 `errors.Unwrap` 来提取被包装的信息。

### 使用 `fmt.Errorf` 包装错误

在学习包装和解包错误时，让我们先来补充一下对现有的 `fmt.Errorf` 函数的认识。之前，`fmt.Errorf` 可以利用动词来创建带有附加信息的格式化错误信息，例如 `%s` 用于字符串，`%v` 用于普通值。Go 1.13增加了一个新的特殊动词， `%w`。当 `%w` 动词包含在格式化字符串中，并且提供了一个 `error` 值时，从 `fmt.Errorf` 返回的错误将包含被包装的 `error` 的值。

现在，打开 `main.go` 文件，加入一个名为 `runValidation` 的新函数。这个函数接收当前正在验证的数字，并对该数字进行任何需要的验证。在这种情况下，就只需要运行 `validateValue` 函数。如果它遇到一个错误的验证值，它将使用 `fmt.Errorf` 和 `%w` 动词来包装这个错误，以显示发生了 `run error` ，然后返回这个新的错误。你也应该更新 `main` 函数，这样就不会直接调用 `validateValue`，而是调用 `runValidation`。

> projects/errtutorial/main.go

```go
...

var (
	errUhOh = errors.New("uh oh")
)

func runValidation(number int) error {
	err := validateValue(number)
	if err != nil {
		return fmt.Errorf("run error: %w", err)
	}
	return nil
}

...

func main() {
	for num := 1; num <= 3; num++ {
		fmt.Printf("validating %d... ", num)
		err := runValidation(num)
		if err == errUhOh {
			fmt.Println("oh no!")
		} else if err != nil {
			fmt.Println("there was an error:", err)
		} else {
			fmt.Println("valid!")
		}
	}
}
```

保存更新后的代码，运行：

```shell
go run main.go
```

输出看起来像这样： 

```
Output
validating 1... there was an error: run error: that's odd
validating 2... there was an error: run error: uh oh
validating 3... valid!
```

在这个输出中，有几个地方值得注意。首先，你会看到值 `1` 对应的错误信息现在包含了 `run error: that's odd` 。这表明错误被 `runValidation` 的 `fmt.Errorf` 包装了，被包装的错误值 `that's odd` 包含在错误信息中。

但是，有一个问题。为 `errUhOh` 错误添加的特殊错误处理并没有正常运行。如果你看看检查参数 `2` 的那一行，你会发现它显示的是默认的错误信息 `there was an error: run error: uh oh`，而不是预期的 `oh no!` 信息。你知道 `validateValue` 函数仍在返回 `uh oh` 错误，因为你可以在包裹的错误末尾看到它，但 `errUhOh` 的错误检测不再如期运行。发生这种情况是因为 `runValidation` 返回的错误不再是 `errUhOh`，而是由 `fmt.Errorf` 创建的包装好的错误。当 `if` 语句试图比较 `err` 变量和 `errUhOh` 时，它将返回 `false` ，因为 `err` 不再等于 `errUhOh`，它等于*包装着* `errUhOh` 的错误。要修复 `errUhOh` 的错误检查，你需要使用 `errors.Unwrap` 函数，以从包装器中提取内部的错误。

### 使用  `errors.Unwrap` 解包错误

Go 1.13 除了增加了 `%w` 动词外，还在 [`errors`](https://pkg.go.dev/errors) 包中增加了一些新的函数。其一是 `errors.Unwrap` 函数，接收一个 `error` 作为参数，如果传递的错误是一个错误包装器，它会返回被包装的 `error`。如果提供的 `error` 不是一个包装器，返回 `nil`。

现在，再次打开 `main.go` 文件，使用 `errors.Unwrap`，更新 `errUhOh` 错误检查，以处理 `errUhOh` 被包装在一个错误包装器中的情况。

> projects/errtutorial/main.go

```go
func main() {
	for num := 1; num <= 3; num++ {
		fmt.Printf("validating %d... ", num)
		err := runValidation(num)
		if err == errUhOh || errors.Unwrap(err) == errUhOh {
			fmt.Println("oh no!")
		} else if err != nil {
			fmt.Println("there was an error:", err)
		} else {
			fmt.Println("valid!")
		}
	}
}
```

保存并运行：

```shell
go run main.go
```

输出看起来像这样：

```
Output
validating 1... there was an error: run error: that's odd
validating 2... oh no!
validating 3... valid!
```

现在，在输出中，你会看到对 `2` 输入值的 `oh no!` 错误处理回归正常了。你在 `if` 语句中增加的 `errors.Unwrap` 函数调用，让它在 `err` 本身是 `errUhOh` 值时，以及 `err` 是直接包装了 `errUhOh` 的错误时，检测到  `errUhOh`。

在本节中，你使用了添加到 `fmt.Errorf` 中的 `%w` 动词，把 `errUhOh` 错误包装在另一个错误中，并附加信息。然后，你使用 `errors.Unwrap` 来访问被包装在另一个错误中的 `errorUhOh` 错误。将错误以字符串的形式包含在其他错误中，对于人类来说阅读错误信息来说是可行的。但有时你又想在错误包装中包含额外的信息，以帮助程序处理错误，例如HTTP请求错误中的状态代码。这时候，你就可以创建一个新的自定义错误来返回。

## 自定义包装错误

由于 Go 对 `error` 接口的唯一规定是它包含了一个 `Error` 方法，所以可以把许多 Go 类型变成一个自定义的错误。一种方法是通过定义一个结构体类型，包含了关于错误的额外信息，同时也拥有一个 `Error` 方法。

对于一个验证错误，知道哪个值实际导致了错误可能是有用的。接下来，让我们创建一个新的 `ValueError` 结构，它包含一个导致错误的 `Value` 字段和一个包含实际验证错误的 `Err` 字段。自定义错误类型通常在类型名称的末尾使用 `Error` 后缀，以表示它是一个实现了 `error` 接口的类型。

打开 `main.go` 文件，添加新的 `ValueError` 错误结构体，以及一个 `newValueError` 函数来创建错误实例。你还需要为 `ValueError` 创建一个名为 `Error` 的方法，以使该结构体被视为 `error`。这个 `Error` 方法应该返回你希望在错误被转换为字符串时显示的值。在这种情况下，它将使用 `fmt.Sprintf` 来返回一个字符串，这个字符串以  `value error:` 开头，后面是被包装的错误。另外，再更新 `validateValue` 函数，这样它就不会只返回基本错误，而是使用 `newValueError` 函数来返回一个自定义错误：

> projects/errtutorial/main.go

```go
...

var (
	errUhOh = fmt.Errorf("uh oh")
)

type ValueError struct {
	Value int
	Err   error
}

func newValueError(value int, err error) *ValueError {
	return &ValueError{
		Value: value,
		Err:   err,
	}
}

func (ve *ValueError) Error() string {
	return fmt.Sprintf("value error: %s", ve.Err)
}

...

func validateValue(number int) error {
	if number == 1 {
		return newValueError(number, fmt.Errorf("that's odd"))
	} else if number == 2 {
		return newValueError(number, errUhOh)
	}
	return nil
}

...
```

保存更新的代码并再次运行：

```shell
go run main.go
```

输出看起来像这样：

```
Output
validating 1... there was an error: run error: value error: that's odd
validating 2... there was an error: run error: value error: uh oh
validating 3... valid!
```

你会看到，现在的输出显示错误被包装在 `ValueError` 内，`value error:` 在被包装的错误前。然而，`uh oh` 错误检测又被破坏了，因为 `errUhOh` 现在在两层包装内：`ValueError` 和 `runValidation` 的 `fmt.Errorf` 包装器。代码中只对错误使用了一次 `errors.Unwrap`，所以这导致第一个 `errors.Unwrap(err)` 现在只返回 `*ValueError` 类型的值而不是 `errUhOh`。

修复这个问题的一种方法是更新 `errUhOh` 检查，增加一个额外的错误检查，调用 `errors.Unwrap()` 两次以解开两层包装。请打开 `main.go` 文件并更新 `main` 函数以实现此操作。

> projects/errtutorial/main.go

```shell
...

func main() {
	for num := 1; num <= 3; num++ {
		fmt.Printf("validating %d... ", num)
		err := runValidation(num)
		if err == errUhOh ||
			errors.Unwrap(err) == errUhOh ||
			errors.Unwrap(errors.Unwrap(err)) == errUhOh {
			fmt.Println("oh no!")
		} else if err != nil {
			fmt.Println("there was an error:", err)
		} else {
			fmt.Println("valid!")
		}
	}
}
```

保存文件并再次运行：

```shell
go run main.go
```

输出看起来像这样：

```
Output
validating 1... there was an error: run error: value error: that's odd
validating 2... there was an error: run error: value error: uh oh
validating 3... valid!
```

你会看到，uh oh，`errUhOh` 的特殊错误处理仍然没有如期运行。在验证 `2` 输入的那一行，我们期望看到特殊错误处理 `oh no!` 输出仍然显示默认的  `there was an error: run error: ...` 错误输出。发生这种情况是因为 `errors.Unwrap` 函数不知道如何解包 `ValueError` 自定义错误类型。为了让自定义错误被成功解包，它需要有自己的 `Unwrap` 方法，将内部错误作为 `error` 值返回。先前使用 `fmt.Errorf` 和 `%w` 动词创建错误时，Go 实际上是为你创建了一个已经添加了 `Unwrap` 方法的错误，所以你不需要自己做。不过现在你使用了自定义函数，需要添加你自己的 `Unwrap` 方法。

为了最终解决 `errUhOh` 错误的问题，打开 `main.go` ，给 `ValueError` 添加一个`Unwrap` 方法，返回 `Err` ，即存储内部的被包装错误的字段。

> projects/errtutorial/main.go

```shell
...

func (ve *ValueError) Error() string {
	return fmt.Sprintf("value error: %s", ve.Err)
}

func (ve *ValueError) Unwrap() error {
	return ve.Err
}

...
```

保存新的 `Unwrap` 方法，再次运行程序：

```shell
go run main.go
```

输出看起来像这样：

```
Output
validating 1... there was an error: run error: value error: that's odd
validating 2... oh no!
validating 3... valid!
```

输出显示 `oh no!` 错误处理的 `errUhOh` 错误又如期运行了，因为 `errors.Unwrap` 现在也能解包 `ValueError`。

在本小节中，你创建了一个新的、自定义的 `ValueError` 错误，给你自己或你的用户提供关于验证过程的信息，并将其作为错误信息的一部分。你还给你的 `ValueError` 添加了对错误解包的支持，所以 `errors.Unwrap` 可以访问到被包装的错误。

不过，错误处理变得有点笨拙，难以维护。每次有一个新的包装层，你就必须在错误检查中添加一个 `errors.Unwrap` 方法来处理它。值得庆幸的是，`errors` 包中的 `errors.Is` 和 `errors.As` 函数可以使处理被包装的错误更容易。

## 与被包装的错误打交道

如果你需要为程序的每一个潜在的错误包装层添加一个新的 `errors.Unwrap` 函数调用，它将变得非常长，而且难以维护。出于这个原因，Go 1.13版本中的 `errors` 包还增加了两个函数。这两个函数使你更容易与错误打交道，无论它们在其他错误中被包装得多深，都可以获取得到。`errors.Is` 函数检查一个特定的哨兵错误值是否被包装在某错误中；`errors.As` 函数可以获取某个包装错误内部中的特定错误类型，无论它被包装在哪一层。

### 使用 `errors.Is` 检查错误

使用 `errors.Is`来检查一个特定的错误，使得 `errUhOh` 的特殊错误处理变得更短，因为它处理了所有你需要手动进行的嵌套错误解包。该函数有两个 `error` 类型的参数，第一个是你实际收到的错误，第二个参数是你想验证的目标错误。

为了让 `errUhOh` 错误处理更清晰，请打开你的 `main.go` 文件，更新 `main` 函数中的 `errUhOh` 检查，用 `errors.Is` 代替。

> projects/errtutorial/main.go

```go
...

func main() {
	for num := 1; num <= 3; num++ {
		fmt.Printf("validating %d... ", num)
		err := runValidation(num)
		if errors.Is(err, errUhOh) {
			fmt.Println("oh no!")
		} else if err != nil {
			fmt.Println("there was an error:", err)
		} else {
			fmt.Println("valid!")
		}
	}
}
```

接着，保存并运行：

```shell
go run main.go
```

输出看起来像这样：

```
Output
validating 1... there was an error: run error: value error: that's odd
validating 2... oh no!
validating 3... valid!
```

输出显示了 `oh no!` 错误信息，这意味着即使只有一个对 `errUhOh` 的错误检查，它仍然会在错误链中被发现。`errors.Is` 利用错误类型的 `Unwrap` 方法，不断深入挖掘错误链，直到找到你要找的错误值，在遇到一个哨兵错误，或者 `Unwrap` 方法返回 `nil` 值时，停止检索。

推荐使用 `errors.Is` 检查特定错误，现在错误包装已经成为了 Go 的一个特性。它不仅可以用于你自己的错误值，还可以用于其他错误值，比如本教程前面提到的 `sql.ErrNoRows` 错误。

### 使用 `errors.As` 提取某个类型的错误

Go 1.13中的 `errors` 包添加的最后一个函数是 `errors.As` 函数。当你想获得某种类型的错误的引用以与之进行更详细的交互时，可以使用这个函数。例如，你之前添加的 `ValueError` 自定义错误可以访问错误的 `Value` 字段中正在验证的实际值，但你只有在首先拥有对该错误的引用时才能访问它。这就是 `errors.As` 诞生的原因。你可以传给 `errors.As` 一个错误（类似于 `errors.Is`），以及期望的错误类型对应的变量。然后它将通过错误链查看是否有任何被包装的错误与提供的类型相匹配。如果有匹配的，传递进来的错误类型的变量将被设置为 `errors.As` 找到的错误，并且该函数将返回 `true`。如果没有匹配的错误类型，它将返回 `false`。

通过使用 `errors.As`，现在你可以利用 `ValueError` 类型，在错误处理中显示额外的错误信息。最后一次打开 `main.go` 文件，更新 `main` 函数，为 `ValueError` 类型的错误添加一个新的错误处理案例，打印出 `value error`、无效的数字，和验证错误。

> projects/errtutorial/main.go

```shell
...

func main() {
	for num := 1; num <= 3; num++ {
		fmt.Printf("validating %d... ", num)
		err := runValidation(num)

		var valueErr *ValueError
		if errors.Is(err, errUhOh) {
			fmt.Println("oh no!")
		} else if errors.As(err, &valueErr) {
			fmt.Printf("value error (%d): %v\n", valueErr.Value, valueErr.Err)
		} else if err != nil {
			fmt.Println("there was an error:", err)
		} else {
			fmt.Println("valid!")
		}
	}
}
```

在上面的代码中，你声明了一个新的 `valueErr` 变量，并使用 `errors.As` 来获得对 `ValueError` 的引用（如果它被包装在 `err` 值中）。通过获得 `ValueError` 的引用，你就能访问该类型提供的任何额外字段，例如验证失败的实际值。如果验证逻辑发生在程序的更深处，而你通常不能访问这些值来给用户提示可能出错的地方，这将是有用的。另一个例子是，如果你在做网络编程时遇到 [`net.DNSError`](https://pkg.go.dev/net#DNSError)，通过获得对错误的引用，你能够看到这个错误是由于无法连接导致的错误，还是由于能够连接、但没有找到你的资源而导致的错误。一旦你知道这一点，你就可以用不同的方式来处理这个错误。

保存并运行程序以看看 `errors.As` 的实际效果：

```shell
go run main.go
```

输出看起来像这样：

```
Output
validating 1... value error (1): that's odd
validating 2... oh no!
validating 3... valid!
```

这次你不会在输出中看到默认的 `there was an error: ...` 信息，因为所有的错误都被其他错误处理程序处理了。验证 `1` 的输出表明 `errors.As` 错误检查返回 `true`，因为 `value error ...` 错误信息显示在了输出中。由于 `errors.As` 函数返回 `true`，`valueErr` 变量被设置为 `ValueError`，可以通过访问 `valueErr.Value` 来打印出验证失败的值。

对于值 `2` ，输出结果还表明，即使 `errUhOh` 被包装在 `ValueError` 的包装器内，`oh no！` 特殊错误处理程序仍然被执行。这是因为使用 `errors.Is` 处理 `errUhOh` 的特殊错误处理器在处理错误的 `if` 语句集合中排在第一位。由于这个判断在 `errors.As` 判断之前返回`true`，所以特殊的 `oh no!` 处理程序被执行了。如果你的代码中的 `errors.As` 在 `errors.Is` 之前，`oh no!` 错误信息将变成与 `1` 值相同的 `value error ...`，只是在这种情况下它将打印 `value error (2): uh oh`。

在本节中，你更新了你的程序，使用了 `errors.Is` 函数，删除了大量对 `errors.Unwrap` 的额外调用，使你的错误处理代码更健壮，更适合未来的需要。你还使用了 `errors.As` 函数来检查任何被包装的错误是否是 `ValueError` 类型，如果是，就可以获得该值的所有字段。

## 结论

在本教程中，你使用 `%w` 格式动词包装了一个错误，使用 `errors.Unwrap` 解包了一个错误。你还创建了一个自定义的错误类型，在你自己的代码中支持 `errors.Unwrap`。最后，你使用你的自定义错误类型来探索新的辅助函数 `errors.Is` 和 `errors.As`。

使用这些新的错误函数，可以更容易地在你创建或处理的错误中加入更深层次的信息。它还可以确保你代码中的错误，即使在未来被深入嵌套时，错误检查也能正常运行。

如果你想找到更多关于如何使用新的错误特性的细节，Go 博客有一篇关于 [上手 Go 1.13 的错误处理](https://go.dev/blog/go1.13-errors)  的文章。[`errors`包](https://pkg.go.dev/errors) 的文档也包含了更多的信息。
