# 使用 Sinatra 助手清理您的代码

> 原文：<https://www.sitepoint.com/using-sinatra-helpers-to-clean-up-your-code/>

![](img/d3323f882cb7739e025499896f376eed.png "shutterstock_19458208")

图片由 Shutterstock 提供

几个月前，我写了[一篇文章](https://www.sitepoint.com/sinatra-heroku-super-fast-deployment/)，讲述了我如何利用 [Sinatra](http://www.sinatrarb.com/) 快速建立起自己的[个人网站](http://daz4126.com/)。当我建立网站的时候，我开始思考将 JavaScript 文件添加到页面的最佳方式。玩了一段时间后，我最终使用了一些自定义的帮助器方法来将 JavaScript 文件添加到任何页面。

在 Sinatra 中，使用布局文件向页面添加 javascript 文件实际上非常容易。例如，如果您想在所有页面上包含 JQuery 和一个定制的 JavaScript 文件(名为`application.js`)，那么您需要做的就是在布局文件中添加以下代码行: