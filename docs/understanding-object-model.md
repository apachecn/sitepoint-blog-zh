# 理解对象模型

> 原文：<https://www.sitepoint.com/understanding-object-model/>

![Structure and hierarchy background](img/e3f6dac4b131155004199dc1365d6c79.png)

你有没有想过 Ruby 为什么会这样？

```
class A
  def self.my_method
    'hello'
  end
  def my_other_method
    'hello again'
  end
end

A.my_method # => "hello"

a = A.new
a.my_other_method # => "hello again"

a.my_method # => NoMethodError: undefined method
```

如果您来自 Java 这样的经典语言，您可能会认为`my_method`很像一个静态方法。但是，有一些关键的区别:

*   类方法只能从类中调用并被继承。
*   实例方法只能通过类的实例调用，并被继承。
*   实例对象也可以声明“类”方法。

困惑了吗？

在本文中，我们将探索对象模型。这将让您更深入地了解 Ruby 堆栈和管理对象的方式。出于本文的目的，我们将使用 Ruby MRI 2.1。如果你感兴趣，我推荐你在控制台上启动`irb`并尝试一下。别忘了穿泳衣，因为我们要去游泳池的深水区。我也要穿上我的。

## 什么是单例方法？

如果我告诉你 Ruby 中的每个方法都是单例方法会怎么样？如果我对你说的是真的，那么每个单例方法必须属于一个“类”单例对象。现在，如果你知道经典语言中的单例设计模式，忘记你所学的一切。如果你从未听说过单例方法，那么你已经领先一步了。让我们来看看:

```
class D; def method_one; 'hello from method_one'; end; end
def D.method_two; 'hello from method_two'; end
D.method_two
# => "hello from method_two"
d = D.new
d.method_one
# => "hello from method_one"
```

从 Ruby 的角度来看，这两个方法调用是相似的。从理论上讲，我们拥有的是被调用的单例对象的单例方法。因此，当接收者`D`或`d`调用一个方法时，Ruby 中必须有一个查找，直接一步到达“类”并向上到达祖先链。但是等等，你说，一个不是实例方法，一个不是类方法吗？

让我们仔细看看这个兔子洞有多深:

```
d.class
# => D
d.class.instance_methods.grep(/method_one/)
# => [:method_one]
```

到目前为止，一切顺利。接收器`d`中的方法查找直接一步到达“类”并找到 method_one。记住，在 Ruby 中，类也是单例对象。但是，D 呢？

```
D.class
# => Class
D.class.instance_methods.grep(/method_two/)
# => []
```

等等，有点不对劲。我的方法没有在`Class`中定义，所以肯定有比看起来更多的东西。

嗯，有些事情感觉不太对劲，那么这个怎么样:

```
def d.method_three; 'hello from method_three'; end;
d.method_three
# => "hello from method_three"
d.class
# => D
d.class.instance_methods.grep(/method_three/)
# => []
```

再等一秒钟！`method_three`也没有在 D 类中定义。也许有一些关键的东西我们没有看到。有一点是肯定的，这最后两次调用，它们都从它们应该的类中消失了。为了让我们的单例方法假设有任何意义，这些方法必须属于一个“类”。让我们看看能否找到这些难以捉摸的类。

## 输入特征类

在 Ruby 中，术语似乎在如何称呼这些元类上存在分歧。松本幸宏还没有宣布正式的名字。但是他似乎喜欢数学家友好的名字**本征类**。另一个被接受的术语是**单例类**。

德语中的单词 *eigen* (发音:AYE-gun)，大致意思是“自己的”。在这种情况下，意味着一个对象的“自己的类”在本文中，我们将坚持使用本征类作为我们的术语。

要在 Ruby 中找到这些难以捉摸的 UFO，您可以:

```
d.singleton_class
# => #<Class:#<D:0x007ff882b629c0>>
```

或者，如果你想用你疯狂的 Ruby 技能给你的朋友留下深刻印象，那就动手吧:

```
class Object; def eigenclass; class << self; self; end; end; end
d.eigenclass
# => #<Class:#<D:0x007ff882b629c0>>
```

通过这段代码，我们重新打开了祖先链中的`Object`(稍后将详细介绍)并添加了一个方法。这种方法直接进入本征类，并返回“自我”，这是接收器的本征类。巧妙的诡计，是吧？

