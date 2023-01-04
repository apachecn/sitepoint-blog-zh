# 使用 Thinking Sphinx 3.0 进行应用程序搜索

> 原文：<https://www.sitepoint.com/app-search-with-thinking-sphinx-3-0/>

Thinking Sphinx 现在是一个非常标准的库，用于与 Sphinx 接口，并且在实现 Sphinx 的各种功能方面取得了很大的进步。新版本，3.0，是一个重大的重写，尤其是在设置方面。此外，它还内置了相当高级的方面搜索。最后，这个版本只与 Rails 3.1 或更高版本兼容。

### 装置

在 Linux 上安装 Sphinx 非常简单。从源代码下载和编译是 Sphinx 安装的首选方法，尽管 apt 和 yum 存储库几乎是最新的。

Sphinx 的新版本支持 mysql 的多查询，因此最好用 mysql 开发头文件来编译它。

```
$ wget http://sphinxsearch.com/files/sphinx-2.0.7-release.tar.gz
$ tar xvzf sphinx-2.0.7-release.tar.gz
$ cd sphinx-2.0.7-release/
$ ./configure --with-mysql
$ make
$ sudo make install
```

一旦 Sphinx 安装成功，您就可以将 Thinking Sphinx 作为一个 gem 来安装，或者将其捆绑到您的应用程序中。

```
$ gem install thinking-sphinx -v "~> 3.0.2"
```

如果您选择 mysql 作为数据库，您必须确保 mysql2 gem 版本锁定在 0.3.12b4 或 0 . 3 . 12 b5。MySQL 2 gem 的早期版本将抛出以下异常:

```
undefined method `next_result' for Mysql2
```

出现这个错误是因为以前版本的 mysql2 gem 不支持多查询，而 Sphinx 现在已经提供了这种支持。

### 配置

Thinking Sphinx 有两个主要的配置文件。在早期版本中，第一个文件被命名为 sphinx.yml，但已被重命名为 thinking_sphinx.yml。

第二个文件是. sphinx.conf(例如 development.sphinx.conf ),用于连接 sphinx 和数据库。每当创建一个新的索引时，都会创建一个新的 conf 文件，该文件根据 database.yml 中定义的索引和连接细节列出所有 Sphinx 查询。

thinking_sphinx.yml 是该文件的扩展，您可以在其中传递额外的选项来改进您的搜索结果。

### 用例

假设我们有一个具有以下模型的应用程序:

*   供应商-名称、等级
*   商店名称、描述、供应商 id、位置
*   产品–名称、sku、描述、价格、商店标识

具有以下模型关联

*   供应商-有许多商店
*   商店——属于供应商的*,有*多种产品
*   产品-所属商店

我们有以下用例，我们将在文章中介绍:

*   分别搜索供应商、商店和产品
*   在所有模型中搜索
*   通过关联搜索
*   定义面以创建过滤器

### 基础–指数的定义。

与早期版本相比，Thinking Sphinx 3.0 采用了一种更简洁的方法来定义索引。

要开始定义索引，首先需要在 app 文件夹下创建一个名为“indexes”的目录。

```
$ mkdir indices
```

让我们按照我们的用例，在 indexes 文件夹中创建三个索引文件:

*   供应商索引. rb
*   shop_index.rb
*   产品 _ 索引. rb

索引文件定义包括需要搜索的类名。它应该与模型类名相同。

属性的索引可以简单地通过编写以下内容来定义:

```
indexes column_name
```

然而，有几个[为 Sphinx 保留了关键字](http://sphinxsearch.com/docs/2.1.1/sphinxql-reserved-keywords.html)(例如 status)。为了让 Sphinx 接受这些列，您必须将它们定义为一个符号。

```
indexes :status
```

根据我们的用例，让我们为供应商模型编写第一个索引。这将索引供应商名称和评级。我们将使用评级作为参数来对供应商的搜索结果进行排序。

```
ThinkingSphinx::Index.define :vendor, :with => :active_record do
  indexes name
  indexes rating, :sortable => true
end
```

现在，在搜索产品和商店时，我们需要供应商名称作为每次搜索的通用标准。因此，如果我们需要在供应商“漫威玩具”中搜索一个名为“蝙蝠侠活动人偶”的产品，我们可以使用供应商名称进行搜索，并找到与之相关的所有产品。这是如何做到的:

```
ThinkingSphinx::Index.define :vendor, :with => :active_record do
  indexes name, :as => vendor_name
  indexes rating, :sortable => true
end
```

在我们的其他指数中，关联被定义为:

```
shop_index.rb
ThinkingSphinx::Index.define :shop, :with => :active_record do
  indexes name, description, location

  has vendor(:name), :as => :vendor_name
