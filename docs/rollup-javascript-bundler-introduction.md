# Rollup.js JavaScript Bundler 简介

> 原文：<https://www.sitepoint.com/rollup-javascript-bundler-introduction/>

**[Rollup.js](https://rollupjs.org/) 是 Rich Harris 的下一代 JavaScript 模块 bundler， [Svelte](https://www.sitepoint.com/svelte-javascript-framework-introduction/) 的作者。它将多个源文件编译成一个包。**

好处包括:

*   当使用较小的、自包含的源文件时，开发更容易管理
*   在捆绑过程中，可以对源代码进行删节、修饰和语法检查
*   *树摇动*移除未使用的功能
*   为了向后兼容，转换到 ES5 是可能的
*   可以生成多个输出文件——例如，您的库可以在 ES5、ES6 模块和 Node.js 兼容的 CommonJS 中提供
*   可以缩小生产包，并删除日志记录

其他捆绑器选项，如 [webpack](https://webpack.js.org/) 、 [Snowpack](https://www.snowpack.dev/) 和[package](https://parceljs.org/)，试图神奇地处理一切:HTML 模板、图像优化、CSS 处理、JavaScript 捆绑等等。当您对默认设置满意时，这样做很好，但是定制配置可能会很困难，并且处理速度较慢。

Rollup.js 主要专注于 JavaScript(虽然也有 HTML 模板和 CSS 的插件)。它有数量惊人的选项，但很容易上手，捆绑速度也很快。本教程解释了如何在您自己的项目中使用典型配置。

## 安装 Rollup.js

Rollup.js 需要 Node.js v8.0.0 或更高版本，可以通过以下软件进行全局安装:

```
npm install rollup --global 
```

这允许在任何包含 JavaScript 文件的项目目录中运行`rollup`命令——比如 PHP、WordPress、Python、Ruby 或其他项目。

但是，如果您在一个更大的团队中创建 Node.js 项目，那么最好在本地安装 Rollup.js，以确保所有开发人员都使用相同的版本。假设您在项目文件夹中有一个现有的 Node.js `package.json`文件，运行:

```
npm install rollup --save-dev 
```

您将不能直接运行`rollup`命令，但是可以使用`npx rollup`。或者，`rollup`命令可以添加到`package.json` `"scripts"`部分。例如:

```
"scripts": {
  "watch": "rollup ./src/main.js --file ./build/bundle.js --format es --watch",
  "build": "rollup ./src/main.js --file ./build/bundle.js --format es",
  "help": "rollup --help"
}, 
```

这些脚本可以用`npm run <scriptname>`来执行，例如`npm run watch`。

下面的例子特别使用了`npx rollup`，因为不管`rollup`是本地安装还是全局安装，它都会工作。

## 示例文件

示例文件和 Rollup.js 配置可以从 [GitHub](https://github.com/sitepoint-editors/rollup-demo) 下载。这是一个 Node.js 项目，所以在克隆后运行`npm install`并检查`README.md`文件以获取说明。注意 Rollup.js 和所有插件都是本地安装的。

或者，您可以在用`npm init`初始化一个新的 Node.js 项目之后手动创建源文件。以下 ES6 模块创建一个实时数字时钟，用于演示 Rollup.js 处理。

`src/main.js`是主入口点脚本。它定位一个 DOM 元素并每秒运行一个函数，将其内容设置为当前时间:

```
import * as dom from './lib/dom.js';
import { formatHMS } from './lib/time.js';

// get clock element
const clock = dom.get('.clock');

if (clock) {

  console.log('initializing clock');

  // update clock every second
  setInterval(() => {

    clock.textContent = formatHMS();

  }, 1000);

} 
```

`src/lib/dom.js`是一个小型的 DOM 实用程序库:

```
// DOM libary

// fetch first node from selector
export function get(selector, doc = document) {
  return doc.querySelector(selector);
}

// fetch all nodes from selector
export function getAll(selector, doc = document) {
  return doc.querySelectorAll(selector);
} 
```

`src/lib/time.js`提供时间格式化功能:

```
// time formatting

// return 2-digit value
function timePad(n) {
  return String(n).padStart(2, '0');
}

// return time in HH:MM format
export function formatHM(d = new Date()) {
  return timePad(d.getHours()) + ':' + timePad(d.getMinutes());
}

// return time in HH:MM:SS format
export function formatHMS(d = new Date()) {
  return formatHM(d) + ':' + timePad(d.getSeconds());
} 
```

通过创建一个带有`clock`类的 HTML 元素并将脚本作为 ES6 模块加载，可以将时钟代码添加到网页中:

```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Rollup.js testing</title>
<meta name="viewport" content="width=device-width,initial-scale=1" />
<script type="module" src="./src/main.js"></script>
</head>
<body>

  <h1>Clock</h1>

  <time class="clock"></time>

</body>
</html> 
```

Rollup.js 提供了优化 JavaScript 源文件的选项。

## Rollup.js 快速入门

可以从项目文件夹的根目录运行以下命令来处理`src/main.js`及其依赖项:

```
npx rollup ./src/main.js --file ./build/bundle.js --format iife 
```

在`build/bundle.js`输出单个脚本。它包含了所有的代码，但是请注意没有使用的依赖项，比如`src/lib/dom.js`中的`getAll()`函数已经被移除了:

```
(function () {
  'use strict';

  // DOM libary

  // fetch first node from selector
  function get(selector, doc = document) {
    return doc.querySelector(selector);
  }

  // time library

  // return 2-digit value
  function timePad(n) {
    return String(n).padStart(2, '0');
  }

  // return time in HH:MM format
  function formatHM(d = new Date()) {
    return timePad(d.getHours()) + ':' + timePad(d.getMinutes());
  }

  // return time in HH:MM:SS format
  function formatHMS(d = new Date()) {
    return formatHM(d) + ':' + timePad(d.getSeconds());
  }

  // get clock element
  const clock = get('.clock');

  if (clock) {

    console.log('initializing clock');

    setInterval(() => {

      clock.textContent = formatHMS();

    }, 1000);

  }

}()); 
```

现在可以更改 HTML `<script>`来引用捆绑文件:

```
<script type="module" src="./build/bundle.js"></script> 
```

*注意:`type="module"`不再需要，所以该脚本应该可以在支持早期 ES6 实现的旧浏览器中工作。您还应该添加一个`defer`属性来确保脚本在 DOM 准备好之后运行(这在 ES6 模块中默认发生)。*

Rollup.js 提供了[许多命令行标志](https://rollupjs.org/guide/en/#command-line-flags)。以下部分描述了最有用的选项。

### Rollup.js 帮助

可以使用`--help`或`-h`标志查看 Rollup 的命令行选项:

```
npx rollup --help 
```

Rollup.js 版本可以用`--version`或`-v`输出:

```
npx rollup --version 
```

### 输出文件

`--file`(或`-o`)标志定义输出包文件，该文件设置为上面的`./build/bundle.js`。如果没有指定文件，结果包将被发送到`stdout`。

### JavaScript 格式

Rollup.js 提供了几个`--format`(或`-f`)选项来配置结果包:

| 选择权 | 描述 |
| --- | --- |
| `iife` | 将代码包装在立即调用的函数表达式`(function () { ... }());`块中，这样它就不会与其他库冲突 |
| `es6` | 标准 ES6 |
| `cjs` | Node.js 的 CommonJS |
| `umd` | [通用模块定义](https://github.com/umdjs/umd)用于客户端和服务器 |
| `amd` | [异步模块定义](https://github.com/amdjs) |
| `system` | [SystemJS 模块](https://github.com/systemjs/systemjs) |

除非您使用特定的模块系统，`iife`将是客户端 JavaScript 的最佳选择。`es6`将生成一个稍微小一点的包，但是要小心全局变量和函数，它们可能会与其他库冲突。

### 输出源地图

源映射提供了对源文件的引用，因此可以在浏览器开发工具中检查它们。这使得在出现错误时设置断点或定位问题变得更加容易。

通过在`rollup`命令中添加一个`--sourcemap`标志，可以创建一个外部源映射:

```
npx rollup ./src/main.js --file ./build/bundle.js --format iife --sourcemap 
```

这将创建一个额外的`./build/bundle.js.map`文件。你可以查看它，虽然它大部分是胡言乱语，不是为了人类消费！该地图在`./build/bundle.js`的末尾作为注释引用:

```
//# sourceMappingURL=bundle.js.map 
```

或者，您可以使用`--sourcemap inline`创建一个内嵌的源映射。不是生成额外的文件，而是将 base64 编码版本的源地图附加到`./build/bundle.js`:

```
//# sourceMappingURL=data:application/json;charset=utf-8;base64,eyJ2ZXJzaW9uIjozLCJmaWxlIjoiY...etc... 
```

生成源映射后，您可以加载一个引用该脚本的示例页面。打开你的开发者工具，在基于 Chrome 的浏览器中导航到**源代码**标签，或者在 Firefox 中导航到**调试器**标签。您将看到原始的`src`代码和行号。

### 观看文件并自动捆绑

`--watch`(或`-w`)标志监控源文件的变化并自动构建包。每次运行时，终端屏幕都会被清除，但您可以使用`--no-watch.clearScreen`将其禁用:

```
npx rollup ./src/main.js --file ./build/bundle.js --format iife --watch --no-watch.clearScreen 
```

## 创建配置文件

命令行标志会很快变得难以使用。上面的例子已经很长了，你还没有开始添加插件！

Rollup.js 可以使用一个 [JavaScript 配置文件](https://rollupjs.org/guide/en/#configuration-files)来定义捆绑选项。默认名称是`rollup.config.js`，它应该放在项目的根目录下(通常是运行`rollup`的目录)。

该文件是一个 ES 模块，用于导出设置 Rollup.js 选项的默认对象。以下代码复制了上面使用的命令:

```
// rollup.config.js

export default {

  input: './src/main.js',

  output: {
    file: './build/bundle.js',
    format: 'iife',
    sourcemap: true
  }

} 
```

*注意:`sourcemap: true`定义了一个外部源映射。使用`sourcemap: 'inline'`作为内嵌源地图。*

通过设置`--config`(或`-c`)标志，可以在运行`rollup`时使用该配置文件:

```
npx rollup --config 
```

如果您将配置命名为除`rollup.config.js`之外的名称，则可以传递文件名。当您有可能位于一个`config`目录中的多个配置时，这可能是实用的。例如:

```
npx rollup --config ./config/rollup.simple.js 
```

### 自动捆绑

`watch`选项可以在配置文件中设置。例如:

```
// rollup.config.js

export default {

  input: './src/main.js',

  watch: {
    include: './src/**',
    clearScreen: false
  },

  output: {
    file: './build/bundle.js',
    format: 'iife',
    sourcemap: true
  }

} 
```

但是，在调用`rollup`时，仍然需要添加一个`--watch`标志:

```
npx rollup --config --watch 
```

### 处理多个包

上面的配置文件返回一个对象来处理一个输入文件及其依赖项。您还可以返回一个对象数组来定义多个输入和输出操作:

```
// rollup.config.js

export default [

  {

    input: './src/main.js',

    output: {
      file: './build/bundle.js',
      format: 'iife',
      sourcemap: true
    }

  },

  {

    input: './src/another.js',

    output: {
      file: './build/another.js',
      format: 'es'
    }

  },

] 
```

即使返回单个对象，定义一个数组也是可行的。这将使以后追加进一步的流程变得更加容易。

### 使用环境变量

配置文件是 JavaScript，因此可以根据任何环境因素更改设置。例如，在开发机器或生产服务器上运行时，您可能希望脚本捆绑稍有不同。

以下配置检测到`NODE_ENV`环境变量，并在它被设置为`production`时删除源地图:

```
// Rollup.js development and production configurations
const dev = (process.env.NODE_ENV !== 'production');

console.log(`running in ${ dev ? 'development' : 'production' } mode`);

const sourcemap = dev ? 'inline' : false;

export default [

  {

    input: './src/main.js',

    watch: {
      clearScreen: false
    },

    output: {
      file: './build/bundle.js',
      format: 'iife',
      sourcemap
    }

  }

] 
```

在 macOS 或 Linux 上，可以从命令行设置`NODE_ENV`的值:

```
NODE_ENV=production 
```

这是 Windows 的`cmd`提示符:

```
set NODE_ENV=production 
```

对于 Windows Powershell:

```
$env:NODE_ENV="production" 
```

但是，Rollup.js 也允许您临时设置/覆盖`--environment`标志中的环境变量。例如:

```
npx rollup --config --environment VAR1,VAR2:value2,VAR3:x 
```

`process.env`然后可以在您的配置文件中检查:

*   `process.env.VAR1`是`true`
*   `process.env.VAR2`是`value2`
*   `process.env.VAR3`是`x`

上面的配置脚本默认为开发模式，但是生产模式(没有源地图)可以通过以下方式触发:

```
npx rollup --config --environment NODE_ENV:production 
```

## Rollup.js 插件

Rollup.js 有大量的插件来补充捆绑和输出过程。您将找到各种选项来注入代码、编译 TypeScript、lint 文件，甚至触发 HTML 和 CSS 处理。

使用插件类似于其他 Node.js 项目。您必须安装插件模块，然后在 Rollup.js 配置文件中的一个`plugin`数组中引用它。以下部分描述了几个最常用的插件。

### 使用 npm 模块

许多 JavaScript 库被打包成 CommonJS 模块，可以使用`npm`安装。Rollup.js 可以通过以下插件将此类脚本包含在包中:

1.  [`node-resolve`](https://github.com/rollup/plugins/tree/master/packages/node-resolve) ，定位项目的`node_modules`目录下的模块
2.  [`plugin-commonjs`](https://github.com/rollup/plugins/tree/master/packages/commonjs) ，必要时将 CommonJS 模块转换为 ES6。

将它们安装到您的项目中:

```
npm install @rollup/plugin-node-resolve @rollup/plugin-commonjs --save-dev 
```

您可以添加一个更全面的日期/时间处理库，比如 [day.js](https://day.js.org/) ，而不是使用`src/lib/time.js`中的时间格式化函数。用`npm`安装:

```
npm install dayjs --save-dev 
```

然后相应地修改`src/main.js`:

```
import * as dom from './lib/dom.js';
import dayjs from 'dayjs';

// get clock element
const clock = dom.get('.clock');

if (clock) {

  console.log('initializing clock');

  setInterval(() => {

    clock.textContent = dayjs().format('HH:mm:ss');

  }, 1000);

} 
```

`rollup.config.js`必须更新，以便在新的`plugins`数组中包含和使用插件:

```
// Rollup.js with npm modules
import { nodeResolve as resolve } from '@rollup/plugin-node-resolve';
import commonjs from '@rollup/plugin-commonjs';

const
  dev = (process.env.NODE_ENV !== 'production'),
  sourcemap = dev ? 'inline' : false;

console.log(`running in ${ dev ? 'development' : 'production' } mode`);

export default [

  {

    input: './src/main.js',

    watch: {
      clearScreen: false
    },

    plugins: [
      resolve({
        browser: true
      }),
      commonjs()
    ],

    output: {
      file: './build/bundle.js',
      format: 'iife',
      sourcemap
    }

  }

]; 
```

像以前一样运行 Rollup.js:

```
npx rollup --config 
```

你现在会发现`day.js`代码已经包含在`build/bundle.js`中。

一旦你对它的工作感到满意，就将`src/main.js`恢复到原来的本地代码库，因为在下面的章节中会用到它。你的`rollup.config.js`不需要换。

### 替换令牌

在构建时传递配置变量通常很有用，这样它们就被硬编码在捆绑的脚本中了。例如，您可以创建一个带有设计标记的 JSON 文件，这些标记指定了颜色、字体、间距、选择器或任何其他可以应用于 HTML、CSS 或 JavaScript 的调整。

Rollup.js [替换插件](https://github.com/rollup/plugins/tree/master/packages/replace)可以替换脚本中的任何引用。安装时使用:

```
npm install @rollup/plugin-replace --save-dev 
```

修改`rollup.config.js`以导入插件并定义一个`tokens`对象，该对象被传递给`plugins`数组中的`replace()`函数。在本例中，您可以修改时钟选择器(`__CLOCKSELECTOR__`)、更新时间间隔(`__CLOCKINTERVAL__`)和时间格式化功能(`__CLOCKFORMAT__`):

```
// Rollup.js configuration
import { nodeResolve as resolve } from '@rollup/plugin-node-resolve';
import commonjs from '@rollup/plugin-commonjs';
import replace from '@rollup/plugin-replace';

const
  dev = (process.env.NODE_ENV !== 'production'),
  sourcemap = dev ? 'inline' : false,

  // web design token replacements
  tokens = {
    __CLOCKSELECTOR__: '.clock',
    __CLOCKINTERVAL__: 1000,
    __CLOCKFORMAT__: 'formatHMS'
  };

console.log(`running in ${ dev ? 'development' : 'production' } mode`);

export default [

  {

    input: './src/main.js',

    watch: {
      clearScreen: false
    },

    plugins: [
      replace(tokens),
      resolve({
        browser: true
      }),
      commonjs()
    ],

    output: {
      file: './build/bundle.js',
      format: 'iife',
      sourcemap
    }

  }

]; 
```

`src/main.js`必须修改才能使用这些令牌。替换字符串可以应用于任何地方——甚至作为函数名或`import`引用:

```
import * as dom from './lib/dom.js';
import { __CLOCKFORMAT__ } from './lib/time.js';

// get clock element
const clock = dom.get('__CLOCKSELECTOR__');

if (clock) {

  console.log('initializing clock');

  setInterval(() => {

    clock.textContent = __CLOCKFORMAT__();

  }, __CLOCKINTERVAL__);

} 
```

运行`npx rollup --config`你会发现`build/bundle.js`和之前的是一样的，但是现在可以通过改变 Rollup.js 配置文件来修改。

### 过渡到 ES5

现代 JavaScript 可以在现代浏览器中运行。不幸的是，这不包括 IE11 等较老的应用程序。许多开发者使用像 [Babel](https://babeljs.io/) 这样的解决方案将 ES6 转换成向后兼容的 ES5。

我对创建 ES5 包有着复杂的感觉:

1.  2020 年 12 月， [IE11 的市场份额不足 1%](https://gs.statcounter.com/browser-version-partially-combined-market-share/desktop-mobile-tablet/worldwide/#monthly-202012-202012-bar) 。包容性很大，但是专注于可访问性和性能比十年前的浏览器更有益吗？
2.  如果采用渐进式增强，可以支持传统浏览器。这些浏览器可能不运行任何 JavaScript，但该网站仍然可以提供一定程度的 HTML 和 CSS 功能。
3.  ES5 包可能比 ES6 大得多。现代浏览器应该接收效率较低的脚本吗？

展望未来，我建议你只捆绑 ES6，让旧的(较慢的)浏览器只依赖 HTML 和 CSS。这并不总是可能的——比如当你有一个包含大量 IE11 用户的复杂应用程序时。在这些情况下，考虑创建 ES6 和 ES5 包，并提供合适的脚本。

Rollup.js 提供了一个[插件](https://github.com/rollup/plugins/tree/master/packages/buble)，它使用 [Bublé](https://github.com/bublejs/buble) 来转换到 ES5。该项目处于维护模式，但仍然运行良好。

*注意:这里有一段来自项目资源库的引用:“Bublé是在 ES2015 还是未来的时候创建的。如今，所有现代浏览器都支持所有 ES2015 以及(在某些情况下)更高版本。除非你需要支持 IE11，否则你很可能不需要用 Bublé把你的代码转换成 ES5。”*

安装插件，这样您就可以输出 ES6 和 ES5 模块:

```
npm install @rollup/plugin-buble --save-dev 
```

在修改配置之前，`src/lib/time.js`中使用的字符串`padStart()`函数在旧版浏览器中没有实现。通过将以下代码添加到一个新的`src/lib/polyfill.js`文件中，可以使用一个简单的聚合填充:

```
// String.padStart polyfill
if (!String.prototype.padStart) {

  String.prototype.padStart = function padStart(len, str) {

    var t = String(this);
    len = len || 0;
    str = str || ' ';
    while (t.length < len) t = str + t;
    return t;

  };

} 
```

ES6 中不需要这种聚合填充，所以您只需要一种方法将其注入到 ES5 代码中。幸运的是，您已经安装了[替换插件](#replace-tokens),因此它可以用于该任务。

在`src/main.js`的顶部添加一个`__POLYFILL__`令牌:

```
__POLYFILL__
import * as dom from './lib/dom.js';
import { __CLOCKFORMAT__ } from './lib/time.js';

// rest of code... 
```

然后在 ES5 `"plugins"`数组的 Rollup.js 配置中设置它:

```
// Rollup.js configuration
import replace from '@rollup/plugin-replace';
import { nodeResolve as resolve } from '@rollup/plugin-node-resolve';
import commonjs from '@rollup/plugin-commonjs';
import buble from '@rollup/plugin-buble';

// settings
const
  dev = (process.env.NODE_ENV !== 'production'),
  sourcemap = dev ? 'inline' : false,

  input = './src/main.js',

  watch = { clearScreen: false },

  tokens = {
    __CLOCKSELECTOR__: '.clock',
    __CLOCKINTERVAL__: 1000,
    __CLOCKFORMAT__: 'formatHMS'
  };

console.log(`running in ${ dev ? 'development' : 'production' } mode`);

export default [

  {
    // ES6 output
    input,
    watch,

    plugins: [
      replace({
        ...tokens,
        __POLYFILL__: '' // no polyfill for ES6
      }),
      resolve({ browser: true }),
      commonjs()
    ],

    output: {
      file: './build/bundle.mjs',
      format: 'iife',
      sourcemap
    }

  },

  {
    // ES5 output
    input,
    watch,

    plugins: [
      replace({
        ...tokens,
        __POLYFILL__: "import './lib/polyfill.js';" // ES5 polyfill
      }),
      resolve({ browser: true }),
      commonjs(),
      buble()
    ],

    output: {
      file: './build/bundle.js',
      format: 'iife',
      sourcemap
    }

  }

]; 
```

运行`npx rollup --config`来构建 ES6 `build/bundle.mjs`和 ES5 `build/bundle.js`脚本。必须相应地更改 HTML 文件:

```
<script type="module" src="./build/bundle.mjs"></script>
<script nomodule src="./build/bundle.js" defer></script> 
```

现代浏览器会加载并运行`./build/bundle.mjs`中包含的 ES6。旧的浏览器会加载并运行包含在`./build/bundle.js`中的 ES5(加上 polyfill)脚本。

### 用巴别塔传送

Bublé更容易、更快、更简单，但是如果您需要特定的选项，可以使用 Babel。安装以下插件:

```
npm install @rollup/plugin-babel @babel/core @babel/preset-env --save-dev 
```

然后将 Babel 包含在您的配置文件中:

```
import { getBabelOutputPlugin } from '@rollup/plugin-babel'; 
```

然后将这段代码添加到您的`plugins`数组中:

```
 plugins: [
      getBabelOutputPlugin({
        presets: ['@babel/preset-env']
      })
    ], 
```

运行前还必须将`output.format`更改为`es`或`cjs`。

### 缩小输出

神话般的 [Terser](https://terser.org/) minifier 可以通过优化语句和删除空白、注释和其他不必要的字符来压缩代码。结果可能是戏剧性的。即使在这个小例子中，Rollup.js 输出(已经创建了一个更小的包)还可以减少 60%。

用以下代码安装 Rollup.js [Terser 插件](https://github.com/TrySound/rollup-plugin-terser):

```
npm install rollup-plugin-terser --save-dev 
```

然后将其导入 Rollup.js 配置文件的顶部:

```
import { terser } from 'rollup-plugin-terser'; 
```

Terser 是一个输出插件，在 Rollup.js 完成其主要捆绑任务后进行处理。因此，`terser()`选项被定义在`output`对象内的`plugins`数组*中。最终配置文件:*

```
// Rollup.js configuration
import replace from '@rollup/plugin-replace';
import { nodeResolve as resolve } from '@rollup/plugin-node-resolve';
import commonjs from '@rollup/plugin-commonjs';
import buble from '@rollup/plugin-buble';
import { terser } from 'rollup-plugin-terser';

// settings
const
  dev = (process.env.NODE_ENV !== 'production'),
  sourcemap = dev ? 'inline' : false,

  input = './src/main-replace.js',

  watch = { clearScreen: false },

  tokens = {
    __CLOCKSELECTOR__: '.clock',
    __CLOCKINTERVAL__: 1000,
    __CLOCKFORMAT__: 'formatHMS'
  };

console.log(`running in ${ dev ? 'development' : 'production' } mode`);

export default [

  {
    // ES6 output
    input,
    watch,

    plugins: [
      replace({
        ...tokens,
        __POLYFILL__: '' // no polyfill for ES6
      }),
      resolve({ browser: true }),
      commonjs()
    ],

    output: {
      file: './build/bundle.mjs',
      format: 'iife',
      sourcemap,
      plugins: [
        terser({
          ecma: 2018,
          mangle: { toplevel: true },
          compress: {
            module: true,
            toplevel: true,
            unsafe_arrows: true,
            drop_console: !dev,
            drop_debugger: !dev
          },
          output: { quote_style: 1 }
        })
      ]
    }

  },

  {
    // ES5 output
    input,
    watch,

    plugins: [
      replace({
        ...tokens,
        __POLYFILL__: "import './lib/polyfill.js';" // ES5 polyfill
      }),
      resolve({ browser: true }),
      commonjs(),
      buble()
    ],

    output: {
      file: './build/bundle.js',
      format: 'iife',
      sourcemap,
      plugins: [
        terser({
          ecma: 2015,
          mangle: { toplevel: true },
          compress: {
            toplevel: true,
            drop_console: !dev,
            drop_debugger: !dev
          },
          output: { quote_style: 1 }
        })
      ]
    }

  }

]; 
```

ES5 和 ES6 的 Terser 配置不同，主要是针对 ECMAScript 标准的不同版本。在这两种情况下，当`NODE_ENV`环境变量被设置为`production`时，`console`和`debugger`语句被删除。

因此，最终的生产版本可以通过以下方式创建:

```
npx rollup --config --environment NODE_ENV:production 
```

产生的文件大小:

*   ES6 `./build/bundle.mjs`:来自 766 字节的原始包的 294 字节(减少了 62%)
*   ES5 `./build/bundle.js`:从 1，131 字节的原始包中取出 485 字节(减少了 57%)

## Rollup.js 的后续步骤

除了上面的命令行选项之外，很少有开发人员需要冒险，但是 Rollup.js 还有其他一些技巧…

### Rollup.js JavaScript API

可以使用 [Rollup.js JavaScript API](https://rollupjs.org/guide/en/#javascript-api) 从 Node.js 代码中触发绑定。API 使用与配置文件类似的参数，因此您可以创建一个异步函数来处理绑定。这可以用在 [Gulp.js 任务](https://rollupjs.org/guide/en/#gulp)或任何其他进程中:

```
const rollup = require('rollup');

async function build() {

  // create a bundle
  const bundle = await rollup.rollup({
    // input options
  });

  // generate output code in-memory
  const { output } = await bundle.generate({
    // output options
  });

  // write bundle to disk
  await bundle.write({
    // output options
  });

  // finish
  await bundle.close();
}

// start build
build(); 
```

或者，当源文件被修改时，您可以使用一个 [`rollup.watch()`函数](https://rollupjs.org/guide/en/#rollupwatch)来触发处理函数。

### 创建自己的 Rollup.js 插件

Rollup.js 提供了[许多插件](https://github.com/rollup/awesome)，但是你也可以[创建自己的](https://rollupjs.org/guide/en/#plugin-development)。所有插件都导出一个函数，这个函数是用 Rollup.js 配置文件中设置的特定于插件的选项调用的。该函数必须返回包含以下内容的对象:

1.  单个`name`属性
2.  多个[构建钩子函数](https://rollupjs.org/guide/en/#build-hooks)，如 [buildStart](https://rollupjs.org/guide/en/#buildstart) 或 [buildEnd](https://rollupjs.org/guide/en/#buildend) ，在特定的捆绑事件发生时被调用，和/或
3.  一些[输出生成钩子](https://rollupjs.org/guide/en/#output-generation-hooks)，比如 [renderStart](https://rollupjs.org/guide/en/#renderstart) 或者 [writeBundle](https://rollupjs.org/guide/en/#writebundle) ，它们在 Bundle 生成后被调用。

我建议导航到任何一个[插件](https://github.com/rollup/awesome)的 GitHub 库来检查它是如何工作的。

## 滚动革命

Rollup.js 的设置需要一点时间，但是最终的配置将适用于您的许多项目。如果你想要一个更快和更可配置的 JavaScript bundler，这是理想的。

快速链接:

*   [Rollup.js 文档](https://rollupjs.org)
*   [常见问题解答](https://rollupjs.org/guide/en/#faqs)
*   [快速入门教程](https://rollupjs.org/guide/en/#tutorial)
*   [Rollup.js 选项列表](https://rollupjs.org/guide/en/#big-list-of-options)
*   [Rollup.js 插件列表](https://github.com/rollup/awesome)

## 分享这篇文章