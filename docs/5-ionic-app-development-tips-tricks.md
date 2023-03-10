# 5 Ionic Framework 应用程序开发技巧和诀窍

> 原文：<https://www.sitepoint.com/5-ionic-app-development-tips-tricks/>

Ionic 是构建 HTML5 混合移动应用的一个非常有用的框架。这是创建 Cordova/Phonegap 移动应用程序的一个很好的起点。它提供了可重复使用和适应性强的通用移动应用程序组件。Ionic 还提供了一个非常有用的 CLI(命令行界面)，允许您轻松创建、编译、运行和导出移动应用程序。它不断添加新功能，使其超越了前端框架。需要在构建原生应用或响应式 web 应用之前构建一个原型吗？Ionic 也是原型的一个很好的选择。

在这篇文章中，我想提供一些我发现的最有用的东西的概述。我希望它能帮助新开发者更快地开发和运行他们自己的应用。

这些指南将假设您已经在 CLI 中启动并运行了一个项目。我们将使用 Ionic“入门”页面上的一个模板。比如`ionic start myApp blank`。如果你是从零开始，这绝对是最好的方法。您将获得所有 Ionic CLI 功能及其核心启动模板。

我将从几乎每个应用程序都想做的事情开始——定制。

## 在 Ionic 1 中，哪里是我的自定义样式的最佳位置？

我在项目结构中尝试了一些想法，寻找最佳方法。一个将是干净的，为我自己的扩展项目，但也将与离子吞咽设置。我不想改变默认的离子大口文件不必要的。

### 就在 www 里放 Styles？

匆忙行事并把新的 scss 文件扔进某处的`www`文件夹可能很有诱惑力。然后在`index.html`中添加一个对 CSS 文件的引用。避免那个！它不太适合安装离子大口。最好不要在里面加东西。

### 更好的方法——将样式添加到 ionic.app.scss 中

在名为`/scss`的文件夹中，你应该会找到一个`ionic.app.scss`文件。这是主要的 SASS 文件，为你的应用程序编译所有的样式。这就是 Ionic 的 Gulp 设置将参考的内容。希望您的代码能够与包括实时刷新在内的所有内容一起工作和编译吗？`ionic.app.scss`文件是不言自明的。你可以在这个文件的末尾添加许多新的样式。这将适用于小而简单的应用程序，只需进行一些定制。

### 更进一步——将你的自定义应用程序风格放到它们自己的文件夹中

很有可能，你的应用程序不会保持小而简单。它将会增长，你会希望它保持可控。如果有一个开发团队参与(无论是现在还是将来)，这一点尤其正确。出于这些原因以及更多原因，我建议将您的自定义样式拆分成一组更整洁的文件。每当我们特别重写 Ionic 本身时，这些文件应该镜像 Ionic 的 sass 文件。将这些自定义应用程序样式放入它们自己的子文件夹中。

我在`scss`文件夹中设置了一个与应用程序名称相匹配的文件夹。我的自定义样式的主要 scss 文件也与该应用程序名称匹配。例如，一个名为 *Antstagram* 的蚂蚁社交网络应用程序在`scss/antstagram/antstagram.scss`中会有它的样式。在`antstagram.scss`中，我们将有几个`@import`语句来导入我们的其他 scss 文件，如`_variables.scss`和`_items.scss`。这是为那些匹配我们离子架构的人而做的。这也是随着我们的应用程序功能，如`_antmountain.scss`特定的定制文件一起完成的。说到`_variables.scss`，有几种方法可以覆盖 Ionic 的变量。我将在下一篇技巧文章中解释这些。

### Ionic 2 中自定义样式的最佳位置

在 ionic 2 中，默认的文件夹结构将每个组件放在自己的 SCSS 文件中。这是你应该放置所有页面特定样式的地方。

![Each component has its own SCSS file](img/0e03b2c274a76e4820f4fe230066d436.png)

全球通用的自定义样式应该放在位于`src/app/app.scss`的`app.scss`文件中

![Global Styles declared in app.scss](img/d649bfd089f9399d161a708ffbd1bd32.png)

要定制应用程序的整体主题，请编辑`src/theme/variables.scss file`。这是声明所有颜色变量的地方。`$primary`颜色是唯一需要的颜色。这是 Ionic 2 中用于设计按钮和其他组件的默认颜色。一个好的方法是将颜色变量设置为设计的颜色。只要有可能，就应该这样做，而不是声明自定义类。这可以确保在整个应用程序中应用一致的颜色。请注意，`$colors`映射应该只包含 UI 组件的变量。任意的颜色变量不应该放在这里。

