# 构建 React 通用博客应用程序:分步指南

> 原文：<https://www.sitepoint.com/building-a-react-universal-blog-app-a-step-by-step-guide/>

当单页面应用程序(SPAs)这个话题出现时，我们往往会想到浏览器、JavaScript、速度以及对搜索引擎的不可见性。这是因为 SPA 使用 JavaScript 呈现页面的内容，并且由于网络爬虫不使用浏览器来查看网页，所以它们不能查看和索引内容——或者至少大部分不能。

这是一些开发人员试图用各种方法解决的问题:

1.  添加一个网站的转义片段版本，要求所有页面都以静态形式可用，增加了很多额外的工作([现已弃用](https://developers.google.com/webmasters/ajax-crawling/docs/specification?hl=en))。
2.  使用付费服务取消浏览将 SPA 转换为静态标记，供搜索引擎蜘蛛抓取。
3.  相信搜索引擎现在已经足够先进，可以阅读我们的纯 JavaScript 内容。(我现在还不会。)

在服务器端使用 Node.js，在客户端使用 React，我们可以将我们的 JavaScript 应用程序构建成**通用**(或**同构**)。这可以从服务器端和浏览器端呈现提供几个好处，允许搜索引擎和使用浏览器的人查看我们的 SPA 内容。

在这个循序渐进的教程中，我将向您展示如何构建一个 React Universal Blog 应用程序，该应用程序将首先在服务器端呈现标记，以使我们的内容可供搜索引擎使用。然后，它将让浏览器接管一个既快速又响应迅速的单页应用程序。

![Building a React Universal Blog App](img/0305660521f615e2d956559367fe41ed.png)

## 入门指南

我们的通用博客应用程序将利用以下技术和工具:

1.  [Node.js](https://nodejs.org/) 用于包管理和服务器端渲染
2.  [对 UI 视图做出反应](http://facebook.github.io/react/)
3.  [Express](http://expressjs.com/) 一个简单的后端 JS 服务器框架
4.  [反应路由器](https://github.com/rackt/react-router)进行路由
5.  [反应热加载器](https://github.com/gaearon/react-hot-loader)用于开发中的热加载
6.  数据流的[通量](https://facebook.github.io/flux/)
7.  用于内容管理的 [Cosmic JS](https://cosmicjs.com)

首先，运行以下命令:

```
mkdir react-universal-blog
cd react-universal-blog 
```

现在创建一个`package.json`文件并添加以下内容:

```
{
  "name": "react-universal-blog",
  "version": "1.0.0",
  "engines": {
    "node": "4.1.2",
    "npm": "3.5.2"
  },
  "description": "",
  "main": "app-server.js",
  "dependencies": {
    "babel-cli": "^6.26.0",
    "babel-loader": "^7.1.2",
    "babel-preset-es2015": "^6.24.1",
    "babel-preset-es2017": "^6.24.1",
    "babel-preset-react": "^6.24.1",
    "babel-register": "^6.26.0",
    "cosmicjs": "^2.4.0",
    "flux": "^3.1.3",
    "history": "1.13.0",
    "hogan-express": "^0.5.2",
    "html-webpack-plugin": "^2.30.1",
    "path": "^0.12.7",
    "react": "^15.6.1",
    "react-dom": "^15.6.1",
    "react-router": "1.0.1",
    "webpack": "^3.5.6",
    "webpack-dev-server": "^2.7.1"
  },
  "scripts": {
    "webpack-dev-server": "NODE_ENV=development PORT=8080 webpack-dev-server --content-base public/ --hot --inline --devtool inline-source-map --history-api-fallback",
    "development": "cp views/index.html public/index.html && NODE_ENV=development webpack && npm run webpack-dev-server"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "react-hot-loader": "^1.3.0"
  }
} 
```

在这个文件中，您会注意到我们添加了以下内容:

1.  将我们的 CommonJS 模块打包，将我们的 ES6 和 React JSX 转换成浏览器兼容的 JavaScript
2.  Cosmic JS 官方 Node.js 客户端从 Cosmic JS 云托管的内容 API 轻松提供我们的博客内容
3.  Flux for app 数据管理(这是我们 React 应用程序中非常重要的元素)。
4.  对服务器和浏览器上的 UI 管理做出反应
5.  为服务器和浏览器上的路由反应路由器
6.  [webpack](https://github.com/webpack/webpack) 用于将所有内容汇集到一个`bundle.js`文件中。

我们还在我们的`package.json`文件中添加了一个脚本。当我们运行`npm run development`时，脚本将`index.html`文件从我们的`views`文件夹复制到我们的`public`文件夹。然后，它将我们的`webpack-dev-server`的内容库设置为`public/`，并启用热重新加载(在`.js`文件保存时)。最后，它帮助我们在源代码中调试我们的组件，并为我们提供一个无法找到的页面的后备(退回到`index.html`)。

现在让我们通过编辑文件`webpack.config.js`来设置我们的 webpack 配置文件:

```
// webpack.config.js
var webpack = require('webpack')

module.exports = {
  devtool: 'eval',
  entry: './app-client.js',
  output: {
    path: __dirname + '/public/dist',
    filename: 'bundle.js',
    publicPath: '/dist/'
  },
  module: {
    loaders: [
      { test: /\.js$/, loaders: 'babel-loader', exclude: /node_modules/ },
      { test: /\.jsx$/, loaders: 'babel-loader', exclude: /node_modules/ }
    ]
  },
  plugins: [
    new webpack.DefinePlugin({
      'process.env.COSMIC_BUCKET': JSON.stringify(process.env.COSMIC_BUCKET),
      'process.env.COSMIC_READ_KEY': JSON.stringify(process.env.COSMIC_READ_KEY),
      'process.env.COSMIC_WRITE_KEY': JSON.stringify(process.env.COSMIC_WRITE_KEY)
    })
 ]
}; 
```

您会注意到我们添加了一个值为`app-client.js`的`entry`属性。这个文件作为我们的应用程序客户端入口点，这意味着从这一点开始，webpack 将捆绑我们的应用程序，并将其输出到`/public/dist/bundle.js`(在`output`属性中指定)。我们还使用加载器让 Babel 在我们的 ES6 和 JSX 代码上施展魔法。React 热加载器用于热加载(无页面刷新！)发展中。

在我们进入 React 相关的内容之前，让我们准备好我们博客的外观和感觉。因为我希望你在本教程中更多地关注功能而不是风格，所以这里我们将使用一个预先构建的前端主题。我从 [Start Bootstrap](http://startbootstrap.com/) 中选了一个叫做 [Clean Blog](http://startbootstrap.com/template-overviews/clean-blog/) 的。在您的终端中运行以下命令:

创建一个名为`views`的文件夹，并在其中放入一个`index.html`文件。打开 HTML 文件并添加以下代码:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="description" content="">
  <meta name="author" content="">
  <title>{{ site.title }}{{# page }} | {{ page.title }}{{/ page }}</title>
  <!-- Bootstrap Core CSS -->
  <link href="/css/bootstrap.min.css" rel="stylesheet">
  <!-- Custom CSS -->
  <link href="/css/clean-blog.min.css" rel="stylesheet">
  <link href="/css/cosmic-custom.css" rel="stylesheet">
  <!-- Custom Fonts -->
  <link href="//maxcdn.bootstrapcdn.com/font-awesome/4.1.0/css/font-awesome.min.css" rel="stylesheet" type="text/css">
  <link href="//fonts.googleapis.com/css?family=Lora:400,700,400italic,700italic" rel="stylesheet" type="text/css">
  <link href="//fonts.googleapis.com/css?family=Open+Sans:300italic,400italic,600italic,700italic,800italic,400,300,600,700,800" rel="stylesheet" type="text/css">
  <!-- HTML5 Shim and Respond.js IE8 support of HTML5 elements and media queries -->
  <!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
  <!--[if lt IE 9]>
    <script src="https://oss.maxcdn.com/libs/html5shiv/3.7.0/html5shiv.js"></script>
    <script src="https://oss.maxcdn.com/libs/respond.js/1.4.2/respond.min.js"></script>
  <![endif]-->
</head>
<body class="hidden">
  <div id="app">{{{ reactMarkup }}}</div>
  <script src="/js/jquery.min.js"></script>
  <script src="/js/bootstrap.min.js"></script>
  <script src="/js/clean-blog.min.js"></script>
  <script src="/dist/bundle.js"></script>
</body>
</html> 
```

要获取`public`中包含的所有 JS 和 CSS 文件，可以从 [GitHub 库](https://github.com/sitepoint-editors/react-universal-blog)中获取。[点击这里下载文件](https://github.com/sitepoint-editors/react-universal-blog/archive/master.zip)。

一般来说，我会使用神奇的 [React Bootstrap](https://react-bootstrap.github.io/) 包，避免使用 jQuery。然而，为了简洁起见，我们将保留主题的预构建 jQuery 功能。

在我们的`index.html`文件中，我们将 React 挂载点设置在`div`的`id="app"`处。模板变量`{{{ reactMarkup }}}`将被转换成我们的服务器呈现的标记，然后一旦浏览器启动，我们的 React 应用程序将接管并使用`id="app"`挂载到`div`。当我们的 JavaScript 加载所有内容时，为了改善用户体验，我们在主体中添加了`class="hidden"`。然后，一旦安装了 React，我们就删除这个类。这听起来可能有点复杂，但是我马上会向您展示我们是如何做到这一点的。

此时，您的应用程序应该具有以下结构:

```
package.json
public
  |-css
    |-bootstrap.min.css
    |-cosmic-custom.css
  |-js
    |-jquery.min.js
    |-bootstrap.min.js
    |-clean-blog.min.js
views
  |-index.html
webpack.config.js 
```

现在我们已经完成了静态部分，让我们开始构建一些 React 组件。

## 我们的博客应用组件(基本示例)

让我们通过为我们的博客设置页面来开始为我们的应用程序构建 UI。因为这将是一个创意专业人士的组合博客，我们的博客将有以下页面:

1.  主页
2.  关于
3.  工作
4.  接触

让我们首先创建一个名为`app-client.js`的文件，并向其中添加以下内容:

```
// app-client.js
import React from 'react'
import { render } from 'react-dom'
import { Router } from 'react-router'
import createBrowserHistory from 'history/lib/createBrowserHistory'
const history = createBrowserHistory()

// Routes
import routes from './routes'

const Routes = (
  <Router history={history}>  { routes }  </Router>
)

const app = document.getElementById('app')
render(Routes, app) 
```

为了更好地理解 React 路由器是如何工作的，你可以访问[他们的 GitHub repo](https://github.com/rackt/react-router) 。这里的要点是，我们在`app-client.js`中有一个`Router`组件，它有一个客户端路由的浏览器历史。我们的服务器呈现的标记不需要浏览器历史，所以我们将创建一个单独的`routes.js`文件，在服务器和客户机入口点之间共享。

将以下内容添加到`routes.js`文件中:

```
// routes.js
import React, { Component } from 'react'
import { Route, IndexRoute, Link } from 'react-router'

// Main component
class App extends Component {
  componentDidMount(){
    document.body.className=''
  }
  render(){
    return (
      <div>  <h1>React Universal Blog</h1>  <nav>  <ul>  <li><Link to="/">Home</Link></li>  <li><Link to="/about">About</Link></li>  <li><Link to="/work">Work</Link></li>  <li><Link to="/contact">Contact</Link></li>  </ul>  </nav>  { this.props.children }  </div>
    )
  }
}

// Pages
class Home extends Component {
  render(){
    return (
      <div>  <h2>Home</h2>  <div>Some home page content</div>  </div>
    )
  }
}
class About extends Component {
  render(){
    return (
      <div>  <h2>About</h2>  <div>Some about page content</div>  </div>
    )
  }
}
class Work extends Component {
  render(){
    return (
      <div>  <h2>Work</h2>  <div>Some work page content</div>  </div>
    )
  }
}
class Contact extends Component {
  render(){
    return (
      <div>  <h2>Contact</h2>  <div>Some contact page content</div>  </div>
    )
  }
}
class NoMatch extends Component {
  render(){
    return (
      <div>  <h2>NoMatch</h2>  <div>404 error</div>  </div>
    )
  }
}

export default (
  <Route path="/" component={App}>  <IndexRoute component={Home}/>  <Route path="about" component={About}/>  <Route path="work" component={Work}/>  <Route path="contact" component={Contact}/>  <Route path="*" component={NoMatch}/>  </Route>
) 
```

从这里开始，我们有了一个包含几个不同页面的博客应用程序的非常基本的工作示例。现在，让我们运行我们的应用程序并检查它！在您的终端中，运行以下命令:

```
mkdir public
npm install
npm run development 
```

然后在浏览器中导航到 [http://localhost:8080](http://localhost:8080) 来查看您的基本博客的运行情况。

这些事情都完成了，现在是时候让它在服务器上运行了。创建一个名为`app-server.js`的文件，并添加以下内容:

```
// app-server.js
import React from 'react'
import { match, RoutingContext } from 'react-router'
import ReactDOMServer from 'react-dom/server'
import express from 'express'
import hogan from 'hogan-express'

// Routes
import routes from './routes'

// Express
const app = express()
app.engine('html', hogan)
app.set('views', __dirname + '/views')
app.use('/', express.static(__dirname + '/public/'))
app.set('port', (process.env.PORT || 3000))

app.get('*',(req, res) => {

  match({ routes, location: req.url }, (error, redirectLocation, renderProps) => {

    const reactMarkup = ReactDOMServer.renderToStaticMarkup(<RoutingContext {...renderProps} />)

    res.locals.reactMarkup = reactMarkup

    if (error) {
      res.status(500).send(error.message)
    } else if (redirectLocation) {
      res.redirect(302, redirectLocation.pathname + redirectLocation.search)
    } else if (renderProps) {

      // Success!
      res.status(200).render('index.html')

    } else {
      res.status(404).render('index.html')
    }
  })
})

app.listen(app.get('port'))

console.info('==> Server is listening in ' + process.env.NODE_ENV + ' mode')
console.info('==> Go to http://localhost:%s', app.get('port')) 
```

在`app-server.js`中，我们正在加载已经设置好的基本路线。这些是将呈现的标记转换成字符串，然后将其作为变量传递给我们的模板。

我们准备启动我们的服务器并在上面查看我们的代码，但是首先，让我们创建一个脚本来完成这项工作。

打开您的`package.json`文件并编辑`script`部分，如下所示:

```
// …
"scripts": {
    "start": "npm run production",
    "production": "rm -rf public/index.html && NODE_ENV=production webpack -p && NODE_ENV=production babel-node app-server.js --presets es2015",
    "webpack-dev-server": "NODE_ENV=development PORT=8080 webpack-dev-server --content-base public/ --hot --inline --devtool inline-source-map --history-api-fallback",
    "development": "cp views/index.html public/index.html && NODE_ENV=development webpack && npm run webpack-dev-server"
  },
// … 
```

现在我们已经设置好了`production`脚本，我们可以在服务器端和客户端运行我们的代码。在您的终端中执行:

```
npm start 
```

在浏览器中导航到 [http://localhost:3000](http://localhost:3000) 。您应该可以看到简单的博客内容，并且能够在 SPA 模式下快速轻松地浏览页面。

继续点击`view source`。请注意，我们的 SPA 代码也可供所有机器人查找。我们两全其美！

## 结论

在第一部分中，我们已经开始深入 React 的世界，看看我们如何使用它和 Node.js 来构建 React 通用博客应用程序。

如果你想让你的博客更上一层楼，知道如何添加和编辑内容，别忘了阅读第二部分，“构建 React 通用博客应用:实现 Flux ”。我们将进入如何使用 React 组织概念和**通量模式**轻松扩展 React 通用博客应用的实质内容。

*我们与开源软件合作，为您带来来自 React 开发者的 **6 个专业技巧。更多开源内容，请查看[开源软件](http://bit.ly/opensourcecraft)。***

[https://www.youtube.com/embed/xa-_FIy2NgE?ecver=2](https://www.youtube.com/embed/xa-_FIy2NgE?ecver=2)

## 分享这篇文章