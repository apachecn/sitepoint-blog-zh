# 更有用的表单的简单技巧

> 原文：<https://www.sitepoint.com/simple-tricks-usable-forms/>

Web 开发人员讨厌构建表单的任务，就像用户讨厌填写表单一样。这些都是网络的不幸事实，但是一些智能 JavaScript 和智能 CSS 可以在很大程度上改变这种情况。在本文中，我将介绍一些提高表单可用性的简单技巧，希望能启发您改进它们并创建自己的表单。

在介绍有趣的东西之前，我将从一些简单的技巧开始。如果材料对你来说太简单了，请随意跳过。

##### 重要的是小事

你肯定会遇到我们的第一个把戏；它被世界上最流行的搜索引擎谷歌使用。每当你打开[www.google.com](http://www.google.com)，你的光标会直接跳到搜索框，等待你输入你的查询。它发生得如此之快，您可能甚至没有想到它，但是，事实上，它在任何情况下都同样适用，只要页面的主要目的是填写表单。有许多不同的方法可以做到这一点，每种方法都假设您想要关注的表单元素有一个设置为“myfield”的 id 属性:

```
<body onload="document.getElementById('myfield').focus()">
```

这或多或少是谷歌使用的方法。它简短扼要。但是，它确实需要向您的 body 元素添加一个 onload，有些人会觉得这不雅观。

```
<script type="text/javascript"> 

window.onload = document.getElementById('myfield').focus; 

</script>
```

这可以添加到 HTML 文件中的任何一点，或者隐藏在外部脚本中。如果你要在一个页面上使用大量的 JavaScript 代码，将它们全部转移到一个外部脚本文件中是有意义的，这样可以将 JavaScript 代码从 HTML 中分离出来。这种方法的缺点是一次只能给`window.onload`事件分配一个东西。

```
<script type="text/javascript"> 

addEvent(window, 'load', function() { 

  document.getElementById('myfield').focus() 

}); 

function addEvent(obj, evType, fn){ 

  if (obj.addEventListener){ 

     obj.addEventListener(evType, fn, true); 

     return true; 

  } else if (obj.attachEvent){ 

     var r = obj.attachEvent("on"+evType, fn); 

     return r; 

  } else { 

     return false; 

  } 

} 

</script>
```

这使用了 Scott Andrew 的`addEvent`函数，我在上一篇文章的[中讨论过这个函数。如果您将代码保存在一个单独的文件中，这可能是最好的方法，因为它也允许将其他函数附加到 onload 事件。](https://www.sitepoint.com/blog/)

```
<script type="text/javascript"> 

document.getElementById('myfield').focus(); 

</script>
```

只有将上述内容放在 HTML 源代码中输入字段之后的某个位置时，它才会起作用。如果您使用的服务器端模板系统很难将代码直接添加到文档的顶部，例如，如果您使用服务器端 include 包含页面的顶部，这将非常有用。

以上四个选项适用于我将在本文中演示的大多数技巧。对于以后的技巧，我将只使用 onload 和 onclick 等内联属性来演示该方法，但是您应该知道有几种方法可以为这只特殊的猫换肤。

##### 标签

增强表单可用性的最快方法是添加标签，如果您还没有使用它们的话。自 1998 年以来,`<label>`元素就已经是 HTML 的一部分，但是许多开发人员仍然不知道它的存在。它允许您将描述表单域的文本与表单域本身进行逻辑关联。当用户单击标签时，浏览器会将焦点移动到相关的表单字段，或者切换单选按钮和复选按钮的状态。在添加标签之前，表单域必须设置 ID 属性。事实上，本文中的技巧几乎都要求在表单字段上设置 ID 属性，因为这提供了一种从 JavaScript 定位该字段的有用方法。

下面是一个最简单的标签实例:

```
<label for="username">Username:</label>  

<input type="text" name="username" id="username" size="10">
```

单击单词“用户名”将使光标聚焦在文本框中。这似乎不是一个特别有用的效果，但是它给了我们一个有用的钩子来设计样式和潜在地添加额外的 JavaScript 行为。它还极大地提高了辅助软件用户对表单的可访问性。

标签真正发挥作用的地方是复选框和单选按钮。这两个小部件都被一个很小的活动区域所困扰，有时被称为“热点”，你需要用鼠标点击它来使它们切换。添加标签会增加热点，以覆盖与小部件相关联的文本:

```
<input type="checkbox" name="accepted" id="accepted">  

<label for="accepted">I agree to the terms and conditions</label>
```

当然，如果人们不知道标签的存在，标签就没什么用了。增加标签可见性的一个简单而有效的技巧是使用 CSS 来改变光标:

```
<style type="text/css">  

label {  

  cursor: pointer;  

  cursor: hand;  

}  

</style>
```

为什么有两个游标声明？CSS 标准规定“指针”为“指示链接的指针”的值。可惜 IE 5 和 IE 5.5 for Windows 不理解这个值，用“手”来表示同一个意思。通过将指针放在第一位，行为不当的微软浏览器忽略它并使用 hand 值，而行为较好的浏览器采用指针并忽略 hand 值。

##### 视觉提示

在大型表单中，很容易忘记当前正在填写的表单域。下面是一个很好的帮助技巧:

```
<style type="text/css">  

input {  

  border: 2px solid #ccc;  

}  

input:focus {  

  border: 2px solid #000;  

}  

</style>
```

这导致所有输入字段都有一个 2 像素宽的灰色边框，而用户当前关注的输入字段会有一个黑色边框，使其与其他字段相区别。有一个警告:Windows 上的 IE 不支持`:focus`伪类！幸运的是，使用 JavaScript 可以复制这种效果:

```
<input type="text" name="myfield" id="myfield"   

       onfocus="this.style.border='2px solid #000'"  

       onblur="this.style.border='2px solid #ccc'">
```

这给 IE 带来的效果，是以大量额外打字为代价的。如果页面上有很多表单字段，那么这样做是有意义的，再次利用上面介绍的`addEvent`函数:

```
<script type="text/javascript">  

addEvent(window, 'load', function() {  

  var input, textarea;  

  var inputs = document.getElementsByTagName('input');  

  for (var i = 0; (input = inputs[i]); i++) {  

    addEvent(input, 'focus', oninputfocus);  

    addEvent(input, 'blur', oninputblur);  

  }  

  var textareas = document.getElementsByTagName('textarea');  

  for (var i = 0; (textarea = textareas[i]); i++) {  

    addEvent(textarea, 'focus', oninputfocus);  

    addEvent(textarea, 'blur', oninputblur);  

  }  

});  

function oninputfocus(e) {  

  /* Cookie-cutter code to find the source of the event */  

  if (typeof e == 'undefined') {  

    var e = window.event;  

  }  

  var source;  

  if (typeof e.target != 'undefined') {  

     source = e.target;  

  } else if (typeof e.srcElement != 'undefined') {  

     source = e.srcElement;  

  } else {  

    return;  

  }  

  /* End cookie-cutter code */  

  source.style.border='2px solid #000';  

}  

function oninputblur(e) {  

  /* Cookie-cutter code to find the source of the event */  

  if (typeof e == 'undefined') {  

    var e = window.event;  

  }  

  var source;  

  if (typeof e.target != 'undefined') {  

     source = e.target;  

  } else if (typeof e.srcElement != 'undefined') {  

     source = e.srcElement;  

  } else {  

    return;  

  }  

  /* End cookie-cutter code */  

  source.style.border='2px solid #ccc';  

}  

</script>
```

上面的千篇一律的代码处理了一些跨浏览器兼容性问题，在我以前的文章中讨论过。

##### 增强文本输入字段

最常见的表单字段是`<input type="text">`。我们已经看到了当页面加载时自动聚焦于此可以产生很好的增强效果。对于包含需要更改的默认值的字段，一个有用的技巧如下:

```
<input type="text" name="myfield" id="myfield" size="30"   

       value="This should be changed"   

       onfocus="this.select()">
```

当字段获得焦点时，它里面的文本将被立即选中；当用户开始输入他们自己的文本时，它会被改写。如果用户可能从小部件中复制和粘贴文本，这也是有用的，因为这使他们不必首先选择它。

对于用来创建有明显标题的内容的表单，有一个很好的技巧，例如，一封电子邮件或网站上的一篇文章:

```
<input type="text" name="title" id="title" size="30"   

       onkeyup="document.title = 'New item: ' + this.value">
```

这产生了类似于许多流行的电子邮件程序的效果，其中文档标题栏中的文本随着电子邮件主题的键入而改变。这在可能同时打开多个窗口的环境中特别有用，例如，在 Webmail 客户端中。

与此相关，有时一个表单字段的值可以通过查看另一个表单字段的值来猜测。一个典型的例子是内容管理系统，其中每个条目都有一个可读的标题和一个唯一的 URL。URL 可以默认匹配标题，但是删除了标点符号并将空格转换为下划线。下面是实现这一点的代码:

```
<input type="text" name="title" id="title" size="30"   

       onkeydown="document.getElementById('url').value =    

                this.value.replace(/[^a-z0-9 ]/ig,   

                '').replace(/ /g, '_')">   

<input type="text" name="url" id="url" size="30">
```

这里关键的一点是，用户仍然可以通过直接在字段中输入文本来忽略 URL 的猜测值。如果您只想在没有用户干预的情况下从标题创建 URL，那么最好在处理表单的服务器端代码中这样做。

##### 确认

客户端表单验证是 JavaScript 最常见的用途之一。在我们继续之前，我想指出，如果你正在构建一个服务器端应用程序，你应该*总是*检查数据在你的服务器端代码中是有效的，不管你是否使用了客户端验证。不这样做可能会让您的应用程序面临各种令人不快的安全问题——记住，恶意攻击者知道如何在他们的浏览器中禁用 JavaScript。这一点怎么强调都不为过。我们现在回到正常的预定节目…

验证是一个很大的话题，在网上的教程中已经被广泛讨论过。我不会老调重弹，而是将重点放在验证用户输入的更有用的方法上。为了向用户提供即时反馈，在每个表单域旁边显示一个图标来指示该域是否已正确完成，如何？这样的图标可以直接挂在我们前面添加的标签元素上，并通过使用 JavaScript 改变标签元素的 class 属性来进行更改。

下面是一个简单的必填表单字段示例，分为 CSS、JavaScript 和 HTML:

```
<label for="subject" class="required">Subject:</label>   

<input type="text" id="subject" name="subject" size="40"    

       onblur="checkRequired('subject');">
```

这很简单。label 元素以“required”类开始，直观地表示该字段是必填字段。JavaScript 函数`checkRequired('subject')`被称为 onblur，指的是焦点从字段移开的点。

```
<style type="text/css">   

label {   

  padding-left: 22px; /* To leave room for the icon */   

}   

label.required {   

  background-image: url(required.gif);   

  background-repeat: no-repeat;   

  background-position: 3px 0px;   

}   

label.problem {   

  background-image: url(caution.gif);   

  background-repeat: no-repeat;   

  background-position: 3px 0px;   

}   

label.completed {   

  background-image: url(complete.gif);   

  background-repeat: no-repeat;   

  background-position: 3px 0px;   

}   

</style>
```

CSS 给每个标签一个 22 像素的左填充。我们将使用的每个图标都是 15×15，这给了我们一点空间。定义了 required、problem 和 completed 等特殊类别，每个类别都有自己的背景图标，显示在表单文本左侧的填充中。

```
<script type="text/javascript">   

function getLabelForId(id) {   

  var label, labels = document.getElementsByTagName('label');   

  for (var i = 0; (label = labels[i]); i++) {   

    if (label.htmlFor == id) {   

      return label;   

    }   

  }   

  return false;   

}   

function checkRequired(id) {   

  var formfield = document.getElementById(id);   

  var label = getLabelForId(id);   

  if (formfield.value.length == 0) {   

    label.className = 'problem';   

  } else {   

    label.className = 'completed';   

  }   

}   

</script>
```

这里，我们定义了两个 JavaScript 函数:一个用于查找与特定 ID 关联的标签，另一个用于检查指定的表单字段中是否有内容，并相应地设置关联标签的类。这是最简单的验证情况；可以编写额外的函数来处理一些问题，比如检查电子邮件地址的格式是否有用。通过禁用 submit 按钮，直到所有的表单域都被正确完成，这种技术可以更进一步；但是，如果这样做了，那么最初在 JavaScript 中禁用提交按钮是非常重要的，以确保不支持 JavaScript 的浏览器仍然可以使用表单。

我将介绍的最后一个技巧与具有特定格式的数据有关。如果用户输入的数据不完全符合格式规则，有时可以在用户输入数据后重新格式化，而不是拒绝用户的输入。一个典型的例子是接受美国电话号码的表单域。美国电话号码，包括区号，正好是 10 位数。传统的显示方式是(785) 555-5555。使用 JavaScript，我们可以获取用户的输入，去掉所有非数字字符，如果只剩下 10 个数字，就将它们重新格式化，如下例所示:

```
<script type="text/javascript">   

function checkPhone() {   

  var phone = document.getElementById('phone');   

  var label = getLabelForId('phone');   

  var digits = phone.value.replace(/[^0-9]/ig, '');   

  if (!digits) {   

    return;   

  }   

  if (digits.length == 10) {   

    phone.value = '(' + digits.substring(0, 3) + ') ' +    

      digits.substring(3, 6) + '-' +    

      digits.substring(6, 10);   

  } else {   

    phone.value = digits;   

  }   

}   

</script>   

<label for="phone">Phone Number:</label>   

<input type="text" id="phone" name="phone" size="20"    

       onblur="handlePhone();">
```

这种技术甚至可以更进一步，允许以多种方式输入结构化数据，如日期，并将任何识别的值转换为标准格式。我的 [better date 输入脚本](http://simon.incutio.com/code/js/date-parser/)就是这么做的，我推荐你[查看源代码](http://simon.incutio.com/code/js/date-parser/dateparse.js)看看它到底是如何工作的。

为了更好地说明本文中的观点，我把这个简单的表单放在一起[，演示了一些介绍的技术。我希望我已经启发了你去思考使用 JavaScript 和 CSS 来提高表单可用性的新方法。](https://www.sitepoint.com/examples/simpletricks/form-demo.html)

## 分享这篇文章