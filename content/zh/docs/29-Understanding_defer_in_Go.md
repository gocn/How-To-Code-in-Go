# 了解 Go 中的 defer

## 简介

Go 有许多其他编程语言中常见的控制流关键字，如 `if`、`switch`、`for` 等。有一个关键词在大多数其他编程语言中都没有，那就是  `defer` ，虽然它不太常见，但你很快就会发现它在你的程序中是多么有用。

`defer` 语句的主要用途之一是清理资源，如打开的文件、网络连接和[数据库句柄](https://en.wikipedia.org/wiki/Handle_(computing))。当你的程序使用完这些资源后，关闭它们很重要，以避免耗尽程序的限制，并允许其他程序访问这些资源。`defer` 通过保持关闭文件/资源的调用与打开调用保持一致，使我们的代码更加简洁，不易出错。

在这篇文章中，我们将学习如何正确使用 `defer` 语句来清理资源，以及使用 `defer` 时常犯的几个错误。

## 什么是 `defer` 语句

`defer` 语句将 `defer` 关键字后面的[函数]({{< relref "/docs/27-How_To_Define_and_Call_Functions_in_Go.md" >}})调用添加到一个栈中。当该语句所在的函数返回时，将执行堆栈中所有的函数调用。由于这些调用位于堆栈上，因此将按照后进先出的顺序进行调用。

让我们看看 `defer` 是如何工作的，打印出一些文本：

<center>main.go</center>

```go
package main

import "fmt"

func main() {
        defer fmt.Println("Bye")
        fmt.Println("Hi")
}
```


在 `main` 函数中，我们有两条语句。第一条语句以 `defer` 关键字开始，后面是 `print` 语句，打印出 `Bye`。下一行打印出 `Hi`。

如果我们运行该程序，我们将看到以下输出：

```shell
Hi
Bye
```

请注意，`Hi` 被首先打印出来。这是因为以 `defer` 为前缀的语句直到该函数结束前，都不会被调用。

让我们再看看这个程序，这次我们将添加一些注释来帮助说明正在发生的事情：

<center>main.go</center>

```go
package main

import "fmt"

func main() {
        // defer statement is executed, and places
        // fmt.Println("Bye") on a list to be executed prior to the function returning
        defer fmt.Println("Bye")

        // The next line is executed immediately
        fmt.Println("Hi")

        // fmt.Println*("Bye") is now invoked, as we are at the end of the function scope
}
```


理解 `defer` 的关键是，当 `defer` 语句被执行时，延迟函数的参数被立即评估。当 `defer` 执行时，它把后面的语句放在一个列表中，在函数返回之前被调用。

虽然这段代码说明了 `defer` 的运行顺序，但这并不是编写 Go 程序时的典型使用方式。我们更可能使用 `defer` 来清理资源，例如文件句柄。接下来让我们看看如何做到这一点。

## 使用 `defer` 来清理资源

使用 `defer` 来清理资源在 Go 中是非常常见的。让我们先看看一个将字符串写入文件的程序，但没有使用 `defer` 来处理资源清理的问题：

<center>main.go</center>

```go
package main

import (
        "io"
        "log"
        "os"
)

func main() {
        if err := write("readme.txt", "This is a readme file"); err != nil {
                log.Fatal("failed to write file:", err)
        }
}

func write(fileName string, text string) error {
        file, err := os.Create(fileName)
        if err != nil {
                return err
        }
        _, err = io.WriteString(file, text)
        if err != nil {
                return err
        }
        file.Close()
        return nil
}
```


在这个程序中，有一个叫做 `write` 的函数，它将首先尝试创建一个文件。如果它有错误，它将返回错误并退出函数。接下来，它试图将字符串 `This is a readme file` 写到指定文件中。如果它收到一个错误，它将返回错误并退出该函数。然后，该函数将尝试关闭该文件并将资源释放回系统。最后，该函数返回 `nil` 以表示该函数的执行没有错误。

虽然这段代码可以工作，但有一个细微的错误。如果对 `io.WriteString` 的调用失败，该函数将在没有关闭文件并将资源释放回系统的情况下返回。

我们可以通过添加另一个 `file.Close()` 语句来解决这个问题，在没有 `defer` 的语言中，你可能会这样解决：

<center>main.go</center>

```go
package main

import (
        "io"
        "log"
        "os"
)

func main() {
        if err := write("readme.txt", "This is a readme file"); err != nil {
                log.Fatal("failed to write file:", err)
        }
}

func write(fileName string, text string) error {
        file, err := os.Create(fileName)
        if err != nil {
                return err
        }
        _, err = io.WriteString(file, text)
        if err != nil {
                file.Close()
                return err
        }
        file.Close()
        return nil
}
```

现在，即使调用 `io.WriteString` 失败了，我们仍然会关闭该文件。虽然这是一个相对容易发现和修复的错误，但对于一个更复杂的函数来说，它可能会被遗漏。

我们可以使用 `defer` 语句来确保在执行过程中无论采取何种分支，我们都会调用 `Close()` ，而不是增加对 `file.Close()` 的第二次调用。

下面是使用 `defer` 关键字的版本：

<center>main.go</center>

```go
package main

import (
        "io"
        "log"
        "os"
)

func main() {
        if err := write("readme.txt", "This is a readme file"); err != nil {
                log.Fatal("failed to write file:", err)
        }
}

func write(fileName string, text string) error {
        file, err := os.Create(fileName)
        if err != nil {
                return err
        }
        defer file.Close()
        _, err = io.WriteString(file, text)
        if err != nil {
                return err
        }
        return nil
}
```

这一次我们添加了这行代码，`defer file.Close()`。这告诉编译器，它应该在退出函数 `write` 之前执行 `file.Close`。

现在我们已经确保，即使我们在未来添加更多的代码并创建另一个退出该函数的分支，我们也会一直清理并关闭该文件。

然而，我们通过添加 `defer` 引入了另一个错误。我们不再检查可能从 `Close` 方法返回的潜在错误。这是因为当我们使用 `defer` 时，没有办法将任何返回值传回给我们的函数。

在 Go 中，在不影响程序行为的情况下多次调用 `Close()` 被认为是一种安全和公认的做法。如果 `Close()` 要返回一个错误，它将在第一次被调用时返回。这使得我们可以在函数的成功执行路径中明确地调用它。

让我们看看我们如何既能 `defer` 对 `Close` 的调用，又能在遇到错误时报告错误。

<center>main.go</center>

```go
package main

import (
        "io"
        "log"
        "os"
)

func main() {
        if err := write("readme.txt", "This is a readme file"); err != nil {
                log.Fatal("failed to write file:", err)
        }
}

func write(fileName string, text string) error {
        file, err := os.Create(fileName)
        if err != nil {
                return err
        }
        defer file.Close()
        _, err = io.WriteString(file, text)
        if err != nil {
                return err
        }

        return file.Close()
}
```

这个程序中唯一的变化是最后一行，我们返回 `file.Close()`。如果对 `Close` 的调用导致错误，现在将按照预期返回给调用函数。请记住，我们的 `defer file.Close()` 语句也将在 `return `语句之后运行。这意味着 `file.Close()` 有可能被调用两次。虽然这并不理想，但这是可以接受的做法，因为它不应该对你的程序产生任何副作用。

然而，如果我们在函数的早期收到一个错误，例如当我们调用 `WriteString` 时，函数将返回该错误，并且也将尝试调用 `file.Close`，因为它被推迟了。尽管 `file.Close` 也可能（而且很可能）返回一个错误，但这不再是我们关心的事情，因为我们收到的错误更有可能告诉我们一开始就出了什么问题。

到目前为止，我们已经看到我们如何使用一个 `defer` 来确保我们正确地清理我们的资源。接下来我们将看到如何使用多个 `defer` 语句来清理多个资源。

## 多个 `defer` 语句

在一个函数中拥有多个 `defer` 语句是很正常的。让我们创建一个只有 `defer` 语句的程序，看看当我们引入多个 `defer` 时，会发生什么情况：

<center>main.go</center>

```go
package main

import "fmt"

func main() {
        defer fmt.Println("one")
        defer fmt.Println("two")
        defer fmt.Println("three")
}
```

如果我们运行该程序，我们将收到以下输出结果：

```shell
three
two
one
```

注意，顺序与我们调用 `defer` 语句的顺序相反。这是因为每个被调用的延迟语句都是堆叠在前一个语句之上的，然后在函数退出范围时反向调用（*后进先出*）。

在一个函数中，你可以根据需要有尽可能多的 `defer` 调用，但重要的是要记住它们都将以相反的顺序被调用。

现在我们了解了多个延迟的执行顺序，让我们看看如何使用多个延迟来清理多个资源。我们将创建一个程序，打开一个文件，向其写入内容，然后再次打开，将内容复制到另一个文件。

<center>main.go</center>

```go
package main

import (
        "fmt"
        "io"
        "log"
        "os"
)

func main() {
        if err := write("sample.txt", "This file contains some sample text."); err != nil {
                log.Fatal("failed to create file")
        }

        if err := fileCopy("sample.txt", "sample-copy.txt"); err != nil {
                log.Fatal("failed to copy file: %s")
        }
}

func write(fileName string, text string) error {
        file, err := os.Create(fileName)
        if err != nil {
                return err
        }
        defer file.Close()
        _, err = io.WriteString(file, text)
        if err != nil {
                return err
        }

        return file.Close()
}

func fileCopy(source string, destination string) error {
        src, err := os.Open(source)
        if err != nil {
                return err
        }
        defer src.Close()

        dst, err := os.Create(destination)
        if err != nil {
                return err
        }
        defer dst.Close()

        n, err := io.Copy(dst, src)
        if err != nil {
                return err
        }
        fmt.Printf("Copied %d bytes from %s to %s\n", n, source, destination)

        if err := src.Close(); err != nil {
                return err
        }

        return dst.Close()
}
```


我们添加了一个新的函数，叫做 `fileCopy`。在这个函数中，我们首先打开我们要复制的源文件。我们检查我们是否收到了一个打开文件的错误。如果是的话，我们 `return` 错误并退出该函数。否则，我们 `defer` 关闭我们刚刚打开的源文件。

接下来我们创建目标文件。再次，我们检查我们是否收到了创建文件的错误。如果是的话，我们 `return` 该错误并退出该函数。否则，我们也 `defer` 目标文件的 `Close()`。我们现在有两个 `defer` 函数，当函数退出其作用域时将被调用。

现在我们已经打开了两个文件，我们将`Copy()` 数据从源文件到目标文件。如果成功的话，我们将尝试关闭两个文件。如果我们在试图关闭任何一个文件时收到错误，我们将 `return` 错误并退出函数作用域。

注意，我们为每个文件明确地调用 `Close()`，尽管 `defer` 也将调用 `Close()`。这是为了确保如果关闭文件时出现错误，我们会报告这个错误。这也确保了如果因为任何原因函数提前退出，例如我们在两个文件之间复制失败，每个文件仍将尝试从延迟调用中正确关闭。

## 总结

在这篇文章中，我们了解了 `defer` 语句，以及如何使用它来确保我们在程序中正确清理系统资源。正确地清理系统资源将使你的程序使用更少的内存，表现更好。要了解更多关于 `defer` 的使用，请阅读处理恐慌的文章，或者探索我们整个[如何在 Go 中编码系列](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go)。

