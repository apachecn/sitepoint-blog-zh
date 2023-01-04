# 将 Jade 模板集成到 Rails 中以获得更简洁的模板

> 原文：<https://www.sitepoint.com/integrating-jade-templates-into-rails-for-cleaner-templates/>

![jade](img/7c38b967329595e2636c07e0f1166a3f.png)

在 web 应用程序中呈现动态内容可以使用许多工具，其中之一是使用一个 [Javascript 模板引擎](https://developer.mozilla.org/en-US/docs/JavaScript_templates)。它使用一种带有特定标记的语言，用于向[文档对象模型](https://www.w3.org/TR/WD-DOM/introduction.html)呈现动态内容。

javascript 模板引擎使用的大多数标记都类似于 HTML，但有些更进一步，引入了一种新的标记，这种标记是为了特定的目标而设计的。与其他 javascript 模板语言相比，Jade 有一个独特的特点。它通过取消使用尖括号和结束标记来简化标记。

在本文中，我将展示如何将 Jade 集成到一个 [Ruby on Rails](http://rubyonrails.org/) 应用程序中。作为一个例子，该应用程序将是类似于 *twitter 的*，包括时间线和用户资料功能，以展示集成。

本文中的一些代码示例来自示例应用程序，一些是来自代码示例的简化代码。完整的版本可以在 [Github](https://github.com/hendrauzia/sitepoint-jade-on-rails) 获得，也有一个[在线](http://sitepoint-jade-on-rails.herokuapp.com/)版本可以演示它看起来如何。

## 玉石入门

我先给你介绍一下玉石的基础知识。读完这一节，你应该能够理解玉的基本构造。然而，这并没有试图复制 Jade [文档](http://jade-lang.com/reference/)。相反，我将突出显示 Jade 中最常用的特性，以便您可以快速启动并运行。

从你知道的开始总是好的。下面是 [ERB(内嵌红宝石)](https://en.wikipedia.org/wiki/ERuby)、Jade 和生成的 HTML 的比较。以下代码摘自我的 tweet 模板实现的完整版本。快速浏览一下下面的代码，您应该可以看到 Jade 与 ERB 的不同之处:

**ERB**

```
<li class="tweet">
  <%= link_to tweet.user do %>
    <img src="<%= tweet.user.thumbnail %>" class="tweet__photo" />
  <% end %>
  <div class="tweet__body">
    <div class="tweet__info">
      <%= link_to tweet.user.name, tweet.user, class: "tweet__name" %>
      <%= link_to "@#{tweet.user.username}", tweet.user, class: "tweet__username" %>
      <span class="tweet__date timeago" title="<%= tweet.created_at.iso8601 %>"></span>
    </div>
    <div class="tweet__text"><%= tweet.text %></div>
  </div>
</li> 
```

**玉**

```
li.tweet
  a(href=tweet.user.links.self)
    img.tweet__photo(src=tweet.user.thumbnail)
  .tweet__body
    .tweet__info
      a.tweet__name(href=tweet.user.links.self)=tweet.user.name
      a.tweet__username(href=tweet.user.links.self) @#{tweet.user.username}
      span.tweet__date.timeago(title=tweet.created_at)
    .tweet__text=tweet.text 
```

**生成的 HTML**

```
<li class="tweet">
  <a href="/@johnappleseed">
    <img class="tweet__photo" src="/path/to/image.jpg">
  </a>
  <div class="tweet__body">
    <div class="tweet__info">
      <a class="tweet__name" href="/@johnappleseed">John Appleseed</a>
      <a class="tweet__username" href="/@johnappleseed">@johnappleseed</a>
      <span class="tweet__date timeago" title="2015-11-30t01:30:10z"></span>
    </div>
    <div class="tweet__text">The quick brown fox jumps over the lazy dog</div>
  </div>
</li> 
```

看一下上面的代码就可以看出杰德和 ERB 之间的明显区别。翡翠看起来比 ERB 简单干净。这是因为翡翠中没有结束标签和尖括号。

### 标签名称、ID、类别和属性

从前面的模板示例中，您可以看到 Jade 使用标记名、ID 和 CSS 文字来创建 DOM 结构。属性类似于 HTML 中的属性，但是它们的值只是普通的 javascript。因为`div` s 是最常见的标签选择，如果您跳过标签名称，它就是默认标签。

以下代码:

```
li.tweet#tweet(title="tweet")
  .tweet__body 
```

将渲染到:

```
<li id="tweet" class="tweet" title="tweet">
  <div class="tweet__body"></div>
</li> 
```

### 缓冲和插值

在模板内部，你可以进行各种 javascript 操作，比如缓冲要写入模板的代码和插入字符串。考虑下面的例子。让我们假设您用下面的结构传递了`tweet`对象:

```
var tweet = {
  text: "The quick brown fox jumps over the lazy dog",
  user: {
    name: "John Appleseed",
    username: "johnappleseed",
    thumbnail: "/path/to/image.jpg",
    links: {
      self: "/@johnappleseed"
    }
  }
}; 
```

您可以生成指向用户的链接，如下所示:

```
a.tweet__name(href=tweet.user.links.self)=tweet.user.name 
```

以上将生成以下 HTML:

```
<a class="tweet__name" href="/@johnappleseed">John Appleseed</a> 
```

如果你想插入一个字符串，你可以使用熟悉的`#{}`操作符:

```
a.tweet__username(href=tweet.user.links.self) @#{tweet.user.username} 
```

它将呈现下面的 HTML。

```
<a class="tweet__username" href="/@johnappleseed">@johnappleseed</a> 
```

## 整合

将 Jade 集成到 Rails 需要几个步骤。安装步骤非常简单，但是在撰写本文时，最新版本的 Rails 存在兼容性问题。此问题有一个解决方法，有关详细信息，请参阅安装说明。

### 装置

要在 Rails 应用程序中安装 Jade，请将以下 gem 放到您的**gem 文件**中:

```
# Gemfile
gem 'ejs'
gem 'jade-rails', '1.9.2' 
```

并要求 Jade 运行时进入你的 **application.js** :

```
# app/views/assets/javascripts/application.js
//= require jade/runtime 
```

如前所述，在撰写本文时，Rails 版本 4.2.5 存在一个模板编译问题。请用我的 github 分支替换`jade-rails`宝石:

```
# Gemfile
gem 'jade-rails', github: 'hendrauzia/jade-rails', branch: 'rails-4-2-5' 
```

顺便说一下，在撰写本文时，上述源代码还没有被合并到`jade-rails` gem 中，但在将来，这可能会得到纠正。请咨询 jade-rails [发布版](https://github.com/mahipal/jade-rails/releases)，查看是否已经应用了修复。

### 模板

模板必须放在应用程序中的特定位置。你需要把它们放在**应用/资产/JavaScript/模板**中，并给它们一个`.jst.jade`扩展名。如果需要的话，您还可以向您的模板添加另一个预处理程序，比如:`.erb`。

请参见以下示例:

```
# app/assets/javascripts/templates/tweet.jst.jade
li.tweet
  ... 
```

如果你需要在模板中放置图片资源，这里有一个小技巧:将 ERB 添加到你的模板预处理器中，并在模板名的末尾添加扩展名`.erb`。这样，可以使用适当的摘要来呈现资产。例如，当用户没有图片时，您可能想要提供默认图像:

```
# app/assets/javascripts/templates/tweet.jst.jade.erb
li.tweet
  a(href=tweet.user.links.self)
    if tweet.user.thumbnail
      img.tweet__photo(src=tweet.user.thumbnail)
    else
      <%= image_tag "users/default.jpg", class: "tweet__photo" %> 
```

不要忘记在你的 **application.js** 中需要你的模板:

```
# app/assets/javascripts/application.js
//= require templates/tweet 
```

其余的模板特性可以在 Jade [文档](http://jade-lang.com/reference/)中找到。但是在你决定做任何有趣的事情之前，请阅读我的介绍的剩余部分。

### 翻译

Jade 中的模板渲染功能由`ejs` gem 提供。要呈现模板，使用以下语句并将其放入 javascript 文件中。使用我们之前的例子，如果我们想要呈现模板，我们需要一个`tweet`对象传递给模板呈现函数:

```
var tweet = {
  text: "The quick brown fox jumps over the lazy dog",
  user: {
    name: "John Appleseed",
    username: "johnappleseed",
    thumbnail: "/path/to/image.jpg",
    links: {
      self: "/@johnappleseed"
    }
  }
};

var htmlString = JST['templates/tweet']({ tweet: tweet }); 
```

上述代码将生成以下 HTML:

```
<li class="tweet">
  <a href="/@johnappleseed">
    <img src="/path/to/image.jpg" class="tweet__photo">
  </a>
  <div class="tweet__body">
    <div class="tweet__info">
      <a class="tweet__name" href="/@johnappleseed">john appleseed</a>
      <a class="tweet__username" href="/@johnappleseed">@johnappleseed</a>
      <span class="tweet__date timeago" title="2015-11-30t01:30:10z"></span>
    </div>
    <div class="tweet__text">the quick brown fox jumps over the lazy dog</div>
  </div>
</li> 
```

如果需要进一步定制，比如将事件侦听器附加到 DOM，或者在附加到容器之前对 DOM 进行处理，可以从 DOM 创建一个 jQuery 对象并对其进行预处理。

例如，这里我们尝试将 [timeago](https://github.com/rmm5t/jquery-timeago) 附加到 tweet 日期:

```
var htmlDOM = $(JST['templates/tweet']({ tweet: tweet }));
htmlDOM.find(".timeago").timeago(); 
```

一旦一切设置完成，您就可以将呈现的 HTML 附加到任何您想要的容器中。在这种情况下，假设我们想在时间轴的末端放置:

```
$('.timeline').append(htmlDOM); 
```

### 限制

在 Rails 中集成 Jade 并不是没有限制的。jade 中有一些特性是客户端中没有的。比如[`includes`](http://jade-lang.com/reference/includes/)[`extends`](http://jade-lang.com/reference/extends/)目前稳定的玉轨宝石都没有。作为一种解决方法，您可以使用 javascript 手动将生成的 HTML 附加到其他生成的 HTML。

## 结论

Jade 是一个简单干净的 javascript 模板引擎。它可以很容易地集成到 Rails 应用程序中，并与资产管道很好地配合。呈现模板轻而易举，不需要太多的配置，但它也不是没有限制。Jade 应该服务于渲染 javascript 模板的常见需求，它的易用性和平滑集成肯定超过了它的局限性。

## 分享这篇文章