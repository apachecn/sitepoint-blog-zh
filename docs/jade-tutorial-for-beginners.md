# 新手翡翠教程

> 原文：<https://www.sitepoint.com/jade-tutorial-for-beginners/>

Jade 是一个优雅的模板引擎，主要用于 NodeJS 中的服务器端模板。简而言之，Jade 给了你一种强大的新方法来编写标记，与普通的 HTML 相比有很多优势。

例如，看看这个 HTML 格式的电影卡片:

```
<div>
  <h1>Ocean's Eleven</h1>
  <ul>
    <li>Comedy</li>
    <li>Thriller</li>
  </ul>
  <p>Danny Ocean and his eleven accomplices plan to rob
     three Las Vegas casinos simultaneously.</p>
</div>
```

这是相同的标记在 Jade 中的样子:

```
div
  h1 Ocean's Eleven
  ul
    li Comedy
    li Thriller
  p.
    Danny Ocean and his eleven accomplices plan to rob
    three Las Vegas casinos simultaneously.
```

玉版优雅简洁。但这不仅仅是漂亮的语法。Jade 有一些非常好的特性，允许你编写模块化和可重用的标记。在我们深入了解这些强大的功能之前，让我们先快速概述一下基础知识。

## 基础知识

我将强调翡翠的三个基本特征

*   简单标签
*   向标签添加属性
*   文本块

