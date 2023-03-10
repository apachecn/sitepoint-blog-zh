# 认识 Igaro 应用程序——潜在的 Web 最佳 SPA 框架

> 原文：<https://www.sitepoint.com/meet-igaro-app-webs-finest-spa-framework/>

有人说网页应该从 HTML 开始，HTML 被认为是(或者曾经被认为是)内容，功能应该加载在 HTML 之上(过滤等等)。对还是错？有了这么多动态生成的内容，也许是时候重新审视我们做网站设计的方式了。无论您倾向于哪种方式，后端将在很大程度上与十年前一样，但会有一些部分服务和新的协议支持。我们仍然面临着老问题:构建和呈现由多个片段组成的页面，并努力避免多次命中数据库服务器，这意味着要仔细考虑数据如何通过表示它们的模块。就前端而言，即使用户一开始从未加载过`x`和`x/y`，尝试将状态放入被服务的`/x/y/x` URL 也会很痛苦。

我一直认为一定有更好的方法，但是 JavaScript 或者运行它的各种设备从来没有真正将工作负载从后端转移到前端。是的，但不是全部。

然后他们就。

框架出现了；骨干，还在这里，但减少了，聚合物，下一个大事情——去年，角爆炸——现在闷烧，最近脸书的反应。我觉得 React 有所不同，它告诉我网络终于朝着正确的方向前进了。首先，它不使用模板，因此不需要双向数据绑定。

