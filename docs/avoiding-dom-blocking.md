# 如何避免 JavaScript 中的 DOM 阻塞

> 原文：<https://www.sitepoint.com/avoiding-dom-blocking/>

JavaScript 程序在浏览器中的单个线程上运行，并在 Node.js 等运行时运行。当代码在浏览器标签中执行时，其他一切都会停止:菜单命令、下载、渲染、DOM 更新甚至 GIF 动画。

这对于用户来说很少是明显的，因为处理在小块中进行得很快。例如:单击一个按钮会引发一个事件，该事件会运行一个函数来进行计算并更新 DOM。完成后，浏览器可以自由处理处理队列中的下一个项目。

JavaScript 代码不能等待事情发生；想象一下，如果一个应用程序每次发出 Ajax 请求时都死机，那该有多沮丧。因此，JavaScript 代码使用事件和回调进行操作:当操作完成且结果就绪时，浏览器或操作系统级进程被指示调用特定的函数。

在下面的例子中，当一个按钮点击事件发生时，一个处理函数被执行，这个事件通过应用一个 CSS 类来激活一个元素。当动画完成时，匿名回调删除该类:

```
// raise an event when a button is clicked
document.getElementById('clickme').addEventListener('click', handleClick);

// handle button click event
function handleClick(e) {

  // get element to animate
  let sprite = document.getElementById('sprite');
  if (!sprite) return;

  // remove 'animate' class when animation ends
  sprite.addEventListener('animationend', () => {
    sprite.classList.remove('animate');
  });

  // add 'animate' class
  sprite.classList.add('animate');
} 
```

ES2015 提供了`Promises`，ES2017 引入了`async` / `await`让编码变得更简单，但回调仍然在表面之下使用。更多信息请参考“现代 JS 中的[流量控制”。](https://www.sitepoint.com/flow-control-callbacks-promises-async-await/)

## 阻挡强盗

不幸的是，有些 JavaScript 操作总是同步的，包括:

*   运行计算
*   更新 DOM
*   使用 [localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API) 或 [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) 来存储和检索数据。

下面的笔展示了一个入侵者，它结合使用 CSS 动画来移动和 JavaScript 来挥动四肢。右图是一个基本的动画 GIF。点击*写*按钮，默认为 100，000 `sessionStorage operations`:

参见 [CodePen](https://codepen.io) 上 site point([@ site point](https://codepen.io/SitePoint))
的笔 [DOM-blocking 动画](https://codepen.io/SitePoint/pen/GzLPJV/)。