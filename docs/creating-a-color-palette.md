# 创建调色板

> 原文：<https://www.sitepoint.com/creating-a-color-palette/>

因此，让我们立即继续我们的色彩系列。上周看了[配色方案，](https://www.sitepoint.com/color-the-scheme-of-things/)现在让我们继续调色板，以及如何创建它们。最后，我们还将研究一些有用的工具，这些工具对于在 web 上使用颜色的人来说几乎是不可或缺的。

“调色板？”你可能会问。“那不是和配色方案一样吗？”嗯，是也不是。一个配色方案只会给你两种、三种或四种颜色。虽然有限的调色板可能很漂亮，但你可能需要更多的颜色来设计你的网站。当你用颜色的语言思考时，最好把这个过程确定下来，而不是在你的布局中需要辅助颜色时随意挑选。你需要的颜色数量取决于你设计的复杂程度。我喜欢在开始时选择至少五六种纯色，然后才考虑将它们应用到我的布局中。

## 十六进制符号

因为这是我们选择每种颜色的阶段，我们需要一种标准的方式来引用调色板中的颜色。你可能已经知道十六进制的 <acronym title="Red, Green, Blue">RGB</acronym> 颜色值，但是如果你不知道，这里有这个理论的快速通过版本。

十六进制计数系统很像你习惯的十进制计数系统，除了它不是基于十的倍数，而是基于十六的倍数，并且有六个额外的数字:A(相当于十进制的 10)、B (11)、C (12)、D (13)、E (14)和 F (15)。表 1，“用十六进制从 1 数到 255”展示了我们如何用十进制和十六进制从 1 数到 255。

那么，这和调色板有什么关系呢？之前，我解释过你的显示器使用加色法 <acronym title="Red, Green, Blue">RGB</acronym> 颜色模型，屏幕上的每个像素都是由红、绿、蓝光组合而成的。我没有提到的是，有 256 种不同级别的红光，256 种级别的绿光，和 256 种级别的蓝光；我们可以用这些来创造 16，777，216 种不同的颜色(是的，超过一千六百七十万种颜色！).

令人欣慰的是，我们有一种快速简单地描述每种颜色的方法——使用十六进制颜色代码。十六进制颜色代码指定了给定颜色的红色、绿色和蓝色级别。例如，将红色、绿色和蓝色以最大可能的程度组合在一起，就形成了白色。为了在网页中使用白色，我们将红色部分设置为 255 ( `FF`十六进制)，绿色部分设置为 255 ( `FF`，蓝色部分设置为 255 ( `FF`)。然后我们按照红、绿、蓝的顺序组合这些十六进制值，得出代码`FFFFFF`。

<colgroup><col width="16%"> <col width="17%"> <col width="17%"> <col width="16%"> <col width="17%"> <col width="17%"></colgroup>
| 小数 | 十六进制的 | 小数 | 十六进制的 | 小数 | 十六进制的 |
| :-: | :-: | :-: | :-: | :-: | :-: |
| Zero | `00` | Sixteen | `10` | Thirty-two | `20` |
| one | `01` | Seventeen | `11` | Thirty-three | `21` |
| Two | `02` | Eighteen | `12` | Thirty-four | `22` |
| three | `03` | Nineteen | `13` | Thirty-five | `23` |
| four | `04` | Twenty | `14` | … |
| five | `05` | Twenty-one | `15` | Two hundred and forty-five | `F5` |
| six | `06` | Twenty-two | `16` | Two hundred and forty-six | `F6` |
| seven | `07` | Twenty-three | `17` | Two hundred and forty-seven | `F7` |
| eight | `08` | Twenty-four | `18` | Two hundred and forty-eight | `F8` |
| nine | `09` | Twenty-five | `19` | Two hundred and forty-nine | `F9` |
| Ten | `0A` | Twenty-six | `1A` | Two hundred and fifty | `FA` |
| Eleven | `0B` | Twenty-seven | `1B` | Two hundred and fifty-one | `FB` |
| Twelve | `0C` | Twenty-eight | `1C` | Two hundred and fifty-two | `FC` |
| Thirteen | `0D` | Twenty-nine | `1D` | Two hundred and fifty-three | `FD` |
| Fourteen | `0E` | Thirty | `1E` | Two hundred and fifty-four | `FE` |
| Fifteen | `0F` | Thirty-one | `1F` | Two hundred and fifty-five | `FF` |

*表 1，“以十六进制从 1 计数到 255”*

通过将红色、绿色和蓝色设置为零(00)而制成的黑色具有代码 000000。我们可以通过将红色设置为 FF 并将绿色和蓝色设置为 00 来创建红色，其代码为 FF0000。图 1，“十六进制颜色示例”显示了几种标准颜色及其十六进制值。在您看到并使用了大量的十六进制颜色之后，您将开始在代码中看到这些颜色。例如，`#F26382`是珊瑚色的粉色，`#371324`是略带紫色的红酒的颜色。一旦你认为你已经达到了绝地妖术大师的地位，尝试一个小游戏“[什么妖术？](http://yizzle.com/whatthehex/)

![Fig. 1, Hexadecimal color examples](img/8504b1e46d7267ceec18fd554096da36.png)
*图 1，“十六进制颜色示例”*

## 颜色工具和资源

现在我们对颜色如何表示为十六进制值有了基本的了解。下一步是找出我们想要处理的每种颜色的值。有许多资源可以帮助你为调色板选择颜色，包括大量适用于 MAC 和 PC 的独立应用程序和插件。以下是我最喜欢的几个:

### 配色方案设计师 3

虽然网上有很多配色师，但我最喜欢的是[配色设计师](http://colorschemedesigner.com/)(原名为 *WellStyled 配色方案生成器*)，如图 2，配色设计师 3——作者的选择”。当许多其他应用程序使用 <acronym title="Red, Green, Blue">RGB</acronym> 或 <acronym title="Cyan, Magenta, Yellow, Black">CMYK</acronym> 色轮时，这个令人敬畏的 <acronym title="HyperText Markup Language">HTML</acronym> 工具使用传统的红色、黄色和蓝色色轮。只需点击几下，您就可以选择和自定义配色方案，以及识别各种其他颜色来构建和谐的调色板。一旦你有了自己喜欢的调色板，你就可以用视觉模拟器来看看那些颜色对不同色盲程度的人来说是什么样子；然后，您可以将颜色导出为 <acronym title="HyperText Markup Language">HTML</acronym> / <acronym title="Cascading Style Sheets">CSS</acronym> 、 <acronym title="eXtensible Markup Language">XML</acronym> 、文本、Photoshop 调色板或 [GIMP](http://www.gimp.org/ "GIMP.org, the GNU Image Manipulation Program") 调色板。

[![Fig. 2, Color Scheme Designer 3—the author’s pick](img/0bad7a0f03386ca4da33176697648062.png)](http://colorschemedesigner.com/#0T31Tw0w0w0w0) 
*图 2、配色设计师 3 —作者精选*

### Adobe Kuler

另一个优秀的选色资源是 [Adobe Kuler](http://kuler.adobe.com/ "kuler.adobe.com") 。在 Kuler 网站上，您可以根据标准配色方案配置创建颜色组合，类似于配色方案设计器 3 的工作方式。不过，与该网站不同的是，Kuler 还会从上传的图像中生成一个调色板。Kuler 的另一个关键特征是它的社区。如果您为该网站创建了一个登录名，您可以保存您的调色板并与其他 Kuler 用户共享，任何人都可以在该网站上浏览最新和最高评级的颜色组合。图 3，“Adobe Kuler AIR 应用程序”展示了[网站](http://www.adobe.com/products/kuler/ "Adobe.com products page: Kuler AIR app")的便捷 <acronym title="Adobe Integrated Runtime">AIR</acronym> 应用程序版本，可以安装在 MAC 和 PC 上。

![Fig. 3, The Adobe Kuler AIR application](img/27ff3aff27e3554012ee94bb336278fd.png)
*图 3，《土坯酷乐 AIR 应用程序》*

### COLOURlovers.com

如果 Kuler 提供的社区过于有限，无法满足你的社交需求，那么图 4 所示的 COLOURlovers 网站“colour lovers——面向颜色爱好者”,肯定会满足你的需求。与其说它是一个颜色生成器工具，不如说它是一个灵感分享网站。它开始只是配色方案，但现在你也可以分享图案和查看颜色(如果你坚持的话，也可以查看颜色)的灵感，用于各种设计领域。

[![Fig. 4, COLOURlovers — for lovers of color](img/8cb208b2229f6336af6761a8d037ab9e.png)](http://www.colourlovers.com/) 
*图 4，《色彩爱好者——献给色彩爱好者》*

### 彩色流 iPhone 应用程序

![Fig. 5, Palettes on the go with Color Stream](img/c137223cd83ec16fee1201b868176098.png) [Color Stream](http://itunes.apple.com/app/color-stream/id327350953?mt=8 "iTunes App Store: Color Steam") 是一款应用程序(iTunes store 售价 2.99 美元)，它将配色方案设计器和 Adobe Kuler 的一些最佳功能放在你的手掌中。由 Sahil Lavingia 创建的 Color Stream 可以让您创建和存储调色板，这些调色板可以从头开始创建，从照片中提取，甚至可以使用内置的颜色方案自动生成。这真的是一个优雅的，精心打磨的应用程序，使挑选调色板变得有趣和容易。图 5，右边的“色彩流中的调色板”显示了在应用程序中从照片中选择调色板的样子。

### 生动的

Kuler 和 COLOURLovers 是很好的工具，可以精心调整和获得关于你正在使用的配色方案的社会反馈，但是如果你在旅途中看到一些颜色灵感呢？这就是[pictaculaous](http://pictaculous.com/)派上用场的地方。Pictaculous 是一个免费的 [MailChimp Labs](http://mailchimp.com/labs/) 项目，它通过电子邮件提供基于你的图片的配色方案。你只需用智能手机拍张照片，然后用电子邮件发给 colors@mailchimp.com。几分钟之内，您将收到一封电子邮件，其中包含一个五色调色板、Kuler 和 COLOURLovers 推荐的各种配色方案，以及一个附件 Adobe 调色板(。aco)文件。不过，除了通过电子邮件，还有其他选择。图 6，“Pictaculous Color”显示了 Pictaculous 对我从我的计算机上传到该服务的图片的颜色建议。

![Fig. 6, Pictaculous Color](img/f5aec6627b24fef65f450ee091342264.png)
*图 6，《绘声绘色》*

### 颜色对比检查

当你为调色板选择颜色时，最好选择至少两种有足够对比度的颜色作为背景色和文本色。在文本和背景颜色之间有一个适当的对比对于交互设计是必不可少的；没有它，有些人可能无法阅读您的网站。确认两种颜色之间有足够对比度的一个简单方法是将前景和背景的 RGB 值插入 Jonathan Snook 的[颜色对比度检查网站](http://snook.ca/technical/colour_contrast/colour.html)。

有时，您认为有效的组合无法满足网页的色差和亮度要求。正如 Jonathan 在他的关于对比度检查器的博客文章中所说，“…这个工具不应该被当作真理…而是应该帮助你选择更好的颜色。”

能够想出一个独特的调色板是所有关于保持你的眼睛睁开。如果你看到一个网站、广告、插图或其他引人注目的图形，试着找出主色调是什么，调色板下面是什么类型的配色方案。请记住，色彩的灵感可以来自任何地方。有没有一种颜色让你想起某首歌？你最喜欢的食物的颜色怎么样？也许你父母家那张俗气的 70 年代壁纸中有一种颜色很适合你。意识到与颜色使用相关的各种问题会让你对颜色有更好的眼光，并有能力提出满足客户要求的原创调色板。

## 美丽网页设计的原则

这篇文章来自杰森·比厄德的 [*美丽网页设计的原则*一书](https://www.sitepoint.com/premium/library "SitePoint.com: The Principles of Beautiful Web Design, 2nd Edition")(第二版已经出来了！).这本《看调色板》总结了色彩方面的章节，但请务必留意本书中关于设计节的更多文章。

## 分享这篇文章