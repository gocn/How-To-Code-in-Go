# 理解 Go 的数据类型

## 介绍

数据类型是指在编写程序时特定变量存储的值的类型。数据类型还决定对数据可以执行哪些操作。

在本文中，我们将介绍 Go 本身的重要数据类型。本文并不是对数据类型的详尽调查，但将帮助你熟悉 Go 中可用的选项。理解一些基本数据类型将可以让你能够编写更清晰的代码，从而更有效地执行。

## 背景

了解数据类型的一种方法是考虑我们在现实世界中使用的不同类型的数据。现实世界中数据的一个例子是数字: 例如，我们可以使用无符号整数（0，1，2，...）、有符号整数（... ，-1，0，1，...）和无理数（π）。

通常，在数学中，我们可以把不同类型的数字组合起来，来得到某个答案。例如，我们可以把 5 加到 π 上:

```
5 + π
```

我们可以把这个方程式作为答案因为存在无理数，也可以把 π 近似舍入到十进制的缩写数字，再把这些数字相加:

```
5 + π = 5 + 3.14 = 8.14 
```

但是，如果我们开始尝试用另一种数据类型，比如单词来计算数字，就变得不那么有意义了。我们如何解下面的公式呢？

```
shark + 8
```

对于计算机来说，每种数据类型区别很大，就像文字和数字一样。因此，对于不同的数据类型的赋值和修改操作，我们必须谨慎。

## 整数

和数学一样，计算机编程中的 _整数_ 可以是正数、负数或者 0（... -1,0,1，...）。在 Go 中，整数被称为 `int`。与其他编程语言一样，不应该在四位数或更多的数字中使用逗号，所以在程序中写入 1,000 时，请写为 `1000`。

我们可以简单向下面这种方式打印一个整数：

```go
fmt.Println(-459)
```

```text
output
-459
```

或者，我们可以声明一个变量，在这个例子中是我们用来表示和操作数字的符号，如：

```go
var absoluteZero int = -459
fmt.Println(absoluteZero)
```

```text
output
-459
```

我们也可以在 Go 中对整数进行计算。在下面的代码块中，我们将使用 `:=` 分配符来声明和实例化变量 `sum`：

```go
sum := 116 - 68
fmt.Println(sum)
```

```text
output
48
```

如输出所示，数学算子 `-` 从 `116` 中减去整数 `68`，得到 `48`。你将在**声明变量的数据类型**部分了解有关变量声明的更多信息。

在 Go 程序中，有多种方式可以使用整数。随着你继续学习 Go，你将有很多机会使用整数并在此数据类型的知识基础上进行构建。

## 浮点数

