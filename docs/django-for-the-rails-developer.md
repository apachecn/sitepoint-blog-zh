# 面向 Rails 开发人员的 Django

> 原文：<https://www.sitepoint.com/django-for-the-rails-developer/>

![](img/fa0732303af34b16ba42c2a55132dc94.png) [Django](https://www.djangoproject.com/) 是最流行的 Python web 应用开发框架。很多人认为是 Python 的 Rails。如果您是一名经验丰富的 Rails 开发人员，本文将帮助您开始构建 Django 应用程序，并帮助您将 Rails 概念映射到 Django。

### 我需要什么来开始？

我们将假设你是一个有经验的开发人员，所以不会花时间解释诸如安装 Django 或 Python 之类的基础知识。如果你需要帮助，请看这里。我们也不会专注于使用 Django 创建一个基本的应用程序。Django 文档已经涵盖了这一点，如果您需要帮助，请阅读本文。

如果您还在阅读，我假设您可以构建一个基本的 Django 应用程序，并且您现在想要将您的 Rails 概念映射到 Django。让我们开始吧。

### 你说 MVC，我说 MTV

Rails 是一个 MVC 框架。你在`app/models/model_name.rb`中编写数据交互，在`app/controllers/controller_name.rb`中编写控制器，并使用`app/views/`中的视图呈现数据。Django 是一个 MVC 框架，但是它不同于标准的 MVC 术语。

在 Django 中，控制器变成了视图，视图变成了模板。

所以在

Rails :控制器是决定显示什么数据的 ruby 代码。
**Django** :视图是决定显示什么数据的 Python 代码。

和

**Rails** :视图决定数据如何呈现。这可以是 erb 或 haml 代码。
**Django** :模板决定了数据的呈现方式。这是带有 Django 特定标签的 html。

Django 称之为 [MTV 架构，在他们的 FAQ](https://docs.djangoproject.com/en/dev/faq/general/#django-appears-to-be-a-mvc-framework-but-you-call-the-controller-the-view-and-the-view-the-template-how-come-you-don-t-use-the-standard-names)中有更多的细节。

### 至少型号是一样的？

你可能希望如此，但那样生活就没那么有趣了。Rails 中的`db/schema.rb`是数据库模式的权威来源。要添加新模型或对现有模型进行更改，您需要创建并运行更改模式的迁移。`schema.rb`文件是根据数据库的当前状态自动生成的。您通常不会手动编辑 schema.rb。您将模型验证和业务逻辑保留在`app/models/model_name.rb`。

在 Django 中，数据库模式的权威来源是`app_name/models.py`。要创建一个新表，您需要编辑 models.py，然后运行`syncdb`或`migrate`命令。你将需要非常流行的[南](http://south.aeracode.org/)应用程序的迁移命令。

在 Rails 中，DB 是权威来源，而`schema.rb`是从它自动生成的。在 Django 中，`models.py`是权威来源，DB 是从它同步的。

### 不是在复杂的关系中

在 Rails 中，您可以通过`has_many`、`belongs_to`、`has_one`、`has_and_belongs_to_many`对外键关系进行建模。在 Django 中，你可以在你的模型上将它们指定为属性`models.ForeignKey`、`models.OneToOneField`、`models.ManyToManyField`。在 Django 中，你只需要指定关系的一方，另一个模型对象会自动获得相关的方法。这里是映射。

所属 _ 所属->外键有 *_* 多- >(不需要，因为 Django 在得到一个外键后自动添加这些)
有 _ 一个- >一个内字段(只需要在其中一个型号上指定)有 _ 和 _ 所属 _ 多- >多内字段(只需要在其中一个型号上指定)

### 模型对象和查询集。

一旦你定义了模型，他们有许多等价的方法。假设您在 Rails 和 Django 中都有一个用户模型。

**Rails** : User.create(..)
Django:user . objects . create(..)

进行一些更改后保存模型

**铁轨**:`user.save!`
姜戈 : `user.save()`

Django queryset 和 Rails AR 关系也非常等价，提供了类似的方法。

**Rails**:model . find(10)
Django:model . objects . get(id = 10)

**轨道** : Model.where(..条件)
姜戈 : Model.objects.filter(..条件)

**Rails** : Model.order("已创建*at ")* Django*:*model . objects . order by("已创建 _at ")

最大的不同是，在 Django 中，方法是在一个经理身上。(一般称为`objects`)，而不是模型类本身。

### 路由和配置

在 Rails 中，配置文件在`config/*`中，每个环境，比如开发、测试和生产，都有自己的配置文件。在 Django 中，大多数配置都在`settings.py`中。Rails 将路由放在`config/routes.rb`。Django 将 rouring/dispatch 放在 urls.py 中，并且基于 regex。

### 模板和视图

Rails 视图是 ERB 或 haml 文件。Django 模板有模板标签，对应于 Rails 助手，Django `{% include ..%}`模板标签对应于 Rails partials。

### 更多的映射。

#### 开发服务器

**Rails** : rails 服务器
Django:python manage . py 服务器

#### 开发控制台

**Rails** : rails 控制台
Django:python manage . py shell

#### 数据库迁移

**Rails** : rake db:创建& & rake db:迁移
**Django**:python manage . py syncdb&&python manage . py 迁移

#### 任务

Django 附带了一个 manage.py 脚本，可以执行 rails/rake 上的许多操作。

#### 按指定路线发送

**Rails**:config/routes . Rb
Django:URLs . py

#### 回收

**Rails** :回调(如之前*save)* Django*:*信号(如 presave)或覆盖生命周期方法如。救援

#### 观察者

**铁轨**:观察员
**姜戈**:信号

#### 自定义任务

**Rails** :自定义 rake 任务
Django:自定义管理命令

### 接下来呢？

这篇文章应该会给你足够的提示，让你应用你的 Rails 知识开始在 Django 上工作。这篇文章并不是一篇 Rails vs Django 的文章。Rails 和 Django 都是令人惊叹的框架，了解 Django 可以给你宝贵的见解和想法，以便更好地与 Rails 合作，即使你从未在生产中使用 Django。

我很想听听你在评论区对 Rails 和 Django 的异同的看法。感谢阅读！

## 分享这篇文章