## 如何在 Ionic 1 中正确覆盖颜色变量？

Ionic 提供了一套预定义的风格，可供您定制和使用。在定义大量你自己的变量之前，先浏览一下默认变量列表。如果它们适合你想要的风格，就使用它们。

这些变量都方便地列在`www/lib/ionic/scss/_variables.scss`中。对于初学者来说，在这里编辑它们是很有诱惑力的。不要这样做！每当您更新 Ionic 时，您的更改将被 Ionic 变量的任何更新所覆盖。通常–不要自定义`www/lib`文件夹中的任何内容。让那些文件保持原样。假装那些文件是完全只读的。如果您有其他想要使用的 JavaScript 库，您可以添加到它们中。否则，您会希望避免在这里自定义文件。

在您的初始项目设置中，您应该已经有了我们前面提到的`scss/ionic.app.scss`。它包含放置变量覆盖的指导。我将添加到他们的方法中——我更喜欢将变量放在单独的 scss 文件中。`scss/ionic.app.scss`默认情况下是这样的:

```
/*
To customize the look and feel of Ionic, you can override the variables
in ionic's _variables.scss file.

For example, you might change some of the default colors:

$light:                           #fff !default;
$stable:                          #f8f8f8 !default;
$positive:                        #387ef5 !default;
$calm:                            #11c1f3 !default;
$balanced:                        #33cd5f !default;
$energized:                       #ffc900 !default;
$assertive:                       #ef473a !default;
$royal:                           #886aea !default;
$dark:                            #444 !default;
*/

// The path for our ionicons font files, relative to the built CSS in www/css
$ionicons-font-path: "../lib/ionic/fonts" !default;

// Include all of Ionic
@import "www/lib/ionic/scss/ionic"; 
```

那么，为什么不在`ionic.app.scss`的末尾重新声明变量呢？为什么都在文件的开头？其原因是它们需要在离子导入之前添加。否则，ionic 的默认值将覆盖您的默认值。

### 我推荐的进口方式

我的首选方法是在我的`antstagram`文件夹中导入一个名为`_variables.scss`的独立 scss 文件。这是在从爱奥尼亚进口任何东西之前完成的。然后，在 Ionic 之后，我的其他自定义样式被导入到我的主要 Antstagram 样式中。这是为了让他们能够超越爱奥尼亚的风格。

```
@import "antstagram/variables";

// The path for our ionicons font files, relative to the built CSS in www/css
$ionicons-font-path: "../lib/ionic/fonts" !default;

// Include all of Ionic
@import "www/lib/ionic/scss/ionic";

// Include Antstagram styles
@import "antstagram/antstagram; // imports everything other than variables 
```

### 在 Ionic 2 中覆盖颜色变量

在 Ionic 2 中，覆盖颜色变量非常简单。这是通过编辑在`src/theme/variables.scss`文件中声明的变量来完成的。

![Overriding Color variables](img/e1e1e8c861f8b324d323becf65648421.png)

## 我丢失了一些离子图标字体！

如果你去 Ionicons 网站并开始使用这些图标，你可能会发现你的项目中缺少了一些。这可能是因为 Ionic 没有安装最新的 Ionicons 套件。请访问 Ionicons 网站，点击下载按钮，获取最新版本。

然后，我们可以替换`www/lib/ionic/fonts`中的 Ionicons 字体文件。用下载的 fonts 文件夹中的字体替换它们。很简单，升级到最新版本。

我们还用下载的 scss 文件夹中的文件替换了`www/lib/ionic/scss/ionicons`中的 scss 文件。这里似乎有一个额外的 scss 文件用于动画——保持原样，只需替换其他文件。

你可能已经注意到了一些矛盾的地方。这些文件在`www/lib/ionic`文件夹中，如果我们升级 Ionic，它们可能会改变，对吗？在我看来，这种情况下没问题。我们暂时将 Ionicons 升级到最新版本，同时等待 ion 赶上。如果爱奥尼亚升级不快，你可能会失去图标。但是，通常情况下，Ionic 的下一次升级也将包括最新版本的 Ionicons。这也为将来 Ionicons 的升级自动到达 Ionic 的更新留有余地。

一旦你更新了你的字体文件，你就可以使用你以前看不到的图标了。在你的终端点击`q`停止运行`ionic serve`，然后重新运行`ionic serve`获得最新图标。

### Ionic 2 中缺失的字体

