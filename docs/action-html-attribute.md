# 动作(HTML 属性)

> 原文：<https://www.sitepoint.com/action-html-attribute/>

## 描述

除非表单提交后进行某种处理，否则`form`是没有用的。action 属性用于通知浏览器在按下`"submit"`按钮后调用哪个页面(或脚本)。

## 例子

在这里，`action`属性告诉浏览器将`form`数据发送到一个处理表单的 PHP 页面(该页面可能会将表单数据转换成对电子邮件更友好的格式):

```
<form *action="form-to-email.php"* method="post"
    accept-charset="windows-1252">
  <div>
    <label for="txtname">Name:</label>
    <input type="text" name="txtname" id="txtname"/>
  </div>
 ⋮
</form>

```

## 价值

这个元素的值是一个文档的 URL，这个文档可能在同一个服务器上(例如，一个有各种表单处理脚本的共享 CGI 文件夹)，甚至是一个完全独立的服务器上的页面或脚本(可能是一个免费的表单处理服务)。

## 分享这篇文章