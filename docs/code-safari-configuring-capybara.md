# Code Safari:配置水豚

> 原文：<https://www.sitepoint.com/code-safari-configuring-capybara/>

欢迎来到 Code Safari。在这个系列中，Xavier Shay 将引导您浏览流行的 gem 和库的源代码，发现新的技术和习惯用法。

Capybara 提供了一个用于与网页交互的 Ruby DSL。它通常用于测试基于机架的应用程序(如 Ruby on Rails 或 Sinatra)，但也可以与一起独立使用来驱动任何网站。

我注意到了 Capybara 的配置语法——这是 ruby 库世界中相当常见的习语——并对它的工作原理很感兴趣。

```
Capybara.configure do |config|
  config.default_wait_time = 2
end
```

让我们来了解一下！拿到水豚的源代码，我们就可以开始洞穴探险了。

```
git clone https://github.com/jnicklas/capybara.git
```

首先，让我们试着找到我们感兴趣的配置代码。搜索方法名通常是一个好的开始。

```
$ ack configure lib
lib/capybara.rb
25:    #     Capybara.configure do |config|
46:    def configure
166:Capybara.configure do |config|
```

(ack 是一个更好的 grep——参见文档了解更多信息)

头奖！我们一开始就找到了我们想要的方法。打开文件找到方法定义。

```
# lib/capybara:46
def configure
  yield self
end
```

好奇:`yield`是将`configure`块的参数设置为`self`，但是在这个上下文中`self`是什么？你也许可以通过扫描文件的其余部分来找到它，但是我们也可以通过直接跳进去用`irb`执行代码来开始利用 ruby 的灵活性。您可以使用`-I`标志向加载路径添加一个目录，确保我们能够需要我们正在查看的文件，而不是可能存在于我们系统中其他地方的另一个版本。

```
# In cloned capybara directory
$ irb -Ilib
irb> require 'capybara'
 => true
irb> Capybara.configure {|config| puts config.inspect }
Capybara
 => nil 
irb> Capybara.configure {|config| puts config.class.inspect }
Module
 => nil
```

我们在这里看到`self`实际上是`Capybara`模块。这很有趣:通常我们认为`self`是对类的实例的引用，但在这种情况下，它是实际的*类对象*。这意味着下面两行是等价的:

```
Capybara.default_wait_time = 2
Capybara.configure {|config| config.default_wait_time = 2 }
```

`configure`提供了不同于直接设置模块访问器的抽象层次，这使得 Capybara 在未来有可能重构其存储偏好的方式。

这种机制是如何工作的开始变得越来越清晰，但是仍然有一些没有答案的问题。如何在模块上定义`configure`，如何定义模块属性？答案在文件的更上面一点，我们看到以下模式:

```
# lib/capybara.rb, trimmed to size
module Capybara
  class << self
    attr_accessor :default_wait_time

    def configure
      self
    end
  end
end
```

这里的神奇之处在于`class , which effectively says “anything inside here belongs to the class, not instances of the class.” (It’s actually little more subtle than that, but that definition is good enough for now.) Traditionally `attr_accessor`被用来定义一个实例的属性，比如:`

```
class Person
  attr_accessor :name
end

p = Person.new
p.name = 'Don'
p.name # => 'Don'
```

我们可以从 Capybara 代码中看到，事实上我们可以用它来定义任何实例的属性，甚至像`Module`这样奇怪的实例。

## 回顾

在阅读 Capybara 源代码以了解配置块是如何实现的过程中，我们学习了两种技术:

*   产生一个构建器对象
*   在`Module`上定义访问器

我们可以使用这些知识来创建我们自己的类似配置系统。

```
module MyApp
  class << self
    attr_accessor :special_number

    def configure
      yield self
    end
  end
end

MyApp.configure do |config|
  config.special_number = 42
end

puts "The special number is #{MyApp.special_number}."
```

为了练习您的代码阅读，这里有一些其他的研究任务供您尝试:

*   RSpec 使用看起来相似的 DSL 进行配置，它的工作方式相同吗？
*   [机械师](http://github.com/notahat/machinist)使用一种完全不同的技术，在没有产生参数的情况下，允许用挡块*进行配置。弄清楚它是如何工作的。*

请在评论中告诉我们你的进展。下周请继续关注代码丛林中更激动人心的冒险。

## 分享这篇文章