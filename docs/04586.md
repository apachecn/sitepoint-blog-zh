# 良好的前端架构

> 原文：<https://www.sitepoint.com/good-front-end-architecture/>

建立良好的前端架构是开始开发 web 应用程序或网站的基本步骤。良好的实践和编码惯例是必不可少的，但是结构呢？我们怎样才能构思出一个好的可及时维护的架构呢？但最重要的是，我们应该从哪里开始？

当我开始思考这个问题时，我意识到我需要几样东西:

1.  我想要一个多页项目(一个网络应用程序或网站)。
2.  我希望我的项目支持不同的屏幕尺寸和分辨率，换句话说:我希望它能响应迅速。
3.  我希望最终产品是可维护的。
4.  我希望最终的产品是高性能的。
5.  我想为任何未来的项目重用相同的模板。

## 合适的工具

现在我们有很多很酷的工具，可以帮助我们进行现代前端开发工作流程。因此，面对第一点和第二点，我告诉自己，我需要一个基于断点的 CSS 架构，它可以帮助我支持不同的设备和桌面大小。另一方面，我也知道如此大量的 CSS 和文件可能会有点乱(因此与第 3 点不兼容。)，这就是为什么我决定开始使用 CSS 预处理器(在我的情况下是 Sass with Compass)。

第四点呢？答案很简单:我决定使用 Gruntjs。最后，第五点呢？即使在那种情况下，答案也在那里:约曼，我认为最好的解决方案。

## 组织工作流程

出于不同的目的和目标，每个前端项目总是包括库、jQuery 插件和大量 JavaScript 和 CSS 文件(在本例中是 SCSS 文件)。融合所有这些元素意味着使用不同的技术，将它们放在一起意味着建立一个良好的前端工作流程。我们会发现自己不得不管理一个涉及不同技术的庞大工作流程。这就是为什么把所有的东西都放在文件夹里，遵循一种模式或惯例，以保持东西的清晰和整洁，这是非常重要的。

我们可以选择在宏组中拆分所有基本前端组件，如下所示:

*   SCSS 档案
*   剧本
*   视图

我们能把他们分成小组吗？我们当然可以:

*   SCSS
    *   变量
    *   混合蛋白
    *   每个布局的公共部分
    *   单一布局
*   射流研究…
    *   库(比如 jquery、angularjs、gAnalytics 等等……)
    *   插件(通常是 jquery 插件)
    *   控制器(我指的是 angularjs 控制器之类的控制器)

在基于模板的架构中(例如使用 blade.php 或带有 nodejs 的 jade ),我们也可以如下分割视图:

*   视图
    *   每个视图的公共部分
    *   单一视图

但是我在本文中并不讨论这种情况，因为我假设我的项目中的每个页面或布局只有一个视图(一个 HTML 文件)。

从所有这些初步的考虑开始，这就是我决定如何组织架构，这是我的文件夹树:

```
project
  \css
  \imgs
  \js
      \controllers
          pageOne.js
          pageTwo.js
      \libs
          angular.js
          jquery.js
          analytics.js
      \plugins
          jquery.tooltip.js
          textResize.js
          formValidation.js
      \views
          pageOne.js
          pageTwo.js
  \scss
      \framework
          _core.scss
          _forms.scss
          _input.scss
          _mixins.scss
          _variables.scss
      \layouts
          _all.scss
          _phablets.scss
          _tablets.scss
          _desktop.scss
          _desktop-large.scss
          _retina.scss

          \pageOne
              _all.scss
              _phablets.scss
              _tablets.scss
              _desktop.scss
              _desktop-large.scss
              _retina.scss

          \pageTwo
              _all.scss
              _phablets.scss
              _tablets.scss
              _desktop.scss
              _desktop-large.scss
              _retina.scss
      \libs
          _animate.scss
          _normalize.scss
          _reset.scss
      \plugins
          _jquery.tooltip.scss
          _jquery.fancyInput.scss
```

## 文件夹解释

### img(联合宇航公司)

我决定把所有的图片文件放在这里:`.png`、`.jpg`、`.jpeg`、壁纸等等。

*例子:*`icon.png``home-background.jpeg``userAvatar.jpg`

### js(主文件夹)

我决定把所有的`.js`文件放在这里，按如下方式组织在子文件夹中:

### \控制器(子文件夹)

这是`angular controllers`的文件夹，每一个都有对应视图的相同名称。例如，如果你的`home.html`需要一个角度控制器，你应该创建这样一个文件:`project\js\controllers\home.js`。

