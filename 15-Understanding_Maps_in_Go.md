# 理解 Go 中的 Map

大多数现代编程语言都有_字典_或_哈希_类型的概念。这些类型通常用于以成对的方式存储数据，其中的**key**映射到**value**。

在 Go 中，_map_ 数据类型就是大多数程序员认为的字典类型。它将键映射到值，形成键值对，是 Go 中存储数据的一种有效方式。一个 map 的构造是通过使用关键字 `map`，然后是方括号中的键数据类型 `[ ]`，接着是值数据类型。然后将键值对放在大括号的两侧 { } 中。

```go
map[key]value{}
```

通常在 Go 中使用 map 来保存相关数据，例如 ID 中包含的信息。一个有数据的 map 看起来像这样。

```go
map[string]string{"name": "Sammy", "animal": "shark", "color": "blue", "location": "ocean"}
```

除了大括号外，整个 map 中还有冒号连接键值对。冒号左边的字是键。键值可以是 Go 中可比较的类型，如 `strings`、`ints` 等。

示例 map 中的键是：

*   `"name"`
*   `"animal"`
*   `"color"`
*   `"location"`

冒号右边的字是值，值可以是任何数据类型。示例 map 中的值是：

*   `"Sammy"`
*   `"shark"`
*   `"blue"`
*   `"ocean"`

像其他数据类型一样，你可以将 map 存储在一个变量内，并将其打印出来：

```go
sammy := map[string]string{"name": "Sammy", "animal": "shark", "color": "blue", "location": "ocean"}
fmt.Println(sammy)

```

输出为：

```go
Output
map[animal:shark color:blue location:ocean name:Sammy]
```

键值对的顺序可能发生了变化。在 Go 中，map 数据类型是无序的。无论顺序如何，键值对将保持不变，你能够根据键值关系来访问数据。

## 获取 map 元素

你可以通过引用相关的键来获取一个 map 的值。由于 map 提供了存储数据的键值对，它们可以成为你的 Go 程序中重要而有用的东西。

如果你想获取 Sammy 的用户名，你可以通过调用 `sammy["name"]` 来实现；持有你的 map 和相关键的变量。我们把它打印出来：

```go
fmt.Println(sammy["name"])
```

输出的值如下：

```go
Output
Sammy
```    

map 的行为就像一个数据库；而不是像分片那样调用一个整数来获得一个特定的索引值，你把一个值分配给一个键，然后调用这个键来获得它的相关值。

通过调用键 `name`，你会得到该键的值，也就是 `Sammy`。

类似地，你可以用同样的格式调用 `sammy` 映射中的其余值：

```go
fmt.Println(sammy["animal"])
// returns shark
    
fmt.Println(sammy["color"])
// returns blue
    
fmt.Println(sammy["location"])
// returns ocean
```

通过利用 map 数据类型中的键值对，你可以引用键来查询值。

## 键和值

与某些编程语言不同，Go 没有任何方便的函数来列出 map 的键或值。比如 Python 可以用 `.keys()` 方法来查看所有的键。然而，它允许通过使用 `range` 操作符来进行迭代查看：

```go
for key, value := range sammy {
    fmt.Printf("%q is the key for the value %q\n", key, value)
}
```

当在 Go 中对一个 map 进行 range 遍历时，它将返回两个值。第一个值是键，第二个值是值。Go 将以正确的数据类型创建这些变量。在这个例子中，map 的键是一个 `string`，所以 `key` 也将是一个字符串。`value` 也是一个字符串：

```go
Output
animal" is the key for the value "shark"
"color" is the key for the value "blue"
"location" is the key for the value "ocean"
"name" is the key for the value "Sammy"
```

要获得一个只有键的列表，你可以再次使用 range 操作符。你可以只声明一个变量，只访问键：


```go
keys := []string{}
    
for key := range sammy {
    keys = append(keys, key)
}
fmt.Printf("%q", keys)
```

程序一开始就声明了一个切片来存储你的键。

只输出 map 的所有键：

```go
Output
["color" "location" "name" "animal"]
```

