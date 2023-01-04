# FontFace jQuery 插件

> 原文：<https://www.sitepoint.com/the-fontface-jquery-plugin/>

在安全的战壕里，浏览器大战仍在进行。这场战争最艰难的前线之一是`@font-face`。

你习惯于需要专有的扩展来使用新的 CSS3 属性——我们可以解决这个问题。然而，即使是经验丰富的老手，也会发现自己对成功实现`@font-face`需要多少特定于浏览器的代码毫无准备。是时候做好准备了！

准备好了吗？

```
@font-face {
  font-family: "BestFontEver";
  src: url('file/path/filename.eot');
  src: local('☺'), url('file/path/filename.woff') format('woff'), url('file/path/filename.ttf') format('truetype'), url('file/path/filename.svg#BestFontEver') format('svg');
}
```

即使这样也不会设置任何元素来使用你的新字体；它只是注册您想要使用它，并请求浏览器提供新的字体。

好消息是，一旦注册，字体可以像我们一直使用的`font-family`一样使用。假设我们有一个无衬线字体:

```
#element, .elements {
  font-family: "BestFontEver", Helvetica, Arial, sans-serif;
} 
```

那么，为什么注册时会有这么多选项呢？

## 清点战斗人员

*   `.eot`(或嵌入式 OpenType)字体文件是微软的部队，所以如果你想让 BestFontEver 出现在 Internet Explorer 中，你就需要一个`.eot`版本。
*   Web 开放字体格式，或`.woff`，是这场冲突中的最新战士。它由 Mozilla 开发，受 Firefox、Chrome、Opera 和 IE9 支持。
*   接下来是扩展名为`.ttf`的 TrueType 字体，这是苹果在 20 世纪 80 年代末开发的。Safari、Opera、Firefox 和 Chrome 支持这些字体
*   SVG 格式？这适用于像 iOS 设备一样支持 SVG 的平台:iPhones、iPads 和任何运行 Mobile Safari 的设备。
*   最后一个`src`条目是什么？`local('☺')`？它是为了防止查看您页面的用户已经安装了名为 BestFontEver 的字体。它通过不加载您的本地字体来保护您的 BestFontEver 字体。这项技术是由保罗·爱尔兰人开发的，所以你可以看看他的[对为什么这是个好主意的解释](http://paulirish.com/2010/font-face-gotchas/#smiley)。

## 你的超级战士

那你现在怎么办？战线已经划定，但战争的迷雾不会很快消散。您需要的是一种快速、轻松地将来源于`@font-face`的字体添加到页面中的方法。

这就是 jQuery 的用武之地！我们可以使用一个插件作为我们的步兵，而我们坐在总部，等待浏览器一决雌雄。然后我们需要做的就是提供一个`jQuery.css()`样式的对象。 [FontFace jQuery 插件](http://sitepoint-code.s3.amazonaws.com/jquery.fontface.js)是一种很好的方式，可以将字体应用到您无法控制的站点，快速原型化字体在设计过程中的外观，给缺乏经验的开发人员更大的控制权，甚至如果您愿意，还可以用于您的生产就绪页面:

```
$("#element, .elements").fontface({
  fontName : "BestFontEver",
  fontFamily : ["Best Font Ever", "BestFontEver", "Helvetica, Arial, sans-serif"],
  filePath : "/_fonts/",
  fileName : "bestfontever-regular-webfont"
});
```

就这样，长版就这样！稍微调整一下插件后，我们可以将需求简化为:

```
$("#element, .elements").fontface({
  fontName : "BestFontEver",
  fileName : "bestfontever-regular-webfont"
});
```

您可以相当安全地删除`fontFamily`细节，并且仍然像这样指定堆栈:

```
$("#element, .elements").fontface({
  fontName : "BestFontEver",
  fontStack : "Helvetica, Verdana, Arial, sans-serif",
  fileName : "bestfontever-regular-webfont"
});
```

为了消除提供`filePath`细节的需要，我们刚刚在插件中设置了一个默认值。在插件代码中查找`defaults`对象:

```
defaults = {
  filePath: "/_fonts/",//change this to your font directory location
```

你仍然可以提供你需要的任何一次性的，但是在插件中设置它会让它一劳永逸。

更好的是，如果您有不同粗细或样式的字体，您可以使用这些:

```
$("#element, .elements").fontface({
  fontName : "BestFontEver",
  fontWeight : "bold",
  fileName : "bestfontever-bold-webfont"
});

$("#element em").fontface({
  fontName : "BestFontEver",
  fontStyle : "italic",
  fileName : "bestfontever-italic-webfont"
}); 
```

[抢到插件](https://s3.sitepoint.com/examples/jquery.fontface.js)自己试试吧！

你可以在诸如[http://code.google.com/webfonts/](http://code.google.com/webfonts/)(TrueType 字体)、 [http://webfonts.info](http://webfonts.info/wiki/index.php?title=Fonts_available_for_%40font-face_embedding) 、[http://www.fontsquirrel.com/](http://www.fontsquirrel.com/)和[http://www.fontspring.com/](http://www.fontspring.com/)这样的网站上找到优秀的、免费的`@font-face`字体。

最后一个建议。如果你相当确定你的大部分读者会安装你的字体，你可以在你的字体堆栈中使用它的常用名称来加载他们的本地版本。这就是我们在第一个例子中所做的。在之前，在你的字体堆栈*中使用公认的名称——有史以来最好的字体——你与`@font-face`加载版本相关联的名称——BestFontEver——意味着我们默认使用本地版本，如果它可用的话。如果它没有被安装，我们将继续我们的堆栈，直到有东西卡住，那将是我们的`@font-face`。*

## 我们站在你这边

战争是见鬼的，但是有了像 jQuery 的`@font-face`插件这样的盟友，你会在不知不觉中赢得战争！

## 分享这篇文章