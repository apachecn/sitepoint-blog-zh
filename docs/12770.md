# ECMAScript Harmony:JavaScript 的新生

> 原文：<https://www.sitepoint.com/ecmascript-harmony-new-life-for-javascript/>

定义网络的核心标准一个接一个地获得新生。首先，W3C 重新启动了 HTML 的开发，放弃了对 XHTML 的专注，将 WHAT-WG 的 HTML 5 草案作为一个新的开始。现在，在 7 月底奥斯陆的一次会议上，长期分裂的负责 JavaScript 语言的标准机构通过妥协找到了新的统一。

描述 JavaScript 的标准称为 ECMAScript(因为“JavaScript”是 Sun Microsystems 拥有的商标)。ECMAScript 的最后一次完整更新(ECMA-262 第三版)于 1999 年发布。自发布以来的八年多时间里，JavaScript 作为 web 标准的进展几乎没有什么变化。

ECMAScript 在那时没有什么变化并不是因为语言的成熟。ECMA-262 第三版已经广泛认识到现实世界的浏览器多年来不得不解决的问题，所以有很多需要第四版。在缺乏一个规范的情况下，浏览器制造商不得不反向工程彼此的实现，以决定如何处理规范中的漏洞——对所有相关人员来说，这几乎是最糟糕的情况。

## TC39 的分裂

在这段时间里，ECMA 技术委员会 39 (TC39，负责开发 ECMAScript 标准的委员会)当然没有闲着。关于第四版 ECMA-262 应该具备哪些特性的讨论一直在不停地进行，但共识却难以达成。

在 TC39 中，来自 Mozilla、Adobe、Opera 和 Google 的代表希望对该语言进行重大改进，并在第三版发布后不久就开始汇编新功能列表。这些年来，许多特性(如名称空间、生成器、可选的静态类型和 getter/setter)已经被添加到 ECMAScript 的实际实现中(例如 Flash/Flex 中的 [ActionScript 3](http://blogs.adobe.com/kiwi/2006/05/as3_language_101_for_cc_coders.html) 、Firefox 中的 [JavaScript 1.7](http://developer.mozilla.org/en/docs/New_in_JavaScript_1.7) 和 [1.8](http://developer.mozilla.org/en/docs/New_in_JavaScript_1.8 "JavaScript 1.8") )。

但是并不是 TC39 中的每个人都喜欢这些变化。一年前，微软和雅虎的代表。在委员会中，他们认为对语言进行相对较小的更新，以解决在第三版中变得明显的问题更合适。例如，他们指出，如果有人试图在浏览器中实现 ActionScript 3 中实现的包、名称空间和早期绑定等功能，将会带来性能挑战。

委员会内部的这种分裂导致了两个小组的形成，每个小组都有自己的草案:那些寻求添加主要功能的人将其草案称为“ECMAScript 4.0”，而更保守的小组将其草案称为“ECMAScript 3.1”。但是只能有一个 ECMA-262 第四版，所以只要这两个小组并行工作，ECMAScript 的未来将仍然是未知的。

上个月在奥斯陆，这一切都改变了。请阅读下面的内容，找出发生了什么。

## 奥斯陆会议上的和谐

原本计划 7 月底在奥斯陆举行的分裂的 TC39 定期会议，对委员会和 JavaScript 语言本身来说是一个巨大的转折点——我们有充分的理由希望如此。

TC39 的成员一致认为，一个分裂的委员会对任何人都没有好处，并达成了一项协议，允许在“ECMAScript 3.1”的名称下继续为第四版 ECMA-262 制定一个单一的统一草案为了反映这代表的分歧的历史性解决，这一努力被称为 ECMAScript Harmony。

在和谐之下，辩论的每一方都做出了关键性的让步:

*   “ECMAScript 4.0”小组已经承认**包、名称空间和早期绑定都是不适合 Web 的特性。**它们已经被永久排除在 ECMA-262 标准的任何未来版本之外。
*   “ECMAScript 3.1”小组已经承认，为 ECMAScript 4.0 提出的一些特性确实有价值，这些特性将被重新处理，以包含在 ECMAScript 3.1 之后的版本中。随后的版本被称为“ES-harmony”。

展望未来，该委员会预计能够在 2009 年上半年推出第四版 ECMA-262，其中至少有两个现实世界的实现(即四个主要浏览器中的两个)。四个主要浏览器中至少有三个已经存在的新特性将不会被添加到这个版本中(例如，[的 getters 和 setter](http://developer.mozilla.org/en/docs/Core_JavaScript_1.5_Guide:Creating_New_Objects:Defining_Getters_and_Setters)将会出现)。

Mozilla 的 Brendan Eich 的官方声明可以在 Ajaxian 上阅读，其他 JavaScript 名人的反应，如[约翰·雷西格](http://ejohn.org/blog/ecmascript-harmony/)、[道格拉斯·克洛克福特](http://yuiblog.com/blog/2008/08/14/premature-standardization/)、[迈克·钱伯斯](http://www.mikechambers.com/blog/2008/08/14/actionscript-3-and-ecmascript-4/)和[亚历克斯·罗素](http://alex.dojotoolkit.org/2008/08/thoughts-on-harmony/)都值得一读(技术细节的水平各不相同)。此外，新推出的[开放网络播客](http://openwebpodcast.com/)的[第二集](http://openwebpodcast.com/episode-2-brendan-eich-and-arun-ranganathan-on-ecmascript-harmony)将这些人聚集在一起，讨论方向的改变。

## 这一切意味着什么？

抛开晦涩难懂的功能不谈，这里的要点是 TC39 已经意识到了 W3C 去年被迫意识到的关于 HTML 的同样的事情:**编写标准并希望浏览器会遵循是行不通的。无论 W3C 对 XHTML2 的推荐在纸面上看起来有多好，它都不会迫使微软在 Internet Explorer 中支持它。有了 HTML 5，W3C 让浏览器尝试新的想法，并从好的东西中锻造标准。**

类似地，TC39 的成员可以将世界上所有的特性添加到 ECMAScript 4.0 中，但是要想知道一个特性是否能在网络上工作，唯一的方法是先将其构建到现实世界的浏览器中。 *然后*你可以决定它是否应该成为标准。

现在是一个显而易见的问题:CSS 是下一个吗？我个人将在未来几周密切关注 W3C 的 CSS 工作组。

## 分享这篇文章