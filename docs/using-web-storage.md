# 使用 Web 存储

> 原文：<https://www.sitepoint.com/using-web-storage/>

欢迎阅读小而可爱的函数系列的另一篇文章，在这篇文章中，我将向您展示一个名为`domStorage()`的简洁抽象，它提供了一组与 Web 存储交互的方便函数，同时离散地处理所有最常见的故障点。

<cite>如果你还不熟悉 Web 存储(又名 DOM 存储)，我推荐科林·伊里格的文章:[Web 存储 API 概述](https://www.sitepoint.com/an-overview-of-the-web-storage-api/)。</cite>

下面是`domStorage()`函数的代码:

```
function domStorage(persist)
{
try
{
var storage = window[persist ? 'localStorage' : 'sessionStorage'];
if(!storage) { return null; }
}
catch(ex) { return null; }

return {

read : function(key)
{
return storage.getItem(key);
}

, write : function(key, value)
{
try
{
return storage[key] = value.toString();
}
catch(ex) { return null; }
}

, erase : function(key)
{
storage.removeItem(key);
return true;
}

, keys : function()
{
for(var keys = [], n = storage.length, i = 0; i < n; i ++)
{
keys.push(storage.key(i));
}
return keys;
}

, clear : function()
{
try
{
storage.clear();
return true;
}
catch(ex) { return false; }
}
};
}
```

## 如何使用该功能

`domStorage()`函数返回一个对象，该对象包含一些方法，这些方法可以用作读写值等操作的快捷方式。然而，如果 Web 存储不被支持，或者在浏览器中被禁用，那么`domStorage()`函数将返回`null`。首先调用`domStorage()`并保存它返回的引用，如下所示。

```
var storage = domStorage();
```

默认情况下，该函数会创建一个对*会话存储*的引用，该数据会在浏览器会话结束时自动删除。您还可以通过传入布尔值`true`来选择持久存储，如下所示。在这种情况下，数据将一直存在，直到您或用户将其删除。

```
var storage = domStorage(true);
```

一旦有了`storage`引用，在尝试使用它的方法之前，应该测试它不是`null`:

```
var storage = domStorage();
if(storage !== null)
{
//storage is supported
}
```

主要的方法有`read()`，它采用单个`key`参数，以及`write()`，它采用一个`key`和`value`。存储交互是**同步的**，所以一旦写入一个值，您就可以读取它:

```
storage.write("key", "value");
storage.read("key");
```

所有存储的值都是字符串，任何其他数据类型都被静默转换(例如，`123`被保存为`"123"`)。如果需要存储复杂的数据，最好的方法是将其保存为 JSON 字符串:

```
storage.write("key", JSON.stringify(data));
```

如果成功，`read()`和`write()`方法都返回一个字符串，如果失败则返回`null`。`read()`的失败意味着该键没有存储值，而`write()`的失败意味着该值没有被保存。根据浏览器首选项设置的配额，只有当值*对于可用的剩余存储空间*太大时，才会发生这种情况，我们将在下一节讨论这一点。

```
if(storage.write("key", "value") === null)
{
//value was not saved
}
```

还有两种删除方法，一种用于删除单个值:

```
storage.erase("key");
```

另一个用于清除存储对象中每个值的*(所以要小心！):*

```
storage.clear();
```

最后，有一个`keys()`方法，为存储对象中当前定义的每个值返回一个字符串键数组:

```
var keys = storage.keys();
```

例如，您可以使用它来检查一个存储对象，并找出它包含多少数据:

```
var size = 0,
keys = storage.keys();

for(var n = keys.length, i = 0; i < n; i ++)
{
size += storage.read(keys[i]).length;
}

alert((size / 1000).toFixed(2) + 'K');
```

## 该功能如何工作

本质上，`domStorage()`函数所做的只是定义一组快捷方法，但它也处理几个可能导致错误的故障点。第一种，也是最有可能的，失败发生在获取对存储对象本身的引用时(根据`persist`参数，要么是`sessionStorage`要么是`localStorage`)。可能不支持存储对象，但即使支持，引用时也可能会引发错误。这是因为规范允许浏览器在存储的使用违反策略决策(如被用户禁用)时抛出安全错误。所以，这是我们需要异常处理的第一个地方，捕捉任何这样的错误并返回`null`失败:

```
try
{
var storage = window[persist ? 'localStorage' : 'sessionStorage'];
if(!storage) { return null; }
}
catch(ex) { return null; }
```

下一个潜在的失败是当**保存一个值**时，因为我们可以存储的数据量是有限制的。浏览器会设置一个配额，大多数浏览器还会提供用户调整配额的偏好。据我所知，没有可靠的方法通过编程来确定配额是多少，但通常足够了——从`2-5MB`开始，取决于浏览器。如果保存一个值会超过该限额，浏览器将抛出另一个错误。因此，我们处理它并返回失败的`null`:

```
try
{
return storage[key] = value.toString();
}
catch(ex) { return null; }
```

您会注意到，我使用了方括号符号而不是`setItem()`函数，这只是为了方便在单个表达式中保存和返回值，作为替代:

```
try
{
storage.setItem(key, value);
return value.toString();
}
catch(ex) { return null; }
```

最后一个失败点是在使用`clear()`函数的时候，因为一些早期的实现不支持它(比如 Firefox 3)。为了避免在这些实现中引发错误，我们再次使用异常处理，然后返回`true`或`false`来表示成功:

```
try
{
storage.clear();
return true;
}
catch(ex) { return false; }
```

如果您确实需要这个功能在旧的实现中工作，那么使用`keys()`方法做同样的事情就足够简单了——遍历它返回的键并手动删除每个值:

```
if(storage.clear() === false)
{
var keys = storage.keys();

for(var n = keys.length, i = 0; i < n; i ++)
{
storage.erase(keys[i]);
}
}
```

注意，Firefox 2-3 对 Web 存储的支持有限——除了缺少对`clear()`的支持，根本不支持`localStorage`，只支持`sessionStorage`。同样值得注意的是，IE7 根本不支持 Web 存储。即使在兼容模式下，IE8 也支持它，这给人一种 IE7 也支持它的错觉。

## 结论

使`domStorage()`成为一个有用的抽象的是它以无缝的方式处理这些不同的故障点的方式。它节省了反复检查、测试和处理异常的时间，因此像阅读和写作这样简单的任务就像它们应该的那样简单！

## 分享这篇文章