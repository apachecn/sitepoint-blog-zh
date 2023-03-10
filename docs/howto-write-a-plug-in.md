# 如何:编写插件

> 原文：<https://www.sitepoint.com/howto-write-a-plug-in/>

在我之前的文章中，我列出了你应该在 Rails 中尝试的 6 件事。我还承诺了一些示例代码来帮助您入门。因为我已经介绍了 rails 的安装和升级，所以下一步就是编写插件。

插件非常棒——它们允许你将普通代码抽象成漂亮的小包，你可以在其他项目中重复使用。Rails 甚至有一个内置系统，可以直接从其他人的 SVN 库中下载插件。随着向 Rails 2.0 的转变，一些曾经是核心的功能被转移到插件中，以清理核心树，并允许其他开发人员在常规 Rails 发布周期之外发布新版本的插件。

在这个(非常简短的)教程中，我们将创建一个名为 acts_as_blabbermouth 的插件，它将打印出随机的报价。显然，这个插件在现实世界中用处不大，但是它应该很好地演示了插件是如何工作的。

多亏了生成脚本，生成样板代码真的很容易。要启动插件，请在 Rails 应用程序的根目录下运行以下命令:

```
 script/generate plugin acts_as_blabbermouth 
```

您应该会看到类似如下的输出:

```
 create  vendor/plugins/acts_as_blabbermouth/lib
create  vendor/plugins/acts_as_blabbermouth/tasks
create  vendor/plugins/acts_as_blabbermouth/test
create  vendor/plugins/acts_as_blabbermouth/README
create  vendor/plugins/acts_as_blabbermouth/MIT-LICENSE
create  vendor/plugins/acts_as_blabbermouth/Rakefile
create  vendor/plugins/acts_as_blabbermouth/init.rb
create  vendor/plugins/acts_as_blabbermouth/install.rb
create  vendor/plugins/acts_as_blabbermouth/uninstall.rb
create  vendor/plugins/acts_as_blabbermouth/lib/acts_as_blabbermouth.rb
create  vendor/plugins/acts_as_blabbermouth/tasks/acts_as_blabbermouth_tasks.rake
create  vendor/plugins/acts_as_blabbermouth/test/acts_as_blabbermouth_test.rb 
```

如您所见，所有的样板代码都创建在 vendor/plugins/acts _ as _ blabbermouth 目录中。

自述文件和 MIT-LICENSE 只是您应该填写的普通文本文件——通常，自述文件是新用户查找插件说明的第一个地方。

lib 目录将保存您的插件的内部内容。任务目录是一个可以存储插件可能需要的任何 rake 任务的地方。测试目录是不言自明的——就像你可以测试你的 Rails 应用一样，你也可以测试你的插件。在安装和卸载插件时会调用 install.rb 和 uninstall.rb 文件——您可以将代码放在这里来初始化您的环境并进行清理。最后，init.rb 是 Rails 实际调用来加载插件的文件。

我们要担心的主要文件是/lib/acts _ as _ blabbermouth . Rb——这是我们的主要代码要去的地方。

首先，我们需要将 acts_as_blabbermouth.rb 文件包含到环境中。这是通过在 init.rb 文件中添加以下行来实现的:

```
 require File.dirname(__FILE__) + '/lib/acts_as_blabbermouth' 

```

接下来，我们将以下代码添加到 lib/acts_as_blabbermouth.rb 中

```
 # ActsAsBlabbermouth
module ActiveRecord #:nodoc:
  module Acts #:nodoc:
    module Blabbermouth #:nodoc:
      def self.included(base)
        base.extend(ClassMethods)
      end

      module ClassMethods
        def acts_as_blabbermouth
          include ActiveRecord::Acts::Blabbermouth::InstanceMethods
          extend ActiveRecord::Acts::Blabbermouth::SingletonMethods
        end
      end

      module SingletonMethods
        def quote_me
          quotes = [
            "When you come to a fork in the road, take it. -Yogi Berra",
            "Every child is an artist. The problem is how to remain an artist once he grows up. -Pablo Picasso",
            "What we anticipate seldom occurs; what we least expected generally happens. -Benjamin Disraeli",
            "Drive-in banks were established so most of the cars today could see their real owners. -E. Joseph Cossman",
            "The greatest pleasure in life is doing what people say you cannot do. -Walter Bagehot"
          ]

          quotes[rand(quotes.size)]
        end
      end

      module InstanceMethods
        def quote_me
          self.class.quote_me
        end
      end
    end
  end
end

ActiveRecord::Base.send(:include, ActiveRecord::Acts::Blabbermouth) 

```

现在，通过运行以下命令创建一个名为 quote.rb 的模型文件

```
script/generate model quote

```

并在类声明之后和结束声明之前添加以下行

```
 acts_as_blabbermouth 

```

启动脚本/控制台并键入:

```
Quote.quote_me

```

瞧啊。如果一切按计划进行，您应该会看到一个随机的报价。恭喜你！你刚刚写了一个插件！

那么我们是怎么做到的呢？

1.  我们深入到 ActiveRecord::Acts 模块，并加入了一个名为 Blabbermouth 的模块。这类似于其他语言中的名称空间，因此我们可以创建自己的类和方法集，而不会践踏其他人的插件。
2.  我们覆盖了 included class 方法，当插件混入另一个模块时会调用该方法。这里，我们包含了 ClassMethods 模块，它向模型类公开了 acts_as_blabbermouth 方法
3.  我们定义 acts_as_blabbermouth 方法。这个方法所做的就是包含 InstanceMethods 和 SingletonMethods 模块。InstanceMethods 模块包含实例化对象的所有可用方法，SingletonMethods 包含未实例化类的所有可用方法。
4.  我们创建一个名为 quote_me 的 SingletonMethod 方法，它返回随机报价。这个可以通过调用 Quote.quote_me 来调用。我们还在 InstanceMethods 模块中创建了一个名为 quote_me 的方法，它调用 singleton method——这样类和对象都可以调用 quote_me 方法。
5.  最后，我们调用 ActiveRecord::Base . send(:include，active record::Acts::Blabbermouth)，它告诉 active record::Base 模块包含我们编写的代码。

对于那些在家玩的人，我已经将[插件源代码附加到一个 tarball](https://www.sitepoint.com/wp-content/uploads/2008/01/acts_as_blabbermouthtar.gz "acts_as_blabbermouth source code") 中，这样你们可以更好地了解它是如何组合在一起的。所以，你可以自己创建一个插件了！

## 分享这篇文章