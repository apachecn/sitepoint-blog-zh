# 10 个有用的 jQuery 片段

> 原文：<https://www.sitepoint.com/10-jquery-snippets/>

如今，jQuery 是许多开发人员最喜欢的 Javascript 框架。通过 jQuery，他们将能够创建令人惊叹的视觉效果，操纵数据等。你可能已经在我之前的其他帖子中发表过，所以我不会解释 jQuery 有什么好处。

## 1.快速复制粘贴

## 2.出生日期

```
$("#lda-form").submit(function(){
	var day = $("#day").val();
	var month = $("#month").val();
	var year = $("#year").val();
	var age = 18;
	var mydate = new Date();
	mydate.setFullYear(year, month-1, day);

	var currdate = new Date();
	currdate.setFullYear(currdate.getFullYear() - age);
	if ((currdate - mydate) 3\. Text Search
[js]
$.fn.egrep = function(pat) {
 var out = [];
 var textNodes = function(n) {
  if (n.nodeType == Node.TEXT_NODE) {
   var t = typeof pat == 'string' ?
    n.nodeValue.indexOf(pat) != -1 :
    pat.test(n.nodeValue);
   if (t) {
    out.push(n.parentNode);
   }
  }
  else {
   $.each(n.childNodes, function(a, b) {
    textNodes(b);
   });
  }
 };
 this.each(function() {
  textNodes(this);
 });
 return out;
};
```

## 4.XML 文件解析器

```
function parseXml(xml) {
  //find every Tutorial and print the author
  $(xml).find("Tutorial").each(function()
  {
    $("#output").append($(this).attr("author") + "");
  });
}
```

## 5.类别悬停添加和移除

```
$('.onhover').hover(
function(){ $(this).addClass('hover_style_class') },
function(){ $(this).removeClass('hover_style_class') }
)
```

## 6.部分页面刷新

```
setInterval(function() {
$("#refresh").load(location.href+" #refresh>*","");
}, 10000); // seconds to wait, miliseconds
```

## 7.鼠标位置

```
function rPosition(elementID, mouseX, mouseY) {
  var offset = $('#'+elementID).offset();
  var x = mouseX - offset.left;
  var y = mouseY - offset.top;

  return {'x': x, 'y': y};
}
```

## 8.延迟动画或效果

```
$(".alert").delay(2000).fadeOut();
```

## 9.弹出窗口开启器

```
jQuery('a.popup').live('click', function(){
	newwindow=window.open($(this).attr('href'),'','height=200,width=150');
	if (window.focus) {newwindow.focus()}
	return false;
});
```

## 10.每个元素

```
$("input").each(function (i) {
//do something with each and pass i as the number of the element
});
```

## 分享这篇文章