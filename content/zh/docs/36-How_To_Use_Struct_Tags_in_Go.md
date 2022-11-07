# 如何在 Go 中使用结构体标签

## 简介

结构，或称结构体，被用来将多个信息聚合在一个单元中。这些[信息集合](https://gocn.github.io/How-To-Code-in-Go/docs/33-Defining_Structs_in_Go)被用来描述更高层次的概念，例如由 `Street`、`City`、`State` 和 `PostalCode` 组成的 `Address`。当你从数据库或 API 等系统中读取这些信息时，你可以使用结构体标签来控制这些信息如何被分配到结构体的字段中。结构体标签是附加在结构体字段上的小块元数据，为与该结构体一起工作的其他 Go 代码提供指示。

## 结构体标签是怎么样的

Go 结构体标签是出现在 Go 结构声明中类型后的注释，每个标签都由与一些相应的值相关的短字符串组成。

一个结构体的标签看起来像这样，标签的偏移量为 ` 字符：

```go
type User struct {
	Name string `example:"name"`
}
```

其他 Go 代码就能够检查这些结构并提取分配给它所要求的特定键的值。如果没有其他代码对其进行检查，结构体标签对你的代码运行没有任何影响。

试试这个例子，看看结构体标签是怎么样的，如果没有来自另一个包的代码，它们将没有任何作用。

```go
package main

import "fmt"

type User struct {
	Name string `example:"name"`
}

func (u *User) String() string {
	return fmt.Sprintf("Hi! My name is %s", u.Name)
}

func main() {
	u := &User{
		Name: "Sammy",
	}

	fmt.Println(u)
}
```

这将输出：

```bash
Output
Hi! My name is Sammy
```

这个例子定义了一个带有 `Name` 字段的 `User` 类型。`Name` 字段被赋了一个结构体标签 `example: "name"`。我们把这个特定的标签称为 “example 结构体标签”，因为它使用 “example”这个词作为它的键。`example` 结构体标签的 `Name` 字段值是 `"name"`。在 `User` 类型中，我们还定义了 `fmt.Stringer` 接口要求的 `String()` 方法。当我们将该类型传递给 `fmt.Println` 时，该方法将被自动调用，并使我们可以生成一个格式化的结构体版本。

在 `main` 方法中，我们创建了一个新的 `User` 类型实例，并将其传递给 `fmt.Println`。尽管这个结构体有一个结构体标签，但我们可以看到它对这个 Go 代码的操作没有影响。如果不存在结构标签，它的行为也会完全一样。

要使用结构标签来完成某些事，必须编写其他 Go 代码在运行时检查结构体。标准库中有一些包将结构体标签作为其操作的一部分，其中最受欢迎的是 `encoding/json` 包。

## 编码 JSON

JavaScript 对象符号（JSON）是一种文本格式，用于编码根据不同字符串键组织的数据集合。它通常用于不同程序之间的数据通信，因为这种格式足够简单，以至于许多不同的语言都有库对其进行解码，下面是一个 JSON 的例子：

```json
{
  "language": "Go",
  "mascot": "Gopher"
}
```

这个 JSON 对象包含两个键，`language` 和 `mascot`。这些键后面是相关的值，`language` 键的值为 `Go`，`mascot` 则为 `Gopher`。

标准库中的 JSON 编码器利用结构体标签作为注解，向编码器表明你想在 JSON 输出中如何命名你的字段。这些 JSON 编码和解码机制可以在 `encoding/json` [包](https://godoc.org/encoding/json)中找到。

试试这个例子，看看没有结构体标签的 JSON 是如何编码的：

```go
package main

import (
	"encoding/json"
	"fmt"
	"log"
	"os"
	"time"
)

type User struct {
	Name          string
	Password      string
	PreferredFish []string
	CreatedAt     time.Time
}

func main() {
	u := &User{
		Name:      "Sammy the Shark",
		Password:  "fisharegreat",
		CreatedAt: time.Now(),
	}

	out, err := json.MarshalIndent(u, "", "  ")
	if err != nil {
		log.Println(err)
		os.Exit(1)
	}

	fmt.Println(string(out))
}
```

这将打印以下输出：

```bash
Output
{
  "Name": "Sammy the Shark",
  "Password": "fisharegreat",
  "CreatedAt": "2019-09-23T15:50:01.203059-04:00"
}
```

我们定义了一个描述用户的结构，其字段包括用户的姓名、密码和用户的创建时间。在 `main` 方法中，我们为所有字段提供了值，除了 `PreferredFish`（Sammy 喜欢所有的鱼），从而创建了这个用户的实例。然后我们把 `User` 实例传递给 `json.MarshalIndent`  方法。这样我们可以更容易地看到 JSON 的输出，而不需要使用外部格式化工具。这个调用可以用 `json.Marshal(u)` 代替，以接收没有任何额外空白的 JSON。`json.MarshalIndent` 的两个额外参数控制输出的前缀（我们用空字符串省略了），以及缩进使用的字符，这里是两个空格字符。任何由 `json.MarshalIndent` 产生的错误都会被记录下来，程序使用 `os.Exit(1)` 终止。最后，我们将从 `json.MarshalIndent` 返回的 `[]byte` 转换为 `string`，并将生成的字符串交给 `fmt.Println` 处理以便在终端打印。

该结构的字段完全按照我们的命名出现。这不是您所期望的典型的 JSON 风格，它使用了字段名的骆驼字母大小写。在接下来的例子中，你将改变字段的名称，使其遵循骆驼大写的风格。正如你在运行这个例子时看到的，这不会起作用，因为想要的字段名与 Go 导出字段名的规则相冲突。

```go
package main

import (
	"encoding/json"
	"fmt"
	"log"
	"os"
	"time"
)

type User struct {
	name          string
	password      string
	preferredFish []string
	createdAt     time.Time
}

func main() {
	u := &User{
		name:      "Sammy the Shark",
		password:  "fisharegreat",
		createdAt: time.Now(),
	}

	out, err := json.MarshalIndent(u, "", "  ")
	if err != nil {
		log.Println(err)
		os.Exit(1)
	}

	fmt.Println(string(out))
}
```

这将呈现以下输出：

```bash
Output
{}
```

在这个版本中，我们把字段的名字改成了驼峰式。现在 `Name` 是 `name`，`Password` 是 `password`，最后 `CreatedAt` 是 `createdAt`。在 `main` 方法中，我们改变了结构的实例化，以使用这些新的名称。然后我们像以前一样将结构体传递给 `json.MarshalIndent` 函数。这次输出是一个空的 JSON 对象，`{}`。

骆驼对字段的正确命名要求第一个字符必须是小写的。虽然 JSON 并不关心你如何命名你的字段，但 Go 关心，因为它表示字段在包外的可见性。由于 `encoding/json` 包与我们使用的 `main` 包是互相独立的，我们必须将第一个字符大写，以使其对 `encoding/json` 可见。看来我们陷入了僵局，我们需要一些方法来向 JSON 编码器传达我们希望这个字段被命名成什么。

## 使用结构体标签来控制编码

你可以修改前面的例子，通过给每个字段注解一个结构体标签，使导出的字段用驼峰大写的字段名进行正确编码。`encoding/json` 识别的结构体标签有一个 `json` 的键和一个控制输出的值。通过将字段名的驼峰版本作为 `json` 键的值，编码器将使用该名称代替。这个例子修正了前两次的尝试：

```go
package main

import (
	"encoding/json"
	"fmt"
	"log"
	"os"
	"time"
)

type User struct {
	Name          string    `json:"name"`
	Password      string    `json:"password"`
	PreferredFish []string  `json:"preferredFish"`
	CreatedAt     time.Time `json:"createdAt"`
}

func main() {
	u := &User{
		Name:      "Sammy the Shark",
		Password:  "fisharegreat",
		CreatedAt: time.Now(),
	}

	out, err := json.MarshalIndent(u, "", "  ")
	if err != nil {
		log.Println(err)
		os.Exit(1)
	}

	fmt.Println(string(out))
}
```

这将输出：

```bash
Output
{
  "name": "Sammy the Shark",
  "password": "fisharegreat",
  "preferredFish": null,
  "createdAt": "2019-09-23T18:16:17.57739-04:00"
}
```

我们把字段名改回来了，通过把名字的第一个字母大写来让其他包看到。然而，这次我们以 `json: "name"` 的形式添加了结构体标签，其中 `"name"` 是我们希望 `json.MarshalIndent` 将结构体打印成 JSON 时使用的名称。

我们现在已经成功地正确格式化了我们的 JSON。然而，请注意，一些我们没有设置值的字段也被打印了出来。如果你愿意，JSON 编码器也可以省略这些字段。

## 删除空的 JSON 字段

最常见的是，我们想省略输出 JSON 中未设置的字段。由于 Go 中的所有类型都有一个“零值”，即它们被设置成的一些默认值，`encoding/json` 包需要额外的信息，以便能够告诉某些字段在赋这个零值时应该被视为未设置。在任何 `json` 结构体标签的值部分，你可以在你的字段的所需名称后面加上 `,omitempty` 来告诉 JSON 编码器，当字段被设置为零值时，省略这个字段的输出。下面的例子修正了之前的例子，不再输出空字段：

```go
package main

import (
	"encoding/json"
	"fmt"
	"log"
	"os"
	"time"
)

type User struct {
	Name          string    `json:"name"`
	Password      string    `json:"password"`
	PreferredFish []string  `json:"preferredFish,omitempty"`
	CreatedAt     time.Time `json:"createdAt"`
}

func main() {
	u := &User{
		Name:      "Sammy the Shark",
		Password:  "fisharegreat",
		CreatedAt: time.Now(),
	}

	out, err := json.MarshalIndent(u, "", "  ")
	if err != nil {
		log.Println(err)
		os.Exit(1)
	}

	fmt.Println(string(out))
}
```

这个例子将输出：

```bash
Output
{
  "name": "Sammy the Shark",
  "password": "fisharegreat",
  "createdAt": "2019-09-23T18:21:53.863846-04:00"
}
```

我们修改了前面的例子，使 `PreferredFish` 字段现在有结构体标签 `json:"preferredFish,omitempty"`。`,omitempty` 的存在使 JSON 编码器跳过该字段，因为我们决定不设置它。在我们以前的例子的输出中，它的值是 `null`。

这个输出看起来好多了，但我们仍然打印出了用户的密码。`encoding/json` 包提供了另一种方法，让我们完全忽略私有字段。

## 忽略私有字段

有些字段必须从结构体中导出，以便其他包可以正确地与该类型交互。然而，这些字段的性质可能是敏感的，所以在这些情况下，即使它被设置了值，我们仍希望 JSON 编码器能够完全忽略该字段。这可以用特殊值 `"-"` 作为 `json:` 结构体标签的值参数来实现。

这个例子修正了暴露用户密码的问题。

```go
package main

import (
	"encoding/json"
	"fmt"
	"log"
	"os"
	"time"
)

type User struct {
	Name      string    `json:"name"`
	Password  string    `json:"-"`
	CreatedAt time.Time `json:"createdAt"`
}

func main() {
	u := &User{
		Name:      "Sammy the Shark",
		Password:  "fisharegreat",
		CreatedAt: time.Now(),
	}

	out, err := json.MarshalIndent(u, "", "  ")
	if err != nil {
		log.Println(err)
		os.Exit(1)
	}

	fmt.Println(string(out))
}
```

当你运行这个例子时，你会看到这样的输出：

```bash
Output
{
  "name": "Sammy the Shark",
  "createdAt": "2019-09-23T16:08:21.124481-04:00"
}
```

这个例子与之前的例子相比，唯一的变化是密码字段。现在使用了特殊的 `"-"` 作为其 `json:` 结构体标签的值。我们看到，在这个例子的输出中，`password` 字段不再存在了。

`encoding/json` 包的这些特征，`,omitempty` 和 `"-"`，并不是标准。一个包决定对结构体标签的值做什么取决于它的实现。因为 `encoding/json` 包是标准库的一部分，其他包也以同样的方式实现这些功能，这是一个惯例。然而，很重要的一点是阅读任何使用结构体标签的第三方软件包的文档，以了解哪些是支持的，哪些是不支持的。

## 总结

结构体标签提供了一种强大的手段来拓展了使用你定义的结构体代码的功能。许多标准库和第三方包提供了通过使用结构体标签来定制其操作的方法。在你的代码中有效地使用它们，既能提供这种定制行为，又能为未来的开发者简要记录这些字段的使用方法。
