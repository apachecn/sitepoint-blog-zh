# 这是 Safari bug 吗？

> 原文：<https://www.sitepoint.com/is-this-a-safari-bug/>

在对我们即将出版的书<cite>JavaScript 选集</cite>进行最后一次编辑时，我发现了 Safari 中一个以前没有记录的错误:

```
function preload(url) {
  var img = new Image();
  img.onload = function() {
    alert(this); // What is this?
  };
  img.src = url;
}
preload('chewbacca.jpg');
```

在 Firefox、Internet Explorer 6 和 Opera 8.5 等表现良好的浏览器中，上述脚本加载图像，然后显示“[object HTMLImageElement]”的某种变体，这表明`this`指的是刚刚触发了`load`事件的图像对象。

然而，在 Safari 1.3 和 2.0 中，`alert`显示“[对象窗口]”，因为`this`指的是脚本在其中运行的`window`对象。

淘气的野生动物园！我问的时候谷歌好像还不知道这个 bug。有人见过这种行为吗？

在这个 bug 被修复之前，你应该使用一个**闭包**从它的`onload`事件处理程序中引用一个图像对象。也就是说，引用处理函数封闭范围内的变量。

```
function preload(url) {
  var img = new Image();
  img.onload = function() {
    alert(img);
  };
  img.src = url;
}
preload('chewbacca.jpg');
```

## 分享这篇文章