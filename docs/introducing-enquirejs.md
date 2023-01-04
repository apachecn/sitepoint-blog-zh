# enquire.js 简介

> 原文：<https://www.sitepoint.com/introducing-enquirejs/>

一段时间以来，我一直在使用 enquire.js 库来增加 JavaScript 中媒体查询的使用。我在各种项目中成功地使用了这个库，但是我遇到的大多数开发人员都承认他们从来没有使用过或者甚至没有听说过它。

在本文中，我想向您介绍这个库，解释您为什么以及如何使用它。

## enquire.js 是什么？

<q cite="http://wicky.nillia.ms/enquire.js/">enquire.js 是一个轻量级的纯 JavaScript 库，用于响应 CSS 媒体查询。</q>用我自己的话来说，在用 JavaScript 处理媒体查询时，enquire.js 在 [`window.matchMedia()`](https://developer.mozilla.org/en-US/docs/Web/API/Window/matchMedia) 之上给了你额外的功能和灵活性。

这个库是由 Nick Williams 编写的，它已经存在了大约三年，有相当数量的[开源活动](https://github.com/WickyNilliams/enquire.js)。其目的是*而不是*替换或多填`matchMedia`；相反，它的存在是为了提供围绕媒体查询的额外功能，而这些功能在本地并不存在。

## 我为什么要用它？

有很多可以使用 enquire.js 的用例，包括:

*   在 DOM 中移动节点
*   通过 Ajax 加载补充内容(如广告)
*   加载并运行一个 JavaScript 库(例如 [Packery](http://packery.metafizzy.co/) )来增强页面

下面几节将介绍这些用例。

### DOM 操作

虽然使用 JavaScript 操作 DOM 有一些性能和可维护性方面的考虑，但是有很多原因可以解释为什么会这样做。

让我们假设在你的博客上，你有一个带有图片的简历。在小屏幕上，侧边栏是隐藏的——但是你仍然想在文章的结尾看到简历。您可以根据屏幕大小在 DOM 中移动它，而不是复制生物(和图像)标记，从而避免标记重复。这里有一个例子:

```
window.$ = document.querySelector.bind(document);

enquire.register("screen and (max-width:40em)", {
  match: function() {
    $(".Post-content").appendChild($(".Bio"));
  },

  unmatch: function() {
    $(".Sidebar").insertBefore($(".Bio"), $(".Sidebar").firstChild);
  }
});
```

此示例的现场演示如下所示:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [MwNYjp](http://codepen.io/SitePoint/pen/MwNYjp/) 。

这是一个基本的例子，老实说，并没有比 native `matchMedia()`提供的更多。事实上，下面是你如何只用`matchMedia()`就能做到的:

```
window.$ = document.querySelector.bind(document);

(function() {
  var mq = window.matchMedia("(min-width:40em)");

  mq.addListener(positionBio);

  function positionBio(mediaQuery) {
    if (mediaQuery.matches) {
      $(".Sidebar").insertBefore($(".Bio"), $(".Sidebar").firstChild);
    } else {
      $(".Post-content").appendChild($(".Bio"));
    }
  }

  // On load
  positionBio(mq);
}());
```

此示例的现场演示如下所示:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看到笔[wvbor](http://codepen.io/SitePoint/pen/WvVbor/)。

让我们回到 enquire.js，并进一步分析上面的例子。在下一段代码中，我们将使用`setup`函数。这是一个只要执行了`enquire.register`就运行一次的函数。在这个例子中，我们还将缓存一些选择器来提高代码的性能，使其更具可读性和可维护性。虽然这是一个虚构的例子(没有 enquire.js 也可以做到这一点)，但它使我能够向您介绍`setup`函数，并探索更复杂的例子:

```
window.$ = document.querySelector.bind(document);

enquire.register("screen and (max-width:40em)", {
  setup: function() {
    this.bio = $(".Bio");
    this.content = $(".Post-content");
    this.sidebar = $(".Sidebar");
  },

  match: function() {
    this.content.appendChild(this.bio);
  },

  unmatch: function() {
    this.sidebar.insertBefore(this.bio, this.sidebar.firstChild);
  }
});
```

此示例的现场演示如下所示:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [MwNYbp](http://codepen.io/SitePoint/pen/MwNYbp/) 。

### 补充内容

让我们把注意力集中在另一个用例上:根据视窗的大小通过 Ajax 加载广告内容。这个例子再次使用了`setup()`函数，并引入了 enquire.js 的另一个特性:`deferSetup()`。它还使用了 [reqwest Ajax 库](https://github.com/ded/reqwest)(这里没有输入错误)和 [`classList` API](https://developer.mozilla.org/en-US/docs/Web/API/Element/classList) 来演示如何在没有 jQuery 的情况下编写更复杂的示例。如果您不知道这个 API 是做什么的，您可以阅读文章[探索 classList API](https://www.sitepoint.com/exploring-classlist-api/) 。

在下一个例子中，在`setup`方法中(我提醒你，当这个媒体查询匹配时，只运行一次)，我们使用 jQuery 的`ajax()`方法请求页面`ad.html`，并将结果存储在一个`ad`属性中。然后，我们将`.Ad`的内容设置为属性，并通过添加一个`is-visible`类(用 CSS 处理)来显示容器。

如果媒体查询曾经是“不匹配的”，我们将移除`is-visible`类，这将依次隐藏`.Ad`容器。

最后，我们将使用`deferSetup`标志和`setup()`方法，这样 Ajax 请求只发生一次，并且只在媒体查询匹配时发生。这很有用，因为对 Ajax 的调用会延迟到需要信息的时候，而不是过早地发出请求。

实现我刚才描述的代码如下:

```
window.$ = document.querySelector.bind(document);

enquire.register("screen and (min-width:40em)", {
  deferSetup: true,
  setup: function() {
    this.ad = "";
    this.adContainer = $(".Ad");
    this.visibleClass = "is-visible";
    this.adUrl = "//codepen.io/damonbauer/pen/LVgxxN.html";

    reqwest({ url: this.adUrl, crossOrigin: true})
      .then(function(response) {
        this.ad = response;
      }.bind(this)).fail(function() {
        this.ad = "Ad could not be loaded.";
      }.bind(this)).always(function(response) {
        this.adContainer.innerHTML = this.ad;
      }.bind(this));
  },

  match: function() {
    this.adContainer.classList.add(this.visibleClass);
  },

  unmatch: function() {
    this.adContainer.classList.remove(this.visibleClass);
  }
});
```

除了前面的 JavaScript 代码，我们还必须使用下面报告的小 CSS 片段:

```
.Ad {
 display: none;
}

.is-visible {
 display: block;
}
```

此示例的现场演示如下所示:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [vOoEyb](http://codepen.io/SitePoint/pen/vOoEyb/) 。

### 页面增强

在这一节中，我们将看看如何添加 Packery 库来增强更大尺寸的页面。根据其[文档](http://packery.metafizzy.co/)，Packery“填补空白”。它帮助你完成类似于 Pinterest 的布局，卡片填充网格中的可用空间。

假设您的页面中有以下 HTML:

```
<div id="Container">
  <div class="Item">...</div>
  <div class="Item">...</div>
  <div class="Item">...</div>
  ...
</div>
```

下面的代码将使用 packery 库来实现我之前描述的功能:

```
enquire.register("screen and (min-width:30em)", {
  deferSetup: true,

  setup: function() {
    this.container = $( "#Container" );
  },

  match: function() {
    this.container.packery({
      itemSelector: '.Item'
    });
  },

  unmatch: function() {
    this.container.packery('destroy');
  }
});
```

此示例的现场演示如下所示:

通过[码笔](http://codepen.io)上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [OVKPWL](http://codepen.io/SitePoint/pen/OVKPWL/) 。