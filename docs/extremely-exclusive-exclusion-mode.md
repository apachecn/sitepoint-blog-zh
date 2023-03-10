# 极度排他排斥模式

> 原文：<https://www.sitepoint.com/extremely-exclusive-exclusion-mode/>

这是我从未用过的一种模式。我可能不是最好的人来张贴一些东西，但因为我涵盖了所有其他混合模式，我不能完全跳过它。所以我去了 Photoshop 的帮助，因为它非常描述了不同的模式，并发现…

> 排除模式:创建与差异模式相似但对比度较低的效果。与白色混合会反转基色值。与黑色混合不会产生任何变化。

"类似但对比较低."是的，这是对它如何工作的最好解释。

我想出了一个例子来展示这两种模式是多么的“相似”。我有两个蓝色的矩形，上面覆盖了另外两层白色/紫色/黑色渐变。一个渐变设置为排除模式，而另一个设置为差异模式。重叠部分是混合的“结果”。

![](img/1e0bd3c94b961568bc70708f99b31fe2.png)

惊人的相似，不是吗？抱歉，我是不是在你的键盘上说了些讽刺的话？据我所知，在差异模式或排除模式下混合纯白和纯黑会产生相同的结果——但中间值截然不同。

为了让我们更加困惑，我制作了另一个示例图形:

![](img/17c9d2bd2f795e694e62c53060ee8c24.png)

同样，纯白和纯黑通过某种“反转”颜色(从白色中减去颜色值，即 255-255-255)与它们在差异模式下的行为相同。中间值也有点类似，带有与混合色相关的轻微色调。

所以。这给我们留下了什么？老实说，我可以依靠的唯一一个实际应用的例子就是我的上一篇博客中的那个，使用纯白和黑色的形状来创建一个“剪切”效果。虽然排除模式的实验确实产生了一些有趣、怪异的数字图像效果，但似乎没有什么特别有用的东西可以传递。

如果我让你失望了，我很抱歉——这种模式对我来说有点太排外了。所以一如既往，我欢迎你张贴你自己的实验或链接到排除模式的酷用法的例子！

([下载样本 psd 文件](https://examples.pixelmill.net/sitepointblog/040921/040921.psd))

## 分享这篇文章