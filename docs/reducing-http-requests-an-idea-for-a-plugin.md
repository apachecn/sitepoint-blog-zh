# 减少 HTTP 请求:一个插件的想法

> 原文：<https://www.sitepoint.com/reducing-http-requests-an-idea-for-a-plugin/>

Flickr 的 Cal Henderson 最近发布了 [Serving Javascript Fast](http://www.thinkvitamin.com/features/webapps/serving-javascript-fast) ，其中他谈到了他们在 Flickr 使用的方法，以减少每页 HTTP 请求的数量，并有效地传播对资产的新更改。

在 Rails 1.1 中，我们免费获得已更改资产的传播:静态资产(javascripts、样式表、图像)的所有 URL 都将它们最后修改的时间戳附加到它们的 URL 上。当资产更新时，URL 会更改，所有客户端都会下载最新版本。

例如，下面的代码:

`<%= stylesheet_link_tag 'common' %>`

将生成类似于以下的内容:

`<link href="/stylesheets/common.css?1148952578" media="screen" rel="Stylesheet" type="text/css" />`

虽然不如 Flickr 的方法有效，但它很简单，对大多数人都有效。

Cal 解决的另一个问题是减少 HTTP 请求的数量，这在 Rails 中并不是免费的。事实上，使用 [script.aculo.us](http://script.aculo.us/) 的 Rails 应用程序的默认设置有 4 个 javascript 文件。这四个文件不经常改变(只有当你升级 Rails 的时候)，它们经常都需要在一起*和*经常同时更新。听起来像是减少 HTTP 请求的好方法？如果当我们将我们的应用程序部署到生产服务器时，我们可以将它们连接在一起，并让我们的视图指向连接的版本，这一切都会自动发生，这不是很好吗？听起来像是一个很好的插件候选…让我们称之为…组合资产！

组合资产将由两部分组成:

1.  将生成连接文件的 rake 任务
2.  在生产服务器上输出不同资产 URL 的视图助手

在您的 environment.rb 文件中，您可以声明一些资产集:

```
 CombinedAssets.javascripts[:rails] = %w(prototype controls dragdrop effects)
CombinedAssets.stylesheets[:yui] = %w(fonts reset grids) 
```

那么在您看来，您可以简单地调用:

```
 <%= javascript_include_tag combined_javascripts(:rails) %>
<%= stylesheet_link_tag combined_stylesheets(:yui) %> 
```

在开发过程中，它的输出类似于下面这样:

```
 <script src="/javascripts/prototype.js?1147878861" type="text/javascript"></script>
<script src="/javascripts/controls.js?1147878861" type="text/javascript"></script>
<script src="/javascripts/dragdrop.js?1147878861" type="text/javascript"></script>
<script src="/javascripts/effects.js?1147878861" type="text/javascript"></script>
<link href="/stylesheets/fonts.css?1147878863" media="all" rel="Stylesheet" type="text/css" />
<link href="/stylesheets/reset.css?1147878863" media="all" rel="Stylesheet" type="text/css" />
<link href="/stylesheets/grids.css?1147878863" media="all" rel="Stylesheet" type="text/css" /> 
```

以及生产中的以下内容:

```
 <script src="/javascripts/prototype_controls_dragdrop_effects.js?1147878861" type="text/javascript"></script>
<link href="/stylesheets/fonts_reset_grids.css?1147878863" media="all" rel="Stylesheet" type="text/css" /> 
```

新访客的 HTTP 请求减少了 5 个。

当然，我们还需要一个真正将文件连接在一起的 rake 任务，当与 [Capistrano](http://rubyforge.org/projects/capistrano/) 结合使用时，您可以在部署期间在生产服务器上自动完成这一任务。

有了像 Ruby 一样简洁的语言和像 Rails 一样灵活的框架，一半的乐趣就来自于最优雅的解决方案。事实上，它通常很容易实现，这是一个额外的好处。

## 分享这篇文章