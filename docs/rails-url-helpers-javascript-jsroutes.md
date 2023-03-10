# 使用 JsRoutes 的 Javascript 中的 Rails URL 助手

> 原文：<https://www.sitepoint.com/rails-url-helpers-javascript-jsroutes/>

![Screenshot 2015-01-15 12.39.58](img/087b9084110706c3e58899179c1348b4.png)

当我们在 Rails 中使用资源路由时(像“/blogs/new”或“/blogs/2/edit”这样的路径)，我们经常使用路径变量，比如`new_blogs_path`。但是，当使用 Javascript 时，我们必须硬编码路径，即“/blog/new”。在本文中，我们将介绍 JsRoutes，这是一个非常棒的工具，它允许我们在 Javascript 代码中使用一组类似的 URL 助手。

## 为什么我们需要 JsRoutes？

这个想法是，如果一个库可以为你做的话，你就不应该用 Javascript 手动构造 URL！一旦开始使用嵌套路由，这一点就变得很明显。例如，考虑以下路由配置:

```
resources :posts do
  resources :comments
end
```

如果我们想让路径编辑与 ID 为 1 的文章相关联的 ID 为 4 的评论，它给出了“/posts/1/comments/4/edit”，这很拗口。使用 Rails 提供的 URL 助手比在我们的 Javascript 中构造那个字符串要容易得多。

## 设置

如果您有在 Rails 中工作的资产管道，设置 JsRoutes 会非常容易。只需将以下内容添加到 gem 文件中:

```
gem "js-routes"
```

然后，运行`bundle install`。在任何 Javascript 文件中(通常在`application.js`中)，您可以通过在文件顶部添加以下内容来要求 JsRoutes:

```
//= require js-routes
```

如果在阅读本文的过程中，您发现 JsRoutes 没有加载到您的 Javascript 中，那么您可能必须清除资产管道缓存:

```
rake tmp:cache:clear
```

现在，您应该已经准备好 JsRoutes 了。让我们来看看一些基本知识。

## 基础知识

假设我们在`config/routes.rb`中添加了一条新的基于资源的路线:

```
resources :posts
```

每个帖子都有一个标题字段和一个内容字段。在`PostsController`中，我们可能会看到这样的内容:

```
class PostsController < ApplicationController
  ...
  def create
    @post = Post.new(:title => params[:title],
            :content => params[:content])
    @post.save
    respond_to do |format| do
        #probably should render something more sensible in a
        #real application.
        format.json { render :json => "{}"}
    end
  end
  ...
end
```

请注意，我们不会返回在将帖子保存为响应的一部分时发生的任何错误。这在实践中是一个糟糕的想法，但是我们不去管它，因为这个例子并不关心从服务器返回的数据。在 Javascript 中，假设我们有一个很好的表单来创建一篇新文章，我们想用 AJAX 提交这个表单。打开 jQuery:

```
$.post(path, {title: $("#title_field").val(), content: $("#content_field").val()}, function(response) {
    //do something w/ the response
  });
```

但是，我们如何获得`path`的值呢？这就是 JsRoutes 发挥作用的地方:

```
var path = Routes.posts_path({format: "json"});
```

如果您尝试`console.log`该变量的值，您会看到`"/paths.json"`，正如所料。记住控制器中哪些 JsRoutes 调用对应于哪些操作可能有点困难(尽管名称与相应的 Rails 帮助器几乎相同)。快速复习的一个方法是添加:

```
console.log(Routes)
```

添加到您的一个 Javascript 文件中，并检查输出。另一种方法是使用这张方便的桌子:

| 控制器#动作 | JsRoutes 函数调用 |
| :-: | --- |
| 帖子#索引 | Routes.posts_path() |
| 帖子#新 | Routes.new_post_path() |
| 帖子#创建 | Routes.posts_path() |
| 帖子#显示 | Routes.post_path(id) |
| 帖子#编辑 | Routes.edit_post_path(id) |
| 帖子#更新 | Routes.post_path(id) |
| 帖子#销毁 | Routes.post_path(id) |

