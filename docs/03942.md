# 真实世界中的 Rails 应用程序模板

> 原文：<https://www.sitepoint.com/rails-application-templates-real-world/>

![railstemplate](img/c5461b5d65b915834004d5cd29bcc332.png)

我即将开始一个新项目，该项目将包含许多基于 Rails(和 rails-api)的服务，我想要一种方法来确保所有服务都是平等的。这个项目还有其他几个开发人员，所以我不希望我们每个人都创建完全不同的应用程序结构或者使用不同的 gem，除非有一个好的理由。

例如，我希望我们从相同版本的 Rails 开始，PostgreSQL 是首选的存储库，所以安装 SQLite gem 是愚蠢的。在测试和开发环境中，如果在生成应用程序时已经准备好了 Rspec 和 Guard，那就太好了。此外，从一开始就使用 Pry 是非常明智的。正是这些和类似的变化让我想改变我们的出发点。

有很多方法可以做到这一点，但我真的不想从 RailsBricks 或 Rails Composer 这样的东西开始。我并不反对这两者，恰恰相反。事实上，我写过关于 RailsBricks 的文章，我认为 Daniel ke hoe(《T2》Rails app 教程和 Rails Composer 的作者)应该被封爵。简单地说，如果可能的话，我希望在不添加其他依赖项的情况下做到这一点。

