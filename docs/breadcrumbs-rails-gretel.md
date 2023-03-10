# 格雷特尔铁路面包屑

> 原文：<https://www.sitepoint.com/breadcrumbs-rails-gretel/>

![Hänsel und Gretel entdecken das Hexenhaus im Wald](img/b446f377e11d8ed5c99fcb94979d459b.png)

您可能知道面包屑是导航元素，可以帮助用户跟踪他们在网站上的当前位置。面包屑可以列出以前访问过的页面或当前页面的父页面(在分层网站上)。你可能也知道这个术语是从哪里来的，但是我还是要提醒你一下。

维基百科上说“汉瑟和葛丽特”是一个著名的德国童话，由格林兄弟录制，于 1812 年出版。这个故事讲述了一对年轻的兄弟姐妹被他们的父亲遗弃在森林里，因为家里没有东西吃。幸运的是，汉瑟碰巧带了一片面包，并留下了一串面包屑，这样他们就能找到回家的路。不幸的是，所有的面包屑都被鸟吃了，孩子们不得不继续流浪，最终面对一个邪恶的女巫。如果你不知道这个故事的结局，那就找个时间读一读吧。

无论如何,“面包屑”这个词来源于这个故事。就像 Hansel 留下了一串面包屑一样，我们向用户呈现了一串超链接，这样他们就可以看到他们在站点中走过的路。希望我们的面包屑不会被鸟吃掉…

在大型分层网站(如 [Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web) )上放置面包屑是一个不错的做法，但是我们如何将它集成到我们的 Rails 应用中呢？

本文使用一个名为 Gretel 的 gem 创建了一个带有面包屑的演示应用程序。这个演示展示了如何为大型网站设计、定制和缩放面包屑。

演示应用的源代码可以在 GitHub 上找到。

## 开始旅程

> 这个演示使用的是 Rails 4.1.4，但是 Rails 3 也可以实现同样的解决方案。

好了，我们走吧。我们将创建音乐商店，一个非常简单的销售音乐专辑的在线商店。当然，我们不会实现所有的东西，但让我们假设有一天这个应用程序会发展成大的东西。

创建没有默认测试套件的新应用程序:

```
$ rails new music_shop -T
```