_浮点数_ 或 _float_ 用于表示不能表示为整数的[实数](https://en.wikipedia.org/wiki/Real_number)。实数包括所有的有理数和无理数，因此，浮点数可以包含小数部分，比如 9.0 或 -116.42。Go 程序中的浮点数，可以认为是一个包含小数点的数字。

就像我们处理整数一样，我们可以用这样一种简单的方式打印出一个浮点数:

```go
fmt.Println(-459.67)
```

```text
output
-459.67
```

我们也可以声明一个变量表示浮点数，如：

```go
absoluteZero := -459.67
fmt.Println(absoluteZero)
```

```text
output
-459.67
```

和整数类似，我们也可以在 Go 中计算浮点数：

```go
var sum = 564.0 + 365.24
fmt.Println(sum)
```

```text
output
929.24
```

对于整数和浮点数，记住 3 ≠ 3.0 很重要，因为 3 表示的是一个整数，而 3.0 表示的是一个浮点数。

## 数字类型的大小

除了整数和浮点数之间的区别之外，Go 还有两种类型的数值数据，这两种数值数据通过其大小的静态或动态特性进行区分。第一种类型是与_体系结构无关_的类型，这意味着无论代码运行在哪台机器上，以位为单位的数据的大小都不会改变。

今天的大多数系统架构都是32位或64位的。例如，你可能正在一台现代的 Windows 笔记本电脑上开发，其操作系统运行在 Windows 64位架构上。然而，如果你正在为一个像健身手表这样的设备开发应用时，你可能正在使用一个32位的架构。如果你使用像 `int32` 这样独立于体系架构的类型，那么无论你为何种体系架构编译，该类型都将具有一个固定的大小。

第二种是_实现专用_的类型。在这种类型中，位大小可以根据程序所在的体系架构的不同而变化。例如，如果我们使用 `int` 类型，当 Go 为 32 位架构编译时，数据类型的大小将为 32 位。如果程序是为 64 位架构编译的，那么变量的大小是 64 位。

除了数据类型大小不同外，类似整数的类型还有两种基本类型: _有符号_和_无符号_。`int8` 是一个有符号整数，可以取 -128 到 127 之间的值。`uint8` 是一个无符号整数，只能取 0 到 255 之间的正数值。

取值范围与位的大小有关。对于二进制数据，8 位可以表示总共 256 个不同的值。因为 `int` 类型需要同时支持正值和负值，所以 8 位整数（`int8`）的范围是 -128 到 127，总共有 256 个不同的值。

Go 具有以下与架构无关的整数类型:

```text
uint8       unsigned  8-bit integers (0 to 255)
uint16      unsigned 16-bit integers (0 to 65535)
uint32      unsigned 32-bit integers (0 to 4294967295)
uint64      unsigned 64-bit integers (0 to 18446744073709551615)
int8        signed  8-bit integers (-128 to 127)
int16       signed 16-bit integers (-32768 to 32767)
int32       signed 32-bit integers (-2147483648 to 2147483647)
int64       signed 64-bit integers (-9223372036854775808 to 9223372036854775807)
```

浮点数和复数的大小也是变化的：

```text
float32     IEEE-754 32-bit floating-point numbers
float64     IEEE-754 64-bit floating-point numbers
complex64   complex numbers with float32 real and imaginary parts
complex128  complex numbers with float64 real and imaginary parts
```

还有一些数字类型的别名，通过分配有用的名称来表示特定的数据类型：

```text
byte        alias for uint8
rune        alias for int32
```

`byte` 别名的目的是清楚地表明程序何时使用字节作为字符串元素的常用计算度量，而不是与字节数据度量无关的小整数。尽管程序编译后 `byte` 和 `uint8` 是相同的，但 `byte` 通常用于表示数字形式的字符数据，而 `uint8` 则用于表示程序中的数字。

`rune` 别名有点不同。如果 `byte` 和 `uint8` 是完全相同的数据，那么 `rune` 可以是一个字节或四个字节，这个范围由 `int32` 决定。`rune` 用来表示 Unicode 字符，而只有 ASCII 字符可以由 `int32` 数据类型单独表示。

此外，Go 还有以下特定实现的类型:

```text
uint     unsigned, either 32 or 64 bits
int      signed, either 32 or 64 bits
uintptr  unsigned integer large enough to store the uninterpreted bits of a pointer value 
```

特定实现的类型大小将由程序编译的架构决定。

### 选择数值数据类型

选择正确的大小通常更多地取决于编程所针对的目标架构的性能，而不是所处理的数据的大小。但是，无需知道性能对程序的具体影响，你可以在动手前遵循一些基本指导原则。

正如本文前面所讨论的，有与架构无关的类型和特定实现的类型。对于整数数据，Go 中通常使用 `int` 或 `uint` 等实现类型，而不是 `int64` 或 `uint64`。这通常会为你的目标架构带来最快的处理速度。例如，如果使用 `int64` 并将其编译为 32 位架构上的应用，那么处理这些值所需的时间至少多花一倍，因为在该架构下移动数据需要额外的 CPU 周期。如果使用 `int`，程序会将其定义为 32 位体系结构的 32 位大小，并且处理起来会快得多。

如果你知道你不会超过特定的大小范围，那么选择与体系结构无关的类型既可以提高速度，又可以减少内存使用。例如，如果你知道你的数据不会超过 `100`，而且只是一个正数，那么选择 `uint8` 会使你的程序更有效率，因为它需要更少的内存。

既然我们已经了解了数值数据类型的一些可能范围，接下来让我们看看如果在程序中超过这些范围会发生什么。

### 溢出 vs. 折叠

当你试图存储一个比设计存储的数据类型更大的值时，Go 有可能会_溢出_和_折叠_一个数字，这取决于这个值是在编译时还是在运行时计算的。当程序在尝试构建程序时发现错误时，就会发生编译时错误。程序编译完成后，在实际执行过程中会发生的是运行时错误。

在下面的例子中，我们将 `maxUint32` 设置为它的最大值:

```go
package main

import "fmt"

func main() {
	var maxUint32 uint32 = 4294967295 // Max uint32 size
	fmt.Println(maxUint32)
}
```

编译后的运行结果如下：

```text
output
4294967295
```

如果我们在运行时对该值加 `1`，它将会被折叠为 `0`：

```text
output
0
```

另一方面，我们修改程序，在分配值且编译前加 `1`：

```go
package main

import "fmt"

func main() {
	var maxUint32 uint32 = 4294967295 + 1
	fmt.Println(maxUint32)

}
```

在编译时，如果编译器可以确定一个值将太大而无法保存在指定的数据类型中时，它将抛出 `overflow` 溢出错误。这意味着计算的值对于指定的数据类型来说太大了。

因为编译器可以确定它会溢出值，它现在会抛出一个错误:

```text
outputprog.go:6:36: constant 4294967296 overflows uint32
```

理解数据的边界将帮助你避免将来程序中可能出现的错误。

现在我们已经介绍了数值类型，让我们来看看如何存储布尔值。

## 布尔值

_boolean_ 数据类型可以从两个值中取值，即 `true` 或 `false`，在声明为数据类型时定义为 `bool`。布尔值用于表示与数学逻辑分支相关的真值，它为计算机科学中的算法提供信息。

值 `true` 和 `false` 将始终分别使用小写的 `t` 和 `f`，因为它们是 Go 中预先声明的标识符。

许多数学运算给出的答案要么是真要么是假：

- 大于
    - 500 > 100 true
    - 1 > 5 false
- 小于
    - 200 < 400 true
    - 4 < 2 false
- 等于
    - 5 = 5 true
    - 500 = 400 false

与数字相似，我们可以用变量保存布尔值：

```go
myBool := 5 > 8
```

接下来我们可以调用 `fmt.Println()` 函数来打印布尔值：

```go
fmt.Println(myBool)
```

因为 `5` 小于 `8`， 我们将会得到下面的输出结果：

```text
output
false
```

随着你在 Go 中编写越来越多的程序，你会越来越熟悉布尔值是如何工作的，以及不同的函数和操作计算为 `true` 或 `false` 会如何改变程序的进程。

## 字符串

字符串是由一个或多个字符（字母、数字、符号）组成的序列，这些字符可以是常量，也可以是变量。字符串存在于 Go 中的反引号 ``` 或 `"` 中，并且根据使用的引号具有不同的特征。

如果使用反引号，就是创建了一个_原始_字符串。如果使用双引号，就是创建了一个_解释_字符串。

### 原始字符串

原始字符串文字是反引号之间的字符序列，通常称为反引号。在引号中，除了引号字符本身之外，反引号之间的任何字符都会显示。

```go
a := `Say "hello" to Go!`
fmt.Println(a)
```

```text
output
Say "hello" to Go!
```

通常，反斜杠用于表示字符串中的特殊字符。例如，在解释字符串中，`\n` 表示字符串中的新行。但是，反斜杠在原始字符串中没有特殊含义:

```go
a := `Say "hello" to Go!\n`
fmt.Println(a)
```

因为反斜杠在字符串文字中没有特殊含义，它实际上会打印出 `\n` 的值，而不是创建一个新的行:

```text
output
Say "hello" to Go!\n
```

原始字符串也可以用来创建多行字符串：

```go
a := `This string is on 
multiple lines
within a single back 
quote on either side.`
fmt.Println(a)
```

```text
output
This string is on 
multiple lines
within a single back 
quote on either side.
```

在前面的代码块中，新行会按字面意思从输入传递到输出。

### 解释字符串

解释字符串是双引号之间的字符序列，如 `"bar"` 中所示。在引号中，除了换行符和非转义双引号外，任何字符都可以出现。要在解释字符串中显示双引号，可以使用反斜杠作为转义字符，如下所示:

```go
a := "Say \"hello\" to Go!"
fmt.Println(a)
```

```text
output
Say "hello" to Go!
```

你几乎总会使用解释字符串，因为它们允许在其中使用转义字符。想要了解更多关于字符串使用的信息，请查看[Go 中使用字符串的简介](https://gocn.github.io/How-To-Code-in-Go/docs/08-An_Introduction_to_Working_with_Strings_in_Go)。

### UTF-8 字符的字符串

UTF-8 是一种编码方案，用于将可变宽度字符编码为一到四个字节。Go 支持开箱即用的 UTF-8 字符，不需要任何特殊设置、库或包。像字母 `A` 这样的罗马字符可以用像数字65这样的 ASCII 值表示。但是，如果使用特殊字符，例如`世`这样的国际字符，则需要 UTF-8。Go 对 UTF-8 数据使用 `rune` 别名类型。

```go
a := "Hello, 世界"
```

你可以在 `for` 循环中使用 `range` 关键字来索引 Go 中的任何字符串，甚至是 UTF-8 字符串。`for` 循环和 `range` 将在本系列后面更深入地讨论; 现在，重要的是知道我们可以使用它来计算给定字符串中的字节数：

```go
package main

import "fmt"

func main() {
	a := "Hello, 世界"
	for i, c := range a {
		fmt.Printf("%d: %s\n", i, string(c))
	}
	fmt.Println("length of 'Hello, 世界': ", len(a))
}
```

在上面的代码块中，我们声明了变量 `a`，并为其赋值 `Hello，世界`。分配的文本中包含 UTF-8 字符。

然后我们使用了一个标准的 `for` 循环以及 `range` 关键字。在 Go 中，`range` 关键字将通过字符串索引，每次返回一个字符，以及字符位于字符串中的字节索引。

使用 `fmt.Printf` 函数中，我们提供了一个格式字符串 `%d:%s\n`。`%d` 是数字的打印动词（在本例中是整数），`%s` 是字符串的打印动词。然后我们提供了 `i` 的值，它是 `for` 循环的当前索引，`c` 是 `for` 循环中的当前字符。

最后，我们使用内建的 `len` 函数打印了变量 `a` 的整个长度。

在前面，我们提到了 rune 是 `int32` 的别名，可以由一到四个字节组成。`世` 字符需要三个字节来定义，当通过 UTF-8 字符串进行循环时，索引也会相应地移动。这就是当它被打印出来时 `i` 不是顺序的原因。

```text
output0: H
1: e
2: l
3: l
4: o
5: ,
6:
7: 世
10: 界
length of 'Hello, 世界':  13
```

如你所见，字符串的长度比它循环迭代的次数大。

你不会总使用 UTF-8 字符串，但当使用时，你将会理解为什么他们叫做 rune 而不是单个 `int32`。

## 声明变量的数据类型

既然你已经了解了不同的基本数据类型，接下来我们将讨论如何在 Go 中将这些类型分配给变量。

在 Go 中，我们可以使用关键字 `var` ，后面跟着变量名和所需的数据类型来定义一个变量。

在下面的示例中，我们将声明一个类型为 `float64` 的变量 `pi`。

关键字 `var` 是首先声明的:

```go
`var` pi float64
```

后面是我们的变量名，`pi`：

```go
var `pi` float64
```

最后是数据类型 `float64`：

```go
var pi `float64`
```

我们也有指定初始值的选项，比如 `3.14`：

```go
var pi float64 = `3.14`
```

Go 是一种_静态类型_语言。静态类型意味着在编译时检查程序中的每个语句。它还意味着数据类型绑定到变量，而在动态链接的语言中是数据类型绑定到值。

例如，在 Go 中，当声明一个变量时，类型被声明:

```go
var pi float64 = 3.14
var week int = 7
```

如果以不同的方式声明这些变量，它们中的每一个都可能是不同的数据类型。

这与 PHP 语言不同，它的数据类型与值相关联:

```php
$s = "sammy";         // $s is automatically a string
$s = 123;             // $s is automatically an integer
```

在前面的代码块中，第一个 `$s` 是一个字符串，因为它被赋值为 `"sammy"`，第二个是一个整数，因为它的值为 `123`。

接下来，让我们看看更复杂的数据类型，比如数组。

## 数组

_数组_是元素的有序序列。数组的容量在创建时定义。一旦数组分配了它的大小，它的大小就不能再改变了。因为数组的大小是静态的，这意味着它只分配一次内存。这使得数组有些僵硬，但是增加了程序的性能。因此，数组通常用于优化程序。接下来介绍的_切片_更加灵活，它与你在其他语言中认识的数组更相似。

数组是通过声明数组的大小来定义的，然后是数据类型和定义在花括号 `{ }`之间的值。

字符串数组如下所示：

```go
[3]string{"blue coral", "staghorn coral", "pillar coral"}
```

我们可以把数组保存在变量中并打印出来：

```go
coral := [3]string{"blue coral", "staghorn coral", "pillar coral"}
fmt.Println(coral)
```

```text
output
[blue coral staghorn coral pillar coral]
```

上面提到过，切片和数组类似，但更加的灵活。让我们看下这个可变的数据类型。

## 切片

一个_切片_是一个可以改变长度的有序元素序列。切片可以动态地增加它们的大小。当你向一个切片添加新元素时，如果该切片没有足够的内存来存储新元素，那么它将根据需要从系统中申请更多的内存。由于可以根据需要扩展切片以添加更多的元素，因此它们比数组更常用。

切片的定义是通过声明前面有开方括号和闭方括号 `[]` 的数据类型，并且在花括号 `{}` 之间有值。

整数切片如下所示：

```go
[]int{-3, -2, -1, 0, 1, 2, 3}
```

浮点数的切片如下所示：

```go
[]float64{3.14, 9.23, 111.11, 312.12, 1.05}
```

字符串的切片如下所示：

```go
[]string{"shark", "cuttlefish", "squid", "mantis shrimp"}
```

我们定义一个字符切片叫做 `seaCreatures`：

```go
seaCreatures := []string{"shark", "cuttlefish", "squid", "mantis shrimp"}
```

我们可以通过调用变量打印出来：

```go
fmt.Println(seaCreatures)
```

输出将和我们创建的列表完全一样：

```text
output
[shark cuttlefish squid mantis shrimp]
```

We can use the `append` keyword to add an item to our slice. The following command will add the string value of `seahorse` to the slice:
我们可以使用 `append` 关键字，在切片中添加一个元素。下面的命令将在切片中增加字符串值 `seahorse`：

```go
seaCreatures = append(seaCreatures, "seahorse")
```

你可以通过打印来验证确实添加了这个元素：

```go
fmt.Println(seaCreatures)
```

```text
output
[shark cuttlefish squid mantis shrimp seahorse]
```

如你所见，若你需要管理一个未知长度的元素，切片将比数据更加适合。

## Maps

_map_是 Go 内建的哈希或字典类型。 Map 使用 _键_ _值_对来存储数据。在编程中快速通过索引或在 Go 中通过键来查找值时非常有用。比如，你可能会用 map 按用户 ID 为索引来保存用户。键可以是用户 ID，用户对象可以是值。map 可以用 `map` 关键字，接着把键的数据类型放在 `[]` 中括号里，后面是值的数据类型，且键值对放在大括号中。

```go
map[key]value{}
```

一般用于保存相关的数据，比如包含 ID 的信息，map 如下所示：

```go
map[string]string{"name": "Sammy", "animal": "shark", "color": "blue", "location": "ocean"}
```

你将注意到，除了大括号之外，整个 map 中还有冒号。冒号左边的字是键。键可以是 Go 中的任何_可比较的_类型。可比较的类型是一些基本类型，如 `strings`、`ints` 等。基本类型是由语言定义的，而不是通过组合任何其他类型构建的。虽然它们可以是用户定义的类型，但为了避免编程错误，保持它们简单被认为是最佳实践。上面字典中的键是: `name`、`animal`、`color` 和 `location`。

冒号右边的单词是值。值可以由任何数据类型组成。上面字典中的值是: `Sammy`，`shark`，`blue` 和 `ocean`。

让我们把 map 存储在一个变量中，然后打印出来:

```go
sammy := map[string]string{"name": "Sammy", "animal": "shark", "color": "blue", "location": "ocean"}
fmt.Println(sammy)
```

```text
output
map[animal:shark color:blue location:ocean name:Sammy]
```

如果我们只想打印 Sammy 的颜色，可以通过调用 `sammy["color"]` 的方式完成。我们把它打印出来：

```go
fmt.Println(sammy["color"])
```

```text
output
blue
```

因为 map 提供了以键-值的方式来存储值，在你的 Go 程序中它们会是重要的元素。

## 结论

此时，你应该对 Go 中可用的一些主要数据类型有了更好的理解。当你使用 Go 语言开发编程项目时，这些数据类型中的每一种都将变得非常重要。

一旦掌握了 Go 中可用的数据类型，就可以学习[如何转换数据类型](https://gocn.github.io/How-To-Code-in-Go/docs/12-How_To_Convert_Data_Types_in_Go)，以便根据具体情况更改数据类型。
