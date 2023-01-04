# 支持卢布主义者

> 原文：<https://www.sitepoint.com/go-rubyists/>

![go](img/f2af9cca36e9fc10c14ae092dd9374bb.png)

谷歌的优秀员工开发了一种很棒的语言，叫做 [Go](http://golang.org) 。乍一看，Ruby 和 Go 似乎充其量只是远亲。然而，他们互补的技能提供了一个完美的匹配。

这绝对值得任何 ruby 爱好者花时间看一看 Go，因为它带来的一些创新非常诱人。

对我来说，Go 是 C++和 Ruby 之间缺失的一环。尤其是在编写需要快速运行的服务器时，我经常选择 C++，并发现自己错过了 Ruby 的优点。我本来想写 Ruby，但是即使最近有了巨大的性能改进，Ruby 还是应付不来。

Go 填补了这个空白。它提供了像 Ruby 或 Python 这样的动态语言的感觉，但其性能可以与许多编译过的同类语言相媲美。

它还有一些真正独特的特性，我们将在本文中探讨。让我们跳进来。

## 为什么要去？

当编写服务器时，当需要处理大量客户端时，为每个客户端打开一个线程作为并发的手段(如果这听起来像是胡言乱语，没关系，继续读下去)是非常可怕的。选项是使用称为非阻塞 IO 的东西(对节点群竖起大拇指)。但是，即使跨 Unix-y 操作系统(例如 Linux、Mac OS X 等。)，高效处理非阻塞 IO 的机制有所不同。然后，除了所有这些复杂因素之外，还有 C 本身。我并不反对 C 语言用于嵌入式设备或者运行速度远远超过开发时间的地方。但是，作为一门日常语言，C 并不适合我。

Go 提供了惊人的并发原语、漂亮的语法、丰富的默认库和快速的编译器。它解决了我在 C 语言中遇到的问题(在某种程度上也是 C++)。即使你的代码库变得更大，使用起来也很有趣。

在本文中，我将快速浏览该语言的基础知识，并经常指向文档。主要的焦点是突出这种语言的创新特征，使它如此独特。

## 单调乏味

围棋意味着没有惊喜；应该很容易就来了。下面是基本的代码布局:

```
package main

func main() {

}
```

调用 main 函数来启动我们。让我们试试“你好，世界:”

```
package main

import "fmt"

func main() {
  fmt.Println("Hello, world!")
}
```

在 Go 中，用于打印和扫描的模块称为“fmt”。与 Ruby 不同，默认情况下不包含它，所以我们在文件顶部添加了一个“import”语句来包含它。来自`fmt`模块的方法`Println`将打印出我们传入的字符串和一个换行符(类似于 Ruby 中的`puts`)。请注意，Go 中的公共方法以大写字母开头。

一些快速循环:

```
package main

import "fmt"

func main() {
  //the basic for loop
  for i:=1; i < 100; i++ {
    fmt.Println(i)
  }
}
```

Go 和 Ruby 对`for`循环的想法完全不同。Go 版本或多或少类似于 c。您定义一个变量，检查一个条件，并指定在一次循环迭代结束时做什么(在本例中，递增`i`)。这基本上是 Golang 定义的唯一一种循环。幸运的是，它的用途极其广泛。例如，这是一个永久循环:

```
for {
}
```

我鼓励你查阅一些(关于`for`的文档)。

注意，当我们在`for`循环中设置`i`的值时，我们不使用“=”，而是使用“:=”。这里有一个区别的例子:

```
package main

import "fmt"

func main() {
  //defines the variable a
  a := 5
  fmt.Println(a)

  //sets a different value to a
  a = 10
  fmt.Println(a)

  //another way to define a variable
  var b int
  b = 15
  fmt.Println(b)
}
```

在`main`函数的第一个块中，变量`a`被声明和赋值。在第二个例子中，值被重新分配，所以使用了`=`。原因是 Go 实际上是一种静态*类型的语言，不像 Ruby 是动态类型的。因此，编译器必须知道变量在哪里声明，它们的内容在哪里被替换。这一点通过`main`函数的第三部分变得很清楚，它使用`var`关键字显式声明并设置变量的值。*

最后，与 Ruby 中的数组相似，Go 中也有切片。它们属于类型`[]type`，其中`type`表示我们希望 slice 返回的对象类型。实例化它们有点奇怪:

```
package main

func main {
  ///this creates a slice of integers with length 15
  mySlice := make([]int, 15)
}
```

我们必须使用`make()`调用来生成切片。

这可能是对 Go 的一些最基本特征的快速浏览；我宁愿花更多的时间在一些有趣的功能上，而不是那些[是](http://golang.org/doc/effective_go.html) [有据可查](http://tour.golang.org/#1) [非常](http://golang.org/ref/spec) [好](http://golang.org/doc/)的基本语法上。

我们去看看戈鲁丁斯。

## 戈里普斯

编写并发代码很难。编写并发网络代码更加困难。问题是传统的线程不能很好地扩展，而且一旦你有几个线程在运行，它们就非常难以处理。Go 团队着手解决这个问题，生产了 goroutines。

本质上，goroutines 是轻量级并发机制，它们使用称为通道的结构相互通信。它们使用起来非常简单:

```
package main

import "fmt"

func wait() {
  //wait around with a forever loop
  for {
  }
}

func main() {
  go wait()
  fmt.Println("We didn't wait because it was called as a goroutine!")
}
```

我们有方法`wait`，它应该是一个永远的循环。但是，我们称这个方法为`go wait()`，而不仅仅是`wait()`。这告诉 Go 我们希望它作为一个 goroutine 被调用，并异步运行它！运行程序不会产生永久循环，因为循环是在后台运行的。

因此，Go 在语言中内置了并发性，也就是说，它有并发性*原语*。但是，这有什么意义呢？不需要包含库或模块似乎没什么大不了的。但是，goroutines 从根本上不同于线程，因为它们更加轻量级。还记得为什么不应该编写每个客户端使用一个线程的服务器吗？对于 goroutines，情况有所不同:

```
package main

import (
  "fmt"
  "net"
)

//notice that in the arguments, the name of
//the variable comes first, then comes the 
//type of the variable, just like in "var"
//declarations
func manageClient(conn net.Conn) {
  conn.Write([]byte("Hi!"))
  conn.Close()
  //do something with the client 
}

func main() {
  //we are creating a server her that listens
  //on port 1337\. Notice that, similar to Ruby,
  //a method can have two return values (although
  //in Ruby, this would be an array instead)
  listener, err := net.Listen("tcp", ":1337")
  for {
    //accept a connection
    connection, _ := listener.Accept()
    go manageClient(connection)
  } 
}
```

哇哦。这看起来可能有点复杂，但是这个想法非常简单。让我们一步一步地分解它。

查看`main`函数，首先调用`net.Listen`方法，该方法返回*两个*值(在 Go 中，你不限于一个值，类似于 Ruby 的数组解包)服务器连接和错误。然后，进入服务器的主循环，它只是坐在那里，用`server.Accept`调用监听请求。该呼叫“挂起”,直到客户来连接。然后，一旦我们连接上了，就把连接对象传入一个`manageClient`，但是调用它作为一个 goroutine！因此，服务器准备好处理下一个客户机。

最后，注意关于`manageClient`方法的一些事情。首先，在参数列表中，变量名在前，类型在后。这或多或少是围棋创作者们做出的一种文体选择；使用这种语言一周左右后，你甚至不会注意到它。

在方法体中，写下“Hi！”消息，然后关闭套接字。

因此，通过几行代码，我们已经为一个简单的服务器写了一个合理的基础。不需要太多的努力，您就可以将它转换成一个 HTTP 代理(如果您实现了一些缓存，这是加分的)。Goroutines *允许*我们这样做。他们实际上不是*只是*轻量级线程；幕后有很多神奇的东西，使得在 goroutines 中编写简单的命令式代码成为可能。

## 频道

Goroutines 本身是有用的，但是它们的效用随着通道的概念而被大量放大。这些作为 goroutines 和主进程之间的通信机制。让我们看一个简单的例子。

```
package main

import (
  "fmt"
)

var eventChannel chan int = make(chan int)

func sayHello() {
  fmt.Println("Hello, world!")

  //pass a message through the eventChannel
  //it doesn't matter *what* we actually send across
  eventChannel < - 1
}

func main() {

  //run a goroutine that says hello
  go sayHello()

  //read the eventChannel
  //this call blocks so it waits until sayHello()
  //is done
  <- eventChannel
}
```

我们有一个名为`sayHello`的 goroutine 打印出一条“Hello，world”消息。但是，注意`eventChannel`的声明。本质上，我们已经声明了一个整数通道。我们可以将内容发送到这个通道中，代码的其他部分可以从这个通道中读取，使它成为一种通信方法。在`sayHello`法中，把`eventChannel pushes the integer` `1`变成了`eventChannel`。然后，在`main`方法中，我们从 eventChannel 中读取。

这里有一点很重要:默认情况下，从通道*读取会阻塞*，所以它会一直等待，直到有东西可以从 eventChannel 读取。

让我们来看看更复杂一点的东西:

```
package main

import (
"fmt"
)

var logChannel chan string = make(chan string)

func loggingLoop() {
  for {
    //wait for a message to arrive
    msg := < - logChannel

    //log the msg
    fmt.Println(msg)
  }
}

func main() {
  go loggingLoop()

  //do some stuff here
  logChannel <- "messaged to be logged"
  //do other stuff here
}
```

这里，我们定义了一个`main`循环，它围绕着监听事件，即`loggingLoop`。当它从`loggingChannel`接收到要记录的信息时，它会将其打印出来。这是一种非常常见的设计，尤其是当事件循环中要保存一些状态时。

因此，通过几行代码，我们已经在 main 函数和 goroutines 之间建立了通信。由于锁定、竞争条件等问题，共享内存传统上一直是开发人员的噩梦。有了 Go，渠道的概念大大降低了大部分传统问题的几率。此外，Go 的通道是语言固有的一部分；不仅仅是图书馆附带的东西。

与 Ruby 相比，Go 的 goroutines *实际上*在后台运行，使用该语言的“默认”实现(MRI Ruby 完全在单线程中运行，因此它不能提供“真正的”并行性)。其次，Ruby 附带了一个线程实现，但是使用它并不容易。事实上，[代理](https://github.com/igrigorik/agent)库试图将 goroutines 的一些细微之处引入 Ruby 世界。

## 包装它(暂时)

在这篇文章中，我们已经讨论了很多内容。我们浏览了 Go 代码的非常非常基本的语法，然后很快进入并发机制。

请继续关注第 2 部分，我们将更深入地研究语法，并讨论 Go 带给我们的其他一些令人惊叹的特性。

## 分享这篇文章