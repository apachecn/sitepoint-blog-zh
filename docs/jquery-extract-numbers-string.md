# jQuery 从字符串中提取数字

> 原文：<https://www.sitepoint.com/jquery-extract-numbers-string/>

使用替换正则表达式方法从字符串中提取数字的快速 jQuery 代码片段。

```
function getId(str)
{
    return str.replace(/[^d.,]+/,'');
}
```

## 分享这篇文章