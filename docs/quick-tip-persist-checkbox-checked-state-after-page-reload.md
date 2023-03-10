# 快速提示:页面重新加载后保持复选框的选中状态

> 原文：<https://www.sitepoint.com/quick-tip-persist-checkbox-checked-state-after-page-reload/>

这个小技巧描述了如何让你的浏览器在页面刷新或者用户离开你的站点以后再回来时记住复选框的状态。

例如，如果您使用复选框来允许用户设置特定于站点的首选项，如在新窗口中打开外部链接或隐藏某些页面元素，则保持复选框的选中状态可能会很有用。

对于你们当中不耐烦的人来说，在文章的最后有一个技术的演示。

## 复选框标记

所以，我们首先需要一些复选框。以下是我之前做的一些:

```
<div id="checkbox-container">
  <div>
    <label for="option1">Option 1</label>
    <input type="checkbox" id="option1">
  </div>
  <div>
    <label for="option2">Option 2</label>
    <input type="checkbox" id="option2">
  </div>
  <div>
    <label for="option3">Option 3</label>
    <input type="checkbox" id="option3">
  </div>

  <button>Check All</button>
</div> 
```

你会注意到我已经包含了一个 *Check All* 按钮，允许用户通过一次点击来选择或取消选择所有的复选框。我们稍后会谈到这一点。

您可能还会注意到，我对每个框中的文本都使用了标签。不言而喻，[表单域无论如何都应该有标签](http://www.coolfields.co.uk/2011/04/accessible-forms-should-every-input-have-a-label/)，但是对于复选框来说，这尤其重要，因为它允许用户通过点击标签文本来选择/取消选择复选框。

最后，您将看到我在块级元素(在本例中是`<div>`元素)中对标签和复选框进行了分组，这样它们出现在彼此之下，并且更易于样式化。

## 应对变化

现在，让我们将一个事件处理程序绑定到复选框上，这样每当您单击它们时就会发生一些事情。我在本教程中使用了 [jQuery](http://jquery.com/) ，当然，这并不是绝对必要的。您可以通过 CDN 包含它，因此:

```
<script src="https://code.jquery.com/jquery-2.2.3.min.js"></script> 
```

现在是 JavaScript:

```
$("#checkbox-container :checkbox").on("change", function(){
  alert("The checkbox with the ID '" + this.id + "' changed");
}); 
```

这里我使用 jQuery 的伪类[:复选框选择器](https://api.jquery.com/checkbox-selector/)，前面是包含`<div>`元素的 ID(`checkbox-container`)。这允许我只锁定我感兴趣的复选框，而不是页面上的所有复选框。

## 保持复选框的选中状态

正如你可能知道的，HTTP 是一个无状态协议。这意味着它将每个请求视为独立的事务，与之前的任何请求都无关。因此，如果你选中一个复选框，然后刷新页面，浏览器没有办法记住复选框的先前状态，在上面的例子中，它将以未选中的形式呈现(尽管[一些浏览器会缓存值](http://stackoverflow.com/questions/299811/why-does-the-checkbox-stay-checked-when-reloading-the-page))。

为了避免这一点，我们将使用本地存储(它是 [Web 存储 API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API) 的一部分)在用户的计算机上存储复选框状态。它有很好的浏览器支持:

我可以使用名称值存储吗？caniuse.com 主要浏览器对名称值存储功能的支持数据。