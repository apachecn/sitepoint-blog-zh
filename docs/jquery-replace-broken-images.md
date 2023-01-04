# 使用 jQuery 通过 AJAX 替换损坏的图像

> 原文：<https://www.sitepoint.com/jquery-replace-broken-images/>

在我上一篇关于[检测和移除破损图像](http://www.jquery4u.com/snippets/jquery-detect-hide-broken-images/)的文章的基础上，我进一步研究了使用 jQuery 替换 AJAX 破损图像的**领域。

在大多数浏览器中，如果找不到图片就会显示 ALT 标签。如果图像很小而 alt 标记很长，这可能是一个问题，因为元素的输出宽度似乎不受 ALT 标记长度的影响。所以用默认图像替换损坏的图像是有意义的。**

## 获取第页上当前图像的相关信息

```
$("img").each( function () {
	console.log($(this).attr('src')+ ' ' + $(this).attr('alt') + ' ' + $(this).width());
});
```

## 使用 AJAX 测试图像是否存在

```
$("img").each( function () 
{
	$.ajax({
		url:$(this).attr('src'),
		type:'HEAD',
		error:
			function(){
				//image doesn't exist
				console.log('ERROR');
			},
		success:
			function(){
				//image exists
				console.log('success');
			}
	});
});
/*
Output:
success
success
ERROR
success
success
*/
```

## 刷新图像

```
d = new Date();
$("#myimg").attr("src", "/myimg.jpg?"+d.getTime()); //cache flush
```

## 使用 ajax 修复损坏的图像

注意增加了 _this 和 e.status。

```
$(".productBoxImage img").each( function () 
{
	var _this = $(this);

	$.ajax({
		url:$(this).attr('src'),
		type:'HEAD',
		async: false,
		error:
			function(e)
			{
				if (e.status == '404') {
					$(_this).attr('src',[replaceImageUrl]);
				}
			}
	});
});
```

## 非 ajax 函数版本

```
/**
 * Returns true if image is broken, false otherwise
 * @param {jQuery} image A single image element
 * @return {Boolean}
 */
isImageBroken: function(image)
{
	$image = $(image);
	if($image.attr('complete') == false || $image.attr('naturalWidth') == 0 || $image.attr('readyState') == 'uninitialized' || this.trim($image.attr('src')) == '')
	{
		return true;
	}
	return false;
},
```

希望所有的都有意义，如果没有发表评论，我会回答你的问题！:)

## 分享这篇文章