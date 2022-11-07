# 介绍 Go 中的 Strings 包

## 介绍

Go 的 [`strings`](https://golang.org/pkg/strings/) 包有几个函数可用于 [string 数据类型](https://gocn.github.io/How-To-Code-in-Go/docs/08-An_Introduction_to_Working_with_Strings_in_Go)。这些函数可以轻松地修改和操作字符串。我们可以将函数视为对代码元素执行的操作。内置函数是那些在 Go 编程语言中定义并且可供我们随时使用的函数。

在本教程中，我们将回顾几个可用于在 Go 中处理字符串的不同函数。

## 字符串大写和小写

函数 `strings.ToUpper` 和 `strings.ToLower` 将返回一个字符串，其中原始字符串的所有字母都转换为大写或小写字母。 因为字符串是不可变的数据类型，所以返回的字符串将是一个新字符串。 字符串中的任何非字母字符都不会更改。

要将字符串 `"Sammy Shark"` 转换为全大写，你可以使用 `strings.ToUpper` 函数：

```go
ss := "Sammy Shark"
fmt.Println(strings.ToUpper(ss))
```

```go
Output
SAMMY SHARK
```

要转换为小写：

```go
fmt.Println(strings.ToLower(ss))
```

```go
Output
sammy shark
```

由于你使用的是 `strings` 包，因此首先需要将其导入程序中。 要将字符串转换为大写和小写，整个程序如下：

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	ss := "Sammy Shark"
	fmt.Println(strings.ToUpper(ss))
	fmt.Println(strings.ToLower(ss))
}
```

`strings.ToUpper` 和 `strings.ToLower` 函数使大小写始终保持一致，更容易评估和比较字符串。 例如，如果用户的姓名全小写，我们仍然可以通过检查全大写版本来确定他们的姓名是否在我们的数据库中。

## 字符串搜索函数

`strings` 包有许多函数可以帮助确定字符串是否包含特定的字符。

| 函数                | 用法                   |
| ------------------- | ---------------------- |
| `strings.HasPrefix` | 从头开始搜索字符串     |
| `strings.HasSuffix` | 从末尾开始搜索字符串   |
| `strings.Contains`  | 搜索字符串中的任何位置 |
| `strings.Count`     | 计算字符串出现的次数   |

`strings.HasPrefix` 和 `strings.HasSuffix` 允许你检查字符串是否以特定字符集开头或结尾。

例如，要检查字符串 `"Sammy Shark"` 是否以 `Sammy` 开头并以 `Shark` 结尾：

```go
ss := "Sammy Shark"
fmt.Println(strings.HasPrefix(ss, "Sammy"))
fmt.Println(strings.HasSuffix(ss, "Shark"))
```

```go
Output
true
true
```

你将使用 `strings.Contains` 函数来检查 `"Sammy Shark"` 是否包含字符 `Sh`：

```go
fmt.Println(strings.Contains(ss, "Sh"))
```

```go
Output
true
```

最后，看看 “Sammy Shark” 这个字符串中出现了多少次字母 “S”：

```go
fmt.Println(strings.Count(ss, "S"))
```

```go
Output
2
```

**注意：** Go 中的所有字符串都区分大小写。 这意味着 `Sammy` 与 `sammy` 不同。

计算小写的 `s` 在 `Sammy Shark` 中出现的次数与使用大写的 `S` 计算结果并不同：

```go
fmt.Println(strings.Count(ss, "s"))
```

```go
Output
0
```

因为 `S` 与 `s` 不同，所以返回的计数将为 `0`。

当你想在程序中比较或搜索字符串时，字符串函数很有用。

## 确定字符串长度

内置函数 `len()` 返回字符串中的字符数。 当你需要强制最小或最大密码长度，或将较大的字符串截断以在特定限制内用作缩写时，此功能很有用。

为了演示这个功能，下面我们将得到一个长字符串的长度：

```go
import (
	"fmt"
	"strings"
)

func main() {
        openSource := "Sammy contributes to open source."
        fmt.Println(len(openSource))
}
```

```go
Output
33
```

我们将变量 `openSource` 设置为字符串 `"Sammy contributes to open source."`，然后使用 `len(openSource)` 将该变量传递给 `len()` 函数。 最后，我们将函数传递给 `fmt.Println()` 函数，以便我们可以在屏幕上看到程序的输出。

请记住，`len()` 函数将计算由双引号绑定的任何字符——包括字母、数字、空白字符和符号。

## 字符串操作函数

`strings.Join`、`strings.Split` 和 `strings.ReplaceAll` 函数是在 Go 中操作字符串的一些额外方法。

`strings.Join` 函数用于将一组字符串组合成一个新的字符串。

要从字符串切片创建逗号分隔的字符串，我们将按以下方式使用此函数：

```go
fmt.Println(strings.Join([]string{"sharks", "crustaceans", "plankton"}, ","))
```

```go
Output
sharks,crustaceans,plankton
```

如果我们想在我们的新字符串中的字符串值之间添加一个逗号和一个空格，我们可以简单地用逗号后的空格重写我们的表达式：`strings.Join([]string{"sharks", "crustaceans", "plankton "}, ", ")`。

就像我们可以将字符串连接在一起一样，我们也可以拆分字符串。 为此，我们可以使用 `strings.Split` 函数并拆分空格：

```go
balloon := "Sammy has a balloon."
s := strings.Split(balloon, " ")
fmt.Println(s)
```

```go
Output
[Sammy has a balloon]
```

输出是一段字符串。 由于使用了`strings.Println`，因此很难通过肉眼来判断输出的是什么类型。 要查看它确实是一段字符串，请使用 `fmt.Printf` 函数和 `%q` 来格式化输出字符串：

```go
fmt.Printf("%q", s)
```

```go
Output
["Sammy" "has" "a" "balloon."]
```

除了 `strings.Split` 之外，另一个有用的函数是 `strings.Fields`。 不同之处在于 `strings.Fields` 将忽略所有空格，并且只会在字符串中拆分出实际的 `fields`：

```go
data := "  username password     email  date"
fields := strings.Fields(data)
fmt.Printf("%q", fields)
```

```go
Output
["username" "password" "email" "date"]
```

`strings.ReplaceAll` 函数可以将原始字符串进行一些替换，并返回更新后的字符串。

假设 Sammy 的气球丢失了。 由于 Sammy 不再有这个气球，我们将在新字符串中将子字符串 `"has"` 从原始字符串中更改为 `"had"`：

```go
fmt.Println(strings.ReplaceAll(balloon, "has", "had"))
```

上面的函数参数，首先是 `balloon` ，用于存储原始字符串的变量； 第二个子字符串“has”是我们要替换的，第三个子字符串“had”是我们要替换第二个子字符串的。 当我们将其合并到程序中时，我们的输出将如下所示：

```go
Output
Sammy had a balloon.
```

使用字符串函数 `strings.Join`、`strings.Split` 和 `strings.ReplaceAll` 将为你在 Go 中操作字符串提供更好的帮助。

## 结论

本教程介绍了一些用于字符串数据类型的常见字符串包函数，你可以使用这些函数在 Go 程序中处理和操作字符串。

你可以在 [理解 Go 的数据类型](https://gocn.github.io/How-To-Code-in-Go/docs/07-Understanding_Data_Types_in_Go) 中了解有关其他数据类型的更多信息，并在 [Go 中处理字符串的介绍](https://gocn.github.io/How-To-Code-in-Go/docs/08-An_Introduction_to_Working_with_Strings_in_Go) 了解更多有关字符串的信息。

------
