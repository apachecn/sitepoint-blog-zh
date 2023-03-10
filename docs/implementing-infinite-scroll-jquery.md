# 在 jQuery 中实现无限滚动

> 原文：<https://www.sitepoint.com/implementing-infinite-scroll-jquery/>

就我记忆所及，当有大量内容需要显示时，web 开发人员会转向老式的分页方式。不要误解我的意思，分页仍然是一种非常有效的显示内容的方式，但是，在这篇文章中，我们将讨论一种替代方式——无限滚动。这种技术也称为惰性滚动(lazy scroll)或 unpaginate，当用户滚动完页面的现有内容时，它通过 Ajax 加载新内容。包括脸书和 Pinterest 在内的一些互联网巨头都在使用无限滚动。在这篇文章中，我们将讨论如何构建自己的 jQuery 插件来实现无限滚动。

## 权衡

优势显而易见。为了获得更多的内容，你不需要被重定向到一个新的页面(当页面加载时，你的焦点倾向于转移到一个不同的区域)。通过实现无限滚动，你基本上是在控制用户在页面上的焦点！

无限滚动并非在所有情况下都有效。例如，如果用户单击一个链接，然后使用浏览器的 Back 按钮，用户就会失去他/她在通过 Ajax 加载的数据流中的位置。实现这个特性的一个预防措施是在新的选项卡或窗口中加载新的内容。

无限滚动的一个相关缺点是它不能保存流中的位置。假设您想通过电子邮件与您的朋友分享无限滚动页面上的一些内容。您不能这样做，因为 URL 会将您带回到初始位置。因此，在你决定继续做之前，想想你的网站的可用性。

此外，在实现无限滚动之前，请记住它对搜索引擎并不友好。为了避免任何关于搜索引擎可见性的问题，请确保您提供了分页或站点地图的替代方案。

## 入门指南

我们将从设计一个非常简单的页面开始。示例 HTML 和 CSS 的重要部分如下所示。其余的文件可以通过点击本教程末尾的演示链接来查看。

**HTML**

```
<div id="data-container">
  <div class="data-item">
    Hi! I am the first item.
  </div>
  <div class="data-item">
    Hi! I am the second item.
  </div>
  <div class="data-item">
    Hi! I am the third item.
  </div>
  <div class="data-item">
    Ok, this is getting boring.
  </div>
  <div class="data-item">
    Let's try something new.
  </div>
  <div class="data-item">
    How about loading some more items through AJAX?
  </div>
  <div class="data-item">
    Click the button below to load more items.
  </div>
</div>
<div id="button-more" onclick="lazyload.load()">
  Load more items
</div>
<div id="loading-div">
  loading more items
</div>
```

**CSS**

```
#data-container {
  margin: 10px;
}

#data-container .data-item {
  background-color: #444444;
  border-radius: 5px;
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  padding: 5px;
  margin: 5px;
  color: #fff;
}

#loading-div {
  display: none;
}

#button-more{
  cursor: pointer;
  margin: 0 auto;
  background-color: #aeaeae;
  color: #fff;
  width: 200px;
  height: 50px;
  line-height: 50px;
}
```

## 基本工作流程

如果你看一下我们创建的文档，当你点击“加载更多”按钮时，应该会加载新的帖子。这里有几点需要考虑。

*   需要向 URL 发出一个请求，该请求返回要添加到页面的新项目。
*   如果再次点击按钮，应该重复这个过程，但是第二次应该返回更新的帖子。
*   新的职位应提供在每一个后续的请求，直到没有更多的职位显示。
*   当没有更多的帖子时，你应该告诉用户他已经到了末尾。

## Ajax 模式

理想情况下，您必须声明一个变量来存储页码，这反过来会更改发送请求的 URL。在我们的演示中，我们有三个这样的页面——`2.html`、`3.html`，以及一个空的`4.html`用于演示目的。

当你点击按钮加载更多的文章时，在请求成功通过和新的文章被加载之前还有一段时间。在这种情况下，我们隐藏 load 按钮，并显示一些文本，说明正在加载新项目:

```
$(buttonId).hide();
$(loadingId).show();
```

## 将数据追加到页面

首先，我们需要撤销请求过程中所做的更改，即再次显示“load more”按钮并隐藏文本。其次，我们需要将收到的响应附加到页面上已经存在的项目列表中。注意，在演示中，我们直接接收 HTML 标记以保持简单。您也可以发送 JSON 响应，添加更多变量，比如消息或状态。附加代码如下所示。

```
$(buttonId).show();
$(loadingId).hide();
$(response).appendTo($(container));
page += 1;
```

## 处理数据结尾

一旦你到达文章的末尾，你需要向你的用户显示页面上没有更多的文章要加载。这可以通过多种方式实现。我们可以通过嵌入在响应本身中的代码或消息来发送状态。然而，由于我们在这个例子中直接使用 HTML 标记，一个空的响应标志着流的结束。

```
$.ajax({
...
  success: function(response) {
    // What to do if the response is empty
    if (response.trim() == "") {
      $(buttonId).fadeOut();
      $(loadingId).text("No more entries to load!");
      return;
    }
    // Do something if the response is right.
  },
...
});
```

## 结论

我们想出的演示本质上是非常基础的，如果我们投入更多的努力，我们可以做得更好。首先，我们可以完全移除按钮，并在用户向下滚动到页面末尾时调用该函数。这将消除用户点击按钮的额外步骤，并使整个过程更快。其次，我们可以通过 JSON 发送原始数据，并使用 jQuery 本身创建标记。例如:

```
$.each(response.items, function(index, value) {
  $("<div />", {
    "class" : "data-item",
    "text" : value
  });
});
```

最后，JSON 响应可以包含一条消息，声明请求是否正确通过、数据以及是否有更多的帖子要加载。在这种情况下，这是一种更有效的发送响应的方式。

想了解更多关于无限卷轴的信息，你可以访问这个致力于这项事业的网站。它包含了这个想法的一般信息，以及你可以用来在你的网站上实现它的现有工具。

GitHub 页面上有一个现场演示。GitHub 上也有[代码](http://github.com/sdaityari/lazy_load)。

## 分享这篇文章