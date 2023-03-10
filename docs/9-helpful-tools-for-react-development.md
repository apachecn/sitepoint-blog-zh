# React 开发的 9 个有用工具

> 原文：<https://www.sitepoint.com/9-helpful-tools-for-react-development/>

*本文是与 [Sencha](https://www.sencha.com/) 合作创作的。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

React 是一个彼此独立的工具和库的集合，这对于有工作要做的 React 开发人员来说是令人望而生畏的。众多的工具赋予了你足够的自由，因为你可以货比三家，为手头的工作选择最好的工具。在 React 中，有许多方法可以解决问题，因此了解多种工具是一个好主意。有些工具在某些问题上比其他工具更好，这完全取决于上下文。以下是我发现在一个大型 React 项目中有用的工具列表，有些工具比其他工具更新，有些工具在使用 React 时解决了一个特定的问题——但我发现所有这些工具都很有用。

对于每个 React 工具，我将提供一个简短的基本信息摘要，然后再深入介绍它能做的很酷的事情和我的想法。请记住，所有这些工具都会发生变化。

## 创建-反应-应用

*   *网站*:【facebook.github.io/create-react-app 
*   *储存库*:【github.com/facebook/create-react-app】T2
*   *GitHub stars* : 6 万+
*   *开发商*:脸书
*   *当前版本* : 2.1.8
*   *贡献者* : 600+人

开始 React 项目的一个好方法是创建 React 应用程序。这个工具将许多 React 工具抽象成一个独立的依赖项。像 Webpack、Babel 和 ESList 这样的工具在引擎盖下是安全的。您可以用一个命令开始整个 React 项目。该工具生成一个最小的文件集，因此您可以专注于应用程序。你需要 Node 6+来使用它。

例如，用`npx`启动一个应用程序:

```
npx create-react-app my-killer-app 
```

运行上面的命令将创建一个目录并生成脚手架文件。该命令无需干预即可安装可传递依赖项。没有复杂的配置文件，只有构建应用程序的最低要求。

升级被封装在一个 NPM 软件包中。没有必要与 webpack 和朋友乱搞，以利用最新的功能。当新版本发布时，您可以使用一个命令升级 Create React App。要升级，安装`react-scripts@latest` NPM 软件包。

在项目文件夹中，您可以找到内置命令。例如，运行`npm start`，启动应用程序。运行时，应用程序会热加载您对代码所做的任何更改。当您保存项目文件时，它会在浏览器中重新加载这些更改。发生的任何编译错误都会显示在运行该应用程序的控制台中。

Create React App 支持测试自动化。运行`npm test`将启动 Jest 并运行项目中的所有测试。该命令在交互模式下运行一个监视器，因此它将跟踪文件的变化。当您进行更改时，观察者只运行受影响的测试。确保将测试文件命名为`*.test.js`,这样 Jest 就可以获取它们。

创建 React 应用程序抽象出项目配置。JavaScript 开发人员经常抱怨配置现代工具很难。因此，这里有一个工具，可以毫不费力地自动完成这项工作。

优点:

*   从一个命令开始
*   只有一个依赖项
*   不需要配置

缺点:

*   没有服务器端呈现

## Next.js

*   *网站*:【nextjs.org 
*   *储存库*:【github.com/zeit/next.js】T2
*   *GitHub stars*:35000+人
*   *开发者* : ZEIT
*   *当前版本* : 8.0.3
*   *贡献者* : 600+人

Next.js 通过同构的 React 应用进行服务器端渲染。无需设置，只需很少的时间即可开始。您可以获得代码分割、基于文件夹的路由、热代码重载和通用呈现。代码拆分仅在页面上加载必要的组件。路由会在项目中查找一个`pages`文件夹，并找到与链接路由匹配的文件。热代码重新加载观察文件的变化，并重新加载应用程序的一部分。这对于喜欢代码变更之间快速反馈循环的开发人员来说很好。渲染在客户端和服务器上都发生，因为应用程序是同构的。这允许代码重用，因为在服务器上运行的相同代码也在客户端上运行。

要开始使用 Next.js，请执行以下操作:

```
npm install --save next react react-dom 
```

确保创建一个带有`package.json`的项目文件夹。在这个包文件中，用`next build`和`next start`脚本连接 Next.js。这些脚本映射到您通过 NPM 执行的运行脚本。

使用 Next.js，您可以通过创建一个`static`文件夹来托管静态文件。您放在此文件夹中的所有内容都将成为应用程序中的静态资产。这允许您提供图像或 CSS 等资产。

您可以将 Next.js 导出为静态 HTML 页面，以消除服务器端呈现。在项目文件夹中`next export`生成静态 HTML 页面，你可以在任何地方托管。导出几乎支持所有功能，动态 URL、预取、预加载和动态导入。默认情况下，没有必要进行配置。它从`pages`目录生成静态 HTML 文件。导出命令位于`package.json`中，所以用`npm run export`运行它。导出的文件会出现在`out`目录下。

优点:

*   具有高级功能的零配置
*   应用程序可轻松投入生产
*   静态 HTML 选项消除了服务器端呈现

缺点:

*   定制路线可能会很麻烦

## Redux

*   *网站*:【redux.js.org 
*   *储存库*:【github.com/reduxjs/redux】T2
*   *GitHub stars*:45000+人
*   *开发者*:月球
*   *当前版本* : 4.0.1
*   *贡献者* : 600+人

在 React 中，传递许多道具和状态参数很麻烦。一个组件层次结构可以有许多 React 组件。链条中某处的单个参数事故是危险的。当有很多组件时，这使得调试参数变得令人沮丧。

一个更好的解决方案是通过状态机抽象出来。Redux 是 JavaScript 程序的状态容器。应用程序的整个状态都在一个商店里。要改变状态，启动一个动作，让 reducers 处理剩下的事情。可以把 Redux 想象成一个状态机，它通过发出动作来改变状态。通过存储调度动作是改变状态的唯一方法。Reducers 不会改变状态，只是在状态改变时返回一个新对象。这使得 reducer 成为一个保持 app 可预测性的纯函数。bug 经常在行为不可预测的地方爬行，所以 reducers 解决了这个问题。

Redux 是一个可以在普通 JavaScript 中使用的独立库。React 绑定可用于轻松连接 React 应用程序。一个`<Provider />`使 Redux 存储对根组件可用。一个`connect`函数将状态映射到商店中所有组件的道具。connect 函数提供每个组件工作所需的参数。

在典型的 React 应用程序中，有一个根组件和一个商店。可以存在分割状态树的 Redux 存储的层次结构。这就像 React 一样，一个根组件可以有许多小组件。

要在 React 项目中设置 Redux 绑定:

```
npm install --save redux react-redux 
```

务必将 Redux 和 React-Redux 视为独立的依赖项。

优点:

*   像纯函数一样包含声音编程
*   绑定可用于 React
*   自动化状态管理

缺点:

*   从头开始设置可能有点痛苦

## 酶

*   *网站*:【airbnb.io/enzyme/ 
*   *储存库*:【github.com/airbnb/enzyme】T2
*   *GitHub stars*:15000+人
*   *开发商* : Airbnb
*   *当前版本* : 3.9.0
*   *贡献者* : 300+人

Enzyme 类似于 jQuery，用于测试 React 组件。您可以在没有无头浏览器的情况下浅层呈现组件。您可以遍历、搜索组件并做出断言。这缩短了反馈循环，因为它消除了浏览器。浅层渲染很有用，因为它不会超过一层深度。这将测试隔离到 React 组件层次结构中的单个级别。

Enzyme 的 API 支持事件模拟。`simulate()`函数调用单个节点中的事件处理程序。例如，`.simulate('click')`调用`onClick`的道具函数。浅层渲染隔离测试中的节点，这增加了焦点。

Enzyme 为每个版本的 React 提供了一个适配器。所以，`enzyme-adapter-react-16`作用于 React 16.4 以上。对于 React 16.3.0，使用`enzyme-adapter-react-16.3`适配器。对于 Jest 15+，酵素不再需要任何特殊配置。要编写测试，在顶部导入 React、酶功能和被测系统。

例如:

```
import React from 'react';
import { shallow } from 'enzyme';

import Foo from '../Foo'; // system under test

describe('A killer test suite', () => {
  it('should shallow render without a browser', () => {
    // arrange
    const component = shallow(<Foo />);

    // act
    const result = component.contains(<div className="foo">Bar</div>);

    // assert
    expect(result).toBe(true);
  });
}); 
```

浅层渲染有各种各样的[选择器和函数](https://airbnb.io/enzyme/docs/api/shallow.html)来查找节点。一次选取一个节点可以让您以独立的方式断言功能。

优点:

*   浅渲染隔离 React 组件
*   用于查找感兴趣的节点的丰富 API
*   不需要无头浏览器

缺点:

*   每次 React 升级时，适配器都可能是危险的

## 提取

*   *网站*:【sencha.com/products/extreact 
*   *储存库*:【github.com/sencha/ext-react】T2
*   *开发者*:森查
*   *当前版本* : 6.7.0

React 提供了一种基于组件的方法来构建 web 应用程序，但是 React 本身不包含任何组件。ExtReact 提供了预先构建的组件，如菜单、网格、图表和表单来提供功能。开源社区很大，有很多组件可用；然而，解决许多组件库和 React 之间的兼容性问题令人望而生畏。一个更好的想法是通过从更少的库中获取尽可能多的组件来降低风险，或者最好是从单个库中获取组件。ExtReact 在一个专业包中提供最完整的组件集，并包括商业支持。

首先，你需要 Node 8.11+，NPM 6+和 Java 8 & 11。通过免费试用，你可以登录位于 npm.sencha.com[的 NPM 知识库。用`npm install -g @sencha/ext-react-gen`安装 app 生成器。然后，用`ext-react-gen app killer-app`创建一个 app。这就产生了一个名为`killer-app`的应用程序。进入项目文件夹，执行`npm start`来启动一个准系统 React 应用程序。](https://npm.sencha.com)

从`ext-react`包中导入 ExtReact 组件。组件可能自带存储来获取数据。许多道具都有预置的功能。例如，`onSelect`告诉`List`组件在点击一个列表项时做什么。排序器可用于对组件中的列表项进行排序。

因此，假设您想要导入一个基本的`List`组件:

```
import React, { Component } from 'react';
import { List } from '@sencha/ext-modern';

export default class BasicList extends Component {
  store = Ext.create('Ext.data.Store', {
    autoLoad: true,
    proxy: {
      type: 'rest',
      url: 'data/people.json'
    },
    sorters: ['first_name']
  });

  tpl = data => (
    <div>  <div>{data.first_name}</div>  </div>
  );

  onSelect = (list, record) => {
    console.log(`${record.get('first_name')}`);
  };

  render() {
    return (
      <List
        shadow
        itemTpl={this.tpl}
        store={this.store}
        onSelect={this.onSelect}
      />
    );
  }
} 
```

ExtReact 提供超过 115 个预构建的 UI 组件，并提供全面的商业支持。ExtReact 组件是为了在应用程序的整个生命周期中实现无缝集成而设计的。

优点:

*   各种预构建的 React 组件
*   减少对许多开源选项的依赖
*   有商业支持的专业组件

缺点:

*   对 Java 的依赖

## React 开发工具

*   *网站*:[Firefox/addon/react-dev tools/](https://addons.mozilla.org/en-US/firefox/addon/react-devtools/)
*   *储存库*:【github.com/facebook/react-devtools】T2
*   *GitHub stars*:9000+人
*   *开发商*:脸书
*   *当前版本* : 3.6.0
*   *贡献者* : 100+人

React 开发工具允许您在浏览器中检查 React 组件。它可以检查组件的层次结构，包括属性和状态。作为 Chrome 和 Firefox 的浏览器扩展，以及一个独立的应用程序。Opera 用户可以在启用 Chrome 扩展的情况下安装 Chrome 扩展。在使用 React 的网站上，扩展图标会亮起，并指示打开开发者工具。

在 React 应用上，注意开发者工具中的 React 选项卡。单击该选项卡会在树视图中显示组件的层次结构。具有状态的组件显示具有不同颜色的树折叠按钮。您可以使用箭头键或`hjkl`在树中导航，就像使用 Vim 一样。右键单击会提供一些选项，如在元素窗格中显示、滚动到视图中或显示源代码。

侧窗格让您更深入地了解每个组件。它允许您在浏览器中进行更改，并突出显示这些更改。您可以将道具和状态以及商店对象作为一个全局变量来操作。

使用搜索栏按名称查找组件。当一个树中有许多组件时，这可以节省您的时间。搜索栏支持正则表达式进行精确搜索。一个技巧是使用常规的 DOM 检查器或 elements 选项卡来查找组件。选择页面上的元素，切换到 React 选项卡。该组件在反应树中被选中。右键单击可以选择“查找 DOM 节点”,这将带您回到常规选项卡。

优点:

*   帮助可视化树中的 React 组件
*   随意操作和调试组件
*   查找组件的有用搜索工具

缺点:

*   额外的附件，但确实提供了一个独立版本

## 冗余开发工具

*   *网站*:[Firefox/addon/reduxdevtools/](https://addons.mozilla.org/en-US/firefox/addon/reduxdevtools/)
*   *储存库*:【github.com/reduxjs/redux-devtools】T2
*   *GitHub stars*:10000+人
*   *开发者*:月球
*   *当前版本* : 2.17.1
*   *贡献者* : 70+人

Redux 开发者工具支持热重装和动作回放。作为浏览器扩展、独立应用程序和 React 组件提供。React 组件位于主机应用程序内部。该工具允许您检查每个状态和动作负载。或者，通过取消操作回到过去。更改代码会重新评估标记为暂存的操作。

通过 Chrome 网络商店或 Mozilla 插件安装扩展。React 组件可用于不支持该扩展的浏览器。要使用扩展，必须对项目进行更改。必须在 Redux 存储中设置扩展以及扩展选项。

例如，使用选项设置 Redux 开发人员工具:

```
const store = createStore(
  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__({
    actionCreators,
    trace: true,
    traceLimit: 5
  })
); 
```

Dispatcher 中可用的操作来自`actionCreators`。这是一个动作功能列表。将项目中所有感兴趣的操作导入此变量。跟踪器将包括每个调度操作的堆栈跟踪。检查 trace 选项卡允许单击跳转到代码。该选项的默认值为 false。跟踪限制设置显示的最大堆栈帧数，默认值为 10。

在商店中应用任何中间件都需要`composeWithDevTools`。从`redux-devtools-extension` NPM 包中获得该功能。`applyMiddleware`函数作为参数进入这个函数。这允许开发工具与中间件一起工作。

优点:

*   app 状态时间旅行工具
*   通过热重载跟踪代码更改
*   每个操作的调试堆栈跟踪

缺点:

*   设置需要更改项目中的代码

## redux-不可变-状态不变

*   *网站*:【npmjs.com/package/redux-immutable-state-invariant 
*   *储存库*:【github.com/leoasis/redux-immutable-state-invariant】T2
*   *GitHub stars* : 700+
*   *开发者* : leoasis
*   *当前版本* : 2.1.0
*   *贡献者* : 10+人

在调度程序中或调度程序之间改变状态时抛出错误的中间件。Reducers 假设参数不会改变状态。每次 reducers 返回状态，它必须是一个新的对象。一个好的做法是避免异径管外的突变状态。这需要程序员的高度自律。因此，一个好主意是通过一个工具来自动化这种检查。

该工具仅在开发过程中用于捕获 bug。它做了大量的对象复制来增强不变性。这意味着高内存消耗会降低应用程序的性能。因此，不要在生产中启用该工具。一种想法是设置一个环境变量`NODE_ENV`，例如，设置为`production`。

要将此库用作中间件:

```
import { applyMiddleware, createStore } from 'redux';

let middleware = [];

// Be sure to ONLY add this middleware in development!
if (process.env.NODE_ENV !== 'production') {
  middleware.push(require('redux-immutable-state-invariant').default());
}

const store = createStore(
  applyMiddleware(...middleware)
); 
```

如果你不是变异状态，那么这个工具就会消失。在调度程序中或调度程序之间改变状态，工具会发出错误消息。像任何好的工具一样，它能让你保持最佳的编程行为。

优点:

*   无缝并适用于任何工作流程
*   自动化编程规程
*   抛出带有描述性消息的错误

缺点:

*   大量的对象复制会影响性能，但只能在 dev 中启用

## 冗余记录器

*   *网站*:【npmjs.com/package/redux-logger 
*   *储存库*:【github.com/LogRocket/redux-logger】T2
*   *GitHub stars*:4000+人
*   *开发者* : LogRocket
*   *当前版本* : 3.0.6
*   *贡献者* : 40+人

Redux 的动作记录器。在浏览器的日志控制台中捕获调度程序操作。带有许多选项，并允许深入了解每个调度的行动。当动作在应用中波动时，它会捕捉并让你深入每个动作。每个日志条目都捕获以前和当前的状态，以及调度程序的操作。将每个操作视为一条日志消息。记录器让您看到这些消息在应用程序中改变状态时的样子。

要使用记录器，请在 Redux 存储中设置它:

```
import { applyMiddleware, createStore } from 'redux';

// Logger with default options
import logger from 'redux-logger';

const store = createStore(
  applyMiddleware(logger)
); 
```

确保记录器是`applyMiddleware`中的最后一个参数，这样它就可以捕获动作。选项可用于更改记录器的行为。你需要从`redux-logger`导入`createLogger`。然后，用一个选项对象调用`createLogger`函数。例如，`predicate`指定了一个在每个动作之前被调用的函数。做`(getState, action) => action.type !== UP_COUNT`过滤掉增加计数的动作。一个`collapsed`选项是一个函数，如果日志组崩溃，它返回 true。执行`(getState, action) => action.type === DOWN_COUNT`会折叠减少计数的动作类型。

优点:

*   捕获调度程序操作，如日志消息
*   允许您深入每个操作并检查应用程序状态
*   选项允许您调整捕获的内容

缺点:

*   仅客户端日志记录器，但是您可以通过一点努力发送消息

## 结论

React 的有用性源于它是运行良好的工具和库的集合。这些工具通常是轻量级的，因此您可以随意即插即用。编程不是重新发明轮子，而是重用现有的工作良好的解决方案。React 的流行源于这样一个事实，即你可以挑选解决眼前问题的方法。这让你专注于那些还没有好的解决方案的问题。

## 分享这篇文章