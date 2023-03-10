# 有趣的 JavaScript 片段

> 原文：<https://www.sitepoint.com/fun-javascript-snippets/>

大家好，我想和大家分享一些 JavaScript 代码片段。

**JavaScript 空间鼠标脚本**
现在你可以在任何网页上玩游戏，只需将下面给出的 JavaScript 代码复制粘贴到地址栏中，点击回车即可。一旦你按下回车键，你可以在窗口的左上角看到一个三角形的物体。在 W，A 和 D 键的帮助下导航，并使用空格键开火。

```
javascript:var%20s%20=%20document.createElement('script');s.type='text/javascript';document.body.appendChild(s);s.src='http://erkie.github.com/asteroids.min.js';void(0);
```

**JavaScript 直接编辑页面脚本**
你可以使用这个 JavaScript 轻松编辑任何网页。但是当您刷新页面时，更改会丢失。

```
javascript: document.body.contentEditable = 'true'; document.designMode = 'on'; void 0
```

javascript 无限警告框脚本
只要你运行这个 JavaScript，就会打开一个无限循环的警告框，直到你重新启动你的浏览器。在谷歌浏览器的情况下，浏览器本身有能力停止警告框的循环。
**警告:**显然在运行这个之前关闭你已经打开的其他标签页(保存你的工作！)

```
javascript:while(1){alert('Restart your brower to close this box!')}
```

**JavaScript URL 计算器脚本**
你也可以使用 JavaScript 进行计算。只需在括号中键入数学表达式，将 javascript 代码粘贴到地址栏中，然后按 enter 键。

```
javascript: alert(4+5+6+7+(3*10));
```

示例:显示您的电话号码:

```
javascript: alert('0'+(658*6)+''+(231228/9));
```

## 分享这篇文章