# Go:用 Beego 构建 Web 应用程序

> 原文：<https://www.sitepoint.com/go-building-web-applications-beego/>

![Developing a web application with Beego - Part 1](img/21a55feda65b582789e62fba37d66cce.png "Developing a web application with Beego - Part 1")

## 介绍

您是一名 web 应用程序开发人员吗？您刚从动态语言如 *PHP* 、 *Python* 或 *Ruby* 中走出来，想知道如何使用它开发基于 web 的应用程序吗？您是否想知道如何以类似于您现有框架的方式进行开发，以便利用您现有的知识？

如果是这样，那么你可能已经做了一些搜索，无论是在*谷歌*、 *StackOverflow* 或其他网站，寻找一个框架来帮助你；而且你可能已经看到有很多选择，包括 [Beego](http://beego.me) 、 [Martini](http://martini.codegangsta.io) 和[Gorilla](http://www.gorillatoolkit.org)；除了 [net/http](http://golang.org/pkg/net/http/) 包。

在这四个当中，我已经试验了很多次的是 **Beego** 。我发现它的功能非常丰富，但并不过分复杂，所以我能够相对较快地掌握它。

Beego 不是普通的 web 开发工具包。除了提供大量功能外，它还建立在大量现有 Go 软件包的基础上，这使它能够提供:

*   完整的 ORM
*   贮藏
*   会话支持
*   国际化(i18n)
*   实时监控和重装
*   部署支持。

但是，尽管 Beego 和前面提到的动态语言的框架之间有许多相似之处，但它们之间有足够的差异，需要花一些时间才能变得真正高效和熟练。

此外，虽然 Beego 文档相当全面，但我觉得它有些地方遗漏了，所以我写了这个由两部分组成的系列来帮助克服这个问题，并帮助您学习 Beego 的基础知识。

在本系列中，您将看到它是一个多么优秀的框架，以及它为我们这些开发人员提供了多少帮助。具体来说，在第 1 部分中，我们将讨论:

*   安装 Beego 和命令行工具 Bee
*   创建项目
*   行动
*   视图/模板
*   按指定路线发送
*   请求参数

如果您想通读本系列的完整代码，[可以在 Github](https://github.com/settermjd/Learning-Beego) 上找到。请随意在网上浏览它，或者克隆它并进行实验。**我们走吧！**

在我们开始之前，请确保您的 GO 环境已经设置好。如果不是，或者你不确定我的意思，请查看 Bill Kennedy 的[入门围棋](https://www.sitepoint.com/getting-started-go/)或[这篇帖子，然后回来让我们继续。](http://www.goinggo.net/2013/06/installing-go-gocode-gdb-and-liteide.html)

## 1.安装 Beego

好，让我们从安装 Beego 开始。像许多框架和工具包一样，Beego 有内置的脚手架支持，通过命令行工具 **bee** 。蜜蜂可以:

*   创建新的应用程序
*   运行应用程序
*   测试应用程序
*   创建路线等

Bee 并不是运行 Beego 应用程序的唯一方式，但它是我将在这个由两部分组成的系列中讨论的一种方式。要安装它，运行`go get github.com/beego/bee`。

## 2.创建核心项目

安装完成后，从您的`$GOPATH`目录中运行以下命令，该命令将搭建应用程序，名为 **sitepointgoapp** :

```
bee new sitepointgoapp
```

这将显示类似于以下内容的输出:

[golang]
【信息】创建应用…
/Users/Matthew setter/Documents/workspace/Golang/src/sitepointgoapp/
/Users/Matthew setter/Documents/workspace/src/sitepointgoapp/conf/
/Users/Matthew setter/Documents/goapp/controllers/
/Users/Matthew setter/Documents/workspace/Golang/src/sitepointgoapp/models/
/Users/matT19

现在，您将看到已经创建了以下目录结构:

```
sitepointgoapp
    ├── conf
│   └── app.conf
├── controllers
│   └── default.go
├── main.go
├── models
├── routers
│   └── router.go
├── static
│   ├── css
│   ├── img
│   └── js
├── tests
│   └── default_test.go
└── views
    └── index.tpl
```

查看文件，我们有:

*   我们的引导文件`main.go`
*   核心配置文件`conf/app.conf`
*   默认控制器`controllers/default.go`
*   `tests/default_test.go`中的一组默认测试
*   `views/index.tpl`中的默认视图模板

好了，基本应用程序已经准备就绪，让我们开始运行吧。从项目目录`$GOPATH/src/sitepointgoapp/`中，运行以下命令:

```
bee run
```

这将加载我们的新应用程序。作为附带的好处，bee 还监视源文件的修改。如果检测到更改，bee 将自动重新加载应用程序。运行上面的命令后，您应该会看到类似下面的输出。

```
14-05-05 11:34:17 [INFO] Start building...
14-05-05 11:34:20 [SUCC] Build was successful
14-05-05 11:34:20 [INFO] Restarting sitepointgoapp ...
14-05-05 11:34:20 [INFO] ./sitepointgoapp is running...
2014/05/05 11:34:20 [I] Running on :8080
```

您可以看到应用程序已经准备好在端口 8080 上运行。在浏览器中加载`http://localhost:8080/`显示如下输出:

![Beego App Home Page](img/fa443380aa1ebc74d808c42d9cef2be0.png "Beego App Home Page")

没什么太花哨的，但很管用。因此，让我们动手扩展默认控制器，添加一个新的动作，以及一些自定义路线。

## 3.添加新操作

打开`controllers/default.go`，你会看到一个非常简单的控制器。这是因为显示逻辑全部包含在视图模板中。让我们稍微改变一下，看看如何添加视图模板变量和指定模板文件。在`default.go`中，增加以下方法:

```
func (main *MainController) HelloSitepoint() {
    main.Data["Website"] = "My Website"
    main.Data["Email"] = "your.email.address@example.com"
    main.Data["EmailName"] = "Your Name"
    main.TplNames = "default/hello-sitepoint.tpl"
}
```

让我们看看这是什么意思。我们所做的是将一个新的方法(或动作)`Get`添加到当前的控制器上，将`main *MainController`指定为该方法的接收者。

然后我们初始化了三个模板变量，*网站*、*电子邮件*和*电子邮件名称*，将它们存储在控制器中的一个字段中，称为`Data`，这是一个表示模板变量和值的映射。

接下来，我通过将`this.TplNames`设置为`default/hello-sitepoint.tpl`来指定模板文件名。如果你想知道，默认情况下，Beego 会在`views`目录中查找指定的文件。

所以在执行这条路线的时候，Beego 会取`views/default/hello-sitepoint.tpl`并渲染。

## 视图

好了，让我们创建附带的视图。在`views`下，创建一个新目录`default`，在那里，创建一个新文件`hello-sitepoint.tpl`，插入下面的代码:

```
<header class="hero-unit">
    <div class="container">
        <div class="row">
            <div class="hero-text">
                <h1>Welcome to the Sitepoint / Beego App!</h1>
                <h2>This is My Test Version</h2>
                <p>{{.Website}} {{.Email}} {{.EmailName}}</p>
            </div>
        </div>
    </div>
</header>
```

如果这是你第一次在 Go 中使用模板，请注意 Beego 的模板层扩展了 [Go 的 html/template 包](http://golang.org/pkg/html/template/)。关于在模板中使用变量的一些很好的例子，[查看文本/模板包中的这个例子](http://golang.org/pkg/text/template/#hdr-Arguments)。

像大多数 Go 包一样，html/template 非常广泛，所以我将只讨论相关的特性。所有模板变量都在全局上下文中可用，可以使用点操作符访问，使用`{{}}`语法嵌入。

因此，为了访问我们之前在控制器动作中设置的三个模板变量，我们将它们称为`{{.Website}}`、`{{.Email}}`和`{{.EmailName}}`。

## 按指定路线发送

好了，我们有了一个新的动作和一个伴随的视图。但是我们还不能执行路线。如果我们试图访问默认操作之外的任何内容，我们会看到默认的 404 错误页面，如下图所示。

![Beego 404 Page](img/cee3353d24cb11065172cad80dc03fb3.png "Beego 404 Page")

所以，我们需要给它添加一条路线。在`routers/router.go`中，更新 init 方法，如下所示:

```
func init() {
    beego.Router("/", &controllers.MainController{})
    beego.Router("/hello-world", &controllers.MainController{}, "get:HelloSitepoint")
}
```

关注最后一行，当我们试图分派给`/hello-world`时，它调用主控制器上的`HelloSitepoint`动作。保存文件，等待片刻重新编译完成，然后在浏览器中打开`http://localhost:8080/hello-world`。如果一切顺利，它将看起来像下面的页面:

![Beego Hello World Route](img/50dd6d8b17db6fd3a5c925123ee79d42.png "Beego Hello World Route")

## 请求参数

到目前为止，我们所做的对于简单的动作来说是很好的。但是在真实的应用程序中，我们将与请求进行交互，检查查询字符串或 POST 数据，然后做出相应的反应。那么我们如何通过 Beego 访问这些信息呢？

让我们从从查询字符串中检索数据开始。Beego 中的另一个预定义值是[包含**输入值**的上下文模块](http://beego.me/docs/module/context.md)，它又封装了一个请求。

它为我们提供了访问以下内容的途径:

*   方法
*   草案
*   用户代理人
*   查询(获取和发布数据)
*   会话信息等

让我们通过更新路由来要求设置一个 id 值，让它变得更有趣一些，然后我们可以在操作中检查并打印出来。在`router.go`中，将`/get`路线改为如下:

```
beego.Router("/hello-world/:id([0-9]+)", &controllers.MainController{}, "get:HelloSitepoint")
```

现在，我们需要提供一个 GET 值，它只能是一个数字，因为我们已经向路由提供了 regex，`([0-9]+)`。保存并尝试在没有 id 的情况下再次加载`/hello-world`。你看到错误了吗？

现在分派到`/hello-world/213`，应该会成功完成。既然已经发生了，那就让我们掌握信息吧。在 Get 方法中，在`this.TplNames`上方添加以下内容:

```
this.Data["Id"] = this.Ctx.Input.Param(":id")
```

然后在`hello-world.tpl`中，在现有模板变量旁边添加以下内容:`{{.Id}}`。重新加载页面将会在邮件名称旁边显示 **213** 。

## 按方法类型限制操作

好了，我们今天的工作就要结束了，但是在我们结束之前，我想讲最后一件事。通常，您希望将对某个操作的访问限制在一个或多个特定的方法上。例如，您可能只想访问一个删除路由，带有一个 **POST** 请求；您可能只想显示带有 **GET** 请求的搜索结果。

Beego 使得通过所有类型访问一个动作变得容易，或者将它限制在一个或几个类型。再次在`router.go`中，将`/hello-world`路线进一步更改如下:

```
beego.Router("/hello-world/:id([0-9]+)", &controllers.MainController{}, "get,post:Get")
```

这样做的目的是允许将调度作为 GET 或 POST 请求。使用下面的例子，尝试将它变成 PUT 或 DELETE，看看会得到什么。

```
# PUT request
curl -X PUT http://localhost:8080/hello-world/213

# DELETE request
curl -X DELETE http://localhost:8080/hello-world/213
```

## 包扎

我希望您喜欢这篇关于 Beego web 应用程序框架的介绍。在第 2 部分中，我们将集成一个数据库(SQLite3)，查看模型、表单和验证。

在第 2 部分的结尾，我们将有一个非常完美的应用程序，涵盖了您希望日常使用的大多数特性。

虽然 Beego 和 go 不同于您可能已经习惯使用的动态语言，但是只要花一点时间和精力，利用它们提供的能力是非常简单的。

别忘了，这个系列[的代码可以在 Github](https://github.com/settermjd/Learning-Beego) 上获得。你可以随意在网上浏览它，或者克隆它并进行实验。

你觉得怎么样？如果你还没有开始使用 Go，这足够让你使用它了吗？请在评论中分享你的想法。

## 链接

*   [http://www . going go . net/2013/06/installing-go-gocode-gdb-and-lite ide . html](http://www.goinggo.net/2013/06/installing-go-gocode-gdb-and-liteide.html)
*   [https://groups.google.com/forum/#!topic/golang-nuts/hbnchmia 05g](https://groups.google.com/forum/#!topic/golang-nuts/hbNCHMIA05g)
*   [http://stack overflow . com/questions/10105935/how-to-convert-a-int-value-to-string-in-go](http://stackoverflow.com/questions/10105935/how-to-convert-a-int-value-to-string-in-go)
*   -= ytet-伊甸园字幕组=-翻译:粒粒粒尘紫月猫姐校对
*   [https://github.com/astaxie/beego](https://github.com/astaxie/beego)
*   [http://golang.org/pkg/text/template/](http://golang.org/pkg/text/template/)
*   [http://golang.org/doc/effective_go.html#if](http://golang.org/doc/effective_go.html#if)
*   [http://blog.golang.org/go-maps-in-action](http://blog.golang.org/go-maps-in-action)

## 分享这篇文章