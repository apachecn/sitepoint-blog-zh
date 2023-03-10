# cssQuery():用 CSS 选择器查询 DOM

> 原文：<https://www.sitepoint.com/cssquery-query-the-dom-with-css-selectors/>

(Via[Planet Web 2.0](http://www.ajaxian.com/archives/2005/09/cssquery_crossb.html))[CSS query()](http://dean.edwards.name/my/cssQuery/)，一个来自狄恩·爱德华兹的新的 JavaScript 库 [IE7 script fame](https://www.sitepoint.com/fixing-ie/) ，位于[文档对象模型](https://www.w3.org/DOM/DOMTR) (DOM)之上，让你使用 CSS 选择器语法获得对文档元素的引用。

DOM 是一个强大而高效的 API，用于读取、编写和修改 HTML 和 XML 文档。与 JavaScript 一起使用，它是 DHTML 效果的基础。但是有时候这需要很多代码。

考虑这个小片段，它来自我们在 SitePoint 上使用的一个表排序脚本:

```
function sortables_init() {
    // Find all tables with class sortable and make them sortable
    if (!document.getElementsByTagName) return;
    tbls = document.getElementsByTagName("table");
    for (ti=0;ti<tbls.length;ti++) {
        thisTbl = tbls[ti];
        if (((' '+thisTbl.className+' ').indexOf("sortable") != -1) && (thisTbl.id)) {
            ts_makeSortable(thisTbl);
        }
    }
}
```

这段代码非常好，但实际上它所做的只是对每个应用了 Sortable 类的 table 元素调用函数 ts_makeSortable。下面是使用 cssQuery()的代码:

```
function sortables_init() {
    // Find all tables with class sortable and make them sortable
    tbls = cssQuery("table.sortable");
    for (ti=0;ti<tbls.length;ti++) {
        ts_makeSortable(tbls[ti]);
    }
}
```

在典型的 DHTML 脚本中，这样可以节省的代码是惊人的。当然，由于 CSS 选择器必须用纯 JavaScript 来解析和解释，所以您放弃了一点点性能，但是目前还没有几个 JavaScript 应用程序让浏览器屈服。

cssQuery()支持所有的 [CSS1](https://www.w3.org/TR/REC-CSS1) 和 [CSS2](https://www.w3.org/TR/REC-CSS2/) 选择器，以及大量的 [CSS3](https://www.w3.org/TR/2001/CR-css3-selectors-20011113/) 选择器。见鬼，这比目前大多数网络浏览器都要好——而且都是用纯 JavaScript 编写的！

**更新:**Simon Williamson 早在 2003 年 3 月就制作了一个类似的库， [getElementsBySelector()](http://simon.incutio.com/archive/2003/03/25/getElementsBySelector) 。它不支持那么多的 CSS 选择器类型，但是它可能比你在日常使用中需要的更多。结果很可能是它变轻了。

## 分享这篇文章