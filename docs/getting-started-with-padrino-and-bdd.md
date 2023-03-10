# Padrino 和 BDD 入门

> 原文：<https://www.sitepoint.com/getting-started-with-padrino-and-bdd/>

Padrino 是一个 web 框架，与其表亲 Sinatra web 框架密切相关。它试图提供 Sinatra 开发的灵活性以及 Rails 的各种助手、工具和插件。

Padrino，意大利语“教父”的意思，是建立在 Sinatra 之上的，所以你可以使用在这个网站上发布的以前的 Sinatra 系列中获得的所有知识。然而，它有些不同，因为它提供了一些看起来与前面提到的文章中的约定非常相似的约定，您将会看到。顺便说一下，我将在本教程中使用 Ruby **1.9.2** 。

为了方便起见，我会通过 RVM 来处理。如果你还没有，那么 Rubysource fame 的 Glenn Goodrich 在本文中概述了建立 RVM 的步骤。一旦安装了 RVM 和 Ruby 1.9.2，就为这个应用程序创建一个 gemset。我把我的命名为“rubysource”，如下图所示:

```
$ rvm use 1.9.2@rubysource --create
```

### 安装 Padrino

要安装 Padrino:

```
$ gem install padrino
```

完成后，通过运行以下命令创建一个新项目:

```
$ padrino g project rubytoday -t cucumber -b
```

**g** 是**生成**
**的简称-t** 是一个用于指定测试框架的开关，在本例中
我指定了 Cucumber，这也包括 RSpec

**-b** 告诉帕德里诺使用邦德勒来管理宝石

这就创建了一个名为 **rubytoday** 的项目。

一旦您切换到 **rubytoday** 目录，如果您使用过 Rails，您将看到一个与 Rails 项目有些相似的目录结构:

```
,-- Gemfile
|-- Gemfile.lock
|-- app
|   |-- app.rb
|   |-- controllers
|   |-- helpers
|   `-- views
|       `-- layouts
|-- config
|   |-- apps.rb
|   `-- boot.rb
|-- config.ru
|-- cucumber.yml
|-- features
|   |-- add.feature
|   |-- step_definitions
|   |   `-- add_steps.rb
|   `-- support
|       |-- env.rb
|       `-- url.rb
|-- public
|   |-- favicon.ico
|   |-- images
|   |-- javascripts
|   `-- stylesheets
|-- spec
|   |-- spec.rake
|   `-- spec_helper.rb
`-- tmp
```

一些注意事项:

*   位于 **app** 目录下的主 app
*   **public** 目录下的 JavaScript 和样式表内容
*   黄瓜特性在**特性**目录下
*   **spec** 目录下的 RSpec 文件夹

现在我们有了最初的裸应用程序，是时候将它签入 Git 了。这样，如果需要，您可以再次恢复到这种状态。在此之前，打开**。gitignore** 文件，并添加编辑器创建的备份文件的正则表达式模式。在我的例子中，Emacs 和备份文件的结尾是: ***~** 。

然后将它添加到 Git 中，就像这样:

```
$ git init
$ git add .
$ git commit -m "initial commit"
$ git status</p>

<h1>On branch master</h1>

<p>nothing to commit (working directory clean)
```

最后一个命令 **git status** 声明我当前在主**主**分支上。为了做进一步的工作，我将创建一个新的分支，并在其中工作。一旦我对它满意了，我会把它合并回主分支。

```
$ git checkout -b hello_world
Switched to a new branch 'hello_world'
```

用命令 **git checkout -b helloworld** 我创建了一个新的分支 **helloworld** 并切换到它。

来自 **git 分支**的输出证实了这一点:

```
$ git branch
* hello_world
  master
```

分公司名称前的首字母 **'*'** 表示我目前工作的分公司。

## BDD 中的 Hello world

如果分支名称是什么，您可能已经猜到了本教程的下一部分将会显示什么。“Hello world”页面的创建。

由于本教程试图教授 Cucumber 的用法和行为驱动开发，我将从编写一个特性文件开始，解释我希望我的应用程序如何运行。

在**功能**目录下我打开一个文件 **hello.feature** 并添加:

```
Feature: hello world
  I want the application to greet the world every time I use it

  Scenario: greet the world
    Given I visit the app
    Then it should greet the world
```

**特征**和**场景**为关键词。**功能**可以说是主标题，它简要概述了被描述的内容，后面是描述。场景是一个具体例子应该如何表现的例子。
还有一个名为 **add.feature** 的文件，不需要了，我把它去掉:

```
$ git rm add.feature
```

然后我运行黄瓜，看看输出:

```
$ bundle exec cucumber
Using the default profile...
Feature: hello world
  I want the application to greet the world every time I use it

