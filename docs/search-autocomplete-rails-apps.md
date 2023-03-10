# 在 Rails 应用中搜索和自动完成

> 原文：<https://www.sitepoint.com/search-autocomplete-rails-apps/>

![Adding search to Rails apps](img/8ef7302a7fbef2fff875fbe5362ab2ed.png)

搜索是几乎所有网站上最常见的功能之一。有许多解决方案可以轻松地将搜索整合到您的应用程序中，但在本文中，我将讨论 Postgres 在 Rails 应用程序中的原生搜索，该应用程序由 [pg_search](https://github.com/Casecommons/pg_search) gem 提供支持。最重要的是，我将向您展示如何在 [select2](https://select2.github.io/) 插件的帮助下添加自动完成功能。

我将探索在 Rails 应用程序中使用搜索和自动完成特性的三个例子。具体来说，本文涵盖了:

*   构建基本搜索功能
*   讨论 pg_search 支持的其他选项
*   构建自动完成功能以显示匹配的用户名
*   使用第三方服务根据用户的输入查询地理位置，并使用自动完成功能来支持该功能。

源代码可以在 [GitHub](https://github.com/bodrovis/Sitepoint-source/tree/master/Autocomplete) 找到。

## 入门指南

继续创建一个新的 Rails 应用程序。我将使用 Rails 5.0.1，但是本文中解释的大多数概念也适用于旧版本。只要我们要使用 Postgres 的搜索，应用程序就应该用 PostgreSQL 数据库适配器初始化:

```
rails new Autocomplete --database=postgresql 
```

创建新的 PG 数据库并正确设置`config/database.yml`。为了从版本控制系统中排除我的 Postgres 用户名和密码
，我使用了 [dotenv-rails](https://github.com/bkeepers/dotenv) gem:

*Gemfile*

```
# ...
group :development do
  gem 'dotenv-rails'
end 
```

要安装它，请运行以下命令:

```
$ bundle install 
```

并在项目的根目录下创建一个文件:

*。环境*

```
PG_USER: 'user'
PG_PASS: 'password' 
```

然后从版本控制中排除此文件:

*。gitignore〔t1〕*

```
.env 
```

您的数据库配置可能如下所示:

*config/database.yml*

```
development:
  adapter: postgresql
  database: autocomplete
  host: localhost
  user: < %= ENV['PG_USER'] %>
  password: < %= ENV['PG_PASS'] %> 
```

现在让我们创建一个表，并用示例数据填充它。我不会在这里发明任何复杂的东西，我将简单地生成一个包含`name`和`surname`列的`users`表:

```
$ rails g model User name:string surname:string
$ rails db:migrate 
```

我们的示例用户应该有不同的名字，以便我们可以测试搜索特性。所以我会用 [Faker](https://github.com/stympy/faker) gem:

*Gemfile*

```
# ...
group :development do
  gem 'faker'
end 
```

通过运行以下命令来安装它:

```
$ bundle install 
```

然后调整`seeds.rb`文件，用随机的名字和姓氏创建 50 个用户:

*db/seeds.rb*

```
50.times do
  User.create({name: Faker::Name.first_name,
              surname: Faker::Name.last_name})
end 
```

运行脚本:

```
$ rails db:seed 
```

最后，引入一个根路由、具有相应动作的控制器和一个视图。目前，它将只显示所有用户:

*config/routes.rb*

```
# ...
resources :users, only: [:index]
root to: 'users#index' 
```

*users_controller.rb*

```
class UsersController < ApplicationController
  def index
    @users = User.all
  end
end 
```

*views/users/index . html . erb*

```
<ul>
  < %= render @users %>
</ul> 
```

*views/users/_ user . html . erb*

```
<li>
  < %= user.name %> < %= user.surname %>
</li> 
```

就是这样；一切准备就绪！现在，我们可以进入下一部分，向应用程序添加搜索功能。

## 搜索用户

我想做的是在根页面的顶部显示一个搜索字段，上面写着“输入用户的姓名”和“搜索！”按钮。提交表单时，应该只显示与输入的术语匹配的用户。

从表格开始:

*views/users/index . html . erb*

```
< %= form_tag users_path, method: :get do %>
  < %= text_field_tag 'term', params[:term], placeholder: "Enter user's name or surname" %>
  < %= submit_tag 'Search!' %>
< % end %> 
```

现在我们需要获取输入的术语并进行实际的搜索。这时 [pg_search](https://github.com/Casecommons/pg_search) 宝石开始发挥作用:

*Gemfile*

```
# ...
gem 'pg_search' 
```

不要忘记安装它:

```
$ bundle install 
```

Pg_search 支持两种模式:[多搜索](https://github.com/Casecommons/pg_search#multi-search)和 [pg_search_scope](https://github.com/Casecommons/pg_search#pg_search_scope) 。在本文中，我将使用后一个选项(multisearchable 支持相同的选项，但是允许在多个模型上进行全局搜索)。这个想法很简单:我们创建一个类似于 ActiveRecord 范围的搜索范围。它有一个名称和各种选项，比如搜索什么字段。在此之前，`PgSearch`模块必须包含在模型中:

*型号/用户. rb*

```
# ...
include PgSearch
pg_search_scope :search_by_full_name, against: [:name, :surname] 
```

在本例中，`:name`和`:surname`列将用于搜索。

现在调整控制器的动作并利用新的搜索范围:

*users_controller.rb*

```
# ...
if params[:term]
  @users = User.search_by_full_name(params[:term])
else
  @users = User.all
end 
```

视图本身不需要任何额外的修改。现在可以启动服务器，访问根页面，输入某个用户的名字或姓氏，一切都应该正常工作了。

### 附加选项

我们的搜索工作，但它不是很方便。例如，如果我只输入姓名的一部分，它不会返回任何结果。这可以通过提供附加参数来轻松解决。

首先，你需要选择使用哪种[搜索技术](https://github.com/Casecommons/pg_search#searching-using-different-search-features)。默认的是 Postgres' [全文搜索](https://www.postgresql.org/docs/current/static/textsearch-intro.html)，我将在本文中使用它。另外两个是 [trigram](https://www.postgresql.org/docs/current/static/pgtrgm.html) 和 [metaphone](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html#AEN177521) 搜索，需要安装额外的 PG 扩展。为了设置技术，使用`:using`选项:

```
pg_search_scope :search_by_full_name, against: [:name, :surname], using: [:tsearch] 
```

每种技术也接受不同的选择。例如，让我们启用对[部分单词](https://github.com/Casecommons/pg_search#prefix-postgresql-84-and-newer-only)的搜索:

*型号/用户. rb*

```
class User < ApplicationRecord
  include PgSearch
  pg_search_scope :search_by_full_name, against: [:name, :surname],
    using: {
      tsearch: {
        prefix: true
      }
    } 
```

现在，如果您输入“jay”作为搜索词，您将看到全名包含“jay”的所有用户。请注意，只有从 Postgres 版本 8.4 开始才支持此功能。

另一个有趣的选项是`:negation`，它允许通过添加符号`!`来提供[排除标准](https://github.com/Casecommons/pg_search#negation)。例如，`Bob !Jones`。按如下方式启用该选项:

*型号/用户. rb*

```
class User < ApplicationRecord
  include PgSearch
  pg_search_scope :search_by_full_name, against: [:name, :surname],
    using: {
      tsearch: {
        prefix: true,
        negation: true
      }
    } 
```

但是，请注意，有时该功能可能会产生意想不到的结果。(此处阅读更多[。)](https://github.com/Casecommons/pg_search#negation)

以某种方式突出显示找到的匹配也很酷。[这是可能的](https://github.com/Casecommons/pg_search#highlight)带`:highlight`选项:

*型号/用户. rb*

```
# ...
include PgSearch
pg_search_scope :search_by_full_name, against: [:name, :surname],
  using: {
    tsearch: {
      prefix: true,
      negation: true,
      highlight: {
        start_sel: '<b>',
        stop_sel: '</b>',
      }
    }
  } 
```

这里我们说选择应该用`b`标签包装。高亮功能还有一些其他选项，可以在[这里](https://www.postgresql.org/docs/current/static/textsearch-controls.html)看到。

为了让高亮特性工作，我们需要再做两处修改。首先，像这样链接`with_pg_search_highlight`方法:

*users_controller.rb*

```
# ...
@users = User.search_by_full_name(params[:term]).with_pg_search_highlight 
```

其次，调整局部:

*views/users/_ user . html . erb*

```
<li>
  < % if user.respond_to?(:pg_search_highlight) %>
    < %= user.pg_search_highlight.html_safe %>
  < % else %>
    < %= user.name %> < %= user.surname %>
  < % end %>
</li> 
```

`pg_search_highlight`方法返回我们的搜索字段(`:name`和`:surname`)的连接值，并突出显示找到的匹配。

## 添加自动完成功能

许多网站上的另一个流行功能是自动完成。让我们看看如何用 pg_search 和 [select2](https://select2.github.io/) 插件来制作它。假设我们有一个“发送消息”页面，用户可以选择给谁写信。我们实际上不会构建消息传递逻辑，但是如果你对如何实现感兴趣，请阅读我的文章[用 Rails 和 ActionCable 构建消息传递系统](https://www.sitepoint.com/build-a-messaging-system-with-rails-and-actioncable/)。

将两颗宝石添加到*宝石文件*中:

*Gemfile*

```
# ...
gem 'select2-rails'
gem 'underscore-rails' 
```

Select2 是一个插件，可以将普通的选择字段变成漂亮而强大的控件，而[下划线. js](http://underscorejs.org/) 将提供一些方便的方法。挂接必要的文件:

*JavaScript s/application . js*

```
//= require underscore
//= require select2
//= require messages 
```

一会儿就会创建出`messages.coffee`文件。Select2 还需要一些样式才能正常工作:

*样式表/应用程序. scss*

```
@import 'select2'; 
```

现在让我们快速添加一个新的路线、控制器和视图:

*config/routes.rb*

```
# ...
resources :messages, only: [:new] 
```

*messages_controller.rb*

```
class MessagesController < ApplicationController
  def new
  end
end 
```

*views/messages/new . html . erb*

```
<%= form_tag '' do %>
  < %= label_tag 'to' %>
  < %= select_tag 'to', nil, style: 'width: 100%' %>
< % end %> 
```

表格没有完成，因为它不会被发送到任何地方。注意，最初选择字段没有任何值；它们将根据用户的输入动态显示。

现在是咖啡脚本:

*JavaScript/messages . coffee*

```
jQuery(document).on 'turbolinks:load', ->
  $('#to').select2
    ajax: {
      url: '/users'
      data: (params) ->
        {
          term: params.term
        }
      dataType: 'json'
      delay: 500
      processResults: (data, params) ->
        {
          results: _.map(data, (el) ->
            {
              id: el.id
              name: "#{el.surname}, #{el.name}"
            }
          )
        }
      cache: true
    }
    escapeMarkup: (markup) -> markup
    minimumInputLength: 2
    templateResult: (item) -> item.name
    templateSelection: (item) -> item.name 
```

我不会深入解释这段代码做什么的所有细节，因为本文并不专门讨论 Select2 和 JavaScript。但是，让我们快速回顾一下主要部分:

*   `ajax`表示输入的数据应该异步发送到等待 JSON 响应的`/users`URL(`dataType`)。
*   `delay: 500`表示请求将在用户完成输入后延迟 0.5 秒。
*   `processResults`说明如何处理从服务器接收的数据。我正在构建一个包含用户 id 和全名的对象
    数组。请注意，`id`属性是必需的。
*   `escapeMarkup`覆盖默认的`escapeMarkup`功能，以防止任何转义发生。
*   `minimumInputLength`规定一个用户至少要输入 2 个符号。
*   `templateResult`定义下拉菜单中显示的选项的外观。
*   `templateSelection`定义所选选项的外观。

下一步是调整我们的`users#index`动作，允许它用 JSON 响应:

*users_controller.rb*

```
# ...
def index
  respond_to do |format|
    if params[:term]
      @users = User.search_by_full_name(params[:term]).with_pg_search_highlight
    else
      @users = User.all
    end
    format.json
    format.html
  end
end 
```

最后，观点:

*views/users/index . JSON . jbuilder*

```
json.array! @users do |user|
  json.id user.id
  json.name user.name
  json.surname user.surname
end 
```

请注意，为了实现这一点，需要 [jBuilder](https://github.com/rails/jbuilder) gem。对于 Rails 5 应用程序，默认情况下它存在于 *Gemfile* 中。

到目前为止，一切都很好，但是我们不能做得更好一点吗？还记得上一节中添加突出显示的特性吗？让我们在这里也使用它！我想做的是在下拉列表中突出显示找到的匹配，但不是在选项已经被选中的情况下。因此，我们需要在 JSON 响应中保留`name`和`surname`字段，并引入`full_name`字段:

*views/users/index . JSON . jbuilder*

```
json.array! @users do |user|
  json.id user.id
  json.full_name user.pg_search_highlight.html_safe
  json.name user.name
  json.surname user.surname
end 
```

现在调整 CoffeeScript，将`processResults`改为如下:

```
processResults: (data, params) ->
  {
    results: _.map(data, (el) ->
      {
        name_highlight: el.full_name
        id: el.id
        name: "#{el.surname}, #{el.name}"
      }
    )
  } 
```

同样将`templateResult`改为:

```
templateResult: (item) -> item.name_highlight 
```

注意，如果不覆盖`escapeMarkup`函数，所有输出都将被转义，用于突出显示的`b`标记将被呈现为纯文本。

就是这样。请随意使用这个特性，并进一步扩展它！

## 使用第三方服务自动完成

学生们有时会问我如何构建一个具有自动完成功能的字段，让用户可以选择他们的位置。作为奖励，我将在本节向您展示一个可能的解决方案。为了搜索城市，我将使用[geonames.org](http://geonames.org)，这是一个免费的服务，提供几乎所有的信息:大城市和小城镇、国家、邮政编码、各种历史地点等等。为了开始使用它，你需要做的就是[注册](http://www.geonames.org/login)并在你的配置文件中启用 API 访问。

首先添加新的路线、控制器和视图:

*config/routes.rb*

```
# ...
resources :cities, only: [:index] 
```

*cities_controller.rb*

```
class CitiesController < ApplicationController
  def index
  end
end 
```

*views/cities/index . html . erb*

```
<%= form_tag '' do %>
  < %= label_tag 'city' %>
  < %= select_tag 'city', '', style: 'width: 100%;' %>
< % end %> 
```

CoffeeScript 代码将非常类似于为消息传递页面添加自动完成功能的代码:

*cities.coffee*

```
template = (item) -> item.text

jQuery(document).on 'turbolinks:load', ->
  $('#city').select2
    ajax: {
      url: 'http://ws.geonames.org/searchJSON'
      data: (params) ->
        {
          name: params.term,
          username: 'your_name',
          featureClass: 'p',
          cities: 'cities1000'
        }
      dataType: 'json'
      delay: 500
      processResults: (data, params) ->
        {
          results: _.map(data.geonames, (el) ->
            name = el.name + ' (' + el.adminName1 + ', ' + el.countryName + ')'
            {
              text: name
              id: name
            }
          )
        }
      cache: true
    }
    escapeMarkup: (markup) -> markup
    minimumInputLength: 2
    templateResult: template
    templateSelection: template 
```

发生变化的三个部分是`url`、`data`和`processResults`。至于`url`，我们使用的是 [searchJSON API 方法](http://www.geonames.org/export/geonames-search.html)。这个方法支持各种参数来定义我们想要搜索的对象和类型。他们的选项在`data`属性中传递:

*   `name`是一个必需的参数，可以包含地点的完整或部分名称。
*   `username`表示您的 GeoNames 帐户。
*   `featureClass`:只要 GeoNames 有一个包含各种地理对象的庞大数据库，我们就想缩小
    的搜索范围。[要素类](http://www.geonames.org/export/codes.html) `P`表示我们希望只搜索城市、城镇和村庄。其他功能类别可在[这里](http://www.geonames.org/export/codes.html)找到。
*   我已经把搜索范围限制在至少有 1000 人的城市。这样做是因为有太多同名的小城镇和村庄。例如，我能够找到至少四个名为“莫斯科”的地方。

在`processResults`中，我们获取返回的 geonames 并构造将在下拉列表中显示的字符串。对于每个城市，我们显示其名称，地区和国家。

差不多就是这样，所以现在您可以观察这个特性的运行情况了！

## 结论

在本文中，我们已经看到了 pg_search gem 和 select2 插件的运行。在他们的帮助下，我们构建了搜索功能，并在应用程序中添加了自动完成功能，使其对用户更加友好。Pg_search 提供了更多令人兴奋的东西，所以一定要浏览它的[文档](https://github.com/Casecommons/pg_search)。

你使用什么搜索解决方案？它们的主要特征是什么？在评论中分享你的经验吧！

## 分享这篇文章