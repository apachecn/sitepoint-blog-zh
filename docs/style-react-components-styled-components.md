# React 中的样式化:从外部 CSS 到样式化组件

> 原文：<https://www.sitepoint.com/style-react-components-styled-components/>

虽然使用 React 构建应用程序的许多方面已经在某种程度上实现了标准化，但样式化仍是一个存在许多竞争选项的领域。每一种都有其优点和缺点，没有明确的最佳选择。

在本文中，我将提供一个关于 React 组件的 web 应用程序样式进展的简要概述。我还会简单介绍一下[样式化组件](https://github.com/styled-components/styled-components)。

![A cosmetics kit with various 'styled-components'](img/3b1c3c3f8284b2cb5e8b567ede4674db.png)

## JavaScript 风格的演变

CSS 最初的发布是在 1996 年，从那以后没有太大的变化。在它的第三个主要版本中，第四个版本还在进行中，它通过添加新的特性继续发展，并保持了它作为基础 web 技术的声誉。CSS 将永远是 web 组件样式的黄金标准，但是它的使用方式每天都在变化。

从我们可以通过分割图像来构建网站的时代，到定制、手工制作的 CSS 可以反映图像的时代，CSS 实现的发展一直伴随着 JavaScript 和作为平台的 web 的发展。

自 2013 年 React 发布以来，组件构建的 web 应用程序已经成为规范。CSS 的实施也受到了质疑。反对在 React 中使用 CSS 的主要理由是关注点分离(SoC)。SoC 是一种设计原则，它描述了将一个程序分成几个部分，每个部分解决一个不同的问题。这一原则主要用于开发人员将三种主要的 web 技术保存在不同的文件中的时候:样式(CSS)、标记(HTML)和逻辑(JavaScript)。

随着 React 中 JSX 的引入，这种情况发生了变化。开发团队认为我们所做的实际上是技术的分离，而不是关注点。有人可能会问，既然 JSX 把标记移到了 JavaScript 代码中，为什么样式要分开呢？

与分离风格和逻辑相反，可以使用不同的方法来合并它们。这方面的一个例子如下:

```
<button style="background: red; border-radius: 8px; color: white;">Click Me</button> 
```

内嵌样式从 CSS 文件中移动 CSS 定义。这样就不需要导入文件，节省了带宽，但是牺牲了可读性、可维护性和样式继承性。

### CSS 模块

**button.css**

```
.button {
  background: red;
  border-radius: 8px;
  color: white;
} 
```

**button.js**

```
import styles from './button.css';
document.body.innerHTML = `<button class="${styles.button}">test</button>`; 
```

从上面的代码示例中我们可以看到，CSS 仍然存在于它自己的文件中。然而，当 [CSS 模块](https://github.com/css-modules/css-modules)与 Webpack 或其他现代捆绑器捆绑在一起时，CSS 会作为脚本标签添加到 HTML 文件中。类名也被散列以提供更细粒度的样式，解决了级联样式表带来的问题。

哈希过程包括生成唯一的字符串，而不是类名。拥有一个类名`btn`将会产生一个`DhtEg`的散列，这将会阻止样式层叠和对不需要的元素应用样式。

**index.html**

```
<style> .DhtEg {
  background: red;
  border-radius: 8px;
  color: white;
} </style>

…

<button class="DhtEg">test</button> 
```

从上面的例子中，我们可以看到 CSS 模块添加的样式标签元素，带有散列的类名和使用散列的 DOM 元素。

### 魅力

Glamor 是一个 CSS-in-JS 库，它允许我们在与 JavaScript 相同的文件中声明 CSS。Glamor 再次对类名进行了哈希处理，但提供了一种清晰的语法来通过 JavaScript 构建 CSS 样式表。

样式定义是通过一个 JavaScript 变量构建的，该变量使用 [camel case](https://en.wikipedia.org/wiki/Camel_case) 语法描述每个属性。使用 camel case 很重要，因为 CSS 定义了 *train case* 中的所有属性。主要的区别是属性名的改变。当从我们的应用程序或 CSS 示例的其他部分复制和粘贴 CSS 时，这可能是一个问题。例如，`overflow-y`将被改为`overFlowY`。然而，通过这一语法变化，Glamor 支持媒体查询和阴影元素，为我们的样式提供了更多功能:

**button.js**

```
import { css } from 'glamor';

const rules = css({
  background: red;
  borderRadius: 8px;
  color: 'white';
});

const button = () => {
  return <button {...rules}>Click Me</button>;
}; 
```

## 样式组件

styled-components 是一个新的库，专注于将 React 组件和样式放在一起。为 React 和 React 本地样式化组件提供一个简洁易用的界面，不仅改变了实现，也改变了构建样式化 React 组件的思维过程。

风格化组件可以通过以下方式从 npm 安装:

```
npm install styled-components 
```

作为任何标准 npm 包导入:

```
import styled from 'styled-components'; 
```

一旦安装完毕，是时候开始让风格化的 React 组件变得简单而有趣了。

## 构建通用风格的 React 组件

样式化的 React 组件可以通过多种方式构建。样式组件库提供了一些模式，使我们能够构建结构良好的 UI 应用程序。从小的 UI 组件——如按钮、输入、排版和标签——构建一个更加统一和一致的应用程序。

使用我们之前的按钮示例，我们可以使用样式化组件构建一个通用按钮:

```
const Button = styled.button` background: red;
  border-radius: 8px;
  color: white; `;

class Application extends React.Component {
  render() {
    return (
      <Button>Click Me</Button>
    )
  }
} 
```

[密码笔](http://codepen.io/SitePoint/pen/bWpoPO)

正如我们所看到的，我们能够创建我们的通用按钮，同时保持 CSS 与 JavaScript 一致。styled-components 提供了大量我们可以设计样式的元素。我们可以通过使用直接的元素引用或者将字符串传递给默认函数来做到这一点。

```
const Button = styled.button` background: red;
  border-radius: 8px;
  color: white; `;

const Paragraph = styled.p` background: green; `;

const inputBg = 'yellow';
const Input = styled.input` background: ${inputBg};
  color: black; `;

const Header = styled('h1')` background: #65a9d7;
  font-size: 26px; `

class Application extends React.Component {
  render() {
    return (
      <div>  <Button>Click Me</Button>  <Paragraph>Read ME</Paragraph>  <Input
          placeholder="Type in me"
        />  <Header>I'm a H1</Header>  </div>
    )
  }
} 
```

[密码笔](http://codepen.io/SitePoint/pen/NjNaQo)

这种样式方法的主要优点是能够编写纯 CSS。正如在 Glamor 示例中看到的，CSS 属性名必须改为 camel case，因为它们是 JavaScript 对象的属性。

styled-components 还生成 React 友好的原语，其行为类似于现有的元素。利用 JavaScript [模板文字](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Template_literals)允许我们使用 CSS 的全部能力来设计组件。如 input 元素示例所示，我们可以定义外部 JavaScript 变量，并将它们应用到我们的样式中。

有了这些简单的组件，我们可以轻松地为我们的应用程序构建一个样式指南。但在许多情况下，我们还需要更复杂的组件，这些组件可以根据外部因素而变化。

## 可定制风格的 React 组件

样式化组件的可定制性是真正的优势。这通常适用于需要根据上下文改变样式的按钮。在这种情况下，我们有两种按钮大小——小按钮和大按钮。下面是纯 CSS 方法:

**CSS**

```
button {
  background: red;
  border-radius: 8px;
  color: white;
}

.small {
  height: 40px;
  width: 80px;
}

.medium {
  height: 50px;
  width: 100px;
}

.large {
  height: 60px;
  width: 120px;
} 
```

**JavaScript**

```
class Application extends React.Component {
  render() {
    return (
      <div>  <button className="small">Click Me</button>  <button className="large">Click Me</button>  </div>
    )
  }
} 
```

[密码笔](http://codepen.io/SitePoint/pen/eWZeOp)

当我们使用 styled-components 重现这一点时，我们创建了一个按钮组件，它具有基本的默认背景样式。由于该组件的行为类似于 React 组件，因此我们可以利用 props 并相应地更改样式结果:

```
const Button = styled.button` background: red;
  border-radius: 8px;
  color: white;
  height: ${props => props.small ? 40 : 60}px;
  width: ${props => props.small ? 60 : 120}px; `;

class Application extends React.Component {
  render() {
    return (
      <div>  <Button small>Click Me</Button>  <Button large>Click Me</Button>  </div>
    )
  }
} 
```

[密码笔](http://codepen.io/SitePoint/pen/qmZVWm)

## 高级用法

styled-components 提供了创建复杂的高级组件的能力，我们可以使用现有的 JavaScript 模式来组成组件。下面的例子演示了组件是如何组成的——在通知消息的用例中，所有组件都遵循一种基本样式，但每种类型都有不同的背景颜色。我们可以构建一个基本的、风格化的组件，并在此基础上构建高级组件:

```
const BasicNotification = styled.p` background: lightblue;
  padding: 5px;
  margin: 5px;
  color: black; `;

const SuccessNotification = styled(BasicNotification)` background: lightgreen; `;

const ErrorNotification = styled(BasicNotification)` background: lightcoral;
  font-weight: bold; `;

class Application extends React.Component {
  render() {
    return (
      <div>  <BasicNotification>Basic Message</BasicNotification>  <SuccessNotification>Success Message</SuccessNotification>  <ErrorNotification>Error Message</ErrorNotification>  </div>
    )
  }
} 
```

[密码笔](https://codepen.io/SitePoint/pen/JNXOjW)

因为 styled-components 允许我们传递标准的 DOM 元素和其他组件，所以我们可以从基本组件组合高级特性。

## 组件结构

从我们的高级和基本示例中，我们可以构建一个组件结构。大多数标准的 React 应用程序都有一个组件目录:我们将样式化的组件放在一个`styledComponents`目录中。我们的`styledComponents`目录保存了所有的基本组件和组合组件。然后这些被导入到我们的应用程序所使用的显示组件中。目录布局如下所示:

```
src/
  components/
    addUser.js
    styledComponents/
      basicNotification.js
      successNotification.js
      errorNotification.js 
```

## 结论

正如我们在这篇文章中看到的，我们设计组件样式的方法千差万别——没有一种是明显的制胜方法。本文已经展示了样式化组件推动了样式化元素的实现，并引起了我们对我们的方法的思考过程的质疑。

包括我自己在内的每个开发人员都有自己喜欢的做事方式，了解根据我们正在开发的应用程序可以使用的不同方法是很好的。这些年来，样式系统和语言已经有了很大的进步，毫无疑问，它们将来肯定会进一步发展和变化。这是前端开发中一个非常令人兴奋和有趣的时代。

你喜欢用什么方式来设计 React 组件，为什么？

* * *

*这篇文章由[维尔丹·索蒂奇](https://www.sitepoint.com/author/vildansoftic/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

## 分享这篇文章