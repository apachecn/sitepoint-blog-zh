# 嵌入(HTML 元素)

> 原文：<https://www.sitepoint.com/embed-html-element/>

## 描述

`embed`是一个非标准但支持良好的元素，用于嵌入多媒体内容，包括浏览器通常不支持的媒体类型。它还可以用于嵌入支持的媒体类型，如中的图像。jpg，。gif，或者。png 格式。

不是任何当前公认标准的一部分，所以如果你使用它，你的页面不可能被验证；为了创建有效的标记，您需要使用在 HTML 4 中定义的[对象](https://reference.sitepoint.com/html/object "Object")元素。也就是说，使用`embed`被广泛认为是以可访问的方式嵌入 Flash 的好方法，许多人认为为了可访问性而牺牲有效性是可以接受的。

`embed`不适用于浏览器本身支持的格式，如 HTML 和图像——使用内置的、适当支持的机制来处理这些格式。`embed`支持的格式通常仅限于那些需要额外插件才能工作的格式。

虽然`embed`是一个空元素，但是为了浏览器的兼容性，仍然需要一个结束标记。

## 例子

在这个例子中，一个非常简单的`embed`元素被用来显示一个 .mp4 格式的视频文件:

```
<embed src="volksworld-video-report.mp4">
</embed>
```

## 将此用于…

此元素用于媒体文件(主要是电影文件，如 Flash 和 QuickTime 视频)。

## 在本节中

*   **[对齐](https://reference.sitepoint.com/html/embed/align)**
    嵌入内容相对于周围内容的对齐/位置
*   **[alt](https://reference.sitepoint.com/html/embed/alt)**
    用于嵌入内容的替代文本
*   **[边框](https://reference.sitepoint.com/html/embed/border)**
    设置嵌入内容周围的边框粗细
*   **[高度](https://reference.sitepoint.com/html/embed/height)**
    设置嵌入内容的高度
*   将嵌入内容设置为不可见
*   **[hspace](https://reference.sitepoint.com/html/embed/hspace)**
    为嵌入内容设置水平(任一侧)空间量
*   **[名称](https://reference.sitepoint.com/html/embed/name)**
    引用嵌入元素的名称
*   **[插件页面](https://reference.sitepoint.com/html/embed/pluginspage)**
    定义所需插件的位置
*   **[类型](https://reference.sitepoint.com/html/embed/type)**
    为嵌入内容定义了 MIME 类型
*   **[vspace](https://reference.sitepoint.com/html/embed/vspace)**
    为对象设置垂直(顶部和底部)空间量

## 分享这篇文章