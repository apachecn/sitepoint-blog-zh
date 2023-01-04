# Rails 中的无限滚动:基础

> 原文：<https://www.sitepoint.com/infinite-scrolling-rails-basics/>

![no_load_more](img/e1ad3d158dbc710048b595badf4f8765.png)

分页是一种非常常见和广泛使用的导航技术，这是有道理的。首先考虑性能。在单个查询中加载所有可用记录的成本可能非常高。此外，用户可能仅对几个最近的记录(即，博客中的最新帖子)感兴趣，而不想等待所有记录都被加载和呈现。此外，分页不会让内容淹没页面，从而使阅读页面变得更加容易。

如今，许多网站使用一种略有不同的技术，叫做*无限滚动*(或*无限页面*)。基本上，当用户向下滚动页面时，使用 AJAX 异步加载更多的记录。以这种方式，滚动看起来更自然，并且对于用户来说比不断点击“下一页”链接更容易。

在本文中，我将解释如何实现无限滚动来代替经典分页。

首先，我们将准备我们的演示项目，使用`will_paginate` gem 实现基本分页。在我们学习教程的过程中，这种分页将变成无限滚动。这将需要编写一些 JavaScript(和 CoffeeScript)代码，以及我们的 Ruby。

如果用户在浏览器中禁用了 javascript，所提供的解决方案将退回到默认分页。最后，我们的视图几乎不需要修改，因此您可以在任何网站上轻松实现。

将涵盖的其他项目:

