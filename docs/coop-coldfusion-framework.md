# 约翰·法勒谈 COOP ColdFusion 框架

> 原文：<https://www.sitepoint.com/coop-coldfusion-framework/>

COOP 是框架块上的新成员，旨在帮助开发人员快速建立并运行全功能原型。在这里，约翰·法勒展示了他的宝贝。

SitePoint: John，你能给我们讲讲你对 COOP 的电梯间推介吗？

COOP 背后的想法是“鼓舞人心的简单”——将设计师的表现与开发人员的逻辑分离开来。这项技术从强大的原型开始，以“preDOM”对象编码和干净的通用逻辑结束。该 API 使得编写具有内置 ColdFusion 标签中非标准功能的代码成为可能。

好的，你能告诉我们更多的主要特点吗？

我们认为 COOP 很容易学习和编码。你不必出去买一本书来理解如何使用这个框架构建好的应用程序。当然，您仍然可以这样做，但是编写好的代码不应该要求把事情做得如此复杂，以至于 CF 开发人员会陷入困境。这种简单性意味着您可以获得更快的投资回报，并让开发人员跟上速度，而无需让他们加入“极客之书”俱乐部。

COOP 的一个重要特性与设计和程序逻辑的适当封装和分离有关。在 AIR/Flex 社区引起巨大轰动的事情之一是 Thermo 项目(它允许设计师在可视化环境中为 Flex 应用程序创建界面)。不管它是如何完成的，这使得开发人员和设计人员可以在相同的页面上工作，而无需接触彼此的代码——或者不破坏彼此的代码。它们都处理不同的文件，但是它们可以并行工作。将设计与逻辑适当分离需要的不仅仅是 CSS，COOP 让这项任务变得异常简单。让我来解释一下。

用户请求一个页面。该页面在标记中包含 ColdFusion/COOP 标记代码。在代码运行到协处理器之前，调用与页面并行运行的 CFC 来管理逻辑。这是一个控制器，它允许像对待对象一样对待页面标签，这意味着您可以从协处理器设置数据和除标签 ID 之外的任何属性。执行标记代码后，将再次调用协处理器，然后将内容发送回用户。因此标记构成了请求的视图部分。如果需要你的模型(数据)，就通过协处理器调用它。是的，对于那些关心这些事情的人来说，它确实是一种 MVC 方法。

此外，该框架非常灵活。我们强烈提倡原型制作。当开发人员进行原型开发时，他们通常会在标记页面中隐藏大量“糟糕的代码”——我们稍后会将这些代码重新分解到协处理器中。在我们做原型的时候做这个没问题。这一过程可以比作不仅仅为汽车原型制造发动机缸体模具的逻辑。这意味着我们可以使用我们将在标记内的协处理器中设置的所有属性。当然，这并不优雅，但是在原型阶段更有效率！这就是 COOP 允许开发人员和设计人员实现更高生产力的方式——设计人员可以传入一个简单的数据字符串列表供原型使用，当原型获得批准时，开发人员可以用来自协处理器的真实数据替换或覆盖字符串列表。

最重要的特性列表的最后一项是使用 COOP 来集成 AJAX 库和封装定制标记和处理的价值。COOP 是可扩展的，并且是开源的。有一些模板可以用来构建你自己的标签。核心 CFC 协处理器和一些附加 CFC 的特性使集成更加容易。底线是，这是将 AJAX 库打包到 ColdFusion 中的一个很好的方法。

COOP 是怎么来的？为什么要创造它？

嗯，我们和大多数程序员一样有不良记录。很久以前，我们用过程化编程来编程。在那之后，其他公司来到 SOSensible，要求我们捡起用过程化编程创建的其他项目的碎片。这个简单的概念并不需要复杂的代码，但是它已经开始发生了。我们也想帮助我们的许多客户，他们没有在设计模式的更好的方面和其他使开发人员成为超级极客的事情上受过训练的员工。我们想让. NET 或 PHP 或 Java 或普通 CF 开发人员可以直接进入并“获得”项目。

你是如何参与其中的？

我是 COOP 的创造者。我大约在 1977 年开始编写软件。我在许多平台上工作过，使用过许多代码语言。也许这种对开发的广泛观点驱使我提供没有复杂、难以理解的编码方法的结构。请不要误解我——我认为设计模式非常有用。这不会是大多数人写代码的方式。主持我婚礼的牧师说，“伟大是简单中蕴含的深刻，”对我来说，这就是 COOP 的意义。它实际上是为我的公司设计的。它的设计目的是让它可以用来为客户替换混乱网站的单个页面，并建立整个网站，这是一种建立和重新访问的乐趣。我们认为这是如此令人兴奋，我们只是想与 ColdFusion 世界的其他人分享它。

开发商应该在什么时候寻求合作？它有什么好处呢？

那么，作为一名开发人员，难道您不想把表示元素当作对象来编程吗？这意味着您可以从业务逻辑中将数据集合分配给标记页面元素的 ID，然后设置该元素的默认或当前 ID。这就是我们所说的“前王国”。假设我们有一组单选按钮，并在标记页面上使用了`<coop:radiobuttonlist id="idOption"/>`标签。在我们协处理器的`onPageStart()`方法中，我们可以使用 api 域变量`_init`为元素设置数据。它应该是这样的:`_init.idOption.data = getOptions()`。在内部，COOP 标签可以处理像单选列表这样的事情。它们很聪明地发现数据是列表、数组还是基于查询的记录集，并相应地处理它。

COOP 的其他很酷的特性包括 ICE (Integrated COOP Element)库，它为 jQuery 插件、timyMCE 等东西打包了 COOP 扩展的域。COOP 使用 sosContent 标签来确保开发人员和设计人员能够更容易地集成 JavaScript 和 CSS。核心是开源的，它的设计使得定制通常不会与核心升级相冲突。COOP 还拥有强大的工具包功能，允许开发人员将通用元素打包并在页面上放置一个标签，而不是粘贴大量代码。

