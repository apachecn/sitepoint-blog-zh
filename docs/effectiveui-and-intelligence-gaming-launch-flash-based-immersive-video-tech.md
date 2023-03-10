# 高效智能游戏推出基于 Flash 的沉浸式视频技术

> 原文：<https://www.sitepoint.com/effectiveui-and-intelligence-gaming-launch-flash-based-immersive-video-tech/>

![](img/b5ab0f8024a136e226860b410648131c.png "flash-logo")正如我们所写的，今天[在](https://www.sitepoint.com/blog/)推出的新 Flash 10 播放器最酷的功能之一是 Pixel Bender 图像处理技术。Pixel Bender 的巧妙之处在于，尽管它是 Adobe 行业标准视频后期制作软件 After Effects 背后的技术，但 Pixel Bender 不仅仅是将视觉效果应用于图像。

Adobe 高级产品经理 Justin Everett-Church 告诉我，Pixel Bender 本质上只是一个数学计算速度非常非常快的引擎。因为它支持多核 CPU(而 ActionScript 不支持)，并且是异步的，在它们自己的线程中运行进程，所以当您进行大量计算时，它不会降低您的应用程序的速度。

很大程度上是因为 Flash 的这一新功能，丹佛的 EffectiveUI 和 [Intelligence Gaming](http://www.intelligencegaming.com/) 选择 Flash 10 来支持他们新的“RealityV”沉浸式视频模拟平台。

RealityV 是一种视频故事讲述技术，它将用户置于 360 度的 3D 环境中，包括双耳声音。这两家公司在这项技术上进行合作，正在使用 Pixel Bender 根据用户输入实时混合 8 个通道的 360 度音频。这在 Flash 9 中是做不到的(或者说，可以用 ActionScript 以编程方式实现，但不能实时实现)。因为 Pixel Bender 在单独的线程上进行高级数学计算，所以在渲染音频时不会降低应用程序的速度。

![](img/91e92c6a3d9b3e5a25e27f19a5a36c5c.png "realityv")

使用现成的视频耳机，RealityV 能够提供身临其境的体验。例如，如果用户转头，音频和视频就会相应地移动，并实时变化(这就是 Pixel Bender 允许他们做的事情)。

![](img/934bd987947db0213d9e467e7e4038c5.png "realityv")

Intelligence Gaming 正在向军方推销 RealityV 平台，以创建文化模拟，用于训练士兵如何在可能存在文化障碍的外国土地上适当化解紧张局势。在未来，智能游戏希望将 RealityV 推向其他培训应用，如医学、执法、第一反应或任何需要在压力下快速决策才能成功的任务。“RealityV 开发框架也是一种理想的技术，可以支持旅游业提供虚拟旅游，娱乐领域提供沉浸式电影短片，以及行为科学评估人类对环境场景的生理反应，”该公司在一份新闻稿中说。

我们迫不及待地想看到《选择你自己的冒险小说》最终被搬上银幕！

## 分享这篇文章