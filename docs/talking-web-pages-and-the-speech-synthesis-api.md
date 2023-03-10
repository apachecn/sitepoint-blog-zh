# 会说话的网页和语音合成 API

> 原文：<https://www.sitepoint.com/talking-web-pages-and-the-speech-synthesis-api/>

几周前，我简单讨论了一下 <abbr title="Natural Language Processing">NLP</abbr> 及其相关技术。在处理自然语言时，有两个不同但互补的方面需要考虑:自动语音识别( <abbr title="Automatic Speech Recognition">ASR</abbr> )和文本到语音转换( <abbr title="Text-to-Speech">TTS</abbr> )。在介绍 Web Speech API 的文章[中，我讨论了 Web Speech API，这是一个在 Web 浏览器中提供语音输入和文本到语音输出功能的 API。您可能已经注意到，我只介绍了如何在网站中实现语音识别，而没有介绍语音合成。在本文中，我们将填补描述](https://www.sitepoint.com/introducing-web-speech-api/)[语音合成 API](https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html#tts-section) 的空白。

语音识别为用户，尤其是那些有残疾的用户，提供了向网站提供信息的机会。回顾我强调的用例:

> 在网站中，用户可以使用他们的声音来导航页面或填充表单域。用户还可以在开车时与页面互动，而无需将视线从路上移开。这些都不是微不足道的用例。

所以，我们可以把它看作是从用户到网站的渠道。语音合成则正好相反，让网站能够通过阅读文本向用户提供信息。这对盲人尤其有用，一般来说，对有视觉障碍的人也是如此。

语音合成和语音识别有一样多的使用案例。想想一些新汽车上安装的系统，这些系统可以读取你的短信或电子邮件，这样你就不必将视线从路上移开。使用电脑的视障人士对像 JAWS 这样的软件很熟悉，这些软件可以读取桌面上的任何内容，让他们执行任务。这些应用程序都很棒，但是要花很多钱。感谢[语音合成 API](https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html#tts-section) ，我们可以帮助人们使用我们的网站，不管他们是否有残疾。

举个例子，想象你正在你的博客上写一篇文章(就像我现在正在做的)，为了提高可读性，你把它分成几段。这不是用语音合成 API 的好机会吗？事实上，我们可以对我们的网站进行编程，这样，一旦用户将鼠标悬停(或聚焦)在文本上，屏幕上就会出现一个演讲者的图标。如果用户点击图标，我们调用一个函数来合成给定段落的文本。这是一个不小的进步。更好的是，它对开发人员来说开销很低，对用户来说没有开销。下面的 JS Bin 展示了这个概念的一个基本实现。

[语音合成 API 演示](http://jsbin.com/puteh/1/embed?html,css,js,output)