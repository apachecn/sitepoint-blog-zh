# 使用 HTML5 和 Less Framework 3 的响应式 Web 设计

> 原文：<https://www.sitepoint.com/responsive-web-design-with-html5-and-the-less-framework-3/>

什么是响应式网页设计？这是一种相对较新的技术，2010 年中期由伊桑·马科特在他的 [*和*同名文章](http://www.alistapart.com/articles/responsive-web-design/)中首次描述。简而言之，它包括提供许多适合各种屏幕宽度的网站布局，然后通过使用 [CSS3 媒体查询](https://reference.sitepoint.com/css/mediaqueries/)来相应地服务这些布局。

用杰弗里·泽尔德曼的[话来说](http://www.zeldman.com/2010/06/23/responsive-design-is-the-new-black/):

> 这就是我们中的一些人在 20 世纪 90 年代对“液体”网页设计的追求，只是它并不糟糕。

这很好，但是你如何使用它呢？毕竟，该[爽的](http://colly.com/) [娃儿](http://hicksdesign.co.uk/journal) [都在](http://simplebits.com/) [都在](http://thinkvitamin.com/)做，你为什么不可以呢？

在这个简短的教程中，我将带你完成一个相当简单的页面设计，并在[乔尼·科尔皮](http://www.jonikorpi.com/)的 [Less Framework 3](http://lessframework.com/) 的帮助下使其具有响应性。“Ack！”我听见你在呼喊；“CSS 框架是给失败者用的！”不要害怕。用其创造者的话说，Less 只是“或多或少”的一个框架。没有那些丑陋的”。grid-24”类——相反，您会发现一些重置样式、一些合理的排版默认设置和四种媒体查询，以及四种不同的常见分辨率的相关正文宽度。这四种布局包括一些有用的意见，告诉你如何将它们分成黄金比例友好的网格，但是是否这样做完全由你决定。

因此，如果你从未尝试过媒体查询或响应式设计，Less 是一个很好的开始方式，可以很快看到一些结果。随着设计的进展，您可能希望调整媒体查询的确切细节，但这很容易，因为没有“框架”包袱来束缚您。少只是一个有益的起点。

所以，我们来做这件事吧！

## 少框架 3

首先，去 http://lessframework.com/。该网站本身很好地展示了响应式设计的力量，所以抓住你的浏览器的大小调整手柄，看看页面如何响应不同的视窗大小。当你玩完了(别担心，我可以等)，滚动到页面底部。有几个定制选项可用，文本区域包含输出 CSS 和 HTML 框架。

出于我们示例的目的，您可以选中所有默认选项。将 HTML 和 CSS 复制到适当命名的文件中，并更新 HTML 文件中的`style`标记源以指向 CSS 文件。

如果你直接在浏览器中打开 HTML 文件，你只会在页面上看到一个蓝色的大框。调整浏览器大小，你会看到它调整尺寸，就像你希望的那样。有了这个基线，是时候开始我们自己的响应布局了。

## 布局

为了便于说明，我们将列出一个简单的食谱。我做了一个快速的[知识共享搜索](http://search.creativecommons.org/)来寻找一些可以使用的内容。我从丽莎的厨房找到了一份[印度鹰嘴豆和南瓜汤的食谱](http://foodandspice.blogspot.com/2009/11/indian-chickpea-and-pumpkin-soup.html)。

对于我们的页面，我们希望有一个由食谱的成分和说明组成的主块，两个分别带有汤的图片和相关食谱列表的侧边栏块，以及一个说明内容来源的页脚。

Less 框架提供了四个建议的网格，每个网格对应一个车身宽度。这些由 60 像素的列和 24 像素的间距组成。最窄的布局是三列宽，主要用于屏幕宽度为 320 像素的纵向移动设备。接下来，有一个五列布局，目标是 480 像素宽度，用于高分辨率移动设备、窄浏览器或横向模式的移动设备。默认的八列布局也将用于不支持媒体查询的浏览器(包括，你猜对了，Internet Explorer)，目标是老式的 768px 屏幕宽度，对平板电脑和上网本也很有用。最后，有一个 13 列的布局，目标是 1280 像素以上的屏幕，包括目前大多数具有良好浏览器的台式机和笔记本电脑。

对于我们的食谱，我们将保持布局非常简单。我们将为两个较窄的布局设计一个单列设计，为两个较宽的布局设计一个两列设计(在侧边栏中有食谱的图片和建议的食谱)。最宽的布局是 13 个 60 像素的列宽，所以我们将在内容和侧边栏之间分配 8-5 个列宽。下一个最宽的是八列，我们将把它分成 5-3 列。

## 加价

因为这个例子纯粹是说明性的，并且因为大多数 SitePoint 读者使用现代的、复杂的浏览器浏览 Web，所以我将使用新的 HTML5 语义元素作为菜谱的标记。在他们最终统治世界之前，这是熟悉他们的一种很酷的方式。

这里有一个骨架:

```
<body>
  <article>
    <header>
      <h1>Indian Chickpea and Pumpkin Soup</h1>
    </header>
    <aside>
      <img src="images/pumpkin_soup2.jpg" />
    </aside>
    <div>
      <p>Winter squash soups are a healthy ... </p>
      <section>
        <header>
          <h2>Soup:</h2>
        </header>
        <ul>
          <li>&frac34; cup dried chickpeas</li>
          <li> ... </li>
        </ul>
      </section>
      <section>
        <header>
          <h2>Tempering:</h2>
        </header>
        <ul>
          <li>1 tablespoon olive oil</li>
          <li> ... </li>
        </ul>
      </section>
      <section>
        <p>Rinse the chickpeas ... </p>
      </section>
    </div>
    <aside>
      <header>
        <h1>Related Recipes</h1>
      </header>
      <p>If you liked this recipe you may also enjoy:</p>
      <ul>
        <li><a href="#">Toor Dal Pumpkin Soup</a></li>
        <li> ... </li>
      <ul>
    </aside>
    <footer>
      <p>This recipe is republished from ... >.</p>
    </footer>
  </article>
</body>
```

菜谱被标记为一个`article`，包含一个`header`，两个`aside`(图像和相关菜谱列表各一个)，一个`div`用于菜谱本身，以及一个`footer`。在配方`div`中，有许多`section`，每组配料和说明列表各有一个。

## 款式

让我们从默认的八列布局开始。在样式表中向下滚动到该部分(就在重置样式和版式默认值的下面)。我们的第一个任务是设置主内容`div`和`aside`的宽度，并使它们彼此相对浮动:

```
 article > div {
 float: left;
 width: 348px;
 margin-right: 24px;
 margin-bottom: 24px;
 padding: 24px;

 background: #FFF;
 -moz-border-radius: 10px;
 -webkit-border-radius: 10px;
 border-radius: 10px;
} 

article > aside {
 float: right;
 width: 228px;
} 
```

你会注意到我正在使用[儿童选择器(> )](https://reference.sitepoint.com/css/childselector/) 。当然，这只是为了举例，但是根据您需要支持的浏览器，您可能需要依赖更传统的选择器，在这种情况下，您需要向您的标记添加一些`class`和`id`属性。

这里的数学很简单:main `div`有五列宽，并且在这些列之间包括四个间距。所以，(5 x 60) + (4 x 24) = 396。两边都有 24px 的衬垫，这样就剩下 348px 作为`div`的宽度。对于`aside` s 来说，计算方法是(3 x 60) + (2 x 24) = 228。布局的整个宽度由内容上的 24 个像素的空白组成`div`。

这些样式已经给了我们足够好的两列布局，尽管还有一些问题。首先，图像对于它所在的列来说太大了。这是一个足够简单的修复方法(我已经添加了一些边框和轻微的阴影):

```
article > aside img {
	width: 218px;
	padding: 4px;
	background-color: #FFFFFF;
	border: 1px solid #DDDDDD;
	-webkit-box-shadow: 2px 2px 2px rgba(0,0,0, 0.2);
	-moz-box-shadow: 2px 2px 2px rgba(0,0,0,0.2);
} 
```

我还为标题、段落和列表设计了一些默认样式，但由于这些与布局无关，我将在这里跳过它们。你可以随时查看[最终示例](http://sitepoint-examples.s3.amazonaws.com/responsive/index.html)的源代码。

好了，现在我们有了默认的布局，让我们继续看样式表。下一个布局，你会看到，是超宽的 1280 像素。在您自己的响应式设计中，您可能希望从两列设计切换到三列甚至四列设计，并重新组织整个布局以更好地利用所有可用空间。但是为了便于说明，让我们放大现有的布局:

```
@media only screen and (min-width: 1212px) {

	body {
		padding: 96px 72px 0;
		width: 1068px;
		position: relative;
	}

	article > div {
		width: 600px;
		margin-bottom: 24px;
	}

	article > aside {
		width: 396px;
	}

	article > aside img {
		width: 386px;
	}
}
```

查看一下`@media`声明的语法:它声明所包含的 CSS 规则应该只应用于屏幕，并且只应用于最小宽度为 1212 像素的屏幕。

有了这些规则，现在你应该能够把你的浏览器窗口拉得更宽，当你超过 1212 像素时，可以看到设计弹出到更大的尺寸。很好很容易！

最后，让我们来看看适用于移动设备和更小屏幕的更窄的单列显示器。这两者都要求我们重写`float`声明，将所有内容都放在一列中。对于最小的布局，在 320 像素，我们也将减少所有的字体大小，以保持行的长度可读:

```
@media only screen and (max-width: 767px) and (min-width: 480px) {

	body {
		padding: 60px 42px 0;
		width: 396px;
		-webkit-text-size-adjust: 100%;
	}

	article > div, article > footer, article > aside {
		float: none;
		clear: none;
	}

	article > div {
		width: 348px;
	}

	article > aside img {
		width: 386px;
	}

}

@media only screen and (max-width: 479px) {

	body {
		padding: 48px 46px 0;
		width: 228px;
		-webkit-text-size-adjust: 100%;
		font-size: 13px;
		line-height: 18px;
	}

	article > div {
		width: 192px;
		padding: 18px;
		margin-bottom: 18px;
	}

	article > header > h1 {
		font-size: 26px;
		line-height: 36px;
	}

	article > div > header > h2 {
		font-size: 16px;
		line-height: 24px;

	}

	article > div, article > footer, article > aside {
		float: none;
		clear: none;
	}

	p, section, aside, ul {
		margin-top: 18px;
	}
}
```

就是这样！现在，从智能手机到宽屏办公显示器，您拥有了一个反应灵敏的设计，可以适应各种屏幕宽度。

请看一下[演示](http://sitepoint-examples.s3.amazonaws.com/responsive/index.html)看看它是如何工作的。

## 最终注释

响应式布局是一种很好的方式，可以让更多的设备更容易访问您现有的网站。然而，就你的移动战略而言，这并不是说你可以到此为止了。正如杰夫·克罗夫特和其他人所指出的，一个好的移动网站不仅仅是一个狭窄的显示屏。也就是说，根据你的资源、你的受众和你网站的焦点，一个完全响应的布局至少是支持移动设备的第一步。

Less Framework 3 实际上根本不是传统意义上的框架，它是使用网格布局实验响应式设计的好方法，并且可以感受这种工作所需的 CSS 语法和结构。一旦你开始创建你自己的响应式设计，你可以把它放在一边，从头开始制作你的 CSS，或者你可以把它作为一个蓝图来启动你自己的响应式布局。

## 分享这篇文章