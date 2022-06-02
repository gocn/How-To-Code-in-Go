## 在Go中处理错误

健壮的代码需要对用户的不正确输入、网络连接错误和磁盘错误等意外情况做出正确的反应。错误处理是识别程序处于异常状态并且采取措施去记录供后期调试诊断信息的过程。

相比于其他编程语言, 要求开发者使用专门的语法去处理错误, 在 Go 中将错误作为 `error`(Go 中的一个接口类型) 类型的值, 并且和其他类型的值一样作为函数返回值的一部分返回。要处理 Go 中的错误, 我们必须检查函数返回值中是否包含了错误信息, 并采取合适的措施去保护数据并告知用户或者操作人员发生错误。

## 创建错误

在处理错误之前，我们需要先创建一些错误。标准库提供了两个内置函数来创建错误：`errors.New` 和 `fmt.Errorf`。这两个函数都允许您指定一条自定义错误消息，这些信息可以向用户展示具体错误信息的一部分。

`errors.New` 只提供了一个字符串类型的参数, 用户在使用的时候可以自定义一个错误发生时具体需要展示的错误消息.

尝试运行以下示例以查看由 `errors.New` 创建的错误并打印到标准输出：

```go
package main

import (
	"errors"
	"fmt"
)

func main() {
    // 使用 errors.New() 创建一个错误, 具体的错误消息是: barnacles
	err := errors.New("barnacles")

    // 将错误直接打印到标准错误输出
	fmt.Println("Sammy says:", err)
}

```

```shell
# 这里是控制台的输出
# Output
Sammy says: barnacles
```

我们使用标准库的 `errors.New` 函数创建了具体的消息是 `"barnacles"` 的错误。这里我们遵循了 [Go 程序设计风格指南](https://github.com/golang/go/wiki/CodeReviewComments#error-strings) 使用小写了表示错误消息。

最后，我们使用 `fmt.Println` 函数将我们的错误消息与`"Sammy says:"`相结合并且输出到控制台。

`fmt.Errorf` 函数允许用户构建动态的错误消息。它的第一个参数是一个字符串，包含包含占位符值的错误消息，例如字符串的 `%s` 和整数的`%d`。`fmt.Errorf` 将这个格式化字符串后面的参数按顺序插入到这些占位符中:

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	// 使用 fmt.Errorf() 来构建动态错误信息
	// 错误内容是 error occurred at: %v
	// 其中 %v 的具体内容由 time.Now() 的具体返回值决定
	err := fmt.Errorf("error occurred at: %v", time.Now())

	// 将具体的错误信息 结合 `An error happened:` 打印到控制台
	fmt.Println("An error happened:", err)
}

```

```shell
# 在控制台中输出错误信息
# Output
# 输出内容中的: 2019-07-11 16:52:42.532621 -0400 EDT m=+0.000137103 是由 `time.Now()` 动态生成的
An error happened: error occurred at: 2019-07-11 16:52:42.532621 -0400 EDT m=+0.000137103
```

我们使用 `fmt.Errorf` 函数来构建一个错误消息，该消息将包括当前时间。我们提供给 `fmt.Errorf` 的格式字符串包含 `％v` 格式指令，该指令告诉 `fmt.Errorf` 使用默认格式为格式化字符串后提供的第一个参数。这个参数由标准库的 `time.Now` 函数提供的当前时间。与较早的示例类似，我们将错误消息与简短前缀结合在一起，并使用 `fmt.Println` 函数将结果打印到标准输出。

## 错误处理

一般来说, 你不会看到像上面一样直接创建错误, 然后直接打印。实际上, 在出现问题时, 错误都是由从函数中创建并且返回这种情况更加普遍。调用者使用 `if` 语句判断返回的错误是否为 `nil`(error 非初始化的值) 来判断错误是否存在。

下面这个示例包含了一个总是返回错误的函数, 需要特别留意的时尽管这里的错误是由一个函数返回的, 当你在运行这个程序时, 它产生的输出总是与前面的示例相同。在其他位置声明错误不会改变错误的消息。

``` go
package main

import (
	"errors"
	"fmt"
)

// 定义一个名为: boom 的函数, 返回值总是 errors.New("barnacles")
func boom() error {
	return errors.New("barnacles")
}

func main() {
	// 调用 boom() 函数, 并将返回值赋值给 err 变量
	err := boom()

	// 判断 err 是否等于  nil 
	if err != nil {
		// 如果 err != nil 条件成立, 输出内容, 然后返回 main.main 函数
		fmt.Println("An error occurred:", err)
		return
	}

	// 如果 err == nil 成立, 
	// 将会输出下面这一句
	fmt.Println("Anchors away!")
}

