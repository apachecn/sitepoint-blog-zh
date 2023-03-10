# 构建一个带有用户登录和验证的 React.js 应用程序

> 原文：<https://www.sitepoint.com/tutorial-build-a-react-js-application-with-user-login-and-authentication/>

![Real-life user authentication](img/fd743d3076273b31ed40c748a2213e1b.png)

*本文最初发表于[风暴之路](https://stormpath.com/blog/build-a-react-app-with-user-authentication?utm_source=sitepoint&utm_medium=post&utm_content=build-a-react-app&utm_campaign=react-2017)。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

React(有时也称为 React.js)是构建 web UIs 的一种很棒的方式。 [Stormpath React SDK](https://github.com/stormpath/stormpath-sdk-react) 扩展了 React 和 React 路由器的路由和组件，允许您使用 Stormpath 解决常见的用户管理任务，如[认证](https://stormpath.com/product/authentication?utm_source=sitepoint&utm_medium=post&utm_content=build-a-react-app&utm_campaign=react-2017)和[授权](https://stormpath.com/product/authorization?utm_source=sitepoint&utm_medium=post&utm_content=build-a-react-app&utm_campaign=react-2017)。

最近，React 获得了相当多的关注，很容易理解为什么。React 允许您将复杂的 ui 转换成简单的可重用组件，这些组件可以很容易地组合在一起。

这篇文章将向您展示如何使用 Stormpath React SDK 添加允许人们注册、登录，甚至查看他们自己的用户配置文件的功能，从头开始构建 React 应用程序。

我们开始吧！

## React + Express.js 应用程序堆栈

由于我们正在从头开始构建我们的应用程序，我们将使用 [ES6](https://babeljs.io/docs/learn-es2015/) 和 [JSX](https://babeljs.io/docs/plugins/transform-react-jsx/) 来编写尽可能少的代码，以及用于用户功能的 Stormpath React SDK。

为了让您更好地了解我们将使用的工具:

*   让我们能够构建简单而强大的用户界面。
*   [React router](https://github.com/rackt/react-router)–组织 React 应用程序中的 URL 导航。
*   [ES6](https://babeljs.io/docs/learn-es2015/)——JavaScript 的下一个版本。允许我们编写真正的 JavaScript 类。
*   [JSX](https://babeljs.io/docs/plugins/transform-react-jsx/)–允许我们在 JavaScript 中放置 HTML，而不需要连接字符串。
*   [storm path](http://www.stormpath.com?utm_source=sitepoint&utm_medium=post&utm_content=build-a-react-app&utm_campaign=react-2017)–允许我们存储和验证用户，而无需为其创建自己的后端。
*   [storm path React SDK](https://github.com/stormpath/stormpath-sdk-react)–不费吹灰之力就将注册表单、登录页面和身份验证集成到 React 应用程序中。
*   Express 允许我们提供 HTML 和 JavaScript 文件。
*   [Express Stormpath](https://github.com/stormpath/express-stormpath)–允许我们通过 [Express](http://expressjs.com/en/index.html) 提供 storm path 的 API。
*   Webpack 允许我们将所有的 JavaScript 文件打包成一个包。
*   巴别塔允许我们将 ES6 和 JSX 转换成 ES5。
*   因为我们希望事情变得美好。

## 设置我们的 React + Express.js 项目

首先创建一个新的项目目录和一个`package.json`文件。

```
$ mkdir my-react-app
$ cd my-react-app
$ npm init --yes 
```

现在安装 Express、用于 Express 的 Stormpath 模块和主体解析器:

```
$ npm install --save express express-stormpath body-parser 
```

我们需要一个服务器来托管我们的应用程序，因此创建一个名为`server.js`的新文件，并将下面的代码放入其中:

```
var express = require('express');
var stormpath = require('express-stormpath');
var bodyParser = require('body-parser');

var app = express();

app.use(stormpath.init(app, {
  web: {
    produces: ['application/json']
  }
}));

app.on('stormpath.ready', function () {
  app.listen(3000, 'localhost', function (err) {
    if (err) {
      return console.error(err);
    }
    console.log('Listening at http://localhost:3000');
  });
}); 
```

太棒了。现在，我们可以通过创建一个名为`stormpath.yml`的新文件，并在其中添加以下代码，将它与 Stormpath 应用程序连接起来。是的，你确实需要用你自己的价值观来取代它。

```
client:
  apiKey:
    id: YOUR_API_KEY_ID
    secret: YOUR_API_KEY_SECRET
application:
  href: https://api.stormpath.com/v1/applications/XXXX <-- YOUR APP HREF 
```

到目前为止一切顺利。现在通过运行`$ node server.js`来尝试服务器。如果一切设置正确，您应该会看到:

```
Listening at http://localhost:3000 
```

如果您看到了这条消息，那么您已经成功地配置了您的服务器来与 Stormpath 对话，并公开了一个 REST API 供我们的 React 应用程序使用。

### 配置 Webpack

在你太兴奋之前，关掉服务器并安装 [Webpack](https://webpack.github.io/) ，这样我们就可以打包我们所有的客户端脚本(我们很快就会需要这个组织)。

```
$ npm install --save webpack
$ npm install --save-dev webpack-dev-middleware 
```

通过创建一个名为`webpack.config.js`的新文件来配置 Webpack，并将下面的代码放入其中:

```
var path = require('path');
var webpack = require('webpack');

module.exports = {
  entry: [
    './src/app'
  ],
  devtool: 'eval-source-map',
  output: {
    path: __dirname,
    filename: 'app.js',
    publicPath: '/js/'
  },
  module: {
    loaders: []
  }
}; 
```

这将查看我们的`/src/`目录(我们将很快创建)并将该目录下的所有脚本及其依赖项打包为一个模块。然后使用文件`/src/app.js`及其导出作为该模块的导出。最后，当它生成模块包时，它将在`/js/app.js`端点下通过 Express 提供服务。

但是为了让 Express 服务于 Webpack 文件，我们必须打开`server.js`并在它的顶部添加以下几行:

```
var webpack = require('webpack');
var config = require('./webpack.config'); 
```

然后在第`var app = express();`行之后立即添加:

```
var compiler = webpack(config);

app.use(require('webpack-dev-middleware')(compiler, {
  noInfo: true,
  publicPath: config.output.publicPath
})); 
```

正如我之前提到的，这将允许 [Webpack](https://webpack.github.io/) 拦截请求并为我们打包的`/js/app.js`文件提供服务。

### 配置巴别塔

因为我们将使用 ES6 和 JSX，我们需要将这些文件转换成 ES5(为了向后兼容非现代浏览器)。这就是巴别尔的用武之地。Babel 可以将我们的 ES6/JSX 文件作为输入，并将其转换成 ES5。

要使用 [Babel](http://babeljs.io/) ，首先安装一些依赖项:

```
$ npm install --save babel-core babel-runtime babel-loader babel-plugin-react-transform \
  babel-preset-es2015 babel-preset-react babel-preset-stage-0 
```

现在我们将指导 Babel 如何编译我们的文件，因此创建一个名为`. babelrc '的新文件，并添加以下代码:

```
{
  "presets": ["stage-0", "es2015", "react"]
} 
```

最后，为了让 Babel 使用 Webpack，我们需要编辑“webpack.config.js”并向“module.loaders”数组添加一个条目，如下所示:

```
module: {
  loaders: [{
    test: /\.js$/,
    loaders: ['babel'],
    include: path.join(__dirname, 'src')
  }]
} 
```

### Index.html 和自举

现在，在使用 React 之前，我们将准备应用程序的入口页面。这个页面将告诉浏览器在我们初始化 React 和我们的应用程序之前它必须加载什么。因此创建一个名为`build`的新目录，然后在其中放一个名为`index.html`的文件。我们的服务器将提供这个文件夹中的所有静态文件。

```
$ mkdir build
$ cd build
$ touch index.html 
```

然后在`index.html`中，输入以下内容:

```
<!doctype html>
<!--[if lt IE 7]><html class="no-js lt-ie9 lt-ie8 lt-ie7"><![endif]-->
<!--[if IE 7]><html class="no-js lt-ie9 lt-ie8"><![endif]-->
<!--[if IE 8]><html class="no-js lt-ie9"><![endif]-->
<!--[if gt IE 8]><!-->
<html class="no-js"><!--<![endif]-->
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <base href="/">
    <title></title>
    <meta name="description" content="">
    <meta name="viewport" content="width=device-width">
    <link rel="stylesheet" href="/css/bootstrap.min.css" />
  </head>
  <body>
    <div id="app-container"></div>
    <script src="/js/app.js"></script>
  </body>
</html> 
```

同样，在`build`目录下，创建一个名为`css`的新目录，并将引导程序下载到其中。将文件命名为`bootstrap.min.css`。

```
$ mkdir css
$ cd css
$ curl -O https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css
$ cd ../.. # return to /my-react-app 
```

现在，为了让我们的浏览器能够访问这些文件，我们需要对它们进行配置，以便通过 Express 提供服务。所以打开`server.js`，在文件顶部添加:

```
var path = require('path'); 
```

然后在`app.use(stormpath.init(app, ...));`一行下面加上:

```
app.get('/css/bootstrap.min.css', function (req, res) {
  res.sendFile(path.join(__dirname, 'build/css/bootstrap.min.css'));
});

app.get('*', function (req, res) {
  res.sendFile(path.join(__dirname, 'build/index.html'));
}); 
```

## React 是如何工作的？

现在我们已经完成了应用程序的框架，我们可以专注于构建 React 应用程序了。但是在我们写任何代码之前，让我们看看 React 是什么，它为我们做了什么。

### 成分

在 React 中，一切都是基于组件构建的。您可以将组件视为呈现 DOM 节点的东西。一个简单的 React 组件如下所示:

```
class HelloWorld extends React.Component {
  render() {
    return <span>Hello World!</span>;
  }
} 
```

这很简单。现在，如果您想将这个组件呈现到页面上，那么您所要做的就是导入 React，然后调用:

```
ReactDOM.render(
  <HelloWorld />,
  document.getElementById('hello-world-element')
); 
```

React 会将组件呈现给该元素。

当然，对于 React 组件来说，还有更多的东西，比如状态。下面是一个计数器组件的例子，它在添加到 DOM 时开始计数，在移除时停止计数。

```
class Counter extends React.Component {
  state = {
    current: 0
  }

  constructor() {
    super(arguments...);
    this.intervalId = null;
  }

  updateCounter() {
    this.setState({ counter: this.state.current + 1 });
  }

  componentWillMount() {
    this.setState({ counter: this.props.from || 0 });
    this.intervalId = setInterval(this.updateCounter.bind(this), 1000);
  }

  componentWillUnmount() {
    clearInterval(this.intervalId);
  }

  render() {
    return <span>{ this.state.current }</span>;
  }
} 
```

注意方法`componentWillMount()`和`componentWillUnmount()`。这些是组件生命周期方法，将在组件生命周期的不同点执行(在本例中，是挂载和卸载)。这些方法通常用于设置和拆除组件，并且是必须使用的，因为如果您试图在组件尚未挂载时设置组件的状态，React 将会出错。

还要注意`this.props.from`。成员`this.props`是传递给组件的所有属性(输入)的集合。可以如下所示设置组件的属性:

```
<Counter from="50" />
<Counter from={ myVariable } /> 
```

### JSX 变量

使用`{ nameOfVariable }`可以很容易地将变量插入到你的 JSX DOM 中，如下所示:

```
render() {
  var myVariable = 123;
  return <span>{ myVariable }</span>;
} 
```

### JSX 和保留的 JavaScript 标识符

因为 JSX 是 JavaScript，所以在使用 React 时需要知道一些注意事项。也就是说，当设置 React DOM 组件的属性时，不能使用`for`或`class`，因为它们被认为是保留的 JavaScript 标识符。为了解决这个问题，React 提出了应该使用的`htmlFor`和`className`。

为了说明这个问题，这是行不通的:

```
<label for="my-input" class="my-label">My Input</label> 
```

但是这将:

```
<label htmlFor="my-input" className="my-label">My Input</label> 
```

### 虚拟 DOM

在 React 中，所有组件都保存在自己的虚拟 DOM 中，而不是直接针对 DOM 工作。您可以将虚拟 DOM 视为 JavaScript 中的 DOM 实现(因为它确实是)。这个虚拟 DOM 然后被映射到一个真实的 DOM 元素。因此，当您呈现 React 组件时，React 将查看组件的 DOM 输出，将其与虚拟 DOM 中的表示进行比较，然后为真实 DOM 生成一个补丁。

这意味着您再也不用考虑手动操作 DOM 元素了。您所要做的就是告诉 React 您希望您的组件看起来是什么样子，它将负责以必要的方式转换 DOM(用最少的努力)。

### 安装 React 依赖项

现在，当我们熟悉 React 时，我们将通过安装一些 React 依赖项来开始:

```
$ npm install --save react react-dom react-router react-stormpath react-document-title history 
```

在我们开始编码之前，我们需要一个地方来放置我们的 React 文件，所以创建一个名为`src`的新目录，然后使用它作为您的工作目录。

```
$ mkdir src
$ cd src 
```

现在，让我们从我们的应用程序的入口开始。这将是我们设置 React 应用程序及其路由的地方。因此，创建一个名为`app.js`的新文件，并输入以下代码:

```
import React from 'react';
import ReactDOM from 'react-dom';
import { Router, IndexRoute, Route, browserHistory } from 'react-router';

ReactDOM.render(
  <Router history={browserHistory}>
  </Router>,
  document.getElementById('app-container')
); 
```

现在我们有了应用程序的基础。让我们继续导入 Stormpath SDK 和一些我们需要的东西。在您的`app.js`文件的顶部，添加导入语句:

```
import ReactStormpath, { Router, HomeRoute, LoginRoute, AuthenticatedRoute } from 'react-stormpath'; 
```

正如您在`app.js`中看到的，现在有两个冲突的`Router`导入。既然 [`ReactStormpath.Router`](https://github.com/stormpath/stormpath-sdk-react/blob/master/docs/api.md#router) 从 [`ReactRouter.Router`](https://github.com/rackt/react-router/blob/master/docs/API.md#router) 延伸而来，我们就不再需要它了。所以继续从`react-router`中移除`Router`导入。重要提示:留下其他 [ReactRouter](https://github.com/rackt/react-router) 进口，我们以后会需要它们。

现在，我们将初始化 Stormpath SDK。在 [`ReactDOM.render()`](https://facebook.github.io/react/blog/2015/10/01/react-render-and-top-level-api.html) 的正上方添加下面一行。

```
ReactStormpath.init(); 
```

那很容易！我们现在准备开始构建我们的页面。

## 母版页

在创建页面之前，我们必须设置路由器。路由器决定了我们如何在 React 应用程序中导航。我们将从创建共享根路由开始。这将作为我们的“主页”。即该路由下的所有路由都将共享相同的主组件(报头)。因此，将下面的代码放在`app.js`中的`<Router>`标记内，使其看起来像这样:

```
<Router history={browserHistory}>
  <Route path='/' component={MasterPage}>
  </Route>
</Router> 
```

如你所见，我们引用了`MasterPage`。还不存在的东西。因此，让我们继续在一个新目录中创建它，我们将其命名为`pages`，在我们的`src`文件夹中。

```
$ mkdir pages
$ cd pages 
```

现在创建一个名为`MasterPage.js`的新文件，并将以下代码添加到其中:

```
import React from 'react';
import { Link } from 'react-router';
import { LoginLink } from 'react-stormpath';
import DocumentTitle from 'react-document-title';

import Header from './Header';

export default class is extends React.Component {
  render() {
    return (
      <DocumentTitle title='My React App'>
        <div className='MasterPage'>
          <Header />
          { this.props.children }
        </div>
      </DocumentTitle>
    );
  }
} 
```

如您所见，我们还没有一个`Header`组件，所以让我们在同一个目录中创建一个名为`Header.js`的新文件，其内容如下:

```
import React from 'react';
import { Link } from 'react-router';
import { LoginLink, LogoutLink, Authenticated, NotAuthenticated } from 'react-stormpath';

export default class Header extends React.Component {
  render() {
    return (
      <nav className="navbar navbar-default navbar-static-top">
        <div className="container">
          <div id="navbar-collapse" className="collapse navbar-collapse">
            <ul className="nav navbar-nav">
              <li><Link to="/">Home</Link></li>
            </ul>
            <ul className="nav navbar-nav navbar-right">
            </ul>
          </div>
        </div>
      </nav>
    );
  }
} 
```

## 索引页

在我们的`MasterPage`中，注意属性`this.props.children`。这将包含我们路由器匹配的子路由的组成部分。所以如果我们有一条看起来像这样的路线:

```
<Route path='/' component={MasterPage}>
  <Route path='/hello' component={HelloPage} />
</Route> 
```

我们试图访问`/hello`。这个`this.props.children`数组将被一个`HelloPage`组件填充，因此这个组件将被呈现在我们的母版页中。

现在想象一下您试图访问`/`的场景。如果没有任何`this.props.children`，这将只呈现您的母版页，但内容为空。这就是 [`IndexRoute`](https://github.com/rackt/react-router/blob/master/docs/guides/basics/IndexRoutes.md) 发挥作用的地方。用一个 [`IndexRoute`](https://github.com/rackt/react-router/blob/master/docs/guides/basics/IndexRoutes.md) 你可以指定当你点击主页路径时应该呈现的组件(在我们的例子中是`/`)。

但是，在我们将 [`IndexRoute`](https://github.com/rackt/react-router/blob/master/docs/guides/basics/IndexRoutes.md) 添加到路由器之前，让我们在`pages`目录中创建一个名为`IndexPage.js`的新文件，并将以下内容添加到其中:

```
import { Link } from 'react-router';
import React, { PropTypes } from 'react';
import { LoginLink } from 'react-stormpath';

export default class IndexPage extends React.Component {
  render() {
    return (
      <div className="container">
        <h2 className="text-center">Welcome!</h2>
        <hr />
        <div className="jumbotron">
          <p>
            <strong>To my React application!</strong>
          </p>
          <p>Ready to begin? Try these Stormpath features that are included in this example:</p>
          <ol className="lead">
            <li><Link to="/register">Registration</Link></li>
            <li><LoginLink /></li>
            <li><Link to="/profile">Custom Profile Data</Link></li>
          </ol>
        </div>
      </div>
    );
  }
} 
```

现在让我们添加我们的 [`IndexRoute`](https://github.com/rackt/react-router/blob/master/docs/guides/basics/IndexRoutes.md) 。打开`app.js`，在标签`<Route path='/' component={MasterPage}>`内添加你的 [`IndexRoute`](https://github.com/rackt/react-router/blob/master/docs/guides/basics/IndexRoutes.md) ，如下图所示:

```
<Route path='/' component={MasterPage}>
  <IndexRoute component={IndexPage} />
</Route> 
```

## 登录页面

我们现在有了一个 React 应用程序，它显示了一个带有默认页面的页眉。但是我们还没有登录页面。因此，让我们创建一个名为“LoginPage.js”的新文件，并向其中添加一些内容:

```
import React from 'react';
import DocumentTitle from 'react-document-title';
import { LoginForm } from 'react-stormpath';

export default class LoginPage extends React.Component {
  render() {
    return (
      <DocumentTitle title={`Login`}>
        <div className="container">
          <div className="row">
            <div className="col-xs-12">
              <h3>Login</h3>
              <hr />
            </div>
          </div>
          <LoginForm />
        </div>
      </DocumentTitle>
    );
  }
} 
```

注意 [`LoginForm`](https://github.com/stormpath/stormpath-sdk-react/blob/master/docs/api.md#loginform) 分量。这是我们必须添加的所有内容，以便我们有一个完整的工作表单，人们可以从中注册。

但是在我们使用它之前，我们需要打开`app.js`并在我们的路由器中为页面添加一个路由。所以在标签`<Route path='/' component={MasterPage}>`中添加以下内容:

```
<LoginRoute path='/login' component={LoginPage} /> 
```

为了能够访问登录页面，我们需要将它添加到我们的菜单中。因此，继续打开`Header.js`，在元素`<ul className="nav navbar-nav navbar-right">`中添加以下内容:

```
<NotAuthenticated>
  <li>
    <LoginLink />
  </li>
</NotAuthenticated> 
```

如你所见，我们使用了 [`NotAuthenticated`](https://github.com/stormpath/stormpath-sdk-react/blob/master/docs/api.md#notauthenticated) 组件。这样，当用户还没有登录时，我们将只显示一个 [`LoginLink`](https://github.com/stormpath/stormpath-sdk-react/blob/master/docs/api.md#loginlink) 。

## 注册页面

现在，让我们添加一个人们可以注册的页面。我们就叫它`RegistrationPage`。因此，创建一个名为`RegistrationPage.js`的新文件，并将以下内容放入其中:

```
import React from 'react';
import DocumentTitle from 'react-document-title';
import { RegistrationForm } from 'react-stormpath';

export default class RegistrationPage extends React.Component {
  render() {
    return (
      <DocumentTitle title={`Registration`}>
        <div className="container">
          <div className="row">
            <div className="col-xs-12">
              <h3>Registration</h3>
              <hr />
            </div>
          </div>
          <RegistrationForm />
        </div>
      </DocumentTitle>
    );
  }
} 
```

注意，我们使用了 [`RegistrationForm`](https://github.com/stormpath/stormpath-sdk-react/blob/master/docs/api.md#registrationform) 组件。正如您可能已经猜到的，这将呈现一个 Stormpath 注册表。注册后，它会将用户指向登录页面，用户可以在那里登录。

才能访问此页面。我们需要添加一条路线。所以继续打开`app.js`，在标签`<Route path='/' component={MasterPage}>`内添加:

```
<Route path='/register' component={RegistrationPage} /> 
```

我们现在有了一条路线，但是除非我们链接到它，否则人们将无法找到该页面，所以打开`Header.js`并在`<ul className="nav navbar-nav navbar-right">`的结束标记(`</ul>`)之前添加以下内容:

```
<NotAuthenticated>
  <li>
    <Link to="/register">Create Account</Link>
  </li>
</NotAuthenticated> 
```

注意 [`NotAuthenticated`](https://github.com/stormpath/stormpath-sdk-react/blob/master/docs/api.md#notauthenticated) 组件的用法。这样，我们将只在用户未登录时显示`/register`链接。

## 个人资料页面

一旦用户登录，我们希望能够向他们显示一些个性化的内容(他们的用户数据)。因此，创建一个名为`ProfilePage.js`的新文件，并将以下代码放入其中:

```
import React from 'react';
import DocumentTitle from 'react-document-title';
import { UserProfileForm } from 'react-stormpath';

export default class ProfilePage extends React.Component {
  render() {
    return (
      <DocumentTitle title={`My Profile`}>
      <div className="container">
          <div className="row">
            <div className="col-xs-12">
              <h3>My Profile</h3>
              <hr />
            </div>
          </div>
          <div className="row">
            <div className="col-xs-12">
              <UserProfileForm />
            </div>
          </div>
        </div>
      </DocumentTitle>
    );
  }
} 
```

注意，我们使用了 [`UserProfileForm`](https://github.com/stormpath/stormpath-sdk-react/blob/master/docs/api.md#userprofileform) 。这是一个简单的助手表单，允许您编辑最基本的用户字段。

但是，为了实际修改用户配置文件，我们需要在服务器上做一些改变。因此，打开`server.js`并在`app.use(stormpath.init(app, ...));`下添加以下路线:

```
app.post('/me', bodyParser.json(), stormpath.loginRequired, function (req, res) {
  function writeError(message) {
    res.status(400);
    res.json({ message: message, status: 400 });
    res.end();
  }

  function saveAccount () {
    req.user.givenName = req.body.givenName;
    req.user.surname = req.body.surname;
    req.user.email = req.body.email;

    req.user.save(function (err) {
      if (err) {
        return writeError(err.userMessage || err.message);
      }
      res.end();
    });
  }

  if (req.body.password) {
    var application = req.app.get('stormpathApplication');

    application.authenticateAccount({
      username: req.user.username,
      password: req.body.existingPassword
    }, function (err) {
      if (err) {
        return writeError('The existing password that you entered was incorrect.');
      }

      req.user.password = req.body.password;

      saveAccount();
    });
  } else {
    saveAccount();
  }
}); 
```

这将允许表单更改用户的名字、姓氏、电子邮件和密码。

如果您希望编辑其他字段，只需定制 [`UserProfileForm`](https://github.com/stormpath/stormpath-sdk-react/blob/master/docs/api.md#userprofileform) 表单，并在上述路线中添加您希望编辑的字段。

现在，为了让我们从菜单访问该页面，打开`Header.js`，在`<li><Link to="/">Home</Link></li>`的正下方添加:

```
<Authenticated>
  <li>
    <Link to="/profile">Profile</Link>
  </li>
</Authenticated> 
```

这样，使用 [`Authenticated`](https://github.com/stormpath/stormpath-sdk-react/blob/master/docs/api.md#authenticated) 组件，当我们有一个用户会话时，我们将呈现一个到`/profile`页面的链接，并允许我们的用户查看他们的用户资料。

为了能够访问该页面，我们必须像其他页面一样将其添加到路由器中。打开`app.js`，在标签`<Route path='/' component={MasterPage}>`内添加:

```
<AuthenticatedRoute path='/profile' component={ProfilePage} /> 
```

注意，我们用的是 [`AuthenticatedRoute`](https://github.com/stormpath/stormpath-sdk-react/blob/master/docs/api.md#authenticatedroute) 。这是一个只有在有经过身份验证的用户会话时才能访问的路由。如果没有会话，那么用户会自动被重定向到 [`LoginLink`](https://github.com/stormpath/stormpath-sdk-react/blob/master/docs/api.md#loginlink) 的路径。

## 回家路线

现在我们已经设置了大部分路线。让我们来看一条叫做 [`HomeRoute`](https://github.com/stormpath/stormpath-sdk-react/blob/master/docs/api.md#homeroute) 的特殊路线。这条路线本身什么都不做。而是充当“标记”，以指示登录和注销时重定向到哪里。

因此，为了指定我们注销时想要结束的位置，打开`app.js`并更改:

```
<Route path='/' component={MasterPage}>
  ...
</Route> 
```

变成:

```
 <HomeRoute path='/' component={MasterPage}>
  ...
</HomeRoute> 
```

现在当注销时，Stormpath SDK 将知道它应该重定向到“/”路径。现在，要指定注销时重定向到哪里，请更改我们在上一步中创建的 [`AuthenticatedRoute`](https://github.com/stormpath/stormpath-sdk-react/blob/master/docs/api.md#authenticatedroute) :

```
<AuthenticatedRoute path='/profile' component={ProfilePage} /> 
```

这样看起来就像:

```
<AuthenticatedRoute>
  <HomeRoute path='/profile' component={ProfilePage} />
</AuthenticatedRoute> 
```

注意 [`AuthenticatedRoute`](https://github.com/stormpath/stormpath-sdk-react/blob/master/docs/api.md#authenticatedroute) 如何包裹 [`HomeRoute`](https://github.com/stormpath/stormpath-sdk-react/blob/master/docs/api.md#homeroute) 。这用于指示登录后我们希望重定向到的经过身份验证的路由。

## 注销

最后，一旦我们的用户注册并登录。我们想给他们注销的选择。好在加这个真的很简单。

所以打开`Header.js`并在`<ul className="nav navbar-nav navbar-right">`内将这段代码添加到末尾:

```
<Authenticated>
  <li>
    <LogoutLink />
  </li>
</Authenticated> 
```

注意 [`LogoutLink`](https://github.com/stormpath/stormpath-sdk-react/blob/master/docs/api.md#logoutlink) 分量。一旦点击此按钮，用户会话将被自动销毁，用户将被重定向到未经认证的 [`HomeRoute`](https://github.com/stormpath/stormpath-sdk-react/blob/master/docs/api.md#homeroute) 。

## 组件中的用户状态

通过请求经过[身份验证的](https://github.com/stormpath/stormpath-sdk-react/blob/master/docs/api.md#authenticated-bool)和[用户](https://github.com/stormpath/stormpath-sdk-react/blob/master/docs/api.md#user-object)上下文类型来访问组件中的用户状态:

```
class ContextExample extends React.Component {
  static contextTypes = {
    authenticated: React.PropTypes.bool,
    user: React.PropTypes.object
  };

  render() {
    if (!this.context.authenticated) {
      return (
        <div>
          You need to <LoginLink />.
        </div>
      );
    }

    return (
      <div>
        Welcome {this.context.user.username}!
      </div>
    );
  }
} 
```

## 导入组件

为了能够引用我们的页面，我们需要导入它们。为了便于导入，我们将它们放在一个`index.js`文件中，这样我们只需导入一次。因此，让我们在我们的`pages`目录中创建一个名为`index.js`的新文件，并从中导出我们的所有页面，如下所示:

```
export MasterPage from './MasterPage'
export IndexPage from './IndexPage'
export LoginPage from './LoginPage'
export RegistrationPage from './RegistrationPage'
export ProfilePage from './ProfilePage' 
```

这样，我们只需进行一次导入就可以访问我们所有的页面。

让我们开始吧。打开`app.js`文件，在文件顶部添加以下导入语句:

```
import { MasterPage, IndexPage, LoginPage, RegistrationPage, ProfilePage } from './pages'; 
```

## 运行项目

现在我们有了一个应用程序，用户可以注册、登录并显示他们的用户数据。所以我们来试试吧！

像以前一样，通过运行以下命令来启动我们的服务器:

```
$ node server.js 
```

如果一切运行顺利，您应该能够看到以下消息:

```
Listening at http://localhost:3000 
```

所以，在你的浏览器中打开 [http://localhost:3000](http://localhost:3000) 试试吧！

## 摘要

正如你在这篇文章中看到的，React 是一个非常强大的工具，当与 ES6、JSX 和 Stormpath 一起使用时，构建应用程序突然又变得有趣了。

如果有任何不确定的地方，请随意查看[示例项目](https://github.com/stormpath/stormpath-sdk-react)，并将其作为参考实现。我也很喜欢[这篇关于 React 设计的帖子](http://jlongster.com/Removing-User-Interface-Complexity,-or-Why-React-is-Awesome)——它用美丽的细节解释了 React 为什么如此棒。

如果你有关于 Stormpath React SDK 的问题，一定要查看它的 T2 API 文档。

## 分享这篇文章