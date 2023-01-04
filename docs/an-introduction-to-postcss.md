# PostCSS 简介

> 原文：<https://www.sitepoint.com/an-introduction-to-postcss/>

CSS 预处理程序很受欢迎，但是它们有一些缺点。在这篇 PostCSS 介绍中，我们将探索 PostCSS 的优势，它是如何工作的，以及它广泛的插件可以实现什么。

![Processing CSS with PostCSS](img/5271bdf7d2f3a36eed2d6f1664dad0c0.png)

## 预处理器的价值和局限性

大多数 CSS 开发人员都熟悉预处理程序。包括 Sass、Less 和 Stylus 在内的工具引入了文件部分、嵌套、变量和混合等概念。一些特性逐渐出现在原生 CSS 中，但是预处理器对于管理大型代码库以及保持样式和编码的一致性仍然很有用。

可能很难想象没有 CSS 预处理器的生活，但是也有不好的一面:

*   **预处理器不可扩展或受限**。大多数预处理器都是一个黑盒，为您提供一组特定的支持特性。也许可以编写自定义函数，但是超出该工具范围的功能仍然是不可能的——例如将 SVG 内联为背景图像。

    类似地，你不能阻止开发者使用你更想避免的选项，比如`@extend`或者深度嵌套。林挺可以帮助，但它不会停止预处理器编译一个有效的文件。

*   **预处理器提供自己的语法**。预处理器代码可能类似于 CSS，但是没有浏览器能够本地解析该文件。语法是不同的，如果您的工具发生变化或不可用，您的代码将需要更新以使其可用。

好处远远超过这些风险，但是有一个替代方案…

## 什么是 PostCSS？

