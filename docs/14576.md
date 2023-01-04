# 在匿名函数中维护状态

> 原文：<https://www.sitepoint.com/maintaining-state-within-anonymous-functions/>

周末，我在我的个人博客上用 JavaScript 玩了一会儿，把我在这里和 SitePoint 上的文章中提到的一些主题放在一起。挑战在于提供一种链接到博客条目中任何段落的方式。我想到的解决方案最终使用了一个不显眼的 JavaScript 脚本、一个 bookmarklet 和一些 CSS。您可以阅读条目中所有血淋淋的细节，但我想在这里进一步剖析 bookmarklet。

这个 bookmarklet(标题为“添加 plink IDs”)的目的是找到当前页面上所有似乎包含 HTML 的文本区域，然后将 ID 属性添加到文本区域中还没有 ID 属性的任何段落标签。例如，下面的 HTML:

```

 <p>This is a paragraph.</p>

<p>This is another paragraph.</p> 

```

会变成:

```
 <p id="p-0">This is a paragraph.</p>

<p id="p-1">This is another paragraph.</p> 

```

以下是我使用的完整 bookmarklet，为了可读性，缩进了一些:

```

 javascript:(function() {
  var tas = document.getElementsByTagName('textarea');
  for (var i = 0; i < tas.length; i++) {
    var ta = tas[ i ];
    var text = ta.value.replace('<p>', function() {
      if (typeof arguments.callee.counter == 'undefined') {
        arguments.callee.counter = 0;
      }
      return '<p id="p-'+arguments.callee.counter++ +'">';
    });
    ta.value = text;
  }
})(); 
```

它被包装在一个匿名函数调用中，这是我在关于 bookmarklet 的文章中描述的一种技术。它基本上非常简单，但是有趣的是执行实际工作的替换调用。下面是有问题的代码:

```
 var text = ta.value.replace('<p>', function() {
  if (typeof arguments.callee.counter == 'undefined') {
    arguments.callee.counter = 0;
  }
  return '</p><p id="p-'+arguments.callee.counter++ +'">';
}); 

```

这里有两个窍门。首先，JavaScript 的字符串替换方法通常有两个参数:要查找的字符串(或正则表达式)和要替换的字符串。然而，代替替换字符串，可以提供一个函数，该函数将在每次替换时执行一次。这里我使用了匿名函数，因为它更简洁。

第二个技巧是 arguments.callee.counter 位。“argument”对象是一个内置的 JavaScript 对象，只在函数内部可用。它表示传递给函数的参数，通常就像 JavaScript 数组一样。但是，它还提供了一个“被调用者”属性，该属性引用实际的函数对象本身(在 JavaScript 中，甚至函数也是对象)。这个特性只在匿名函数中有用，在匿名函数中没有函数名来引用函数。

因为 JavaScript 函数是对象，所以它们可以有属性。匿名函数开始处的 if 语句检查函数的 callee 属性是否已经定义:如果还没有，它被初始化为 0。然后在 return 语句中，对该属性调用++ (postincrement)操作符——它返回该属性的当前值，但增加它，以便下一次它会更高。

最终结果是，对于在 textarea 中找到的每个段落标记，都会调用一次匿名函数，每次都返回一个带有新 ID 属性的 paragrah 标记。“counter”属性仅用于维护函数调用之间的状态。事实上，使用全局变量同样可以实现这一点，但是将属性放在使用它的函数“内部”会更干净，这样可以避免不必要的命名空间污染。

## 分享这篇文章