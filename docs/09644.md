# jQuery 绑定到创建的元素

> 原文：<https://www.sitepoint.com/jquery-binding-created-elements/>

澄清一些关于向动态注入 DOM 的元素添加事件的事情(即，当页面被加载时它们并不存在)。

## 最佳实践方法

*   使用**。Bind()** 和**。住()**而不是**。点击()**。
*   使用**。Bind()** 用于静态 DOM 元素(页面加载的元素)。
*   使用**。Live()** 用于动态 DOM 元素(通过 JavaScript 更改/插入的元素)。

## 例子

在本例中，它是一个有序列表，在您将 JSON 文件加载到工具中后就会出现。然后鼠标悬停事件被应用到 DOM 中的每个组件。

```
/*mouse IN hover to show path of node*/
$('#div li').live('mouseenter', function () {
    //do something
});

/*click event for hyperlink*/
$('#div a').bind('click', function (e) {
	//don't follow the hyperlink href
	e.preventDefault();
    //do something else
	myFunction();
});
```

## 其他实时选项

有一个实时查询插件可以为你处理这些，只需要一个声明。实时查询插件:[http://brandonaaron.net/code/livequery/demos](http://brandonaaron.net/code/livequery/demos)当我有机会检查并测试它的时候，我会写更多关于实时查询插件的内容。

**参见:** [绑定到具有相同 id 的元素](http://www.jquery4u.com/dom-modification/jquery-bind-elements-id/)

## 分享这篇文章