```

```shell
# Output
An error occurred: barnacles
```

这里我们先定义了一个名为 `boom()` 的函数并且总是返回单个使用 `errors.New` 构造 `error` 的函数。然后, 我们通过 `err := boom()` 这行调用 `boom()` 并捕捉错误(赋值给 err 变量即为捕捉错误)。在赋值 error 之后, 我们使用 `if err != nil` 这个条件判断语句来进行判断错误是否存在。因为 `boom()` 函数总是返回有效的 `error` 所以这里的判断条件永远为 `true`。

但是情况并非总是如此(值的是 `boom()` 函数总是返回有效的 `error` 变量), 所以, 最好有逻辑去处理错误不存在和错误存在这两种情况。当错误存在时, 就像上面的示例中一样, 我们使用 `fmt.Println` 和前面的前缀打印错误。最后我们使用 `return` 语句来跳过 `fmt.Println("Anchors away!")` 语句的执行, 因为这个语句只有在 `err == nil` 时才会执行。

> **注意:** 在 Go 中主要采用上一个示例中的 `if err != nil` 来进行错误处理。函数运行到哪里都有可能发生错误, 重要的是使用 `if` 语句来判断错误是否发生。这样, Go 代码通常就具有第一个缩进级别的 [快乐路径](https://en.wikipedia.org/wiki/Happy_path) 的逻辑, 并且所有的 "悲伤的路径" 在第二个缩进。

`if` 语句有一个可选的赋值子句，可以用来帮助压缩函数调用和错误处理。

运行下一个程序，查看与前面示例相同的输出，但这一次使用复合 `if` 语句来减少一些重复的代码:

``` go
package main

import (
	"errors"
	"fmt"
)

func boom() error {
	return errors.New("barnacles")
}

func main() {
	// 将 err 变量的赋值和判断都压缩在一个语句块中执行
	if err := boom(); err != nil {
		fmt.Println("An error occurred:", err)
		return
	}
	fmt.Println("Anchors away!")
}
```

```shell
#Output
An error occurred: barnacles
```

和之前的示例一样, 我们定义一个 `boom()` 总是返回错误的函数。我们将从 `boom()` 返回的错误赋值给 `err` 作为 `if` 语句的一部分。在 `if` 语句的第二部分语句中, `err` 变量变得可用。我们检查错误是否存在, 然后像以前一样使用一个简短的前缀字符串打印我们的错误。

在本节中，我们学习了如何处理只返回错误的函数。这些函数很常见，但是能够处理可能返回多个值的函数的错误也很重要。

## 同时返回错误和多个值

返回单个值的函数通常是影响某些状态更改的函数。 比如将行数据插入到数据库中。通常还会编写这样的函数: 如果成功则返回一个值, 如果失败则返回一个潜在的错误。Go 允许函数返回多个结果, 可以用来同时返回一个值和一个错误类型。

为了创建一个返回多个值的函数, 我们需要在函数签名的括号中列出返回值类型。例如, 一个 `capitalize` 函数返回值类型是 `string` 和 `error`, 那么我们可以这么声明 `func capitalize(name string)(string, error){}`。其中 `(string, error)` 这一块的语法是告诉 Go 的编译器, 函数会按照 `string` 和 `error` 这一顺序返回值。

运行下面的程序并且查看函数返回的 `string` 和 `error`:

``` go
package main

import (
	"errors"
	"fmt"
	"strings"
)

func capitalize(name string) (string, error) {
	if name == "" {
		return "", errors.New("no name provided")
	}
	return strings.ToTitle(name), nil
}

func main() {
	name, err := capitalize("sammy")
	if err != nil {
		fmt.Println("Could not capitalize:", err)
		return
	}

	fmt.Println("Capitalized name:", name)
}

```

```shell
# Output
Capitalized name: SAMMY
```

我们定义了 `capitalize()` 函数, 这个函数需要传递一个字符串作为参数(完成将字符串的转为大写)并返回字符串和错误。在 `main()` 函数中, 我们调用 `capitalize()`, 然后在 `:=` 运算符的左边将函数的返回值赋值给 `name` 和 `err` 这两个变量。之后, 我们执行 `if err != nil` 检查错误, 如果存在错误, 使用 `fmt.Prtintln` 将错误信息打印到标准输出。如果没有错误, 输出 `Capitalized name: SAMMY`。

如果将 `err := capitalize("sammy")` 中的 `"sammy"` 更改为为空字符串 `("")`，你将收到 `Could not capitalize: no name provided` 这个错误。

当函数的调用者为 `name` 参数提供一个空字符串时， `capitalize` 函数将返回错误。当 `name` 参数不是空字符串时，`capledize()` 调用 `strings.ToTitle` 函数将 `name` 参数转为大写并返回为 `nil` 的错误值。

这个例子遵循一些微妙的规约，这些规约是 Go 代码的典型特征，但 GO 编译器并没有强制执行。当函数返回多个值（包括错误）时，规约我们将 `error` 类型作为最后一项。具有多个返回值的函数返回错误时，通常约定 GO 代码还将每个不是 `error` 类型的值设置为零值。比如字符串的零值空字符串，整数为 0，一个用于结构类型的空结构，以及用 `nil` 表示接口和指针类型的零值。我们在有关 [变量和常数的教程](https://www.digitalocean.com/community/tutorials/how-to-use-variables-and-constants-in-go#zero-values) 中更详细地介绍零值。

### 简化重复的代码

如果函数有多个返回值时，遵守这些约定可能会变得啰嗦。我们可以使用 [匿名函数](https://en.wikipedia.org/wiki/Anonymous_function) 来帮助减少重复的代码。匿名函数是分配变量的过程。与我们在较早的示例中定义的函数相反，它们仅在你声明它们的函数中可用 - 这使其非常适合用作可重复使用的 `helper` 逻辑代码片段。

以下程序是修改了最后一个示例，返回值增加了一个类型, 包括大写的名称的长度。由于它具有三个值可以返回的值，因此如果没有匿名函数来帮助我们，处理错误可能会变得麻烦:

``` go
package main