同样，这些键没有被排序。如果你想对它们进行排序，你可以使用[`sort`](https://golang.org/pkg/sort)包中的 `sort.Strings` 函数：


```go
sort.Strings(keys)
```

使用这个函数，你会收到以下输出：

```go
Output
["animal" "color" "location" "name"]
```

你可以使用同样的模式来检索一个 map 中的值。在下一个例子中，你预先分配了切片以避免分配，从而使程序更有效率：


```go
sammy := map[string]string{"name": "Sammy", "animal": "shark", "color": "blue", "location": "ocean"}
    
items := make([]string, len(sammy))
    
var i int
    
for _, v := range sammy {
    items[i] = v
    i++
}
fmt.Printf("%q", items)
```

首先，你声明一个切片来存储键；因为你知道需要多少个元素，你可以通过定义切片的大小来避免潜在的内存分配。然后你声明索引变量。由于你不想要这个键而使用 `_` 操作符，当开始循环时，忽略这个键的值。输出将如下：


```go
Output
["ocean" "Sammy" "shark" "blue"]
```

要查看一个 map 中的元素数量，可以使用内置的`len`函数：

```go
sammy := map[string]string{"name": "Sammy", "animal": "shark", "color": "blue", "location": "ocean"}
fmt.Println(len(sammy))
```

输出展示 map 中的元素数量：

```go
Output
4
```

尽管 Go 没有提供获取键和值的便利函数，但在需要时只需要几行代码就可以检索到键和值。

## 检查存在性

当请求的键不存在时，Go 中的 map 将为 map 的值类型返回零值。正因为如此，你需要用另一种方法来区分存储零值和不存在的键。

我们来查询 map 中的一个不存在的键，并看看返回的值：

```go
counts := map[string]int{}
fmt.Println(counts["sammy"])
```

你会看到一下输出：

```go
Output
0
```

即使键 `sammy` 不在 map 中，Go 仍然返回 `0` 。这是因为值的数据类型是 `int`，由于 Go 中所有的变量都有零值，所以它返回的是 `0` 的零值。

在很多情况下，这是不可取的，会导致你的程序出现错误。在查找 map 中的值时，Go 可以返回两个值。这第二个值是一个 `bool` 类型，如果找到了键，则为 `true` ，如果没有找到键，则为 `false`。在 Go 中，惯用变量名为 `ok`。尽管你可以把捕捉第二个参数的变量命名为任何名字，但在 Go 中，`ok` 是一种惯用方式：

```go
count, ok := counts["sammy"]
```

如果键 `sammy` 存在于 `counts` map 中，那么 `ok` 将是 `true`。否则，`ok` 将是 `false`。

你可以使用 `ok` 变量来决定在你的程序中做什么：

```go
if ok {
    fmt.Printf("Sammy has a count of %d\n", count)
} else {
    fmt.Println("Sammy was not found")
}
```

输出结果如下：

```go
Output
Sammy was not found
```

在 Go 中，你可以将变量声明和条件检查与 if/else 相结合。这使得你可以使用一个单一的语句来进行这种检查：

```go
if count, ok := counts["sammy"]; ok {
    fmt.Printf("Sammy has a count of %d\n", count)
} else {
    fmt.Println("Sammy was not found")
}
```

在 Go 中从 map 中查询一个值时，检查其是否存在是很好的做法，以避免程序中出现错误。

## 修改 map

map 是一个可变的数据结构，所以你可以修改它们。让我们在本节中看看添加和删除 map 的元素。

### 增加和修改 map 的元素

在不使用方法或函数的情况下，你可以向 map 添加键值对。你可以使用 map 的变量名，然后是方括号中的键值 `[ ]`，并使用 `=` 操作符来设置一个新值：

```go
map[key] = value
```

你可以通过在一个名为 `usernames` 的 map 上添加一个键值对来看到这个行为：

```go
usernames := map[string]string{"Sammy": "sammy-shark", "Jamie": "mantisshrimp54"}
    
usernames["Drew"] = "squidly"
fmt.Println(usernames)
```

输出将展示 map 中新的 `Drew:squidly` 键值对：

```go
Output
map[Drew:squidly Jamie:mantisshrimp54 Sammy:sammy-shark]
```

因为 map 是无序返回的，这一对键值可以出现在 map 输出的任何地方。如果你程序后面使用 `usernames` 的 map，它将包括新加的这个键值对。

你也可以使用这个语法来修改一个键的值。在这种情况下，你引用一个现有的键，并传递一个不同的值给它。

考虑一个名为 `followers` 的 map，它记录某个网络上用户的粉丝。用户 `drew` 今天的粉丝增加了，所以你需要更新 `drew` 键的整数值。可以使用 `Println()` 函数来检查 map 是否被修改：

```go
followers := map[string]int{"drew": 305, "mary": 428, "cindy": 918}
followers["drew"] = 342
fmt.Println(followers)
```

以下输出了 `drew` 更新后的值：

```go
Output
map[cindy:918 drew:342 mary:428]
```

你可以用这种方法将键值对添加到用户输入的 map 中。让我们写一个快速程序 `usernames.go`，它在命令行上运行，允许用户输入，以增加更多的名字和相关的用户名：

usernames.go

```go
package main
    
import (
    "fmt"
    "strings"
)
    
func main() {
    usernames := map[string]string{"Sammy": "sammy-shark", "Jamie": "mantisshrimp54"}
    
    for {
        fmt.Println("Enter a name:")
    
        var name string
        _, err := fmt.Scanln(&name)
    
        if err != nil {
            panic(err)
        }
    
        name = strings.TrimSpace(name)
    
        if u, ok := usernames[name]; ok {
            fmt.Printf("%q is the username of %q\n", u, name)
            continue
        }
    
        fmt.Printf("I don't have %v's username, what is it?\n", name)
    
        var username string
        _, err = fmt.Scanln(&username)
    
        if err != nil {
            panic(err)
        }
    
        username = strings.TrimSpace(username)
    
        usernames[name] = username
    
        fmt.Println("Data updated.")
    }
}
```

在 `usernames.go` 中，你首先定义 map。然后设置了一个循环来迭代名字。你要求用户输入一个名字，并声明一个变量来存储它。接下来检查是否有错误；如果有，程序将以 _panic_ 退出。因为 `Scanln` 捕获了整个输入，包括回车，你需要从输入中删除空格；你可以用 `strings.TrimSpace` 函数来做这个。

`if` 代码块检查名字是否存在于 map 中并打印反馈。如果名字是存在的，则继续回到循环的顶部。如果名字不在 map 中，它会向用户提供反馈，然后会要求提供一个新的用户名。程序再次检查，看是否有错误。如果没有错误，它就去掉回车键，将用户名的值分配给名字键，然后打印出数据被更新的反馈。

让我们在命令行上运行该程序：

```go
go run usernames.go
```

你将看到如下输出：

```go
Output
Enter a name:
Sammy
"sammy-shark" is the username of "Sammy"
Enter a name:
Jesse
I don't have Jesse's username, what is it?
JOctopus
Data updated.
Enter a name:
```

完成测试后，可以通过 `CTRL + C` 键退出程序。

这展示了你如何以交互方式修改 map。对于这个特殊的程序，只要你用 `CTRL + C` 退出程序，你就会丢失所有的数据，除非你实现了处理读写文件的方法。

总结一下，你可以用 `map[key] = value` 语法向 map 添加元素或修改值。

### 删除 map 的元素

正如你可以在 map 数据类型中添加键值对和改变数值一样，你也可以在 map 中删除元素。

要从一个 map 中删除一个键值对，你可以使用内置函数 `delete()`。第一个参数是你想要删除的 map。第二个参数是你要删除的键：

```go
delete(map, key)
```

我们定义一个名为 `permission` 的 map：

```go
permissions := map[int]string{1: "read", 2: "write", 4: "delete", 8: "create", 16:"modify"}
```

你不再需要 `modify` 变量，所以你要把它从 map 上删除。然后，你要打印出 map，以确认它被删除。

```go
permissions := map[int]string{1: "read", 2: "write", 4: "delete", 8: "create", 16: "modify"}
delete(permissions, 16)
fmt.Println(permissions)
```

输出如下：

```go
Output
map[1:read 2:write 4:delete 8:create]
```

这一行 `delete(permissions, 16)` 从 `permissions` map 中移除键值对 `16: "modify"`。

如果你想清除一个 map 的所有值，你可以通过把它设置为一个相同类型的空 map 来实现。这将创建一个新的空 map 来使旧的 map 被垃圾收集器清除。

让我们删除 `permissions` map 中的所有元素：

```go
permissions = map[int]string{}
fmt.Println(permissions)
```

输出展示 map 为空：

```go
Output
map[]
```

因为 map 是可变的数据类型，它们可以被添加、修改、删除和清除元素。

## 总结

本教程探讨了 Go 中的 map 数据结构。map 是由键值对组成的，提供了一种不依赖索引的数据存储方式。这使得我们可以根据其含义和与其他数据类型的关系来检索数值。