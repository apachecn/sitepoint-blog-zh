# Javascript 中缺少的五个有用的函数

> 原文：<https://www.sitepoint.com/5-useful-functions-missing-in-javascript/>

JavaScript 已经存在好几年了，它的核心不断成熟，包括新的类和函数来帮助程序员完成他们的工作。然而，一些基本的实用功能仍然缺失，而是用像 [jQuery](http://jquery.com/) 、 [Prototype](http://prototypejs.org/) 和 [MooTools](http://mootools.net/) 这样的库来实现。使用这些工具很好，但在某些情况下，它们的使用超出了您的需求。本文涵盖了我一直认为属于 JavaScript 的五个有用的函数。

## getElementsByClassName()

JavaScript 允许通过函数`getElementById()`使用其`id`来检索元素，但是在 HTML5 之前，没有使用类名来获取一个或多个元素的原生函数。新功能名为`getElementsByClassName()`，在 Firefox 3+，Opera 9.5+，Safari 3.1+，以及所有版本的谷歌 Chrome 中都有。不幸的是，正如你可能猜到的，它并不是在所有版本的 Internet Explorer 中都可用，而 Internet Explorer 是网页设计者的头号敌人。只有 Internet Explorer 9+支持`getElementsByClassName()`，所以对于旧版本你需要一个包装器函数。

我发现的最好的函数是罗伯特·尼曼写的。他的实现是在 MIT 许可下发布的，[已经被<abbr title="Web Hypertext Application Technology Working Group">WHATWG</abbr>T5 推荐。<q cite="http://blog.whatwg.org/the-road-to-html-5-getelementsbyclassname">它在支持它的浏览器中使用原生的`getElementsByClassName()`方法，然后回退到鲜为人知的`document.evaluate()`方法，该方法被旧版本的 Firefox(至少从 1.5 开始)和 Opera(至少从 9.27 开始)支持。如果所有其他方法都失败了，脚本就退回到递归遍历 DOM 并收集与给定类名</q>匹配的元素。你可以在下面和他的库](http://blog.whatwg.org/the-road-to-html-5-getelementsbyclassname)[上找到代码。](http://code.google.com/p/getelementsbyclassname/)

```
var getElementsByClassName = function (className, tag, elm){
  if (document.getElementsByClassName) {
    getElementsByClassName = function (className, tag, elm) {
      elm = elm || document;
      var elements = elm.getElementsByClassName(className),
      nodeName = (tag)? new RegExp("\b" + tag + "\b", "i") : null,
      returnElements = [],
      current;
      for(var i=0, il=elements.length; i<il; i+=1){
        current = elements[i];
        if(!nodeName || nodeName.test(current.nodeName)) {
          returnElements.push(current);
        }
      }
      return returnElements;
    };
  }
  else if (document.evaluate) {
    getElementsByClassName = function (className, tag, elm) {
      tag = tag || "*";
      elm = elm || document;
      var classes = className.split(" "),
      classesToCheck = "",
      xhtmlNamespace = "https://www.w3.org/1999/xhtml",
      namespaceResolver = (document.documentElement.namespaceURI === xhtmlNamespace)? xhtmlNamespace : null,
                           returnElements = [], elements, node;
      for(var j=0, jl=classes.length; j<jl; j+=1){
        classesToCheck += "[contains(concat(' ', @class, ' '), ' " + classes[j] + " ')]";
      }
      try {
        elements = document.evaluate(".//" + tag + classesToCheck, elm, namespaceResolver, 0, null);
      }
      catch (e) {
        elements = document.evaluate(".//" + tag + classesToCheck, elm, null, 0, null);
      }
      while ((node = elements.iterateNext())) {
        returnElements.push(node);
      }
      return returnElements;
    };
  }
  else {
    getElementsByClassName = function (className, tag, elm) {
      tag = tag || "*";
      elm = elm || document;
      var classes = className.split(" "),
          classesToCheck = [],
          elements = (tag === "*" && elm.all)? elm.all : elm.getElementsByTagName(tag),
          current,
          returnElements = [],
          match;
      for(var k=0, kl=classes.length; k<kl; k+=1){
        classesToCheck.push(new RegExp("(^|\s)" + classes[k] + "(\s|$)"));
      }
      for(var l=0, ll=elements.length; l<ll; l+=1){
        current = elements[l];
        match = false;
        for(var m=0, ml=classesToCheck.length; m<ml; m+=1){
          match = classesToCheck[m].test(current.className);
          if (!match) {
            break;
          }
        }
        if (match) {
          returnElements.push(current);
        }
      }
      return returnElements;
    };
  }
  return getElementsByClassName(className, tag, elm);
};
```

## 扩展()

如果你曾经写过一个插件，你几乎肯定面临过合并两个或更多对象的问题。当您有一些默认设置并希望用户能够替换一些默认值时，这种情况经常发生。如果您正在使用 jQuery，您可以使用`extend()`，但是因为我们正在讨论原始的 JavaScript，坏消息是没有本地函数。幸运的是，您可以轻松地自己构建它。以下示例将向您展示如何创建与 jQuery 方法工作方式相同的代码。我将我们的`extend()`方法添加到了`Object`原型中，这样所有的对象都可以共享相同的方法。

```
Object.prototype.extend = function() {
  if (arguments.length === 0)
    return this;

  for (var i = 0; i < arguments.length; i++) {
    for (var property in arguments[i]) {
      if (arguments[i].hasOwnProperty(property))
        this[property] = arguments[i][property];
    }
  }
  return this;
};
```

该函数接受可变数量的参数。这是因为使用了`arguments`，一个在每个函数中都可用的类似局部数组的对象。如果你需要更多关于`arguments`的信息，我建议你阅读[arguments:A JavaScript Oddity](https://www.sitepoint.com/arguments-a-javascript-oddity/)。

## 等于()

对象比较是一种非常常见的操作。虽然这个测试可以使用严格的相等操作符(`===`)来完成，但是有时您不想测试两个变量是否引用内存中的同一个对象。相反，您想知道两个对象是否具有相同的属性值。下面的代码正是这样做的。请注意，下面的代码不是我的；它属于一个叫做 [crazyx](http://stackoverflow.com/questions/1068834/object-comparison-in-javascript) 的用户。再次，`equals()`被添加到了`Object.prototype`中。

```
Object.prototype.equals = function(x) {
  var p;
  for(p in this) {
    if (typeof(x[p]) == "undefined")
      return false;
  }
  for(p in this) {
    if (this[p]) {
      switch(typeof(this[p])) {
        case "object":
          if (!this[p].equals(x[p]))
            return false;
          break;
        case "function":
          if (typeof(x[p]) == "undefined" ||
             (p != "equals" && this[p].toString() != x[p].toString()))
            return false;
          break;
        default:
          if (this[p] != x[p])
            return false;
      }
    }
    else {
      if (x[p])
        return false;
    }
  }
  for(p in x) {
    if(typeof(this[p])=="undefined")
      return false;
  }
  return true;
}
```

## inArray()

JavaScript 没有本地方法来测试值是否在数组中。我们将编写一个函数，如您所料，如果值存在，将返回`true`,否则返回`false`。这个函数简单地对给定值和数组中的每个元素进行同一性比较。就像前两个例子一样，`inArray()`被添加到了`Array`类的`prototype`属性中。

```
Array.prototype.inArray = function (value) {
  for (var i = 0; i < this.length; i++) {
    if (this[i] === value)
      return true;
  }
  return false;
};
```

这个函数，由于它的简单性，在很多情况下并不像你预期的那样工作。虽然它对基本类型如`String`和`Numbers`很有效，但如果你比较对象，只有当函数找到相同的对象时，它才返回`true`。为了更好地理解它是如何工作的，让我们看下面的例子。

```
var array = [1, 2, 3];
console.log(array.inArray(2)); // print true

var obj = {"prop": "value"};
array = [{"prop": 1}, {"prop": "a long string"}, {"prop": "value"}];
console.log(array.inArray(obj)); // print false, not true as you might expect

array = [{"prop": 1}, {"prop": "a long string"}, obj];
console.log(array.inArray(obj)); // print true
```

在前面讨论的`equals()`功能的帮助下，可以增强所显示的功能。这样，如果两个对象具有相同的属性和值，我们就可以得到一个匹配。我们可以做的另一个改进是让函数返回元素的位置，而不是简单的`true`或`false`。该函数的最终版本如下所示。

```
Array.prototype.inArray = function (value) {
  for (var i = 0; i < this.length; i++) {
    if (typeof value === "object") {
      // If both are objects, uses the equals function
      if (typeof this[i] === "object" && value.equals(this[i]))
          return i;
    }
    else if (this[i] === value)
      return i;
  }
  return false;
};
```

现在，如果您再次运行上面的示例，您将得到:

```
1
2
2
```

## toggleclass()

jQuery 中另一个常用的方法是`toggleClass()`。它根据类名是否已经存在，在元素中添加或删除一个类。下面是一个简单版本的`toggleClass()`。

```
function toggleClass(id, className) {
  var element = document.getElementById(id);
  var classes = element.className.split(/s+/);
  var length = classes.length;

  for(var i = 0; i < length; i++) {
    if (classes[i] === className) {
      classes.splice(i, 1);
      break;
    }
  }
  // The className is not found
  if (length === classes.length)
    classes.push(className);

  element.className = classes.join(" ");
}
```

这段代码还可以改进。函数中的`for`循环只搜索`classes`数组。我们可以用一个对`inArray()`函数的调用来代替这个循环，得到下面的代码。

```
function toggleClass(id, className) {
  var element = document.getElementById(id);
  var classes = element.className.split(/s+/);
  var length = classes.length;
  var found = classes.inArray(className);
  if (found !== false)
    classes.splice(found, 1);
  // The className is not found
  if (length === classes.length)
    classes.push(className);
  element.className = classes.join(" ");
}
```

## 结论

这篇文章强调了我认为 JavaScript 缺少的一些最重要的功能。当然，JavaScript 遗漏了其他东西，我们将在接下来的几周看到。但是，现在，我想指出以下几点:

*   像 jQuery 这样的框架有很多有用的功能，但是它们增加了开销。所以，如果你只需要几个函数，使用原始的 JavaScript，把你需要的方法放在一个外部文件中。
*   如果您使用了新 JavaScript 版本中引入的函数，不要放弃它。用一个条件语句包装它，测试它是否被支持，如果不被支持，使用你的旧代码，就像为`getElementsByClassName()`指出的那样。这样你将继续支持旧的浏览器。
*   在可能的情况下，向对象的原型添加功能，如`extend()`所示。所有的实例将共享相同的方法，您将有更好的性能。
*   如果可能的话，重用你的代码，如第二版`toggleClass()`所示。

## 分享这篇文章