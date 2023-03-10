# 围棋中的函数

> 原文：<https://www.sitepoint.com/closer-look-functions-go/>

函数是任何编程语言的基本构件之一。然而，不同语言的特性集和它们所扮演的角色有很大的不同。在本文中，我们将看看 Go 在函数方面提供了什么。我们将发现函数是 Go 中的一等公民，并为开发人员提供非常丰富的功能集——包括一些起初不明显的有趣细节。让我们从基础知识开始。

## 争论

Go 中的函数可以接受任意数量的类型化参数。所有参数都是必需的，因为没有可选参数的概念。同样重要的是要知道，参数不是通过引用传递的，而是被复制到函数中(有几个值得注意的例外，我们很快就会谈到)。这意味着如果参数在函数内部被修改，它不会对原始值有任何影响。避免这种情况的唯一方法是传递一个指向函数的指针。如果您来自 PHP、Ruby 或 JavaScript 等动态语言，那么指针的概念可能对您并不熟悉。然而，你应该隐式地使用指针，因为这些语言通过引用传递(一些)参数。指针的基本概念很简单:当你在程序中声明一个变量时，它存在于内存中的某个位置。指针只包含那个位置的地址(它们“指向”变量)。这意味着通过一个指针，你可以得到实际值并修改它。

现在，回到函数。如前所述，大多数参数都被复制到函数中。如果指针被复制，它仍然指向内存中的同一个地址，所以你可以修改这个值。一个简单的例子:

[golang]
var counter int
func increment(I int){
I = I+1
}
counter = 0
increment(counter)
fmt。Printf("%v "，counter) //打印 0
[/golang]

和...相对

【golang】
var counter int
func increment(I * int){
* I = * I+1
}
counter = 0
increment(&counter)
fmt。Printf("%v "，counter) //打印 1 个
[/golang]

`&`创建一个指针，`*`解引用它(返回指针指向的值)。

