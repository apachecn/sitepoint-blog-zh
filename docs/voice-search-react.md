# 向 React 应用程序添加语音搜索

> 原文：<https://www.sitepoint.com/voice-search-react/>

**语音命令并不仅仅适用于谷歌或 Alexa 这样的助手。它们也可以添加到您的移动和桌面应用程序中，为您的最终用户提供额外的功能甚至乐趣。向应用程序添加语音命令或语音搜索非常简单。在本文中，我们将使用 Web Speech API 来构建一个语音控制的图书搜索应用程序。**

我们将要构建的完整代码可以在 [GitHub](https://github.com/sitepoint-editors/book-voice-search) 上获得。对于不耐烦的人来说，在文章的最后有一个我们将要构建的工作的[演示](#demo)。

## 网络语音 API 简介

在我们开始之前，重要的是要注意 Web Speech API 目前对浏览器的支持有限。要理解这篇文章，你需要**使用一个支持的浏览器**。