import (
	"errors"
	"fmt"
	"strings"
)

func capitalize(name string) (string, int, error) {
	handle := func(err error) (string, int, error) {
		return "", 0, err
	}

	if name == "" {
		return handle(errors.New("no name provided"))
	}

	return strings.ToTitle(name), len(name), nil
}

func main() {
	name, size, err := capitalize("sammy")
	if err != nil {
		fmt.Println("An error occurred:", err)
	}

	fmt.Printf("Capitalized name: %s, length: %d", name, size)
}

```

```shell
# Output
Capitalized name: SAMMY, length: 5
```

在 `main()` 中，我们现在可以从 `capitalize()` 函数中获取转为大写的 `name`，`size` 和 `err` 这三个返回的参数。然后，我们检查是否通过检查错误变量不等于 `nil`。在尝试使用 `capitalize()` 返回的任何其他值之前，这一点很重要，因为匿名函数可以将它们设置为零值。由于我们提供了字符串 `"Sammy"`，因此没有发生错误，因此我们打印出转为大写之后的名称及其长度。

再次，你可以尝试将 `"Sammy"` 更改为空字符串 `("")` 以查看已打印的错误情况 (`An error occurred: no name provided`)。

在 `capitalize` 函数中，我们将 `handle` 变量定义为匿名函数。它需要传递要给错误类型的参数，并以与 `capitalize` 函数的返回值相同的顺序返回相同的值。`handle` 将这些值设置为零值，并将其作为最终返回值作为参数传递的错误转发。然后，使用 `err` 作为 `handle` 的参数，就可以返回在 `capitalize` 中遇到的任何错误。

请记住，`capitalize` 必须一直返回三个值，因为这就是我们定义函数的方式。有时我们不想处理函数可能返回的所有值。幸运的是，我们在赋值并如何使用这些值方面具有一定的灵活性。

### 处理多回报功能的错误

当函数返回许多值时，Go 要求我们将每个值分配给变量。在最后一个示例中，我们通过提供从 `capitalize` 函数返回的两个值的名称来做到这一点。这些名称应通过逗号分隔，并出现在 `:=` 操作符的左侧。从 `capitalize` 返回的第一个值将分配给 `name` 变量，第二个值(`error`)将分配给 `err` 这个变量。有时，我们只对错误值感兴趣。您可以丢弃使用特殊 `_` 变量名称返回功能的任何不需要值。

在以下程序中，我们修改了涉及大写功能的第一个示例，以通过传递空字符串 `("")` 来产生错误。尝试运行此程序，以查看我们如何通过使用 `_` 变量丢弃第一个返回的值来检查错误：

``` go
package main

import (
	"errors"
	"fmt"
	"strings"
)

func capitalize(name string) (string, error) {
	if name == "" {
		return "", errors.New("no name provided")
	}
	return strings.ToTitle(name), nil
}

func main() {
	_, err := capitalize("")
	if err != nil {
		fmt.Println("Could not capitalize:", err)
		return
	}
	fmt.Println("Success!")
}

```

```shell
# Output
Could not capitalize: no name provided
```

这次在 `main()` 函数中，我们将 `capitalize` 的第一个返回值 (首先返回的字符串) 分配给下划线变量(`_`)。同时，我们分配了通过  `capitalize` 返回的 `err` 变量返回的错误。然后，我们通过 `if err != nil` 条件判断错误是否存在。由于我们已经对一个空字符串进行了硬编码，作为在行中大写的参数，`_, err := capitalize("")`，因此该条件始终将评估为 `true`。这会产生输出`"Could not capitalize: no name provided"`，该输出由 `fmt.Println` 函数在 `if` 语句的正文中打印出来。此后的返回将跳过 `fmt.Println("Success!")`。

## 结论

我们已经看到了许多使用标准库创建错误的方法，以及如何构建以惯用方式返回错误的函数。在本教程中，我们设法使用标准库的 `errors.New` 和 `fmt.Errorf` 函数成功地创建了各种错误。在将来的教程中，我们将研究如何创建自己的自定义错误类型，以向用户传达更丰富的信息。
