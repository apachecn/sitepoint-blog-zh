# 模块化设计模式:JavaScript 中的私有、特权和受保护成员

> 原文：<https://www.sitepoint.com/modular-design-patterns-in-javascript/>

在本文中，我将描述扩展模块化设计模式的结构和优点，它包括四种主要成员类型:

*   **public** :可以从任何地方访问的成员
*   **private** :只能从对象内部访问的成员
*   **特权**:只能从对象内部*直接*访问，但可以通过公共方法从外部*间接*访问的成员
*   **受保护的**:只能从对象或其任何模块内部访问的成员。

本文不是关于面向对象的，因为我们将要创建的结构是一种 **singleton** ，这是一种不能实例化多次的单实例结构。事实上，它根本没有被实例化(从有构造函数的意义上来说)，所以它是一个基于**对象的**而不是面向对象的模式。

<cite>关于基于对象和面向对象编程之间的区别，以及 JavaScript 中面向对象编程的介绍，我推荐 Ryan Frishberg 的文章: [JavaScript 面向对象编程](https://www.sitepoint.com/oriented-programming-1/)。</cite>

## 模块化设计模式

我将在这里使用的基本设计模式是**显示模块模式**，它是经典**模块模式**的变体，但是有更多的控制。尽管已经有相当多的文章描述了这些模式，但是很少有人花时间来解释它们是如何工作的，更少有人有如此完整的特性集！

### 基本模块模式

经典的**模块模式**将一个脚本的所有代码包装在一个匿名函数文本中，当它返回时，该文本被赋给一个变量。一个**函数文字**只是一个调用自身的函数，所以函数文字的最终赋值不是函数，而是函数返回的任何值。

举个简单的例子，函数文字包含一个计算，因此分配给`sum`的最终值就是该计算的结果:

```
var sum = (function() { return 6 * 7; })();
```

为了说明该语法的含义，下面是抽象成命名函数的相同代码:

```
function calculate() { return 6 * 7; }
var sum = (calculate)();
```

在这种情况下，我们实际上不需要用括号括住`calculate`，但是在使用匿名函数时我们需要，这样它就可以作为一个单独的表达式来计算，而不需要预先赋值。

### 公共和私人成员

因此，在函数文字中，我们可以放入任何其他代码——比如变量、属性和函数——而正是*不同种类的语法*用于定义内部数据，这些数据决定了公共和私有成员之间的划分。

这里有一个例子:

```
var MyModule = (function() {
  var myPrivateData = 303;
  function myPrivateFunction() {
    alert('private');
  }
  return {
    myPublicData : 42,
    myPublicFunction : function() {
      alert('public');
    }
  };
})();
```

因为我们返回了一个属性对象，并将其赋给了`MyModule`，所以可以从对象外部以`MyModule.myPublicData`和`MyModule.myPublicFunction`的形式访问属性。但是我们根本不能访问`myPrivateData`或`myPrivateFunction`，因为变量只能在它们原来的作用域内访问。变量的**范围**是定义它的上下文，通过使用`var`语句来确定。在这个例子中，私有变量的范围是`MyModule`对象，因此只能从该对象内部访问它们。

### 启示模块模式

在模块模式中，我们使用了两种不同的语法来区分公共成员和私有成员。**揭示模块模式**是它的一个变体，它允许我们对所有的内部结构使用同一种语法，只是在最后遵从对象文字符号，以返回(或揭示)公共成员。

让我们再来看看这个对象，但这次使用显示模块模式:

```
var MyModule = (function() {
  var myPrivateData = 303;
  function myPrivateFunction() {
    alert('private');
  }
  var myPublicData = 42;
  function myPublicFunction() {
    alert('public');
  }
  return {
    myPublicData : myPublicData,
    myPublicFunction : myPublicFunction
  };
})();
```

公共成员和私有成员之间的划分仍然是一样的，但是这一次它不是通过用来定义这些成员的原始语法来实现的，而是简单地通过返回的内容来实现的。这使得揭示模块模式成为一种有用的变体，因为它允许内部一致的编码风格。这也意味着您可以在返回公共成员时更改它们的名称，甚至可以随时更改哪些成员将是公共的。

<cite>这种模式是由 Christian Heilmann 创新的，他对这种模式及其所基于的模块模式做了很好的解释，在他的文章中:[又是模块模式——向世界揭示一些东西](http://christianheilmann.com/2007/08/22/again-with-the-module-pattern-reveal-something-to-the-world/)。</cite>

### 不同语法的好处

虽然有时候，语法和编码风格的不同会有所帮助，只是为了提醒自己和他人哪些成员是公共的，哪些是私有的。您使用的语法可以是一种自我文档，例如，表明使用`function foo`语法声明的函数将总是私有的。

你为一个函数使用的语法种类也会影响你在函数中使用*的语法种类。在下面的例子中，对公共函数使用`this.foo`语法，意味着它可以使用相同的语法来引用其他公共属性(如果它们也是用该语法定义的)。如果**所有的**公共成员都用相同的语法定义，那么你最终需要返回的是`this`:*

```
var MyModule = (function() {
  var myPrivateData = 303;
  function myPrivateFunction() {
    alert('private');
  }
  this.myPublicData = 42;
  this.myPublicFunction = function() {
    alert(this.myPublicData);
  }
  return this;
})();
```

现在这已经不再是一个公开的模块模式了，它只是一个普通的模块模式，只是语法不同而已。但是理解这两种模式及其变体是很重要的，因为我将向您展示的最后一种模式将使用所有这些模式的特性。

### 添加特权成员

虽然私有成员只能从对象内部访问，但是引用它们的公共成员也可以有效地使它们成为公共成员。例如，公共函数可以返回私有变量的值，如下所示:

```
var MyModule = (function() {
  var myPrivateData = 303;
  this.myPublicFunction = function() {
    return myPrivateData;
  }
  return this;
})();
alert(MyModule.myPublicFunction()); //alerts 303
```

当一个公共函数允许从对象外部访问私有数据时，这被称为**特权**。在严格的编程术语中，函数本身是有特权的，但我个人认为将它们访问的*变量视为特权变量更有帮助；他们才是与众不同的。*

问题中的私有变量仍然不能从对象外部直接*访问*，只能通过公共函数间接*访问*。这意味着该功能可以控制**允许什么样的访问**，根据需要或宽松或精确。例如，它可能会限制对最大调用次数的访问，或者在返回值进行转换或规范化之前对其进行解析；上面的例子允许变量被返回但不被修改，但是相反，我们可能希望它被修改但不被返回。

对我来说，对特权成员做的最有用的事情就是将它们用于一个**库的全局配置**。例如，一个动画库会有单独的公共方法，每个方法都有自己的设置作为参数传递。但是它也可以有一个定义全局设置的配置对象，例如决定动画有多粗糙或多精细的分辨率值。使用特权对象，我们可以验证对这些设置的任何更改，例如，将输入值转换为数字，然后拒绝失败的值:

```
var MyLibrary = (function() {
  var config = { resolution : 10 };
  this.define = function(key, value) {
    if(typeof config[key] == 'undefined') {
      alert('There is no config option "' + key + '"');
    } else {
        if(isNaN(value = parseInt(value, 10))) {
          alert('The value defined for "' + key + '" is not a number');
        } else {
          config[key] = value;
        }
    }
  };
  return this;
})();
MyLibrary.define('fail', 20); //alerts the first failure
MyLibrary.define('resolution', 'fail'); //alerts the second failure
MyLibrary.define('resolution', 20); //resolution is now 20
```

这样，配置值被有效地**屏蔽**；由于库用户不能直接修改它们*，他们不可能通过使用无效的设置无意中破坏库的功能。*

 *## 创建附加模块

现在，我们已经创建了一个具有公共、私有和特权成员的设计模式。但是请记住，我们称之为**模块**模式，因此它必须能够在相同的对象上下文中拥有**附加模块(即共享相同的公共数据并具有相同的`this`概念)。**

有几种方法可以实现这一点，但我更喜欢使用`apply()`。**应用方法**允许您指定在其中评估函数的对象上下文，有效地覆盖了`this`的含义。因此，要将额外的模块绑定到`MyModule`上下文中，我们只需修改函数文字语法，使其通过`apply`:

```
var MyModule = (function() {
  this.version = '1.0';
  return this;
})();
var MyModule = (function() {
  this.getVersion = function() {
    return this.version;
  };
  return this;
}).apply(MyModule);
alert(MyModule.getVersion()); //alerts "1.0"
```

以这种方式绑定附加模块有时被称为**增强**。你也可能听说过它被描述为**严格增强**或**宽松增强**——其中**严格增强**意味着模块必须以*同步顺序*加载，与**宽松增强**相反，它们可以以*任何顺序*加载。(普通的`<script>`标签以同步的源代码顺序加载它们的内容，而稍后添加的动态生成的脚本将异步加载。)

根据我的经验，一个应用程序很少能够使用松散增强。模块不可避免地相互依赖，因此必须以特定的顺序加载。我们的例子都是这样的。

### 添加受保护的成员

将一个脚本分成多个模块是一种常见且方便的做法。它使得大型代码库更容易管理，并且当模块并不总是需要时，可以节省带宽。

但是如果我们想在不同的模块之间共享数据呢？如果我们将数据公开，那么我们将失去隐私的好处，但如果我们将数据保密，它将只对一个模块可用。我们真正需要的是*共享私有成员*，这些被称为**保护**。

JavaScript 本身没有受保护的成员，但是我们可以通过让数据**暂时公开**来有效地创建它们。为了实现这一点，让我首先向您介绍两个关键函数——`extend`和`privatise`——我们将它们定义为 utility-functions 对象的一部分:

```
var utils = {
  extend : function(root, props) {
    for(var key in props) {
      if(props.hasOwnProperty(key)) {
        root[key] = props[key];
      }
    } return root;
  },
  privatise : function(root, prop) {
    var data = root[prop];
    try { delete root[prop]; } catch(ex) { root[prop] = null; }
    return data;
  }
};
```

**扩展函数**简单地给对象添加新的属性，而**私有化函数**复制一个属性，然后删除原来的属性。我们可以在一个模块中使用`extend`创建对私有变量的公共引用，然后在另一个模块中使用`privatise`将其复制回私有变量并删除公共引用。

所以这里有一个**第一个模块**的例子，它有两个受保护成员(包括`utils`对象本身)和一个公共成员。为了简化代码示例，实用函数只是空壳，但它们与我刚才展示的函数是相同的:

```
var MyModule = (function() {
  var myProtectedData = 909;
  var utils = {
    extend : function(root, props) { },
    privatise : function(root, prop) { }
  };
  this.myPublicData = 42;
  return utils.extend(this, { myProtectedData : myProtectedData, utils : utils });
})();
```

您可以看到我们如何使用揭示模块模式的变体，不仅返回公共成员，还返回受保护的成员。所以在这一点上，我们有*三个*公共成员:`MyModule.myProtectedData`、`MyModule.utils`和`MyModule.myPublicData`。

现在这里有一个**最后一个模块**的例子，它使用`privatise`函数将指定的公共成员复制回私有变量，然后删除它们的公共引用:

```
var MyModule = (function() {
  var myProtectedData = this.utils.privatise(this, 'myProtectedData');
  var utils = this.utils.privatise(this, 'utils');
  return this;
}).apply(MyModule);
```

一旦这样做了，受保护的成员就被<q>锁定</q>在它们的对象内部，对两个模块都是私有的，但是从它们的外部就不再可用了。

注意，`privatise`函数依赖于让*将对象和属性键的参数*分开，因为 JavaScript 中的对象是通过引用传递的**。所以`root`是对`MyModule`的引用，当我们从其中删除一个由`key`指定的属性时，我们也从被引用的对象中删除了该属性。**

但是如果是这样:

```
privatise : function(root) {
  var data = root;
  try { delete root; } catch(ex) { root = null; } return data;
}
```

并且这样称呼:

```
var myProtectedData = this.utils.privatise(this.myProtectedData);
```

那么公共成员**将不会被删除**——该函数将简单地删除*引用*，而不是它所引用的属性。

对于不支持`delete`的旧版本 <abbr title="Internet Explorer">IE</abbr> 来说，`try ... catch`构造也是必要的。在这种情况下，我们使公共属性无效，而不是删除它，这显然是不一样的，但具有否定成员公共引用的等价最终结果。

### 扩展受保护成员

有了这个框架，我们可以添加任意数量的附加模块，并让它们共享受保护的成员，只需将它们添加到第一个和最后一个模块之间**。在这些中间模块中，成员没有被私有化，他们只是通过:**

```
var MyModule = (function() {
  var myProtectedData = this.myProtectedData;
  var utils = this.utils;
  return this;
}).apply(MyModule);
```

事实上，完全没有必要将受保护的成员复制到私有变量中，但是这样做意味着我们在每个模块中都有一致的引用形式。

我们还可以在特定于模块的基础上扩展受保护的对象，例如，定义只有某些模块需要的额外的实用函数。这种扩展显然也可用于后面的模块:

```
var MyModule = (function() {
  var myProtectedData = this.myProtectedData;
  var utils = this.utils.extend(this.utils, { extraStuff : function() { } });
  return this;
}).apply(MyModule);
```

最后要注意的一点是**受保护的成员也可以享受特权**。我之前向您展示的特权`config`对象的例子是可以有效保护的数据的主要候选对象。最终结果将是所有模块都可以使用的配置设置，但是如果不通过公共的`define`函数，用户仍然不能修改这些设置。

## 最终的扩展模块模式

我准备了一个下载文件，其中包含了本文中涉及的所有特性，并被分成**三个独立的文件** : `Master.js`是声明原始成员的根对象，`Extension.js`是可选的中间模块(可以使用任意数量的实例)，然后`Runtime.js`是密封受保护成员的最后一个模块:

*   **[扩展模块模式示例](https://github.com/jsprodotcom/source/blob/master/extended-module-pattern.zip)**

## 分享这篇文章*