# 学习 Ruby 元编程大有裨益

> 原文：<https://www.sitepoint.com/learn-ruby-metaprogramming-for-great-good/>

![Rr](img/1cc6345cdcb8e5962a4ebcb46e1851a3.png)

Ruby 元编程可能是一件好事。

最近，我在审查我的一个学生的代码。他有一个程序，用许多方法打印出各种信息，例如:

```
class MyClass
  def calculate(a)
    result = a ** 2
    puts "The result is #{result}"
  end
end

class MyOtherClass
  def some_action(a, b)
    puts "The first value is #{a}, the second is #{b}"
  end

  def greet
    puts "Welcome!"
  end
end 
```

我建议他将这些消息存储在一个单独的文件中，以简化处理它们的过程。然后，我想起了 [I18n 翻译](https://www.sitepoint.com/go-global-rails-i18n/)是如何存储在 Rails 中的，有了一个想法。为什么我们不创建一个 YAML 文件(一个所谓的*字典*)包含所有的消息和一个助手方法来正确地获取它们，同时支持像插值这样的附加特性呢？这完全可以通过 Ruby 的元编程实现！

这就是 [messages_dictionary](https://github.com/bodrovis-learning/messages_dictionary) gem(主要用于学习目的)的诞生——我甚至在我的其他几个项目中使用它。因此，在本文中，我们将看到 Ruby 的元编程在从头开始编写这个 gem 时的实际应用。

## 基本宝石结构

让我快速介绍一下 gem 的文件和文件夹:

*   [Gemfile](https://github.com/bodrovis-learning/messages_dictionary/blob/master/Gemfile)
*   一个包含系统信息的 [gemspec](https://github.com/bodrovis-learning/messages_dictionary/blob/master/messages_dictionary.gemspec) 。你可以在 GitHub 上查看 gem 的源代码，看看它们看起来怎么样。
*   [Rakefile](https://github.com/bodrovis-learning/messages_dictionary/blob/master/Rakefile) 包含引导用 RSpec 编写的测试的指令。
*   [。rspec](https://github.com/bodrovis-learning/messages_dictionary/blob/master/.rspec) 包含 rspec 的选项。在这种情况下，例如，我希望测试以随机的顺序运行，默认情况下应该需要 *spec_helper.rb* 文件，并且输出应该显示为 verbose。当然，也可以在从终端运行 RSpec 时设置这些选项。
*   [.travis.yml](https://github.com/bodrovis-learning/messages_dictionary/blob/master/.travis.yml) 包含对 [Travis CI](https://travis-ci.org/) 服务的配置，该服务为每个提交或拉取请求自动运行测试。这是一个非常棒的服务，所以如果你以前从未见过，请尝试一下。
*   README.md 包含 gem 的文档。
*   [spec/](https://github.com/bodrovis-learning/messages_dictionary/tree/master/spec) 包含了 RSpec 中编写的所有测试。我不会在本文中讨论测试，但是您可以自己研究它们。
*   [lib/](https://github.com/bodrovis-learning/messages_dictionary/tree/master/lib) 包含 gem 的主代码。

让我们在 *lib* 目录下开始工作。首先创建一个 *messages_dictionary.rb* 文件和一个 *messages_dictionary* 文件夹。messages_dictionary.rb 将需要所有第三方 gem，以及一些其他文件，并定义我们的模块。有时配置也放在这个文件中，但我们不会这样做。

*lib/messages _ dictionary . Rb*

```
require 'yaml'
require 'hashie'

module MessagesDictionary
end 
```

非常简约。注意这个宝石有两个依赖:YAML 和哈希。YAML 将用于解析*。yml* 文件，而 Hashie 为基本数组和散列类提供了一堆非常酷的扩展。打开[这个 RubyGems 页面](https://rubygems.org/gems/messages_dictionary)，注意 Hashie 被放在 Dependencies 部分下。这是因为在 gemspec 中，我们有以下代码行:

```
spec.add_dependency 'hashie', '~> 3.4' 
```

YAML 解析器是 Ruby core 的一部分，但是 Hashie 是一个定制的解决方案，因此我们必须将其指定为一个依赖项。

现在在 *lib/messages_dictionary* 里面创建一个 *version.rb* 文件:

*lib/messages _ dictionary/version . Rb*

```
module MessagesDictionary
  VERSION = 'GEM_VERSION_HERE'
end 
```

将 gem 的版本定义为常量是一种常见的做法。接下来，在 gemspec 文件中引用该常量:

```
spec.version = MessagesDictionary::VERSION 
```

还要注意，gem 的所有代码都是在模块`MessagesDictionary`下命名的。命名空间非常重要，因为否则，您可能会引入命名冲突。假设有人希望在自己的项目中使用这个 gem，但是已经在某个地方定义了一个`VERSION`常量(毕竟这是一个很常见的名字)。将 gem 的版本放在模块之外可能会重写这个常量，并引入难以检测的错误。因此，为你的 gem 想一个名字，并确保这个名字还没有被使用，然后用这个名字命名你所有的代码。

好了，准备工作已经完成，我们可以开始编码了！

## 动态定义方法

首先，我们来讨论一下，我们希望这块宝石如何使用。当然，在做任何其他事情之前，必须要求:

```
require 'messages_dictionary' 
```

接下来，我们的模块必须包括:

```
class MyClass
  include MessagesDictionary
end 
```

那么应该有一个方法告诉`MessagesDictionary`去做它的工作。姑且称这种方法为`has_messages_dictionary`，灵感来自 Rails 的[拥有*安全*密码](http://api.rubyonrails.org/classes/ActiveModel/SecurePassword/ClassMethods.html#method-i-has_secure_password):

```
class MyClass
  include MessagesDictionary
  has_messages_dictionary
end 
```

用户的下一步是创建一个*。包含消息的 yml* 文件:

```
hi: "Hello there!" 
```

最后，为了显示这个消息，必须调用一个特殊的方法:

```
class MyClass
  include MessagesDictionary
  has_messages_dictionary

  def greet
    pretty_output(:hi) # Prints "Hello there!" in the terminal
  end
end 
```

这是一些基本的功能，但是我们稍后将扩展它。

在 *lib/messages_dictionary* 目录下新建一个名为 *injector.rb* 的文件。最大的问题是如何在运行中为一个类配备一个额外的方法`has_messages_dictionary`？幸运的是，Ruby 提供了一个叫做`included`的特殊钩子方法，一旦一个模块被包含到一个类中，这个方法就会运行。

*lib/messages _ dictionary/injector . Rb*

```
module MessagesDictionary
  def self.included(klass)
  end
end 
```

`included`是一个类方法，所以我需要给它加上前缀`self`。此方法接受一个对象，该对象表示包含此模块的类。注意，我故意将这个局部变量称为`klass`，因为`class`是 Ruby 中的保留字。

我们下一步想做什么？很明显，定义一个名为`has_messages_dictionary`的新方法。然而，我们不能为此使用`def`——这必须在运行时动态完成。另外，请注意，`has_messages_dictionary`必须是一个类方法，因此我们必须使用[定义*单例*方法](http://ruby-doc.org/core-2.2.2/Object.html#method-i-define_singleton_method)。如果你想了解更多关于单例方法的知识，请观看我关于它们的[截屏。简单来说，类方法就是单体方法。](https://www.sitepoint.com/premium/screencasts/learning-about-the-uses-of-singleton-methods-in-ruby)

然而，有一个小问题。如果我像这样使用`define_singleton_method`

```
module MessagesDictionary
  def self.included(klass)
    define_singleton_method :has_messages_dictionary do |opts = {}|
    end
  end
end 
```

那么这个方法将在`MessagesDictionary`模块中定义**，而不是在类中定义！因此，我们不得不使用另一个叫做 [class_exec](http://ruby-doc.org/core-2.2.2/Module.html#method-i-class_exec) 的方法，正如你可能已经猜到的那样，在某个类的上下文中评估某些代码:**

*lib/messages _ dictionary/injector . Rb*

```
module MessagesDictionary
  def self.included(klass)
    klass.class_exec do
      define_singleton_method :has_messages_dictionary do |opts = {}|
      end
    end
  end
end 
```

注意，`define_singleton_method`有一个名为`opts`的局部变量，默认设置为空散列。如果我们不必动态定义这个方法，相应的代码看起来会更熟悉:

```
def self.has_messages_dictionary(opts = {})
end 
```

使用`included`钩子并在另一个类的上下文中定义一些方法的概念非常普遍，例如，在[设计](https://github.com/plataformatec/devise/blob/9a11586a724487dc98dddea0ab9c8afcc0e9439d/lib/devise/test/integration_helpers.rb#L20)中就使用了这种概念。

## 打开文件

接下来，我们的代码应该打开一个包含消息的文件。为什么我们不期望这个文件以类名命名呢？例如，如果类被称为`MyClass`，那么文件应该是 *my_class.yml* 。我们唯一需要做的是将类名从 camel 转换为 snake case。尽管 Rails 确实有这样的方法，但是 Ruby 没有提供，所以让我们为它定义一个单独的类。`snake_case` case 方法的代码取自 Rails ActiveSupport 模块:

*lib/messages _ dictionary/utils/snake _ case . Rb*

```
module MessagesDictionary
  class SpecialString
    attr_accessor :string

    def initialize(string)
      @string = string
    end

    def snake_case
      string.gsub(/::/, '/').
          gsub(/([A-Z]+)([A-Z][a-z])/,'\1_\2').
          gsub(/([a-z\d])([A-Z])/,'\1_\2').
          tr("-", "_").
          downcase
    end
  end
end 
```

当然，我们可能已经重新打开了一个现有的`String`类，但是用户的应用程序可能已经定义了这样的方法，我们不想重新定义它。

使用这个新的助手类:

*injector.rb*

```
# ...
define_singleton_method :has_messages_dictionary do |opts = {}|
  file = "#{SpecialString.new(klass.name).snake_case}.yml"
end 
```

下一步是加载一个文件，如果没有找到，就暂停程序的执行:

*injector.rb*

```
# ...
define_singleton_method :has_messages_dictionary do |opts = {}|
  file = "#{SpecialString.new(klass.name).snake_case}.yml"
  begin
    messages = YAML.load_file(file)
  rescue Errno::ENOENT
    abort "File #{file} does not exist..." # you may raise some custom error instead
  end
end 
```

将包含基于文件内容的散列，但我想为用户提供一些灵活性，允许他们通过提供符号或字符串作为密钥来访问消息。这被称为[冷漠访问](https://github.com/intridea/hashie#indifferentaccess)，哈希确实支持它。

为了添加这个特性，定义一个名为`Dict`的特殊类，并在那里添加适当的模块:

*lib/messages _ dictionary/utils/dict . Rb*

```
module MessagesDictionary
  class Dict < Hash
    include Hashie::Extensions::MergeInitializer
    include Hashie::Extensions::IndifferentAccess
  end
end 
```

现在稍微调整一下主文件:

*injector.rb*

```
# ...
messages = Dict.new(YAML.load_file(file))
# ... 
```

本节的最后一步是将这些消息存储在某个地方。让我们用一个类常数来表示。下面是生成的代码:

*injector.rb*

```
module MessagesDictionary
  def self.included(klass)
    klass.class_exec do
      define_singleton_method :has_messages_dictionary do |opts = {}|
        file = "#{SpecialString.new(klass.name).snake_case}.yml"
        begin
          messages = Dict.new(YAML.load_file(file))
        rescue Errno::ENOENT
          abort "File #{file} does not exist..."
        end
        klass.const_set(:DICTIONARY_CONF, {msgs: messages})
      end
    end
  end
end 
```

`const_set`为类动态创建一个名为`DICTIONARY_CONF`的常量。这个常量包含我们的消息的散列。稍后我们将在这个常量中存储附加选项。

## 支持选项

我们的剧本开始有了些眉目，但是太死板了。这里有一些需要介绍的明显的增强功能:

*   应该可以提供一个自定义的消息文件
*   目前，消息文件必须和脚本放在同一个目录下，这不太方便。因此，应该可以定义文件的自定义路径。
*   在某些情况下，将带有消息的散列直接传递给脚本可能比创建文件更方便。
*   应该可以存储嵌套消息，就像在 Rails 的本地文件中一样。
*   默认情况下，所有消息都将使用`puts`方法打印到`STDOUT`中，但是用户可能想要改变这一点。

其中一些功能看起来可能很复杂，但实际上，它们实现起来有些简单。让我们从自定义路径和文件名开始。

### 提供自定义路径

如果用户想要重新定义默认路径名，我们将指导他们提供`:dir`和`:file`选项。以下是该脚本的新版本:

*injector.rb*

```
# ...
define_singleton_method :has_messages_dictionary do |opts = {}|
  file = "#{SpecialString.new(klass.name).snake_case}.yml"
  begin
    file = opts[:file] || "#{SpecialString.new(klass.name).snake_case}.yml"
    file = File.expand_path(file, opts[:dir]) if opts[:dir]
  rescue Errno::ENOENT
    abort "File #{file} does not exist..."
  end
  klass.const_set(:DICTIONARY_CONF, {msgs: messages})
end 
```

基本上我们只修改了两行代码。我们要么获取用户提供的文件名，要么基于类名生成它，然后使用 [expand_path](http://ruby-doc.org/core-2.2.2/File.html#method-c-expand_path) 方法，如果他们提供了目录的话。

现在用户可以提供这样的选项:

```
has_messages_dictionary file: 'test.yml', dir: 'my_dir/nested_dir' 
```

### 传递消息散列

这是一个易于实现的特性。只需提供对`messages`选项的支持:

*injector.rb*

```
# ...
define_singleton_method :has_messages_dictionary do |opts = {}|
  if opts[:messages]
    messages = Dict.new(opts[:messages])
  else
    file = opts[:file] || "#{SpecialString.new(klass.name).snake_case}.yml"
    file = File.expand_path(file, opts[:dir]) if opts[:dir]
    begin
      # ...
    end
  end
end 
```

现在消息可以以散列的形式提供:

```
has_messages_dictionary messages: {hi: 'hello!'} 
```

### 支持嵌套和显示消息

这个特性有点复杂，但是在 Hashie 的 [deep_fetch](https://github.com/intridea/hashie#deepfetch) 方法的帮助下是完全可行的。它接受一个或多个表示散列的键(或数组的索引)的参数，并尽最大努力找到相应的值。例如，如果我们有这个散列:

```
user = {
  name: { first: 'Bob', last: 'Smith' }
} 
```

你可以说:

```
user.deep_fetch :name, :first 
```

去找“鲍勃”。此方法还接受在找不到请求的值时运行的块:

```
user.deep_fetch(:name, :middle) { |key| 'default' } 
```

然而，在使用这个方法之前，我们需要用新的功能扩展我们的对象:

*injector.rb*

```
# ...
klass.const_set(:DICTIONARY_CONF, {msgs: messages.extend(Hashie::Extensions::DeepFetch)})
# ... 
```

让我们决定用户应该如何提供嵌套值的路径。为什么我们不使用 Rails 的 I18n 方法，其中键用`.`分隔:

```
pretty_output('some_key.nested_key') 
```

是时候添加实际的`pretty_output`方法了:

*injector.rb*

```
# ...
define_method :pretty_output do |key|
  msg = klass::DICTIONARY_CONF[:msgs].deep_fetch(*key.to_s.split('.')) do
    raise KeyError, "#{key} cannot be found in the provided file..."
  end
end 
```

获取密钥并通过`.`进行分割，得到一个数组。然后数组的元素应该被转换成方法的参数，这就是为什么我们给这个命令加上前缀`*`。接下来，获取请求的值，如果没有找到，则引发一个错误。

### 输出消息

最后，我们将显示消息，同时允许重新定义输出位置和要使用的方法。我们姑且称这两个新选项为`output`(默认为`STDOUT`)和`method`(默认为`:puts`):

*injector.rb*

```
# ...
klass.const_set(:DICTIONARY_CONF, {msgs: messages.extend(Hashie::Extensions::DeepFetch),
                                   output: opts[:output] || STDOUT,
                                   method: opts[:method] || :puts})
# ... 
```

接下来只需使用这些选项。只要我们在动态调用一个方法，而不知道它的名字，就使用 [send](http://ruby-doc.org/core-2.2.2/Object.html#method-i-send) :

*injector.rb*

```
# ...
define_method :pretty_output do |key|
  msg = klass::DICTIONARY_CONF[:msgs].deep_fetch(*key.to_s.split('.')) do
    raise KeyError, "#{key} cannot be found in the provided file..."
  end

  klass::DICTIONARY_CONF[:output].send(klass::DICTIONARY_CONF[:method].to_sym, msg)
end 
```

宝石快完成了，只剩下几个项目了。

## 插入文字

将值插入字符串是一种非常常见的做法，所以，当然，我们的程序应该支持它。我们只需要选择一些特殊的符号来标记插值占位符。我会选择[车把式](http://handlebarsjs.com/) `{{`和`}}`，当然你也可以选择其他的:

```
show_result: "The result is {{result}}. Another value is {{value}}" 
```

然后，这些值将作为哈希进行传递:

```
pretty_output(:show_result, result: 2, value: 50) 
```

这意味着`pretty_output`方法应该再接受一个参数:

*injector.rb*

```
# ...
define_method :pretty_output do |key, values = {}|
end 
```

为了用实际值替换占位符，我们将坚持使用`gsub!`:

*injector.rb*

```
# ...
define_method :pretty_output do |key, values = {}|
  msg = klass::DICTIONARY_CONF[:msgs].deep_fetch(*key.to_s.split('.')) do
    raise KeyError, "#{key} cannot be found in the provided file..."
  end

  values.each do |k, v|
    msg.gsub!(Regexp.new('\{\{' + k.to_s + '\}\}'), v.to_s)
  end

  klass::DICTIONARY_CONF[:output].send(klass::DICTIONARY_CONF[:method].to_sym, msg)
end 
```

## 定制转换和最终确定 Gem

要实现的最后一个特性是为消息提供定制的*转换*的能力。我指的是应该应用于获取的字符串的用户定义的操作。例如，有时您可能希望只获取字符串，而不在任何地方显示它。其他时候，您可能希望将其大写或去掉一些符号，等等。

因此，我希望我们的`pretty_output`方法接受一个带有一些自定义代码的可选块:

*injector.rb*

```
# ...
define_method :pretty_output do |key, values = {}, &block|
end 
```

如果提供了代码块，只需运行代码，否则，执行默认操作:

*injector.rb*

```
# ...
define_method :pretty_output do |key, values = {}, &block|
  # ...
  block ?
      block.call(msg) :
      klass::DICTIONARY_CONF[:output].send(klass::DICTIONARY_CONF[:method].to_sym, msg)
end 
```

通过删除程序块名称中的`&`符号，我们将它变成一个过程。接下来，简单地使用[调用](http://ruby-doc.org/core-2.2.2/Proc.html#method-i-call)方法来运行这个过程，并将我们的消息传递给它。

现在可以像这样提供转换:

```
pretty_output(:welcome) do |msg|
  msg.upcase!
  msg # => Returns "WELCOME", does not print anything
end 
```

有时，为整个类提供转换逻辑可能比单独传递一个块更好:

*injector.rb*

```
# ...
define_singleton_method :has_messages_dictionary do |opts = {}|
  # ...
  klass.const_set(:DICTIONARY_CONF, {msgs: messages.extend(Hashie::Extensions::DeepFetch),
                                     output: opts[:output] || STDOUT,
                                     method: opts[:method] || :puts,
                                     transform: opts[:transform]})
end

define_method :pretty_output do |key, values = {}, &block|
  # ...
  transform = klass::DICTIONARY_CONF[:transform] || block
  transform ?
      transform.call(msg) :
      klass::DICTIONARY_CONF[:output].send(klass::DICTIONARY_CONF[:method].to_sym, msg)
end 
```

你可以说`block || klass::DICTIONARY_CONF[:transform]`来使传递给单个方法的块更优先。

我们已经完成了 gem 的特性，现在让我们来完成它。`pretty_output`是一个实例方法，但是我们可能不希望从类的外部调用它。因此，让我们将其设为私有:

*injector.rb*

```
# ...
define_method :pretty_output do |key, values = {}, &block|
  # ...
end
private :pretty_output 
```

名称`pretty_output`很好，但是有点太长，所以我们为它提供一个别名:

*injector.rb*

```
# ...
private :pretty_output
alias_method :pou, :pretty_output 
```

现在显示一条消息就像说

```
pou(:welcome) 
```

最后一步是要求所有文件按正确的顺序排列:

*lib/messages _ dictionary . Rb*

```
require 'yaml'
require 'hashie'

require_relative 'messages_dictionary/utils/snake_case'
require_relative 'messages_dictionary/utils/dict'
require_relative 'messages_dictionary/injector'

module MessagesDictionary
end 
```

## 结论

在本文中，我们已经看到了 Ruby 的元编程如何在现实世界中使用，并编写了一个`MessagesDictionary` gem，允许我们轻松地获取和处理字符串。希望你现在对使用像`included`、`define_singleton_method`、`send`这样的方法，以及使用 blocks 更有信心了。

最终结果连同完整的文档可在 GitHub 上获得。您也可以在那里找到 RSpec 测试。请随意建议您自己对这个宝石的改进，毕竟，它是为学习目的而创建的。一如既往，我感谢你和我呆在一起，并很快看到你！

## 分享这篇文章