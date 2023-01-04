# 五个在 JavaScript 中非常有用的 PHP 函数

> 原文：<https://www.sitepoint.com/five-php-functions-id-love-to-see-in-javascript/>

在我的第一篇文章中，我断言，虽然 JavaScript 核心继续通过新方法得到改进，但它仍然缺少一些有用的方法。作为一名 PHP 出身的人，我最欣赏的一点是它令人惊叹的原生库。这篇文章将讨论我希望在 JavaScript 中看到的五个 PHP 函数。


请注意，本文不会断言 JavaScript 比 PHP 差/好。相反，这是一个讨论一些功能并了解如何实现它们的机会。此外，请记住，这些不是“必须具备”的功能，而是“最好具备”的功能。JavaScript 仍然是一门伟大的语言，尽管它缺少以下实用工具。对于这里描述的所有方法，我将把它们添加到适当对象的原型中，这样所有的实例将共享相同的方法。

## 唯一()

处理数组时最常见的任务之一是提取唯一值。实现这个目标有不同的方法，但我将展示一种尽可能通用的方法。这意味着，如果数组包含原始值或对象，使用我在前面引用的文章中展示的`inArray()`方法的方法将会工作。

```
Array.prototype.unique = function (sort, sortingFunction) {
  var array = [];
  for (var i = 0; i < this.length; i++) {
    if (array.inArray(this[i]) === false)
      array.push(this[i]);
  }

  if (sort === true) {
    if (typeof sortingFunction === 'function')
      array.sort(sortingFunction);
    else
      array.sort();
  }

  return array;
};
```

图示的方法有两个参数，第一个是布尔值，指定返回的数组是否必须排序，第二个是对排序函数的引用。返回的数组被初始化为一个空数组，然后对于每个数组的元素，函数测试它是否已经被添加。如果元素不在数组中，它将被添加。在推送所有必要的元素之后，该方法检查用户是否想要对数组进行排序，以及是否给定了排序函数。如果是这样，数组将使用给定的函数进行排序，否则`sort()`方法将把项目转换成字符串，并按字典顺序进行比较。现在你已经知道代码是做什么的了，因为这是最有趣的方法，我将向你展示三个不同的例子。

### 示例 1–使用整数

在这个例子中，我将使用一个只包含数字的简单数组。在下面两行中，我将打印数组的唯一值和排序后的唯一值。

```
var array = [1, 2, 9, 6, 2, 1, 9, 3];
console.log(array.unique()); // print [1, 2, 9, 6, 3]
console.log(array.unique(true)); // print [1, 2, 3, 6, 9]
```

### 示例 2–使用字符串

此示例使用字符串数组。这一次，对`unique()`方法的第二次调用使用了一个排序函数，它将帮助我们以相反的字母顺序返回数组。

```
var array = ["hi", "hi", "hi", "aurelio", "hello", "aurelio", "de rosa", "jspro.com"];
console.log(array.unique());
// print ["hi", "aurelio", "hello", "de rosa", "jspro.com"]
console.log(array.unique(true, function(a, b){ return -1 * a.localeCompare(b); }));
// print ["jspro.com", "hi", "hello", "de rosa", "aurelio"]
```

### 示例 3–使用对象

第三个例子使用一个对象数组来演示`unique()`有多强大。

```
var array = [{x: 1, y: 2}, {x: 12, y: 3}, {x: 1, y: 2}, {x: 2, y: 3}, {x: 2, y: 4}, {x: 20, y: 23}, {x: 2, y: 3}];
console.log(array.unique());
// print [{x: 1, y: 2}, {x: 12, y: 3}, {x: 2, y: 3}, {x: 2, y: 3}, {x: 20, y: 23}]
```

## htmlSpecialChars()

在 PHP 中，你经常需要打印精化的结果，但是你必须小心你实际注入到 HTML 代码中的内容。事实上，如果没有一些字符的正确转义，您可能会允许 XSS 攻击。这个问题也会影响 JavaScript 代码。在 PHP 中，为了保护自己，您可以使用`htmlspecialchars()`函数，该函数将像`<`和`>`这样的特殊字符转换成像`&lt;`和`&gt;`这样的等价编码。这使您可以显示字符串，而不用担心浏览器会将其解释为 HTML。不幸的是，JavaScript 没有等价的本机函数，因此，我们将创建它。

我们将使用 JavaScript native `replace()`方法来处理这个问题。我们要做的是搜索有问题的字符，并用编码的等价实体替换它们。因为我们想要改变所有的出现，我们将使用一个正则表达式和`g`标志。

