# W3C 可访问性指南

> 原文：<https://www.sitepoint.com/w3c-accessibility-guidelines/>

可访问性可以被认为是“不管情况或环境而提供访问”在万维网的背景下，可访问性是对访问、阅读和理解网站内容的难易程度的一种度量。

网站的可访问性因以下事实而变得复杂:网站与其说是一个已出版的作品，不如说是一个活的文档，可以被不同的浏览器和不同的平台以不同的方式解读。网络不是印刷媒体——尽管网站通常以视觉方式阅读，但有许多不同的方式可以体验网页，例如通过语音浏览器或索引机器人。

##### 指导方针

1999 年 5 月 5 日， [W3C](http://www.w3c.org) 发布了一个关于网页内容可访问性的建议，旨在指导网站所有者进行可访问性审查。该规范包含十四条指导原则，它们构成了无障碍设计的一般原则。每个指南都与一个或多个检查点相关联，这些检查点描述了如何将该指南应用于网页的特定功能。

***1。提供听觉和视觉内容的等效替代方案***

尽管有些用户可能无法访问页面上的听觉或视觉内容，但他们可能能够使用等效的内容。例如，假设您有一个指向屏幕左侧的图形箭头，向用户表示如果他们单击箭头就可以返回。根据指导方针，你应该提供一个替代图形的文本，比如“返回上一页”。

提供文本的非文本等同物(例如图片、视频和预先录制的音频)对许多用户也是有益的，尤其是那些阅读困难或不可能的用户。在电影或视觉演示中，肢体语言和其他视觉线索等动作可能没有足够的听觉信息来传达相同的信息。除非提供这种视觉信息的口头描述，否则看不到(或看不到)视觉内容的人将无法感知它。

***2。不要光靠颜色***

对于色盲用户来说，网络体验不仅仅是令人沮丧。问题可能像不可见的文本和导航一样严重，会导致网站完全不可用。因此，指南建议我们避免使用颜色作为视觉线索。然而，如果你这样做，确保你已经提供了足够的替代线索。如果你在文本或背景中使用红色或绿色的阴影，使用它们和更安全的颜色(例如，白色背景上的暗红色文本)。并确保背景和前景文本或图形之间有强烈的对比。

***3。正确使用标记和样式表***

确保在创建文档时使用正确的结构元素。用样式表而不是用表示元素和属性来控制表示。通过这种方式，你可以区分内容、结构和表现，并允许使用专门软件的用户理解页面的组织，并在其中导航。

***4。澄清自然语言的用法***

使用 LANG 标签标识文档头中的语言，这允许您预先指定在文档中使用的语言。该属性的值可以是任何 ISO 标准的双字符语言缩写，例如，LANG=EN 告诉浏览器文档是用英语编写的。此外，确保在文档中第一次出现缩写时指定缩写，使用缩写的标题属性和 ABBR 标签。

***5。创建优雅变换的表格***

表格应该用于保存表格数据，而不仅仅是为了实现某种表示风格或设计。如果您必须使用它们进行布局，请尝试创建线性化后有意义的表格，或者提供页面的非表格版本。否则，如果您有包含列的报纸样式的表格，语音阅读器可能会先阅读每列的第一行，然后再阅读第二行，这对正在收听该内容的人来说没有多大意义。此外，请确保您为所包含的任何数据表提供了摘要。

***6。确保采用新技术的页面优雅地转变***

当你开发一个网站，并试图把你能做的一切都整合到网站上，只是为了证明你是最新的技术，确保旧版本的浏览器仍然能够浏览网页。这也将影响那些选择关闭特定功能(如 Java、JavaScript、图像等)的新浏览器用户。).

***7。确保用户控制对时间敏感的内容更改***

确保可以暂停或停止移动、闪烁、滚动或自动更新对象或页面。一些有认知或视觉障碍的人不能足够快地阅读移动的文本，或者根本不能。移动还会导致注意力分散，对于有认知障碍的人来说，页面的其余部分变得无法阅读。屏幕阅读器无法阅读移动的文本。身体残疾的人可能无法快速或准确地与移动的物体互动。

***8。确保嵌入式用户界面的直接可访问性***

确保用户界面遵循无障碍设计的原则:独立于设备的功能访问、键盘可操作性和自发声等。当一个嵌入对象有自己的“界面”时，这个界面——就像浏览器本身的界面一样——必须是可访问的。如果嵌入对象的接口不可访问，则必须提供一个替代的、可访问的解决方案。换句话说，所有嵌入到页面中的接口(小程序、插件等。)也应该遵循这些关于可访问性的指南。

***9。独立于设备的设计***

独立于设备的访问意味着用户可以使用首选的输入(或输出)设备(例如，鼠标、键盘、语音或读写头)与用户代理或文档进行交互。例如，如果表单控件只能用鼠标或其他定点设备激活，那么使用页面的人如果看不到、用不到语音输入、用不到键盘或其他非定点输入设备，就无法访问表单。为图像映射或用作链接的图像提供文本等价物，使得用户可以在没有定点设备的情况下与这些元素进行交互。通常，允许键盘交互的页面也可以通过语音输入或命令行界面来访问。

***10。使用临时解决方案***

使用临时辅助功能解决方案，以便辅助技术和旧浏览器能够正常运行。例如，旧的浏览器不允许用户导航到空的编辑框。旧的屏幕阅读器将连续链接的列表作为一个链接读取，这使得这些活动元素很难或不可能被访问。此外，改变当前窗口或弹出新窗口会让用户感到迷惑，因为他们看不到这种情况的发生。

***11。使用 W3C 技术和指南***

这一条很明显:照他们说的做，或者做一些接近他们所说的事情。

***12。提供背景和方位信息***

将元素分组并提供关于它们之间关系的上下文信息对所有用户都很有用。对于有认知障碍或视觉障碍的人来说，页面各部分之间的复杂关系可能难以理解。试着给你的框架命名，并描述它们的目的和相互关系(如果从标题上看不明显的话)。此外，将大块信息分成小块。

***13。提供清晰的导航机制***

提供清晰一致的导航机制——方向信息、导航条、网站地图等。—增加人们找到他们正在寻找的东西的可能性。

***14。确保文件清晰简单***

使用清晰简单的语言可以促进有效的交流。对于有认知或学习障碍的人来说，获取书面信息可能很困难。使用清晰简单的语言也有利于母语不同于你的人，包括那些主要用手语交流的人。争取清晰准确的标题和链接描述。这包括使用在断章取义时有意义的简明链接短语，或者作为一系列链接的一部分(一些用户通过从一个链接跳到另一个链接来浏览，并且只听链接文本)。使用信息标题，这样用户可以快速浏览网页，而不是细读。

##### 总之…

鉴于未来的不确定性，无障碍设计是为下一代用户界面(如智能代理和语音识别)做准备的主要方式。几年后，人们将通过电话或在开车时使用语音输入和输出来操作计算机。这些技术还处于起步阶段，但是它们使用了与辅助工具相同的技术。事实上，残疾人已经使用这些开创性的技术很多年了，有一天这些技术会变得很普遍。

本文的内容来自以下资源:

[万维网联盟网站](http://www.w3c.org)

[微软公司无障碍网站](http://www.microsoft.com/enable)

**残疾人无障碍上网**
Michael g . paci ello
2000 年 10 月
CMP Books；ISBN: 1929629087

## 分享这篇文章