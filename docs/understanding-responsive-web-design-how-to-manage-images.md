# 理解响应性网页设计:响应性图像

> 原文：<https://www.sitepoint.com/understanding-responsive-web-design-how-to-manage-images/>

在我们的[理解响应式网页设计](https://www.sitepoint.com/series/understanding-responsive-web-design/ "Understanding Responsive Web Design")系列的第五部分，我将向你展示一个网页设计师在构建一个响应式网站时面临的典型问题之一:图像的管理。正如您到目前为止所看到的，移动设计人员面临的挑战有很多，认为最大的挑战之一是为多种屏幕尺寸提供多种图像格式并不奇怪或不寻常。目前有三种策略可供开发者选择:他可以选择“对抗”浏览器，“放弃”自己，或者依赖服务器。让我们试着去理解每个选项的优点和缺点。


### 对抗浏览器

大多数前端开发者选择应用第一种策略，对抗浏览器。“斗浏览器”代表什么？这意味着尽最大努力向浏览器提供“正确的”图像(即图像的大小适合用于查看网站的设备)，以便在浏览器下载“错误的”图像(即图像的“默认”或桌面版本)之前加载该图像。这是一项越来越困难的任务，因为现代浏览器和不断加快的带宽意味着用户试图尽快下载图像。

### 辞职

有时候最简单最舒服的策略是唯一可行的选择:承认失败。一般来说，这种方法包括将默认图像加载到小屏幕上，其次，如果需要，为更大尺寸的屏幕加载更大的图像。虽然这种方法简单明了，但不推荐使用，因为在第二种情况下，当只需要一个请求时，会向浏览器发送两个请求。

### 依靠服务器

第三种策略涉及到使用服务器、服务器端编程语言和一些基本的检测形式来确定加载哪个图像。在浏览器能够看到和解释 HTML 代码之前，所有的逻辑都被执行。不幸的是，这种策略有其自身的缺陷:维护一个不断增长的移动设备和屏幕尺寸列表会变得非常复杂，并且需要不断的维护。

为了帮助您更好地理解这项技术的作用以及如何使用它，让我们考虑一个例子。在下面的例子中，我将向您展示几行 PHP 代码，但是不要担心，您不必向我这样的 PHP 专家介绍如何创建响应图像，即使您以前没有使用过 PHP，您也会理解这个例子。让我们假设您正在动态地创建一个页面，并且您正在使用下面的代码行注入一个`<img>`标记:

```
echo '<img src="heavy-image.png" />';
```

正如你可能已经知道的，移动连接没有桌面连接快。因此，为了加快页面在移动设备上的呈现速度，如果用户使用的是……可能是 iPhone，那么您需要注入一个轻量级版本的图像。您可以在服务器端检查发出请求的用户代理，如果设备是 iPhone，则插入较小的图像。下一个片段演示了这种技术:

```
if (strpos($_SERVER['HTTP_USER_AGENT'], 'iPhone') === false) {
  // Not an iPhone
  echo '<img src="heavy-image.png" />;
}
else {
  // An iPhone
  echo '<img src="light-image.png" />;
}
```

正如您所看到的，该方法非常容易实现，但是这种方法不是非常可靠，因为用户代理信息本身并不是 100%可靠。如果该方法失败，您的影像可能会出现一些明显的问题。

### 一种方法比其他方法好吗？

显然，每种方法都有其独特的优势和局限性，不可能有一种方法是所有移动项目的理想选择。但是，开发人员可以使用一些额外的技术和资源来创建响应性图像。

#### Sencha.io Src

第一种选择非常快速简单。这是由 James Pearce 开发的一项服务，它可以返回你选择加载的图像，并根据你的需要调整大小。它的名字是 Sencha.io Src，你所要做的就是把你的图片的 Sencha.io Src URL 位置作为图片的来源。

```
<img src="http://src.sencha.io/http://mysite.cimg/my-image.jpg" />
```

该服务使用请求设备的用户代理字符串来确定所需的图像大小，并相应地调整图像的大小。默认情况下，图像缩放到屏幕宽度的 100%。Sencha.io Src 定制化程度高；可以设置特定的宽度或任何其他参数。例如，如果您想将图像宽度设置为 250 像素，您只需将大小添加到 URL 中，如下所示:

```
<img src="http://src.sencha.io/250/http://mysite.cimg/my-image.jpg" />
```

该服务还缓存请求，因此每次加载页面时不会重新加载图像。

### 适应性图像

Matt Wilcox 提出了一个类似的解决方案。它包括首先确定屏幕的大小，然后创建(并缓存)正确图像尺寸的缩放版本。这是一个理想的技术，使现有网站的图像响应。下载代码后(可以在[adaptive-images.com](http://adaptive-images.com/)找到)，要让这个解决方案正常运行，你需要遵循三个简单的步骤:

1.  添加两个文件，**。htaccess** 和 adaptive-images.php**，到根文件夹；**
***   创建缓存文件夹并允许写权限；*   在文档的开头添加以下 Javascript 代码行:**

 **[source code language = " JavaScript "][/源代码]

由于这一行代码，屏幕分辨率被捕获并存储在浏览器 cookie 中。可以在文件 adaptive-images.php 中配置多个选项，但该文件的主要目的是为分辨率(`$resolutions`)设置变量。

```
$resolutions = array (800, 480, 320);
```

这些分辨率是基于屏幕分辨率(以像素度量的宽度)的图像“断点”。在这种情况下，该工具将为像素宽度为 320 或更小的设备提供一个适合移动设备的小图像。如果显示超过 320 像素，新的参考值是 480，这是上面数组中的下一个数值。

创建后，图像将存储在缓存文件夹中(您可以在其中更改文件名)，这样您就不再需要生成它们。

除了我所展示的，我想强调的是，关于自适应图像讨论的关键点在于它们的大小。这当然是一个重要的因素，但是我们经常忘记它不是唯一的因素。例如，为较小的显示器调整图像大小通常会降低图像的影响和识别度。在这些情况下，可能需要修改图像。即使是简单的裁剪边缘或多余的元素，也可以帮助图像在较小的尺寸下保持其影响力和重要性。

### 结论

在这篇文章中，我从响应式设计的角度描述了三种处理图像的方法。首先是与浏览器斗争——也就是说，尽最大努力给浏览器提供“正确的”图像来下载。第二种方法是选择 Sencha.io 之类的服务，这些服务使用发送请求的设备的用户代理字符串来理解屏幕大小并相应地调整图像大小。最后一个涉及到自适应图像——在这种情况下，开发人员只需要确定屏幕的大小，图像就会缩放以适应。

总之，在上面讨论的三种方法中，自适应图像可能是最好的解决方案，因为它不需要依赖于另一个站点或链接。在本系列的下一篇文章中，我们将研究开发人员可以用来管理响应式网站的所有图像的其他方法。

想了解更多关于响应式网页设计的知识吗？看看 SitePoint 的新书 [Jump Start 响应式网页设计](https://www.sitepoint.com/books/responsive1/)！** 

## **分享这篇文章**