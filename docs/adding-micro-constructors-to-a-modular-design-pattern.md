# 向模块化设计模式添加微构造器

> 原文：<https://www.sitepoint.com/adding-micro-constructors-to-a-modular-design-pattern/>

最近在 <cite>JSPro</cite> 上，我写了一篇关于[模块化设计模式](https://www.sitepoint.com/modular-design-patterns-in-javascript/ "Modular Design Patterns: Private, Privileged, and Protected Members in JavaScript")的文章，四种不同的成员类型为如何组织脚本提供了高度的灵活性。在这篇后续文章中，我将通过添加**微构造函数**，即对象构造函数的公共函数，来寻找扩展该模式的方法，这样你就可以拥有*多个独立的模块功能实例*。这有效地结合了面向对象的能力，以及单例设计模式的控制和灵活性。


## 示例使用案例

最近，一位客户要求我开发一个用于在线调查的小部件。该小部件将有一个出现在实际调查中的界面，这是调查受访者用来回答问题的。它在调查控制面板中也有一个不同的界面，这是调查所有者用来设计问题和回答的。出于商业保密的原因，我不能详细描述这个小部件实际做了什么，但可以说它代表了一个特殊的编程挑战，仅仅是因为它必须支持的架构模型:

1.  有两个接口，在任何给定的页面上，可以有任意数量的一个或另一个的实例(但不能两个都有)。
2.  每个接口实例都需要自己的公共方法——比如用于与该实例交互的`load`和`save`。
3.  它还需要控制功能来管理任一接口的所有实例，并在它们之间共享数据。

所以为了满足所有这些要求，我想出了这个主意；但是事情并没有那么简单！

### 公共构造函数的问题是

每个接口需要能够支持多个实例，而[面向对象](https://www.sitepoint.com/oriented-programming-1/ "JavaScript Object-Oriented Programming")是这方面的完美解决方案，因为公共构造函数可以被多次调用。此外，我们可以通过将它们定义为原型来创建必要的实例方法。但是所有这些实例仍然需要由控制模块管理，并且都没有不需要的公共数据。

构造对象的实例在内部被称为`this`，因此构造对象的属性是使用`this.property`语法定义的。如果我们希望原型方法能够访问构造函数的数据，我们必须用公共语法定义这些属性。构造函数中定义的私有变量只能在构造函数的中*访问。问题是:如果构造函数是公共的，那么它的属性也是公共的。*

那么，我们如何实现构造的公共对象的特定于实例的属性，同时将所有数据隐藏在私有范围内呢？其实比听起来简单！

## 小部件的模块结构

让我们先来看看小部件的模块结构，它将代码分成两个独立的脚本。第一个脚本是`Widget.js`，它创建根对象并定义所有共享功能，类似于[上一篇文章](https://www.sitepoint.com/modular-design-patterns-in-javascript/ "Modular Design Patterns: Private, Privileged, and Protected Members in JavaScript")中的<q>主</q>模块示例。除了预期的 config 和 utility functions 对象之外，还有另一个名为`instances`的受保护对象，我们稍后会详细讨论。为了保持代码示例简短，对象和函数只是空壳，但是您可以在本文末尾获得完整的代码。

```
var Widget = (function()
{
  var instances = {},
      config = {},
      utils = {
        extend : function(root, props){ ... },
        privatise : function(root, prop){ ... }
      };

  this.define = function(key, value){ ... };

  return utils.extend(this,
  {
    instances : instances,
    config    : config,
    utils     : utils
  });
})();
```

第二个脚本不是`DeveloperInterface.js`就是`RespondentInterface.js`，就像[上一篇文章](https://www.sitepoint.com/modular-design-patterns-in-javascript/ "Modular Design Patterns: Private, Privileged, and Protected Members in JavaScript")中的<q>运行时</q>模块示例。它的首要工作是密封受保护的成员。这是定义公共接口构造函数的地方，公共对象也有自己的公共方法。在任何给定的页面上只需要一个界面脚本，对于这个例子，我使用的是开发人员界面。

```
Widget = (function()
{
  var instances = this.utils.privatise(this, 'instances'),
      config = this.utils.privatise(this, 'config'),
      utils = this.utils.privatise(this, 'utils');

  this.DeveloperInterface = function()
  {
  };
  this.DeveloperInterface.prototype =
  {
    load : function(){ ... },
    save : function(){ ... }
  };

  return this;
}).apply(Widget);
```

## 在构造函数内部

公共构造函数用于创建接口的实例，并将引用键(部分`id`)传递给它增强的静态标记。

```
var example = new Widget.DeveloperInterface("A1");
```

该键用于获取对标记的一个 <abbr title="Document Object Model">DOM</abbr> 引用。这两个值都需要从`load`和`save`方法中访问。在其他条件相同的情况下，我们将它们定义为公共财产:

```
this.DeveloperInterface = function(key)
{
  this.key = key;
  this.question = document.getElementById('Question-' + this.key);
};
```

但是现在的问题是这两个值都可以从小部件外部访问，作为实例`example.key`和`example.question`的属性。我们实际上想要的是接口的大部分数据对小部件是私有的；但是我们已经知道不能仅仅用私有变量来定义它。

所以这是不可避免的——在这个过程中，我们别无选择，只能创造公共财产。但是，我们可以将该数据限制为单个引用值，然后使用该值来引用私有数据。这就是`instances`对象的用途。

### 使用实例对象

让我们再次定义构造函数，但是这次使用由实例`key`引用的`instances`对象:

```
this.DeveloperInterface = function(key)
{
  this.key = key;
  instances[this.key] =
  {
    question : document.getElementById('Question-' + this.key)
  };
};
```

`key`是参考值，也是唯一的公共属性。`question`属性现在被屏蔽在一个受保护的对象中，但是接口方法仍然可以像`instances[this.key].question`一样访问它。然后可以用任意数量的属性扩展`instances`对象，所有这些属性对于小部件都是私有的，但对于实例方法都是可用的。

### 用钥匙吊着

不屏蔽数据的危险在于，用户可能会无意中弄坏东西。例如，将自定义属性添加到恰好与现有属性同名的单个实例中可能会导致严重的明显问题。不幸的是，这往往只是一个微妙的或间歇性的问题。更糟糕的是，它可能表现为只发生在特定配置或特定浏览器上的事情。

如果我们承认我们不能使这些实例完全安全，我们至少可以确保任何这样的问题都是显而易见的，只需影响一些能迅速中止脚本执行的东西。我们的公钥就是这样，因为它的丢失或修改会破坏实例使用的所有其他数据的主引用。

## 保持全球控制

数据屏蔽当然很重要，但同样重要的是，我们现在有了所有接口实例的集中引用。这使得实现总体功能成为可能。接口脚本中的函数可以遍历所有实例，从它们读取数据，将数据写回它们，或者进行管理和控制所需的任何操作。因为`instances`对象受到保护，所以主`Widget`模块也可以访问它。由此，我们可以实现应用于任一接口实例的共享功能。

但是假设我们使用委托事件监听器共享功能——事件绑定到整个文档，然后通过`target`引用过滤。识别事件何时来自问题元素内部很简单，但是我们如何知道该元素属于哪个对象实例呢？要做到这一点，我们需要定义一个额外的循环引用——引用回其所属实例的`question`元素的属性。

```
this.DeveloperInterface = function(key)
{
  this.key = key;
  instances[this.key] =
  {
    question : document.getElementById('Question-' + this.key)
  };

  instances[this.key].question.instance = this;
};
```

这里有一个使用全局`click`事件的简单例子。事件侦听器将在主`Widget`模块中定义，然后通过在任何实例化接口的`question`元素中单击来触发:

```
document.addEventListener('click', function(e)
{
  var target = e.target;
  do
  {
    if(typeof(target.instance) !== 'undefined')
    {
      break;
    }
  }
  while(target = target.parentNode);

  if(target)
  {
    alert(target.instance.key);
    alert(target === instances[target.instance.key].question);
  }
}, false);
```

从这些示例警报中，您可以看到我们如何使用`instance`引用来引用实例`key`，并由此循环引用回`target`。

## 最终的构造函数模块模式

我准备了一个下载文件，其中包含了本文涉及的所有特性。它被分成两个独立的文件，`Widget.js`和`DeveloperInterface.js`，如本文所述:

*   [构造器模块模式示例](https://github.com/jsprodotcom/source/blob/master/constructor-module-pattern.zip)

## 分享这篇文章