# 波本威士忌笔芯提供了嵌入式设计和功能

> 原文：<https://www.sitepoint.com/bourbon-refills-provide-drop-design-functionality/>

设计模板和 web 组件框架的所有优势，而无需放弃对源代码的控制。

## 波旁酒续杯哲学

如果你厌倦了牺牲自己的标准去迎合那些可能不像你一样关注干净一致的源代码的人设计的模板，或者那些想要接管你整个网站的框架，你可能已经准备好尝试波本酒了。

笔芯库包含有用的独立和可定制的网页组件，如折叠、选项卡导航、页脚、字体堆栈和其他便利工具。波本威士忌笔芯可以作为协调的 HTML、SCSS 和 jQuery 代码集在您的网站中使用，以快速片段的形式提供布局、功能和设计。

不像要么全有要么全无的网络框架，只有当你使你的整个网站适应它们的惯例时，它才能工作得最好，填充可以外科手术般地应用在任何一个支持基本的轻量级[波旁酒](http://bourbon.io)结构，有[纯](http://neat.bourbon.io)和[苦](http://bitters.bourbon.io)的网站中，不管网站的其余部分是否基于波旁酒。补充是社区支持的，但编译和管理图书馆的大部分工作已经由 Thoughtbot 的开发者完成，他们也是波旁酒的幕后力量。

笔芯库最好的一个方面是每个模式或组件都完全独立于其他模式或组件。由于整洁，填充元素都是响应性的，并遵循使它们不显眼和可移植的约定，例如避免 CSS 嵌套超过两层，保持样式最少，以及使用内部范围的变量操作。

你甚至可以贡献你开发的新模式和组件来帮助扩展这个库，只要你确保你遵循了指南。

## 开始使用笔芯

要成功使用笔芯库，您需要在您的网站上满足一些基本要求:

*   布尔本(4.0+)
*   整洁的
*   苦味酒(10.1+)
*   jQuery(用于需要 JavaScript 的元素)

不难设置所有这些工具在开发期间无缝运行，并轻松部署到生产中。我在 Sitepoint 上写过一些关于[波旁酒](https://www.sitepoint.com/getting-started-sass-bourbon/)、[纯酒](https://www.sitepoint.com/sass-bourbon-neat-lightweight-semantic-grids/)和[苦味酒](https://www.sitepoint.com/adding-bitters-to-bourbon-and-sass/)的文章，以及如何快速使用它们。

如果您想要的只是一个快速的页眉、页脚和一些小部件，这听起来可能会增加页面的潜在权重。但是请记住波旁酒的工作方式。当您为您的页面编译 Sass 时，除非您明确地使用某些东西，否则 Bourbon 和 Neat 实际上会消失。Bitters 是一个非常轻量级的样式调整集，大小可以压缩到基本的 CSS 重置(这也是跨浏览器一致性的建议)。jQuery 几乎无处不在，在方便的跨浏览器兼容 JavaScript 方面增加了如此多的价值，以至于你可能已经在使用它了。如果您从内容分发网络(CDN)加载 jQuery，当访问者到达您的页面时，它可能已经缓存在您的浏览器上了。

一旦你设置好了，你所需要做的就是浏览[填充页面](http://refills.bourbon.io)找到你想要添加到你的站点的元素，复制并粘贴代码到你的 HTML、Sass 和 JavaScript 文件中。

是的，我说复制粘贴。冷静点。疯狂是有方法的。

与其他孤立存在并在需要时被动态调用的库不同，补充库被设计为在代码中进行合并和修改。许多笔芯元素包含了您想要用自己的内容修改的样本 HTML，并且大多数 Sass 和 JavaScript 可能需要进行其他更改以适应您自己的站点风格。续杯只是为你提供一个起点，而不是最终目的地。

向您的站点添加修改过的笔芯就像使用自定义生成的小部件一样，除了您在进行修改时可以完全控制哪些内容发生了变化。通常，所提供的 HTML 可以更多地被视为一个使用示例，而不是一段必需的代码。例如,“Tables”模式的 HTML 只显示了一个标准表，其中包含一个触发填充样式的自定义类。

这种方法意味着，替换库不关心您选择如何组织您的文件，只要将每个部分粘贴到您的堆栈能够找到并解释它的地方。例如，如果您使用一种模块化的方法来进行 Sass 编译，您可能想要为您选择使用的每个替换的 SCSS 代码创建一个单独的`module-name.scss`文件，然后在您的`main.scss`文件中的`@import`这些文件带有您可能已经定义的任何其他模块化组件。填充将支持这种方法，就像其他任何方法一样容易。

## 模式

填充库包含一个吸引人的外观熟悉的表示组件，他们称之为模式。如果你熟悉许多基于 [Twitter Bootstrap](http://getbootstrap.com/) 或 [Zurb Foundation](http://foundation.zurb.com/) 构建的网站的典型外观，这些元素中的大部分看起来会很熟悉。不同的是，你可以挑选你想在你的代码中出现的，并让你的网站的其余部分以你想要的方式出现。

这些模式包括选项卡式导航、卡片样式的呈现元素(带有可选的功能区覆盖)、用于呈现图像或信息的灵活网格、带有下拉菜单导航的标准标题、英雄框、不同的页脚样式、样式化的表格、一个 Google Maps 小部件，以及其他几个。你可以把它们中的任何一个直接放到一个没有风格的网站上，让它看起来整洁有序，只需要很少或者根本不需要视觉设计，就可以得到一个可以接受的极简结果。

这些元素非常基本，但这为您留下了足够的空间来定制和修改它们，以满足设计的需要。由于与 Bitters 的集成，您可以轻松地更改视觉方面，如通用边框样式或调色板，并将这些更改级联到整个设计中。笔芯模式也从波旁威士忌整洁的网格布局的无缝集成中获得其响应特性。

一个典型的示例模式是页面导航滚动条。这允许您在页面顶部创建一个目录，并使用平滑的动画滚动向下导航到页面的任何部分，默认情况下使用“返回顶部”按钮返回到目录。

这种模式从一些简单的标准 HTML 开始，用 CSS 和 JavaScript 用来定位动作的几个类和 id 进行标记:

```
<div class="scroll-on-page">
  <aside id="scroll-on-page-top">
    <a class="scroll-on-page-link" href="#scroll-link-1">First Scroll Link</a>
    <a class="scroll-on-page-link" href="#scroll-link-2">Second Link</a>
    <a class="scroll-on-page-link" href="#scroll-link-3">Last Link</a>
  </aside>

  <article>
    <section>
      <h4 id="scroll-link-1">First Target</h4>
      <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Cum aliquid necessitatibus, repudiandae veniam labore, consequatur maiores dolore unde non deleniti, aliquam minima ex nulla error eveniet vel tempore, in incidunt?</p>
      <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Cum aliquid necessitatibus, repudiandae veniam labore, consequatur maiores dolore unde non deleniti, aliquam minima ex nulla error eveniet vel tempore, in incidunt? Lorem ipsum dolor sit amet, consectetur adipisicing elit. Cum aliquid necessitatibus, repudiandae veniam labore, consequatur maiores dolore unde non deleniti, aliquam minima ex nulla error eveniet vel tempore, in incidunt?</p>
    </section>
    <section>
      <h4 id="scroll-link-2">Second Target</h4>
      <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Lorem ipsum dolor sit amet, consectetur adipisicing elit. Cum aliquid necessitatibus, repudiandae veniam labore, consequatur maiores dolore unde non deleniti, aliquam minima ex nulla error eveniet vel tempore, in incidunt?</p>
    </section>
    <section>
      <h4 id="scroll-link-3">Last Target</h4>
      <p>lorem ipsum dolor sit amet, consectetur adipisicing elit. Cum aliquid necessitatibus, repudiandae veniam labore, consequatur maiores dolore unde non deleniti, aliquam minima ex nulla error eveniet vel tempore, in incidunt? Lorem ipsum dolor sit amet, consectetur adipisicing elit. Cum aliquid necessitatibus, repudiandae veniam labore, consequatur maiores dolore unde non deleniti, aliquam minima ex nulla error eveniet vel tempore, in incidunt? Lorem ipsum dolor sit amet, consectetur adipisicing elit. Cum aliquid necessitatibus, repudiandae veniam labore, consequatur maiores dolore unde non deleniti, aliquam minima ex nulla error eveniet vel tempore, in incidunt?</p>
      <a class="scroll-on-page-link" href="#scroll-on-page-top">Back to top</a>
    </section>
  </article>
</div>
```

如您所见，这显然只是占位符内容，您最有可能将它用作开发和标记您自己的页面内容的模型，而不是逐字复制它。虚拟文本很长，所以你可以看到垂直滚动是如何工作的，而不会使页面短得离谱。

下一个代码部分为示例样式提供了 SCSS，该样式相应地将导航元素与包含可导航部分的文章分开保存:

```
.scroll-on-page aside {
 @include media($large-screen) {
 @include span-columns(4);
  }
}

.scroll-on-page-link {
 border-bottom: 1px solid transparentize($base-link-color, .8);
 padding: $base-spacing / 2 0;
 margin-right: $base-spacing;
 display: block;

 &:last-child {
 margin-bottom: $base-spacing;
  }
}

.scroll-on-page article {
  h4 {
 margin-bottom: .5em;
  }

  section p:last-of-type {
 margin-bottom: 2em;
  }

 @include media($large-screen) {
 @include span-columns(8);
  }
}

// Based on code by http://codepen.io/xmark/
```

最后，有一个部分使用了一些 JavaScript 来帮助导航:

```
(function (jQuery) {
  jQuery.mark = {
    jump: function (options) {
      var defaults = {
        selector: 'a.scroll-on-page-link'
      };
      if (typeof options == 'string') defaults.selector = options;
      var options = jQuery.extend(defaults, options);
      return jQuery(options.selector).click(function (e) {
        var jumpobj = jQuery(this);
        var target = jumpobj.attr('href');
        var thespeed = 1000;
        var offset = jQuery(target).offset().top;
        jQuery('html,body').animate({
          scrollTop: offset
        }, thespeed, 'swing')
        e.preventDefault();
      })
    }
  }
})(jQuery);

jQuery(function(){
  jQuery.mark.jump();
});
```

在这种情况下，完整的 jQuery 实现就像您将它放在一个`<script>`元素中一样被包含，在作用域中使用了`jQuery`变量，而不是我们更熟悉的`$`变量。当您探索填充库时，您可能会注意到一些元素只显示了您将在`(function (jQuery) { ... })(jQuery);`块中使用的 jQuery 代码。你也可能更喜欢在你的函数范围内使用`$`，因为它已经被保护了。但是这些选择是留给你的，当你从一个填充元素移动到另一个填充元素时，你需要注意它们，因为它们并不一致

## 成分

与替换模式不同，替换组件往往是更小的部件，可以存在于页面上更大的上下文中。然而，对于过去使用过其他 CSS 框架或者在不同网站上看到过这些约定的人来说，它们中的许多仍然会显得非常熟悉。一些较小的似乎只是波旁苦味库的逻辑扩展，而其他的则提供了更多的功能。

您可以选择的组件包括折叠菜单、面包屑轨迹、徽章、按钮组、淡入元素、flash 消息、悬停动画、分页、视差滚动实现、进度条、滑动菜单、一组常见纹理、工具提示和其他一些组件。

同样，重点是使这些组件尽可能独立和通用，并让它们继承 Bitters 的风格。

我最喜欢的方法之一是使用一个漂亮的 HTML 技巧来提供一个交互式的模态元素，它不依赖于任何 JavaScript，而是利用基于隐藏的 HTML 复选框和 CSS 同级选择器的状态来设计元素的能力。

对于这一个，提供的 HTML 同样非常简单:

```
<div class="modal">
  <label for="modal-1">
    <div class="btn js-btn">Click for Modal</div>
  </label>
  <input class="modal-state" id="modal-1" type="checkbox" />
  <div class="modal-window">
    <div class="modal-inner">
      <label class="modal-close" for="modal-1"></label>
      <h1>Modal Title</h1>
      <p class="intro">Intro text lorem ipsum dolor sit ametm, quas, eaque facilis aliquid cupiditate tempora cumque ipsum accusantium illo modi commodi  minima.</p>
      <p class="body">Body text lorem ipsum dolor ipsum dolor sit sit possimus amet, consectetur adipisicing elit. Itaque, placeat, explicabo, veniam quos aperiam molestias eriam molestias molestiae suscipit ipsum enim quasi sit possimus quod atque nobis voluptas earum odit accusamus quibusdam.</p>
    </div>
  </div>
</div>
```

您可能希望更改一些类，如“body”和“intro ”,以匹配您处理内容分类的方式。SCSS 是奇迹发生的地方。在这种情况下，它的编写方式非常干净地处理了许多跨浏览器问题，同时遵循了许多良好的实践，并保持了响应能力:

```
.modal {
 $modal-padding: 3em;
 $modal-background: $base-background-color;
 $modal-close-color: $light-gray;
 $modal-image-height: 135px;
 $modal-image-width: $modal-image-height;

  label {
 cursor: pointer;
 margin-bottom: 0;
  }

  label img {
 $img-width: 300px;
 border-radius: $img-width/2;
 display: block;
 max-width: $img-width;
  }

  input[type="checkbox"] {
 display: none;
  }

  .btn {
 @extend button;
  }

  .modal-open {
 overflow: hidden;
  }

  .modal-window { // overlay
 @include transition(opacity .25s ease);
 @include position(fixed, 0px 0px 0px 0px);
 background: rgba(0,0,0, .85);
 opacity: 0;
 text-align: left;
 visibility: hidden;
 z-index: 99999999999;

    .modal-bg {
 @include position(absolute, 0px 0px 0px 0px);
 cursor: pointer;
    }
  }

  .modal-close {
 @include position(absolute, ($modal-padding /2) ($modal-padding /2) null null);
 @include size(1.5em);
 cursor: pointer;
 background: $modal-background;

 &:after,
 &:before {
 @include position(absolute, 3px 3px 0 50%);
 @include transform(rotate(45deg));
 @include size(.15em 1.5em);
 background: $modal-close-color;
 content: ';
 display: block;
 margin: -3px 0 0 -1px;
    }

 &:hover:after,
 &:hover:before {
 background: darken($modal-close-color, 10);
    }

 &:before {
 @include transform(rotate(-45deg));
    }
  }

  .modal-inner {
 @include transition(opacity .25s ease);
 border-radius: $base-border-radius;
 background: $modal-background;
 margin: auto;
 max-height: 95%;
 position: relative;
 overflow: auto;
 width: 95%;
 padding: $modal-padding /2;
 margin-top: .6em;

 @include media($medium-screen) {
 padding: $modal-padding;
 width: 60%;
 max-height: 60%;
 margin-top: 10em;
    }

 @include media($large-screen) {
 width: 50%;
 margin-top: 10em;
    }

    h1 {
 color: $base-font-color;
 margin-bottom: .6em;
 text-align: left;
 text-transform: capitalize;
    }

    p.body, p.intro {
 font-size: $base-font-size;
 max-width: 100% !important;
 text-align: left;

      &.intro {
 color: $blue;
 line-height: 1.6em;
      }

      &.body {
 color: $base-font-color;
 line-height: 1.45em;

 @include media($medium-screen) {
 @include columns(2 8em);
        }
      }
    }

    a.cta {
 color: white;
 display: inline-block;
 margin-right: .5em;
 margin-top: 1em;

 &:last-child {
 padding: 0 2em;
      }
    }
  }

 .modal-state:checked + .modal-window {
 opacity: 1;
 visibility: visible;
  }

 .modal-state:checked + .modal-window .modal-inner {
 top: 0;
  }
}

// Based on code by Kasper Mikiewicz
```

正如您所看到的，响应代码默认情况下被嵌入到 SCSS 中，这取决于您处理响应的方式。复制和粘贴这些元素很容易，但重要的是通读代码，并理解一旦将代码放入页面，它是如何发挥魔力的。

请注意，这个魔术的原始代码来自于 Kasper Mikiewicz，波本威士忌的管理者们会小心翼翼地注明他们使用的任何代码的出处。

## 类型系统

波旁笔芯提供的第三个便利是一套优雅和精心调整的类型系统，旨在捡起苦味剂留下的地方，提供定制的字体匹配，可以给这个特殊的网站带来额外的东西。这些也设置了一致的垂直节奏，以获得文本块的愉悦和可读效果。如果你经营一个博客，并希望你的读者阅读多段文字，这是非常重要的。

提供的字体系统包括衬线字体、无衬线字体、传统字体、平板字体和其他字体。这些名字对于在现代网络时代使用字体的人来说应该是熟悉的，但是笔芯网站给出了每个字体的视觉示例，这样你就可以确定你得到了你想要的。一旦你应用了一个类型系统，你将会看到它的样式反映在你定义的范围内的任何填充元素中。

使用笔芯的另一个优点是，如果不支持可下载字体，字体会被设置为将 gracefull 降级为与自定义字体具有相同设计目标的原生字体。这样做的方式应该可以在不同的操作系统上很好地工作，这些操作系统可能没有相同的可用字体。

典型的字体系统笔芯是圆形无衬线字体。这种补充又从样本 HTML 开始，与直接复制粘贴到您的站点相比，您更有可能将样本 HTML 用作指南。需要注意的关键是，`<head>`元素包含了`link`元素，这些元素从 Google 的 CDN 中获取实际的字体文件，以实现快速一致的访问:

```
<head>
  <link href='http://fonts.googleapis.com/css?family=Nunito:400,700,300' rel='stylesheet' type='text/css'>
  <link href='http://fonts.googleapis.com/css?family=Varela+Round' rel='stylesheet' type='text/css'>
</head>

<article class="type-system-rounded">
  <p class="type">Article Type</p>
  <h1>Article Heading</h1>
  <h2>These override some of the styles specified in the <code>_variables.scss</code> and <code>_typography.scss</code> partials in <a href="//bitters.bourbon.io">Bitters</a>. You can replace the typography variables and the header font specifications in Bitters with these styles. Then you won&rsquo;t need the wrapping class <code>.type-system-name</code>.</h2>
  <p class="date">30 Mar 2014</p>
  <p><span>Lorem ipsum dolor sit amet</span>, consectetur adipisicing elit. Consequatur a, ullam, voluptatum incidunt neque doloremque vel inventore distinctio laudantium harum</a> illo quam nulla dolor alias iure impedit! Accusamus! Lorem ipsum dolor sit amet, consectetur adipisicing elit. Accusamus! Lorem ipsum dolor sit amet, consectetur adipisicing elit. Consequatur, a, ullam, voluptatum incidunt neque porro numquam doloremque vel inventore distinctio laudantium harum illo quam nulla dolor alias iure impedit.
    <a href="javascript:void(0)" class="read-more">Read More <span>&rsaquo;</span></a>
  </p>
  <h3>Subheading lorem</h3>
  <p><span>Consequatur ullam, voluptatum</span> incidunt neque porro numquam doloremque vel inventore distinctio laudantium harum illo quam nulla dolor alias iure impedit. Accusamus. Consequatur, a, ullam, voluptatum incidunt neque porro numquam doloremque vel inventore distinctio laudantium harum illo quam nulla dolor alias iure impedit! Accusamus.</p>
  <hr>
  <p class="author">Author Name</p>
</article>
```

所提供的 SCSS 利用了 HTML 中链接到的字体，并以一种使用标准 HTML 元素来展示其最大优势的方式来应用它们。

```
article.type-system-rounded {
 $sans-serif: 'Nunito', sans-serif;
 $sans-serif-2: 'Varela Round', sans-serif;

 @include clearfix;
 text-align: left;

  .type {
 border-bottom: 1px solid;
 display: inline-block;
 font-family: $sans-serif;
 font-size: .7em;
 font-weight: 800;
 margin-bottom: 2em;
 padding: .3em 0;
 text-align: left;
 text-transform: uppercase;
  }

  h1 {
 font-family: $sans-serif-2;
 font-size: 1.8em;
 margin-bottom: .5em;

 @include media($medium-screen) {
 font-size: 2.4em;
    }
  }

  h2 {
 font-family: $sans-serif-2;
 font-size: 1.2em;
 font-weight: 400;
 line-height: 1.4em;
 margin-bottom: .9em;

 @include media($medium-screen) {
 font-size: 1.4em;
    }
  }

  code {
 white-space: nowrap;
 font-family: monaco;
 background: #F7F7F7;
 border: 1px solid #E0E0E0;
 border-radius: $base-border-radius * 1.5;
 padding: .1em .4em;
 font-size: .7em;
 font-style: normal;
  }

  h3 {
 font-family: $sans-serif-2;
 font-size: 1.2em;
 font-weight: 400;
 line-height: 1.3em;
 margin-bottom: .4em;
  }

  p.date {
 color: transparentize($base-font-color, .6);
 font-family: $sans-serif;
 font-size: .9em;
 margin-bottom: .3em;
  }

  p {
 font-family: $sans-serif;
 line-height: 1.4em;
 font-size: 1.05em;
 font-weight: 300;
 margin-bottom: 1.5em;

    span {
 font-family: $sans-serif-2;
 text-transform: uppercase;
 font-size: 0.8em;
    }
  }

  a.read-more {
 display: inline-block;
 font-family: $sans-serif-2;
 font-weight: 700;
 font-size: .8em;
 text-transform: uppercase;
 margin-left: .2em;
 position: relative;

    span {
 position: absolute;
 font-size: 1.5em;
 top: -1px;
 right: -12px;
    }
  }

  hr {
 width: 3em;
  }

  p.author {
 font-family: $sans-serif;
  }
}
```

SCSS 协调字体粗细和行高，以提供最愉悦的阅读体验。当然，如果您选择进行任何修改，代码就在那里，您可以根据自己的喜好进行操作和调整。

## 要记住的事情

虽然对于不想使用预定义框架或模板的人来说，重新填充是一个很好的资源，但在使用它时，有一些事情需要记住。我在笔芯网站上发现的一个烦恼是，当你点击“复制”按钮将一个例子的文本复制到你的剪贴板时，没有反馈。一旦你意识到这一点，你只需要注意并确保你已经复制并粘贴了你所期望的。

另一个问题是，笔芯库是一组静态示例，可以集成到您的页面中，但是它们并没有以多种格式提供。例如，当我写我的标记时，我经常喜欢使用 HAML，但是提供的代码总是标准的 HTML。你是想使用 HTML，把 HTML 改写成 HAML、Jade 或者你自己喜欢的语法，做一些命令行的魔术，还是使用自动转换工具的[，这都是个人的选择。](http://htmltohaml.com/)

同样，Sass 示例都是以流行的 SCSS 格式提供的。如果您使用传统的缩进式 Sass 格式，您可能需要修改您的工作流，以允许正确解释 SCSS 代码。每种语法都有许多优点，最终取决于您喜欢如何进行开发，所以在考虑补充时要记住这一点。

您可能还会注意到 JavaScript 在填充方面的一些不一致。许多组件都是由社区生成的，因此它们可能不完全遵循相同的编码约定。例如，我注意到一些组件使用较旧的`.bind()` jQuery 语法进行事件处理，而其他组件则采用了更现代的`.on()`语法。

只要你有你喜欢的惯例，更新笔芯元素以遵循你喜欢的风格应该不难。

## 给每个人续杯

关于笔芯的伟大之处在于它们与波旁图书馆玩得多么好，以及它们是多么的小和独立。站点设计的灵活性不会因为在页面中添加笔芯而受到影响，你可以很快从没有设计变得相当有吸引力，并为设计做好准备。

对于一个知道自己在做什么，并希望为一个项目设计一个无害的设计，同时提供一个良好的基线交给真正的设计师的开发人员来说，波本威士忌可能是最方便的选择。

## 分享这篇文章