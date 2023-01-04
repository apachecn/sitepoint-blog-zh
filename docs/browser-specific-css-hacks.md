# 特定于浏览器的 CSS 技巧介绍

> 原文：<https://www.sitepoint.com/browser-specific-css-hacks/>

越来越多的网络开发者抛弃了表格，转而使用 CSS 来控制网站的布局。而且，考虑到使用 CSS 的许多好处，比如更快的下载时间，更好的可访问性和更容易的网站管理，为什么不呢？

### CSS 的问题是

从历史上看，使用 CSS 的主要问题是缺乏浏览器支持。这种情况已经不复存在，因为第 5 版浏览器对 CSS 都提供了很好的支持，现在占使用中的浏览器的 99%以上。

仍然存在的问题是，浏览器有时会以不同的方式解释 CSS 命令，这一事实本身就导致许多开发人员举起手臂，切换回像素完美的表格布局。不过，不要害怕！随着你对 CSS 了解的越来越多，你会逐渐开始理解不同的浏览器解释，并意识到其实并没有那么多——而且，在必要的地方，可以使用各种变通办法或黑客来迎合它们的特性。

### CSS 黑客是如何工作的

CSS hacks 的工作方式是向你试图欺骗的浏览器发送一个 CSS 规则，并向其他浏览器发送覆盖第一个命令的第二个 CSS 规则。如果您有两个带有相同选择器的 CSS 规则，第二个 CSS 规则几乎总是优先。

例如，在 Internet Explorer 中，您希望页面的标题区域和内容之间的空间总计为 25px。这种差距在 IE 中可能看起来不错，但在 Firefox、Opera 和 Safari 中差距是巨大的——你决定 10px 的差距看起来更好。要在所有浏览器中实现这种完美的外观，您可以使用以下两条 CSS 规则:

```
#header {margin-bottom:25px} 

#header {margin-bottom:10px}
```

第一个命令适用于 IE，第二个命令适用于所有其他浏览器。这是如何工作的？嗯，目前不会，因为所有的浏览器都能理解 T2 和 T3 的 CSS 规则。因此，他们都将使用第二个 CSS 规则，因为它在第一个规则之后。

通过插入 CSS hack，我们可以对 IE 隐藏第二个 CSS 规则来执行浏览器检测。这意味着 IE 甚至不知道规则的存在，因此将使用第一个 CSS 规则。我们如何做到这一点？请继续阅读，寻找答案！

### Internet Explorer 的浏览器检测

要给 IE 发送不同的 CSS 规则，我们可以使用 IE 无法理解的子选择器命令。子选择器命令包含两个元素，其中一个是另一个的子元素。所以，`html>body`指子，`body`，包含于父，`html`。

以页眉边距为例，我们的 CSS 命令应该是:

```
#header {margin-bottom:3em} 

html>body #header {margin-bottom:1em}
```

IE 无法理解第二个 CSS 规则，由于`html>body` CSS 命令，所以会忽略它，使用第一个规则。所有其他浏览器将使用第二条规则。

### Internet Explorer 5 的浏览器检测

乍一看，向不同版本的浏览器发送不同的 CSS 规则似乎很奇怪，但是对于 IE5 来说，这是非常必要的。问题在于 IE5 对盒子模型的曲解。当我们在 CSS 中指定元素的宽度时，填充和边框不包括在这个值中。然而，IE5 将这些值合并到宽度值中，这导致元素宽度在该浏览器中变得更小。

以下 CSS 规则将导致所有浏览器的宽度为 10em，IE5 除外，它的宽度仅为 5em (IE5 在计算宽度时将在左侧和右侧包含两组填充和边框):

```
#header {padding: 2em; border: 0.5em; width: 10em}
```

