# JavaScript 位置主机名和 URL 示例

> 原文：<https://www.sitepoint.com/javascript-location-hostnames-url-examples/>

操纵位置 URL 的简单 JavaScript 代码片段(你知道地址栏里那个奇怪的东西吧！)来获取主机、主机名、路径名、协议、端口和正则表达式，以检查一个字符串是否是主机名。

```
console.log(window.location.href);
//output: https://www.jquery4u.com/javascript/javascript-location-hostnames-url-examples/

console.log(window.location.hostname);
//output: www.jquery4u.com

console.log(window.location.host);
//output: www.jquery4u.com

console.log(window.location.pathname);
//output: /javascript/javascript-location-hostnames-url-examples/

console.log(window.location.protocol);
//output: https:

console.log(window.location.port);
//output: (an empty string)

//regular expression to check if a string is a hostname.
console.log(window.location.hostname.match(/^.*?-?(w*)./)[1]);
//output: www
```

[https://bl.ocks.org/abernier/raw/3070589/](https://bl.ocks.org/abernier/raw/3070589/)

**提示:**如果你只是在 [firebug](https://www.jquery4u.com/utilities/live-jquery-debugging-firebug/) 中输入 window.location，你就可以分析以上所有内容。

## 分享这篇文章