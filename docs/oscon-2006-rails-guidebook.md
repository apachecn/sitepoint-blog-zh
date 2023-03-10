# OSCON 2006:铁路指南

> 原文：<https://www.sitepoint.com/oscon-2006-rails-guidebook/>

迪夫·托马斯和安迪·亨特一起经营务实程序员公司。他和亨特合著了《实用程序员》和[编程 Ruby](http://www.rubycentral.com/book/) (又名《鹤嘴锄书》)。Mike Clark 是《敏捷 Web 开发》和《实用项目自动化》的合著者，也是《实用工作室:Ruby on Rails》的合作教师。

这个教程是在一个较大的房间里，并且(不出所料)非常满。它基本上只是一个扎实的、一步一步的 Rails 介绍——自然包括快速构建基本应用程序的标准 Rails 演示。除了对 Rails 应用程序结构的基本介绍(例如，什么文件放在哪里)以及如何构建 Rails 应用程序之外，他们还探讨了 Rails 开发的一些其他领域，我对此很感兴趣:

**迁移**

[迁移](http://wiki.rubyonrails.org/rails/pages/UnderstandingMigrations)提供了一种创建和修改数据库模式的过程化方法(在 Ruby 代码中)。如果您想要一种独立于数据库的方式来维护您的模式，这是非常有用的。它们也有助于让多个开发人员同步数据库的结构变化。迁移也是可逆的，因此如果您不喜欢更改，可以撤销更改。

**模型查找器方法**

他们演示了如何为`find`组合不同的参数。看到一些不同的方法对像我这样倾向于使用`find_by_sql`的人很有帮助。

**多对多关联**

众所周知，Rails 非常擅长管理简单的数据模型，包括基本的一对一和一对多关系。现在你可以更好地处理稍微复杂一点的设置。最初处理多对多关系的方法是一个简单的连接表，它不会出现在模型中的任何地方。较新的方法是通过关联''使用一个叫做['的东西，这是一个实际上放在模型中的对象，其中您还可以挂起关于关联的元数据(例如，一个订单有许多商品，每个商品关联都有一个单价和数量)。](http://wiki.rubyonrails.org/rails/pages/ThroughAssociations)

**生成 XML**

通过给你的模板一个“rxml”扩展，将你的结果集转换成 XML 是非常简单的，它使用一个可编程的[构建器模板](http://www.recentrambles.com/pragmatic/view/47)，而不是普通的标记和 RHTML 标签的混合。

**单纯宁静**

[Simply RESTful](http://www.artima.com/forums/flat.jsp?forum=123&thread=170554) 是一个用于路由的 Rails 插件，它将用于 REST 风格通信的 HTTP 动词’映射到 Rails 中常见 CRUD 操作的 URL 路径(例如，POST =‘create’，PUT =‘update’，DELETE =‘destroy’等)。).

## 分享这篇文章