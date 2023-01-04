# JavaScript 中的正则表达式

> 原文：<https://www.sitepoint.com/expressions-javascript/>

本教程将解释正则表达式是如何在 JavaScript 中使用的，并用一两个实例来帮助概念的融合。在这篇文章结束时，你可能仍然只是一个凡人，但是你一定能够用你新获得的文本杂耍技巧在聚会上给人留下深刻印象！

## 在 JavaScript 中使用正则表达式

一旦你理解了正则表达式的基础知识，JavaScript 中的正则表达式就变得简单了。您可以用 JavaScript 创建正则表达式，如下所示:

```
const myRE = /regexp/; 
```

其中 regexp 是正则表达式代码，如上所述。例如，下面创建了一个检测字符串“JavaScript”的正则表达式:

```
const myRE = /JavaScript/; 
```

同样，下面的示例匹配" banana "、" nababa "、" baba "、" nana "、" ba "、" na "等。

```
const myRE = /^(ba|na)+$/; 
```

默认情况下，JavaScript 正则表达式区分大小写，并且只搜索任何给定字符串中的第一个匹配项。通过在第二个`/`之后添加`g`(对于**全局**)和`i`(对于**忽略大小写**)修饰符，可以分别对字符串和忽略大小写中的所有匹配进行正则表达式搜索。下面是一些正则表达式的例子。对于每一个，我都指出了它们将匹配字符串`"test1 Test2 TEST3"`的哪一部分:

| 正则表达式 | 匹配项: |
| /Test[0-9]+/ | 仅“测试 2” |
| /Test[0-9]+/i | 仅“测试 1” |
| /Test[0-9]+/gi | “测试 1”、“测试 2”和“测试 3” |

使用正则表达式很容易。每个包含文本字符串的 JavaScript 变量都支持三种使用正则表达式的方法(或者函数，如果你不习惯面向对象的术语的话):`match()`、`replace()`和`search()`。

### 匹配()

`match()`将一个正则表达式作为参数，并返回在所考虑的字符串中找到的所有匹配字符串的数组。如果没有发现匹配，那么`match()`返回假。回到我们最初的例子，假设我们想要一个函数，它可以检查用户输入的作为他或她的电话号码的字符串的形式是(XXX) XXX-XXXX。下面的代码可以解决这个问题:

```
function checkPhoneNumber(phoneNo) {
  const phoneRE = /^\(\d\d\d\) \d\d\d-\d\d\d\d$/; 
  if (phoneNo.match(phoneRE)) {
    return true; 
  } else {
    alert( "The phone number entered is invalid!" );
    return false;
  }
} 
```

首先，这个函数定义了一个正则表达式。我们来分解一下，了解一下它的工作原理。正则表达式以`^`开头，表示任何匹配都必须从字符串的开头开始。接下来是`\(`，它将刚好匹配左括号。我们在字符前加了一个反斜杠，以消除它在正则表达式语法中的特殊含义(标记一组匹配选项的开始)。如前所述，`\d`是匹配任何数字的特殊代码；因此，`\d\d\d`匹配任意三个数字。我们可以写`[0-9][0-9][0-9]`来达到同样的效果，但是这个更短。模式的其余部分应该是不言自明的。`\)`匹配右括号，空格匹配电话号码中必须留的空格，然后`\d\d\d-\d\d\d\d`匹配三个数字，后面是破折号，再后面是四个数字。最后，`$`表示任何匹配都必须在字符串末尾结束。

顺便提一下，我们可以通过使用上面没有提到的另一种快捷方式，将这个正则表达式缩短为下面的形式。如果你能明白这是怎么回事，你就是天才！

```
const phoneRE = /^\(\d{3}\) \d{3}-\d{4}$/; 
```

然后我们的函数检查`phoneNo.match(phoneRE)`的计算结果是`true`还是`false`。换句话说，它检查包含在`phoneNo`中的字符串是否匹配我们的正则表达式(从而返回一个数组，在 JavaScript 中该数组的值为`true`)。如果检测到匹配，我们的函数返回`true`来证明该字符串确实是一个电话号码。如果没有，则显示一条警告该问题的消息，并且函数返回`false`。

这种类型的函数最常见的用途是在允许提交表单之前验证用户输入。通过在表单的`onSubmit`事件处理程序中调用我们的函数，如果输入的信息格式不正确，我们可以阻止表单被提交。这里有一个简单的例子来演示我们的`checkPhoneNumber()`函数的用法:

```
<form action="...">
  <label>Enter phone number (e.g. (123) 456-7890): 
    <input type="text" name="phone">
  </label>
  <input type="submit">
</form>

<script> let form = document.querySelector('form');
  form.addEventListener('submit', function() {
    return checkPhoneNumber(this.phone.value);
  }); </script> 
```

除非输入电话号码，否则用户将无法提交此表单。任何这样做的尝试都会产生由我们的`checkPhoneNumber()`函数生成的错误消息。

参见 [CodePen](https://codepen.io) 上 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )的 Pen [match()示例](https://codepen.io/SitePoint/pen/QEwGoq/)。