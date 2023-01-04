# 风格反应组件:7 种比较方式

> 原文：<https://www.sitepoint.com/react-components-styling-options/>

有多种方法可以对 React 组件进行样式化。选择正确的方法来设计组件并不是绝对的。这是一个特定的决定，应该服务于您的特定用例、个人偏好，尤其是您工作方式的架构目标。

例如，我使用 [Noty](https://blog.praveen.science/notifications-in-react-js-using-noty/) 在 React JS 中使用通知，样式也应该能够处理插件。

我回答这个问题的一些目标包括:

*   全局命名空间
*   属国
*   复用性
*   可量测性
*   死码消除

对于生产级工作，似乎有许多在行业中广泛使用的设计 React 组件的方式:

*   内嵌 CSS
*   普通 CSS
*   JS 库中的 CSS
*   CSS 模块
*   萨斯和 SCSS 公司
*   较少的
*   可造型的

对于每一种方法，我将查看依赖项的需求、难度以及该方法是否真的是一种好方法。

## 内嵌 CSS

*   依赖关系:**无**
*   难度:**简单**
*   方法:**最差**

我想没有人需要介绍内联 CSS。这是直接使用 HTML 或 [JSX](https://www.sitepoint.com/an-introduction-to-jsx/) 发送给元素的 CSS 样式。您可以在 React 组件中包含 CSS 的 JavaScript 对象，尽管有一些限制，例如对包含连字符的属性名进行大小写。您可以使用 JavaScript 对象以两种方式对 React 组件进行样式化，如示例所示。

### 例子

```
import React from "react";

const spanStyles = {
  color: "#fff",
  borderColor: "#00f"
};

const Button = props => (
  <button style={{
    color: "#fff",
    borderColor: "#00f"
  }}>  <span style={spanStyles}>Button Name</span>  </button>
); 
```

## 常规 CSS

*   依赖关系:**无**
*   难度:**简单**
*   方法:**好的**

常规 CSS 是一种常见的方法，可以说比内联 CSS 好一步。与直接应用于特定元素的内联 CSS 不同，样式可以导入到任意数量的页面和元素中。普通 CSS 有几个优点，比如原生浏览器支持(它不需要依赖)，不需要学习额外的工具，也没有被供应商锁定的危险。

您可以维护任意数量的样式表，并且在需要时可以更容易地更改或自定义样式。但是，如果你在从事一个有很多人参与的更大的项目，特别是在没有一个公认的 CSS 样式指南的情况下，常规的 CSS 可能是一个主要的问题。

### 例子

```
/* styles.css */

a:link {
  color: gray;
}
a:visited {
  color: green;
}
a:hover {
  color: rebeccapurple;
}
a:active {
  color: teal;
} 
```

```
import React from "react";
import "styles.css";

const Footer = () => (
  <footer> &copy; 2020 <a href="https://twitter.com/praveenscience">Find me on Twitter</a>  </footer>
);

export default Footer; 
```

### 更多信息

你可以在 W3C 的[学习 CSS](https://www.w3.org/Style/CSS/learning) 页面阅读更多关于 CSS 的常规用法。有很多游乐场——比如 [JS Bin](https://jsbin.com/?html,output) 、 [JSFiddle](https://jsfiddle.net/) 、 [CodePen](https://codepen.io/) 和[repl . it](https://repl.it/)——你可以在那里现场试用，并实时获得结果。

## CSS-in-JS

CSS-in-JS 是一种让您能够使用 JavaScript 来设计组件样式的技术。当这个 JavaScript 被解析时，CSS 被生成(通常作为一个`<style>`元素)并附加到 DOM 中。

这种方法有几个好处。例如，默认情况下，生成的 CSS 是有范围的，这意味着对组件样式的更改不会影响该组件之外的任何内容。这有助于防止样式表随着时间的推移而变得臃肿；如果删除一个组件，就会自动删除它的 CSS。

另一个优势是您可以利用 JavaScript 的力量与 CSS 进行交互。例如，您可以在 JavaScript 中创建自己的助手函数，并直接在 CSS 中使用它们来修改代码。

接下来，我们将看看两个可用于在 React 应用程序中实现这一点的库。

## JSS

*   **`react-jss`** 依赖关系
*   难度:**简单**
*   方法:**得体**

JSS 宣称自己是“CSS 的创作工具，它允许你用 JavaScript 以一种声明性的、无冲突的和可重用的方式来描述风格”。它是框架不可知的，但当涉及到设计 React 组件时， [React-JSS](https://cssinjs.org/react-jss/) 使用新的 Hooks API 集成了 JSS 和 React。

### 例子

```
import React from "react";
import {render} from "react-dom";
import injectSheet from "react-jss";

// Create your styles. Since React-JSS uses the default JSS preset,
// most plugins are available without further configuration needed.
const styles = {
  myButton: {
    color: "green",
    margin: {
      // jss-expand gives more readable syntax
      top: 5, // jss-default-unit makes this 5px
      right: 0,
      bottom: 0,
      left: "1rem"
    },
    "& span": {
      // jss-nested applies this to a child span
      fontWeight: "bold" // jss-camel-case turns this into 'font-weight'
    }
  },
  myLabel: {
    fontStyle: "italic"
  }
};

// Define the component using these styles and pass it the 'classes' prop.
const Button = ({ classes, children }) => (
  <button className={classes.myButton}>  <span className={classes.myLabel}>{children}</span>  </button>
);

// Finally, inject the stylesheet into the component.
const StyledButton = injectSheet(styles)(Button);

const App = () => <StyledButton>Submit</StyledButton>
render(<App />, document.getElementById('root')) 
```

### 更多信息

你可以在 JSS 官方文档中了解更多关于这种方法的信息。还有一种方法可以让[使用他们的 REPL(读取-评估-打印循环)来尝试一下](https://cssinjs.org/repl)。

## 样式组件

*   **`styled-components`** 依赖关系
*   难度:**中等**
*   方法:**得体**

[Styled-components](https://styled-components.com/) 是实现上述 CSS-in-JS 技术的另一个库。它利用带标签的模板文字——在两个反斜线之间包含实际的 CSS 代码——来设计组件的样式。这很好，因为您可以从另一个项目(或 Web 上的任何地方)复制/粘贴 CSS 代码，并让事情正常工作。不像其他一些库那样需要转换成 camel case 或 JS 对象语法。

Styled-components 还删除了组件和样式之间的映射。正如[在他们的文档](https://styled-components.com/docs/basics#getting-started)中读到的，这意味着当你定义你的风格时，你实际上是在创建一个普通的 React 组件，你的风格附加在它上面。这使得您的代码更简洁，更容易理解，因为您最终使用的是一个`<Layout>`组件，而不是一个类名为“layout”的`<div>`。

Props 可用于样式化组件，就像它们被传递给普通 React 组件一样。CSS 中使用 Props 代替类，动态设置属性。

### 例子

```
import React from "react";
import styled, { css } from "styled-components";

const Button = styled.button` cursor: pointer;
  background: transparent;
  font-size: 16px;
  border-radius: 3px;
  color: palevioletred;
  margin: 0 1em;
  padding: 0.25em 1em;
  transition: 0.5s all ease-out;
  ${props =>
    props.primary &&
    css` background-color: white;
      color: green;
      border-color: green; `}; `;

export default Button; 
```

### 更多信息

Styled-components 有详细的文档，该网站还提供了一个动态编辑器，您可以在其中试用代码。在 [styled-components: Basics](https://www.styled-components.com/docs/basics) 获取更多关于 styled-components 的信息。

### 样式组件备选方案

根据您的需求，还有许多其他 CSS-in-JS 库可以考虑。一些常见的例子包括:

*   [情感](https://emotion.sh/docs/introduction)比样式化组件[更小更快](https://github.com/jsjoeio/styled-components-vs-emotion)。如果你已经使用了样式化组件，你可能不需要急于改变库——它的维护者说它正在缩小差距。
*   Linaria 是一个受开发者欢迎的选择，他们希望最大化核心网站的重要分数。Linaria 的核心区别在于它是一个零运行时库——所有的 CSS-in-JS 都在构建期间提取到 CSS 文件中。

## CSS 模块

*   **`css-loader`** 依赖关系
*   难度:**难**(使用装载机配置)
*   方法:**更好的**

如果您曾经觉得 CSS 全局范围问题占用了您的大部分时间，当您必须找到一个特定的样式做什么时，或者如果清除 CSS 文件让您紧张地想知道您是否会破坏代码库中的其他地方，我理解您。

CSS 模块通过确保一个组件的所有样式都在一个地方并且只应用于那个特定的组件来解决这个问题。这当然解决了 CSS 的全局范围问题。它们的[组合特性](https://github.com/css-modules/css-modules#composition)就像一件武器，在你的应用程序中代表不同状态之间的共享风格。它们类似于 Sass 中的 mixins，这使得组合多组样式成为可能。

### 例子

```
import React from "react";
import style from "./panel.css";

const Panel = () => (
  <div className={style.panelDefault}>
    <div className={style.panelBody}>A Basic Panel</div>
  </div>
);

export default Panel; 
```

```
.panelDefault {
  border-color: #ddd;
}
.panelBody {
  padding: 15px;
} 
```

请注意，如果你使用 Create React App，[它支持现成的 CSS 模块](https://create-react-app.dev/docs/adding-a-css-modules-stylesheet/)。否则，您将需要 webpack 和一些使 webpack 能够捆绑 CSS 文件的加载器。Robin Wieruch 有一个关于这个的[很棒的教程。](https://www.robinwieruch.de/react-css-modules)

## 萨斯和 SCSS 公司

*   **`node-sass`** 依赖关系
*   难度:**简单**
*   方法:**最佳**

Sass 声称它是世界上最成熟、最稳定、最强大的专业级 CSS 扩展语言。它是一个 CSS 预处理器，在常规 CSS 中添加了变量、嵌套规则和混合(有时称为“语法糖”)等特殊功能。目的是使编码过程更简单、更有效。就像其他编程语言一样，Sass 允许使用变量、嵌套、部分、导入和函数，这为常规 CSS 增加了超级能力。

有许多方法可以导出 Sass 样式表并在 React 项目中使用。正如你所料， [Create React App 支持 Sass 开箱即用](https://create-react-app.dev/docs/adding-a-sass-stylesheet/)。如果你使用 webpack，你需要使用 [sass-loader](https://github.com/webpack-contrib/sass-loader) ，或者你可以只使用`sass --watch`命令。

在本文的最后，我们来看看如何使用 Sass 和 Create React App。

### 例子

```
$font-stack: 'Open Sans', sans-serif;
$primary-color: #333;

body {
  font: 100% $font-stack;
  color: $primary-color;
} 
```

### 更多信息

从 Sass 的官方文档中了解更多关于使用和安装各种编程语言的信息，Sass:语法上令人敬畏的样式表。如果你想尝试一些东西，有一种服务叫做[萨斯迈斯特——萨斯游乐场！](https://www.sassmeister.com/)在这里你可以体验萨斯和 SCSS 的不同特色。

## 较少的

*   依赖关系: **`less`，`less-loader`**
*   难度:**简单**
*   方法:**好**

[Less](http://lesscss.org/) (Leaner Style Sheets)是一种开源的、动态的预处理器样式表语言，可以编译成 CSS，在客户端或服务器端运行。它从 CSS 和 Sass 中获得灵感，类似于 SCSS。一些显著的区别包括变量在 Less 中以`@`符号开始，在 Sass 中以`$`符号开始。

### 例子

```
@pale-green-color: #4D926F;

#header {
  color: @pale-green-color;
}
h2 {
  color: @pale-green-color;
} 
```

### 更多信息

你可以从官方文档中的 Less T1 开始，还有 T2 Less tester，一个将你的 Less 代码转换成 CSS 的 Less 沙箱 T3。

## 可造型的

*   依赖关系: **`stylable`，`@stylable/webpack-plugin`**
*   难度:**难**
*   方法:**更好的**

如果你不是 CSS-in-JS 的最大粉丝，那么 [Stylable](https://stylable.io/) 可能适合你。它是一个预处理器，使你能够将样式应用于组件，这样它们就不会泄漏，也不会与应用程序中其他地方的样式冲突。它附带了几个方便的特性——比如定义自定义[伪类](https://stylable.io/docs/references/pseudo-classes)的能力——这样你就可以基于状态对组件应用样式。它也受到了 TypeScript 的启发，该项目的主页上写着:

> 我们想给 CSS 一个类型系统——为 CSS 做 TypeScript 为 JavaScript 做的事情。

当谈到集成 Stylable 和 React 时，他们提供了一个[方便的指南](https://stylable.io/docs/getting-started/react-integration)。还有 [create-stylable-app 项目](https://github.com/wix/stylable/tree/master/packages/create-stylable-app)，它将初始化一个基于 React 的 web 应用程序，并将 stylable 作为其样式解决方案。

### 例子

```
@namespace "Example1";

/* Every Stylable stylesheet has a reserved class called root
that matches the root node of the component. */
.root {
  -st-states: toggled, loading;
}
.root:toggled { color: red; }
.root:loading { color: green; }
.root:loading:toggled { color: blue; } 
```

```
/* CSS output*/
.Example1__root.Example1--toggled { color: red; }
.Example1__root.Example1--loading { color: green; }
.Example1__root.Example1--loading.Example1--toggled { color: blue; } 
```

### 更多信息

Stylable 提供了更多。关于[入门](https://stylable.io/docs/get-started)的官方文档提供了详细的解释。

## 弄脏我们的手

有这么多选择，我把手弄脏了，一个一个地尝试。多亏了 Git，我能够进行版本控制并比较所有东西，看看哪个选项是赢家。在处理复杂的样式时，我能够清楚地知道我使用了多少依赖项，以及我的工作流程是怎样的。除了普通的 CSS、CSS 模块和 SCSS 之外，我和其他东西都有一点斗争。

### 办公用字

CSS 模块有助于导入和其他事情，但当我尝试使用它时，没有太多的支持来扩展它。当我使用多个 CSS 类和层次结构时，CSS 模块出现了问题。它唯一的好处是你不会得到任何 CSS 冲突，但缺点是你的 CSS 代码极其庞大。有点像 BEM 方法论。

除此之外，处理伪元素和伪状态简直是地狱。有时，当我试图从不同的文件导入另一个类的内容时，CSS 模块甚至无法编译。我更喜欢在 SCSS 使用 mixins 来实现这个，但是不幸的是，CSS 模块在这个领域仍然是非常基础的。这里的`composes`关键词几乎从未对我起作用。这是我个人面临的巨大弊端。这可能是我的错，因为我没有正确地使用它，但它甚至对一个真正的案例都不起作用。

### 个人项目

我使用 SCSS 作为我的下一个尝试与反应风格。幸运的是，它起作用了。比较 SCSS 和 CSS 模块给了我很好的洞察力。他们之间有很多共同点。这里的一个问题是，我已经使用了 SCSS，我对它非常满意，因为它类似于 CSS，但具有超能力。在使用 Create React App 时，我唯一需要做的就是再安装一个依赖项`node-sass`，除此之外别无其他。不要触摸 webpack 配置或从创建 React 应用程序中弹出 React JS 应用程序。

看看 CSS 模块的威力，我对 SCSS 和 CSS 模块做了一个浅显的比较，发现 SCSS 的大部分功能我都有。谈到作曲——这是我的团队选择 CSS 模块的主要原因——我们可以在 SCSS 使用`@mixin`和`@include`,它们工作得非常好。我没有见过 CSS 模块与 JavaScript 交互，所以 SCSS 也是一样——没有与 JavaScript 部分交互。函数的循环和包含是 SCSS 独有的，所以我想和 SCSS 一起进行我的个人项目和我办公室的一个新项目。

## 获胜者是…

显然，SCSS 是这里的绝对赢家。与 CSS 模块相比，SCSS 提供了许多现成的新特性。下面是对 SCSS 的深入分析——它如何更好，以及为什么你应该在你的下一个项目中使用 SCSS。

### SCSS 赢了:深度分析

我喜欢 SCSS，因为它的特色。第一点是它和 CSS 非常相似。你不需要学习新的东西来理解 SCSS。如果你知道 CSS，你可能知道 Sass。Sass 带有[两种不同的语法](https://sass-lang.com/documentation/syntax) : Sass 本身和 SCSS，后者用得更多。SCSS 语法与 CSS 兼容，所以你只需要将你的`.css`文件重命名为`.scss`。当然，这样做你并没有使用 Sass 提供的任何超能力，但至少你意识到你不需要花很多时间来开始使用 Sass。从这个起点开始，您将能够学习 Sass 语法。

你可以前往 [Sass Basics](https://sass-lang.com/guide) 开始学习基础知识。在 React 中，为您的项目设置 Sass 支持并开始使用 SCSS 进行样式化非常简单。与任何 CSS 预处理程序一样，使用 SCSS 的下一个优势是使用变量的能力。变量允许您存储一个值或一组值，并在您的 Sass 文件中随时随地重用这些变量。简单、强大且有用。这有助于您的产品或应用程序主题化，并根据客户需求进行风格化，而不需要做太多工作，只需切换一些代码。

在 CSS(或 SCSS)中嵌套是 Sass 能够提供的最好的东西之一。Sass 允许您使用嵌套语法，即包含在另一段执行更广泛功能的代码中的代码。在 Sass 中，嵌套允许以更干净的方式定位元素。换句话说，你可以通过使用 [CSS 选择器](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors)来嵌套你的 HTML 元素。用 Sass 嵌套代码有很多好处，主要是 SCSS 的可维护性和可读性。我们都听说过代码中的 [DRY 概念](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)，但是在 CSS 中，这避免了多次重写选择器的需要。这也有助于更容易地维护代码。

使用偏音的能力很棒。你可以在任何地方将 SCSS 分割成片段，并在需要的地方包含它们。你也可以把它们分成[个 mixins](https://sass-lang.com/documentation/at-rules/mixin) 并传递一些参数来提供一个完全不同的 CSS。使用变量很好，但是如果样式表中有重复的代码块呢？这时混音就开始发挥作用了。Mixins 类似于其他编程语言中的函数。它们返回一个值或一组值，并可以接受包括默认值在内的参数。注意，Sass 也有[函数](https://sass-lang.com/documentation/at-rules/function)，所以不要混淆 mixin 和函数。

### 将 SCSS 与 React 一起使用

随着最近发布的升级版 [Create React App](https://scotch.io/tutorials/whats-new-in-create-react-app-2) ，我们有了很多新工具可以玩。Sass 是我很高兴能内置的一个工具，因为我们过去必须编译`.scss`文件，并将其写入文件夹结构中的`.css`文件。您可能会担心在 React 中使用 Sass。使用 CSS-in-JS 库编写样式，比如 [styled-components](https://www.styled-components.com/) 或 [aphrodite](https://github.com/Khan/aphrodite) ，难道不是一种更聪明的方式吗？相信加入 Sass 支持创建 React App 对 React 初学者会有很大的帮助。

以下是一些需要遵循的步骤:

1.  让我们从安装 Create React 应用程序开始。你可以通过全局运行`npm install -g create-react-app`或者使用`npx create-react-app`来下载并立即调用它，这样你的安装包就不会在你的全局中了。你可以[在这里](https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b)找到更多关于 npx 的信息。
2.  用`create-react-app <app-name>`创建一个新的 React 项目，然后切换到那个目录。
3.  使用`npm install node-sass --save`安装`node-sass`依赖项。这将把你的`scss`编译成`css`。
4.  就这样——我们结束了。我们可以通过将我们的`src/App.css`文件更改为`src/App.scss`文件并更新`src/App.js`来导入它来测试配置。然后我们可以尝试一些很酷的萨斯/SCSS 特色。

### 常见示例

这里有一种在 SCSS 使用变量的方法:

```
$blue: #004BB4;
$ubuntu-font: 'Ubuntu', 'Arial', 'Helvetica', sans-serif;
$nunito-font: 'Nunito', 'Arial', 'Helvetica', sans-serif; 
```

一旦创建了变量，您就可以在任何需要的地方使用它们，就像这样:

```
h1 {
  font: $ubuntu-font;
  color: $blue;
}
a {
  font: $nunito-font;
  background-color: $blue;
  padding: 6px;
} 
```

当你编译你的 SCSS 文件时，Sass 编译器会处理你在源文件中使用的变量，用它的存储值替换变量名。改变颜色的值就像更新变量内容和重新编译一样快。在你最喜欢的文本编辑器中使用“查找和替换”来改变 CSS 文件颜色的日子已经一去不复返了。

我之前提到的一个有价值的特性是 SCSS 的“嵌套”特性。这里可以展示一个例子:

```
<ul class="navbar">
  <li><a href="/">Item <span>1</span></a></li>
  <li><a href="/">Item <span>2</span></a></li>
  <li><a href="/">Item <span>3</span></a></li>
  <li><a href="/">Item <span>4</span></a></li>
  <li><a href="/">Item <span>5</span></a></li>
</ul> 
```

```
.navbar {
  font: $ubuntu-font;
  color: $blue;
  li {
    margin-left: 1rem;
    a {
      padding: 5px;
      font-size: 1.5rem;
      span {
        font-weight: 600;
      }
    }
  }
} 
```

但是，请注意嵌套太深不是好的做法。嵌套越深，Sass 文件就越冗长，编译后的 CSS 就越大，因为编译时嵌套是扁平的。因此，过度使用嵌套会创建非常具体的 CSS 规则，很难维护。选择器有可能无法重用，而且还有性能问题。嵌套的选择器将创建一个很长的 CSS 选择器字符串，最终将生成一个更大的 CSS 文件。

## 包扎

在本文中，我研究了在 React 应用程序中设计组件样式的几种方法。然后我比较和对比了这些方法，检查它们的优缺点。最后，我演示了如何在一个 Create React 应用程序项目中使用 Sass(我喜欢的设计 React 应用程序样式的方法)。

Sass 是一个 CSS 预处理器，CSS 预处理器将会一直存在。它们扩展了基本的 CSS 特性，为您提供了一组强大的功能，可以立即提高您的工作效率。我提到了一些好处，但还有更多，比如继承、函数、控制指令，以及像`if()`、`for()`或`while()`这样的表达式、数据类型、插值等等。

成为一名萨斯大师可能需要一点时间；你所需要做的就是查看引导 Sass 文件，看看 Sass 如何变成一件复杂的事情。但是学习基础知识并为你的项目做好准备是你今天就可以开始的事情。

## 分享这篇文章