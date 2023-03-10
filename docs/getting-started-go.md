# Go 入门

> 原文：<https://www.sitepoint.com/getting-started-go/>

除非你这几年一直生活在岩石下，否则你会在几年前听说过 [Go(有时被称为 Golang)](http://golang.org/) ，一种源自谷歌的语言。

[围棋被定为:](http://golang.org/)

> …一种开源编程语言，可以轻松构建简单、可靠、高效的软件。

[引用另一个消息来源](http://en.wikipedia.org/wiki/Go_%28programming_language%29)，去

> 是一种静态类型的语言，语法松散地从 C 语言派生而来，增加了自动内存管理、类型安全、一些动态类型功能、额外的内置类型(如可变长度数组和键值映射)以及一个大型标准库。

它可用于大多数现代操作系统，包括 *Mac OS X* 、 *Linux* 、 *BSD* 和*微软 Windows* ，早在 2007 年，它就由 **Robert Griesemer** 、 **Rob Pike** 和 **Ken Thompson** 在谷歌构思和最初创建。这三个开发者都有令人印象深刻的血统。

[Robert Griesemer](http://en.wikipedia.org/wiki/Robert_Griesemer) 曾参与 Google 的 V8 JavaScript 引擎和 Java HotSpot 虚拟机；[罗布·派克](http://en.wikipedia.org/wiki/Rob_Pike)和[肯·汤普森](http://en.wikipedia.org/wiki/Ken_Thompson)都在贝尔实验室工作，实现最初的 UNIX 操作系统。

大多数编程语言，尤其是具有如此深厚的 C 语言传统的语言，可能很难入门。走在另一方面，则完全相反。在今天的教程中，我将向您展示如何开始使用 Google Go 进行开发，从安装到运行应用程序。

## 开始安装

无论你使用的是 Mac OS X、Linux 和 FreeBSD，还是 Windows，Go 都很容易安装和配置。在本教程中，我假设您有一个 UNIX/Linux 操作系统。当我第一次在 Mac 上安装 Go 时，我使用的是最新的包文件。

但是您可以使用以下命令从源代码轻松安装:

```
tar -C /usr/local -xzf go1.2.1.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
```

如果您不熟悉的话，这些工具会在您的文件系统中从`go1.2.1.linux-amd64.tar.gz`到`/usr/local/go`提取 go 文件的副本，并将其中的`bin`目录添加到您的配置文件路径中。

或者，如果你在 Windows 上，下载并运行 [MSI 安装程序](http://code.google.com/p/go/downloads/list?q=OpSys-Windows+Type%3DInstaller)。

当安装程序完成时，你会在`c:\Go`下找到安装的文件；安装程序应该已经将`c:\Go\bin`添加到您的`PATH`环境变量中。你可能想检查一下，只是为了确认一下。

## 配置您的环境

完成后，我们就有了开始所需的二进制文件。但是我们需要确保我们的环境也准备好了，这需要更多的步骤和一些背景知识。

Go 开发使用了**工作空间**的概念，其中包含源文件( **src** )、编译后的二进制文件( **bin** )和包( **pkg** )。

源文件目录结构通常模拟开放源代码库，如 Github。因此，典型的源目录可能如下所示:

```
 src
        github.com
        settermjd   
        sitepoint
                hello-world.go
```

你可以看到在`src`下，我链接到了我的 Github 库，名为`sitepoint`，其中有一个文件`hello-world.go`。

我可以拥有多个存储库，并链接到多个主机，包括[位桶](https://bitbucket.org/)和[代码库](http://www.codebasehq.com/)。通过采用这种方法，我相信 Go 本质上保持了代码的良好结构和组织。

由于这种结构，Go 编译器需要设置一个系统变量`GOPATH`，指向这个目录的根目录。因此，让我们创建 GOPATH 目录并设置环境变量。

在今天的教程中，我将在我的主目录中创建它。但是根据您通常保存代码文件的位置，您可以随意使用它。

运行以下命令将通过-p 开关一步创建所需的目录结构，并设置 GOPATH 环境变量。

```
mkdir -p /Users/settermjd/go/src/github.com/settermjd/sitepoint
export GOPATH=/Users/settermjd/go
```

如果您使用的是 Windows，不要忘记添加一个 GOPATH [环境变量](http://www.computerhope.com/issues/ch000549.htm)。完成后，我们就可以创建我们的第一个 Go 应用程序了。

## 一个简单的应用

为了简单起见，我只介绍了一小部分语言特性。但是在以后的文章中，我们将在这里已经介绍的基础上继续。

我们将通过一个带注释的应用程序来展示如何基于读取 JSON 字符串的内容以及一些条件逻辑来打印一个简单的语句，而不是查看一组语言特性。

在 Github 上可以找到下面[代码的未注释版本。您可以随意克隆它，并在本教程中逐步完成。但除此之外，让我们完成下面的注释版本。](https://github.com/settermjd/go-hello-world)

```
package main
```

每个 Go 应用程序都是由包组成的，使用 Java 引用，程序使用主包作为缺省包。

```
import ( 
    "encoding/json"
    "fmt"
    "strings"
)
```

正如我在教程开始时提到的，Go 有一个[相当大的标准库](http://golang.org/pkg/)。要访问该功能，您需要导入所需的特定包。

在这里，我导入了三个:`encoding/json`、`fmt`和`strings`。如果看起来很洋气，那真的不应该。在 PHP 和 C 中你会使用 includes(或 requires)，在 Python 中你会使用 imports，在 Ruby 中你会使用 require。

可用的软件包列表非常广泛。因此，如果你对其余的感到好奇，或者想要关于以上三个的更具体的信息，给自己拿一杯咖啡，然后[查阅文档](http://golang.org/pkg/)。全面的覆盖已经超出了本教程的范围。

```
type User struct {
    FirstName string `json:"first_name"`
    LastName string `json:"last_name"`
    Age int
    Languages []string `json:"languages"`
}
```

Go 不实现类，但是结构在提供这种功能上有很大的帮助，我们将在后面的教程中看到。目前，结构是存储命名信息集合的一种便捷方式。

在上面的例子中，我声明了一个名为`User`的结构。它有四个字段:*名*、*姓*、*年龄*和*语言*。这里我们可以看到一些核心 Go 数据类型的用法。名字和姓氏是字符串，年龄是整数，语言是字符串数组。

在每一个结尾，你都会看到`json:"first_name"`。这有助于将 struct 字段映射到 JSON 字符串中的一个字段，我们很快就会看到，这可能超出了 intro 示例的范围。

```
func printUserData(jsonData string, age int) string {
```

这里我们定义了一个名为`printUserData`的函数，它有两个参数，一个名为`jsonData`的字符串和一个名为`age`的整数，并将返回一个字符串。

```
var output string
```

这里我声明了一个名为`output`的变量，它的类型是 string。

```
res := &User{}
```

在这里，我声明了一个变量`res`，它将是我们之前定义的自定义用户结构的。这是一个将变量定义为集合类型并一次性初始化的例子。

```
json.Unmarshal([]byte(jsonData), &res)
```

这里我们调用了`json`包中的`Unmarshal`函数，传入了第一个参数 jsonData 和 res Struct 变量。

```
if res.Age > age {
    output = fmt.Sprintf("User %s %s, who's %d can code in the following languages: %s\n", res.FirstName, res.LastName,  res.Age, strings.Join(res.Languages, ", "))    
} else {
    output = fmt.Sprintf("User %s %s must be over %d before we can print their details", res.FirstName, res.LastName, age)
}
```

无论你的背景是 **PHP** 、 **Java** 、 **C/C++** 、 **Python** 、 **Ruby** ，还是 **JavaScript** ，这一部分看起来都很熟悉。有一个小小的改变。在 Go 中，`if`语句**不需要括号**，但是**需要花括号**。

这里我检查转换后的 JSON 对象中的人的年龄是否大于作为第二个函数参数传入的年龄。

如果是这样，我们打印出他们的全名，年龄和他们可以编码的语言。如果没有，我们打印一个不同的消息，说我们不能显示它们的细节。

这里我们看到了另外两个标准库包的例子:`fmt.Sprintf`和`strings.Join`。我特意选择了这些例子，因为它们可能用在你可能熟悉的最常见的 web 脚本语言中，尤其是 PHP。

`fmt.Sprintf`将用提供的占位符替换第一个参数字符串中的占位符。因为语言是一个数组，所以我使用了字符串。Join 函数，类似于 PHP 的内爆函数，将数组转换为字符串，用“‘，”分隔元素。

然后将结果赋给字符串变量 output。

```
 return output
}
```

在函数的最后，我们返回输出。

```
func main() {
    var age int = 24
    str := `{"first_name": "Matthew", "last_name": "Setter", "age": 21, "languages": ["php", "javascript", "go"]}`
    fmt.Println(printUserData(str, age))
}
```

从主包中的主功能启动 Go 应用程序。所以我实现了 main，将一个整型变量初始化为值 24，然后将一个字符串变量`str`初始化为一个简单的 JSON 字符串。

您可以看到它包含四个组件:

*   西方人名的第一个字
*   姓
*   年龄
*   语言

最后，我调用了`fmt.Println`方法，传递了对`printUserData`函数的调用，其中我传递了 str 和 age 变量。

## 编译代码

与 PHP、Ruby、Python 等不同，Go 是一种编译语言。因此，从您的项目目录，在我的例子中是`/Users/settermjd/go/src/github.com/settermjd/sitepoint`，运行以下命令:

```
go install
```

这将在`bin/`目录中编译一个 go 二进制文件，以包名命名为`sitepoint`。要运行它，只需像调用任何其他系统二进制文件或可执行文件一样调用它，例如

```
$GOPATH/bin/sitepoint
```

结果将是:

```
User  Matthew  Setter must be over 24 before we can print their details
```

## 最后

这是一个动手的例子，展示了如何使用 go 进行开发的基础。我希望这个例子能够很好地向您介绍围棋基础知识，并向您展示入门是多么容易。

我自己也比较新，正在积极学习。但是如果你对和我一起探索围棋感兴趣，那么在接下来的几周和几个月里，和我一起加入即将到来的教程，让我们一起学习这种令人兴奋、有趣而且非常有趣的语言。

如果您想了解更多信息，请查看以下优秀的示例:

*   [举例说明](https://gobyexample.com/)
*   [围棋博客](http://blog.golang.org/)
*   [新手 Go 程序员资源](http://dave.cheney.net/resources-for-new-go-programmers)

## 分享这篇文章