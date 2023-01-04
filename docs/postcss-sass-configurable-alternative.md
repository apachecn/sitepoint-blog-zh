# 如何使用 PostCSS 作为 Sass 的可配置替代方案

> 原文：<https://www.sitepoint.com/postcss-sass-configurable-alternative/>

Web 开发人员喜欢 [Sass CSS 预处理器](https://sass-lang.com/)。根据 CSS 调查的[状态的](https://2020.stateofcss.com/) [Sass 意见](https://2020.stateofcss.com/technologies/pre-post-processors/)，每个开发者都知道它是什么，89%的人经常使用，88%的人满意度很高。

许多 web bundlers 包含 Sass 处理，但是你也可能在没有意识到的情况下使用了 [PostCSS](https://postcss.org/) 。PostCSS 主要以其 [Autoprefixer 插件](https://github.com/postcss/autoprefixer)而闻名，该插件在需要时自动将`-webkit`、`-moz`和`-ms`厂商前缀添加到 CSS 属性中。它的插件系统意味着它可以做更多的事情… *比如编译`.scss`文件而不需要使用 Sass 编译器。*

本教程解释了如何创建一个定制的 CSS 预处理器，它可以编译 Sass 语法并补充更多的特性。它非常适合任何对 CSS 有特定需求并且了解一点 Node.js 的人。

## 快速启动

PostCSS 项目的一个例子可以从 [GitHub](https://github.com/sitepoint-editors/postcss-demo) 中克隆。它需要 Node.js，所以运行`npm install`来获取所有依赖项。

使用以下代码将演示`src/scss/main.scss`源代码编译成`build/css/main.css`:

```
npm run css:dev 
```

每当文件改变时自动编译，使用:

```
npm run css:watch 
```

然后在终端按下`Ctrl` | `Cmd` + `C`退出观看。

这两个选项还会在`build/css/main.css.map`处创建一个源映射，它会引用开发人员工具中的原始源文件。

没有源地图的生产级缩小 CSS 可以使用以下方式编译:

```
npm run css:build 
```

更多信息请参考`README.md`文件。

## 应该用 PostCSS 代替 Sass 吗？

Sass 编译器没有问题，但是要考虑以下因素。

### 模块依赖性

Sass 的最新 Dart 版本可以使用 Node.js `npm`包管理器进行全局安装:

```
npm install -g sass 
```

使用以下代码编译 Sass `.scss`代码:

```
sass [input.scss] [output.css] 
```

源映射是自动生成的(`--no-source-map`将关闭它们)，或者`--watch`可以在源文件改变时添加到自动编译源文件中。

最新版本的 Sass 需要不到 5MB 的安装空间。

PostCSS *should* 需要更少的资源和一个带有自动前缀的基本的类似 Sass 的编译器，缩小需要小于 1MB 的空间。实际上，你的`node_modules`文件夹会扩展到 60MB 以上，并且随着插件的增加而迅速增加。这主要是`npm`安装其他依赖项。即使 PostCSS 可能不使用它们，也不能认为是轻量级的替代方案。

但是，如果您已经将 PostCSS 用于 Autoprefixer 或其他目的，Sass 可能就没有必要了。

### 加工速率

缓慢的、基于 Ruby 的 Sass 编译器早已过时，最新版本使用编译过的 [Dart](https://dart.dev/) 运行时。*真快。*

PostCSS 是纯 JavaScript，虽然基准会有所不同，但它在编译相同的源代码时会慢三倍。

然而，如果您已经在 Sass 之后运行 PostCSS，这种速度差异就不那么明显了。两阶段过程可能比单独使用 PostCSS 要慢，因为它的大部分工作涉及到标记 CSS 属性。

### 用户化

Sass 语言包含了大量的特性，包括变量、嵌套、部分、混合等等。也有不利的一面:

1.  您不能轻易添加新功能。

    也许你想选择将 HSLA 颜色转换成 RGB。也许可以编写一个自定义函数，但是其他要求是不可能的——比如将一个 SVG 内联为背景图像。

2.  你不能轻易地限制特性集。

    也许你更希望你的团队不要使用嵌套或者`@extend`。林挺规则会有所帮助，但他们不会阻止 Sass 编译有效的`.scss`文件。

PostCSS 的可配置性要高得多。

就其本身而言，PostCSS 什么也不做。处理功能需要一个或多个可用的插件。大多数执行单一任务，所以如果你不想嵌套，不要添加嵌套插件。如果有必要，你可以[在一个标准的 JavaScript 模块中编写你自己的插件](https://github.com/postcss/postcss/blob/main/docs/writing-a-plugin.md)，该模块可以利用 Node.js 的能力

## Install PostCSS

PostCSS 可以与 [webpack、Parcel、Gulp.js 和其他构建工具](https://github.com/postcss/postcss#usage)一起使用，但本教程展示了如何从命令行运行它。

如果需要，用`npm init`初始化一个新的 Node.js 项目。通过安装以下用于基本`.scss`解析的模块来设置 PostCSS，这些模块带有用于部分、变量、混合、嵌套和自动前缀的插件:

```
npm install --save-dev postcss postcss-cli postcss-scss postcss-advanced-variables postcss-nested autoprefixer 
```

和[示例项目](https://github.com/sitepoint-editors/postcss-demo)一样，PostCSS 及其插件都是本地安装的。如果您的项目可能有不同的编译需求，这是一个实用的选择。

*注意:PostCSS 只能从 JavaScript 文件运行，但是 [`postcss-cli`](https://github.com/postcss/postcss-cli) 模块提供了一个可以从命令行调用的包装器。 [postcss-scss](https://github.com/postcss/postcss-scss) 模块允许 postcss 读取`.scss`文件，但不转换它们。*

### 自动修复程序配置

Autoprefixer 使用 [browserslist](https://github.com/browserslist/browserslist) 来根据您支持的浏览器列表确定需要哪些供应商前缀。将这个列表定义为`package.json`中的`"browserslist"`数组是最简单的。以下示例在任何浏览器拥有至少 2%市场份额的情况下添加供应商前缀:

```
"browserslist": [
  "> 2%"
], 
```

## 你的第一次建造

您通常会有一个根 Sass `.scss`文件，它导入所有需要的部分/组件文件。例如:

```
// root Sass file
// src/scss/main.scss
@import '_variables';
@import '_reset';
@import 'components/_card';
// etc. 
```

可以通过运行`npx postcss`开始编译，然后运行输入文件、一个`--output`文件和任何必需的选项。例如:

```
npx postcss ./src/scss/main.scss \
    --output ./build/css/main.css \
    --env development \
    --map \
    --verbose \
    --parser postcss-scss \
    --use postcss-advanced-variables postcss-nested autoprefixer 
```

该命令:

1.  解析`./src/scss/main.scss`
2.  输出到`./build/css/main.css`
3.  将`NODE_ENV`环境变量设置为`development`
4.  输出外部源映射文件
5.  设置详细的输出和错误消息
6.  设置`postcss-scss` Sass 解析器，并且
7.  使用插件`postcss-advanced-variables`、`postcss-nested`和`autoprefixer`来处理部分、变量、混合、嵌套和自动前缀

可选地，当`.scss`文件被修改时，您可以添加`--watch`来自动编译。

## 创建 PostCSS 配置文件

对于更长的插件列表，命令行很快变得难以使用。您可以将其定义为一个`npm`脚本，但是 PostCSS 配置文件是一个更简单的选项，它提供了额外的可能性。

PostCSS 配置文件是名为`postcss.config.js`的 JavaScript 模块文件，通常存储在项目的根目录(或者运行 PostCSS 的目录)。模块必须`export`单一功能:

```
// postcss.config.js
module.exports = cfg => {

  // ... configuration ...

}; 
```

它传递了一个属性由`postcss`命令设置的`cfg`对象。例如:

```
{
  cwd: '/home/name/postcss-demo',
  env: 'development',
  options: {
    map: undefined,
    parser: undefined,
    syntax: undefined,
    stringifier: undefined
  },
  file: {
    dirname: '/home/name/postcss-demo/src/scss',
    basename: 'main.scss',
    extname: '.scss'
  }
} 
```

您可以检查这些属性并做出相应的反应——例如，确定您是否正在以`development`模式运行并处理一个`.scss`输入文件:

```
// postcss.config.js
module.exports = cfg => {

  const
    dev = cfg.env === 'development',
    scss = cfg.file.extname === '.scss';

  // ... configuration ...

}; 
```

该函数必须返回一个属性名与 [`postcss-cli`](https://github.com/postcss/postcss-cli) 命令行选项相匹配的对象。以下配置文件复制了在上使用的长[快速启动命令:](#postcss-quick-start)

```
// postcss.config.js
module.exports = cfg => {

  const
    dev = cfg.env === 'development',
    scss = cfg.file.extname === '.scss';

  return {

    map: dev ? { inline: false } : false,
    parser:  scss ? 'postcss-scss' : false,
    plugins: [
      require('postcss-advanced-variables')(),
      require('postcss-nested')(),
      require('autoprefixer')()
    ]

  };

}; 
```

现在可以使用一个更短的命令来运行 PostCSS:

```
npx postcss ./src/scss/main.scss \
    --output ./build/css/main.css \
    --env development \
    --verbose 
```

以下是一些需要注意的事项:

*   `--verbose`是可选的:在`postcss.config.js`中没有设置。
*   只有当输入是一个`.scss`文件时，才应用 Sass 语法解析。否则，它默认为标准 CSS。
*   仅当`--env`设置为`development`时，才会输出源图。
*   仍可添加`--watch`进行自动编译。

如果你想让`postcss.config.js`在另一个目录中，可以用`--config`选项来引用它——比如`--config /mycfg/`。在[示例项目](https://github.com/sitepoint-editors/postcss-demo)中，上面的配置位于 [`config/postcss.config.js`](https://github.com/sitepoint-editors/postcss-demo/blob/main/config/postcss.config.js) 。它被运行`npm run css:basic`引用，调用:

```
npx postcss src/scss/main.scss \
    --output build/css/main.css \
    --env development \
    --verbose \
    --config ./config/ 
```

## 添加更多插件

下面几节提供了 PostCSS 插件的例子，这些插件要么解析额外的`.scss`语法，要么提供超出 Sass 编译器范围的处理。

### 使用设计符号

设计令牌是一种技术不可知的方式来存储变量，如公司范围内的字体、颜色、间距等。您可以在 JSON 文件中存储令牌名称-值对:

```
{

  "font-size": "16px",

  "font-main": "Roboto, Oxygen-Sans, Ubuntu, sans-serif",
  "lineheight": 1.5,

  "font-code": "Menlo, Consolas, Monaco, monospace",
  "lineheight-code": 1.2,

  "color-back": "#f5f5f5",
  "color-fore": "#444"

} 
```

然后在任何 web、Windows、macOS、iOS、Linux、Android 或其他应用程序中引用它们。

Sass 不直接支持设计令牌，但是可以将一个包含名称-值对的`variables`属性的 JavaScript 对象传递给现有的[PostCSS-advanced-variables](https://github.com/jonathantneal/postcss-advanced-variables)PostCSS 插件:

```
// PostCSS configuration
module.exports = cfg => {

  // import tokens as Sass variables
  const variables = require('./tokens.json'); // NEW

  const
    dev = cfg.env === 'development',
    scss = cfg.file.extname === '.scss';

  return {

    map: dev ? { inline: false } : false,
    parser:  scss ? 'postcss-scss' : false,
    plugins: [
      require('postcss-advanced-variables')({ variables }), // UPDATED
      require('postcss-nested')(),
      require('autoprefixer')()
    ]

  };

}; 
```

该插件将所有的值转换为全局 Sass `$variables`,可以在任何部分中使用。可以设置回退值，以确保变量可用，即使它从`tokens.json`中丢失。例如:

```
// set default background color to #FFF
// if no "color-back" value is set in tokens.json
$color-back: #fff !default; 
```

然后可以在任何`.scss`文件中引用令牌变量。例如:

```
body {
  font-family: $font-main;
  font-size: $font-size;
  line-height: $lineheight;
  color: $color-fore;
  background-color: $color-back;
} 
```

在[示例项目](https://github.com/sitepoint-editors/postcss-demo)中，定义了一个 [`token.json`](https://github.com/sitepoint-editors/postcss-demo/blob/main/tokens.json) 文件，在运行`npm run css:dev`时加载使用。

### 添加 Sass 地图支持

[萨斯图](https://sass-lang.com/documentation/values/maps)是键值对象。`map-get`函数可以通过名称查找值。

以下示例将媒体查询断点定义为一个 Sass 映射，带有一个用于获取命名值的`respond` mixin:

```
// media query breakpoints
$breakpoint: (
  'small':  36rem,
  'medium': 50rem,
  'large':  64rem
);

/*
responsive breakpoint mixin:
@include respond('medium') { ... }
*/
@mixin respond($bp) {
  @media (min-width: map-get($breakpoint, $bp)) {
    @content;
  }
} 
```

然后，可以在同一个选择器中定义默认属性和媒体查询修改。例如:

```
main {
  width: 100%;

  @include respond('medium') {
    width: 40em;
  }
} 
```

编译成 CSS:

```
main {
  width: 100%;
}

@media (min-width: 50rem) {

  main {
    width: 40em
  }

} 
```

[postcss-map-get](https://github.com/Scrum/postcss-map-get) 插件增加了 Sass 地图处理。安装时使用:

```
npm install --save-dev postcss-map-get 
```

并更新`postcss.config.js`配置文件:

```
// PostCSS configuration
module.exports = cfg => {

  // import tokens as Sass variables
  const variables = require('./tokens.json');

  const
    dev = cfg.env === 'development',
    scss = cfg.file.extname === '.scss';

  return {

    map: dev ? { inline: false } : false,
    parser:  scss ? 'postcss-scss' : false,
    plugins: [
      require('postcss-advanced-variables')({ variables }),
      require('postcss-map-get')(), // NEW
      require('postcss-nested')(),
      require('autoprefixer')()
    ]

  };

}; 
```

### 添加媒体查询优化

因为我们已经添加了媒体查询，所以将它们合并并排序为移动优先顺序将会很有用。例如，下面的 CSS:

```
@media (min-width: 50rem) {

  main {
    width: 40em;
  }

}

@media (min-width: 50rem) {

  #menu {
    width: 30em;
  }

} 
```

可以合并成:

```
@media (min-width: 50rem) {

  main {
    width: 40em;
  }

  #menu {
    width: 30em;
  }

} 
```

这在 Sass 中是不可能的，但是可以用 PostCSS[PostCSS-sort-media-queries](https://github.com/solversgroup/postcss-sort-media-queries)插件来实现。安装时使用:

```
npm install --save-dev postcss-sort-media-queries 
```

然后添加到`postcss.config.js`:

```
// PostCSS configuration
module.exports = cfg => {

  // import tokens as Sass variables
  const variables = require('./tokens.json');

  const
    dev = cfg.env === 'development',
    scss = cfg.file.extname === '.scss';

  return {

    map: dev ? { inline: false } : false,
    parser:  scss ? 'postcss-scss' : false,
    plugins: [
      require('postcss-advanced-variables')({ variables }),
      require('postcss-map-get')(),
      require('postcss-nested')(),
      require('postcss-sort-media-queries')(), // NEW
      require('autoprefixer')()
    ]

  };

}; 
```

### 添加资产处理

资产管理在 Sass 中不可用，但是 [postcss-assets](https://github.com/borodean/postcss-assets) 使它变得容易。该插件解析 CSS 图像 URL，添加缓存破坏，定义图像尺寸，并使用 base64 符号内联文件。例如:

```
#mybackground {
  background-image: resolve('back.png');
  width: width('back.png');
  height: height('back.png');
  background-size: size('back.png');
} 
```

编译为:

```
#mybackground {
  background-image: urlimg/back.png');
  width: 600px;
  height: 400px;
  background-size: 600px 400px;
} 
```

使用以下内容安装插件:

```
npm install --save-dev postcss-assets 
```

然后添加到`postcss.config.js`。在这种情况下，插件被指示在`simg/`目录中定位图像:

```
// PostCSS configuration
module.exports = cfg => {

  // import tokens as Sass variables
  const variables = require('./tokens.json');

  const
    dev = cfg.env === 'development',
    scss = cfg.file.extname === '.scss';

  return {

    map: dev ? { inline: false } : false,
    parser:  scss ? 'postcss-scss' : false,
    plugins: [
      require('postcss-advanced-variables')({ variables }),
      require('postcss-map-get')(),
      require('postcss-nested')(),
      require('postcss-sort-media-queries')(),
      require('postcss-assets')({   // NEW
        loadPaths: ['simg/']
      }),
      require('autoprefixer')()
    ]

  };

}; 
```

### 添加缩小

cssnano 设定了 CSS 缩小的标准。缩小可能比其他插件花费更多的处理时间，所以它只能应用于生产。

安装 cssnano 时使用:

```
npm install --save-dev cssnano 
```

然后添加到`postcss.config.js`。在这种情况下，只有当`NODE_ENV`设置为除`development`之外的任何值时，才会发生缩小:

```
// PostCSS configuration
module.exports = cfg => {

  // import tokens as Sass variables
  const variables = require('./tokens.json');

  const
    dev = cfg.env === 'development',
    scss = cfg.file.extname === '.scss';

  return {

    map: dev ? { inline: false } : false,
    parser:  scss ? 'postcss-scss' : false,
    plugins: [
      require('postcss-advanced-variables')({ variables }),
      require('postcss-map-get')(),
      require('postcss-nested')(),
      require('postcss-sort-media-queries')(),
      require('postcss-assets')({
        loadPaths: ['simg/']
      }),
      require('autoprefixer')(),
      dev ? null : require('cssnano')() // NEW
    ]

  };

}; 
```

将`--env`设置为`prodution`会触发缩小(并移除源地图):

```
npx postcss ./src/scss/main.scss \
    --output ./build/css/main.css \
    --env prodution \
    --verbose 
```

在[示例项目](https://github.com/sitepoint-editors/postcss-demo)中，可以通过运行`npm run css:build`来编译生产 CSS。

## 进展到 PostCSS？

PostCSS 是一个强大的可配置工具，可以编译`.scss`文件，增强(或限制)标准 Sass 语言。如果您已经在使用 PostCSS for Autoprefixer，那么您可以完全删除 Sass 编译器，同时保留您喜欢的语法。

更多链接:

*   [萨斯语言参考](https://sass-lang.com/documentation)
*   [PostCSS home page](https://postcss.org/)
*   [PostCSS plugins list](https://github.com/postcss/postcss/blob/main/docs/plugins.md)
*   [PostCSS 插件的可搜索目录](https://www.postcss.parts/)
*   [PostCSS plugins on npmjs.com](https://www.npmjs.com/search?q=keywords:postcss)
*   [编写 PostCSS 插件](https://github.com/postcss/postcss/blob/main/docs/writing-a-plugin.md)
*   [PostCSS CLI](https://github.com/postcss/postcss-cli)

## 分享这篇文章