# 实用网页设计——弹出窗口:恶意还是恶意？

> 原文：<https://www.sitepoint.com/pop-ups-malign-maligned/>

你了解他们，也可能讨厌他们。

弹出窗口是那些烦人的小生物，它们在我们无辜地冲浪时偷偷溜进来。他们要么跳到我们面前，喊着，“看着我！你看我！”同时把任何东西的广告推到我们面前，或者它们潜伏在我们的浏览器窗口下，等着我们关闭浏览器，这样它们就可以敲击我们的虚拟手指，引起我们的注意。

他们吸走宝贵的带宽和系统资源，他们让我们发狂，他们强迫我们安装弹出杀手来阻止他们。Earthlink 和 America Online 等互联网服务提供商现在向他们的客户提供弹出窗口拦截软件。一些商业网站，包括 [SitePoint](https://www.sitepoint.com) 和 [iVillage](http://www.ivillage.com) ，拒绝托管弹出窗口(尽管 iVillage 似乎违背了他们的承诺)。一些冲浪者因为这样或那样的原因保持禁用脚本；这使得弹出窗口变得毫无意义。浏览器 Mozilla 提供了一个阻止弹出窗口出现的选项。主要的免费软件和共享软件网站将弹出杀手程序列为客户的长期最爱。

事实上，反对弹出窗口的理由被认为是多余的(想象一下，可怜的弹出窗口设计者被一群流口水的狗追着穿过荒野，一群愤怒的网络冲浪者在不远处挥舞着火把和干草叉)。

但是弹出窗口都是不好的吗？除了发誓要避免弹出窗口之外，新兴的网页设计师不能对弹出窗口做任何事情吗？

答案是有条件的“是”。好吧，一个非常合格的“是”。当然，我们必须记住，弹出窗口最初会引起快速而强烈的负面反应，因此，应该谨慎使用，并且总是有明确的目的。但是弹出窗口确实有一些用途，并且是任何设计师的能力库的便利补充。我们来看看为什么。

##### 弹出窗口的基础知识

当你把网上冲浪者的情感包袱从弹出窗口的想法中剥离出来时，它是一个非常简单的设备。根据 [Webopedia](http://www.webopedia.com) 的说法，弹出窗口是“当你用鼠标选择一个选项或按下一个特殊功能键时突然出现(弹出)的窗口”。

当然，当我们浏览时，弹出广告并不遵循这一规则，因为它经常被编码为在没有用户任何输入的情况下出现——我们没有要求它就得到它。大多数弹出窗口是动态生成的“附属”网页，它们动态打开(即无需任何用户输入)并显示在所访问页面的上方或下方。

这很好地描述了普通的弹出广告。但这并不是使用弹出窗口的唯一方式。

这是一个简单、简洁的弹出窗口的基本代码，它会在加载父页面时自动出现，抄袭自 Joe Burns 的 [HTML Goodies](http://www.htmlgoodies.com/beyond/openwin.html) 站点:

```
<SCRIPT LANGUAGE="javascript"> 

<!-- 

window.open ('page.html') 

--> 

</SCRIPT>
```

当然，您必须创建`page.html`页面，但是没有什么比快速弹出一个小的弹出页面并将该代码片段插入到主页面的代码中更容易的了。然而，大多数弹出窗口都没有滚动条、工具栏之类的东西，那么如果没有这些功能，您如何构建自己的弹出窗口呢？毕竟，我们的想法是让它们变得简单——阅读弹出窗口中的信息，然后继续。Joe 向我们展示了:

```
<SCRIPT LANGUAGE="javascript"> 

<!-- 

window.open ('page.html', 'newwindow', config='height=100, 

width=400, toolbar=no, menubar=no, scrollbars=no, resizable=no, 

location=no, directories=no, status=no') 

--> 

</SCRIPT>
```

现在，弹出窗口代码配置了许多修改和控制窗口外观的属性。`window.open`命令打开一个新窗口，如最后一段代码所示。`newwindow`是窗口的名称，JavaScript 需要它才能正常运行。如果你懂 JavaScript，那你就知道为什么了。如果没有，那么就不值得担心，只需添加命令并继续前进。

上面的`config`命令配置如下:

*   `height`为 100，*   `width`为 400，并且*   浏览器窗口的所有其他常见属性，包括`toolbar`、`menubar`、`scrollars`、调整大小的能力、`location`或地址栏、`directories`或书签栏以及`status`栏，都不会出现。

您可以根据自己的意愿更改属性——更改高度和宽度，允许滚动条、工具栏等——只需更改属性编号，或者根据需要将`no`更改为`yes`。根据经验，弹出窗口应该只是一个额外的窗口，浏览一次就关闭了，所以通常不需要工具栏、菜单栏等等。

如果你真的想被剥离下来，你可以在 IE 5 和更高版本中创建一个甚至没有标题栏的窗口。但是由于这将剥夺用户通过点击 X 关闭窗口的能力，我建议避免这个选项，(当然，除非你想让你的访问者感到沮丧)。

Joe 为我们提供了许多扩展这个基本脚本的方法，但是由于它们对于功能性来说不是必需的，所以我将让您自己探索它们。例如，如果您想创建弹出窗口，请查看参考书目中解释代码的 HTML Goodies 站点的链接。我个人看不出 pop-under 有什么可取之处。使用它，或者任何其他的弹出式代码，风险自负，如果你疏远了你的访问者，不要怪我，或者 SitePoint！

你也可以访问 About 的[弹出窗口生成器](http://javascript.about.com/library/tools/blpopwindow_maker.htm)来创建你自己的代码，而不必弄脏你的手，或者使用一个为你创建它们的程序。此外，还有许多其他更复杂的脚本，它们用更多的附加功能来完成这项工作。下面我列举了几个，还有很多。你说了算。

##### 多种用途

弹出窗口的一个有趣用途是创建显示一系列图形或文本块的“幻灯片”。想象一下网页的幻灯片。想做一个假期幻灯片？“这是格雷西阿姨被老忠实信徒淋湿的地方，下一张是爸爸被熊追赶的时候。”任何形式的演示，无论是商业的还是非商业的，都可以通过使用幻灯片技术来处理，就像一系列的证明或一个简短的指导课程一样。到 Katsuey 的设计作品的相应页面寻找免费代码，以及如何使用它的教程。

[JavaScript 源代码](http://javascript.internet.com/page-details/smart-popup.html)给了我们一个非常可控的小弹出窗口，它可以在点击或者鼠标经过时出现。虽然你可以，如果你愿意，在窗口放一个伟哥的广告或者一个摄像头，但是你可能会更好地使用它来显示这样或那样的边栏信息。

弹出窗口的另一个有用的功能是提供关于单词、短语或图形的附加信息，这在 Chris Churchill 2002 年 12 月的文章“用 DHTML 创建弹出注释[中有详细描述 Chris 告诉我们如何创建光滑的小“便利贴”,当点击时弹出来定义一个术语或首字母缩写词，提供一个脚注或侧边栏，或任何其他你喜欢的东西。这里需要注意的是，设计者必须同时使用 JavaScript 和 CSS 来使用这种特殊的技术。然而，这是一个非常有用却未被充分利用的效果。](https://www.sitepoint.com/blog/)

很多很多商业网站都使用这样或那样的弹出窗口。大多数用它们做广告(“看美丽的塔斯曼岛！”"在一次简单的治疗中提高你的网页设计技能 300% . ").其他人用它们向访问者发布时间敏感的公告。而且，信不信由你，弹出式广告确实有效。根据 2003 年 2 月的一项研究，弹出式菜单和弹出式菜单的点击率高达 6.5%，考虑到人们对这些东西的厌恶程度，这已经很可观了。Orbitz 甚至在营销“弹出窗口”,这种窗口不需要点击就能把访问者带到想要的网站。不过，我无法想象这些东西的用途不涉及广告，也不会激怒网络用户。

然而，弹出式广告的商业用途并不局限于不受欢迎的广告。例如，一个销售摇椅的网站可能会显示网站上销售的每个摇椅的图片，点击每张图片就可以将该商品添加到买家的购物车中。暂时售完或不可用的项目可能会在单击时出现弹出窗口，以提醒用户该特定摇椅此时不可用。用户可以简单地关闭窗口并继续购物。代码很简单，很容易滑入和滑出页面，因此网站管理员可以随着项目可用性的变化随时添加和删除弹出窗口。

网站工具提供商 Bravenet 在你注册他们的服务时会显示一个弹出窗口。当您注册并选择用户名时，您可以在继续之前点击“名称检查”框。将出现一个弹出窗口，通知您所选择的用户名是否可用。这可以省去您填写用户表单、提交表单以及因为您选择的用户名已被占用而被拒绝的麻烦。

这些只是实际有用的商业实现的弹出窗口的几个例子。我很想知道更多。

##### 个案研究:罗比的自由背景

几个月前，我有幸被美国南部的平面设计师 Robby 联系到。他拥有并经营着一个网站，为任何来访的人提供免费和低成本的图片。除了他制作可爱的图形这一事实之外，有一件事立即吸引了我，那就是他使用弹出窗口来展示他的创作。随着时间的推移，我收集了许多免费的图形，我无法告诉你这个过程有多糟糕。但是 Robby 使用了弹出技术来使它变得简单和没有痛苦。

每个图形都以缩略图视图显示；要以正确的大小查看图形，只需单击图形，它就会显示在弹出窗口中。喜欢就省省吧。如果你没有，就关闭窗口，进入下一个吸引你注意力的图形。没有比这更简单的了。

像我一样，罗比不是代码战士；他使用一个名为 [Ace PopUp](http://www.cgiscript.net/site_software.htm) 的免费弹出生成器来创建他的代码。“我不必告诉你，在你建立了一个网站之后……你会不断地进去，改变周围的东西，”他写给我。“我不断改变我的弹出窗口，因为我想确保它们被搜索引擎搜索到。”

后来在他的笔记中，他继续给了我这个过程的更多细节:“在我的图片背景(日落和风景)上，我只是在一个新的窗口中瞄准它们，`target="new"`，然后如果我想要一个气球标签来描述这个链接，我只需要使用`title="description of link"`。这个比较基础但是很有效。

从我的软件链接页面，我使用另一个脚本，将每个页面的中心和大小。脚本的一部分放在头部分，其余部分放在链接所在的主体部分。说实话，我不认为我得到了一个完全正确的，但它按照我想要的方式工作，因为我喜欢一个你可以调整大小的窗口，它会在屏幕上找到你想要的位置，而不是失控。我喜欢这个弹出脚本的一点是，你知道窗口总是在页面的正中央。"

我在一个 13 岁的教室里实地测试了罗比的代码；他们可以毫不费力地使用 Robby 的弹出窗口快速轻松地选择他们喜欢的网络背景。如果他们可以这么容易地使用它，你的网站访问者也可以。

##### 结论

钢可以用来制造犁头或剑。同样的道理，弹出窗口可以用来让你的访客体验更加丰富和有收获，也可以用来激怒他们。

如果你能摆脱弹出窗口只不过是广告的场所的想法，那么你可以找到它们的许多用途，这将增强和丰富你的网站。上面列出了一些。如果你想到了别人，请告诉我！

##### 参考书目和进一步阅读

王牌弹出生成器软件
[http://www.cgiscript.net/site_software.htm](http://www.cgiscript.net/site_software.htm)

勇敢网
[http://www.bravenet.com/](http://www.bravenet.com/)

用 DHTML
[https://www.sitepoint.com/article/965](https://www.sitepoint.com/blog/)创建弹出便笺

DHTML 模态对话框
[http://JavaScript . about . com/library/scripts/blmodaldialog . htm](http://javascript.about.com/library/scripts/blmodaldialog.htm)

如何制作一个烦人的网页
[http://www.users.nac.net/falken/annoying/main.html](http://www.users.nac.net/falken/annoying/main.html)

如何制作弹出窗口
[http://junior.apk.net/~jbarta/popup/](http://junior.apk.net/~jbarta/popup/)

JS 弹出代码
[http://www.webmasterbase.com/static/popjs.php?](http://www.webmasterbase.com/static/popjs.php?)

Katsuey 的设计作品
[http://www.katsueydesignworks.com/](http://www.katsueydesignworks.com/)

完美弹出
[https://www.sitepoint.com/article/955](https://www.sitepoint.com/blog/)

流行的弹出式菜单？
[http://www.internetnews.com/ec-news/article.php/1561411](http://www.internetnews.com/ec-news/article.php/1561411)

弹出窗口制作人
http://JavaScript . about . com/library/tools/blpopwindow _ Maker . htm

弹出脚本窗口
[http://javascript.about.com/library/scripts/blpopwindow.htm](http://javascript.about.com/library/scripts/blpopwindow.htm)

网页设计师弹出幻灯片教程
[http://www.katsueydesignworks.com/cgipopup.htm](http://www.katsueydesignworks.com/cgipopup.htm)

弹出式广告、弹出式广告、叠加广告和侵入式广告的利弊
[http://webdesign.about.com/library/weekly/aa021703a.htm](http://webdesign.about.com/library/weekly/aa021703a.htm)

罗比的自由背景
[http://www.lowbudgetdesigns.com/](http://www.lowbudgetdesigns.com/)

SitePoint:隐私政策
[https://www.sitepoint.com/privacy.php](https://www.sitepoint.com/privacy.php)

所以，你想要一个弹窗，嗯？
[http://www.htmlgoodies.com/beyond/popunder.html](http://www.htmlgoodies.com/beyond/popunder.html)

你想打开窗户吗？
[http://www.htmlgoodies.com/beyond/openwin.html](http://www.htmlgoodies.com/beyond/openwin.html)

网页设计洞察:高级:JavaScript:弹出窗口
[http://insights.iwarp.com/advanced/javascript/popupwin.html](http://insights.iwarp.com/advanced/javascript/popupwin.html)

## 分享这篇文章