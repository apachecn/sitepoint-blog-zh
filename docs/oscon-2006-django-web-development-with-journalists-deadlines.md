# OSCON 2006: Django:记者截稿的网络开发

> 原文：<https://www.sitepoint.com/oscon-2006-django-web-development-with-journalists-deadlines/>

本周，凯文·杨克从俄勒冈州波特兰市的 [OSCON 2006](http://conferences.oreillynet.com/os2006/) 发回报道。

首席开发者 Jacob Kaplan-Moss 将 Django 视为 T2 Ruby on Rails T3 的竞争对手。Django 是一个 web 开发框架，诞生于堪萨斯州劳伦斯市的一家小型[社区报纸](http://www.lawrence.com/)，它最初是一个内容管理系统，专门用于在紧迫的时间期限内制作在线报纸。

像 Rails 一样，Django 利用了一种“替代”语言——具体来说就是[Python](http://www.python.org/)——的优势，提供了一个 web 开发平台，消除了特定类别的开发人员反复面临的许多烦恼。

与 Rails 不同，Django 并不严格遵循目前在 web 框架中非常流行的模型-视图-控制器(MVC)架构。django有模型吗，而且写起来极其简单。编写一个 Python 类，列出一系列字段、它们的类型和任何特殊属性(例如最大长度)，Django 将自动生成一个灵活的管理界面，它完全支持用户、用户级别和大量便利功能，例如从提交的图像中提取 EXIF 数据。

Django 的其余部分明显不像所有的 MVC 框架。一切都围绕着一个光滑的、设计者友好的模板引擎，模板可以在继承层次结构中组合在一起。这允许您的设计人员使用熟悉的 HTML 和 CSS，您的开发人员根据需要扩展它们以插入动态数据，而无需接触设计人员创建的文件。

Django 内置的其他细节:自动表单验证、一组通用视图，它们为构建您自己的管理界面奠定了坚实的基础、自动化联合(完全支持各种风格的 RSS 和 Atom)、访问者评论、完全国际化和本地化等等。

## 分享这篇文章