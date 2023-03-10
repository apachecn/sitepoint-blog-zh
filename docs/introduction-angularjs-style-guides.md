# AngularJS 样式参考线介绍

> 原文：<https://www.sitepoint.com/introduction-angularjs-style-guides/>

*这篇文章由[汤姆·格列柯](https://www.sitepoint.com/author/tgreco/)、[迪威·托利亚](https://twitter.com/designMoreWeb)和[拉比·基兰](https://www.sitepoint.com/author/rkiran/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

什么是风格指南？AngularJS 项目需要风格指南吗，为什么？最受欢迎的 AngularJS 风格指南有哪些？你如何在开发团队中使用风格指南？本文将回答所有这些问题。在深入 AngularJS 风格指南之前，让我们看看什么是风格指南，以及为什么我们开发人员需要一个。

## 为什么选择风格指南

Wikipedia 提供了一个很好的通用定义，有助于理解为什么样式指南很重要，以及在深入研究 AngularJS 样式指南之前了解整体情况。

> 风格指南(或风格手册)是一套用于一般用途或特定出版物、组织或领域的文档写作和设计的标准。风格指南建立并实施风格以改善沟通。为此，它确保一个文档内和多个文档之间的一致性，并在使用和语言构成、视觉构成、拼写和排版方面实施最佳实践。对于学术和技术文档，指南还可以强制实施道德规范(如作者身份、研究道德和披露)、教学规范(如说明和清晰)和合规性(技术和监管)方面的最佳实践。

编码风格指南加强了与特定语言和组织需求相关的最佳实践。

### 项目样式指南

JavaScript 项目需要风格指南的原因有很多。在本文中，我不打算详细介绍所有这些内容，但是它们通常会在语言风格指南的基础上进行扩展，包括以下附加主题:

1.  **模块化**:单一责任，立即调用函数表达式，模块依赖
2.  **应用结构**:架构模式，文件夹结构
3.  **命名惯例**:用于模块、控制器、配置和规格文件
4.  林挺:JavaScript 代码检查器
5.  **测试**:编写规范的方法
6.  **注释**:生成文档
7.  **启动逻辑**:配置，启动逻辑
8.  **路由**:导航流程，视图构成
9.  **异常处理**:装饰器、异常捕捉器、路由错误
10.  **性能**和**安全**:缩小、混淆

### 现有的 JavaScript 样式指南

对于 JavaScript，有许多通用的和特定于项目的风格指南:

*   [谷歌 JavaScript 指南](https://google.github.io/styleguide/javascriptguide.xml)
*   [Mozilla 编码风格](https://developer.mozilla.org/en-US/docs/Mozilla/Developer_guide/Coding_Style)
*   [克罗克福德的代码约定](http://javascript.crockford.com/code.html)
*   [Waldron 编写一致、地道的 JavaScript 的原则](https://github.com/rwaldron/idiomatic.js/)
*   [npm 的“搞笑”编码风格](https://docs.npmjs.com/misc/coding-style)
*   [jQuery 的 JavaScript 风格指南](https://contribute.jquery.org/style-guide/js/)
*   [Airbnb JavaScript 风格指南](https://github.com/airbnb/javascript)

尽管有大牌，但上面提到的风格指南没有一个是完全全面的。在我看来，Airbnb 风格指南是最新和最完整的，并且还提供了 [eslint 配置](https://github.com/airbnb/javascript/tree/master/packages/eslint-config-airbnb)文件，使用这些文件你可以自动检查你的代码风格。eslint 配置文件可以扩展，[,就像我在构建我的网站时做的](https://github.com/franciov/portfolio/blob/master/.eslintrc)。

### 为什么 AngularJS 项目需要风格指南

AngularJS 项目需要一个样式指南，原因和所有 JavaScript 项目都需要一个样式指南差不多，但是也有一些特定于 Angular 的项目可以涵盖在内。

让我们考虑以下特定于 AngularJS 的示例:

*   **如何使用 ng 标签**。AngularJS *ng* 指令可以以不同的方式使用，并具有不同的语法，例如，在使用 *ng* 指令作为 HTML 属性时，更喜欢使用 *data-ng* 而不是 *ng* ，以便符合 W3C。在代码风格指南中指定如何编写 *ng* 指令有助于提高 HTML 文件的一致性。

*   **实现组件的不同方式**。AngularJS 使用自定义指令实现 web 组件。自定义指令可以基于 HTML 元素名称、属性、类名以及注释。例如，一个风格指南可能确保在一个项目中只使用一种类型的指令。

*   **采用哪种架构模式**。AngularJS 允许 MV*(或 MVW)架构模式。这让 JavaScript 开发人员可以选择是基于 MVC 还是 MVVM 来实现他们的应用程序。关于在项目中必须使用哪种方法的指南有助于保持整个团队在同一轨道上。

既然我们已经知道了什么是风格指南，我们就可以开始研究现有的 AngularJS 风格指南了。

## AngularJS 样式参考线

谷歌提供了[官方风格指南](https://google.github.io/styleguide/angularjs-google-style.html)和[最佳实践](https://github.com/angular/angular.js/wiki/Best-Practices)，但最受欢迎和最全面的指南来自 AngularJS 社区:

*   [Minko Gechev 的 AngularJS 风格指南](https://github.com/mgechev/angularjs-style-guide)
*   [托德座右铭的 AngularJS 风格指南](https://github.com/toddmotto/angular-styleguide)
*   [约翰爸爸的 AngularJS 风格指南](https://github.com/johnpapa/angular-styleguide)

很难说哪一个是最好的，因为它们都是很好的风格指南。约翰爸爸的指南是全面的和不断发展的，托德座右铭的是简洁的和伟大的开始，明科格切夫的是翻译成不同的语言。但似乎约翰爸爸的风格指南[已经被官方推荐](http://angularjs.blogspot.co.uk/2014/02/an-angularjs-style-guide-and-best.html)为最新最详细的 AngularJS 风格指南。

在我看来，这些是 Jonh Papa 的风格指南中最重要的 AngularJS 特定要点，在开始一个全新的 AngularJS 项目时需要考虑:

*   升力原理
*   控制器语法
*   Fle 模板和片段
*   种子应用

### 升力原理

[提升原则](https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md#application-structure-lift-principle)与 app 结构相关，并定义了以下准则:

1.  快速找到您的代码
2.  我一眼就认出了代码
3.  尽可能保持最新的结构
4.  试着保持干燥

这使得应用程序结构更具可扩展性，开发人员也更高效，因为他们可以更快地找到代码。使用一个[基于特征的文件夹结构](https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md#folders-by-feature-structure)自然有助于遵循这个原则。

### 控制器

在“带有`$scope`的经典控制器”语法上使用[控制器的](https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md#controlleras-controller-syntax)语法使得代码在视图中更具可读性:

```
<!-- avoid -->
<div ng-controller="CustomerController">
    {{ name }}
</div>

<!-- recommended -->
<div ng-controller="CustomerController as customer">
    {{ customer.name }}
</div> 
```

在控制器中:

```
/* avoid */
function CustomerController($scope) {
    $scope.name = {};
    $scope.sendMessage = function() { };
}

/* better */
function CustomerController() {
    this.name = {};
    this.sendMessage = function() { };
} 
```

实际上，我们建议更进一步，将`this`赋给一个变量，这样您就可以很容易地从您的控制器方法中访问它:

```
/* recommended */
function CustomerController() {
    var customerVM = this;
    customerVM.name = {};
    customerVM.sendMessage = function() {
        // we can access the controller's scope as customerVM
    };
} 
```

### 文件模板和片段

在约翰爸爸的指南中，为不同的编辑器和 ide 列出了大量的[文件模板和片段](https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md#file-templates-and-snippets)。使用文件模板和代码片段可以确保不同文件、模块和子系统之间的一致性，特别是如果您在团队中工作，这可以在重构期间或者当新开发人员加入您的团队时节省大量时间。它还保持了项目代码的整洁和可重用性。

### 种子应用

如果你赶时间，或者想从一个完整的例子中学习，在开始新的 AngularJS 项目时， [HotTowel](https://github.com/johnpapa/generator-hottowel) 是一个可以考虑的选择。这个[约曼](http://yeoman.io/)生成器创建了一个应用程序，作为你的 AngularJS 应用程序的起点，遵循约翰爸爸的风格指南。生成的应用程序配置有一个 npm 包、一个 gulp 文件、JavaScript 和一些提示，因此，如果您没有特殊的需求，您所要做的就是实现新的特性！你可以[在 GitHub 上的我的 AngularJS playground 中浏览并运行一个使用 HotTowel](https://github.com/franciov/angularjs-sample-app/tree/styleguide/yeoman-generator/angular1/hottowel) 生成的 AngularJS 应用程序。

### 现实世界的例子

用于现实项目的样式指南的一个例子是[GoCardless AngularJS style guide](https://github.com/gocardless/angularjs-style-guide)，在其中您可以找到更具体和更高级的代码片段。它提倡在 HTML 中使用指令而不是控制器。例如，看看关于指令名的指导原则:

> **指令名只能包含 a-z 和至少一个破折号(-)** 。原因:*定制元素必须有一个破折号(名称空间)，以区别于本地元素，并防止未来的组件冲突*。

```
<!-- Recommended -->
<dialog-box></dialog-box>
<button click-toggle="isActive"></button>

<!-- Avoid -->
<dialog></dialog>
<button toggle="isActive"></button> 
```

GoCardless 样式指南可以作为一个很好的起点，它可以启发你根据你的团队和项目需求定制和扩展上面提到的通用样式指南。

### 在团队中使用风格指南

代码风格指南应该是大多数 AngularJS 项目的必需输入，特别是如果项目预期快速增长或者团队是新的。此外，代码风格指南应该在项目过程中保持活力，并在每次出现新的需求或发现时进行更改或扩展。

### 未来:2016 年及以后

在不久的将来，事情会发生很大的变化。下一个挑战是更新 AngularJS 2 的样式指南，特别是关于 web 组件、ECMAScript 2015 (ES6)和 ECMAScript 2016 及其他版本的开发。

我很想听听其他人对风格指南的体验，无论是 Angular 还是一般的 JavaScript。有没有我漏掉的很棒的指南，或者你认为特别有争议的指南？请在评论中告诉我！

## 分享这篇文章