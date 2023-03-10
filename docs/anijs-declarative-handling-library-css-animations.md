# AniJS:CSS 动画的声明性处理库

> 原文：<https://www.sitepoint.com/anijs-declarative-handling-library-css-animations/>

动画一直是学习编程的学生的最爱。在我最近参加的一个周末网络开发研讨会上，有一个关于使用 CSS 制作动画的讨论。就在那时，我偶然发现了一个用于 CSS 动画的声明式处理库 [AniJS](http://anijs.github.io/) 。

在本教程中，我们将了解 AniJS 是什么，以及如何使用它来创建一些很酷的动画效果。

## AniJS 简介

使用 AniJS，我们可以在 HTML 中使用声明性命令来编写动画。它提供了一种更简单的方法来使用表达逻辑的声明来管理动画。以下是 AniJS 声明性语法的一个示例:

```
<input id="txtName" type="text" data-anijs="if: focus, do: wobble, to: a">
```

在上面的代码中，声明性语法说，*如果被聚焦，则对锚标记元素进行摆动。*

## 入门指南

在本教程的过程中，我们将使用 AniJS 创建一个动画增强的应用程序。这个应用程序将是一个简单的用户注册和登录应用程序，将包括使用 AniJS 的一些动画。本教程的主要焦点是我们如何使用 AniJS 在我们的 web 应用程序中制作动画。

首先，[下载 AniJS](https://github.com/anijs/anijs/archive/master.zip) 并将其包含在你的页面中，或者参考[CDN 版本](http://www.jsdelivr.com/#!anijs)。

```
<script src="http://cdn.jsdelivr.net/anijs/0.4.0/anijs-min.js"></script>
```

对于样式，我们将使用 Dan Eden 的[animate . CSS](http://daneden.github.io/animate.css/)library。

```
<link rel="stylesheet"
      href="http://cdn.jsdelivr.net/animatecss/3.1.0/animate.css">
```

## 创建登录屏幕

首先，我们将创建一个供用户登录的屏幕。以下是 html:

```
<div class="container">

    <div id="divLogin" class="login_screen">
      <h2 id="spnLogin">Sign In</h2>
      <input id="txtLoginId" type="text" placeholder="Email Id" />
      <input id="txtLoginPass" type="password" placeholder="Password" />
      <a href="#" class="btn">
        SignIn
      </a>
      <a href="#" class="signup-link">Register?</a>
    </div><!-- .login-screen -->

</div><!-- .container -->
```

到目前为止，我们只有一个静态的登录页面。对于一些 CSS，它看起来像这样:

参见钢笔 [AniJS 教程，](http://codepen.io/SitePoint/pen/Gtgjx/) [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的静态登录画面。