从设计者的角度来看也有好处，这对开发人员来说也是一个好处，因为它创造了一个更好的工作环境。我们采访的设计师一遍又一遍地告诉我们，他们不喜欢编程。问题是，即使他们没有编写自动化的 web 应用程序，他们也必须在不弄乱代码的情况下仔细阅读我们的代码。COOP 让设计师专注于展示，而我们专注于交付内容和业务逻辑。

使用 COOP 的先决条件是什么？有什么对新用户有帮助的先验知识吗？它是否使用了其他可以简化或复杂化事情的框架？

COOP 将与其他框架一起工作，但它不需要任何框架。您可以在其他框架中使用 COOP，也可以在 COOP 中使用数据库框架或 IOC 框架。底线是它集成并允许集成。

是什么让 COOP 有别于其他框架？

如果你喜欢 ColdFusion，我们认为你会被 COOP 吸引。我们喜欢说 COOP 认为 ColdFusion 比其他任何框架都更像 cold fusion。这并没有从其他伟大的作品中拿走任何东西——事实上，我们希望他们也能得到这个概念，为他们的用户创造一些同样的东西。如果你想集成一个 Ajax 库并以 ColdFusion 的方式实现，这可能是最简单的框架。查看我们的一些 ICE 库，您将看到 Ajax 库的简单功能与标记的类对象交互相结合。标签可以更新。可以添加新标签。没有必要等待 ColdFusion 的下一个版本发布！这是一个真正的开源解决方案，在共享主机上和在企业中一样好用。

有没有很棒的 COOP“在野外”的例子？

嗯，我们还没有亚马逊或谷歌的产品…

当然可以。嗯，你能给我们看一些代码吗？

没问题。下面是我们如何构建一个简单的合作页面片段。

```
**OLD SCHOOL (Procedural code at its finest)**  

<cfoputput>  

<select id="myFruit" name="myFruit">  

<cfloop list="apple,blue berry,cherry,mango,orange" index="iFruit">  

<option value="#iFruit#">#iFruit#</option>  

</cfloop>  

</select>  

</cfoutput>  

**STAGE 1: Prototype**  

<!--- Notes:  

We're passing in a list but coop can spot  

if the data is a list, array of structures or   

a query and knows how to handle that internally.  

--->  

<coop:selectlist id="myFruit"  

data="apple,blue berry,cherry,mango,orange">  

**STAGE 2: Refactor**  

(in the word of one developer, the markup page gets "elegant")  

<coop:selectList id="myFruit">  

(the co-processor page now adds this...)  

<cfscript>  

/* The init is the base for the page object modeling.  

* The myFruit is the ID of the object we are programming  

* before creating it. A.K.A. preDOM.  

*/  

_init.myFruit.data = "apple,blue berry,cherry,mango,orange";  

</cfscript>  

**STAGE 3: Enhance**  

<cfscript>  

/* Enhancing the code to real data and control  

* by setting attributes in code rather than   

* inside the markup.  

*/  

_init.myFruit.data = getFruit();  

_init.myFruit.valueField = "fruit_ID"; // id field in recordset  

_init.myFruit.displayField = "fruit"; // fruit name in recordset  

</cfscript>
```

COOP 中内置并简化了更多功能。用这种技术编程网站，让设计者不用 if…then…else、循环和我们开发人员喜欢的所有东西就能完成他们的工作。它让我们不用接触设计就能控制页面，所以我们不会因为弄乱了外观和感觉而经常受到指责！如果你独自在一个网站上工作，它让你一次只专注于一件事，这很好。

许可、社区、支持和文档呢？

我们的许可模式在 http://coop.riaforge.org/上市——基本上 COOP 是我们的创造，使用它没有责任，欢迎其他人在任何项目中使用它。如果用户有任何问题，他们可以直接提问，我们会让他们知道它在您的场景中是如何工作的。为了获得社区支持，我们成立了一个 Google group，初始文档可以从我们的网站上获得。

COOP 的未来会怎样？

首先，我们正在开发 2.0 版本，但我们将发布 1.5 版本，为将来铺平道路。这些变化包括一些简单的事情，比如在后端进行完整的单元测试，以测试所有的 COOP CFCs 和标签。我们重新设计了我们的代码库，使其更具可移植性，并且不局限于网站的根目录。我们现在处理数组的结构，并且可以使用 XML 作为数据源。在文档方面，我们正在为平台的每个标签和方面制作 YouTube 视频。

在未来，我们将实现 ICE 库(TinyMCE，jQuery Grid，Flot，YUI 编辑器，以及更多其他工具)。这些是 COOP 风格的库(“ColdFusionified”，如果你喜欢的话)，集合已经被重新分解，以便通过 ColdFusion 的力量更容易工作和更有效地集成。

人们在哪里可以找到更多关于 COOP 的信息？

COOP 的基地是 http://coop.riaforge.org，那里的维基，在 http://coop.riaforge.org/wiki/的 T2，提供了很多信息。

我们的初始文档可以在我们的网站上找到——它们应该可以让大多数人开始。

同样，查看即将发布的 YouTube 视频，了解如何使用 COOP 的所有强大功能的更多细节。如果你想加入 1.5 测试版，[去我们位于 RIAForge 的论坛](http://coop.riaforge.org/forums/messages.cfm?threadid=9A16A8A5-E3D0-00E7-D724D5391E3AAE41)。查理的 UGTV 上还有一个[的在线演示，从产品升级到 1.0 版本开始。](http://experts.acrobat.com/p75325859/)

## 分享这篇文章