Scenario: greet the world        # features/hello.feature:4
    Given I visit the app          # features/hello.feature:5
      Undefined step: "I visit the app" (Cucumber::Undefined)
      features/hello.feature:5:in 'Given I visit the app'
    Then it should greet the world # features/hello.feature:6
      Undefined step: "it should greet the world" (Cucumber::Undefined)
      features/hello.feature:6:in 'Then it should greet the world'

1 scenario (1 undefined)
2 steps (2 undefined)
0m0.002s

You can implement step definitions for undefined steps with these snippets:

Given /^I visit the app$/ do
  pending # express the regexp above with the code you wish you had
end

Then /^it should greet the world$/ do
  pending # express the regexp above with the code you wish you had
end
```

黄瓜抱怨它不能识别未定义的步骤。所以我将它们复制并添加到一个新文件中:
**features/step definitions/hello steps . Rb**:

```
Given /^I visit the app$/ do
  pending # express the regexp above with the code you wish you had
end

Then /^it should greet the world$/ do
  pending # express the regexp above with the code you wish you had
end
```

当我再次运行黄瓜时:

```
$ bundle exec cucumber
Using the default profile...
Feature: hello world
  I want the application to greet the world every time I use it

Scenario: greet the world        # features/hello.feature:4
    Given I visit the app          # features/step_definitions/hello_steps.rb:1
      TODO (Cucumber::Pending)
      ./features/step_definitions/hello_steps.rb:2:in '/^I visit the app$/'
      features/hello.feature:5:in 'Given I visit the app'
    Then it should greet the world # features/step_definitions/hello_steps.rb:5

1 scenario (1 pending)
2 steps (1 skipped, 1 pending)
0m0.002s
```

第一步失败，跳过第二步。因为第一步实际上没有定义任何东西，所以我添加了以下内容:

```
Given /^I visit the app$/ do
  visit '/'
end
```

再次运行黄瓜:

```
$ bundle exec cucumber
Using the default profile...
Feature: hello world
  I want the application to greet the world every time I use it

  Scenario: greet the world        # features/hello.feature:4
    Given I visit the app          # features/step_definitions/hello_steps.rb:1
    Then it should greet the world # features/step_definitions/hello_steps.rb:5
      TODO (Cucumber::Pending)
      ./features/step_definitions/hello_steps.rb:6:in '/^it should greet the world$/'
      features/hello.feature:6:in 'Then it should greet the world'

1 scenario (1 pending)
2 steps (1 pending, 1 passed)
0m0.367s
```

场景中的第一步通过了，但第二步仍未完成。所以我修改了它:

```
Then /^it should greet the world$/ do
  page.should have_content("Hello world")
end
```

然后我又跑黄瓜:

```
$ bundle exec cucumber
Using the default profile...
Feature: hello world
  I want the application to greet the world every time I use it

  Scenario: greet the world        # features/hello.feature:4
    Given I visit the app          # features/step_definitions/hello_steps.rb:1
    Then it should greet the world # features/step_definitions/hello_steps.rb:5
      undefined local variable or method 'response_body' for #<Object:0x9741db0> (NameError)
      ./features/step_definitions/hello_steps.rb:6:in '/^it should greet the world$/'
      features/hello.feature:6:in `Then it should greet the world'

Failing Scenarios:
cucumber features/hello.feature:4 # Scenario: greet the world

1 scenario (1 failed)
2 steps (1 failed, 1 passed)
0m0.205s
```

这是可以理解的，因为我还没有创建任何可以呈现这种问候的东西。

为了纠正这一点，我运行:

```
$ padrino gen controller events index
```

