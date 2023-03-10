# 全屏视频测试

> 原文：<https://www.sitepoint.com/fullscreen-video-testing/>

只是记录一些全屏视频技术和 jQuery UI 对话窗口。我在我的演示中嵌入了 YouTube 视频，但是你可以嵌入任何类型的视频。

### 检查浏览器全屏显示的能力。

![fs-banner](img/0b55bde92aee88d4a15bea7918753554.png "fs-banner")

### 将视频元素转换成 jQuery UI 对话框窗口。

![fs-widgets](img/1257dd23b369996b5aa8ce595acbdae9.png "fs-widgets")

### 使 1 个元素全屏显示。

![fs-1-element](img/4f51efb27d138c8930ad2913ba93017b.png "fs-1-element")

### 使两个元素全屏显示(就像缩略图列表视图)。

![](img/fb5c4e53510b5c7beff16f0aafb65f27.png "fs-widgets-fs")

### 现在是很酷的东西。使一个元素全屏显示，另一个元素可在较小的窗口中拖动。

![](img/8ac9336819608459721f01a6a1d744dc.png "fsvid-widgets-fs-small")

### 让我们看看一些代码

 `/**
* jQuery 全屏视频测试
*
*简化了制作全屏视频的过程，还测试了使用 jQuery UI 对话框制作
*视频小部件，其中一个视频可拖动到另一个之上。
*
* @版权 Copyright(c)2013 jquery 4 u
* @许可 http://jquery4u.com/license/
* @链接 http://jquery4u.com
* @自 1.0 版本
* @作者山姆迪林
* @ filesource jquery 4 u-fs vid . js
*
*/

(函数($，W，D，未定义)
{

W.FSVID = W.FSVID || {};

W.FSVID =
{
名称:“全屏视频”，

/*
*初始化。
*/
init:function(settings)
{
this . settings = $。extend({}，this.settings，settings)；
this . checkfullscreenpossible()；
this . setup eventhandlers()；
}，

checkFullscreenPossible:function()
{
var $ fs status = $(' # fs status ')；
if(w . fullscreenapi . supportsfullscreen)
{
//浏览器支持全屏
$fsStatus.html('是:你的浏览器支持全屏！').add class(' full screen supported ')；
}
else
{
//浏览器不支持全屏
$fsStatus.html('对不起:您的浏览器不支持全屏！').add class(' fullscreenotsupported ')；
}
}，

/*
*设置按钮点击事件。
*/
setup eventhandlers:function()
{
var _ this = W . fs vid；
$('#createWidgets ')。on('click '，函数(e)
{
e . prevent default()；
_ this[" create widgets "]()；
})；

//——————————————————————

//防止浏览器安全漏洞
var f element1 = document . getelementbyid(' 1 ')；

//处理按钮 click
$("#gofs1 ")。on('click '，function()
{
window . fullscreenapi . request full screen(f element 1)；
})；

//——————————————————————

var fselement 2 = document . getelement byid(' 2 ')；

//处理按钮 click
$("#gofs2 ")。on('click '，function()
{
window . fullscreenapi . request full screen(f element 2)；
})；

//——————————————————————

var fs container = document . getelementbyid(' vid container ')；

$("#gofsall ")。on('click '，function()
{
w . full screenapi . request full screen(fs container)；
})；

//——————————————————————

var fs container = document . getelementbyid(' vid container ')；

$("#gofsallwidgets ")。on('click '，function()
{
$('#w_1，#w_2 ')。分离()。前置到(' # vid container ')；//将小部件移入 fs 容器
w . fullscreenapi . request full screen(fs container)；
})；

//——————————————————————

var fs container = document . getelementbyid(' vid container ')；

$("#gofssmart ")。on('click '，function()
{
$('#w_1，#w_2 ')。分离()。前置到(' # vid container ')；//将小部件移入 fs 容器
//最大化 w1
$('#w_1 ')。css({
"height": "100% "，
"width": "100% "，
"z-index": "1001 "，
"left": "0 "，
" top ":" 0 "
})；
//移动 w2
$('#w_2 ')。css({
"height": "360px "，
"width": "480px "，
"z-index": "1002 "，
"left": "2% "，
" top ":" 62% "
})；
w . full screenapi . request full screen(fs container)；
})；

//捕捉全屏事件
$(W)。on(w . full screenapi . full screen event name，function()
{
if(w . full screenapi . is full screen())
{
//console . log('回车全屏')；
$(’。f-btns’)。hide()；//进入全屏
}
else
{
//console . log('退出全屏')；
$(’。f-btns’)。show()；//退出全屏
}
})；
}，

/*
*创建一个带有视频的 jQuery UI 小部件。
*/
create widget:function(wid)
{
var $ vid = $('。视频# '+wid)；

//创建 jQuery UI 对话框
$ vid . dialog(
{
" title ":$ vid . find('。title ')、
“宽度”:“480”、
“高度”:“360”、
“位置”:[ ($('。ui-小部件)。length*500)+20，290 ]，
"resizable": true，
"draggable": true
})。css(
{
"宽度":" 100% "，
"高度":" 100% "
})；
$vid.parent('。ui-小部件)。attr('id '，' w_'+wid)。css(
{
"width": "480px "，
" height ":" 360 px "
})；

//保持简单，否则会遇到全屏浏览器安全问题
var fs button = document . getelementbyid(' gofs '+wid)，
fs element = document . getelementbyid(' w _ '+wid)；

//移除之前的事件处理程序
$(fsButton)。关('点击')；
$(f element)。off(w . full screenapi . full screen event name)；

//处理全屏按钮 click
$(fsButton)。on('click '，function()
{
w . full screenapi . request full screen(fs element)；
})；
}，

/*
*将所有视频元素变成 widgets。
*/
create widgets:function()
{
$('。视频’)。each(function (i，v)
{
W . fs vid . create widget($(v)。attr(' id ')；
})；
}

}

$(D)。ready(function()
{
W . fs vid . init()；//加载数据并启动 dashboard obj
})；

})(jQuery，窗口，文档)；

/*原生全屏 JavaScript API
————————————*/

(function(){
var full screenapi = {
supports full screen:false，
is full screen:function(){ return false；}，
request full screen:function(){ }，
cancel full screen:function(){ }，
fullScreenEventName:"，
前缀:"
}，
browser prefixes = ' WebKit moz o ms khtml '。拆分(“”)；

//如果(type of document . cancel full screen！= ' undefined '){
full screenapi . supportsfullscreen = true；
} else {
//通过厂商前缀
为(var i = 0，il = browserPrefixes.lengthI<il；i++){ fullscreenapi . prefix = browser prefix[I]；if(文档类型[full screenapi . prefix+' cancel full screen ']！= ' undefined '){ full screenapi . supportsfullscreen = true；打破；} } } //更新方法做一些有用的 if(full screenapi . supportsfullscreen){ full screenapi . full screenevent name = full screenapi . prefix+' full screenchange '；full screenapi . is full screen = function(){ switch(this . prefix){ case ' ':返回 document.fullScreencase“WebKit”:return document . webkitis full screen；默认:返回文档[this . prefix+' full screen ']；} } full screenapi . request full screen = function(El){ return(this . prefix = = = ' ')？El . request full screen():El[this . prefix+' request full screen ']()；} fullscreenapi . cancelfullscreen = function(El){ return(this . prefix = = = ' ')？document . cancel full screen():document[this . prefix+' cancel full screen ']()；} } // jQuery 插件 if(jQuery 类型！= ' undefined '){ jQuery . fn . request full screen = function(){ return this . each(function(){ var El = jQuery(this)；if(full screenapi . supportsfullscreen){ full screenapi . request full screen(El)；} });};} //导出 API window . fullScreenApi = fullScreenApi；})();[/js]` 

## `分享这篇文章`