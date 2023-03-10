# 使用 SetTimeout 创建 jQuery 计时器

> 原文：<https://www.sitepoint.com/settimeout-jquery-timer/>

这个例子展示了一个 jQuery 倒计时，数字每秒钟从 10 开始倒计时。这是通过递减计数器数值，然后在超时函数调用结束时再次调用 setTimeout 来实现的。

这是代码。

## jQuery 代码

```
jQuery(document).ready(function () {

	var countdown;
	var countdown_number;

	function countdown_init() {
		countdown_number = 11;
		countdown_trigger();
	}

	function countdown_trigger() {
		if(countdown_number > 0) {
			countdown_number--;
			$('countdown_text').innerHTML = countdown_number;
			if(countdown_number > 0) {
				countdown = setTimeout('countdown_trigger()', 1000);
			}
		}
	}

	function countdown_clear() {
		clearTimeout(countdown);
	}

});
```

## HTML 代码

```
 <input type="button" value="start countdown" onclick="countdown_init()">
    <input type="button" value="stop countdown" onclick="countdown_clear()">

Placeholding text
```

## 分享这篇文章