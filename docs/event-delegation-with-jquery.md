# 使用 jQuery 的事件委托

> 原文：<https://www.sitepoint.com/event-delegation-with-jquery/>

jQuery 使得 JavaScript 中的事件处理变得简单。但是，定义的事件处理程序越多，使用的内存就越多，最终会降低性能并使 UI 运行缓慢。本文研究事件委托如何帮助防止这种情况，以及如何将事件委托应用于 jQuery。

事件委托是一种事件处理技术，它不是将事件处理程序直接附加到要侦听事件的每个元素，而是将单个事件处理程序附加到这些元素的父元素，以侦听其子元素上发生的事件。当处理事件时，检查哪个元素触发了事件，并相应地做出响应。事件委托依赖于 DOM 中的事件冒泡。在这个过程中，子元素上触发的事件沿着 DOM 树向上传播到其父元素，以及其父元素的父元素，等等。，直到到达`document`。程序员也可以使用 [`event.stopPropagation()`](http://api.jquery.com/event.stopPropagation/) 来停止冒泡。请注意，并不是所有的 DOM 事件都会传播——聚焦、模糊、加载和卸载不会。

事件委托使用较少的内存，因为您用单个事件处理程序替换了多个事件处理程序。例如，如果您将事件处理程序附加到一个包含十个链接的列表中的每个链接，那么您将有十个事件处理程序占用内存空间。相反，如果您使用事件委托并在父元素`<ul>`上处理事件，那么您只需创建一个事件处理程序，并且使用的内存比单独附加到每个链接上要少。除了减少内存消耗之外，事件委托还有以下好处。

*   在 DOM 中添加或删除元素时，不需要手动管理事件。如果我们使用传统的事件处理，我们必须将事件处理程序附加到添加到 DOM 的元素上，并从 DOM 中移除的元素中移除事件处理程序。
*   通过更少的事件处理函数，需要管理的代码更少。这可以留给我们更简单的代码，没有任何重复的事件处理逻辑，这有助于保持我们的 JavaScript 良好和[干燥](http://en.wikipedia.org/wiki/Don't_repeat_yourself)。

## jQuery 中事件委托的一个例子

假设您正在开发一个销售小猫图片的单页面应用程序。当页面加载时，显示前 20 只小猫。随着用户向下滚动页面，更多的小猫被加载。我们的 HTML 如下所示。

```
<section id="cats">
  <ul>
    <li>
      <img src="http://placekitten.com/200/200" alt=""/>
      <a href="/moreinfo">More info</a>
      <button>Add to cart</button>
    </li>
    ...
  </ul>
</section>
```

对于传统的事件处理，我们需要连接事件处理程序来:

1.  当用户点击缩略图时显示更大的图片。
2.  当用户点击“更多信息”链接时显示更多信息。
3.  当用户单击“添加到购物车”时，将图片添加到购物车。
4.  将这三个事件附加到用户向下滚动页面时添加的新 DOM 元素。

这段代码类似于下面的例子。请注意，这是样板代码，旨在展示将事件处理程序附加到单个元素与使用事件委托有何不同，因此没有给出`loadImage()`、`moreInfo()`、`addToCart()`和`loadNewKittens()`函数的实现。

```
$(document).ready(function() {
  var cats = $('#cats');

  cats.find('img')
    .on('click', function() {
      loadImage();
    })

  cats.find('a')
    .on('click', function(event) {
      event.preventDefault();
      moreInfo();
    });

  cats.find('button')
    .on('click', function() {
      addToCart();
    });

  $(window).scroll(function() {
    var fragment = loadNewKittens();
    // attach event handlers for new DOM elements
    fragment.find('img')
      .on('click', function() {
        loadImage();
      });

    fragment.find('a')
      .on('click', function(event) {
        event.preventDefault();
        moreInfo();
      });

    fragment.find('button')
      .on('click', function() {
        addToCart();
      });

    fragment.appendTo('#cats ul');
  });
});
```

这相当多的代码。现在，让我们看看如果使用事件委托，我们的代码会是什么样子:

```
$(document).ready(function() {
  $('#cats')
    .on('click', 'img, a, button', function(event) {
      event.preventDefault();
      var target = event.target;

  switch(target.tagName.toLowerCase()) {
    case 'img':
      loadImage();
      break;
    case 'a':
      moreInfo();
      break;
    case 'button':
      addToCart();
      break;
    default:
      // do nothing
  }
});

  $(window).scroll(function() {
    var fragment = loadNewKittens();
    fragment.appendTo('#cats ul');
  });
});
```

关键是可选的第二个参数为 [`on()`](http://api.jquery.com/on/) 。通过在这里传递一个选择器，`on()`知道它正在处理一个委托事件处理程序，而不是一个直接绑定的事件处理程序。

我们的事件处理代码现在也简单多了。通过获得 [`event.target`](http://api.jquery.com/event.target/) ，并打开它的`tagName`，我们可以知道哪个元素触发了事件，并可以做出适当的响应。另外，我们不再需要为`$(window).scroll`中加载的元素附加事件处理程序，因为这些新元素触发的事件被委托给父元素。

使用事件委托时需要注意的一个潜在问题是，任何附加到子元素的事件处理程序都是在被删除的事件处理程序触发之前*处理的。因此，子事件处理程序有可能调用`event.stopPropagation()`或`return false`，这将防止事件冒泡到委托事件处理程序，并让您绞尽脑汁地想为什么您的事件没有被委托。*

## 结论

在本文中，我们已经了解了事件委托。我们已经看到了它如何通过减轻必须承担的事件处理负载来帮助提高您的站点的性能。我们还看到了如何通过`on()`函数在 jQuery 中实现事件委托。

## 分享这篇文章