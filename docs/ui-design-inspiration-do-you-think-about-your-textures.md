# UI 设计灵感:你考虑过你的纹理吗？

> 原文：<https://www.sitepoint.com/ui-design-inspiration-do-you-think-about-your-textures/>

“平面设计”成为默认的主导设计风格已经有十年多了，这是有充分理由的。在经历了“web 2.0”的所有光滑表面和阴影之后，我们都需要在新的响应布局中保持清晰易读的设计元素。

然而，有一个危险是，所有这些平淡的颜色设计可能会开始看起来有点“千篇一律”。但是，有时混合一些额外的纹理可以给你的 UI 设计带来它所需要的提升，而不是添加新的颜色或字体。让我们来看看一些杀手级的例子。

### 添加纸张纹理

2019 年，谷歌发布了一个名为“[AI 的 A-Z](https://atozofai.withgoogle.com/intl/en-GB/)”的酷边项目，旨在解释人工智能的基本概念。风格是现代和友好的，使用广泛的颜色面板，简单的儿童书籍插图，和大的，富有表现力的衬线字体。

但是仔细观察平坦的颜色区域，你会注意到纸一样的颗粒感。我放大了下图中的一小块区域，但你可能需要[检查实物](https://atozofai.withgoogle.com/intl/en-GB/)才能完全欣赏这种纹理。它是如此微妙，我怀疑大多数用户没有意识到它，但我认为它完美地支持了不复杂的儿童书籍的样式。

 [![The designers of Google's A-Z of AI project gave an essentially flat design a warm, tactile twist](img/9f04d87a01ae1de85b5ce351db404676.png)](http://https://atozofai.withgoogle.com/intl/en-GB)

The designers of Google’s A-Z of AI project gave this essentially flat design a warm, tactile twist

### 那么，他们是怎么得到这种效果的呢？

我必须承认，我花了一些时间梳理所有大型平板区域的`background-image` CSS 属性来寻找平铺图形——但是没有发现*任何东西*。令人费解。

直到第二天，我才得到一点启示。如果他们不是在每个面板上添加纹理，而是创建一个几乎看不见的“粒状透镜层”覆盖整个网站，会怎么样？你可以把它想象成 Snapchat 的颗粒感滤镜。

答对了。我找错了地方。

![This is the 'noise' graphic they use to get the grain texture (noise.jpg)](img/0df7cf7a51c3d60adde22c5a20b741dc.png)

如果您检查 HTML，您会发现一个带有附加 CSS 的`:after`伪元素:

```
 ._3rV4LQ0BePEq9V1dxEjhEF::after {
    background: url(/static/noise.jpg);
    content: "";
    height: 100%;
    left: 0;
    opacity: .05;
    pointer-events: none;
    position: absolute;
    top: 0;
    width: 100%;
    z-index: 201;
} 
```

![An :after pseudo element is attached to one of the main app containers](img/323ef675f434f381fe2a0d92fb05763f.png)

An :after pseudo element creates an almost transparent texture layer.

这一层使用颗粒图像(`noise.jpg`)作为平铺背景，他们已经将其定位为覆盖整个屏幕(`width:100%`、`height: 100%`、`top:0`、`left:0`和`z-index:201`)。

正如我们所怀疑的，不透明度被设置为几乎透明(`opacity: .05`)，这样剩下的只是一点不均匀的纹理。用这个“镜头层”(即使它是透明的)覆盖屏幕的唯一潜在问题是，它将阻止光标访问它下面的所有链接、输入和其他用户交互。

令人高兴的是，这很容易通过添加`pointer-events: none`来解决，它使这个镜头层对用户的光标不可见。

我认为这是一个真正有用的技术。它使用不超过一个微小的图形和十几行 CSS 来提供站点范围的视觉冲击。这对巴克来说是个好消息。

### 颜料、铅笔和其他传统媒体

计算机是为了精确和干净而制造的，与大多数传统媒体不同，数字色彩不会意外地流动、涂抹、出血或涂抹。这有助于保持你的桌子整洁，但也意味着真实、松散、有机、自然的媒介，如颜料和铅笔，当你能找到合适的设置时，它们会脱颖而出。

 [![The More of Less campaign for AirBNB China brings Chinese water-color into the digital world](img/d3e147cb2d50d0e8a6c757cb14131629.png)](https://fnlprjct.com/V2/project/airbnb-kv-animation-resn/)

The More of Less campaign for AirBNB China brings Chinese water-color into the digital world.

虽然上面显示的应用程序已经过期，但新西兰机构 [Resn](https://www.resn.co.nz/) 最近给了我们一个完美的例子，真实地复制了中国传统的水彩画风格，以促进 AirBNB 在中国农村的体验。

这种水彩风格浸透了应用程序的每一个像素，从移动动画序列到全屏背景，使它成为一项巨大的事业。

### 褪色的记忆

当 Obys 经纪公司向一级方程式赛车王牌尼基·劳达致敬时，他们的设计方法主要依靠尼基职业生涯中的大量精彩档案照片和镜头。

[![Screenshot of the Obys tribute to Niki Lauda](img/a4275bebabaabc954fe3cb6e0e95efba.png)](https://nikilauda.obys.agency/prologue/)

The Obys tribute to Niki Lauda.

粗糙的红褐色照片给这个网站一种温暖的，略带渴望的感觉。人们很容易过分追求复古主题，或许模仿一本旧书或旧报纸。令人高兴的是，Obys 能够在一种动态网络布局中展示档案图像，这在传统书籍中是不可能的，从而避免了这种陈词滥调。

### 数字复古外观

![Retro inspiration: Tron (1982), WarGames (1983) and Blade Runner(1982)](img/5fb474c1365d8a734dc5a650fd1690a6.png)

Retro inspiration: Tron (1982), WarGames (1983) and Blade Runner(1982).

当然，“复古”从来都不是一个特定的时刻，而是一种观点，正如 [Hypebeast](https://hypebeast.com/2019/8/ozweego) 以他们的超级 80 年代复古主题展示的那样。他们旋转的漩涡、双色线条和发光的扫描线让人想起了[创光循环](https://tron.fandom.com/wiki/Light_Cycle_5th_generation)、[战争游戏](https://en.wikipedia.org/wiki/WarGames)指挥中心和[银翼杀手](https://en.wikipedia.org/wiki/Blade_Runner)。

 [![Hypebeast adds a big spoonful of 1982 to their mix of music and streetwear.](img/f6b12533f6f886b3da96b8eeb9e2db03.png)](https://hypebeast.com/2019/8/ozweego)

Hypebeast adds a big spoonful of 1982 to their mix of music and streetwear.

尽管 Hypebeast 使用 HTML5 canvas 来渲染他们的动画，但这种“闪光的电视扫描线效果”非常适合我们在本章前面剖析的镜头层技术。

### 半色调和本日点

如果你曾经眯着眼睛仔细看过一本旧的漫画书、报纸或杂志，你可能会注意到圆点的图案创造了图像的基调。

从技术上讲，印刷中有两种类型的点图案。半色调网屏使用一个由微小透镜组成的塑料网格，将黑点烧到它们下面的摄影胶片上。更多的光产生更大的点。

下图的第一幅画展示了一幅 1964 年安迪·沃霍尔壁画的半色调图片。

![Halftone and Ben Day dots in Warhol's pop art, newspaper ads and comic books](img/62a4b6fa5edce0f6b9a7237029eab999.png)

Halftone and Ben Day dots in Warhol’s pop art, newspaper ads and comic books.

**[本日圆点](https://en.wikipedia.org/wiki/Ben-Day_dots)** 略有不同。漫画书——就像上面显示的超人例子——通常采用黑色线条艺术，并放入扁平、大小均匀的本日圆点区域，以模拟额外的墨水颜色。白纸上的小红点网格是获得粉色的一种廉价方式。

虽然这些技术看起来脏兮兮的，有点点的样子开始是印刷过程中不可避免的副产品，但最终它凭借自己的权利成为了一种艺术声明——并且一直沿用到今天。

岛根县的日本小村庄 [Misato 给了我们最近最喜欢的这种技术的例子。他们的网站展示了一幅带插图的地图，让你可以虚拟游览这个村庄。插图很有趣，大部分是平面颜色，但有一些精彩的半色调图片，增加了深度和质感。](https://www.town.shimane-misato.lg.jp/misatoto/)

 [![Misato, Shimane, uses chunky halftones to give flat color illustrations depth and bite](img/c3743343d2617b70b3144383fe9f0614.png)](https://www.town.shimane-misato.lg.jp/misatoto/)

Misato, Shimane, uses chunky halftones to give flat color illustrations depth and bite.

### DIY 半色调

有几十种 Photoshop 滤镜可以模拟图像的半色调处理。它们…很好，但是如果我们要通过将照片转换为半色调来清除图像细节，那么将它转换为矢量图形是有意义的——理想情况下是 SVG。为此，我可以愉快地推荐 [HalftonePro](https://halftonepro.com/app) 。

[![HalftonePro makes it relatively easy to convert any bitmap into an SVG](img/e49aeed9f5874b444c3f2aea6760ca4b.png)](https://halftonepro.com/)

HalftonePro makes it relatively easy to convert any bitmap into an SVG.

*HalftonePro* 允许您上传任何位图并应用大量半色调设置，包括:

*   网格比例尺
*   网格图案(圆形、正方形、三角形等。)
*   圆点形状(圆形、正方形、三角形等。)
*   对比
*   彩色输出

我制作良好的 SVG 半色调的快速技巧是:

1.  不是所有的图片都适合这种风格。大胆、高对比度的图像通常更成功。
2.  如果选择非常精细的网格，文件大小会变得很大并且难以控制。拥抱艰难。
3.  首先使用预设来帮助您了解什么是有效的。
4.  使用类似于 Jake Archibald 的 SVG 优化器的东西来压缩你的文件。

![Four different halftone treatments applied to The Velvet Underground's Andy Warhol](img/9556216c033fe7c468fbb52afb11301e.png)

Four different halftone treatments applied to The Velvet Underground’s Andy Warhol cover.

请记住，HalftonePro 不是免费的。目前的费用是 15 美元，但请注意，这是*为*终身会员*支付的一次性*——在一个充满月度或年度订阅计划的世界里。我买了它，仍然认为它物有所值。

### 隐藏的纹理

也许我最近见过的最隐秘的纹理应用是在哈佛电影资料馆展出的。打开网站，你可能会想，“什么质地？这个网站是平面颜色，因为他们来了。”

[![Harvard Film Archive applies an animated film grain to images](img/838ef729553cad4f5d113f66059146a2.png)](https://harvardfilmarchive.org/)

Harvard Film Archive applies an animated film grain to images.

然而，密切关注这些图像，你会开始注意到那里有一种嗡嗡的能量——有点像电影的闪烁。前端设计师在所有图像的表面添加了微妙的动画胶片颗粒。(不过，请注意，我手机上的移动视图没有这种效果。)我怀疑很少有用户会有意识地注意到它，但我认为它肯定让这些静态照片 ***感觉*** 像电影剧照。

### 他们是怎么做到的？

![Close-up of the film grain effect.](img/bb94f3ae8999084a0fe264edb7523eea.png)

Close-up of the film grain effect.

对于那些对技术细节感兴趣的人，开发人员使用 JavaScript 在每张图片上创建一个 [HTML5 canvas](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/canvas) 层。每个画布播放一个几乎透明的随机噪声动画。虽然很巧妙，但这个想法的复杂性只会让我更加钦佩早期谷歌 AI 纸张纹理的简单性。

### 开始你自己的质地趋势

正如我们在上面看到的，纹理对人们如何看待你的设计有很大的影响。站在当前网页设计趋势的顶端对于创造有效的当代设计是必不可少的，但是了解过去发生在互联网历史之外的模式将有助于你建立自己的风格和原创设计。

最终，你的客户试图建立的形象，以及他们设定的沟通目标，应该是你应用多少和什么类型纹理的决定因素。

摘自“[《美丽网页设计的原则》，第四版](https://www.sitepoint.com/premium/books/the-principles-of-beautiful-web-design-4th-edition/)”。

## 分享这篇文章