# 编写 JavaScript 时考虑可访问性

> 原文：<https://www.sitepoint.com/writing-javascript-with-accessibility-in-mind/>

关于如何提高 JavaScript 组件的可访问性并为用户提供更多更好的方式来与您的网站或 web 应用程序交互的提示。

*本文原载于[媒体](https://medium.com/@matuzo/writing-javascript-with-accessibility-in-mind-a1f6a5f467b9#.nza29zabs)。*

在我的第一篇文章[写 HTML 时考虑可访问性](https://medium.com/@matuzo/writing-html-with-accessibility-in-mind-a62026493412#.bxkw48cy7)中，我解释了为什么以及如何开始使用 web 可访问性。我还分享了一些关于如何改进你的标记以使你的网站更易访问的技巧。其中一些非常基础，但仍然很有价值。这一切都归结为前端开发中最重要的两条不成文的规则:**学习基础知识**和**花足够的时间来计划和编写 HTML** 。您和您的用户都将受益于清晰的语义标记。

幸运的是，HTML 不是我们制作网站的唯一语言，但是语言越复杂，事情就越容易出错，JavaScript 也会变得非常复杂。虽然对我们的代码能够工作感到满意，但是很容易忘记使用除鼠标或触摸板之外的其他输入设备的用户，例如键盘或屏幕阅读器用户。这是关于 web 可访问性的四篇文章中的第二篇，我收集了一些关于编写 JavaScript 时应该考虑什么以及如何使 JavaScript 组件更易访问的技巧。

## JavaScript 不是敌人

![JavaScript Accessibility](img/005a801636afb06c9899bb0cafca0ded.png)

在你阅读我的建议之前，我想指出一件重要的事情——创建一个可访问的网站并不意味着你必须决定是否使用 JavaScript。可访问性是指让尽可能多的人可以访问内容，这也包括使用旧浏览器和旧电脑、慢速互联网连接、严格的安全限制(例如没有 JavaScript)等的用户。在这种情况下，JavaScript 可能无法运行或加载时间过长，这种体验可能并不理想，但如果网站可以访问和使用，这种体验还是足够好的。

如果 JavaScript 是可执行的，它甚至可以用来提高可访问性。 [Sara Soueidan](https://twitter.com/sarasoueidan) 在[写了她创建工具提示窗口小部件的经历构建一个完全可访问的帮助工具提示……比我想象的要难](https://sarasoueidan.com/blog/accessible-tooltips/)。她解释了“每一个非 JS 解决方案都有一个非常糟糕的缺点，对用户体验产生了负面影响”，以及为什么 JavaScript 对可访问性很重要。

[Marco Zehe](https://twitter.com/MarcoInEnglish) 在他的文章[中写了更多关于 JavaScript 和可访问性的内容，JavaScript 不是可访问性的敌人！我强烈建议你阅读他的帖子。](https://www.marcozehe.de/2016/11/23/javascript-not-enemy-accessibility/)

但是介绍性的谈话已经够了！让我们开始吧…

## 重点管理至关重要

确保我们的网站可以通过键盘导航是很重要的。许多用户在网上冲浪时依赖键盘。其中包括有运动障碍的人，盲人和没有手或因为任何原因不能使用鼠标或跟踪板的人。

通过键盘浏览一个站点意味着按照 DOM 顺序从一个可聚焦的元素跳到另一个元素。这通常通过使用`Tab`键或`Shift` + `Tab`反转方向来完成。可聚焦元素包括[链接、按钮和表单元素](https://allyjs.io/data-tables/focusable.html)。它们可以用`Enter`键选择，有时也可以用`Spacebar`键选择。通过以不同的方式聚焦和选择，它们提供了非常有用的默认功能。因此，使用正确的语义元素并按照逻辑顺序编写 HTML 是有意义的。

默认情况下，`<p>`、`<h2>`或`<div>`等元素不能被聚焦。我们经常使用这样的标签来创建由 JavaScript 驱动的定制组件，这对键盘用户来说可能是个问题。

### 使不可聚焦的元素可聚焦

通过添加带有整数值的 [tabindex 属性](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/tabindex)，可以使不可聚焦的元素成为可聚焦的。如果该值被设置为`0`，那么该元素就变成可聚焦的，并且可以通过键盘访问。如果该值是负数，则该元素是可编程聚焦的(例如，使用 JavaScript)，但不能通过键盘访问。你也可以使用一个大于`0`的值，但是这会改变自然的 tab 键顺序，被认为是一个[反模式](http://adrianroselli.com/2014/11/dont-use-tabindex-greater-than-0.html)。

```
<h2 tabindex="0">A focusable heading</h2> 
```

如果你想了解更多关于`tabindex`的信息，请观看由[罗布·多德森](https://twitter.com/rob_dodson)撰写的《A11ycasts》剧集[用塔宾德克斯](https://www.youtube.com/watch?v=Pe0Ce1WtnUM&list=PLNYkxOF6rcICWx0C9LVWWVqvHlYJyqw7g&index=7)控制焦点。

### 用 JavaScript 聚焦元素

即使元素是可聚焦的，有时它们的 DOM 顺序也不正确。为了说明，我用 HTML、CSS 和 JS 创建了一个简单的模态窗口组件( [demo](http://codepen.io/SitePoint/debug/LWmMGq) 和 [editable Pen](http://codepen.io/SitePoint/pen/LWmMGq) )。

如果使用`Tab`键跳转到按钮并按下`Enter`，将弹出一个模态窗口。如果你再次按下`Tab`键，焦点将跳转到模态窗口下面的下一个链接。预期的行为是下一个聚焦的元素在模式窗口中。但这并不是因为元素是按 DOM 顺序聚焦的，并且模态窗口位于文档的底部。你可以在[这个录制](https://www.youtube.com/watch?v=bmpQFEzRwFI)的屏幕中看到这一点。

要解决这个问题，你必须使模态窗口可聚焦，然后用 JavaScript 聚焦它。

```
<div class="modal" id="modal2" tabindex="0">
  ...
</div> 
```

```
function showModal() {
  var modal = document.getElementById('modal2');
  modal.focus();
  ...
} 
```

你可以看到在更新的例子中([演示](http://codepen.io/SitePoint/debug/dvewGL)和[可编辑笔](http://codepen.io/SitePoint/pen/dvewGL))通过跳转到按钮，按下`Enter`并再次跳转。您将看到模式窗口本身现在被聚焦了。

这很好，但是这里仍然有两个问题。

如果按下`Esc`关闭模态窗口，焦点将会丢失。理想情况下，焦点会跳回到按钮在你打开模式窗口之前的位置。为了实现这一点，您必须将最后一个聚焦的元素存储在一个变量中。

我们可以使用`document.activeElement`来获得当前元素的焦点。

```
var lastFocusedElement;

function showModal() {
  lastFocusedElement = document.activeElement;

  var modal = document.getElementById(modalID);
  modal.focus();
  ...
} 
```

现在你有了一个对按钮的引用，当模式窗口关闭时你可以再次聚焦它。

```
function removeModal() {
  ...
  lastFocusedElement.focus();
} 
```

我已经用另一支笔更新了代码([演示](http://codepen.io/SitePoint/debug/VpxqeO)和[可编辑笔](http://codepen.io/SitePoint/pen/VpxqeO))。可访问性现在好了很多，但仍有改进的空间。

打开模式窗口时，最好将焦点保持在窗口内。现在仍然可以跳出模态。这里就不赘述了，但是为了完整起见我用所谓的*键盘陷阱* ( [演示](http://codepen.io/SitePoint/debug/vxjvLo)和[可编辑笔](http://codepen.io/SitePoint/pen/vxjvLo))做了第四支笔。焦点将停留在模态窗口内，只要它是活动的，[可以在这个屏幕记录](https://www.youtube.com/watch?v=2Q56TbgvN5c)中看到。

如果你比较第一笔的[和最后一笔](http://codepen.io/SitePoint/pen/LWmMGq)的[，你会发现没有太多额外的代码。它可能并不完美，但最终的解决方案使用起来要好得多。](http://codepen.io/SitePoint/pen/vxjvLo)

还有另一个可访问模型的例子，谷歌的人写了一篇名为《使用 tabindex 的 T2》的文章。如果你想了解更多关于键盘测试的信息，请访问[网站](http://webaim.org/techniques/keyboard/#testing)。他们提供了一个“最常见的在线交互，交互的标准按键，以及测试中需要考虑的其他信息”的列表

关于焦点管理的更多例子，请看 egghead.io 视频[使用 CSS、HTML 和 JavaScript 的焦点管理](https://egghead.io/lessons/css-focus-management-using-css-html-and-javascript)作者 [Marcy Sutton](https://twitter.com/marcysutton) 或者 A11ycasts 剧集[什么是焦点？罗布·多德森。](https://www.youtube.com/watch?v=EFv9ubbZLKw)

## 如果你需要一个按钮，使用<button>元素</button>

我已经在第一篇文章中写了关于按钮的内容，但是显然[很多人使用通用元素作为按钮](https://css-tricks.com/random-interesting-facts-htmlsvg-usage/#article-header-id-11)。所以，我想多写一些关于这个话题的东西也无妨。

我做了一支笔([调试模式](http://codepen.io/SitePoint/debug/RpyEab) / [笔，代码为](http://codepen.io/SitePoint/pen/RpyEab))来说明在`<button>`或`<input>`元素上使用`<span>`或`<div>`作为按钮的一些问题。如果你浏览页面，你会发现你可以聚焦第一个按钮，但不能聚焦第二个。当然，这是因为第一个按钮是`<button>`，第二个是`<div>`。您可以通过将`tabindex="0"`添加到`<div>`来解决这个问题，这使得最初不可聚焦的元素成为可聚焦的。这就是为什么第三和第四个按钮是可聚焦的，即使它们是`<div>` s。

```
<!-- Button and focusable -->
<button class="btn">I'm a button</button>

<!-- Div and not focusable -->
<div class="btn">I'm a div</div>

<!-- Still just a div, but focusable -->
<div class="btn" tabindex="0">I'm a div</div>

<!-- Button role and focusable -->
<div class="btn" tabindex="0" role="button">I'm a div</div> 
```

这个 *div-button* 确实是可聚焦的，但是即使你添加了一个`button`的`role`，它的行为仍然像一个`<div>`。为了说明这一点，我向所有的`.btn`元素( [Pen](http://codepen.io/SitePoint/debug/RpyEab) )添加了一个简单的 click 事件处理程序。如果你点击按钮，会弹出一个警告框，但是如果你尝试用按键(`Enter`或`Spacebar`)做同样的事情，只有第一个按钮会触发一个事件。您必须向 *div-buttons* 添加一个按键事件处理程序来完全模仿默认的按钮行为，这看起来像是很多不必要的开销，不是吗？这就是为什么如果你需要一个按钮，你应该使用`<button>`元素。

观看 Rob Dodson 的 [A11ycasts 集“只需使用按钮”](https://www.youtube.com/watch?v=CZGqnp06DnI&index=4&list=PLNYkxOF6rcICWx0C9LVWWVqvHlYJyqw7g)或阅读[链接、按钮、提交和 Divs，哦地狱](http://adrianroselli.com/2016/01/links-buttons-submits-and-divs-oh-hell.html)由 [Adrian Roselli](https://twitter.com/aardrian) 提供的更多细节和示例。

### 当内容动态变化时，必须通知屏幕阅读器用户

通常，屏幕阅读器只在元素被聚焦或者用户使用他们的屏幕阅读器自己的导航命令时才宣布内容。如果内容被动态加载并插入到 DOM 中，那么只有视力正常的用户才会意识到这些变化。ARIA Live Regions 提供了几个选项来解决这个问题。我将通过一个例子向您展示如何操作。

假设您有一个个人资料设置页面，您可以在其中编辑和保存个人数据。当单击“保存”按钮时，保存更改而不重新加载页面。一个警告通知用户更改是否成功。这可能会立即发生或需要一些时间。我录制了一个简短的视频给你看我刚刚解释的内容。

可以看到动作成功了，但是听不到。屏幕阅读器用户不会注意到这个变化，但是这个问题有一个简单的解决方案。通过在消息框中添加一个`status`或`alert`的`role`，屏幕阅读器将监听该元素中的内容更新。

```
<div class="message" role="status">Changes saved!</div> 
```

如果消息的文本发生变化，将会读出新的文本。你可以在这个视频中看到和听到这个动作[，看看这支笔](https://youtu.be/YPOpIHUtkPo)中的代码[。](http://codepen.io/SitePoint/pen/GWdPZg)

### 对你的用户有礼貌

`status`和`alert`的区别在于，如果`alert`正在宣布别的事情，它会打断屏幕阅读器。相比之下，`status`会等到屏幕阅读器宣告完毕。

还有一个属性叫做`aria-live`，它有三个可能的值:`off`、`polite`或`assertive`。其中，`off`是默认值，`aria-live="polite"`相当于`role="status"`，`aria-live="assertive"`相当于`role="alert"`。在一些[众所周知的预定义情况下](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions#Preferring_Specialized_Live_Region_Roles)最好使用特定提供的*活区域角色*。此外，如果浏览器不支持`role`，你可能想尝试使用这两个属性。 [Léonie Watson](https://twitter.com/LeonieWatson) 在[屏幕阅读器对 ARIA live 区域的支持](https://www.paciellogroup.com/blog/2014/03/screen-reader-support-aria-live-regions/)中分享了一些测试结果。

```
<div role="alert" aria-live="assertive"></div> 
```

### 有时，不仅仅是宣布发生变化的内容也是有意义的

默认情况下，屏幕阅读器只显示已更改的内容，即使在相同的活动区域中还有其他内容，但偶尔公布整个文本也是有意义的。可以用`aria-atomic`属性改变默认行为。如果将它设置为`true`，辅助技术将呈现元素的全部内容。

有一个[aria-原子测试案例演示](http://pauljadam.com/demos/aria-atomic-relevant.html)由 [Paul J. Adam](http://pauljadam.com/) 制作，比较不同的现场区域设置。他还在 iOS 8.1 上用 VoiceOver 测试了他的演示，并进行了录制，这样你就可以看到它的实际效果了。如果你想更好地理解`aria-atomic`的可能用例，我建议你观看录音( [VoiceOver iOS 8.1 朗读字符保留 aria-atomic&aria-live regions 上的 aria-related](https://www.youtube.com/watch?v=MOx9cX_nQMk))。

#### 一些需要考虑的事情

*   实时区域不会移动焦点，它们只是触发文本公告
*   仅在关键变化时使用`alert`。大多数情况下，T1 更好，因为它更礼貌。
*   避免设计自动消失的警告，因为它们可能消失得太快。
*   在我的测试中，我的画外音有问题。使用 CSS 隐藏警告或动态创建警告并不总是有效。确保你用不同的软件在不同的浏览器中彻底测试你的活动区域。

当然，还有 A11ycasts 剧集[预警！](https://www.youtube.com/watch?v=5lzAj1ahRSI)Rob dods on，了解更多详细信息和示例。[海登·皮克林](https://twitter.com/heydonworks)在他的咏叹调样本集中有[另一个现场区域的例子。](http://heydonworks.com/practical_aria_examples/#button-controlled-input)

## 您不必猜测您的小部件必须提供哪些使用模式

通常很难想到一个小部件在导航和可访问性方面必须提供的所有功能。很高兴有一个叫做 [WAI-ARIA 创作实践 1.1](https://www.w3.org/TR/wai-aria-practices-1.1) 的资源可以帮助我们做到这一点。WAI-ARIA 创作实践是理解如何使用 WAI-ARIA 创建一个可访问的富互联网应用程序的指南。它描述了推荐的 WAI-ARIA 使用模式，并介绍了这些模式背后的概念。

他们有制作手风琴、滑块、标签等的指南。

### 可访问的 JavaScript 组件

有几个关于可访问 JavaScript 组件的很好的资源:

*   [实用咏叹调示例](http://heydonworks.com/practical_aria_examples/)
*   [模态——WCAG 2.0 AA 级可访问模态窗口插件](https://github.com/humaan/Modaal)
*   [Frend——一系列可访问的现代前端组件](https://frend.co/)
*   [A11Y 项目模式](http://a11yproject.com/patterns.html)

如果你知道任何额外的资源，请在评论中分享。

## 概述

利用 JavaScript 的优势来提高网站的可访问性。注意焦点管理，了解常见的使用模式，并在操作 DOM 时考虑屏幕阅读器用户。最重要的是，不要忘记你是在为谁做网站，并从中获得乐趣。

## 超越

暂时就这样了。我希望这些技巧能帮助你写出更容易理解的 HTML 和 JavaScript。非常感谢 Heydon Pickering，因为他的书[包容性前端设计模式](https://www.smashingmagazine.com/inclusive-design-patterns/)是你刚刚阅读的大部分内容的基础。如果你想了解更多关于可访问性和包容性设计的知识，我强烈建议你读一读他的书。

特别感谢阿德里安·罗塞利对我这篇文章的帮助，以及伊娃对我文章的校对。

## 资源

这是本文中链接到的所有资源的列表。

*   [A11ycasts #03 —什么是专注？](https://www.youtube.com/watch?v=EFv9ubbZLKw&list=PLNYkxOF6rcICWx0C9LVWWVqvHlYJyqw7g&index=8)
*   [a11y cas # 04—使用 tabindex 控制焦点](https://www.youtube.com/watch?v=Pe0Ce1WtnUM&list=PLNYkxOF6rcICWx0C9LVWWVqvHlYJyqw7g&index=7)
*   [a11y cas # 05—只需使用按钮](https://www.youtube.com/watch?v=CZGqnp06DnI&index=7&list=PLNYkxOF6rcICWx0C9LVWWVqvHlYJyqw7g)
*   [a11y 广播#10 —警报！](https://www.youtube.com/watch?v=5lzAj1ahRSI&index=2&list=PLNYkxOF6rcICWx0C9LVWWVqvHlYJyqw7g)
*   [Book:包容性前端设计模式](https://www.smashingmagazine.com/inclusive-design-patterns/)
*   [不要使用大于 0 的 Tabindex】](http://adrianroselli.com/2014/11/dont-use-tabindex-greater-than-0.html)
*   [使用 CSS、HTML 和 JavaScript 的焦点管理](https://egghead.io/lessons/css-focus-management-using-css-html-and-javascript)
*   [可聚焦元素—浏览器兼容性表](https://allyjs.io/data-tables/focusable.html)
*   [链接、按钮、提交和 div，哦，见鬼](http://adrianroselli.com/2016/01/links-buttons-submits-and-divs-oh-hell.html)
*   [MDN: HTMLElement.focus()](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/focus)
*   [MDN: tabindex](https://developer.mozilla.org/de/docs/Web/HTML/Global_attributes/tabindex)
*   [MDN:键盘可导航的 JavaScript 小部件](https://developer.mozilla.org/en-US/docs/Web/Accessibility/Keyboard-navigable_JavaScript_widgets)
*   [不可思议的可访问模态窗口](https://github.com/gdkraus/accessible-modal-dialog)
*   [使用 tabindex](https://developers.google.com/web/fundamentals/accessibility/focus/using-tabindex)
*   [WebAIM 键盘测试](http://webaim.org/techniques/keyboard/#testing)
*   [WebAIM 键盘辅助功能](http://webaim.org/techniques/keyboard/#intro)
*   [WAI-ARIA 咏叹调——原子](https://www.w3.org/TR/wai-aria/states_and_properties#aria-atomic)

## 分享这篇文章