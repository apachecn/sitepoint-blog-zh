# 与承诺并行预加载图像

> 原文：<https://www.sitepoint.com/preloading-images-in-parallel-with-promises/>

这篇文章的题目其实挺具体的。最近，我遇到了一个需要并行预加载大量图像的情况。在给定的限制条件下，它比最初预期的更具挑战性，我在旅途中确实学到了很多。但首先，让我描述一下开始前不久的情况。

假设我们在页面上有一些“甲板”。广义地说，一副牌是一组图像的集合。我们希望预加载每副牌的图像，并能够知道一副牌何时加载完所有图像。在这一点上，我们可以自由地运行任何我们想要的代码，比如添加一个类到甲板上，运行一个图像序列，记录一些东西，无论什么…

起初，这听起来很容易。这听起来甚至非常简单。虽然也许你和我一样，忽略了一个细节:我们希望所有的甲板平行装载，而不是按顺序。换句话说，我们不希望加载来自平台 1 的所有图像，然后加载来自平台 2 的所有图像，然后加载来自平台 3 的所有图像，依此类推。

事实上，这并不理想，因为我们最终有甲板等待前一个完成。因此，在第一个平台有几十个图像，而第二个平台只有一个或两个图像的情况下，我们必须等待第一个平台完全加载，然后才能准备第二个平台。呃，不太好。我们肯定能做得更好！

因此，我们的想法是并行装载所有的甲板，这样当一个甲板满载时，我们就不必等待其他甲板。为此，大致要点是加载所有卡片组的第一个图像，然后加载所有卡片组的第二个图像，依此类推，直到所有图像都已预加载。

好的，让我们从创建一些标记开始，这样我们就能对将要发生的事情达成一致。顺便说一下，在这篇文章中，我假设你熟悉承诺的概念。如果不是这样，我推荐这个[小阅读](https://www.promisejs.org/)。

## 加价

就标记而言，deck 只不过是一个元素，比如一个`div`，一个`deck`类，这样我们就可以定位它，还有一个`data-images`属性，包含一组图像 URL(如 JSON)。

```
<div class="deck" data-images='["...", "...", "..."]'>...</div>
<div class="deck" data-images='["...", "..."]'>...</div>
<div class="deck" data-images='["...", "...", "...", "..."]'>...</div>
```

## 准备场地

在 JavaScript 方面，这有点复杂，这并不奇怪。我们将构建两个不同的东西:一个 deck *类*(请将它放在很大的引号之间，不要对术语吹毛求疵)，和一个*预加载器*工具。

因为预加载程序必须知道来自所有平台的所有图像，以便以特定的顺序加载它们，所以它需要在所有平台之间共享。一个 deck 不能有自己的预加载程序，否则我们最终会遇到最初的问题:代码是按顺序执行的，这不是我们想要的。

因此，我们需要一个预装器，它将被传递到每个甲板。后者将其图像添加到预加载器的队列中，并且一旦所有甲板都将其项目添加到队列中，预加载器就可以开始预加载。

执行代码片段应该是:

```
// Instantiate a preloader
var ip = new ImagePreloader();
// Grab all decks from the DOM
var decks = document.querySelectorAll('.deck');

// Iterate over them and instantiate a new deck for each of them, passing the
// preloader to each of them so that the deck can add its images to the queue
Array.prototype.slice.call(decks).forEach(function (deck) {
  new Deck(deck, ip);
});

// Once all decks have added their items to the queue, preload everything
ip.preload();
```

希望有意义，到此为止！

## 搭建平台

取决于你想对这副牌做什么，“课”可能会很长。对于我们的场景，我们唯一要做的事情是在节点的图像加载完成后向节点添加一个`loaded`类。

`Deck`功能没有太大的关系:

1.  加载数据(从`data-images`属性)
2.  将数据附加到预加载器队列的末尾
3.  告诉预加载器当数据已经被预加载时做什么

```
var Deck = function (node, preloader) {
  // We get and parse the data from the `data-images` attribute
  var data = JSON.parse(node.getAttribute('data-images'));

  // We call the `queue` method from the preloader, passing it the data and a
  // callback function
  preloader.queue(data, function () {
    node.classList.add('loaded');
  });
};
```

到目前为止一切都很顺利，不是吗？唯一剩下的是预加载程序，尽管它也是本文中最复杂的代码。

## 构建预加载程序

我们已经知道，我们的预加载程序需要一个`queue`方法来将图像集合添加到队列中，还需要一个`preload`方法来启动预加载。它还需要一个助手函数来预加载图像，名为`preloadImage`。让我们从这个开始:

```
var ImagePreloader = function () { ... };
ImagePreloader.prototype.queue = function () { ... }
ImagePreloader.prototype.preloadImage = function () { ... }
ImagePreloader.prototype.preload = function () { ... }
```

预加载程序需要一个内部队列属性来保存它必须预加载的卡片组，以及它们各自的回调。

```
var ImagePreloader = function () {
  this.items = [];
}
```

`items`是一个对象数组，其中每个对象有两个键:

*   `collection`包含要预加载的图像 URL 的数组，
*   `callback`包含甲板满载时要执行的功能。

知道了这些，我们就可以写`queue`方法了。

```
// Empty function in case no callback is being specified
function noop() {}

ImagePreloader.prototype.queue = function (array, callback) {
  this.items.push({
    collection: array,
    // If no callback, we push a no-op (empty) function
    callback: callback || noop
  });
};
```

好吧。此时，每副牌都可以将其图像添加到队列中。我们现在必须构建`preload`方法，它将负责实际预加载图像。但是在开始编写代码之前，让我们后退一步来理解我们需要做什么。

我们的想法是不要一个接一个地从每副牌中预加载所有的图像。这个想法是预加载每副牌的第一张图片，然后是第二张，第三张，依此类推。

*预加载*一个图像意味着从 JavaScript 创建一个新图像(使用`new Image()`)并对其应用一个`src`。这将提示浏览器异步加载源代码。由于这个异步过程，我们需要注册一个承诺，当资源被浏览器下载后，这个承诺将被解析。

基本上，我们将用一个承诺替换数组中的每个图像 URL，该承诺将在浏览器加载给定图像时解析。此时，我们将能够使用`Promise.all(..)`获得一个最终的承诺，当数组中的所有承诺都已解析时，该承诺也将解析。每副牌都有这个。

让我们从`preloadImage`方法开始:

```
ImagePreloader.prototype.preloadImage = function (path) {
  return new Promise(function (resolve, reject) {
    // Create a new image from JavaScript
    var image = new Image();
    // Bind an event listener on the load to call the `resolve` function
    image.onload  = resolve;
    // If the image fails to be downloaded, we don't want the whole system
    // to collapse so we `resolve` instead of `reject`, even on error
    image.onerror = resolve;
    // Apply the path as `src` to the image so that the browser fetches it
    image.src = path;
  });
};
```

现在，是`preload`方法。它做两件事(因此可能被分成两个不同的功能，但这超出了本文的范围):

1.  它以特定的顺序(每副牌中的第一个图像，然后第二个，然后第三个……)用承诺替换所有的图像 URL
2.  对于每一副牌，它注册一个承诺，当该副牌中的所有承诺都已解决时，该承诺从该副牌调用回调。)

