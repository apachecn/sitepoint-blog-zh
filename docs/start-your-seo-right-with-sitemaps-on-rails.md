# 从 Rails 上的站点地图开始您的 SEO

> 原文：<https://www.sitepoint.com/start-your-seo-right-with-sitemaps-on-rails/>

![](img/5f3a0e35762358fab4387740162bdc5e.png)

在设计好你的网站后，下一步通常涉及到搜索引擎优化(SEO)。记住这一点，创建一个*站点地图*是你需要解决的任务之一。根据[协议](https://www.sitemaps.org/protocol.html)，网站地图是 UTF-8 编码的 XML 文件，描述你的网站结构。它们非常简单，但是对于大型网站来说，手工创建它们是不可取的。因此，自动生成站点地图是明智之举。

Rails 生成站点地图的解决方案有很多，但我更喜欢一个名为 [sitemap_generator](https://github.com/kjvarga/sitemap_generator) 的 gem。它得到了积极的维护，并具有许多很酷的功能:

*   它是框架不可知的，所以你可以不用 Rails 来使用它
*   它非常灵活
*   它有自己的配置文件，并不严格绑定到您的应用程序的路线
*   它允许你自动上传网站地图到第三方存储
*   当一个新的网站地图生成时，它会自动 pings 搜索引擎
*   它支持多个网站地图文件和各种类型的网站地图(视频，新闻，图片等。)

在本文中，我们将通过将 SitemapGenerator 集成到一个示例 Rails 应用程序中并讨论其主要特性来了解它的运行情况。我还将解释如何将站点地图导出到云存储，以便在 Heroku 这样的平台上一切正常。

本文的源代码可以在 [GitHub](https://github.com/bodrovis/Sitepoint-source/tree/master/Sitemaps) 获得。

## 创建示例站点

像往常一样，首先创建一个新的 Rails 应用程序:

```
$ rails new Sitemapper -T 
```

我将使用 Rails 5.0.1，但是 SitemapGenerator [几乎可以在任何版本上运行。](https://github.com/kjvarga/sitemap_generator#compatibility)

我们需要一些样品模型、路线和控制器。在这个演示中可以省略视图，因为站点实际上有什么内容并不重要。

假设我们正在创建一个有帖子和类别的博客；一个类别可以有许多文章。运行以下命令来生成模型和迁移:

```
$ rails g model Category title:string
$ rails g model Post category:belongs_to title:string body:text
$ rails db:migrate 
```

确保模型设置了正确的关联:

*型号/类别. rb*

```
[...]
has_many :posts, dependent: :destroy
[...] 
```

*车型/后 rb*

```
[...]
belongs_to :category
[...] 
```

现在让我们设置一些路线。为了让事情变得更有趣，我将它们嵌套起来:

*config/routes.rb*

```
[...]
resources :categories do
  resources :posts
end
[...] 
```

另外，当我们在这里时，添加根路由:

*config/routes.rb*

```
[...]
root to: 'pages#index'
[...] 
```

现在创建控制器。我们实际上不需要任何内部操作，所以它们会非常简单:

*categories_controller.rb*

```
class CategoriesController < ApplicationController
end 
```

*posts_controller.rb*

```
class PostsController < ApplicationController
end 
```

*pages_controller.rb*

```
class PagesController < ApplicationController
end 
```

太好了！然而，在继续之前，让我们也关注一下应用程序内部的样本数据。

### 加载样本数据

为了查看 SitemapGenerator 的运行情况，我们还需要一些示例数据。我将使用 [Faker](https://github.com/stympy/faker) 宝石来完成这项任务:

*Gemfile*

```
[...]
group :development do
    gem 'faker'
end
[...] 
```

安装它:

```
$ bundle install 
```

现在修改 *db/seeds.rb* 文件:

*db/seeds.rb*

```
5.times do
  category = Category.create({
                               title: Faker::Book.title
                             })

  5.times do
    category.posts.create({
                            title: Faker::Book.title,
                            body: Faker::Lorem.sentence
                          })
  end
end 
```

我们正在创建五个类别，每个类别有五个帖子，这些帖子有一些随机内容。要运行此脚本，请使用以下命令:

```
$ rails db:seed 
```

不错！准备工作做完了，我们进行主要部分吧。

## 集成网站地图生成器

将我们正在使用的宝石添加到*宝石文件*中:

*Gemfile*

```
[...]
gem 'sitemap_generator'
[...] 
```

安装它:

```
$ bundle install 
```

要创建带有有用注释的示例配置文件，请使用以下命令:

```
$ rake sitemap:install 
```

在 *config* 目录中你会发现一个 *sitemap.rb* 文件。这里要做的第一件事是指定您站点的主机名:

*config/sitemap.rb*

```
SitemapGenerator::Sitemap.default_host = "http://www.example.com" 
```

注意这个 gem 还支持[多个主机名](https://github.com/kjvarga/sitemap_generator#generating-multiple-sitemaps)。

SitemapGenerator 的主要指令应该放在传递给`SitemapGenerator::Sitemap.create`方法的块中。例如，让我们添加一个指向根路径的链接:

*config/sitemap.rb*

```
SitemapGenerator::Sitemap.create do
  add root_path
end 
```

`add`方法接受一组[参数](https://github.com/kjvarga/sitemap_generator#supported-options-to-add)。指定根页面每天更新:

*config/sitemap.rb*

```
add root_path, :changefreq => 'daily' 
```

帖子和类别呢？它们是由用户动态添加的，因此我们必须查询数据库并即时生成链接:

*config/sitemap.rb*

```
[...]
Category.find_each do |category|
  add category_posts_path(category), :changefreq => 'weekly', :lastmod => category.updated_at

  category.posts.each do |post|
    add category_post_path(category), :changefreq => 'yearly', :lastmod => post.updated_at
  end
end
[...] 
```

注意，这里我还提供了`:lastmod`选项来指定页面上次更新的时间(默认值是`Time.now`)。

### 运行生成器并检查站点地图文件

要生成新的站点地图(或更新现有的站点地图)，请运行以下命令:

```
$ rails sitemap:refresh 
```

请注意，如果由于某种原因，一个站点地图未能生成，旧版本不会被删除。另一件需要记住的重要事情是，该脚本将自动 [ping](https://github.com/kjvarga/sitemap_generator#pinging-search-engines) Google 和 Bing 搜索引擎，通知新版本的站点地图可用。以下是上面命令的输出示例:

```
+ sitemap.xml.gz          1 sitemap /  251 Bytes
Sitemap stats: 62 links / 1 sitemap / 0m01s

Pinging with URL 'http://www.example.com/sitemap.xml.gz':
    Successful ping of Google
    Successful ping of Bing 
```

如果您需要 ping 其他引擎，您可以修改`SitemapGenerator::Sitemap.search_engines`散列。你也可以省略搜索引擎的 ping，说

```
$ rails sitemap:refresh:no_ping 
```

生成的站点地图将被放置在 *public* 目录下，扩展名为 *.xml.gz* 。您可以提取这个文件，并用任何文本编辑器浏览它。如果出于某种原因你不想用 GZip 压缩文件，将`SitemapGenerator::Sitemap.compress`选项设置为`false`。

现在您已经有了一个站点地图，应该修改 *public/robots.txt* 文件以提供到它的链接:

*public/robots.txt*

```
Sitemap: http://www.example.com/sitemap.xml.gz 
```

SitemapGenerator 可能会创建一个索引文件，这取决于您的站点地图有多少链接。默认情况下(`:auto`选项)如果有超过 50 000 个链接，它们将被分离到不同的文件中，并且到它们的链接将被添加到索引中。您可以通过更改`SitemapGenerator::Sitemap.create_index`选项来控制这种行为。其他可用选项有`true`(总是生成索引)和`false`(从不生成索引)。

如果您希望将链接[直接添加到索引文件](https://github.com/kjvarga/sitemap_generator#adding-links-to-the-sitemap-index)中，请使用与`add`方法非常相似的`add_to_index`方法。

## 多个地区

现在假设我们的博客支持两种语言:英语和俄语。将英语设置为默认语言环境，并调整`available_locales`设置:

*配置/应用. rb*

```
[...]
config.i18n.default_locale = :en
config.i18n.available_locales = [:en, :ru]
[...] 
```

现在查看路线:

*config/routes.rb*

```
[...]
scope "(:locale)", locale: /#{I18n.available_locales.join("|")}/ do
    resources :categories do
      resources :posts
    end

    root to: 'pages#index'
end
[...] 
```

将英语和俄语的网站地图放在不同的文件中可能是个好主意。这是完全可能的，因为 SitemapGenerator [支持组](https://github.com/kjvarga/sitemap_generator#customizing-your-sitemaps):

*config/sitemap.rb*

```
[...]
{en: :english, ru: :russian}.each_pair do |locale, name|
  group(:sitemaps_path => "sitemaps/#{locale}/", :filename => name) do
    add root_path(locale: locale), :changefreq => 'daily'

    Category.find_each do |category|
      add category_posts_path(category, locale: locale), :changefreq => 'weekly', :lastmod => category.updated_at

      category.posts.each do |post|
          add category_post_path(category, post, locale: locale), :changefreq => 'yearly', :lastmod => post.updated_at
      end
    end
  end
end
[...] 
```

想法很简单。我们正在创建一个包含 *ru* 和 *en* 文件夹的 *public/sitemaps* 目录。里面有*english.xml.gz*和*russian.xml.gz*文件。我还将指示脚本始终生成索引文件:

*config/sitemap.rb*

```
[...]
SitemapGenerator::Sitemap.create_index = true
[...] 
```

## 部署到 Heroku

我们的站点已经可以部署了，但是有一个问题:Heroku 不允许我们保存自定义文件。因此，我们必须将生成的站点地图导出到云存储中。我将在这个演示中使用亚马逊 S3，所以在*gem 文件*中添加一个新的 gem:

*Gemfile*

```
[...]
gem 'fog-aws'
[...] 
```

安装它:

```
$ bundle install 
```

现在我们需要为 SitemapGenerator 提供一个[特殊配置](https://github.com/kjvarga/sitemap_generator#upload-sitemaps-to-a-remote-host-using-adapters),解释将文件导出到哪里:

*config/sitemap.rb*

```
[...]
SitemapGenerator::Sitemap.adapter = SitemapGenerator::S3Adapter.new(fog_provider: 'AWS',
                                                                    aws_access_key_id: 'KEY',
                                                                    aws_secret_access_key: 'SECRET',
                                                                    fog_directory: 'DIR',
                                                                    fog_region: 'REGION')

SitemapGenerator::Sitemap.public_path = 'tmp/'
SitemapGenerator::Sitemap.sitemaps_host = "https://example.s3.amazonaws.com/"
SitemapGenerator::Sitemap.sitemaps_path = 'sitemaps/'
[...] 
```

`SitemapGenerator::S3Adapter.new`包含 S3 的配置。要获得密钥对，你需要登录到 aws.amazon.com 的[并创建一个具有读/写权限的账户来访问 S3 服务。**不要**公开暴露这个密钥对！同样在一个选择的区域创建一个 S3 桶(默认为`us-east-1`)。](https://aws.amazon.com)

接下来，我们为`public_path`选项设置`tmp/`——这是文件在导出到 S3 之前最初创建的目录。

`sitemaps_host`应该包含一个路径到你的 S3 桶。

`sitemaps_path`是桶内的相对路径。

关于此配置的更多信息可在本页的[中找到。](https://github.com/kjvarga/sitemap_generator/wiki/Generate-Sitemaps-on-read-only-filesystems-like-Heroku)

另一个问题是，一些平台(例如 Bing)要求站点地图位于同一个域下，因此我们也需要注意这个问题。让我们向我们的应用程序添加一个路由`/sitemap`,它将简单地执行重定向到 S3:

*config/routes.rb*

```
[...]
get '/sitemap', to: 'pages#sitemap'
[...] 
```

相应的动作:

*pages_controller.rb*

```
[...]
def sitemap
  redirect_to 'https://example.s3.amazonaws.com/sitemaps/sitemap.xml.gz'
end
[...] 
```

正如你所记得的，默认情况下，SitemapGenerator 会 ping 搜索引擎，但它会提供一个直接链接到 S3，这不是我们想要的。利用`ping_search_engines`方法来覆盖此行为:

*config/sitemap.rb*

```
[...]
SitemapGenerator::Sitemap.ping_search_engines('http://example.com/sitemap')
[...] 
```

请注意，现在您需要通过运行

```
$ rake sitemap:refresh:no_ping 
```

因为否则 SitemapGenerator 将 ping 搜索引擎与*的直接链接和`http://example.com/sitemap`。*

 *最后，用一个新链接更新 *robots.txt* :

*public/robots.txt*

```
Sitemap: http://www.example.com/sitemap 
```

就这样，现在你的网站可以发布到 Heroku 了！

## 结论

我们已经到了这篇文章的结尾！到目前为止，您应该已经熟悉了 SitemapGenerator 的主要特性，并且能够将其集成到您自己的应用程序中。如果你有任何问题，不要犹豫，发表在评论里。此外，浏览 [gem 的文档](https://github.com/kjvarga/sitemap_generator)，因为它有许多我们没有讨论的其他特性。

谢谢你和我呆在一起，再见！* 

## *分享这篇文章*