# 如何删除 IE 中的文本区滚动条

> 原文：<https://www.sitepoint.com/ie-remove-textarea-scrollbars/>

这可能是我发布过的最快的 CSS 技巧，但是谁会想要在周五看复杂的教程呢！当你使用表单时，Internet Explorer 总是显示滚动条，即使是在不必要的时候…

![IE textarea scrollbar](img/92c0107f4086656c0d17ddaca551245e.png)

Firefox、Chrome、Safari 或 Opera 中不会出现滚动条。这毫无意义，也有点难看——尤其是当`textarea`被标准的`input`盒子包围的时候。这从来没有真正困扰过我:我只是假设这就是 IE 所做的，并没有进一步质疑它。

现在，解决方案似乎非常明显:

```
 textarea
{
	overflow: auto;
} 
```

就这么简单。只有当`textarea`中的内容超出可用空间时，才会出现滚动条。它在 IE6、7、8 — [**查看演示**](https://blogs.sitepointstatic.com/examples/tech/textarea-overflow/index.html) 中有效。

如果你已经这样做了很多年，请接受我的道歉，但这是那些有用的 CSS 小技巧之一，我一定没有注意到！希望对某个人有帮助。

## 分享这篇文章