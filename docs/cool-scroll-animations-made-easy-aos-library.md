# AOS 图书馆让滚动动画变得更酷

> 原文：<https://www.sitepoint.com/cool-scroll-animations-made-easy-aos-library/>

![Cool On Scroll Animations with AOS Library](img/945602b03be98bc2a1e7b4a93dfd3ae1.png)

作为前端开发人员，客户可能会向您提出一个普遍的要求，即在页面滚动时实现令人惊叹的动画效果。有许多库可以让我们更容易完成这项任务。 [AOS](https://michalsnik.github.io/aos/) ，也被称为**滚动动画**，就是这样一个库，它确实如它的名字所暗示的那样:当元素滚动到视图中时，它允许你将不同种类的动画应用到元素上。

在这里，你将了解 AOS 的内部运作，如何安装库并让它工作。到本教程结束时，为你的客户创建滚动动画将变得轻而易举。

## 如何安装 AOS 图书馆

你可以使用[鲍尔](https://bower.io/)或者 [npm](https://www.npmjs.com/) 来安装 AOS。

鲍尔:

```
bower install aos --save
```

npm:

```
npm install aos --save
```

接下来，链接 AOS 风格和脚本:

```
<link rel="stylesheet" href="bower_components/aos/dist/aos.css">

<script src="bower_components/aos/dist/aos.js"></script>
```

如果您愿意，可以使用 CDN 下载 AOS 样式表和 JavaScript 文件，如下所示:

CSS:

```
<link href="https://cdn.rawgit.com/michalsnik/aos/2.1.1/dist/aos.css" rel="stylesheet">
```

JavaScript:

```
<script src="https://cdn.rawgit.com/michalsnik/aos/2.1.1/dist/aos.js"></script>
```

就是这样，没有其他依赖，这有助于保持您的网站的性能在控制之下。

要初始化 AOS，只需在 JavaScript 文件中写下下面一行。

```
AOS.init();
```

## AOS 入门

初始化库之后，你要做的就是添加一些特定的属性。

要使用基本的动画，你只需要将`data-aos="animation_name"`添加到你的 HTML 元素中。

有几种类型的动画可供选择。例如，您可以添加像“淡入淡出”、“向上淡出”和“向左向下淡出”这样的淡入淡出动画。类似地，你也可以添加翻转和滑动动画，比如“上翻”、“左翻”、“下滑”和“右滑”。

下面是我们第一个例子的标记:

```
<div class="box a" data-aos="fade-up">
  <h2>Animated using fade-up.</h2>
</div>
<div class="box b" data-aos="flip-down">
  <h2>Animated using flip-down.</h2>
</div>
<div class="box b" data-aos="zoom-in">
  <h2>Animated using zoom-in.</h2>
</div>
```

除了上一节中的初始化行，动画元素不需要您做任何其他事情。

看看上面运行的代码:

在 [CodePen](https://codepen.io) 上通过 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )看到笔[在卷轴上动画示例](https://codepen.io/SitePoint/pen/qmvxoL/)。