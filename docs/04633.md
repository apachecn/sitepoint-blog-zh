# 带搜索的高级搜索

> 原文：<https://www.sitepoint.com/advanced-search-ransack/>

![search flat icon, christmas button](img/c525bf49d8cb54b208128a58d2f69447.png)

在本教程中，我们将探索如何在 Rails 应用程序中添加复杂的搜索功能。这项任务将变得更加容易，由真棒洗劫红宝石宝石。搜索提供了优秀的助手和建设者处理先进的搜索你的模型。它有一些真正强大的功能，无需编写大量代码就可以开箱即用，比如排序或条件搜索。

我们将使用在 [**布伦特里系列**](https://www.sitepoint.com/integrate-braintree-payments-rails/) 中使用的相同应用程序。请参考帖子来设置和设计基本应用程序。这是我们将在应用程序[中使用的资源库的一个分支。](https://github.com/wazery/moviestore-ransack)

## 目标

下面是我们将要实现的功能列表:

*   按标题搜索电影
*   按价格范围搜索电影
*   按标题、价格、发行年份、升序或降序排序
*   搜索电影的任何(模型)属性，选项如等于，大于，小于等。
*   按任何模型属性排序

你可以在 Heroku 上查看这个应用程序的工作演示

## 探索洗劫提供演示

**ActiveRecordHackery** 有自己的使用洗劫宝石[的演示应用这里](http://ransack-demo.herokuapp.com/)。它提供了两种搜索模式:基本和高级。在基本模式下，您只能按名字、姓氏、电子邮件或文章标题进行搜索。在高级模式下，您有很多选项来定制搜索。尝试它们来发现搜索在复杂搜索中提供的能力。

## 工具

*   **红宝石**`(2.1.0)`–编程语言
*   **轨道**`(4.1.1)`–后端框架
*   **基础 5**–前端 CSS 框架
*   **洗劫**–红宝石
*   **设计**–用户认证

### 步骤 1:添加搜索

第一步是将洗劫宝石添加到您的宝石文件中:

```
gem 'ransack'
```

确保运行`bundle install`。

### 步骤 2:添加搜索对象

在最常见的用例中，在模型上调用搜索提供的`search`方法，传递搜索参数:

```
@search = Movie.search(params[:q])
```

结果可以存储在`@movies`实例变量中:

```
@movies = @search.result
```

整个索引操作如下所示:

```
def index
  @search = Movie.search(params[:q])
  @movies = @search.result
end
```

### 步骤 3:添加搜索表单

让我们创建一个表单来处理搜索。该表单只需要一个文本字段(电影标题)和一个提交按钮。表单可以放在网站标题的正下方。

在应用程序布局的 header 部分添加一个`yield`语句。这个`yield`的内容在索引视图中创建。

打开**layouts/application . html . erb**文件并添加:

```
<%= yield(:search) %>
```

在标题部分。然后，在索引页面中，添加简单的搜索表单，如下所示:

```
<% content_for :search do %>
  <div class="large-8 small-9 columns">
  <%= search_form_for @search do |f| %>
    <%= f.text_field :title_cont, class: "radius-left expand", placeholder: "Movie title" %>
  </div>
    <div class="large-4 small-3 columns">
      <%= f.submit "Search", class: "radius-right button" %>
    </div>
  <% end %>
<% end %>
```

如您所见，我们使用了 sorack 的助手方法`search_form_for`来创建我们的搜索表单，并传递了之前创建的`search`对象。它包含一个带有符号`:title_cont`的`text_field`，告诉搜索搜索一个*包含*某个值的标题。这里的 *cont* 称为谓词，它是搜索确定匹配什么信息的方式。

作为一个简单的重构，我将这个内容移动到一个单独的部分(**_ title _ search _ box . html . erb**)中，以便可以重用。在索引页中添加一行来呈现这部分内容:

```
<%= render "title_search_box" %>
```

**注意:**screave 也有一个标签的助手，但是我们的表单中并不需要它。

![((screenshot))](img/c5e27b94520e15092dd53cb8bcd9ca31.png)

### 步骤 4–1:添加价格范围

在上一步中，我们创建了简单的搜索功能。让我们尝试一些更高级的东西，比如通过提供最小值和最大值来搜索电影价格范围。

这是我们要实现的目标:

![((screenshot-2))](img/601866ce12d2dd923f8b6208839d32a8.png)

![((screenshot-3))](img/107df6f16a533cdad926c22270751fa0.png)

我们将从搜索框样式开始，从 MovieStore 应用程序借用一些 CSS 类。 **form-container 和 glass-BG**类(分别位于 **layout.css.scss** 和 **helpers.scss** 中)为搜索框添加了玻璃背景。我用类`column`创建了一个新的 div 来包含高级搜索框。盒子最初是隐藏的。

```
<div class="column">
  <div class="advanced-search hide form-container glassy-bg columns">
    <a class="close-advanced-search fi-x"></a>
  </div>
</div>
```

添加一个按钮，单击该按钮将打开高级搜索框，如下所示:

```
<div class="column">
  <h5>
    <a class="show-advanced-search">Advanced Search <span class="fi-plus"/></a>
  </h5>
</div>
```

现在，在 **movies.js.coffee** 中，为这个链接添加`click`事件，显示我们的高级搜索框:

```
$ ->
  $('.show-advanced-search').click ->
    $('.advanced-search').show() // which removes the hide class
    $(this).hide()
```

处理关闭高级搜索框`click`事件，同样:

```
$('.close-advanced-search').click ->
    $('.advanced-search').hide()
    $('.show-advanced-search').show()
```

### 步骤 4–2:添加价格范围

是时候将价格范围添加到搜索表单中了。我们将使用相同的`search_form_for`方法来创建带有相同的`@search`实例变量的表单。

在下面的代码中，`:price_gteq`和`price_lteq`分别代表*价格大于等于*和*价格小于等于*。在这里查看可用谓词[的列表](https://github.com/activerecord-hackery/ransack/wiki/Basic-Searching)

```
<h4>Advanced Search</h4>
<%= search_form_for @search do |f| %>
  <div class="large-5 small-4 columns">
    <%= f.label :price_gteq, class: "movie-label" %>
    <%= f.text_field :price_gteq, class: "radius", placeholder: "Minumum Price" %>
  </div>
  <h6 class="large-2 small-4 columns center"><span>And</span></h6>
  <div class="large-5 small-4 columns">
    <%= f.label :price_lteq, class: "movie-label" %>
    <%= f.text_field :price_lteq, class: "radius", placeholder: "Maximum Price" %>
  </div>
  <div class="column">
    <%= f.submit "Search", class: " radius button" %>
  </div>
<% end %>
```

![((screenshot-4))](img/4028756b03ce7ac15936734c9e6e5830.png)

### 步骤 5:添加排序

scorave 有一个表单生成器，它提供了按升序或降序对列进行排序的链接。这个建造者是`sort_link`。它使用起来非常简单，只需传递搜索对象和想要排序的列名(属性)以及一个占位符。

我已经覆盖了**Foundation _ and _ overrides . scss**文件中的三个基础标签。

```
<h4 class="column">Featured Movies</h4>
<div class="row right padm">
  <div class="column">
    <div class="filter-label red">
      <%= sort_link @search, :title, "Title" %>
    </div>
    <div class="filter-label dark-golden-rod">
      <%= sort_link @search, :price, "Price" %>
    </div>
    <div class="filter-label dark-slate-gray">
      <%= sort_link @search, :release_year, "Release Year" %>
    </div>
  </div>
</div>
```

![((screenshot-5))](img/b7664a43ed76d1d82da46e57d94dad11.png)

### 步骤 6:添加条件搜索

在这一步，让我们探索如何添加条件搜索字段。制作一个包含所有电影属性的选择框和一个包含所有谓词的选择框。最后的输入将是一个文本字段，用于输入在搜索中使用的值。对于这种情况，sorava 有一些表单生成器，如下面的代码所示。

只是为了好玩，我添加了一些排序功能，但是是以选择的形式。一个下拉菜单用于模型属性，另一个是顺序(升序或降序):

```
<h4>Advanced Search</h4>
<%= search_form_for @search do |f| %>
  <%= f.condition_fields do |c| %>
    <div class="large-4 small-4 columns">
      <%= c.attribute_fields do |a| %>
        <%= a.attribute_select nil, class: "radius" %>
      <% end %>
    </div>
    <div class="large-4 small-4 columns">
      <%= c.predicate_select Hash.new, class: "radius" %>
    </div>
    <div class="large-4 small-4 columns">
      <%= c.value_fields do |v| %>
        <%= v.text_field :value, class: "radius" %>
      <% end %>
    </div>
  <% end %>
  <h5>Sort</h5>
  <div class="column">
  <%= f.sort_fields do |s| %>
    <%= s.sort_select Hash.new, class: "large-5 small-4 columns mrs radius" %>
  <% end %>
  </div>
  <%= f.submit "Search", class: "radius button" %>
<% end %>
```

![((screenshot-6))](img/9d3e3b1cc12d44a8c24d7b279bb49fd4.png)

### 步骤 7:重构搜索

将我们的代码移到名为 **condition_fields** 的分部是一个好主意:

*_ condition _ fields . html . erb*

```
<div class="field">
  <div class="large-4 small-4 columns">
    <%= f.attribute_fields do |a| %>
      <%= a.attribute_select nil, class: "radius" %>
    <% end %>
  </div>
  <div class="large-4 small-4 columns">
    <%= f.predicate_select Hash.new, class: "radius" %>
  </div>
  <div class="large-4 small-4 columns">
    <%= f.value_fields do |v| %>
      <%= v.text_field :value, class: "radius" %>
    <% end %>
    <%= link_to "remove", "#", class: "remove_fields" %>
  </div>
</div>
```

### 步骤 8:添加标准的链接

让我们在高级搜索框中添加一个链接来添加条件。在 **index.html.erb** 中，添加一个添加标准的链接，如下所示:

```
<%= f.condition_fields do |c| %>
  <%= render "condition_fields", f: c %>
<% end %>
<p><%= link_to_add_fields "Add Conditions", f, :condition %></p>
```

首先，在应用程序助手中创建`link_to_add_fields`助手方法:

```
def link_to_add_fields(name, f, type)
  new_object = f.object.send "build_#{type}"
  id = "new_#{type}"
  fields = f.send("#{type}_fields", new_object, child_index: id) do |builder|
    render(type.to_s + "_fields", f: builder)
  end
  link_to(name, '#', class: "add_fields", data: {id: id, fields: fields.gsub("\n", "")})
end
```

在 **movies.js.coffee** 中，为`add_fields`类处理`click`事件:

```
$('form').on 'click', '.add_fields', (event) ->
  time = new Date().getTime()
  regexp = new RegExp($(this).data('id'), 'g')
  $(this).before($(this).data('fields').replace(regexp, time))
  event.preventDefault()
```

### 步骤 9:删除标准的链接

明智的做法是添加一个链接来删除条件。将其添加到**条件字段**部分，伴随每个条件:

```
<div class="large-4 small-4 columns">
  <%= f.value_fields do |v| %>
    <%= v.text_field :value, class: "radius" %>
  <% end %>
  <%= link_to "", "#", class: "remove_fields fi-x" %>
</div>
```

回到 **movies.js.coffee** ，必要的`click`事件处理程序:

```
$('form').on 'click', '.remove_fields', (event) ->
  $(this).closest('.field').remove()
  event.preventDefault()
```

### 步骤 10:发布请求

如果我们添加太多的条件，参数的数量可能会达到 GET 请求所允许的极限。这可以通过使用 POST 来避免。

在 **config/routes.rb** 文件中添加:

```
resources :movies, only: [:show, :index] do
  match :search, to: 'movies#index', via: :post, on: :collection
end
```

然后，在 **index.html.erb** 中，更改搜索表单以使用新的搜索路径，如下所示:

```
<%= search_form_for @search, url: search_movies_path, method: :post do |f| %>
```

### 摘要

在本教程中，我们使用了在“[使用 Rails](https://www.sitepoint.com/build-online-store-rails/) 构建在线商店”中使用的相同在线电影商店应用程序来添加复杂的搜索功能。通过使用**搜索**宝石，电影应用程序现在有一个简单和复杂的搜索界面。

## 分享这篇文章