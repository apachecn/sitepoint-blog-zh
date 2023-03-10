# loccasinos:安装导轨第 2 部分

> 原文：<https://www.sitepoint.com/rails-development-101-installing-rails-part-2/>

## Rails Gem 依赖项，续

上次我们停止了 tz info gem[，在本文中我们将从 ERubis 开始。](https://www.sitepoint.com/rails-development-101-installing-rails-part-2/)

[Erubis](http://www.kuwata-lab.com/erubis/)
Erubis 是 eRuby 的一个实现，是 Ruby 视图中`之间的 Ruby 表达式。`

 `ActionPack 是一件大事。rubydoc for ActionPack 声明它“提供了 MVC 范例中的视图和控制器层”。你可以说它是 Rails 的三分之二。它包括 ActionDispatch(路由和 HTTP 善良，像缓存、会话和 cookies)，ActionController(为 Rails 控制器提供基类)。ActionView(处理 HTML 或 ATOM 提要等格式的视图和呈现)。

如果你想真正了解 Rails，深入研究 ActionPack 会让你走得更远。

Arel 给了 ActiveRecord(有效的 Rails 3)很酷的语法。它是一个“SQL AST 管理器”，其中“AST”意味着“抽象语法树”。AST 是超级书呆子的概念之一，它将天才与其他人区分开来。解释一个 AST 是*方式*超出了本文的范围(更不用说我大脑的范围了)。

[active record](http://ar.rubyonrails.org/)
active record 是 Rails 使用的默认对象关系映射器(ORM)。基本上，它将您的模型映射到数据库结构。如果你愿意，可以使用其他 ORM，比如[数据映射器](http://ar.rubyonrails.org/)。

[active resource](http://api.rubyonrails.org/classes/ActiveResource/Base.html)
active resource 是赋予 Rails 漂亮、 [RESTful 接口](http://en.wikipedia.org/wiki/Representational_State_Transfer)的宝石，这(在作者看来)可能是 Rails 最好的特性。理解 REST 是成为优秀 web 开发人员的关键。O'Reilly 的书《RESTful Web Services》是开始学习 REST 的一个好地方。

[MIME 类型](http://mime-types.rubyforge.org/)
MIME 类型 gem 被 Rails 用来标识请求的 MIME 类型，比如映射`text/html`到 HTML 等。

[Polyglot](http://polyglot.rubyforge.org/)Polyglot 注册要与 Ruby 一起使用的文件扩展名*需要*语句。所以，如果你想用一个。funk '扩展。funk 的分机可以用多种语言注册。然后，`require 'wegotthe'`会找到一个名为“wegotthe.funk”的文件。

Treetop
Treetop 是另一个处理大师级概念的瑰宝，允许开发者轻松创建语法和解析“表达式语法”。Treetop 以其最基本的方式被用来为语言添加语法(我会解释更多，但这篇文章会爆炸)。

[邮件](https://github.com/mikel/mail)和[动作邮件](http://api.rubyonrails.org/classes/ActionMailer/Base.html)

这些 gem 专注于(你猜对了)从 Rails 发送邮件。Mail 处理电子邮件的生成、发送和解析，而 ActionMailer 创建“邮件程序”(想想:电子邮件的控制器)和邮件模板的视图。

托尔
托尔帮助构建命令行工具和实用程序。它经常被看作是默认的 Ruby make 工具 Rake 的竞争对手，并且拥有一批忠实的追随者。托尔是那些你应该好好研究并加入你的红宝石武库的宝石之一(以奥丁的胡子起誓！).

[Rack SSL](https://github.com/josh/rack-ssl)
Rack SSL gem 提供了一个中间件来支持传输层安全(TLS)，最常见的是安全套接字层(SSL)。这里的是一篇关于它的用法的好文章。

RDoc
RDoc 简化了基于 HTML 的文档的创建，并用于记录 Rails 本身。

[钢轨](http://rubydoc.info/gems/railties/3.0.9/frames)
钢轨，从 3.0 开始，已经包含在核心钢轨中。Railties(可以说)是扩展 Rails 的方式，许多主要组件，如 ActionMailer、ActionController 和 ActionView 都是 Railties。理解 Rails 如何工作是初露头角的 Rails 开发人员的另一个关键任务。[这里的](http://www.igvita.com/2010/08/04/rails-3-internals-railtie-creating-plugins/)是一篇吊你胃口的好文章。

Bundler 是 Rails 管理其 gem 依赖项的方式。Bundler 使用 Rails 应用程序根中的 gem 文件来确保所有必要的 gem 都可用，并识别任何冲突。花些时间在[捆绑网站](http://gembundler.com/)上看看有多少装载宝石的选项。

[Rails](http://github.com/rails/rails) 如果您查看 Github 上的源代码，您会发现它包含了这两篇文章中讨论的许多精华。您可能想在这里开始您的代码浏览。

我们现在已经讨论了 Rails 3.1 中的 gem 依赖性。尽管 Rails 以简单的 web 框架而闻名，但在构建这种简单性方面还有很多工作要做。

## Rails 应用程序生成

终于到了生成我们的 Rails 应用的时候了。如你所料，在创建应用程序之前，我将浏览所有可用的选项，所以请准备好进行另一次深入研究。让我们从查看 Rails 可执行文件开始，这是 Rails gem 的一个特殊部分。【旁白:我喜欢使用 [open_gem](https://github.com/adamsanderson/open_gem) gem 在我的编辑器(vim)中快速打开一个特定的 gem。一旦安装了 open_gem，您只需输入“gem open ”,命令行和您的编辑器就会在 gem 的目录中打开。rails gem 目录只有一个包含 *rails* 可执行文件的 bin 子目录。这个可执行文件的源代码非常简洁:

```
#!/usr/bin/env ruby 
if File.exists?(File.join(File.expand_path('../..', __FILE__), '.git')) 
  railties_path = File.expand_path('../../railties/lib', __FILE__) 
  $:.unshift(railties_path) 
end 
require "rails/cli"
```

简而言之，运行一个检查来查看当前环境是否是从本地源代码(即 Git 存储库)运行的，结果是到 railties gem 的路径发生了变化。之后需要 *cli* 文件。这个文件位于 railties gem 中，主要检查我们是否在 Rails 应用程序目录中。如果我们在一个 rails 应用程序中，那么 Rails 可执行文件的行为就像一个脚本，否则我们将生成一个新的应用程序或插件。

现在，我们想专注于创建一个应用程序，它由 railties gem 中的*rails/commands/application . Rb*处理。

```
require 'rails/version' 
if ['--version', '-v'].include?(ARGV.first) 
  puts "Rails #{Rails::VERSION::STRING}" 
  exit(0) 
end 
if ARGV.first != "new" 
  ARGV[0] = "--help" 
else 
  ARGV.shift 
end 

require 'rubygems' 
if ARGV.include?("--dev") require 'rails/generators' 

require 'rails/generators/rails/app/app_generator' 
module Rails 
  module Generators 
    class AppGenerator 
    # We want to exit on failure to be kind to other libraries 
    # This is only when accessing via CLI 
      def self.exit_on_failure? 
        true 
      end 
    end 
  end 
end 

Rails::Generators::AppGenerator.start
```

在版本检查之后，脚本确保“new”是第一个参数，否则它强制使用“–help”参数。这种检查解决了您可能会意外地错误输入一个参数，从而导致在错误输入的目录名中出现一个新的 Rails 应用程序的问题(我过去经常这样做！).

如果一切正常，我们将 *AppGenerator* 拉进来并启动它。 *AppGenerator* 代码也在 railties gem 中找到，在*rails/generators/rails/app/app _ generator . Rb*中。这个文件的完整源代码有点太大了，不能发布，但是你应该打开它[看一下](https://github.com/rails/rails/blob/master/railties/lib/rails/generators/rails/app/app_generator.rb)。我惊喜地发现代码是如此简单，并立即看到我可以如何修改 Rails 应用程序的生成。同一个目录有一个 *templates* 目录，它(您可能已经猜到了)是一个新的 Rails 应用程序的基本目录结构。 *app_generator.rb* 文件中定义了几个类:*Generators::app generator*和 *AppBuilder* 。

在 *AppBuilder* 类中有像`copy_file`和`empty_directory_with_gitkeep`这样的方法，它们很好地说明了如何从模板创建一个新的应用程序。如果您想在自己的项目中生成任何类型的目录结构，学习 AppBuilder 类会展示一种奇妙且可维护的方法。

从 *AppBase* 继承而来的 *AppGenerator* 类。 *AppBase* (在 railties gem 的 rails/generators/app_base.rb 中找到)定义了生成应用程序的绝大多数可用选项，这将很快介绍(意思是下一篇文章)。 *AppGenerator* 包含一个 *AppBuilder* 类，它处理应用程序结构的实际构建。您可以覆盖 builder 类，作为“rails new”命令选项的一部分。

在下一篇文章中，我们将讨论创建一个新的 rails 应用程序的每一个选项，以及为什么您可能想要使用这些选项。我的目标是生成我们的 Rails 应用程序，这样我们就有了有形的东西。我希望您喜欢这篇关于 Rails 入门的更深入的文章。如果你想看一些关于 Rails 的介绍，或者认为我遗漏了什么，请在下面留下评论。

## 分享这篇文章`