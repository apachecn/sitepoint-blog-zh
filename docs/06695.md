# JavaScript 代码片段强制 DOM 元素重绘/重画

> 原文：<https://www.sitepoint.com/javascript-snippet-force-dom-element-redrawrepaint/>

强制 DOM 元素重绘/重画的 JavaScript 代码片段。有点粗糙，但作为最后的手段是有用的。

```
var element = document.getElementById('id');
var n = document.createTextNode(' ');
var disp = element.style.display;  // don't worry about previous display style

element.appendChild(n);
element.style.display = 'none';

setTimeout(function(){
    element.style.display = disp;
    n.parentNode.removeChild(n);
},20); // you can play with this timeout to make it as short as possible
```

## 使用 Transit.js

如果你使用类似于 [transit.js](http://ricostacruz.com/jquery.transit/) 的过渡插件，这也是可行的:

```
$('#element')
    .transition({ x: '-500px', easing: 'snap', duration:'0' })
    .transition({ x: '0', easing: 'snap', duration:'0' })
    .css('z-index','10');
```

## CSS:

```
#element {
     position: absolute;
     right: '-500px';
     z-index: -1;
}
```

## 分享本文