这个问题的解决方法？Tantek elik 发明的 [box model hack](http://tantek.com/CSS/Examples/boxmodelhack.html) ，他因为这个 hack 而在 CSS 世界中变得非常有名——这是理所当然的。要执行浏览器检测，并向 IE5 发送不同的 CSS 规则，您可以使用以下代码:

```
#header {padding: 2em; border: 0.5em; width: 15em; voice-family: ""}""; voice-family:inherit; width: 10em}
```

谁也猜不出切利克先生是如何研究出这种方法的。重要的是它能工作:IE5 将使用第一个宽度值 15em，其中 5em 将被两组填充和边框占用(左边和右边各一组)。这将最终使元素在 IE5 中具有 10em 的宽度。

15em 的值将被第二个宽度值 10em 覆盖，IE5 除外，由于某种原因，IE5 无法理解紧跟在所有这些曲线之后的 CSS 命令。它看起来不漂亮，但它确实有效！

box model hack 有一个小问题，因为它经常会“扼杀”IE5.0 中的下一个 CSS 规则。然而，还有许多其他的 box model hack 可以替代实现。

### Mac 上 Internet Explorer 的浏览器检测

很简单，Mac 上的 IE 用 CSS 做奇怪的事情。自从微软宣布不会发布更新版本以来，这款浏览器已经有点过时了。因此，许多 Web 开发人员对他们的 CSS 驱动的站点进行编码，以便该站点可以在 IE/Mac 中工作，尽管它可能无法提供与其他平台和浏览器组合的用户相同级别的高级功能或设计。如果 IE/Mac 用户可以访问网站的所有区域，这被视为一种合适的做事方式。

使用 [IE/Mac CSS hack](http://www.sam-i-am.com/work/sandbox/css/mac_ie5_hack.html) 隐藏命令很简单。它包括用一组破折号和星号围绕尽可能多的 CSS 规则:

```
/* Hide from IE-Mac */ 

#header {margin-bottom:3em} 

#footer {margin-top:1.5em} 

/* End hide */
```

IE/Mac 将简单地忽略所有这些命令。如果网站的某个区域在 IE/Mac 中不能正常工作，这个 CSS 技巧实际上非常有用。如果这一部分对访问者使用网站并不重要，你可以简单地在 IE/Mac 中隐藏它，就像这样:

```
#noiemac {display: none} 

/* Hide from IE-Mac */ 

#noiemac {display: block} 

/* End hide */
```

第一个 CSS 规则隐藏了分配了 noiemac id 的整个部分(即`<div id="noiemac">`)。第二个 CSS 规则，IE/Mac 看不到，显示这个部分。

### Netscape 4 浏览器检测

Netscape 4 对 CSS 的支持有限而且有些不稳定。这款浏览器的市场份额已经下滑至 1%以下，为其设计 CSS 布局极具挑战性。如今，在浏览器中完全隐藏 CSS 文件已经成为一种常见的做法。这可以通过使用`@import`指令调用 CSS 文档来实现:

```
<style type="text/css">@import url(cssfile.css);</style>
```

Netscape 4 将显示网站的非风格版本，因为它不能理解这个`@import`指令。

### 在不同的浏览器中检查你的站点

在撰写本文时，主要的互联网浏览器包括 IE5、IE6、Firefox、Opera 和 Safari。(查看[TheCounter.com](http://www.thecounter.com/stats/)了解最新的浏览器统计数据。)你可以在 [Evolt 浏览器档案馆](http://browsers.evolt.org/)下载所有这些浏览器，以及一些不太知名的浏览器。如果你不确定如何安装，看看如何[在你的电脑上安装多个版本的 IE 浏览器](http://labs.insert-title.com/labs/article.aspx?ID=795)。

理想情况下，你应该在 PC 和 Mac 上的所有这些浏览器中检查你的网站(除了 IE6 和 Safari，它们分别只在 PC 和 Mac 上可用)。如果你没有 Mac 电脑，你可以通过 Dan Vine 的 iCapture 在 Safari 上运行你的网站截图，或者你可以付费使用 [Browsercam](http://www.browsercam.com/) ，它提供了更广泛的屏幕截图服务。

### 结论

总的来说，现代浏览器对 CSS 有很好的支持——对你来说，使用 CSS 来控制布局和表现当然足够了。但是，有时某些页面元素在不同的浏览器中会有不同的显示。

如果您不知道原因，请不要太担心:尝试 CSS 规则并进行一些试错式的更改，比如将填充值更改为边距。如果这不起作用，你可以用这些 CSS 技巧来解决它，你的网页在所有浏览器上都应该看起来很棒！

## 分享这篇文章