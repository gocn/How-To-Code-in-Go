# 如何在 Go 中使用日期和时间

## 介绍

软件旨在使完成工作更容易，对许多人来说，这包括与日期和时间的交互。日期和时间在现代软件中随处可见。例如，跟踪汽车何时需要维修并让车主知道，跟踪数据库的变化以创建审计日志，或者只是将一个时间与另一个时间相比较以确定一个过程花了多长时间。因此，检索当前时间，操作时间值以从中提取信息，并以易于理解的格式显示给用户，是对一个应用程序最基本的要求之一。

在本教程中，你将创建一个 Go 程序来获取你的计算机的当前本地时间，然后以一种更容易让人阅读的格式将其打印到屏幕。接下来，你将从字符串中提取日期和时间信息。你还将转换两个时区之间的日期和时间值，以及加减时间值以确定两个时间之间的间隔。

## 先决条件

在阅读本教程前，你需要：

-   Go 版本 >= 1.16。你可以按照 [如何安装 Go 和设置本地编程环境]({{< relref "/docs/01-How_To_Install_Go_and_Set_Up_a_Local Programming_Environment_on_Ubuntu_18.04_DigitalOcean.md" >}}) 来安装 Go。

## 获取当前时间

在本节中，你将使用 Go 的 `time` 包获得当前时间。Go 标准库中的 `time` 包提供了各种与日期和时间有关的函数，并且可以使用 `time.Time` 类型来表示一个特定的时间点。除了时间和日期之外，它还可以保存时区信息。

在开始创建一个程序来探索 `time` 包前，你需要创建一个目录。这个目录可以在你的计算机上的任何地方创建，但许多开发者倾向于为他们的项目专门建立一个目录。在本教程中，你将使用一个名为 `projects` 的目录。

创建 `projects` 目录并切换至此目录：

```shell
mkdir projects
cd projects
```

在 `projects` 目录下，使用 `mkdir` 来创建一个 `datetime` 目录，然后使用 `cd` 命令进入此目录：

```shell
mkdir datetime
cd datetime
```

完成目录创建操作后，使用 `nano` 或你喜欢的文本编辑器创建一个名为 `main.go` 的文件：

```shell
nano main.go
```

在 `main.go` 文件中，添加一个 `main` 函数以获取当前时间并打印出来：

> projects/datetime/main.go

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	currentTime := time.Now()
	fmt.Println("The time is", currentTime)
}
```

在这个程序中， `time` 包中的 `time.Now` 函数被用来获取当前本地时间作为 `time.Time` 值，然后将其存储在 `currentTime` 变量中。接着，`fmt.Println` 函数以 `time.Time` 类型的默认字符串输出格式把 `currentTime` 打印到屏幕上。

使用 `go run` 命令，以 `main.go` 为参数，运行程序：

```shell
go run main.go
```

这将会输出你当前的日期和时间，看起来类似于这样：

```
Output
The time is 2021-08-15 14:30:45.0000001 -0500 CDT m=+0.000066626
```

输出将显示你当前的日期和时间，显示会与例子不同。此外，你看到的时区（本输出中的 `0500 CDT`）也可能会不同，因为 `time.Now()` 返回的是本地时区的时间。

你可能还注意到输出中的 `m=` 值。这个值是 [_单调时钟（monotonic clock）_](https://pkg.go.dev/time#hdr-Monotonic_Clocks)，被 Go 用来在内部计算时间差时使用。单调时钟的设计是为了保证程序运行时计算机系统时钟的日期和时间发生了任何潜在变化时，时间计算仍能正常运行。通过使用单调时钟，一个 `time.Now` 值与五分钟后的 `time.Now` 值相比，即使计算机的系统时钟在这五分钟的间隔内向前或向后改变了一个小时，最终仍会得出正确的结果（五分钟的间隔）。对于本教程中的代码或例子，你不需要彻底了解它，但如果你想了解更多关于单调时钟以及 Go 如何使用它们，`time` 包文档中的 [单调时钟](https://pkg.go.dev/time#hdr-Monotonic_Clocks) 部分提供了更多细节。

现在，虽然你确实显示了当前时间，但它对用户来说可能并不实用。它可能不是你期望的格式，或者它可能包含了比你想显示的更多的日期或时间部分。

幸运的是，`time.Time` 类型有着各种方法来获得你想要的日期或时间的特定部分。例如，如果你只想知道 `currentTime` 变量的年份部分，你可以使用 `Year` 方法，或者使用 `Hour` 方法获得当前小时。

再次打开 `main.go` 文件，在输出中添加一些 `time.Time` 的方法，看看发生了什么：

> projects/datetime/main.go

```go
...

