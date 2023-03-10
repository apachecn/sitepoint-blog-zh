# PHP 和 Ruby——让我们好好相处吧

> 原文：<https://www.sitepoint.com/php-vs-ruby-lets-all-just-get-along/>

您经常会看到在一种语言上有丰富经验的开发人员尝试使用另一种语言，然后在两者之间进行快速比较。这种比较通常是没有价值的，但是 clickbait 标题为他们带来了大量的流量。

相反，我认为从一个真正喜欢编写 PHP 和 Ruby 并且已经这样做了很多年的人的角度来看，做一个稍微公平一点的比较会很有趣。这里的目的不是找出哪个“更好”，而是指出我喜欢 Ruby 及其生态系统的几个关键之处。

![Screenshot 2015-11-21 01.20.42](img/07a3629d3693c31e1c03f998eaf8eb5b.png)

## 概念差异

不同的语言经常被比较，以找出哪种更好，而构成它们的差异更多的是意识形态上的。有时候，一件事情对一群人来说似乎更好，而同样的事情却让这种语言成为其他开发人员的噩梦。

考虑到这一点，在我开始“比较我喜欢的 Ruby”之前，我认为解释一些概念上的差异是很重要的。

### 方法、变量、属性？

PHP 提供了不同的语法来访问属性、方法或变量。露比没有。

**PHP**

```
$this->turtle   # Instance Property
$this->bike()   # Method
$apple          # Variable 
```

**红宝石**

```
@turtle         # Instance Property
turtle          # "Instance Property" using attr_reader: :turtle
bike            # Method
apple           # Variable 
```

*学究们在这里会指出，`attr_reader :turtle`会动态定义一个方法，作为`@turtle`的 getter，让`turtle`和`bike`成为同一个东西。一个 PHP 开发人员在查看没有明确定义方法或变量名的`turtle`的用法时，会对它的出处感到非常困惑。*

这不应该是经常给你带来麻烦的事情，但是我偶尔会遇到。有时候你团队中的人可能会把事情从一个`attr_reader`改变成一个完整的方法，或者相反，这可能会引起连锁反应。

也就是说，它可以允许一些真正灵活的 API，让你在最激动的时候做一些你感激的厚颜无耻的事情。

删除了一个字段，但是有大量代码，JSON 契约仍然希望它在那里？

```
class Trip
  def canceled_at
    nil
  end
end 
```

那会很好地工作。任何调用`trip.canceled_at`的东西都会为这个字段获取一个`nil`，这很好。我们稍后会适当地移除它。

### 类型提示与鸭类型

在 PHP 世界中，类型提示是一件奇怪而奇妙的事情。像 Golang 这样的语言绝对需要你为参数定义一个类型，并返回类型。PHP 在 5.0 中为参数增加了可选的类型提示。您可能需要数组、特定的类名、接口或抽象，以及最近的可调用性。