我们将使用 [Twitter Bootstrap](http://getbootstrap.com/) 来设计风格和布局，所以把这个宝石放到你的*宝石文件*中:

*Gemfile*

```
[...]
gem 'bootstrap-sass'
```

奔跑

```
$ bundle install
```

接下来，将`application.css`重命名为`application.css.scss`,并将其内容更改为:

*样式表/application.css.scss*

```
@import 'bootstrap';
@import 'bootstrap/theme';
```

要利用 Bootstrap 的魔力，请更改布局:

*layouts/application . html . erb*

```
[...]
<div class="navbar navbar-inverse" role="navigation">
  <div class="container">
    <div class="navbar-header">
      <%= link_to 'Music shop', root_path, class: 'navbar-brand' %>
    </div>
    <ul class="nav navbar-nav">
      <li><%= link_to 'Albums', albums_path %></li>
    </ul>
  </div>
</div>

<div class="container">
  <%= yield %>
</div>
[...]
```

好了，现在我们可以继续讨论模型和相应的路线了。对于这个演示，只有一个模型——`Album`——具有以下字段(更不用说默认的`id`、`created_at`、`updated_at`):

*   `title` ( `string`)
*   `description` ( `text`)
*   `artist` ( `text`)
*   `price` ( `decimal`)

创建相应的迁移:

```
$ rails g model Album title:string description:text artist:string price:decimal
```

打开迁移文件并修改以下行:

*xx_create_albums.rb*

```
[...]
t.decimal :price, precision: 6, scale: 2
[...]
```

价格最多可以有 6 位数，小数点后有 2 位数。应用迁移:

```
$ rake db:migrate
```

和一些路线(我们现在将跳过`update`、`destroy`、`new`和`create`，并假设这些将在下一次迭代中创建):

*routes.rb*

```
[...]
resources :albums, only: [:index, :edit, :show]
root to: 'albums#index'
[...]
```

现在，创建相应的控制器:

*相册 _ 控制器. rb*

```
class AlbumsController < ApplicationController
  def index
    @albums = Album.all
  end

  def edit
    @album = Album.find(params[:id])
  end

  def show
    @album = Album.find(params[:id])
  end
end
```

让我们也来关注一下`index`视图:

*albums/index.html.erb*

```
<div class="page-header">
  <h1>Albums</h1>
</div>

<table class="table table-hover">
  <tr>
    <th>Title</th>
    <th>Artist</th>
    <th>Description</th>
    <th>Price</th>
  </tr>
  <% @albums.each do |album| %>
    <tr>
      <td><%= link_to album.title, album_path(album) %></td>
      <td><%= album.artist %></td>
      <td><%= album.description %></td>
      <td><%= number_to_currency album.price, unit: '$' %></td>
    </tr>
  <% end %>
</table>
```

注意，我们使用`number_to_currency`助手来正确格式化价格(我们假设价格是以美元输入的)。

### 添加一些相册

此时，我们有了模型、控制器和视图，但是没有数据。我们可以手动输入，但那真的很繁琐。相反，让我们使用`seeds.rb`将一些演示数据加载到我们的数据库中。

使用 Benjamin Curtis 创造的 [faker](https://github.com/stympy/faker) 宝石生成一些随机的标题和价格。将`faker`添加到*gem 文件*中:

*Gemfile*

```
[...]
gem 'faker'
[...]
```

别忘了跑步

```
$ bundle install
```

创建一个脚本来加载演示数据:

*seeds.rb*

```
50.times do
  Album.create({title: Faker::Lorem.words(2).join(' ').titleize,
                description: Faker::Lorem.paragraph(3, true, 4),
                artist: Faker::Name.name, price: Faker::Commerce.price})
end
```

`words(2)`意味着我们生成两个随机单词的数组。`titleize`用于将那些文字格式化成标题。`paragraph(3, true, 4)`用四个随机的句子创建三个段落。正如你可能已经猜到的那样，`name`生成一个随机的名字，`price`创建一个价格。很简单，不是吗？

加载数据:

```
$ rake db:seed
```

运行服务器并检查这一切看起来如何。我们已经做好了基础工作，准备开始整合面包屑。

## 拿面包片

就像 Hansel 吃了一片面包一样，我们需要自己的工具来开始创建防鸟路径。认识一下由 Lasse Bunk 创建的灵活的 Rails breadcrumbs 插件。正如这张[漂亮的摘要图](https://i.imgur.com/nH25yiH.png)中所述，Gretel 不会弄乱你的控制器——所有需要的配置都放在别处。

将宝石放入您的宝石档案中:

*Gemfile*

```
[...]
gem 'gretel'
[...]
```

然后跑

```
$ bundle install
```

使用生成器创建基本配置文件:

```
$ rails generate gretel:install
```

在 *config* 目录中，您将找到包含您所有配置的 *breadcrumbs.rb* 文件。您可能在想，“这意味着我每次修改这个文件时都必须重新加载我的服务器？”。幸运的是，你没有。

从版本 2.1.0 开始，在开发环境中，该文件在每次更改时都会自动重新加载。在生产环境中，它只加载一次。

现在，我们可以使用以下代码创建第一个面包屑:

*breadcrumbs.rb*

```
crumb :root do
  link "Home", root_path
end
```

布局和视图也需要修改:

*layouts/application . html . erb*

```
<%= breadcrumbs pretext: "You are here: " %>
```

`pretext`选项指定应该放在页面上面包屑前面的文本。还可以指定`posttext`在面包屑后面放点东西。

*albums/index.html.erb*

```
<% breadcrumb :root %>
[...]
```

这一行需要告诉哪一个面包屑渲染。

启动服务器，导航到主页，然后……面包屑就不会出现了。这是为什么呢？？？

默认情况下，如果只有一个链接，Gretel 不会显示面包屑。要解决这个问题，改变`breadcrumbs`方法:

*layouts/application . html . erb*

```
<%= breadcrumbs pretext: "You are here: ", display_single_fragment: true %>
```

您现在应该会看到面包屑块。另一个问题是造型——现在看起来很丑。幸运的是，Gretel 已经知道如何为 Bootstrap 渲染面包屑。就像这样提供`style`选项:

*layouts/application . html . erb*

```
<%= breadcrumbs pretext: "You are here: ", display_single_fragment: true, style: :bootstrap %>
```

`style`可以取其他值，如`ul`、`ol`、`foundation5`(渲染面包屑由[基金会](http://foundation.zurb.com/)、`inline`进行样式化)。

重新加载页面，瞧！Bootstrap 会帮我们打理造型。但是，面包屑之间的分离器有一个问题。这是因为 Bootstrap 的样式附加了`/`作为分隔符，Gretel 默认使用`›`。所以，再一次，改变`breadcrumbs`方法:

*layouts/application . html . erb*

```
<%= breadcrumbs pretext: "You are here: ", display_single_fragment: true, style: :bootstrap, separator: '' %>
```

酷！顺便说一下，如果你想让当前的面包屑被渲染成一个链接，将`link_current`选项设置为`true`。您也可以通过将`semantic`设置为`true`来使用[语义面包屑](https://support.google.com/webmasters/answer/185417?hl=en)。Gretel 有一些其他的选择，可以在这里找到。

## 创造踪迹

好吧，把线索延伸一点怎么样？我们仍然需要创建`show`和`edit`视图。先说`show`:

*albums/show.html.erb*

```
<div class="page-header">
  <h1><%= @album.title %></h1>
</div>

<p><%= @album.description %></p>

<%= link_to 'Edit', edit_album_path(@album), class: 'btn btn-primary' %>
```

在真正的应用程序中，你需要某种认证和授权系统，这样“编辑”按钮就不会被所有人看到，但这只是一个演示。

现在创建一个新面包屑:

*breadcrumbs.rb*

```
crumb :album do |album|
  link album.title, album
  parent :root
end
```

这里，`album`变量用于获取相册的标题并显示在页面上。我们还将面包屑的父代指定为`root`。顺便说一下，我们可以跳过这一行，因为默认情况下，Gretel 使`root`成为 crumb 的父级(它由`autoroot`选项控制)。

将`breadcrumb`方法添加到“显示”视图中:

*albums/show.html.erb*

```
<% breadcrumb :album, @album %>
[...]
```

正如您在这里看到的，我们不仅提供了面包屑的名称，而且还传递了资源。实际上，我们可以提供尽可能多的参数。这一行也可以简化(面包屑会自动推断):

```
<% breadcrumb @album %>
```

最后，让我们处理一下`edit`视图:

*albums/edit.html.erb*

```
<% breadcrumb :edit_album, @album %>

<div class="page-header">
  <h1>Edit <%= @album.title %></h1>
</div>

<p>Coming in the next version...</p>
```

不要忘记创建新的面包屑:

*breadcrumbs.rb*

```
[...]
crumb :edit_album do |album|
  link "Edit #{album.title}", album
  parent :album, album
end
```

同样，我们使用`Album`资源并将`album` crumb 指定为直接父节点。重新加载你的页面，导航到一个相册，然后点击“编辑”——注意面包屑是如何变化的。很简单，是吧？

## 延伸踪迹

好吧，让我们创造更多的面包屑。正如我们已经讨论过的，真正的在线会有某种认证。我们不打算这样做，但我们将创建一个用户的个人资料页面，并为它添加一个面包屑。

创建新路线:

*routes.rb*

```
resources :users, only: [:show]
```

以及相应的控制器:

*users_controller.rb*

```
class UsersController < ApplicationController
end
```

提供一个到`show`页面的链接(使用假用户 id):

*layouts/application . html . erb*

```
<div class="navbar navbar-inverse" role="navigation">
  <div class="container">
    <div class="navbar-header">
      <%= link_to 'Music shop', root_path, class: 'navbar-brand' %>
    </div>
    <ul class="nav navbar-nav">
      <li><%= link_to 'Albums', albums_path %></li>
    </ul>

    <ul class="nav navbar-nav navbar-right">
      <li><%= link_to 'Profile', user_path(1) %></li>
    </ul>
    [...]
```

并创建实际视图:

*users/show.html.erb*

```
<% breadcrumb :user %>
<div class="page-header">
  <h1><%= current_user %>'s profile</h1>
</div>

<p>Coming soon...</p>
```

假设我们有返回用户名的`current_user` helper 方法。我们可以在创建面包屑时使用这个助手吗？我们来试试吧！

*application_helper.rb*

```
module ApplicationHelper
  def current_user
    "Someone"
  end
end
```

*breadcrumbs.rb*

```
[...]
crumb :user do
  link "#{current_user}'s profile"
end
```

看来助手可以毫无问题的使用！我们也可以在`crumb`方法中实现条件。假设如果用户是管理员，我们希望在 breadcrumb 中包含一个到管理区域的链接。添加这个`admin?`存根方法:

*application_helper.rb*

```
[...]
def admin?
  true
end
[...]
```

改变你的面包屑:

*breadcrumbs.rb*

```
[...]
crumb :admin_root do
  link "Admin's area"
  parent :root
end

crumb :user do
  link "#{current_user}'s profile"
  if admin?
    parent :admin_root
  else
    parent :root
  end
end
```

重新加载页面后，您会看到管理区“crumb”。注意，第二个参数没有提供给`link`方法，这导致 crumb 被呈现为纯文本，没有链接。

好吧，还有一个实验。假设我们有一个搜索页面，也想在其中包含面包屑。我们可以在面包屑中显示实际的搜索词吗？

再添加一条路线:

*routes.rb*

```
[...]
get '/search', to: 'pages#search', as: :search
[...]
```

创建控制器:

*pages_controller.rb*

```
class PagesController < ApplicationController
  def search
    @term = params[:q]
  end
end
```

查看:

*pages/search.html.erb*

```
<% breadcrumb :search, @term %>

<% parent_breadcrumb do |link| %>
  <%= link_to "Back to #{link.text}", link.url %>
<% end %>

<div class="page-header">
  <h1>Searching for <%= @term %></h1>
</div>

<p>Guess what? Coming soon!</p>
```

和一个搜索表单:

*layouts/application . html . erb*

```
[...]
<div class="navbar navbar-inverse" role="navigation">
  <div class="container">
    <div class="navbar-header">
      <%= link_to 'Music shop', root_path, class: 'navbar-brand' %>
    </div>
    <ul class="nav navbar-nav">
      <li><%= link_to 'Albums', albums_path %></li>
    </ul>

    <ul class="nav navbar-nav navbar-right">
      <li><%= link_to 'Profile', user_path(1) %></li>
    </ul>

    <%= form_tag search_path, method: :get, class: 'navbar-form navbar-right' do %>
      <%= text_field_tag 'q', nil, placeholder: 'Search' %>
    <% end %>
  </div>
</div>
[...]
```

创建新面包屑:

*breadcrumbs.rb*

```
[...]
crumb :search do |keyword|
  link "Searching for #{keyword}", search_path(q: keyword)
end
```

如您所见，路由助手可用于生成用户提供的实际搜索词的链接。去试试这个吧！

顺便说一下，您还可以获取父面包屑并创建一个“返回”链接，如下所示:

```
<% parent_breadcrumb do |link| %>
  <%= link_to "Back to #{link.text}", link.url %>
<% end %>
```

这有时会派上用场。

最后要提到的是，一个面包屑可能有多个链接。例如，我们可以这样做:

*breadcrumbs.rb*

```
crumb :root do
  link "Music shop"
  link "Home", root_path
end
[...]
```

现在，在“主页”链接之前会显示一个“音乐商店”链接。

我们还可以根据条件调整链接的名称。例如，如果我们的商店在周一有很好的折扣，我们可以通知用户(好吧，这是一个非常做作的例子):

*breadcrumbs.rb*

```
crumb :root do
  link "Music shop" + (Time.now.monday? ? " (SALES!)" : '')
  link "Home", root_path
end
[...]
```

### 攀登小径

对于大型网站来说， *breadcrumbs.rb* 文件可能会变得非常复杂。幸运的是，Gretel 允许将配置轻松地分成多个文件。为此，在*配置*中创建一个名为 *breacrumbs* 的新目录，并放置*。rb* 文件与你的配置在那里。

在这个演示中，我用与专辑相关的面包屑创建了 *albums.rb* ，用其他面包屑创建了 *other_stuff.rb* 。你可以删除 *breadcrumbs.rb* 文件或者保留其他配置——它仍然会被加载。

另一件值得一提的事情是，面包屑也将从您的应用程序拥有的任何[引擎](http://guides.rubyonrails.org/engines.html)中加载，但是主应用程序中的面包屑具有更高的优先级。

## 结论

各位，今天就到这里。我们已经了解了 Gretel gem，可以轻松地将面包屑添加到您的应用程序中。宝石是灵活的，真的很容易使用，所以我鼓励你尝试一下！

你觉得面包屑怎么样？它们有用吗？你在你的应用程序中实现过它们吗？你是用了宝石还是从头开始写一切？分享一下你的经验！

感谢阅读！

## 分享这篇文章