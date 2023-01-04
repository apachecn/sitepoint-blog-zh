# React 路由器 v5:完全指南

> 原文：<https://www.sitepoint.com/react-router-complete-guide/>

**[React 路由器](https://reacttraining.com/react-router/)是 React 事实上的标准路由库。当您需要在具有多个视图的 React 应用程序中导航时，您将需要一个路由器来管理 URL。React Router 负责这一点，使您的应用程序 UI 和 URL 保持同步。**

本教程向您介绍 React Router v5 以及您可以用它做的许多事情。

## 介绍

React 是一个流行的库，用于创建在客户端呈现的单页面应用程序(spa)。一个 SPA 可能有多个**视图**(又名**页面**)，与传统的多页面应用不同，浏览这些视图不会导致整个页面被重新加载。相反，我们希望视图在当前页面中以内联方式呈现。习惯于多页面应用的最终用户希望 SPA 具备以下特性:

*   每个视图都应该有一个唯一指定该视图的 URL。这是为了让用户可以将该 URL 标记为书签，以供以后参考。比如`www.example.com/products`。
*   浏览器的后退和前进按钮应该可以正常工作。
*   动态生成的嵌套视图最好也有自己的 URL 比如`example.com/products/shoes/101`，其中 101 是产品 ID。

**路由**是保持浏览器 URL 与页面上呈现的内容同步的过程。React Router 让您以声明方式处理路由*。声明式路由方法允许您控制应用程序中的数据流，方法是说“路由应该是这样的”:*

```
<Route path="/about">  <About />  </Route> 
```

您可以将您的`<Route>`组件放在任何您想要呈现路线的地方。因为`<Route>`、`<Link>`和我们将要处理的所有其他 React 路由器 API 都只是组件，所以您可以很容易地在 React 中启动并运行路由。

注意:有一种普遍的误解，认为 React 路由器是脸书开发的官方路由解决方案。实际上，它是一个第三方库，因其设计和简单性而广受欢迎。

## 概观

本教程分为不同的部分。首先，我们将使用 npm 设置 React 和 React 路由器。然后，我们将直接进入一些 React 路由器基础知识。您将发现 React 路由器运行中的不同代码演示。本教程中涵盖的示例包括:

*   基本导航路线
*   嵌套路由
*   带路径参数的嵌套路由
*   受保护的路由

与建设这些路线相关的所有概念将在此过程中讨论。

该项目的完整代码可在 GitHub repo 上获得。

我们开始吧！

## 设置 React 路由器

要继续学习本教程，您需要在 PC 上安装 Node 的最新版本。如果不是这样，那么就去节点主页[下载适合您系统的正确二进制文件](https://nodejs.org/en/download/)。或者，您可以考虑使用版本管理器来安装 Node。我们有一个关于使用版本管理器的[教程。](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)

Node 与 npm 捆绑在一起，NPM 是 JavaScript 的一个包管理器，我们将使用它来安装一些将要使用的库。您可以[在这里](https://www.sitepoint.com/beginners-guide-node-package-manager/)了解更多关于使用 npm 的信息。

您可以通过从命令行发出以下命令来检查两者是否安装正确:

```
node -v
> 12.19.0

npm -v
> 6.14.8 
```

完成之后，让我们开始用 [Create React App](https://www.npmjs.com/package/create-react-app) 工具创建一个新的 React 项目。您可以全局安装，或者使用`npx`，如下所示:

```
npx create-react-app react-router-demo 
```

完成后，切换到新创建的目录:

```
cd react-router-demo 
```

React 路由器库包含三个包: [react-router](https://www.npmjs.com/package/react-router) 、 [react-router-dom](https://www.npmjs.com/package/react-router-dom) 和 [react-router-native](https://www.npmjs.com/package/react-router-native) 。路由器的核心包是`react-router`，而另外两个是特定于环境的。如果你在建立一个网站，你应该使用`react-router-dom`，如果你在一个使用 React Native 的移动应用开发环境中，你应该使用`react-router-native`。

使用 npm 安装`react-router-dom`:

```
npm install react-router-dom 
```

然后使用以下命令启动开发服务器:

```
npm run start 
```

恭喜你！现在你已经有了一个安装了 React 路由器的 React 应用程序。可以查看运行在 [http://localhost:3000/](http://localhost:3000/) 的 app。

## React 路由器基础知识

现在让我们熟悉一下基本的 React 路由器设置。为此，我们将制作一个具有三个独立视图的应用程序:主页、类别和产品。

### `Router`组件

我们需要做的第一件事是将我们的`<App>`组件包装在一个`<Router>`组件中(由 React Router 提供)。由于我们正在构建一个基于浏览器的应用程序，我们可以使用 React Router API 中的两种类型的路由器:

*   [浏览器路由器](https://reactrouter.com/web/api/BrowserRouter)
*   [哈希表](https://reactrouter.com/web/api/HashRouter)

它们之间的主要区别在于它们创建的 URL:

```
// <BrowserRouter>
http://example.com/about

// <HashRouter>
http://example.com/#/about 
```

`<BrowserRouter>`是两者中更受欢迎的，因为它使用 [HTML5 历史 API](https://developer.mozilla.org/en-US/docs/Web/API/History_API) 来保持你的 UI 与 URL 同步，而`<HashRouter>`使用 URL 的散列部分(`window.location.hash`)。如果你需要支持不支持历史 API 的遗留浏览器，你应该使用`<HashRouter>`。否则`<BrowserRouter>`对于大多数用例来说是更好的选择。你可以[在这里](https://stackoverflow.com/q/51974369/1136887)阅读更多关于差异的内容。

因此，让我们导入`BrowserRouter`组件并将其包装在`App`组件周围:

```
// src/index.js

import React from "react";
import ReactDOM from "react-dom";
import App from "./App";
import { BrowserRouter } from "react-router-dom";

ReactDOM.render(
  <BrowserRouter>  <App />  </BrowserRouter>,
  document.getElementById("root")
); 
```

上面的代码为整个`<App>`组件创建了一个`history`实例。让我们看看这意味着什么。

### 一点历史

> `history`库让您可以在 JavaScript 运行的任何地方轻松管理会话历史。一个`history`对象抽象出了各种环境中的差异，并提供了一个最小的 API，让您可以管理历史堆栈、导航和保持会话之间的状态。— [React 培训文档](https://github.com/ReactTraining/history)

每个`<Router>`组件创建一个`history`对象，该对象跟踪当前位置(`history.location`)以及堆栈中先前的位置。当当前位置发生变化时，视图会重新呈现，您会有一种导航的感觉。当前位置如何变化？历史对象有像`history.push`和`history.replace`这样的方法来处理这个问题。当你点击一个`<Link>`组件时，调用`history.push`方法，当你使用一个`<Redirect>`时，调用`history.replace`。其他方法——如`history.goBack`和`history.goForward` —用于通过后退或前进页面来浏览历史堆栈。

继续，我们有链接和路线。

### `Link`和`Route`组件

组件是 React 路由器中最重要的组件。如果当前位置与路线的路径匹配，它会呈现一些 UI。理想情况下，`<Route>`组件应该有一个名为`path`的属性，如果路径名与当前位置匹配，它就会被渲染。

另一方面，`<Link>`组件用于在页面间导航。它可以与 HTML 锚元素相媲美。然而，使用锚链接会导致整个页面刷新，这是我们不希望的。因此，我们可以使用`<Link>`导航到一个特定的 URL，并在不刷新的情况下重新呈现视图。

现在，我们已经涵盖了让我们的应用程序工作所需的一切。更新`src/App.js`如下:

```
import React from "react";
import { Link, Route, Switch } from "react-router-dom";

const Home = () => (
  <div>  <h2>Home</h2>  </div>
);

const Category = () => (
  <div>  <h2>Category</h2>  </div>
);

const Products = () => (
  <div>  <h2>Products</h2>  </div>
);

export default function App() {
  return (
    <div>  <nav className="navbar navbar-light">  <ul className="nav navbar-nav">  <li>  <Link to="/">Home</Link>  </li>  <li>  <Link to="/category">Category</Link>  </li>  <li>  <Link to="/products">Products</Link>  </li>  </ul>  </nav>  { /* Route components are rendered if the path prop matches the current URL */}  <Route path="/"><Home /></Route>  <Route path="/category"><Category /></Route>  <Route path="/products"><Products /></Route>  </div>
  );
} 
```

这里，我们在`App.js`中声明了`Home`、`Category`和`Products`的组件。虽然现在这样还可以，但是当一个组件开始变大时，最好为每个组件都有一个单独的文件。根据经验，如果一个组件占用的代码超过 10 行，我通常会为它创建一个新文件。从第二个演示开始，我将为那些已经变得太大而无法放入`App.js`文件的组件创建一个单独的文件。

在`App`组件内部，我们已经编写了路由逻辑。`<Route>`的路径与当前位置匹配，一个组件被渲染。以前，应该呈现的组件作为第二个道具传入。然而，React Router 的最新版本引入了一种新的路由呈现模式，即要呈现的组件是`<Route>`的子组件。

这里的`/`与`/`和`/category`都匹配。因此，两条路线都被匹配和渲染。我们如何避免这种情况？你要把`exact`道具传给有`path='/'`的`<Route>`:

```
<Route exact path="/">  <Home />  </Route> 
```

如果你想在路径完全相同的情况下渲染一条路线，你应该使用[确切的属性](https://reactrouter.com/web/api/Route/exact-bool)。

## 嵌套路由

为了创建嵌套路由，我们需要更好地理解`<Route>`是如何工作的。现在让我们来看看。

正如你在 [React Router 文档](https://reactrouter.com/web/api/Route/route-render-methods)上看到的，推荐的用`<Route>`渲染的方法是使用`children`元素，如上所示。然而，有一些其他的方法可以用来渲染带有`<Route>`的东西。这些主要是为支持应用程序而提供的，这些应用程序是在引入挂钩之前用路由器的早期版本构建的:

*   `component`:当 URL 匹配时，路由器使用`React.createElement`从给定的组件创建一个 React 元素。
*   `render`:便于内嵌渲染。`render`属性需要一个函数，该函数在位置与路径匹配时返回一个元素。
*   `children`:这与`render`类似，因为它期望一个返回 React 组件的函数。然而，无论路径是否与位置匹配，`children`都会被渲染。

### 路径和匹配

`path`属性用于标识路由器应该匹配的 URL 部分。它使用 [Path-to-RegExp 库](https://www.npmjs.com/package/path-to-regexp)将路径字符串转换成正则表达式。然后将它与当前位置进行匹配。

如果路由器的路径和位置匹配成功，就会创建一个名为[匹配对象](https://reactrouter.com/web/api/match)的对象。`match`对象包含更多关于 URL 和路径的信息。该信息可通过其属性访问，如下所示:

*   `match.url`:返回 URL 匹配部分的字符串。这对于构建嵌套的`<Link>`组件特别有用。
*   `match.path`:返回路线路径字符串的字符串，即`<Route path="">`。我们将用它来构建嵌套的`<Route>`组件。
*   `match.isExact`:一个布尔值，如果完全匹配(没有任何尾随字符)，则返回 true。
*   `match.params`:包含由 Path-to-RegExp 包解析的 URL 中的键/值对的对象。

### 道具的隐式传递

注意，当使用`component`属性来呈现路线时，`match`、`location`和`history`路线属性被隐式地传递给组件。当使用较新的路由呈现模式时，情况并非如此。

例如，以这个组件为例:

```
const Home = (props) => {
  console.log(props);

  return (
    <div>  <h2>Home</h2>  </div>
  );
}; 
```

现在将路线渲染成这样:

```
<Route exact path="/" component={Home} /> 
```

这将记录以下内容:

```
{
  history: { ... }
  location: { ... }
  match: { ... }
} 
```

但是现在将路线渲染成这样:

```
<Route exact path="/"><Home /></Route> 
```

这将记录以下内容:

```
{} 
```

起初这可能看起来不利，但不用担心！React v5.1 引入了几个钩子来帮助你在你需要的地方访问你需要的东西。这些钩子为我们提供了管理路由器状态的新方法，并在一定程度上整理了我们的组件。

我将在本教程中使用其中的一些钩子，但是如果你想更深入地了解，请查看 [React Router v5.1 发布公告](https://reacttraining.com/blog/react-router-v5-1/)。还请注意，钩子是在 React 的 16.8 版本中引入的，所以你至少需要在那个版本上才能使用它们。

### `Switch`组件

在我们开始演示代码之前，我想向您介绍一下[开关组件](https://reactrouter.com/web/api/Switch)。当多个`<Route>`一起使用时，所有匹配的路线都被包含地呈现。考虑演示 1 中的这段代码。我添加了一条新路线来展示为什么`<Switch>`有用:

```
<Route exact path="/"><Home /></Route>
<Route path="/category"><Category /></Route>
<Route path="/products"><Products /></Route>
<Route path="/:id">  <p>This text will render for any route other than '/'</p>  </Route> 
```

如果 URL 是`/products`，则呈现所有匹配位置`/products`的路线。因此，带有路径`/:id`的`<Route>`与`<Products>`组件一起被渲染。这是故意的。然而，如果这不是您所期望的行为，您应该将`<Switch>`组件添加到您的路线中。使用`<Switch>`，只有匹配位置的第一个孩子`<Route>`被渲染:

```
<Switch>  <Route exact path="/"><Home /></Route>  <Route path="/category"><Category /></Route>  <Route path="/products"><Products /></Route>  <Route path="/:id">  <p>This text will render for any route other than those defined above</p>  </Route>  </Switch> 
```

`path`的`:id`部分用于动态路由。它将匹配斜杠后的任何内容，并使该值在组件中可用。在下一节中，我们将看到一个这样的例子。

现在我们已经了解了所有的`<Route>`和`<Switch>`组件，让我们将嵌套路由添加到我们的演示中。

### 动态嵌套路由

之前，我们创建了`/`、`/category`和`/products`的路线。但是如果我们想要一个`/category/shoes`形式的 URL 呢？

让我们从如下更新`src/App.js`开始:

```
import React from "react";
import { Link, Route, Switch } from "react-router-dom";
import Category from "./Category";

const Home = () => (
  <div>  <h2>Home</h2>  </div>
);

const Products = () => (
  <div>  <h2>Products</h2>  </div>
);

export default function App() {
  return (
    <div>  <nav className="navbar navbar-light">  <ul className="nav navbar-nav">  <li>  <Link to="/">Home</Link>  </li>  <li>  <Link to="/category">Category</Link>  </li>  <li>  <Link to="/products">Products</Link>  </li>  </ul>  </nav>  <Switch>  <Route path="/"><Home /></Route>  <Route path="/category"><Category /></Route>  <Route path="/products"><Products /></Route>  </Switch>  </div>
  );
} 
```

你会注意到我们已经将`Category`移到了它自己的组件中。这是我们的嵌套路由应该去的地方。

现在让我们创建`Category.js`:

```
// src/Category.js

import React from "react";
import { Link, Route, useParams, useRouteMatch } from "react-router-dom";

const Item = () => {
  const { name } = useParams();

  return (
    <div>  <h3>{name}</h3>  </div>
  );
}

const Category = () => {
  const { url, path } = useRouteMatch();

  return (
    <div>  <ul>  <li>  <Link to={`${url}/shoes`}>Shoes</Link>  </li>  <li>  <Link to={`${url}/boots`}>Boots</Link>  </li>  <li>  <Link to={`${url}/footwear`}>Footwear</Link>  </li>  </ul>  <Route path={`${path}/:name`}>  <Item />  </Route>  </div>
  );
};

export default Category; 
```

这里，我们使用 [useRouteMatch 钩子](https://reactrouter.com/web/api/Hooks/useroutematch)来访问`match`对象。如前所述，`match.url`将用于构建嵌套链路，`match.path`用于嵌套路由。如果您在理解 match 的概念上有困难，`console.log(useRouteMatch())`提供了一些有用的信息，可能有助于澄清这个问题。

```
<Route path={`${path}/:name`}>  <Item />  </Route> 
```

这是我们在动态路由方面的第一次尝试。我们没有对路线进行硬编码，而是在`path`属性中使用了一个变量。`:name`是一个路径参数，捕捉`category/`之后的所有内容，直到遇到另一个正斜杠。因此，像`products/running-shoes`这样的路径名将创建一个`params`对象，如下所示:

```
{
  name: "running-shoes";
} 
```

为了在`<Item>`组件中访问这个值，我们使用了 [useParams 钩子](https://reactrouter.com/web/api/Hooks/useparams)，它返回一个 URL 参数的键/值对对象。

在您的浏览器中尝试一下。类别部分现在应该有三个子部分，每个子部分都有自己的路径。

### 带路径参数的嵌套路由

让我们把事情弄复杂一点，好吗？现实世界中的路由器必须处理数据并动态显示。假设我们有一些由 API 以如下格式返回的产品数据:

```
const productData = [
  {
    id: 1,
    name: "NIKE Liteforce Blue Sneakers",
    description:
      "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Proin molestie.",
    status: "Available",
  },
  {
    id: 2,
    name: "Stylised Flip Flops and Slippers",
    description:
      "Mauris finibus, massa eu tempor volutpat, magna dolor euismod dolor.",
    status: "Out of Stock",
  },
  {
    id: 3,
    name: "ADIDAS Adispree Running Shoes",
    description:
      "Maecenas condimentum porttitor auctor. Maecenas viverra fringilla felis, eu pretium.",
    status: "Available",
  },
  {
    id: 4,
    name: "ADIDAS Mid Sneakers",
    description:
      "Ut hendrerit venenatis lacus, vel lacinia ipsum fermentum vel. Cras.",
    status: "Out of Stock",
  },
]; 
```

我们还假设需要为以下路径创建路由:

*   这将显示一个产品列表。
*   `/products/:productId`:如果有带`:productId`的产品，则显示产品数据，如果没有，则显示错误信息。

创建一个新文件`src/Products.js`并添加以下内容(确保从上面复制产品数据):

```
import React from "react";
import { Link, Route, useRouteMatch } from "react-router-dom";
import Product from "./Product";

const Products = ({ match }) => {
  const productData = [ ... ];
  const { url } = useRouteMatch();

  /* Create an array of `<li>` items for each product */
  const linkList = productData.map((product) => {
    return (
      <li key={product.id}>  <Link to={`${url}/${product.id}`}>{product.name}</Link>  </li>
    );
  });

  return (
    <div>  <div>  <div>  <h3>Products</h3>  <ul>{linkList}</ul>  </div>  </div>  <Route path={`${url}/:productId`}>  <Product data={productData} />  </Route>  <Route exact path={url}>  <p>Please select a product.</p>  </Route>  </div>
  );
};

export default Products; 
```

首先，我们使用`useRouteMatch`钩子从`match`对象中获取 URL。然后，我们使用每个产品的`id`属性构建一个`<Links>`组件列表，并将其存储在一个`linkList`变量中。

第一条路线使用了`path`属性中的变量，该变量对应于产品 ID 的变量。当它匹配时，我们呈现出`<Product>`组件(我们将在一分钟内定义它)，向它传递我们的产品数据:

```
<Route path={`${url}/:productId`}>  <Product data={productData} />  </Route> 
```

第二个路由有一个`exact`属性，所以只有当 URL 为`/products`并且没有选择任何内容时才会呈现。

现在，这里是`<Product>`组件的代码。您需要在`src/Product.js`创建这个文件:

```
import React from "react";
import { useParams } from "react-router-dom";

const Product = ({ data }) => {
  const { productId } = useParams();
  const product = data.find(p => p.id === Number(productId));
  let productData;

  if (product) {
    productData = (
      <div>  <h3>  {product.name}  </h3>  <p>{product.description}</p>  <hr />  <h4>{product.status}</h4>  </div>
    );
  } else {
    productData = <h2> Sorry. Product doesn't exist </h2>;
  }

  return (
    <div>  <div>{productData}</div>  </div>
  );
};

export default Product; 
```

`find`方法用于在数组中搜索 ID 属性等于`match.params.productId`的对象。如果产品存在，显示`productData`。否则，将显示“产品不存在”消息。

最后，更新您的`<App>`组件，如下所示:

```
import React from "react";
import { Link, Route, Switch } from "react-router-dom";
import Category from "./Category";
import Products from "./Products";

const Home = () => (
  <div>  <h2>Home</h2>  </div>
);

export default function App() {
  return (
    <div>  <nav className="navbar navbar-light">  <ul className="nav navbar-nav">  <li>  <Link to="/">Home</Link>  </li>  <li>  <Link to="/category">Category</Link>  </li>  <li>  <Link to="/products">Products</Link>  </li>  </ul>  </nav>  <Switch>  <Route exact path="/"><Home /></Route>  <Route path="/category"><Category /></Route>  <Route path="/products"><Products /></Route>  </Switch>  </div>
  );
} 
```

现在，当您在浏览器中访问应用程序并选择“产品”时，您将看到一个呈现的子菜单，它依次显示产品数据。

试玩一下演示。向自己保证一切正常，并且你理解代码中发生了什么。

## 保护路线

许多现代 web 应用程序的一个常见要求是确保只有登录的用户才能访问网站的某些部分。在下一节中，我们将看看如何实现一个受保护的路由，这样如果有人试图访问`/admin`，他们将被要求登录。

然而，我们需要首先介绍 React 路由器的几个方面。

### `Redirect`组件

与服务器端重定向一样，React 路由器的[重定向组件](https://reactrouter.com/web/api/Redirect)将用一个新位置替换历史堆栈中的当前位置。新位置由`to`属性指定。下面是我们将如何使用`<Redirect>`:

```
<Redirect to={{pathname: '/login', state: { from: location }}} 
```

因此，如果有人试图在注销时访问`/admin`路线，他们将被重定向到`/login`路线。关于当前位置的信息通过`state` prop 传递，因此如果认证成功，用户可以被重定向回他们最初试图访问的页面。

### 自定义路线

自定义路线是描述嵌套在组件中的路线的一种奇特方式。如果我们需要决定一条路线是否应该被渲染，那么编写一条自定义路线是一个不错的选择。

在`src`目录下新建一个文件`PrivateRoute.js`，添加以下内容:

```
import React from "react";
import { Redirect, Route, useLocation } from "react-router-dom";
import { fakeAuth } from './Login';

const PrivateRoute = ({ component: Component, ...rest }) => {
  const location = useLocation();

  return (
    <Route {...rest}>  {fakeAuth.isAuthenticated === true ?
        <Component />
      :
        <Redirect to={{ pathname: "/login", state: { from: location } }} />
      }  </Route>
  );
};

export default PrivateRoute; 
```

如你所见，在函数定义中，我们将收到的道具解构为一个`Component`道具和一个`rest`道具。`Component`道具将包含我们的`<PrivateRoute>`正在保护的组件(在我们的例子中，是`Admin`)。`rest`道具将包含我们已经通过的任何其他道具。

然后我们返回一个`<Route>`组件，根据用户是否登录，它要么呈现受保护的组件，要么将我们重定向到我们的`/login`路由。这在这里由一个从`<Login>`组件导入的`fakeAuth.isAuthenticated`属性决定。

这种方法的好处是它显然更具声明性，并且`<PrivateRoute>`是可重用的。

### 重要安全注意事项

在现实世界的应用程序中，**您需要验证对服务器**上受保护资源的任何请求。这是因为在客户端运行的任何东西都有可能被逆向工程和篡改。例如，在上面的代码中，人们可以打开 React 的开发工具并更改`isAuthenticated`的值，从而访问受保护的区域。

React 应用中的身份验证值得单独编写一个教程，但是实现它的一种方法是使用 [JSON Web 令牌](https://www.sitepoint.com/using-json-web-tokens-node-js/)。例如，您的服务器上可能有一个接受用户名和密码组合的端点。当它(通过 Ajax)收到这些凭证时，它会检查凭证是否有效。如果是，它用 React 应用程序保存的 JWT 进行响应(例如在`sessionStorage`中)，如果不是，它向客户端发回一个`401 Unauthorized`响应。

假设登录成功，客户端会将 JWT 作为报头与对受保护资源的任何请求一起发送。然后，在发送响应之前，服务器会对此进行验证。

存储密码时，**服务器不会以明文形式存储密码**。相反，它会加密它们——例如，使用 [bcryptjs](https://github.com/dcodeIO/bcrypt.js) 。

### 实现受保护的路由

现在，让我们实现我们的受保护路线。像这样改变:

```
import React from "react";
import { Link, Route, Switch } from "react-router-dom";
import Category from "./Category";
import Products from "./Products";
import Login from './Login';
import PrivateRoute from "./PrivateRoute";

const Home = () => (
  <div>  <h2>Home</h2>  </div>
);

const Admin = () => (
  <div>  <h2>Welcome admin!</h2>  </div>
);

export default function App() {
  return (
    <div>  <nav className="navbar navbar-light">  <ul className="nav navbar-nav">  <li>  <Link to="/">Home</Link>  </li>  <li>  <Link to="/category">Category</Link>  </li>  <li>  <Link to="/products">Products</Link>  </li>  <li>  <Link to="/admin">Admin area</Link>  </li>  </ul>  </nav>  <Switch>  <Route exact path="/"><Home /></Route>  <Route path="/category"><Category /></Route>  <Route path="/products"><Products /></Route>  <Route path="/login"><Login /></Route>  <PrivateRoute path="/admin" component={Admin} />  </Switch>  </div>
  );
} 
```

正如你所看到的，我们已经在文件的顶部添加了一个`<Admin>`组件，并且在`<Switch>`组件中包含了我们的`<PrivateRoute>`。如前所述，如果用户登录，这个定制路由会呈现`<Admin>`组件。否则，用户将被重定向到`/login`。

最后，下面是登录组件的代码:

```
import React, { useState } from "react";
import { Redirect, useLocation } from "react-router-dom";

export default function Login() {
  const { state } = useLocation();
  const { from } = state || { from: { pathname: "/" } };
  const [redirectToReferrer, setRedirectToReferrer] = useState(false);

  const login = () => {
    fakeAuth.authenticate(() => {
      setRedirectToReferrer(true);
    });
  };

  if (redirectToReferrer) {
    return <Redirect to={from} />;
  }

  return (
    <div>  <p>You must log in to view the page at {from.pathname}</p>  <button onClick={login}>Log in</button>  </div>
  );
}

/* A fake authentication function */
export const fakeAuth = {
  isAuthenticated: false,
  authenticate(cb) {
    this.isAuthenticated = true;
    setTimeout(cb, 100);
  }
}; 
```

到目前为止，希望这里没有什么太棘手的事情发生。我们使用[使用定位钩子](https://reactrouter.com/web/api/Hooks/uselocation)来访问路由器的`location`道具，从中我们获取`state`属性。然后我们使用[对象析构](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)来获取用户在被要求登录之前试图访问的 URL 的值。如果不存在，我们将它设置为`{ pathname: "/" }`。

然后我们使用 React 的`useState`钩子将一个`redirectToReferrer`属性初始化为`false`。根据该属性的值，用户要么被重定向到他们要去的地方(即用户已登录)，要么向用户显示一个按钮让他们登录。

一旦按钮被点击，就会执行`fakeAuth.authenticate`方法，该方法将`fakeAuth.isAuthenticated`设置为`true`，并(在一个回调函数中)将`redirectToReferrer`的值更新为`true`。这将导致组件重新呈现，用户被重定向。

## 工作演示

让我们把拼图拼在一起，好吗？这是我们使用 React router 构建的应用程序的最终演示。

[https://codesandbox.io/embed/react-router-demo-final-forked-r59jg?fontsize=14&hidenavigation=1&theme=dark&view=preview](https://codesandbox.io/embed/react-router-demo-final-forked-r59jg?fontsize=14&hidenavigation=1&theme=dark&view=preview)

## 摘要

正如您在本文中看到的，React Router 是一个强大的库，它补充了 React 来构建更好的声明式路由。与 React 路由器的早期版本不同，在 v5 中，一切都“只是组件”。此外，新的设计模式完全符合 React 的工作方式。

在本教程中，我们学习了:

*   如何设置和安装 React 路由器
*   路由的基础知识和一些基本组件，如`<Router>`、`<Route>`和`<Link>`
*   如何创建用于导航和嵌套路由的最小路由器
*   如何使用路径参数构建动态路由
*   如何使用 React 路由器的钩子及其更新的路由呈现模式

最后，我们学习了一些高级路由技术，用于制作受保护路由的最终演示。

## 分享这篇文章*