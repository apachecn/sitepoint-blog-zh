# jQuery Detect 滚动到底部——读取 T&C

> 原文：<https://www.sitepoint.com/jquery-detect-scroll-bottom-read-tc/>

jQuery 代码片段，用于在启用条款和条件复选框之前检测用户是否已滚动到页面底部(或带 scroll 的 div)。

```
 Terms of service jargon stuff here

<input name="TERMS_ACCEPTED_YN" type="checkbox" id="TERMS_ACCEPTED_YN" value="Y"><label>I accept the blah, blah, blah.</label>

jQuery(document).ready(function() {
	jQuery("input#TERMS_ACCEPTED_YN").attr("disabled", true);

	var $box = $("#scrollPane"),
	$inner = $("> .inner", $box),
	innerOuterHeight = $inner.outerHeight();
	boxHeight = $box.height();
	boxOffsetTop = $box.offset().top;

	jQuery("#scrollPane").scroll(function() {
		if (Math.ceil(boxHeight - $inner.offset().top + boxOffsetTop) >= innerOuterHeight ) {
		jQuery("input#TERMS_ACCEPTED_YN").removeAttr("disabled");
	}
 });
});
```

## 分享这篇文章