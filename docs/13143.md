# 宁静的铁轨。第一部分

> 原文：<https://www.sitepoint.com/restful-rails-part-i/>

RESTful Rails 一直是 Rails 核心中备受争议的部分，因为最初的 restful_rails 插件在一年前被合并到核心中。但是随着 Rails 2.0 的改进，它们将继续存在，所以理解它们是什么、它们如何工作以及何时(以及何时不)使用它们非常重要。这实际上是一个相当大的话题，我认为首先给你一些背景是值得的，所以我将这篇文章分成两部分——第一部分是理论性的，第二部分是实践性的。所以，戴上你的网络协议帽子，准备学习网络语言的内部工作原理。

## 休息与脏污

如果没有首字母缩略词，网络会是什么样子？ <abbr title="REpresentational State Transfer">REST</abbr> 代表**RE**presentational**S**state**T**transfer，描述了*资源*(在我们的例子中是 URL)，我们可以在这些资源上执行*动作*。 <abbr title="Create, Read, Update, Delete">CRUD</abbr> ，代表 **C** reate， **R** ead， **U** pdate， **D** elete，是我们执行的动作。尽管在 Rails 中，REST 和 CRUD 是最好的伙伴，但这两者可以独立工作。事实上，每次你写了一个允许你从数据库中添加、编辑和删除条目的后端系统，和一个允许你查看这些条目的前端系统，你都是在使用 CRUD。

## HTTP 动词

任何必须处理表单数据的 web 开发人员都知道 GET 和 POST 方法。前者将通过类似下面的查询字符串向服务器提交数据？key2=value2&key2=value2，后者通过 HTTP 头发送数据。你可能没有意识到，每次你加载一个页面(这不是一种 POST 类型的形式)时，你都在发出一个 GET 请求。所以当你点击 RSS 阅读器上的链接时，你就发出了一个 GET 请求。

还有两个动词你可能没听说过，因为浏览器不支持它们:PUT 和 DELETE。删除动作是显而易见的——如果指示服务器删除某些东西。PUT 指令稍微隐晦一些——从通过头发送数据的角度来看，它与 POST 动作相同，但是它被设计来修改某些东西(如果对象还不存在，它也应该创建一个新的对象，但是 Rails 在技术上忽略了这一点)。

更精明的人可能已经注意到 GET 听起来很像 READ，POST 看起来像 CREATE，PUT 和 UPDATE 一样，DELETE 是 DELETE。如果是的话，直接移到类的开头——这正是 Rails 在使用 RESTful rails 时使用的关联。

## HTTP 响应代码

每个人都知道，如果你试图访问一个不存在的页面，服务器会把你扔回来 *404 Page not Found* ，或者如果你弄乱了你的 rails 代码，你会得到一个 *500 应用错误*。这些是服务器发回的响应代码，让您的浏览器知道发生了什么。200-299 之间的任何值表示请求成功，300-399 表示请求正常，但是您需要做其他事情，400-499 是一个错误，500-599 是一个非常严重的错误。正如有匹配 RESTful 请求的动词一样，也有对应 RESTful 响应的响应代码。

200 OK

You will get from a GET, PUT or a DELETE request. It means that the request checked out and the appropriate action has been taken

201 Created

Notifies you that the POST command successfully created the posted object.

404 Not found

Means the request resource wasn’t found. You can get this from a GET, PUT or DELETE.

406 Not Acceptable

The verb isn’t allowed at the resources you reqested (More on this next part)

500 Internal Server Error

Something went horribly wrong

## 资源

您将习惯于看到类似/posts/view/2 的 Rails URLs 大致意思是“请让我查看 id 号为 2 的帖子”。RESTful 资源非常相似，因为它们有一个控制器和(可能)id。他们通常没有动作，因为它是 HTTP 动词所固有的。

为了实现这一点，Rails 团队在定义资源的控制器中定义了许多特殊的方法——在本例中是 posts(即。xml 位将在下一部分变得清晰)。

| 方法 | 资源 | 动词 | 说明 |
| 指数 | /posts.xml | 得到 | 返回所有项目 |
| 显示 | /posts/1.xml | 得到 | 返回 id = 1 的单个项目 |
| 创造 | /posts.xml | 邮政 | 创建项目 |
| 更新 | /posts/1.xml | 放 | 更新 id = 1 的项目 |
| 删除 | /posts/1.xml | 删除 | 删除 id = 1 的项目 |

实际上，您可以定义额外的动作(您不能让每个网站都符合这种模式)，我将在下一篇文章中讨论这些。

因此，在下一部分，我们将看看如何创建一个 RESTful rails 项目，以及如何将所有东西连接起来。在那之前…

## 分享这篇文章