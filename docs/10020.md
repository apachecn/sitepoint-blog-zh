# 自定义 Google 搜索 API 设置教程

> 原文：<https://www.sitepoint.com/google-search-api-ajax/>

所以今天我做了一个关于如何为你的博客/网站设置谷歌搜索 API 的小教程。这可以用来代替 WordPress 主题中通常提供的标准搜索工具。这真的很容易设置！您可以自定义搜索，只显示您的博客/网站和其他搜索结果，如图像、视频、新闻等(完整列表见下文)。

## 怎么做

1.  获取您的 [Google API 密钥](http://code.google.com/apis/loader/signup.html)
2.  将您的密钥放入以下代码中
3.  自定义搜索(添加您自己的域名！)
4.  定制适合你的网站的风格
5.  尽情享受吧！

## JavaScript

```
google.load("search", "1", {"language" : "en"});

// Call this function when the page has been loaded
function initialize() {
var searchControl = new google.search.SearchControl();

// site restricted web search
var siteSearch = new google.search.WebSearch();
siteSearch.setUserDefinedLabel("jquery4u.com");
siteSearch.setUserDefinedClassSuffix("siteSearch");
siteSearch.setSiteRestriction("jquery4u.com");
searchControl.addSearcher(siteSearch);

//Search API Topics
searchControl.addSearcher(new google.search.WebSearch());
searchControl.addSearcher(new google.search.NewsSearch());
searchControl.addSearcher(new google.search.BlogSearch());
searchControl.addSearcher(new google.search.ImageSearch());
searchControl.addSearcher(new google.search.BookSearch());
searchControl.addSearcher(new google.search.VideoSearch());
//others
//searchControl.addSearcher(new google.search.LocalSearch());
//searchControl.addSearcher(new google.search.PatentSearch());

// create a drawOptions object
var drawOptions = new google.search.DrawOptions();
// tell the searcher to draw itself in tabbed mode
drawOptions.setDrawMode(google.search.SearchControl.DRAW_MODE_TABBED);
searchControl.draw(document.getElementById("searchcontrol"), drawOptions);
}
google.setOnLoadCallback(initialize);
```

## HTML

```
 在下面输入搜索！ 

## CSS

```
/* the width of the controls (keep same as gsc-results for flush look) */
#searchcontrol { width:600px; }
.gsc-control { width:600px; }
/* the width of the search results box; no height value = nice auto look  */
.gsc-results { width:600px; }
/* the width of the search input */
.gsc-input { width:20px; }
/* hide "powered by google" (optional) */
.gsc-branding { display:none; }
/* custom colors */
.gs-title a { color:orange; font-weight:bold; }
#searchcontrol a { color:orange; }
```

[开发者指南](http://code.google.com/apis/websearch/docs/)

## 分享这篇文章

```