func main() {
	currentTime := time.Now()
	fmt.Println("The time is", currentTime)
	
	fmt.Println("The year is", currentTime.Year())
	fmt.Println("The month is", currentTime.Month())
	fmt.Println("The day is", currentTime.Day())
	fmt.Println("The hour is", currentTime.Hour())
	fmt.Println("The minute is", currentTime.Hour())
	fmt.Println("The second is", currentTime.Second())
}
```

现在，使用 `go run` 再次运行程序：

```shell
go run main.go
```

输出看起来像这样：

```
Output
The time is 2021-08-15 14:30:45.0000001 -0500 CDT m=+0.000066626
The year is 2021
The month is August
The day is 15
The hour is 14
The minute is 14
The second is 45
```

和前面的输出一样，你看到的输出中的日期和时间会和例子中的不同，但格式应该是相似的。在这次在输出中，你不但能看到完整的日期和时间被打印出来，就像以前一样，也能看到年、月、日、时、分、秒被分别打印出来。请注意，月份不是以数字的形式出现（就像在完整日期中那样），而是以英文字符串 `August` 的形式出现。这是因为 `Month` 方法将月份作为 `time.Month` 类型返回，而不仅仅是一个数字。同时，该类型被设计为，在以 `string` 的格式打印时输出完整的英文名称。

现在，再次更新程序中的 `main.go` 文件，使用 `fmt.Printf` 函数来格式化各种函数输出，这样你就可以用更接近你可能想要显示给用户的格式来打印当前日期和时间：

> projects/datetime/main.go

```go
...

func main() {
	currentTime := time.Now()
	fmt.Println("The time is", currentTime)
	
	fmt.Printf("%d-%d-%d %d:%d:%d\n",
		currentTime.Year(),
		currentTime.Month(),
		currentTime.Day(),
		currentTime.Hour(),
		currentTime.Hour(),
		currentTime.Second())
}
```

保存并运行程序：

```shell
go run main.go
```

输出看起来像这样：

```
Output
The time is 2021-08-15 14:30:45.0000001 -0500 CDT m=+0.000066626
2021-8-15 14:14:45
```

这次的输出可能更接近于你想要的，但仍有一些可以调整的地方。月份现在又显示为数字格式了，因为 `fmt.Printf` 使用 `%d` 来告诉 `time.Month` 类型应该作为数字格式渲染而不是 `string`。但它只显示了一位数的月份，如果你想显示两位数，你可以改变 `fmt.Printf` 的格式化控制字符。但如果你还想显示12小时的时间而不是上面输出中显示的24小时的时间呢？使用 `fmt.Printf` 方法，你将不得不自己做数学运算。使用 `fmt.Printf` 打印日期和时间是可行的，但正如你所看到的，它最终会变得很麻烦。这样做，你可能需要为每个时间字段都要写大量的显示控制代码，甚至需要额外做一些数学运算。

在本节中，你创建了一个新的程序，使用 `time.Now` 获得当前时间。一旦你得到了当前时间，就可以使用各种函数，如 `time.Time` 类型上的 `Year` 和 `Hour` 函数来打印出关于当前时间的信息。不过，以自定义的格式来显示确实很麻烦。为了使这种对时间的基本操作变得更容易，许多编程语言，包括 Go，都提供了一种特殊的方式来格式化日期和时间，类似于 `fmt.Printf` 可以用来格式化字符串。

## 打印和格式化特定日期

除了 `time.Time` 类型提供的 `Year`、`Hour` 和其他与数据相关的方法外，它还提供了一个叫做 `Format` 的方法。`Format` 方法允许你提供一个格式化字符串，类似于你提供给 `fmt.Printf` 或 `fmt.Sprintf` 的格式那样，这将告诉  `Format` 方法你希望以什么样的格式打印出日期和时间。在本节中，你将复制在上一节中添加的时间输出，但使用 `Format` 方法来控制输出格式要简洁得多。

在你使用 `Format` 方法之前，如果每次运行程序时 `Format` 不发生变化，就能更容易看到 `Format` 如何影响日期和时间的输出。到目前为止，你是用 `time.Now` 来获得当前时间的，所以每次运行都会显示不同的数字。Go的 `time` 包提供了另一个有用的函数，`time.Date` 函数，它允许你指定一个特定的日期和时间让 `time.Time` 来表示。

为了使用 `time.Date` 而不是 `time.Now` 来获取时间，请再次打开 `main.go` 文件并更新它：

> projects/datetime/main.go

```go
...

