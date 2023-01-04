# 如何用 PostCSS 构建自己的 CSS 预处理器

> 原文：<https://www.sitepoint.com/build-css-preprocessor-postcss/>

使用 CSS 预处理器一段时间后，没有它的生活将变得不可想象。许多开发人员采用了 LESS、Stylus 或目前最受欢迎的 Sass。理想情况下，你还应该使用 [Autoprefixer](https://github.com/postcss/autoprefixer) 对你的 CSS 进行后处理，在需要的时候添加厂商前缀，而不需要求助于 mixins 或者像 [Compass](http://compass-style.org/) 这样的框架。

Autoprefixer 是 [PostCSS](https://github.com/postcss/postcss) 的插件；一个将 CSS 转换成可用于操作样式的对象模型的工具。还有[更多可用的插件](https://github.com/postcss)，它们实现了类似预处理器的特性，比如变量、混合和嵌套。

Ben Frain 最近的文章 [*与萨斯分手:不是你，是我*](http://benfrain.com/breaking-up-with-sass-postcss/) 提出了一个有趣的命题。你真的需要 Sass、LESS 或 Stylus 吗？如果你可以使用 PostCSS 来制作你想要的 CSS 预处理器？

可以在构建过程中创建一个基本的类似 Sass 的解析器。对于这个例子，我将使用一个 [Gulp.js](https://www.sitepoint.com/introduction-gulp-js/) 任务文件，但是同样的概念也适用于 Grunt 或任何其他构建系统。首先，我们使用 npm 安装 PostCSS 和我们需要的插件，例如

```
npm install gulp-postcss postcss-mixins postcss-simple-vars postcss-nested autoprefixer-core --save-dev
```

然后在`gulpfile.js`中创建 CSS 处理函数的数组，例如

```
var
	gulp = require('gulp'),
	postcss = require('gulp-postcss'),
	processors = [
		require('postcss-mixins'),
		require('postcss-simple-vars'),
		require('postcss-nested'),
		require('autoprefixer-core')({ browsers: ['last 2 versions', '> 2%'] })
	];
```

并编写 CSS 处理任务，例如

```
// compile CSS
gulp.task('css', function() {
  return gulp.src('source/css/styles.css')
    .pipe(postcss(processors))
    .pipe(gulp.dest('dest/styles/'));
});
```

您还可以使用 [PostCSS API](https://github.com/postcss/postcss/blob/master/API.md) 创建自己的处理函数。例如，我们可以对所有的`font-family`声明应用无衬线回退:

```
processors = [
		require('postcss-mixins'),
		require('postcss-simple-vars'),
		require('postcss-nested'),
		function(css) {
			// sans-serif fallback
			css.eachDecl('font-family', function(decl) {
				decl.value = decl.value + ', sans-serif';
			});
		},
		require('autoprefixer-core')({ browsers: ['last 2 versions', '> 2%'] })
	];
```

如果文件`/source/css/styles.css`包含以下代码:

```
$colorfore: #333;
$colorback: #fff;

@define-mixin reset {
	padding: 0;
	margin: 0;
}

main {
	font-family: Arial;
	@mixin reset;
	color: $colorfore;
	background-color: $colorback;

	article {
		color: $colorback;
		background-color: $colorfore;
	}
}
```

运行`gulp css`会在`/dest/css/styles.css`中创建这个 CSS:

```
main {
	font-family: Arial, sans-serif;
	padding: 0;
	margin: 0;
	color: #333;
	background-color: #fff;
}

main article {
	color: #fff;
	background-color: #333;
}
```

## 优势

PostCSS 将您从预处理程序作者强加的限制和选择中解放出来。这种方法有几个好处:

*   模块化
    你只需要为你的项目添加你需要的插件和功能。
*   轻量级
    前置处理器是越来越大型和复杂的应用。你可能不想要或使用每一个功能，但它们仍然存在。PostCSS 减少了体积。
*   **立即实现**
    你有没有坐等 Sass、LibSass、LESS、Stylus 或其他预处理器中的某样东西变得可用？现在，您可以使用…开发自己的功能
*   **JavaScript functions**
    Your CSS preprocessor uses JavaScript — a true programming language *(despite what some people say!)*

    大多数预处理器语言结构都是基本的。你会经常看到比它们创建的原始 CSS 更加复杂和难以理解的函数和混合。有了 PostCSS，在 Sass/LESS/Stylus 中永远不会实现的功能就可以实现了。您可以轻松打开文件、读取数据库、发出 HTTP 请求或创建复杂的计算。

*   **实施开发策略**
    假设您希望您的团队[避免@extend 声明](https://www.sitepoint.com/avoid-sass-extend/)。任何人都不可能再使用`@extend`，除非他们在构建过程中添加一个[扩展插件](https://github.com/davidtheclark/postcss-simple-extend)。这将是显而易见的。
*   很快——非常快
    作者估计 PostCSS 比同等的预处理器快 4 到 40 倍。我怀疑如果你只需要几个插件，收益会更高。整个解决方案是用 JavaScript 构建的，不需要跳到另一个库或语言解释器。

## 缺点是

一切都好吗？不幸的是，PostCSS 并不是一个完美的解决方案:

*   **Increased complexity**
    Your build process will become more difficult to manage.

    添加 Sass 通常只需要一两行代码，但是 PostCSS 需要更多的计划——特别是因为插件必须以特定的顺序调用。例如，`@import`在解析变量之前应该先解析内联。但是如果在你的`@import`声明中有变量呢？在某些情况下，可能需要多次调用一个插件。

*   **不同的语法**
    我最初试图将一个小的 Sass 项目转换成 PostCSS。*连试都不要试！虽然我最终成功了，但 PostCSS 插件经常使用稍微不同的语法，例如`@define-mixin`而不是`@mixin`。这可能会导致混乱和大量的代码更新。部分原因…*
*   **PostCSS 需要有效的 CSS**
    大多数预处理程序解析纯文本文件，所以理论上任何语法都是可行的。PostCSS 创建一个对象模型，所以它从一开始就需要语法正确的 CSS。即使是一个`// single line comment`也可能导致它失败。你可以在传递给 PostCSS 之前预处理你的 CSS 文件，但是之后你又回到了使用预处理程序！

## 你应该放弃你的预处理器吗？

如果您在一个人的团队中从事一个小型的、相对简单的自包含项目，定制的 PostCSS 处理器可能是一个有吸引力的选择。

我还推荐 PostCSS 用于任何真正的后处理任务，例如厂商前缀、将媒体查询打包到一个声明中、简化`calc()`方程、为旧浏览器应用后备、支持 CSS4 选择器、缩小等等。自己做那项工作没什么好处。

然而，萨斯已经达到了临界质量。没有一个预处理语法是完美的，但是你的团队中的大多数开发人员都会理解它。任何细微的不同都不太可能给每个人带来显著的好处或吸引力。

也就是说，PostCSS 和类似的 [Rework](https://github.com/reworkcss/rework) 框架有着巨大的潜力。如果一个模块化的 CSS 插件系统可以复制——*甚至混合*——我们想要的来自 Sass、LESS 和 Stylus 的语法和功能，我们将有一个单一的预处理器来击败所有其他的。你可以打赌某人，在某个地方正在做那个项目…

你成功地使用 PostCSS 作为你的项目的预处理器了吗？它是否让你远离了无礼？你会从更少开始吗？你会放弃手写笔吗？

## 分享这篇文章