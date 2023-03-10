# 如何构建自己的渐进式图像加载器

> 原文：<https://www.sitepoint.com/how-to-build-your-own-progressive-image-loader/>

![A magician revealing an image via progressive image loader](img/56aefdac9f30e23e543343c236393398.png)

你可能在[脸书](https://code.facebook.com/posts/991252547593574/the-technology-behind-preview-photos/)和[媒体](https://jmperezperez.com/medium-image-progressive-loading-placeholder/)上遇到过进步图像。当元素滚动到视图中时，模糊的低分辨率图像会被全分辨率图像替换:

![progressive image example](img/1938446d81c0b6c0b6aee6591c5a3f90.png)

预览图像很小，可能是 20px 宽的高度压缩的 JPEG。该文件可以少于 300 字节，并立即出现，给人以快速加载的印象。真实图像在需要时被延迟加载。

渐进图像是伟大的，但目前的解决方案相当复杂。幸运的是，我们可以用一点 HTML5、CSS3 和 JavaScript 来构建一个。该准则将:

*   速度快，重量轻——只有 463 字节的 CSS 和 1，007 字节的 JavaScript(精简版)
*   支持[响应图像](https://www.sitepoint.com/how-to-build-responsive-images-with-srcset/)加载更大或高分辨率(视网膜)屏幕的替代版本
*   没有依赖性——它适用于任何框架
*   适用于所有现代浏览器(IE10+)
*   逐步增强，以便在较旧的浏览器中工作，或者在 JavaScript 或图像加载失败时工作
*   要好用。

## 我们的演示和 GitHub 代码

这就是我们的技术的样子:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看到笔[响应式图像](http://codepen.io/SitePoint/pen/VPVEZm/)。

[从 GitHub 下载代码](https://github.com/craigbuckler/progressive-image.js)

## HTML

我们将从一些基本的 HTML 开始来实现我们的渐进式图像:

```
<a href="full.jpg" class="progressive replace">
  <img src="tiny.jpg" class="preview" alt="image" />
</a>
```

其中:

*   `full.jpg`是链接`href`中包含的大尺寸全分辨率图像，以及
*   是我们微小的预览图像。

我们已经有了一个最低限度的工作系统。没有任何 JavaScript——或者在可能失败的旧浏览器中——用户可以通过点击预览来查看完整的图像。

两幅图像必须具有相同的纵横比。例如，如果`full.jpg`是 800 x 200，那么它的长宽比为 4:1。`tiny.jpg`因此可以是 20 x 5，但是你不应该使用 30px 的宽度，因为这需要 7.5px 的高度。

注意链接和预览图像上使用的类；这些将在我们的 JavaScript 代码中用作钩子。

### 要内嵌或不内嵌图像

预览图像也可以作为数据 URI 被内联

```
<img src="data:image/jpeg;base64,/9j/4AAQSkZJ..."  class="preview" />
```

内联图像立即出现，需要更少的 HTTP 请求，并避免额外的页面回流。然而:

*   添加或更改内联图像*需要更多的努力(尽管构建过程如[吞咽](http://gulpjs.com/)会有帮助)*
*   base-64 编码效率较低，通常比二进制数据大 30%*(尽管这被额外的 HTTP 请求头抵消)*
*   无法缓存内联图像。它们将被缓存在 HTML 页面中，但是如果不重新发送相同的数据，就不能在另一个页面上使用。
*   HTTP/2 减少了对内嵌图像的需求。

务实:如果图像用于单个页面，或者生成的代码很小，即不比 URL 长多少，那么内联是一个很好的选择！

## CSS

我们从定义链接容器样式开始:

```
a.progressive {
  position: relative;
  display: block;
  overflow: hidden;
  outline: none;
}
```

这将设置基本的容器布局属性。如果需要，链接可以应用其他类和样式来设置尺寸或位置。

你可以考虑设置精确的尺寸或者使用[这个`padding-top`技巧来加强固有的纵横比](https://www.sitepoint.com/maintain-image-aspect-ratios-responsive-web-design/)。这将确保在图像加载和回流被避免之前容器被调整大小。然而，有必要计算每个图像的大小和/或宽高比。我选择保持简单:

*   预览图像和大图像必须具有相同的纵横比*(见上文)*
*   预览图像将几乎立即定义容器的高度，因为它是内嵌的或者加载很快。

还是那句话，务实。如果您定义容器的宽度和高度，性能将会提高，但在包含大量图像的页面上，性能会特别明显，例如图库(所有图像都可能具有相同的纵横比)。

当加载完整图像并禁用点击时，容器上的`replace`类被删除。因此，我们可以删除标准链接指针:

```
a.progressive:not(.replace) {
  cursor: default;
}
```

容器内的预览和大图像根据容器宽度调整大小:

```
a.progressive img {
  display: block;
  width: 100%;
  max-width: none;
  height: auto;
  border: 0 none;
}
```

请注意，`height: auto`是必需的，否则 IE10/11 可能会误算图像的高度。

使用长度`2vw`对预览图像进行模糊处理，确保模糊量看起来相似，与页面尺寸无关。应用于容器的`overflow: hidden`为图像提供了一个硬边。它还被缩放 1.05 倍，以防止页面背景色透过图像模糊的外边缘显示。这也意味着我们可以使用令人满意的缩放效果来显示完整的图像。

```
a.progressive img.preview {
  filter: blur(2vw);
  transform: scale(1.05);
}
```

最后，我们定义完整图像显示时的样式和动画:

```
a.progressive img.reveal {
  position: absolute;
  left: 0;
  top: 0;
  will-change: transform, opacity;
  animation: reveal 1s ease-out;
}

@keyframes reveal {
  0% {transform: scale(1.05); opacity: 0;}
  100% {transform: scale(1); opacity: 1;}
}
```

完整图像位于预览上方，然后不透明度从 0 增加到 1，比例在一秒内从 1.05 变到 1。您可以根据需要应用其他变换和/或滤镜效果。

## JavaScript

我们正在练习负责任的渐进增强，因此 JavaScript 代码在向页面添加`load`事件监听器之前，首先检查所需的浏览器 API 是否可用:

```
// progressive-image.js
if (window.addEventListener && window.requestAnimationFrame && document.getElementsByClassName) window.addEventListener('load', function() {
```

当页面和所有资产完成加载时，触发`load`事件。我们不希望在字体、CSS、JavaScript 和预览图像等基本资源准备好之前就开始加载大图像(如果我们使用 DOM 准备好时触发的 [`DOMContentLoaded`](https://developer.mozilla.org/en/docs/Web/Events/DOMContentLoaded) 事件，就会发生这种情况)。

接下来，我们获取类名为`progressive`和`replace`的所有图像容器元素:

```
var pItem = document.getElementsByClassName('progressive replace'), timer;
```

[`getElementsByClassName()`](https://developer.mozilla.org/en-US/docs/Web/API/Document/getElementsByClassName) 返回一个动态的类似数组的 HTMLCollection，它随着相关元素在页面中的添加和删除而变化。好处很快就会显现出来。

接下来我们将定义一个`inView()`函数，通过比较其 [`getBoundingClientRect`](https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect) 位置和`window.pageYOffset`垂直滚动位置来确定每个容器是否在视窗中:

```
// image in view?
function inView() {
  var wT = window.pageYOffset, wB = wT + window.innerHeight, cRect, pT, pB, p = 0;
  while (p < pItem.length) {

    cRect = pItem[p].getBoundingClientRect();
    pT = wT + cRect.top;
    pB = pT + cRect.height;

    if (wT < pB && wB > pT) {
      loadFullImage(pItem[p]);
      pItem[p].classList.remove('replace');
    }
    else p++;
  }
}
```

当容器在视图中时，它的节点被传递给一个`loadFullImage()`函数，而`replace`类被删除。这会立即从`pItem` HTMLCollection 中删除节点，这样容器就不会再被重新处理。

`loadFullImage()`函数创建一个新的 HTML `Image()`对象，并根据需要设置其值，即通过将容器的`href`复制到`src`属性并应用一个`reveal`类:

```
// replace with full image
function loadFullImage(item) {
  if (!item || !item.href) return;

  // load image
  var img = new Image();
  if (item.dataset) {
    img.srcset = item.dataset.srcset || '';
    img.sizes = item.dataset.sizes || '';
  }
  img.src = item.href;
  img.className = 'reveal';
  if (img.complete) addImg();
  else img.onload = addImg;
```

一旦图像加载完毕，就会调用一个内部`addImg`函数:

```
// replace image
  function addImg() {
    // disable click
    item.addEventListener('click', function(e) { e.preventDefault(); }, false);

    // add full image
    item.appendChild(img).addEventListener('animationend', function(e) {
      // remove preview image
      var pImg = item.querySelector && item.querySelector('img.preview');

      if (pImg) {
        e.target.alt = pImg.alt || '';
        item.removeChild(pImg);
        e.target.classList.remove('reveal');
      }
    });
  }
}
```

此代码:

*   禁用容器上的单击事件
*   将图像附加到启动淡化/缩放动画的页面
*   使用`animationend`监听器等待动画结束，然后复制`alt`标签，删除预览图像节点，并从完整图像中删除`reveal`类。这一步有助于提高性能，还可以防止在调整 Edge 浏览器大小时出现一些奇怪的剪切问题。

最后，我们必须调用`inView()`函数来检查当它第一次运行时，页面上是否有任何渐进式图像容器可见:

```
inView();
```

我们还必须在页面滚动或浏览器调整大小时调用该函数。一些较老的浏览器*(是的，主要是 IE)* 可以非常迅速地对这些事件做出反应，所以我们将限制回调，以确保它不能每 300 毫秒调用一次以上:

```
window.addEventListener('scroll', scroller, false);
window.addEventListener('resize', scroller, false);

function scroller(e) {
  timer = timer || setTimeout(function() {
    timer = null;
    requestAnimationFrame(inView);
  }, 300);
}
```

注意对 [`requestAnimationFrame`](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame) 的调用，它在下一次重画之前运行`inView`。

## 响应图像

HTML5 图像`srcset`和`sizes`属性定义了不同尺寸和分辨率的多个图像。然后，浏览器会选择最适合该设备的版本。

上面的代码支持这个特性——向链接容器添加`data-srcset`和`data-sizes`属性，例如

```
<a href="small.jpg"
  data-srcset="small.jpg 800w, large.jpg 1200w"
  data-sizes="100vw"
  class="progressive replace">
  <img src="preview.jpg" class="preview" alt="image" />
</a>
```

加载后，完整的图像代码将是:

```
<img src="small.jpg"
    srcset="small.jpg 800w, large.jpg 1200w"
    sizes="100vw"
    alt="image" />
```

现代浏览器会在视口宽度为 800px 或更高时加载`large.jpg`。旧浏览器和视窗宽度较小的浏览器将接收到`small.jpg`。有关更多信息，请参考[如何使用 srcset](https://www.sitepoint.com/how-to-build-responsive-images-with-srcset/) 构建响应式映像。

## 使用说明

我将代码保持得很小，但是可以在任何项目中随意使用或改进它。要考虑的增强功能:

*   水平滚动检查。仅检查垂直滚动，因此水平平面中的所有图像都将被替换。
*   动态添加渐进图像。使用 JavaScript 添加到页面的渐进式图像只有在发生滚动或调整大小事件时才会被替换。
*   火狐性能。浏览器在替换大图像时可能会有困难，您可能会看到明显的闪烁。

如果你觉得有用，请发微博给我。