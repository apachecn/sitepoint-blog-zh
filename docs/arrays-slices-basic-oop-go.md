# Go 中的数组、切片和基本 OOP

> 原文：<https://www.sitepoint.com/arrays-slices-basic-oop-go/>

最近我写了一篇关于使用 Go 的介绍，看看它是什么，如何安装它，以及如何准备好您的环境。本文以一个小应用程序结束，该应用程序涵盖了*变量*、*输出*、*函数*和*结构*的 Go 基础知识。

在今天的文章中，我们将在这个基础上，通过查看一些新概念；具体来说:
–数组&切片
–贴图
–方法

## 阵列和切片

在 Go 中有很多方法可以初始化一个数组。让我们看一下 5 个例子:

[golang]
var numberList[5]int
var string list[10]string
var float 64 list[15]float 64
[/golang]

这里，我们创建了三个不同类型(int、string 和 float64)和不同大小(5、10 和 15)的空数组。

[golang]
x := [5]float64{ 98, 93, 77, 82, 83 }
[/golang]

这里，我们创建了一个数组，5 个元素大小为 float64 类型，同时初始化它。

y := […]int{ 98，93，77，82，83 }
[/golang]

这里，我们创建了一个 int 类型的数组，但是没有指定大小。相反，我们传入了`...`和一系列值。当以这种方式初始化数组时，它将根据所提供的元素数量来确定大小。

如果你熟悉动态语言，比如 *Python* 、 *PHP* 和 *Ruby* ，你将习惯于创建固定大小的数组，使用混合或相同类型的元素，但也能够动态地添加和删除元素。这里有一个 PHP 的例子:

```
$userList = array("Matthew", "Bruno", "Peter", "Chris");
unset($userList[0]);
$userList = array_merge(
    $userList, array("Jaco", "James", "Michael")
);
var_dump($userList); //"Bruno", "Peter", "Chris", "Jaco", "James", "Michael"
```

我们将`$userList`初始化为一个数组，包含四个名字，然后移除第一个元素，并在末尾使用`array_merge`添加三个新的元素。不幸的是，我们不能在 Go 中做到这一点，因为数组的大小总是固定的。这里有一个尝试这样做的例子:

[golang]
var buffer[5]string
buffer[0]= " Robert "
buffer[1]= " Michael "
buffer[2]= " James "
buffer[3]= " Aaron "
buffer[4]= " Simon "
buffer[5]= " Alex "
[/golang]

我们首先将一个新变量`buffer`初始化为一个 5 元素的字符串。然后，我们试图故意添加 6 个元素，超出了它的容量。如果我们尝试运行它，我们会收到以下错误:

```
# command-line-arguments
invalid array index 5 (out of bounds for 5-element array)
```

与动态语言不同，在 Go 中，数组的大小是其定义的一部分；所以`var buffer [5]string`和`var buffer [6]string`并不像你可能预料的那样是一回事。

这真的不是问题，因为 Go 有切片的概念。正如 Rob Pike(Go 的创始人之一)所说:

> 数组是围棋中的重要组成部分，但就像建筑的基础一样，它们通常隐藏在更可见的组件下面。

更明显的组成部分之一是切片。切片很像我们在 *PHP* 、 *Ruby* 和 *Python* 中习惯使用的东西。切片提供了处理数组的能力，但是是动态的、可变的。

然而，在我学习围棋的过程中，有一件事让我有点困惑，那就是这个定义有点太简单了。这里有一个更具体的定义，来自有效的 Go 书:

> 切片包含对基础数组的引用，如果将一个切片分配给另一个切片，两个切片都引用同一个数组。

乍一看，这似乎有点奇怪。对我来说的确是这样，因为我已经纯粹用动态语言工作了一段时间。但过一会儿就说得通了。

这对于数组和存储片来说都有点仓促，所以让我们稍微慢下来，更详细地看一下这两者。具体来说，让我们看看以下 3 件事:

1.  从现有数组(或切片)创建切片
2.  长出一片
3.  修改切片

## 从现有数组(或切片)创建切片

好了，让我们用缓冲区数组创建一个切片。看看下面的代码:

[golang]
small _ buffer:= buffer[1:4]
[/golang]

这样做的目的是通过指定`[1:4]`来创建一个新的切片`small_buffer`，它由元素 2–4 组成。1 指定开始的元素，4 指定向上的元素，但不包括。因此，我们有元素 1，2 和 3。

我们不需要指定这两个参数。如果我们只指定了`:4`，那么我们将从 0 开始，在元素 4 结束。如果我们只指定了`2:`，我们将从元素 3 开始，在缓冲区的末尾结束。所以，相当灵活。

## 修改切片

正如我前面提到的，切片链接到组成它们的底层切片或数组。因此，如果您更改了一个切片，它会更改底层数组或切片。让我们看一个例子，看看结果。

[golang]
small _ buffer[1]= " Rodriguez "
[/golang]

这样做之后，buffer 将包含`"Robert", "Michael", "Rodriguez", "Aaron", "Simon", "Alex"`，small_buffer 将包含`"Michael", "Rodriguez", "Aaron", "Simon", "Alex"`。因此，您可以看到，除了修改切片之外，它还修改了底层数组。

## 长出一片

