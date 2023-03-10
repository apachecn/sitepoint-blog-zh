# 最佳 jsperf(2000-2013)——第 1/3 部分

> 原文：<https://www.sitepoint.com/jsperf1/>

在浏览了数百个 jsperf 测试后，今天我向大家介绍 jsperf——最佳(2000-2013)。对于那些不知道的人来说，[jsperf.com](http://jsperf.com)是一个为我们提供 JavaScript 性能测试的在线工具。对**提高你的 JavaScript & jQuery 代码性能** /比较不同算法和代码片段的速度非常有用！这篇文章给出了我所见过的(到目前为止)一些最好的结果的快速概述。

测试有很多，所以我把它们分成了以下速度测试组:

*   [第 1 部分——jQuery 选择器、JavaScript 字符串、JavaScript DOM 缓存/DOM 遍历](http://www.jquery4u.com/testing/jsperf1/)
*   [第二部分——jQuery Each/Loops、jQuery AJAX、jQuery Animation/CSS、JavaScript 模板](http://www.jquery4u.com/testing/jsperf2/)
*   [第 3 部分–各种速度测试(其他测试中最好的！)](http://www.jquery4u.com/testing/jsperf3/)

一如既往地留下评论，如果我错过了任何你认为重要的，请分享。让我们改进编码，提高网络速度！

## jQuery 选择器速度测试

这些测试比较了不同 jQuery 选择器的性能。

### id vs 类 vs 标签 vs 伪 vs 属性选择器

[![jquery-id-vs-tag](img/c4ea2ccea1366b6fc956f420b4baa302.png)](http://jsperf.com/id-vs-class-vs-tag-selectors/2)

### 快速查看不同 jQuery 选择器的速度。

[![jquery-parent-child-selectors](img/1c0def9f7db5377afff46df3842d19c8.png)](http://jsperf.com/jquery-selectors-context/2)

## JavaScript 字符串速度测试

### 连接文字

比较连接文字与一个长字符串赋值
[![js-literals](img/a51acad4bfb46fa1baa0fd6118d6d8de.png)](http://jsperf.com/literalconcat) 的速度

### 字符串连接与正则表达式

[![str-vs-regex](img/40daa38ba53cee366466f0989b77bad0.png)](http://jsperf.com/strvsregex)

### jQuery。大小()与长度。

测试 jQuery 方法 size()比直接检查长度慢多少。
[![size-vs-length](img/c846731b34aa3010ba0f32d721452ab8.png)](http://jsperf.com/size-vs-length)

## JavaScript DOM 缓存/遍历

### DOM 缓存与无 DOM 缓存

在一个基本的 JavaScript 命名空间模式中测试 DOM 缓存的性能。
[![dom-cache-loop-111](img/90e39e50bf30d569d298933c94c1a6ae.png)](http://jsperf.com/dom-caching-namespace/5)

### 在循环外缓存 DOM 引用

[![dom-cache1](img/e292cd4b6b920fe44a25809a991bd59c.png)](http://jsperf.com/sw-loop-cache)

### dom 缓存练习和节点类型

[![dom-caching-2](img/3d06b7a05f164ce093627a88b22d30f9.png)](http://jsperf.com/dom-caching-excercise)

### jQuery 过滤器与查找。

另请参见 [JQUERY 过滤器函数示例](http://www.jquery4u.com/jquery-functions/jquery-filter-function/)。
T3![filter-vs-find](img/3f1c02aa754cc133f41b9508a3ce2aab.png)

## 分享这篇文章