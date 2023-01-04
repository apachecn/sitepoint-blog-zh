# 辛纳特拉闪光

> 原文：<https://www.sitepoint.com/sinatra-flash/>

flash 是 Rails 中一个方便的小助手方法，允许您在请求之间存储信息。当您的应用程序处理一个请求时，它通常以重定向到另一个 url 结束，这会生成另一个请求。闪存是一种类似哈希的对象，它存储错误消息等临时值，以便以后可以检索它们，通常是在下一次请求时。一旦信息被使用，它也会被删除。

Sinatra Flash 是由 Stephen Eley 编写的一个简洁的扩展。它包括 Rails Flash 的所有功能，但是，为了保持 Sinatra 的极简主义性质，它使用的代码要少得多。

## 装置

首先我们需要安装 sinatra-flash gem，就像这样:

```
gem install sinatra-flash 
```

要尝试基本功能，我们需要一个基本的 Sinatra 应用程序。创建一个名为“main.rb”的文件，其中包含以下代码: