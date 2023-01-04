# jQuery 复制到剪贴板 4 选项

> 原文：<https://www.sitepoint.com/jquery-copy-clipboard-4-options/>

![jquery-clipboard-copy](img/54f4f8cd6a0f59f68f4e8b21125d1234.png "jquery-clipboard-copy")

好了，今天我尝试使用 jQuery(和普通的 JavaScript)来自动化复制到剪贴板的过程，大家都知道可以通过右键单击>复制或按 CTRL+C 来轻松完成。复制到剪贴板的一般工作方式是，您可以简单地调用 exec copy 命令来将当前选择内容复制到剪贴板。然而，似乎唯一支持这个的浏览器是 IE。现在，我们有了一个解决方法，使用 Flash SWF 文件来运行并以这种方式将选择内容保存到剪贴板。你可以使用 jQuery 插件，在我看来，你有以下四种选择。

注意:我的建议是使用 3。复制文本的变通办法，因为它可以是痛苦的尝试，让其他插件工作。

## 1.zClip

zClip 只对超链接有效。

```
function copy (str)
{
    //for IE ONLY!
    window.clipboardData.setData('Text',str);
}
```

[来源](http://www.steamdev.com/zclip/)

## 2.零剪贴板

一个非常漂亮的剪贴板插件:[下载页面](http://code.google.com/p/zeroclipboard/downloads/list)。

```
function init() {
	clip = new ZeroClipboard.Client();
	clip.setHandCursor( true );

	clip.addEventListener('load', my_load);
	clip.addEventListener('mouseOver', my_mouse_over);
	clip.addEventListener('complete', my_complete);

	clip.glue( 'd_clip_button' );
}
```

[来源](http://bowser.effectgames.com/~jhuckaby/zeroclipboard/)

## 3.复制文本解决方法

### JQUERY 代码

```
$(document).ready(function(){
	//store nodepath value to clipboard	(copy to top of page)
	$('li').live('click', function(){
		//console.log($('#pathtonode').html()+ " copied to window");
		var path = $('#pathtonode').html();
		path = path.replace(/ &gt; /g,".");
		//console.log(path);
		addtoppath(path);
	});
	//initially hide copy window
	$('#toppathwrap').hide();

	function addtoppath(path) {
		//console.log(path);
		$('#copypath').val(path);
		$('#toppathwrap').show();
		$('#copypath').focus();
		$('#copypath').select();
	}	
});
```

对于动态 DOM 元素:

```
$('#copypath', 'body')
	.find('a')
		.livequery('click', function() {
			$(this)
				.blur();
			//console.log('copied to copy window');
			var nodetext = $('#id-of-element-to-copy').html();
			$('#copypath input').focus();
			$('#copypath input').select();
			return false;
		});
```

### HTML 代码

```
<div id="toppathwrap">
	<textarea id="copypath"></textarea>
</div>
```

### CSS 代码

```
#toppathwrap { position:fixed; top:0px; right:0px; background-color:#F2F1E8; padding:5px; display:none; }
```

[来源](https://www.sitepoint.com/online-json-tree-viewer/)

## 4.大卫·沃尔什教程

```
//javascript copy function
		function copy(inElement) {
		  if (inElement.createTextRange) {
			var range = inElement.createTextRange();
			if (range && BodyLoaded==1)
			 range.execCommand('Copy');
		  } else {
			var flashcopier = 'flashcopier';
			if(!document.getElementById(flashcopier)) {
			  var divholder = document.createElement('div');
			  divholder.id = flashcopier;
			  document.body.appendChild(divholder);
			}
			document.getElementById(flashcopier).innerHTML = '';
			var divinfo = '<embed src="_clipboard.swf" flashvars="clipboard='+escape(inElement.value)+'" width="0" height="0" type="application/x-shockwave-flash">';
			document.getElementById(flashcopier).innerHTML = divinfo;
		  }
		}
```

[来源](http://davidwalsh.name/clipboard)

## 分享这篇文章