如果你想在我们进行的过程中尝试一下，你可以使用 [CodePen](http://codepen.io/) 并选择 Jade 作为你的 HTML 预处理器，或者使用官方 Jade 页面上的[在线编译器](http://jade-lang.com/demo/)将你的 Jade 编译成 HTML。

## 简单标签

您可能已经注意到，在 Jade 中没有“结束”标签。相反，Jade 使用缩进(即空白)来确定标签的嵌套方式。

```
div
  p Hello!
  p World!
```

在上面的例子中，由于段落标签是缩进的，它们将在`div`标签中结束。简单！

```
<div>
  <p>Hello!</p>
  <p>World!</p>
</div>
```

Jade 通过将每一行的第一个单词视为一个标记，而将该行的后续单词视为标记内的文本，从而准确地编译了这一点。

[在 CodePen 上查看此示例](http://codepen.io/SitePoint/pen/PZLgaN/)

## 属性

所有这些都很棒，但是我们如何给标签添加属性呢？非常简单。让我们回到第一个例子，加入一些类和一个海报图片。

```
div(class="movie-card", id="oceans-11")
  h1(class="movie-title") Ocean's 11
  img(src="/img/oceans-11.png", class="movie-poster")
  ul(class="genre-list")
    li Comedy
    li Thriller
```

很漂亮吧？

```
<div class="movie-card" id="oceans-11">
  <h1 class="movie-title">Ocean's 11</h1>
  <img src="/img/oceans-11.png" class="movie-poster">
  <ul class="genre-list">
    <li>Comedy</li>
    <li>Thriller</li>
  </ul>
</div>
```

[在 CodePen 上查看此示例](http://codepen.io/SitePoint/pen/pgYBZd)

但这并没有停止。Jade 为 id 和类提供了特殊的简写，使用熟悉的符号进一步简化了我们的标记:

```
div.movie-card#oceans-11
  h1.movie-title Ocean's 11
  img.movie-poster(src="/img/oceans-11.png")
  ul.genre-list
    li Comedy
    li Thriller
```

[在 CodePen 上查看此示例](http://codepen.io/SitePoint/pen/VeRNEZ)

正如你所看到的，Jade 使用了我们在编写 CSS 选择器时已经熟悉的语法，这使得识别类更加容易。

## 文本块

假设您有一个段落标记，您想在其中放置一大块文本。Jade 将每一行的第一个单词视为一个 HTML 标签——那么您会怎么做呢？

您可能已经注意到，在本文的第一个代码示例中有一个无辜的句点。在您的标签后添加一个句号(句号)表示该标签内的所有内容都是文本，Jade 不再将每行的第一个单词视为 HTML 标签。

```
div
  p How are you?
  p.
    I'm fine thank you.
    And you? I heard you fell into a lake?
    That's rather unfortunate. I hate it when my shoes get wet.
```

[在 CodePen 上查看此示例](http://codepen.io/SitePoint/pen/XXGQxP)

为了说明这一点，如果我在本例中删除了`p`标记后的句点，编译后的 HTML 会将单词“I'm”中的“I”视为开始标记(在本例中，它将是`<i>`标记)。

## 强大的功能

现在我们已经介绍了基础知识，让我们来看一看一些强大的特性，它们将使您的标记更加智能。在本教程的剩余部分，我们将了解以下特性:

*   环
*   Java Script 语言
*   插入文字
*   混合蛋白

## 在 Jade 中使用 JavaScript

Jade 是用 JavaScript 实现的，所以在 Jade 中使用 JavaScript 超级简单。这里有一个例子。

```
- var x = 5;
div
  ul
    - for (var i=1; i<=x; i++) {
      li Hello
    - }
```

我们刚刚做了什么？！通过用连字符开始一行，我们向 Jade 编译器表明我们想开始使用 JavaScript，它就像我们预期的那样工作。当您将上面的 Jade 代码编译成 HTML 时，您会得到以下结果:

```
<div>
  <ul>
    <li>Hello</li>
    <li>Hello</li>
    <li>Hello</li>
    <li>Hello</li>
    <li>Hello</li>
  </ul>
</div>
```

[在 CodePen 上查看此示例](http://codepen.io/SitePoint/pen/eJXoQK)

当代码不直接添加输出时，我们使用连字符。如果我们想用 JavaScript 在 Jade 中输出一些东西，我们就用`=`。让我们修改上面的代码来显示序列号。

```
- var x = 5;
div
  ul
    - for (var i=1; i<=x; i++) {
      li= i + ". Hello"
    - }
```

瞧，我们现在有了序列号:

```
<div>
  <ul>
    <li>1\. Hello</li>
    <li>2\. Hello</li>
    <li>3\. Hello</li>
    <li>4\. Hello</li>
    <li>5\. Hello</li>
  </ul>
</div>
```

[在 CodePen 上查看此示例](http://codepen.io/SitePoint/pen/YwgMdz)

当然，在这种情况下，一个有序列表会更合适，但是你明白了。现在，如果你担心 XSS 和 HTML 逃脱，[阅读文档以获得更多信息](http://jade-lang.com/reference/code/)。

## 环

Jade 提供了一个优秀的循环语法，所以你不需要求助于 JavaScript。让我们遍历一个数组:

```
- var droids = ["R2D2", "C3PO", "BB8"];
div
  h1 Famous Droids from Star Wars
  for name in droids
    div.card
      h2= name
```

这将编译如下:

```
<div>
  <h1>Famous Droids from Star Wars</h1>
  <div class="card">
    <h2>R2D2</h2>
  </div>
  <div class="card">
    <h2>C3PO</h2>
  </div>
  <div class="card">
    <h2>BB8</h2>
  </div>
</div>
```

[在 CodePen 上查看此示例](http://codepen.io/SitePoint/pen/ZQPNxZ)

您可以迭代对象，也可以使用`while`循环。[查看文档了解更多](http://jade-lang.com/reference/iteration/)。

## 插入文字

像这样将 JavaScript 混合到文本中会很烦人。Jade 对此有优雅的解决方案吗？你打赌。

```
- var profileName = "Danny Ocean";
div
  p Hi there, #{profileName}. How are you doing?
```

[在 CodePen 上查看此示例](http://codepen.io/SitePoint/pen/LGaorZ)

这不是很棒吗？

## 混合蛋白

混合就像函数。它们将参数作为输入，将标记作为输出。混合是使用关键字`mixin`定义的。

```
mixin thumbnail(imageName, caption)
  div.thumbnail
    img(src="/img/#{imageName}.jpg")
    h4.image-caption= caption
```

一旦定义了 mixin，就可以用`+`语法调用 mixin。

```
+thumbnail("oceans-eleven", "Danny Ocean makes an elevator pitch.")
+thumbnail("pirates", "Introducing Captain Jack Sparrow!")
```

它将输出这样的 HTML:

```
<div class="thumbnail">
  <img src="/img/oceans-eleven.jpg">
  <h4 class="image-caption">
    Danny Ocean makes an elevator pitch.
  </h4>
</div>
<div class="thumbnail">
  <img src="/img/pirates.jpg">
  <h4 class="image-caption">
    Introducing Captain Jack Sparrow!
  </h4>
</div>
```

## 把所有的放在一起

让我们把到目前为止所学的所有东西放在一起。假设我们有一个很好的电影数组，每个项目包含电影的标题、演员(一个子数组)、评级、类型、到 IMDB 页面的链接和电影海报的图像路径。该数组如下所示(为便于阅读，添加了空格):

```
- var movieList = [
  {
    title: "Ocean's Eleven",
    cast: ["Julia Roberts", "George Clooney", "Brad Pitt", "Andy Garcia"],
    genres: ["Comedy", "Thriller"],
    posterImage: "/img/oceans-eleven",
    imdbURL: "http://www.imdb.com/title/tt0240772/",
    rating: 7
  }
  // etc...
];
```

我们有 10 部电影，我们想为每部电影制作精美的电影卡片。最初，我们不打算使用 IMDB 链接。如果一部电影被评为 5 分以上，我们会给它一个大拇指，否则，我们会给它一个大拇指。我们将使用 Jade 的所有优秀特性编写一些模块化代码来完成以下任务:

1.  为电影卡创建混音
    *   遍历演员列表并显示演员。我们将对流派做同样的事情。
    *   检查评分，并决定是显示拇指向上还是拇指向下。
2.  遍历电影列表，使用 mixin 为每部电影创建一张卡片。

所以让我们先创建 mixin。

```
mixin movie-card(movie)
  div.movie-card
    h2.movie-title= movie.title
    img.movie-poster(src=movie.posterImage)
    h3 Cast
    ul.cast
      each actor in movie.cast
        li= actor
    div.rating
      if movie.rating > 5
        img(src="img/thumbs-up")
      else
        img(src="img/thumbs-down")
    ul.genre
      each genre in movie.genres
        li= genre
```

上面有很多东西，但我确信它看起来很熟悉——我们在本教程中已经涵盖了所有这些。现在，我们只需要在一个循环中使用 mixin:

```
for movie in movieList
  +movie-card(movie)
```

就是这样。那很优雅吗？这是最终代码。

```
- var movieList = [
  {
    title: "Ocean's Eleven",
    cast: ["Julia Roberts", "George Clooney", "Brad Pitt", "Andy Garcia"],
    genres: ["Comedy", "Thriller"],
    posterImage: "/img/oceans-eleven",
    imdbURL: "http://www.imdb.com/title/tt0240772/",
    rating: 9.2
  },
  {
    title: "Pirates of the Caribbean",
    cast: ["Johnny Depp", "Keira Knightley", "Orlando Bloom"],
    genres: ["Adventure", "Comedy"],
    posterImage: "/img/pirates-caribbean",
    imdbURL: "http://www.imdb.com/title/tt0325980/",
    rating: 9.7
  }
];

mixin movie-card(movie)
  div.movie-card
    h2.movie-title= movie.title
    img.movie-poster(src=movie.posterImage)
    h3 Cast
    ul.cast
    each actor in movie.cast
      li= actor
    div.rating
      if movie.rating > 5
        img(src="img/thumbs-up")
      else
        img(src="img/thumbs-down")
    ul.genre
      each genre in movie.genres
        li= genre

for movie in movieList
  +movie-card(movie)
```

这是编译后的 HTML:

```
<div class="movie-card">
  <h2 class="movie-title">Ocean's Eleven</h2>
    <img src="/img/oceans-eleven" class="movie-poster"/>
  <h3>Cast</h3>
  <ul class="cast">
    <li>Julia Roberts</li>
    <li>George Clooney</li>
    <li>Brad Pitt</li>
    <li>Andy Garcia</li>
  </ul>
  <div class="rating">
    <img src="img/thumbs-up"/>
  </div>
  <ul class="genre">
    <li>Comedy</li>
    <li>Thriller</li>
  </ul>
</div>
<div class="movie-card">
  <h2 class="movie-title">Pirates of the Carribean</h2>
  <img src="/img/pirates-caribbean" class="movie-poster"/>
  <h3>Cast</h3>
  <ul class="cast">
    <li>Johnny Depp</li>
    <li>Keira Knightley</li>
    <li>Orlando Bloom</li>
  </ul>
  <div class="rating">
    <img src="img/thumbs-up"/>
  </div>
  <ul class="genre">
    <li>Adventure</li>
    <li>Comedy</li>
  </ul>
</div>
```

但是等一下。如果我们现在想在点击一部电影的标题时进入电影的 IMDB 页面，该怎么办？我们可以在 mixin 中添加一行:`a(href=movie.imdbURL)`。

```
mixin movie-card(movie)
  div.movie-card
    a(href=movie.imdbURL)
      h2.movie-title= movie.title
    img.movie-poster(src=movie.posterImage)
    h3 Cast
    ul.cast
    each actor in movie.cast
      li= actor
    div.rating
      if movie.rating > 5
        img(src="img/thumbs-up")
      else
        img(src="img/thumbs-down")
    ul.genre
      each genre in movie.genres
        li= genre
```

[在 CodePen 上查看此示例](http://codepen.io/SitePoint/pen/Bjbgpv)

## 结论

我们从对 Jade 一无所知到制作一些漂亮的模块化电影卡。关于 Jade 还有很多，但是为了简单起见，我忽略了一些概念。所以我希望这篇教程能激起你的好奇心[去了解更多](http://jade-lang.com/reference/)。

**重要提示:正如你们中的一些人可能已经知道的那样， [Jade 已经因为一个软件商标声明而更名为 Pug](https://github.com/pugjs/jade/issues/2184) 。今后，有关玉石的文章将使用新名称“Pug”或“PugJS”。**

## 分享这篇文章