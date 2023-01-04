# 从头开始构建一个响应迅速、移动友好的网站:响应规则

> 原文：<https://www.sitepoint.com/build-a-responsive-mobile-friendly-website-from-scratch-responsive-rules/>

在上一篇文章中，我们已经看到了为网站创建简单布局的基本规则。在关注了 pc 桌面版之后，在本系列的第五部分中，我们将看到哪些规则适用于使布局具有响应性。

因此，最终的结果对于平板电脑和智能手机来说都将变得令人愉快，我们将在最后一部分看到这一点。请记住，响应式设计不仅仅是一种自我表扬的技巧，而是一种增强用户使用网站体验的方式。

正如我在本系列的第四篇文章中介绍的，为了清楚起见，我们将在我们的网站上使用两个样式表来区分不同的概念；关于这个主题，最好记住，在生产中，您应该使用一个可用的工具来最小化各种 CSS 样式表并将其合并到一个唯一的文件中，以提高性能。你可以使用像 [Minify](https://code.google.com/p/minify/ "How to minimize and and unite various CSS files into a single file") 这样的离线工具，也可以使用[在线工具](http://refresh-sf.com/yui/)，它可以合并多个 CSS 或 Javascript 文件，删除不必要的空白和注释，并将它们作为一个文件提供。

回到我们的文件，第一个文件叫做`common.css`，它包含了所有通用的 CSS 规则，这些规则将被项目的三个版本**共享(继承)**，而另一个叫做`responsive.css`的文件包含了应用于所选设备的规则规范。这就是为什么必须设置我们称之为“断点”的原因，断点是浏览器的宽度，一旦浏览器在声明的宽度范围内，它就有一个媒体查询声明来改变布局。

现在，让我们深入代码，检查哪些是我们必须应用以获得结果的最重要的更改。

首先，我们必须在文档中设置选择的断点。为此，您必须将这一行放在`responsive.css`的顶部:

```
@media screen and (max-width: 768px)
```

这样，我们就将定义的样式限制在屏幕上(例如，非打印或盲文设备)，并将范围进一步限制在宽度为 768 像素或更小的视窗上。因此，我们将在此规范下设置的所有规则将仅适用于屏幕不大于 768 像素的设备。在继续之前，花一分钟思考一个事实:让网站具有响应性并不意味着要和像素绑定在一起。

当一个网页设计者通过应用响应式网页设计技术来规划和实现一个网站时，他首先要给用户创造一种愉快的体验，使他们能够使用不同的设备来访问这个网站；与此同时，这种经验必须得到这一系列技术原则的正确应用的支持，而这样做意味着要避免过于严格地参照僵化和固定的方案。在选择断点的情况下，设计者应该考虑对这些指示不严格敏感的布局，但要灵活和适应。

从这个角度来看，Craig Buckler 的这篇文章非常有趣，它展示了浏览器在触发媒体查询事件时的不同行为，介绍了它们计算垂直滚动条大小的不同方式。特别是，选择像素限制会增加回到过去的风险，那时网站只针对特定大小进行优化，即使是最轻微的变化也会影响其正确的视图。所以，当你设计和实现一个有响应的网站时，记住这一点，最重要的是，在你的工作完成之前，做所有必要的测试。

在这个简短的题外话之后，让我们看看我们的平板电脑布局最终会是什么样子。

![tablet layout](img/ceca5f37e24386a41e0ef8140e9cef81.png)

正如你所看到的，它的外观没有太大的变化，但是这次页面占据了屏幕的全部可用宽度。菜单列表保持了同样的风格，尽管现在它的项目比以前更接近了。此外，带有社交图标的页脚没有改变，包含主要内容的框也保持了在空间中的浮动。

正如您在阅读下面的代码时将会看到的，这个版本几乎没有需要指定的规则。

```
@media screen and (max-width: 768px)
{
   #banner > img
	{
		max-width: 100%;
	}

	ul.menu-list
   {
      height: auto;
      text-align: justify;
      -ms-text-justify: distribute-all-lines;
      text-justify: distribute-all-lines;
   }

   li.menu-item
   {
      margin: 0em 0.2em;
      width: auto;
      vertical-align: top;
      display: inline-block;
      *display: inline;
      zoom: 1;
   }

   .stretch
   {
      width: 100%;
      display: inline-block;
      *display: inline;
      zoom: 1;
   }

   a.menu-item-link
   {
      font-size: 1.1em;
   }

   #wrapper
   {
      width: inherit;
   }

   .small-box
   {
      width: inherit;
      float: none;
   }

   #wrapper
   {
      float: none;
   }

   .social-bar a
   {
      margin-right: 0em;
   }

   #newsletter-form
   {
      font-size: 1em;
   }

   input#newsletter-email
   {
      width: 10em;
      -webkit-transition: none;
      -moz-transition: none;
      -o-transition: none;
      -ms-transition: none;
      transition: none;
   }

   #team-logo-bar a
   {
      text-decoration: none;
      width: 100%;
      display: inline-block;
      text-align: center;
   }
}
```

在这个版本中，您应该更加注意应用于横幅图像、菜单项和简讯框的规则。对于前者，由于规则`max-width: 100%;`,只要图像的宽度不超过其容器的宽度，图像就会以其原始尺寸呈现。调整浏览器窗口大小时，图像的比例将保持不变，即使页面相应缩小。

对于菜单项来说，如果所有的菜单项之间的间距是均匀的就好了。此外，间距也必须是“流动的”,以便随着浏览器窗口尺寸的缩放，即使各种声音之间的间距也会减小，同时保持相等。上面的解决方案兼容所有浏览器，包括 IE 6。回想一下第三篇文章中的 HTML 代码，我们已经向列表中添加了一个类为`stretch`的条目，方式如下:

```
<nav class="menu">
   <ul class="menu-list">
      <li class="menu-item">
         <a class="menu-item-link" href="./homepage" title="Homepage">Homepage</a>
      </li>
      ...
      <li class="stretch">
      </li>
   </ul>
</nav>
```

对列表(`ul.menu-list`)我们应用了规则`text-align: justify;`，它与类`stretch`结合使用来平衡盒子的位置，并得到合理的间距。

下一步是为类`stretch`设置规则`display: inline-block;`。正如您可能知道的，规则为`display: inline-block;`的元素被放置在一行中(例如，在关闭内容的同一行上)，但表现为一个块。因为在 IE7 和更低版本中不支持后者，所以我们必须编写一个非常简单的小程序。它处理的是所谓的“安全 CSS 黑客”也就是说，引用 Mathias Bynens 的解释是一种<q cite="http://mathiasbynens.be/notes/safe-css-hacks">黑客，它在特定版本的 web 浏览器中工作，不太可能被所有其他浏览器解析，包括未来的版本。</q>

为此，我们必须向样式元素添加这两行短代码(就像我们对菜单项所做的那样),仅此而已:

```
*display: inline;
zoom: 1;
```

对于 IE6/7，zoom: 1 提供了属性`hasLayout`。有“布局”是`display: inline-block`一直工作的前提。正如[这篇文章](http://satzansatz.de/cssd/onhavinglayout.html)明确解释的那样，

> `zoom`总是触发`hasLayout`。既有“布局”又有`display: inline`的元素的行为方式与标准所说的`inline-block`类似:它们像段落中的单词一样水平流动，对垂直对齐敏感，并对其内容应用某种收缩包装。

最后值得讨论的是关于交易。对于小屏幕的设备来说，用户应该能够快速、毫无困难地找到他们想要的东西。最好的办法是去掉过多的纯文体效果。第一个原因更加理论化:用户会对在小屏幕上看到这样的效果感兴趣吗？还是他们主要担心网站的功能？

答案可能是功能性，这证明了省略的合理性。第二个原因更实际:大多数平板电脑和手机都配备了触摸技术。事实上，平板电脑和智能手机是不使用鼠标的设备，因此它们没有悬停事件。

## 结论

在这个系列的第五部分中，我们看到了一些规则，我们必须应用这些规则来使我们的网站具有响应性，并确保在平板电脑上的良好性能。

在下一个也是最后一个部分，我们将用移动代码和一些最后的考虑和建议来结束它。

## 分享这篇文章