PHP 7.0 最终允许返回类型提示，并支持提示`int`、`string`、`float`等。这是用[标量类型提示 RFC](https://wiki.php.net/rfc/scalar_type_hints_v5) 完成的。这个 RFC 被激烈地争论和辩论，但是它进入了那里，并且它仍然是完全可选的；对于 PHP 的各种用户来说，这是个好消息。

Ruby 实际上没有这些。

Duck Typing 是实现这一目标的方法，PHP 世界中的一些人也认为这是一种更好的方法。

不要说:“参数需要是实现 FooInterface 的类的一个实例”并且知道`FooInterface`将有一个`bar(int $a, array $b)`方法，你实际上应该说:“参数可以是响应`bar`方法的任何东西，如果没有，我们可以做点别的。”

**红宝石**

```
def read_data(source)
  return source.read if source.respond_to?(:read)
  return File.read(source.to_str) if source.respond_to?(:to_str)
  raise ArgumentError
end

filename = "foo.txt"
read_data(filename) #=> reads the contents of foo.txt by calling 
                    #   File.read()

input = File.open("foo.txt")
read_data(input) #=> reads the contents of foo.txt via 
                 #   the passed in file handle 
```

这确实很灵活，但对某些人来说这是一种代码味道。特别是在 PHP 这样的语言中，a `int(0)`或`int(1)`被认为是弱模式下的有效布尔项，接受任何值并希望它正确工作可能是一个可怕的举动。

在 PHP 世界中，我们可能只定义了两种不同的方法/函数:

```
function read_from_filename(string $filename)
{
    $file = new SplFileObject($filename, "r");
    return read_from_object($file);
}

function read_from_object(SplFileObject $file)
{
  return $file->fread($file->getSize());
}

$filename = "foo.txt";
read_from_filename($filename);

$file = new SplFileObject($filename, "r");
read_from_object($file); 
```

也就是说，如果我们想在 PHP 中使用完全相同的 duck 类型方法，我们可以很容易地做到:

```
function read_data($source)
{
    if (method_exists($source, 'read')) {
        return $source->read();
    } elseif (is_string($source)) {
        $file = new SplFileObject($source, "r"));
        return $file->fread($file->getSize());
    }
    throw new InvalidArgumentException;
}

$filename = "foo.txt";
read_data($filename); #=> reads the contents of foo.txt by calling 
                      #   SplFileObject->read();

$input = new SplFileObject("foo.txt", "r");
read_data($input); #=> reads the contents of foo.txt via 
                   #   the passed in file handle 
```

你可以在 PHP 中做任何一件事。PHP 不在乎。

假装 Ruby“更好”是因为它使用了 duck 类型可能会被误导，但这很常见。您可能更喜欢这种方法，PHP 可以做到这两者，但是基本上，Ruby 缺少 PHP 所具有的一个特性。我觉得能够随心所欲确实是 PHP 的一大优势，因为即使开发者想用 Ruby 输入提示，也是完全不可能的。

也就是说，有许多 PHP 开发人员强烈反对类型提示，他们希望根本没有类型提示，当 PHP 7.0 中添加了更多类型提示时，他们感到很沮丧。

有趣的是，Python 曾经和 Ruby 一样完全缺乏类型提示。然后最近他们[加了他们](https://docs.python.org/3/library/typing.html)。我很想知道有多少人在那次转变中翻了桌子。

## 有趣的功能

一旦我接受了这些差异，我就能专注于有趣的事情。这些是我开始注意到自己经常使用，或者不太经常使用的功能。

### 嵌套类

对于 PHP 开发人员来说，嵌套类听起来有点陌生。我们的类存在于名称空间中，一个类和一个名称空间可以共享同一个名称。所以，如果我们有一个只与一个类相关的类，我们命名它，就这样。

所以，我们有一个叫做`Box`的类，它可以抛出一个`ExplodingBoxException`:

```
namespace Acme\Foo;

class Box
{
    public function somethingBad()
    {
      throw new Box\ExplodingBoxException;
    }
} 
```

异常类声明必须存在于某个地方。我们可以把它放在类的顶部，但是我们在一个文件中有两个类……嗯，对很多人来说这感觉有点滑稽。它还违反了《PSR 协议一》,其中指出:

> 这意味着每个类都单独在一个文件中，并且位于至少一个级别的名称空间中:顶级供应商名称。

所以，它放在自己的文件里:

```
namespace Acme\Foo\Box;

class ExplodingBoxException {} 
```

要加载这个异常，我们必须点击自动加载程序并再次进入文件系统。这样做不是免费的！如果启用了操作码缓存，PHP 5.6 会降低后续请求的开销，但这仍然是额外的工作。

在 Ruby 中，可以将一个类嵌套在另一个类中:

```
module Acme
  module Foo
    class Box
      class ExplodingBoxError < StandardError; end

      def something_bad!
        raise ExplodingBoxError
      end
    end
  end
end 
```

这可用于定义类，也可用于类外:

```
begin
  box = Acme::Foo::Box.new
  box.something_bad!
rescue Acme::Foo::Box::ExplodingBoxError
  # ...
end 
```

这可能看起来有点奇怪，但它很酷。一个类只和一个类相关。分组！

另一个例子是数据库迁移。

从 CodeIgniter 到 Laravel，许多流行的 PHP 框架都提供了移植功能。任何经常使用它们的人都会知道，如果您在迁移中引用一个模型或任何其他类，那么稍后您更改该类，您的旧迁移将以奇怪而奇妙的方式中断。

Ruby 用嵌套类很好地解决了这个问题:

```
class PopulateEmployerWithUserAccountName < ActiveRecord::Migration
  class User < ActiveRecord::Base
    belongs_to :account
  end

  class Account < ActiveRecord::Base
    has_many :users
  end

  def up
    Account.find_each do |account|
      account.users.update_all(employer: account.name)
    end
  end

  def down
    # Update all users whose have account id to previous state
    # no employer set
    User.where.not(account_id: nil).update_all(employer: nil)
  end
end 
```

将使用嵌套版本的`User`和`Account` ORM 模型来代替全局声明的类，这意味着它们更像是我们所需要的快照。这比用移动的标杆调用代码有用得多，因为标杆随时都可能改变。

再说一次，这对于一些人来说听起来很疯狂，直到你被咬了几次。

PHP 开发人员通常会复制复杂的逻辑来完成这项工作，或者手动编写 SQL，与仅仅复制模型的相关部分相比，这是一种时间和精力的浪费。

### 调试器

XDebug 是一件很棒的作品。不要误解我的意思，使用断点是令人惊讶的，它彻底改变了 PHP 开发人员调试应用程序的方式，超越了初级开发人员中非常普遍的“`var_dump()` + refresh”调试工作流。

也就是说，让 XDebug 与您选择的 IDE 一起工作，如果缺少正确的插件，找到合适的 PHP 版本的`php.ini`,为您的 CLI 和 web 版本启用`zend_extension=xdebug.so`,即使您使用的是 vagger，也要发送断点，等等。，可能是一个巨大的痛苦。

Ruby 有一点不同的方法。就像在浏览器中调试 JavaScript 一样，您只需在代码中敲入`debugger`关键字，就可以得到一个断点。在你的代码执行那一行的时候——无论是`$ rails server`、单元测试、集成测试等等。，您将拥有一个 [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) 的实例来与您的代码进行交互。

周围有一些调试器。一个流行的调试器叫做`pry`，但是另一个叫做`byebug`。它们都是宝石，可以通过 Bundler 添加到你的`Gemfile`中进行安装:

```
group :development, :test do
  gem "byebug"
end 
```

这相当于一个 dev Composer 依赖项，一旦安装，如果你使用 Rails，你可以调用`debugger`。如果没有，你需要先`require "byebug"`。

一个方便的 Rails 指南[调试 Rails 应用程序](http://guides.rubyonrails.org/debugging_rails_applications.html#debugging-with-the-byebug-gem)，展示了如果你在应用程序中加入关键字:

```
[1, 10] in /PathTo/project/app/controllers/articles_controller.rb
    3:
    4:   # GET /articles
    5:   # GET /articles.json
    6:   def index
    7:     byebug
=>  8:     @articles = Article.find_recent
    9:
   10:     respond_to do |format|
   11:       format.html # index.html.erb
   12:       format.json { render json: @articles }

(byebug) 
```

箭头显示了 REPL 实例正在运行的行，您可以从那里执行代码。此时，`@articles`还没有定义，但是可以调用`Article.find_recent`看看是怎么回事。如果出错，您可以键入`next`进入同一上下文中的下一行，或者键入`step`进入下一条要执行的指令。

方便的东西，特别是当你试图找出为什么一些代码没有给你你期望的输出。检查每一个值，直到找到问题所在，试着让它在那个上下文中工作，然后任何最终成为工作代码的代码都可以被复制并粘贴到文件中。

在你的测试中做到这一点是惊人的。

### 除非

很多人不喜欢`unless`。它经常被滥用，就像许多编程语言的许多特性一样，正如 2008 年的[这篇文章](http://www.railstips.org/blog/archives/2008/12/01/unless-the-abused-ruby-conditional/)指出的那样，`unless`多年来一直困扰着人们。

`unless`控制结构与`if`相反。当条件评估为`true`时，它不会执行代码块，只会在条件评估为`false`时执行。

```
unless foo?
  # bar that thing
end

# Or

def something
  return false unless foo?
  # bar that thing
end 
```

这使得事情变得更容易解决，特别是当有多个条件时，可能有一个`||`和一些括号。而不是像这样砰的一声切换:`if ! (foo || bar) && baz`你可以简单地做`unless (foo || bar) && baz`。

现在，这可能有点多，工作中没有人会让你提交一个带有`else`的`unless`，但是一个除非本身就是一个方便的特性。

当人们在 2007 年为 PHP 请求这个功能时，它被忽略了一段时间，直到 PHP 的创造者拉斯马斯·勒德尔夫说，这对于任何拥有`unless()`功能的人来说都是一个突破，而且“对于像我这样的非英语母语的人来说并不明显”

> 这是一个奇怪的词，本质上意味着不是——即使它在逻辑上应该等同于“更多”,因为“更多”的反义词是“更少”,如果在它前面加上“联合国”会突然完全改变它的意思。

我不同意这一点，现在仍然如此。阅读`unless`的人不会仅仅基于`un`就认为它是“少的对立面”。如果是这样的话，人们会读到函数`uniqid()`，并认为它与`iqid()`相反。

我们提出了同样的建议，却被告知我们“只是在犯傻”。它从此被贴上了`wontfix`的标签。

### 谓词方法

在 Ruby 的世界中有一些很酷的约定，PHP 被迫用其他方式来解决。其中之一是谓词方法，它是具有布尔响应类型的方法。鉴于 Ruby 没有返回类型提示，这对于使用该方法的开发人员来说是一个很好的建议。

Ruby 有很多内置，比如`object.nil?`。这基本上就是 PHP 里的`$object === nil`。一个`include?`而不是`include`也更清楚，因为它是在问问题，而不是执行动作。

定义你自己的很酷:

```
class Rider
  def driver?
    !potential_driver.nil? && vehicle.present?
  end
end 
```

许多 PHP 开发人员会通过在方法名前面加上`is`和/或`has`来做到这一点，所以你可以用`isDriver()`或者`hasVehicle()`来代替，但是有时你必须使用其他前缀。我用 Ruby 写的一个名为`can_drive?`的方法在 PHP 中将会是`canDrive()`，这还不清楚它是不是一个谓词方法。我不得不把它重新命名为`isAbleToDrive()`或者其他一些废话，以使它与 PHP 世界中的`is` / `has`传统相一致。

### 甚至更短的数组语法

在 PHP 中，定义文字数组很容易，在 PHP 5.4 中，定义文字数组变得不那么冗长，增加了短数组语法:

```
// < 5.4
$a = array('one' => 1, 'two' => 2, 'three' => 'three');
// >= 5.4
$a = ['one' => 1, 'two' => 2, 'three' => 'three']; 
```

有些人会说[还是有点太冗长了。在 Ruby 1.9 中，他们增加了一个新的选项，允许用分号替换火箭，如果用 PHP 实现，会进一步减少语法:](http://phpsadness.com/sad/45)

```
$a = ['one': 1, 'two': 2, 'three': 'three']; 
```

如果你问我，我会觉得这很好，当你使用嵌套数组的时候，当你一天要做几百次的时候，真的可以节省一点输入。

肖恩·科茨在 2011 年向 RFC 提出了这一建议，但从未成功。

这可能永远不会成为 PHP。PHP 试图在语法上做到极简，并且经常反对添加新的方法来做旧的事情，即使新的方法更好一些。基本上，语法糖并不是 PHP 维护者的首要任务，然而它似乎是 Ruby 的核心目标。

### 对象文字

上面链接的同一个 RFC 突出了 Ruby 中的一个特性，我很想看到它被放入 PHP: object literals。在 PHP 中，如果你想用值定义一个`StdClass`，你有两种方法:

```
$esQuery = new stdClass;
$esQuery->query = new stdClass;
$esQuery->query->term = new stdClass;
$esQuery->query->term->name = 'beer';
$esQuery->size = 1;

// OR

$esQuery = (object) array(
   "query" => (object) array(
       "term" => (object) array(
           "name" => "beer"
       )
   ),
   "size" => 1
); 
```

我知道这是 PHP 一直以来的做法，但它可以简单得多。

RFC 中提出的语法几乎与 Ruby 完全匹配:

**PHP**

```
$esQuery = {
   "query" : {
       "term" : {
           "name" : "beer"
       }
   },
   "size" : 1
}; 
```

**红宝石**

```
esQuery = {
   "query" : {
       "term" : {
           "name" : "beer"
       }
   },
   "size" : 1
} 
```

我真的真的很希望这样做，但同样，这在过去已经尝试过了，但很少有人感兴趣。

### 拯救方法

Ruby 没有 PHP 中的`try` / `catch`，而是有`begin` / `rescue`。它们的工作方式基本相同，PHP 5.6 甚至获得了`finally`，以匹配 Ruby 的`ensure`。

PHP 和 Ruby 都可以在任何地方从异常中恢复，遵循它们各自的`try`或`begin`关键字，但是 Ruby 可以做一些非常聪明的事情:你可以跳过`begin`方法，直接从函数/方法体中恢复:

**红宝石**

```
def create(params)
  do_something_complicated(params)
  true
rescue SomeException
  false
end 
```

如果事情出错，错误可以以某种方式处理，而不是冒泡并强迫被调用者处理它。这并不总是您想要的，但是有了这个选项就很方便了，并且不需要缩进整个东西来包装在一个`begin`中。

在 PHP 中这不起作用，但是如果实现的话，这个特性可能看起来有点像这样:

```
function create($params) {
  do_something_complicated($params);
  return true;
} catch (SomeException $e) {
  return false;
} 
```

虽然这可能不是非常重要，但是有很多像这样的小细节让 Ruby 觉得它想帮助你。

### 异常重试

几个月前，我发现了一个以前从未注意到的非常方便的功能，即`retry`关键字:

```
begin
  SomeModel.find_or_create_by(user: user)
rescue ActiveRecord::RecordNotUnique
  retry
end 
```

在这个例子中，竞争条件突然出现，因为`find_or_create_by`不是原子的(ORM 先执行`SELECT`，然后执行`INSERT`),如果您真的不走运，这可能导致另一个进程在`SELECT`之后、`INSERT`之前创建记录。

因为这种情况只会发生一次，你可以简单地指示`begin...rescue`再试一次，它就会被`SELECT`找到。在其他的例子中，你可能想在适当的地方放置一些逻辑，只重试一次或两次，但是让我们暂时忽略它。

把注意力放在拿一段代码再试一次会有多烦人。在 Ruby 中，您可以这样做:

```
def upload_image
  begin
    obj = s3.bucket('bucket-name').object('key')
    obj.upload_file('/path/to/source/file')
  rescue AWS::S3::UploadException
    retry
  end
end 
```

PHP 将要求您为 begin 块的内容创建一个新的函数/方法:

```
function upload_image($path) {
  $attempt = function() use ($path) {
    $obj = $this->s3->bucket('bucket-name')->object('key');
    $obj->upload_file($path);
  };

  try {
    $attempt();
  } catch (AWS\S3\UploadException $e)
    $attempt();
  }
} 
```

同样，是的，您可能想在两者中放一些样板文件来阻止无限循环，但是 Ruby 的例子在重试代码方面要干净得多。

一只小鸟告诉我，这项功能正在作为 RFC 积极开发，有望很快公布。如果进展顺利，理论上它可以成为 PHP 7.1 的一个特性。

## 最后的想法

在过去涉足 Ruby 的时候，我主要是像 PHP 一样写 Ruby。在过去的一年里，与一个令人惊叹的、经验极其丰富的 Ruby 开发人员团队一起工作，教会了我许多与众不同的 Ruby 主义和实践，我并不讨厌它。

这篇文章指出了如果我重新开始使用 PHP，我会想念 Ruby 的一些东西，但这不会阻止我继续使用它。讨厌 PHP 的人经常忽略的是 PHP 核心贡献者取得的进步，虽然他们可能没有我喜欢的 Ruby 的 X 或 Y 特性，但他们为 PHP 7 做了一些令人惊讶的事情，PHP 7.1 看起来充满了有趣的惊喜。

PHP 在一致性方面做了很多努力，有[统一变量语法](https://wiki.php.net/rfc/uniform_variable_syntax)、[上下文敏感词法分析器](https://wiki.php.net/rfc/context_sensitive_lexer)和[抽象语法树](https://wiki.php.net/rfc/abstract_syntax_tree)。所有这些使得 PHP 作为一个整体更加一致，不管标准库中的不一致。

虽然标准库不会很快得到修复，但是如果 PHP 能够像上面那样添加一些方便的特性和语法糖，那就太好了。

其他语言可以向各个方向发展，致力于新的理论、实验，做出让黑客新闻类型高兴的酷东西，然后最终抓住对 PHP 有意义的东西。我喜欢这种方法。毕竟 [PHP 是个掠夺的海盗](http://blog.astrumfutura.com/2012/04/php-innocent-villagefolk-or-a-pillagin-pirate/)。

只要你有时间和兴趣，就可以尝试多种语言。Ruby 是一个好的开始，Golang 很有趣，而 Elixir 很古怪，但也是一个很好的挑战，但不要觉得有必要从一个跳到另一个。你可以写几个，它让你的大脑保持良好和活跃。

## 分享这篇文章