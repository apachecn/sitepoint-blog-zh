# MVC 和 web 应用:石油和水

> 原文：<https://www.sitepoint.com/mvc-and-web-apps-oil-and-water/>

将时钟拨回两年。那是在 AJAX、Web 2.0、Rails 之前。那时我们在哪里？好好思考一下 MVC 到底是不是一个好主意。从那里继续…

**什么是 MVC？**

长话短说，我见过的最好的(针对网站站长)是[杰夫的](http://www.procata.com/blog)分析[这里](http://www.phpwact.org/pattern/model_view_controller)。通过[维基百科](http://en.wikipedia.org/wiki/Model-view-controller)可以找到更多，在旧的 [C2 维基](http://c2.com/cgi/wiki?ModelViewController)上还可以找到更多。

对于 web 应用程序(带有 PHP 倾向)，一个简短的答案可能是——一种基于以下思想组织代码的方法；

*   模型——那些将调用打包到数据库的函数
*   视图–输出 HTML 的模板/脚本
*   控制器——检查像$_GET 和$_POST 这样的变量并计算出下一步该做什么的东西

需要记住的重要一点是，MVC 没有绝对的定义，有许多灰色区域需要 T2 解释，当涉及到在线使用时就更多了。这本身就意味着，如果你正在构建一个框架，你只需要把“模型”、“视图”和“控制器”这些词放在某个地方，你可能和其他人一样“正确”。前阵子 [John Lim](http://phplens.com/phpeverywhere/) 敢问[MVC 过度设计是废话吗？我怀疑这是一个有经验的程序员的直觉。在后来的更新中，他挖苦地评论道；](http://phplens.com/phpeverywhere/?q=node/view/143)

> MVC 过度设计是废话吗？我对过度设计的看法是错误的——它实际上是设计不足。是垃圾吗？这还有待观察。

**为什么是 MVC？**

那么，为什么 MVC 会成为 web 应用程序的一种常见模式(或者至少是一个常见术语)，记住它最初是为了帮助构建*桌面*图形用户界面而进化的[？](http://heim.ifi.uio.no/~trygver/themes/mvc/mvc-index.html)

据我所知，它第一次出现在孙的 [Model 2](http://en.wikipedia.org/wiki/Model_2) 架构中是在 1999 年左右(？)然后具体化为一个特定的(也是流行的)实现: [struts](http://struts.apache.org/) ，大约在 Y2K 左右。从那时起，坦率地说，它似乎已经成为一个自我延续的咒语或技术旅鼠综合症。除了它不是[模型 1](http://en.wikipedia.org/wiki/Model_1) (普通的 ASP / PHP 将被视为模型 1)之外，很难找到“为什么是 MVC”与任何给定替代方案的理由。我今天看到的大多数框架都在宣传 MVC，却没有说明他们为什么要使用它。

让我们面对现实吧:对于不了解情况的人来说，MVC 听起来令人印象深刻(也令人生畏)。当我们看到一个以前没有遇到过的术语时我们会怎么做——[搜索](http://www.google.com/search?q=Model+View+Controller)发现很多其他人都说“是的 MVC”。

那么，我们问了足够多的问题了吗？还是说，我们只是在集体赶时髦？事实上，Jeff 在[MVC](http://www.procata.com/blog/archives/2004/11/28/the-value-of-mvc/)的价值中清楚地陈述了赶时髦的因素——因为对 struts 开发者的需求，MVC 中必须有“某些东西”。

对我来说，还有一个危险信号在挥舞，这是桌面应用程序中 MVC 的经典标志，其中多个不相关的视图会在模型发生任何变化时自动更新(Jeff 很好地将其描述为[活动模型](http://www.phpwact.org/pattern/model_view_controller#active_model))。不幸的是，在传统的(非 AJAX) web 应用程序中，一个视图对应一个浏览器的单个网页响应，在 HTTP 这样的请求-响应协议中，没有空间让*服务器*指示你打开的其他浏览器窗口也需要更新:web 服务器不联系你——你联系他们。MVC 的标志在 web 上不起作用，除非你破解了某种客户端刷新机制来检查更新。这是不是暗示 MVC 分离不是最佳选择？

我怀疑的是，如果我们问“为什么选择 MVC 而不是[在此插入模式]？”如果声音足够大，回应只会是脚步的移动和偶尔的“为什么不呢？”。也许开发“模型 2”的人会很坦率——“我们需要一个我们的(AWT / Swing)开发人员社区能够与之相关的模式，而 MVC 已经足够接近了。它本不应该被看得这么严重。”

所有这些都不是说 MVC 很糟糕，你不能用它来构建 web 应用。只是我认为我们在谈论方钉和圆孔。真正的问题是有更好的方法吗？

**替代品**

ASP.NET 和 JSF 都试图将 T2 的事件驱动编程引入服务器端，反应不一。鉴于我们现在在 ASP.NET 时钟上有足够的英里数，我得到的印象是经验丰富的 *web* 开发者对 ASP.NET 保持警惕，因为它“太聪明了”——掌握的学习曲线很重要，也许在那些 web 控件中有太多聪明的东西正在透明地完成。ASP.NET 可能更讨那些来自 VB6 背景的人的喜欢。事件驱动编程是否真的是 MVC [的竞争范例还有待商榷](http://c2.com/cgi/wiki?MvcVersusEventDriven)，但是可以公平地说*将控制器*降为一个不太重要的角色。

与此同时，Coldfusion 的开发人员仍在继续开发保险丝盒。努夫说。

就我个人而言，我没有什么特别的东西可以提供，但是我有更多的想法，关于我们如何实现 MVC 以及具体的实现问题。这里我不排除 MVC，只是质疑它是如何被普遍使用的。

**使资源一流**

我对 struts 和衍生物的一个问题是，它们将*动作*提升为[第一类对象](http://en.wikipedia.org/wiki/First-class_object)(在 Rails 中，动作是类方法，但我仍然认为是“第一类”)。

HTTP 只有两个动作(至少是实际正在使用的)——GET 和 POST——检索或更新它。在这一点上，值得一读[为什么 Web 服务器 API 如此糟糕？](http://www.mnot.net/blog/2003/12/08/http_api)。

对我来说，将动作提升为第一类对象的一个直接后果是，我们最终会将[动词放在像“http://example . com/item/1/delete”这样的 URL](https://www.sitepoint.com/who-gets-rest/)中，这反过来会导致像这样的问题，并意味着紧密的[耦合](http://en.wikipedia.org/wiki/Coupling_(computer_science))(在客户端和服务器之间)和 [RPC](http://en.wikipedia.org/wiki/Remote_procedure_call) 。GET 显然被[标记为](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)“安全/只读”，而在 URL 中放置一个动词意味着将要做一些事情(可能安全，也可能不安全)；

> 特别是，约定已经建立，GET 和 HEAD 方法不应该具有采取除检索之外的动作的意义。这些方法应该被认为是“安全的”。这允许用户代理以一种特殊的方式表示其他方法，比如 POST、PUT 和 DELETE，这样用户就知道正在请求一个可能不安全的操作。

从某种程度上说，直到 2005 年我们才集体意识到这种东西的重要性，这是令人惊讶的。

将动作放在第一位的另一个缺点是，我认为它会导致与直觉相反的代码，除了核心框架人员。对大多数人来说，网络意味着文档、页面、图像、电影——有形的东西，可以离线下载和存储——换句话说就是资源。

因此，让我们将资源作为第一类对象，并让框架 API 反映这一点。这就是 [REST](http://en.wikipedia.org/wiki/Representational_State_Transfer#Resources) 试图告诉我们的。

这可能只是意味着找到更聪明的方法来编写以视图为中心的代码，强调 T2 的枯燥，同时避免意大利面条——考虑一下这个 URL 中的“article.php”。

或者，这可能意味着我们花更多的时间在像这样的类上；

```
class Joke extends Resource {
    function GET($Params) {
        # Return / display a joke
    }

    function POST($Params) {
        # Create or update a joke
    }

    function DELETE($Id) {
        # Delete a joke
    }
}

class Jokes extends Resources {
    function GET($Conditions = array()) {
        # Return / display a list of jokes
    }
}

```

或者，对于关于把资源放在第一位的真正具有挑战性的观点，你必须看看[小麦](http://www.wheatfarm.org/)(一个很遗憾似乎已经失败的原型，我猜是因为项目目标在非常高的高度)；

> 每个物体都有一个 URI

又从小麦[介绍](http://www.wheatfarm.org/talk/intro.html) [这里](http://www.wheatfarm.org/talk/talk6.html)；

> 有人问我，为什么这需要一种新的语言(不知何故，似乎没有人关心新的环境……)。以下是我较为啰嗦的回复之一:
> 
> “今天没有一种语言将对象视为持久的、全局可访问的实体，而这正是今天的 web 编程试图建模的。Web 编程与桌面和服务器应用程序编程有很大的不同，因此需要一个新的模型:当构建这些环境时，我们希望程序在某个时间点启动，将用户的数据从一些数据存储区读入堆中，对其进行处理，可能与用户进行交互，写回数据，然后退出。基于堆的对象，或者更一般地说，操作单元基于短暂的、基于内存的数据的语言，非常适合应用程序。当我们想到 web 编程时，我们试图创建一个视图，在这个视图中，用户的内容永远存在，永远鲜活，并且可以被广泛地访问。与在基于记忆的基础上构建世界观不同，Wheat 有一个对象和语言模型，可以直接支持这种编程风格。”

吓人！如果我们真的都找错了对象呢？也许这里有一种关系，与对[闭包](http://en.wikipedia.org/wiki/Continuation)的兴趣有关。

补充说明:我认为 [eZ Publish](http://ez.no/products/ez_publish_cms) 中的[内容类和对象](http://ez.no/products/ez_publish_cms/documentation/customization/content_structure/content_classes)符合上述描述，被保存在数据库中。

**AJAX 和表单**

想象一下，AJAX 真的如它所描述的那样；每个人都有支持它的浏览器，他们打开了 Javascript 等等。等等。在这里，我们将有一个 MVC *真的可以*被正确应用的环境——非常接近于 MVC 设计的原始胖客户端，但是由 Javascript、DOM 和 XMLHttpRequest 提供支持。

那么这会给服务器端带来什么呢？它将其向下推至少一层，成为“后端”——在逻辑上相当于今天 web 应用程序中的数据库。您只是希望它以无状态的方式服务和接受对[资源](http://en.wikipedia.org/wiki/Representational_State_Transfer#Resources)的更新——GET 和 POST。

如果你仍然在服务器端应用 MVC，你可能会让它尽可能简单，更喜欢使用[策略](http://particletree.com/features/the-hows-and-whys-of-degradable-ajax/)而不是框架。服务器上仍然有业务逻辑，但是控制逻辑(包括工作流的执行)已经转移到 AJAX 客户端。我猜服务器端已经变成了一个[服务层](http://www.martinfowler.com/eaaCatalog/serviceLayer.html)。

旁注:activegrid 看起来正在和 T2、BPEL 一起做的事情似乎正朝着这个方向发展，尽管它仍然是服务器端技术。在 activegrid 的“思维模式”中，PHP 脚本被归入“服务”一类(也许是正确的)，仅仅是[发送文本](http://www.looselycoupled.com/blog/lc00aa00077.html)。

您也很有可能不再在服务器端生成表单，而是在客户端用 Javascript 动态地生成表单。如果做不到这一点，也许你正在使用“独立”的表单页面，它们与你的其他资源是完全分开的。多页的“向导”表单不再需要隐藏的输入字段，而是作为 Javascript 对象存在于浏览器的内存中，直到表单完成。如果从服务器端消除表单生成听起来很牵强，考虑一下 Firefox 中的先例搜索框(右上角)——就我个人而言，我已经很久没有在 Googles 主页上查看搜索表单了。

我在这里真正想说的是，我认为服务器端对 MVC 的渴望是对表单的痴迷、Javascript 的局限性、与桌面应用竞争的渴望的结果，而 form method="DELETE "从未发生过(因此有必要通过 POST the action 将其捆绑为另一个*动作*)。您的表单上有一个名为“添加到购物车”的按钮，所以您显然希望在某个地方有一个 [AddToCartAction](http://jakarta.apache.org/taglibs/doc/rdc-doc/javadoc/org/apache/taglibs/rdc/sampleapps/musicstore/struts/AddToCartAction.html) 。随着 AJAX 的完善，我们可能会纯粹在客户端实现购物车，只是在“结账”时再回到服务器。

**RUD 不是 CRUD**

也许另一个试图让桌面在线的后遗症是谈论[CRUD](http://en.wikipedia.org/wiki/CRUD_(acronym))——四个操作:创建、检索、更新、删除。对于 HTTP，您只有三个重要的操作(其中一个，DELETE，没有被使用)——检索(GET)和更新(POST):特别是创建和更新之间没有区别。这听起来可能是一个微不足道的观点，但这里有一个被忽视的重要意义。

CRUD 是在数据库的上下文中定义的，在数据库中，您处理的是信息的*集合*——您需要区分插入集合和更新集合中的现有成员。

同时，HTTP 是为访问资源而设计的，“主键”由它的 URL 决定(而不必担心[插入 id](http://www.php.net/mysql_insert_id) )。如果你想到“文档”，很明显没有必要区分创建和更新——创建文档会导致第一个版本。更新意味着用新版本覆盖现有文档。但是在这两种情况下，客户端发布的是相同的内容，不需要知道文档是否已经存在。

同时，服务器端框架的第一个常见演示应用是 CRUD 示例。这里的含义是框架非常强调数据库，而 HTTP 在很大程度上被忽略了(甚至很少看到 HTTP 状态代码是框架的基本部分)。

避免冗长的文件系统与数据库的讨论(比如需要具有可扩展属性的[虚拟文件系统](http://pflanze.mine.nu/more.html))就足够了——考虑一下 [Dokuwiki](http://wiki.splitbrain.org) 与 [MediaWiki](http://www.mediawiki.org/wiki/MediaWiki) 相比，如何将 wiki 页面一对一地存储为文件。什么对你更有意义？也许我们的网站已经被数据库驱动得太远了？

这里的要点是，考虑到 HTTP 和 CRUD 之间的不匹配，我们将 CRUD 放在第一位，这反过来使*动作*成为我们框架中的第一类。我们的目标是支持 N 种不同类型的动作(动词),而实际上我们应该只处理三种——GET、POST 和 DELETE(根据一些框架/表单约定，后者可能被重新路由到特定的“资源类”方法)。

**回到超越**

回到[这一点](http://today.java.net/pub/a/today/2003/12/11/mvc.html)，今天的结论似乎是，与其放弃 MVC，不如放弃 Java(或 Perl / PHP / Python)来支持减少代码行的平台——“请用最少痛苦的 MVC”。MVC 是我们对更好的 web 应用程序开发需求的同义词——“您想要更好的实践吗？使用 MVC”。

但是如何提取我们真正想要的东西作为一个独立的列表呢——暂时忘记 MVC 吧？脱口而出(而且一点也不全面)；

*   保姆:告诉我如何变得有条理——清楚地指明放代码的地方。
*   加水就行:[现在把我的原型给我](http://media.rubyonrails.org/video/rails_take2_with_sound.mov)！
*   不要让我想:即使在我最笨的日子里，我也能做这些事情。
*   [干](http://www.artima.com/intv/dry.html):同样的变化做 50 次不酷。
*   反意大利面:帮我避免意大利面
*   保安:请不要有令人讨厌的意外。帮我第一次就搞清楚。
*   测试:帮助我保护自己不受伤害。
*   [在此插入更多内容] x N

到目前为止我还没想出有什么东西是*只有*可以用 MVC 解决的(还有一些根本和 MVC 没关系)。

也许有更好的模式适合 web？或许 MVC 中的“控制器”暗示着强调“动作优先”。如果我们将操作降级为支持 GET、POST 和 DELETE 的资源，也许框架会自动为我们处理控制器？

不管怎样，因为没有更好的地方来结束这个话题，引用一下 [The Register](http://www.theregister.co.uk/2005/02/14/codecon_2005/) 关于小麦的一段话；

> 在第一次周日演示后，一名观众声称他发现新的 web 编程语言“太美了，它让我哭了！”

## 分享这篇文章