# 品味的语法:重构条件句

> 原文：<https://www.sitepoint.com/syntax-taste-refactoring-conditionals/>

![tune](img/e592f4e0892d2314453b5f6393a4c477.png)

条件句本质上并不坏，但它们确实有令人烦恼的激增趋势。就像真菌一样，它们在黑暗潮湿的地方茁壮成长，比如说代码库。

在这里，我们将看到一个典型的、自制的条件，并了解如何获得少量的控制。

## 迎合相互矛盾的偏好

我有一个虚构的应用程序。它在命令行上运行，播放音乐，使用起来非常简单，甚至我的非程序员朋友也在使用它:

```
$ fm
```

就是这样。

默认情况下，它会播放一个精选的音乐列表，但这并不总是合适的，甚至是不可取的。为了考虑到各种不同的社交场合和偶尔的怀旧，`fm`支持一个附加语法:

```
$ fm <GENRE> # e.g. acid-jazz
```

最后，还有“哦，我不知道，给我个惊喜”模式:

```
$ fm mixtape
```

这是一种最令人愉快的混合，它可以从任何地方获得，无论是本地的还是偏远的。

对`fm`最大的抱怨是`mixtape`命令包含了人们不喜欢的流派。正如你所料，没有两个人会抱怨相同的类型。

抱怨通常有两种类型。有些人对极端的多样性非常满意，他们只是想过滤掉一些非常特殊的类型，这些类型会让他们感到不适。其他人对他们的音乐更挑剔，只希望在一个非常狭窄的舒适和熟悉的流派中获得惊喜。

> 哦，我们这里有两种音乐，乡村音乐和西部音乐。布鲁斯兄弟

## 扩展 API

为了允许不同的偏好，管理播放列表的`Stream`对象需要知道是否启用了一个流派:

```
class Stream
  def genre_enabled?
    # magic happens here
  end
end
```

`fm`代码库将配置隔离到单个`Config`对象中，流可以访问该对象:

```
Stream.new(Config.new)
```

这是你需要的样板文件，以便在家里遵循。

```
# lib/fm/config.rb
module FM
  class Config
    # ...
  end
end

# lib/fm/stream.rb
module FM
  class Stream
    attr_reader :config
    def initialize(config)
      @config = config
    end

    # ...
  end
end

# test/fm/stream_test.rb
gem 'minitest', '~> 5.4'
require 'minitest/autorun'
require './lib/fm/stream'
require './lib/fm/config'

class StreamTest < Minitest::Test
  # ...
end
```

可以使用以下命令运行测试:

```
ruby test/fm/stream_test.rb
```

## 确保向后兼容性

对于那些根本不想配置`mixtape`功能的人来说，这款应用仍然可以工作。在这种情况下，`mixtape`命令应该播放任何内容。无论流中出现什么类型，都应该启用它:

```
def test_all_genres_enabled_by_default
  stream = FM::Stream.new(FM::Config.new)

  assert stream.genre_enabled?(:abc), "expected abc to be enabled"
  assert stream.genre_enabled?(:xyz), "expected xyz to be enabled"
end
```

少量代码就能通过测试:

```
# lib/fm/stream.rb
def genre_enabled?(genre)
  true
end
```

在这一点上，如果不考虑人们将如何限制流派，就无法前进。

## 选择用户界面

对于白名单类型，一个相当惯用的用户界面应该是这样的:

```
$ fm configure --only jazz,blues,power-metal,trance
```

对于不太挑剔的听众来说，黑名单功能看起来像这样:

```
$ fm configure --except house,opera
```

不幸的是，这种类型的 UI 会以惊人的方式失败。强迫非程序员处理命令行选项是卑鄙的。

一个更好的 UI 应该是根本没有 UI。

由于这不是一个将被广泛分发的应用程序，我可以简单地花几分钟为少数人设置好一切。

让 *no* UI 工作所需的编程量可以忽略不计。没有必要进行参数、写入文件、加载 JSON 或 YAML，或者交互式提示。

环境变量很容易处理，非程序员编辑文本文件没有问题:

```
ENV['FM_GENRE_WHITELIST'] = 'abc,def,ghi,jkl'
```

## 启用和停用类型

环境变量是全局的，这意味着测试有可能相互干扰。他们需要小心翼翼地清理自己:

```
def test_enabled_via_whitelist
  ENV['FM_GENRE_WHITELIST'] = 'abc, def,ghi , jkl'
  # ...
ensure
  ENV.delete('FM_GENRE_WHITELIST')
end
```

流派列表中不一致的空白是考虑到很少有非程序员注意到“苹果、香蕉、樱桃”和“苹果、香蕉、樱桃”之间的区别。

我们需要一个流对象来完成所有的工作:

```
stream = FM::Stream.new(FM::Config.new)
```

最后，我们需要两种类型的断言:

1.  列表中定义的所有内容都已打开。
2.  列表上没有的内容被关闭。

    ```
    %i(abc def ghi jkl).each do |genre|
      assert stream.genre_enabled?(genre), "expected '#{genre}' to be enabled"
    end

    refute stream.genre_enabled?(:xyz), "expected xyz to be disabled"
    ```

通过测试并不需要太多的修改:

```
# lib/fm/config.rb
def included_genres
  @included_genres ||= ENV['FM_GENRE_WHITELIST'].to_s.split(/\s*,\s*/).map { |s| s.strip.to_sym }
end

# lib/fm/stream.rb
def genre_enabled?(genre)
  return true if config.included_genres.empty?

  config.included_genres.include?(genre)
end
```

