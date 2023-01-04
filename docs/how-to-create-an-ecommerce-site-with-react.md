# 如何用 React 创建电子商务网站

> 原文：<https://www.sitepoint.com/how-to-create-an-ecommerce-site-with-react/>

在本教程中，我们将看看如何用 React 构建一个非常简单的电子商务 web 应用程序。它不会是下一个 Shopify，但希望它可以作为一种有趣的方式来展示 React 是如何适合构建动态和迷人的用户界面。

该应用程序将演示一个基本的购物车管理系统，以及处理用户认证的简单方法。我们将利用 React Context 作为 Redux 或 MobX 等状态管理框架的替代方案，并且我们将使用 json-server 包创建一个伪后端。

下面是我们将要构建的内容的屏幕截图:

![The finished app](img/ede860a83aa3a96462d1a7d1e32205d0.png)

该应用程序的代码可在 [GitHub](https://github.com/sitepoint-editors/React-Ecommerce) 上获得。

## 先决条件

本教程假设您对 JavaScript 和 React 有基本的了解。如果你是反应新手，你可能想看看我们的[初学者指南](https://www.sitepoint.com/getting-started-react-beginners-guide/)。

要构建该应用程序，您需要在 PC 上安装 Node 的最新版本。如果不是这样，那么就去节点主页[下载适合您系统的正确二进制文件](https://nodejs.org/en/download/)。或者，您可以考虑使用版本管理器来安装 Node。我们有一个关于使用版本管理器的[教程。](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)

Node 与 npm 捆绑在一起，NPM 是 JavaScript 的一个包管理器，我们将使用它来安装一些将要使用的库。您可以[在这里](https://www.sitepoint.com/beginners-guide-node-package-manager/)了解更多关于使用 npm 的信息。

您可以通过从命令行发出以下命令来检查两者是否安装正确:

```
node -v
> 12.18.4

npm -v
> 6.14.8 
```

完成之后，让我们开始用 [Create React App](https://www.npmjs.com/package/create-react-app) 工具创建一个新的 React 项目。您可以全局安装，或者使用`npx`，如下所示:

```
npx create-react-app e-commerce 
```

完成后，切换到新创建的目录:

```
cd e-commerce 
```

在这个应用程序中，我们将使用 [React 路由器](https://www.npmjs.com/package/react-router)来处理路由。要安装此模块，请运行:

```
npm install react-router-dom 
```

我们还需要 [json-server](https://www.npmjs.com/package/json-server) 和 [json-server-auth](https://www.npmjs.com/package/json-server-auth) 来创建我们的伪后端来处理认证:

```
npm install json-server json-server-auth 
```

我们将需要 [axios](https://www.npmjs.com/package/axios) 向我们的伪后端发出 Ajax 请求。

```
npm install axios 
```

我们还需要 [jwt-decode](https://www.npmjs.com/package/jwt-decode) ,这样我们就可以解析我们的后端将响应的 jwt:

```
npm install jwt-decode 
```

最后，我们将使用[布尔玛 CSS 框架](https://www.npmjs.com/package/bulma)来设计这个应用程序。要安装它，请运行以下命令:

```
npm install bulma 
```

## 入门指南

首先，我们需要将样式表添加到应用程序中。为了实现这一点，我们将添加一个导入语句，将这个文件包含在`src`文件夹的`index.js`文件中。这将在应用程序的所有组件中应用样式表:

```
import "bulma/css/bulma.css"; 
```

### 上下文设置

如前所述，我们将在整个应用中使用 [React Context](https://reactjs.org/docs/context.html) 。这是 React 的一个相对较新的特性，它提供了一种通过组件树传递数据的方式，而不必在每一层手动传递属性。

如果你想复习一下在 React 应用程序中使用上下文，可以看看我们的教程“[如何用 React 钩子和上下文 API](https://www.sitepoint.com/replace-redux-react-hooks-context-api/) 替换 Redux”。

在通常需要上下文的复杂应用程序中，可以有多个上下文，每个上下文都有自己的数据和方法，这些数据和方法与需要这些数据和方法的组件集相关。例如，可以有一个`ProductContext`用于处理使用产品相关数据的组件，另一个`ProfileContext`用于处理与认证和用户数据相关的数据。然而，为了使事情尽可能简单，我们将只使用一个上下文实例。

为了创建上下文，我们在应用程序的`src`目录中创建了一个`Context.js`文件和一个`withContext.js`文件:

```
cd src
touch Context.js withContext.js 
```

然后将以下内容添加到`Context.js`:

```
import React from "react";
const Context = React.createContext({});
export default Context; 
```

这将创建上下文并将上下文数据初始化为一个空对象。接下来，我们需要创建一个组件包装器，我们将使用它来包装使用上下文数据和方法的组件:

```
// src/withContext.js

import React from "react";
import Context from "./Context";

const withContext = WrappedComponent => {
  const WithHOC = props => {
    return (
      <Context.Consumer>  {context => <WrappedComponent {...props} context={context} />}  </Context.Consumer>
    );
  };

  return WithHOC;
};

export default withContext; 
```

这可能看起来有点复杂，但本质上它所做的只是制作一个[高阶组件](https://www.sitepoint.com/react-higher-order-components/)，它将我们的上下文附加到一个包装组件的道具上。

稍微分解一下，我们可以看到`withContext`函数将一个 React 组件作为它的参数。然后，它返回一个函数，该函数将组件的道具作为参数。在返回的函数中，我们将组件包装在我们的上下文中，然后将上下文作为属性分配给它:`context={context}`。`{...props}`位确保组件保留最初传递给它的任何属性。

所有这些意味着我们可以在整个应用程序中遵循这种模式:

```
import React from "react";
import withContext from "../withContext";

const Cart = props => {
  // We can now access Context as props.context
};

export default withContext(Cart); 
```

## 搭建应用程序

现在，让我们为我们的应用程序的基本导航功能创建一个组件的框架版本。这些是`AddProducts`、`Cart`、`Login`和`ProductList`，我们将把它们放在`src`目录内的`components`目录中:

```
mkdir components
cd components
touch AddProduct.js Cart.js Login.js ProductList.js 
```

在`AddProduct.js`中添加:

```
import React from "react";

export default function AddProduct() {
  return <>AddProduct</>
} 
```

在`Cart.js`中添加:

```
import React from "react";

export default function Cart() {
  return <>Cart</>
} 
```

在`Login.js`中添加:

```
import React from "react";

export default function Login() {
  return <>Login</>
} 
```

最后，在`ProductList.js`中添加:

```
import React from "react";

export default function ProductList() {
  return <>ProductList</>
} 
```

接下来，我们需要设置`App.js`文件。在这里，我们将处理应用程序的导航，并定义其数据和管理方法。

首先，让我们设置导航。将`App.js`更改如下:

```
import React, { Component } from "react";
import { Switch, Route, Link, BrowserRouter as Router } from "react-router-dom";

import AddProduct from './components/AddProduct';
import Cart from './components/Cart';
import Login from './components/Login';
import ProductList from './components/ProductList';

import Context from "./Context";

export default class App extends Component {
  constructor(props) {
    super(props);
    this.state = {
      user: null,
      cart: {},
      products: []
    };
    this.routerRef = React.createRef();
  }

  render() {
    return (
      <Context.Provider
        value={{
          ...this.state,
          removeFromCart: this.removeFromCart,
          addToCart: this.addToCart,
          login: this.login,
          addProduct: this.addProduct,
          clearCart: this.clearCart,
          checkout: this.checkout
        }}
      >  <Router ref={this.routerRef}>  <div className="App">  <nav
            className="navbar container"
            role="navigation"
            aria-label="main navigation"
          >  <div className="navbar-brand">  <b className="navbar-item is-size-4 ">ecommerce</b>  <label
                role="button"
                class="navbar-burger burger"
                aria-label="menu"
                aria-expanded="false"
                data-target="navbarBasicExample"
                onClick={e => {
                  e.preventDefault();
                  this.setState({ showMenu: !this.state.showMenu });
                }}
              >  <span aria-hidden="true"></span>  <span aria-hidden="true"></span>  <span aria-hidden="true"></span>  </label>  </div>  <div className={`navbar-menu ${
                  this.state.showMenu ? "is-active" : ""
                }`}>  <Link to="/products" className="navbar-item"> Products </Link>  {this.state.user && this.state.user.accessLevel < 1 && (
                  <Link to="/add-product" className="navbar-item"> Add Product </Link>
                )}  <Link to="/cart" className="navbar-item"> Cart <span
                    className="tag is-primary"
                    style={{ marginLeft: "5px" }}
                  >  { Object.keys(this.state.cart).length }  </span>  </Link>  {!this.state.user ? (
                  <Link to="/login" className="navbar-item"> Login </Link>
                ) : (
                  <Link to="/" onClick={this.logout} className="navbar-item"> Logout </Link>
                )}  </div>  </nav>  <Switch>  <Route exact path="/" component={ProductList} />  <Route exact path="/login" component={Login} />  <Route exact path="/cart" component={Cart} />  <Route exact path="/add-product" component={AddProduct} />  <Route exact path="/products" component={ProductList} />  </Switch>  </div>  </Router>  </Context.Provider>
    );
  }
} 
```

我们的`App`组件将负责初始化应用程序数据，还将定义操作这些数据的方法。首先，我们使用`Context.Provider`组件定义上下文数据和方法。数据和方法作为属性`value`在`Provider`组件上传递，以替换在上下文创建时给出的对象。(注意，该值可以是任何数据类型。)我们传递状态值和一些方法，我们很快会定义它们。

接下来，我们构建我们的应用程序导航。为了实现这一点，我们需要用一个`Router`组件包装我们的应用程序，这个组件可以是`BrowserRouter`(就像我们的例子)或者是`HashRouter`。接下来，我们使用`Switch`和`Route`组件定义应用程序的路由。我们还创建了应用程序的导航菜单，每个链接都使用 React 路由器模块中提供的`Link`组件。我们还向`Router`组件添加了一个引用`routerRef`，使我们能够从`App`组件内部访问路由器。

为了测试这一点，转到项目根(例如，`/files/jim/Desktop/e-commerce`)并使用`npm start`启动 Create React App dev 服务器。一旦启动，您的默认浏览器应该会打开，您应该会看到我们的应用程序的框架。请务必点击周围，并确保所有的导航工作。

## 制造一个假的后端

下一步，我们将建立一个假的后端来存储我们的产品并处理用户认证。如上所述，为此我们将使用 json-server 创建一个假的 REST API，并使用 json-server-auth 向我们的应用程序添加一个简单的[基于 JWT 的](https://jwt.io/)认证流。

json-server 的工作方式是从文件系统中读入一个 json 文件，并使用该文件创建一个内存中的数据库，并使用相应的端点与之进行交互。现在让我们创建 JSON 文件。在项目的路径中，创建一个新的`backend`文件夹，并在该文件夹中创建一个新的`db.json`文件:

```
mkdir backend
cd backend
touch db.json 
```

打开`db.json`，添加以下内容:

```
{
  "users": [
    {
      "email": "regular@example.com",
      "password": "$2a$10$2myKMolZJoH.q.cyXClQXufY1Mc7ETKdSaQQCC6Fgtbe0DCXRBELG",
      "id": 1
    },
    {
      "email": "admin@example.com",
      "password": "$2a$10$w8qB40MdYkMs3dgGGf0Pu.xxVOOzWdZ5/Nrkleo3Gqc88PF/OQhOG",
      "id": 2
    }
  ],
  "products": [
    {
      "id": "hdmdu0t80yjkfqselfc",
      "name": "shoes",
      "stock": 10,
      "price": 399.99,
      "shortDesc": "Nulla facilisi. Curabitur at lacus ac velit ornare lobortis.",
      "description": "Cras sagittis. Praesent nec nisl a purus blandit viverra. Ut leo. Donec quam felis, ultricies nec, pellentesque eu, pretium quis, sem. Fusce a quam."
    },
    {
      "id": "3dc7fiyzlfmkfqseqam",
      "name": "bags",
      "stock": 20,
      "price": 299.99,
      "shortDesc": "Nulla facilisi. Curabitur at lacus ac velit ornare lobortis.",
      "description": "Cras sagittis. Praesent nec nisl a purus blandit viverra. Ut leo. Donec quam felis, ultricies nec, pellentesque eu, pretium quis, sem. Fusce a quam."
    },
    {
      "id": "aoe8wvdxvrkfqsew67",
      "name": "shirts",
      "stock": 15,
      "price": 149.99,
      "shortDesc": "Nulla facilisi. Curabitur at lacus ac velit ornare lobortis.",
      "description": "Cras sagittis. Praesent nec nisl a purus blandit viverra. Ut leo. Donec quam felis, ultricies nec, pellentesque eu, pretium quis, sem. Fusce a quam."
    },
    {
      "id": "bmfrurdkswtkfqsf15j",
      "name": "shorts",
      "stock": 5,
      "price": 109.99,
      "shortDesc": "Nulla facilisi. Curabitur at lacus ac velit ornare lobortis.",
      "description": "Cras sagittis. Praesent nec nisl a purus blandit viverra. Ut leo. Donec quam felis, ultricies nec, pellentesque eu, pretium quis, sem. Fusce a quam."
    }
  ]
} 
```

我们在这里创建两个资源— `users`和`products`。查看`users`资源，您会注意到每个用户都有一个 ID、一个电子邮件地址和一个密码。密码看起来是一堆字母和数字，因为它是用 [bcryptjs](https://github.com/dcodeIO/bcrypt.js) 加密的。重要的是，不要在应用程序的任何地方用纯文本*存储密码。*

也就是说，每个密码的纯文本版本只是“密码”，没有引号。

现在，通过从项目根目录发出以下命令来启动服务器:

```
./node_modules/.bin/json-server-auth ./backend/db.json --port 3001 
```

这将在`http://localhost:3001`启动 json-server。多亏了 json-server-auth 中间件，`users`资源还将为我们提供一个`/login`端点，我们可以用它来模拟登录应用程序。

让我们用 [https://hoppscotch.io](https://hoppscotch.io) 来试试吧。在新窗口中打开链接，然后将方法改为`POST`，将 URL 改为`http://localhost:3001/login`。接下来，确保*原始输入*开关设置为上的*，并输入以下内容作为*原始请求体*:*

```
{
  "email": "regular@example.com",
  "password": "password"
} 
```

点击*发送*，您应该会收到一个类似如下的响应(在页面的下方):

```
{
  "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InJlZ3VsYXJAZXhhbXBsZS5jb20iLCJpYXQiOjE2MDE1Mzk3NzEsImV4cCI6MTYwMTU0MzM3MSwic3ViIjoiMSJ9.RAFUYXxG2Z8W8zv5-4OHun8CmCKqi7IYqYAc4R7STBM"
} 
```

那是一个 JSON Web 令牌，一个小时内有效。在一个有适当后端的普通应用程序中，您可以将它保存在客户端，然后在您请求受保护的资源时将其发送到服务器。服务器将验证它收到的令牌，如果一切都通过检查，它将使用您请求的数据进行响应。

这一点值得重复。您需要验证对服务器上受保护资源的任何请求。这是因为在客户端运行的代码可能会被逆向工程和篡改。

这里有一个在 [Hoppscotch](https://hoppscotch.io/?method=POST&url=http://localhost:3001&path=/login&contentType=application/json&rawParams=%7B%0A%20%20%22email%22:%20%22regular@example.com%22,%0A%20%20%22password%22:%20%22password%22%0A%7D) 上完成请求的链接。你只需要按下*发送*。

如果你想了解更多关于在 Node.js 中使用 JSON Web 令牌的信息，请参考我们的教程。

## 在 React 应用程序中实现身份验证

对于这一部分，我们需要在我们的应用程序中使用 axios 和 jwt_decode 包。将导入添加到`App.js`文件的顶部:

```
import axios from 'axios';
import jwt_decode from 'jwt-decode'; 
```

如果你看一下这个类的顶部，你会看到我们已经在 state 中声明了一个用户。这最初设置为 null。

接下来，我们需要通过在组件挂载上设置用户来确保用户在应用程序启动时被加载，如下所示。将该方法添加到`App`组件，该组件将最后一个用户会话从本地存储加载到状态(如果存在的话):

```
componentDidMount() {
  let user = localStorage.getItem("user");
  user = user ? JSON.parse(user) : null;
  this.setState({ user });
} 
```

接下来，我们定义附加到上下文的`login`和`logout`方法:

```
login = async (email, password) => {
  const res = await axios.post(
    'http://localhost:3001/login',
    { email, password },
  ).catch((res) => {
    return { status: 401, message: 'Unauthorized' }
  })

  if(res.status === 200) {
    const { email } = jwt_decode(res.data.accessToken)
    const user = {
      email,
      token: res.data.accessToken,
      accessLevel: email === 'admin@example.com' ? 0 : 1
    }

    this.setState({ user });
    localStorage.setItem("user", JSON.stringify(user));
    return true;
  } else {
    return false;
  }
}

logout = e => {
  e.preventDefault();
  this.setState({ user: null });
  localStorage.removeItem("user");
}; 
```

`login`方法向我们的`/login`端点发出一个 Ajax 请求，将用户输入到登录表单中的任何内容传递给它(我们将在一分钟内完成)。如果来自端点的响应具有 200 状态代码，我们可以假设用户的凭证是正确的。然后，我们对服务器响应中发送的令牌进行解码，以获得用户的电子邮件，然后保存电子邮件、令牌和用户的访问级别。如果一切顺利，方法返回`true`，否则返回`false`。我们可以在我们的`Login`组件中使用这个值来决定显示什么。

注意，这里对访问级别的检查是非常肤浅的，对于一个登录的普通用户来说，让自己成为管理员并不困难。但是，假设在发送响应之前，对受保护资源的请求已经在服务器上进行了验证，那么用户除了看到一个额外的按钮之外，将无法做更多的事情。服务器验证将确保他们无法获取任何受保护的数据。

如果您想实现一个更健壮的解决方案，您可以在用户登录时或者在应用程序加载时发出第二个请求来获取当前用户的权限。不幸的是，这超出了本教程的范围。

`logout`方法从状态和本地存储中清除用户。

### 创建登录组件

接下来，我们可以处理`Login`组件。该组件利用了上下文数据。为了能够访问这些数据和方法，必须使用我们之前创建的`withContext`方法对其进行包装。

像这样改变:

```
import React, { Component } from "react";
import { Redirect } from "react-router-dom";
import withContext from "../withContext";

class Login extends Component {
  constructor(props) {
    super(props);
    this.state = {
      username: "",
      password: ""
    };
  }

  handleChange = e => this.setState({ [e.target.name]: e.target.value, error: "" });

  login = (e) => {
    e.preventDefault();

    const { username, password } = this.state;
    if (!username || !password) {
      return this.setState({ error: "Fill all fields!" });
    }
    this.props.context.login(username, password)
      .then((loggedIn) => {
        if (!loggedIn) {
          this.setState({ error: "Invalid Credentails" });
        }
      })
  };

  render() {
    return !this.props.context.user ? (
      <>  <div className="hero is-primary ">  <div className="hero-body container">  <h4 className="title">Login</h4>  </div>  </div>  <br />  <br />  <form onSubmit={this.login}>  <div className="columns is-mobile is-centered">  <div className="column is-one-third">  <div className="field">  <label className="label">Email: </label>  <input
                  className="input"
                  type="email"
                  name="username"
                  onChange={this.handleChange}
                />  </div>  <div className="field">  <label className="label">Password: </label>  <input
                  className="input"
                  type="password"
                  name="password"
                  onChange={this.handleChange}
                />  </div>  {this.state.error && (
                <div className="has-text-danger">{this.state.error}</div>
              )}  <div className="field is-clearfix">  <button
                  className="button is-primary is-outlined is-pulled-right"
                > Submit </button>  </div>  </div>  </div>  </form>  </>
    ) : (
      <Redirect to="/products" />
    );
  }
}

export default withContext(Login); 
```

该组件呈现一个带有两个输入的表单，以收集用户登录凭证。提交时，组件调用通过上下文传递的`login`方法。如果用户已经登录，该模块还确保重定向到产品页面。

如果你现在进入[http://localhost:3000/log in](http://localhost:3000/login)，你应该可以用上面提到的用户名/密码组合登录。

![The login page with an email and password field](img/2a1cef6c8c8ccd0d1eb892d79ba521d4.png)

## 创建产品视图

现在，我们需要从我们的后端获取一些产品，以显示在我们的应用程序。我们可以在`App`组件中的组件挂载上再次这样做，就像我们对登录用户所做的那样:

```
async componentDidMount() {
  let user = localStorage.getItem("user");
  const products = await axios.get('http://localhost:3001/products');
  user = user ? JSON.parse(user) : null;
  this.setState({ user,  products: products.data });
} 
```

在上面的代码片段中，我们已经将`componentDidMount`生命周期挂钩标记为[异步](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)，这意味着我们可以向我们的`/products`端点发出请求，然后等待数据返回，再将它置于状态。

接下来，我们可以创建产品页面，它也将作为应用程序登录页面。该页面将使用两个组件。第一个是`ProductList.js`，它将显示页面主体，另一个是列表中每个产品的`ProductItem.js`组件。

改变`Productlist`组件，如下所示:

```
import React from "react";
import ProductItem from "./ProductItem";
import withContext from "../withContext";

const ProductList = props => {
  const { products } = props.context;

  return (
    <>  <div className="hero is-primary">  <div className="hero-body container">  <h4 className="title">Our Products</h4>  </div>  </div>  <br />  <div className="container">  <div className="column columns is-multiline">  {products && products.length ? (
            products.map((product, index) => (
              <ProductItem
                product={product}
                key={index}
                addToCart={props.context.addToCart}
              />
            ))
          ) : (
            <div className="column">  <span className="title has-text-grey-light"> No products found! </span>  </div>
          )}  </div>  </div>  </>
  );
};

export default withContext(ProductList); 
```

因为列表依赖于数据的上下文，所以我们也用`withContext`函数包装它。这个组件使用`ProductItem`组件呈现产品，我们还没有创建这个组件。它还从上下文(我们还没有定义)传递一个`addToCart`方法给`ProductItem`。这消除了在`ProductItem`组件中直接处理上下文的需要。

现在让我们创建`ProductItem`组件:

```
cd src/components
touch ProductItem.js 
```

并增加以下内容:

```
import React from "react";

const ProductItem = props => {
  const { product } = props;
  return (
    <div className=" column is-half">  <div className="box">  <div className="media">  <div className="media-left">  <figure className="image is-64x64">  <img
                src="https://bulma.img/placeholders/128x128.png"
                alt={product.shortDesc}
              />  </figure>  </div>  <div className="media-content">  <b style={{ textTransform: "capitalize" }}>  {product.name}{" "}  <span className="tag is-primary">${product.price}</span>  </b>  <div>{product.shortDesc}</div>  {product.stock > 0 ? (
              {product.stock + " Available"}
            ) : (
              Out Of Stock
            )}  <div className="is-clearfix">  <button
                className="button is-small is-outlined is-primary   is-pulled-right"
                onClick={() =>
                  props.addToCart({
                    id: product.name,
                    product,
                    amount: 1
                  })
                }
              > Add to Cart </button>  </div>  </div>  </div>  </div>  </div>
  );
};

export default ProductItem; 
```

该元素在卡片上显示产品，并提供一个动作按钮将产品添加到用户的购物车中。

![Products](img/7e1ae2e83f8fe6470c8f4543e634cbb3.png)

### 添加产品

既然我们的商店中有东西要展示，让我们为管理员用户创建一个添加新产品的界面。首先，让我们定义添加产品的方法。我们将在`App`组件中这样做，如下所示:

```
addProduct = (product, callback) => {
  let products = this.state.products.slice();
  products.push(product);
  this.setState({ products }, () => callback && callback());
}; 
```

该方法接收`product`对象并将其附加到产品数组中，然后将其保存到 app 状态。它还接收一个回调函数，以便在成功添加产品时执行。

现在我们可以继续填写`AddProduct`组件:

```
import React, { Component } from "react";
import withContext from "../withContext";
import { Redirect } from "react-router-dom";
import axios from 'axios';

const initState = {
  name: "",
  price: "",
  stock: "",
  shortDesc: "",
  description: ""
};

class AddProduct extends Component {
  constructor(props) {
    super(props);
    this.state = initState;
  }

  save = async (e) => {
    e.preventDefault();
    const { name, price, stock, shortDesc, description } = this.state;

    if (name && price) {
      const id = Math.random().toString(36).substring(2) + Date.now().toString(36);

      await axios.post(
        'http://localhost:3001/products',
        { id, name, price, stock, shortDesc, description },
      )

      this.props.context.addProduct(
        {
          name,
          price,
          shortDesc,
          description,
          stock: stock || 0
        },
        () => this.setState(initState)
      );
      this.setState(
        { flash: { status: 'is-success', msg: 'Product created successfully' }}
      );

    } else {
      this.setState(
        { flash: { status: 'is-danger', msg: 'Please enter name and price' }}
      );
    }
  };

  handleChange = e => this.setState({ [e.target.name]: e.target.value, error: "" });

  render() {
    const { name, price, stock, shortDesc, description } = this.state;
    const { user } = this.props.context;

    return !(user && user.accessLevel < 1) ? (
      <Redirect to="/" />
    ) : (
      <>  <div className="hero is-primary ">  <div className="hero-body container">  <h4 className="title">Add Product</h4>  </div>  </div>  <br />  <br />  <form onSubmit={this.save}>  <div className="columns is-mobile is-centered">  <div className="column is-one-third">  <div className="field">  <label className="label">Product Name: </label>  <input
                  className="input"
                  type="text"
                  name="name"
                  value={name}
                  onChange={this.handleChange}
                  required
                />  </div>  <div className="field">  <label className="label">Price: </label>  <input
                  className="input"
                  type="number"
                  name="price"
                  value={price}
                  onChange={this.handleChange}
                  required
                />  </div>  <div className="field">  <label className="label">Available in Stock: </label>  <input
                  className="input"
                  type="number"
                  name="stock"
                  value={stock}
                  onChange={this.handleChange}
                />  </div>  <div className="field">  <label className="label">Short Description: </label>  <input
                  className="input"
                  type="text"
                  name="shortDesc"
                  value={shortDesc}
                  onChange={this.handleChange}
                />  </div>  <div className="field">  <label className="label">Description: </label>  <textarea
                  className="textarea"
                  type="text"
                  rows="2"
                  style={{ resize: "none" }}
                  name="description"
                  value={description}
                  onChange={this.handleChange}
                />  </div>  {this.state.flash && (
                <div className={`notification ${this.state.flash.status}`}>  {this.state.flash.msg}  </div>
              )}  <div className="field is-clearfix">  <button
                  className="button is-primary is-outlined is-pulled-right"
                  type="submit"
                  onClick={this.save}
                > Submit </button>  </div>  </div>  </div>  </form>  </>
    );
  }
}

export default withContext(AddProduct); 
```

这个组件做了很多事情。它检查上下文中是否存储了当前用户，以及该用户的`accessLevel`是否小于 1(即，他们是否是管理员)。如果是，它将呈现表单以添加新产品。如果没有，它会重定向到应用程序的主页面。

请再次注意，这种检查很容易在客户端被绕过。在现实世界的应用程序中，您需要在服务器上执行额外的检查，以确保用户被允许创建新产品。

假设表单被呈现，有几个字段供用户填写(其中`name`和`price`是必填的)。无论用户输入什么，都会在组件的状态中被跟踪。当表单被提交时，组件的`save`方法被调用，它向我们的后端发出一个 Ajax 请求来创建一个新产品。我们还创建了一个惟一的 ID(这是 json-server 所期望的)并传递它。代码来自堆栈溢出的[线程。](https://stackoverflow.com/a/44078785)

最后，我们调用通过上下文接收的`addProduct`方法，将新创建的产品添加到我们的全局状态并重置表单。假设所有这些都成功了，我们在 state 中设置了一个`flash`属性，它将更新界面，通知用户产品已经创建。

如果`name`或`price`字段丢失，我们设置`flash`属性来通知用户。

![Add product page](img/99c3780886e7e336ebb3f112dbfad403.png)

花一点时间检查你的进度。以管理员身份登录(电子邮件:`admin@example.com`，密码:`password`)，确保在导航中看到一个*添加产品*按钮。导航到该页面，然后使用该表单创建几个新产品。最后，返回主页，确保新产品出现在产品列表中。

## 添加购物车管理

现在我们可以添加和显示产品了，最后要做的是实现我们的购物车管理。我们已经在`App.js`中将购物车初始化为一个空对象，但是我们还需要确保在组件加载时从本地存储中加载现有的购物车。

更新`App.js`中的`componentDidMount`方法如下:

```
async componentDidMount() {
  let user = localStorage.getItem("user");
  let cart = localStorage.getItem("cart");

  const products = await axios.get('http://localhost:3001/products');
  user = user ? JSON.parse(user) : null;
  cart = cart? JSON.parse(cart) : {};

  this.setState({ user,  products: products.data, cart });
} 
```

接下来，我们需要定义购物车功能(也在`App.js`中)。首先，我们将创建`addToCart`方法:

```
addToCart = cartItem => {
  let cart = this.state.cart;
  if (cart[cartItem.id]) {
    cart[cartItem.id].amount += cartItem.amount;
  } else {
    cart[cartItem.id] = cartItem;
  }
  if (cart[cartItem.id].amount > cart[cartItem.id].product.stock) {
    cart[cartItem.id].amount = cart[cartItem.id].product.stock;
  }
  localStorage.setItem("cart", JSON.stringify(cart));
  this.setState({ cart });
}; 
```

该方法使用商品 ID 作为购物车对象的键来追加商品。我们对购物车使用了一个对象而不是一个数组来实现简单的数据检索。该方法检查 cart 对象，查看是否存在具有该键的商品。如果有，就增加金额；否则，它会创建一个新条目。第二个`if`语句确保用户不能添加多于实际可用的项目。然后，该方法将购物车保存到状态，该状态通过上下文传递给应用程序的其他部分。最后，该方法将更新后的购物车保存到本地存储以便持久化。

接下来，我们将定义`removeFromCart`方法从用户购物车中删除特定产品，定义`clearCart`方法从用户购物车中删除所有产品:

```
removeFromCart = cartItemId => {
  let cart = this.state.cart;
  delete cart[cartItemId];
  localStorage.setItem("cart", JSON.stringify(cart));
  this.setState({ cart });
};

clearCart = () => {
  let cart = {};
  localStorage.removeItem("cart");
  this.setState({ cart });
}; 
```

`removeCart`方法使用提供的产品密钥删除产品。然后，它会相应地更新应用状态和本地存储。`clearCart`方法将 cart 重置为空对象状态，并删除本地存储上的 cart 条目。

![The cart](img/88785d31710caf6160731d8fe96275d9.png)

现在，我们可以继续制作购物车用户界面。与产品列表类似，我们使用两个元素来实现这一点:第一个元素`Cart.js`，它呈现页面布局，第二个元素`CartItem.js`，它显示购物车商品列表:

```
// ./src/components/Cart.js

import React from "react";
import withContext from "../withContext";
import CartItem from "./CartItem";

const Cart = props => {
  const { cart } = props.context;
  const cartKeys = Object.keys(cart || {});
  return (
    <>  <div className="hero is-primary">  <div className="hero-body container">  <h4 className="title">My Cart</h4>  </div>  </div>  <br />  <div className="container">  {cartKeys.length ? (
          <div className="column columns is-multiline">  {cartKeys.map(key => (
              <CartItem
                cartKey={key}
                key={key}
                cartItem={cart[key]}
                removeFromCart={props.context.removeFromCart}
              />
            ))}  <div className="column is-12 is-clearfix">  <br />  <div className="is-pulled-right">  <button
                  onClick={props.context.clearCart}
                  className="button is-warning "
                > Clear cart </button>{" "}  <button
                  className="button is-success"
                  onClick={props.context.checkout}
                > Checkout </button>  </div>  </div>  </div>
        ) : (
          <div className="column">  <div className="title has-text-grey-light">No item in cart!</div>  </div>
        )}  </div>  </>
  );
};

export default withContext(Cart); 
```

`Cart`组件还将一个方法从上下文传递给`CartItem`。`Cart`组件遍历上下文 cart 对象值的数组，并为每个值返回一个`CartItem`。它还提供了一个清除用户购物车的按钮。

接下来是`CartItem`组件，它与`ProductItem`组件非常相似，但有一些细微的变化:

让我们首先创建组件:

```
cd src/components
touch CartItem.js 
```

然后添加以下内容:

```
import React from "react";

const CartItem = props => {
  const { cartItem, cartKey } = props;

  const { product, amount } = cartItem;
  return (
    <div className=" column is-half">  <div className="box">  <div className="media">  <div className="media-left">  <figure className="image is-64x64">  <img
                src="https://bulma.img/placeholders/128x128.png"
                alt={product.shortDesc}
              />  </figure>  </div>  <div className="media-content">  <b style={{ textTransform: "capitalize" }}>  {product.name}{" "}  <span className="tag is-primary">${product.price}</span>  </b>  <div>{product.shortDesc}</div>  {`${amount} in cart`}  </div>  <div
            className="media-right"
            onClick={() => props.removeFromCart(cartKey)}
          >  <span className="delete is-large"></span>  </div>  </div>  </div>  </div>
  );
};

export default CartItem; 
```

该组件显示产品信息和所选项目的数量。它还提供了一个从购物车中移除产品的按钮。

最后，我们需要在`App`组件中添加 checkout 方法:

```
checkout = () => {
  if (!this.state.user) {
    this.routerRef.current.history.push("/login");
    return;
  }

  const cart = this.state.cart;

  const products = this.state.products.map(p => {
    if (cart[p.name]) {
      p.stock = p.stock - cart[p.name].amount;

      axios.put(
        `http://localhost:3001/products/${p.id}`,
        { ...p },
      )
    }
    return p;
  });

  this.setState({ products });
  this.clearCart();
}; 
```

该方法在继续之前检查用户是否登录。如果用户没有登录，它会使用我们之前附加到`Router`组件的路由器引用将用户重定向到登录页面。

通常，在一个常规的电子商务网站中，这是计费过程发生的地方，但是对于我们的应用程序，我们只是假设用户已经付款，因此从可用项目列表中删除他们购买的项目。我们还将使用 axios 来更新后端的库存水平。

这样，我们就成功地完成了基本的购物车。

![Products](img/18c8a4f301bf7c2face4ea19006968ac.png)

## 结论

在本教程中，我们已经使用 React 搭建了一个基本购物车的界面。我们使用上下文在多个组件之间移动数据和方法，并使用 json-server 持久化数据。我们还使用 json-server auth 实现了一个基本的身份验证流程。

这个应用程序绝不是一个成品，可以在许多方面进行改进。例如，下一步是添加一个适当的数据库后端，并在服务器上执行身份验证检查。您还可以赋予管理员用户编辑和删除产品的能力。

我希望你喜欢这个教程。请不要忘记这个应用程序的代码可以在 [GitHub](https://github.com/sitepoint-editors/React-Ecommerce) 上获得。

想深入了解更多反应吗？查看 SitePoint Premium 上的 [React 设计模式和最佳实践](https://www.sitepoint.com/premium/books/react-design-patterns-and-best-practices-second-edition/)和[大量其他 React 资源](https://www.sitepoint.com/premium/library/)。

## 分享这篇文章