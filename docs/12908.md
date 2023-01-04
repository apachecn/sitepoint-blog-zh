# SproutCore: JavaScript 应用程序

> 原文：<https://www.sitepoint.com/sproutcore-javascript-applications/>

苹果公司在最近的 [WWDC](http://developer.apple.com/wwdc/) 发布了作为 MobileMe 网络应用基础的，JavaScript 框架兄弟会的最新成员: [SproutCore](http://www.sproutcore.com/) 。我敢肯定，如果我告诉你 SproutCore 是另一个用于创建富浏览器应用程序界面的 JavaScript 框架，你会更愿意用钢丝棉擦擦眼睛，而不是继续阅读。但是不要马上离开，因为 SproutCore 与其他框架有很大不同，值得一看。

SproutCore 是一个开源框架，用于创建桌面风格的应用程序，这些应用程序仅使用 HTML、CSS 和 JavaScript 在浏览器中运行。首先在本地开发环境中创建应用程序，然后使用 SproutCore 构建工具将应用程序编译成一组可以放在 web 服务器上的静态文件。“胖客户端”一词是由 SproutCore 的首席开发人员 Charles Jolley 创造的，用来描述 SproutCore 的方法。SproutCore 应用程序完全独立于任何服务器端技术。整个应用程序在浏览器中运行；与服务器的唯一交互是通过 Ajax 保存或加载数据。

当我说“创建应用程序”时，我指的是复杂的、基于模型-视图-控制器的应用程序设计，其灵感来自苹果的 [Cocoa](http://developer.apple.com/cocoa/) 。实际上，感觉就像你在构建一个全功能的 Ruby on Rails 应用程序——构建工具是用 Ruby 编写的，并且使用了类似 Rails 的特性，比如生成器。模型和控制器用 JavaScript 编写，视图用嵌入式 Ruby 的高性能实现 [Erubis](http://www.kuwata-lab.com/erubis/) 编写。

这里有一个非常简单的演示它是如何工作的。如果你想了解更多，我推荐你在 SproutCore 网站上做 [hello world 教程](http://www.sproutcore.com/documentation/hello-world-tutorial/)。

在通过 RubyGems 系统安装了构建工具之后，您可以通过键入命令:`sproutcore hello_goodbye`开始一个新的应用程序，用您的应用程序的名称替换`hello_goodbye`。这将生成一个与您的应用程序同名的目录，其中包含使您的应用程序在本地运行所需的所有文件。

为了能够做任何有趣的事情，你需要一个控制器——一个管理应用程序逻辑的 JavaScript 对象。您可以通过在项目根目录中键入以下命令来使用内置生成器:

```
sc-gen controller hello_goodbye/app
```

这将生成名为`HelloGoodbye.appController`的应用控制器。

使用绑定和观察器连接应用程序的接口元素。让我们通过添加一个`greeting`属性来修改控制器:

```
HelloGoodbye.appController = SC.Object.create(
{
  greeting: "Hello World!"
});
```

然后，我们将向默认视图文件添加一些视图助手:一个标签视图和一个按钮视图:

```
<%= label_view :my_label, 
    :tag => 'h1', 
    :bind => { :value => 'HelloGoodbye.appController.greeting' } %>
<%= button_view :toggle_button, 
    :title => 'Change Greeting', 
    :action => 'HelloGoodbye.appController.toggleGreeting' %>
```

通过从项目根目录输入命令`sc-server`来启动包含的 Mongrel web 服务器，将允许您在`http://localhost:4020/hello_goodbye`时在浏览器中加载项目。您将看到一个带有文本“Hello World！”的`h1`元素和“改变问候”按钮。
![A screenshot of a simpleSproutCore app](img/d56955c02a083a6c29487388c412fc0e.png)

标签视图的文本内容已经绑定到控制器的`greeting`属性。如果使用 SproutCore API 更改`greeting`的值:

```
HelloGoodbye.appController.set('greeting', 'Goodbye World!')
```

标题中的文本也会奇迹般地改变。

按钮视图的`:action`属性表示当按钮被点击时将被调用的控制器方法。我们将把该方法添加到控制器中:

```
HelloGoodbye.appController = SC.Object.create(
{
  greeting: "Hello World!",
  toggleGreeting: function() {
    var currentGreeting = this.get('greeting');
    var newGreeting = (currentGreeting === 'Hello World!') ? 'Goodbye World!' : 'Hello World!' ;
    this.set('greeting', newGreeting);
  }
});
```

如果在浏览器中刷新页面，单击按钮应该会在“Hello World！”和“再见世界！”。您可能会注意到，我们根本不需要用 JavaScript 编写任何 DOM 操作代码就可以实现这一点。

一旦您准备好部署，您需要做的就是从项目的根目录中运行下面的命令:`sc-build`。这将创建一个包含静态文件的目录，您可以将这些文件移动到您的 web 服务器。

我们上面的小例子被转换成下面的 html:

```
<h1 id="my_label" class="sc-label-view my_label"></h1>
<a title="Change Greeting" id="toggle_button" 
    class="sc-button-view button regular normal toggle_button">
  <span class="button-inner">
    <span class="label">Change Greeting</span>
  </span>
</a>
```

除了 HTML，您还可以找到运行应用程序所需的所有 JavaScript 代码。

查看生成的源代码，很明显 SproutCore 是一个真正意义上的应用程序框架:忽略生成的 HTML，最终产品看起来和感觉上都像一个桌面应用程序。对于像我这样的 web 标准爱好者来说，基于浏览器的桌面风格的应用程序总是很难推销。我更喜欢 restful 的、基于语义标记的、以文档为中心的方法，让浏览器像浏览器一样工作。你知道:书签和历史，右键>在新标签页打开，超链接的神圣和纯洁。然而，浏览器作为一个应用平台，似乎不可避免地会被推向极限。我不禁回想起软件公司的乔尔曾预测这样的事情可能会发生。

作为一个 JavaScript 爱好者，我印象深刻；接口性能非常出色。SproutCore 提高了 JavaScript 在浏览器中的能力。在最近的一次 Audible Ajax 上，Jolley 谈到了一个 SproutCore 应用程序如何只有 6 个事件处理程序。SproutCore 使用事件委托来确定主体中的哪个元素是事件的目标。我确信从 SproutCore 代码库中可以挖掘出许多 JavaScript 瑰宝。SproutCore 博客已经包含了一些[的好帖子](http://www.sproutcore.com/2008/05/28/understanding-the-ie-dom/ "Understanding the IE DOM")。

可访问性呢？辅助技术将如何处理 SproutCore 生成的 HTML？我怀疑与标准 HTML 格式兼容的屏幕阅读器，除了把网页解释为文档之外，没有任何机会理解它。也许，在未来，像 WAI Accessible Rich Internet Applications 规范这样的标准最终会拯救我们。我很想看看苹果对 MobileMe 采取的方法。

尽管我有所顾虑，但我还是很欣赏苹果选择了基于浏览器的开源技术，并且不需要插件作为在线应用的基础。在我看来，这比 Adobe 和微软的替代技术要好得多。

## 分享这篇文章