进而在 **app/controllers** 下创建 **events.rb** ，在 **spec/app/controllers/** 下创建**events _ controller _ spec . Rb**。

如果你打开**spec/app/controllers/events _ controller _ spec . Rb**，你会看到一个默认的例子:

```
require 'spec_helper'

describe "EventsController" do
  before do
    get "/"
  end

it "returns hello world" do
    last_response.body.should == "Hello World"
  end
end
```

巧合的是这正是我想要描述的。

当我运行规范时，像这样:

```
$ padrino rake spec
```

它会失败。这是意料之中的，因为我还没有写任何东西。为了解决这个问题，我在**app/controllers/events . Rb**中为事件控制器添加了一个索引路由:

```
Rubytoday.controllers :events do
  get :index, :map => "/" do
    "Hello world"
  end
end
```

要验证路线，我可以通过 Rake 的任务来完成:

```
$ padrino rake routes
=> Executing Rake routes ...

Application: Rubytoday
    URL                  REQUEST  PATH
    (:events, :index)      GET    /
```

这确认了我已经设置了事件控制器的索引方法的默认根路径。

然后通过 RSpec 进行验证:

```
$ padrino rake spec
=> Executing Rake spec ...

EventsController
  returns hello world (FAILED - 1)

Failures:

1) EventsController returns hello world
     Failure/Error: last_response.body.should == "Hello World"
       expected: "Hello World"
            got: "Hello world" (using ==)
     # ./spec/app/controllers/events_controller_spec.rb:9:in `block (2 levels) in <top (required)>'

Finished in 0.28037 seconds
1 example, 1 failure

Failed examples:

rspec ./spec/app/controllers/events<em>controller</em>spec.rb:8 # EventsController returns hello world
rake aborted!
ruby -S bundle exec rspec -fs --color ./spec/app/controllers/events<em>controller</em>spec.rb failed

Tasks: TOP => spec => spec:app
(See full trace by running task with --trace)
```

这失败了，但是如果你看这个例子，唯一的区别是这个例子期望输出是“Hello world”而不是“Hello World”。错误在拼写上。一旦我改变了这一切…

```
$ padrino rake spec
=> Executing Rake spec ...

<p>EventsController
  returns hello world

<p>Finished in 0.22763 seconds
1 example, 0 failures
```

该示例通过。然后验证黄瓜是否通过:

```
$ bundle exec cucumber
Using the default profile...

<p>Feature: hello world
  I want the application to greet the world every time I use it

Scenario: greet the world        # features/hello.feature:4
    Given I visit the app          # features/step<em>definitions/hello</em>steps.rb:1
    Then it should greet the world # features/step<em>definitions/hello</em>steps.rb:5

1 scenario (1 passed)
2 steps (2 passed)
0m0.402s
```

我还应该通过启动应用程序来检查自己的输出:

```
$ padrino start
=> Padrino/0.10.2 has taken the stage development at http://0.0.0.0:3000
[2011-10-03 00:51:01] INFO  WEBrick 1.3.1
[2011-10-03 00:51:01] INFO  ruby 1.9.2 (2010-08-18) [i686-linux]
[2011-10-03 00:51:01] INFO  WEBrick::HTTPServer#start: pid=16767 port=3000
```

并在浏览器中打开网址: [http://0.0.0.0:3000](http://0.0.0.0:3000) 。

![Hello wold screenshot](img/a665afa9d127c61f191b4426350dc764.png)

Hello wold 截图

这就完成了期望的输出，并演示了 BDD 流程:

黄瓜->RSpec->代码

首先我编写了特性并描述了行为，然后我用 RSpec 编写了示例，最后我实现了代码以适应特性和示例。

现在是最后一步，将它提交给 Git:

```
$ git status
# On branch hello_world
# Changed but not updated:
#   (use "git add/rm <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#   deleted:    features/add.feature
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#   app/controllers/
#   app/helpers/
#   features/hello.feature
#   features/step_definitions/hello_steps.rb
#   spec/app/
no changes added to commit (use "git add" and/or "git commit -a")
```

这列出了所有需要提交给 Git 的新内容以及删除的内容:features/add.feature 文件，我在前面已经删除了该文件。

```
$ git add .
$ git status
# On branch hello_world
# Changes to be committed:
# (use "git reset HEAD <file>..." to unstage)
#
#  new file:   app/controllers/events.rb
#  new file:   app/helpers/events_helper.rb
#  new file:   features/hello.feature
#  new file:   features/step<em>definitions/hello</em>steps.rb
#  new file:   spec/app/controllers/events<em>controller</em>spec.rb
#
# Changed but not updated:
#  (use "git add/rm <file>..." to update what will be committed)
#  (use "git checkout -- <file>..." to discard changes in working directory)
#
#     deleted:    features/add.feature
#
```

我准备新内容，然后添加它:

```
$ git commit -m "Implemented the 'Hello world' example"
[hello_world de9a0d4] Implemented the 'Hello world' example
 5 files changed, 58 insertions(+), 0 deletions(-)
 create mode 100644 app/controllers/events.rb
 create mode 100644 app/helpers/events_helper.rb
 create mode 100644 features/hello.feature
 create mode 100644 features/step_definitions/hello_steps.rb
 create mode 100644 spec/app/controllers/events_controller_spec.rb
```

本系列的第一篇教程到此结束。在下一篇教程中，我将展示如何在 Git 中合并内容，如何将其部署到 Heroku，我将继续构建应用程序的其余部分。如果这是你第一次看帕德里诺，我很想听听你在下面的评论中的想法。所有的代码都可以从这里获得:[https://github.com/RubySource/simic_padrino](https://github.com/RubySource/simic_padrino "https://github.com/RubySource/simic_padrino")。

## 分享这篇文章