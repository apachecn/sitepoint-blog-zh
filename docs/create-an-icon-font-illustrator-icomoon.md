# 使用 Illustrator & IcoMoon 创建图标字体

> 原文：<https://www.sitepoint.com/create-an-icon-font-illustrator-icomoon/>

[![Hieroglyphs -- 'old school' iconography](img/12ce24857d32a15c5fe5d3ccb53b39c5.png)](https://www.flickr.com/photos/charlestilford/3092699714/)

照片:listentoreason

图标系统通常会成为 web 项目中的核心设计元素。你可以在网络上找到许多免费或商业图标集，它们经常被包含在主要的 CSS 框架中([Bootstrap 3 glyphicon](http://getbootstrap.com/components/#glyphicons)就是一个完美的例子)。

然而，在某些场合，你可能需要更具体的 *[字形](http://en.wikipedia.org/wiki/Glyph)* 不包括在一个通用集中。或者你可能只是想更好地协调你的图标设计与项目的其他设计元素。在这种情况下，你需要从头开始绘制一个新的图标集。

有两种广泛使用的方法可以将一组图标添加到项目中:

1.  Web 字体
2.  挽救（saving 的简写）

今天我将讨论网络字体。我们将在另一篇文章中讨论 SVG 的使用。

传统上，字体创建被认为是一项困难而艰巨的任务，但幸运的是，由于有了简单的在线工具，如 [IcoMoon](https://icomoon.io/) 、 [Fontastic](http://fontastic.me) 或 [Fontello](http://fontello.com/) ，构建**图标字体集**已经变得相当容易。每一个都允许你通过从公共库中“精选”预建的图标，或者通过上传和编译你自己准备的 SVG 文件来构建字体。

## BYO 图标

要构建自己的图标字体，第一步是设计图标——我们将使用 Illustrator 来完成这项任务——然后将它们保存为 SVG。

### 找到你的设计风格

在开始绘制之前，您需要为字形定义样式属性，如笔画大小、网格比例等。

首先在不同的尺寸下做一些可读性测试，然后建立一个你将用来绘制所有字形的模板。设计在低分辨率下无法工作的精细图标是没有用的。

在画之前画出你的字体草图也是非常有用的，一个非常粗略的草图(如下图)会帮助你找到你需要的所有符号的想法。

![A very very rough sketch](img/dea4a0e05e13410763c0391cb0fb2ab0.png)

然后你可以决定你的字形的网格大小。我的建议是**不要**直接按照你的目标尺寸(例如 16x16px)或者非常大的尺寸来画。相反，我发现 60-80px 的网格通常对我有好处。从这个大小开始放大或缩小通常是成功的。

当然，这不是一个“一成不变”的规则:项目需求和经验可能会根据具体情况而变化。

在这篇文章中，我选择用 8px 的笔画来绘制图标，没有填充，并设置在 64x64px 的网格内。

## 开始绘图

Illustrator 画板对这项工作非常有用:您可以在不同的画板中绘制每个字形，将每个字形保存在一个文件中。画板可以重新排列、重命名，并可以导出为独立的 SVG 文件(这正是我们需要的 IcoMoon)。

每个画板也可以有独立的轴坐标，这对于对象对齐非常有价值，我们将在后面看到。

我们现在可以开始创建一个新的 64x64px Illustrator 文档:

![Illustrator New document panel](img/16d20be56136a29c47187e9efd586d48.png)

请注意，您可以使用**数量的画板**选项开始创建您需要的所有画板:我更喜欢在需要时添加画板，但是没有理由为什么您不能首先创建您的所有画板。

![The new document with the first artboard](img/302c95c426155b87233696893492089d.png)

现在我们可以设置网格了。我们希望它从每个画板原点(左上角)开始，并将画板分成一个 8×8 的棋盘。在此之前，让我们快速了解一下 Illustrator 坐标系的工作方式。

### Illustrator 工作空间

Illustrator 工作区由一个包含一个或多个**画板**的**画布**组成。

画板从称为**全局标尺原点**的点开始放置在画布上。此外，每个画板都有自己的*原点和标尺。*

当您使用**画板工具**定位新画板时，您将始终使用全局标尺。在几乎所有其他情况下，您将使用画板标尺根据**视图** → **标尺** → **更改为画板*(或全局)*标尺**选项。

如果您使用全局标尺，您在任何画板上绘制的每个对象都是相对于该单一原点(即*全局标尺原点*)绘制的。否则，坐标指的是包含它的画板的原点。

简而言之，使用画板标尺系统:

*   画板根据*全局标尺原点*放置在画布中
*   我们绘制的对象按照*画板标尺原点*放置在一个画板中(在下面的截图中，可以看到两个方块即使放置在两个不同的画板中，坐标值也是相同的)。

![Illustrator workspace](img/845f44c99c7b6e8012b108b181e56e71.png)

对于这项工作，我们将使用画板标尺，因为它更适合字形对齐。

### 设置网格

Illustrator 的网格从全局标尺原点开始放置在工作空间中。您可以选择**首选项** → **参考线&网格**来设置其属性。由于我们希望我们的 64x64px 画布每边被分成 8 个部分，我们将每隔 16px 设置一个主网格线(这将导致我们的画板被分成 4×4 个部分),包含两个子部分:

![Guides & Grid preferences panel](img/c5a1507f0f646ec572de72f831dd1531.png)

现在，我们必须确保网格与画板完全匹配。默认情况下，第一个画板放置在全局标尺原点，但是，由于画板可以移动和重置:要执行此操作，只需使用画板工具双击标尺原点。

我们的网格现在应该与我们的画板完全对齐:

![Workspace with grid](img/556d221df342c50d6a1a14ad08b15c03.png)

### 第一个字形

让我们开始我们的第一个字形，一个简单的*圆圈内的复选标记*:

![Check sign](img/db36bcfb3573551e13f531ecd85f81a0.png)

要画圆，我们必须选择**椭圆工具**，点击画板内的一个点，输入椭圆的宽度和高度:

![Ellipse tool](img/02f10764fc6770f208a62093ae407de0.png)

接下来，我们必须删除任何填充，并设置一个 8px 大小的黑色笔画。此时，我们可以将圆与画板对齐(确保选择了**对齐画板**选项):

![Align panel](img/2cc895f5b7696fde53f2ac3ff092a458.png)

但是结果并不完全符合我们的预期:Illustrator 在没有计算笔画宽度的情况下对齐了*路径*。幸运的是，这很容易解决，在首选项面板中设置**使用预览边界**选项。现在，Illustrator 将在画板内计算笔画宽度，而不考虑对齐和调整大小，这使得绘制更快、更准确。

![Preview Bounds example](img/07d97dbcf1b471a8673dfc8861b7f0cc.png)

### 完成字形绘制

我们现在必须绘制每个字形，每次添加一个新的画板。您可以使用**画板工具**和 option/alt 拖动现有画板或单击**画板面板**中的**新画板**图标来添加画板。

然后，可以按照画板面板中的顺序选择**对象** → **画板** → **重新排列**(或“画板”面板菜单中的**重新排列画板**)来轻松地重新排列画板。

![Rearrange artboards panel](img/cd66a33b8571972096ac557f691c5ca1.png)

请注意，字形总是按字母顺序上传，因此如果您希望 Illustrator 中的画板顺序与字体中的字形顺序相匹配，请记住这一点。

给每个画板一个名称(没有空格):这将成为字形的名称，也是生成的 CSS 的类名，所以为它们使用有意义的单词非常重要。

![The completed icons file](img/5e421949459e4ff0aad9b5d95712a8f3.png)

虽然我们经常使用笔画来绘制符号，但字体创建有一些特殊的需求。我们必须记住:

*   每个笔画都必须转换为填充
*   如果需要，必须组合填充创建复合路径(没有重叠的形状)
*   任何颜色都将被忽略:字形将始终被解释为黑色。你也不能用白色做背景:你只能用透明的
*   SVG 中的任何嵌入图像都将被忽略

您可以使用**扩展**命令将路径转换为填充的形状，并使用**路径查找器**面板将重叠的形状快速合并为单个元素。

![Strokes to paths process](img/0a41e5bc844d42f47e13be80903c8232.png)

一旦我们的字形准备好了，我们可以通过使用**另存为**对话框中的**使用画板**选项将每个字形保存为一个不同的 SVG 文件。

![Exported SVG files](img/de2a213a9c87c8e90ae681b00ae82328.png)

Illustrator 保存 SVG 文件的方式是在文件名前加上`.ai`文档名(`icons_`)。由于 IcoMoon 使用文件名作为字形，我更喜欢去掉这个前缀(有很多小应用程序可以轻松地重命名所有文件)以减少任何混淆。

## 将文件上传到 IcoMoon

IcoMoon 网站提供了一个在线应用程序来构建图标字体。该应用程序以一个未命名的项目*开始，可以访问一些免费的图标库，你可以从中选择符号。*

由于我们将上传自己的字形，我们可以使用每个字形右侧的小菜单删除所有预加载的库(注意，这不是强制性的，因为只有选定的符号会添加到您的字体中)。它只是保持你的项目干净。

然后，您可以点击应用菜单栏右侧的**管理项目**图标来保存您的项目。请注意，使用 IcoMoon 免费帐户，您的项目将存储在本地浏览器中，因此，您将无法在其他地方访问它，直到您下载项目数据(JSON 文件)或升级到高级帐户(允许您将项目存储在云中)。

![Icomoon app](img/07c8a79ed6bb8eb4204482fac56160b8.png)

现在，你可以使用**导入图标**按钮上传你的 SVG 文件:你的字形将作为**集**出现在应用程序中。在这个阶段，使用应用程序提供的工具可以很容易地重新排列、删除它们或编辑每个字形。您还可以编辑 set 元数据(set 的名称和一些作者信息)或使用 set 右侧的小菜单执行其他任务。

可以使用编辑工具在应用程序中编辑图标。您不能以这种方式改变矢量数据，但是可以调整字形大小或移动字形，下载 SVG 文件或替换它。

![The edit symbol panel](img/9025cd8b9adde95813b44718f7ed8d52.png)

为了构建你的字体，你首先需要使用选择工具选择所有需要的图标(或者如果合适的话，只需要使用*设置*菜单中的**全选/无**命令)。您将能够通过黄色边框来识别选定的字形。然后你必须点击页面底部的**字体**按钮。

![IcoMoon app with all glyphs selected](img/ef7b88613148146aa65e9f9cce1acf33.png)

该应用程序将加载一个包含所有选定符号的二级窗口，显示每个符号的字形、名称和 IcoMoon 在 [PUA](http://en.wikipedia.org/wiki/Private_Use_Areas "Private Use Area") 范围内分配给它的 [Unicode 点](http://en.wikipedia.org/wiki/Code_point)。使用 PUA 范围被认为是托管你的图标的最安全的 unicode 扇区，尽管 IcoMoon 允许你使用标准的**基本拉丁语**范围，如果你喜欢，通过点击顶部栏中的**代码**按钮。

您也可以通过手动插入新代码或字符，为每个字符指定不同的范围代码:

![The glyphs list](img/228ec713a89d6b748ed84a9b1eca53a3.png)

在下载你的字体之前，添加一个**字体名称**(这将在`@font-face`规则中使用)，以及**类前缀**和/或**后缀**(这将在生成的 CSS 中用作选择器)。

还有许多其他参数可以设置:从**元数据信息**到**度量值**，我建议仔细阅读简短且解释清楚的[文档](https://icomoon.io/#docs)来正确设置它们。

![The font preferences panel](img/89eda5bffb6d4099e4f136ad679ea919.png)

你的图标设置完成了！下载您的字体(或者，如果您有高级帐户，只需链接到 CSS)并将其添加到您的项目中。

下载的`.zip`文件包含各种格式的字体，一个可以用作参考的演示文件，一个可以重新上传以恢复项目的`.json`文件(例如，在另一台电脑上查看)，当然，还有一个编码图标的`.css`文件。

![The font folder](img/1314be5c5eaa11602080c479d7b793c4.png)

CSS(你可以在下面看到一个例子)包含了`@font-face`规则和所有的符号选择器。

```
@font-face {
    font-family: 'icomoon';
    src:url('fonts/icomoon.eot?-88cxph');
    src:url('fonts/icomoon.eot?#iefix-88cxph') format('embedded-opentype'),
        url('fonts/icomoon.woff?-88cxph') format('woff'),
        url('fonts/icomoon.ttf?-88cxph') format('truetype'),
        url('fonts/icomoon.svg?-88cxph#icomoon') format('svg');
    font-weight: normal;
    font-style: normal;
}

[class^="myicon-"], [class*=" myicon-"] {
    font-family: 'icomoon';
    speak: none;
    font-style: normal;
    font-weight: normal;
    font-variant: normal;
    text-transform: none;
    line-height: 1;

    /* Better Font Rendering =========== */
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
}

.myicon-alert:before {
    content: "\e600";
}
.myicon-arrow-down:before {
    content: "\61";
}

/* etc */
```

### 试一试！

我发现这是构建图标字体的一种非常快速的方法，但是找到完美的工作流程并测试 Illustrator 和 IcoMoon 提供的所有选项肯定需要时间。

玩得开心

## 分享这篇文章