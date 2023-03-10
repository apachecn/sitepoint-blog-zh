# 快速跟踪您的 Flash 网站

> 原文：<https://www.sitepoint.com/fast-track-flash-site/>

许多使用 Flash 的网站可以进行优化，加载速度比现在快得多。通过分解一个大的。swf 分成多个更小的。swf 文件，您可以减少网站加载时间，同时使网站更容易管理。这篇文章将着眼于如何打破你的网站分成多个。swf 文件，并讨论为什么这是一个好主意。

让我们从为什么开始。毕竟，你不会关心如何做，直到你意识到为什么这会对你的网站有益。

##### 为什么要模块化？

为什么要创建多个更小的？swf 文件而不是一个大文件？这看起来像更多的工作，对不对？

实际上，正如我们稍后将看到的，这真的没有那么多工作。事实上，您只需添加一行代码，好处是巨大的！

***加载次数更快***

第一个也可能是最大的好处是加载时间。假设我们有一个闪存文件(。swf)那是 200k 大小。为了我的数学技能，我们将保持这个非常简单，想象一下在 28.8 的连接上加载文件需要 40 秒。在 40 秒结束时，整个站点被加载并准备好供查看。现在假设这个 200k 的网站有四个主要部分:“关于”、“历史”、“新闻”和“产品”。如果访问者只对你网站的某个部分感兴趣，比如“关于”部分，该怎么办？你让他们等待整个网站加载，而他们想要的只是这一个部分！

为了避免这种不必要的等待，我建议你做的是创建五个独立的。swf 文件:一个主。swf 和一个单独的。网站每个部分的 swf:“关于”，“历史”，“新闻”和“产品”。当然，这些加在一起。swfs 也将等于 200k，但这是我们看到加载时间优势的地方。你想要这个。swf 只包含基本要素—导航按钮和界面。因为我们已经从文件中删除了所有其他部分，所以主。swf 现在应该可以快速加载了。

我们假设我们能够得到主要的。swf 将在五秒内加载。现在，当用户点击“关于”链接时，也需要五秒钟来加载。这个例子中的完整加载时间是 10 秒，为这个用户节省了 30 秒的总加载时间！他们可能不会马上出来感谢你节省了他们的时间，但他们更有可能再次访问你的网站。

***易于维护***

更模块化的 Flash 站点的第二个好处是，这种方法使将来维护和更新站点变得更加容易。如果你想修改你网站的“关于”部分，你可以——而不必搞乱整个网站。您可以只对“关于”进行更改。主权财富基金就是这样！如果您想添加或删除站点的某些部分，您不必修改整个站点，将整个框架集从一层移动到另一层。模块化将挑剔的维护变成了更简单的过程。

##### 规划模块化场地

所以我们来复习一下。现在我们有五个。主权财富基金:一个主要的。尽可能小的 swf 以确保快速的加载时间——我尽量保持这个主要的。70k 以下的 swf，可能的话更小。如果你想知道主菜包括什么。swf，一个很好的经验法则是合并那些总是出现在屏幕上的元素，或者是在不同部分之间不会改变的东西。所以在我的网站上。swf 通常包含一些界面和导航按钮。

我们还为网站的每个附加部分准备了一个. swf 文件。当从主菜单中选择该部分的导航按钮时。理想的主权财富基金。swf 加载到 main .swf 中，不言而喻，每个。swf 应该有一个预加载程序。

好了，现在我们知道了模块化你的 Flash 站点的好处，让我们来看看你是怎么做的。

##### 加载外部。swfs 到主 Flash 电影中

正如我所承诺的，这真的很简单。

***1。添加电影剪辑***

您需要做的第一件事是将电影剪辑添加到您的 main。swf 在您想要外部的位置。加载时出现的 swf。这个 MovieClip 只是外部。swf，所以里面不一定要有什么东西。一旦您将电影剪辑添加到主菜单中。swf，给它一个实例名；我通常叫我的“电影人”。

太好了。您的电影剪辑已放置在您需要外部剪辑的位置。当它的导航按钮被选中，并且您已经给了它一个实例名——现在是代码。

***2。插入**代码*

去你的主层。只用于代码的 swf。你有这个，对吧？如果没有，你应该——我所有的底层。swfs 总是严格地为代码保留，所以当我对文件进行更改或修改时，我可以很容易地找到它。

在此图层中，找到您在步骤 1 中添加了 MovieClip 的关键帧。插入此代码:

```
loadMovie("external.swf", "movieclip_instance_name"); 
```

应该可以了！这段代码将把你的外部电影加载到你的主。swf——希望可以无缝地完成！

在下面的布局中可以看到一个模块化的 Flash 站点是如何组合在一起的例子。这展示了我最近设计的一个网站的设计布局，显示了每个. swf 的位置和大小。

![944_Layout](img/47423b9d018fb5837daf33653c309280.png)

##### 带上你的。主权财富基金

1.  一旦 Flash 打开一个新的空白阶段，添加另一层，给你两层。
2.  将顶层标记为“电影容器”,底层标记为“动作脚本”
3.  在“电影支架”层的第一帧中，添加一个矩形。
4.  选择矩形并点击 F8。做成 MovieClip，叫“mc_holder”。
5.  在矩形的属性中，给它一个实例名“movieholder”。
6.  打开 MovieClip，右键单击包含矩形的图层，然后选择属性。选择图层类型参考线，它将隐藏矩形。请记住，这个电影剪辑只是一个外部的巢。我们很快就会加载它，所以你实际上不需要看到这个矩形。当你回到主路。你将不再看到矩形，只有电影剪辑。

8.  接下来，将 actionscript:
    `loadMovie("external.swf","movieholder");`
    添加到标题为“actionscript”图层的第一帧。

10.  拯救电影。现在，我们将制作一部新电影，并最终将其加载到我们刚刚制作的电影中。
11.  开始一部新电影，并在舞台上添加一个圆形(或任何您想要的元素)。
12.  将此影片另存为“外部”(或您在步骤 7 的 actionscript 中使用的任何文件名)，然后发布它。这将生成一个。名为“external.swf”的 swf
13.  现在打开我们拍的第一部电影。你会注意到外部的。swf 现在不再出现在其中，但是如果您发布它，外部 swf 将被加载到 main .swf 中。

应该可以了！记住这一切。主权财富基金应该有一个预加载程序。

我希望这篇文章对如何充分利用你的 Flash 站点，并使它尽可能快地加载有所启发。Flash 的加载时间受到了太多的批评——这些批评应该更多地指向设计者，而不是技术。设计师们，去让 Flash 骄傲吧！

## 分享这篇文章