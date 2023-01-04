# ES6 实践:如何使用代理

> 原文：<https://www.sitepoint.com/es6-proxies/>

用计算术语来说，代理位于你和你正在交流的事物之间。该术语最常用于代理服务器，即介于浏览器(Chrome、Firefox、Safari、Edge 等)之间的设备。)和 web 服务器(Apache、Nginx、IIS 等。)页面所在的位置。代理服务器可以修改请求和响应。例如，它可以通过缓存定期访问的资产并将其提供给多个用户来提高效率。

ES6 代理位于代码和对象之间。代理允许您执行元编程操作，例如拦截检查或更改对象属性的调用。

以下术语与 ES6 代理相关:

**目标**
代理将虚拟化的原始对象。这可能是一个 JavaScript 对象，如 jQuery 库，也可能是本机对象，如数组，甚至是其他代理。

**处理程序**
一个对象，它使用…

**traps**
在处理程序中定义的函数，当调用特定的属性或方法时，这些函数提供对目标的访问。

最好用一个简单的例子来解释。我们将创建一个名为`target`的目标对象，它有三个属性:

```
const target = {
  a: 1,
  b: 2,
  c: 3
}; 
```

我们现在将创建一个拦截所有`get`操作的 handler 对象。这将在目标的属性可用时返回该属性，否则返回 42:

```
const handler = {
  get: function(target, name) {
    return (
      name in target ? target[name] : 42
    );
  }
}; 
```

我们现在通过传递目标和处理程序对象来创建一个新的代理。我们的代码可以与代理交互，而不是直接访问`target`对象:

```
const proxy = new Proxy(target, handler);

console.log(proxy.a);  // 1
console.log(proxy.b);  // 2
console.log(proxy.c);  // 3
console.log(proxy.meaningOfLife);  // 42 
```

让我们进一步扩展代理处理程序，这样它只允许设置从`a`到`z`的单字符属性:

```
const handler = {
  get: function(target, name) {
    return (name in target ? target[name] : 42);
  },

  set: function(target, prop, value) {
    if (prop.length == 1 && prop >= 'a' && prop <= 'z') {
      target[prop] = value;
      return true;
    }
    else {
      throw new ReferenceError(prop + ' cannot be set');
      return false;
    }
  }
};

const proxy = new Proxy(target, handler);

proxy.a = 10;
proxy.b = 20;
proxy.ABC = 30;
// Exception: ReferenceError: ABC cannot be set 
```

## 代理陷阱类型

我们已经看到了`get`和`set`的作用，它们可能是最有用的陷阱。但是，您可以使用其他几种陷阱类型来补充代理处理程序代码:

*   **construct(target，argList)**
    用`new`操作符捕获新对象的创建。
*   **get(目标，属性)**
    Traps `Object.get()`，必须返回属性值。
*   **设置(目标，属性，值)**
    陷阱`Object.set()`并且必须设置属性值。如果成功，返回`true`。在严格模式下，返回`false`将抛出一个 TypeError 异常。
*   **deleteProperty(target，property)**
    对对象的属性进行`delete`操作。必须返回`true`或`false`。
*   **apply(target，thisArg，argList)**
    Traps 对象函数调用。
*   **有(目标，属性)**
    陷阱`in`运算符，必须返回`true`或`false`。
*   **own keys(target)**
    Traps`Object.getOwnPropertyNames()`，必须返回一个可枚举对象。
*   **getPrototypeOf(target)**
    Traps`Object.getPrototypeOf()`并且必须返回原型的对象或 null。
*   **setPrototypeOf(目标，原型)**
    Traps `Object.setPrototypeOf()`设置原型对象。不返回值。
*   **is extensible(target)**
    Traps`Object.isExtensible()`，决定一个对象是否可以添加新的属性。必须返回`true`或`false`。
*   **prevent extensions(target)**
    Traps`Object.preventExtensions()`，防止新属性被添加到对象中。必须返回`true`或`false`。
*   **getOwnPropertyDescriptor(target，property)**
    Traps `Object.getOwnPropertyDescriptor()`，返回未定义或者属性为`value`、`writable`、`get`、`set`、`configurable`和`enumerable`的属性描述符对象。
*   **defineProperty(目标，属性，描述符)**
    Traps `Object.defineProperty()`定义或修改一个对象属性。如果成功定义了目标属性，则必须返回`true`，否则必须返回`false`。

## 代理示例 1:概要分析

