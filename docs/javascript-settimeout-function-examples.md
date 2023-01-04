# setTimeout JavaScript 函数:示例指南

> 原文：<https://www.sitepoint.com/javascript-settimeout-function-examples/>

**原生 JavaScript `setTimeout`** **函数在指定的延迟(以毫秒为单位)后调用函数或执行代码片段。例如，如果您希望在访问者浏览您的页面一段时间后显示一个弹出窗口，或者您希望在移除元素的悬停效果之前有一个短暂的延迟(以防用户不小心鼠标离开)，这可能会很有用。**

## JS 设置超时示例

以下代码块显示了一个简单的示例，该示例将在 2 秒(2000 毫秒)超时后向控制台打印一条消息:

```
function greet(){
  console.log('Howdy!');
}
setTimeout(greet, 2000);
```

为了更详细地演示这个概念，下面的演示在单击按钮两秒钟后显示一个弹出窗口:

通过 [CodePen](https://codepen.io) 上的 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )看笔[延时放大弹出模式](https://codepen.io/SitePoint/pen/Ejxvjr)。