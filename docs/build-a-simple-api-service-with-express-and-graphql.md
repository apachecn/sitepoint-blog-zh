# 用 Express 和 GraphQL 构建一个简单的 API 服务

> 原文：<https://www.sitepoint.com/build-a-simple-api-service-with-express-and-graphql/>

*这篇文章最初发表在[Okta 开发者博客](https://developer.okta.com/blog/2018/09/27/build-a-simple-api-service-with-express-and-graphql)上。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

GraphQL 已经成为 REST APIs 非常受欢迎的替代品。使用 GraphQL 带来的灵活性让开发人员更容易获得应用程序所需的任何信息，而*只需要*应用程序这一部分所需的信息。这给你一种非常定制化的 API 的感觉，并有助于减少带宽。

在本教程中，我将向您展示如何使用 Node 和 Express 编写自定义的 GraphQL API。我还将向您展示如何保护 API 的一部分，同时让其他部分对公众开放。

## 使用 Express 创建 GraphQL API

要创建 API，首先创建一个新文件夹，并创建一个`package.json`文件来管理您的依赖项。您还需要安装一些依赖项来启动和运行 GraphQL with Express:

```
mkdir graphql-express
cd graphql-express
npm init -y
npm install express@2.8.4 express-graphql@0.6.12 graphql@14.0.2 graphql-tag@2.9.2 cors@2.8.4 
```

现在创建一个名为`index.js`的文件。这将是你的主要切入点:

```
const express = require('express')
const cors = require('cors')
const graphqlHTTP = require('express-graphql')
const gql = require('graphql-tag')
const { buildASTSchema } = require('graphql')

const app = express()
app.use(cors())

const schema = buildASTSchema(gql` type Query {
    hello: String
  } `)

const rootValue = {
  hello: () => 'Hello, world'
}

app.use('/graphql', graphqlHTTP({ schema, rootValue }))

const port = process.env.PORT || 4000
app.listen(port)
console.log(`Running a GraphQL API server at localhost:${port}/graphql`) 
```

这是 GraphQL 服务器所能做到的最简单的事情。这只是在查询“hello”时返回“Hello，world ”,但这只是一个开始。要进行测试，运行`node .`，然后在另一个选项卡中打开浏览器进入 [GraphQL 游乐场](https://graphqlbin.com)。在那里，输入`http://localhost:4000/graphql`来访问您的 GraphQL 服务器。

![graphql playground - enter endpoint url](img/561b4ebe9e017ac5e2d5141cc4f58f8f.png)

GraphQL Playground 将帮助探索您的模式并测试查询。它甚至会自动为您创建一些文档。

![graphql playground - hello world schema](img/526cf1ddee79e634780f6c96e804dc90.png)

尝试使用以下查询来查询`hello`:

```
query {
  hello
} 
```

![hello world](img/ef8df1f8aed33e2229cb42a06c5d9ad8.png)

## 改善您的 GraphQL 开发人员体验

这里有几个快速提示，可以帮助您改善开发体验:

**1。安装一个 linter 来帮助捕捉编辑器中的错误。这将有助于保持你的风格一致，并捕捉任何容易避免的错误。**

要安装 [StandardJS](https://standardjs.com/) ，输入`npm install --save-dev standard@12.0.1`。大多数编辑器能够在你输入的时候显示警告和错误。

您还可以编辑您的`package.json`的`scripts`对象，这样您就可以随时使用`npm test`运行 linter:

```
"scripts": {
  "test": "standard"
}, 
```

**2。**进行更改时自动重启服务器。

用`npm install --save-dev nodemon@1.18.4`安装`nodemon`。

给`package.json`添加另一个脚本，这样就可以用`npm start`运行服务器了。结合以上，你的`scripts`对象应该是这样的:

```
"scripts": {
  "test": "standard",
  "start": "nodemon ."
}, 
```

继续并关闭您用`node .`运行的服务器，现在键入`npm start`重启开发服务器。从现在开始，您所做的任何更改都将自动重新启动服务器。

## 创建 GraphQL 查询

为了获得更有用的东西，让我们制作一个帖子编辑器。GraphQL 是强类型的，允许您为每个对象创建一个类型并将它们连接起来。一个常见的场景可能是有一个由人写的带有一些文本的帖子。更新您的模式以包含这些类型。您也可以更新您的`Query`类型来利用这些新类型。

```
 type Query {
    posts: [Post]
    post(id: ID): Post
    authors: [Person]
    author(id: ID): Person
  }

  type Post {
    id: ID
    author: Person
    body: String
  }

  type Person {
    id: ID
    posts: [Post]
    firstName: String
    lastName: String
  } 
```

即使没有设置解析器，您也可以返回到 GraphQL Playground，通过单击`localhost` URL 旁边的圆形箭头图标来刷新模式。

![url and refresh button](img/b3a567f07fe2d231751dd54753dfdf70.png)

schema explorer 对于确定如何创建查询非常有用。单击绿色的`SCHEMA`按钮来检查您的新模式。

![full query schema](img/7c161090cede496447acabd4578a13f1.png)

你需要一些方法来存储数据。为了简单起见，使用 JavaScript 的 [`Map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) 对象进行内存存储。您还可以创建一些类来帮助将数据从一个对象连接到另一个对象。

```
const PEOPLE = new Map()
const POSTS = new Map()

class Post {
  constructor (data) { Object.assign(this, data) }
  get author () {
    return PEOPLE.get(this.authorId)
  }
}

class Person {
  constructor (data) { Object.assign(this, data) }
  get posts () {
    return [...POSTS.values()].filter(post => post.authorId === this.id)
  }
} 
```

现在，如果您有一个`Person`的实例，您可以通过简单地请求`person.posts`来找到他们的所有帖子。由于 GraphQL 只允许您请求您想要的数据，除非您请求，否则`posts` getter 永远不会被调用，如果这是一个昂贵的操作，这可以加快查询速度。

您还需要更新您的解析器(`rootValue`中的函数)以适应这些新类型。

```
const rootValue = {
  posts: () => POSTS.values(),
  post: ({ id }) => POSTS.get(id),
  authors: () => PEOPLE.values(),
  author: ({ id }) => PEOPLE.get(id)
} 
```

这很好，但是还没有数据。现在，插入一些假数据。您可以在赋值给`rootValue`之后添加这个函数和对它的调用。

```
const initializeData = () => {
  const fakePeople = [
    { id: '1', firstName: 'John', lastName: 'Doe' },
    { id: '2', firstName: 'Jane', lastName: 'Doe' }
  ]

  fakePeople.forEach(person => PEOPLE.set(person.id, new Person(person)))

  const fakePosts = [
    { id: '1', authorId: '1', body: 'Hello world' },
    { id: '2', authorId: '2', body: 'Hi, planet!' }
  ]

  fakePosts.forEach(post => POSTS.set(post.id, new Post(post)))
}

initializeData() 
```

既然您已经设置好了所有的查询，并插入了一些数据，那么就回到 GraphQL Playground，玩一会儿吧。尝试获取所有帖子，或者获取与每个帖子相关的所有作者和帖子。

![query all posts](img/c2ab38f3f0dbae2571bd2b809b7f99bb.png)

或者变得奇怪，通过 id 获得一个帖子，然后是该帖子的作者，以及该作者的所有帖子(包括您刚才查询的那篇)。

![get weird](img/14f91bdf3ebc355b912c4d32f6e8e5ec.png)

## 向您的 Express + GraphQL API 添加用户验证

向项目添加身份验证的一个简单方法是使用 Okta。Okta 是一种云服务，允许开发人员创建、编辑和安全存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。如果你还没有，那么[注册一个永远免费的开发者账户](https://developer.okta.com/signup/)。

你需要保存一些信息，以便在应用程序中使用。创建一个名为`.env`的新文件。在其中，输入您的组织 URL。

```
HOST_URL=http://localhost:4000
OKTA_ORG_URL=https://{yourOktaOrgUrl} 
```

您还需要一个随机字符串作为会话的应用程序密码。您可以使用以下命令生成此消息:

```
echo "APP_SECRET=`openssl rand -base64 32`" >> .env 
```

接下来，登录到您的开发者控制台，导航到**应用**，然后点击**添加应用**。选择**网页**，然后点击**下一步**。

![create new application settings](img/fa16d724f7d47088e287a4e325127716.png)

创建应用程序后，您进入的页面有更多信息需要保存到您的`.env`文件中。复制客户端 ID 和客户端密码。

```
OKTA_CLIENT_ID={yourClientId}
OKTA_CLIENT_SECRET={yourClientSecret} 
```

您需要从 Okta 获得的最后一条信息是一个 API 令牌。在您的开发者控制台中，导航到**API**->-**令牌**，然后点击**创建令牌**。您可以有许多令牌，所以只需给这个令牌起一个提醒您它的用途的名称，比如“GraphQL Express”。你会得到一个你现在只能看到的令牌。如果您丢失了令牌，您必须重新创建一个。也将此添加到`.env`中。

```
OKTA_TOKEN={yourOktaAPIToken} 
```

创建一个名为`okta.js`的新文件。在这里，您将创建一些实用函数，并为 Okta 初始化应用程序。当通过 Okta 进行身份验证时，您的应用程序将使用 JWT 通过访问令牌进行身份验证。您可以使用它来确定用户是谁。为了避免在你的应用程序中直接处理认证，用户可以登录 Okta 的服务器，然后给你发送一个你可以验证的 JWT。

**okta.js**

```
const session = require('express-session')

const OktaJwtVerifier = require('@okta/jwt-verifier')
const verifier = new OktaJwtVerifier({
  clientId: process.env.OKTA_CLIENT_ID,
  issuer: `${process.env.OKTA_ORG_URL}/oauth2/default`
})

const { Client } = require('@okta/okta-sdk-nodejs')
const client = new Client({
  orgUrl: process.env.OKTA_ORG_URL,
  token: process.env.OKTA_TOKEN
})

const { ExpressOIDC } = require('@okta/oidc-middleware')
const oidc = new ExpressOIDC({
  issuer: `${process.env.OKTA_ORG_URL}/oauth2/default`,
  client_id: process.env.OKTA_CLIENT_ID,
  client_secret: process.env.OKTA_CLIENT_SECRET,
  redirect_uri: `${process.env.HOST_URL}/authorization-code/callback`,
  scope: 'openid profile'
})

const initializeApp = (app) => {
  app.use(session({
    secret: process.env.APP_SECRET,
    resave: true,
    saveUninitialized: false
  }))
  app.use(oidc.router)
  app.use('/access-token', oidc.ensureAuthenticated(), async (req, res, next) => {
    res.send(req.userContext.tokens.access_token)
  })
}

module.exports = { client, verifier, initializeApp } 
```

`initializeApp`函数增加了一些中间件，让你可以用 Okta 登录。每当您进入`http://localhost:4000/access-token`时，它会首先检查您是否登录。如果你不是，它会首先将你发送到 Okta 的服务器进行认证。认证成功后，它会将您返回到`/access-token`路线，并打印出您当前的访问令牌，其有效期约为一小时。

您正在导出的`client`允许您在服务器上运行一些管理调用。稍后，您将使用它来根据用户的 ID 获取有关用户的更多信息。

`verifier`是用来验证 JWT 是否有效的，它提供了用户的一些基本信息，比如用户 id 和电子邮件地址。

现在，在`index.js`中，您需要导入这个文件并调用`initializeApp`函数。你还需要使用一个叫做`dotenv`的工具，它会读取你的`.env`文件并将变量添加到`process.env`。在文件的最顶部，添加以下行:

```
require('dotenv').config({ path: '.env' }) 
```

就在`app.use(cors())`行之后，添加以下内容:

```
const okta = require('./okta')
okta.initializeApp(app) 
```

要使这一切工作，您还需要安装一些新的依赖项:

```
npm i @okta/jwt-verifier@0.0.12 @okta/oidc-middleware@1.0.0 @okta/oidc-sdk-nodejs@1.2.0 dotenv@6.0.0 express-session@1.15.6 
```

您现在应该能够转到`http://localhost:4000/access-token`登录并获得一个访问令牌。如果您只是在您的开发人员控制台，您可能会发现您已经登录。您可以注销开发人员控制台，以确保流程正常工作。

## 创建 GraphQL 突变

现在该用真实数据了。可能有一些真正的约翰和简在那里，但机会是他们还没有你的申请帐户。接下来，我将向您展示如何添加一些突变，这些突变将使用您的当前用户来创建、编辑或删除帖子。

要为一篇文章生成 id，可以使用`uuid`。用`npm install uuid@3.3.2`安装，然后用以下命令将其添加到`index.js`:

```
const uuid = require('uuid/v4') 
```

这应该放在文件的顶部，紧挨着其他的`require`语句。

仍然在`index.js`中，将以下类型添加到您的模式中:

```
 type Mutation {
    submitPost(input: PostInput!): Post
    deletePost(id: ID!): Boolean
  }

  input PostInput {
    id: ID
    body: String!
  } 
```

要验证用户并将其保存为新用户，您需要两个新的实用函数。在`const rootValue`之前加上这些:

```
const getUserId = async ({ authorization }) => {
  try {
    const accessToken = authorization.trim().split(' ')[1]
    const { claims: { uid } } = await okta.verifier.verifyAccessToken(accessToken)

    return uid
  } catch (error) {
    return null
  }
}

const saveUser = async (id) => {
  try {
    if (!PEOPLE.has(id)) {
      const { profile: { firstName, lastName } } = await okta.client.getUser(id)

      PEOPLE.set(id, new Person({ id, firstName, lastName }))
    }
  } catch (ignore) { }

  return PEOPLE.get(id)
} 
```

`getUserId`函数将检查`authorization`请求报头是否有有效的令牌。如果成功，它将返回用户的 ID。

`saveUser`函数检查用户是否已经被保存。如果是，它就返回缓存的值。否则，它将获取用户的名字和姓氏，并将其存储在`PEOPLE`对象中。

现在将以下解析器添加到`rootValue`:

```
 submitPost: async ({ input }, { headers }) => {
    const authorId = await getUserId(headers)
    if (!authorId) return null

    const { id = uuid(), body } = input

    if (POSTS.has(id) && POSTS.get(id).authorId !== authorId) return null
    await saveUser(authorId)

    POSTS.set(id, new Post({ id, authorId, body }))

    return POSTS.get(id)
  },
  deletePost: async ({ id }, { headers }) => {
    if (!POSTS.has(id)) return false

    const userId = await getUserId(headers)
    if (POSTS.get(id).authorId !== userId) return false

    POSTS.delete(id)

    if (PEOPLE.get(userId).posts.length === 0) {
      PEOPLE.delete(userId)
    }

    return true
  } 
```

`submitPost`变异首先检查用户 ID，如果没有用户，则返回`null`。这意味着除非您通过验证，否则不会执行任何操作。然后，它从用户的输入中获取`id`和`body`。如果没有`id`，它会生成一个新的。如果已经有一篇文章具有所提供的 ID，它会检查该文章是否由试图编辑它的用户所拥有。如果不是，它再次返回`null`。

一旦`submitPost`确定用户能够添加或编辑这篇文章，它就调用`saveUser`。如果用户已经存在，那么`saveUser`函数不会做任何事情，但是如果用户不存在，它会添加用户。接下来，`submitPost`将文章添加到`POSTS`对象中，并返回值，以防客户端想要查询添加的文章(例如，获取 ID)。

只有当你是帖子的创建者时，`deletePost`变异才会让你删除帖子。成功删除帖子后，它会检查用户是否还有其他帖子。如果这是他们唯一的帖子，`deletePost`还会将该用户从数据集中删除，以清理一些(相当少量的)内存。

既然有能力添加真实数据，也可以去掉`initializeData`函数。

## 测试新的 GraphQL 突变

尝试对新的突变进行调用，并创建一个帖子。因为你没有被认证，你应该得到`null`作为响应。

![cannot create post](img/56d7f7547c7612e4b427e102c2b72092.png)

通常，某种类型的应用程序，无论是 web 应用程序还是本地应用程序，都会处理用户界面进行身份验证，然后无缝地将`Authorization`头传递给 API。在这种情况下，因为我们只关注 API，所以我让您实现一个端点来手动获取 auth 令牌。

前往[http://localhost:4000/access-token](http://localhost:4000/access-token)登录 Okta 并获得访问令牌。复制访问令牌，然后返回 GraphQL 操场。在页面底部，有一个写着`HTTP HEADERS`的链接。当您点击它时，将会打开一个区域，允许您添加一些作为 JSON 的头。添加以下内容，确保将`Bearer`添加到令牌的前面，这样它看起来应该类似于`Bearer eyJraWQ...xHUOjj_A`(尽管真实的令牌会长得多):

```
{
  "authorization": "Bearer {yourAccessToken}"
} 
```

现在您应该已经通过了身份验证，相同的帖子将返回一个有效的帖子:

![authenticated - can create post](img/1a035e05c853c4b105d173e2f2d59c36.png)

如果你想和其他用户混在一起，你可以通过导航到**用户**->-**人**，然后点击**添加人**，从开发者控制台添加人。然后，您可以从一个匿名窗口或者在退出开发人员控制台后访问`/access-token`端点。

![add a person](img/6dc1aa8ce299bfb3066c2f404e6e5acf.png)

## 了解有关 GraphQL、Express 和 Okta 的更多信息

试着玩一会儿 API，看看你能用它做些什么有趣的事情。我想您很快就会看到是什么让 GraphQL 比传统的 REST API 强大得多，以及即使您只是在玩游戏，使用它也是多么有趣。看看能不能找到可以连接的数据点，或者从外部来源获取数据。由于解析器只是简单的`async`函数，您可以很容易地从外部 API 或数据库获取数据。你的想象力是极限。

如果你想看最终的样本代码，可以在 github 上找到[。](https://github.com/oktadeveloper/okta-express-graphql-example)

如果您想了解更多关于 GraphQL 或 Express 的知识，请查看 Okta 开发者博客上的其他一些帖子:

*   [用 Spring Boot 和 GraphQL 构建一个安全的 API](https://developer.okta.com/blog/2018/08/16/secure-api-spring-boot-graphql)
*   [使用 React、GraphQL 和用户认证构建健康跟踪应用](https://developer.okta.com/blog/2018/07/11/build-react-graphql-api-user-authentication)
*   [通过实例构建并理解 Express 中间件](https://developer.okta.com/blog/2018/09/13/build-and-understand-express-middleware-through-examples)
*   [建立并理解一个简单的 Node.js 网站，并进行用户认证](https://developer.okta.com/blog/2018/08/17/build-and-understand-user-authentication-in-node)
*   [教程:用 Node.js 构建一个基本的 CRUD App](https://developer.okta.com/blog/2018/06/28/tutorial-build-a-basic-crud-app-with-node)

如果你对这篇文章有任何问题，请在下面添加评论。更多精彩内容，在 Twitter 上关注 [@oktadev](https://twitter.com/oktadev) ，在脸书上关注我们[，订阅](https://www.facebook.com/oktadevelopers/)[我们的 YouTube 频道](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q)。

## 分享这篇文章