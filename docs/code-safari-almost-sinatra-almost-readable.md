# 代码旅行:几乎 Sinatra，几乎可读

> 原文：<https://www.sitepoint.com/code-safari-almost-sinatra-almost-readable/>

Sinatra 是一个用 Ruby 创建 web 应用程序的最小化的 web 框架。[几乎辛纳特拉](https://github.com/rkh/almost-sinatra)是同样的东西，但在*中只有八行代码*。肯定有什么诡计正在酝酿中？让我们看看这些线。

```
# almost-sinatra/almost_sinatra.rb
%w.rack tilt backports INT TERM..map{|l|trap(l){$r.stop}rescue require l}
$n=Sinatra=Module.new{extend Rack;a,D,S,$p,q,Application=Builder.new,Object.method(:define_method),/@@ *([^n]+)n(((?!@@)[^n]*n)*)/m,4567,a
%w[get post put delete].map{|m|D.(m){|u,&amp;b|a.map(u){run->(e){[200,{"Content-Type"=>"text/html"},[a.instance_eval(&amp;b)]]}}}}
Tilt.mappings.map{|k,v|D.(k){|n,*o|$t||=(h={};File.read(caller[0][/^[^:]+/]).scan(S){|a,b|h[a]=b};h);v.new(*o){n.to_s==n ?n:$t[n.to_s]}.render(a,o[0].try(:[],:locals)||{})}}
%w[set enable disable configure helpers use register].map{|m|D.(m){|*_,&amp;b|b.try :[]}};END{Handler.get("webrick").run(a,Port:$p){|s|$r=s}}
%w[params session].map{|m|D.(m){q.send m}};a.use Session::Cookie;a.use Lock
D.(:before){|&amp;b|a.use Rack::Config,&amp;b};before{|e|q=Request.new e;q.params.dup.map{|k,v|params[k.to_sym]=v}}}
puts "== almost #$n/No Version has taken the stage on #$p for development with backup from Webrick"
```

这就是全部了。肯定是密集的！这里有一些宝石，所以让我们开始一行一行地把它拆开。阅读混乱的代码是深入了解一门语言的语法的一个很好的方式，到本文结束时，你将准备好搬进去，把脏盘子放在沙发上。

## 一号线

我将第一行重新格式化，使其更易于阅读。这总是我处理混乱代码的第一步。通常将变量重命名为有意义的名称也会有所帮助，尽管我在本文中没有这样做。

```
%w.rack tilt backports INT TERM..map{|l|
  trap(l) {
    $r.stop
  } rescue require l
}
```

最初语法是什么？它看起来像是一个列表，因为后面有一个`#map`调用，但是我从来没有见过这样的列表。这种类型的代码很难找到信息，因为谷歌搜索标点符号很少有用。鉴于它看起来像一个数组，我打了个平手，搜索了“ruby array syntax ”,发现了 [ruby 编程 wikibook](http://en.wikibooks.org/wiki/Ruby_Programming/Syntax/Literals#Arrays) 中的数组部分:

> 当子字符串仅由空格分隔时,%w 实际上是 String 方法 split 的简写。

给出的例子使用`'`作为分隔符，使用`irb`我们可以确认在`%w`后面可以使用任何标点符号。选择使用哪一种纯粹是风格问题。

```
%w'rack tilt' # => ["rack", "tilt"]
%w.rack tilt. # => ["rack", "tilt"]
%w(rack tilt) # => ["rack", "tilt"]
%w!rack tilt! # => ["rack", "tilt"]
```

现在把我们的注意力转向列表的内容——它似乎包含了需要*和*信号进行救援的两个文件。由于`rack`、`tilt`和`backports`都是无效的信号名，因此`#trap`调用将引发异常。通常在 ruby 中，异常被显式地拯救:

```
begin
  trap("bogus signal name") {}
rescue ArgumentError => e
  puts "Invalid signal name"
end
```

Ruby 也允许尾随形式，就像你可以对`if`语句做的一样。

```
puts "Small enough" if line_count <= 10
trap("bogus") {} rescue puts ":("
```

这将拯救所有的异常，但这不是那种你想在你的代码库中随意传播的牛仔行为。更不用说使用异常进行流量控制了，但是当线路非常昂贵时，一切都是公平的。

## 二号线

随着 requires 和其他序言的结束，让我们深入框架本身。

```
$n = Sinatra = Module.new {
  extend Rack;
  a,D,S,$p,q,Application = 
    Builder.new,
    Object.method(:define_method),
    /@@ *([^n]+)n(((?!@@)[^n]*n)*)/m,
    4567,
    a
```

这里有一些有趣的技巧。首先是动态定义一个`Module`。在 ruby 中，一切都是对象，甚至是模块和类，所以在你通常使用`module`关键字来定义模块的地方，你可以通过调用`Module.new`来动态地这样做。唯一的区别是关键字形式将有一个明确的名称和常量定义，而后者将是匿名的。

```
module Test
end

Test.name       # => 'Test'
Module.new.name # => nil
```

以`$`为前缀的变量名并不常见，所以你可能不熟悉。搜索“ruby variable types”找到了[一篇文章](http://www.techotopia.com/index.php/Ruby_Variable_Scope)，其中有一个方便的表格总结了不同的前缀，在这种情况下，我们发现以`$`开头的变量是一个全局变量。这就是你不常见到他们的原因…

这里使用了两种形式的变量赋值。第一行中使用的方法依赖于这样一个事实，即 ruby 中的所有内容都隐式返回值。你可以在`irb`中看到这一点——执行的每条语句后面都有一个值，即使它通常为零。

```
# puts returns the value 'nil'
irb> puts "hello"
hello
 => nil
```

赋值返回被赋值的值，这意味着可以将它们链接在一起。在上面的代码片段中，这意味着`$n`和`Sinatra`将被分配相同的`Module`。

```
irb> $n = Sinatara = Module.new {}
irb> $n == Sinatra 
 => true
```

正在使用的另一个赋值技巧叫做多重或并行赋值，它允许你在一行中给不同的变量赋不同的值。这种用法的有趣之处在于，左手边的一个变量`a`在右手边被重用，以分配给应用程序常量。有一段时间我对此非常困惑，但在`irb`中玩了一圈后确认这实际上是代码中的一个 bug！

```
a,Application=1,a # => [1, nil]
a=1;Application=a
```

## 三号线

这一行为不同的 HTTP 动词设置了方法，为了理解它，我们首先需要熟悉`Rack::Builder` API，因为这个类的一个实例被分配给了`D`变量。这有点跑题了，所以我将把它留到以后的文章中，而是把重点放在这个片段使用的两个有趣的语法特性上，这两个特性都与 lambdas 相关，并且是在 ruby 1.9 中引入的。

```
%w[get post put delete].map{|m|
  D.(m){|u,&amp;b|
    a.map(u){
      run->(e){
        [200,{"Content-Type"=>"text/html"},[a.instance_eval(&amp;b)]]
      }
    }
  }
}
```

首先是一种新的调用 lambas 和 procs 的方式，`.()`，是`#call`的别名。这使得在 ruby 中调用 lambda 的方法总数达到了三种:

```
add_two = lambda {|x| x + 2 }
add_two.call(1) # => 3
add_two.(1)     # => 3
add_two[1]      # => 3
```

有一些微妙的区别:`.()`*是否总是*委托给`#call`而`[]`可以分别被覆盖，但是在大多数情况下它们是等价的。

另一个有趣的语法是`run->(e){}`。这被亲切地称为“stabby”语法，是一种定义 lambdas 的新方法，引入它是为了允许指定默认参数值。对于 ruby 解析器来说，使用旧的`run(lambda {|e| })`语法是不可能解决这个问题的(如果你感兴趣的话，[更多细节](http://eigenclass.org/hiki.rb?Changes+in+Ruby+1.9#l9))。

```
add_two       = lambda {|x| x + 2}
add_two       = ->(x) { x + 2 }       # The same!
double_or_add = ->(a,b=a|2) { a + b } # Just try that with the old syntax...
```

旧的语法将永远存在，但是新的方法给了你一个不那么冗长、更灵活的选择。

## 等等

告诉过你它很密集！一整篇文章，我们只有三行。另一方面，它是通过一个框架的一半！我们在很短的时间内讨论了很多有趣的语法:

*   `%w`数组语法
*   尾随`rescue`语法
*   使用`Module.new`的匿名模块
*   链接赋值(`a = b = 1`)
*   多重赋值(`a,b = 1,2`)
*   `.()`调用代码块的语法
*   用于编写 lambdas 的“stabby”语法

其余的线大多是相同的，但有几个曲线球。作为练习，我建议你继续练习。其他需要改进的项目:

*   [露营](https://github.com/camping/camping)，4k 口袋满满的 gags web 微框架。这是一个已经存在多年的老派框架。它包括一个未删节的版本，因此是对废弃代码的相对温和的介绍。
*   它很老了，但是 2005 年国际混淆 Ruby 代码竞赛的参赛作品，在这篇博客文章中得到很好的反映。

请在评论中告诉我们你的进展。下周请继续关注代码丛林中更激动人心的冒险。

## 分享这篇文章