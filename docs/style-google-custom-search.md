# 快速提示:如何手动设置谷歌自定义搜索的样式

> 原文：<https://www.sitepoint.com/style-google-custom-search/>

这篇文章由[马克·布朗](https://www.sitepoint.com/author/mbrown)同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

网站所有者经常决定使用谷歌的[定制搜索引擎](https://cse.google.com) (GCSE)来搜索他们的内容，而不是使用内置和/或定制的搜索功能。原因很简单——工作量要少得多，而且通常会奏效。如果你不需要高级过滤器或自定义搜索参数，那么 GSCE 是你的。

在这个快速技巧中，我将向您展示如何手动*呈现搜索表单(不使用特殊的 GCSE 标签)和一个结果框，该结果框允许更多的控制和一种更简洁的方式来设置搜索输入字段的样式。*

## 问题是

通常，将 GCSE 添加到您的站点就像将脚本和自定义 HTML 标签复制粘贴到您的站点一样简单。在放置特殊 GCSE 标签的地方，将呈现一个输入搜索字段。在此字段中键入并开始搜索，将根据之前配置的参数进行谷歌搜索(例如，仅搜索 sitepoint.com)。

经常出现的一个问题是“我如何改变 GCSE 输入字段的占位符？”。不幸的是，建议的答案经常是错误的，因为它使用了不可靠的`setTimeout`方法来等待 GCSE 的 Ajax 调用完成(确保输入已经附加到 DOM ),然后通过 JavaScript 更改属性。

我们还将查询元素并使用 JS 更改属性，但是我们将使用 GCSE 提供的回调来保证输入已经加载，而不是盲目的`setTimeout()`。

## 创建 GCSE 帐户

搜索引擎是完全在线配置的。第一步是进入[GCSE 站点](https://cse.google.com/cse/all)，点击*添加*。遵循向导，填写您想要搜索的域(通常是您的站点 URL)。您现在可以忽略任何高级设置。

当您点击*完成*时，您会看到三个选项:

1.  *获取代码*，该代码将指导您了解为了让搜索显示在您的网站上，您必须复制什么以及复制到哪里
2.  *公共 URL* 将向您展示您设置的搜索的工作预览
3.  *用于定制搜索的控制面板*

转到*控制面板*，点击*搜索引擎 ID* 并记下该值以备后用。

## HTML 设置

为了测试这一点，我们将创建一个包含所需 HTML 的基本`index.html`和一个包含渲染和定制搜索所需函数的`app.js`文件。

继续创建一个基本的 HTML 文件，其中包含:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>GCSE test</h1>
    <div class="gcse-search-wrapper"></div>
    <div class="gcse-results-wrapper"></div>
    <script src="app.js"></script>
</body>
</html> 
```

我们添加了两个带有特殊类的`<div>`，以便被识别为应该呈现搜索表单和结果的元素。

## 手动渲染功能

现在进入您的`app.js`文件并添加以下内容:

```
var config = {
  gcseId: '006267341911716099344:r_iziouh0nw',
  resultsUrl: 'http://localhost:8080',
  searchWrapperClass: 'gcse-search-wrapper',
  resultsWrapperClass: 'gcse-results-wrapper'
};

var renderSearchForms = function () {
  if (document.readyState == 'complete') {
    queryAndRender();
  } else {
    google.setOnLoadCallback(function () {
      queryAndRender();
    }, true);
  }
};

var queryAndRender = function() {
  var gsceSearchForms = document.querySelectorAll('.' + config.searchWrapperClass);
  var gsceResults = document.querySelectorAll('.' + config.resultsWrapperClass);

  if (gsceSearchForms) {
    renderSearch(gsceSearchForms[0]);
  }
  if (gsceResults) {
    renderResults(gsceResults[0]);
  }
};

var renderSearch = function (div) {
    google.search.cse.element.render(
      {
        div: div.id,
        tag: 'searchbox-only',
        attributes: {
          resultsUrl: config.resultsUrl
        }
      }
    );
    if (div.dataset &&
        div.dataset.stylingFunction &&
        window[div.dataset.stylingFunction] &&
        typeof window[div.dataset.stylingFunction] === 'function') {
      window[div.dataset.stylingFunction](form);
    }
};

var renderResults = function(div) {
  google.search.cse.element.render(
    {
      div: div.id,
      tag: 'searchresults-only'
    });
};

window.__gcse = {
  parsetags: 'explicit',
  callback: renderSearchForms
};

(function () {
  var cx = config.gcseId;
  var gcse = document.createElement('script');
  gcse.type = 'text/javascript';
  gcse.async = true;
  gcse.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') +
    '//cse.google.com/cse.js?cx=' + cx;
  var s = document.getElementsByTagName('script')[0];
  s.parentNode.insertBefore(gcse, s);
})(); 
```

首先，我们为配置声明一些变量。将您之前记下的 ID 放入配置的`gcseId`字段。将本地 index.html 文件的 URL 放入`resultsUrl`字段。这是用户提交查询后搜索将重定向到的位置。此外，GCSE 希望在提供的 URL 上呈现一个结果字段。

### 添加搜索表单

这个函数检查[页面是否已经被加载](https://developer.mozilla.org/en/docs/Web/API/Document/readyState)，如果是，调用负责渲染的函数`queryAndRender()`，或者，如果文档还没有被加载，那么设置一个回调函数，以便在文档完成加载后返回到这里。

### queryAndRender

这个函数使用配置文件中提供的类在 DOM 中查询元素。如果找到包装器 div，调用`renderSearch()`和`renderResults()`分别呈现搜索和结果字段。

### 呈现搜索

这是真正的奇迹发生的地方。

我们使用 Google Search API(关于如何使用*Google . Search . CSE . element*对象[这里](https://developers.google.com/custom-search/docs/element#cse-element)的更多文档)来创建搜索框，如果有一个查询活动(结果)，则创建结果框。

render 函数接受的参数比本例中提供的多，因此如果需要进一步定制，请务必查阅文档。`div`参数实际上是我们希望呈现搜索结果的 div 的 ID，而`tag`参数表示我们到底想要呈现什么(*结果*或*搜索*或两者)。

此外，`renderSearch()`寻找包装器元素的数据属性，如果给定了一个*样式-功能*属性，它将在作用域中寻找该函数名，并将其应用到元素上。这是我们设计元素样式的机会。

```
window.__gcse = {
  parsetags: 'explicit',
  callback: renderSearchForms
}; 
```

在这个代码片段中，我们在全局范围内设置了一个回调变量，以便 GCSE 在内部使用这个变量，并在完成加载后执行回调函数。这是使这种方法比编辑输入字段的占位符(或任何其他东西)的`setTimeout()`解决方案好得多的部分。

## 试运转

到目前为止，我们已经包含了呈现搜索框和结果所需的所有内容。如果你已经安装了 node.js，进入你放置`index.html`和`app.js`文件的文件夹，运行`http-server`命令。默认情况下，它将在端口 8080 上提供本地主机文件夹中的内容。

![Test Run](img/a3269f4ad0c4846eccfd3c8ed2356629.png "Screen Shot 2016-08-02 at 15.10.27.png")

## 造型功能

现在我们准备向搜索 div 添加自定义样式函数。返回到`index.html`，在#searchForm div 上添加一个样式功能属性

```
<div styling-function="removePlaceholder"
id="searchForm" class="gcse-search-wrapper"></div> 
```

现在进入`app.js`，在文件的顶部，在配置变量声明下，添加一个新函数:

```
var removePlaceholder = function(div) {
  var inputField = div.querySelector("input.gsc-input");

  // Change the placeholder
  inputField.placeholder = "Search SitePoint";

  // Remove the background
  inputField.style.background = "none";

  // The background will get re-attached on blur, so add an event that will also remove it on blur
  // Another way to do this would be to de-attach the element from the DOM and then re-attach again, hence wiping the listeners
  inputField.addEventListener("blur", function() {
    inputField.style.background = "none";
  });
}; 
```

现在尝试再次加载测试页面，您将看到一个合适的占位符。

![Voila!](img/407142ee7e88cbe5ef9c5243a4b0c6d1.png "Screen Shot 2016-08-02 at 16.30.11.png")

# 结论

对于快速建立一个简单的搜索，特别是如果网站只是静态 HTML，谷歌定制搜索引擎工作得很好。只需一点点 JavaScript，不仅可以定制搜索表单，还可以定制结果页面，为用户提供更加无缝的体验。

你是在用 GCSE，还是找到了更好的解决方案？下面评论！

## 分享这篇文章