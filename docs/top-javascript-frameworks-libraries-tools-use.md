# 2019 年最佳 JavaScript 框架、库和工具

> 原文：<https://www.sitepoint.com/top-javascript-frameworks-libraries-tools-use/>

似乎 JavaScript 框架、库和工具的数量比开发人员的数量还要多。2018 年底，在 GitHub 上进行[快速搜索会发现 230 万个 JavaScript 项目。`npm`已经成为](https://github.com/search?utf8=%E2%9C%93&q=language:JavaScript&type=Repositories&ref=advsearch&l=JavaScript&l=)[世界上最大的模块系统](http://www.modulecounts.com/)，在[npmjs.com](https://www.npmjs.com/)上有 70 万个可用包，每个月有数十亿次下载。

> **2018.12.06:** 这篇文章被更新以反映 JavaScript 生态系统的当前状态。今年的更新由 [Sencha](https://www.sencha.com) 赞助。感谢您对使 SitePoint 成为可能的合作伙伴的支持！

本文试图解释最流行的客户端 JavaScript 框架、库和工具之间的基础和基本差异。他们是否“最适合”你是另一个问题。选择一件事，坚持一段时间。请注意，无论你选择什么，你最喜欢的选项都会被“更好”的东西取代！

### 目录 

*   [目录](#tableofcontents)
*   [棘手的术语](#trickyterminology)
*   [图书馆](#libraries)
*   [框架](#frameworks)
*   [工具](#tools)
*   不要给我贴标签！
*   [JavaScript 框架和库](#javascriptframeworksandlibraries)
*   [jQuery](#jquery)
*   [反应](#react)
*   [角为 1.x](#angularjs1x)
*   [角度 2+(现为 7.x)](#angular2now7x)
*   vista . js
*   [Sencha Ext JS](#senchaextjs)
*   [连字符和下划线](#lodashandunderscore)
*   [Backbone.js](#backbonejs)
*   [Ember.js](#emberjs)
*   [Knockout.js](#knockoutjs)
*   [值得注意的提及](#notablementions)
*   [工具:通用任务运行器](#toolsgeneralpurposetaskrunners)
*   [网络包](#webpack)
*   [Gulp.js](#gulpjs)
*   [npm](#npm)
*   [咕噜声](#grunt)
*   [工具:模块捆绑器](#toolsmodulebundlers)
*   [浏览确认](#browserify)
*   [要求](#requirejs)
*   [工具:林挺](#toolslinting)
*   [ESLint](#eslint)
*   [JSHint](#jshint)
*   [JSLint](#jslint)
*   [工具:测试套件](#toolstestsuites)
*   [是](#jest)
*   [摩卡](#mocha)
*   [茉莉](#jasmine)
*   [工具:杂项](#toolsmiscellaneous)
*   [总结和建议](#summaryandrecommendations)
*   [评论](#comments)

在阅读本文之前，请接受以下条款和条件！…

*   JavaScript 的前景每天都在变化。这篇文章一发表就过时了！
*   我说的*是指*“最受欢迎/使用/炒作的通用项目”*。所有都有免费/开源选项，但列表中可能不包括您的最爱。*
**   停止的项目，如 [YUI](https://yuilibrary.com/) 不包括在内，即使他们可能仍然在网络上有很高的使用率。*   只引用客户端项目。一些*可以*在服务器端工作，但是这个列表不包括纯粹的基于服务器的框架，比如 [Express.js](https://expressjs.com/) 或者[哈比神](https://hapijs.com/)。*   每个项目的信息都是有意简短的，以便为进一步的研究提供一个概述。*   每个项目都提供了一个使用普及指标，但众所周知，统计数据很难整理，而且可能会产生误导。*   我有偏见。你有偏见。其他人都有偏见！我还没有尝试这里的每一个工具，我将宣布我的最爱，但你应该根据你的需求做出自己的评估。*   我和 SitePoint 都不对你做出的任何灾难性的决定负责！*

 *## 复杂的术语

术语“框架”、“库”和“工具”在不同的时间和环境下对不同的人有不同的含义。这里使用的一般定义:

### 图书馆

一个**库**是有用功能的有组织的集合。一个典型的库可以包括处理字符串、日期、HTML DOM 元素、事件、cookies、动画、网络请求等等的函数。每个函数都向调用应用程序返回值，这些值可以按照您选择的方式实现。把它想象成一个汽车部件的选择:你可以自由地使用任何一个来帮助建造一辆可以工作的汽车，但是你必须自己制造引擎。

库通常提供了更高层次的抽象，可以消除实现细节和不一致性。例如，可以使用 [XMLHttpRequest API](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) 来实现 Ajax，但是这需要几行代码，而且不同浏览器之间有细微的差别。一个库可能会提供一个更简单的`ajax()`函数，这样你就可以专注于更高层次的业务逻辑。

一个库可以减少 20%的开发时间，因为你不必担心更细微的细节。缺点是:

*   库中的 bug 可能很难定位和修复
*   不能保证开发团队会很快发布补丁
*   一个补丁可能会改变 API 并导致你的代码发生重大变化。

### 结构

一个**框架**是一个应用程序框架。它要求你以一种特定的方式进行软件设计，并在某些点上插入你自己的逻辑。通常会为您提供事件、存储和数据绑定等功能。用汽车做类比，框架提供了工作底盘、车身和引擎。你可以添加，删除或修补一些部件，假设车辆仍在运行。

框架通常比库提供更高层次的抽象，可以帮助你快速构建项目的前 80%。缺点是:

*   如果您的应用程序超出了框架的界限，那么最后的 20%可能会很困难
*   框架更新或迁移可能会很困难—*如果不是不可能的话*
*   核心框架代码和概念很少会过时。开发人员总会找到更好的方法来做同样的事情。

### 工具

一个**工具**有助于开发，但不是你的项目的一个组成部分。工具包括构建系统、编译器、传输器、代码精简器、图像压缩器、部署机制等等。

工具应该提供更简单的开发过程。例如，许多编码人员更喜欢 [Sass](http://sass-lang.com/) 而不是 CSS，因为它提供了代码分离、嵌套、渲染时变量、循环和函数。浏览器不理解 Sass/SCSS 语法，因此在测试和部署之前，必须使用适当的工具将代码编译成 CSS。

### 不要给我贴标签！

库、框架和工具之间的区别很少是清晰的。一个框架可以包括一个库。一个库可以实现类似框架的方法。工具对两者都是必不可少的。我试图给每个项目贴上标签，但范围可能会有所不同。

如果这听起来太复杂，你可以考虑编写普通的 JavaScript 代码。这很好，但是您将不可避免地编写您自己的库和/或必须维护的框架代码。JavaScript 本身是浏览器和操作系统抽象塔上的抽象！

## JavaScript 框架和库

按使用/流行/宣传的大致顺序排列的项目…

### jQuery

<colgroup><col> <col></colgroup> 
| jQuery | 描述 |
| --- | --- |
| 类型 | 图书馆 |
| 网站 | [jquery.com](http://jquery.com/) |
| 仓库 | [github.com/jquery/jquery](https://github.com/jquery/jquery) |
| GitHub stars | 50,000+ |
| 当前版本 | 3.3.1 |
| 开发者 | jQuery 团队 |
| 发射日期 | 2006 年 8 月 |
| 典型尺寸 | 最低 30kb |
| 典型用途 | 通用 |
| 使用 | [所有网站的 73.5%](https://w3techs.com/technologies/details/js-jquery/all/all) |

jQuery 仍然是最常用的 JavaScript 库，并与 WordPress、ASP.NET 和其他几个框架一起发布。它革新了客户端开发，将 CSS 选择器引入 DOM 节点检索，并链接应用事件处理程序、动画和 Ajax 调用。

近年来，jQuery 已经不再受到开发人员的青睐，但使用率仍然很高。对于需要少量 JavaScript 功能的项目，它仍然是一个可行的选择。

优点:

*   小分布尺寸
*   浅的学习曲线，可观的在线帮助
*   简洁的语法
*   易于扩展

缺点:

*   增加了本机 API 的速度开销
*   既然浏览器兼容性已经提高，就不那么重要了
*   用法已经持平了
*   一些[行业](https://www.sitepoint.com/do-you-really-need-jquery/?aref=cbuckler) [强烈反对不必要的使用](http://youmightnotneedjquery.com/)。

### 反应

<colgroup><col> <col></colgroup> 
| 反应 | 描述 |
| --- | --- |
| 类型 | 图书馆 |
| 网站 | [facebook.github.io/react/](https://facebook.github.io/react/) |
| 仓库 | [github.com/facebook/react](https://github.com/facebook/react) |
| GitHub stars | 115,000+ |
| 当前版本 | 16.6.3 |
| 开发者 | 脸书和贡献者 |
| 发射日期 | 2013 年 3 月 |
| 典型尺寸 | 最小 21kb |
| 典型用途 | 单页应用程序 |
| 使用 | [低](https://w3techs.com/technologies/details/js-react/all/all) |

或许是过去几年中谈论最多的库，React 声称是一个用于构建用户界面的 JavaScript 库。它侧重于模型-视图-控制器(MVC)开发的“视图”部分，并使创建保留状态的 UI 组件变得容易。它是最早实现虚拟 DOM 的图书馆之一；内存中的结构计算差异并有效地更新页面。

React 的使用率在统计中似乎很低，可能是因为它被用于应用程序而不是网站。几乎 70%的开发人员声称有一些使用库的经验。

优点:

*   小巧、高效、快速且灵活
*   简单组件模型
*   良好的文档和在线资源
*   服务器端呈现是可能的
*   仍然受欢迎并且增长迅速
*   很多招聘 React 开发人员的广告

缺点:

*   要学习的新概念和语法
*   构建工具必不可少
*   可能需要其他库或框架来提供模型和控制器方面
*   可能与修改 DOM 的代码和其他库不兼容

### 安圭拉 1.x

<colgroup><col> <col></colgroup> 
| 安古斯 | 描述 |
| --- | --- |
| 类型 | 结构 |
| 网站 | [安圭拉语. org](https://angularjs.org/) |
| 仓库 | [github.com/angular/angular.js](https://github.com/angular/angular.js) |
| GitHub stars | 60,000+ |
| 当前版本 | 1.7.5 |
| 开发者 | 谷歌 |
| 发射日期 | 2010 年十月 |
| 典型尺寸 | 144kb |
| 典型用途 | 单页应用程序 |
| 使用 | [低](https://w3techs.com/technologies/details/js-angularjs/all/all) |

Angular 是第一个出现在这个列表中的框架——或者说 *MVC 应用框架*。最流行的版本仍然是 1.x 版，它用双向数据绑定扩展了 HTML，同时将 DOM 操作从应用程序逻辑中分离出来。

Angular 1.x 尽管发布了版本 2(现在是版本 4！)迷茫？见下文…

优点:

*   几个大公司采用的流行框架
*   生产现代 web 应用程序的单一解决方案
*   “标准”意味着堆栈的一部分(MongoDB，Express。JS，AngularJS，NodeJS)有这么多的文章和教程

缺点:

*   比一些替代产品更陡峭的学习曲线
*   大型代码库
*   无法升级到 Angular 2.x
*   尽管是谷歌的项目，谷歌似乎并没有使用它？

### 角度 2+(现在是 7.x)

<colgroup><col> <col></colgroup> 
| 有角的 | 描述 |
| --- | --- |
| 类型 | 结构 |
| 网站 | [angular.io](https://angular.io/) |
| 仓库 | [github.com/angular/angular](https://github.com/angular/angular) |
| GitHub stars | 43,000+ |
| 当前版本 | Seven point one |
| 开发者 | 谷歌 |
| 发射日期 | 2016 年 9 月 |
| 典型尺寸 | 最低 450kb |
| 典型用途 | 单页应用程序 |
| 使用 | [低](https://w3techs.com/technologies/details/js-angularjs/all/all) |

Angular 2.0 于 2016 年 9 月发布。这是一次彻底的重写，引入了用 TypeScript(编译成 JavaScript)创建的模块化的基于组件的模型。更令人困惑的是，2017 年 3 月发布了 4.0 版本(为了避免语义版本问题，跳过了 v3)。

Angular 2+与 v1 截然不同。两者都不兼容——也许谷歌应该给这个项目取一个不同的名字？！

优点:

*   生产现代 web 应用程序的单一解决方案
*   仍然是平均堆栈的一部分，尽管可用的 [Angular 2+教程](https://www.sitepoint.com/angular-2-tutorial/)较少
*   TypeScript 为那些熟悉静态类型语言(如 C#和 Java)的人提供了一些优势。

缺点:

*   比一些替代产品更陡峭的学习曲线
*   大型代码库
*   无法从 Angular 1.x 升级
*   与 1.x 相比，Angular 2.x 的使用率相对较低
*   尽管是谷歌的项目，谷歌似乎并没有使用它？

### view . js-检视. js

<colgroup><col> <col></colgroup> 
| view . js-检视. js | 描述 |
| --- | --- |
| 类型 | 结构 |
| 网站 | vuej . org |
| 仓库 | [github . com/vuej/view](https://github.com/vuejs/vue) |
| GitHub stars | 120,000+ |
| 当前版本 | 2.5.17 |
| 开发者 | 尤雨溪 |
| 发射日期 | 2014 年 2 月 |
| 典型尺寸 | 最小 19kb |
| 典型用途 | 组件和单页应用程序 |
| 使用 | [低](https://w3techs.com/technologies/details/js-vuejs/all/all) |

Vue.js 是一个用于构建用户界面的轻量级渐进框架。核心提供了一个类似 React 的虚拟 DOM 视图层，它可以与其他库集成，但也能够支持单页面应用程序。这个框架是由尤雨溪创建的，他以前在 AngularJS 工作，但想提取他喜欢的部分。

Vue.js 使用 HTML 模板语法将 DOM 绑定到实例数据。模型是普通的 JavaScript 对象，当数据改变时更新视图。附加工具为搭建、路由、状态管理、动画等提供了便利。

优点:

*   快速采用和日益普及
*   易于上手，开发人员满意度高
*   轻量级、小依赖性和良好的性能

缺点:

*   会被 React 掩盖
*   在一定程度上依赖单个开发人员进行更新
*   资源少于替代品

### Sencha Ext JS

<colgroup><col> <col></colgroup> 
| extjs | 描述 |
| --- | --- |
| 类型 | 框架和组件库 |
| 网站 | [sencha.com/products/extjs/](https://www.sencha.com/products/extjs/) |
| 当前版本 | 6.6.0 |
| 开发者 | 森查 |
| 发射日期 | 二零零七年十二月 |
| 典型尺寸 | 取决于要求:85–500 kb |
| 典型用途 | 组件和单页应用程序 |
| 使用 | [低](https://w3techs.com/technologies/details/js-extjs/all/all) |

Ext JS 由 YUI-Ext 演变而来，在这个名单上有着最长的历史。尽管 Ext JS 以其广泛的可配置、可访问、跨浏览器的 UI 组件和数据可视化工具而闻名，但它也为构建完整的应用程序提供了一个框架。或者，可以将组件库与 React 或 Angular 一起使用。

Ext JS 是这里唯一提供商业培训和支持的框架。还有选择让 Sencha 团队帮助审查您的代码，自动化测试，并迁移到其他平台。

优点:

*   各种可用的组件
*   包含创建 web 和移动应用程序所需的一切
*   脚本定制、设计工具和快速原型
*   提供可视化构建和设计 web 应用程序的工具
*   商业支持和[优秀的文档](https://docs.sencha.com/extjs/6.6.0/)
*   [30 天免费试用](https://www.sencha.com/products/extjs/evaluate/)和[社区版](https://www.sencha.com/products/extjs/communityedition/)适用于较小的应用程序(年收入低于 10，000 美元)

缺点:

*   到目前为止，与 React 和 Angular 之外的框架集成可能是不切实际的——参见 Ext JS 7 的路线图计划
*   深度定制 UI 更改可能很困难

### Lodash 和下划线

<colgroup><col> <col></colgroup> 
| 洛达什 | 描述 |
| --- | --- |
| 类型 | 图书馆 |
| 网站 | [lodash.com/](https://lodash.com/) |
| 仓库 | [github.com/lodash/lodash/](https://github.com/lodash/lodash/) |
| GitHub stars | 35,000+ |
| 当前版本 | 4.17.11 |
| 开发者 | 约翰-大卫·道尔顿 |
| 发射日期 | 2012 年 4 月 |
| 典型尺寸 | 4kb–最小 24kb |
| 典型用途 | 通用 |
| 使用 | [低](https://w3techs.com/technologies/details/js-lodash/all/all) |

<colgroup><col> <col></colgroup> 
| 强调 | 描述 |
| --- | --- |
| 类型 | 图书馆 |
| 网站 | [underscorejs.org/](http://underscorejs.org/) |
| 仓库 | [github.com/jashkenas/underscore](https://github.com/jashkenas/underscore) |
| GitHub stars | 24,000+ |
| 当前版本 | 1.8.3 |
| 开发者 | 杰里米·阿什肯纳斯 |
| 发射日期 | 2009 年十月 |
| 典型尺寸 | 最小 6kb |
| 典型用途 | 通用 |
| 使用 | [低](https://w3techs.com/technologies/details/js-underscore/all/all) |

Lodash 和下划线在本节中合并在一起，因为它们提供了数百个功能性的 JavaScript 实用程序来补充原生的字符串、数字、数组和其他基本对象方法。有一些重叠，所以你不太可能在一个项目中需要两个库。

客户端使用率很低，但是服务器端 Node.js 应用程序可以采用这两个库。

优点:

*   小而简单
*   易于学习，有很好的文档
*   与大多数库和框架兼容
*   不扩展内置对象
*   可以在客户端或服务器上使用

缺点:

*   一些方法在 ES2015 和更高版本的 JavaScript 中可用。

### 骨干网. js

<colgroup><col> <col></colgroup> 
| 骨干网. js | 描述 |
| --- | --- |
| 类型 | 结构 |
| 网站 | [backbonejs.org](http://backbonejs.org/) |
| 仓库 | [github.com/jashkenas/backbone/](https://github.com/jashkenas/backbone/) |
| GitHub stars | 37,000+ |
| 当前版本 | 1.3.3 |
| 开发者 | 杰里米·阿什肯纳斯 |
| 发射日期 | 2010 年十月 |
| 典型尺寸 | 最小 8kb |
| 典型用途 | 单页应用程序 |
| 使用 | [低](https://w3techs.com/technologies/details/js-backbone/all/all) |

Backbone.js 是最早提供服务器端框架中常见的 MVC 结构的客户端选项之一。它唯一的依赖项是由同一个开发者创建的[下划线. js](#lodashandunderscore) 。

Backbone.js 自称是一个库，因为它可以与其他项目集成。我怀疑大多数开发人员认为它是一个框架，尽管没有其他人那么固执己见。

优点:

*   体积小、重量轻且不复杂
*   不向 HTML 添加逻辑
*   出色的文档
*   被许多应用程序采用，包括 Trello、WordPress.com、LinkedIn 和 Groupon

缺点:

*   比 AngularJS 等替代方案更低的抽象级别(尽管这可能被认为是一种优势)
*   需要额外的组件来实现数据绑定等功能
*   最近的框架已经脱离了 MVC 架构

### Ember.js

<colgroup><col> <col></colgroup> 
| Ember.js | 描述 |
| --- | --- |
| 类型 | 结构 |
| 网站 | [人类. com](https://emberjs.com/) |
| 仓库 | [github . com/humans/humans . js】的缩写](https://github.com/emberjs/ember.js) |
| GitHub stars | 20,000+ |
| 当前版本 | 3.6.0 |
| 开发者 | 烬队 |
| 发射日期 | 2011 年 12 月 |
| 典型尺寸 | 最小 95kb |
| 典型用途 | 单页应用程序 |
| 使用 | [低](https://w3techs.com/technologies/details/js-emberjs/all/all) |

js 是基于模型-视图-视图模型(MVVM)模式的大型固执己见的框架之一。它在一个包中实现了模板、数据绑定和库。有 Ruby on Rails 经验的人会很快熟悉“约定胜于配置”的概念。

优点:

*   为客户端应用程序提供单一解决方案
*   开发人员可以立即提高工作效率——它使用 jQuery
*   良好的向后兼容性和升级选项
*   采用了现代 web 开发标准

缺点:

*   大量可分配的
*   与其他向更小组件结构发展的框架相比，它被认为是一个整体
*   陡峭的学习曲线——这是 Ember 的方式或没有方式

### Knockout.js

<colgroup><col> <col></colgroup> 
| Knockout.js | 描述 |
| --- | --- |
| 类型 | 结构 |
| 网站 | [knockoutjs.com](http://knockoutjs.com) |
| 仓库 | [github.com/knockout/knockout](https://github.com/knockout/knockout) |
| GitHub stars | 9,000+ |
| 当前版本 | 3.5.0 |
| 开发者 | 史蒂夫·桑德森 |
| 发射日期 | 2010 年 7 月 |
| 典型尺寸 | 最小 59kb |
| 典型用途 | 单页应用程序 |
| 使用 | [低](https://w3techs.com/technologies/details/js-knockout/all/all) |

作为较早的 MVVM 框架之一，Knockout.js 使用观察器来确保 UI 与底层数据保持同步。它的特点是模板和依赖跟踪。

优点:

*   小巧轻便，无依赖性
*   出色的浏览器支持可追溯到 IE6
*   良好的文档

缺点:

*   大型项目可能会变得复杂
*   发展已经放缓
*   使用率似乎在下降

### 值得注意的提及

还想吃吗？以下项目不太受欢迎，但值得考虑:

*   [Polymer](https://www.polymer-project.org/)–一个支持跨浏览器 HTML5 web 组件的库
*   [Meteor](https://www.meteor.com/)–web 应用的全栈平台。
*   Aurelia–一个轻量级的跨平台框架
*   一个相当新的项目，将框架源代码转换成类似香草的 JavaScript
*   [conditor . js](http://conditionerjs.com/)——一个新的库，根据状态自动加载和卸载模块。

## 工具:通用任务运行器

构建工具自动执行各种 web 开发任务，如预处理、编译、模块捆绑、图像优化、代码精简、林挺和运行测试。任务通常在单个可执行包中一起管理。最受欢迎的选项:

### 网络包

<colgroup><col> <col></colgroup> 
| 网络包 | 描述 |
| --- | --- |
| 网站 | [网页包. js.org](https://webpack.js.org/) |
| 仓库 | [github.com/webpack/webpack](https://github.com/webpack/webpack) |
| GitHub stars | 45,000+ |
| 当前版本 | 4.25.1 |
| 每周下载量 | 四百万 |

Webpack 支持所有流行的模块选项，并且已经成为 React 开发的同义词。虽然自称是模块捆绑器，但 Webpack 可以作为通用任务运行器。基于 JavaScript 对象的配置可能有点笨拙。[生成器可用](https://generatewebpackconfig.netlify.com/)，但是一些 Webpack 用户已经迁移到[包](https://github.com/parcel-bundler/parcel)以便更容易配置和改进编译时间。

### Gulp.js

<colgroup><col> <col></colgroup> 
| Gulp.js | 描述 |
| --- | --- |
| 网站 | [gulpjs.com](http://gulpjs.com/) |
| 仓库 | [github.com/gulpjs/gulp](https://github.com/gulpjs/gulp) |
| GitHub stars | 30,000+ |
| 当前版本 | 4.0.0 (3.9.1 仍处于活动状态) |
| 每周下载量 | 750,000+ |

虽然它不是第一个任务运行者，但 Gulp 很快成为最受欢迎的一个，也是我个人最喜欢的一个。Gulp 使用易于阅读的 JavaScript 代码，将源文件加载到流中，并在输出到构建文件夹之前通过各种插件传输数据。它简单、快速且有趣，但开发者一直在向 Webpack 迁移。

### npm

<colgroup><col> <col></colgroup> 
| npm | 描述 |
| --- | --- |
| 网站 | [npmjs.com](https://www.npmjs.com/) |
| 仓库 | [github.com/npm/npm](https://github.com/npm/npm) |
| GitHub stars | 17,000+ |
| 当前版本 | 6.4.1 |
| 每周下载量 | 一百万 |

npm 是 Node.js 包管理器，但是它的脚本工具可以用于运行的[通用任务。对于较少依赖的简单项目来说，这是一个很有吸引力的选择。然而，更复杂的任务可能很快变得不切实际。](https://www.sitepoint.com/guide-to-npm-as-a-build-tool/?aref=cbuckler)

### 咕哝

<colgroup><col> <col></colgroup> 
| 咕哝 | 描述 |
| --- | --- |
| 网站 | [gruntjs.com](https://gruntjs.com/) |
| 仓库 | [github.com/gruntjs/grunt](https://github.com/gruntjs/grunt) |
| GitHub stars | 17,000+ |
| 当前版本 | 1.0.3 |
| 每周下载量 | Four hundred and twenty-five thousand |

Grunt 是最早实现大规模采用的 JavaScript 任务运行程序之一，但速度和复杂的 JSON 配置导致了 Gulp 的兴起。最糟糕的问题已经解决，咕哝仍然是一个受欢迎的选择。

## 工具:模块捆绑器

管理多个 JavaScript 文件可能会变得很麻烦。除非您可以本机采用相对较新的 [ES6 模块导入语法](https://www.sitepoint.com/understanding-es6-modules/?aref=cbuckler)，否则浏览器中的 JavaScript 依赖项必须以适当的顺序加载或连接。如果您需要支持旧浏览器(2018 年之前发布的任何浏览器)，可以使用模块捆绑器，但随着时间的推移，使用这些工具的需求将会减少。

### 浏览

<colgroup><col> <col></colgroup> 
| 浏览 | 描述 |
| --- | --- |
| 网站 | [browserify.org](http://browserify.org/) |
| 仓库 | [github.com/browserify/browserify](https://github.com/browserify/browserify) |
| GitHub stars | 12,000+ |
| 当前版本 | 16.2.3 |
| 每周下载量 | Four hundred and eighty thousand |

Browserify 支持 Node.js 使用的 CommonJS 模块，将所有模块编译成一个浏览器兼容的文件。

### 要求的

<colgroup><col> <col></colgroup> 
| 要求的 | 描述 |
| --- | --- |
| 网站 | 【T0 需求. org】t1 |
| 仓库 | [github.com/requirejs/r.js](https://github.com/requirejs/r.js) |
| GitHub stars | 2,500+ |
| 当前版本 | 2.3.6 |
| 每周下载量 | Two hundred and thirty thousand |

RequireJS 是一个浏览器内模块加载器，虽然它也可以在 Node.js 中使用。

## 工具:林挺

*林挺*分析你的代码是否有潜在的错误或偏离语法标准。您再也不会错过右括号或未声明的变量了！

### 埃斯林特

<colgroup><col> <col></colgroup> 
| 埃斯林特 | 描述 |
| --- | --- |
| 网站 | [eslint.org](http://eslint.org/) |
| 仓库 | [github.com/eslint/eslint](https://github.com/eslint/eslint) |
| GitHub stars | 12,000+ |
| 当前版本 | 5.9.0 |
| 每周下载量 | 四百万 |

ESLint 是最受欢迎的林挺工具，受到大多数 ide、编辑器、捆绑器和任务运行程序的支持。每个规则都是一个插件，所以可以根据你的喜好进行配置。

### JSHint

<colgroup><col> <col></colgroup> 
| JSHint | 描述 |
| --- | --- |
| 网站 | [jshint.com](http://jshint.com/) |
| 仓库 | [github.com/jshint/jshint](https://github.com/jshint/jshint) |
| GitHub stars | 8,000+ |
| 当前版本 | 2.9.6 |
| 每周下载量 | Three hundred and ninety thousand |

一个灵活的 JavaScript linter，比 ESLint 更难配置，但是在真正的错误和迂腐的语法需求之间取得了很好的平衡。

### JSLint

<colgroup><col> <col></colgroup> 
| JSLint | 描述 |
| --- | --- |
| 网站 | [jslint.com](http://jslint.com/) |
| 仓库 | [github.com/reid/node-jslint](https://github.com/reid/node-jslint) |
| GitHub stars | 8,000+ |
| 当前版本 | 0.12.0 |
| 每周下载量 | Eleven thousand five hundred |

它实现了一套严格的默认规则。开发速度变慢了，对一些开发人员来说，这可能有点不妥协。

## 工具:测试套件

测试驱动开发要求你在开始写代码之前先写代码来测试你的代码。欢迎您编写代码来测试您的测试代码！

有很多选择，包括[艾娃](https://ava.li/)、[胶带](https://github.com/substack/tape)和[茉莉](https://jasmine.github.io/)，但是[三个最受欢迎的选择](https://ashleynolan.co.uk/blog/frontend-tooling-survey-2018-results#js-testing)是目前…

### 玩笑

<colgroup><col> <col></colgroup> 
| 玩笑 | 描述 |
| --- | --- |
| 网站 | [https://jestjs.io/](https://jestjs.io/) |
| 仓库 | [github.com/kof/node-qunit](https://github.com/facebook/jest) |
| GitHub stars | 21,000+ |
| 当前版本 | 23.6.0 |
| 每周下载量 | 两百万 |

一个来自脸书的测试框架，由于它与 React 和 Webpack 的紧密联系而越来越受欢迎。

### 摩卡

<colgroup><col> <col></colgroup> 
| 摩卡 | 描述 |
| --- | --- |
| 网站 | [莫哈韦斯. org](https://mochajs.org/) |
| 仓库 | [github.com/mochajs/mocha](https://github.com/mochajs/mocha) |
| GitHub stars | 16,500+ |
| 当前版本 | 5.2.0 |
| 每周下载量 | 一百八十万 |

Mocha 可以在 Node.js 和浏览器中运行测试。它支持异步测试，通常与 [Chai](http://chaijs.com/) 一起使用，以使测试代码能够以可读的方式表达。这是几年来最受欢迎的选择。

### 茉莉

<colgroup><col> <col></colgroup> 
| 茉莉 | 描述 |
| --- | --- |
| 网站 | [jasmine.github.io](https://jasmine.github.io/) |
| 仓库 | [github.com/jasmine/jasmine-npm](https://github.com/jasmine/jasmine-npm) |
| GitHub stars | 300+ |
| 当前版本 | 3.3.0 |
| 每周下载量 | Seven hundred and fifty thousand |

Jasmine 是一个行为驱动的测试套件，可以自动测试你的 UI 和浏览器中的交互。

## 工具:杂项

尽管我尽了最大努力，但我承认不是每个人都喜欢 JavaScript！诸如 [TypeScript](https://www.typescriptlang.org/) 、 [LiveScript](http://livescript.net/) 和 [CoffeeScript](http://coffeescript.org/) 这样的编译器可以让你的开发生活更愉快一些。或者，考虑 [Babel](https://babeljs.io/) 将现代、简洁的 [ES2015](https://www.sitepoint.com/premium/courses/diving-into-es2015-2924) 源代码转换成跨浏览器兼容的 ES5 代码。

有几十个基于 JavaScript 的 HTML 模板引擎，包括[小胡子](https://mustache.github.io/)、[车把](http://handlebarsjs.com/)、[帕格(杰德)](https://pugjs.org/api/getting-started.html)和 [EJS](http://embeddedjs.com/) 。我更喜欢保留 JavaScript 语法的轻量级选项，比如 [EJS](http://ejs.co/) 和[点](https://olado.github.io/doT/)。

最后，既然可以自动化，为什么还要编写自己的文档呢？兼容 ES2015 的文档生成器包括 [ESDoc](https://esdoc.org/) 、 [JSDoc](http://usejsdoc.org/) 、 [YUIdoc](http://yui.github.io/yuidoc/) 、 [documentation.js](http://documentation.js.org/) 和 [Transcription](https://github.com/affirmix/transcription) 。

## 总结和建议

如果你跟随群众的智慧，势头目前在 [React](https://facebook.github.io/react/) 之后，其他库也在朝着类似的技术方向前进。这是一个安全的职业选择，但你也应该考虑 [Vue.js](https://vuejs.org/) 或 React 兼容但更小的 [Preact](https://preactjs.com/) 。

整体框架已经失宠了，但是，如果你需要一个严格的结构用于更大的项目，AngularJS 仍然是一个受欢迎的选择。大多数开发人员坚持使用 1.0 版本，但这可能是出于需要而不是选择。从长远来看，版本 2+可能是一个更安全的赌注，但你需要学习 TypeScript。

Sencha 的 [Ext JS](https://www.sencha.com/products/extjs) 是公司(从小型企业到大型企业)寻找一个既包括框架又包括预建的集成组件和工具的选项的绝佳选择，包括获得商业支持。Ext JS 还可以轻松地将其健壮的组件库与 React 和 Angular 集成在一起，这是为了让开发人员实现预构建的组件，而不是自己构建组件。

不要忽略 jQuery 。它并不时髦，也很少在技术媒体上被提及，但它正在积极开发中，对于网站和应用程序来说能力更强。jQuery 的学习曲线很浅，世界各地的许多开发人员都理解它。

如果你喜欢冒险，新的 transpiler 选项如[svelet](https://svelte.technology/)和 [Rawact](https://github.com/sokra/rawact/) 将框架代码转换成普通的 JavaScript。框架依赖性被移除，代码变得相当小并且运行得更快。

工具的选择不太重要，可以因项目而异。大多数 [WebPack](https://webpack.js.org/) 、[囫囵吞下](http://gulpjs.com/)或 npm 脚本。使用 [ESLint](http://eslint.org/) 和 [Jest](https://jestjs.io/) 进行测试不会错太多，但是有很多替代方法可以尝试。

也就是说，每个项目、团队和技能都是不同的。你只有有限的时间来做评估，所以利用你所知道的很有诱惑力。这篇文章将会收到推荐 *FrameworkX* 的评论，但是当你有锤子的时候，一切看起来都像钉子。

最后，不要忘记库、框架和工具是可选的！在过去的十年中，JavaScript 开发已经发生了革命性的变化；我们已经从几个基本的助手库变成了一个压倒性的选择。很容易陷入不断增加的复杂性的陷阱，或者每隔几个月就切换到最新的热门框架。总是考虑普通的 JavaScript——特别是对于小的和个人的。您获得的知识不会过时，在评估其他项目的框架时会变得非常有价值。

我是否错过了、忽略了或者没有称赞你喜欢的 JavaScript 库、框架和工具的好处？我当然有！*欢迎评论…*

## 分享这篇文章*