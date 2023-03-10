# 使用 Pleeease 进行 CSS 后处理

> 原文：<https://www.sitepoint.com/css-post-processing-pleeease/>

像 Sass、Less 和 Stylus 这样的预处理器已经彻底改变了 CSS 开发。如果你像我一样对预处理器持怀疑态度，尝试在一两个项目中使用 Sass:你不会再回到原始 CSS 了。尽管如此，开发者很少考虑后处理器。请可以改变这种看法…

## 什么是 CSS 后处理器？

在 CSS 文件被手动编码或由预处理器生成后，后处理器对其进行修改*。一个基本的例子是缩小:你的 CSS 文件被解析以删除空白、注释和不必要的尺寸，例如`margin: 0px 0px 0px 0px;`变成了`margin:0;`。结果将保存到一个新的较小文件中，例如 styles.min.css。*

是一个 Node.js 应用程序，它将一系列优秀的后处理器整合到一个方便的命令行工具中，可以在任何平台上工作。

## 命令行？不用了，谢谢！

不要害怕；安装或运行起来并不复杂。也就是说，如果你不能忍受输入一些命令，试试[please online playground](http://pleeease.io/play/)；您不会得到所有的好处，但您可以使用剪切和粘贴来测试系统。

## 请安装

首先，从 nodejs.org 安装 Node.js。你可以下载 Windows、Mac OS 和 Linux 的安装程序，或者使用软件包管理器[。](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager)

接下来，打开命令行/终端窗口，在 Windows 上输入以下命令:

```
npm install -g pleeease
```

或者，在 Mac/Linux 上:

```
sudo npm install -g pleeease
```

注意“pleeease”的独特拼写，它有四个 e，中间有三个。

## 请使用

从命令行，你现在需要使用`cd`命令导航到你的 CSS 文件所在的 web 项目文件夹。假设您在 Windows C:驱动器中有一个名为“myproject\styles”的文件夹，您应该输入:

```
c:
cd \myproject\styles
```

或者，在 Mac/Linux 上，如果您的个人文件夹中有一个“myproject/styles”文件夹，您应该输入:

```
cd ~/myproject/styles
```

通常提示会改变以显示你所在的文件夹。要运行 Pleeease，现在输入:

```
pleeease compile
```

默认情况下，Pleeease 会将所有 CSS 文件合并成一个文件，并在同一个文件夹中创建一个新的 **app.min.css** 文件。在编辑器中打开该文件，看看做了些什么。

## 请问是做什么的？

请在您的 CSS 源代码上运行以下处理器。

**Inline @import** 请将所有文件内联，创建一个单一的样式表，减少 HTTP 请求和带宽。

令人惊奇的[自动前缀修复程序](https://www.npmjs.org/package/autoprefixer)让你永远忘记厂商前缀。您声明必须支持哪些浏览器，autoprefixer 使用来自[caniuse.com](http://caniuse.com/)的信息添加适当的前缀。默认情况下，Pleeease 会为 Firefox ESR、Opera 12.1、每个浏览器的最后两个版本以及那些[市场份额大于 1%](/browser-trends-october-2014-back-work/) 的浏览器添加前缀。

就个人而言，我不会为预处理器前缀生成 mixins 或 autoprefixer 编辑器插件而烦恼；每次运行时，请根据需要添加支持。因此，随着时间的推移，供应商前缀的数量将会减少。

将媒体查询打包成单一规则
我喜欢这个。您可能在整个 CSS 中散布着相同的媒体查询规则。预处理程序鼓励你将 CSS 分割成模块化文件，这加剧了问题。Pleeease 使用 [MQPacker](https://github.com/hail2u/node-css-mqpacker) 在单个媒体查询中应用所有声明。小心这可能会改变 CSS 应用程序的顺序，所以测试是至关重要的。

**rem fallback**
`rem`字体大小单位是相对于根元素的(参见[新 CSS3 相对字体大小单位](/new-css3-relative-font-size/))。不幸的是，该单元在 IE8 和以下版本中失败，所以请基于默认的 16px 字体大小应用像素后退。

**缩小代码**
请使用 [CSSWring](https://github.com/hail2u/node-csswring) 从你的 CSS 中删除每一个不必要的字节。你会很高兴听到它没有遭受在其他系统中发现的[缩小混乱](/avoid-css3-animation-minification-muddles/)。

**杂项变化**
如果这还不够，请问还可以:

*   使用 SVG 回退应用 CSS3 过滤器，如`grayscale`或`blur`
*   将两个冒号的伪元素(如::after、::before 和::first-line)转换为一个冒号，以实现 IE8 兼容性
*   为不透明度添加 IE8 滤镜
*   生成[源地图](https://www.sitepoint.com/using-source-maps-debug-sass-chrome/)

最后，可以启用一些实验特性来转换原生 CSS 变量，应用 CSS4 颜色函数，并在可能的情况下求解`calc()`方程。

## 自定义配置

如果你幸运的话，Pleeease 不需要额外的配置就可以工作——但是你可以通过创建一个**来改变选项。请将**文件放在样式表文件夹中。这包含如下所示的 JSON 代码:

```
{
	"in": ["styles.css", "extra.css"],
	"out": "styles.min.css",
	"import": true,
	"autoprefixer": {"browsers": ["last 2 versions"]},
	"mqpacker": true,
	"minifier": true,
	"rem": ["14px", {"replace": false}],
	"pseudoElements": true,
	"opacity": false,
	"filters": false,
	"sourcemaps": false,
	"next": false
}
```

在这个例子中，我们将依次组合 **styles.css** 和 **extra.css** ，忽略所有其他 css 文件，生成一个名为 **styles.min.css** 的文件。将被内联，将为每个浏览器的最后两个版本添加前缀，合并相同的媒体查询，并缩小结果。我们不太关心 IE8，l，但是我们将替换伪元素双冒号，并提供基于 14px 基本大小的`rem`单元回退。

[请参阅文档](http://pleeease.io/docs/)描述了所有选项。

## 文件观看

如果您不想在每次进行 CSS 更新时都键入`pleeease compile`，请运行以下命令:

```
pleeease watch
```

请将监视所有输入文件，并在发生更改时重新生成输出文件。按 Ctrl/Cmd + C 停止该过程。

## 咕噜声、吞咽声和早午餐整合

如果你已经采用了 [Grunt](http://gruntjs.com/) 、 [Gulp](http://gulpjs.com/) 或 [Brunch](http://brunch.io/) 作为构建工具，你也可以在你的工作流程中使用 Pleeease。诚然，您可以单独设置每个 Pleeease 组件，但这会花费较长的时间，并且不是每个后处理器都适用于所有工具。更多信息，请参考[请工作流程文档](http://pleeease.io/workflow/)。

[Pleeease](http://pleeease.io/) 可能做不到你以前做不到的事情，但它是打包的，所以它易于使用，并且适用于任何项目，无论年龄或技术堆栈如何。推荐。

## 分享这篇文章