# 使用 CSS 和 JavaScript 文章操纵滚动条颜色

> 原文：<https://www.sitepoint.com/colors-css-javascript/>

滚动条的默认颜色是暗淡难看的——通常这种颜色是灰色的。改变这种颜色来更好地适应你网站的整体主题不是很好吗？幸运的是，级联样式表和 JavaScript 可以用来做到这一点！

##### 使用 CSS

在 CSS 中，只需将下面的定义添加到页面顶部，就可以自定义浏览器滚动条的颜色。CSS 的伟大之处在于不理解它的浏览器会直接跳过它。IE5.5 及更高版本支持滚动条绘制。

`<style>
<!--
BODY{
scrollbar-face-color:#8080FF;
scrollbar-arrow-color:#FFFFFF;
scrollbar-track-color:#DDDDFF;
scrollbar-shadow-color:'';
scrollbar-highlight-color:'';
scrollbar-3dlight-color:'';
scrollbar-darkshadow-Color:'';
}`

–>
</风格>

我打赌你从来没有意识到滚动条由这么多组件组成！前三个定义是最重要的，因为它们对应于滚动条最明显的方面。请随意使用其他定义，看看它们会产生什么影响。

##### 使用 JavaScript

您还可以使用 JavaScript 来动态改变滚动条的颜色。当你想做一些有趣的事情时，比如将滚动条从一种颜色切换到另一种颜色，这是很有用的。滚动条 CSS 定义的 JavaScript 翻译如下:

`document.body.style.scrollbarFaceColor="colorname"
document.body.style.scrollbarArrowColor="colorname"
document.body.style.scrollbarTrackColor="colorname"
document.body.style.scrollbarShadowColor="colorname"
document.body.style.scrollbarHighlightColor="colorname"
document.body.style.scrollbar3dlightColor="colorname"
document.body.style.scrollbarDarkshadowColor="colorname"`

下面是一个“闪烁”滚动条的例子，它每秒钟改变一次颜色:

`<script>`

where 模式=0

函数 blink scroll(){
if(mode = = 0)
document . body . style . scrollbarfacecolor = " blue "
else
document . body . style . scrollbarfacecolor = " green "
mode =(mode = = 0)？1:0
}
setInterval(" blink scroll()"，1000)
< /script >

Svetlin Staev 编写了一个使用 JavaScript 操作滚动条的更详细的例子，名为[onMouseover scroll bar Effect](http://www.dynamicdrive.com/dynamicindex11/scrolleffect.htm)。当你移动鼠标离开滚动条时，它会改变滚动条的颜色。

我看到越来越多的网站自定义滚动条的颜色，以融入他们网站的其余部分。我希望这些建议对帮助你做同样的事情有用！

## 分享这篇文章