func main() {
	theTime := time.Date(2021, 8, 15, 14, 30, 45, 100, time.Local)
	fmt.Println("The time is", theTime)
}
```

`time.Date` 函数的参数包括你想要的 `time.Time` 的日期和时间的年、月、日、时、分、秒。最后两个参数中的第一个是纳秒，最后一个参数是要创建的时间的时区。本教程将在后面介绍和使用时区。

保存并运行程序：

```shell
go run main.go
```

输出看起来像这样：

```
Output
The time is 2021-08-15 14:30:45.0000001 -0500 CDT
```

现在你看到的输出应该更接近上面的输出，因为你之前在运行程序时使用的是一个特定的本地日期和时间，而不是当前的日期和时间。(根据你计算机设置的时区的不同，时区可能会显示得不同）。输出格式看起来仍然与你之前看到的相似，因为它仍然使用默认的 `time.Time` 格式。现在你可以使用一个固定的日期和时间，并基于它在使用 `Format` 方法格式化时间时，来调整时间的显示格式。

### 使用 `Format` 方法来自定义日期字符串

许多其他编程语言有类似的方式来格式化日期和时间的显示，但如果你在其他语言中使用过这些格式，Go 的日期格式化方式可能与你习惯的方式略有不同。在其他语言中，日期格式化使用类似于 `Printf` 在 Go 中的格式化方式，在 `%` 字符后面有一个字母，代表要插入的日期或时间的部分。例如，一个4位数的年份可以用 `%Y` 表示。但在 Go 中，日期或时间的特定部分是由代表特定日期的字符来表示的。要在 Go 的日期格式中包含一个4位数的年份，你要在字符串本身中包含 `2006`。这种布局的好处是，你在代码中看到的东西真实代表了你将在输出中看到的东西。当你能够看到你的输出格式化字符串时，你也能更容易仔细检查你的格式是否符合你所期望的，也使其他开发者更容易理解程序的输出，而不需要先运行程序。

Go 在字符串格式化中用于日期和时间布局的具体日期是 `01/02 03:04:05PM '06 -0700`。如果你看一下日期和时间的每个组成部分，你会发现每一部分都会加一。月 `01`、日 `02` 、时 `03`、分 `04`、秒 `05`、年 `06`（或`2006`），时区 `07`。记住这个顺序将使今后创建日期和时间格式更加容易。在 Go 的 [`time`包的文档](https://pkg.go.dev/time#pkg-constants) 中也可以找到格式化选项的详细例子。

现在，使用这个新的 `Format` 方法来复制和清理你在上一节打印的日期格式。它需要几行函数调用来显示，使用 `Format` 方法应该可以使它更容易、简明地复制。

打开 `main.go` 文件，使用 `Format` 方法格式化 `theTime` 这个日期，并用 `fmt.Println` 来输出格式化的结果：

> projects/datetime/main.go

```go
...

func main() {
	theTime := time.Date(2021, 8, 15, 14, 30, 45, 100, time.Local)
	fmt.Println("The time is", theTime)

	fmt.Println(theTime.Format("2006-1-2 15:4:5"))
}
```

如果你看一下正在使用的格式布局，你会发现它使用了和上面相同的时间来指定日期和时间的格式（2006年1月2日）。有一点需要注意的是，小时使用的是 `15`，而不是像前面的例子中的 `03`。这表明你希望以24小时格式而不是12小时格式显示小时。

保存程序并使用 `go run` 运行，以看看新的格式化方式的输出：

```shell
go run main.go
```

输出看起来像这样：

```
Output
The time is 2021-08-15 14:30:45.0000001 -0500 CDT
2021-8-15 14:30:45
```

你现在看到的输出将类似于上一节末尾的输出，但它的代码实现要简单得多。你只要写一行代码和一个布局字符串，`Format` 函数为你完成了剩下的工作。

取决于你要显示的日期或时间，使用变长格式打印数字（就像上一段程序那样），有可能对你自己、你的用户或其他试图读取数值的代码造成困难。使用 `1` 作为月份格式会导致三月显示为 `3` ，而十月会占用两个字符，显示为 `10` 。现在，打开 `main.go`，在你的程序中添加一行，加入另一个更加结构化的布局。在这个布局中，在组件上包括一个 `0` 前缀，并更新小时，使用12小时格式：

> projects/datetime/main.go

```go
...

func main() {
	theTime := time.Date(2021, 8, 15, 14, 30, 45, 100, time.Local)
	fmt.Println("The time is", theTime)

	fmt.Println(theTime.Format("2006-1-2 15:4:5"))
	fmt.Println(theTime.Format("2006-01-02 03:04:05 pm"))
}
```

保存并运行：

```shell
go run main.go
```

输出看起来像这样：

