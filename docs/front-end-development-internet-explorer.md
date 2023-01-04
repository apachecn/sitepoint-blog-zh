# Internet Explorer 之后的前端开发

> 原文：<https://www.sitepoint.com/front-end-development-internet-explorer/>

![Internet Explorer as an old man with the ducks](img/113308ffe9740f76027c3c698d1561a1.png)

SitePoint/ [Natalia Balska 的作品](http://sf-lab.net/)

欢迎来到这个小系列的第二部分，致力于社区所说的 *oldIE* (即 Internet Explorer 直到并包括版本 10)的开始和结束。[我们系列的第一部分](https://www.sitepoint.com/cleaning-house-after-internet-explorer/)列出了为了简化前端开发过程可以丢弃的过时技术和实践。它也在评论中引发了一场非常有趣的讨论，这让我添加了以下免责声明(事后看来，它也应该包括在第一部分中):

关于是否放弃对 oldIE 的支持的决策过程是一个完全不同的主题，这不是本系列主题的一部分。如果你的网站或应用程序的细节迫使你保持支持，我同情你。如果你能永远结束这一章，那就加入我们吧。

也就是说，今天我们将开始探索我们现在可以不受限制地免费使用的一长串现代功能。非常感谢[CanIUse.com](http://caniuse.com/)的创建者——没有他们这个奇妙的工具，这篇文章将会很难被恰当地记录。

我们将这些功能分为四大类:

1.  现代 CSS 属性和功能；
2.  HTML5 特性；
3.  JavaScript 特性…
4.  …以及任何不符合上述条件的东西。

为了保持整洁，我们将只讨论那些在 *oldIE* 上缺乏适当支持，但可以在 Internet Explorer 11 或微软 Edge 上安全使用的功能。在本文中，我们将研究前两个特性组。让表演开始吧。

## 1.现代 CSS 属性和功能

对旧版本的抱怨主要来源于它对现代 CSS 属性和特性的不良支持，这是由于更新周期太慢。从这个地区开始我们的旅程是有意义的。

### flex box(flex box)的缩写形式

继表格和浮动之后，Flexbox 是创建 web 布局的第三个主要步骤。Foundation 6 已经提供了一个由 Flexbox 供电的备用电网，未来的 Bootstrap 版本 4 将会包含相同的功能。也有许多基于 Flexbox 的小网格，其中一些已经在 SitePoint 上提到过，比如 [sGrid](https://www.sitepoint.com/introducing-sgrid-a-stylus-based-flexbox-grid-system/) 。

仍然有[问题和缺陷](http://caniuse.com/#search=flexbox)(特别是对于 Internet Explorer 11 的实现)，但是它们被很好地记录下来，人们可以找到解决它们的方法。因此，如果您还没有体验过 Flexbox，现在是时候开始尝试并在您的项目中使用它了。这里有一些资源可供您参考:

*   [通过去除芥蒂迁移到 Flexbox](https://www.sitepoint.com/migrating-flexbox-cutting-mustard/)
*   [使用带指南针的 Flexbox 的初级读本](https://www.sitepoint.com/a-primer-on-using-flexbox-with-compass/)
*   [Flexbox Froggy——学习 CSS flexbox 的游戏](http://flexboxfroggy.com/)

### CSS 渐变

CSS 渐变允许您在两种或多种指定颜色之间创建平滑的视觉过渡。通过一点创意，你也可以生成像其他`background-image`一样可以重复的图案。不再需要为页面背景、按钮或其他 UI 元素切割图像。只要在适当的位置画一个渐变就可以了。如果您不知道从哪里开始了解它们，这里有一些有用的链接:

*   CSS 渐变:语法速成班
*   [Lea Verou 的渐变画廊](https://lea.verou.me/css3patterns/)

### CSS 动画

很长一段时间，网络上的动画都是通过使用 Flash 或 JavaScript 来实现的。CSS3 用[动画模块](https://www.w3.org/TR/2009/WD-css3-animations-20090320/)给我们带来了另一种选择。由于 Flash 被放到了后排座位上，CSS 动画相对于 JavaScript 驱动的动画的主要优势在于它们是在不同的线程中执行的，不会阻塞页面上的其余脚本。[并不是每个酒店都可以制作动画](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_animated_properties)和[有些酒店比其他酒店需要更高的演出价格](http://csstriggers.com/)。现代浏览器提供了更多帮助，内置了对硬件加速的支持。这意味着，当适当调整时，CSS 动画可以非常流畅(至少 60 fps)，并且对性能的影响最小。这个话题最近在 SitePoint 上非常受欢迎，有许多文章讨论了调整 CSS 动画的优点。这里仅举几个例子:

*   [CSS 动画硬件加速简介](https://www.sitepoint.com/introduction-to-hardware-acceleration-css-animations/)
*   CSS 大师的动画建议
*   [将 CSS 动画与 HTML5 音频同步](https://www.sitepoint.com/syncing-css-animations-with-html5-audio/)

### 2D/3D 变换和过渡

变换和过渡是 CSS 动画的主要组成部分。通过适当的关心和对细节的关注，我们可以做一些不久前还需要 Flash 的事情。虽然这不是唯一可用的现代动画技术(想到了 SVG、Canvas 和各种 JavaScript 技术)，但它是最容易学习、使用和维护的技术之一。SitePoint 的其他作者已经写了大量关于这些主题的文章，使得每个人都可以很容易地开始学习这些有用的技术:

*   [Craig Buckler 关于 CSS3 过渡的系列文章](https://www.sitepoint.com/css3-transitions-101/)
*   [关于 CSS3 转换的初级读本](https://www.sitepoint.com/the-definitive-guide-to-css-transitions/)
*   [CSS 过渡的权威指南](https://www.sitepoint.com/the-definitive-guide-to-css-transitions/)

### 边框图像

改变边框外观的常用方法是使用`border-style`属性的可用选项。在*老古董*的时代，添加花哨的装饰元素(比如自定义边框)的唯一方法是使用复杂的标记结构和切片图像。今天，我们可以使用另一种基于`border-image`属性的技术，而没有进一步的限制。这种技术允许你拍摄一幅图像，将它切割成虚拟切片，然后用这些切片来构成边框。与旧技术没有太大区别，但是没有杂乱的标记，只使用一张图片。

这里有一些文章会教你所有你需要知道的关于`border-image`的事情:

*   [用 CSS 边框图片装饰网页](https://www.sitepoint.com/decorating-the-web-with-css-border-images/)
*   [CSS3 边框图像](https://www.sitepoint.com/css3-border-image/)
*   [漂亮、灵活的盒子的 CSS3 边框图像](https://www.sitepoint.com/css3-border-images/)

### 多栏布局

尽管网络大受欢迎，印刷杂志仍然有自己的魅力——一个很好的原因是精心制作的布局，通常分为多个栏目。数字媒体长期以来试图模仿印刷世界的选项。今天，我们正在收集这项工作的第一批成果，因为我们现在可以利用这个选项将内容安排在多个列中。如果你不知道从哪里开始，这里有一些提示:

*   [CSS3 列和分页可回流内容](https://www.sitepoint.com/css3-columns-and-paged-reflowable-content/)
*   [响应友好的 CSS 专栏指南(CSS 窍门)](https://css-tricks.com/guide-responsive-friendly-css-columns/)
*   [使用 CSS 多栏布局(MDN)](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Columns/Using_multi-column_layouts)

### Rem 和视口单位

CSS 单元是一个大家庭，即使大多数开发人员大多熟悉像素和(可能)ems。随着大坏蛋*老爹*退休，我们现在可以让更多的兄弟姐妹出来玩，尤其是雷姆和视口单位。让我们看看这些单元是什么，我们可以用它们做什么。

*   [看看 CSS 中的长度单位](https://www.sitepoint.com/look-at-length-units-in-css/)
*   [理解和使用 CSS 中的 rem 单位](https://www.sitepoint.com/understanding-and-using-rem-units-in-css/)

### 指针事件

在很长一段时间里，JavaScript 是除了链接和按钮之外为元素添加交互性的主要方式。随着指针事件的广泛采用，情况发生了变化。尽管指针事件起源于 SVG，但它允许开发人员指定图形元素是否/何时可以响应鼠标事件。这可以有有趣的应用，像这个[火星望远镜观看伦敦奥运会](https://blogs.sitepointstatic.com/examples/tech/css3-pointer-events/index.html)。微软的人也将指针事件视为统一交互的一种方式，无论是使用触摸、鼠标、手写笔还是图形笔。让我们看看还能做些什么:

*   [CSS3 Gems:指针事件属性](https://www.sitepoint.com/css3-pointer-events/)
*   [指针事件如何简化跨浏览器触摸支持](https://www.sitepoint.com/pointer-events-will-make-cross-browsers-touch-support-easy/)
*   [用指针事件统一触摸和鼠标](https://www.sitepoint.com/unifying-touch-and-mouse-with-pointer-events/)

### SVG Filters

对于网络上发生的所有进步，对于操作图像的所有新字体和技术，每当需要任何更好的效果时，我们仍然会回到图形编辑器。颜色混合、文字效果、蒙版和其他类似的要求仍然让我们跑去找设计师，要求得到想要的图像。

好了，现在你可以给这个可怜的家伙(或女孩)一个受之无愧的机会了。所有的现代浏览器都支持一个名为`svg-filters`的功能，允许你重新创建以前需要图像编辑器才能实现的大部分效果。虽然你不能在网页中使用 Photoshop，但事情也不会太遥远。不相信我？看看 GitHub 上的这个演示:[FILDROP–SVG 过滤器](http://jorgeatgu.github.io/svg-filters/)。很漂亮，对吧？

现在，在你急着启动你的代码编辑器之前，先休息一会儿重温一下这个理论。找出[为什么 SVG 过滤器如此棒](https://www.smashingmagazine.com/2015/05/why-the-svg-filter-is-awesome/)，学习[将 SVG 效果应用到 HTML 内容的正确方法](https://developer.mozilla.org/en/docs/Applying_SVG_effects_to_HTML_content)或者从 SVG 的[女主人 Sara Soueidan 那里学习高级技术](https://sarasoueidan.com/articles/index.html)。

## 2.HTML5 功能

HTML5 带来的不仅仅是一组新的标签和属性——它让原生浏览器支持一些以前需要使用 JavaScript 或其他脚本方法的操作。让我们来看看我们现在可以玩的一套新的闪亮的东西。

### 自（动）调焦装置

这个属性确实如其名——它将焦点放在输入中。想想当你到达谷歌搜索页面时，如何直接输入(注意——他们使用的是不同的解决方案)。如果您确实想使用 JavaScript 集成回退，请确保使用`DOM ready`事件而不是`window.load`。当用户已经开始与内容交互时，让页面跳转到输入位置(甚至调出屏幕键盘)并不是一个好主意。

有关更多信息，您可以查看以下资源:

*   6 个漂亮、省时的 HTML 属性
*   [HTML5 甚至更高级的表单](https://www.sitepoint.com/html5-forms/)
*   [输入属性:自动对焦(HTML5Tutorial.info)](http://www.html5tutorial.info/html5-autofocus.php)

### 类别列表(DOMTokenList)

在 DOM 元素上操作类并不是一个新想法。作为流行性(和有用性)的证明，jQuery 对这些行为有很好的支持，代价是要携带整个库。对 DOMTokenList 的本机支持允许您更快、更有效地执行相同的操作。虽然 IE11 中的支持不是 100%完整，但这不应该阻止您探索该功能提供的可能性。

要更好地理解 DOMTokenList 的细节，可以参考以下资源:

*   [探索类列表 API](https://www.sitepoint.com/exploring-classlist-api/)
*   [class list API(html 5 doctor)](http://html5doctor.com/the-classlist-api/)
*   [Element.classList (MDN)](https://developer.mozilla.org/en-US/docs/Web/API/Element/classList)

### 数据集和数据-*属性

在标记中存储数据并不是一个新想法。开发人员这样做已经有一段时间了，最初是定制类名，后来是使用`data-*`属性。jQuery 和他们的[在这个方向上很有帮助。数据()](https://api.jquery.com/data/)功能。但是在我们的上下文中，您可以完全绕过 jQuery，使用原生支持来直接处理`data-*`属性。

如果您需要更新对`dataset`和`data-*`属性的本地支持，您可以从以下资源开始:

*   [使用 HTML5 数据集 API 管理定制数据](https://www.sitepoint.com/managing-custom-data-html5-dataset-api/)
*   [如何使用 HTML5 数据属性](https://www.sitepoint.com/use-html5-data-attributes/)
*   [使用数据属性(MDN)](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Using_data_attributes)

### 电子邮件、电话和 URL 输入类型

表单验证总是让各地的 web 开发人员头疼。你在互联网上搜索过多少次完美的正则表达式来验证电子邮件？幸运的是，在`email`、`telephone`或`URL`输入类型的帮助下，HTML5 约束 API 可以用最少的 JavaScript 使这项任务变得更容易。以下是几个如何做到这一点的例子:

*   [使用 HTML5 约束 API 进行表单验证](https://www.sitepoint.com/using-the-html5-constraint-api-for-form-validation/)
*   [HTML5 表单验证](https://www.sitepoint.com/html5-form-validation/)
*   [数据表单验证(MDN)](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Forms/Data_form_validation)

### 闪亮的新属性:隐藏，占位符，拼写检查，模式

HTML5 带来了一组新的有用属性，旨在为一些通常使用 JavaScript 集成的技术提供原生支持。它们中的大多数扩展了输入字段的功能，增强了可用性和验证过程。虽然它们都不是真正的惊喜(已经存在了一段时间)，但现在它们第一次获得如此广泛的支持，这使得它们易于使用，无需部署 polyfills。如果您需要刷新 HTML5 客户端验证，请查看这篇文章—[使用 HTML5 进行客户端表单验证](https://www.sitepoint.com/client-side-form-validation-html5/)。否则，让我们看看清单上有什么:

1.  **“隐藏”属性**–这实际上是 CSS 的一个分支，因为它的行为与`display: none`完全一样。它甚至可以被 CSS 使用`display`属性覆盖，并且可以应用于任何 HTML 元素。作为一种解决方案，它在语义上更加正确，并且应该更有效地帮助屏幕阅读器。
2.  **“模式”属性**–有些字段的自动验证不能标准化。让我们考虑一下`<input type="tel">`。电话格式因国家而异，所以你不能有一个通用的验证模型。这就是`pattern`属性发挥作用的地方。您可以指定一个正则表达式作为它的值，浏览器将使用它来验证用户输入。对于各种模式的集合，您应该查看 [HTML5Pattern](http://html5pattern.com/) 。
3.  **“占位符”属性**–占位符属性指定一个简短的提示，描述输入字段的预期值。有些人甚至用它们来代替标签，但是这带来了很多可用性问题——一旦字段被填充，占位符就消失了。加上缺乏可见的标签，这可能会导致很多混乱。虽然有很多滥用的空间，但如果使用得当，占位符可以给用户很多有用的提示。
4.  **“拼写检查”属性**–我们已经习惯于看到文本区对我们写的文本进行拼写检查纠正。大多数开发人员不知道的是，您可以控制这个功能，打开或关闭它，甚至在普通的文本输入字段上启用它。你可能会问，为什么要这么做？例如，当 textarea 用于输入代码时，您可能希望对搜索字段的查询进行拼写检查或禁用该功能。要了解更多细节，您可以查看 Mozilla 开发者网络的以下文章— [控制 HTML 表单中的拼写检查](https://developer.mozilla.org/en-US/docs/Web/HTML/Controlling_spell_checking_in_HTML_forms)。

### 新的语义元素

HTML5 中的语义元素与 web 内容的机器处理密切相关，其中两个主要领域受益最大——一方面是索引和搜索引擎，另一方面是辅助技术。支持仍然不是 100%，这里那里都有小问题，在每种情况下选择正确的元素在开始时可能会令人生畏。关于用例的争论仍然很激烈，这取决于我们每个人选择部署多少这些元素以及在哪里部署。HTML5Doctor.com 有一篇很棒的关于 HTML5 语义主题的文章，里面有大量的信息，并且指向大量的相关资源。

### PNG 收藏夹图标

早在 1999 年，Internet Explorer 5 引入了一个新功能，称为`favicon`——一个微小的图像，位于地址栏、书签列表以及最近加载网站的标签中网站 URL 的旁边。旧的*。ico* 格式仍然具有一定程度的通用性(它可以封装 16×16、32×32 和 48×48 像素的多种图像格式)，但在高密度屏幕的时代已经过时了。不过不要担心，因为现在你可以使用 PNG 图像作为收藏夹图标。只要留意每个浏览器的特性就行了，因为它们在这篇关于图标的 CSS-Tricks 文章中有详细描述。

### 进度元素

自从第一个图形操作系统诞生以来，进度条就一直出现在计算机屏幕上。网络上也有各种各样的实现。今天，我们可以对进度条使用本地控件，只要我们注意每个浏览器集成的细节。幸运的是，我们已经有了很好的指南和资源，如下所示:

*   [html 5 进度元素(CSS-Tricks)](https://css-tricks.com/html5-progress-element/)
*   [<进度>元素(MDN)](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/progress)

### 范围输入类型(滑块)

就像我们上面提到的进度元素一样，滑块几十年来一直是电脑屏幕上的另一个常量。在各种 JavaScript 解决方案的帮助下，它也没有花很长时间就登陆到网页上。最后，您现在可以用一个 HTML 标签创建一个本地的 slide 控件。在许多情况下，该元素是最直观的解决方案。了解如何以及何时使用它:

*   [输入类型:Range (HTML5Tutorial.info)](http://www.html5tutorial.info/html5-range.php)
*   [如何设计跨多种浏览器的 HTML5 范围滑块(Hongkiat.com)](http://www.hongkiat.com/blog/html5-range-slider-style/)
*   [玩 HTML5 范围滑块输入(新代码)](http://thenewcode.com/757/Playing-With-The-HTML5-range-Slider-Input)

### Iframe 沙盒

近年来，Iframes 变得越来越普遍，尽管最终用户几乎察觉不到它们。社交媒体插件、横幅广告和其他广告——它们最终都会打开 iframes 来显示它们想要的内容。随着如此多的门进入您的页面，恶意脚本成功攻击您的网站的机会也增加了。

这就是 iframes 的沙盒模式发挥作用的地方。它指示浏览器减少特权(如运行 JavaScript 或触发弹出窗口)。为了方便起见，微软和 Chromium 团队都记录了这个特性:

*   [如何用 HTML5 沙盒保护你的网站(MSDN)](https://msdn.microsoft.com/en-us/hh563496)
*   [深度安全:HTML5 的@sandbox (Chromium 博客)](http://blog.chromium.org/2010/05/security-in-depth-html5s-sandbox.html)

### 会话历史管理

“老派”网站的行为是，当你加载新页面时，网址会改变，并存储到浏览器历史中。有了这个，当你想再看一页刚刚离开的页面时，你就可以在历史中来来回回。“单页面应用”的兴起带来了巨大的挑战——所有的事情都通过 AJAX 调用发生在同一个页面上，然而我们仍然需要应用程序不同状态的不同 URL。历史管理的最新更新解决了这个问题，即`popstate`事件、`history.pushState`和`history.replaceState`方法。您可以查看以下资源以深入分析该特性:

*   [Saner HTML5 历史管理(AdequatelyGood.com)](http://www.adequatelygood.com/Saner-HTML5-History-Management.html)
*   [操纵浏览器历史(MDN)](https://developer.mozilla.org/en-US/docs/Web/API/History_API)

## 结论

这是一份相当长的清单，我还在为另一篇文章攒差不多同样的钱。本文中概述的特性都不是新的。大多数已经以这样或那样的方式上市几年了。改变的是主流浏览器的原生支持，以及老式浏览器市场份额的减少。最重要的结果是，大多数时候，我们不再需要无数的 polyfill 解决方案。因此，看看你的网站的分析，如果可能的话，放下 polyfill 训练轮，利用现代浏览器的全部功能。您的客户会对改进的性能和体验心存感激。

## 分享这篇文章