# 使用 Torii 向 Ember.js 应用程序添加 Twitter 身份验证

> 原文：<https://www.sitepoint.com/twitter-authentication-ember-js-torii/>

![Ember.js logo](img/fd463cac34d0eee0895585f6ca1166a0.png)

[Torii](http://vestorly.github.io/torii/) 是 Ember.js 的轻量级认证库，支持各种 OAuth 提供者(如 Twitter、Google 或 FaceBook)，可以用来实现基于弹出的 OAuth 重定向流程。它使用会话管理器(用于维护当前用户)和适配器(用于保持身份验证状态)。

在本文中，我将演示如何使用 Twitter 实现登录，以及如何使用 Torii 处理用户会话。Twitter 使用 OAuth 1.0a 进行认证，这不需要太多的客户端设置(只需要弹出窗口和会话管理)。然而，它确实需要一个重要的服务器端组件，为此我将使用 Sinatra。

对于那些希望跟随本文的人，您可以从 GitHub 获取本文的代码。

## 在 Twitter 上设置应用程序

如果你想跟进，你还需要在 Twitter 上设置一个应用程序。你可以去[http://apps.twitter.com](http://apps.twitter.com)点击“创建新应用”。之后，填写您的详细信息，确保在回调 URL 字段中输入`http://127.0.0.1:9292`(假设您正在本地测试)。

创建应用程序后，您将被重定向至包含应用程序设置的页面。单击“密钥和访问令牌”选项卡，记下您的消费者密钥和消费者机密。

## 服务器设置

这需要对 OAuth 1.0a 的工作原理有一点了解(如果你想复习一下，可以查看 Twitter 网站上的文档)。它还需要一个支持不同 OAuth 提供者身份验证的库。因为我们正在使用 Sinatra， [OmniAuth](http://intridea.github.io/omniauth/) 是一个很好的选择(它是基于 Rack 构建的，所以几乎可以在任何 Ruby web 应用程序中工作)。如果我们使用 Node，我们可以选择[护照](http://passportjs.org/)来代替。

你可以拿一个应用程序的工作副本，自己启动它，而不是带你去完成服务器的设置。方法如下:

```
git clone https://github.com/sitepoint-editors/torii-twitter-example.git
cd torii-twitter-example
```

然后，在您的终端中将您的消费者密钥和消费者秘密添加到您的环境中

```
export TWITTER_KEY=twitter_key TWITTER_SECRET=twitter_secret
```

运行`bundle`来安装任何依赖项(假设您已经安装了 Ruby)，然后运行`rake db:migrate`来设置数据库。

之后，您需要构建 Ember 应用程序:

```
cd public
npm install && bower install
ember build
```

最后运行`rackup`启动 Sinatra，导航到`http://127.0.0.1:9292`。如果一切按计划进行，你应该可以使用 Twitter 登录你的新应用。

请注意，服务器端点如下:

### 未经验证的用户:

*   `get '/auth/twitter'`:启动一个登录流程，从 Twitter 请求一个令牌，将用户重定向到 Twitter 进行身份验证。
*   `get '/auth/twitter/callback'` : Twitter 在这里认证并发送令牌，服务器用令牌交换访问令牌并认证用户。

### 已验证的用户:

*   `post '/logout'`:清除用户认证。
*   `get '/users/me'`:返回认证用户信息。

现在，让我们使用我们的应用程序来看看如何在自己的项目中实现 Torii。

## 安装 Torii

首先，用 [Ember CLI](http://www.ember-cli.com/) 设置一个 Ember 项目，安装 Torii(我们的安装在`public`文件夹中):

```
ember init
npm install –save-dev torii
```

## 配置提供程序

接下来，添加 Twitter 提供者，并将`requestTokenUri`设置为流程开始的服务器端点:`/auth/twitter`。还要设置`sessionServiceName: 'session'`来设置会话管理器。

`config/environment.js`

```
ENV.torii = {
  sessionServiceName: 'session',
  providers: {
    'twitter': {
      requestTokenUri: '/auth/twitter'
    }
  }
};
```

Torii 有[几个内置的提供者](https://github.com/Vestorly/torii#built-in-providers)，然而创作你自己的被设计得很容易。

## 签到

接下来，设置登录模板。如果用户通过了身份验证，显示他们的用户名和注销链接。如果他们未通过身份验证，则显示登录链接。将它放在`application`模板中是有意义的，这样它在每条路线中都可见。

`app/templates/application.hbs`

```
{{#if session.isWorking }}
  Working..
{{else}}
  {{#if session.isAuthenticated }}
    <p>Welcome {{session.currentUser.name}}
      <a href="#" {{action 'logout'}}>Logout</a>
    </p>
  {{else}}
    <a href="#" {{action 'signInViaTwitter'}}>Login via Twitter</a>
  {{/if}}
{{/if}}
```

属性是由 Torri 的会话管理器注入的，它公开了几个有用的属性。在从`opening`到`authenticated`或者从`closing`到`unauthenticated`的状态转换之间`session.isWorking`为真。在转场之间不要使用`session`，而是显示一个加载栏。`session.currentUser`是经过身份验证的用户——由适配器设置。

然后，定义一个`signInViaTwitter`动作，该动作将打开一个弹出窗口并启动 OAuth 登录流程。

`app/routes/login.js`

```
actions: {
  signInViaTwitter: function() {
    var route = this;
    this.get('session').open('twitter').then(function() {
      route.transitionTo('index');
    }, function() {
      console.log('auth failed');
    });
  }
}
```

请注意，`this.get('session').open('twitter')`返回一个承诺，它在验证用户身份后被解析，然后关闭弹出窗口并建立会话。一旦用户会话建立，它就转换到索引路由，而如果失败，它什么也不做。

如果用户刷新浏览器，或者第一次打开浏览器，而会话仍在运行，应用程序应该获取现有会话并继续运行，就像用户已经登录一样。最后，如果用户注销，会话应该被销毁。

`app/routes/application.js`

```
export default Ember.Route.extend({
  beforeModel: function() {
    return this.get('session').fetch().then(function() {
      console.log('session fetched');
    }, function() {
      console.log('no session to fetch');
    });
  },

  actions: {
    logout: function() {
      this.get('session').close();
    }
  }
});
```

这里`this.get('session').fetch()`获取现有的会话，并将用户设置为已验证。我们将它放在`beforeModel`钩子中，这样应用程序将会等待，直到用户在渲染之前被获取。正如您所料，`this.get('session').close()`会关闭会话——这发生在用户点击“注销”的时候。

## 会话管理和适配器

Torii 适配器通过三种方法处理服务器认证和管理用户会话，`open`、`fetch`和`close`。它们放在文件夹`app/torii-adapters`中。默认是一个应用程序适配器，它扩展了`Ember.Object`。

`open`方法创建会话。当我们的服务器向 Ember 应用程序(通过重定向弹出窗口)发送一个带有`code`参数(比如`/?code=authentication_code`)的认证令牌时，它就会这样做。这个`code`由 Torii 解析，并通过`auth`参数传递给我们的适配器。简单描述一下流程:

1.  鸟居打开一个弹出窗口到`/auth/twitter`。
2.  服务器重定向到 Twitter。
3.  用户通过 Twitter 认证。
4.  Twitter 重定向到`/auth/twitter/callback`。
5.  服务器对用户进行身份验证，并生成访问令牌。
6.  服务器使用访问令牌重定向到我们的 Ember 应用程序，例如:`/?code=access_token`
7.  Torii 关闭弹出窗口，解析代码并将其传递给适配器。

一旦令牌可用，它就被放在本地存储中，并为 Ajax 请求设置授权头。通过向`users/me`发送 Ajax 请求来检索经过身份验证的用户，并将其存储在会话中。

`app/torii-adapters/application.js`

```
open: function(auth) {
    if (!auth.code) {
      return rejectPromise();
    }

    localStorage.token = auth.code;
    var adapter = this.container.lookup('adapter:application');
    adapter.set('headers', { 'Authorization': localStorage.token });

    return this.get('store').find('user', 'me').then(function(user) {
      return {
        currentUser: user
      };
    });
  },
```

`auth`参数包含代码——如果不可用，承诺将被拒绝，认证将失败。为 Ember 数据设置头的方法是使用`this.container.lookup`找到`adapter:application`，并在那个适配器上设置头。`this.get('store').find('user', 'me')`向`users/me`发送请求，然后返回一个具有`currentUser`属性(设置为认证用户)的对象。Torii 将把它添加到`session`对象中，它将把这个对象注入到所有的路线中，这样它将在模板中可用。

**注意**:您需要使用 Ember CLI 定义一个用户模型，以便使用 Ember 数据向`users/me`端点发出请求:

`ember g model user name:string token:string`

`fetch`方法检查本地存储中的现有令牌。如果存在，它获取令牌并建立会话。只要令牌有效并保存在本地存储中，就可以在页面刷新之间对用户进行身份验证。

```
fetch: function() {
  if (!localStorage.token) {
    return rejectPromise();
  }

  var adapter = this.container.lookup('adapter:application');
  adapter.set('headers', { 'Authorization': localStorage.token });

  return this.get('store').find('user', 'me').then(function(user) {
    return {
      currentUser: user
    };
  });
},
```

用令牌获取认证用户的方法是从`users/me`获取用户。Torii 不知道如何保持会话，只要你通过适配器钩子提供逻辑。请随时分享您可能有的任何替代方法。

最后，`close`方法关闭会话。它从本地存储中删除令牌，并向服务器发出一个`post /logout` Ajax 请求，这将使访问令牌无效。

```
close: function() {
  var authToken = localStorage.token;

  localStorage.token = null;
  var adapter = this.container.lookup('adapter:application');
  adapter.set('headers', { 'Authorization': authToken });

  return new Ember.RSVP.Promise(function(resolve, reject) {
    Ember.$.ajax({
      url: '/logout',
      headers: {
        'Authorization': authToken
      },
      type: 'POST',
      success: Ember.run.bind(null, resolve),
      error: Ember.run.bind(null, reject)
    });
  });
}
```

## 结论

我花了一些时间才理解身份验证应该如何为单页面应用程序工作，更不用说 OAuth 是如何工作的了。如果应用程序与 REST API 通信，这种情况尤其如此，因为 REST API 应该是无状态的，因此没有服务器端会话可用于持久化用户。所以，我更喜欢基于令牌的认证。Ember 很不幸的是缺少这样的教程，所以如果你想了解更多，你应该搜索其他框架如 AngularJS。

以下是一些你可能会觉得有用的阅读材料:

*   [使用 AngularJS 进行基于令牌的认证](http://code.tutsplus.com/tutorials/token-based-authentication-with-angularjs-nodejs--cms-22543)
*   [令牌与基于 cookie 的认证](https://auth0.com/blog/2014/01/07/angularjs-authentication-with-cookies-vs-token/)
*   [使用 AngularJS 认证](https://www.sitepoint.com/implementing-authentication-angular-applications/)
*   [使用 Torii 进行 GitHub 认证](http://appallingfarrago.com/ember/embercli/github/divshot/torii/2014/11/02/ember-cli-github-authentication-and-divshot.html)
*   [JSON 网络令牌](https://www.sitepoint.com/using-json-web-tokens-node-js/)
*   [Satellizer、AngularJS 版 Torii](https://github.com/sahat/satellizer)

## 分享这篇文章