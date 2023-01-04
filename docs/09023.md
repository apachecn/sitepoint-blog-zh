# 类变量——一个 Ruby 陷阱

> 原文：<https://www.sitepoint.com/class-variables-a-ruby-gotcha/>

Ruby 中的类变量有一个不好的名字。这是真的，他们被认为是有害的，近乎邪恶的。那么，为什么是坏的说唱？好吧，大多数抱怨都是关于他们在继承问题上的表现。更正:这完全是关于他们的行为与我们的期望相比如何。

为了给我们的小问题孩子描绘一幅图画，让我们看看我们对 PHP 的期望。

```
<?php

class DemoClass {
    public $myvar;

  public function __construct() {
    $this->myvar = "hello world : ";
  }

  public function getMyVar() {
    echo $this->myvar;
  }

}
class Demo2Class extends DemoClass {
    public function __construct() {
        $this->myvar = "goodbye world : ";
    }
}

$demo1 = new DemoClass();
$demo2 = new Demo2Class();

$demo1->getMyVar();
$demo2->getMyVar();
$demo1->getMyVar();

// Produces
// hello world : goodbye world : hello world : "
```

这对我来说很有意义。我们用具体的构造函数和抽象的 getter 来定义一个类。当继承时，我们可以覆盖构造函数并将“类变量”设置为其他值。基础类不受影响，世界继续旋转。

在 Ruby 中，如果我们尝试使用类变量做类似的事情，我们会遇到一两个问题。

```
class DemoClass
  @@my_var = nil

  def initialize
    @@my_var = "hello world"
  end

  def my_var
    puts @@my_var
  end
end

class Demo2Class < DemoClass
  def initialize
    @@my_var = "goodby world"
  end
end

demo1 = DemoClass.new
demo1.my_var
demo2 = Demo2Class.new

demo2.my_var
demo1.my_var

# Produces

#hello world

#goodbye world

#goodbye world
```

当你以这种方式处理 Ruby 中的类变量时，它们似乎是错误的。在`Demo2Class`中对`my_var`的改变已经上升到我们的基类。为了缓解这个问题，假设我们再次子类化`DemoClass`，类变量`my_var`的行为是不可预测的。它们是伪装的全局变量。

## 那么为什么要使用类变量呢？

从表面上看，关于阶级变量的谣言是真的。但是现在我们知道了不要做什么，有没有一个场景中类级别的变量实际上是有用的？我承认。我在几乎所有的网络应用程序中都使用它们。

使用我们示例中描述的方法，依赖使用类级变量作为类“状态”实现的初始化数据是一个非常糟糕的想法。然而，我确实为初始化数据使用了类变量。

Rails 应用程序通常至少有 4 个环境，生产、试运行、开发和测试。很可能每个环境的配置都会发生变化，无论是邮件异常的电子邮件地址还是 web 服务沙箱的 URL。代码本身看起来像:

```
class AppConfig
  @@app_config = YAML::load(ERB.new((IO.read("#{RAILS_ROOT}/config/app_config.yml"))).result)[RAILS_ENV]

  def self.method_missing(sym, *args, &block)
    @@app_config[sym.to_s]
  end
end
```

这应该是不言自明的。YAML 文件被解析成类 variable app _ config`. Then using`method _ missing`at class level I can retrieve values easily with`appconfig . exception _ email _ addresses `。

## 类别级配置

所以现在希望我们能同意，类变量并不完全是邪恶的。有时候，我们确实想在一个类层次上分享一些特征。实现这一点而不引发前面描述的问题的常用技术是使用类实例变量。

虽然有点拗口，但类实例变量非常简单，(从 PHP 开始它们没有意义)我们只需要稍微调整一下思路。我总是发现最先发现的最好方法是`irb`。

```
ruby-1.9.2-p290 :001 > class Demo
ruby-1.9.2-p290 :002?>   @my_instance_variable = "whaaa?"
ruby-1.9.2-p290 :003?>   end
 => "whaaa?"
ruby-1.9.2-p290 :004 > Demo.class
 => Class
ruby-1.9.2-p290 :005 > Demo.superclass
 => Object
ruby-1.9.2-p290 :006 > Demo.instance_variables
 => [:my_instance_variable]
```

这就是了，与更经典的语言不同，Ruby 类(像其他任何东西一样)只是带有实例变量的对象。那么这些知识对我们有什么帮助呢？回到上一个例子，使用一点访问器技巧。

```
class DemoClass
  class << self
    attr_accessor :my_var
  end

  @my_var = nil

  def initialize
    self.class.my_var = "hello world"
  end

  def my_var
    puts self.class.my_var
  end
end

class Demo2Class < DemoClass
  def initialize
    self.class.my_var = "goodby world"
  end
end

demo1 = DemoClass.new
demo1.my_var
demo2 = Demo2Class.new

demo2.my_var
demo1.my_var

# Produces

# hello world

# goodbye world

# hello world
```

这个例子现在看起来很可怕，在任何解释之前，让我们把它清理干净，这样它就不会再冒犯我们的眼睛了。

```
class DemoClass
  class << self
    attr_accessor :my_var
  end

  @my_var = "hello world"
end

class Demo2Class < DemoClass
  @my_var = "goodby world"
end

puts DemoClass.my_var
puts Demo2Class.my_var
puts DemoClass.my_var
```

如果你不确定特征类(又名单例类，但我最初是一个 PHP 人，单例类起初意味着完全不同的东西)在这个演示中，我们只是像平常一样用它来创建一个`attr_accessor`，只有这样做才能为我们的类实例变量创建它。

## 包扎

还记得我说过类变量并不总是不好的，并展示了一个当它们完全可以接受时的例子(反正在我的书中是可以接受的)。我确实遇到了那个片段的问题。

我不得不为两个不同的客户复制一份申请。显然，配置数据是不同的，但代码库是相同的。我想在单个 SCM 库中维护代码库(错误修复或更新将适用于两个版本),我不想使用 git merge mashes，事实上我不想让 SCM 在我的工作流中承担新的角色，它只是做 SCM，而不是管理跨客户端的配置。

显然，我们希望将配置保存在数据库中，这在 Rails 中使用如下的`ActiveRecord`模型很容易实现:

```
# app/models/application_config.rb

class ApplicationConfig < ActiveRecord::Base
  serialize :config_value
  after_save :reset_config

  def reset_config
    AppConfig.reset_configs
  end
end

# lib/app_config.rb

class AppConfig
  class << self
    attr_accessor :configs
  end

  @configs = {}

  def self.method_missing(sym, *args, &block)
    @configs[sym.to_s] ||= ""
  end

  def self.reset_configs
    self.configs = {}
    configs = ApplicationConfig.all

  configs.each do |setting|
    self.configs = {setting.config_key => setting.config_value}.merge self.configs
  end
  end
end
```

使用上面的代码，我可以在不重构任何正在访问配置数据的代码的情况下，为应用程序添加一个持久化的配置(这是一个巨大的胜利，因为它是一个相当大的应用程序)。我还可以使用一个简单的脚手架来动态更新和编辑配置。类实例变量的使用允许我缓存配置数据，而不用为类收到的每个`method_missing`消息访问数据库。

但愿，这已经驱散了阶级变量是无用的/邪恶的神话。像大多数事情一样，缺乏理解和幼稚的实现给了类变量一个坏名声。我确实花了一段时间(和许多天真的实现)来理解它们发生了什么。至于特征类的东西，查看 Nathan Kleyns 关于元编程的文章了解更多细节。

## 分享这篇文章