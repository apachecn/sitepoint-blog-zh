# 介绍 SMIL——小本经营的多媒体演示

> 原文：<https://www.sitepoint.com/presentation-shoestring/>

并不是每个网站所有者或网络开发者都能负担得起 Flash、Director 或视频编辑或创作工具来将图片、声音和视频整合到他们的网站中。甚至 PowerPoint 也可能会超出你的理解范围。让我们面对现实吧，它们也不是最容易掌握的工具。但是如果你*有记事本，和一系列的声音、视频或图像文件呢？答案是什么？微笑！准确地说，是 SMIL。*

 *##### 什么是 SMIL？

SMIL 代表同步多媒体集成语言，由 W3C 指定。

它允许你做什么？SMIL 是一种基于 XML 的标记语言，它允许你将独立的图像、声音和视频组合成一个演示文稿。虽然它不会与 Director 或其他类似产品竞争高端功能，但 SMIL 的优点在于它很容易使用。*非常*简单！如果你学 HTML 没什么困难，那么你很可能在一个小时内掌握 SMIL。

##### SMIL 结构

SMIL 类似于 HTML，它分为`HEAD`和`BODY`部分。然而，这里的一个关键区别是，演示的基本空间布局是在`HEAD`部分使用区域定义的。然后，这些区域的内容被添加到`BODY`部分。

看一下下面的代码，看看这些部分是如何组成一个文档的:

```
<smil> 

  <head> 

    <layout> 

      <region id="reg1" top="0" left="0" width="300" height="200" /> 

      <region id="reg2" top="0" left="300" width="300" height="200" /> 

    </layout> 

  </head> 

  <body> 

    <seq> 

      <par> 

        <img id="img1" src="image.gif" region="reg1" dur="4s" /> 

        <audio id="aud1" src="audio.wav" dur="4s" /> 

      </par> 

      <par> 

        <img id="img2" src="image2.gif" region="reg2" dur="4s" /> 

        <audio id="aud2" src="audio2.wav" dur="4s" /> 

      </par> 

      <img id="img3" src="finish.gif" region="reg1" dur="4s" /> 

    </seq> 

  </body> 

</smil>
```

正如您所看到的，空间布局在`HEAD`部分中被清楚地定义，然后媒体格式被定义来填充由区域 id 定义的不同区域，尽管您不需要为声音文件定义一个区域。希望你也注意到了:

*   XHTML 语法用于结束所有标签，
*   “dur”属性的使用，以及
*   `<seq>`和`<par>`标签的使用。

***`dur`标记***

`dur`标签代表持续时间，允许您指定您希望媒体可见或可听的秒数。您应该将它指定为一个数字值，后跟表示秒的`s`。或者，如果您希望您的文件永久显示，您可以使用属性`indefinite`。

***`<seq>``<par>`标签***

`<seq>`和`<par>`标签分别代表“顺序”和“并行”,它们关注的是播放时文件的排序。

在你的`BODY`部分的开始有一个`<seq>`标签，指示查看器按顺序回放标签中包含的所有内容，这样一个文件将一直播放到结束，之后下一个文件将开始播放。使用`<par>`标签则相反——它指示包含在其中的所有文件同时播放。

然而，SMIL 给了你嵌套这些标签的能力，这允许你在控制事件上有更大的灵活性。我在上面的例子中放置`<seq>`和`<par>`标签的方式意味着一个图像将与一个播放 4 秒钟的音频文件同时显示。然后，第二个图像将伴随着声音文件显示 4 秒钟。最后，一幅图像会自己出现 4 秒钟。因此，这将是一个总时长为 12 秒的演示，使用 5 个不同的文件，每个文件 4 秒钟。

##### 模块化架构

SMIL 的建筑是模块化的。它拥有总共 45 个与各种功能相关的不同模块，这些模块分为以下功能区域:

*   结构
*   元信息
*   布局
*   时机
*   媒体对象
*   连接
*   内容控制
*   动画
*   过渡
*   时间操纵

这种模块化结构允许软件制造商根据需要在其产品中包含一些或所有模块，并允许标准计划也这样做。希望你能体会到这个简单概念的力量。

##### SMIL 的 HTML 特性

SMIL 还整合了许多基本的 HTML 功能，例如:

```
<a show="new" href="index2.smi"> 

  <img src="image.gif" region="reg1" dur="indefinite" /> 

</a>
```

***正文***

```
<text src="text1.txt" region="reg1" dur="10s" />
```

就是这样！您现在可以开始创建了！你所需要的只是一个记事本，一个可以显示你的作品并将你的文件保存在*中的浏览器。smi 格式。快乐融入！

##### 进一步的信息

有许多玩家可用，但一个流行和广泛使用的选项是[真正的一个玩家](http://www.realnetworks.com/solutions/leadership/realone.html)。

请务必在[http://www.realnetworks.com/resources/samples/smil.html](http://www.realnetworks.com/resources/samples/smil.html)观看 SMIL 可能性的逐个功能演示

你可以在[https://www.w3.org/AudioVideo](https://www.w3.org/AudioVideo)找到更多关于当前(SMIL 2.0)和过去建议的信息和资源** 

## **分享这篇文章**