要获得 Ionic 2 上的最新图标集，请检查您的 package.json。只需运行`npm install`,你就可以设置好所有的图标。请继续关注新的 [Ionicon Docs](http://ionicframework.com/docs/ionicons/) ，因为一些名称已经更改。

## 在 Ionic 1 中设置在 iOS 和 Android 之间显示不同的图标

Ionic 有几个有用的函数，您可以在代码中使用它们来检测使用哪个图标。这些包括`ionic.Platform.isIOS()`、`ionic.Platform.isAndroid()`和`ionic.Platform.isWindowsPhone()`。这方面的一个例子是:

```
var icon;

if (ionic.Platform.isIOS()) {
  icon = 'ion-ios-arrow-back';
} else if (ionic.Platform.isAndroid()) {
  icon = 'ion-android-arrow-back';
} else {
  icon = 'ion-android-arrow-back';
} 
```

实际上，我更喜欢做简单的检查。如果是 iOS，就用 iOS 图标，否则就用安卓。如果你想指定`ionic.Platform.isWindowsPhone()`，这就不那么简单了。到目前为止，Ionicons 没有 Windows Phone 特有的图标，所以我默认使用 Android 图标:

```
var icon = ionic.Platform.isIOS() ? 'ion-ios-arrow-back' : 'ion-android-arrow-back'; 
```

当这是在我经常使用的对象中时，我把对象变成一个包含这个逻辑的指令。例如，自定义后退按钮(用您用于模块的全局应用程序名称替换`starter`):

```
angular.module('starter.directives', [])
   .directive('backbutton', [function () {
      return {
        restrict: 'E',
        replace: true,
        scope: false,
        template: '<button class="button icon button-clear"></button>',

        compile: function (element, attrs) {
           var icon = ionic.Platform.isIOS() ? 'ion-ios-arrow-back' : 'ion-android-arrow-back';
           angular.element(element[0]).addClass(icon);
        }
      };
   }]) 
```

这将创建一个元素

```
<backbutton ng-click="goBack()"></backbutton> 
```

我可以在整个应用程序中重复使用，并在一个中心位置随时更新任何更改。

注意:Ionic 中有一个 back 按钮元素，但我喜欢为一些情况创建自己的更简单的版本，如自定义模态窗口。这个例子同样适用于你想要使用的其他按钮和图标

### 在 Ionic 2 中显示不同的图标

Ionic 的新版本在框架中内置了这一功能。组件将根据平台动态加载正确的图标。

```
<!-- Will load the correct icon for ios and android -->
<ion-icon name="beer"></ion-icon> 
```

通过指定平台版本，您仍然可以对使用什么图标有更多的控制。

```
 <ion-icon ios="ios-beer" md="md-beer"></ion-icon> 
```

## 在 iOS 上保持自定义标题元素一致

如果你在标题中添加一个自定义元素，比如我的自定义搜索框，你很快就会注意到一件事:

![iOS Header](img/764352336ec218a56d694d77b220fe77.png)

就是在 iOS 上，你的元素会卡在时间和信号强度后面:

![iOS Header](img/cad2ca6bad55939762df2705639aa037.png)

Ionic 有专门的类来处理这个问题——`platform-ios`和`platform-cordova`。Ionic 的预建元素针对这些类。他们在标题中的按钮上增加 20px 的上边距来弥补这些情况。为了让我自己的定制元素做同样的事情，我遵循相同的模式。对于我的`.search-bar`，如果我们有一个`.platform-ios.platform-cordova:not(.fullscreen)`车身等级，我会增加一个余量:

```
.platform-ios.platform-cordova:not(.fullscreen) .search-bar {
  margin-top: 20px;
} 
```

此修复使我的搜索框恢复正常:

![iOS Header](img/24b14e58f42c3e34cb36b84a68fcca25.png)

## 结论

Ionic 已经发展成为一个强大的框架，拥有丰富的服务生态系统。希望我分享的建议能帮助那些想要踏上自己的离子之旅的人！

如果你有任何自己的小技巧可以分享，或者有其他方法可以实现上述想法，请在评论中留下你的想法。

## 参考

> [离子文档](http://ionicframework.com/docs/)。
> [Ionicons 网站](http://ionicframework.com/docs/ionicons/)
> [棱角分明的小抄](https://angular.io/cheatsheet)。
> [造型离子 2 app](https://www.joshmorony.com/tips-tricks-for-styling-ionic-2-applications/)。
> [Ionic 2 应用程序风格指南](https://www.joshmorony.com/a-guide-to-styling-an-ionic-2-application/)。

*如果你喜欢这个，你可能想知道我们在 SitePoint Premium* 上有一些很棒的[移动内容](https://www.sitepoint.com/premium/?q=&slugs%5B%5D=mobile)

## 分享这篇文章