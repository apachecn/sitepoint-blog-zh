# jQuery 1.6 的新特性

> 原文：<https://www.sitepoint.com/whats-new-in-jquery-16/>

网络上最受欢迎的 JavaScript 库已经更新。jQuery 1.6 现在可以从以下网站下载:

[https://code . jquery . com/jquery-1.6 . js](https://code.jquery.com/jquery-1.6.js)
[https://code . jquery . com/jquery-1.6min . js](https://code.jquery.com/jquery-1.6.min.js)

jQuery 团队试图保持与旧版本兼容。然而，虽然大多数人不会遇到问题，但没有什么可以替代严格的测试。除了众多的错误修复和速度改进，最新版本中还有几个重大变化…

## 变化:DOM 属性和特性的单独处理

在大多数情况下，JavaScript 开发人员以相同的方式处理 DOM 节点属性。以前版本的 jQuery 没有进行任何区分，但是这种方法存在一些问题。考虑:

```
 <input type="checkbox" checked /> 
```

在这种情况下，DOM。checked 属性设置为 true，但属性值为空字符串。在以前版本的 jQuery 中，。attr("checked ")将返回 true —它现在返回" "。新的[。道具()](http://api.jquery.com/prop/)和[。removeProp()](http://api.jquery.com/removeProp/) 方法可以用来相应地修改或删除一个 DOM 属性。

## 更改:数据属性大小写

[。data()](http://api.jquery.com/data/) 方法自动导入 HTML5 数据属性，例如

```
 <div data-day-now="Monday" /> 
```

在 jQuery 1.5 中，这会产生一个数据对象{ day-now: "Monday" }。1.6 版遵循 W3C HTML5 规范，设置{ day now:“Monday”}。

## 新:焦点选择器

现在可以选择具有焦点的元素，例如

```
 $("input:focus").addClass("focused"); 
```

请注意，如果您正在搜索当前有焦点的元素，`$(document.activeElement)`会更快更有效。

## NEW: jQuery.holdReady( hold)

[$。holdReady()](http://api.jquery.com/jQuery.holdReady/) 方法延迟 jQuery 的就绪事件。这可用于在就绪事件被激发之前动态加载脚本，例如

```
 $.holdReady(true);
$.getScript("anotherScript.js", function() {
     $.holdReady(false);
	 // ready event can now fire
}); 
```

## 改进:相对 CSS

CSS 属性现在可以使用相对值来修改，例如

```
 // move 10px to the right
$("#item").css("left", "+=10px"); 
```

## 改进:jQuery.map()

现在可以[映射](http://api.jquery.com/jQuery.map/)对象的属性以及数组元素，例如

```
 var obj = { p1: 1, p2: 2, p3: 3 };
jQuery.map( obj, function( val ) { ... }); 
```

## 改进:find()，closest()和 is()

遍历和定位 DOM 树中的节点现在可以与元素、选择器字符串或 jQuery 对象相匹配。

## 在 jQuery 1.7 上…

jQuery 团队现在正在接受 1.7 版本的建议。如果你渴望一个新的或改进的特性，请在 [jQuery 1.7 路线图提议表](https://spreadsheets.google.com/viewform?hl=en&authkey=CPmgicsO&formkey=dG0yTEs2ZTFWQUhDRUp5dzRyc3NwV2c6MA#gid=0)上留下你的评论。

## 分享这篇文章