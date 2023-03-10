# 去找 Rubyists，II

> 原文：<https://www.sitepoint.com/go-rubyists-ii/>

![go](img/f2af9cca36e9fc10c14ae092dd9374bb.png)

在本文中，我们将通过一个红宝石镜头来了解 Go 的更多特性。如果你错过了，你可以在这里找到第一部分，尽管你没有*有*去阅读它来理解这篇文章。

我们将浏览 Go 对传统 OOP 策略(例如继承)的回答。Go 本质上*消除了*传统的继承，提供了一个更加流畅的对象结构。然后，我们来看看一个名为 Web.go 的 Go web 框架。

让我们跳进来。

## 继承的谬误

特别是在重要的项目中，继承是非常常见的做法。但是，继承会使即使是简单的更改(众所周知，这是必然会发生的)的实现变得复杂，并且还会产生对代码复制的需求。

例如，如果你有一个名为“Horse”的类和两个子类“GallopingHorse”和“SadHorse”(也许这两个具有完全不同的特征；不仅仅是状态变化)。如果你有一匹悲伤的*和*飞奔的马怎么办？这意味着一些行为被锁在每个类中。

继承还有其他问题。它本质上使你的代码僵化，因为它涉及类型之间的关系。正如 Golang 的常见问题所指出的:

> “面向对象编程，至少在最著名的语言中，涉及了太多类型之间关系的讨论，这些关系通常可以自动导出。Go 采取了不同的方式。”

那么，Go 会做什么呢？

它使用接口。Ruby 没有内置接口的概念，但是你可以[让它们工作](http://metabates.com/2011/02/07/building-interfaces-and-abstract-classes-in-ruby/)。本质上，接口就像一个契约。如果一个名为“马”的类“实现”了名为“动物”的接口，动物接口说它必须有“吃()”和“睡()”的方法。你会问，这有什么意义？好吧，让我们拿出一点伪红宝石:

```
#this is Pseudo-Ruby. We're pretending that Animal is an "interface"
#and Horse is a class. 

def full_day(animal)
  animal.eat()
  animal.sleep()
end
```

首先，我们暂时假设如果“full_day”方法的参数没有实现“Animal”接口，Ruby 会拒绝运行这段代码。所以，我们可以有一个方法，只期待满足“动物”接口的东西；它并不特别在乎它是“马”、“鸭”还是“人”。因为真正的 Ruby 是一种动态类型的语言，所以接口的应用是不同的；不管“animal”是否有“eat”、“sleep”方法，代码都会正确地编译成字节码。然而，在 Go 这种静态类型语言中，接口会在编译时被验证。

理论够了。让我们建立几个例子。

## 动物

首先，我们需要某种对象。让我们给自己做一匹“马”:

```
package main

type Horse struct {
  name string
}

func main() {

}
```

写着`type Horse struct`的那行基本上是说“我在做一个新的类型，叫它马，应该是这种*结构*”。结构本质上是数据结构的集合(如字符串、整型、数组等)。)我们在 Ruby 中有非常相似的结构，除了它们免费提供给你 getter 和 setter 方法。考虑到这个结构，我们注意到这个结构有一条叫做马的“名字”的信息(注意，在 Go 中，数据的类型在数据的名字之后*)。*

现在，让我们为我们的马定义一两种方法:

```
package main

import (
  "fmt"
)

type Horse struct {
  name string
}

/* added a couple of methods here */
func (h *Horse) Sleep() {
  fmt.Println("zzz...") 
}

func (h *Horse) Eat() {
  fmt.Println("omnomnomnom")
}

func main() {

}
```

注意我们是如何定义方法的。它们完全在结构之外声明，与 Ruby 相反，Ruby 的方法是在类声明块中声明的。还有，他们前面有一个`(h *Horse)`。这告诉编译器这些是可以在`Horse`类上调用的方法，并且还为给定的方法提供了`h`，一个 Horse 的实例。

让我们修复动物界面:

```
package main

import (
  "fmt"
)

/* added an interface */
type Animal interface {
  Eat()
  Sleep()
}

type Horse struct {
  name string
}

func (h *Horse) Sleep() {
  fmt.Println("zzz...") 
}

func (h *Horse) Eat() {
  fmt.Println("omnomnomnom")
}

func main() {

}
```

虽然只有几行代码，但它描述了所有人的行为:他们吃饭和睡觉。

Go 中的接口没有被显式地实现。如果您以前编写过任何 Java 代码，您可能会看到类似这样的内容:

```
class Horse implements Animal
```

在 Go 中，只要已经写好了所需的方法，接口就自动实现了。所以，我们的`Horse`对象已经满足了`Animal`接口！在我们继续之前，让我们创建一个名为`George`的马的实例:

```
package main

import (
  "fmt"
)

type Animal interface {
  Eat()
  Sleep()
}

type Horse struct {
  //changed the name of the field from
  //"name" to Name
  //fields that begin with an uppercased letter
  //are public fields.
  Name string
}

func (h *Horse) Sleep() {
  fmt.Println("zzz...") 
}

func (h *Horse) Eat() {
  fmt.Println("omnomnomnom")
}

func main() {
  /* create an instance */
  george := new(Horse)
  george.Name = "George"

  george.Sleep()
  george.Eat()
}
```

这里有几件事需要注意。首先我把名为`name`的字段改成了`Name`。以大写字母开头的字段是公共的。考虑到`main`方法，我使用了`new`内置来创建对`Horse`的引用。最后，我们给它命名，调用它上面的`Eat`和`Sleep`方法。让我们把它变得更有趣一点。一个对所有动物都适用的方法怎么样？