如果你想增长一个数组呢？有一个内置的方法 append，它使这变得非常简单。它所做的是从两个或多个现有切片中生成一个切片，使用起来非常简单。让我们看一个例子:

[golang]
//扩展现有切片
g := []string{"John "，" Paul"}
h := []string{"George "，" Ringo "，" Pete"}
y := append(g，h…)
[/golang]

在这里，我们创建了两个切片:`g` & `h`，它们分别包含了完整的披头士合奏。但是我们可以调用 append，传入 g 和 h，初始化一个新的变量 y 作为两者的组合。你注意到`...`跟着 h 了吗？这将`h`扩展为一组参数，它们被单独附加到 g 的末尾。

如果这不太有意义，看看变量函数的链接[。结果是包含["John "、" Paul "、" George "、" Ringo "、" Pete"]的切片。有一点要注意，如果我们修改 y，它不会影响 g 或者 h，因为它们没有联系。](https://gobyexample.com/variadic-functions)

## 地图

好了，现在让我们看看地图。继续我的动态语言类比，你可能习惯于在 PHP 中使用*关联数组*，在 Python 中使用*字典*，或者在 Ruby 中使用*散列*。嗯，围棋里的一张地图真的很像。本质上，它只是一个命名的键/值对列表(或集合)。

与动态语言的主要区别在于键和值类型是静态的，而不是动态的。一旦你指定了它们的类型，你就可以在你的地图中使用这些类型。这是第一个例子，所以你可以明白我的意思。

【golang】
导入(
【琴弦】
)

var retval = map[string]int { }
my homeland:= "海湾边的布里斯班"

var 令牌=字符串。fields(my homeland)
为 I:= 0；我< len(代币)；i++ {
retval[tokens[I]]= len(tokens[I])
}
[/golang]

我所做的是初始化一个新的变量，`retval`作为一个空映射，它将包含字符串。然后，我初始化了一个新的字符串变量，`myHomeTown`。接下来，使用字符串包中的`Fields`方法，我标记了`myHomeTown`的值。

Fields 方法通过用空格分割字符串来返回一个数组。如果这有助于使它更清楚的话，`tokens`可以这样初始化:

[golang]
tokens:=[4]string { " Brisbane "，" By "，" The "，" Bay"}
[/golang]

然后我们遍历令牌，将当前索引的值作为键，将其长度作为值。运行该代码将输出以下内容:

地图[布里斯班:8 By:2 The:3 Bay:3]
[/golang]

这是一种方法。从一开始就用值初始化地图怎么样？看看下面的例子:

[golang]
类型用户结构{
名称，客户端字符串
年龄 int

var users = map[string]User {
" settermjd ":{ name:" Matthew Setter "，client: "Sitepoint "，年龄:38 }，
}
fmt。Println(用户)
[/golang]

首先，我创建了一个名为`User`的新结构，包含了*姓名*、*客户*和*年龄*的属性。然后我创建了一个 Map，它有一个字符串键和一个用户结构作为它的值。

您可以看到，我能够一次创建地图并初始化第一个用户元素；相当方便。这样，我们就拥有了静态语言的能力和动态语言的灵活性。

## Go 中的方法和 OOP

如果你来自一个动态的语言背景或者来自像*这样的语言。Net* 、 *C++* 等等，你会期望能够使用面向对象(OOP)的方法进行开发。然而，Go 并没有提供对 OOP 的支持，这可能不是你所期望的。方法与*略有不同*。

还记得我们在这篇文章和上一篇文章中创建的结构吗？你需要做的第一件事是把你的结构看作一个类的基础。然后，您需要随后将方法添加到结构中。

这是通过在创建方法时指定方法的接收者来实现的。这一开始可能没什么意义。让我们来看一个例子。

【golang】
包总管

import(
" fmt "
" strings "
)
type User struct {
first name，LastName，email address string
Age int
}
[/golang]

这里我们扩展了前面的结构 User，添加了一个新的字符串属性`EmailAddress`。

[golang]
func(u * User)full name()string {
return fmt。Sprintf(
"%s %s "，u.firstname，u . last name
)
}
[/golang]

然后我们定义了一个函数`FullName`，它没有参数，但返回一个字符串。新的是`(u *User)`。这意味着用户将成为函数的接收者，有效地将函数添加到结构中。该函数本身只是通过调用从`fmt`包导出的`Sprintf`来返回`FirstName`和`LastName`属性的串联。

[golang]
func main(){
u:= User { first name:" Matthew "，LastName: "Setter"}
fmtprintln(u . full name())
}
[/golang]

现在让我们使用它。在上面的代码中，我们实例化了一个新的用户结构，只指定了`FirstName`和`LastName`属性。然后我们对它调用了`FullName()`，将方法调用传递给`fmt.Println()`，它打印出`Matthew Setter`。

## 包扎

就是这样！我们已经介绍了*变量*、*输出*、*函数*和*结构*的基础知识，然后逐步查看*数组*、*切片*、*映射*和函数。

我意识到，在某些方面，Go 不像动态类型语言那样简单，而且确实对你有更多的限制。但是另一方面，它给了你更多的力量，只需要很少的额外努力。

在评论里说说你的看法。我错过了什么吗？你会用不同的方式处理这件事吗？

## 分享这篇文章