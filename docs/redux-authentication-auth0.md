# Redux 验证:使用 Auth0 保护您的应用程序

> 原文：<https://www.sitepoint.com/redux-authentication-auth0/>

*这篇文章由 [Peleke Sengstacke](https://github.com/Peleke) 同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

Redux 现在在 React 社区内外风靡一时，而且理由充分。这是一个由 Dan Abramov 创建的库，它为处理单向数据流带来了理智，并允许开发人员使用强大的开发功能，如时间旅行和记录/回放。

听起来很棒，对吧？困难在于:这是以需要编写更多代码为代价的。但是，如果您有过维护大型应用程序的经验，您可能会知道数据处理会变得笨拙和难以管理。有了 Redux，我们可以随时清楚地看到应用程序的状态，并确切地知道我们的数据在做什么。

在本教程中，我们将了解如何开始创建一个现实生活中的 React + Redux 应用程序，该应用程序验证用户并调用远程 API 获取数据。我们的应用程序将从节点后端检索星球大战绝地的列表，这样我们就可以显示他们的姓名和照片。对于身份验证，我们将使用 [Auth0](https://auth0.com/signup/?utm_source=sitepoint&utm_medium=gp&utm_campaign=react_redux) ，这样我们可以快速启动并运行，还可以轻松获得像[社交登录](https://auth0.com/learn/social-login/?utm_source=sitepoint&utm_medium=gp&utm_campaign=react_redux)和[多因素身份验证](https://auth0.com/learn/multifactor-authentication/?utm_source=sitepoint&utm_medium=gp&utm_campaign=react_redux)这样的功能。

![single jedi](img/c5bfdf850dd1bad5eb171df8c07f12e9.png)

我们不会深入研究 Redux 的基本概念，所以如果您是这个库的新手，可以查看一些很好的入门资源:

*   [如何使用 React、Redux 和 Immutable.js 构建 Todo 应用](https://www.sitepoint.com/how-to-build-a-todo-app-using-react-redux-and-immutable-js/)
*   [还原文档](http://redux.js.org/)

我们将要开发的应用程序的源代码可以在这里下载。

## Redux 身份验证:入门

本教程的 React 项目将在 ES2015 中编写，因此我们将使用 Babel 向下转换到 ES5，并使用 webpack 处理模块绑定。与其从头开始设置，我们为什么不从丹在 [Redux repo](https://github.com/reactjs/redux) 中的[真实世界启动器示例](https://github.com/reactjs/redux/tree/master/examples/real-world)开始。获取该文件的副本并安装依赖项。

```
npm install 
```

## 注册 Auth0

对单页应用程序(比如我们正在构建的这个)进行身份验证的最佳方式是使用 [JSON Web 令牌](https://jwt.io/introduction) (JWT)。jwt 提供了一种针对 RESTful API 进行无状态认证的方法，这为[提供了许多优于会话和基于 cookie 的认证的优势](https://auth0.com/blog/2016/05/31/cookies-vs-tokens-definitive-guide/?utm_source=sitepoint&utm_medium=gp&utm_campaign=react_redux)。缺点是，推出我们自己的 JWT 认证解决方案可能会很棘手且容易出错，但幸运的是，我们可以使用 Auth0，而不用担心任何服务器或安全实现细节。

如果你还没有这样做，请前往[注册一个免费的 Auth0 帐户](https://auth0.com/signup/?utm_source=sitepoint&utm_medium=gp&utm_campaign=react_redux)。通过免费计划，我们获得了 7，000 名定期活跃用户，并可以使用两个社交身份提供商。

注册后，按照提示初始化您的帐户。请记住，您可以在同一个帐户下使用多个应用程序，因此请选择一个适合您的情况的域名，也许是您所在组织的名称。作为第一步，我们需要将我们的`localhost` URL 设置为允许的来源。这可以在“允许的来源(CORS)”文本区完成。

![auth0 dashboard](img/ab47684b67397550509c0f99ba9238ba.png)

## 设置 Web 服务器

不如我们先把绝地网络服务器解决掉。这只需要一个简单的 RESTful API，以 JSON 数据的形式返回我们的 Jedis，快速的方法是使用 Express 框架使用 NodeJS。只要返回 JSON 数据，您可以使用任何您喜欢的服务器端语言或框架。

> 注意:星球大战纯粹主义者会注意到我们在整个应用程序中使用“Jedis”作为绝地的复数形式，但这不是正确的复数形式。相反，我们应该简单地使用“绝地”。也许是这样，但我没意见，因为这让我们的应用程序变得更简单:)

首先，初始化应用程序并安装依赖项:

```
mkdir server && cd server
touch server.js
npm init
npm install express express-jwt cors 
```

我们可以在一个 JavaScript 文件中提供服务器需要的所有代码。

```
// server.js

const express = require('express');
const app = express();
const jwt = require('express-jwt');
const cors = require('cors');

app.use(cors());
app.use(express.static('public'));

// Authentication middleware provided by express-jwt.
// This middleware will check incoming requests for a valid
// JWT on any routes that it is applied to.
const authCheck = jwt({
  secret: 'AUTH0_SECRET',
  // If your Auth0 client was created before Dec 6, 2016,
  // uncomment the line below and remove the line above
  // secret: new Buffer('AUTH0_SECRET', 'base64'),
  audience: 'AUTH0_CLIENT_ID'
});

var jedis = [
  {
    id: 1,
    name: 'Luke Skywalker',
    image: 'http://localhost:70img/luke-skywalker.jpg'
  },
  {
    id: 2,
    name: 'Anakin Skywalker',
    image: 'http://localhost:70img/anakin-skywalker.png'
  },
  {
    id: 3,
    name: 'Yoda',
    image: 'http://localhost:70img/yoda.png'
  },
  {
    id: 4,
    name: 'Obi-Wan Kenobi',
    image: 'http://localhost:70img/obi-wan-kenobi.jpg'
  },
  {
    id: 5,
    name: 'Mace Windu',
    image: 'http://localhost:70img/mace-windu.jpg'
  }
];

app.get('/api/jedis', (req, res) => {
  const allJedis = jedis.map(jedi => { 
    return { id: jedi.id, name: jedi.name }
  });
  res.json(allJedis);
});

app.get('/api/jedis/:id', authCheck, (req, res) => {
  res.json(jedis.filter(jedi => jedi.id === parseInt(req.params.id))[0]);
});

app.listen(7000);
console.log('Listening on http://localhost:7000'); 
```

我们有一组 Jedis 和两个端点来处理它们。第一个端点返回所有的 Jedis，但只返回它们的`id`和`name`属性。在`/jedis/:id`的第二个端点返回一个绝地武士，但也包括图像 URL。第二个端点是我们将使用身份验证中间件保护的端点，并且仅限于经过身份验证的用户。

但是我们实际上如何保护这个端点呢？我们使用 **express-jwt** 来创建一个中间件，该中间件寻找一个传入的 JSON Web 令牌，并根据我们提供的密钥对其进行验证。然后，我们可以将这个中间件应用于我们的任何端点——我们正在对第二个参数应用于端点`/jedis/:id`——只有那些包含有效令牌的请求才会被允许通过。

中间件本身是通过向`authCheck`提供我们的 Auth0 密钥和客户端 ID 来设置的，在这里您可以提供特定于您的应用程序的密钥。这些密钥可以在*应用*下的[授权 0 管理仪表板](https://manage.auth0.com)中找到。

绝地图像来自服务器上的`public`目录。你可以从[回购](https://github.com/sitepoint-editors/redux-auth0)中抓取相同的图片，或者你也可以在数据中包含其他来源的图片链接。

服务器就绪后，让我们验证 API 是否按预期工作。我们可以用类似于 [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop?hl=en) 这样的工具来做这件事。

![postman](img/7a861dffc20ef38596754d44dd10d49c.png)

如果我们走`/api/jedis`路线，我们就能得到预期的 Jedis 的完整列表。然而，如果我们试图得到一个绝地，我们不被允许拿回资源，因为我们没有向服务器发送令牌。

![postman invalid token](img/c67791bcf4c3eb1b2ec0c9379b7c3dc6.png)

一旦我们在应用程序本身中实现了 API 调用，我们将会看到如何在我们的请求中发送令牌，但是本质上我们需要做的就是使用`Bearer`方案将它包含在`Authorization`头中。

## 设置 Redux

### Auth0Lock

Auth0 帐户带有一个名为 [Lock](https://auth0.com/lock/?utm_source=sitepoint&utm_medium=gp&utm_campaign=react_redux) 的令人敬畏的预建小部件，它极大地简化了实际登录应用程序的过程。我们可以从 Auth0 的 CDN 获得这个小部件所需的 JavaScript。

```
<!-- index.html -->

<!-- Auth0Lock script -->
<script src="//cdn.auth0.com/js/lock-9.min.js"></script>

<!-- Setting the right viewport -->
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" /> 
```

### 按指定路线发送

为了简洁起见，我们将把我们的应用程序限制在一条路线上。当涉及到路由时，有一些身份验证问题，比如将某些路由限制到那些经过身份验证的用户，但是这是以后文章的主题。现在，让我们简单地定义一条路线。

```
// routes.js

import React from 'react'
import { Route } from 'react-router'
import App from './containers/App'

export default (
  <Route path="/" component={App}></Route>
) 
```

这给了我们一个使用组件`App`的基本路由。

### 设置行动

Redux 的核心是 Redux，它们为我们提供了一种干净且可预测的方式来改变应用程序的状态。当使用 Reducers 时，我们需要确保没有数据发生变异。这让我们能够检查应用程序中数据的每个先前状态，这是 Redux 中的一个重要概念。

虽然减速器是关键，但我们仍然需要[动作](http://redux.js.org/docs/basics/Actions.html)来使应用程序中的事情发生。让我们输入 Jedis 应用程序所需的所有动作。

```
// actions/index.js

import { CALL_API } from '../middleware/api'

export const LOGIN_SUCCESS = 'LOGIN_SUCCESS'
export const LOGIN_ERROR = 'LOGIN_ERROR'

function loginSuccess(profile) {
  return {
    type: LOGIN_SUCCESS,
    profile
  }
}

function loginError(err) {
  return {
    type: LOGIN_ERROR,
    err
  }
}

export function login() {
  const lock = new Auth0Lock('AUTH0_CLIENT_ID', 'AUTH0_DOMAIN')
  return dispatch => {
    lock.show((err, profile, token) => {
      if(err) {
        return dispatch(loginError(err))
      }
      localStorage.setItem('profile', JSON.stringify(profile))
      localStorage.setItem('id_token', token)
      return dispatch(loginSuccess(profile))
    })
  }
}

export const LOGOUT_SUCCESS = 'LOGOUT_SUCCESS'

function logoutSuccess(profile) {
  return {
    type: LOGOUT_SUCCESS
  }
}

export function logout() {
  return dispatch => {
    localStorage.removeItem('id_token');
    localStorage.removeItem('profile');
    return dispatch(logoutSuccess());
  }
}

export const JEDIS_REQUEST = 'JEDIS_REQUEST'
export const JEDIS_SUCCESS = 'JEDIS_SUCCESS'
export const JEDIS_FAILURE = 'JEDIS_FAILURE'

function fetchJedis() {
  return {
    [CALL_API]: {
      types: [ JEDIS_REQUEST, JEDIS_SUCCESS, JEDIS_FAILURE ],
      endpoint: 'jedis',
      authenticatedRequest: false
    }
  }
}

export function loadJedis() {
  return dispatch => {
    return dispatch(fetchJedis())
  }
}

export const JEDI_REQUEST = 'JEDI_REQUEST'
export const JEDI_SUCCESS = 'JEDI_SUCCESS'
export const JEDI_FAILURE = 'JEDI_FAILURE'

function fetchJedi(id) {
  return {
    [CALL_API]: {
      types: [ JEDI_REQUEST, JEDI_SUCCESS, JEDI_FAILURE ],
      endpoint: `jedis/${id}`,
      authenticatedRequest: true
    }
  }
}

export function loadJedi(id) {
  return dispatch => {
    return dispatch(fetchJedi(id))
  }
} 
```

我们正在导出的`const`值是我们希望能够在组件中监听的“动作类型”,我们将在后面看到。当我们处理对 API 的请求时，我们通常希望有三种动作类型:一种表示我们已经发出请求，一种处理成功情况，最后一种处理错误情况。这正是我们在 API 调用中所做的，我们正在设置一些依赖于 Redux 所允许的一个伟大特性的功能:中间件。

有了 Redux 中的[中间件](http://redux.js.org/docs/advanced/Middleware.html)，我们有了很多可能性，包括像[登录](https://www.npmjs.com/package/redux-logger)这样的事情。我们还可以使用中间件来处理我们实际的 API 调用，并且我们在这里的操作中依赖于它来向我们的 API 发出请求。我们很快就会看到完整的 API 中间件代码。

Redux 动作依赖于我们使用`dispatch`来实际启动它们，这就是我们在所有导出的动作函数中所做的。其思想是，我们可以基于 React 组件中的某个事件来调度一个动作，与该动作相关联的动作类型将触发 reducer 做出相应的响应。这个响应告诉我们的应用程序，它的一部分必须更新。

值得注意的是，我们为 Lock 设置的动作只有两种类型:一种表示成功，另一种表示失败。为什么不为锁何时打开以及何时发出请求创建一个动作类型呢？归根结底，我们是否真的需要了解这些行为。知道锁定小部件已经被打开，我们会得到什么？也许在某个地方有它的用例，但是对于我们的应用程序，我们真的只需要知道用户是否已经被成功认证。

`login`函数是真正的 Auth0 认证魔法发生的地方。Lock 小部件给了我们一个`show`方法，它实际上使小部件出现。一旦我们输入凭证并点击 submit，回调就会处理接下来的事情。如果我们因为任何原因遇到错误，我们用消息`dispatch`错误类型。

但是，如果一切正常，我们将从响应中返回的 JWT 和用户配置文件对象保存在本地存储中，以便以后在应用程序中调用和使用它们。如果发生这种情况，我们还使用成功类型，并传递概要文件，以便在必要时可以在 reducer 中使用。

现在我们导出了一些动作函数，我们可以在组件中使用它们。然而，在我们这样做之前，我们需要创建我们在`CALL_API`中使用的 API 中间件。

## 创建 API 中间件

如前所述，Redux 中间件在我们的应用程序中开辟了许多可能性。它们也是处理 API 调用的完美机制。我们可以创建一个单一的通用 API 中间件，然后在我们的任何操作中使用它来调用我们的后端。我们将使用 Fetch 调用 API，并提供发送经过身份验证和未经身份验证的请求的选项。

```
// middleware/api.js

export const API_ROOT = 'http://localhost:7000/api/'

function callApi(endpoint, authenticatedRequest) {

  let token = localStorage.getItem('id_token') || null
  let config = {}

  if(authenticatedRequest) {
    if(token) {
      config = {
        headers: { 'Authorization': `Bearer ${token}` }
      }
    } else {
      throw new Error("No token saved!")
    }
  }

  return fetch(API_ROOT + endpoint, config)
    .then(response =>
      response.json()
      .then(resource => ({ resource, response }))
    ).then(({ resource, response }) => {
      if (!response.ok) {
        return Promise.reject(resource)
      }      
      return resource
    })
}

export const CALL_API = Symbol('Call API')

export default store => next => action => {

  const callAPI = action[CALL_API]

  if (typeof callAPI === 'undefined') {
    return next(action)
  }

  let { endpoint, types, authenticatedRequest } = callAPI

  if (typeof endpoint !== 'string') {
    throw new Error('Specify a string endpoint URL.')
  }

  if (!Array.isArray(types) || types.length !== 3) {
    throw new Error('Expected an array of three action types.')
  }

  if (!types.every(type => typeof type === 'string')) {
    throw new Error('Expected action types to be strings.')
  }

  function actionWith(data) {
    const finalAction = Object.assign({}, action, data)
    delete finalAction[CALL_API]
    return finalAction
  }

  const [ requestType, successType, failureType ] = types
  next(actionWith({ type: requestType }))

  return callApi(endpoint, authenticatedRequest).then(
    response => next(actionWith({
      response,
      authenticatedRequest,
      type: successType
    })),
    error => next(actionWith({
      type: failureType,
      error: error.message || 'Error!'
    }))
  )
} 
```

从顶部开始，我们定义我们的基本 API 端点，然后创建一个函数来进行 API 调用。该函数接受请求应该到达的端点的参数，以及请求是否应该被验证的布尔值。如果它应该是一个安全请求，我们设置一个`Authorization`头，包含来自本地存储的用户 JWT。

正如我们在动作中看到的，当我们使用`CALL_API`符号触发中间件时，我们向`types`键提供了所有三种动作类型的数组。这些动作类型在动作中被挑选出来，这样它们就可以传递给我们的 reducer。正如我们所料，`successType`用于成功情况下的`callApi`函数的`then`方法，而`errorType`用于错误情况下。

## 实施减速器

我们已经到了可以在 React 组件中使用 Redux 的最后阶段了！我们需要实现一个 Redux reducer 来真正响应我们的动作，并以不可变的方式返回数据。我们需要检查用户 JWT 的到期时间，我们可以通过 Auth0 中的 **[jwt-decode](https://www.npmjs.com/package/jwt-decode)** 库轻松完成这项工作，现在让我们安装它。

```
npm install jwt-decode 
```

准备就绪后，让我们创建减速器。

```
// reducers/index.js

import * as ActionTypes from '../actions'
import { routerReducer as routing } from 'react-router-redux'
import { combineReducers } from 'redux'
const jwtDecode = require('jwt-decode')

function checkTokenExpiry() {
  let jwt = localStorage.getItem('id_token')
  if(jwt) {
    let jwtExp = jwtDecode(jwt).exp;
    let expiryDate = new Date(0);
    expiryDate.setUTCSeconds(jwtExp);

    if(new Date() < expiryDate) {
      return true;
    }
  }
  return false;  
}

function getProfile() {
  return JSON.parse(localStorage.getItem('profile'));
}

function auth(state = {
    isAuthenticated: checkTokenExpiry(),
    profile: getProfile(),
    error: ''
  }, action) {
  switch (action.type) {
    case ActionTypes.LOGIN_SUCCESS:
      return Object.assign({}, state, {
        isAuthenticated: true,
        profile: action.profile,
        error: ''
      })
    case ActionTypes.LOGIN_ERROR:
      return Object.assign({}, state, {
        isAuthenticated: false,
        profile: null,
        error: action.error
      })
    case ActionTypes.LOGOUT_SUCCESS:
      return Object.assign({}, state, {
        isAuthenticated: false,
        profile: null
      })
    default:
      return state
    }
}

function jedis(state = {
  isFetching: false,
  allJedis: [],
  error: ''
}, action) {
  switch (action.type) {
    case ActionTypes.JEDIS_REQUEST:
      return Object.assign({}, state, {
        isFetching: true
      })
    case ActionTypes.JEDIS_SUCCESS:
      return Object.assign({}, state, {
        isFetching: false,
        allJedis: action.response,
        error: ''
      })
    case ActionTypes.JEDIS_FAILURE:
      return Object.assign({}, state, {
        isFetching: false,
        allJedis: [],
        error: action.error
      })
    default:
      return state
  }
}

function jedi(state = {
  isFetching: false,
  singleJedi: {},
  error: ''
}, action) {
  switch (action.type) {
    case ActionTypes.JEDI_REQUEST:
      return Object.assign({}, state, {
        isFetching: true
      })
    case ActionTypes.JEDI_SUCCESS:
      return Object.assign({}, state, {
        isFetching: false,
        singleJedi: action.response,
        error: ''
      })
    case ActionTypes.JEDI_FAILURE:
      return Object.assign({}, state, {
        isFetching: false,
        singleJedi: {},
        error: action.error
      })
    default:
      return state
  }
}

const rootReducer = combineReducers({
  routing,
  auth,
  jedis,
  jedi
})

export default rootReducer 
```

reducer 本质上定义了一个初始状态，然后基于一个动作返回一个新状态。新返回的状态**必须**是一个新的对象，而不仅仅是原始状态的变异版本。这就是为什么我们使用`Object.assign`并传入一个空对象作为第一个参数；它向我们保证，新的国家将是独一无二的。

`auth`减速器通过检查用户的 JWT 是否过期来启动。这个`isAuthenticated`布尔值将在整个应用程序中使用，有条件地隐藏和显示依赖于用户身份验证状态的各种元素。

当用户成功登录时，他们的`isAuthenticated`状态被设置为 true，他们的配置文件也被设置为来自 Auth0 的配置文件对象。这个概要文件对象具有类似于`picture`、`nickname`的属性，以及其他一些对显示概要文件区域有用的属性。

我们绝地请求的缩减器有一个`isFetching`布尔值，以及来自动作的数据。

最后，我们使用`combineReducers`将所有这些单独的减速器包装成一个`rootReducer`。现在让我们继续看实际的应用程序代码！

## 创建应用程序组件

我们的第一个组件是名为`App`的根容器组件。

```
// containers/App.js

import React, { Component, PropTypes } from 'react'
import { connect } from 'react-redux'
import { loadJedis, loadJedi, login, logout } from '../actions'
import JedisList from '../components/JedisList'
import Jedi from '../components/Jedi'
import Auth from '../components/Auth'

class App extends Component {
  constructor(props) {
    super(props)
    this.handleGetJedisClick = this.handleGetJedisClick.bind(this)
    this.handleGetJediClick = this.handleGetJediClick.bind(this)
    this.handleLoginClick = this.handleLoginClick.bind(this)
    this.handleLogoutClick = this.handleLogoutClick.bind(this)
  }

  handleGetJedisClick() {
    this.props.loadJedis()
  }

  handleGetJediClick(id) {
    this.props.loadJedi(id)
  }

  handleLoginClick() {
    this.props.login()
  }

  handleLogoutClick() {
    this.props.logout()
  }

  render() {
    const { allJedis, singleJedi, error, isAuthenticated, profile } = this.props
    return (
      <div>  <div className="navbar navbar-default">  <div className="container-fluid">  <a className="navbar-brand">Redux Jedi</a>  <Auth 
              isAuthenticated={isAuthenticated}
              profile={profile}
              onLoginClick={this.handleLoginClick}
              onLogoutClick={this.handleLogoutClick}
            />  </div>  </div>  <div className="container-fluid">  <JedisList
            jedis={allJedis}
            error={error}
            onClick={this.handleGetJedisClick}
            onGetJediClick={this.handleGetJediClick}
            isAuthenticated={isAuthenticated}
          />  <Jedi jedi={singleJedi} />  </div>  </div>
    )
  }
}

function mapStateToProps(state) {
  const { jedis, jedi, auth } = state
  const { allJedis, error } = jedis
  const { singleJedi } = jedi
  const { isAuthenticated, profile } = auth
  return {
    allJedis,
    singleJedi,
    error,
    isAuthenticated,
    profile
  }
}

export default connect(mapStateToProps, {
  loadJedis,
  loadJedi,
  login,
  logout
})(App) 
```

这里发生的大多数事情都是非常标准的 React:我们引入了几个将在下一节创建的其他组件，并且我们将道具传递给它们。我们传递的道具包括我们的认证和 Jedis 数据，以及一些用于响应点击事件的处理函数。

使用 Redux 时有一点不同，那就是我们需要向`connect`函数提供状态图和动作对象。这个函数本质上将我们的 React 组件连接到在`store`目录中提供的 Redux 存储。当我们提供`mapStateToProps`函数时，组件将在我们的动作被调度后更新数据。

现在让我们创建我们需要的其他组件。

## 创建身份验证组件

在`Auth`组件中，我们将显示“登录”和“注销”按钮，以及用户的个人资料照片和昵称。

```
// components/Auth.js

import React, { Component, PropTypes } from 'react'

export default class Auth extends Component {
  constructor(props) {
    super(props)
  }

  render() {
    const { onLoginClick, onLogoutClick, isAuthenticated, profile } = this.props
    return (
      <div style={{ marginTop: '10px' }}>  { !isAuthenticated ? (
          <ul className="list-inline">  <li><button className="btn btn-primary" onClick={onLoginClick}>Login</button></li>  </ul>
        ) : (
          <ul className="list-inline">  <li><img src={profile.picture} height="40px" /></li>  <li><span>Welcome, {profile.nickname}</span></li>  <li><button className="btn btn-primary" onClick={onLogoutClick}>Logout</button></li>  </ul>
        )}  </div>
    )
  }
} 
```

现在，当用户点击“登录”按钮时，Auth0 的锁定小部件将会弹出。

![lock widget](img/24790477741cf5114881ce78b1ce28c9.png)

成功登录后，用户的个人资料图像和昵称会显示在导航栏中，同时还会显示“注销”按钮。

![profile auth0](img/94bf5b2cbccff9ce47fae47f934260f8.png)

现在我们可以登录应用程序了，让我们把`Jedi`和`JedisList`组件安装到位。

## 创建绝地和杰迪斯特组件

我们想要一个侧栏列出我们所有的 Jedis。

```
// components/JediList.js

import React, { Component, PropTypes } from 'react'

function getJediListItem(jedi, isAuthenticated, onClick) {
  return(
    <li key={jedi.id} className="list-group-item">  { isAuthenticated ? (
        <a onClick={() => onClick(jedi.id)}>  <h4 style={{ cursor: 'pointer' }}>{jedi.name}</h4>  </a>
      ) : (
        <h4>{jedi.name}</h4>
      )}  </li>
  )
}
export default class JedisList extends Component {

  constructor(props) {
    super(props)
  }

  render() {
    const { jedis, error, onClick, onGetJediClick, isAuthenticated } = this.props
    const jedisList = jedis.map(jedi => getJediListItem(jedi, isAuthenticated, onGetJediClick))
    return (
      <div className="col-sm-3">  <button className="btn btn-primary" onClick={onClick} style={{ marginBottom: '10px' }}>Get Jedis</button>  { jedis &&
          <ul className="list-group">  {jedisList}  </ul>
        }  { error &&
          <span className="text-danger">{error}</span>
        }  </div>
    )
  }
} 
```

当我们点击“Get Jedis”按钮时，我们的`loadJedis`函数被调用，该函数分派一个动作向服务器发出获取请求。

![jedi list](img/5e788309991b3d41eacc46e0e487c46f.png)

我们为 Jedi 列表构建的 HTML 包括一个点击处理程序，它使用`onGetJediClick`来获取一个 Jedi。因为我们用认证中间件保护返回单个 Jedis 的端点，所以如果用户没有通过认证，我们不想让它们可点击，所以我们检查`isAuthenticated`值以有条件地返回一个不可点击的列表。

现在我们只需要`Jedi`组件。

```
// components/Jedi.js

import React, { Component, PropTypes } from 'react'

export default class Jedi extends Component {

  render() {
    const { jedi } = this.props
    return (
      <div className="col-sm-9">  { jedi &&
          <div>  <h2>{jedi.name}</h2>  <img src={jedi.image} />  </div>
        }  </div>
    )
  }
} 
```

这个组件简单地显示了每个绝地的名字和照片。

![single jedi](img/c5bfdf850dd1bad5eb171df8c07f12e9.png)

## 包扎

使用 Redux 无疑意味着我们需要编写更多的代码来完成工作，但这可能是让我们的数据流可预测的合理代价，这是我们的应用程序开始增长的关键。正如整篇文章中提到的，使用 Redux 也给我们带来了好处，可以利用一些很酷的功能，比如[时间旅行](https://www.youtube.com/watch?v=xsSnOQynTHs)和录制/回放。

对于任何单页面应用程序来说，身份验证都是棘手的，但是 Auth0 使快速启动和运行它变得很容易。使用 Auth0 也意味着我们可以立即实现像[社交登录](https://auth0.com/learn/social-login/?utm_source=sitepoint&utm_medium=gp&utm_campaign=react_redux)和[多因素认证](https://auth0.com/learn/multifactor-authentication/?utm_source=sitepoint&utm_medium=gp&utm_campaign=react_redux)这样的功能

## 分享这篇文章