# 从 Rails 5 开始:添加、更改和废弃

> 原文：<https://www.sitepoint.com/onwards-to-rails-5-additions-changes-and-deprecations/>

![](img/a72ffde038061a0cdb8a6113459b53af.png)

Rails 5 是流行的 web 框架的一个新的主要版本，即将推出，当然，我们也很期待。如果您想了解即将推出的一些新功能的概述，请参考[这篇文章](https://www.sitepoint.com/whats-new-rails-5/)。然而，今天我们将深入探讨一些技术细节，讨论哪些方法被删除或弃用，哪些设置被更改，以及如何将您现有的 Rails 4 应用程序迁移到版本 5。

不要忘记，在 Rails 5 发布后，所有的错误修复将只针对 Rails 5.0.x，而常规的安全修复将应用于 Rails 4.2.x，正如[官方维护政策](http://guides.rubyonrails.org/maintenance_policy.html)中所述。

这款应用的源代码可以在 [GitHub](https://github.com/bodrovis/Sitepoint-source/tree/master/Rails5_Migration) 获得。

## 准备

在继续之前要考虑的第一件事是 Rails 5 需要 Ruby 2.2.2 或更高版本，所以如果你还没有安装这个版本，现在就[下载它](https://www.ruby-lang.org/en/downloads/)。

在将您现有的应用程序迁移到 Rails 5 之前，我真的建议您拥有一个可靠的测试套件，否则迁移过程可能会变得更加复杂和痛苦。在本文中，我将使用一个名为 MusicalShop 的演示应用程序，它是在我的 screencast 系列 [RSpec with Rails](https://www.sitepoint.com/premium/series/rspec) 中引入的。这是一个非常简单的 Rails 4 应用程序，但它确实有用 RSpec 编写的自动化测试。你可以从[这个分支](https://github.com/learnable-content/RSpec-collection/tree/features)克隆最新版本的 app。

另外，建议迁移到 Rails 4 的最新补丁版本(目前是 4.2.5.1)。此外，您可以通过运行以下命令来检查您的项目中是否使用了最新版本的 gems

```
$ bundle outdated 
```

但是要小心，不要通过明确要求所有 gem 的最新版本来引入任何冲突(尤其是对于依赖项)。

另一件要考虑的事情是一些 gems 可能还不兼容 Rails 5，所以要做好准备。浏览 gem 的依赖项，浏览 GitHub 上的公开问题，了解其他开发人员在 Rails 5 上使用该库时是否面临问题。一些宝石(例如，像设计)可能需要直接从`master`分支中包含。

好的，继续检查测试是否成功运行:

```
$ rspec . 
```

不要忘记，正如 Edsger Dijkstra 所说，“测试永远不能证明软件中没有错误，只能证明它们的存在”。但我们会抱最好的希望。

最后要做的事情是切换到另一个分支，这样如果事情没有按计划进行，您可以随时返回到旧版本:

```
$ git checkout -b rails5 
```

## 迁移

深呼吸，修改你的 *Gemfile* :

*Gemfile*

```
[...]
gem 'rails', '>= 5.0.0.beta3', '< 5.1'
[...] 
```

目前 Rails 还在测试阶段，但是很快就会发布候选版本。

奔跑

```
$ bundle update 
```

你也可以跑

```
$ rake rails:update 
```

它创建 Rails 5 中引入的新文件，并修改旧文件。

总而言之，Rails 5 的配置文件与 Rails 4 非常相似，所以你有可能在看到一些反对消息的同时马上启动你的应用。有些人可能还记得从 Rails 3 到 Rails 4 的迁移过程要痛苦得多。

尽管如此，许多被否决的方法[还是被移除了](https://github.com/rails/rails/pull/18325)，所以如果你正在使用它们中的任何一个，你必须相应地修改代码库。让我们来讨论一下主要的变化，这样你就能理解应用程序的哪些部分最需要你的关注。

### 控制器

好的，首先，让我们观察与控制器相关的变化。

*   `skip_action_callback`方法被[完全移除](https://github.com/rails/rails/pull/19060)。

*   `render`方法的`:nothing`选项是[已弃用的](https://github.com/vngrs/rails/commit/44781b6e9790d90b4f8b9a41d2b2c114b1a582ee)。

*   仍然支持`*_filter`方法(例如`before_filter`)，但是[已被弃用](https://github.com/rails/rails/commit/7644a99)，并将在 Rails 5.1 中被移除。用`*_action`代替。

*   一个很好的方法`redirect_back`正在被引入以支持`redirect_to :back`。它尝试重定向到 HTTP_REFERER 或提供的位置，因此您可以在您的方法中使用它:

```
redirect_back root_path 
```

*   不推荐使用`render :text`，因为它不提供`text/plain`响应。使用`render :plain`或`render :html`显示`text/html`页面；

```
render plain: 'My text' 
```

*   `respond_to`和`respond_with`方法[已经被提取](https://github.com/rails/rails/commit/afd5e9a)到[响应者 gem](https://github.com/plataformatec/responders) 中，所以如果你使用那些方法，一定要把它包含在**gem 文件**中。

*   XML 序列化[已经被提取](https://github.com/rails/rails/pull/21161)到一个单独的[active model-serializer-XML gem](https://github.com/rails/activemodel-serializers-xml)中。

### 视图

默认情况下，每个表单现在都有自己的 CSRF 令牌。这是为了防止攻击者在指向某些恶意网站的页面上注入自己的表单时发生表单劫持。

创建一个初始化文件来全局控制这个行为:

*config/initializer/per _ form _ csrf _ tokens . Rb*

```
Rails.application.config.action_controller.per_form_csrf_tokens = true 
```

或者在单个控制器中使用以下设置:

```
self.per_form_csrf_tokens = true 
```

`content_tag_for`和`div_for`方法[从`ActionView`中移除](https://github.com/rails/rails/pull/18411)并提取到[记录*标签*辅助宝石](https://github.com/rails/record_tag_helper)。

### 模型

Rails 5 引入了一个名为`ApplicationRecord`的新抽象类(这意味着它不能被实例化),所有模型都默认继承自它，而不是继承自`ActiveRecord::Base`。这样做是为了直接防止猴子打补丁`ActiveRecord::Base`。所以，你可以在`ApplicationRecord`里面引入所有的扩展。如果你想使用这个功能，在你的**模型**目录下创建**应用记录. rb** 文件:

*models/application _ record . Rb*

```
class ApplicationRecord < ActiveRecord::Base
  self.abstract_class = true
end 
```

接下来，确保所有模型都继承了这个新类，例如:

*型号/用户. rb*

```
class User < ApplicationRecord
  [...]
end 
```

另一个增加与`belongs_to`关联相关:在 Rails 5 中，默认情况下关联记录必须存在。`required`选项现在被[弃用](https://github.com/rails/rails/pull/18937)，一个新的`optional`参数被引入。这意味着，默认情况下，Rails 会在保存对象之前验证“父”记录是否存在。如果您不希望在某些关联中使用这种行为，请使用

```
belongs_to :some_parent, optional: true 
```

Rails 5 还附带了一个**活动*记录*属于*需要*通过* default.rb*** 初始化文件来控制整个应用程序的这种行为。您也可以创建它:

*config/initializer/active _ record _ owners _ to _ required _ by _ default . Rb*

```
Rails.application.config.active_record.belongs_to_required_by_default = true 
```

在你的模型中返回`false`的回调不再停止回调链。如果您希望显式停止回调链，请使用`throw(:abort)`来代替:

```
def my_callback
  throw(:abort) if something_bad_happened
end 
```

新的 Rails 5 应用程序附带了一个控制这种行为的 **callback_terminator.rb** 初始化文件:

*config/initial izers/callback _ terminator . Rb*

```
ActiveSupport.halt_callback_chains_on_return_false = false 
```

还要记住，如果你打算在 PostgreSQL 中使用 Rails，[只有 9.1 以上的版本才受支持。](https://github.com/rails/rails/pull/23434)

### 隐藏物

开发中的缓存管理变得更加方便。在 Rails 4 中，我们曾经有过接受`true`或`false`的`config.action_controller.perform_caching`设置。然而，在 Rails 5 中，现在出现了以下代码片段:

*config/environments/development . Rb*

```
[...]
if Rails.root.join('tmp/caching-dev.txt').exist?
  config.action_controller.perform_caching = true
  config.cache_store = :memory_store
  config.public_file_server.headers = {
    'Cache-Control' => 'public, max-age=172800'
  }
else
  config.action_controller.perform_caching = false
  config.cache_store = :null_store
end
[...] 
```

要启用或禁用缓存，您[可以运行](https://github.com/rails/rails/issues/18875)命令在 **tmp** 目录下创建 **caching-dev.txt** 文件。

### 按指定路线发送

多个`root`路由现在可能存在于同一个作用域中(之前，出现了一个错误)。您可以提供约束并选择要使用的根:

```
root 'blog#show', constraints: ->(req) { some_condition }
root 'pages#show' 
```

`rails routes`命令[现在接受](https://github.com/rails/rails/pull/23225)以下选项来
搜索特定路线:

*   `-c`返回指定控制器的所有路线
*   `-g`根据指定模式返回路线

### 试验

不推荐使用像`get`和`post`这样的 HTTP 请求方法。你应该用`process`来代替。例如，在我的测试中，我有这样的调用:

*spec/controllers/albums _ controller _ spec . Rb*

```
[...]
get :index
[...]
post :create, album: {title: ''}
[...] 
```

这些应该重写为

*spec/controllers/albums _ controller _ spec . Rb*

```
[...]
process :index, method: :get
[...]
process :create, method: :post, params: { album: {title: ''} }
[...] 
```

`assigns`和`assert_template`方法[已经被提取](https://github.com/rspec/rspec-rails/issues/1393)到一个单独的[轨道控制器测试宝石](https://github.com/rails/rails-controller-testing)。将其添加到您的 **Gemfile** 中:

*Gemfile*

```
[...]
group :test do
  gem 'rails-controller-testing'
end
[...] 
```

如果你像我一样使用 RSpec，作为一个临时的修正，把下面几行代码添加到你的 **rails_helper.rb** 文件中(RSpec 还不正式支持 Rails 5):

*spec/rails_helper.rb*

```
[...]
RSpec.configure do |config|
  config.include Rails::Controller::Testing::TestProcess
  config.include Rails::Controller::Testing::TemplateAssertions
  config.include Rails::Controller::Testing::Integration
  [...]
end 
```

### 一些其他的东西

Rails 5 的另一个很酷的特性是所有的`rake`命令[现在都活在`rails`里的](https://github.com/rails/rails/issues/18878)(比如`rake db:migrate`变成了`rails db:migrate`)。

`ActiveRecord::Base.raise_in_transactional_callbacks`设置已被否决且无效。它将被删除而不会被替换，所以你可以安全地从 **config/application.rb** 文件中删除它。

ActiveJob [现在从中间的`ApplicationJob`类继承了](https://github.com/rails/rails/pull/19034)。在 **jobs** 目录下创建一个 **application_job.rb** 文件:

*jobs/application_job.rb*

```
class ApplicationJob < ActiveJob::Base
end 
```

确保您的工作继承自`ApplicationJob`。

`ActionDispatch::Static` [接受](https://github.com/rails/rails/pull/19135)自定义 HTTP 头，因此您可以指定自定义缓存控制选项。

最后要注意的是，`ActionCable` [不再需要](http://weblog.rubyonrails.org/2016/2/2/Rails-5-0-beta2/) Redis，EventMachine，和赛璐珞在场了。

## 结论

所以，Rails 5 承诺了一组很棒的新特性和附加功能，我真的鼓励你尝试一下。另外，一定要浏览[完整的变更日志](http://weblog.rubyonrails.org/2015/12/18/Rails-5-0-beta1/#a-few-other-highlights)。

您是否计划在不久的将来迁移到 Rails 5？你的应用中有多少还在运行 Rails 3(甚至 Rails 2)？请在评论中分享你的观点！

## 分享这篇文章