end
```

```
product_index.rb
ThinkingSphinx::Index.define :product, :with => :active_record do
  indexes name, description
  indexes price, :sortable => true

  has shop.vendor.name, :as => :vendor_name
end
```

在上面的两个文件中，我们使用关联调用了`vendor_name`。商店属于一个供应商，所以我们可以从供应商处直接调用它，并将其称为`vendor_name`，而在产品中，我们通过`shop`协会来调用它。

### 运行和生成索引

一旦我们在文件中编写完索引，我们就需要生成一个索引并运行 Sphinx 服务器。

```
$ rake ts:index
$ rake ts:start
```

### 搜索

一旦我们的思维 Sphinx 启动并运行，我们就可以编写控制器方法来搜索和显示结果。

在单个模型上运行搜索如下所示:

```
@search_products = Product.search(params[:search], :ranker => :proximity, :match_mode => :any)
```

如果您想要创建一个应用程序范围的搜索，您可以调用`ThinkingSphinx.search`方法来定义要搜索的模型。您可以将它与任何路线联系起来，并将搜索词作为参数传递。

```
def search
   @search =  ThinkingSphinx.search(params[:search],
                             :classes => [ Vendor, Store, Product],
                             :ranker => :bm25,
                             :match_mode => :any,
                             :order => '@weight DESC',
                             :page => params[:page],
                             :per_page => 10)
end
```

排名使用不同的算法，如 [bm25](http://xapian.org/docs/bm25.html) 和邻近度，在搜索结果中生成最佳匹配。

排序使用顺序字段，并且可以定义为以不同的方式对结果进行排序。基于评级，按字母顺序或 created_at 只是它可以排序的一些方式。

@weight 是包含 Sphinx 排名值的默认关键字。

### 其他东西

字段权重:
我们可以根据特定模型中的不同字段对搜索结果进行排名:

```
@search_products = Product.search(params[:search],
                                 :ranker => :proximity,
                                 :match_mode => :any,
                                 :field_weights => {:description => :15,
                                                    :name => 10})
```

您还可以使用如下的`set_property`规则在索引文件中写入字段权重:

```
:set_property :field_weights => {:description => 15,
                                 :name => 10})
```

从搜索结果中获取摘录:

```
@excerpter = ThinkingSphinx::Excerpter.new 'product_core', params[:search], { :before_match => '<span class="match">',
 :after_match => '</span>',
 :chunkseparator => ' … '}
```

添加方面:

当您需要根据不同的类或参数进行过滤时，可以使用基于方面的搜索。可以通过简单地针对索引文件中的属性定义一个`facet => true`规则来定义方面。

```
indexes name, :facet => true
```

然后重建索引。

```
rake ts:rebuild
```

在您的控制器中，`facet`接受与`search`相同的参数，因此您的刻面将如下所示:

```
@facets = ThinkingSphinx.facets(params[:search],
                                :class_facet => false,
                                :page => params[:page],
                                :per_page => 10)
```

如果没有在任何模型上定义方面，并且编写了方面搜索，那么它将退回到类方面，在单个模型类上调用搜索。您可以通过在 facet 规则中调用 false 来关闭它，如上所示。

可以按以下方式在视图中显示多面结果:

```
&lt;% @facets[:class].each do |option, count|%>
&lt;%= link_to "#{option} (#{count})", :params => { :facet => option, :page => 1}%>
&lt;%end%><br/>
```

可以在 thinking_sphinx.yml 中使用`min_infix_len`来搜索部分单词，或者在索引文件中使用`set_property`来搜索部分单词。这意味着在声明匹配之前，它将匹配至少 3 个字符。`min_infix_len`不建议保持在 1，因为它可能是一个严重的内存猪。

```
development:
   mem_limit: 128M
   min_infix_len: 3
 test:
   mem_limit: 128M
   min_infix</em>len: 3
 production:
   mem_limit: 128M
   min_infix_len: 3
```

## 增量指数

默认情况下，每次运行 index 命令时，Sphinx 都会从头开始运行索引。为了避免 Sphinx 从头开始，我们可以定义 delta 索引。这将仅索引新创建的文档。

```
set_property :delta => true
```

## 结论

Thinking Sphinx 3.0 带来了以前版本的许多改进和错误修复。它采用一种非常简洁的方法，将搜索代码放在应用模型之外。因此，当查询开始变得复杂时，代码仍然可读。

希望这篇文章能够启发您在应用程序中尝试一下 Thinking Sphinx。

## 分享这篇文章