# 使用 jQuery 和 Web 存储 API 自动填写表单

> 原文：<https://www.sitepoint.com/auto-filling-forms-jquery-web-storage-api/>

在我几年前开发的一个项目中，有一个搜索表单，由一个主字段和许多其他字段组成，以优化搜索。在该项目中，用户通常需要多次执行相同的搜索，只需更改一两个字段。一次又一次地填写表单是一件痛苦的事情，所以我们决定帮助用户快速实现他们的目标。

在本文中，我将向您展示如何使用 [jQuery](http://jquery.com) 、 [jQuery.deserialize](https://github.com/kflorence/jquery-deserialize) 和 [Web 存储 API](https://www.w3.org/TR/webstorage/) 来重现同样的改进。

## 要求

为了改进我在介绍中提到的表单，我们决定显示一个以前执行的搜索列表，最多 20 个。使用 Web 存储 API 将搜索结果存储在浏览器中。如果您有一个登录系统，您可能希望修改演示，以便将搜索存储在数据库中。在我的情况下，这不是一个选项，因为没有登录系统。

列表中的每一项都由表示用户在主字段中写入的值的文本和显示字段名称以及写入或选择的值(对于复选框和单选按钮)的子列表组成。当用户单击这些搜索之一时，表单的字段会自动填充该搜索的值。这样，如果用户需要执行相同的研究，他/她除了点击提交按钮之外什么也不用做；否则，用户可以更改所需的字段，然后执行搜索。这个小小的改进为该项目的用户节省了大量时间，受到了极大的赞赏。

本文的最终结果如下所示，也可以作为 JSFiddle 获得[:](https://jsfiddle.net/56a5baLk/)

[https://jsfiddle.net/56a5baLk/embedded/](https://jsfiddle.net/56a5baLk/embedded/)

## 加价

第一步是创建要增强的表单。如果您将在您正在处理的项目中使用这种方法，您将拥有自己的特定字段，但是为了这里的示例，我将为每个字段创建一个包含不同类型的虚拟字段。例如，我将使用`search`、`text`、`email`、`checkbox`、`radio`和`date`类型。通过这种方式，您可以看到该方法如何处理不同的类型。

关于这个表单没有什么要说的了，所以下面是我们将使用的代码:

```
<form name="form" id="form">
   <label for="search">This is the main search field:</label>
   <input type="search" name="search" id="search" />
   <label for="text">This a text field:</label>
   <input type="text" name="text" id="text" />
   <label for="email">This an email field:</label>
   <input type="email" name="email" id="email" />
   <label>This a set of checkbox:</label>
   <label>
      Checkbox1:
      <input type="checkbox" name="checkbox[]" value="checkbox1" />
   </label>
   <label>
      Checkbox2:
      <input type="checkbox" name="checkbox[]" value="checkbox2" />
   </label>
   <label>This a set of radio buttons:</label>
   <label>
      Radio1:
      <input type="radio" name="radio" value="radio1" checked />
   </label>
   <label>
      Radio2:
      <input type="radio" name="radio" value="radio2" />
   </label>
   <label>
      Radio3:
      <input type="radio" name="radio" value="radio3" />
   </label>
   <label for="date">This a date field:</label>
   <input type="date" name="date" id="date" />

   <input type="reset" value="Reset" />
   <input type="submit" value="Submit" />
</form>
```

表单就绪后，我们还需要一个元素来显示之前的搜索。为此，我将在页面中添加以下有序列表:

```
<ol id="searches-list">
</ol>
```

就是这样！我们的演示不需要其他元素，至少不需要静态元素，我们将在后面的章节中看到。

## 风格

默认情况下，旧搜索列表将只显示主字段的值，而隐藏字段名/值的子列表。这很有用，尤其是当你的表单有很多字段时，因为整个列表很容易变长，长到用户需要滚动。像往常一样，我们也想考虑，由于可访问性问题，仅当用户悬停在某个元素上时才显示某些内容是一种糟糕的方法。因此，当主值获得焦点时，我们将显示子列表。这是通过以下 CSS 完成的:

```
#searches-list > li:hover dl,
#searches-list > li:focus dl
{
   display: block;
}

#searches-list dl
{
   margin: 0;
   display: none;
}
```

最后，我们希望给出一个视觉线索，表明列表可以做一些事情，因此我们也将光标改为指针:

```
#searches-list
{
   cursor: pointer;
}
```

既然我们已经完成了样式，是时候讨论推动改进的业务逻辑了。

## 商业逻辑

这个小演示将使用 [jQuery](http://jquery.com) 和 [jQuery.deserialize](https://github.com/kflorence/jquery-deserialize) ，因此您需要做的第一件事就是获取它们的副本并将其包含在页面中。您还需要一些关于 [Web 存储 API](https://www.w3.org/TR/webstorage/) 的知识，所以我鼓励您阅读文章[<cite>Web 存储 API</cite>](https://www.sitepoint.com/an-overview-of-the-web-storage-api/) 概述。

本节的第一步是检索页面的主要元素，即表单和搜索列表，并将其存储到一个变量中，因为我们会多次使用它们:

```
var $searchesList = $('#searches-list');
var $form = $('#form');
```

然后，我们需要验证用户是否已经存储了一些搜索。如果是这种情况，我们将把它们保存到一个变量中，否则我们将把变量初始化为一个空数组:

```
var searches = window.localStorage.getItem('searches');
searches = (searches === null) ? [] : JSON.parse(searches);
```

非常有想象力地，我将代码存储搜索的键命名为“搜索”。在第二个语句中，我需要使用`JSON.parse()`方法，因为 Web 存储 API 不允许存储复杂的数据。

此时，我们必须指示当用户执行新的搜索时，我们的页面应该做什么。因此，我们将为表单的`submit`事件添加一个事件处理程序。

<small>在我一开始展示的现场演示的代码中，处理程序的第一条语句停止表单以实际提交数据。这样做是因为演示没有后端，但在现实世界的项目中你不需要它。</small>

当用户填写并提交表单时，我们需要在发送请求之前将插入到本地存储中的值存储起来。为此，我们首先使用 jQuery 的`serialize()`方法序列化表单，然后将最后执行的搜索放在列表的顶部。在这个演示中，我们将避免多次存储相同的搜索，因此代码会搜索重复项并删除它们。最后，演示程序最多为每个用户存储 10 个搜索，但是您可以根据需要更改这个值。一旦我们对搜索执行了所有这些操作，我们需要将它们存储回本地存储中。

这是通过以下代码实现的:

```
$form.submit(function(event) {
   // Serializes the form
   var currentSearch = $(this).serialize();
   searches.unshift(currentSearch);
   // Removes the duplicates
   for(var i = 1; i < searches.length; i++) {
      if (searches[0] === searches[i]) {
         searches.splice(i, 1);
      }
   }

   // Stores only the last 10 searches
   if (i === searches.length && searches.length > 10) {
      searches.pop();
   }

   // Stores the new list into the local storage
   window.localStorage.setItem('searches', JSON.stringify(searches));
});
```

<small>在演示的最后一条语句中，我将调用一个名为`buildSearchesList()`的函数，我们稍后将讨论这个函数。这是必要的，因为演示没有后端，所以每次提交表单时都需要重新创建 HTML 列表。</small>

到目前为止，该页面能够存储执行的搜索，但我们需要向用户显示列表，以便如果他/她单击其中一项，表单会自动填充。对于这最后一部分，我们将创建一个名为`buildSearchesList()`的函数。在它里面，我们清空任何以前建立的列表，然后遍历旧的搜索。正如我提到的，列表中的每一项都将显示主字段的值作为助记名称，以及一个包含表单所有字段的子列表，只有当用户将鼠标悬停或聚焦在助记名称上时，才会显示该子列表。每当用户点击或按下列表项目上的`ENTER`或`SPACE`时，表单会使用所选的一组值自动填充。

基于此描述，该功能的框架如下:

```
function buildSearchesList(searches, $searchesList, $form) {
   $searchesList.empty();

   for (var i = 0; i < searches.length; i++) {
      // Other code goes here...
   }
}
```

在`for`中，我们必须将从本地存储中获取的列表中的每一项转换成 JSON 可解析的字符串，然后再将其转换成等价的对象。然后，我们遍历该对象的属性，创建包含字段名称和值的子列表。子列表是使用一个`dl`元素及其相关的`dt`和`dd`创建的。

实现这些步骤的代码如下:

```
var params = JSON.parse('{"' +
   decodeURIComponent(
      searches[i]
         .replace(/&/g, '","')
         .replace(/=/g, '":"')
         .replace(/\+/g, ' ')
   ) +
   '"}'
);

var text = '<dl>';
for (var key in params) {
   text += '<dt>' + key + ':</dt><dd> ' + params[key] + '</dd>';
}
text += '</dl>';
```

现在我们已经创建了先前搜索的列表项，我们需要将它添加到列表中，并指示如果用户在表单被自动填充之前单击或按下两个键中的一个。表单由 jQuery.deserialize 插件和对其`deserialize()`方法的调用自动填充。但是因为我们在一个循环中处理事件处理程序，所以我们必须将代码封装到一个生命中，以避免任何闭包问题。最后，每个列表项(`li`)必须有一个`tabindex="0"`属性，否则不能使用`TAB`键聚焦。下面列出了实现最后一部分的代码:

```
(function(searchData) {
   $searchesList.append(
      $('<li tabindex="0">')
         .text(params['search'])
         .on('click keypress', function(event) {
            if (
               event.type !== 'keypress' ||
               event.keyCode === 13 ||
               event.keyCode === 32
            ) {
               $form
                  .trigger('reset')
                  .deserialize(searchData);
            }
         })
         .append(text)
   );
})(searches[i]);
```

有了这最后一个片段，我们就结束了我们的演示。同样，本文的最终结果如下所示，也可以通过 JSFiddle 获得[:](https://jsfiddle.net/56a5baLk/)

[https://jsfiddle.net/56a5baLk/embedded/](https://jsfiddle.net/56a5baLk/embedded/)

## 结论

在这篇文章中，我讨论了一个简单的技术来增强用户的体验，以防您的项目有一个在典型会话中被多次填充的表单。除了 Web 存储 API 之外，通过使用 jQuery 和 jQuery.deserialize，我们还创建了一个页面，它能够存储一组给定的搜索，并在必要时恢复它们。我希望你喜欢这篇文章，并发现它很有用。

## 分享这篇文章