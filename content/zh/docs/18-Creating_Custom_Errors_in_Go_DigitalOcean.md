# 在 Go 中创建自定义错误

## 介绍

GO 标准库提供了[`errors.New` and `fmt.Errorf`](https://www.digitalocean.com/community/tutorials/handling-errors-in-go#creating-errors) 这两种方法来在创建错误。但是这两种方法并不能满足你的用户或者后期调试时提供更加复杂的错误信息或者报告发生了什么。为了传递这种更复杂的错误信息并获得更多功能，我们可以实现标准库 `error` 接口类型。

`error` 接口定义如下：

```go
type error interface {
  Error() string
}

```

[`内置`](https://golang.org/pkg/builtin/) 软件包将 `error` 定义为具有单个 `Error()` 方法的接口，该接口将错误消息字符串作为返回。通过实现此方法，我们可以将定义的任何类型转换为自己的错误。

让我们尝试运行以下示例以查看 `error` 接口的实现：

```go
package main

import (
	"fmt"
	"os"
)

// 定义一个 MyError 的接口体
type MyError struct{}

// 实现 error 接口的 Error 方法
func (m *MyError) Error() string {
	return "boom"
}

// 定义 sayHello 函数
// 函数返回类型为 string 和 error
func sayHello() (string, error) {
    // 由于 *MyError 实现了 error 接口
    // 所以 &MyError{} 可以作为 error 对象返回
	return "", &MyError{}
}

func main() {
	s, err := sayHello()
	if err != nil {
		fmt.Println("unexpected error: err:", err)
		os.Exit(1)
	}
	fmt.Println("The string:", s)
}

```

我们将看到以下输出：

```shell
# Output
unexpected error: err: boom
exit status 1
```

在这里，我们创建了一个新的空结构类型 `MyError`，并在其上定义了 `Error()` 方法。`Error()` 方法返回字符串 `"Boom"`。

在 `main()` 中，我们调用 `sayhello` 函数，该函数返回一个空字符串和一个新 `MyError` 实例。由于 `sayhello` 将始终返回错误，因此在`main()` 中的 `if` 语句主体内的 `fmt.Println` 调用将始终执行。我们使用 `fmt.Println` 来打印短前缀字符串 `"unexpected error:"`以及在保存`MyError` 实例中的在 `err` 变量。

> 值得注意的是，我们不必直接调用 `Error()`，因为 `fmt` 包能够自动检测到已经实现了 `error` 接口。它 [透明](<https://en.wikipedia.org/wiki/Transparency_(human%E2%80%93computer_interaction)>) 地调用 `Error()` 来获取字符串 `"hoom"`，并将其与前缀字符串 `"unexpected error: err:"` 相连。

## 自定义错误收集详细信息

有时，自定义错误是捕获详细错误信息的最有效的方式。例如，假设我们要捕获 HTTP 请求产生的错误的状态代码；运行以下程序以查看 `error` 的实现，使我们能够清晰捕获该信息：

```go
package main

import (
	"errors"
	"fmt"
	"os"
)

type RequestError struct {
	StatusCode int

	Err error
}

func (r *RequestError) Error() string {
	return fmt.Sprintf("status %d: err %v", r.StatusCode, r.Err)
}

func doRequest() error {
	return &RequestError{
		StatusCode: 503,
		Err:        errors.New("unavailable"),
	}
}

func main() {
	err := doRequest()
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
	fmt.Println("success!")
}

```

我们将看到以下输出：

```shell
# Output
status 503: err unavailable
exit status 1
```

在此示例中，我们创建了创建一个错误的 `RequestError` 的新实例, 其中包含一个状态码和使用标准库提供的 `errors.New` 函数创建的 `err`。之后，如前所述，我们使用 `fmt.Println` 打印了错误信息。

在 `RequestError` 的 `Error()` 方法中，我们使用创建 `error` 对象时提供的信息和 `fmt.Sprintf` 函数构造字符串。

## 类型断言和自定义错误

`error` 接口仅公开一种方法，但是为了正确处理错误, 我们可能需要访问 `error` 实现类型的其他方法。例如，我们可能有几个暂时的自定义错误实现，可以通过 `Temporary()` 方法的存在来重述。

接口为类型提供的更广泛的方法集提中供了一个狭窄的视图，因此，我们必须使用类型断言来更改视图正在显示的方法，或完全删除它。

下面的示例增加了前面显示的 `RequestError` 具有 `Temporary()` 方法，该方法将指示调用者是否应重试请求：

```go
package main

import (
	"errors"
	"fmt"
	"net/http"
	"os"
)

type RequestError struct {
	StatusCode int

	Err error
}

func (r *RequestError) Error() string {
	return r.Err.Error()
}

func (r *RequestError) Temporary() bool {
    // 如果状态码是 503 返回 true
	return r.StatusCode == http.StatusServiceUnavailable // 503
}

func doRequest() error {
	return &RequestError{
		StatusCode: 503,
		Err:        errors.New("unavailable"),
	}
}

func main() {
	err := doRequest()
	if err != nil {
		fmt.Println(err)
        // 进行类型断言
		re, ok := err.(*RequestError)
        // 如果类型断言成功
		if ok {
            
			if re.Temporary() {
				fmt.Println("This request can be tried again")
			} else {
				fmt.Println("This request cannot be tried again")
			}
		}
		os.Exit(1)
	}

	fmt.Println("success!")
}

```

我们将看到以下输出：

```shell
# Output
unavailable
This request can be tried again
exit status 1
```

在 `main()` 中，我们调用 `doRequest()` 将错误接口返回给我们。我们首先打印由 `Error()` 方法返回的错误消息。接下来，我们尝试通过使用类型的断言 `re, ok := err.(*RequestError)`。如果类型断言成功，我们然后使用 `Temporary()` 方法来查看此错误是否是临时错误。由于`doRequest()` 设置的状态代码为 `503`，它匹配 `HTTP.Statusserviceunavailable`，因此将返回 `true`，并且要打印`"This request can be tried again"` 的原因。实际上，我们将提出另一个请求，而不是打印消息。

## 包装错误

通常，错误是从程序的外部产生(例如：数据库，网络连接等)。这些错误提供的错误消息不能够帮助任何人找到错误的根源。有必要在错误消息开始时，将错误与额外信息包装，将为成功调试提供一些必要的上下文。

下面的示例说明了我们如何将一些上下文信息附加到从其他功能中返回的其他隐性错误：

```go
package main

import (
	"errors"
	"fmt"
)

// 定义一个错误的包装类型
type WrappedError struct {
    // 上下文信息
	Context string
    // 具体错误内容
	Err     error
}

func (w *WrappedError) Error() string {
	return fmt.Sprintf("%s: %v", w.Context, w.Err)
}

func Wrap(err error, info string) *WrappedError {
	return &WrappedError{
		Context: info,
		Err:     err,
	}
}

func main() {
	err := errors.New("boom!")
	err = Wrap(err, "main")

	fmt.Println(err)
}

```

我们将看到以下输出

```shell
# Output
main: boom!
```

`WrappedError` 是一个具有两个字段的结构：字符串类型的 `context` 字段和 `error`, 这让 `WrappedError` 提供了更多信息。当调用 `Error()` 方法时，我们再次使用 `fmt.Sprintf` 打印上下文消息和 `error`(`fmt.Sprintf` 也会隐式调用 `err` 的 `Error()` 方法)。

在 `main()` 中，我们使用 `errors.New` 创建一个错误，然后我们使用定义的 `Wrap` 函数包装该错误。这使我们可以指出此错误是在 `"main"` 中生成的。另外，由于我们的 `WrappedError` 也是一个 `error`，因此我们也可以包装其它的`WrappedError` - 这将使我们看到链条来帮助我们追踪错误源。在标准库的一点帮助下，我们甚至可以在错误中嵌入完整的堆栈跟踪。

## 总结

由于 `error` 接口只提供一种方法，我们已经看到，在为不同情况提供不同类型的错误方面，我们有很大的灵活性。这可以包含所有内容，从传达多个信息作为错误的一部分到实现 [指数退回](https://en.wikipedia.org/wiki/Exponential_backoff)。尽管表面上的错误处理机制似乎很简单，但我们可以使用这些自定义错误来处理常见和不常见情况。

GO 有另一种传达意外行为的机制，panic。在错误处理系列的下一篇文章中，我们将检查恐慌 - 它们是什么以及如何处理它们。
