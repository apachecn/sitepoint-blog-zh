# 另一篇关于客户端表单验证的文章

> 原文：<https://www.sitepoint.com/client-side-form-validation/>

使用 JavaScript 的客户端验证并不新鲜，对吗？但是使用 HTML DOM 并使用专门的属性来扩展输入字段可能会有问题。请继续阅读！

我假设您已经阅读了大量关于客户端和服务器端表单验证的文章，但是这篇文章在这个主题上有了新的变化。我在这里要展示的是如何将一些面向对象的编程整合到表单验证中。那么有什么问题呢？这个技巧适用于支持 JavaScript 和 W3C 的 HTML DOM 的浏览器，比如 Internet Explorer 6、Netscape 6 和 Opera 6，以及某种程度上更早的版本。虽然我没有测试过 Konqueror，但根据规格，它应该工作得很好。

公平的警告:要阅读本文，您需要相对精通 JavaScript。

##### 标签和对象

每个表单都有许多输入标签，这些标签通常具有名称和值属性。文本框也可能有 size 和 maxsize。在本文中，我添加了一些您通常不会发现的属性:`pattern`和`errorMsg`。这些属性是非标准的，但是不会影响浏览器。对于面向对象编程的爱好者来说，这些属性就像一个类的成员，在这种情况下，这个类就是输入标签。

一个普通的输入标签:

```
<input type="text" name="firstname" size="20" maxsize="20" />
```

修改后的输入标签:

```
<input type="text" name="firstname" size="20" maxsize="20"  

 pattern="[^A-Za-z]+" errorMsg="Invalid charcter found in firstname" />
```

Java 中的等效类:

```
public class Input { 

 String type = "text"; 

 String name = "firstname"; 

 String size = "20"; 

 String maxsize = "20";  

 String pattern = "[^A-Za-z]+"; 

 String errorMsg = "Invalid character found in firstname"; 

}
```

通常，您需要为每个想要验证的输入字段编写函数。但是让每个输入标签“知道”它的匹配模式和错误消息，使得验证代码的编写更加简单。事实上，在这样的设置中可以使用一个通用的解决方案，它可以用于任何可以用正则表达式(简称 RegExps)描述的值。关于正则表达式的链接，请参见本文末尾的参考资料。

##### 文档对象模型

大多数属性在 DHTML 中被表示为对象，但是这些添加的属性不是。因此，为了能够获得这些值，我们需要使用 DOM(有关 DOM 的链接，请参见最后的参考资料列表)。和 DHTML 一样，完整的 HTML 页面被称为`document`。文档可用的一种方法是`getElementsByTagName(String)`。此方法用于查找所有输入标签:

```
var elements = document.getElementsByTagName('input');
```

如果`elements`是一个普通的 JavaScript 对象，人们可以猜测它将是一个保存所有输入标签的数组。但在 DOM 中没有:这里是一个 `NodeList`。与元素由`myarray[i]`到达的数组不同，`NodeList` s 有一个`items`集合，(例如`elements.item(i)`)。

然后，我们可以使用普通的 for 循环迭代所有元素:

```
for (var i = 0; i < elements.length; i++) {
```

但是现在该是模式属性的时候了:

```
 var pattern = elements.item(i).getAttribute('pattern');
```

我们需要获得这个输入字段的模式，以便能够验证它。在本文中，只有强制元素具有模式属性(验证总是使用 RegExps 来完成)。

因此，我们从输入字段中获取值，并检查是否有任何字符与模式不匹配。换句话说，我们搜索冒犯的字符。

```
 var value = elements.item(i).value; 

  var offendingChar = value.match(pattern);
```

如果我们发现任何非法值，我们会为用户创建一条错误消息:

```
 str += elements.item(i).getAttribute('errorMsg') + "n" + 

         "Found this illegal value: '" + offendingChar + "' n";
```

为了给用户一点额外的帮助，让我们更改包含无效输入的字段的背景颜色(注意，这在 Opera 中不起作用，但也没有任何害处)。

