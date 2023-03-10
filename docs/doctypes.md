# Doctypes

> 原文：<https://www.sitepoint.com/doctypes/>

文档类型只是告诉浏览器或任何其他解析器他们正在查看的文档类型的一种方式。在 HTML 文件的情况下，它们指的是 HTML 的特定版本和风格。doctype 应该始终是所有 HTML 文件顶部的第一项。在过去，doctype 声明是一堆难看且难以记忆的东西。对于 XHTML 1.0 严格版:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"
  "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
```

对于 HTML4 过渡版:

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
  "https://www.w3.org/TR/html4/loose.dtd">
```

多年来，代码编辑软件开始提供已经包含 doctype 的 HTML 模板，或者提供自动插入模板的方法。很自然地，快速的网络搜索会很容易地显示出插入您需要的任何文档类型的代码。

虽然在我们的文档顶部有一长串文本并没有真正伤害我们(除了强迫我们网站的浏览者下载一些额外的字节)，HTML5 已经去掉了这个难以辨认的眼中钉。现在你只需要这个:

```
 <!doctype html>
```

简单明了。你会注意到声明中明显缺少了“5”。虽然当前的 web 标记迭代被称为“HTML5”，但它实际上只是以前 HTML 标准的一个演变——未来的规范将只是我们今天的发展。因为浏览器必须支持 Web 上的所有现有内容，所以不需要依赖 doctype 来告诉它们在给定的文档中应该支持哪些特性。

## 分享这篇文章