真正的架构决策不是使用什么样的模板语言，或者是否应该使用 TypeScript，或者是否应该使用框架，而是是否应该在服务器端呈现任何东西。一年前，它是强制性的，因为可以决定一个产品成败的服务 GoogleBot 不能处理一个网络应用。但是[现在它可以](https://developers.google.com/webmasters/ajax-crawling/docs/learn-more)了，所以我必须问你一个你在过去几年中已经问过自己很多次的问题:“我应该通过 Ajax 作为一个片段重新加载页面的那个部分，还是用前端代码重新生成那个片段(也就是 React)，或者用前端代码修改 DOM 结构，或者只是重新加载页面？”

那我再问你一个问题。现在是 2015 年。为什么我们还在问这个问题？

一年前，我开始着手寻找解决方案。

## Igaro 应用 JavaScript 框架简介

Igaro App 是一个 web 应用程序框架，也叫 SPA(单页应用程序)框架，但它完全背离了常规。首先，它没有使用 HTML 和 T2 MVC。它避免使用 DOM 查询选择器方法来提高性能，因此没有模板引擎。相反，它使用 routes 来构建页面，并使用标准 CSS 来进行样式和布局。

它是用纯 JavaScript ES3/5/6 编写的(没有依赖性),目标不是在几个方面，而是在所有方面超越和超越。它的目的是证明，通过跳出框框思考，有可能产生一个架构上的辉煌基础，在这个基础上，开发人员可以使用他们喜欢的 JavaScript 编码，而不是一些抽象的、字符串解析的衍生物。

大胆的想法和大胆的主张，你应该持怀疑态度。当你第一次浏览 Igaro 应用程序的[网站时，你可能会想知道居中对齐的“Apple like”销售页面在哪里，它有着花哨的大字体和基本图像。它本质上是不同的，因为网站是 web 应用程序，也是可下载的存储库。它很实用，所有的文档和小部件都内置在里面。](http://app.igaro.com/)

现在就开始克隆吧:

```
mkdir igaro 
git clone https://github.com/igaro/app.git igaro/git
npm install grunt-cli 
gem install compass 
cd igaro/git 
npm install 
grunt 
```

一旦克隆并运行，用户就有了一个准备好的开发环境。Igaro 编译成两种模式——调试和部署，每种模式的 web 服务器分别位于端口 3006 和 3007。这些将在您工作时自动重新加载。修改“页面”或路线就像编辑包含的`route.*`文件一样简单。要查看哪个路由文件映射到哪个页面，或者检查任何页面的源代码，只需单击 header 部分中的花括号。XHR 小部件允许无缝的 JSON 检索，并且可以通过检查任何包含的`instance.*`文件来创建跨多个页面的新小部件。

## Igaro App 剖析

### 按指定路线发送

如果你习惯于编写 HTML 和“连接”控制器，那么理解抛弃 MVC 的想法就有点困难了。在 Igaro 应用程序中，您必须考虑到 URL 包含它们的路线或数据。Root ( `/`)是基本路由，默认 repo 具有不可导航的路由—页眉、main(成为基本路由)和页脚。如果用户浏览到`/store`,该路线可以从文件或通过 API 加载，由提供商定义。无论哪种方式，JavaScript 都会创建嵌入到父路由或基本路由中的视图。下面显示了一个基本的“hello world”路线示例。

```
//# sourceURL=route.main.helloworld.js
module.requires = [
    { name:'route.main.helloworld.css' }
];
module.exports = function() {
 "use strict";
    return function(route) {
        var wrapper = route.wrapper,
            domMgr = route.managers.dom;
        domMgr.mk('p',wrapper, _tr("Hello World"));
    };
}; 
```

这里，在路由可用之前，加载了依赖模块(CSS)。使用路由上的管理器创建 DOM 元素，以便可以保持关系(破坏路由，破坏 DOM 元素)。当一个受祝福的对象(下一节将详细介绍)在 Igaro 中被销毁时，与之相关的所有东西都会被清理掉——事件、DOM 元素、子对象和循环引用。

Igaro 应用程序针对多个区域设置，`_tr`是默认的解析名称。

### 对象交互

Igaro App 引入了一个名为 bless 的[概念，它采用标准的 JavaScript 对象并用属性和助手来修饰它们。其中最重要的一个是对父对象的循环引用，这反过来意味着事件可以被传播(不用担心，析构函数事件会自动清除这些循环引用)。最终结果是，对象不需要被复制和扩展来继承其父对象的属性，因此内存消耗和性能得到了显著改善。](http://app.igaro.com/bless/)

bless 的另一个特点是带管理人员的装修。标准管理器是“dom”(允许 DOM 元素链接到对象并自动删除)、“debug”和“events”，而可选管理器如“store”允许任何对象本地/远程保存/检索数据。

这里值得一提的是，Igaro 中的大多数函数都返回一个承诺，并且整个代码库都是无回调的。代码以一种可读的方式流动，这种方式可以记录它自己。这里没有使用 JSDoc，而是在应用程序内置的手册中记录了每个函数及其参数。

### 插件

Igaro 应用程序附带了一个插件库,其中可能包含页面的小部件，并且可以根据需要从文件服务器或 API 加载。插件是 bog 标准的 JavaScript OO，bless 将它们连接在一起。该库目前在其库存中包括 XHR、存储、RTE(富文本数据输入)、手风琴、幻灯片、验证、祝酒(弹出通知)、页面消息、模态对话框、oauth2、导航和 JSONP。以下示例显示了如何向路线添加可隐藏的页面小部件。

```
route.managers.object.create('pagemessage',{
    container:wrapper,
    type:'info',
    message: _tr("Hide me and I'll not return."),
    hideable: true,
    id:route.path.join('.')+'.test01'
}) 
```

### 安全性和身份验证

Igaro 应用程序没有公共变量，也不会将自己加载到窗口对象中，因此它不能被其他脚本访问。

在包含的小部件集中有一个 [Oauth2 模块](http://app.igaro.com/modules/instance.oauth2/)，它支持透明认证，无需 URL 切换或重定向。Google Oauth2 的示例代码包含在文档中。对 API 的 XHR 调用不需要处理 401，因为它将被 Oauth2 模块拾取、处理，并且一旦提供了凭证，请求就会继续。在 Angular 中可以通过注入中间件来做到这一点。在 Igaro 应用程序中没有中间件，一切都是通过事件来完成的。下面的例子展示了如何向 Google API 发出请求。

```
return route.managers.object.create('xhr',{
    stash: { auth:'google' }
}).get({ res:'https://www.googleapis.com/drive/v2/files' }).then(function (data) {
   // data here
});
```

Promise catch 方法仅用于定制的错误处理，否则返回 Promise 将允许在基本级别处理错误。

### 构建和部署

[Grunt](http://gruntjs.com/) 用于工作流，并且(默认情况下)将 Igaro App 编译成两种模式:调试和部署。不同之处包括 JSMinify 和处理错误的方式。在 Igaro App 中，所有的错误都会得到处理，并以一种有意义的方式报告给用户。对于部署模式，可以将错误发送到远程服务器进行进一步分析。两种模式的实时编译都是自动的。

### 测试

为 web 应用程序中提供的所有页面提供了一个测试套件。假设任何错误都会调用标准响应，那么很容易测试一个页面是否完整无误地加载了。对 Igaro App 模块的单元测试正在进行中，需要进一步的覆盖，尽管在撰写本文时没有已知的错误。提供的 web 应用程序缺少 DOM 事件测试，但考虑到使用了[摩卡](https://mochajs.org/)、[守夜人](http://nightwatchjs.org/)和[硒驱动程序](http://www.seleniumhq.org/projects/webdriver/)，一旦你创建了自己的应用程序，包含自己的应用程序并不困难。

## 摘要

Igaro 应用程序可能是您一直在寻找的新鲜空气。它可能不是由谷歌或脸书赞助的，也可能不能很好地与您现有的代码库兼容，但它确实提供了一个优秀的架构，您可以在此基础上构建下一个。

在以后的文章中，我将通过使用标准的 OO JavaScript 和 Igaro 应用程序的 bless 创建一个 todomvc 小部件来提供对该框架的更多技术见解。

与此同时，克隆回购，给 Igaro 应用一个旋转。尽情享受吧！

## 分享这篇文章