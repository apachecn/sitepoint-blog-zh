# 构建 React 通用博客应用程序:实现 Flux

> 原文：<https://www.sitepoint.com/building-a-react-universal-blog-app-implementing-flux/>

**在[这部迷你剧](https://www.sitepoint.com/building-a-react-universal-blog-app-a-step-by-step-guide)的第一部分中，我们开始深入 React 的世界，看看我们如何使用它，以及 Node.js，来构建一个 React 通用博客应用。**

在第二部分也是最后一部分，我们将通过学习如何添加和编辑内容，把我们的博客提升到一个新的高度。我们还将讨论如何使用 React 组织概念和**通量模式**轻松扩展 React 通用博客应用程序。

![Building a React Universal Blog App: Implementing Flux](img/6edb8adebace05f4d34328c1b6c403a7.png)

## 给我分解一下

随着我们向我们的博客添加更多的页面和内容，我们的`routes.js`文件将很快变大。因为 React 的指导原则之一是将事情分解成更小、更易管理的部分，所以让我们将我们的路线分成不同的文件。

打开您的`routes.js`文件并编辑它，使它具有以下代码:

```
// routes.js
import React from 'react'
import { Route, IndexRoute } from 'react-router'

// Store
import AppStore from './stores/AppStore'

// Main component
import App from './components/App'

// Pages
import Blog from './components/Pages/Blog'
import Default from './components/Pages/Default'
import Work from './components/Pages/Work'
import NoMatch from './components/Pages/NoMatch'

export default (
  <Route path="/" data={AppStore.data} component={App}>  <IndexRoute component={Blog}/>  <Route path="about" component={Default}/>  <Route path="contact" component={Default}/>  <Route path="work" component={Work}/>  <Route path="/work/:slug" component={Work}/>  <Route path="/blog/:slug" component={Blog}/>  <Route path="*" component={NoMatch}/>  </Route>
) 
```

我们在我们的博客中添加了几个不同的页面，并通过将页面分成独立的组件，显著减小了我们的`routes.js`文件的大小。此外，请注意，我们通过包含`AppStore`添加了一个商店，这对于扩展 React 应用程序的后续步骤非常重要。

## 商店:真理的唯一来源

在流动模式中，存储是一个非常重要的部分，因为它充当数据管理的*单一真实来源*。这是理解 React 开发如何工作的一个关键概念，也是 React 最受吹捧的好处之一。这门学科的美妙之处在于，在我们应用程序的任何给定状态下，我们都可以访问`AppStore`的数据，并确切地知道其中发生了什么。如果您想要构建一个数据驱动的 React 应用程序，需要记住几个关键事项:

1.  我们从不直接操纵 DOM。
2.  我们的 UI 回答数据和数据生活在商店里
3.  如果我们需要改变我们的用户界面，我们可以去商店，商店将创建我们的应用程序的新数据状态。
4.  新数据被提供给更高级别的组件，然后根据接收到的新数据，通过`props`组成新的 UI，向下传递给更低级别的组件。

有了这四点，我们基本上就有了单向数据流 T2 应用程序的基础。这也意味着，在我们应用程序的任何状态下，我们都可以`console.log(AppStore.data)`，如果我们正确地构建了我们的应用程序，我们将确切地知道我们可以期望看到什么。您将体验到这对于调试来说是多么强大。

现在让我们创建一个名为`stores`的存储文件夹。在其中，创建一个名为`AppStore.js`的文件，内容如下:

```
// AppStore.js
import { EventEmitter } from 'events'
import _ from 'lodash'

export default _.extend({}, EventEmitter.prototype, {

  // Initial data
  data: {
    ready: false,
    globals: {},
    pages: [],
    item_num: 5
  },

  // Emit change event
  emitChange: function(){
    this.emit('change')
  },

  // Add change listener
  addChangeListener: function(callback){
    this.on('change', callback)
  },

  // Remove change listener
  removeChangeListener: function(callback) {
    this.removeListener('change', callback)
  }

}) 
```

您可以看到我们已经附加了一个事件发射器。这允许我们编辑存储中的数据，然后使用`AppStore.emitChange()`重新呈现我们的应用程序。这是一个强大的工具，应该只在应用程序的某些地方使用。否则，很难理解`AppStore`数据在哪里被修改，这就引出了下一点…

## 反应组分:较高和较低水平

丹·阿布拉莫夫写了一篇关于智能和非智能组件概念的伟大文章。其思想是将数据更改操作保留在高级(智能)组件中，而低级(非智能)组件通过 props 获得数据，并基于该数据呈现 UI。每当在较低级别的组件上执行一个动作时，该事件就会通过 props 传递到较高级别的组件，以便被处理成一个动作。然后，它通过应用程序重新分发数据(单向数据流)。

说到这里，让我们开始构建一些组件。为此，创建一个名为`components`的文件夹。在其中，创建一个名为`App.js`的文件，内容如下:

```
// App.js
import React, { Component } from 'react'

// Dispatcher
import AppDispatcher from '../dispatcher/AppDispatcher'

// Store
import AppStore from '../stores/AppStore'

// Components
import Nav from './Partials/Nav'
import Footer from './Partials/Footer'
import Loading from './Partials/Loading'

export default class App extends Component {

  // Add change listeners to stores
  componentDidMount(){
    AppStore.addChangeListener(this._onChange.bind(this))
  }

  // Remove change listeners from stores
  componentWillUnmount(){
    AppStore.removeChangeListener(this._onChange.bind(this))
  }

  _onChange(){
    this.setState(AppStore)
  }

  getStore(){
    AppDispatcher.dispatch({
      action: 'get-app-store'
    })
  }

  render(){

    const data = AppStore.data

    // Show loading for browser
    if(!data.ready){

      document.body.className = ''
      this.getStore()

      let style = {
        marginTop: 120
      }
      return (
        <div className="container text-center" style={ style }>  <Loading />  </div>
      )
    }

    // Server first
    const Routes = React.cloneElement(this.props.children, { data: data })

    return (
      <div>  <Nav data={ data }/>  { Routes }  <Footer data={ data }/>  </div>
    )
  }
} 
```

在我们的`App.js`组件中，我们已经为我们的`AppStore`附加了一个事件监听器，当`AppStore`发出一个`onChange`事件时，它将重新呈现状态。这个重新呈现的数据将作为道具传递给子组件。还要注意，我们添加了一个`getStore`方法，该方法将分派`get-app-store`动作在客户端呈现我们的数据。一旦从 Cosmic JS API 获取了数据，它将触发一个`AppStore`更改，包括将`AppStore.data.ready`设置为`true`，移除加载符号并呈现我们的内容。

## 页面组件

要创建我们博客的第一页，创建一个`Pages`文件夹。在其中，我们将使用以下代码创建一个名为`Blog.js`的文件:

```
// Blog.js
import React, { Component } from 'react'
import _ from 'lodash'
import config from '../../config'

// Components
import Header from '../Partials/Header'
import BlogList from '../Partials/BlogList'
import BlogSingle from '../Partials/BlogSingle'

// Dispatcher
import AppDispatcher from '../../dispatcher/AppDispatcher'

export default class Blog extends Component {

  componentWillMount(){
    this.getPageData()
  }

  componentDidMount(){
    const data = this.props.data
    document.title = config.site.title + ' | ' + data.page.title
  }

  getPageData(){
    AppDispatcher.dispatch({
      action: 'get-page-data',
      page_slug: 'blog',
      post_slug: this.props.params.slug
    })
  }

  getMoreArticles(){
    AppDispatcher.dispatch({
      action: 'get-more-items'
    })
  }

  render(){

    const data = this.props.data
    const globals = data.globals
    const pages = data.pages
    let main_content

    if(!this.props.params.slug){

      main_content = &lt;BlogList getMoreArticles={ this.getMoreArticles } data={ data }/&gt;

    } else {

      const articles = data.articles

      // Get current page slug
      const slug = this.props.params.slug
      const articles_object = _.keyBy(articles, 'slug')
      const article = articles_object[slug]
      main_content = &lt;BlogSingle article={ article } /&gt;

    }

    return (
      <div>  <Header data={ data }/>  <div id="main-content" className="container">  <div className="row">  <div className="col-lg-8 col-lg-offset-2 col-md-10 col-md-offset-1">  { main_content }  </div>  </div>  </div>  </div>
    )
  }
} 
```

这个页面将作为我们博客列表页面(主页)和单个博客页面的模板。这里，我们向组件添加了一个方法，该方法将在组件挂载之前使用 React lifecycle `componentWillMount`方法获取页面数据。然后，一旦组件在`componentDidMount()`装载，我们将把页面标题添加到文档的`<title>`标签中。

除了这个高级组件中的一些呈现逻辑，我们还包括了`getMoreArticles`方法。这是一个很好的例子，一个行动号召存储在一个较高级别的组件中，并通过 props 提供给较低级别的组件。

现在让我们进入我们的`BlogList`组件，看看这是如何工作的。

创建一个名为`Partials`的新文件夹。然后，在其中创建一个名为`BlogList.js`的文件，内容如下:

```
// BlogList.js
import React, { Component } from 'react'
import _ from 'lodash'
import { Link } from 'react-router'

export default class BlogList extends Component {

  scrollTop(){
    $('html, body').animate({
      scrollTop: $("#main-content").offset().top
    }, 500)
  }

  render(){

    let data = this.props.data
    let item_num = data.item_num
    let articles = data.articles

    let load_more
    let show_more_text = 'Show More Articles'

    if(data.loading){
      show_more_text = 'Loading...'
    }

    if(articles && item_num <= articles.length){
      load_more = (
        <div>  <button className="btn btn-default center-block" onClick={ this.props.getMoreArticles.bind(this) }>  { show_more_text }  </button>  </div>
      )
    }

    articles = _.take(articles, item_num)

    let articles_html = articles.map(( article ) => {
      let date_obj = new Date(article.created)
      let created = (date_obj.getMonth()+1) + '/' + date_obj.getDate() + '/' + date_obj.getFullYear()
      return (
        <div key={ 'key-' + article.slug }>  <div className="post-preview">  <h2 className="post-title pointer">  <Link to={ '/blog/' + article.slug } onClick={ this.scrollTop }>{ article.title }</Link>  </h2>  <p className="post-meta">Posted by <a href="https://cosmicjs.com" target="_blank">Cosmic JS</a> on { created }</p>  </div>  <hr/>  </div>
      )
    })

    return (
      <div>  <div>{ articles_html }</div>  { load_more }  </div>
    )
  }
} 
```

在我们的`BlogList`组件中，我们已经为我们的`Show More Articles`按钮添加了一个`onClick`事件。后者执行作为道具从更高级别的页面组件传下来的`getMoreArticles`方法。当这个按钮被点击时，事件冒泡到`Blog`组件，然后在`AppDispatcher`上触发一个动作。`AppDispatcher`充当我们的高层组件和我们的`AppStore`之间的中间人。

为了简洁起见，我们不打算在本教程中构建所有的`Page`和`Partial`组件，所以请[下载 GitHub repo](https://github.com/sitepoint-editors/react-universal-blog/archive/master.zip) 并从`components`文件夹中添加它们。

## AppDispatcher

`AppDispatcher`是我们的应用程序中的操作符，它接受来自高层组件的信息，并将动作分配给存储，然后存储重新呈现我们的应用程序数据。

要继续本教程，请创建一个名为`dispatcher`的文件夹。在其中，创建一个名为`AppDispatcher.js`的文件，包含以下代码:

```
// AppDispatcher.js
import { Dispatcher } from 'flux'
import { getStore, getPageData, getMoreItems } from '../actions/actions'

const AppDispatcher = new Dispatcher()

// Register callback with AppDispatcher
AppDispatcher.register((payload) => {

  let action = payload.action

  switch(action) {

    case 'get-app-store':
      getStore()
      break

    case 'get-page-data':
      getPageData(payload.page_slug, payload.post_slug)
      break

    case 'get-more-items':
      getMoreItems()
      break

    default:
      return true

  }

  return true

})

export default AppDispatcher 
```

我们在这个文件中引入了`Flux`模块来构建我们的调度程序。让我们现在加入我们的行动。

## 行动:商店前的最后一站

首先，让我们在新创建的名为`actions`的文件夹中创建一个`actions.js`文件。该文件将包含以下内容:

```
// actions.js
import config from '../config'
import Cosmic from 'cosmicjs'
import _ from 'lodash'

// AppStore
import AppStore from '../stores/AppStore'

export function getStore(callback){

  let pages = {}

  Cosmic.getObjects(config, function(err, response){

    let objects = response.objects

    /* Globals
    ======================== */
    let globals = AppStore.data.globals
    globals.text = response.object['text']
    let metafields = globals.text.metafields
    let menu_title = _.find(metafields, { key: 'menu-title' })
    globals.text.menu_title = menu_title.value

    let footer_text = _.find(metafields, { key: 'footer-text' })
    globals.text.footer_text = footer_text.value

    let site_title = _.find(metafields, { key: 'site-title' })
    globals.text.site_title = site_title.value

    // Social
    globals.social = response.object['social']
    metafields = globals.social.metafields
    let twitter = _.find(metafields, { key: 'twitter' })
    globals.social.twitter = twitter.value
    let facebook = _.find(metafields, { key: 'facebook' })
    globals.social.facebook = facebook.value
    let github = _.find(metafields, { key: 'github' })
    globals.social.github = github.value

    // Nav
    const nav_items = response.object['nav'].metafields
    globals.nav_items = nav_items

    AppStore.data.globals = globals

    /* Pages
    ======================== */
    let pages = objects.type.page
    AppStore.data.pages = pages

    /* Articles
    ======================== */
    let articles = objects.type['post']
    articles = _.sortBy(articles, 'order')
    AppStore.data.articles = articles

    /* Work Items
    ======================== */
    let work_items = objects.type['work']
    work_items = _.sortBy(work_items, 'order')
    AppStore.data.work_items = work_items

    // Emit change
    AppStore.data.ready = true
    AppStore.emitChange()

    // Trigger callback (from server)
    if(callback){
      callback(false, AppStore)
    }

  })
}

export function getPageData(page_slug, post_slug){

  if(!page_slug || page_slug === 'blog')
    page_slug = 'home'

  // Get page info
  const data = AppStore.data
  const pages = data.pages
  const page = _.find(pages, { slug: page_slug })
  const metafields = page.metafields
  if(metafields){
    const hero = _.find(metafields, { key: 'hero' })
    page.hero = config.bucket.media_url + '/' + hero.value

    const headline = _.find(metafields, { key: 'headline' })
    page.headline = headline.value

    const subheadline = _.find(metafields, { key: 'subheadline' })
    page.subheadline = subheadline.value
  }

  if(post_slug){
    if(page_slug === 'home'){
      const articles = data.articles
      const article = _.find(articles, { slug: post_slug })
      page.title = article.title
    }
    if(page_slug === 'work'){
      const work_items = data.work_items
      const work_item = _.find(work_items, { slug: post_slug })
      page.title = work_item.title
    }
  }
  AppStore.data.page = page
  AppStore.emitChange()
}

export function getMoreItems(){

  AppStore.data.loading = true
  AppStore.emitChange()

  setTimeout(function(){
    let item_num = AppStore.data.item_num
    let more_item_num = item_num + 5
    AppStore.data.item_num = more_item_num
    AppStore.data.loading = false
    AppStore.emitChange()
  }, 300)
} 
```

这里有一些方法是由这个`actions.js`文件公开的。`getStore()`连接到 Cosmic JS API，为我们的博客内容提供服务。`getPageData()`从提供的`slug`(或 page key)中获取页面数据。`getMoreItems()`控制在我们的`BlogList`和`WorkList`组件中可以看到多少项目。

当`getMoreItems()`被触发时，它首先将`AppStore.data.loading`设置为`true`。然后，300 毫秒后(为了效果)，它允许将另外五个条目添加到我们的博客帖子或工作条目列表中。最后，它将`AppStore.data.loading`设置为`false`。

## 配置你的宇宙 JS CMS

为了开始在 [Cosmic JS](https://cosmicjs.com) 上从云托管的内容 API 接收数据，让我们创建一个`config.js`文件。打开此文件并粘贴以下内容:

```
// config.js
export default {
  site: {
    title: 'React Universal Blog'
  },
  bucket: {
    slug: process.env.COSMIC_BUCKET || 'react-universal-blog',
    media_url: 'https://cosmicjs.com/uploads',
    read_key: process.env.COSMIC_READ_KEY || '',
    write_key: process.env.COSMIC_WRITE_KEY || ''
  },
} 
```

这意味着内容将来自宇宙 JS 桶`react-universal-blog`。要为自己的博客或应用程序创建内容，[注册一个免费的宇宙 JS](https://cosmicjs.com) 账户。当被要求“添加一个新桶”时，点击“安装启动桶”，你就可以按照步骤安装“React 通用博客”了。一旦完成，您就可以将您独特的 bucket 的 slug 添加到这个配置文件中。

## 服务器端渲染

现在我们已经设置了大部分 React 组件和 Flux 架构，让我们通过编辑我们的`app-server.js`文件来渲染服务器端产品中的所有内容。该文件将包含以下代码:

```
// app-server.js
import React from 'react'
import { match, RoutingContext, Route, IndexRoute } from 'react-router'
import ReactDOMServer from 'react-dom/server'
import express from 'express'
import hogan from 'hogan-express'
import config from './config'

// Actions
import { getStore, getPageData } from './actions/actions'

// Routes
import routes from './routes'

// Express
const app = express()
app.engine('html', hogan)
app.set('views', __dirname + '/views')
app.use('/', express.static(__dirname + '/public/'))
app.set('port', (process.env.PORT || 3000))

app.get('*',(req, res) => {

  getStore(function(err, AppStore){

    if(err){
      return res.status(500).end('error')
    }

    match({ routes, location: req.url }, (error, redirectLocation, renderProps) => {

      // Get page data for template
      const slug_arr = req.url.split('/')
      let page_slug = slug_arr[1]
      let post_slug
      if(page_slug === 'blog' || page_slug === 'work')
        post_slug = slug_arr[2]
      getPageData(page_slug, post_slug)
      const page = AppStore.data.page
      res.locals.page = page
      res.locals.site = config.site

      // Get React markup
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
})

app.listen(app.get('port'))

console.info('==> Server is listening in ' + process.env.NODE_ENV + ' mode')
console.info('==> Go to http://localhost:%s', app.get('port')) 
```

这个文件使用我们的`getStore`动作方法从 Cosmic JS API 服务器端获取我们的内容，然后通过 React Router 来确定将安装哪个组件。然后用`renderToStaticMarkup`将一切都呈现到静态标记中。这个输出然后被存储在一个模板变量中，供我们的`views/index.html`文件使用。

让我们再一次更新我们的`package.json`文件的`scripts`部分，使它看起来如下所示:

```
"scripts": {
    "start": "npm run production",
    "production": "rm -rf public/index.html && NODE_ENV=production webpack -p && NODE_ENV=production babel-node app-server.js --presets es2015",
    "webpack-dev-server": "NODE_ENV=development PORT=8080 webpack-dev-server --content-base public/ --hot --inline --devtool inline-source-map --history-api-fallback",
    "development": "cp views/index.html public/index.html && NODE_ENV=development webpack && npm run webpack-dev-server"
  }, 
```

我们现在可以在开发模式下运行热重载，也可以在生产模式下运行服务器呈现的标记。运行以下命令，在生产模式下运行完整的 React Universal Blog 应用程序:

```
npm start 
```

我们的博客现在可以在 [http://localhost:3000](http://localhost:3000) 查看了。它可以在服务器端和浏览器端观看，我们的内容可以通过我们的云托管内容平台[宇宙 JS](https://cosmicjs.com) 进行管理。

## 结论

React 是一种非常复杂的管理应用程序中 UI 和数据的方法。它也是渲染服务器端内容的一个非常好的选择，以满足 JavaScript 堕落的网络爬虫，并渲染 UI 浏览器端以保持我们快速浏览。通过使我们的应用程序通用化，我们可以获得两个世界的最佳结果。

我真的希望你喜欢这篇文章。同样，[完整代码可以从 GitHub](https://github.com/sitepoint-editors/react-universal-blog/) 下载。

## 分享这篇文章