*   如何实现一个“加载更多”按钮，而不是无限滚动，很像在[网站](https://www.sitepoint.com)上使用的
    按钮。
*   一些陷阱和潜在的问题，特别是，历史 API 和滚动间谍可以帮助我们。

工作演示可以在[http://sitepoint-infinite-scrolling.herokuapp.com](http://sitepoint-infinite-scrolling.herokuapp.com)找到。

源代码可以在 [GitHub](https://github.com/bodrovis/SitePoint-InfiniteScroll) 上找到。

听起来不错吧？让我们开始吧！

## 准备项目

在这篇文章中，我将使用 Rails 3.2.16，但是你可以用 Rails 4 实现同样的解决方案。

让我们创建一个非常简单的博客。目前，它只会显示演示文章。

```
$ rails new infinite_scrolling -T
```

这里意味着我们想要跳过生成测试套件(我更喜欢 RSpec，但是当然你可以省略这个标志)。

我们会找到一些有用的宝石:

*Gemfile*

```
gem 'will_paginate', '~> 3.0.5'
gem 'betterlorem', '~> 0.1.2'
gem 'bootstrap-sass', '~> 3.0.3.0'
gem 'bootstrap-will_paginate', '~> 0.0.10'
```

会把我们的记录分页。我将在下一节更详细地介绍这个宝石。`betterlorem`在我们的记录中生成演示文本。还有其他类似的 gem 可以生成“Lorem Ipsum”文本，但是我发现这个对我们的情况来说是最方便的(我们将在`seeds.rb`中使用它，而不是在视图中)。

创建一个获奖的设计是没有意义的，所以作为一个快速简单的(尽管不是最小的，考虑到重量)解决方案，我们将使用 Twitter Bootstrap 3。gem 将它添加到我们的 Rails 项目中。`bootstrap-will_paginate`包含一些分页本身的引导样式。

不要忘记跑步

```
$ bundle install
```

现在添加

```
//= require bootstrap
```

`application.js`并且

```
@import "bootstrap";
```

添加到`application.css.scss`以包含所有的引导样式和脚本。当然，在实际应用中，您可以只选择所需的组件。

## 模型

将只有一个表:Post。它非常简单，包含以下
列:

*   `id`(整数，主键)
*   `title`(字符串)
*   `body`(正文)
*   `created_at` (datetime)
*   `updated_at` (datetime)

运转

```
$ rails g model Post title:string body:text
$ rake db:migrate
```

将创建适当的迁移，然后将其应用于数据库。

下一步是产生一些测试数据。最简单的方法是使用`seeds.rb`。

*seeds.rb*

```
50.times { |i| Post.create(title: "Post #{i}", body: BetterLorem.p(5, false, false)) }
```

这创建了 50 个帖子，由`BetterLorem`生成`body`。每组生成的内容由 5 个`p`段落组成。最后两个参数告诉`BetterLorem`将文本放在一个`p`标签中，并包含一个尾随句点。

运转

```
$ rake db:seed
```

将用一些测试帖子填充我们的数据库。厉害！

最后一件事是用`index`和`show`方法创建一个`PostsController`，以及相应的视图(`index.html.erb`和`show.html.erb`)。另外，不要忘记设置路线:

*routes.rb*

```
resources :posts, only: [:index, :show]
root to: 'posts#index'
```

最后，如果你使用的是 Rails 3，一定要删除`public/index.html`文件。

## 控制器

我们准备好进入有趣的部分了。首先，让我们显示一些带有截断正文的分页帖子。为此，我们将使用 [will_paginate](https://github.com/mislav/will_paginate) ，这是一个简单而方便的 gem，由 Mislav Marohni 开发，可以与 Ruby on Rails、Sinatra、Merb、DataMapper 和 Sequel 一起使用。

这种解决方案有一个替代方案——阿基拉·松田隼的《神成》,它更强大、更复杂。也可以试一试。基本上用哪种宝石都无所谓。

在我们的控制器中:

*posts_controller.rb*

```
@posts = Post.paginate(page: params[:page], per_page: 15).order('created_at DESC')
```

对`paginate`方法的调用接受一个`page`选项，告诉它使用哪个 GET 参数来获取所需的页码。`per_page`选项指定每页应该显示多少条记录。可以为整个模型或整个项目指定`per_page`选项，如下所示:

*post.rb*

```
class Post
  self.per_page = 10
end
```

*will_paginate.rb* (在初始化式中)

```
WillPaginate.per_page = 10
```

`paginate`方法返回一个`ActiveRecord::Relation`,这样我们就可以链接对`order`方法的调用。

## 景色

*index.html.erb*

```
<div class="page-header">
  <h1>My posts</h1>
</div>

<div id="my-posts">
  <%= render @posts %>
</div>

<div id="infinite-scrolling">
  <%= will_paginate %>
</div>
```

在引导类的帮助下指定一个页头。下一个块`#my-posts`，包含我们分页的文章。使用`render @posts`使用`_post.html.erb`部分显示数组中的每个帖子。最后一个块`#infinite-scrolling`，包含分页控件。

注意`will_paginate`足够聪明，能够理解我们想要分页`@posts`。你可以这样明确地指定它:`will_paginate @posts`。

这是我们的部分帖子。

*_post.html.erb*

```
<div>
  <h2><%= link_to post.title, post_path(post) %></h2>

  <small><em><%= post.timestamp %></em></small>

  <p><%= truncate(strip_tags(post.body), length: 600) %></p>
</div>
```

我们用一个`div`来包装每篇文章，然后显示标题，作为阅读整篇文章的链接。时间戳表示帖子的创建时间。这个`timestamp`方法在模型中是这样定义的:

`post.rb`

```
def timestamp
  created_at.strftime('%d %B %Y %H:%M:%S')
end
```

最后，我们使用`strip_tags`方法删除帖子中的所有标签，使用`truncate`去除除前 600 个符号以外的所有标签。这结束了我们对视图的工作(我省略了对`layout.html.erb`和`show.html.erb`的标记，因为这并不重要；你可以在 [GitHub 回购](https://github.com/bodrovis/SitePoint-InfiniteScroll)里查看。

## 无限滚动

我们准备将分页转换为无限滚动。jQuery 将帮助我们完成这项任务。

在`javascripts`目录下创建一个新文件`pagination.js.coffee`。

*pagination.js.coffee*

```
jQuery ->
  if $('#infinite-scrolling').size() > 0
    $(window).on 'scroll', ->
      more_posts_url = $('.pagination .next_page a').attr('href')
        if more_posts_url && $(window).scrollTop() > $(document).height() - $(window).height() - 60
            $('.pagination').html('<img src="/assets/ajax-loader.gif" alt="Loading..." title="Loading..." />')
            $.getScript more_posts_url
        return
      return
```

这里我们将一个`scroll`事件绑定到窗口，只有当分页出现在页面上时。当用户滚动时，获取下一个页面的链接——访问它将使 Rails 从该页面加载记录(我们仍然需要对控制器进行一些修改以使其工作)。

然后，检查 URL 是否存在，用户是否滚动到页面底部减去`60px`。这是我们想要加载更多帖子的时候。`60px`的值是任意的，您可能希望根据自己的情况更改它。

如果这些条件成立，我们将用“加载”GIF 图像替换我们的分页，GIF 图像可以在 [ajaxload.info](http://www.ajaxload.info) 免费下载。最后要做的事情是使用我们之前获取的 URL 实际执行一个异步请求。`$.getScript`会从服务器加载一个 JS 脚本，然后执行。

注意两条`return`指令。默认情况下，CoffeeScript 将返回最后一个表达式(同样的概念也适用于 Ruby)，但这里我们不希望 jQuery 函数或事件处理程序返回任何内容，因此指定`return`意味着“不返回任何内容”。

`PostController#index`方法应该同时响应 HTML 和 JavaScript。我们将使用`respond_to`来实现这一点:

*posts_controller.rb*

```
@posts = Post.paginate(page: params[:page], per_page: 15).order('created_at DESC')
respond_to do |format|
  format.html
  format.js
end
```

最后要做的事情是创建一个在用 JS 响应时将被呈现的视图:

*index.js.erb*

```
$('#my-posts').append('<%= j render @posts %>');
<% if @posts.next_page %>
  $('.pagination').replaceWith('<%= j will_paginate @posts %>');
<% else %>
  $(window).off('scroll');
  $('.pagination').remove();
<% end %>
```

我们通过将帖子添加到`#my-posts`块来渲染更多的帖子。之后，检查是否还有剩余的页面。如果是这样，用新的分页替换当前的分页块(此时包含“加载”图像)。否则，我们移除分页控件，并从`window`中解除`scroll`事件的绑定，因为监听它已经没有意义了。

至此，我们的无限滚动就准备好了。即使用户在浏览器中禁用了 JavaScript，也会呈现给用户默认的分页，由于使用了`bootstrap-will_paginate` gem，该分页应用了一些样式。

值得一提的是滚动会触发大量的`scroll`事件。如果你想延迟这个事件的处理，你可以使用 Brian Grinstead 编写的开源库 [BindWithDelay](http://briangrinstead.com/files/bindWithDelay) 。要使用它，只需下载并将其包含在项目中。然后，对脚本进行以下修改:

*pagination.js.coffee*

```
$(window).bindWithDelay 'scroll', ->
  # the code
, 100
```

这将延迟 100 毫秒触发事件。`index.js.erb`中的`$(window).off('scroll');`仍然会解除事件绑定，所以不需要做任何修改。

文章第一部分到此结束。在下一部分，我们将讨论“加载更多”按钮以及使用无限滚动时出现的一些问题。感谢您的阅读，再见！

## 分享这篇文章