黑名单几乎是同样的东西，只是断言相反:

```
def test_disabled_via_blacklist
  ENV['FM_GENRE_BLACKLIST'] = 'abc, def,ghi , jkl'
  stream = FM::Stream.new(FM::Config.new)

  %i(abc def ghi jkl).each do |genre|
    refute stream.genre_enabled?(genre), "expected '#{genre}' to be disabled"
  end

  assert stream.genre_enabled?(:xyz), "expected xyz to be enabled"
ensure
  ENV.delete('FM_GENRE_BLACKLIST')
end
```

配置非常简单:

```
# lib/fm/config.rb
def included_genres
  @included_genres ||= tokenize(ENV['FM_GENRE_WHITELIST'])
end

def excluded_genres
  @excluded_genres ||= tokenize(ENV['FM_GENRE_BLACKLIST'])
end

private

def tokenize(s)
  s.to_s.split(/\s*,\s*/).map { |s| s.strip.to_sym }
end
```

然而，`Stream`对象突然变得更加复杂了:

```
# lib/fm/stream.rb
def genre_enabled?(genre)
  if config.excluded_genres.empty? && config.included_genres.empty?
    return true
  end
  if !config.included_genres.empty?
    return config.included_genres.include?(genre)
  end
  !config.excluded_genres.include?(genre)
end
```

如果你有轻微的布尔障碍，这些代码不会马上显现出来。

## 消除不必要的复杂性

现在我们正在处理三种情况，但是*未配置*的情况可以被视为黑名单的一种特殊情况，其中没有任何东西被列入黑名单。

换句话说，如果白名单中有一些东西，那么我们就处于白名单模式，否则我们可以安全地假设我们正在处理黑名单，它可能是也可能不是空的。

这大大简化了事情:

```
def genre_enabled?(genre)
  if !config.included_genres.empty?
    return config.included_genres.include?(genre)
  end
  !config.excluded_genres.include?(genre)
end
```

这并不可怕。

好吧，这的确很糟糕，但至少不是很长。我认为没有人会抱怨在他们的产品代码库中找到这样的代码。

注意`config` : `included_genres`和`excluded_genres`中方法名的重复后缀。似乎我们在处理一种类型的事情，`genres`，它有两种风格:`included`和`excluded`。

或者，如果你愿意，*白名单*和*黑名单*。

如果你一直知道你在和`genres`对话，但是不知道或者不关心它们是如何实现的，那该怎么办？

```
genres.enabled?(:opera)
```

这可以是包装条件的单个对象:

```
class Genres
  attr_reader :values
  def initialize(list)
    @values = list.to_s.split(/\s*,\s*/).map { |s| s.strip.to_sym }
  end

  def enabled?(genre)
    if !config.included_genres.empty?
      return config.included_genres.include?(genre)
    end
    !config.excluded_genres.include?(genre)
  end
end
```

或者您可能有两个对象，一个用于白名单，一个用于黑名单:

```
class BlacklistedGenres
  # ...
  def enabled?(genre)
    !values.include?(genre.to_sym)
  end
end

class WhitelistedGenres
  # ...
  def enabled?(genre)
    values.include?(genre.to_sym)
  end
end
```

突然间，没有必要知道这种类型的味道。这可能是我祖母的配置，充满了恐惧、灵魂和摇滚:

```
genres = WhitelistedGenres.new("funk,soul,rock")
```

或者可能是住在隔壁的 12 岁鼓手的声音，他会听任何东西，只要它包含某种节拍:

```
genres = BlacklistedGenres.new("gregorian-chants")
```

即使有两个很小的对象，条件句也没有完全消失。它仍然在那里，在`Config`中，但是现在的问题不是*如何配置它，给定这个特定的状态，我如何查询正确的值？*，问题是*什么对象知道如何解决这个问题？*。

现在，迎合人们音乐品味的所有复杂性都可以归结为创造合适的对象:

```
# lib/fm/config.rb
def genres
  @genres ||= if !!ENV['FM_GENRE_WHITELIST']
    WhitelistedGenres.new(ENV['FM_GENRE_WHITELIST'])
  else
    BlacklistedGenres.new(ENV['FM_GENRE_BLACKLIST'])
  end
end
```

一旦你抓住了一个`genres`实例，它做什么或者如何做就真的无关紧要了。它只需要回答一个简单的问题:

```
genres.enabled?(:jazz)
```

## 识别潜在的重构

在马丁·福勒的书《重构》中，有许多重构都是关于将条件转化为对象的。它们有着花哨的名字，看起来好像都是非常高级的 refactor-fu。

不是的。

重新排列代码通常很简单。棘手的部分是知道从哪里开始，并认识到你能做到。

克服这个障碍的一部分就是简单地做几次。找到一个条件，并为它的每个块创建一个对象，该对象的唯一职责就是完成该任务。

这永远是正确的选择吗？绝对不行。但是试一试。在大 app 和小 app 里试试，大条件句和小条件句。感受一下它能为你做什么，以及哪里有问题。

如果这能让你感觉更好，那就在你完成的时候把代码扔掉。重点不是把这个特殊的条件转换成对象，而是知道如何把一些条件转换成对象。

知道*如果*你应该重构你的`if`来自实践。

## 分享这篇文章