```
 elements.item(i).style.background ="red"; 
```

细心的读者可能已经注意到，`elements.item[i].value`中的`value`被表示为一个对象。但是`elements.item(i).getAttribute('value')`有可能到达吗？不，那只是显示标签中的默认值(如果有的话)，而不是用户输入的值。

##### 助手功能

有两个小函数我还没有提到。首先，我们需要确保浏览器能够处理这个脚本:

```
function DOMCheck() {  

  if(!document.getElementsByTagName('html')) {  

    alert("Sorry! Your browser does not support the W3C HTML DOM!");  

  }  

}
```

这只是检查是否有一个`html`元素。请注意，如果省略了`<html>`标记，Internet Explorer 仍然会将页面呈现为 HTML，但是脚本的返回值将是零，因此会给出不正确的错误消息。因此，请务必在你的文档中包含`<html>`标签。

其次，当用户在输入标签中输入新值时，我们需要清除背景颜色:

```
function changeColor(th) {  

  //'resets' the background-color to white  

  th.style.background = "white";  

}
```

请注意，这在 Opera 中不起作用。使用事件处理程序`'onfocus'`从输入标签调用该方法。

##### 源码表

我们的验证函数的完整清单如下所示:

```
function validate() {  

  var str = "";  

  var elements = document.getElementsByTagName('input');  

  // loop through all input elements in form  

  for(var i = 0; i < elements.length; i++) {  

    // check if element is mandatory; ie has a pattern   

    var pattern = elements.item(i).getAttribute('pattern');  

    if (pattern != null) {  

      var value = elements.item(i).value;  

      // validate the value of this element, using its defined pattern  

      var offendingChar = value.match(pattern);  

      // if an invalid character is found or the element was left emtpy  

      if(offendingChar != null || value.length == 0) {  

        // add up all error messages  

        str += elements.item(i).getAttribute('errorMsg') + "n" +  

               "Found this illegal value: '" + offendingChar + "' n";  

        // notify user by changing background color, in this case to red  

        elements.item(i).style.background = "red";   

      }  

    }  

  }   

  if (str != "") {  

    // do not submit the form  

    alert("ERROR ALERT!!n" +str);   

    return false;  

  } else {  

    // form values are valid; submit  

    return true;  

  }  

}
```

##### 进一步扩展

这种添加属性的想法可以进一步扩展为最小值和最大值等属性。假设 RegExp 还不够，我们需要进一步限定一个给定值:

```
Age: <input type="text" name="age" maxlength="2" size="2"   

      min_reqs="18" max_reqs="30" errorMsg="Age must be 18-30 years" />
```

仍然使用相同的想法，通过为这个输入元素添加一些逻辑(在验证函数内),通用验证代码可以用于所有使用最小/最大值的输入标签。

```
 var min_reqs = elements.item(i).getAttribute('min_reqs');  

  var max_reqs = elements.item(i).getAttribute('max_reqs');  

  if (min_reqs != null && max_reqs != null) {  

    var value = elements.item(i).value;  

    if (value < min_reqs || value > max_reqs) {  

      str += elements.item(i).getAttribute('errorMsg') + 'n';  

    }  

  }
```

##### 结论

这是该走的路吗？也许还没有，但在不久的将来，当所有或大多数用户使用最新的浏览器时。当然，为浏览器提供统一的对象模型(DOM)还有其他好处，但是这个小技巧会让 Web 开发变得更加容易。

##### 资源

*   Internet Explorer 6，第一部分:DOM 标准支持
*   从 Mozilla 用 JavaScript 和 DOM 接口遍历 HTML 表
*   [Opera 4 支持的 Web 规范](http://www.opera.com/opera4/specs.html#css)
*   斯蒂芬·拉姆齐的正则表达式
*   [模式匹配和正则表达式](http://www.webreference.com/js/column5/)在 Webreference
*   [浏览器统计](http://www.cen.uiuc.edu/bstats/latest.html)

## 分享这篇文章