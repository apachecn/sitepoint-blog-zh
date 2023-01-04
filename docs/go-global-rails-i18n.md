# 借助 Rails 和 I18n 走向全球

> 原文：<https://www.sitepoint.com/go-global-rails-i18n/>

![bola del mundo](img/a73e0153752e787d925d72f2ddeedd62.png)

你可能听说过[巴别塔](https://en.wikipedia.org/wiki/Tower_of_Babel)。这是一个圣经传说，讲述了几百年前的一个时代，那里所有的人都说同一种语言。然而，当他们开始建造巴别塔，试图达到天空，上帝混淆了他们的语言，使他们不再能理解对方，并完成这项工作。

不管是真是假，今天的人们说不同的语言。有一天，你可能需要在你的网站上启用对不同语言的支持。你会怎么做？如果有用户生成的内容也需要翻译呢？请继续阅读并找出答案！

本文将涵盖以下主题:

*   Rails 国际化 API (I18n)
*   在网站上切换语言
*   根据用户的位置自动设置语言(Geocoder gem)
*   在数据库中储存不同版本的用户生成内容(Globalize gem)

源代码可以在 [GitHub](https://github.com/bodrovis/SitePointI18n) 上找到，工作演示可以在【http://sitepoint-i18n.herokuapp.com】的[找到。](http://sitepoint-i18n.herokuapp.com)

## 基础

在这个演示中，我将使用 Rails 4.1.5，但同样的解决方案也可以用 Rails 3 来实现。

为了今天的目的，让我们创建一个简单的教育网站，名为*教育家*。首先，我们得打下一些基础。不要担心——这个迭代会很短。

创建一个没有默认测试套件的新 Rails 应用程序:

```
$ rails new educator -T
```

我们的网站会有导师发布的帖子。我们不打算实现一个认证和授权系统——只有一个模型、控制器、视图和几个路由。以下是将出现在`articles`表中的字段列表(与`id`、`created_at`、`updated_at`一起):

*   `title` ( `string`)
*   `body` ( `text`)

创建并应用所需的迁移:

```
$ rails g model Article title:string body:string
$ rake db:migrate
```

现在将以下路线添加到您的 *routes.rb* 文件中:

*config/routes.rb*

```
[...]
resources :articles, only: [:index, :new, :create, :edit, :update]
root to: 'articles#index'
[...]
```

接下来，创建 **articles_controller.rb** 文件，并粘贴以下代码:

*articles_controller.rb*

```
class ArticlesController < ApplicationController
  def index
    @articles = Article.order('created_at DESC')
  end

  def new
    @article = Article.new
  end

  def create
    @article = Article.new(article_params)
    if @article.save
      flash[:success] = "The article was published successfully!"
      redirect_to articles_path
    else
      render 'new'
    end
  end

  def edit
    @article = Article.find(params[:id])
  end

  def update
    @article = Article.find(params[:id])
    if @article.update_attributes(article_params)
      flash[:success] = "The article was updated successfully!"
      redirect_to articles_path
    else
      render 'edit'
    end
  end

  private

  def article_params
    params.require(:article).permit(:body, :title)
  end
end
```

这里没有什么特别的——一些显示、创建和编辑文章的基本方法。

如果您在 Rails 3 上，并且没有使用 [strong_params](https://github.com/rails/strong_parameters) ，用`params[:article]`替换`article_params`方法，并将下面一行添加到模型中:

*models/article.rb*

```
[...]
attr_accessible :body, :title
[...]
```

继续查看。为了帮助设计风格，让我们使用 Twitter Bootstrap:

*Gemfile*

```
[...]
gem 'bootstrap-sass'
[...]

bundle install
```

*样式表/application.css.scss*

```
@import 'bootstrap';
@import 'bootstrap/theme';
```

*JavaScript s/application . js*

```
[...]
//= require bootstrap
```

现在，我们可以利用 Bootstrap 的强大功能:

*layouts/application . html . erb*

```
[...]
<body>
  <div class="navbar navbar-default navbar-static-top">
    <div class="container">
      <div class="navbar-header">
        <%= link_to 'Educator', root_path, class: 'navbar-brand' %>
      </div>

      <ul class="nav navbar-nav navbar-left">
        <li><%= link_to 'Add new article', new_article_path %></li>
      </ul>
    </div>
  </div>

  <div class="container">
    <% flash.each do |key, value| %>
      <div class="alert alert-<%= key %>">
        <%= value %>
      </div>
    <% end %>

    <div class="page-header">
      <h1><%= yield :page_header %></h1>
    </div>

    <%= yield %>
  </div>
</body>
[...]
```

请注意，我在这里使用了`yield :page_header`,以便在呈现不同的视图时可以方便地提供不同的页面标题。

创建实际视图:

*articles/index.html.erb*

```
<% content_for(:page_header) {"List of articles"} %>

<%= render @articles %>
```

`content_for`这里用来给我上面讲的`yield`块提供一个页面标题。我们还必须创建一个 **_article.html.erb** 分部，它将为`@articles`数组中的每篇文章呈现:

*articles/_ article . html . erb*

```
<h2><%= article.title %></h2>

<%= article.created_at.strftime('%-d %B %Y %H:%M:%S') %>

<p><%= article.body %></p>

<p><%= link_to 'Edit', edit_article_path(article), class: 'btn btn-default' %></p>
<hr/>
```

`strftime`以下列格式显示创建日期:“2014 年 1 月 1 日 00:00:00”。点击阅读更多关于`strftime` [支持的标志。](http://ruby-doc.org/core-2.1.1/Time.html#method-i-strftime)

`new`和`edit`动作的视图甚至更简单:

*articles/new.html.erb*

```
<% content_for(:page_header) {"New article"} %>

<%= render 'form' %>
```

*articles/edit.html.erb*

```
<% content_for(:page_header) {"Edit article"} %>

<%= render 'form' %>
```

最后， **_form.html.erb** partial:

*articles/_form.html.erb*

```
<%= form_for @article do |f| %>
  <div class="form-group">
    <%= f.label :title %>
    <%= f.text_field :title, class: 'form-control', required: true %>
  </div>

  <div class="form-group">
    <%= f.label :body %>
    <%= f.text_area :body, rows: 3, class: 'form-control', required: true %>
  </div>

  <%= f.submit 'Submit', class: 'btn btn-primary btn-lg' %>
<% end %>
```

启动服务器并创建几篇文章来检查一切是否正常。太好了，现在我们可以进入下一个迭代了！

## “国际化”这个长词

假设我们的教育网站越来越受欢迎，我们希望鼓励更多的用户访问。做到这一点的一个方法是使网站多语言化。来自不同国家的学生可以选择合适的语言，舒适地使用我们的资源。

现在我们将为用户提供两种语言供选择:英语和俄语。为什么是俄罗斯人？嗯，它是世界上最受欢迎的语言之一，有 2.54 亿人在使用它……嗯，它是我的母语。当然，你可以选择任何一种语言(选择一种非普遍使用的语言可能需要你做一些额外的工作，我稍后会讨论这个问题。)

在你的宝石档案中放入一颗新宝石:

*Gemfile*

```
[...]
gem 'rails-i18n', '~> 4.0.0' # for Rails 4
# OR
gem 'rails-i18n', '~> 3.0.0' # for Rails 3
[...]
```

别忘了跑步

```
$ bundle install
```

rails-i18n 是一个为 Ruby on Rails I18n 收集地区数据的存储库。好吧，I18n 到底是什么？实际上，这只不过是“国际化”这个词的缩写——在第一个“I”和最后一个“n”之间正好有 18 个字母。很傻，不是吗？

这个 gem 为不同的语言提供了基本的地区数据，比如月份名称、日期、验证消息、一些多元化规则等的翻译。支持语言的完整列表可以在[这里](https://github.com/svenfuchs/rails-i18n#available-locales)找到。如果您的语言不在列表中，您需要自己添加翻译。

我们需要在我们的应用程序中提供一系列语言:

*配置/应用. rb*

```
[...]
I18n.available_locales = [:en, :ru]
[...]
```

顺便说一下，您还可以调整默认时区(最初使用“中部时间(美国和加拿大)”和默认区域设置(`:en`)，正如您所猜测的，默认区域设置是“英语”):

```
config.time_zone = 'Moscow' # set default time zone to "Moscow" (UTC +4)
config.i18n.default_locale = :ru # set default locale to Russian
```

每个区域都需要一个文件来存储标题、按钮、标签和我们站点的其他元素的翻译。这些翻译文件位于 **config/locales** 目录中，扩展名为*。yml*([YAML](http://www.yaml.org/)——又一种标记语言)。

默认情况下只有一个文件， **en.yml** ，里面有一些演示内容和快速解释。用以下内容替换此文件:

*config/locales/en.yml*

```
en:
  activerecord:
    attributes:
      article:
        title: "Title"
        body: "Body"
  forms:
    messages:
      success: "Operation succeeded!"
    buttons:
      edit: "Edit"
      submit: "Submit"
  menu:
    new_article: "Add an article"
  articles:
    index:
      title: "List of articles"
    edit:
      title: "Add article"
    new:
      title: "New article"
```

这里需要注意一些事情。首先，我们必须提供这个文件的区域设置，这是通过根键`en`完成的。所有其他翻译数据都嵌套在 locale 键中。要存储活动记录属性的翻译，请使用结构`activerecord - attributes - *model_name*`。

之后，就真的要由开发人员根据自己的个人喜好来组织密钥了。我使用了一个`forms`键来包含表单元素的翻译。

我们现在将跳过对`articles`块的讨论。现在，让我们用一些奇怪的西里尔字母来存储俄语翻译:

*config/locales/ru.yml*

```
ru:
  activerecord:
   attributes:
     article:
       title: "Название"
       body: "Текст"
  forms:
    messages:
      success: "Операция была успешно выполнена!"
    buttons:
     edit: "Редактировать"
     submit: "Отправить"
  menu:
    new_article: "Добавить статью"
  articles:
    index:
      title: "Список статей"
    edit:
      title: "Редактировать статью"
    new:
      title: "Добавить статью"
```

有一个笑话讲的是一个人用拉丁字母展示了一些“技巧”，比如:“r –>я”、“n –>и”。实际上，他只是在用西里尔字母。无论如何，我们可以在视图中使用这些翻译数据。

有一个名为`translate`的方法，它接受至少一个参数，并在翻译文件中查找应用程序当前设置的语言环境。点击阅读更多[。如果您不熟悉 I18n，我鼓励您完整地阅读该文档。](http://guides.rubyonrails.org/i18n.html#looking-up-translations)

在视图中，可以用别名`t`调用`translate`方法:

```
t('menu.new_article')
```

或者:

```
t(:new_article, scope: :menu) # This uses a scope to explain where to find the required key
```

好的，让我们在布局文件中尝试一下:

*layouts/application . html . erb*

```
<body>
  <div class="navbar navbar-default navbar-static-top">
    <div class="container">
      <div class="navbar-header">
        <%= link_to 'Educator', root_path, class: 'navbar-brand' %>
      </div>

      <ul class="nav navbar-nav navbar-left">
        <li><%= link_to t('menu.new_article'), new_article_path %></li>
      </ul>
    </div>
  </div>

  <div class="container">
    <% flash.each do |key, value| %>
      <div class="alert alert-<%= key %>">
        <%= value %>
      </div>
    <% end %>

    <div class="page-header">
      <h1><%= yield :page_header %></h1>
    </div>

    <%= yield %>
  </div>
</body>
```

我已经把`换成了`。现在，根据当前的语言环境，这个链接将包含不同的文本。这是处理不同地区的一种非常方便的方式。如果将来某个时候，您需要在您的站点上添加对德语的支持，只需创建另一个语言环境文件并将德语添加到可用语言的数组中。``

 ``现在让我们修改`index.html.erb`视图:

```
<% content_for(:page_header) {t('.title')} %>
<%= render @articles %>
```

这里发生了一些不同的事情。我只是使用了`t('.title')`，但是索引页面的标题存储在`articles.index`范围内。为什么会起作用？为了让我们的生活更简单，Rails 支持[“懒惰”查找](http://guides.rubyonrails.org/i18n.html#lazy-lookup)。如果我们有一个存储在`articles`目录中的`index.html.erb`视图和翻译文件中的以下结构:

```
articles:
  index:
    title: "List of articles"
```

在`index.html.erb`中，我们可以通过简单地发出`t('.title')`来查找`title`键。对于其他视图，可以实现相同的解决方案:

*articles/new.html.erb*

```
<% content_for(:page_header) {t('.title')} %>
<%= render 'form' %>
```

*articles/edit.html.erb*

```
<% content_for(:page_header) {t('.title')} %>
<%= render 'form' %>
```

转到*_ article . html . erb*partial:

*articles/_ article . html . erb*

```
<h2><%= article.title %></h2>

<%= l(article.created_at, format: '%-d %B %Y %H:%M:%S') %>

<p><%= article.body %></p>

<p><%= link_to t('forms.buttons.edit'), edit_article_path(article), class: 'btn btn-default' %></p>
<hr/>
```

注意，`strftime`方法被替换成了`l`，它是`localize`的别名。该方法获取提供的日期和/或时间，并通过使用相应的翻译数据(由`rails-i18n` gem 提供)以适当的方式对其进行格式化。

不要忘记表格:

*articles/_form.html.erb*

```
<%= form_for @article do |f| %>
  <div class="form-group">
    <%= f.label :title %>
    <%= f.text_field :title, class: 'form-control', required: true %>
  </div>

  <div class="form-group">
    <%= f.label :body %>
    <%= f.text_area :body, rows: 3, class: 'form-control', required: true %>
  </div>

  <%= f.submit t('forms.buttons.submit'), class: 'btn btn-primary btn-lg' %>
<% end %>
```

只要我们为`Article`的属性指定了翻译，它们就会被放到相应的标签中。

最后，翻译简讯的文本:

*articles_controller.rb*

```
[...]

def create
  @article = Article.new(article_params)
  if @article.save
    flash[:success] = t('forms.messages.success')
    redirect_to articles_path
  else
    render 'new'
  end
end

[...]

def update
  @article = Article.find(params[:id])
  if @article.update_attributes(article_params)
    flash[:success] = t('forms.messages.success')
    redirect_to articles_path
  else
    render 'edit'
  end
end

[...]
```

让我们在这个迭代中再讨论一件事。假设您想显示数据库中有多少教育文章。为了统计所有的文章，你可以使用类似`@articles.length`的东西，但是复数呢？在英语中，复数规则很简单:“0 冠词”、“1 冠词”、“2 冠词”等。然而，在俄语中，事情开始变得有点复杂，因为复数规则不是那么简单。看来 I18n 模块足够聪明，可以处理多元化规则。

*locales/en.yml*

```
[...]
articles:
  index:
    count:
      one: "%{count} article"
      other: "%{count} articles"
[...]
```

房地/ru.yml

```
articles:
  index:
    count:
      zero: "%{count} статей"
      one: "%{count} статья"
      few: "%{count} статьи"
      many: "%{count} статей"
      other: "%{count} статьи"
```

在这里，我只是简单地添加了在什么情况下使用哪个变量。另外，注意`%{count}`–这是插值。

现在我们可以将这一行添加到视图中:

*articles/index.html.erb*

```
<%= t('.count', :count => @articles.length) %>
```

我将 hash 作为第二个参数传递给`t`方法。它使用这个`count`属性来选择所需的翻译，并将其值插入到字符串中。很酷，不是吗？

重新加载服务器并观察结果。只要默认区域设置为英语，你就不会发现任何重大的差异。

让我们给用户一个改变网站语言的方法。

## 改变语言

让我们在顶部菜单中添加语言切换控件。借助 Bootstrap 的样式和脚本，我们可以轻松实现如下下拉菜单:

```
<div class="navbar navbar-default navbar-static-top">
  <div class="container">
    [...]
    <ul class="nav navbar-nav navbar-right">
      <li class="dropdown">
        <a class="dropdown-toggle" data-toggle="dropdown" href="#">
          <%= t('menu.languages.lang') %>
          <span class="caret"></span>
        </a>
        <ul class="dropdown-menu" role="menu">
          <li>
            <%= link_to t('menu.languages.en'), change_locale_path(:en) %>
          </li>
          <li>
            <%= link_to t('menu.languages.ru'), change_locale_path(:ru) %>
          </li>
        </ul>
      </li>
    </ul>
    [...]
```

添加新的翻译数据:

*locales/en.yml*

```
[...]
menu:
  new_article: "Add an article"
  languages:
    lang: "Language"
    ru: "Russian"
    en: "English"
[...]
```

房地/ru.yml

```
[...]
menu:
  new_article: "Добавить статью"
  languages:
    lang: "Язык"
    ru: "Русский"
    en: "Английский"
[...]
```

我们需要一条新的路线来改变地点。为了简单起见，我将创建一个名为`SettingsController`的新控制器，并在那里添加一个`change_locale`方法(为了遵循 REST 原则，您可以创建一个单独的控制器来管理地区并使用一个`update`方法):

*config/routes.rb*

```
[...]
get '/change_locale/:locale', to: 'settings#change_locale', as: :change_locale
[...]
```

实际方法:

*设置 _ 控制器. rb*

```
class SettingsController < ApplicationController
  def change_locale
    l = params[:locale].to_s.strip.to_sym
    l = I18n.default_locale unless I18n.available_locales.include?(l)
    cookies.permanent[:educator_locale] = l
    redirect_to request.referer || root_url
  end
end
```

检查传递了哪个区域设置，以及它是否有效。我们不想把地区设置成一些胡言乱语。然后，设置一个永久 cookie(并不像你想象的那么永久。它将在 20 年后过期)来存储所选择的区域并将用户重定向回。

太好了。我们需要做的最后一件事是检查 cookie 的内容并相应地调整区域设置。我们希望这发生在每一页上，所以使用`ApplicationController`:

*application _ controller . Rb*

```
[...]
before_action :set_locale

def set_locale
  if cookies[:educator_locale] && I18n.available_locales.include?(cookies[:educator_locale].to_sym)
    l = cookies[:educator_locale].to_sym
  else
    l = I18n.default_locale
    cookies.permanent[:educator_locale] = l
  end
  I18n.locale = l
end
[...]
```

检查是否设置了 cookie，以及提供的语言是否在可用区域设置列表中。如果是–获取其内容并相应地设置区域设置。否则，设置默认区域设置。

重新加载服务器并尝试更改区域设置。您会注意到所有的标题、菜单、按钮和标签
都相应地改变了它们的内容。但是我们能做得更好吗？我们可以通过 IP 检查用户的国家并设置最合适的语言环境吗？答案是，是的，我们可以！

## 根据用户的国家设置区域设置

为了根据 IP 地址获取用户的国家，我们将使用 Alex Reisner 的 [Geocoder](https://github.com/alexreisner/geocoder) gem。

*Gemfile*

```
[...]
gem 'geocoder'
[...]
```

别忘了跑步

```
$ bundle install
```

要检查用户的国家，请使用以下行:

```
request.location.country_code
```

它返回类似“RU”、“CN”、“DE”等字符串。只要我们提供俄语支持，我们将为来自 [CIS](https://en.wikipedia.org/wiki/Commonwealth_of_Independent_States) 国家的每个用户设置它，并为所有其他用户设置英语:

*application _ controller . Rb*

```
def set_locale
  if cookies[:educator_locale] && I18n.available_locales.include?(cookies[:educator_locale].to_sym)
    l = cookies[:educator_locale].to_sym
  else
    begin
      country_code = request.location.country_code
      if country_code
        country_code = country_code.downcase.to_sym
        # use russian for CIS countries, english for others
        [:ru, :kz, :ua, :by, :tj, :uz, :md, :az, :am, :kg, :tm].include?(country_code) ? l = :ru : l = :en
      else
        l = I18n.default_locale # use default locale if cannot retrieve this info
      end
    rescue
      l = I18n.default_locale
    ensure
      cookies.permanent[:educator_locale] = l
    end
  end
  I18n.locale = l
end
```

如果具有首选区域设置的 cookie 已经存在，则使用它。否则，尝试获取用户的位置。如果做不到这一点，请使用默认的语言环境。如果国家的 ISO 代码在列表中，则使用俄语，否则将区域设置为英语。

仅供参考，最初设置网站语言还有其他方法(如域名、用户代理和其他——点击阅读更多[)。](http://guides.rubyonrails.org/i18n.html#setting-and-passing-the-locale)

## 存储文章的翻译

此时，用户可以更改网站的语言，所有链接、按钮、标签和菜单项都将被翻译。但是，文章不会。当然，文章只会以撰写时的语言显示。我们如何存储用户生成内容的翻译？

最简单的方法是使用 Sven Fuchs 和其他人创造的全球化宝石。将其添加到您的 *Gemfile* :

*Gemfile*

```
[...]
gem 'globalize', '~> 4.0.2' # For Rails 4
# Or
gem 'globalize', '~> 3.1.0' # For Rails 3
```

然后跑

```
$ bundle install
```

这个 gem 使用一个单独的翻译表。在我们的演示中，它将被称为`article_translations`，包含以下字段:

*   `id`
*   `article_id`
*   `locale`
*   这是翻译本身的创作日期，而不是原文的创作日期
*   `updated_at`
*   `title`
*   `body`

我们存储每种语言的文章翻译版本。在创建翻译表之前，修改我们的模型来标记应该翻译的字段(例如，我们不需要翻译像`id`这样的字段):

*models/article.rb*

```
[...]
translates :title, :body
[...]
```

现在创建适当的迁移:

```
$ rails g migration create_translation_for_articles
```

像这样修改迁移:

*XXX _ create _ translation _ for _ articles . Rb*

```
class CreateTranslationForArticles < ActiveRecord::Migration
  def up
    Article.create_translation_table!({
                                          title: :string,
                                          body: :text}, {migrate_data: true})
  end

  def down
    Article.drop_translation_table! migrate_data: true
  end
end
```

首先，注意我们必须使用`up`和`down`方法。全球化不能用`change`的方法。第二，需要`migrate_data: true`选项，因为我们的`articles`表已经包含了一些数据，我们想要迁移它(它将被迁移到默认的语言环境)。

运行迁移

```
$ rake db:migrate
```

启动你的服务器。如果您想为任何文章创建俄语版本，请打开它进行编辑，将站点的区域设置更改为俄语，然后输入新内容。Globalize 会自动将它存储在带有正确区域设置的 translations 表中。对于尚未翻译的文章，默认情况下将使用默认语言环境中的内容。

当您使用 [Globalize-versioning](https://github.com/globalize/globalize-versioning) gem 时，globalize 还支持使用 [PaperTrail](https://github.com/airblade/paper_trail) 进行版本控制。

> > If you don't know what PaperTrail is or how to integrate version control into your Rails application, you may be interested in my article [Using PaperTrail for Version Control](https://www.sitepoint.com/versioning-papertrail/)

在 Globalize 的页面上浏览替代解决方案，但是大多数都已经过时了。

## 结论

我们很快就建立了一个完全国际化的网站。对更多语言的支持可以很快很容易地增加——最困难的部分是所有需要的文本的实际翻译。

你在你的项目中实现过类似的功能吗？你用了哪些工具？您是否曾经需要存储用户生成内容的翻译？在评论中分享你的经验吧！

## 分享这篇文章``