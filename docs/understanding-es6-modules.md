# 了解 ES6 模块

> 原文：<https://www.sitepoint.com/understanding-es6-modules/>

**这篇文章探索了 ES6 模块，展示了如何在移植程序的帮助下使用它们。**

几乎每种语言都有*模块*的概念——一种将一个文件中声明的功能包含在另一个文件中的方法。通常，开发人员会创建一个负责处理相关任务的封装代码库。该库可以被应用程序或其他模块引用。

好处:

1.  代码可以被分割成具有独立功能的较小文件。
2.  相同的模块可以在任意数量的应用程序之间共享。
3.  理想情况下，模块永远不需要被另一个开发人员检查，因为它们已经被证明是有效的。
4.  引用模块的代码知道它是一个依赖项。如果模块文件被更改或移动，问题会立即显现出来。
5.  模块代码(通常)有助于消除命名冲突。模块 1 中的函数`x()`不能与模块 2 中的函数`x()`冲突。采用了命名空间等选项，因此呼叫变成了`module1.x()`和`module2.x()`。

## JavaScript 中的模块在哪里？

几年前开始 web 开发的任何人都会震惊地发现 JavaScript 中没有模块的概念。不可能在另一个 JavaScript 文件中直接引用或包含一个 JavaScript 文件。开发商因此求助于替代方案。

### 多个 HTML `<script>`标签

HTML 可以使用多个`<script>`标签加载任意数量的 JavaScript 文件:

```
<script src="lib1.js"></script>
<script src="lib2.js"></script>
<script src="core.js"></script>
<script> console.log('inline code'); </script> 
```