```
func FullDay(a Animal) {
  a.Eat()
  a.Sleep()
}
```

如果我们用一个`FullDay(george)`调用传入 George，我们应该得到和以前一样的输出。除此之外，我们可以看到接口在 Go 中是如何工作的。你定义一个数据结构并附加一些方法到它上面，它所填充的接口将被自动确定。这使得你的代码库更加灵活，如果需要的话更容易修改。

## Web.go

既然我们已经很好地理解了抽象在 Go 中是如何工作的，我们可以看看 Web.go，一个非常棒的 Go web 框架。

首先，为了在您的系统上运行这些，您需要有一个工作的“Go 环境”。为了做到这一点，我鼓励您克隆[范例库](https://github.com/RubySource/web_go_examples)。然后，一旦您进入“webgo-examples/”目录，您就可以在您的 shell 中运行它:

```
export GOPATH=`pwd`
cd src
go get github.com/hoisie/web
```

这应该会给你一个 Web.go 框架的副本。如果您打开“main/hello-world.go”文件，您应该会看到以下内容:

```
package main

import (
    "github.com/hoisie/web"
)

func hello(val string) string { 
    return "hello " + val 
} 

func main() {
    web.Get("/(.*)", hello)
    web.Run("0.0.0.0:9999")
}
```

这是 Web.go 最简单的可能用例；一个小小的“你好”应用程序。可以看到，Web.go 和 Ruby 的 Sinatra 很像；一个包含路线和相应方法的文件。但是，Web.go 中的路由是通过正则表达式指定的。作为个人偏好，我发现 Sinatra 的做事方式更直接，更不容易出错，但有许多人更喜欢正则表达式，因为它们更强大。

如果您用“go build hello-world . go；。/hello_world”，您应该会看到一个服务器正在运行。导航到“localhost:9999”应该会返回一条“hello”消息。我们可以用稍微不同的方式写这个例子(借用 Web.go 文档):

```
package main

import (
    "github.com/hoisie/web"
)

func hello(ctx *web.Context, val string) { 
    ctx.WriteString("hello " + val)
} 

func main() {
    web.Get("/(.*)", hello)
    web.Run("0.0.0.0:9999")
}
```

请注意，`hello`方法的参数已经更改；现在我们有一个上下文方法传入到方法中。这个上下文对象非常有用:

```
package main

import (
    "github.com/hoisie/web"

    "fmt"
)

func logName(ctx *web.Context, val string) { 
  name := ctx.Params["name"]
  fmt.Println(name)
  ctx.WriteString("Hi, " + name)
} 

func main() {
    web.Get("/log_name?(.*)", logName)
    web.Run("0.0.0.0:9999")
}
```

如您所见，我们已经连接了一个路由`/log_name`，它读取一个名为`name`的参数，响应请求并打印出名称。如果你去`/log_name?name=Whateveryournameis`，你会受到友好的问候。

Web.go 对静态文件有很好的支持。如果您将名为`static`的文件夹添加到 web 应用程序的目录中，Web.go 将自动为匹配的文件名提供请求，(例如，“/css/index.css”对应于“static/css/index.css”)。

还有一套非常酷的工具，可以让我们构建一个流响应。Web.go 文档中包含了一个例子，但是没有太多的解释:

```
package main

import (
    "github.com/hoisie/web"
    "net/http"
    "strconv"
    "time"
)

func hello(ctx *web.Context, num string) {
    flusher, _ := ctx.ResponseWriter.(http.Flusher)
    flusher.Flush()
    n, _ := strconv.ParseInt(num, 10, 64)
    for i := int64(0); i < n; i++ {
        ctx.WriteString("<br>hello world</br>")
        flusher.Flush()
        time.Sleep(1e9)
    }
}

func main() {
    web.Get("/([0-9]+)", hello)
    web.Run("0.0.0.0:9999")
}
```

如果您运行这个并转到“/10”，您会得到一个不断向页面添加更多“hello world”的响应。这是如何工作的？本质上，服务器不是向客户机(即 web 浏览器)发回完整的响应，而是向它发送部分响应。如果你看一下代码，神奇的事情发生在“flusher”对象上。我们使用它来将我们的一些数据“刷新”到客户端，然后我们停留一会儿(用`time.Sleep`)并发送另一次刷新，等等。因此，我们使用`flusher`来错开响应，并将其分段发送出去。当然，这意味着服务器处理每个请求需要花费大量的时间，但是在某些情况下(不可否认，这种情况很少发生；如今，WebSockets 和 Pusher 可以让这种事情发生)的情况下，这可能是一个非常有用的技巧！

尽管 Web.go 非常棒，但我不认为它比得上 Sinatra 的简单；后者感觉起来更快更灵活(一般来说 Ruby 也是如此)。我认为很大一部分原因可能是 Go 的“写-编译-运行”循环，这在解释语言中是不存在的。然而，即使就代码本身而言，Go 似乎也比 Ruby 及其同类产品更冗长。

Web.go 确实展示了围棋的多功能性。它可以作为一种系统语言使用(我目前正使用它来处理分布式文件系统),也非常适合 web 开发，更不用说性能提升了。

## 结论

Go 是一门非常棒的语言。在我看来，它设法保留了动态语言的一些最佳特性，同时忠实于它的系统背景。

我目前正在做一个基于 Go 的项目，处理 Hadoop。我可以毫不犹豫地说，在这方面，我的围棋经历非常棒。至于 web 开发，我还没有用 Go 部署一个主要的应用程序，但是总体感觉还不错。正如我提到的，Web.go 没有 Sinatra 快，但它提供的一些功能在我的工具箱中为它留出了空间。

## 分享这篇文章