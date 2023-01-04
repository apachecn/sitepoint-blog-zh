# 使用 ElasticSearch 在 Rails 中进行全文搜索

> 原文：<https://www.sitepoint.com/full-text-search-rails-elasticsearch/>

![Search icon](img/1ecb7d13b3c90e6dcfb1be632bbc5e6e.png)

在本文中，您将学习如何将 ElasticSearch 集成到 Rails 应用程序中。

全文搜索引擎在尝试匹配搜索标准(用户指定的文本)时，会检查每个存储文档中的所有单词。例如，如果您想要查找关于 rails 的文章，您可以使用术语“Rails”进行搜索。如果没有专门的索引技术，就意味着要全面扫描所有记录来寻找匹配，效率会非常低。解决这个问题的一种方法是“倒排索引”,它将所有记录内容中的单词映射到它在数据库中的位置。

例如，如果主键索引是这样的:

```
article#1 -> "breakthrough drug for schizophrenia"
article#2 -> "new schizophrenia drug"
article#3 -> "new approach for treatment of schizophrenia"
article#4 -> "new hopes for schizophrenia patients"
...
```

这些记录的倒排索引如下所示:

```
breakthrough    -> article#1
drug            -> article#1, article#2
schizophrenia   -> article#1, article#2, article#3, article#4
approach        -> article#3
new             -> article#2, article#3, article#4
hopes           -> article#4
...
```

现在，搜索单词“药品”使用倒排索引，并直接返回*文章#1* 和*文章#2* 。