注意，JsRoutes 由函数调用组成，在 Javascript 中，这意味着我们必须有调用括号(即“()”)，不管我们是否有“id”参数。还要注意，不同操作的一些路径是相同的(例如，posts#index 和 posts#create 的路径)。对于这些，基于使用的 HTTP 动词调用正确的动作(例如 GET 与 POST)。

## 嵌套路由

好了，让我们看看如何用 JsRoutes 处理嵌套路由。幸运的是，如果您有使用 Rails URL 帮助程序的经验，您会感觉如鱼得水。假设我们有这样的路由配置:

```
resources :posts do
  resources :comments
end
```

URL 帮助程序有点罗嗦，但想法是一样的:

| 控制器#动作 | JsRoutes 函数调用 |
| :-: | --- |
| 评论#索引 | routes . post _ comments _ path(post _ id) |
| 评论#新 | routes . new _ post _ comment _ path(post _ id) |
| 评论#创建 | routes . post _ comments _ path(post _ id) |
| 评论#显示 | routes . post _ comment _ path(post _ id，comment_id) |
| 评论#编辑 | routes . edit _ post _ comment _ path(post _ id，comment_id) |
| 评论#更新 | routes . update _ post _ comment _ path(post _ id) |
| 评论#销毁 | Routes.post_path(id) |

作为一个规则，如果你为一个特定的资源传递一个 ID，使用该资源的单数形式(例如“post”)，如果你不传递一个 ID，使用复数形式(例如“comments”)。

## “to_param”的魔力

如果您的应用程序中有“/post/138”这样的 URL，SEO 可能会有点困难，因为从 URL 中，不可能说出 URL 所引用的帖子的任何信息(如标题或内容)。这就是为什么 Rails 给了我们`to_param`。我们可以在我们的 Post 模型中看到它的作用:

```
class Post < ActiveRecord::Base
  #...
  #maybe verify uniqueness of title

  def to_param
    title.gsub(/ /, '_').downcase
  end

  #...
end
```

如果我们有一个标题为“JSRoutes 太棒了”的帖子，那么我们现在可以将这个帖子称为`/posts/jsroutes_is_awesome`，它在 SEO 方面提供了一点改进。JsRoutes 允许您使用“to_param”来生成路径。让我们来看一个平面资源的例子:

```
var path = Routes.post_path({to_param: "jsroutes_is_awesome"}, {format: "json"});
```

这就产生了路径`/posts/jsroutes_is_awesome.json`。这确实意味着，如果您在 Javascript 中有文章标题的副本，您将不得不在 Javascript 中重新实现`to_param`逻辑，这是不幸的，但或多或少是不可避免的。

## 配置 JsRoutes

有许多方法可以配置 JsRoutes，这对于特定的情况非常有用。你可以通过一个初始化器来管理配置，所以在**config/initializer/jsroutes . Rb**中创建一个新的。初始化器的一般格式是这样的:

```
JsRoutes.setup do |config|
  config.option = value
end
```

基本上，您可以设置一系列可能的配置选项。让我们来看看其中的一些。对我来说最有用的是:

```
JsRoutes.setup do |config|
  config.default_url_options = {:format => "json"}
end
```

这需要所有的 url 帮助程序，默认情况下，会加上一个“.”。json”结尾。如果您正在编写一个单页应用程序，其中大部分 HTTP 请求实际上都是由 Javascript 触发的，那么您几乎从不加载 HTML，因为您的大部分数据都存储在 JSON 中。在这种情况下，将默认格式设置为 JSON 更有意义。

另一个重要的 URL 配置选项是`:exclude`:

```
JsRoutes.setup do |config|
  config.default_url_options = {:exclude => [/^admin$/]}
end
```

这将从提供给 Javascript 代码的 Route 对象中排除任何与“admin”相关的 URL。这个想法是，你可能不希望你的项目中的所有 URL 都暴露给客户端(然而，在设计你的应用程序时，建议假设攻击者*有*的路径列表)。因此，使用`exclude`键，我们可以使用正则表达式省略某些路径。

假设您总是想要完整路径(例如“http://example.com/posts”)而不是本地路径(例如“/posts”)。这也很简单:

```
JsRoutes.setup do |config|
  config.prefix = "http://example.com"
end
```

请注意，没有尾随斜线。

## 包装它

在现代 Javascript 驱动的 web 中编写 Rails 代码需要大量的心理上下文切换。有了 JsRoutes，至少当您切换到代码库的前端部分时，不必重新考虑处理路径的方式。

## 分享这篇文章