2018 年的[平均网页使用 25 个独立的脚本](http://httparchive.org/trends.php#bytesJS&reqJS)，然而这并不是一个实用的解决方案:

*   每个脚本启动一个新的 HTTP 请求，这会影响页面性能。HTTP/2 在一定程度上缓解了这个问题，但是它对其他域(比如 CDN)上引用的脚本没有帮助。
*   每个脚本在运行时都会停止进一步的处理。
*   依赖性管理是一个手动过程。在上面的代码中，如果`lib1.js`引用了`lib2.js`中的代码，该代码将会失败，因为它还没有被加载。这可能会中断 JavaScript 的进一步处理。
*   除非使用适当的[模块模式](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#designpatternsjavascript)，否则函数可以覆盖其他函数。早期的 JavaScript 库因使用全局函数名或覆盖本地方法而臭名昭著。

### 脚本连接

多个`<script>`标签问题的一个解决方案是将所有 JavaScript 文件连接成一个大文件。这解决了一些性能和依赖性管理问题，但是可能会导致手动构建和测试步骤。

### 模块加载器

像 [RequireJS](http://requirejs.org/) 和 [SystemJS](https://github.com/systemjs/systemjs) 这样的系统提供了一个库，用于在运行时加载和命名其他 JavaScript 库。需要时，使用 Ajax 方法加载模块。这些系统是有帮助的，但是对于更大的代码库或者添加标准`<script>`标签的网站来说会变得复杂。

### 模块捆扎机、预处理机和传输机

捆绑器引入了一个编译步骤，因此 JavaScript 代码是在构建时生成的。代码被处理成包含依赖关系，并产生一个单一的 ES5 跨浏览器兼容的连接文件。受欢迎的选项包括[巴别塔](https://babeljs.io/)、[浏览器](http://browserify.org/)、[网络包](https://webpack.github.io/)以及更一般的任务运行器，如[咕噜](https://gruntjs.com/)和[大口](https://gulpjs.com/)。

JavaScript 构建过程需要一些努力，但也有好处:

*   处理是自动化的，因此人为错误的可能性更小。
*   进一步的处理可以 lint 代码，删除调试命令，缩小结果文件等。
*   Transpiling 允许你使用其他的语法，比如 [TypeScript](https://www.typescriptlang.org/) 或者 [CoffeeScript](http://coffeescript.org/) 。

## ES6 模块

上面的选项引入了各种竞争性的模块定义格式。广泛采用的语法包括:

*   CommonJS—node . js 中使用的`module.exports`和`require`语法
*   异步模块定义(AMD)
*   通用模块定义(UMD)。

因此，在 ES6 (ES2015)中提出了单一的本地模块标准。

默认情况下，ES6 模块中的所有东西都是私有的，并且以严格模式运行(不需要`'use strict'`)。使用`export`公开公共变量、函数和类。例如:

```
// lib.js
export const PI = 3.1415926;

export function sum(...args) {
  log('sum', args);
  return args.reduce((num, tot) => tot + num);
}

export function mult(...args) {
  log('mult', args);
  return args.reduce((num, tot) => tot * num);
}

// private function
function log(...msg) {
  console.log(...msg);
} 
```

或者，可以使用单个`export`语句。例如:

```
// lib.js
const PI = 3.1415926;

function sum(...args) {
  log('sum', args);
  return args.reduce((num, tot) => tot + num);
}

function mult(...args) {
  log('mult', args);
  return args.reduce((num, tot) => tot * num);
}

// private function
function log(...msg) {
  console.log(...msg);
}

export { PI, sum, mult }; 
```

`import`用于将项目从一个模块拉至另一个脚本或模块:

```
// main.js
import { sum } from './lib.js';

console.log( sum(1,2,3,4) ); // 10 
```

在这种情况下，`lib.js`与`main.js`在同一个文件夹中。可以使用绝对文件引用(以`/`开头)、相对文件引用(以`./`或`../`开头)或完整的 URL。

一次可以导入多个项目:

```
import { sum, mult } from './lib.js';

console.log( sum(1,2,3,4) );  // 10
console.log( mult(1,2,3,4) ); // 24 
```

并且导入可以有别名以解决命名冲突:

```
import { sum as addAll, mult as multiplyAll } from './lib.js';

console.log( addAll(1,2,3,4) );      // 10
console.log( multiplyAll(1,2,3,4) ); // 24 
```

最后，所有公共项都可以通过提供名称空间来导入:

```
import * as lib from './lib.js';

console.log( lib.PI );            // 3.1415926
console.log( lib.add(1,2,3,4) );  // 10
console.log( lib.mult(1,2,3,4) ); // 24 
```

## 在浏览器中使用 ES6 模块

在撰写本文时，基于 Chromium 的浏览器(v63+)、Safari 11+和 Edge 16+支持 [ES6 模块](https://caniuse.com/#feat=es6-module)。火狐支持将在 60 版本中到来(在 v58+中它在一个`about:config`标志后面)。

使用模块的脚本必须通过在`<script>`标签中设置一个`type="module"`属性来加载。例如:

```
<script type="module" src="./main.js"></script> 
```

或内嵌:

```
<script type="module"> import { something } from './somewhere.js';
  // ... </script> 
```

模块被解析一次，不管它们在页面或其他模块中被引用了多少次。

### 服务器注意事项

模块必须使用 MIME 类型`application/javascript`。大多数服务器会自动这样做，但是要警惕动态生成的脚本或`.mjs`文件([参见](#usinges6modulesinnodejs)下面的 Node.js 部分)。

常规的`<script>`标签可以获取其他域上的脚本，但是模块是使用跨源资源共享(CORS)来获取的。因此，不同域上的模块必须设置一个适当的 HTTP 头，比如`Access-Control-Allow-Origin: *`。

最后，模块不会发送 cookies 或其他头部凭证，除非在标签`<script>`中添加了一个`crossorigin="use-credentials"`属性，并且响应包含头部`Access-Control-Allow-Credentials: true`。

### 模块执行被推迟

属性延迟脚本的执行，直到文档被加载和解析。模块— *包括内联脚本* —默认情况下延迟。示例:

```
<!-- runs SECOND -->
<script type="module"> // do something... </script>

<!-- runs THIRD -->
<script defer src="c.js"></script>

<!-- runs FIRST -->
<script src="a.js"></script>

<!-- runs FOURTH -->
<script type="module" src="b.js"></script> 
```

### 模块退回

没有模块支持的浏览器不会运行`type="module"`脚本。可以为回退脚本提供一个与模块兼容的浏览器忽略的`nomodule`属性。例如:

```
<script type="module" src="runs-if-module-supported.js"></script>
<script nomodule src="runs-if-module-not-supported.js"></script> 
```

### 你应该在浏览器中使用模块吗？

浏览器支持正在增长，但是切换到 ES6 模块可能还为时过早。目前，最好使用模块捆绑器来创建一个在任何地方都适用的脚本。

## 在 Node.js 中使用 ES6 模块

当 Node.js 在 2009 年发布时，任何运行时不提供模块都是不可想象的。采用了 CommonJS，这意味着可以开发节点包管理器 npm。从那时起，使用率呈指数级增长。

CommonJS 模块的编码方式与 ES2015 模块类似。使用`module.exports`而不是`export`:

```
// lib.js
const PI = 3.1415926;

function sum(...args) {
  log('sum', args);
  return args.reduce((num, tot) => tot + num);
}

function mult(...args) {
  log('mult', args);
  return args.reduce((num, tot) => tot * num);
}

// private function
function log(...msg) {
  console.log(...msg);
}

module.exports = { PI, sum, mult }; 
```

`require`(而不是`import`)用于将此模块拉入另一个脚本或模块:

```
const { sum, mult } = require('./lib.js');

console.log( sum(1,2,3,4) );  // 10
console.log( mult(1,2,3,4) ); // 24 
```

`require`也可以导入所有物品:

```
const lib = require('./lib.js');

console.log( lib.PI );            // 3.1415926
console.log( lib.add(1,2,3,4) );  // 10
console.log( lib.mult(1,2,3,4) ); // 24 
```

所以 ES6 模块很容易在 Node.js 中实现，对吗？*呃，没有*。

ES6 模块[在 Node.js 9.8.0+](https://nodejs.org/api/esm.html) 中的一个标志之后，至少在版本 10 之前不会完全实现。虽然 CommonJS 和 ES6 模块共享相似的语法，但它们以完全不同的方式工作:

*   ES6 模块是预解析的，以便在执行代码之前解析进一步的导入。
*   CommonJS 模块在执行代码时按需加载依赖项。

在上面的示例中，这没有什么不同，但请考虑以下 ES2015 模块代码:

```
// ES2015 modules

// ---------------------------------
// one.js
console.log('running one.js');
import { hello } from './two.js';
console.log(hello);

// ---------------------------------
// two.js
console.log('running two.js');
export const hello = 'Hello from two.js'; 
```

ES2015 的输出:

```
running two.js
running one.js
hello from two.js 
```

使用 CommonJS 编写的类似代码:

```
// CommonJS modules

// ---------------------------------
// one.js
console.log('running one.js');
const hello = require('./two.js');
console.log(hello);

// ---------------------------------
// two.js
console.log('running two.js');
module.exports = 'Hello from two.js'; 
```

CommonJS 的输出:

```
running one.js
running two.js
hello from two.js 
```

执行顺序在某些应用中可能很关键，如果 ES2015 和 CommonJS 模块混合在同一个文件中会发生什么？为了解决这个问题，Node.js 将只允许扩展名为`.mjs`的文件中包含 ES6 模块。扩展名为`.js`的文件将默认为 CommonJS。这是一个简单的选择，它消除了很多复杂性，应该有助于代码编辑器和 linters。

### 应该在 Node.js 中使用 ES6 模块吗？

ES6 模块只从 Node.js v10 开始实用(2018 年 4 月发布)。转换现有项目不太可能带来任何好处，并且会使应用程序与 Node.js 的早期版本不兼容。

对于新项目，ES6 模块提供了 CommonJS 的替代方案。语法与客户端编码相同，并且可以提供更容易的途径来实现同构 JavaScript，这种 JavaScript 可以在浏览器或服务器上运行。

## 模块混战

一个标准化的 JavaScript 模块系统花了很多年才实现，甚至花了更长的时间，但是问题已经得到了解决。从 2018 年年中开始，所有主流浏览器和 Node.js 都支持 ES6 模块，尽管在每个人升级时应该会有切换滞后。

今天学习 ES6 模块，明天受益于您的 JavaScript 开发。

## 分享这篇文章