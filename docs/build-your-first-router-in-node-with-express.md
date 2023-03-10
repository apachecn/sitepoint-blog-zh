# 使用 Express 在 Node 中构建您的第一台路由器

> 原文：<https://www.sitepoint.com/build-your-first-router-in-node-with-express/>

*这篇文章最初发表在[Okta 开发者博客](https://developer.okta.com/blog/2018/11/27/first-router-node-express)上。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

如果您在过去几年中使用 Node 进行过任何 web 开发，那么您可能使用过 Express。即使您没有直接使用它，许多旨在使 web 开发更加简单的框架仍然是基于 Express 构建的。

Express 的一个关键特性是创建路线的能力。URL 的无限组合可以访问同一个 Express 服务器，而路由是您确定哪些 URL 运行哪段代码的方式。您可以使用参数和通配符，这样就不必显式地声明每个端点。

在本教程中，我将带你创建一个服务器，并教你所有你需要知道的关于 Express 中的路线。

## Express 中的路线是什么？

路由决定了在给定任何 URL 的情况下应该传递什么数据。我们以最基础的文件服务器为例。假设您有一个文件结构:

```
files/
├── images/
│   ├── cat.png
│   ├── dog.jpg
│   └── pig.bmp
└── text/
    ├── README.md
    └── todo.txt 
```

然后，您可以运行一个简单的 HTTP 服务器，它将自动为这些文件提供服务，并为目录创建一个索引。没有`files/index.html`，但是服务器仍然在生成一个网页，并根据该文件夹中的文件提供内容。如果你img/cow.gif`你会得到一个 404 错误——即使那里没有文件，它仍然提供*什么的*。

```
npm install -g http-server
cd files
http-server 
```

![File Server Demo](img/e0e6cbb880ee290e16cec9f89d551aa1.png)

在 Express 中，一条路线由一个`method`、一个`path`和一个`handler`组成。

### 方法、路径和处理程序，天啊！

`method`可以是任何 HTTP 动词，比如`GET`(获取内容——这是大多数网页使用的),或者`POST`(向服务器发送内容——这在 HTML 表单中很常见)。如果您愿意，还可以指定希望 Express 为所有方法处理相同的路径。

`path`是描述相对 URL 的字符串或正则表达式。如果您正在使用应用程序的根目录，这将描述绝对 URL。路径可以用几种方式定义。

*   **简单字符串**:字符串`'/'`指定您想要在路由器的根使用该路由。一串`'/asdf'`将覆盖路径`/asdf`
*   **通配符**:字符串也可以包含一些通配符，其工作方式类似于正则表达式，但有一点限制:
    *   `?` : A `?`表示前一个字符是可选的。路径`'/Joh?n'`将覆盖`/Jon`和`/John`
    *   `+` : A `+`表示前面的角色可以重复任意多次，但必须至少一次。一条`'/ni+ce'`路径将覆盖`/nice`以及`/niiiiiiiiiiiiiiiiice`
    *   `*` : A `*`表示前一个字符是可选的，可以重复任意多次。一条`'/wow!*'`路径将匹配`/wow`、`/wow!`，甚至`/wow!!!!!!!!!!!!`
    *   `()`:您也可以对一组字符应用通配符。`'/(ha)+'`将匹配`/ha`、`/haha`和`/hahahahaha`，但不匹配`/hah`
*   **正则表达式**:如果你想超越基本的通配符，你可以使用正则表达式。有了`/^\/(pen-)?((pine)?apple-)+pen$/`，你可以匹配`/apple-pen`、`/pineapple-pen`或`/pen-pineapple-apple-pen`。
*   **参数**:另一个非常有用的功能，就是你可以在你的路线中设置参数。这让您可以轻松地为 RESTful URLs 提供动态部分。一个路径`'/posts/:postId'`将不仅匹配`/posts/42`，而且请求将包含一个值为`'42'`的`params.postId`变量。

当做某事时，方法和路径对于知道*是必不可少的，但是处理程序是回调函数，在那些情况下实际上被调用。一个处理程序被传递一个`request`、一个`response`和一个`next`回调，这些参数通常被写成`(req, res, next)`..*

*   **请求(`req` )** :请求包含了用户询问的各种信息。从这里，您可以访问路径、参数、头文件和许多其他东西。对于请求中的所有内容，您可以参考 [API 参考](https://expressjs.com/en/4x/api.html#req)
*   **回应(`res` )** :回应就是你如何把信息反馈给用户。发回数据最简单的方法是使用`.send`方法(例如`res.send('Hello, world!')`，但是还有许多其他方法。同样，您可以在 [API 参考](https://expressjs.com/en/4x/api.html#res)中找到所有的方法
*   **下一次回调(`next`)**:`next`功能允许您对同一路线使用多个处理程序。您可以使用一个处理程序来处理信息，当它完成时，它可以调用`next()`来表示可以继续下一个处理程序了。如果您传入一个字符串，它将抛出一个错误，您可以在别处捕捉到这个错误，或者显示给用户(例如`next('You must be authenticated to access this route')`)。

## Express 中的路由器是什么？

现在，您对路由有了更多的了解，它与路由器有何不同？您可以将路由器视为路由的集合。这是组织应用程序不同部分的有效方法。

使用路由器时，您可以考虑根路径，即使您将从某个子路径使用该路由器。例如，假设您有一个管理消息的 API。你可以有一个路由器，它有一条路径`'/'`到`GET`所有消息或者`POST`一条新消息。你可以有另一条路径`'/:id'`到`GET`或`PUT`(编辑)一条特定的消息。

然后，您的应用程序可以在`/messages`使用`app.use('/messages', messageRouter)`托管该路由器。路由器本身不必关心它的全局路径是什么，甚至可以在多条路由中使用(例如`/messages`、`/texts`和`/email`)。

## 使用 Express 在节点中创建一个带有路由器的简单应用程序

已经说得够多了…让我们来看看真正的代码。首先，创建一个存放所有代码的文件夹。然后建立一个`package.json`文件夹来帮助管理依赖关系。你可以用`npm init`来做到这一点。你还需要安装 [Express](https://expressjs.com/) 。

```
mkdir my-first-router
cd my-first-router
npm init -y
npm install express@4.16.4 hbs@4.0.1 
```

用以下代码创建一个`index.js`文件:

**index.js**

```
const express = require('express')
const path = require('path')

const app = express()

app.set('views', path.join(__dirname, 'views'))
app.set('view engine', 'hbs')

app.get('/', (req, res) => {
  res.render('index', {
    title: 'Hello, world!',
    content: 'How are you?'
  })
})

const port = process.env.PORT || 3000
app.listen(port, () => console.log(`App listening on port ${port}`)) 
```

这告诉 Express 使用[手柄](https://handlebarsjs.com) ( `hbs`)作为视图引擎。它使用节点的内置`path`来告诉它包含视图的目录。`/`路径被告知使用`index.hbs`呈现页面，这将把`content`放在一个段落(`p`)标签中。

为了确保 Express 有要渲染的模板，创建一个名为`views`的新文件夹，然后在那里创建一个名为`layout.hbs`的新文件。当你告诉 Express 渲染一个视图时，它将首先渲染`layout.hbs`，并将视图的内容放在`{{{body}}}`标签内。这允许你为应用程序建立一个框架。这里有一些使用 [Bootstrap](https://getbootstrap.com/) 的基本 HTML，它会给你一些漂亮的样式而不需要写任何 CSS。这也将在你的`/`路线中渲染传入上下文的`title`。

**views/layout.hbs**

```
<!doctype html>
<html lang="en">
  <head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css" integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">

    <title>{{title}}</title>
  </head>
  <body>
    <h1>{{title}}</h1>
    <main>
      {{{body}}}
    </main>
  </body>
</html> 
```

您还需要创建一个`index.hbs`视图，这只是目前最基本的:

**views/index.hbs**

```
<p>{{content}}</p> 
```

为了让开发变得简单一点，您可以安装`nodemon`:

```
npm install --save-dev nodemon@1.18.4 
```

然后修改您的`package.json`文件，使`"scripts"`条目包含一个带有`nodemon .`的启动脚本。这将使您可以简单地运行`npm start`，并且您的服务器将在您做出更改时自动重启:

```
"scripts": {
  "start": "nodemon ."
} 
```

现在，在您的终端中，如果您键入`npm start`，您将启动服务器。然后，您可以前往`http://localhost:3000`查看应用程序的运行情况。

![File Server Demo](img/a1e7789fcd9a9bc7722bb23f9e446f84.png)

## 在 Express 中创建路由器

嗯，那有点无聊。让它做点有用的事情怎么样？让我们创建一个简单的待办事项列表。首先创建一个路由器来管理项目列表。创建一个名为`todo.js`的新文件:

**todo.js**

```
const express = require('express')

const router = express.Router()

let todo = []

router.post('/', (req, res, next) => {
  todo = [...req.body.todo || []]
  if (req.body.remove) todo.splice(req.body.remove, 1)
  if (req.body.new) todo.push({})

  next()
})

router.use('/', (req, res) => {
  res.render('todo', { title: 'To-do list', todo })
})

module.exports = router 
```

这里有两个路由处理器。第一个监听`POST`请求(用`router.post`表示)。它将用从表单接收到的任何内容的副本替换待办事项列表。如果表单包含`remove`属性(包含一个索引)，它将使用`splice`删除该索引处的元素。如果表单包含`new`属性，一个新的条目将被推送到数组中。在完成修改待办事项列表后，它调用`next()`继续下一个路由处理程序。

总是使用第二个路由处理器(用`router.use`表示)。它的唯一目的是呈现待办事项列表。通过像这样分离路由，您可以很容易地总是做一件事，而只在特定情况下做另一件事(在本例中是基于`POST`请求)。

要告诉应用程序使用这个路由器，您必须在`index.js`中添加几行代码:

**index.js**

```
@@ -1,11 +1,15 @@
 const express = require('express')
 const path = require('path')
+const todoRouter = require('./todo')

 const app = express()

 app.set('views', path.join(__dirname, 'views'))
 app.set('view engine', 'hbs')

+app.use(express.urlencoded({ extended: true }))
+app.use('/todo', todoRouter)
+
 app.get('/', (req, res) => {
   res.render('index', {
     title: 'Hello, world!', 
```

现在来看一下`todo`模板。它有点大，所以我把它留到最后。如果你熟悉 HTML，它应该不会太难理解。Handlebars 增加了一些功能，让您可以访问变量。在这种情况下，如果没有任何条目，您将使用一个`{{#if}}`块来呈现一些特殊的内容，并使用一个`{{#each}}`块以最少的标记来呈现每个列表条目。

这里使用的唯一 JavaScript 是当您更改某些内容时自动提交表单。如果 JavaScript 被禁用，当按下键盘上的“Enter”键时，它仍然可以工作，这要感谢标记为“Autosave”的隐藏按钮。

**views/todo.hbs**

```
<form method="post">
  <div class="row">
    <div class="col">
      <button hidden>Autosave</button>
      <button class="btn btn-success" name="new" value="true">New</button>
    </div>
  </div>
  <div class="row mt-3">
    <div class="col">
      {{#if todo.length}}
        <ul class="list-group">
          {{#each todo}}
            <li class="list-group-item d-flex align-items-center">
              <input
                type="checkbox"
                onchange="this.form.submit()"
                name="todo[{{@index}}][checked]"
                {{#if this.checked}}checked{{/if}}
              />
              <input
                name="todo[{{@index}}][text]"
                onchange="this.form.submit()"
                class="form-control mx-2"
                value="{{this.text}}"
              />
              <button class="btn btn-danger" name="remove" value="{{@index}}">Remove</button>
            </li>
          {{/each}}
        </ul>
      {{else}}
        <h5>Your To-Do List is empty</h5>
      {{/if}}
    </div>
  </div>
  <style> input[type=checkbox]:checked + input {
      text-decoration: line-through;
      opacity: 0.75;
    } </style>
</form> 
```

现在转到`http://localhost:3000/todo`，在你的待办事项列表中输入一些项目。

![Buy Milk](img/260b69784b781c32b7f6147b19053e7f.png)

## 在节点中添加用户验证

现在你有了一个功能性的待办事项列表。你可能已经注意到了，只有当你希望每个人都使用它来共享同一个列表的时候，这个功能才会起作用。如果您添加了身份验证，您可以为每个用户创建一个单独的待办事项列表。

添加用户不一定是一件痛苦的事。其实用 Okta 真的很简单就能做到。*什么是 Okta？*，你可能会问。Okta 是一种云服务，允许开发人员创建、编辑和安全存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。

如果你还没有，那么[注册一个永远免费的开发者账户](https://developer.okta.com/signup/)。

你需要保存一些信息，以便在应用程序中使用。创建一个名为`.env`的新文件。在其中，输入您的组织 URL。

```
HOST_URL=http://localhost:3000
OKTA_ORG_URL=https://{yourOktaOrgUrl} 
```

您还需要一个随机字符串作为会话的应用程序密码。您可以使用以下命令生成该文件:

```
echo -e "\nAPP_SECRET=`npx -q uuid`" >> .env 
```

接下来，登录你的开发者控制台，导航到**应用**，然后点击**添加应用**。选择**网**，然后点击**下一个**。给你的应用起一个名字，比如“我的第一个路由器”。将**基地 URI** 更改为`http://localhost:3000/`，将**登录重定向 URI** 为`http://localhost:3000/authorization-code/callback`，然后点击**完成**

点击**编辑**并添加`http://localhost:3000/`的**注销重定向 URI** ，然后点击**保存**。

![Okta application settings](img/8a2e3cafb3e583c63e1ac8a6b74459b8.png)

创建应用程序后，您进入的页面有更多信息需要保存到您的`.env`文件中。复制客户端 ID 和客户端密码。

```
OKTA_CLIENT_ID={yourClientId}
OKTA_CLIENT_SECRET={yourClientSecret} 
```

现在回到代码。您需要添加 Okta 的 OIDC 中间件来控制认证。它也依赖于使用会话。您需要使用`dotenv`从`.env`文件中读入变量。要安装您需要的依赖项，请运行以下命令:

```
npm install @okta/oidc-middleware@1.0.1 dotenv@6.1.0 express-session@1.15.6 
```

现在修改您的`index.js`文件。在这里，您将添加会话和 OIDC 中间件，以及一个`logout`路由，以便用户可以注销应用程序。您还将专门为`todoRouter` ( `app.use('/todo', oidc.ensureAuthenticated(), todoRouter)`)添加一个中间件。通过添加`oidc.ensureAuthenticated()`，您可以让 Okta 确保除非用户登录，否则无法到达该路由。如果用户没有登录，并试图到达该路线，他们将被带到一个安全的网站登录，然后重定向回您的网站。

**index.js**

```
@@ -1,14 +1,46 @@
+require('dotenv').config()
+
 const express = require('express')
 const path = require('path')
+const session = require('express-session')
+const { ExpressOIDC } = require('@okta/oidc-middleware')
+
 const todoRouter = require('./todo')

+const oidc = new ExpressOIDC({
+  issuer: `${process.env.OKTA_ORG_URL}/oauth2/default`,
+  client_id: process.env.OKTA_CLIENT_ID,
+  client_secret: process.env.OKTA_CLIENT_SECRET,
+  redirect_uri: `${process.env.HOST_URL}/authorization-code/callback`,
+  scope: 'openid profile'
+})
+
 const app = express()

+app.use(session({
+  secret: process.env.APP_SECRET,
+  resave: true,
+  saveUninitialized: false
+}))
+app.use(oidc.router)
+
 app.set('views', path.join(__dirname, 'views'))
 app.set('view engine', 'hbs')

 app.use(express.urlencoded({ extended: true }))
-app.use('/todo', todoRouter)
+app.use('/todo', oidc.ensureAuthenticated(), todoRouter)
+
+app.get('/logout', (req, res) => {
+  if (req.userContext) {
+    const idToken = req.userContext.tokens.id_token
+    const to = encodeURI(process.env.HOST_URL)
+    const params = `id_token_hint=${idToken}&post_logout_redirect_uri=${to}`
+    req.logout()
+    res.redirect(`${process.env.OKTA_ORG_URL}/oauth2/default/v1/logout?${params}`)
+  } else {
+    res.redirect('/')
+  }
+})

 app.get('/', (req, res) => {
   res.render('index', { 
```

为了在用户注销时使事情变得更简单，可以在主页上添加一个到待办事项列表的链接。

**views/index.hbs**

```
<p>{{content}}</p>
<a href="/todo">Go to To-Do List</a> 
```

您还可以在您的`layout.hbs`上添加欢迎信息和注销按钮。

**views/layout.hbs**

```
@@ -12,6 +12,12 @@
   </head>
   <body class="container">
     <h1>{{title}}</h1>
+    {{#if userinfo}}
+      <h4>
+        Welcome back, {{userinfo.given_name}}!
+        <a href="/logout">Click here to log out</a>
+      </h4>
+    {{/if}}
     <main>
       {{{body}}}
     </main> 
```

为此，在呈现视图时，您需要将`userinfo`添加到上下文中。

**todo.js**

```
--- a/todo.js
+++ b/todo.js
@@ -13,7 +13,7 @@ router.post('/', (req, res, next) => {
 })

 router.use('/', (req, res) => {
-  res.render('todo', { title: 'To-do list', todo })
+  res.render('todo', { title: 'To-do list', todo, userinfo: req.userContext.userinfo })
 })

 module.exports = router 
```

**index.js**

```
@@ -43,7 +43,10 @@ app.get('/logout', (req, res) => {
 })

 app.get('/', (req, res) => {
+  const { userinfo } = req.userContext || {}
+
   res.render('index', {
+    userinfo,
     title: 'Hello, world!',
     content: 'How are you?'
   }) 
```

好了，现在你要求用户登录后才能编辑待办事项列表，但它仍然是一个单一的共享列表。为了将它分割成针对每个用户的单独列表，对`todo.js`做了另一个小的修改。

**todo.js**

```
@@ -2,17 +2,21 @@ const express = require('express')

 const router = express.Router()

-let todo = []
+const todosByUser = {}

 router.post('/', (req, res, next) => {
-  todo = [...req.body.todo || []]
+  const todo = [...req.body.todo || []]
   if (req.body.remove) todo.splice(req.body.remove, 1)
   if (req.body.new) todo.push({})

+  todosByUser[req.userContext.userinfo.sub] = todo
+
   next()
 })

 router.use('/', (req, res) => {
+  const todo = todosByUser[req.userContext.userinfo.sub] || []
+
   res.render('todo', { title: 'To-do list', todo, userinfo: req.userContext.userinfo })
 }) 
```

![Multi-User Demo](img/0ff291f4b947538b749f2fa46e355b0d.png)

## 了解有关 Node、Express 和 Secure Web 开发的更多信息

现在你有了一个功能齐全的待办事项列表，我鼓励你扩展它。尝试将数据存储在数据库中，甚至[让 Okta 为您存储](https://developer.okta.com/blog/2018/01/23/replace-local-storage-with-okta-profile-attributes)！看看是否可以创建更多的路由器来添加到 web 服务器。

如果你想看最终的代码样本，可以在 GitHub 上找到[。](https://github.com/oktadeveloper/okta-node-express-router-example)

如果您想了解更多关于 Node 和 Express 的知识，请查看 Okta 开发者博客上的其他文章:

*   [通过实例构建并理解 Express 中间件](https://developer.okta.com/blog/2018/09/13/build-and-understand-express-middleware-through-examples)
*   [建立并理解一个简单的 Node.js 网站，并进行用户认证](https://developer.okta.com/blog/2018/08/17/build-and-understand-user-authentication-in-node)
*   [用 Node 和 OAuth 2.0 构建一个简单的 REST API](https://developer.okta.com/blog/2018/08/21/build-secure-rest-api-with-node)
*   [使用 Passport.js 和 OpenID Connect 建立安全节点认证](https://developer.okta.com/blog/2018/05/18/node-authentication-with-passport-and-oidc)
*   [使用 OAuth 2.0 客户端证书保护节点 API](https://developer.okta.com/blog/2018/06/06/node-api-oauth-client-credentials)

如果你对这篇文章有任何问题，请在下面添加评论。更多精彩内容，请在 Twitter 上关注 [@oktadev](https://twitter.com/oktadev) ，就像我们在脸书关注[，或者订阅](https://www.facebook.com/oktadevelopers/)[我们的 YouTube 频道](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q)。

## 分享这篇文章