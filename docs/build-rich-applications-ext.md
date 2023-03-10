# 用 Ext 构建富互联网应用程序

> 原文：<https://www.sitepoint.com/build-rich-applications-ext/>

Ext(可从[extjs.com](http://extjs.com/)获得)是一个 JavaScript 开发框架，由德高望重的 JavaScript 大师 Jack Slocum 发起并维护。通过提供一个不断增长的 JavaScript 类结构，解决许多与用 JavaScript 开发富互联网应用程序(RIA)相关的常见问题，Ext 有可能为您的 web 应用程序开发节省几个月的时间。

在本文中，我将向您详细介绍 Ext 实际上是什么和做什么——为什么我认为我们作为 web 开发人员需要它，以及它如何使开发 RIA 变得如此容易。我们将回答这些问题:Ext 到底是什么？我们为什么需要它？

##### 定义“富互联网应用程序”

正如您所知，在整篇文章中，我特意选择放弃广为人知(并被大肆宣传)的“Ajax”和“Web 2.0”这两个术语，因为在我看来，对它们的真正含义似乎还有一些困惑。然而，“富应用程序”的定义是普遍接受的。该术语定义了具有功能丰富的用户界面的应用程序——与我们日常使用的任何桌面应用程序一样响应迅速且智能的应用程序。Adobe Photoshop 和 Microsoft Excel(如下所示)是丰富桌面应用程序的很好例子。

![Microsoft Excel: a rich desktop application](img/b3eb1d2a829e05cb284a32569639d2e4.png)

那么，我们为什么需要 RIA 呢？我们的桌面上已经有了我们需要的所有应用程序，对吗？虽然这是真的，但是越来越多的公司正在接受 RIA 的概念，用基于 web 的应用程序取代传统的桌面应用程序。当然，这种方法最明显的好处是，您的潜在客户可以从任何可以访问 web 浏览器和 Internet 连接的地方获得对您的应用程序的完全访问权。对于使用由第三方开发、管理和运营的应用程序的客户来说，运营任务关键型应用程序的责任被转移给了外部方，这有可能释放内部资源。

![Google Spreadsheets: a Rich Internet Application, Google Spreadsheets (RIA)](img/50a26402b9a480a61f34d1cbfcce51be.png)

通过 Web 分发应用程序属于 SaaS(软件即服务)概念，这在过去几年中已经成为业界最热门的话题之一。在北美，采用 SaaS 已经很普遍，一些人预计它很快会在欧洲流行起来。

SaaS 和 RIA 的概念是紧密联系在一起的:SaaS 的潜在成功自然依赖于市场生产优秀 web 应用程序的潜力:除非基于 web 的替代软件被证明同样优秀，否则人们不会愿意放弃他们的传统软件。为了构建能够与传统桌面应用程序竞争的网络应用程序，我们需要为传统的静态网络添加另一个维度。这就是 Ext 的用武之地。但是首先，让我们来看看开发人员在开发 RIA 时一直在努力解决的问题。

##### 开发富互联网应用程序

RIA 可以使用几种不同的技术来开发:Java Applets、Flash 和微软的 XBAP 都是可以作为平台的技术。然而，最广泛采用的技术——实际上，已经成为事实上的标准——是 JavaScript。通过使用 JavaScript 来动态改变加载到用户浏览器中的 HTML 和 CSS，开发人员找到了一种方法来创建外观和感觉都像真实应用程序的网站，并具有 HTML 的所有可访问性和 SEO 优势。

但是这种方法也不是没有缺点:大部分用户界面必须从头开始创建。例如，在使用 Windows 窗体开发 Windows 桌面应用程序时，您有一组预定义的小部件，可以自动生成好看的表格、树、对话框、上下文菜单、工具栏等等。这些小部件在 JavaScript 中都不存在。毕竟，JavaScript 和 CSS 处于相当低的技术水平，因此使用这些技术从头开始编写高级用户界面相当复杂，或者至少非常麻烦。

当然，上面提到的构建模块的许多例子已经存在，作为代码例子甚至自由软件分布在互联网上的各个站点。因此，如果您在这方面投入一些努力，您可能会找到构建 RIA 所需的大多数构件的工作版本，或者至少是代码示例。问题是这些代码样本分散在整个互联网上，它们在质量和风格上都各不相同。与从头开始相比，收集 JavaScript 代码并使用您找到的内容作为应用程序的基础可能会节省您大量的开发时间。但是，这可能还需要您修改找到的大部分代码，以获得一致的外观和感觉。此外，您无法保证您会获得更新或错误修复，这将使这种方法不太理想。

如果我们想轻松地开发一个统一的、专业的、易于使用的、稳定的 RIA，我们显然必须克服一些问题。

##### Ext 来帮忙了

简而言之，Ext 提供了我们所需要的——一个稳定统一的 JavaScript 平台，用于构建丰富的 web 应用程序。最初建立在 [Yahoo！UI 库](http://developer.yahoo.com/yui/)，Ext 一段时间以来看起来很有前途。然而，当 2.0 版本在上周发布时，这个库已经发展成为可能是开发富 web 用户界面最健壮的 JavaScript 库。

当然，也有一些替代品，比如 Dojo 和 Yahoo！UI 库，但有几个关键点，当结合起来时，使 Ext 脱颖而出。这些要点包括:

*   Ext 很快。在编写 JavaScript 时，性能常常是一个问题。
*   Ext 是以 100%面向对象、结构良好、一致的方式实现的。这使得库学习起来很快，代码易于阅读和理解。
*   具有一致基础的模块化实现使得该库易于扩展。
*   所有的 Ext 元素(小部件)都可以使用了。与许多其他库相比，这些小部件可以直接使用，具有预定义的样式、设置和行为。尽管如此，所有的元素都是完全可定制的，如果需要的话还可以设置主题。
*   Ext 开发人员非常敬业，也非常能干，他们理解用户的需求，最重要的是对用户的需求感兴趣。Ext 文档非常全面，充满了工作示例。
*   Ext 社区很活跃，基调普遍很积极。
*   Ext 可以在免费和商业许可下使用。
*   最后但同样重要的是，Ext 看起来非常光滑！

对于其他库来说，其中的一些(甚至全部)也是如此。我并不是说没有其他很好的选择，你应该在决定选择一个之前调查所有的选择。然而，根据我的经验，Ext 给人的总体印象最好，这也是我决定使用它的原因。

让我们看一个简单的例子，看看 Ext 能为我们做什么。假设我们想在应用程序中实现一个图形淡入淡出特性——我们可以用它来让一个对象以一种赏心悦目的方式出现和消失。下面是这种特性在普通 JavaScript 中的一种可能实现，不使用 Ext:

```
<html>  

  <head>  

    <title>Fading without Ext</title>  

  </head>  

  <body>  

    <div  

      id="my-element-to-fade"  

      style="width:100px;height:100px;background-color:Red">  

    </div>  

    <br />  

    <button onclick="shiftOpacity('my-element-to-fade', 1000);">  

      Fade!  

    </button>  

    <script type="text/javascript">  

      function changeOpacity(opacity, id)  

      {   

        var object = document.getElementById(id).style;   

        object.opacity = (opacity / 100);  

        object.MozOpacity = (opacity / 100);   

        object.KhtmlOpacity = (opacity / 100);   

        object.filter = "alpha(opacity=" + opacity + ")";   

      }  

      function setOpacity(id, opStart, opEnd, ms)  

      {              

        var speed = Math.round(ms / 100);   

        var timer = 0;   

        if(opStart > opEnd)  

        {   

          for(i = opStart; i >= opEnd; i--)  

          {   

          setTimeout(  

            "changeOpacity(" + i + ",'" + id + "')",  

            (timer * speed)  

          );   

            timer++;   

          }  

        }   

        else if(opStart < opEnd)  

        {   

          for(i = opStart; i <= opEnd; i++)   

          {  

            setTimeout(  

              "changeOpacity(" + i + ",'" + id + "')",  

              (timer * speed)  

            );   

            timer++;  

          }  

        }  

      }  

      function shiftOpacity(id, ms)  

        {  

          if(document.getElementById(id).style.opacity == 0)  

          {  

            setOpacity(id, 0, 100, ms);   

          }  

          else  

          {  

            setOpacity(id, 100, 0, ms);   

          }  

        }  

    </script>  

  </body>  

</html>
```

这段代码生成了一个简单的页面，上面只有一个红色方块和一个按钮([查看演示](https://www.sitepoint.com/examples/ext/fade-square.html))。点按该按钮可以淡入或淡出红色方块，这取决于它当前是否可见。该功能工作得很好，但是正如您所看到的，它需要相当多行(难看的)代码。幸运的是，我们可以使用 Ext 实现完全相同的功能，但代码要少得多(如果你想在家玩，可以[下载 Ext](http://extjs.com/download/) ):

```
<html>  

  <head>  

    <title>Fading with Ext</ title>  

    <script  

      type="text/javascript"  

      src="ext/adapter/prototype/prototype.js">  

    </script>  

    <script  

      type="text/javascript"  

      src="ext/adapter/prototype/scriptaculous.js?load=effects">  

    </script>  

    <script  

      type="text/javascript"  

      src="ext/adapter/prototype/ext-prototype-adapter.js">  

    </script>  

    <script  

      type="text/javascript"  

      src="ext/ext-all.js">  

    </script>  

  </head>  

  <body>  

    <div  

      id="my-element-to-fade"  

      style="width:100px;height:100px;background-color:Red;">  

    </div>  

    <button onclick="showHide('my-element-to-fade');" style="margin-top: 10px;">  

      Fade!  

    </button>  

    <script type="text/javascript">  

      function showHide(id)  

      {  

        Ext.get(id).toggle(true);  

      }  

    </script>  

  </body>  

</html>
```

两个代码示例给出了完全相同的结果，如下图所示([自己看](https://www.sitepoint.com/examples/ext/ext-fade-square.html))。

![JavaScript fading example](img/e3760acc452f039cdb1b8631da7a3c6b.png)

在这些例子中，我们感兴趣的代码是您在两个代码清单中的`<script>`标记之间看到的。虽然这是一个非常简单的例子，但差别是相当显著的。如果你把它放在一个更大的环境中(比如一个成熟的基于网络的文字处理器)，你可以想象使用 ext 这样的库会有什么不同。

##### 摘要

Ext JavaScript 库可以让你免除巨大的麻烦。七年前，在 Ajax 和 Rich Internet Application 这样的词出现之前，我加入了一个团队，开始开发一个功能全面的基于 web 的业务系统。我们想让它成为基于桌面的系统的有价值的替代品，所以丰富和智能的用户界面是必须的。在那个时候，甚至没有任何与 Ext 相近的东西存在，虽然我们今天的系统实现了我们需要它实现的一切，但毫无疑问，如果像 Ext 这样的东西从一开始就存在，它会大大减少开发工作。

正如我在本文中多次提到的，Ext 是一个非常一致的库——大部分功能可以在整个库中找到。这意味着从一开始就把事情做好是很重要的，因为当你前进到更高级(也更有趣)的特性时，这将对你有很大帮助。

## 分享这篇文章