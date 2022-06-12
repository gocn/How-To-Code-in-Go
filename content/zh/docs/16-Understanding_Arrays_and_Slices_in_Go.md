# 理解 Go 中的数组和切片

## 介绍

在 Go 中，数组和切片是[数据结构](https://en.wikipedia.org/wiki/Data_structure)，由元素的有序序列组成。当你想处理许多相关的值时，这些数据集合是很好的选择。它们可以让你把相同类型的数据放在一起，降低代码量，并同时对多个值执行相同的方法和操作。

虽然 Go 中的数组和切片都是元素的有序序列，但两者之间有很大的区别。Go 中的数组是一个[数据结构](https://en.wikipedia.org/wiki/Data_structure)，由一个有序的元素序列组成，其容量在创建时已经定义。一旦数组分配了它的大小，就不能再改变它的大小。另一方面，切片是数组的可变长度版本，为使用这些数据结构的开发者提供更多的灵活性。切片其实是你在其他语言中认为的数组。

鉴于这些差异，在一些特定的情况下，你会选择使用一个替代另一个。如果你是 Go 的新手，确定何时使用它们可能会让人困惑。尽管切片的多功能性使其在大多数情况下成为更合适的选择，但在一些特殊情况下，数组可以优化程序的性能。

本文将详细介绍数组和切片，以便在这些数据类型之间做出适当的选择。此外，你将回顾声明和处理数组和切片的常用方法。本教程将首先提供对数组的描述以及如何操作它们，然后解释切片以及它们的区别。

## 数组

数组是具有固定数量元素的数据结构。因为数组的大小是静态的，所以数据结构只需要分配一次内存，而可变长度的数据结构则必须动态地分配内存，以便将来可以变大或变小。尽管数组的固定长度会使它们在工作时有些僵硬，但一次性的内存分配可以提高程序的速度和性能。正因为如此，开发人员在优化程序时通常使用数组，在这种情况下，数据结构不需要可变数量的元素。

## 定义一个数组

数组的定义是在大括号 `[]` 中声明数组的大小，然后是各元素的数据类型。Go 中的数组必须使其所有元素都是相同的[数据类型]({{< relref "/docs/07-Understanding_Data_Types_in_Go.md" >}})。在数据类型之后，你可以用大括号 `{ }` 来声明数组元素的单个值。

下面是声明一个数组的一般模式：

```go
[capacity]data_type{element_values}
```

**注意：** 重要的是要记住，每一个新数组的声明都会创建一个不同的类型。所以，尽管 `[2]int` 和 `[3]int` 都有整数元素，但它们不同的长度使得它们的数据类型不兼容。

如果你不声明数组元素的值，默认为零值，这意味着数组的元素将是空的。对于整数，用 `0` 表示，对于字符串，用空字符串表示。

例如，下面的数组 `numbers` 有三个整数元素，但还没有值：

```go
var numbers [3]int
```

如果你打印 `numbers`，会得到以下输出：

```go
Output
[0 0 0]
```

如果你想在创建数组时指定元素的值，需要将这些值放在大括号里。一个有设定值的字符串数组看起来像这样：

```go
[4]string{"blue coral", "staghorn coral", "pillar coral", "elkhorn coral"}
```

可以将一个数组存储在一个变量中并打印出来：

```go
coral := [4]string{"blue coral", "staghorn coral", "pillar coral", "elkhorn coral"}
fmt.Println(coral)
```

打印结果如下：

```go
Output
[blue coral staghorn coral pillar coral elkhorn coral]
```

请注意，当数组被打印出来时，数组中的元素之间没有划分，因此很难分辨一个元素在哪里结束，另一个元素在哪里开始。由于这个原因，有时使用 `fmt.Printf` 函数是很有帮助的，它可以在打印到屏幕前对字符串进行格式化。在该命令中提供 `%q` 谓词并加上引号：

```go
fmt.Printf("%q\n", coral)
```

打印的结果如下：

```go
Output
["blue coral" "staghorn coral" "pillar coral" "elkhorn coral"]
```

现在每个元素都有引号。`\n` 谓词指示格式化会在每行结束打印换行符。

有了关于如何声明数组及其组成的概念，现在可以继续学习如何用索引号指定数组中的元素。

## 索引数组和切片

数组中的每个元素（也包括切片）都可以通过索引单独调用。每个元素都对应着一个索引号，它是一个从索引号 `0` 开始向上计数的 `int` 值。

在下面的例子中，我们将使用一个数组，但是你也可以使用一个切片，因为它们在索引方式上是相同的。

对于数组 `coral` 来说，索引看起来像这样。

“blue coral” “staghorn coral” “pillar coral” “elkhorn coral”

0 1 2 3

第一个元素，字符串 `"blue coral"`，从索引 `0` 开始，在索引为 `3` 的元素 `"elkhorn coral"`结束。

因为切片或数组中的每个元素都有一个相应的索引号，我们能够以与其他顺序数据类型相同的方式访问和操作它们。

现在我们可以通过引用索引号来调用切片 中的一个离散元素：

```go
fmt.Println(coral[1])
```

```go
Output
staghorn coral
```

这个切片的索引号范围是 `0-3`，如前表所示。所以要单独调用一个元素的话，我们要像这样引用索引号：

```go
coral[0] = "blue coral"
coral[1] = "staghorn coral"
coral[2] = "pillar coral"
coral[3] = "elkhorn coral"
```

我们调用数组 `coral` 的索引号如果大于 `3`，它就会超出范围并 panic，因为它是无效的：

```go
fmt.Println(coral[18])
```

```go
Output
panic: runtime error: index out of range
```

当对数组或切片进行索引时，你必须始终使用正数。不像有些语言允许你用负数进行反向索引，在 Go 中这样做会导致错误：

```go
fmt.Println(coral[-1])
```

```go
Output
invalid array index -1 (index must be non-negative)
```

我们可以使用 `+` 操作符将数组或切片中的字符串元素与其他字符串连接起来：

```go
fmt.Println("Sammy loves " + coral[0])
```

```go
Output
Sammy loves blue coral    
```

我们能够将索引号为 `0` 的字符串元素与 `"Sammy loves"` 的字符串连接起来。

有了与数组或切片中的元素相对应的索引号，我们就能够单独访问每个元素，并对这些元素进行操作。为了证明这一点，我们接下来将看看如何修改某个索引的元素。

## 修改元素

我们可以使用索引号来给元素赋值来改变数组或切片中的元素。这使我们对切片和数组中的数据有了更大的控制，并将允许我们以编程方式操作单个元素。

如果我们想把数组 `coral` 中索引为 `1` 的元素的字符串值从 `"staghorn coral"` 改为 `"foliose coral"`，我们可以这样做：

```go
coral[1] = "foliose coral"
```

现在，当我们打印 `coral` 时，数组将是不同的：

```go
fmt.Printf("%q\n", coral)
```

```go
Output
["blue coral" "foliose coral" "pillar coral" "elkhorn coral"]
```

现在你知道了如何操作数组或切片的单个元素，让我们来看看几个函数，它们将在处理集合数据类型时给你更多的灵活性。

## 使用 `len` 计算元素个数

在 Go 中，`len()` 是一个内置函数，用来帮助你处理数组和切片。像处理字符串一样，你可以用数组或切片作为参数通过使用 `len()` 来计算一个数组或切片的长度。

例如，要想知道 `coral` 数组中有多少个元素，你可以使用：

```go
len(coral)
```

如果你打印出数组 `coral` 的长度，你会收到以下输出：

```go
Output
4
```

可以看到数据类型为 `int` 的数组长度为 `4`，这是正确的，因为数组 `coral` 有四个元素：

```go
coral := [4]string{"blue coral", "foliose coral", "pillar coral", "elkhorn coral"}    
```

如果你创建一个有更多元素的整数数组，你也可以对其使用 `len()` 函数：

```go
numbers := [13]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12}
fmt.Println(len(numbers))
```

得到的输出如下：

```go
Output
13
``` 

尽管这些例子数组的元素相对较少，但在元素非常多的数组中 `len()` 函数特别有用。

接下来，我们将介绍如何向集合数据类型添加一个元素，并展示由于数组的固定长度，追加这些静态数据类型将导致错误。

## 使用 `append()` 追加元素

`append()` 是 Go 中的一个内置方法，可以向集合数据类型添加元素。然而，当这个方法用于数组时，将无法工作。如前所述，数组与切片不同的是数组的大小不能被修改。这意味着，虽然你可以改变数组中元素的值，但在定义了数组后，你不能使其变大或变小。

让我们看看你的 `coral` 数组：

```go
coral := [4]string{"blue coral", "foliose coral", "pillar coral", "elkhorn coral"}   
```

假设你想把 `"black coral"` 这个元素添加到这个数组中。如果你试图用 `append()` 函数在数组中输入：

```go
coral = append(coral, "black coral")
```

你将得到一个错误的输出：

```go
Output
first argument to append must be slice; have [4]string
```

为了解决这个问题，我们进一步了解切片的数据类型，如何定义一个切片，以及如何从数组转换为切片。

## 切片

切片是 Go 中的一种数据类型，它是一个可变的数据结构，有序的元素序列。由于切片的大小是可变的，所以在使用时有更大的灵活性；当处理可能需要扩容或缩容的数据集合时，使用切片可以确保你的代码在操作集合的长度时不会出现错误。在大多数情况下，与数组相比，这种可变性值得切片有时可能需要内存重新分配。当你需要存储大量的元素或对元素进行迭代，并且你希望能够随时修改这些元素时，切片数据类型是你的首选。

### 定义一个切片

切片的定义是通过声明数据类型，前面是一组空的方括号（`[]`）和大括号之间的元素列表（`{}`）。你会注意到，与需要在大括号之间加入 `int` 来声明特定长度的数组不同，一个切片在大括号之间没有任何东西来代表其可变长度。

我们来创建一个包含字符串数据类型元素的切片：

```go
seaCreatures := []string{"shark", "cuttlefish", "squid", "mantis shrimp", "anemone"}
```

当我们打印出切片时，就可以看到切片中的元素：

```go
fmt.Printf("%q\n", seaCreatures)
```

可以得到如下的输出：

```go
Output
["shark" "cuttlefish" "squid" "mantis shrimp" "anemone"]
```

如果你想在不填充元素的情况下创建一个特定长度的切片，你可以使用内置的 `make()` 函数：

```go
oceans := make([]string, 3)
```

打印切片将得到如下输出：

```go
Output
["" "" ""]
```

如果你想预先分配一定容量的内存，你可以向 `make()` 传入第三个参数：

```go
oceans := make([]string, 3, 5)
```

这将给分配一个长度为 `3` 容量为 `5` 的零切片。

你现在知道如何声明一个切片了。然而，这还没有解决我们之前在 `coral` 数组中遇到的错误。要在 `coral` 中使用 `append()` 函数，你首先要学会如何在一个数组中切出部分内容。

## 将数组切片

通过使用索引号来确定开始和结束点，你可以调用一个数组内的一个分片值。这被称为对数组进行切分，你可以通过创建一个由冒号分隔的索引号范围来实现，其形式为 `[first_index:second_index]`。然而，需要注意的是，当对一个数组进行切分时，其结果是一个切片，而不是一个数组。

假设你想只打印 `coral` 数组的中间项，而不打印第一个和最后一个元素。你可以通过创建一个从索引 `1` 开始，在索引 `3` 之前结束的分片来实现：

```go
fmt.Println(coral[1:3])
```

运行程序会产生以下结果：

```go
Output
[foliose coral pillar coral]
```

当创建一个切片时，如 `[1:3]`，第一个数字是切片开始的地方（包含），第二个数字是第一个数字和你想检索的元素总数之和：

```go
array[starting_index : (starting_index + length_of_slice)]
```

在这个例子中，你调用第二个元素（或索引 1）作为起点，总共调用了两个元素。计算结果会是这样的：

```go
array[1 : (1 + 2)]
```

所以我们可以得到下面的代码：

```go
coral[1:3]
```

如果你想把数组的开始或结束作为切片的起点或终点，你可以省略 `array[first_index:second_index]` 语法中的一个数字。例如，如果你想打印数组 `coral` 的前三个元素--即 `"blue coral"`、`"foliose coral"` 和 `"pillar coral"` -- 你可以这么做：

```go
fmt.Println(coral[:3])
```

将输出：

```go
Output
[blue coral foliose coral pillar coral]
```

这就打印了数组的开头，在索引 `3` 之前就停止了。

如果要包括数组末尾的所有元素，你可以反过来使用这个语法：

```go
fmt.Println(coral[1:])
```

这将得到以下切片：

```go
Output
[foliose coral pillar coral elkhorn coral]
```

本节讨论了通过分片来调用数组的各个部分。接下来，你将学习如何使用分片将整个数组转换为切片。

## 数组转换为切片

如果你创建了一个数组，并决定需要它有一个可变的长度，你可以把它转换为一个切片。要将一个数组转换为切片，使用你在本教程的**将数组切片**步骤中学到的切片过程，只是这次要省略决定端点的两个索引号，从而选择整个切片：

```go
coral[:]
```

请记住，你不能将变量 `coral` 本身转换为一个切片，因为一旦在 Go 中定义了一个变量，它的类型就不能被改变。为了解决这个问题，你可以把数组的全部内容复制到一个新的变量中作为一个切片：

```go
coralSlice := coral[:]
```

如果你打印 `coralSlice`，你将得到以下输出：

```go
Output
[blue coral foliose coral pillar coral elkhorn coral]
```

现在，使用 `append()` 在新切片中添加 `black coral` 元素：

```go
coralSlice = append(coralSlice, "black coral")
fmt.Printf("%q\n", coralSlice)
```

这将输出添加了新元素的切片：

```go
Output
["blue coral" "foliose coral" "pillar coral" "elkhorn coral" "black coral"]
```

我们也可以在一条 `append()` 语句中添加一个以上的元素：

```go
coralSlice = append(coralSlice, "antipathes", "leptopsammia")
```

```go
Output
["blue coral" "foliose coral" "pillar coral" "elkhorn coral" "black coral" "antipathes" "leptopsammia"]
```

要将两个切片组合在一起，可以使用 `append()`，但必须使用 `...` 的扩展语法来扩展第二个参数进行追加。

```go
moreCoral := []string{"massive coral", "soft coral"}
coralSlice = append(coralSlice, moreCoral...)
```

```go
Output
["blue coral" "foliose coral" "pillar coral" "elkhorn coral" "black coral" "antipathes" "leptopsammia" "massive coral" "soft coral"]    
```

现在你已经学会了如何将一个元素追加到你的切片中，我们再来看看如何删除一个元素。

### 从切片中删除元素

与其他语言不同，Go 没有提供任何内置函数来从切片中删除元素。需要通过分片的方式从切片中删除元素。

要删除一个元素，你必须切出该元素之前的元素，切出该元素之后的元素，然后将这两个新的切片加在一起，不包括你想删除的元素。

如果 `i` 是要删除的元素的索引，那么这个过程的格式看起来就像下面这样：

```go
slice = append(slice[:i], slice[i+1:]...)
```

从 `coralSlice` 中，让我们删除 `"elkhorn coral"` 这个元素。它的索引为 `3`。

```go
coralSlice := []string{"blue coral", "foliose coral", "pillar coral", "elkhorn coral", "black coral", "antipathes", "leptopsammia", "massive coral", "soft coral"}
    
coralSlice = append(coralSlice[:3], coralSlice[4:]...)
    
fmt.Printf("%q\n", coralSlice)
```

```go
Output["blue coral" "foliose coral" "pillar coral" "black coral" "antipathes" "leptopsammia" "massive coral" "soft coral"]    
```

现在索引位置 `3` 的元素 `"elkhorn coral"`，已经不在我们的切片 `coralSlice` 中了。

我们也可以用同样的方法删除一个范围。假设我们不仅想删除 `"elkhorn coral"` 这一项，还想删除 `"black coral"` 和 `"antipathes"`。我们可以在表达式中使用一个范围来完成这个任务：

```go
coralSlice := []string{"blue coral", "foliose coral", "pillar coral", "elkhorn coral", "black coral", "antipathes", "leptopsammia", "massive coral", "soft coral"}
    
coralSlice = append(coralSlice[:3], coralSlice[6:]...)
    
fmt.Printf("%q\n", coralSlice)
```

这段代码将从切片中取出索引 `3`、`4` 和 `5`：

```go
Output["blue coral" "foliose coral" "pillar coral" "leptopsammia" "massive coral" "soft coral"]
```

现在你知道了如何从一个切片中添加和删除元素，让我们来看看如何衡量一个切片所能容纳的数据量。

## 使用 `cap()` 获取切片的容量

由于切片的长度是可变的，`len()` 方法不是确定这种数据类型大小的最佳选择。相反，你可以使用 `cap()` 函数来了解一个切片的容量。这将告诉你一个切片可以容纳多少个元素，这是由已经为切片分配的内存的多少决定的。

**注意：** 由于数组的长度和容量总是相同的，`cap()`函数对数组不起作用。

`cap()` 的一个常见用途是创建一个有预设元素数量的切片，然后再填入元素。这就避免了潜在的不必要的内存分配，因为使用 `append()` 来添加超出当前分配容量的元素会导致内存重新分配。

让我们来看看这样的场景：我们想做一个数字列表，`0` 到 `3`。我们可以在一个循环中使用 `append()` 来完成，或者我们可以先预分配切片，然后使用 `cap()` 来循环填充这些数值。

首先，我们可以看看使用`append()`：

```go
numbers := []int{}
for i := 0; i < 4; i++ {
    numbers = append(numbers, i)
}
fmt.Println(numbers)
```

```go
Output
[0 1 2 3]
```

在这个例子中，我们创建了一个切片，然后创建了一个 `for` 循环，这个循环会迭代四次。每次迭代都将循环变量 `i` 的当前值追加到 `numbers` 切片的索引中。然而，这可能会导致不必要的内存分配，使你的程序变慢。当添加到一个空切片时，每次你调用 append 时，程序都会检查切片的容量。如果添加的元素超过了这个容量，程序将分配额外的内存来存储它。这在你的程序中产生了额外的开销，并可能导致执行速度变慢。

现在让我们在不使用 `append()` 的情况下，通过预先分配一定的长度/容量来填充切片:

```go
numbers := make([]int, 4)
for i := 0; i < cap(numbers); i++ {
    numbers[i] = i
}
    
fmt.Println(numbers)
```

```go
Output
[0 1 2 3]
```

在这个例子中，我们使用 `make()` 来创建一个切片，并让它预先分配 `4` 个元素。然后我们在循环中使用 `cap()` 函数来迭代每个归零的元素，填充每个元素直到达到预先分配的容量。在每个循环中，我们将循环变量 `i` 的当前值放入 `numbers` 的索引中。

虽然 `append()` 和 `cap()` 在功能上是等同的，但 `cap()` 的例子避免了使用 `append()` 函数所需的额外的内存分配。

## 构建多维切片

你也可以定义由切片作为元素的切片，每个括号内的列表都包含在父切片的大括号内。像这样的切片集合被称为多维切片。这些可以被认为是描述多维坐标的；例如，一个由五个切片组成的集合，每个切片有六个元素，可以代表一个水平长度为 5、垂直高度为 6 的二维网格。

让我们来看看下面这个多维切片：

```go
seaNames := [][]string{{"shark", "octopus", "squid", "mantis shrimp"}, {"Sammy", "Jesse", "Drew", "Jamie"}}    
```

要访问这个切片中的一个元素，我们必须使用多个索引，结构的每个维度都有一个索引：

```go
fmt.Println(seaNames[1][0])
fmt.Println(seaNames[0][0])    
```

在前面的代码中，我们首先打印索引为 `1` `0` 的元素，然后我们打印索引为 `0` `0` 的元素。这将产生以下结果：

```go
OutputSammy
shark
```

以下是其余各个元素的索引值：

```go
seaNames[0][0] = "shark"
seaNames[0][1] = "octopus"
seaNames[0][2] = "squid"
seaNames[0][3] = "mantis shrimp"
    
seaNames[1][0] = "Sammy"
seaNames[1][1] = "Jesse"
seaNames[1][2] = "Drew"
seaNames[1][3] = "Jamie"
```

在处理多维切片时，重要的是要记住，你需要参考一个以上的索引号，以便访问嵌套切片中的特定元素。

## 总结

在本教程中，你学到了在 Go 中使用数组和切片的基础。通过多个练习来证明数组的长度是固定的，而切片的长度是可变的，并发现这种差异是如何影响这些数据结构的用途场景。

要继续学习 Go 中的数据结构，请查看我们的文章[理解 Go 中的 Map]({{< relref "/docs/15-Understanding_Maps_in_Go.md" >}})，或探索整个[如何在 Go 中编码](https://gocn.github.io/How-To-Code-in-Go/)系列。