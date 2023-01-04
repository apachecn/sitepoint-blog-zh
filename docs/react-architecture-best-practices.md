# 5 2023 年 React 架构最佳实践

> 原文：<https://www.sitepoint.com/react-architecture-best-practices/>

毫无疑问，React 彻底改变了我们构建用户界面的方式。它易于学习，极大地方便了创建可重用的组件，为您的站点提供一致的外观和感觉。

然而，由于 [React](https://www.sitepoint.com/getting-started-react-beginners-guide/) 只负责应用程序的视图层，它并不强制任何特定的架构(如 MVC 或 MVVM)。随着 React 项目的增长，这可能会使您难以保持代码库的有序性。

在 [9elements](https://9elements.com/) ，我们的旗舰产品之一是[photo editorsdk](https://www.photoeditorsdk.com/?utm_source=Sitepoint&utm_medium=article&utm_campaign=sitepoint-react-best-practices-2021)——一个完全可定制的照片编辑器，可以轻松集成到你的 HTML5、iOS 或 Android 应用程序中。PhotoEditorSDK 是一款面向开发者的大型 React app。它需要高性能，小构建，并需要非常灵活的样式，尤其是主题。

在 PhotoEditorSDK 的多次迭代中，我和我的团队获得了许多组织大型 React 应用程序的最佳实践，我们希望在本文中与您分享其中的一些实践:

1.  [目录布局](#1directorylayout)
2.  [JavaScript 中的 CSS](#2cssinjavascript)
3.  [自定义挂钩](#3customhooks)
4.  [*功能为子*模式](#4functionaschildren)
5.  [渲染道具](#5renderprops)

## 1.目录布局

最初，组件的样式和代码是分开的。所有的样式都存在于一个共享的 CSS 文件中(我们使用 SCSS 进行预处理)。实际组件(在本例中为`FilterSlider`)与样式分离:

```
├── components
│   └── FilterSlider
│       ├──  __tests__
│       │   └── FilterSlider-test.js
│       └── FilterSlider.jsx
└── styles
    └── photo-editor-sdk.scss 
```

经过多次重构，我们发现这种方法不能很好地伸缩。在未来，我们的组件将需要在多个内部项目之间共享，比如 SDK 和我们目前正在开发的一个实验性文本工具。所以我们转向以组件为中心的文件布局:

```
components
    └── FilterSlider
        ├── __tests__
        │   └── FilterSlider-test.js
        ├── FilterSlider.jsx
        └── FilterSlider.scss 
```

这个想法是，属于一个组件的所有代码(比如 JavaScript、CSS、assets、tests)都位于一个文件夹中。这使得将代码提取到一个 npm 模块中变得非常容易，或者，如果您赶时间，可以简单地与另一个项目共享该文件夹。

### 导入组件

这种目录结构的缺点之一是，导入组件需要您导入完全限定的路径，如下所示:

```
import FilterSlider from 'components/FilterSlider/FilterSlider' 
```

但是我们真正想写的是:

```
import FilterSlider from 'components/FilterSlider' 
```

要解决这个问题，您可以创建一个`index.js`并立即导出缺省值:

```
export { default } from './FilterSlider'; 
```

另一个解决方案稍微广泛一点，但是它使用 Node.js 标准解析机制，使它坚如磐石，经得起未来的考验。我们所做的就是在文件结构中添加一个`package.json`文件:

```
components
    └── FilterSlider
        ├── __tests__
        │   └── FilterSlider-test.js
        ├── FilterSlider.jsx
        ├── FilterSlider.scss
        └── package.json 
```

在`package.json`中，我们使用[主属性](https://docs.npmjs.com/files/package.json#main)来设置组件的入口点，就像这样:

```
{
  "main": "FilterSlider.jsx"
} 
```

添加后，我们可以像这样导入一个组件:

```
import FilterSlider from 'components/FilterSlider' 
```

## 2.JavaScript 中的 CSS

样式，尤其是主题，一直是一个小问题。如上所述，在我们应用的第一次迭代中，我们有一个很大的 CSS (SCSS)文件，其中存放了我们所有的类。为了避免名称冲突，我们使用了一个全局前缀，并遵循 [BEM 约定](https://www.sitepoint.com/css-architecture-block-element-modifier-bem/)来创建 CSS 规则名称。当我们的应用程序增长时，这种方法不能很好地扩展，所以我们寻找一种替代方法。首先我们评估了 [CSS 模块](https://www.sitepoint.com/understanding-css-modules-methodology/)，但是那时他们有一些性能问题。此外，通过 webpack 的[提取文本插件](https://github.com/webpack-contrib/mini-css-extract-plugin)提取 CSS 也不太好(尽管在撰写本文时应该没问题)。此外，这种方法严重依赖于 webpack，使得测试变得非常困难。

接下来，我们评估了最近出现的其他一些 CSS-in-JS 解决方案:

*   [风格组件](https://github.com/styled-components/styled-components):最大社区最受欢迎的选择
*   最热门的竞争者
*   [Linaria](https://github.com/callstack/linaria) :零运行时间解决方案

选择这些库中的一个主要取决于您的用例:

*   需要库吐槽一个编译好的 CSS 文件进行制作吗？EmotionJS 和 Linaria 可以做到！Linaria 甚至不需要运行时。它通过 [CSS 变量](https://www.sitepoint.com/practical-guide-css-variables-custom-properties/)将道具映射到 CSS，这排除了 IE11 支持——但是谁还需要 IE11 呢？
*   它需要在服务器上运行吗？这对于所有库的最新版本都没有问题！

对于目录结构，我们喜欢将所有样式放在一个`styles.js`中:

```
export const Section = styled.section` padding: 4em;
  background: papayawhip; `; 
```

这样，纯前端人员也能够编辑一些样式，而无需处理 React，但他们必须学习最基本的 JavaScript 以及如何将属性映射到 CSS 属性:

```
components
    └── FilterSlider
        ├── __tests__
        │   └── FilterSlider-test.js
        ├── styles.js
        ├── FilterSlider.jsx
        └── index.js 
```

将你的主要组件文件从 HTML 中分离出来是一个很好的实践。

## 争取反应元件的单一责任

当您开发高度抽象的 UI 组件时，有时很难将关注点分开。在某些时候，您的组件将需要来自您的模型的某个领域逻辑，然后事情变得混乱。在接下来的部分中，我们将向您展示一些干燥组件的方法。以下技术在功能上有所重叠，为您的架构选择正确的技术更多的是风格上的偏好，而不是基于确凿的事实。但是让我先介绍一下用例:

*   我们必须引入一种机制来处理对登录用户有上下文意识的组件。
*   我们必须用多个可折叠的`<tbody>`元素来呈现一个表格。
*   我们必须根据不同的状态显示不同的组件。

在下一节中，我将展示针对上述问题的不同解决方案。

## 3.定制挂钩

有时，您必须确保 React 组件仅在用户登录到您的应用程序时显示。最初，你会在渲染时做一些理智检查，直到你发现自己重复了很多。在你干涸代码的任务中，你迟早要编写[自定义钩子](https://www.sitepoint.com/react-hooks/)。不要害怕:这并不难。看一下下面的例子:

```
import { useEffect } from 'react';
import { useAuth } from './use-auth-from-context-or-state-management.js';
import { useHistory } from 'react-router-dom';

function useRequireAuth(redirectUrl = "/signup") {
  const auth = useAuth();
  const history = useHistory();

  // If auth.user is false that means we're not
  // logged in and should redirect.
  useEffect(() => {
    if (auth.user === false) {
      history.push(redirectUrl);
    }
  }, [auth, history]);
  return auth;
} 
```

`useRequireAuth`钩子将检查用户是否登录，否则将重定向到不同的页面。可以通过[上下文](https://www.sitepoint.com/replace-redux-react-hooks-context-api/)或像 [MobX](https://www.sitepoint.com/manage-javascript-application-state-mobx/) 或 [Redux](https://www.sitepoint.com/getting-started-redux/) 这样的状态管理系统来提供`useAuth`钩子中的逻辑。

## 4.作为子模式运行

创建一个可折叠的表格行并不是一项简单的任务。如何呈现折叠按钮？当桌子没有折叠时，我们如何展示孩子？我知道有了 JSX 2.0，事情变得简单多了，因为你可以返回一个数组而不是一个标签，但是我将扩展这个例子，因为它展示了一个将*函数作为子*模式的良好用例。想象一下下表:

```
export default function Table({ children }) {
  return (
    <table>  <thead>  <tr>  <th>Just a table</th>  </tr>  </thead>  {children}  </table>
  );
} 
```

和可折叠的桌体；

```
import { useState } from 'react';

export default function CollapsibleTableBody({ children }) {
  const [collapsed, setCollapsed] = useState(false);

  const toggleCollapse = () => {
    setCollapsed(!collapsed);
  };

  return (
    <tbody>  {children(collapsed, toggleCollapse)}  </tbody>
  );
} 
```

您可以按以下方式使用该组件:

```
<Table>  <CollapsibleTableBody>  {(collapsed, toggleCollapse) => {
      if (collapsed) {
        return (
          <tr>  <td>  <button onClick={toggleCollapse}>Open</button>  </td>  </tr>
        );
      } else {
        return (
          <tr>  <td>  <button onClick={toggleCollapse}>Closed</button>  </td>  <td>CollapsedContent</td>  </tr>
        );
      }
    }}  </CollapsibleTableBody>  </Table> 
```

您只需将一个函数作为子级传递，它将在父组件中被调用。您可能还见过这种技术被称为“渲染回调”，或者在特殊情况下，称为“渲染道具”。

## 5.渲染道具

“渲染道具”这个术语是由迈克尔·杰克逊创造的，他提出[高阶组件模式可以在 100%的时间内被一个带有“渲染道具”](https://cdb.reacttraining.com/use-a-render-prop-50de598f11ce)的常规组件所取代。这里的基本思想是，所有的 React 组件都是函数，函数可以作为道具传递。那么为什么不通过 props 传递 React 组件呢？！轻松点。

下面的代码试图概括如何从 API 获取数据。(请注意，这个例子只是为了演示。在实际项目中，您甚至可以将这个获取逻辑抽象到一个`useFetch`钩子中，以进一步将其从 UI 中分离出来。)下面是代码:

```
import { useEffect, useState } from "react";

export default function Fetch({ render, url }) {

  const [state, setState] = useState({
    data: {},
    isLoading: false
  });

  useEffect(() => {
    setState({ data: {}, isLoading: true });

    const _fetch = async () => {
      const res = await fetch(url);
      const json = await res.json();

      setState({
        data: json,
        isLoading: false,
      });
    }

    _fetch();
  }, https%3A%2F%2Feditor.sitepoint.com);

  return render(state);
} 
```

如您所见，有一个名为`render`的属性，它是渲染过程中调用的函数。内部调用的函数获取完整的状态作为参数，并返回 JSX。现在来看下面的用法:

```
<Fetch
  url="https://api.github.com/users/imgly/repos"
  render={({ data, isLoading }) => (
    <div>  <h2>img.ly repos</h2>  {isLoading && <h2>Loading...</h2>}  <ul>  {data.length > 0 && data.map(repo => (
          <li key={repo.id}>  {repo.full_name}  </li>
        ))}  </ul>  </div>
  )} /> 
```

如您所见，`data`和`isLoading`参数是从状态对象中析构出来的，可用于驱动 JSX 的响应。在这种情况下，只要承诺还没有实现，就会显示“正在加载”的标题。由您决定将状态的哪些部分传递给渲染道具，以及如何在用户界面中使用它们。总的来说，这是一个提取通用 UI 行为的非常强大的机制。上面描述的 *function as children* 模式与属性为`children`的模式基本相同。

**Protip** :因为*渲染道具*模式是*函数作为子*模式的一个概括，所以没有什么可以阻止你在一个组件上拥有多个渲染道具。例如，`Table`组件可以为标题获得一个渲染道具，然后为正文获得另一个渲染道具。

## 让我们继续讨论吧

我希望你喜欢这篇关于架构反应模式的文章。如果你在这篇文章中遗漏了什么(肯定有更多的最佳实践)，或者如果你只是想取得联系，请在 [Twitter](https://twitter.com/sippndipp) 上 ping 我。

PS: 9elements 一直在寻找有才华的开发者，所以[可以随意申请](https://9elements.kenjo.io/)。

## 分享这篇文章