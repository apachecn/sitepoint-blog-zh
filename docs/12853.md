# HTML 电子邮件的条件注释

> 原文：<https://www.sitepoint.com/conditional-comments-for-html-email/>

看看这个:

```
<!--[if gte mso 9]>

<![endif]-->
```

一般的语法可能会很快被人们所熟悉——这是一个[条件注释](http://www.quirksmode.org/css/condcom.html),只对 Windows 版本的 Internet Explorer 提供特定的内容有用。但是您知道它们也适用于办公应用程序吗？

![](img/d6ecb307dd14d9c5c679400889c889c1.png)

我也不知道…直到最近。

我们刚刚为我们的[电子邮件简讯](https://www.sitepoint.com/newsletter/)彻底检查了 <abbr title="HyperText Markup Language">HTML</abbr> 模板……当然这是一个痛苦的过程——如果你认为网络浏览器是苛刻的任务大师，你应该尝试编码以适应电子邮件客户！最近的一篇文章，[如何编写 HTML 电子邮件简讯](https://www.sitepoint.com/how-to-code-html-email-newsletters/)，阐述了在最流行的电子邮件客户端中制作一个像样的布局是多么的复杂，以及你必须求助的技术是多么的神秘。这个问题变得更加复杂，因为最流行的 Windows 客户端之一——Outlook——不再使用 Internet Explorer 进行 HTML 呈现，而是使用 Word！

正是在仔细阅读 Word 文档中“另存为网页”输出的 franken-code 时，我第一次看到了这些小金块——正是我们需要从 Outlook 中隐藏的内容，这些内容太混乱了，不能留下。

所以你走吧。如果你正在发布 HTML 格式的电子邮件，并且正在努力处理 Outlook 的渲染，这里有一个额外的小工具。该语法甚至暗示它可以用于 Office 版本的精确定位；但我没有测试过这个，所以任何信息都非常感谢:)

## 分享这篇文章