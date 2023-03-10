# jQuery 代码仅在 Firebug 打开时运行

> 原文：<https://www.sitepoint.com/jquery-code-runs-firebug-open/>

![firebug1](img/a06b7e8b963c8d9c60d989e5cb73423e.png "firebug1") ![ie-sad](img/daa3a445daf37546fc7a82582df1b9c0.png "ie-sad")

所以您可能想知道为什么您的 jQuery 代码只在 firebug 在您的浏览器上打开时才运行。这可能是因为您在代码中使用了 console.log 命令，而 jQuery 代码因为控制台不存在而失败。

要修复这个简单的把你的 console.log 和 firebug 命令放在下面的代码里面:

```
if (window.console) {
	console.log(text);
}
```

## 确保没有安装 Firebug 的浏览器不会出现 JavaScript 错误

```
if (!window.console || !console.firebug)
{
    var names = ["log", "debug", "info", "warn", "error", "assert", "dir", "dirxml",
    "group", "groupEnd", "time", "timeEnd", "count", "trace", "profile", "profileEnd"];

    window.console = {};
    for (var i = 0; i Turn it into a debug function sir?
[js]
function debug(text) {
	if ((typeof(Debug) !== 'undefined') && Debug.writeln) {
		Debug.writeln(text);
	}
	if (window.console && window.console.log) {
		window.console.log(text);
	}
	if (window.opera) {
		window.opera.postError(text);
	}
	if (window.debugService) {
		window.debugService.trace(text);
	}
}
```

## 如果控制台可用，记录错误

```
if (typeof(console) != 'undefined' && typeof(console.log) == 'function') {
	// If console available, log the error.
	console.log('Problem hiding the form', e);
}
```

## 分享这篇文章