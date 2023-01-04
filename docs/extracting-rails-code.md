# 提取 Rails 代码

> 原文：<https://www.sitepoint.com/extracting-rails-code/>

Ryan Carson 发表了他对 Rails 创建者 David Heinemeier Hansson 的最新采访:其中 David 提到，好的框架应该是提取而不仅仅是抽象。这个[并不是第一次](http://www.37signals.com/svn/archives2/rails_11_loaded_with_37s_extractions.php)提到它的 [确切地说是](http://www.karmacrash.com/articles/2005/10/06/readingonrails-9-frameworks-are-extractions) [，但是你可能会觉得有趣的是，他指出 Rails 组件是高级组件膨胀的一个明显的罪魁祸首，我注意到我已经在许多场合重构了 Rails 的一部分。](http://www.loudthinking.com/arc/000407.html)

我经常听到请求插件来帮助多步表单，在刚刚实现了多步结帐过程之后，我可以告诉你这绝对不是一个简单的提取。仅仅是搞清楚表单验证和存储就够难的了——你是使用多个 ActiveRecord 类，一个 ActiveRecord 类还是 ActiveForm 对象；你是将它们存储在会话、cookies 还是数据库中；您是存储对象本身还是只存储属性？我认为有太多的意见你要么必须考虑，要么必须忽略，无论哪种方式，只记录不同的方法都比用代码库封装它们要好。

另一方面，在同一个项目中有几个很好的提取候选，要么是因为我重用了前一个项目中完全相同的代码，要么是打算在下一个项目中这样做。这些主要是*simple authorization*，应用程序范围的 HTTP 密码保护，用于应用程序开发的初始私人阶段；和 *AccessibleFormBuilder* ，用于管理标签和验证消息。

花更少的时间抽象是我真正努力的目标。Rails 已经彻底解决了基础问题，所以通常不值得花时间去寻找完美的插件——只管勇往直前，尽你所能实现它，只在之后的**提取好的部分，让它成功运行。**

## 分享这篇文章