如果你想了解更多这方面的知识，我推荐这本 [IR 书](http://www-nlp.stanford.edu/IR-book/)。

## 构建文章应用程序

我们将从 Rails 指南使用的著名的[博客示例](http://guides.rubyonrails.org/getting_started.html)开始。

### 创建 Rails 应用程序

在命令提示符下键入以下内容:

```
$ rails new blog
$ cd blog
$ bundle install
$ rails s
```

### 创建文章控制器

使用 Rails 生成器创建文章控制器，将路由添加到 **config/routes.rb** ，并添加显示、创建和列出文章的方法。

```
$ rails g controller articles
```

然后打开 **config/routes.rb** 并添加该资源:

```
Blog::Application.routes.draw do
  resources :articles
end
```

现在，打开**app/controllers/articles _ controller . Rb**并添加创建、查看和列出文章的方法。

```
def index
  @articles = Article.all
end

def show
  @article = Article.find params[:id]
end

def new
end

def create
  @article = Article.new article_params
  if @article.save
    redirect_to @article
  else
    render 'new'
  end
end

private
  def article_params
    params.require(:article).permit :title, :text
  end
```

### 物品模型

我们需要一个文章模型，所以生成如下:

```
$ rails g model Article title:string text:text
$ rake db:migrate
```

### 视图

#### 新文章形式

在**app/views/articles/new . html . erb**处创建一个新文件，内容如下:

```
<h1>New Article</h1>  

<%= form_for :article, url: articles_path do |f| %>

  <% if not @article.nil? and @article.errors.any? %>
  <div id="error_explanation">
    <h2><%= pluralize(@article.errors.count, "error") %> prohibited
      this article from being saved:</h2>
    <ul>
    <% @article.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
    </ul>
  </div>
  <% end %>

  <p>
    <%= f.label :title %><br>
    <%= f.text_field :title %>
  </p>

  <p>
    <%= f.label :text %><br>
    <%= f.text_area :text %>
  </p>

  <p>
    <%= f.submit %>
  </p>
<% end %>

<%= link_to '<- Back', articles_path %>
```

#### 显示一篇文章

在**app/views/articles/show . html . erb**处创建另一个文件:

```
<p>
  <strong>Title:</strong>
  <%= @article.title %>
</p>

<p>
  <strong>Text:</strong>
  <%= @article.text %>
</p>

<%= link_to '<- Back', articles_path %>
```

#### 列出所有文章

在**app/views/articles/index . html . erb**处创建第三个文件:

```
<h1>Articles</h1>

<ul>
  <% @articles.each do |article| %>
    <li>
      <h3>
        <%= article.title %>
      </h3>
      <p>
        <%= article.text %>
      </p>
    </li>
  <% end -%>
</ul>
<%= link_to 'New Article', new_article_path %>
```

您现在可以添加和查看文章。确保启动 Rails 服务器并转到[http://localhost:3000/articles](http://localhost:3000/articles)。点击“新文章”，添加几篇文章。这些将用于测试我们的全文搜索能力。

## 集成弹性搜索

目前，我们只能通过 *id* 找到一篇文章。集成 ElasticSearch 将允许通过标题或文本中的任何单词查找文章。

### 为 Ubuntu 和 Mac 安装

#### 人的本质

去[elasticsearch.org/download](http://www.elasticsearch.org/download/)下载 **DEB** 文件。一旦文件位于本地，请键入:

```
$ sudo dpkg -i elasticsearch-[version].deb
```

#### 苹果个人计算机

如果你用的是 Mac，自制软件会让它变得简单:

```
$ brew install elasticsearch
```

#### 验证安装

打开这个网址: [http://localhost:9200](http://localhost:9200) 你会看到 ElasticSearch 是这样回复的:

```
{
  "status" : 200,
  "name" : "Anvil",
  "version" : {
    "number" : "1.2.1",
    "build_hash" : "6c95b759f9e7ef0f8e17f77d850da43ce8a4b364",
    "build_timestamp" : "2014-06-03T15:02:52Z",
    "build_snapshot" : false,
    "lucene_version" : "4.8"
  },
  "tagline" : "You Know, for Search"
}
```

### 添加基本搜索

创建一个名为`search`的控制器，以及一个视图，这样您就可以做类似于: */search？q =红宝石*。

#### Gemfile

```
gem 'elasticsearch-model'
gem 'elasticsearch-rails'
```

记得运行`bundle install`来安装这些 gem。

#### 搜索控制器

创建`SearchController`:

```
$ rails g controller search
```

将此方法添加到**app/controller/search _ controller . Rb**:

```
def search
  if params[:q].nil?
    @articles = []
  else
    @articles = Article.search params[:q]
  end
end
```

### 将搜索整合到文章中

要将 ElasticSearch 集成添加到文章模型中，需要`elasticsearch/model`并将主模块包含在`Article`类中。

修改 **app/models/article.rb** :

```
require 'elasticsearch/model'

class Article < ActiveRecord::Base
  include Elasticsearch::Model
  include Elasticsearch::Model::Callbacks
end
Article.import # for auto sync model with elastic search
```

#### 搜索视图

在**app/views/search/search . html . erb**创建一个新文件:

```
<h1>Articles Search</h1>

<%= form_for search_path, method: :get do |f| %>
  <p>
    <%= f.label "Search for" %>
    <%= text_field_tag :q, params[:q] %>
    <%= submit_tag "Go", name: nil %>
  </p>
<% end %>

<ul>
  <% @articles.each do |article| %>
    <li>
      <h3>
        <%= link_to article.title, controller: "articles", action: "show", id: article._id%>
      </h3>
    </li>
  <% end %>
</ul>
```

#### 搜索路线

将搜索路线添加到 *_config/routes.rb* -:

```
get 'search', to: 'search#search'
```

你现在可以进入[http://localhost:3000/search](http://localhost:3000/search)，在你创建的文章中搜索任意单词。

## 加强搜索

你可能会注意到你的搜索引擎有一些限制。比如搜索一个词的一部分，比如*“rub”*或者*“roby”*而不是*“ruby”*，会给你零结果。此外，如果搜索引擎给出的结果包含与你的搜索词相似的词，那就更好了。

ElasticSearch 提供了许多功能来增强您的搜索。我举几个例子。

### 自定义查询

我们可以使用不同类型的查询。到目前为止，我们只是使用默认的 ElasticSearch 查询。为了增强搜索结果，我们需要修改这个默认查询。例如，我们可以给予像 *title* 这样的字段比其他字段更高的优先级。

ElasticSearch 提供了基于 JSON 的全查询 DSL 来定义查询。一般来说，有一些基本的查询，比如术语或前缀。还有复合查询，如 bool 查询。查询还可以有与之关联的过滤器，例如 filtered 或 constant_score 查询。

让我们在 **app/models/article.rb** 中为我们的文章模型添加一个自定义搜索方法:

```
def self.search(query)
  __elasticsearch__.search(
    {
      query: {
        multi_match: {
          query: query,
          fields: ['title^10', 'text']
        }
      }
    }
  )
end
```

*注意:当搜索词在标题中匹配时，^10 会增加 10 的命中分数*

### 自定义映射

映射是定义文档应如何映射到搜索引擎的过程，包括其可搜索的特征，如哪些字段是可搜索的以及它们是否/如何被标记化。

显式映射是在索引/类型级别定义的。默认情况下，不需要定义显式映射，因为当引入新类型或新字段时，会自动创建并注册一个显式映射(没有性能开销),并且有合理的默认值。只有当需要覆盖默认值时，才必须提供映射定义。

我们将改进搜索，以便您可以搜索类似于*、【搜索】、*的术语，并获得还包括*、*和*、【搜索】、*的结果..等等。这将使用 ElasticSearch 中内置的英语分析器在索引前应用[词干](http://xapian.org/docs/stemming.html)。

将此映射添加到文章类:在 **app/models/article.rb**

```
settings index: { number_of_shards: 1 } do
  mappings dynamic: 'false' do
    indexes :title, analyzer: 'english'
    indexes :text, analyzer: 'english'
  end
end
```

最好在文件末尾添加以下几行，以便在加载 **article.rb** 时自动删除并重新填充索引:

```
# Delete the previous articles index in Elasticsearch
Article.__elasticsearch__.client.indices.delete index: Article.index_name rescue nil

# Create the new index with the new mapping
Article.__elasticsearch__.client.indices.create \
  index: Article.index_name,
  body: { settings: Article.settings.to_hash, mappings: Article.mappings.to_hash }

# Index all article records from the DB to Elasticsearch
Article.import
```

### 搜索突出显示

基本上，我们希望显示文章中出现我们要搜索的术语的部分。这就像当你在谷歌搜索时，你会看到一个文档样本，其中包括你用粗体显示的术语。在 ElasticSearch 中，这被称为“高光”。我们将在查询中添加一个*突出显示*参数，并指定我们想要突出显示的字段。ElasticSearch 将返回一个 **标签之间的术语，以及术语前后的几个单词。**

 **假设我们正在搜索术语*“open source”*，ElasticSearch 将返回如下内容:

```
Elasticsearch is a flexible and powerful <em>opensource</em>, distributed, real-time search and analytics
```

注意，*“开源”*被一个 **标签包围。**

 **#### 向 SearchController 添加突出显示

首先，向 ElasticSearch 查询添加“highlight”参数:

```
def self.search(query)
  __elasticsearch__.search(
    {
      query: {
        multi_match: {
          query: query,
          fields: ['title^10', 'text']
        }
      },
      highlight: {
        pre_tags: ['<em>'],
        post_tags: ['</em>'],
        fields: {
          title: {},
          text: {}
        }
      }
    }
  )
end
```

#### 在视图中显示突出显示

在视图中显示这个高光非常容易。转到**app/views/search/search . html . erb**，将`ul`元素替换为:

```
<ul>
  <% @articles.each do |article| %>
    <li>
      <h3>
        <%= link_to article.try(:highlight).try(:title) ? article.highlight.title[0].html_safe : article.title,
          controller: "articles",
          action: "show",
          id: article._id%>
      </h3>
      <% if article.try(:highlight).try(:text) %>
        <% article.highlight.text.each do |snippet| %>
          <p><%= snippet.html_safe %>...</p>
        <% end %>
      <% end %>
    </li>
  <% end %>
</ul>
```

现在在**app/assets/style sheets/search . CSS . scss**中为*添加一个样式:*

```
em {
  background: yellow;
}
```

我们需要的最后一件事是 ElasticSearch 返回的突出显示的术语被几个词包围起来。如果您需要从头显示标题，请将`index_options: 'offsets'`添加到标题映射中:

```
settings index: { number_of_shards: 1 } do
  mappings dynamic: 'false' do
    indexes :title, analyzer: 'english', index_options: 'offsets'
    indexes :text, analyzer: 'english'
  end
end
```

这是将 ElasticSearch 集成到 Rails 应用程序中的一个简单例子。我们添加了基本的搜索，然后使用自定义查询、映射和高亮来混合一些东西。你可以从这里下载完整的源代码

## 参考

*   [elastic search/elastic search-rails](https://github.com/elasticsearch/elasticsearch-rails)
*   [rails-应用-模板](https://github.com/elasticsearch/elasticsearch-rails/tree/master/elasticsearch-rails#rails-application-templates)
*   [映射指南](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/mapping.html)
*   [查询-DSL 指南](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl.html)
*   [rubyonrails.org/getting_started.html](http://guides.rubyonrails.org/getting_started.html)

## 分享这篇文章*****