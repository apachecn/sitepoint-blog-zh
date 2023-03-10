# 使用 jQuery 更改 iFrame 内容上的 CSS

> 原文：<https://www.sitepoint.com/jquery-change-css-iframe-content/>

在这篇文章中，我还提供了一些在不同域中尝试改变 iframed 内容的 CSS 的失败案例。还有一些关于如何在同一个域的 iframe 中改变 css 的信息，据我所知，如果没有代理通行证之类的东西是无法做到的。

## 跨领域工作

**工作原理:**

| 比较的 URL | 结果 | 理由 |
| http://www.example.com/dir/page.html | 成功 | 相同的协议和主机 |
| http://www.example.com/dir2/other.html | 成功 | 相同的协议和主机 |
| http://www.example.com:81/dir/other.html | 失败 | 相同的协议和主机，但端口不同 |
| https://www.example.com/dir/other.html | 失败 | 不同的协议 |
| http://en.example.com/dir/other.html | 失败 | 不同的主机 |
| http://example.com/dir/other.html | 失败 | 不同的主机(需要完全匹配) |
| http://v2.www.example.com/dir/other.html | 失败 | 不同的主机(需要完全匹配) |

### 插入 iFrame:

```
 `[<a%20target=](<a%20target=)` Ways to to change CSS in an iframe
Some of these work, and some don't but i've included both for you to ponder amd make up your own decisions.

This method didn't work for me.
[js]
var text = 'hi';
var content = "" + text + "";

var iframe = document.createElement("iframe");
iframe.src = ' document.body.appendChild(iframe);

var doc = iframe.document;
if(iframe.contentDocument)
doc = iframe.contentDocument; // For NS6
else if(iframe.contentWindow)
doc = iframe.contentWindow.document; // For IE5.5 and IE6
// Put the content in the iframe
doc.open();
doc.writeln(content);
doc.close();

doc.getElementsByTagName("body").style.backgroundColor = 'red';
```

这个方法对我没用。

```
var cssLink = document.createElement("link")
cssLink.href = "style.css";
cssLink .rel = "stylesheet";
cssLink .type = "text/css";
frames['frame1'].document.body.appendChild(cssLink);
```

这个方法对我很有效。

```
var frm = frames['frame'].document;
var otherhead = frm.getElementsByTagName("head")[0];
var link = frm.createElement("link");
link.setAttribute("rel", "stylesheet");
link.setAttribute("type", "text/css");
link.setAttribute("href", "style.css");
otherhead.appendChild(link);
```

这个方法对我很有效。

 `var iframe = top.frames[name]。文档；
var css = " +
'` 

## `分享这篇文章`