*例子:*`home.js``user-registration.js``user-login.js`

### \ libs(子文件夹)

我为 javascript 库创建了这个文件夹。当我说库时，我不是指插件，这就是为什么我决定区分第一个和第二个，创建两个不同的文件夹。

*例子:*`jquery-latest.js``angular.js``googleAnalytics.js`

### \插件(子文件夹)

插件需要依赖关系才能工作，库不需要。所以我创建了一个不同的文件夹:

*举例:*`jquery-fancyInput.js``restangular.js``customPlugin.js``jquery-airport.js`

### \视图(子文件夹)

我为所有的演示文稿创建了这个文件夹。每个文件必须与相应的视图同名。例如，如果你的`home.html`需要一些效果、素材和插件初始化，你应该创建一个这样的文件:`project\js\views\home.js`，它有自己的`document ready`。

*举例:*`home-animations.js``user-registration.js``user-login.js``site-animations.js`

### css(主文件夹)

该文件夹包含所有从主`scss`文件生成的`css`。例如`home.scss`会在这个文件夹中生成相应的`home.css`文件。

### \ libs(子文件夹)

即使对于 CSS 文件，我也会区分库和插件。以下是一些 CSS 库示例:

*举例:*`_meyers-reset.scss``_normalize.scss``_animate.scss``_960gridSystem.scss`

### \插件(子文件夹)

这个文件夹中包含的 CSS 文件是 JavaScript 插件工作所必需的样式。

*例子:* `_jquery-fancyInput.scss`，`_jqueryTooltip.scss`

### \框架(子文件夹)

我决定将所有项目页面共享的`scss`文件放在这个目录中

*框架子文件夹的组织方式如下:*

*   `_variables.scss`(项目变量声明——颜色、间距等。)

*   `_mixins.scss`(项目 mixin 声明——排版、clearfix、动画等。)

*   `_forms.scss`(自定义表单样式&复位)

*   `_input.scss`(自定义输入样式&复位)

### \布局(子文件夹)

这里是响应部分。该目录中的文件以覆盖所有屏幕分辨率的方式组织，遵循移动优先原则。所以，你应该开始使用`_all.scss`文件来声明你自己的风格。此文件中声明的规则对所有屏幕尺寸和所有视图都有效。如果你想让你的网站具有适应性，那就重写规则，并声明它们适用于任何其他的屏幕分辨率。

该文件夹的组织方式如下:

*   平板手机(**481 以上** ) `_phablets.scss`

*   平板电脑和小型笔记本电脑(**768 以上** ) `_tablets.scss`

*   台式机(**1030 以上** ) `_desktop.scss`

*   大屏幕台式机(**1204 以上** ) `_desktop-large.scss`

*   视网膜显示异常( **@2x** ) `_retina.scss`

这些文件将处理布局异常，并由 mediaqueries 调用。

**请注意:**这些文件在所有视图(HTML 页面)中共享。这就是为什么你需要在`scss\layouts`下创建一个新的文件夹来给特定的页面赋予特定的样式。

*举例——如果你想设计一个响应性主页和一个响应性用户登录页面*

1.  在`scss\layouts`下创建一个新文件夹，用与您想使其自适应的视图相同的名称命名(例如 home 和 userLogin)所以:`scss\layouts\home`和`scss\layouts\userLogin`

2.  将上面列出的相同文件放入这些文件夹:`_all.scss`、`_phablets.scss`、`_tablets.scss`、`_desktops.scss`、`_desktop-large.scss`和`_retina.scss`。

## 最后的结果

最终的结果是一个约曼生成器来构建一个响应性的多页面 web 应用程序或网站。我还决定为*的缩小、混淆和 Sass 编译*加入一些单调的任务。你可以在这里【https://github.com/mcarella/wormhole[看一下](https://github.com/mcarella/wormhole)，或者直接从 https://www.npmjs.org/package/generator-wormhole[NPM](https://www.npmjs.org/package/generator-wormhole)下载。

## 从这里去哪里

我知道这不是一个通用的解决方案，也许在你的情况下，它似乎包括一些冗余，我知道它不能适合所有情况，但我认为这是一个很好的起点。您可以添加任意多的繁重任务，或者以不同的方式组织您的 SCS，使其更轻或更重。不管你的目标是什么，我通常建议人们根据自己的需求开发自己的架构或框架，特别注意性能和可维护性，同时不忽视用户体验。

## 分享这篇文章