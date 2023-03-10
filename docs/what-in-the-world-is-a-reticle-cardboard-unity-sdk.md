# 十字线到底是什么？探索 Cardboard Unity SDK

> 原文：<https://www.sitepoint.com/what-in-the-world-is-a-reticle-cardboard-unity-sdk/>

在我写了一篇关于使用 Unity 构建 Google Cardboard 虚拟现实体验的指南后不久，Google 将他们的 SDK 更新到了 0.6 版本，并做了一些改动。这些变化中的大多数并不太剧烈，但是有一个相当大的变化引起了一点混乱。**十字线。**

## 什么是十字线？

这是一个听起来相当荒谬的词，是一个听起来同样荒谬的词的同义词——“网格”。基本上，在我们的情况下，这是描述十字准线或目标的更高级形式的另一种方式。它是一系列线条，标记出我们的 VR 相机指向的位置。在谷歌 Cardboard SDK 中，默认情况下它是一个圆形，每当它看到新的物体时就会增长，当它在路径上看不到物体时就会收缩。

## 旧 Cardboard SDK (v0.5.2 及更低版本)中的 Reticles

在我们之前的例子中，在 Unity 文章中的[构建一个 Google Cardboard VR 应用程序，我们使用了一个看起来像这样的目标图像:](https://www.sitepoint.com/building-a-google-cardboard-vr-app-in-unity/)

![Our old target from the last article](img/f429f13969bc11b97ce3856c6bf232d2.png)

这基本上是一个图像文件，当你悬停在一个可点击的对象上时就会出现。我们可以让它代表我们想要的任何东西，所以十字准线是有意义的。然而，谷歌已经改变了他们的方法，不再使用图像！经过相当多的挖掘，我无法找到一个好的简单的方法来再次引入图像，但是你可以对 Google Cardboard SDK 的新十字线概念进行各种定制，应该可以达到相同的目的。

## 更新 Google Cardboard SDK

如果您正在使用一个使用旧 SDK 的现有项目，就像我们上一篇文章中构建的演示一样，这里有一个升级您的应用程序的快速指南。

首先，您需要从用于 Unity GitHub repo 的 [Cardboard SDK 下载最新的 SDK。](https://github.com/googlesamples/cardboard-unity)

接下来，我们要删除资产中的旧 Cardboard SDK 文件夹。 ***注意:**在升级之前，确保你已经备份了整个项目。此外，在继续之前，请检查您没有在“Cardboard”文件夹中添加任何您自己的自定义资产！*

右键单击资产中的“Cardboard”文件夹，然后选择“删除”:

![Deleting the old Cardboard SDK](img/5bd5474b3760837d76ad6ef5f0547d5a.png)

然后，转到*资产>导入包>定制包…* ，重新导入新的 Google Cardboard SDK:

![Importing a custom package](img/dccf4f6eac5e05a266ef9754129f23a5.png)

导航到您新下载的 v0.6 Cardboard SDK 并选择:

![Choosing the new Cardboard SDK](img/44ef635edaa590fce4d1cff7cf236ea6.png)

确保在出现的窗口中勾选了所有内容。你可能不需要“遗留”的，可以随意解开它，然后如果你有任何问题，通过重复这个过程导入剩下的。升级项目时，我不需要“遗留”项目。

![Ticking SDK components to import](img/5859563ef6c6d42df79d2ee1b89bbbd8.png)

如果你现在玩你的场景来测试，你会看到一切仍然工作…但任何花哨的自定义十字准线，你可能已经包括丢失！

![Upgraded scene with no target appearing](img/ef3e75aa99bd4fda91b1b2ef151c5960.png)

## 新纸板十字线预制

Google Cardboard SDK for Unity 的 0.6 版本包含一个名为“CardboardReticle”的新预置。这似乎是十字准线风格的用户在看哪里的反馈的最佳选择，并且一旦你知道了它，设置起来非常简单。

对于跟随我们之前教程的人来说，我们的首要任务是禁用或删除我们的“目标”游戏对象(它在*cardboard main>Head>Target*中)。我已经禁用了它，希望我能在将来找到重新实现图像目标的方法:

![Disabling our old target game object](img/8563c31cab4a790704ca66d990145e76.png)

为了带来你的新十字线，去*资产>纸板>预置> UI* 你会找到“纸板十字线”预置。将此拖动到“主摄像机”游戏对象中。CardboardReticle 需要成为“主相机”的子对象才能工作。

![Adding a Cardboard Reticle into the Main Camera](img/cbe5322fad49507177bc5fc01a411e28.png)

如果你现在播放这个场景，你会看到一个小圆圈跟着你的视线:

![Our reticle when it isn't active](img/8d14133f9241b04a07414dd88fd97823.png)

如果您将鼠标悬停在可以交互的元素上，圆圈会放大以显示以下内容:

![Our reticle when it is active over an object](img/5b406406c13cf1f498487d9c22b4b247.png)

## 定制纸板十字线

圆形可能不像其他形状那样适合你的虚拟现实应用。或者您可能想要自定义颜色。虽然您不能在这里设置图像十字线，但您可以调整纸板十字线的形状和颜色。

### 制作形状

纸板十字线由许多线段(线)组成，这些线段连接在一起形成一个形状。默认情况下，分段数为 20，这构成了一个圆形。如果您想改变形状，您可以在纸板十字线的“十字线线段”选项中调整线段数。三段组成三角形，四段组成菱形，五段组成五边形，六段组成六边形，依此类推:

![Examples of different reticle shapes](img/6261f447c77d7d42245298ea56ca6143.png)

值得注意的是，在场景运行时，您不能更改分段数，因此您需要停止，更改值，然后再次播放场景以测试每个值。

## 标线生长速度

另一个你可能想要调整的选项是当十字线到达一个它可以相互作用的物体时它增长的速度。这可以在“标线生长速度”选项中完成。这是不言自明的，通过反复试验你会发现不同之处。就我个人而言，我发现默认值 8 已经足够了。如果你走得太慢，体验会变得有点混乱。你想让用户立刻知道他们可以和什么互动。

## 更改十字线颜色

要改变纸板十字线的颜色，我们可以调整十字线的材料。CardboardReticle 有一个着色器，您可以像这样扩展它的选项:

![Our reticle shader options](img/680d0c324f3205cc8943a22dfe3e5294.png)

如你所见，我们可以将十字线的颜色从白色改为任何我们想要的颜色:

![Shader color options](img/8a6666867972801ab25f37f48c4a796d.png)

内径、外径和以米为单位的距离似乎没有任何明显的区别。当场景运行时，它们似乎被重置为其他值。我认为没有必要改变这些，我认为在未来的 Cardboard SDK 版本中可能会增加更多的特性。

## 结论

一旦你理解了 Cardboard SDK 背后的新方法，在 Cardboard SDK 中创建一个定制的十字线/十字线/VR 十字线是非常简单的。我希望很快能探索出用图像和其他元素再次完全定制十字线的方法！

你用新的 Cardboard SDK 构建了什么？你是如何制作十字线的？请在下面的评论中告诉我，或者在 Twitter 上通过 [@thatpatrickguy](https://www.twitter.com/thatpatrickguy) 与我联系。在 [Dev Diner 的 VR with Unity 页面](https://devdiner.com/guides/vr-with-unity)，我还获得了一系列学习 VR 和 Unity 的资源链接。

## 分享这篇文章