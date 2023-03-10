# 避免邪恶的 JavaScript

> 原文：<https://www.sitepoint.com/avoiding-evil-javascript/>

以下内容转载自《科技时报》第 158 期。

是什么让一些 JavaScript 变得邪恶，初学者能从第一天就学会写好 JavaScript 代码吗？卡梅隆·亚当斯和我已经开始写一本书来证明他们可以做到，但是这并不像我希望的那么容易。

糟糕的 JavaScript 比没有 JavaScript 更糟糕，因为它会阻止一些用户访问你的站点。在混合使用 JavaScript 时，至少有三类人是您应该考虑的，我在这里按照难度递增的顺序列出了他们:

1.  没有支持 JavaScript 的浏览器的用户
2.  不使用鼠标浏览的用户
3.  使用屏幕阅读器浏览的用户

也许有点理想化，我认为任何阻止这些人访问你的网站的 JavaScript 代码都是邪恶的。

上周在詹姆斯·爱德华兹墨尔本举行的[网络标准小组](http://webstandardsgroup.org/)会议上(巧合的是，Cameron Adams 是 JavaScript 选集[的合著者)总结得非常简洁:](https://www.sitepoint.com/books/jsant1/)

> “一个人的喜好是另一个人的真实需要。可能一群用户觉得 Ajax 更容易，但是如果另一群用户觉得它完全不可能，那么你就把他们排除在外了，你这样做基本上是白做的。
> 
> “我认为这是一个层次结构，基本上，可访问性是最重要的事情，可用性次之，偏好、设计和美学次之。所有这些事情都很重要，但是如果一个影响到另一个，那么你必须考虑哪个是最重要的。
> 
> “在我看来，可访问性永远是最重要的，因为可访问性影响到人们真正需要的东西。其他的都只是偏好。”

James 在演讲的最后呼吁喜欢 Ajax 的开发人员尽可能避免使用 Ajax，除非你可以在不影响网站可访问性的情况下使用它。James 特别提到了 Ajax，但同样的观点也适用于一般的 JavaScript。

所以问题变成了，我们能合理地期望 JavaScript 初学者写出不降低可访问性的代码吗？

我在上面提到了三个用户群，它们给那些希望在不放弃可访问性的情况下用 JavaScript 增强站点的开发人员带来了越来越大的挑战。我想简要介绍一下支持这些团体的具体困难，并考虑是否应该期望初学者来处理这些困难:

**Users that don’t have JavaScript-enabled browsers**

如果您构建的网站依赖 JavaScript 来提供对某些功能的访问，甚至是基本的导航功能，那么您实际上是将大量用户拒之门外，因为这样或那样的原因，这些用户没有支持 JavaScript 的浏览器。

幸运的是，解决这个问题的方法相对简单，主要是方法的问题。如果您首先构建一个不使用 JavaScript 的网站，然后使用 JavaScript 应用增强功能，那么问题就解决了。

我们绝对可以教初学者用这种方式思考 JavaScript，事实上这也是本书第一章的内容。

**Users that browse without using a mouse**

由于各种各样的障碍，特别是那些影响精细运动控制的障碍，某些用户在浏览网页时不能使用鼠标。相反，他们使用浏览器的键盘导航功能在网上四处走动。

在大多数情况下，键盘导航实现起来并不比鼠标导航复杂。所需要的只是一点思考，以及一些额外的代码来处理这种交互的替代方法。例如，您需要确保用户可以使用键盘焦点(通常使用 Tab 键)到达页面上的每个“活动”元素，并在到达那里后执行等效的操作。

作为一等公民，支持键盘交互是你可以从初学者开始编写交互式 JavaScript 时就教他们的。

**Users that browse using a screen reader**

这就是事情变得棘手的地方。目前，对于许多视障用户来说，最好的 web 浏览体验是屏幕阅读器软件。屏幕阅读器位于桌面 web 浏览器之上，大声朗读页面，并提供在内容中导航和完成填写表单等任务的其他方法。

不幸的是，对于这个软件如何解释页面的内容，还没有公认的标准，因此，与 CSS 和 JavaScript 等新的 web 技术的兼容性因供应商而异。

也就是说，有些限制是非常普遍的。其中两个与 JavaScript 开发特别相关:

*   屏幕阅读器不读取通过将 CSS display 属性设置为 none 而隐藏的内容。
*   屏幕阅读器对页面的静态快照进行操作，该快照偶尔会在开发人员无法启动或检测到的进程中刷新。

编写在这些条件下运行的脚本，而不干扰用户理解内容和使用网站功能的能力，即使在某些情况下不是不可能，也是极具挑战性的。那么，关于这个问题，我们应该教初学者什么呢？我们能期望他们的脚本在多大程度上与屏幕阅读器一起工作呢？

在一个极端，我们可以采取这样的态度:如果 JavaScript 不适合他们，用户可以随时关闭它，只需编写适合视力正常的用户的脚本。

另一方面，我们可以牢记詹姆斯·爱德华兹的请求，为了最大化可访问性而完全避免使用 JavaScript。

我认为，鼓励初学者的方法介于两者之间。让他们意识到这个问题，在你的脚本中演示一些简单的方法来迎合屏幕阅读器用户(例如，通过使用左/屏幕外定位来隐藏元素而不是显示:无)，并使他们能够对他们自己的脚本的可访问性做出明智的决定。

这就是我们在书中对 JavaScript 和可访问性所采取的方法。每个例子都能在所有屏幕阅读器中完美运行吗？几乎可以肯定不是。但是键盘用户将被迎合，我们将为屏幕阅读器提供一个容易访问的选择。

请原谅我们为编写了唯一一本从第一页就赋予可访问性的初学者 JavaScript 书而感到自豪。

## 分享这篇文章