```
String.prototype.htmlSpecialChars = function(){
  return this
         .replace(/&/g, '&amp;')
         .replace(/</g, '&lt;')
         .replace(/>/g, '&gt;')
         .replace(/"/g, '&quot;');
};
```

让我们看一个使用我们的`htmlSpecialChars()`方法的演示。

```
var string = 'Me, myself & I like to "code" <html>';
console.log(string.htmlSpecialChars());
// print Me, myself &amp; I like to &quot;code&quot; &lt;html&gt;
```

## htmlSpecialCharsDecode()

在上一节中，我们看到了`htmlSpecialChars()`方法。有时，您希望运行相反的过程，将编码字符串转换成普通字符串。为了实现这个目标，我将向您展示 PHP 的`htmlspecialchars_decode()`函数的等价物。下面的和前面的很像，所以我就不做进一步讨论了。它只是交换两个`replace()`参数，将实体转换成等价的字符。

```
String.prototype.htmlSpecialCharsDecode = function(){
  return this
         .replace(/&amp;/g, '&')
         .replace(/&lt;/g, '<')
         .replace(/&gt;/g, '>')
         .replace(/&quot;/g, '"');
};
```

让我们看一个使用我们的`htmlSpecialCharsDecode()`方法的演示。

```
var string = 'Me, myself & I like to "code" <html>';
console.log(string);
// print Me, myself & I like to "code" <html>
string = string.htmlSpecialChars();
console.log(string);
// print Me, myself &amp; I like to &quot;code&quot; &lt;html&gt;
string = string.htmlSpecialCharsDecode();
console.log(string);
// print Me, myself & I like to "code" <html>
```

## ucwords()

网络上充斥着每个月都会发布大量文章的博客。许多博客会将文章标题中的每个单词都大写。假设您想使用 JavaScript 来应用这种转换。可惜 JavaScript 没有 PHP 的`ucwords()`那样的功能。但是，您可以轻松地自己创建它。我们正在开发的方法的关键点是提取并处理字符串中的每个单词。为了实现这个目标，我们将依赖 JavaScript `split()`方法和一个简单的正则表达式。正如你可能猜到的，我们将在空格(不仅仅是空白)上拆分字符串，将提取的单词大写，然后再将它们连接起来。我们的`ucwords()`方法的代码如下所示。

```
String.prototype.ucwords = function() {
  var words = this.split(/(s+)/);
  for(var i = 0; i < words.length; i++)
    words[i] = words[i][0].toUpperCase() + words[i].substr(1, words[i].length);

  return words.join('');
};
```

正如你从阅读代码中看到的，在`for`循环中，我们将使用`toUpperCase()`方法来大写字符串的第一个字符，然后连接其余的字符。之后，我们将通过重新分配新的字符串到它的位置来替换旧的字符串。现在您已经知道了该方法的工作原理，让我们来看一个例子。

```
var string = 'my name is aurelio de rosa';
console.log(string.ucwords());
// print My Name Is Aurelio De Rosa
```

## ucfirst()

从`ucwords()`的代码中，我们可以提炼出另一个类似 PHP 函数的思路——`ucfirst()`。后者使字符串的第一个字符大写。与`ucwords()`实现不同的是，我们不需要循环来迭代单词，但是我们可以拆分，只转换第一个单词，然后连接。`ucfirst()`的来源如下图。

```
String.prototype.ucfirst = function() {
  var words = this.split(/(s+)/);
  if (words[0] != null)
     words[0] = words[0][0].toUpperCase() + words[0].substr(1, words[0].length);

  return words.join('');
};
```

下面举例说明了该函数的用法。

```
var string = 'my name is aurelio de rosa';
console.log(string.ucfirst());
// print My name is aurelio de rosa
```

## 结论

尽管 JavaScript 的核心在每个版本中都在不断发展，但还是缺少了一些功能。但是，您可以在自己选择的框架中找到很多，比如 jQuery。如果您更喜欢使用原始 JavaScript，或者只需要很少的函数，本文向您展示了编写自己的实现是多么容易。最后，我想强调的是，我描述的所有方法都被添加到了它们各自类型的原型中——`unique()`到`Array`原型，以及`htmlSpecialChars()`、`htmlSpecialCharsDecode()`、`ucwords()`和`ucfirst()`到`String`原型。这允许该类型的所有变量共享相同的代码。

## 分享这篇文章