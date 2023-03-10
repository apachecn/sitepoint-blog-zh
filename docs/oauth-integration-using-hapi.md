# 使用哈比神的 OAuth 集成

> 原文：<https://www.sitepoint.com/oauth-integration-using-hapi/>

保护 web 资源通常是一项困难而艰巨的任务。以至于它经常被留到开发的最后阶段，然后被匆忙处理，没有做好。这是可以理解的；安全性在开发中是一个非常专业的领域，大多数人只是想一想——“是的，这可能应该是安全的……”所以开发人员很快拼凑出一个特别的安全方法:

```
if (password === "password1") {
  setCookie();
}
else {
  send(401);
}
```

运输充满安全漏洞的产品。希望这个片段过于简单，但是这个观点仍然有效。

值得庆幸的是，那里有*的*开发者，他们花费*大量*的时间试图保护网站和网络资源，我们可以依靠他们的专业知识来帮助我们保护自己的项目，而不必重新发明轮子。

在本文中，我们将介绍如何使用 OAuth 令牌通过 GitHub 凭证对用户进行身份验证。所有这些单词放在一起可能听起来非常困难，但是由于一些有很好文档记录的模块，我想你会惊讶它是多么容易。

## 先决条件

假设读者:
1。对使用 [hapi](https://github.com/hapijs/hapi) 服务器框架有功能上的理解。
2。已经建立了网络资源。
3。对 cookies 有基本的了解。
4。拥有 GitHub 帐户。
5。对什么是誓言以及它的用途有一个初步的了解(你可以从阅读关于它的维基百科文章开始)。

如果这些假设中有任何一个不成立，强烈建议你先掌握列出的先决条件，然后再回来学习如何保护你的网页。

## 入门指南

您需要做的第一件事是创建一个 GitHub 应用程序。这个过程将使您获得`ClientID`和`ClientSecret`——这两个值都是您在 web 服务器上设置 OAuth 所需要的。

1.  登录你的 GitHub 账户，进入设置页面(https://github.com/settings/profile)
2.  点击“应用程序”
3.  按下“生成新应用程序”按钮，您将被导航到一个新屏幕，如下所示:
    ![Register a new OAuth application](img/6da75bc5b5595b810c4baa66d009f78b.png)
4.  **应用名称**和**应用描述**可以是你想要的任何东西。对于**主页 URL** 和**授权回调 URL** ，让我们将它们设置为我们将要使用的本地服务器。在我的例子中，我将使用端口 9001，因此将两个值都设置为“http://localhost:9001”。我的完整设置如下:
    ![Completed application configuration](img/8320adfde06955739fb275038343b1b5.png)
5.  按下“注册应用程序”后，您将被重定向到一个新屏幕，该屏幕将列出`ClientID`和`ClientSecret`。记下这些值以备后用。

### 摘要

这一步纯粹是行政性的。我们创建了一个新的 GitHub 应用程序，当用户试图登录您的网站时，会被询问这个问题。我们不信任 http://localhost:9001 和我们的 GitHub 凭证，而是信任 GitHub 应用程序来验证用户，然后在完成后回调我们的网站。

## 规划服务器

在我们开始编码之前，让我们大致了解一下我们希望服务器做什么。为了简单起见，我们将从四个路由开始:一个本地路由、一个帐户信息路由、一个登录路由和一个注销路由。

在 home route 中，如果用户已经过身份验证，就让我们打印他们的名字，否则就打印一条通用消息。对于帐户路径，我们将显示 GitHub 发送给我们的所有信息。如果用户请求帐户页面而没有首先进行身份验证，我们将用正确的状态代码 401 进行响应。登录路径将到达 GitHub，请求用户允许我们的 GitHub 应用程序访问他们的一些帐户信息，然后返回到我们的本地 web 服务器。最后，注销路径会将用户从我们的网站注销。

## 服务器框架

让我们先把样板文件和路由配置放在一边。

```
var Hapi = require('hapi');
var server = new Hapi.Server();

server.connection({ port: 9001 });

server.register([], function (err) {

    if (err) {
        console.error(err);
        return process.exit(1);
    }

    server.route([{
            method: 'GET',
            path: '/login',
            config: {
                handler: function (request, reply) {

                    // Reach out to GitHub, ask the user for permission for their information
                    // if granted, response with their name
                    reply();
                }
            }
        }, {
            method: 'GET',
            path: '/account',
            config: {
                handler: function (request, reply) {

                    // Show the account information if the have logged in already
                    // otherwise, send a 491
                    reply();
                }
            }
        }, {
            method: 'GET',
            path: '/',
            config: {
                handler: function (request, reply) {

                    // If the user is authenticated reply with their user name
                    // otherwise, replay back with a generic message.
                    reply();
                }
            }
        }, {
            method: 'GET',
            path: '/logout',
            config: {
                handler: function (request, reply) {

                    // Clear the session information
                    reply.redirect();
                }
            }
        }
    ]);
    server.start(function (err) {

        if (err) {
            console.error(err);
            return process.exit(1);
        }

       console.log('Server started at %s', server.info.uri);
    });
});
```

**清单 1** 骨架 hapi 服务器

### 摘要

上面的代码创建了一个服务器，一个端口`9001`上的连接，并添加了一些带有存根处理函数的路由。你会注意到`server.register([], function() {...}`，我们正在传递一个空数组。随着我们的继续，我们将开始向 hapi 中添加插件，但是对于最初的样板文件，我们将把它们保留下来。我们使用`server.route`来指定我们想要构建的四条路线，并向它们传递`path`和`method`字符串以及一个`config`对象。在接下来的部分中,`config`对象将会被大量使用。目前，我们在每条路由上都回复了一个空响应。如果启动服务器，您应该会看到:

```
Server started at http://hostname.local:9001
```

您应该能够向所有已定义的路由发出`GET`请求，并收到空的 200 响应。

如果您过去曾经与 hapi 一起工作过，那么这份样板文件中没有什么值得惊讶的。如果没有，请到文档网站[这里](http://hapijs.com/api)来帮助澄清事情。

## 插入电源

hapi 最好的部分之一是插件系统。插件允许 hapi 应用程序的片段被分割成小的、可移植的模块。几乎所有你可以用 hapi 服务器对象做的事情，你都可以用插件来做。您可以添加路由、扩展点、监听事件、创建缓存段；甚至注册一个与主服务器对象不同的视图引擎。更多关于插件的信息，请查看 hapijs.com 上的[教程](http://hapijs.com/tutorials/plugins)。

对于这个例子，我们将使用[贝尔](https://github.com/hapijs/bell)和 [hapi-auth-cookie](https://github.com/hapijs/hapi-auth-cookie) 插件。

### 钟

bell 是一个 hapi 插件，用来处理与第三方 OAuth 提供者集成所需的大部分繁琐的握手。它内置了对最常用的 OAuth 客户端(脸书、Twitter、GitHub 和 Google，仅举几例)的支持。这意味着 OAuth 与 GitHub 集成的大部分繁重工作已经完成。我们只需要配置我们的 hapi 服务器来使用它。

bell 处理 OAuth 所需的所有来回操作，并且当用户成功通过身份验证时，只*调用相关的 hapi 处理函数。否则，hapi 将以 401 响应。值得注意的一点是，bell 没有任何用户会话的概念。这意味着一旦单个请求通过第三方进行了身份验证，该身份验证将在后续请求中失效。您可以使用 bell 来保护您的所有路由，但是用户对您的网站发出的每个请求都需要 OAuth 舞蹈，这将是非常低效的。我们需要一种方法来创建保存 OAuth 会话信息的安全 cookie，并使用该安全 cookie 来验证未来的请求。*

### 哈皮认证饼干

hapi-auth-cookie 提供了简单易用的 cookie 会话管理。用户必须通过其他方式进行身份验证；hapi-auth-cookie 所做的只是提供一个 api 来获取和设置加密的 cookie。它还有一些其他的实用功能，但是重要的是要理解它本身并不进行任何身份验证。

hapi-auth-cookie 通过`request.auth.session`添加方法来扩展 hapi `request`对象；具体来说就是`request.auth.session.set`和`request.auth.session.clear`。`set`用于创建安全会话 cookie，`clear`用于删除它。这些方法被添加到一个' onPreAuth' [服务器扩展点](https://github.com/hapijs/hapi/blob/master/API.md#serverextevent-method-options)中。

对于我们的服务器，bell 将负责所有的 OAuth 协商，如果协商成功，将使用 hapi-auth-cookie 设置一个带有`request.auth.session.set`的加密 cookie。

### 配置插件

在下一个代码部分，我们将填充空的`register`函数，并为我们在图 1 中开始的服务器配置两个插件。

```
var Hapi = require('hapi');
var Bell = require('bell');
var AuthCookie = require('hapi-auth-cookie');

//... refer to Listing 1

server.register([Bell, AuthCookie], function (err) {

    if (err) {
        console.error(err);
        return process.exit(1);
    }

    var authCookieOptions = {
        password: 'cookie-encryption-password', //Password used for encryption
        cookie: 'sitepoint-auth', // Name of cookie to set
        isSecure: false
    };

    server.auth.strategy('site-point-cookie', 'cookie', authCookieOptions);

    var bellAuthOptions = {
        provider: 'github',
        password: 'github-encryption-password', //Password used for encryption
        clientId: 'huU4KjEpMK4TECW',//'YourAppId',
        clientSecret: 'aPywVjShm4aWub7eQ3ub3FbADvTvz9',//'YourAppSecret',
        isSecure: false
    };

    server.auth.strategy('github-oauth', 'bell', bellAuthOptions);

    server.auth.default('site-point-cookie');

    //... refer to Listing 1
```

**清单 2** 配置 bell 和 hapi-auth-cookie 插件

#### 代码解释

`server.register`是向 hapi 服务器添加插件的入口点。它支持几种不同的函数签名，但是根据我们的需要，我们将传递一个对象数组。每个对象必须实现一个`register`函数，该函数将被调用并提供给当前的 hapi 服务器对象。一旦注册了所有插件，回调就会执行。

这里我们需要稍微绕一下，解释一下 hapi 是如何处理认证的。使用 hapi 的认证分为两个概念；模式和策略。文档中，[这里的](http://hapijs.com/tutorials/auth)描述得最好:

> 把一个方案想象成一种通用类型的身份验证，比如“基本”或“摘要”。另一方面，策略是方案的预先配置和命名的实例。

除了非常特殊和高级的情况，您将使用预先构建的方案，并配置适合您的应用程序的特定策略。认证策略将在整个应用程序中使用以保护资源，并且是方案的“实例”；方案是对请求进行身份验证的一种方式。bell 和 hapi-auth-cookie 都通过`server.auth.scheme`注册新方案；“贝尔”和“曲奇”计划。

方案名称是`server.auth.strategy`的第二个参数。在注册使用该方案的策略之前，必须向 hapi 服务器注册该方案。这就是为什么我们需要先注册插件，然后通过`server.auth.strategy`设置策略。

#### 站点点 cookie

在清单 2 中，我们首先注册了一个“cookie”策略，并将其命名为“site-point-cookie”。在整个代码中，我们将引用“site-point-cookie”来指代这个已配置的 cookie 策略。所有可用选项的完整解释可在[这里](https://github.com/hapijs/hapi-auth-cookie)找到。在我们的例子中，我们只使用了`password`、`cookie`和`isSecure`。`password`应该是一个强字符串，因为它将被 [iron 模块](https://github.com/hueniverse/iron)用来加密和解密 cookie。`cookie`是 cookie 的名称，`isSecure`设置结果集 Cookie 头的“安全”选项。这意味着这个 cookie 将只通过 HTTPS 连接传输。我们现在将它设置为`false`是为了更容易使用这个例子，但是一般来说，它应该被设置为`true`。

#### github-oauth

第二个也是更有趣的策略是一个名为“github-oauth”的“bell”类型。类似于“site-point-cookie”注册，我们传递一个名称、一个方案和一个选项对象。贝尔战略选项的完整列表可以在贝尔回购[这里](https://github.com/hapijs/bell)找到。`provider`设置为‘GitHub ’,因为 bell 内置了对 GitHub OAuth 集成的支持。如果您试图与 bell 未知的提供者集成，也可以将它设置为一个对象。`password`是在协议授权步骤中用于加密*临时* cookie 的字符串。该 cookie 仅在授权步骤期间存在，之后会被销毁。`clientId`和`clientSecret`是我们在“入门”部分创建的价值。清单 2 中的值将*而不是*起作用，因为它们只是随机的乱码。对于这个例子，您需要将自己的值插入代码中。最后，`isSecure`提供与它在‘站点-点-cookie’中相同的功能。

最后，我们为整个服务器设置默认身份验证，以使用名为“site-point-cookie”的 cookie 策略。这只是一个方便的设置。它告诉 hapi 对每个添加了`server.route`的路由使用“站点-点-cookie”策略来验证请求。这大大减少了每条路由所需的重复配置选项的数量。

## 让它工作

我们终于完成了所有的配置和设置！剩下的就是几行逻辑将所有东西连接在一起。一旦您看到所需的代码量，您就会发现 hapi 实际上是一个以配置为中心的框架。让我们仔细检查清单 1 中的每一条路线，并更新配置对象和处理程序以发挥作用。

### 登录路线

登录路由是需要到达 GitHub 服务器并与 GitHub 服务器进行 OAuth 舞蹈的路由。清单 3 显示了更新后的路由配置选项:

```
method: 'GET',
path: '/login',
config: {
    auth: 'github-oauth',
    handler: function (request, reply) {

        if (request.auth.isAuthenticated) {

            request.auth.session.set(request.auth.credentials);
            return reply('Hello ' + request.auth.credentials.profile.displayName);
        }

        reply('Not logged in...').code(401);
    }
}
```

**清单 3** 登录路线更新

这里只更改了`config`选项。首先，我们要将`auth`选项设置为‘github-oauth’。这个值指的是我们在清单 2 中创建的名为“github-oauth”的“bell”策略。这告诉 hapi 在尝试认证该路由时使用“github-oauth”策略。如果我们省略了这个选项，hapi 将退回到使用我们在清单 2 中指定的默认策略；“站点点 cookie”。可用的`auth`选项的完整列表超出了本文的范围，但是您可以在这里阅读关于它们的更多内容[。](https://github.com/hapijs/hapi/blob/master/API.md#route.config.auth)

在处理函数中，我们检查请求的`request.auth.isAuthenticated`值。在启用了认证的路由上，`request.auth`仅被添加到`request`和*中。如果`isAuthenticated`为真，我们希望设置一个 cookie 来表明这一点。记住，hapi-auth-cookie 用`set`和`clear`函数给`request.auth`添加了一个`session`对象。现在用户已经通过了 GitHub 的身份验证，我们想创建一个会话 cookie，用`request.auth.session.set`在整个应用程序中使用，并传递从 GitHub 返回给我们的凭证对象。这将根据我们传递给 hapi-auth-cookie 的选项创建一个名为' sitepoint-auth '的加密 cookie。最后，我们想用一条显示 GitHub 显示名称的小消息来响应。*

如果用户没有通过身份验证或拒绝 GitHub OAuth 访问，我们将用一条消息和一个 401 状态代码来响应。

### 客户路线

如果用户已经登录，account route 应该向用户显示 GitHub 信息，如果没有登录，则用 401 响应。更新后的配置和处理程序代码如清单 4 所示。

```
method: 'GET',
path: '/account',
config: {
    handler: function (request, reply) {

        reply(request.auth.credentials.profile);
    }
}
```

**清单 4** 账户路线更新

这条路线没什么变化。因为我们没有覆盖`config`对象中的任何`auth`值，所以这条路由使用默认的 cookie 策略。当请求帐户路由时，hapi 将查找“site point-auth”cookie，并确保它存在并且是该请求的有效 cookie。如果是，将调用处理程序，否则响应将是 401。`request.auth.credentials`是我们在清单 3 的登录路径中设置的 cookie 值，`profile`是 GitHub 存储大部分用户帐户信息的地方。

至此，您应该能够测试我们添加的两条路由('/login '和'/account ')，并了解它们如何协同工作以及如何响应。

### 回家路线

像大多数网站一样，我们应该在站点的根目录下有一个路由。回头看看我们希望该路由做什么，响应应该根据用户的身份验证状态来定制。如果用户没有登录，他们不应该收到 401，而是应该看到一个非定制的主页。如果他们登录了，我们希望用定制的消息欢迎他们回来。

```
method: 'GET',
path: '/',
config: {
    auth: {
        mode: 'optional'
    },
    handler: function (request, reply) {

        if (request.auth.isAuthenticated) {
            return reply('welcome back ' + request.auth.credentials.profile.displayName);
        }

        reply('hello stranger!');
    }
}
```

**清单 5** 回家路线更新

清单 5 向`auth`设置引入了一个新概念；`mode`。`mode`值可以取三个字符串值之一；必需的、可选的和尝试的。“必需”意味着请求必须具有有效的身份验证。“可选”意味着请求不需要*需要*进行认证，但是如果需要，它必须是有效的。最后，“尝试”与“可选”相同，但身份验证不必有效。

这条路由有我们在清单 2 中设置的默认 cookie 策略，所以我们需要做的就是设置`mode`并且`strategy`将是‘site-point-cookie’。在处理程序中，我们可以检查请求的`auth`状态，类似于清单 3。如果为真，则用户拥有一个有效的‘site point-auth’cookie，我们可以通过请求来响应存储在`request.auth.credentials`中的信息；就像清单 4 一样。如果`auth`状态为假，我们对用户一无所知，处理函数将回复一条普通消息。尝试将`mode`更改为“必需”并清除您的 cookies，以查看“必需”和“可选”之间的区别。

### 注销路线

最后，让我们更新注销路由以删除会话 cookie。

```
method: 'GET',
path: '/logout',
config: {
    auth: false,
    handler: function (request, reply) {

        request.auth.session.clear();
        reply.redirect('/');
    }
}
```

**清单 6** 注销路线更新

因为我们对所有路由都有一个默认的身份验证策略，所以我们希望禁用此路由的`auth`,以允许任何请求通过。如果使用默认策略，记住这一点很有用。否则，您将最终对每个发送到您的服务器的请求进行身份验证，而您可能不希望这样；尤其是对于静态资源。在处理程序中，我们调用`request.auth.session.clear()`来清除‘site point-auth’cookie，最后我们将用户重定向回站点的根目录。如果用户没有‘site point-auth’cookie，这段代码本质上是“无操作的”,但不会造成任何伤害，是绝对安全的。

## 摘要

这看起来像是很多话，但是大部分是解释配置选项和一些 hapi 身份验证内部是如何工作的。hapi 将认证分为两个概念；方案和策略。方案是一般类型的认证，而策略是方案的配置实例。我们使用 bell 与 GitHub 进行 OAuth 舞蹈，并使用 hapi-auth-cookie 将用户的 GitHub 信息保存到一个名为“sitepoint-auth”的加密 cookie 中。我们在应用程序的其余部分使用这个 cookie 来确定身份验证状态。

实际路由处理程序中的大部分代码都非常琐碎，因为大部分繁重的工作都是由 hapi 插件完成的。在登录路径中，我们设置了一个包含所有从 GitHub 发送的信息的安全 cookie。在 account 资源中，cookie 的当前内容作为 JSON 发送回用户。在 home route 中，我们更改了认证`mode`以允许混合使用无认证和认证，这对于根资源来说是一个非常常见的场景，并做出了相应的响应。最后，我们完全禁用了注销路由的 auth，并清除了“site point-auth”cookie，将用户重定向到主页。

希望在阅读完这篇文章后，您会发现所需的大部分工作只是在配置方面。除了基本的 hapi 样板文件之外，几乎没有什么代码。我鼓励你在这里查看完整的工作代码[，自己尝试不同的选项和认证设置。](https://gist.github.com/arb/9a1d8e694bbd12d5b455)

* * *

如果你想了解更多关于 Hapi.js 的信息，请观看我们的[用 Hapi.js](https://www.sitepoint.com/premium/courses/plugin-systems-with-hapi-js-2934) 构建插件迷你课程的视频样本。在本课程中，你将通过一系列视频学习哈比神的基本原理，包括路由、视图、请求生命周期和哈比神强大的插件系统。

## 分享这篇文章