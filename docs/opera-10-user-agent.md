# 为什么 Opera 10 的用户代理很臭

> 原文：<https://www.sitepoint.com/opera-10-user-agent/>

Opera 浏览器还在继续发展，第 10 版将很快发布。

所有的浏览器都用一个独特的代码来标识自己，这个代码被称为用户代理。2008 年 12 月发布的 alpha 版本使用了一个逻辑用户代理字符串，例如用于英语地区的 Mac:

```
Opera/10.00 (Macintosh; Intel Mac OS X; U; en) Presto/2.2.0
```

但是，所有后续版本和最终版本都将使用:

```
Opera/9.80 (Macintosh; Intel Mac OS X; U; en) Presto/2.2.15 Version/10.00
```

9.8 版本？这是怎么回事？

Opera 是最古老的浏览器之一，也是第一个接近两位数版本号的浏览器。不幸的是，Opera 开发人员在选择使用浏览器嗅探来提供特定版本内容和/或脚本的站点时开始遇到奇怪的问题。许多嗅探脚本只是检测用户代理字符串中的第一个数字，并大胆假设它们运行在 Opera 1 而不是 Opera 10 上。更糟糕的是，许多网站认为浏览器不受支持，拒绝提供任何内容。

Opera 团队仔细考虑了网站兼容性问题，并提出了最佳折衷方案:将字符串的第一部分冻结在 9.80，但在末尾附加真实的版本号。选择 v9.80 而不是 v.9.99，以防将来需要更新初始版本号。

同样的问题很可能会打击微软；Internet Explorer 离 8.0 版本不远了，如果该公司 6 年来没有放弃浏览器的话，它可能现在就在那里了。Mozilla、苹果和谷歌可能还有一段路要走，但 Opera 的问题说明了浏览器嗅探是一个坏主意的另一个原因… *更多原因即将到来…*

最初的 Opera 10 用户代理字符串会影响你的网站吗？

## 分享这篇文章