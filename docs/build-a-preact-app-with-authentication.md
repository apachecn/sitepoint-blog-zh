# 构建带身份验证的 Preact 应用程序

> 原文：<https://www.sitepoint.com/build-a-preact-app-with-authentication/>

*这篇文章最初发表在[Okta 开发者博客](https://developer.okta.com/blog/2017/10/19/build-a-preact-app-with-authentication)上。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

React 是一个快速、轻量级的库，这使得它在 SPA(单页应用程序)生态系统中得到了快速采用。Preact 是一种更轻更快的反应替代产品，重量只有区区 3kb！对于不太复杂的应用程序，这可能是一个很好的选择。

在本教程中，您将使用 [Okta 登录小部件](https://developer.okta.com/code/javascript/okta_sign-in_widget)构建一个包含几个页面和用户认证的基本 Preact 应用程序。

## 使用 PreactCLI 引导您的应用

为了启动您的项目，您将使用 [NPM](https://www.npmjs.com/package/preact-cli) 安装 [PreactCLI](https://github.com/developit/preact-cli) 。

```
npm install -g preact-cli 
```

安装 CLI 后，运行命令创建基本 Preact 应用程序:

```
preact create okta-preact-example 
```

这个命令将持续几分钟，搭建一个基本的 Preact 应用程序并安装所有的依赖项。一旦完成，您应该会在命令行上看到一堆信息，通知您下一步可以做什么。

进入应用程序目录。

```
cd okta-preact-example 
```

然后启动应用程序，只是为了确保一切按预期工作。

```
npm start 
```

您应该会看到一个快速构建运行，屏幕会清空并显示应用程序正在`http://localhost:8080`运行。当您在浏览器中打开该 URL 时，您应该会看到如下页面:

![Base Preact Application](img/f1e86695e9878648b4b34a2263173fd6.png)

### 关于 PreactCLI 的一些注意事项

尽管 PreactCLI 生成的应用程序看起来很像由 [create-react-app](https://github.com/facebookincubator/create-react-app) 生成的 react 应用程序，并且您甚至可以在 Preact 应用程序中使用一些 React 插件(如 [React-Router](https://reacttraining.com/react-router/) ),但还是有一些显著的差异。

例如，与 ReactCLI 不同，没有办法`eject`配置 [Webpack](https://webpack.js.org/) 。相反，Preact 鼓励开发人员通过创建一个名为`preact.config.js`的文件来定制 Webpack，使用 Preact 的 [Webpack 配置助手](https://github.com/developit/preact-cli/blob/HEAD/docs/webpack-helpers.md)并导出函数来改变 Webpack 的行为方式。

即使 PreactCLI 说应用程序在`http://0.0.0.0:8080`运行，也要使用`http://localhost:8080`。这是同样的事情，当你在 Okta 仪表板中设置你的应用程序时，你将设置`http://localhost:8080`作为你的基本 URL 和回调 URL，所以这只是确保你可以调用 Okta APIs。

## 创建您的 Okta 应用程序

现在您已经有了应用程序的基本外壳，是时候添加用户身份验证了。如果您还没有帐户，请在 [Okta](https://developer.okta.com/signup/) 创建一个免费(永久)帐户。

一旦你创建了一个帐户，进入管理面板，点击页面菜单中的“应用程序”。然后单击绿色的“添加应用程序”按钮，然后单击绿色的“创建新应用程序”按钮，这样您会看到一个模式窗口，如下所示:

![Create Application Screen](img/9be356eae7f71b5ce835a79d7e647985.png)

从站台按钮中选择“SPA”。单击“下一步”按钮创建您的应用程序。

这将带您进入创建应用程序向导的“应用程序设置”页面。在应用程序名称字段中输入“OktaPreactExample ”,并添加`http://localhost:8080`作为您的基本 URI 和登录重定向 URI。完成后，您的表单应该如下所示:

![Create Integration Screen](img/fd76678a14c35c0a7a1ac2d738c88b20.png)

您需要创建一个用户(如果您还没有用户的话),并将您的新应用程序分配给他们。或者您可以使用您用来登录 Okta 帐户的凭证(admin 用户)登录。

## 安装 Okta 登录小工具

让 Okta 的认证进入你的新 Preact 应用程序的最简单的方法是使用 Okta 的[登录小工具](https://github.com/okta/okta-signin-widget)。您将使用 npm 安装它:

```
npm install @okta/okta-signin-widget --save 
```

您还需要安装`preact-router`,包括:

```
npm install preact-router --save 
```

## 添加授权高阶组件

完成后，现在需要添加一些高阶组件来帮助认证。

在`/src/lib`文件夹中添加一个名为`auth.js`的文件，并添加以下代码:

```
import {h} from 'preact';
import { route } from 'preact-router';
import OktaSignIn from '@okta/okta-signin-widget/dist/js/okta-sign-in.min.js';
import '@okta/okta-signin-widget/dist/css/okta-sign-in.min.css';
import '@okta/okta-signin-widget/dist/css/okta-theme.css';

class Auth {
  constructor() {
    this.login = this.login.bind(this);
    this.logout = this.logout.bind(this);
    this.isAuthenticated = this.isAuthenticated.bind(this);
    this.handleAuthentication = this.handleAuthentication.bind(this);

    this.widget = new OktaSignIn({
      baseUrl: 'https://{yourOktaDomain}.com/',
      clientId: '{clientId}',
      redirectUri: 'http://localhost:8080',
      authParams: {
        responseType: ['id_token', 'token'],
        scopes: ['openid', 'email', 'profile']
      }
    });
  }

  isAuthenticated() {
    // Checks if there is a current accessToken in the TokenManger.
    return !!this.widget.tokenManager.get('accessToken');
  }

  getCurrentUser(){
    return this.widget.tokenManager.get('idToken');
  }

  login() {
    // Redirect to the login page
    route('/login/');
  }

  async logout() {
    this.widget.tokenManager.clear();
    await this.widget.signOut();
    location = '/';
  }

  handleAuthentication(tokens) {
    for (let token of tokens) {
      if (token.idToken) {
        this.widget.tokenManager.add('idToken', token);
      } else if (token.accessToken) {
        this.widget.tokenManager.add('accessToken', token);
      }
    }
  }
}

// create a singleton
const auth = new Auth();
export const withAuth = WrappedComponent => props =>
  <WrappedComponent auth={auth} {...props} />; 
```

在第一行代码中，您可以看出一些不同之处。Preact 中的`h`模块将 [JSX](https://facebook.github.io/react/docs/introducing-jsx.html) 转换成 [DOM 元素](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction)。通常，React 会使用`React`库来生成`React.createElement`语句，从而从 JSX 生成 DOM 元素。Preact 使用`h`库来生成类似于`h('div', {class:'something'}, 'Content')`的语句来完成这项工作。

接下来，您从 preact-router 导入了位于`h`导入正下方的`route`。这是 Preact 用来在`login`函数中进行重定向的。注意，`Auth`类只是一个常规函数，并没有扩展`Component`。在构造函数中，内部函数与来自 Auth 类的`this`上下文绑定在一起。

然后在 Okta 登录小部件配置中输入您的 Okta 组织 URL 和客户端 ID。您的组织 URL 将是您登录 Okta 帐户(例如 http://dev-12345.oktapreview.com)时使用的 URL，您可以从应用程序的“常规”选项卡上的管理仪表板的应用程序属性页中获取您的客户 ID(显然，您的 ID 不会被模糊掉):

![Client ID Screen](img/b4c0093822212ab4174b6d7ea0dbd0af.png)

您还需要将`redirectUri`属性更改为`http://localhost:8080`，因为对于普通的 react 应用程序，Preact 使用端口`8080`而不是`3000`。

`login`函数只是将用户路由到登录页面，而`logout`函数清除保存在小部件令牌管理器中的令牌，调用小部件上的`signOut`，并将用户重定向到应用程序的根目录。

最后，创建一个由所有组件共享的`Auth`类的单例，并作为一个名为`auth`的道具传递给用`withAuth`包装的任何组件。

## 创建小部件包装器

在您的`/src/lib`文件夹中创建一个名为`OktaSignInWidget.js`的文件。输入此组件的代码:

```
import { h, Component } from 'preact';

export default class OktaSignInWidget extends Component {
  componentDidMount() {
    this.widget = this.props.widget;
    this.widget.renderEl({ el: this.widgetContainer }, this.props.onSuccess, this.props.onError);
  }

  componentWillUnmount() {
    this.widget.remove();
  }

  render() {
    return <div ref={(div) => { this.widgetContainer = div; }} />
  }
}; 
```

这里，`componentDidMount`方法将 Okta 登录小部件呈现给呈现方法中的`div`,而`componentWillUnmount`函数移除小部件。

在`render`方法中，有一些看起来很奇怪的代码。这允许您将对当前元素的引用设置到一个名为`widgetContainer`的变量中，然后在`componentDidMount`中将它用作`this.widgetContainer`。很整洁，是吧？感谢[马特·雷布尔](https://twitter.com/mraible)给我展示了那个魔术！

## 创建重定向组件

React 路由器有一个重定向组件，但是 Preact 路由器没有，所以您需要一个。幸运的是，创建你自己的很容易。在您的`/src/lib`文件夹中创建一个名为`Redirect.js`的文件，并添加以下代码:

```
import { Component } from 'preact';

export default class Redirect extends Component {
  componentWillMount() {
    location = this.props.to.pathname;
  }

  render() {
    return null;
  }
} 
```

这只是一个组件，它将根据传递给它的 URL 进行重定向。在这种情况下，将使用`window.location`机制重定向使用，主要是因为您想要刷新页面。您也可以只使用`route(this.props.to.pathname)`并让 Preact 的路由器重定向用户。

## 创建登录组件

接下来，在`src/routes`中创建一个`Login`文件夹。在那个文件夹中创建一个`index.js`文件和一个`style.css`文件。这只是遵循了 Preact CLI 创建组件的方式。

在`index.js`文件中，创建一个`Login`组件，包装在`withAuth`组件中。首先，通过导入所需的模块:

```
import { h, Component } from 'preact';
import Redirect from '../../lib/Redirect';
import OktaSignInWidget from '../../lib/OktaSignInWidget';
import { withAuth } from '../../lib/auth'; 
```

通过封装您之前创建的`withAuth`高阶组件来启动组件，并设置开始状态。这里，默认情况下将`redirectToReferrer`设置为 false。

```
export default withAuth(class Login extends Component {
  state = {
    redirectToReferrer: false
  };
} 
```

在`componentWillMount`生命周期函数中，连接`onSuccess`和`onError`函数，并在组件中创建它们。

```
componentWillMount() {
  this.onSuccess = this.onSuccess.bind(this);
  this.onError = this.onError.bind(this);
}

onSuccess(tokens) {
  this.props.auth.handleAuthentication(tokens);
  this.setState({
    redirectToReferrer: true
  });
}

onError(err) {
  console.log('error logging in', err);
} 
```

您会注意到，您的组件将身份验证的处理传递回更高级别的组件。这是 JavaScript 中高阶组件和组合好处的主要例子。

最后，创建`render`函数，该函数将决定显示登录小部件，或者如果用户已经登录，将他们重定向到主页。当用户被重定向到登录页面时，您也可以将他们重定向到他们要去的页面，但是现在让我们跳过这一步。

```
render({location, auth}, {redirectToReferrer}) {
  let from;
  if (location && location.state) {
    from = location.state;
  } else {
    from = { pathname: '/' };
  }

  if (auth.isAuthenticated() || redirectToReferrer) {
    return <Redirect to={from} />;
  }

  return (
    <OktaSignInWidget
      widget={auth.widget}
      onSuccess={this.onSuccess}
      onError={this.onError} />
  );
} 
```

您会注意到这里的 Preact 有一点不同，它提供了`props`和`state`的句柄作为`render`函数的参数。这段代码简单地使用了这些参数的析构，使得使用`location`、`auth`和`redirectToReferrer`变得简单，而不需要太多的`this.props.{whatever}`。

因此，您最终的`/src/routes/login/index.js`文件将看起来像这样:

```
import { h, Component } from 'preact';
import Redirect from '../../lib/Redirect';
import OktaSignInWidget from '../../lib/OktaSignInWidget';
import { withAuth } from '../../lib/auth';

export default withAuth(class Login extends Component {
  state = {
    redirectToReferrer: false
  };

  componentWillMount() {
    this.onSuccess = this.onSuccess.bind(this);
    this.onError = this.onError.bind(this);
  }

  onSuccess(tokens) {
    this.props.auth.handleAuthentication(tokens);
    this.setState({
      redirectToReferrer: true
    });
  }

  onError(err) {
    console.log('error logging in', err);
  }

 render({location, auth}, {redirectToReferrer}) {
    let from;
    if (location && location.state) {
      from = location.state;
    } else {
      from = { pathname: '/' };
    }

    if (auth.isAuthenticated() || redirectToReferrer) {
      return <Redirect to={from} />;
    }

    return (
      <OktaSignInWidget
        widget={auth.widget}
        onSuccess={this.onSuccess}
        onError={this.onError} />
    );
  }
}) 
```

## 更新个人资料页面

现在您已经有了`Login`组件，它正在使用 Okta 登录小部件，使用您创建的`auth`组件并更新配置文件页面(在`/src/routes/profile/index.js`中)以提供更多关于用户的信息。这是您最终的`/src/routes/profile/index.js`文件的样子:

```
import { h, Component } from 'preact';
import { route } from 'preact-router';
import { withAuth } from '../../lib/auth';
import style from './style';

export default withAuth(class Profile extends Component {
  constructor(props){
    super(props);
  }

  componentWillMount(){
    if(this.props.auth.isAuthenticated()){
      this.state = {
        user: this.props.auth.getCurrentUser()
      };
    }else{
      return route('/login/');
    }
  }

  render(props, { user }) {
    return (
      user ?
      <div class={style.profile}>
        <h3 class={style.heading}>Profile</h3>
        <ul>
          <li>
            <span class={style.key}>Name:</span>
            <span class={style.value}>{user.claims.name}</span>
          </li>
          <li>
            <span class={style.key}>Email:</span>
            <span class={style.value}>{user.claims.email}</span>
          </li>
        </ul>
      </div> :
      null
    );
  }
}) 
```

您已经在组件的`componentWillMount`函数中添加了`Component`级别的认证保护。如果用户通过了身份验证，它将调用高阶组件上的`getCurrentUser`函数，并将用户添加到组件的状态中。在`render`函数中，您只需输出用户名和电子邮件。

## 更新标题组件

现在，您只需要将路线放入您的应用程序，并让菜单链接到它们。首先将`/src/components/header/index.js`文件改为:

```
import { h, Component } from 'preact';
import { Link } from 'preact-router/match';
import { withAuth } from '../../lib/auth';
import style from './style';

export default withAuth(class Header extends Component {
  componentWillMount(){
    if(this.props.auth.isAuthenticated()){
      this.setState({
          user: this.props.auth.getCurrentUser()
      });
    }
  }

  render(props, {user}) {
    return (
      <header class={style.header}>
        <h1>Preact App</h1>
        <nav>
          <Link activeClassName={style.active} href="/">Home</Link>
          {user ? <Link activeClassName={style.active} href="/profile">Profile</Link> : null }
          {user
            ? <Link activeClassName={style.active} onClick={props.auth.logout.bind(null, props.history)} href="javascript:;">Logout</Link>
            : <Link activeClassName={style.active} onClick={props.auth.login.bind(null, props.history)} href="javascript:;">Login</Link>}
        </nav>
      </header>
    );
  }
}) 
```

如果用户未登录，将显示“登录”按钮，如果用户已登录，将显示“注销”按钮。它还将只向那些已登录的用户显示“个人资料”菜单项。

## 改变你的路线

最后，更改您的`/src/components/app.js`文件中的路由，以便您的应用程序知道您的新路由以及如何处理它们。因此，您的新`app.js`文件将看起来像这样:

```
import { h, Component } from 'preact';
import { Router } from 'preact-router';

import Header from './header';
import Home from '../routes/home';
import Profile from '../routes/profile';
import Login from '../routes/Login';
// import Home from 'async!./home';
// import Profile from 'async!./profile';

export default class App extends Component {
  /** Gets fired when the route changes.
   *  @param {Object} event   "change" event from [preact-router](http://git.io/preact-router)
   *  @param {string} event.url The newly routed URL
   */
  handleRoute = e => {
    this.currentUrl = e.url;
  };

  render() {
    return (
      <div id="app">
        <Header />
        <Router onChange={this.handleRoute}>
          <Home path="/" />
          <Profile path="/profile/" />
          <Login path="/login/" />
        </Router>
      </div>
    );
  }
} 
```

真正改变的是，您导入了新创建的登录组件，删除了传递给配置文件组件的用户属性，并为登录组件添加了一个新的路由。

## 运行您的新 Preact 应用程序！

现在，您应该能够保存您的工作并在根文件夹中运行`npm start`,并通过 Okta 查看带有用户认证的全功能 Preact 应用程序！

Preact 和 react 之间有许多相似之处，但也有一些关键的区别。Preact 适用于要求下载量保持较小的应用程序。Preact 路由器有一些很好的便利，但是也缺少一些东西(比如`withRouter`)。还有一些简洁的便利，比如将`props`和`state`传递给`render`函数。总而言之，我认为 Preact 很棒，但我认为确实需要一个成熟的 react 来处理复杂的应用程序。

## 了解更多信息

你可以从[他们的网站](https://preactjs.com/)找到更多关于 Preact 的信息，从 [Github 库](https://github.com/developit/preact-router)找到 Preact-Router。

您也可以从 [Okta 开发者 Github 资源库](https://github.com/oktadeveloper/okta-preact-widget-example)获得本文的完整代码。

一如既往，如果你对文章、准则、Preact 或 Okta 有任何问题、评论或担忧，请随时通过[电子邮件](mailto:lee.brandt@okta.com)联系我，或在评论中或通过 Twitter [@leebrandt](https://twitter.com/leebrandt) 联系我。

## 分享这篇文章