# 使用 reveal.js 创建漂亮的 HTML 演示文稿

> 原文：<https://www.sitepoint.com/creating-slick-html-presentations-using-reveal-js/>

做演示并不是什么新鲜事。但是这一次必须是特别的，我们有竞争。演示是创造整体印象的一种方式。为了给人留下印象，我们需要与众不同、令人印象深刻的东西。不像传统的做演示的方式，(PowerPoint 等。)，我们决定这次做得不一样。就在那时，我们撞上了 [reveal.js](http://lab.hakim.se/reveal-js/#/) 。

reveal.js 是一个使用 HTML 创建漂亮演示的框架。它有许多灵活的功能，如降价内容、嵌套幻灯片、PDF 导出和用于控制幻灯片导航的 JavaScript APIs。使用 reveal.js 的演示文稿是用 HTML 编写的。对于那些不太懂技术的人来说，还有一个界面[。](https://slid.es)

## 设置 reveal.js

在使用 reveal.js 之前，你应该已经在你的机器上安装了 [Node.js](http://nodejs.org/) 和 [Grunt](http://gruntjs.com/getting-started#installing-the-cli) 。接下来的步骤是从 GitHub 克隆 [reveal.js 库](https://github.com/hakimel/reveal.js.git)，安装所有的依赖项，并启动 reveal 服务器。下面的命令列表用于完成这些步骤。

```
git clone https://github.com/hakimel/reveal.js.git
cd reveal.js
npm install
grunt serve
```

接下来，在浏览器中导航到`http://localhost:8000/`观看演示。

## 创建演示文稿

下面的代码清单是一个基本的 reveal.js HTML 页面，不包含演示幻灯片。在`body`标签结束之前，我们有一个脚本，它是所有表示配置的关键。我们可以配置许多选项。例如，我们可以选择显示演示进度，启用过渡，并为演示设置主题。一旦我们开始在演示文稿中添加幻灯片，我们将对此进行更深入的探讨。

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>reveal.js - The HTML Presentation Framework</title>
  <meta name="description" content="A framework for easily creating beautiful presentations using HTML">
  <meta name="author" content="Hakim El Hattab">
  <meta name="apple-mobile-web-app-capable" content="yes" />
  <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <link rel="stylesheet" href="css/reveal.min.css">
  <link rel="stylesheet" href="css/theme/default.css" id="theme">

  <!-- For syntax highlighting -->
  <link rel="stylesheet" href="lib/css/zenburn.css">

  <!-- If the query includes 'print-pdf', include the PDF print sheet -->
  <script>
    if (window.location.search.match(/print-pdf/gi)) {
      var link = document.createElement('link');

      link.rel = 'stylesheet';
      link.type = 'text/css';
      link.href = 'css/print/pdf.css';
      document.getElementsByTagName('head')[0].appendChild(link);
    }
  </script>

  <!--[if lt IE 9]>
    <script src="lib/js/html5shiv.js"></script>
  <![endif]-->
</head>
<body>

  <!-- Slides  content to be added here -->

  <script src="lib/js/head.min.js"></script>
  <script src="js/reveal.min.js"></script>
  <script>
    // Full list of configuration options available here:
    // https://github.com/hakimel/reveal.js#configuration
    Reveal.initialize({
      controls: true,
      progress: true,
      history: true,
      center: true,

      theme: Reveal.getQueryHash().theme, // available themes are in /css/theme
      transition: Reveal.getQueryHash().transition || 'default', // default/cube/page/concave/zoom/linear/fade/none

      // Parallax scrolling
      // parallaxBackgroundImage: 'https://s3.amazonaws.com/hakim-static/reveal-js/reveal-parallax-1.jpg',
      // parallaxBackgroundSize: '2100px 900px',

      // Optional libraries used to extend on reveal.js
      dependencies: [{
        src: 'lib/js/classList.js',
        condition: function () {
          return !document.body.classList;
        }
      }, {
        src: 'plugin/markdown/marked.js',
        condition: function () {
          return !!document.querySelector('[data-markdown]');
        }
      }, {
        src: 'plugin/markdown/markdown.js',
        condition: function () {
          return !!document.querySelector('[data-markdown]');
        }
      }, {
        src: 'plugin/highlight/highlight.js',
        async: true,
        callback: function () {
          hljs.initHighlightingOnLoad();
        }
      }, {
        src: 'plugin/zoom-js/zoom.js',
        async: true,
        condition: function () {
          return !!document.body.classList;
        }
      }, {
        src: 'plugin/notes/notes.js',
        async: true,
        condition: function () {
          return !!document.body.classList;
        }
      }]
    });
  </script>
</body>
</html>
```

## 幻灯片

现在，我们将开始在空的演示文稿中添加幻灯片。让我们添加第一张幻灯片，使用下面的 HTML。`section`元素代表幻灯片。我们甚至可以将幻灯片嵌套在其他幻灯片中，如示例中嵌套的`section`所示。

```
<div class="reveal">
  <div class="slides">
    <section>
      Welcome to Reveal.js Demo
    </section>
    <section>
      Theme Changes to Solarized. Isn't it Nice ;)
    </section>
    <section>
        <section>
            LalaLand Floor 1
        </section>
        <section>
            LalaLand Floor 2
        </section>
    </section>
  </div>
</div>
```

保存文件并使用命令`grunt serve`重启服务器。你应该会看到我们新制作的幻灯片。请注意，可以使用箭头键来控制幻灯片。尽管默认情况下这个功能是启用的，但是您可以在`Reveal.initialize()`方法中使用`keyboard: true`来配置行为(参见原始的 HTML 文件)。

## 主题

位于`css/theme`的`beige`、`solarized`、`sky`等主题有很多。为了使用它们，您只需更改页面上的默认样式，如下例所示。

```
<link rel="stylesheet" href="css/theme/default.css" id="theme">
```

## 过渡

可以使用`transition`和`transitionSpeed`参数在`Reveal.initialize()`中配置过渡风格和速度。这方面的一个例子如下所示。

```
transitionSpeed: 'default', // default / fast / slow
backgroundTransition: 'default', // default / none / slide / concave / convex / zoom
```

## 降价幻灯片内容

如果你是 Markdown 的粉丝，那么用 Markdown 写你的幻灯片会让你兴奋。简单地给你的`section`标签添加一个`data-markdown`属性，然后将你的内容包装在一个