```
ImagePreloader.prototype.preload = function () {
  // Promises are not supported, let's leave
  if (!('Promise' in window)) {
    return;
  }

  // Get the length of the biggest deck
  var max = Math.max.apply(Math, this.items.map(function (el) {
    return el.collection.length;
  }));

  // Loop from 0 to the length of the largest deck
  for (var i = 0; i < max; i++) {
    // Iterate over the decks
    this.items.forEach(function (item) {
      // If the deck is over yet, do nothing, else replace the image at
      // current index (i) with a promise that will resolve when the image
      // gets downloaded by the browser.
      if (typeof item.collection[i] !== 'undefined') {
        item.collection[i] = this.preloadImage(item.collection[i])
      }
    }, this);
  }

  // Iterate over the decks
  this.items.forEach(function (item, index) {
    // When all images from the deck have been fetched by the browser
    Promise.all(item.collection)
      // Execute the callback
      .then(function () { item.callback() })
      .catch(function (err) { console.log(err) });
  });
};
```

就是这样！没那么复杂，你同意吗？

## 把事情推进一步

代码工作得很好，尽管使用回调来告诉预加载程序在一副牌被加载时该做什么不是很优雅。你可能想要使用一个承诺而不是一个回拨，特别是因为我们一直在使用承诺！

我不知道如何解决这个问题，所以我不得不同意我请我的朋友 Valérian Galliat 帮助我。

我们这里用的是*延期承诺*。延期承诺不是本机承诺 API 的一部分，因此我们需要填充它；谢天谢地，这只是几行代码的问题。基本上，延期承诺是一种你可以以后再解决的承诺。

将它应用到我们的代码中，只会改变很小的东西。第`.queue(..)`法:

```
ImagePreloader.prototype.queue = function (array) {
  var d = defer();

  this.items.push({
    collection: array,
    deferred: d
  });

  return d.promise;
};
```

`.preload(..)`方法中的分辨率:

```
this.items.forEach(function (item) {
  Promise.all(item.collection)
    .then(function () { item.deferred.resolve() })
    .catch(console.log.bind(console));
  });
```

最后，当然是我们将数据添加到队列中的方式！

```
preloader.queue(data)
  .then(function () {
    node.classList.add('loaded');
  })
  .catch(console.error.bind(console));
```

我们完事了。

如果您想看看实际运行的代码，请看下面的演示:

在[码笔](http://codepen.io)上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [QjjGaL](http://codepen.io/SitePoint/pen/QjjGaL/) 。