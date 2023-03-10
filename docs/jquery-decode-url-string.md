# jQuery 编码/解码 URL 字符串

> 原文：<https://www.sitepoint.com/jquery-decode-url-string/>

简单的 jQuery 代码片段，用于编码/解码(转换)url 字符串(http 地址)中的 href 参数，以便可以在网页上正确查看它们。例如，%20 是相当于空格的 html，而%40 是一个&符号(@)。

## 编码 URL 字符串

```
var url = $(location).attr('href'); //get current url
//OR
var url = 'folder/index.html?param=#23dd&noob=yes'; //or specify one

var encodedUrl = encodeURIComponent(url);
console.log(encodedUrl);
//outputs folder%2Findex.html%3Fparam%3D%2323dd%26noob%3Dyes
```

## 解码 URL 字符串

```
var url = $(location).attr('href'); //get current url
//OR
var url = 'folder%2Findex.html%3Fparam%3D%2323dd%26noob%3Dyes'; //or specify one

var decodedUrl = decodeURIComponent(url);
console.log(decodedUrl);
//outputs  folder/index.html?param=#23dd&noob=yes
```

## html url 编码参考

```
space 	%20
! 	%21
" 	%22
# 	%23
$ 	%24
% 	%25
& 	%26
' 	%27
( 	%28
) 	%29
* 	%2A
+ 	%2B
, 	%2C
- 	%2D
. 	%2E
/ 	%2F
... 
etc

```

[查看所有 HTML URL 编码参考](http://www.w3schools.com/tags/ref_urlencode.asp)

## 分享这篇文章