# 使用 jQuery 的$。最接近的()对$。父母()

> 原文：<https://www.sitepoint.com/jquerys-closest-parents/>

所以我猜你应该考虑用 [$了。最接近()](http://api.jquery.com/closest/)而不是 [$。父母()](http://api.jquery.com/parents/)。但是首先，像往常一样，你需要知道你在处理什么——它们之间的关键区别。

| 。最近的() | 。父母() |
| --- | --- |
| 从当前元素开始 | 从父元素开始 |
| 沿着 DOM 树向上移动，直到找到与所提供的选择器匹配的内容 | 沿着 DOM 树向上移动到文档的根元素，将每个祖先元素添加到一个临时集合中；然后，它根据选择器(如果提供了选择器的话)过滤该集合 |
| 对于原始集合中的每个元素，返回的 jQuery 对象包含零个或一个元素 | 对于原始集中的每个元素，返回的 jQuery 对象包含零个或多个元素 |

这个 [jsperf](http://jsperf.com/jquery-parents-vs-closest/20) 显示速度结果。

![22-04-2013 12-09-37 AM](img/0214d5f2e830306d0e454938e37b2372.png)

### 使用$获取父容器。父母()

代码示例。

```
//remove handler
$('.btn.remove').on('click', _this.cache.$container, function(e)
{
     e.preventDefault();
     console.log('remove...');
     $(this).parents('li').first().remove();
});
```

### 使用$获取父容器。最近的()

代码示例。

```
//remove handler
$('.btn.remove').on('click', _this.cache.$container, function(e)
{
     e.preventDefault();
     console.log('remove...');
     $(this).closest('li').remove();
});
```

## 分享这篇文章