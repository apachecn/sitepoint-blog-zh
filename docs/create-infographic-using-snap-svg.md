# 使用 Snap.svg 创建信息图

> 原文：<https://www.sitepoint.com/create-infographic-using-snap-svg/>

![snap-svg logo](img/897a10813b2ca22d33779523c06070f2.png)

[Snap SVG](http://snapsvg.io/) 是( [Dmitry Baranovskiy](http://dmitry.baranovskiy.com/) 的 [Raphael JS](http://raphaeljs.com/) 的下一代和自然进化的矢量库。

虽然没有什么可以阻止你编写原始的 SVG，但是像 [Snap](http://snapsvg.io/) ， [SVG 这样的框架。JS](http://www.svgjs.com/ "A lightweight library for manipulating and animating SVG") 和 [Bonsai](http://docs.bonsaijs.org/ "Bonsai is a graphics library which includes an intuitive graphics API and an SVG renderer.") 可以帮助解决一些跨平台和跨浏览器的问题。

简而言之，Snap 库包含了 SVG 技术的所有强大功能，使我们能够以更简单直接的方式创建各种独立于分辨率的矢量图形，包括静态和交互式图形。

它坚定地面向现代浏览器，因此，与 Raphael 相反，它支持高级功能，如遮罩、剪辑、图案、全渐变、组等。

在本教程中，我们将使用 Snap 提供的许多功能从头开始创建一个信息图。

我选择的主题是关于构成每个网页的三层(HTML、CSS 和 JS)。它将主要是静态信息图，但也将包括一个动画和互动的组成部分。

你可以在下面的截图中看到最终的结果:

[![Example Graphic: The Three Layers of Every Web Page](img/7c23d5bb9b16b90c2fb429d6cecdeb4c.png)](https://www.sitepoint.com/create-infographic-using-snap-svg/)

信息图表示例:每个网页的三个层次

## 入门指南

我们需要做的第一件事是创建一个名为*Snap info graph*的新文件夹，它将保存我们项目的文件。

然后，去 [Snap 的主页](http://snapsvg.io/)下载库。打开`.zip`文件，在 *dist* 文件夹中找到`snap.svg.js`文件，并解压到我们的项目文件夹中。接下来，在同一个文件夹中创建一个名为`infographic.html`的新文件。

打开它，并添加以下代码:

```
<!DOCTYPE HTML>
<html>
<head>
    <meta charset="utf-8" />
    <title>Snap Infographic</title>
    <script src="snap.svg.js"></script>
</head>
<body>

<script type="text/javascript"> window.onload = function () {

    var snap = Snap(912,912);

}; </script>

</body>
</html>
```

让我们看看这里有什么。

首先，在`head`标签中，我们添加了对`snap.svg.js`文件的引用。接下来，我们将一个`script`标签放在了`body`标签中。在`script`标签中，我们创建了变量`snap`，我们将使用它作为对库的引用。

这将创建一个具有指定尺寸的 SVG 绘图表面。

## 构建信息图

为了构建信息图，我们将如下进行:我们将从在`snap`变量后添加第一个代码块开始，然后，后面的每个代码片段都将放在前一个代码片段的下面。

我不会对每个方法及其参数进行过多的描述，因为这会导致无聊和不必要的冗长说明。我将只解释这些重要的代码部分。

要找到关于特定 API 方法的更多信息，请查阅 [Snap 的文档](http://snapsvg.io/docs/)。

### 背景

我们要添加到信息图中的第一个组件是背景。为此，我们将创建一个图案，由两条对角线交叉的线组成，放在蓝色矩形的后面。

```
var p_line1 = snap.paper.line(0,0, 8,8).attr({stroke: '#00ADEF'});
var p_line2 = snap.paper.line(8,0, 0,8).attr({stroke: '#00ADEF'});
var p_rect = snap.paper.rect(0,0, 8,8).attr({fill: '#3A54A4', opacity: 0.9});

var pattern = snap.paper.g(p_line1, p_line2, p_rect).pattern(0,0, 8,8);

var background = snap.paper.rect(0,0, 912,912).attr({fill: pattern});
```

在前三行代码中，我们创建了模式所需的元素。`p_line1`和`p_line2`做两条 X 交叉的线。`p_rect`使蓝色正方形微微透明。下一段代码将模式的元素组合到一个组中，并从中创建实际的模式。

最后，我们通过创建一个矩形元素来定义实际的背景，该元素与上面的绘图表面具有相同的尺寸，并用我们刚刚创建的图案填充它。

### 朗读者

现在，我们将创建标题部分:

```
var headline = snap.paper.text(56,100, ['The Three Layers','of','Every Web Page']).attr({fill: '#FBAF3F', fontFamily: 'Impact'});

    headline.select('tspan:first-of-type').attr({fontSize: '2.8em'})
    headline.select('tspan:nth-of-type(2)').attr({fill: 'none', stroke: '#FBAF3F', fontSize: '2.2em', dx: '15px', dy: '5px'});
    headline.select('tspan:last-of-type').attr({fontSize: '3.6em', x: '56px', y: '160px'});
```

这里，`headline`变量创建了一个文本元素。通常`text()`方法中的第三个参数只是一个字符串，但是我们这里需要的是一个包含多个文本字符串的数组。

这就是 Snap 如何为数组的每个字符串创建一个单独的`tspan`元素。

通过这样做，我们能够独立地设计每个文本字符串的样式。在接下来的三行中，我们使用 CSS 选择器来选择标题的第一、第二和第三部分，并分别操作它们。

我们使用`dx`和`dy` (delta x 和 delta y)属性将第二个字符串稍微向右下方偏移。使用绝对属性`x`和`y`定位第三个字符串。

接下来我们要创建的是一组代表网页三层的矩形。

```
var f_Shadow = snap.paper.filter(Snap.filter.shadow(-2, 4, 4));

var html_layer = snap.paper.rect(490,110, 200,30).attr({fill: '#00ADEF'}).transform('skewX(45)');
var css_layer = snap.paper.rect(500,100, 200,30).attr({fill: '#8CC63E', filter: f_Shadow}).transform('skewX(45)');
var js_layer = snap.paper.rect(510,90, 200,30).attr({fill: '#ED1C24', filter: f_Shadow}).transform('skewX(45)');

var underline = snap.paper.line(56,180, 856,180).attr({stroke: '#FBAF3F', strokeWidth: '5px'});
```

我们需要一个阴影滤镜来模拟投射阴影效果，所以我们在第一行代码中创建了一个。接下来，我们添加三个矩形，并将其转换为我们想要的样子。此外，我们把阴影过滤器的第二和第三个，从而使整个图片更加现实。

最后，我们添加了一条下划线，以便更清楚地将标题与我们刚刚创建的主体部分分开。

### 身体

这一部分的第一部分将是一个网格，我们将使用它作为一个容器来放置信息图的主要组件。

```
// Grid 

var arrow = snap.paper.polygon([0,10, 4,10, 2,0, 0,10]).attr({fill: '#FFF'}).transform('r270');
var marker = arrow.marker(0,0, 10,10, 0,5);

var grid_hl1 = snap.paper.line(56,380, 856,380).attr({stroke: '#FFF', strokeWidth: '2px'});
var grid_hl2 = grid_hl1.clone().transform('t0,200');
var grid_hl3 = grid_hl1.clone().transform('t0,400');
var grid_vl1 = snap.paper.line(256,200, 256,800).attr({stroke: '#FFF', strokeWidth: '2px', markerStart: marker});
var grid_vl2 = grid_vl1.clone().transform('t330,0');
```

首先，我们需要创建一个标记，我们将需要网格的两条线。我们定义了标记的形状(一个三角形箭头)。然后，在第二行中，我们用它作为实际标记的源。

下一个代码块创建网格。

我们添加第一条水平线，然后克隆它，并将其位置再平移两次。这给了我们三条水平线。

以同样的方式，我们创建两条垂直线。此外，我们还为它们添加了标记。

我们希望标记出现在行首，因此我们使用了`markerStart`属性。

我们将箭头放在垂直线上，作为信息图必须从下到上检查的提示。

```
// Shapes

var html_square = snap.paper.rect(96,620, 120,120).attr({fill: '#00ADEF', stroke: '#FFF', strokeWidth: '2px', filter: f_Shadow}); 
var html_square_text = snap.paper.text(110,693, 'HTML').attr({fill: '#FFF', fontFamily: 'Comic Sans MS', fontSize: '2em', fontWeight: 'bold'});

var css_circle = snap.paper.circle(156,480, 60).attr({fill: '#8CC63E', stroke: '#FFF', strokeWidth: '2px', filter: f_Shadow});
var css_circle_text = snap.paper.text(124,493, 'CSS').attr({fill: '#FFF', fontFamily: 'Comic Sans MS', fontSize: '2em', fontWeight: 'bold'});

var js_triangle = snap.paper.polygon([96,325, 216,325, 156,235]).attr({fill: '#ED1C24', stroke: '#FFF', strokeWidth: '2px', filter: f_Shadow});
var js_triangle_text = snap.paper.text(132,306, 'JS').attr({fill: '#FFF', fontFamily: 'Comic Sans MS', fontSize: '2em', fontWeight: 'bold'});
```

现在，我们将开始填充网格的销售。我们从一个正方形开始我们的 HTML 层。首先，我们创建一个正方形，并添加阴影过滤器。然后，我们创建一个文本元素，并把它放在形状里面。我们以同样的方式完成下一个图形。

```
// Ribbons &amp; Text Areas

var html_ribbon = snap.paper.polygon([296,798, 546,798, 526,780, 546,762, 296,762, 316,780, 296,798]).attr({fill: '#00ADEF', filter: f_Shadow});
var css_ribbon = html_ribbon.clone().attr({fill: '#8CC63E'}).transform('t0,-200');
var js_ribbon = html_ribbon.clone().attr({fill: '#ED1C24'}).transform('t0,-400');

var html_ribbon_text = snap.paper.text(355,790, 'STRUCTURE').attr({fill: '#FFF', fontFamily: 'Comic Sans MS', fontSize: '1.4em'});
var css_ribbon_text = snap.paper.text(333,590, 'PRESENTATION').attr({fill: '#FFF', fontFamily: 'Comic Sans MS', fontSize: '1.4em'});
var js_ribbon_text = snap.paper.text(330,390, 'INTERACTIVITY').attr({fill: '#FFF', fontFamily: 'Comic Sans MS', fontSize: '1.4em'});
```

我们转到第二列。首先，我们使用`.polygon()`方法创建一个色带，并再次添加阴影滤镜。

然后，我们克隆丝带，再平移两次，这样就产生了三个丝带，每层一个。接下来，我们需要添加标题到丝带。我们通过创建三个文本元素并将它们放置在正确的位置来实现。

```
var html_textarea = snap.paper.text(300,630, ['HTML adds structure to a website\'s', 'content (text, images, and so on).', 'You can think of it as the anatomy', 'of the web pages.']).attr({fill: '#FFF', fontFamily: 'Trebuchet MS', fontStyle: 'italic'});
var css_textarea = snap.paper.text(300,430, ['CSS adds style to the content.', 'It makes the web page\'s components', 'to appear in different colors, sizes,', 'and so on.']).attr({fill: '#FFF', fontFamily: 'Trebuchet MS', fontStyle: 'italic'});
var js_textarea = snap.paper.text(300,230, ['JavaScript adds behavior', 'to the content. It makes the latter', 'responsive to user\'s actions.', 'Click the first post\'s image to see', 'an interactive example.']).attr({fill: '#FFF', fontFamily: 'Trebuchet MS', fontStyle: 'italic'});

var textareas = [html_textarea, css_textarea, js_textarea];

for (var i = 0; i &lt; textareas.length; i++) {

    textareas[i].selectAll('tspan').attr({x: '300px', dy: '1.2em'});

};
```

在这一点上，我们需要解释一下每一层的职责和作用。

因此，我们创建了三个包含多个字符串的文本元素。我们需要每个字符串出现在单独的行。

因此，我们将元素放在一个数组中，然后使用 For 循环对它们进行迭代。该循环所做的是从每个文本元素中选择所有的`tspan`元素，并使其`x`属性等于`300px`，还将每个字符串垂直偏移`1.2em`。

最终结果是，您放入特定文本元素的所有字符串都被正确定位——所有字符串都是左对齐的，每个字符串都出现在单独的行上，并且行与行之间的间距被正确调整。

```
// Wireframes

var page = snap.paper.rect(610,400, 230,160).attr({fill: '#DFEDCC', filter: f_Shadow});
var logo = snap.paper.rect(625,410, 70,30).attr({fill: '#BDD1EC'});
var nav = snap.paper.line(620,450, 830,450).attr({stroke: '#652C90', strokeWidth: '6px', strokeDasharray: '25 5'});

var post_img = snap.paper.rect(625,464, 50,50).attr({fill: '#1B75BB'});
var post_txt = snap.paper.line(650,520, 650,550).attr({stroke: '#939597', strokeWidth: '50px', strokeDasharray: '3 7'});

var post = snap.paper.g(post_img, post_txt);
var post_2 = post.clone().transform('t70,0');
var post_3 = post.clone().transform('t140,0');
```

在第三列中，我们将放置微型网页线框，其目的是说明第二列中的解释。

首先，我们为网页创建一个新的矩形，并在其中添加阴影滤镜。然后，我们为徽标创建另一个矩形。对于导航，我们使用粗虚线。

接下来，我们创建帖子所需的元素(`post_img`和`post_txt`)。然后，我们通过对上述元素进行分组来创建第一个帖子。另外两个帖子是我们通过克隆第一个帖子并将其翻译到右边来创建的。

现在我们有了线框所必需的原型。

```
var f_Saturate = snap.paper.filter(Snap.filter.saturate(1));

var css_wireframe = snap.paper.g(page, logo, nav, post, post_2, post_3);
var html_wireframe = css_wireframe.clone().transform('t0,200').attr({filter: f_Saturate});
var js_wireframe = css_wireframe.clone().transform('t0,-200');
```

此时，我们需要另一个过滤器，这是我们在第一行中创建的。然后，我们使用刚刚创建的线框元素来创建第一个线框。

接下来，我们通过克隆第一个线框来创建第二个线框，并在其中添加饱和滤镜。最后降低颜色的饱和度，以显示 HTML 只是一个没有任何样式的结构。最后，我们以类似的方式创建第三个线框。

```
var panel = snap.paper.rect(650,200, 150,100, 3,3).attr({fill: '#1C98BA'});

var circle = snap.paper.circle(800,200, 10).attr({fill: '#1B75BB', stroke: '#DFEDCC', strokeWidth: '2px'});
var close_icon = snap.paper.text(796,204, 'x').attr({fill: '#FFF', fontFamily: 'Verdana'});
var close_button = snap.paper.g(circle, close_icon);

var lightbox = snap.paper.g(panel, close_button).attr({opacity: 0}).insertBefore(js_wireframe);
```

我们想在这里创建一个图像灯箱。所以，我们从一个圆角矩形开始。然后，我们创建一个圆形和一个“X”文本，通过组合它们来创建一个关闭按钮。

最后，我们通过将面板和关闭按钮组合在一个组中来完成 lightbox，并使其最初不可见。此外，我们把它放在`js_wireframe`后面，因为否则它会阻碍人们点击帖子。

```
var ipost = js_wireframe.select('g:first-of-type&gt;rect').attr({fill: '#1B75BB'})
                        .mouseover(function(){
                            this.attr({fill: '#003EBA', cursor: 'pointer'});
                        })
                        .mouseout(function(){
                            this.attr({fill: '#1B75BB'});
                        });

ipost.click(function(){

    lightbox.animate({transform: 't0,30', opacity: 1}, 400)
            .insertAfter(js_wireframe);

});
```

是时候让事情互动了。为此，我们从第一篇文章中选择图像(实际上是蓝色方块)，并在鼠标悬停时强制改变其颜色。

然后，我们添加 click 事件监听器，其目的是当第一篇文章的图像被点击时显示 lightbox。

```
close_button.mouseover(function(){

    this.attr({cursor: 'pointer'});

}).click(function(){

    lightbox.animate({transform: 't0,0', opacity: 0}, 400, function(){

        lightbox.insertBefore(js_wireframe);

    });

});
```

在上面的代码中，我们让鼠标悬停在关闭按钮上时，光标看起来像指针；当点击时，灯箱移动到原来的位置并淡出。

我们还向`animate()`方法添加了一个回调函数，在动画结束后将 lightbox 放在`js_wireframe`的后面。

### 页脚

我们最后的任务是在页脚添加一些文本。但是首先我们需要在`script`标签下面添加下面的代码片段(我重复一遍:在`script`标签下面):

```
<!-- SVG Heart Shape -->

<svg version="1.1" id="heart" xmlns="https://www.w3.org/2000/svg" xmlns:xlink="https://www.w3.org/1999/xlink" x="0px" y="0px"
     width="24px" height="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<path fill-rule="evenodd" clip-rule="evenodd" fill="none" d="M11.995,6.243C8.888-2.079,0.057-0.138-0.001,9.521
    c-0.032,5.305,4.205,7.288,7.026,9.408c2.736,2.057,4.683,4.87,4.988,6.068c0.261-1.173,2.43-4.066,4.962-6.125
    c2.769-2.251,7.059-4.104,7.026-9.408C23.943-0.219,14.958-1.748,11.995,6.243z"/>
</svg>
```

这是我从 Adobe Illustrator 创建的 SVG 文件中获得的代码。

```
var footer_text = snap.paper.text(543,872, ['Made with','for SitePoint.com']).attr({fill: '#B5DD25', fontFamily: 'Comic Sans MS', fontSize: '1.2em'});
    footer_text.select('tspan:nth-child(2)').attr({dx: '45px'});

Snap.select('#heart').appendTo(snap);
Snap.select('path').attr({fill: '#EC008B', stroke: '#FFF'}).transform('t645,850').insertAfter(background);
```

回到`script`标签内部，我们创建一个包含两个字符串的文本元素，然后通过使用`dx`属性用`45px`偏移第二个字符串。

您可能已经注意到，由于我们刚刚添加的 SVG 代码，我们的信息图稍微向右移动了一点。

别担心。我们马上就能解决这个问题。

首先，我们通过使用它的`id`属性选择 SVG 片段代码，并将其附加到绘图表面。然后，我们选择心脏的路径，使其中风白色，并填补了漂亮的粉红色。

此外，我们改变它的位置，以出现在两个字符串之间，并将其插入背景后，使其可见。

好的。现在一切应该都好了。

这是我们的快照信息图的工作代码。

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [Snap.svg 示例](http://codepen.io/SitePoint/pen/nleba/)。