代理允许您为任何对象创建通用包装器，而无需更改目标对象本身的代码。

在这个例子中，我们将创建一个分析代理，它计算一个属性被访问的次数。首先，我们需要一个返回`Proxy`对象并保留计数状态的`makeProfiler`工厂函数:

```
// create a profiling Proxy
function makeProfiler(target) {

  const
    count = {},
    handler = {

      get: function(target, name) {
        if (name in target) {
          count[name] = (count[name] || 0) + 1;
          return target[name];
        }
      }
    };

  return {
    proxy: new Proxy(target, handler),
    count: count
  }
}; 
```

我们现在可以将这个代理包装器应用于任何对象或另一个代理。例如:

```
const myObject = {
  h: 'Hello',
  w: 'World'
};

// create a myObject proxy
const pObj = makeProfiler(myObject);

// access properties
console.log(pObj.proxy.h); // Hello
console.log(pObj.proxy.h); // Hello
console.log(pObj.proxy.w); // World
console.log(pObj.count.h); // 2
console.log(pObj.count.w); // 1 
```

虽然这是一个简单的例子，但是想象一下，如果您必须在不使用代理的情况下在几个不同的对象中执行属性访问计数，需要付出多大的努力。

## 代理示例 2:双向数据绑定

数据绑定同步对象。它通常用于 JavaScript MVC 库中，当 DOM 改变时更新内部对象，反之亦然。

假设我们有一个 ID 为`inputname`的输入字段:

```
<input type="text" id="inputname" value="" /> 
```

我们还有一个名为`myUser`的 JavaScript 对象，其属性`id`引用了这个输入:

```
// internal state for #inputname field
const myUser = {
  id: 'inputname',
  name: ''
}; 
```

我们的第一个目标是当用户改变输入值时更新`myUser.name`。这可以通过字段上的`onchange`事件处理程序来实现:

```
inputChange(myUser);

// bind input to object
function inputChange(myObject) {
  if (!myObject || !myObject.id) return;

  const input = document.getElementById(myObject.id);
  input.addEventListener('onchange', function(e) {
    myObject.name = input.value;
  });
} 
```

我们的下一个目标是在 JavaScript 代码中修改`myUser.name`时更新输入字段。这并不简单，但是代理提供了一个解决方案:

```
// proxy handler
const inputHandler = {
  set: function(target, prop, newValue) {
    if (prop == 'name' && target.id) {
      // update object property
      target[prop] = newValue;

      // update input field value
      document.getElementById(target.id).value = newValue;
      return true;
    }
    else return false;
  }
}

// create proxy
const myUserProxy = new Proxy(myUser, inputHandler);

// set a new name
myUserProxy.name = 'Craig';
console.log(myUserProxy.name); // Craig
console.log(document.getElementById('inputname').value); // Craig 
```

这可能不是最有效的数据绑定选项，但是代理允许您改变许多现有对象的行为，而无需更改它们的代码。

## 进一步的例子

赫曼思。HM 的文章[JavaScript 中的负数组索引](http://h3manth.com/new/blog/2013/negative-array-index-in-javascript/)建议使用代理来实现负数组索引。例如，`arr[-1]`返回最后一个元素，`arr[-2]`返回倒数第二个元素，依此类推。

Nicholas C. Zakas 的文章[用 ECMAScript 6 代理创建类型安全属性](https://www.nczonline.net/blog/2014/04/29/creating-type-safe-properties-with-ecmascript-6-proxies/)展示了如何通过验证新值来使用代理实现类型安全。在上面的例子中，我们可以验证`myUserProxy.name`总是被设置为一个字符串，否则抛出一个错误。

## 代理支持

代理的力量可能不会立即显现出来，但是它们提供了强大的元编程机会。JavaScript 的创造者 Brendan Eich 认为[代理很棒](https://www.youtube.com/watch?v=sClk6aB_CPk)！

目前，除了 Internet Explorer 11 之外，在 Node 和所有当前浏览器中都实现了代理支持。但是，请注意，不是所有的浏览器都支持所有的陷阱。通过查阅 MDN 代理页面上的这个[浏览器兼容性表](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy#Browser_compatibility)，您可以更好地了解支持什么。

不幸的是，使用像 [Babel](https://babeljs.io/) 这样的工具来填充或传输 ES6 代理代码是不可能的，因为代理是强大的，没有 ES5 的等价物。

## 分享这篇文章