现在我们已经找到了我们的特征类，让我们看看我的理论是否有意义:

```
D.eigenclass.instance_methods.grep(/method_two/)
# => [:method_two]
d.eigenclass.instance_methods.grep(/method_three/)
# => [:method_three]
```

Tada！我们已经找到了方法。注意这个方法查找与`method_one`上的查找没有任何不同。概括地说，这是通向“阶级”和祖先链的第一步。Ruby 似乎用一种叫做`singleton_methods`的查找方法为我们包装了这段代码，但是现在我们知道这一切都是虚幻的。

我应该提一下鸭子类型，因为有些人被 Ruby 中的单例方法吓坏了。鸭子打字是:“如果它走路像鸭子，呱呱叫像鸭子，那它一定是鸭子。”在 Ruby 中，对象的“类型”是它所响应的一组单独的方法。它是**而不是**绑定到类定义的。

我知道，你可能会听到蜘蛛侠的本叔叔在你脑海中说:“权力越大，责任越大。”Ruby 总是假设您负责运用这项强大的技术。

## 行动中的方法查找

有了我们对 Ruby 的可靠了解，我们应该能够向深水区划去。让我们起草一个快速的“实验室老鼠”程序，这样我们可以验证工作中的对象模型:

```
class C; def an_instance_method; end; end
def C.a_class_method; end
class E < C; end
obj = E.new
def obj.a_singleton_method; end
```

现在，让我们直接进入“类”,沿着祖先链向上，看看一切是如何安排的:

```
arr = [:an_instance_method, :a_class_method, :a_singleton_method]
obj.eigenclass
# => #<Class:#<E:0x007f8b2a85cd40>>
obj.eigenclass.instance_methods.select { |m| arr.include?(m) }
# => [:a_singleton_method, :an_instance_method]
obj.eigenclass.superclass
# => E
obj.class
# => E
E.superclass
# => C
C.instance_methods.select { |m| arr.include?(m) }
# => [:an_instance_method]
C.superclass
# => Object
Object.superclass
# => BasicObject
BasicObject.superclass
# => nil
```

需要注意的重要一点是，对象的特征类的超类是对象的类。我建议一遍又一遍地重复最后一句话，直到一切运转正常。祖先链停留在类上，这就是为什么我们可以在 obj 的特征类中找到方法。如图所示，`:an_instance_method`属于`C`。同样，`instance_methods`也从祖先链中提取方法。

我们班的课程怎么样？

```
E.eigenclass
# => #<Class:E>
E.eigenclass.superclass
# => #<Class:C>
C.eigenclass.instance_methods.select { |m| arr.include?(m) }
# => [:a_class_method]
C.eigenclass.superclass
# => #<Class:Object>
Object.eigenclass.superclass
# => #<Class:BasicObject>
BasicObject.eigenclass.superclass
# => Class
Class.superclass
# => Module
Module.superclass
# => Object
```

看上面的例子，一个类的特征类的超类就是这个类的超类的特征类。现在，重复最后一句话，超快！类和特征类的祖先链在`Object`合并。看，和“大男孩”一起游泳并不困难。

## 但是，模块呢？

事实证明，在 Ruby 中，模块和类没什么不同。关键的区别在于，当你键入超类时，它们不会出现在祖先链中。让我们看看是否有跟踪模块的方法，试试:

```
module F; def a_module_method; end; end
class E; include F; end
E.superclass
# => C
E.ancestors
# => [E, F, C, Object, Kernel, BasicObject]
```

正如您所看到的，当您添加一个模块时，它会被附加到祖先链中。我们可以通过以下方式对此进行验证:

```
E.instance_methods.include?(:a_module_method)
# => true
```

## 最后

好了，现在是突击测验的时间了！

你能告诉我当我这样做的时候会发生什么吗？

```
module F; class G; end; end
```

这在祖先链中看起来如何？有办法实例化 G 类吗？如果你完全迷失了，留下评论。

Ruby 对象模型神秘而简单。一旦你理解了它，整个语言就会变得井井有条。我希望你现在对掌握这门美丽的语言有了更多的了解。

黑客快乐！

## 分享这篇文章