还记得我提到过几乎所有的东西都是通过值传递的吗？重要的是要知道，地图，切片和渠道是不同的，这可能是相当混乱的第一。Golang FAQ 对这个设计决策背后的推理有一个详细的[解释](http://golang.org/doc/faq#references)。另一件事是，有接口类型的接收方或参数没有加上`*`，但它们仍然可能是指针！

## 返回值

在 Go 中，函数也可以有返回值。如果没有指定，该函数将不返回任何内容(与其他语言相反，在其他语言中，您可能会得到`nil`或`undefined`，它们实际上是*或*)。事实上，试图使用没有返回值的函数的结果是一个编译错误。

一个更有趣的特性是 Go 允许多个返回值。这是大多数流行的 web 编程语言所缺乏的，事实证明这非常方便。在 Go 的标准库中，它通常用于错误处理。考虑`Writer`接口:

【golang】
type Writer 接口{
Write(p []byte) (n int，err error)
}
[/golang]

如果`Write`成功，`n`将是写入的字节数，`err`将是`nil`。在写入失败的情况下，`n`将为`0`，而`err`将保存发生的错误。消费者代码可以通过以下方式使用它:

【golang】
如果 n，err := file。写(p)；呃！= nil {
fmt。Println("无法写入。")
}
[/golang]

`Writer`接口展示了关于返回值的另一个有趣之处:它们可以预先命名。这消除了在函数内部初始化变量的需要。这些被命名的变量被自动初始化为它们的零值，并且可以在函数中随时通过一个简单的`return`语句返回。

## 接收器

到这里为止，我们已经讨论了正则函数。如果你熟悉面向对象语言，你会知道它们有一个类似于函数的概念，叫做方法，它基本上是一个函数，但是在对象的上下文中起作用。

Go 通过接收器提供这种功能。但是，有一点小小的不同，因为任何东西都可以是接收者。它可以是一个结构，但也可以只是一个整数。同样，接收者可能是一个值或者一个指针。

以定义在类型`User`(是一个结构)的**值**上的方法`Name`为例:

[golang]
type User struct {
first Name string
last Name string
}
func(User 用户)Name()string {
return User . first Name+" "+User . last Name
}
Peter:= User { first Name:" Peter "，lastname: "Pan"}
fmt。Println(彼得。Name()) //打印“彼得潘”
[/golang]

这里，每当调用`Name()`时，就复制`User`结构。通常，使用指针接收器更好。此外，如果要修改接收器，也有必要这样做，例如:

[golang]
func(User * User)swap name(){
first name:= User . first name
User . first name = User . last name
User . last name = first name
}
Paul:=&User { first name:" Paul "，lastname: "Panther"}
paul。SwapName()
fmt。Println(保罗。Name()) //打印" Panther Paul"
[/golang]

如果这个方法是在接收者`user User`上定义的，那么名字只会在函数内部交换，`fmt.Println`会打印出`Paul Panther`。

如果我们尝试调用 use `peter.SwapName`会发生什么？`peter`的类型是`User`(一个值)，然而接收者的类型是`*User`(一个指针)。这有用吗？有趣的是，确实如此。Go 足够聪明，可以创建并传递一个指向`User`值的指针，然后函数对该指针进行操作，修改`peter`变量。

## 可怕的空指针

在阅读关于接收者的文章时，你可能会问自己的另一个问题是，如果接收者是`nil`会发生什么？这是许多语言中的一个常见问题，我敢肯定，在你作为程序员的生活中，你曾经试图在一个对象上调用一个方法，而结果一点也不好。然后，你在代码中任何可能出现空指针的地方增加了保护措施，最终得到了非常难看的代码…

去救援！假设我们有一个返回第一个用户的`FirstUser()`方法。然而，我们还没有任何用户，所以最初的实现将是:

[golang]
func first User()* User {
return nil
}
[/golang]

现在，如果我们想在代码中获得第一个用户的名字，我们可以这样写:

[golang]
name := FirstUser()。Name()
[/golang]

有了这样的代码，并且因为现在`FirstUser()`返回`nil`，Go 将会崩溃。解决这个问题最简单的方法是添加如上所述的安全措施，但这看起来并不太好:

【golang】
用户:= FirstUser()
如果用户！= nil {
姓名:=用户。Name()
}
[/golang]

在 Go 中，我们可以用一种更好的方式来处理这个问题:在函数内部。我们可以这样做，因为在 Go 中，`nil` s 可以被键入:

[golang]
func(u * User)Name()string {
if u = = nil {
return " "
}
return User . first Name+" "+User . last Name
}
[/golang]

与其他语言不同，在其他语言中，你不能在`nil`对象上调用任何方法，Go 允许你调用指针接收器上的函数。因此，如果可能出现接收者是`nil`的情况，请确保在函数内部处理它，这样就不需要外部的保护措施，从而减少消费者代码的混乱。

## 关闭

Go 函数的最后一个特征是，它们可以是匿名的，可以用作闭包，这与您可能从 JavaScript 中了解到的非常相似。

例如:

[golang]
func counter()func()int {
c:= 0
return func()int {
c+= 1
return c
}
}
[/golang]

`counter`函数的返回类型为`func() int`，这意味着它返回一个返回类型为`int`的函数。如果我们调用计数器函数并将其返回值存储在一个变量中，我们可以多次调用该变量(它保存一个函数值)，计数器将增加(因为内部函数可以访问同一个`c`变量):

【golang】
计数:= counter()
fmt。Println(count()) //打印 1 个
fmt。Println(count()) //打印 2 个
fmt。Println(count()) //打印 3 个
[/golang]

## 结论

函数是 Go 语言的主要组成部分。它们有一些不寻常的特性，比如多重返回值和复制传递，这些特性对程序的编写方式有很大的影响。通过接收器实现“方法”非常灵活，不仅允许典型的方法，如对`nil`的良好处理。然而，Go 函数还要求程序员彻底了解值和指针，以及何时使用它们。

## 分享这篇文章