```
Output
The time is 2021-08-15 14:30:45.0000001 -0500 CDT
2021-8-15 14:30:45
2021-08-15 02:30:45 pm
```

你会看到，通过给布局字符串中的数字添加 `0` 前缀，新的输出中的月份的 `8` 变成了 `08` 以匹配布局。小时，现在是12小时的格式，也有自己的 `0` 前缀。不过，最终你在输出中看到的东西反映了你在代码中看到的格式，所以如果你需要，很容易调整格式化布局字符串。

很多时候，格式化的日期是要被其他程序读取并解析的，每次你想使用它们时，重新创建这些格式会成为一种负担。在某些情况下，你可以使用一个预定义的格式。

### 使用预定义格式

有许多常用的日期格式，如日志信息的时间戳，每次你想使用它们时，重新创建这些格式可能会麻烦。对于其中的一些情况，`time` 包涵盖了你可以使用的预定义格式。

其中一个常用的格式由 [RFC 3339](https://datatracker.ietf.org/doc/html/rfc3339) 定义。[_RFC_](https://en.wikipedia.org/wiki/Request_for_Comments) 是一份用来定义互联网上的标准如何工作的文件，然后其他 RFC 可以在此基础上发展。例如，有一个 RFC 定义了 HTTP 的工作方式（[RFC 2616](https://datatracker.ietf.org/doc/html/rfc2616)），而其他 RFC 则在此基础上进一步定义 HTTP。因此，就 RFC 3339 而言，该 RFC 定义了一种用于互联网上的时间戳的标准格式。这种格式在互联网上是众所周知的，并且得到了广泛的支持，所以能在很多地方看到它。

在 `time` 包中的每个预定义的时间格式都由一个 `const string` 表示，并以它们所代表的格式命名。RFC3339 刚好有两种格式，一种叫做 `time.RFC3339`，另一种叫做 `time.RFC3339Nano` 。这两种格式的区别在于，`time.RFC3339Nano` 版本的格式中包括纳秒。

打开 `main.go` 文件，更新程序，使其使用 `time.RFC3339Nano` 格式输出时间：

> projects/datetime/main.go

```go
...

func main() {
	theTime := time.Date(2021, 8, 15, 14, 30, 45, 100, time.Local)
	fmt.Println("The time is", theTime)
	
	fmt.Println(theTime.Format(time.RFC3339Nano))
}
```

由于预定义的格式是所期望的时间格式的 `string` 值，所以你只需要选取其中一种 `RFC3339` 格式来替换之前的格式化字符串就好。

再次运行程序以查看输出：

```shell
go run main.go
```

输出看起来像这样：

```
Output
The time is 2021-08-15 14:30:45.0000001 -0500 CDT
2021-08-15T14:30:45.0000001-05:00
```

如果你需要把时间值保存为一个 `string` ，RFC 3339格式是很好的选择。它可以被许多其他的编程语言和应用程序读取，`string` 格式很灵活，又与日期和时间一样紧凑。

在这一节中，你更新了程序，使用 `Format` 方法来打印出一个日期和时间。使用这种灵活的布局也使你的代码看起来与最终的输出相似。最后，你使用了一个预定义的布局字符串来打印出一个日期和时间，并使用了一个被广泛应用的格式。在下一节中，你将继续更新程序，将同样的 `string` 值转换回 `time.Time` 值。

## 从字符串中解析出日期和时间

在开发应用程序时，你经常会遇到以 `string` 形式表示的日期，你需要以某种方式来读取并解析它。有时，你需要知道该值的日期部分，有时你可能需要知道时间部分，还有些时候，你可能需要整个值。除了使用 `Format` 方法根据 `time.Time` 值创建 `string` 值外，Go 的 `time` 包还提供了一个 `time.Parse` 函数来将 `string` 转换成 `time.Time` 值。`time.Parse` 函数的工作原理与 `Format` 方法类似，它有两个参数，一个是预期的日期和时间布局，另一个是待解析的日期和时间的 `string` 值。

现在，打开你程序中的 `main.go` 文件，更新它以使用 `time.Parse` 函数来把 `timeString` 解析到 `time.Time` 变量：

> projects/datetime/main.go

```go
...

func main() {
	timeString := "2021-08-15 02:30:45"
	theTime, err := time.Parse("2006-01-02 03:04:05", timeString)
	if err != nil {
		fmt.Println("Could not parse time:", err)
	}
	fmt.Println("The time is", theTime)
	
	fmt.Println(theTime.Format(time.RFC3339Nano))
}
```

与 `Format` 方法不同，`time.Parse` 方法还会返回一个潜在的 `error` 值，以防传入的 `string` 值与提供的布局（第一个参数）不符。如果你看一下使用的布局，你会发现传给 `time.Parse` 的布局使用了与 `Format` 方法中相同的布局：使用 `1` 表示月，`2` 表示日，等等。

保存、更新，并运行程序：

```shell
go run main.go
```

输出看起来像这样：

```
Output
The time is 2021-08-15 02:30:45 +0000 UTC
2021-08-15T02:30:45Z
```

在这次的输出中，有这么几个地方需要注意：

一个是，解析 `timeString` 时使用了默认的时区，它是一个 `+0` 的偏移量，被称为[协调世界时](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) (UTC)。由于时间值和布局都不包括时区，所以 `time.Parse` 函数不知道该把它与哪个时区联系起来。不过，如果你在某些时候需要它，`time` 包确实包含了一个 [`time.ParseInLocation`](https://pkg.go.dev/time#ParseInLocation) 函数，所以你可以提供期望的时区。

另一个需要注意的地方是，在 RFC 3339的输出中，输出使用 `time.RFC3339Nano` 布局，但输出不包括任何纳秒。这是因为 `time.Parse` 函数没有解析出任何纳秒信息，所以该值被设置为默认的 `0`。当纳秒为 `0` 时，`time.RFC3339Nano` 格式将不在输出中显示纳秒。

`time.Parse` 方法在解析 `string` 值时也可以使用 `time` 包中提供的任何预定义的时间布局。要想在实操中看到这一点，请打开 `main.go` 文件，更新 `timeString` 值，使之与前面 `time.RFC3339Nano` 的输出相匹配，并更新 `time.Parse` 参数，使之相匹配：

> projects/datetime/main.go

```go
...

func main() {
	timeString := "2021-08-15T14:30:45.0000001-05:00"
	theTime, err := time.Parse(time.RFC3339Nano, timeString)
	if err != nil {
		fmt.Println("Could not parse time:", err)
	}
	fmt.Println("The time is", theTime)
	
	fmt.Println(theTime.Format(time.RFC3339Nano))
}
```

更新、保存并运行程序：

```shell
go run main.go
```

输出看起来像这样：

```
Output
The time is 2021-08-15 14:30:45.0000001 -0500 CDT
2021-08-15T14:30:45.0000001-05:00
```

这一次，`Format ` 方法的输出显示，`time.Parse` 能够从 `timeString` 中解析出时区和纳秒。

在本节中，你用 `time.Parse` 函数解析了一个任意方式格式化的日期和时间字符串值，以及一个预定义的布局。不过到目前为止，你还没有与你所看到的各种时区进行交互。Go 为 `time.Time` 类型提供的另一组特性是能够在不同的时区之间进行转换，你将在下一节中看到这一点。

## 处理时区

当开发一个有着来自世界各地、或只有几个时区的用户的应用程序时，一个常见的做法是使用 [协调世界时](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)(UTC) 来存储日期和时间，然后在需要时转换为用户的当地时间。这能让数据以一致的格式存储，并使它们之间的计算更容易，因为只有在向用户显示日期和时间时才需要转换。

在本教程的前几节中，你创建了一个基于你自己的本地时区的时间的程序。如果要把你的 `time.Time` 值以 UTC 格式保存，你首先需要把它们转换为 UTC。你可以用 `UTC` 方法来完成这一转换，该方法将返回你所调用的时间的 UTC 格式的副本。

**注意：**本节在你计算机的本地时区和 UTC 之间进行时间转换。如果你使用的计算机的本地时区设置与 UTC 一致，你会发现在 UTC 之间转换与转回不会显示出差别。

现在，打开 `main.go` 文件，更新程序，使用 `theTime` 的 `UTC` 方法来返回 UTC 版本的时间：

> projects/datetime/main.go

```go
...

func main() {
	theTime := time.Date(2021, 8, 15, 14, 30, 45, 100, time.Local)
	fmt.Println("The time is", theTime)
	fmt.Println(theTime.Format(time.RFC3339Nano))
	
	utcTime := theTime.UTC()
	fmt.Println("The UTC time is", utcTime)
	fmt.Println(utcTime.Format(time.RFC3339Nano))
}
```

这次程序在你的本地时区创建 `time.Time` 类型的时间值 `theTime` ，并以两种不同的格式打印出来，然后使用 `UTC` 方法把该时间从你的本地时间转换为 UTC 时间。

再次运行程序并看看输出：

```shell
go run main.go
```

输出看起来像这样：

```
Output
The time is 2021-08-15 14:30:45.0000001 -0500 CDT
2021-08-15T14:30:45.0000001-05:00
The UTC time is 2021-08-15 19:30:45.0000001 +0000 UTC
2021-08-15T19:30:45.0000001Z
```

你的输出会因你当地的时区而不同，但在上面的输出中，你会看到第一个打印出来的时间是 `CDT`（北美中部夏令时），与UTC相比是 `-5` 小时。一旦调用 `UTC` 方法，打印出 UTC 时间，你可以看到时间中的小时数从 `14` 变成了 `19`，因为把时间转换为 UTC 增加了 5 个小时。

也可以用同样的方法在 `time.Time` 上调用 `Local` 方法把 UTC 时间转换回本地时间。再次打开 `main.go` 文件，更新它，在 `utcTime`  上 添加对 `Local` 方法的调用，将其转换回你的本地时区：

> projects/datetime/main.go

```go
...

func main() {
	theTime := time.Date(2021, 8, 15, 14, 30, 45, 100, time.Local)
	fmt.Println("The time is", theTime)
	fmt.Println(theTime.Format(time.RFC3339Nano))
	
	utcTime := theTime.UTC()
	fmt.Println("The UTC time is", utcTime)
	fmt.Println(utcTime.Format(time.RFC3339Nano))

	localTime := utcTime.Local()
	fmt.Println("The Local time is", localTime)
	fmt.Println(localTime.Format(time.RFC3339Nano))
}
```

保存并运行程序：

```shell
go run main.go
```

输出看起来像这样：

```
Output
The time is 2021-08-15 14:30:45.0000001 -0500 CDT
2021-08-15T14:30:45.0000001-05:00
The UTC time is 2021-08-15 19:30:45.0000001 +0000 UTC
2021-08-15T19:30:45.0000001Z
The Local time is 2021-08-15 14:30:45.0000001 -0500 CDT
2021-08-15T14:30:45.0000001-05:00
```

你会看到，UTC 时间已经被转换回你的本地时区。在上面的输出中，UTC 转换回 CDT 意味着从 UTC 中减去了5个小时，将小时数从 `19` 变为 `14`。

在这一节中，你更新了程序，使用 `UTC` 方法在本地时区和标准 UTC 时区之间进行日期和时间的转换，然后使用 `Local` 方法再次转换回来。

一旦你有了日期和时间值，Go `time` 包提供了一些额外的特性，可以在你的应用程序中发挥作用。其中一个特性是判断一个给定的时间是在另一个时间之前还是之后。

## 比较两个时间

比较两个时间，有时看似很困难，因为在比较时要考虑内部的所有变量。传说，需要考虑到时区，或者每个月都有不同的天数。不过，`time` 包提供了一些方法来让比较变得更简单。

`time` 包提供了两种方法使时间比较更加容易：`Before` 和 `After` 方法，可用于 `time.Time` 类型。这些方法都接受一个单一的时间值，并根据调用者的时间是在所提供的时间（函数参数）之前还是之后，返回 `true` 或 `false`。

要看这些函数的实际效果，请打开你的 `main.go` 文件，并更新它以创建两个不同的日期，然后使用 `Before` 和 `After` 来比较这些日期，看看输出：

> projects/datetime/main.go

```go
...

func main() {
	firstTime := time.Date(2021, 8, 15, 14, 30, 45, 100, time.UTC)
	fmt.Println("The first time is", firstTime)

	secondTime := time.Date(2021, 12, 25, 16, 40, 55, 200, time.UTC)
	fmt.Println("The second time is", secondTime)

	fmt.Println("First time before second?", firstTime.Before(secondTime))
	fmt.Println("First time after second?", firstTime.After(secondTime))

	fmt.Println("Second time before first?", secondTime.Before(firstTime))
	fmt.Println("Second time after first?", secondTime.After(firstTime))
}
```

更新并运行程序：

```shell
go run main.go
```

输出看起来像这样：

```
Output
The first time is 2021-08-15 14:30:45.0000001 +0000 UTC
The second time is 2021-12-25 16:40:55.0000002 +0000 UTC
First time before second? true
First time after second? false
Second time before first? false
Second time after first? true
```

因为代码使用的是 UTC 时区的确定的日期，所以你看到的输出应该与上面的输出一致。你会看到，当 `firstTime` 调用  `Before` 方法并与 `secondTime` 比较时，它说 `2021-08-15` 在 `2021-12-25` 之前是 `true`。当 `firstTime` 调用 `After`方法并与 `secondTime` 进行比较时，它说 `2021-08-15` 在 `2021-12-25` 之后是 `false`。改变顺序来调用 `secondTime` 上的方法，显示出相反的结果，正如预期。

用 `time` 包比较两个日期和时间的另一种方式是 `Sub` 方法。`Sub` 方法用一个日期减去另一个日期，并使用一个新的类型 `time.Duration` 返回一个值。与代表绝对时间点的 `time.Time` 值不同，`time.Duration` 值代表了时间间隔。例如，"一小时内" 是一个时间间隔，因为它会因当前时间的不同而表达出不同的意思，但 "中午" 代表一个特定的、绝对的时间。Go 在很多地方使用 `time.Duration` 类型，比如你想定义一个函数在返回错误之前应该等待多长时间，或者在本例中，你需要知道一个时间和另一个时间之间有多久。

现在，更新你的 `main.go` 文件，对 `firstTime` 和 `secondTime` 值使用 `Sub` 方法并打印出结果：

> projects/datetime/main.go

```go
...

func main() {
	firstTime := time.Date(2021, 8, 15, 14, 30, 45, 100, time.UTC)
	fmt.Println("The first time is", firstTime)

	secondTime := time.Date(2021, 12, 25, 16, 40, 55, 200, time.UTC)
	fmt.Println("The second time is", secondTime)

	fmt.Println("Duration between first and second time is", firstTime.Sub(secondTime))
	fmt.Println("Duration between second and first time is", secondTime.Sub(firstTime))
```

保存并运行程序：

```shell
go run main.go
```

输出看起来像这样：

```
Output
The first time is 2021-08-15 14:30:45.0000001 +0000 UTC
The second time is 2021-12-25 16:40:55.0000002 +0000 UTC
Duration between first and second time is -3170h10m10.0000001s
Duration between second and first time is 3170h10m10.0000001s
```

上面的输出说两个日期之间有3170小时、10分钟、10秒和100纳秒，输出中有几个需要注意的地方：首先是第一个和第二个时间之间的时间间隔是一个负值。这说明第二个时间在第一个时间之后，类似于 `0` 减 `5` 得到 `-5`。第二是，时间间隔的最大计量单位是小时，所以它不会分解成天或月。由于每个月的天数并不一致，而且 "日" 在夏令时的转换中可能有不同的含义，一小时是最准确的度量，不会有波动。

在本节中，你更新了你的程序，用三种不同的方法比较两个时间。首先，你使用 `Before` 和 `After` 方法来判断一个时间是在另一个时间之前还是之后，然后你使用 `Sub` 来计算两个时间之间有多长。不过，获取两个时间之间的时间间隔并不是 `time` 包使用 `time.Duration` 的唯一方式。你还可以用它来增加或减少 `time.Time` 值中的时间，我们将在下一小节中学到。

## 增减时间

在编写应用程序时，使用日期和时间的一个常见操作是根据某个时间来生成一个过去或未来的时间。它可以用来实现一些功能，比如确定一个订阅的下一次续订时间，或者确定自某些值被检查以来已经过了一定的时间。无论是哪种方式，Go 的 `time` 包都提供了相应的处理方式。不过，要根据你已经知道的日期找到另一个日期，你需要能够自行创建 `time.Duration` 值。

创建 `time.Duration` 值类似于你在纸上写下一个时间间隔，只是乘了个时间单位。例如，要创建一个代表若干小时的 `time.Duration`，你可以用 `time.Hour` 值乘你希望的小时数：

```go
oneHour := 1 * time.Hour
twoHours := 2 * time.Hour
tenHours := 10 * time.Hour
```

声明更小的时间单位的方式也类似，例如使用 `time.Minute`,  `time.Second` 等：

```go
tenMinutes := 10 * time.Minute
fiveSeconds := 5 * time.Second
```

一个时间间隔也可以加到另一个时间间隔上，得到时间间隔的总和。想看看实际效果的话，请打开你的 `main.go` 文件，并更新它，声明一个 `toAdd` 时间间隔变量，并向它添加不同的时间间隔：

> projects/datetime/main.go

```go
...

func main() {
	toAdd := 1 * time.Hour
	fmt.Println("1:", toAdd)

	toAdd += 1 * time.Minute
	fmt.Println("2:", toAdd)

	toAdd += 1 * time.Second
	fmt.Println("3:", toAdd)
}
```

更新、保存并运行程序：

```shell
go run main.go
```

输出看起来像这样：

```
Output
1: 1h0m0s
2: 1h1m0s
3: 1h1m1s
```

看看输出，你会发现打印的第一个时间间隔是1小时，对应于你代码中的 `1 * time.Hour` 。接下来，你把 `1 * time.Minute` 添加到 `toAdd` 值中，显示为1小时1分钟的值。最后，你把 `1 * time.Second` 加到 `toAdd` 中，结果为 `time.Duration` 值是1小时1分1秒钟。

也可以在一条语句中把持续时间加在一起，或者把一个持续时间从另一个持续时间中减去，都是可以的。

```go
oneHourOneMinute := 1 * time.Hour + 1 * time.Minute
tenMinutes := 1 * time.Hour - 50 * time.Minute
```

接下来，打开你的 `main.go` 文件，更新程序，使用这样的组合，从 `toAdd` 中减去1分1秒：

> projects/datetime/main.go

```go
...

func main() {
	
	...
	
	toAdd += 1 * time.Second
	fmt.Println("3:", toAdd)
	
	toAdd -= 1*time.Minute + 1*time.Second
	fmt.Println("4:", toAdd)
}
```

保存并运行程序：

```shell
go run main.go
```

输出看起来像这样：

```
Output
1: 1h0m0s
2: 1h1m0s
3: 1h1m1s
4: 1h0m0s
```

在输出中新增加的第四行显示，你在新代码的末尾减去了 `1 * time.Minute` 和 `1 * time.Second` 的总和，他们如期运行，最后又变回了原来的一小时零分零秒。

使用这些时间间隔并配合 `time.Time` 类型的 `Add` 方法，可以使你基于一个已有的时间值，获取该时间之前或之后的其他任意的时间点。想看实际例子的话，请打开你的 `main.go` 文件并更新它，将 `toAdd` 值设置为24小时，或 `24 * time.Hour`。然后，在 `time.Time` 值上使用 `Add` 方法，看看24小时之后的时间是多少：

> projects/datetime/main.go

```go
...

func main() {
	theTime := time.Date(2021, 8, 15, 14, 30, 45, 100, time.UTC)
	fmt.Println("The time is", theTime)

	toAdd := 24 * time.Hour
	fmt.Println("Adding", toAdd)

	newTime := theTime.Add(toAdd)
	fmt.Println("The new time is", newTime)
}
```

保存并运行程序：

```shell
go run main.go
```

输出看起来像这样：

```
Output
The time is 2021-08-15 14:30:45.0000001 +0000 UTC
Adding 24h0m0s
The new time is 2021-08-16 14:30:45.0000001 +0000 UTC
```

看一下输出结果，你会发现在 `2021-08-15` 的基础上加上24小时，得到的新日期是 `2021-08-16`。

要减去时间，你也需要使用 `Add` 方法，这稍微有点违反直觉。因为 `Sub` 方法是用来获取两个日期之间的时间差的，你通过使用 `Add` 方法，传入负的时间间隔，以减去时间。

打开 `main.go` 并更新它，将24小时的 `toAdd` 值改为负值：

> projects/datetime/main.go

```go
...

func main() {
	theTime := time.Date(2021, 8, 15, 14, 30, 45, 100, time.UTC)
	fmt.Println("The time is", theTime)

	toAdd := -24 * time.Hour
	fmt.Println("Adding", toAdd)

	newTime := theTime.Add(toAdd)
	fmt.Println("The new time is", newTime)
}
```

保存并运行：

```shell
go run main.go
```

输出看起来像这样：

```
Output
The time is 2021-08-15 14:30:45.0000001 +0000 UTC
Adding -24h0m0s
The new time is 2021-08-14 14:30:45.0000001 +0000 UTC
```

这次你会在输出中看到，新的日期不是在原来的时间上增加24小时，而是在原来的时间上减去24小时。

在这一节中，你用 `time.Hour` ,  `time.Minute` , 和 `time.Second` 来创建不同跨度的 `time.Duration` 值。你还使用 `time.Duration`值与 `Add` 方法来获得在原始值之前和之后的新的 `time.Time` 值。通过结合 `time.Now`、`Add` 方法和 `Before` 或 `After` 方法，你还可以让你的应用程序获得强大的日期和时间功能。

## 结论

* 在本教程中，你使用 `time.Now` 检索计算机上当前本地时间的 `time.Time` 值，然后使用 `Year`、`Month`、`Hour` 和其他方法来访问该时间的具体信息。

* 然后，你使用 `Format` 方法，使用自定义的格式和预定义的格式打印时间。
* 接下来，你使用了 `time.Parse` 函数来解析一个包含时间的 `string` 值，并从中提取时间值。
* 一旦你有了时间值，你就可以使用 `UTC` 和 `Local` 方法在 UTC 和你的本地时区之间进行时间转换。
* 最后，你使用 `Sub` 方法来获得两个不同时间之间的时间间隔，然后使用 `Add` 方法来找到相对于不同时间值的时间点。

使用本教程中描述的各种函数和方法对你的应用程序很有帮助，如果你有兴趣，也可以看看 Go [`time`](https://pkg.go.dev/time) 包的一些其他特性。
