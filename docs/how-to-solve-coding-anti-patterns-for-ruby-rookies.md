# 如何解决 Ruby 新手的编码反模式

> 原文：<https://www.sitepoint.com/how-to-solve-coding-anti-patterns-for-ruby-rookies/>

![Ruby Rookies](img/8176a0d11ab9710eaddee7813b5d81c2.png)

作为一名自由开发人员，我经常与来自不同编程背景和不同技能水平的开发人员一起工作。随着时间的推移，我学会了通过查看程序员写的代码来判断他们的 Ruby 经验水平。不仅如此，我还可以根据代码中的某些模式，对程序员的编码背景下一个安全的赌注，这些模式给出了关于程序员以前的编程经验的微妙线索。Ruby 新手往往会经常采用某些编码实践。让我们称这些为反模式。这篇文章列举了这些反模式中最常见的。

### 连接，而不是插入

Ruby 是少数几种不使用任何特殊方法就可以直接对字符串进行插值的语言之一。不幸的是，许多 Ruby 新手更喜欢连接字符串:

```
puts “error description” + e.cause.to_s + “happened on: ” + Time.now.to_s 
```

相反，他们可以写:

```
puts “error description #{e.cause} happened on: #{Time.now}” 
```

这不仅看起来更容易，而且在插值对象上自动调用了`#to_s`方法。串联时未能显式调用`#to_s`将导致`TypeError`。因此，插值会产生更短、更漂亮、更安全的代码。

还有，如果你想向你串联的朋友炫耀，为什么不用 Ruby 的格式化插值呢？

```
pets = %w{dog cat rabbit}
puts "My first pet is a %s, my second one a %s and my third is a %s" % pets
=> My first pet is a dog, my second one a cat and my third is a rabbit 
```

### 实话实说

许多从其他语言进入 Ruby 的人没有意识到 Ruby 的真实模型是多么简单。因此，他们编写这样的代码:

```
if an_object != nil && an_object.method? == true
  #do something
end 
```

在 Ruby 中，只有值`false`和`nil`被评估为 false(实际上它们都是对象)。其他一切都为真。这样，我们的条件变得简单多了:

```
if an_object && an_object.method?
  #do something
end 
```

当我们谈到布尔逻辑时，这里有另一个我经常遇到的反模式。

```
if !an_object.active?
  #do something
end 
```

负面断言本身并没有错，除了一点:可读性。这个感叹号太容易被忽略了，让读者对代码的作用产生了相反的想法。幸运的是，Ruby 以`unless`语句的形式提供了一些不错的语法糖:

```
unless an_object.active?
  #do something
end 
```

这是一个很好的，强调的陈述，很难错过。

### AndNotOr

许多 Ruby 新手对类似英语的逻辑操作符欣喜若狂，以至于他们一直在使用它们，取代符号操作符。然而，他们并不总是意识到`[!, &&, || ]`和`[not, and, or]`不一样。区别在于优先顺序。

```
puts "hello" && "world"
 => world 
```

在这种情况下，`&&`操作符在`puts`之前计算，有效地将表达式简化为`puts "world"`

如果我们使用`and`操作符，表达式的计算结果是`(puts "hello") and ("world")`

```
puts "hello" and "world"
 => hello 
```

观察符号操作符比它们的自然语言对应物更“粘”。这可能会导致微妙的错误:

```
arr = [1,2,3]
!arr[0].is_a?(String) && arr.length > 3
 => false
not arr[0].is_a?(String) && arr.length > 3
 => true 
```

根据经验，只使用自然语言操作符来控制执行流程，而对于布尔运算，则坚持使用符号操作符。

### 不要害怕(鸭子打字)收割者

Ruby 不太关心一个对象说它属于哪个类，它更关心这个对象能做什么。如果我想订一个比萨饼，我希望比萨饼店给我提供一个订比萨饼的方法。比萨饼店可以自称为“比萨饼店”、“比萨饼商场”，或者随便什么，我真的不太在乎它的名字。我只是希望它支持`order_pizza`方法。许多来自静态类型背景的开发人员很难接受这个概念。

刚开始用 Ruby 编程的普通静态类型程序员会有这样的想法。这里有一个例子:

```
def my_method(arg)
  #hold on, as there are no type declarations in Ruby, arg could be anything at all
  #better be defensive about this
  if arg.is_a? MyClass #that will ensure I’m dealing with the right type
     # oh, but in Ruby one can delete methods from objects at run-time
     # better protect against that too
     if arg.respond_to? :my_method
        arg.my_method  # now I can safely call my method
     else
        # panic!
     end
  end
  #that’s nice, solid code. Well done me.
end 
```

实际上，这只是臃肿、多余的代码。仅仅因为一些事情会发生并不意味着它会发生。如果你期望你的对象以某种方式行为，它很有可能会这样。如果意外发生，大多数编程语言倾向于通过提供异常处理来处理意外，Ruby 就是这么做的。如果对象不支持某个方法，将会引发异常。

