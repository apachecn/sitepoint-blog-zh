# jQuery 移动组件

> 原文：<https://www.sitepoint.com/jquery-mobile-components/>

你好。这是旨在解释 jQuery Mobile 基础知识的系列文章中的第二篇。在介绍性文章 [Hello jQuery Mobile](https://www.sitepoint.com/hello-jquery-mobile/) 中，我们介绍了设置、页面结构、导航、转换和基本列表视图。

在此过程中，我们构建了一个参考站点，演示了如何使用 CSS 创建没有图像的形状。本文继续开发那个 [CSS3 Shapes](http://3easy.org/buildmobile/jquerymobile/) 参考站点，并继续称我们的新朋友为 jQM，但是这一次我们将对组件进行短暂的参观。

### 在最前沿

当我们向您介绍 jQM 时，我们注意到代码处于 *Alpha* 状态。从那时起，第一个 [jQM *Beta* 开始在街上出现](http://jquerymobile.com/blog/2011/06/20/jquery-mobile-beta-1-released/)。有许多方法可以跟上这种发展，我们一会儿再回来讨论。当然，对于硬核来说，还有 jQuery Mobile GitHub 库。

GitHub repo 吸引了我，因为正如在`README.md`中提到的，你可以让 docs &演示在本地运行，这有最新的 jQM 版本的双重好处。我们将克隆存储库的副本。如果你现在还没有加入 Git 的行列，那你真该感到羞耻。还不算太晚。

您需要一个本地 web 服务器。我正在使用配置为在 Mac OS X 的“站点”文件夹中运行的 [MAMP](http://www.mamp.info/) ，所以我将在“站点”中创建新的“jQueryMobile”文件夹。然后在终端中输入以下命令，一次一个。诚然，这是我的环境所特有的，所以您可能需要做一些小的调整。

**克隆 jQM Repo**

```
cd ~/sites/jquerymobile
git clone git://github.com/jquery/jquery-mobile.git
cd jquery-mobile
make
```

仅此而已。将您的浏览器指向我们新的`jquerymobile/jquery-mobile/`文件夹，查看本地文档和演示！该项目位于在克隆过程中创建的`jquery-mobile`文件夹中。这对于在公园或咖啡馆做一些开发是很棒的，而且浏览起来更快。

作为`make`命令的结果，你将有一个名为`compiled`的文件夹，其中包含了`*.js`和`*.css`文件的最新副本。将新文件复制到我们参考项目的`jqm`文件夹中，更新`<head>`中的链接，我们就处于最前沿了。

请注意，第一个 *Beta* 中的图像与 *Alpha* 中的图像相同，但是如果它们确实发生了变化，或者您是第一次这样做，请使用来自`/themes/defauimg/`的图像添加或更img/`。最后，注意 jQuery 是 1.6.1 版，jQM 项目使用它，所以也要更新那个文件。

### 跟上发展

跟上发展是很重要的。除了 GitHub，阅读 jQuery 移动博客，在 Twitter 上关注开发者，像 [@scottjehl](https://twitter.com/#!/scottjehl/status/83154535186563072) 这样的人会宣布更新，并建议必读的内容。这一点尤其重要，因为 jQM 现在发展非常迅速。

例如，缩放在 *Alpha* 之前被禁用，在 *Beta* 中被恢复，原因在 [Beta 1 发布帖](http://jquerymobile.com/blog/2011/06/20/jquery-mobile-beta-1-released/)中有解释。从现在开始，开发人员必须将`meta viewport`标签添加到标记中，而以前它是动态插入的。

**都在脑袋里**

```
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>CSS3 Shapes</title> 
    <link rel="stylesheet" href="jqm/jquery.mobile-1.0b2pre.min.css" />
    <script src="jqm/jquery-1.6.1.min.js"></script>
    <script src="jqm/jquery.mobile-1.0b2pre.min.js"></script>
</head>
```

有了这些对`<head>`的添加，我们的参考站点现在可以正确缩放，其中列宽变窄，但是文本和图标保持全尺寸。友好的开发人员尽他们所能帮助 T2，但是确保你能跟上 T4 的步伐，尤其是在 jQM 项目酝酿期间。

### 没有回头路

在这一阶段，我们有了原始的参考项目，在`/jqm/`文件夹中有更新的 jQM 代码文件和更新的`meta viewport`标签。打开你的参考网站，注意除了主页或`index.html`之外的页面上没有“后退”按钮。“返回”按钮现在是默认关闭的，[正如预测的](https://www.sitepoint.com/hello-jquery-mobile/#notesonnavigation)。

一个简单的解决方法是给每个`data-role="page"`元素添加`data-add-back-btn="true"`，这将恢复“后退”按钮。该按钮忽略`href`并返回一个历史条目。等效的手动代码将是`<a href="index.html" data-icon="arrow-l" data-rel="back">Back</a>`

但是让我们考虑一下这个手动代码，因为当你在你的站点上编码链接的时候，它会带来一些复杂性。文档说你应该为 C 级浏览器提供一个有意义的`href`,它实际上指回引用页面。这意味着你需要编写代码来动态地*填充`href`以指向前一页。*

 *当使用`data-add-back-btn="true"`属性以编程方式添加按钮时，jQM 会添加一个简单的`href="#"`。这对 jQM 来说没问题，因为如果它使用 Javascript 添加一些东西，那么它可以假设使用 Javascript 处理 back 按钮不会有问题。C 级浏览器的优雅后退或者缺乏 JS，将需要一个动态的`href`属性。

### 标题工具栏和按钮

在`data-role="header"`元素中文本的两边都有一个按钮槽，所以在标准配置中有两个。jQM header 插件寻找`data-role="header"`的直接子节点，它找到的第一个链接在左边，第二个在右边。

一旦我们离开了主页，让我们在标题中添加一个“home”按钮，方法是在`data-role="header"`元素中添加`<a href="#shapes" data-icon="home">Home</a>`。仅此一点，你就会想知道我们的“后退”按钮到哪里去了。通过添加`class="ui-btn-right"`来控制位置，并且“返回”按钮返回视图，因为“主页”按钮不在它的右边。

如果我们将`data-rel="back"`添加到“Home”按钮链接，jQM 将从历史堆栈中删除一个项目，并在它被触摸或点击时反转转换。添加`data-iconpos="notext"`会从按钮中移除文本，但是我们可以将文本留在标记中。

**主页按钮**

```
<header data-role="header">
    <h1>Shape</h1>
    <a href="#shapes" class="ui-btn-right" data-rel="back">Home</a>
</header>
```

当我们离“主页”只有一步之遥时，这很好，但是当我们离“CSS”页面只有两步之遥时，标记必须改变，因为`data-rel="back"`将返回到“形状”页面而不是“主页”。所以我们将使用一个有意义的`href`和`data-direction="reverse"`，这意味着我们得到了从左到右的幻灯片，并且心智模型保持不变。

注意，[有一个错误，即`class="ui-btn-active"`没有被正确删除](https://github.com/jquery/jquery-mobile/issues/1405)，以至于在返回主页后重新访问页面时,“主页”按钮仍然处于活动状态。评论中建议了一些短期的解决方案，使用额外的 Javascript 来解决这个问题。

### 页脚工具栏和固定位置

基本上，适用于`data-role="header"`的规则同样适用于`data-role="footer"`。主要的区别是结构更少，按钮不是左右放置的，而是都是内嵌的。

一个很酷的特性是通过使用一个公共的`data-id=""`，在转换之间保持`data-role="footer"`不变。注意，这要求`data-role="header"`和`data-role="footer"`使用固定定位。

要利用本机滚动并让工具栏在滚动后重新出现，请使用`data-position="fixed"`。将这段代码添加到代码中的所有`data-role="header"`和`data-role="footer"`元素中。

现在我们可以添加一个持久的页脚，通过添加一个公共的`data-id="footer"`到每个`data-role="footer"`元素。注意，在所有`data-role="footer"`元素上设置的`data-position="fixed"`意味着它是全局元素。现在我们有了一个固定的位置`footer`，它在页面切换时保持不变。酷毙了。

**持久页脚**

```
<footer data-role="footer" data-position="fixed" data-id="footer">
    ...
</footer>
```

### 对话框和数据图标

由于我们的`data-role="footer"`是一个全局元素，我们将在其中放一些全局的东西，比如关于 web 应用程序的页面。我们将使我们的应用程序页面呈现为一个对话框，展示代码并讨论其含义。首先要注意的是，对话框不包括在哈希状态历史跟踪中。

在源代码底部创建一个新的`data-role="page"`元素，以保持合理的源代码顺序。这个页面有一个稍微不同的结构，一个简单的`data-role="page"`和`data-role="header"`，包括我们的“主页”按钮。没有`data-position="fixed"`应用于`data-role="header"`，因为那会破坏模态对话框。根本没有`data-role="footer"`，因为那也会破坏模态对话框。

接下来，在所有标准`data-role="page"`元素的`data-role="footer"`元素中，添加一个到新对话框的链接。将`data-rel="dialog"`添加到链接中，确保链接的页面以对话框的形式出现。我们将为按钮图像选择一个`data-icon=""`，我们使用的是`data-icon="gear"`。

模态对话框的默认转换是`data-transition="pop"`，否则建议使用`slideup`或`flip`让它看起来更像对话框。默认情况下填充是关闭的，所以我们需要为按钮、导航条和其他小部件重新添加填充。以这种方式使用时，将`class="ui-bar"`添加到所有`data-role="footer"`元素中。

**对话齿轮**

```
<footer data-role="footer" data-position="fixed" data-id="footer" class="ui-bar">
    <a href="#about" data-rel="dialog" data-icon="gear" data-transition="pop">About</a>
</footer>
```

### 小跟班

我们可以通过添加`data-role="button"`将任何锚链接样式化为按钮。默认情况下，按钮会填满可用的屏幕宽度。通过使用`data-inline="true"`属性，使按钮紧凑，并且仅与它们包含的文本和图标一样宽。通过将按钮包装在应用了`data-inline="true"`属性的包含元素中，并排设置按钮。

通过将按钮包装在应用了`data-role="controlgroup"`属性的容器中，对块中的按钮进行分组。默认情况下垂直堆叠，通过将`data-type="horizontal"`添加到`data-role="controlgroup"`元素来更改为水平组。

向按钮添加图标是通过`data-icon=""`属性实现的。jQM 附带了几个精灵，包括移动应用程序最常用的图标选择，只需在属性中指定您的图标选择。默认情况下，位置在文本的左边，但是按钮图标的位置可以使用`data-iconpos=""`属性来控制。

对于我们的参考应用程序，我们将坚持使用一个简单的`controlgroup`和两个`data-role="button"`元素，一个用于“形状”，一个用于“CSS”。我们将把这个模式应用到每个“Shape”和“CSS”页面中，从而在形状和用于生成它们的代码之间提供快速切换。

**形状**

```
<nav data-role="controlgroup">
    <a data-role="button">Shape</a>
    <a href="#shape_css" data-role="button" data-transition="pop">CSS</a>
</nav>
```

**CSS**

```
<nav data-role="controlgroup">
    <a href="#shape" data-role="button" data-rel="back">Shape</a>
    <a data-role="button">CSS</a>
</nav>
```

### 列表

我们的好朋友 jQM 包含了广泛的列表类型和格式选项，涵盖了大多数常见的设计模式，所有这些都很容易使用，只需在标记中添加`data`项。我们在第一篇文章 [Hello jQuery Mobile](https://www.sitepoint.com/hello-jquery-mobile/) 中讨论了相当多的选项。但是还有一个简单的例子。

我们将在“关于”对话框中添加所有“形状”和所有“CSS”页面的索引。添加一个`<h4>`作为每个列表的标题，然后为项目添加普通的语义 HTML 元素`<ul>`和`<li>`。我们只需要添加两个额外的`data`项目，它们是`<ul data-role="listview" data-inset="true">`。这将在“关于”页面中产生两个插入列表。

**插入列表**

```
<h4>Shapes</h4>
<ul data-role="listview" data-inset="true">
    <li><a href="#circle">Circle</a></li> 
    ...
</ul>
```

### 内容格式化

jQM 对内容样式采取了一种温和的方法，允许浏览器的本机呈现优先，只添加一点点填充以方便阅读。你可以使用主题系统来控制字体和颜色。我们将把主题框架留到以后的炉边谈话中。

返回布局。jQM 通过一个叫做`ui-grid`的约定提供了一种简单的方法来实现基于 CSS 的列。尽管多栏布局在移动环境中很难实现，但布局网格可以通过一些预设的配置来实现。

对于两列网格，只需向包含元素添加一个`class="ui-grid-a"`，并在其中添加带有`class="ui-block-a"`和`class="ui-block-b"`的元素。一个三列网格使用一个应用了`class="ui-grid-b"`的容器，并期望容器中有`class="ui-block-a"`、`class="ui-block-b"`和`class="ui-block-c"`。

最后，请注意，多行网格可以通过向两列网格添加四个子块、向三列网格添加六或九个子块来实现。根据您想要的布局，在`class="ui-block-a"`和`class="ui-block-b"`之间循环

**多行网格**

```
<div class="ui-grid-b">
    <div class="ui-block-a">Row 1 Cell 1</div>
    <div class="ui-block-b">Row 1 Cell 2</div>
    <div class="ui-block-c">Row 1 Cell 3</div>
    <div class="ui-block-a">Row 2 Cell 1</div>
    <div class="ui-block-b">Row 2 Cell 2</div>
    <div class="ui-block-c">Row 2 Cell 3</div>
</div>
```

随着可折叠块的出现，内容格式化的乐趣不断涌现。可折叠的内容块是通过向容器添加一个`data-role="collapsible"`属性来创建的，其中 jQM 会向直接放置在容器内的`heading`元素添加一个“+/–”图标。

通过一个`<h6>`添加一个`<h1>`的`heading`元素，该元素通过扩展图标保持可见，然后标记`data-role="collapsible"`容器中的剩余内容，然后可以通过`heading`打开和关闭其可见性。

**可折叠**

```
<div data-role="collapsible">
    <h3>Header & Icon</h3>
    <p>Collapsible Content</p>
</div>
```

当我们走向终点时，有一点需要注意，页面的内容区域应该从应用于`data-role="page"`元素的`data-theme="x"`属性中获取大部分样式。jQM 中有一个全面的面向对象的 CSS 框架，您可以按照自己的想法来使用它。

### 结束语

到目前为止，我们已经介绍了启动和运行 jQM 的基础知识，然后是简单地添加到标记中就可以使用的组件的 whistlestop 之旅。我们所做的一切都是通过标记实现的。

有很大的深度有待发现，而我们仅仅触及了表面。如果你足够幸运开始追求 jQM，你可能会发现她丰富的 API，涉及到事件、方法和工具、响应布局和主题框架。

在 [3Easy](http://3easy.org/buildmobile/jquerymobile/) 查看完整的参考网站。请在评论中留下你的想法和问题，大胆地提出你下一步想去哪里。谢谢你的来访，现在我向你告别。我和 jQM 有个约会。

*   [GitHub jQuery 手机演示](https://github.com/buildmobile/jqm)
*   [3 简易 jQuery 移动演示](http://3easy.org/buildmobile/jquerymobile/)* 

## *分享这篇文章*