Rails 核心团队在 [Rails 应用程序模板](http://guides.rubyonrails.org/rails_application_templates.html)中提供了一种方法，所以为什么不尝试使用核心团队使用的方法呢。

剧透:很好用。

## 模板 API

Rails 模板 API 是一种领域特定语言(DSL)，由 T2 或 T3 提供支持。它在核心雷神动作的基础上增加了[后续动作](https://github.com/rails/rails/blob/bee9434cdf4f56dc51027a8890cf04f506544735/railties/lib/rails/generators/actions.rb):

*   `gem`–在**宝石文件**中添加一颗宝石
*   `gem_group`–在 gem 文件中创建一个`group`块，其中包含所提供块中的宝石
*   `add_source`–将 gem 源添加到 Gem 文件，例如，私有 Gem 服务器
*   `environment`–在**应用程序. rb** 或提供的环境文件中添加一行。这也是`application`的别名。我将在我的最终模板中展示一个例子。
*   `git`–运行 git 命令
*   `vendor`–在**供应商**目录下创建一个文件
*   `lib`–在 **lib** 中创建一个文件或目录
*   `rakefile`–创建一个新的 **Rakefile**
*   `initializer`–用提供的内容创建一个新的初始化文件
*   `generate`–运行轨道发电机
*   `rake`–运行 Rake 任务
*   `capify`–运行`capify`
*   `route`–在 **routes.rb** 中创建一个条目
*   `readme`–将所提供文件的内容打印到控制台
*   `after_bundle`–一旦`bundle install`完成就运行的回调(你猜对了)

事实证明，这几个动作，加上核心的 Thor 动作，足以为真正的 Rails 应用程序提供一个健壮的起点。

## 这个例子

正如我之前提到的，我想改变核心网站`rails new`生成的一些项目。以下是我想定制的东西的完整列表:

*   将`pg` gem 添加到 gem 文件和数据库配置中
*   使用 Rails API。这意味着我将改变包括哪些核心铁路。
*   为我的演示者使用 ROAR
*   在开发中使用撬(通过`byebug`宝石)
*   在测试中，使用 Rspec、variant、SimpleCov 和 Guard，并运行 generators/install
*   为应用程序创建 Dockerfile 和 docker-compose.yml
*   补充一个更好的。gitignore 文件
*   围绕 JSON Schema 添加一些工具。Heroku 的好人们有很多这样的人
*   为[健康检查端点](http://byterot.blogspot.com/2014/11/health-endpoint-in-api-design-slippery-rest-api-design-canary-endpoint-hysterix-asp-net-web-api.html)添加路由

正如您所看到的，这并不太多，但是拥有一个开发人员可以在生成服务时使用的模板将会节省一些时间，并消除开发环境之间的一些不协调。一旦这个模板完成，开发人员将能够输入

```
rails new service_name -m our_template.rb
```

然后直接进入开发。像`guard`这样的东西会立即工作，不需要任何设置。就像它应该的那样。

## 模板

这是完整的模板:

```
# Add the current directory to the path Thor uses
# to look up files
def source_paths
  Array(super) + 
    [File.expand_path(File.dirname(__FILE__))]
end

remove_file "Gemfile"
run "touch Gemfile"
add_source 'https://rubygems.org'
gem 'rails', '4.2.1'
gem 'rails-api'
gem 'puma'

gem 'pg'
gem 'roar-rails'

gem 'committee'

gem_group :development, :test do
  gem 'spring'
  gem 'pry-rails'
  gem 'web-console', '~> 2.0'
  gem 'prmd'
  gem 'rspec-rails', require: false
end

gem_group :test do
  gem 'simplecov', require: false
  gem 'simplecov-rcov', :require => false
  gem 'guard-rspec'
  gem 'mutant'
  gem 'mutant-rspec'
end

copy_file "Dockerfile"
copy_file "docker-compose.yml"
remove_file ".gitignore"
copy_file ".gitignore"

inside 'config' do
  remove_file 'database.yml'
  create_file 'database.yml' do <<-EOF
default: &default
  adapter: postgresql
  host: db 
  port: 5432
  pool: 5
  timeout: 5000
  user: postgres
  password: postgres

development:
  <<: *default
  database: #{app_name}_development

# Warning: The database defined as "test" will be erased and
# re-generated from your development database when you run "rake".
# Do not set this db to the same as development or production.
test:
  <<: *default
  database: #{app_name}_test
  host: 192.168.59.103

production:
  <<: *default
  database: #{app_name}_production

EOF
  end
end

create_file 'schema/meta.json' do <<-EOF
{
"description": "Service",
"id":"service-uu",
"links": [{
"href" : "https://api.esalrugs.com",
"rel" : "self"
}],
"title" : "UU Service"
}
EOF
end

# JSON Schema
empty_directory "schema/schemata"

rakefile("schema.rake") do <<-EOF
require 'prmd/rake_tasks/combine'
require 'prmd/rake_tasks/verify'
require 'prmd/rake_tasks/doc'

namespace :schema do
    Prmd::RakeTasks::Combine.new do |t|
      t.options[:meta] = 'schema/meta.json'    
      # use meta.yml if you prefer YAML format
      t.paths << 'schema/schemata'
      t.output_file = 'schema/api.json'
    end

    Prmd::RakeTasks::Verify.new do |t|
      t.files << 'schema/api.json'
    end

    Prmd::RakeTasks::Doc.new do |t|
      t.files = { 'schema/api.json' => 'schema/api.md' }
    end
  task default: ['schema:combine', 'schema:verify', 'schema:doc']
end
EOF
end

after_bundle do
  remove_dir "app/views"
  remove_dir "app/mailers"
  remove_dir "test"

  insert_into_file 'config/application.rb', after: "require 'rails/all'\n" do <<-RUBY
require "active_record/railtie"
require "action_controller/railtie"
  RUBY
  end

  gsub_file 'config/application.rb', /require 'rails\/all'/, '# require "rails/all"'

  application do <<-RUBY
    config.assets.enabled = false
    config.generators do |g|
      g.test_framework :rspec, fixture: true
      g.view_specs false
      g.helper_specs false
    end

    # Validates the supplied and returned schema. 
    # docs: https://github.com/interagent/committee
    config.middleware.use Committee::Middleware::RequestValidation, schema: JSON.parse(File.read("./schema/api.json")) if File.exist?("./schema/api.json")
  RUBY
  end

  gsub_file 'config/environments/development.rb', /action_mailer/, ''
  gsub_file 'config/environments/test.rb', /.*action_mailer.*/n/, ''

  gsub_file 'app/controllers/application_controller.rb', /protect_from_forgery/, '# protect_from_forgery'

  run "spring stop"
  generate "rspec:install"
  run "guard init"

  # Health Check route
  generate(:controller, "health index")
  route "root to: 'health#index'"

  git :init
  git add: "."
  git commit: "-a -m 'Initial commit'"
end
```

嗯，那看起来很长，不是吗？我们来分解一下模板。

```
# Add the current directory to the path Thor uses
# to look up files
def source_paths
  Array(super) + 
    [File.expand_path(File.dirname(__FILE__))]
end
```

Thor 使用`source_paths`来查找发送给[基于文件的 Thor 动作](https://github.com/erikhuda/thor/blob/master/lib%2Fthor%2Factions%2Ffile_manipulation.rb)的文件，比如`copy_file`和`remove_file`。它在这里被重新定义，这样我就可以添加模板目录并将文件从其中复制到生成的应用程序中。

默认的 Rails **Gemfile** 有一堆我们不需要的东西(sqlite、所有的资产东西、turbolinks)，所以与其繁琐地删除 gem，不如让我们直接重建文件。当然，每个 Gemfile 都需要一个源来搜索，所以它被添加到文件的顶部。

```
# We'll be building the Gemfile from scratch
remove_file "Gemfile"
run "touch Gemfile"

add_source 'https://rubygems.org'
```

这是我们需要的所有宝石:

```
gem 'rails', '4.2.1'
gem 'rails-api'
gem 'puma'

gem 'pg'
gem 'roar-rails'

gem 'committee'

gem_group :development, :test do
  gem 'spring'
  gem 'pry-rails'
  gem 'web-console', '~> 2.0'
  gem 'prmd'
  gem 'rspec-rails', require: false
end

gem_group :test do
  gem 'simplecov', require: false
  gem 'simplecov-rcov', :require => false
  gem 'guard-rspec'
  gem 'mutant'
  gem 'mutant-rspec'
end
```

`gem`和`gem_group`是由 Rails 模板 API 提供的，它们做的正是您所期望的。

这个模板的目标之一是使应用程序的归档非常简单。解释 Docker 及其优点(和缺点)超出了本文的范围。相反，我会鼓励你去看看 Docker(我希望很快就能写一篇关于它的文章)并看看下面的内容，因为我只需要将一些自定义文件复制到生成的应用程序中。

```
copy_file "Dockerfile"
copy_file "docker-compose.yml"
```

**Dockerfile** 和 **docker-compose.yml** 是我的模板目录中的文件。这些文件是用 Thor 的`copy_file`动作复制进来的。

让我们做一个闪亮的，新的**。gitignore** 文件。首先删除文件的原因是为了避免`rails new`命令提示用户覆盖文件:

```
remove_file ".gitignore"
copy_file ".gitignore"
```

在一个可能有点争议的决定中，有一个核心的 **database.yml** 文件，我想要分发。多亏了 Docker，我们可以稍微标准化一下。酷的`inside`方法确保文件最终出现在正确的文件夹中:

```
inside 'config' do
  remove_file 'database.yml'
  create_file 'database.yml' do <<-EOF
default: &default
  adapter: postgresql
  host: db 
  port: 5432
  pool: 5
  timeout: 5000
  user: postgres
  password: postgres

development:
  <<: *default
  database: #{app_name}_development

# Warning: The database defined as "test" will be erased and
# re-generated from your development database when you run "rake".
# Do not set this db to the same as development or production.
test:
  <<: *default
  database: #{app_name}_test
  host: 192.168.59.103

production:
  <<: *default
  database: #{app_name}_production

EOF
  end
end
```

注意，我使用`app_name`来创建数据库名称。`app_name`和`app_path`是模板的可用变量。

是时候利用一些 JSON 模式工具来确保我有良好的模式和文档生成，以及一些验证了。在 Rails 中，JSON 模式流仍然有点笨拙，但是结果证明了方法的合理性，IMO:

```
create_file 'schema/meta.json' do <<-EOF
{
"description": "Service",
"id":"service-uu",
"links": [{
"href" : "https://api.esalrugs.com",
"rel" : "self"
}],
"title" : "UU Service"
}
EOF
end

# JSON Schema
empty_directory "schema/schemata"

rakefile("schema.rake") do <<-EOF
require 'prmd/rake_tasks/combine'
require 'prmd/rake_tasks/verify'
require 'prmd/rake_tasks/doc'

namespace :schema do
    Prmd::RakeTasks::Combine.new do |t|
      t.options[:meta] = 'schema/meta.json'    
      # use meta.yml if you prefer YAML format
      t.paths << 'schema/schemata'
      t.output_file = 'schema/api.json'
    end

    Prmd::RakeTasks::Verify.new do |t|
      t.files << 'schema/api.json'
    end

    Prmd::RakeTasks::Doc.new do |t|
      t.files = { 'schema/api.json' => 'schema/api.md' }
    end
  task default: ['schema:combine', 'schema:verify', 'schema:doc']
end
EOF
end
```

首先，创建一个 **meta.json** 文件，它将保存关于这个服务的元数据。标题、URL 等。接下来，创建一个**模式/模式**目录来保存我的资源的模式文件。在这篇文章中，我不打算深入 JSON 模式是什么和如何的，但是我鼓励你去看看。最后，我添加了一些 rake 任务来生成将记录 API 的模式和 markdown 文档。Rails 应用程序模板 API 提供了`rakefile`方法，这很酷。

现在我们已经到了后`bundle`回调。捆绑后有很多工作要做，其中一些可能会移到预捆绑中，但现在我们来了。这是一个 Rails API 应用程序，所以我们不需要视图，邮件也不会被这些服务处理。此外，我们正在使用 Rspec，所以只需删除**测试**目录:

```
after_bundle do
  remove_dir "app/views"
  remove_dir "app/mailers"
  remove_dir "test"
```

由于 Rails API 不使用所有的核心 Railties，我们可以通过只要求我们需要的东西来获得一些性能。托尔的酷方法允许我们把它放在我们想要的地方。完成后，我们可以使用`gsub_file`注释掉包含所有内容的行:

```
insert_into_file 'config/application.rb', after: "require 'rails/all'\n" do <<-RUBY
    require "active_record/railtie"
    require "action_controller/railtie"
  RUBY
  end

  gsub_file 'config/application.rb', /require 'rails\/all'/, '# require "rails/all"'
```

因为没有资产或视图，所以完全禁用资产是个好主意。此外，虽然我希望 Rspec 作为测试框架，但我不希望查看规范。在这里，我使用 Rails 模板 API 方法`application`，如果您还记得的话，它只是`environment`的别名:

```
application do <<-RUBY
    config.assets.enabled = false
    config.generators do |g|
      g.test_framework :rspec, fixture: true
      g.view_specs false
      g.helper_specs false
    end
```

下面是更多的 JSON 模式，以中间件的形式执行验证:

```
# Validates the supplied and returned schema. 
# docs: https://github.com/interagent/committee
config.middleware.use Committee::Middleware::RequestValidation, schema: JSON.parse(File.read("./schema/api.json")) if File.exist?("./schema/api.json")

RUBY
end
```

[委员会](https://github.com/interagent/committee) gem 提供中间件来验证请求和响应中的 JSON 模式。输入验证很棒。

接下来，去掉所有的动作邮件配置:

```
gsub_file 'config/environments/development.rb', /.*action_mailer.*/n/, ''
  gsub_file 'config/environments/test.rb', /.*action_mailer.*/n/, ''
```

因为我们不会在 API 中使用 cookies(因为那是完全疯狂的)，所以不需要`protect_from_forgery`方法:

```
gsub_file 'app/controllers/application_controller.rb', /protect_from_forgery/, '# protect_from_forgery'
```

我想让测试/规范环境准备就绪，这意味着运行 Rspec 生成器并初始化 Guard。我痛苦地认识到，如果我不停止弹簧，发电机就会挂掉:

```
run "spring stop"
  generate "rspec:install"
  run "guard init"
```

现在，我希望每个服务都有一个健康检查端点，所以让我们创建一个:

```
# Health Check route
generate(:controller, "health index")
route "root to: 'health#index'"
```

差不多完成了，只需要设置 Git:

```
git :init
  git add: "."
  git commit: "-a -m 'Initial commit'"
end
```

就这样结束了！我可以告诉我们的开发团队使用我的模板来生成他们的 Rails 服务，我们将拥有一个共同的基础和一个现成的环境。只需`cd`进入新的应用程序目录，然后`guard`或`docker-compose up`就可以了。对于很少的工作来说还不错。我已经做了很多事情来鼓励一些最佳实践，并消除 cruft。当人们说 Rails 让您更有效率时，依我拙见，这是一个很好的例子。

## 逮到你了

当然，使用这个特殊的例子需要注意一些事情。最大的问题是，因为我决定从模板目录中复制文件，所以不能对模板文件使用 HTTP 端点。换言之，这是行不通的:

```
rails new service_name -m https://raw.githubusercontent.com/skookum/rails-api-template/master/rails-api-template.rb
```

因为它不会找到 **Dockerfile** 来复制。这可以通过使用 heredocs 将所有文件内容内联到模板本身来解决。所以，如果你要使用它，一定要将库克隆到本地，并在`-m`参数中引用本地路径。

另外，你们中的一些人可能会问为什么我不使用`rails-api new service_name`。这是个很好的问题。基本上，我查看了`rails-api new`生成的内容，并让我的模板做了一些类似的事情。值得一提的是，您可以将同一个`-m`参数传递给`rails-api`生成器，您将在类似的地方结束。

最后，这个模板还没有被证明出来。当我们使用它并发现它没有做或缺少的东西时，我计划更新存储库。因此，模板可能会偏离文章。

## 模板所有的东西

希望这篇文章能鼓励你在基于团队的 Rails 开发中采用类似的方法。我们只是刚刚开始使用模板，所以我肯定我错过了一些东西，或者你们中的一个有一些关于如何做得更好的好建议。把那些东西放在评论里！

感谢阅读！

更新:这篇文章的前一个版本错误地声称 Rails Composer 是由 M. Hartl 创建的，而事实上，它是由 Daniel Kehoe 创建的。我们对此表示歉意。那些负有责任的人已经被解雇了。

## 分享这篇文章