所以，如果你想谨慎行事，只需捕捉异常:

```
def m(arg)
  begin
    arg.my_method
  rescue => e
  # handle exception here
  end
end 
```

这是足够的防御，而不是过分。此外，更有经验的 Ruby 开发人员会知道方法声明本身可以充当 begin 块，因此代码可以变得更简单。

```
def m(arg)
  arg.my_method
rescue => e
 # handle exception here
end 
```

这是没有膨胀的安全代码。鲁比是不是很聪明？

### 一堆假设

太多的`if`和`elsif`会让我们的代码看起来难看并且难以阅读，尤其是当它们是嵌套的时候。对于菜鸟来说，`if..else`的陈述经常被用作敲开任何坚果的锤子。有很多方法可以避免多个条件句的重复和混乱。一个非常常见的解决方案是简单地将至少一个条件语句重构为一个单独的方法。然而，我最喜欢的技术之一是使用散列作为业务规则对象。简而言之，我们可以在一个 Hash 对象中抽象出一些条件逻辑，从而使我们的代码看起来更容易并且可重用。

考虑一下这个:

```
if @document.save
   if current_user.role == "admin"
     redirect_to admin_path
   elsif current_user.role == "reviewer"
     redirect_to reviewer_path
   elsif current_user.role == "collaborator"
     redirect_to collaborator
   else
     redirect_to user_path
   end
 else
   render :new
 end 
```

我们可以使用一个*逻辑散列*对象和[三元运算符](http://ruby.about.com/od/beginningruby/a/The-Ternary-Or-Conditional-Operator.htm)来使代码更加简洁和可维护:

```
redirection_path = Hash.new{|hash,key| hash[key] = user_path}
redirection_path[:admin] =  admin_path
redirection_path[:reviewer] =  reviewer_path
redirection_path[:collaborator] =  collaborator_path

@document.save ? redirect_to redirection_path[current_user.role.to_sym]
    :  (render :new) 
```

我们将 5 条潜在的执行路径缩减为 2 条。但是，等一下……我们可以利用 Ruby 的元编程能力将这种简洁提升到一个新的层次。

```
redirection_path = Hash.new{|hash,key| hash[key] = ( %w(reviewer admin collaborator).include?(key.to_s) ?
                                instance_eval("#{key}_path") : instance_eval('user_path') )}

@document.save ? redirect_to redirection_path[current_user.role.to_sym]
    :  (render :new) 
```

好吧，我承认我们现在有点偏离新手的范畴了。要点是:Ruby 提供了选择简洁和明确的语句而不是嵌套的条件语句的能力，所以继续使用它吧。

### 列出理解中的内容

令人遗憾的是，Ruby 新手没有充分利用由块实现的 Ruby 功能，尤其是在基于其他列表构造列表时。比方说，我们需要从一个列表中提取偶数并将它们乘以 3。然后，我们只想将数字保持在某个阈值以下。许多新手会采用这种方法:

```
arr =[1,2,3,4,5,6,7,8,9,10]

new_arr = []
arr.each do |x|
  if x % 2 == 0
    new_arr << x * 3 if x * 3 < 20
  end
end 
```

这工作得很好，但不太比得上的表现力:

```
arr.select{|x| x % 2 == 0 }.map{|x| x * 3}.reject{|x| x > 19} 
```

通过使用块，我们可以有效地将函数传递给函数。许多 Ruby 方法利用这一点来实现一些简洁、强大但可读性很强的代码。一个 Ruby 新手可能会编写一些代码，有条件地加载一些 Rake 任务，如下所示:

```
load "project/tasks/annotations.rake"
load "project/tasks/dev.rake"
load "project/tasks/framework.rake"
load "project/tasks/initializers.rake"
load "project/tasks/log.rake"
load "project/tasks/middleware.rake"
load "project/tasks/misc.rake"
load "project/tasks/restart.rake"
load "project/tasks/routes.rake"
load "project/tasks/tmp.rake"
load "project/tasks/statistics.rake" if Rake.application.current_scope.empty? 
```

现在来看看 Rails 是如何通过一个优雅的动作实现同样的事情的:

```
%w(
  annotations
  dev
  framework
  initializers
  log
  middleware
  misc
  restart
  routes
  tmp
).tap { |arr|
  arr << 'statistics' if Rake.application.current_scope.empty?
}.each do |task|
  load "rails/tasks/#{task}.rake"
end 
```

利用自引用的 [tap 方法](http://ruby-doc.org/core-2.2.3/Object.html#method-i-tap)和一个[枚举器](http://ruby-doc.org/core-2.2.0/Enumerator.html)(每一个)来产生干净和可维护的代码。漂亮！

## 下一步是什么？

本文介绍了*编码*反模式。在下一篇文章中，我将介绍一下*设计*反模式，这是 Ruby 新手以一种不那么精明的方式构建代码的方式。

## 分享这篇文章