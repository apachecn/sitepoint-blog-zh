# Flash 脚本-火焰效果文章

> 原文：<https://www.sitepoint.com/flash-script-fire-effect/>

 **<object classid="clsid:d27cdb6e-ae6d-11cf-96b8-444553540000" width="100" height="100" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,40,0"><param name="src" value="https://i2.sitepoint.com/flash/fireeffect.swf"><embed type="application/x-shockwave-flash" width="100" height="100" src="https://i2.sitepoint.com/flash/fireeffect.swf"></object>**

怎样才能创造出很棒的火效果？像这样！

**1。**首先创建一个径向渐变圆形对象，如下图。

![1130_fire1](img/bc9711e6691230508acb63d6b1ccf455.png)

**2。**将此转化为图形对象，命名为‘火焰’。

**3。**再次将该图形对象转换为电影剪辑。将此影片剪辑的实例名命名为“fl”。

**4。**现在，进入电影片段 fl 的时间轴。缩小火焰图形对象的大小，如下图所示。

![1130_fire2](img/c888b183034d428a071fd51c237f2d81.png)

**5。**延长 4 帧，在第四帧放置一个关键帧。

**6。**现在，如下图缩放火焰物体。在“效果”调色板中，我将色调改为黄色，得到了这个:

![1130_fire3](img/256518780f47689d7254ca4af0ce4b7a.png)

**7。**从第四个关键帧开始，再延长 24 帧，在该点放置一个关键帧。

**8。在这一帧中，我将火焰物体垂直向上移动了 150 像素。**

我还将火焰对象缩小到很小的尺寸，将颜色改为红色，并将 alpha 降低到 23%。我还对帧进行了运动补间。

**9。**现在，回到主时间线。延长 3 帧。

10。在当前图层上新建一个图层，命名为 actions。创建三个空白关键帧。

**11。**在第一个关键帧中，插入动作:

`i = 0;
setProperty ("fl", _visible, 0);
xx = getProperty("fl",_x);
19.In the second key frame give action:
if(i < 35)
{
duplicateMovieClip ("/fl", "fl" add i, i);
setProperty ( "fl" add i, _alpha,i*8);
setProperty ( "fl" add i, _x,random(3) + xx);
i = i + 1;
}`

**12。**在第三个关键帧中，插入这个动作:

`gotoAndPlay (2);`

你完了！测试电影。

## 分享这篇文章