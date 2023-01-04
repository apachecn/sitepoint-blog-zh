# 通过网络音频 API 使用傅立叶变换

> 原文：<https://www.sitepoint.com/using-fourier-transforms-web-audio-api/>

Web Audio API 让 JavaScript 程序员可以轻松地进行声音处理和合成。在本文中，我们将介绍自定义振荡器，这是 Web Audio API 的一个鲜为人知的功能，它可以轻松地将傅立叶变换用于在浏览器中合成独特的声音效果。

## 网络音频振荡器

Web Audio API 允许您编写音频元素的图形来产生声音。振荡器就是这样一种元件——产生纯音频信号的声源。你可以设置它的频率和类型，可以是正弦、方波、锯齿波或三角波，但正如我们将要看到的，还有一个强大的自定义类型。

首先，让我们试试标准振荡器。我们只需将其频率设置为 440 Hz，音乐家会将其识别为 A4 音符，我们还包括一个类型选择器，让您听到正弦、方波、锯齿波和三角波之间的差异。

参见 [CodePen](http://codepen.io) 上 Seb Molines ( [@Clafou](http://codepen.io/Clafou) )的 Pen [网络音频振荡器](http://codepen.io/Clafou/pen/Ajzaq/)。