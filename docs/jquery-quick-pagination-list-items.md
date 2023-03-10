# 使用 jQuery 生成快速分页

> 原文：<https://www.sitepoint.com/jquery-quick-pagination-list-items/>

SitePoint 会不时删除存放在不同 HTML 页面上的多年前的演示。我们这样做是为了降低过时代码的风险，因为暴露的漏洞会给我们的用户带来风险。谢谢你的理解。

使用快速 jQuery 分页在几秒钟内将长列表和页面内容转换成带编号的页面！

[下载](https://github.com/sdeering/jquery-quick-pagination)

## 快速分页的好处

*   可以在几秒钟内用于任何列表
*   您可以在顶部、底部或顶部和底部指定导航
*   您可以指定每页显示的项目数
*   非常轻量级，只有 3kb！
*   我已经测试了所有的浏览器，它非常稳定

## 类似分页插件的缺点

*   它目前只对列表有效(但是你可以在 div 上使用它，你必须把它们放在 li 标签中)。
*   它目前没有后退和前进按钮。

### 用法示例 1

默认选项 10 每页列出项目，页面导航在底部。

```
$("ul.pagination1").quickPagination();
```

### 用法示例 2

默认选项 10 每页列出项目，页面导航在顶部和底部。

```
$("ul.pagination2").quickPagination({pagerLocation:"both"});
```

### 用法示例 3

指定每页 3 个列表项和顶部和底部的页面导航。

```
$("ul.pagination3").quickPagination({pagerLocation:"both",pageSize:"3"});
```

## 分享这篇文章