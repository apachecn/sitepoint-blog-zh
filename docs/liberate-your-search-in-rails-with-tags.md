# 用标签解放你的搜索

> 原文：<https://www.sitepoint.com/liberate-your-search-in-rails-with-tags/>

![](img/d0b2a4f2b786bc71075a6b0e56a794fb.png)

搜索是任何网站必不可少的一部分，对于一个以内容为中心的网站更是如此。一个好的搜索系统是一个提供准确结果的快速系统。搜索和过滤基本上是一样的，这取决于你如何看待它。在本文中，我将把搜索和过滤视为同一种功能。

从我们使用的工具中有许多现有的方法来实现搜索，比如 [PostgreSQL 全文搜索](http://www.postgresql.org/docs/9.1/static/textsearch-intro.html)，你可以[在 Rails](https://github.com/Casecommons/pg_search) 中使用它。然而，今天我们将看到如何使用 PostgreSQL 在 Rails 应用程序中实现基于标记的过滤。

让我们开始吧。

## 关于实现

让我们看看基于标签的实现是如何工作的。假设我们有一个名为`companies`的表，它有以下关系关联链:

```
companies -> BELONGS_TO -> cities -> BELONGS_TO -> states -> BELONGS_TO -> countries 
```

`companies`表有`yearly_revenue`、`employee_strength`、`started_year`等栏目。

假设我们有一个公司页面，页面上有过滤器。我们现在想过滤某个国家的所有公司。我们可以通过连接表并根据提供的国家 ID 进行过滤来实现这一点，非常简单。但是，如果过滤器组合在一起，变得更加复杂，该怎么办呢？

例如，我们需要以下过滤器:

```
Companies that are in New York and started before 2001 and have revenue of more than $200 million and employ more than 1000 people. 
```

这仍然是可以实现的，但是查询变长了，而且很可能变得有点难看。真正的问题是方便用户选择过滤器。一种方法是为我们允许用户执行的每个过滤器放置相关元素(文本框和选择)，当我们想要包含越来越多的过滤器时，这就成了一个问题。如果我们想对信息进行 50 种不同的过滤，那么每个过滤器使用一个元素就不太好了。此外，在查询部分，考虑来自 10 个不同方向的公司的关系，每个关系都嵌套了自己的一组关联？我相信你能看到事情变得有多糟糕。这就是标签解决方案更好的答案。

在基于标记的实现中，我们基本上是将一个具有数组数据类型的`tag`列添加到我们想要执行过滤的表中。在我们的例子中，这是`companies`表。该列由一个通用唯一标识符(UUIDs)数组组成，它对应于与记录相关的标签的 id。我们将有一个名为`tags`的单独的表，由标签`id`、`name`和`type`组成。现在，搜索和过滤就像在`tags`表上搜索一样简单，找到 UUIDs，然后根据这些标签 id 过滤公司。

让我们借助一个例子来实现这一点。

## 示例 Rails 应用程序

我们的 Rails 应用程序将有五个表格

*   公司
*   城市
*   州
*   国家
*   标签

首先创建 Rails 应用程序和必要的模型:

```
rails new tag-example -d postgresql 
```

为了使标记实现工作得最好，我们将坚持在所有表中使用 UUID 作为主键，所以让我们创建一个迁移来启用 PostgreSQL 的`uuid-ossp`扩展:

```
rails g migration enable_uuid_ossp 
```

生成迁移文件后，将以下命令添加到文件中:

```
enable_extension "uuid-ossp" 
```

运行迁移:

```
rake db:create && rake db:migrate 
```

创建我们的模型，从公司开始:

```
rails g scaffold companies name founding_year:integer city_id:uuid 
```

正如我上面提到的，我们需要维护`id`字段作为表的 UUID。转到`create_companies`迁移文件，修改`create_table`行，指定`id`列应该是一个`uuid`。PostgreSQL 将负责自动生成 UUIDs。另外，添加一行将`tags`列添加到`companies`表中，因为将它包含在 scaffold 中会使它显示给用户。

```
create_table :companies, id: :uuid do |t|
    t.string :tags, array: true, default: [] 
```

接下来，创建城市、州和国家模型，如上所述，通过修改迁移，每个模型都使 ID 成为 UUID:

```
rails g model cities name state_id:uuid
rails g model states name country_id:uuid
rails g model countries name
rails g model tags name tag_type 
```

运行迁移后，在模型文件中快速建立关系:

```
## company.rb
belongs_to :city

## city.rb
has_many :companies
belongs_to :state

## state.rb
has_many :cities
belongs_to :country

## country.rb
has_many :states 
```

如果我们生成一些种子数据，这个例子会更清楚。在这个练习中，我使用 [faker](https://github.com/stympy/faker) gem 来生成公司、城市和州的信息。这里是我用过的 seed ( **/db/seed.rb** )文件，你可以参考这个；

```
(1..10).each do |i|
  country = Country.create(name: Faker::Address.country)
  (1..10).each do |j|
    state = State.create(name: Faker::Address.state, country: country)
    (1..10).each do |k|
      city = City.create(name: Faker::Address.city, state: state)
    end
  end
end

City.all.each do |city|
  (1..10).each do |count|
    company_name = Faker::Company.name
    Company.create(name: company_name, founding_year: rand(1950..2015), city: city)
    p "Saved - #{company_name} - #{count}"
  end
end 
```

现在，让我们将我们的`before_save`回调添加到 companies 模型中，它将在每次保存时为公司生成标签。这也负责在 tags 表中创建条目。在 **models/company.rb** 中添加以下代码:

```
class Company < ActiveRecord::Base
  belongs_to :city

  before_save :update_tags

  def save_location_tags
    [city, city.state, city.state.country].map do |loc|
      (Tag.it({id: loc.id, name: loc.name, tag_type: 'LOCATION'}).id rescue nil)
    end
  end

  def update_tags
    self.tags = [
      save_location_tags,
      (Tag.it({name: founding_year, tag_type: 'COMPANY_FOUNDING_YEAR'}).id rescue nil)
    ].flatten.compact
  end
end 
```

上面的代码实际上会在每次保存时重新生成和更新公司标签。在 **apps/models/tag.rb** 中，添加以下内容:

```
class Tag < ActiveRecord::Base
  def self.it content
    Tag.where(content).first_or_create! rescue nil
  end
end 
```

您可以向`update_tag`方法添加任意数量的标签。从数据更新的角度来看，我们都准备好了。现在让我们实现标签搜索和过滤。

## 标签过滤器

我们将有一个页面显示公司列表，并在标签名称的输入上有一个`autocomplete`。此外，每当用户做出选择时，代码将调用过滤器，并显示当前选择的过滤器。

在**app/controllers/companies _ controller . Rb**中，将以下几行添加到`index`动作中:

```
def index
  if params[:tags]
    @companies = Company.tagged(params[:tags])
  else
    @companies = Company.all.limit(10)
  end
end 
```

在 **app/models/company.rb** 中，添加标记范围如下:

```
scope :tagged, -> (tags) {where('companies.tags @> ARRAY[?]::varchar[]', [tags].flatten.compact)} 
```

这个查询接受一个标签数组，并过滤所有给定数组出现在它的`tags`列中的记录。

我们现在将使用`pg_trgm`扩展来为标签创建自动完成功能。就像另一个扩展一样，我们需要一个迁移并启用它:

```
enable extension "pg_trgm" 
```

让我们创建一个标记控制器，并向它添加一个自动完成端点，我们将从前端使用它来为用户查询获取匹配的标记 id:

```
rails g controller tags autocomplete 
```

将以下代码添加到标签控制器中的`autocomplete`方法中:

```
def autocomplete
  results = Tag.select("*, string <-> #{ActiveRecord::Base.sanitize(params[:q])} as distance").order('distance').limit(5)
  render json: results
end 
```

现在我们有了一个完整的基于标记的过滤器端点和标记自动完成端点的工作集。您可以通过启动 Rails 服务器(`rails s`)并尝试以下端点来检查它们:

*   标签自动完成示例:http://localhost:3000/tags/autocomplete？q =端口
*   根据标签过滤公司示例:http://localhost:3000/companies？标签[]=来自*标签*表的任意 *UUID*

我将让您在前端实现自动完成功能，因为有许多方法和许多好的教程可以教您如何实现这一点。将它与我们上面的过滤器组合在一起，你将拥有一个强大的数据过滤系统。

## 结论

至此，我们的教程到此结束。我们现在有一个强大而高效的过滤和搜索系统，可以集成到任意数量的表中。在一个新的项目中使用它或者集成到一个现有的项目中是很容易的。

示例中显示的所有代码都可以在 [github](https://github.com/avinoth/tag-example) 中找到。

感谢您通读，我希望您今天学到了一些东西。

## 分享这篇文章