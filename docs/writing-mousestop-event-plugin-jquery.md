# 使用 jQuery 编写鼠标停止事件插件

> 原文：<https://www.sitepoint.com/writing-mousestop-event-plugin-jquery/>

我最近发布了一个我自己的 jQuery 工具提示插件，非常简单，当你悬停在一个元素上时，它就会弹出来，没有什么特别的。然而，我注意到默认浏览器在实际显示之前有一点延迟。它也不会显示出来，直到你停止在元素上移动鼠标。最后，在工具提示完全不出现之前，你只有一定的时间停止移动鼠标。

我想重建这个功能，并最终在 jQuery 中编写了我自己的小**鼠标停止事件插件，我想我会分享它。这是一段非常简单的代码，基本上是当鼠标停留在某个元素上时触发，有几个定时器选项。**

你可以在这里查看相关文档: [jQuery 鼠标停止插件](http://www.websanova.com/plugins/websanova/mousestop)
也可以在这里查看我的工具提示插件: [jQuery 工具提示插件](http://www.websanova.com/plugins/websanova/tooltip)

## 1.鼠标停止事件

首先，我们只想知道如何检测鼠标停止，这实际上很容易，只需要一个定时器不断循环，直到足够的延迟发生。

```
var movementTimer = null;

    $("#element").mousemove(function(e)
    {
    	clearTimeout(movementTimer);
    	movementTimer = setTimeout(function()
    	{
    		//do something here
    	}, 300);
    })
```

## 2.鼠标移出时停止计时器

我们让鼠标停止工作，但是现在当鼠标离开元素时会发生什么呢？我们不希望触发 mousestop 事件，因为从技术上讲，我们不会在元素上停止。这里我们需要做的就是添加一个 mouseout 事件来清空我们的计时器，以确保它不会被执行。

```
var movementTimer = null;

    $("#element").mouseout(function(e)
    {
    	clearTimeout(movementTimer);
    })
```

## 3.设置宽限期

这就是开始变得棘手的地方。如果我们想设置 mousestop 事件在完全关闭之前触发的时间，我们需要引入第二个计时器。

```
var counter = 0;
    var counterMax = 20;
    var movement = false;

    elem
    .mouseover(function(e)
    {
    	movement = true;

    	//check if movement has stopped to a maximum time of 100*counterMax, after that event will not run at all unless you re-mouseover
    	displayTimer = setInterval(function()
    	{
    		counter++;

    		if(counter 4\. Getting Proper Coordinates
There is one more crucial piece here that we need to add. Since we are running this from the mouseover event, our event.pageX and event.pageY coordinates will be from where they entered the element, however we will probably want the mouse position of where it is now while the mouse is moving around.

[js]
    var x = null;
    var y = null;

    var movementTimer = null;

    $("#element").mousemove(function(e)
    {
    	x = e.pageX;
    	y = e.pageY;
    })
```

## 5.把所有的放在一起

我们现在可以把这些放入一个插件中，如下所示。这个小 jQuery 插件只有不到 100 行代码，缩小后不到 1KB。

 `(function($)
{
var default settings =
{
time tostop:null，//在停止运行之前停止运行的时间量
delay tostop:' 300 '，//被认为是“停止”的延迟
onMouseout:null，//鼠标移出元素时运行的函数
onStopMove:null//在停止后再次开始移动时运行的函数
}；

$ . fn . mouse stop = func(func，settings)
{
settings = $。extend({}，defaultSettings，settings | | { })；

return this . each(function()
{
var elem = $(this)；

var 运动=假；

var displayTimer = null
var movementTimer = null;

//只有当鼠标停止有时间限制时，才需要这一块。
if(settings . timetostop！= null)
{
var x = null；
var y = null；

var 计数器= 0；
var counter max = math . ceil(settings . timetostop/100)；

elem
。mouseover(函数(e)
{
movement = true；

//检查移动是否停止到最大时间 100*counterMax，之后事件根本不会运行，除非将鼠标重新悬停在
display timer = setInterval(function()
{
counter ++上；

if(counter About the Author
![websanova](img/0b914474a88436a2e1a78b50b7abdf04.png "websanova_avatar")
)Robert Nova 是一名 web 开发人员，他对 jQuery 插件特别感兴趣，并想方设法帮助开发人员尽快构建他们的项目。他是 www.websanova.com T4 的创始人，这是一个致力于 jQuery 插件和其他创业资源的网站。` 

## `分享这篇文章`