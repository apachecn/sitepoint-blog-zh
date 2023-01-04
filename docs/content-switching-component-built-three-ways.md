# 一个内容切换组件以 3 种方式构建:jQuery、JS、CSS

> 原文：<https://www.sitepoint.com/content-switching-component-built-three-ways/>

不久前，我的一个朋友构建了一个 UI 组件，根据当前选择的`<select>`元素来改变页面的内容。她的代码工作正常，但由于对 JavaScript 不熟悉，她用 jQuery 构建了这个组件，并要求我帮助优化它。

虽然没有完全复制代码，但我创建了以下示例来展示她试图创建的 UI 组件的类型:

```
<div class="select-area">
  <select name="choose" id="choose" class="input-select">
    <option value="nul" selected>Make a Selection</option>
    <option value="opt1">Option 1</option>
    <option value="opt2">Option 2</option>
    <option value="opt3">Option 3</option>
  </select>
</div>

<section class="jqueryOptions opt1">
  <div class="content">
    <h2>Option 1 Content</h2>
    <p>
      ...
    </p>
  </div>
</section>

<section class="jqueryOptions opt2">
  <div class="content">
    <h2>Option 2 Content</h2>
    <p>
      ...
    </p>
  </div>
</section>

<section class="jqueryOptions opt3">
  <div class="content">
    <h2>Option 3 Content</h2>
    <p>
      ...
    </p>
  </div>
</section>
```

经过一点优化后，下面是我们最终用来切换所选内容块显示状态的 jQuery。

```
$(function() {
  $('.jqueryOptions').hide();

  $('#choose').change(function() {
    $('.jqueryOptions').slideUp();
    $('.jqueryOptions').removeClass('current-opt');
    $("." + $(this).val()).slideDown();
    $("." + $(this).val()).addClass('current-opt');
  });
});
```

## 这是怎么回事？

上面的 jQuery 函数查找所有具有“jqueryOptions”类的内容块，并在默认情况下隐藏它们。

然后，当用户更改`select`输入的选定选项(其 ID 为“choose”)时，该函数使用 jQuery 的`.slideUp()`方法关闭任何可能打开的内容块，然后使用`slideDown()`打开选定的选项。它通过获取所选选项的值(引用为`this`)并引用具有与“this”中的值相匹配的类名的元素来做到这一点。

所以:

```
<option value="opt1">Option 1</option>
```

匹配:

```
<section class="options opt1">
  ...
</section>
```

这里有一个演示:

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )用 jQuery 查看笔[内容切换组件。](http://codepen.io/SitePoint/pen/mGgDf/)