PostCSS 是[不是一个预处理器](https://github.com/postcss/postcss)(虽然它可以表现得像一个)。这是一个 Node.js 工具，它接受有效的 CSS 并增强它。甚至那些使用 Sass、Less 或 Stylus 的人也经常在初始 CSS 编译后运行 PostCSS 步骤。您可能遇到过 PostCSS [Autoprefixer 插件](https://github.com/postcss/autoprefixer)，它会自动将`-webkit`、`-moz`和`-ms`厂商前缀添加到需要它们的 CSS 属性前面。

就其本身而言，PostCSS 什么也不做。这是一个解析器，它将 CSS 代码标记化以创建一个抽象的语法树。插件可以处理这个树并相应地更新属性。一旦所有插件都完成了它们的工作，PostCSS 将所有内容重新格式化成一个字符串并输出到一个 CSS 文件。

大约有 [350 个插件可用](https://github.com/postcss/postcss/blob/main/docs/plugins.md)，大多数执行单一任务，如[内联`@import`声明](https://github.com/postcss/postcss-import)、[简化`calc()`功能](https://github.com/postcss/postcss-calc)、[处理图像资产](https://github.com/borodean/postcss-assets)、[语法林挺](https://github.com/stylelint/stylelint)、[缩小](https://cssnano.co/)等等。PostCSS [插件目录](https://www.postcss.parts/)提供了一个更加用户友好的插件搜索。

后 CSS 福利包括:

*   你从标准 CSS 开始。PostCSS 对于 CSS 就像 [Babel](http://babeljs.io/) 对于 JavaScript 一样。它可以采用在最新浏览器中工作的标准样式表，并输出在任何地方都可以工作的 CSS 例如，将新的 [`inset`属性](https://developer.mozilla.org/docs/Web/CSS/inset)转换回`top`、`bottom`、`left`和`right`属性。随着时间的推移，随着越来越多的浏览器支持`inset`，你可以放弃这个过程。

    诚然，一些插件允许你解析类似预处理器的语法，这不是标准的 CSS，但你不必使用它们。

*   **使用你需要的插件和功能**。PostCSS 是可配置的，你可以采用你需要的插件。例如，您可以支持[部分](https://github.com/postcss/postcss-import)和[嵌套](https://github.com/postcss/postcss-nested)，但是不允许 Sass 中的变量、循环、混合、地图和其他特性。

*   **为每个项目**提供自定义配置。单个项目配置可以增强或减少其他地方使用的插件集。选项比任何预处理程序都要多得多。

*   **自己写 PostCSS 插件**。大量插件可用于扩展语法、解析未来属性、添加后备、优化代码、处理颜色、图像、字体，甚至用其他语言编写 CSS，如[西班牙语](https://github.com/ismamz/postcss-spanish-stylesheets)和[俄语](https://github.com/Semigradsky/postcss-russian-stylesheets)。

    万一你找不到你需要的东西，你可以用 JavaScript 编写你自己的 PostCSS 插件。

*   **你可能已经在使用 PostCSS 了**。如果您已经在运行一个 PostCSS 插件，比如 AutoPrefixer，那么您可能能够删除您的预处理器依赖项。PostCSS 不一定比使用预处理器更快或更轻量级，但它可以在一个步骤中处理所有 CSS 处理。

## 安装 PostCSS

postscs 需要 [Node.js](https://nodejs.org/) ，但是本教程演示了如何从任何文件夹安装和运行 postscs——甚至那些不是 Node.js 项目的文件夹。您也可以使用来自 [webpack、Parcel、Gulp.js 的 PostCSS，以及其他工具](https://github.com/postcss/postcss#usage)，但是我们将坚持使用命令行。

通过运行以下命令，在您的系统上全局安装 PostCSS:

```
npm install -g postcss-cli 
```

通过输入以下命令确保它正在工作:

```
postcss --help 
```

## 安装你的第一个 PostCSS 插件

你需要至少一个插件来做任何实际的事情。PostCSS [import](https://github.com/postcss/postcss-import) 插件是一个很好的选择，它内嵌所有的`@import`声明，并将你的 CSS 合并到一个文件中。像这样全局安装它:

```
npm install -g postcss-import 
```

要测试这个插件，打开或创建一个新的项目文件夹，比如`cssproject`，然后为您的源文件创建一个`src`子文件夹。创建一个`main.css`文件来加载所有的部分:

```
/* src/main.css */
@import '_reset';
@import '_elements'; 
```

然后在同一文件夹中创建一个`_reset.css`文件:

```
/* src/reset.css */
* {
  padding: 0;
  margin: 0;
} 
```

接下来是一个`_elements.css`文件:

```
/* src/elements.css */
body {
  font-family: sans-serif;
}

label {
  user-select: none;
} 
```

通过向`--use`传递输入 CSS 文件、插件列表和一个`--output`文件名，从项目的根文件夹运行 PostCSS:

```
postcss ./src/main.css --use postcss-import --output ./styles.css 
```

如果没有任何错误，下面的代码将输出到项目根目录下的一个新的`styles.css`文件中:

```
/* src/main.css */
/* src/reset.css */
* {
  padding: 0;
  margin: 0;
}
/* src/elements.css */
body {
  font-family: sans-serif;
}
label {
  user-select: none;
}
/* sourceMappingURL=data:application/json;base64,... 
```

*注意，PostCSS 可以在任何地方输出 CSS 文件，但是输出文件夹必须存在；它不会为您创建文件夹结构。*

## 启用和禁用源映射

默认情况下，输出内联源映射。当在 HTML 页面中使用编译的 CSS 文件时，在浏览器的开发工具中检查它将显示原始的`src`文件和行。例如，查看`<body>`样式将突出显示`src/_elements.css`行 2，而不是`styles.css`行 8。

您可以通过在`postcss`命令中添加一个`--map`(或`-m`)开关来创建一个外部源地图。除了 CSS 文件更干净，浏览器不需要加载源地图，除非开发者工具打开之外，没有什么好处。

可以用`--no-map`移除源地图。为生产部署输出 CSS 文件时，请始终使用此选项。

## 安装并使用自动修复插件

[Autoprefixer](https://github.com/postcss/autoprefixer) 插件通常是开发者第一次接触 PostCSS。它根据浏览器的使用和在[caniuse.com](https://caniuse.com/)定义的规则添加厂商前缀。供应商前缀在现代浏览器中很少使用，它将实验功能隐藏在标志后面。但仍有 [`user-select`](https://developer.mozilla.org/docs/Web/CSS/user-select) 等属性需要`-webkit-`、`-moz-`、`-ms-`前缀。

使用以下命令全局安装插件:

```
npm install -g autoprefixer 
```

然后将其作为另一个`--use`选项引用到您的`postcss`命令中:

```
postcss ./src/main.css --use postcss-import --use autoprefixer --output ./styles.css 
```

检查`styles.css`第 11 行的`label`声明，查看供应商前缀属性:

```
label {
  -webkit-user-select: none;
     -moz-user-select: none;
      -ms-user-select: none;
          user-select: none;
} 
```

AutoPrefixer 使用 [browserlist](https://github.com/browserslist/browserslist) 模块来确定要添加哪些浏览器前缀。默认值为:

*   `> 0.5%`:至少拥有 0.5%市场份额的浏览器
*   这些浏览器的最后两个版本
*   `Firefox ESR`:包括 Firefox 扩展支持版本
*   `not dead`:任何未停产的浏览器

您可以通过创建一个`.browserslistrc`文件来更改这些默认值。例如:

```
> 2% 
```

或者可以在 Node.js 项目中给`package.json`添加一个`"browserslist"`数组。例如:

```
"browserslist": [
   "> 2%"
] 
```

针对拥有 2%或更多市场份额的浏览器，只需要在 Safari 中添加一个`-webkit-`前缀:

```
label {
  -webkit-user-select: none;
          user-select: none;
} 
```

## 用 cssnano 缩小 CSS

通过去除空白、注释和其他不必要的字符来缩小 CSS。结果会有所不同，但您可以预期文件减少 30%,您可以将其部署到生产服务器以获得更好的网页性能。

全局安装 cssnano:

```
npm install -g cssnano 
```

然后将其添加到您的`postcss`命令中。我们还将包含`--no-map`来禁用源地图:

```
postcss ./src/main.css --use postcss-import --use autoprefixer --use cssnano --no-map --output ./styles.css 
```

这将 CSS 文件减少到 97 个字符:

```
*{margin:0;padding:0}body{font-family:sans-serif}label{-webkit-user-select:none;user-select:none} 
```

## 源文件更改时自动生成

当任何源文件改变时，PostCSS `--watch`选项自动构建您的 CSS 文件。您可能还想添加`--verbose`开关，它在构建发生时进行报告:

```
postcss ./src/main.css --use postcss-import --use autoprefixer --use cssnano --no-map --output ./styles.css --watch --verbose 
```

你的终端会显示`Waiting for file changes`。对任何文件进行更改，然后重新构建`styles.css`。PostCSS 还会报告任何问题，比如语法错误。

要结束，在终端中按下`Ctrl` | `Cmd` + `C`。

## 创建 PostCSS 配置文件

随着你添加更多的插件和选项,`postcss`命令会变得冗长而麻烦。您可以创建一个 JavaScript 配置文件，该文件定义了所有选项，并且可以从逻辑上确定它是在开发环境中运行还是在生产环境中运行。

在项目文件夹的根目录下创建一个名为`postcss.config.cjs`的配置文件。Aso 注意以下事项:

*   您可以将文件放在另一个文件夹中，但是您需要在运行`postcss`时指定`--config <dir>`
*   您可以使用`postcss.config.js`作为文件名，但是在`package.json`中设置了`"type": "module"`的 Node.js 项目中，PostCSS 可能会失败

将以下代码添加到`postcss.config.cjs`:

```
// PostCSS configruation
module.exports = (cfg) => {

  const devMode = (cfg.env === 'development');

  return {

    map: devMode ? 'inline' : null,
    plugins: [
      require('postcss-import')(),
      require('autoprefixer')(),
      devMode ? null : require('cssnano')()
    ]

  };

}; 
```

PostCSS 传递一个包含命令行选项的`cfg`对象。例如:

```
{
  cwd: '/home/yourname/cssproject',
  env: undefined,
  options: {
    map: { inline: true },
    parser: undefined,
    syntax: undefined,
    stringifier: undefined
  },
  file: {
    dirname: '/home/yourname/cssproject/src',
    basename: 'main.css',
    extname: '.css'
  }
} 
```

该模块必须返回具有可选属性的对象:

*   `map`:源地图设置
*   `parser`:是否使用非 CSS 语法解析器(比如 [scss](https://github.com/postcss/postcss-scss) 插件)
*   `plugins`:以指定顺序处理的插件和配置的数组

上面的代码检测`postcss`命令是否有一个`--env`选项。这是设置`NODE_ENV`环境变量的快捷方式。要在开发模式下编译 CSS，使用`--env development`运行`postcss`，并可选地设置`--watch --verbose`。这将创建一个内联源映射，并且不会缩小输出:

```
postcss ./src/main.css --output ./styles.css --env development --watch --verbose 
```

要在生产模式下运行并在没有源映射的情况下编译缩小的 CSS，请使用以下命令:

```
postcss ./src/main.css --output ./styles.css 
```

理想情况下，您可以将这些作为终端或`npm`脚本运行，以进一步减少输入工作量。

## CSS 后进度

您现在已经了解了 PostCSS 的基础知识。增强功能就是添加和配置更多的插件。投入一些时间，你很快就会有一个适合任何 web 项目的工作流。

关于如何使用 PostCSS 作为 Sass 的[可配置替代方案的教程提供了更多的配置示例和插件选项。](https://www.sitepoint.com/postcss-sass-configurable-alternative/)

更多链接:

*   PostCSS [home page](https://postcss.org/)
*   PostCSS [CLI](https://github.com/postcss/postcss-cli)
*   PostCSS 的插件列表
*   一个可搜索的 PostCSS 插件目录
*   npmjs.com 上用于 PostCSS 的插件
*   [为 PostCSS 编写插件](https://github.com/postcss/postcss/blob/main/docs/writing-a-plugin.md)

## 分享这篇文章