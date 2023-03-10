# 带有#to_proc 符号的 Insta-block

> 原文：<https://www.sitepoint.com/insta-block-with-symbolto-proc/>

在 Ruby 阅读中可能会忽略的东西是`Symbol#to_proc`，它从 0.14.4 开始就在 Rails 中了。

使用 Ruby tricky 的这个小妙招，您可以简化代码，例如:

```
 articles.collect { |a| a.title }
articles.sort { |a| a.created_at } 
```

到

```
 articles.collect(&:title)
articles.sort(&:created_at) 
```

…如果您想知道为什么`&:ruby_is_the_new_black`在您刚刚编写的漂亮的 ruby shell 脚本中不工作，那是因为`Symbol#to_proc`不是标准的 Ruby thang。引用 Mauricio Fernandez 的话，他[提到它最近包含在即将发布的 Ruby 1.9](http://eigenclass.org/hiki.rb?Changes+in+Ruby+1.9+update+4) 中:

> 符号#to_proc:首先被某个匿名的日本 ruby 家发现(相当确定)，然后被 Florian Gro 重新发现(当我在#ruby-lang 上浪费时间时，我看到了这种情况)，然后被 Pragdave 和 Rails 普及，最后被正式采用。这是一个漫长的旅程。

“那很好，”你说，“但是我还是不太明白这些触发和阻挡到底是怎么回事。”好吧，让我给你讲一些红宝石积木的喃喃自语。

我认为，如果你来自 web-design-land，描述一个块的最好方式是把它想象成 Javascript 中的一个匿名函数。

例如，下面这块红宝石:

```
 socceroos.each do |s|
  s.congratulate
end 
```

相当于下面的 Javascript:

```
 socceroos.each(function(s) {
  s.congratulate();
}) 
```

传递给函数的块由一个&符号表示:

```
 class Team
  def each(&block)
    for player in @players
      block.call(player)
    end
  end
end 
```

事实上，上述调用块的模式非常常见，Ruby 更进了一步，允许您简单地编写:

```
 class MyTeam
  def each
    for member in @members
      yield member
    end
  end
end 
```

`yield member`另一种说法是“调用相关的块并传递给它成员”。你甚至不需要在参数表中添加`&block`。

在处理 ActiveRecord 时，您会看到这种模式。`ActiveRecord::Base`类允许您在创建新实例时指定一个块。例如，如果`Player`是一个 ActiveRecord 子类，您可以执行以下操作:

```
 @player = Player.new do |p|
  p.name = "Ronaldo"
  p.nickname = "Porky"
end 
```

ActiveRecord 是如何提供这种简洁的 API 的？轻松点。它只是检查你是否指定了一个块，如果是，调用这个块，传递新的 Player 对象给它。

实现这一目标的最低逻辑是:

```
 class ActiveRecord::Base
  def initialize
    yield self if block_given?
  end
end 
```

## 分享这篇文章