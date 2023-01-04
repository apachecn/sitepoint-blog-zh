# 5 项提高网站性能的繁重任务

> 原文：<https://www.sitepoint.com/5-grunt-tasks-improve-performance-website/>

在 SitePoint 上，我多次提到如何实现良好的性能是当今的一个主要关注点，以及你应该如何努力实现快速网页。在我的一些关于 JavaScript APIs 的文章中，比如[介绍资源计时 API](https://www.sitepoint.com/introduction-resource-timing-api/) 和[发现用户计时 API](https://www.sitepoint.com/discovering-user-timing-api/) ，我给了你所有你需要的力量，让你知道是什么拖慢了你的项目。今年早些时候，Craig Buckler 也谈到了这个话题，他的文章[是关于减轻页面重量的完整指南](https://www.sitepoint.com/complete-guide-reducing-page-weight/)。

如果你没有停留在过去，你可能会使用像[咕噜](http://gruntjs.com/)或[大口](http://gulpjs.com/)这样的任务运行器，通过自动化我们过去手动执行的许多操作来改善你的工作流程。在这篇文章中，我将描述五个帮助我们提高网页性能的简单任务。

## 咕噜-贡献-图像最小

我要提到的第一个任务是 [grunt-contrib-imagemin](https://github.com/gruntjs/grunt-contrib-imagemin) 。我想先讨论它的原因是图像正在扼杀网络。说真的！

如果你看一看[HTTParchive.org](http://httparchive.org/interesting.php#bytesperpage)的统计数据，你会发现图片占了页面总大小的 63%以上。这种膨胀的原因是图像通常没有被压缩到尽可能低的重量。grunt-contrib-imagemin 是可以用来解决这个问题的任务之一。

这项任务带有以下优化器，能够压缩网络上使用的大多数图像格式:

*   压缩 GIF 图像
*   jpegtran 来压缩 JPEG 图像
*   选择压缩 png 图像
*   svgo 压缩 SVG 图像

此任务的配置示例如下所示:

```
imagemin: {
   dist: {
      options: {
        optimizationLevel: 5
      },
      files: [{
         expand: true,
         cwd: 'src/images',
         src: ['**/*.{png,jpg,gif}'],
         dest: 'dist/'
      }]
   }
}
```

这种配置允许通过使用`optimizationLevel`选项进行高级别的优化。该值的范围从 0 到 7，默认值为 3。在这种情况下，优化的图像是扩展名为 png、jpg 或 gif 的图像，位于“src/images”文件夹及其所有子文件夹中。优化的结果将存储在“dist”文件夹中。

## 咕哝-贡献-丑陋

任务 [grunt-contrib-uglify](https://github.com/gruntjs/grunt-contrib-uglify) 用于缩小 JavaScript 文件。该任务删除源代码中所有不必要的空格，并一致地重命名变量和函数，以使用尽可能短的名称。

这个任务中经常用到的一些选项是`sourceMap`和`banner`。前者在与目标文件相同的目录中创建源映射文件。要启用该选项，您必须将其设置为`true`(默认值为`false`)。`banner`是一个字符串，添加到缩小的输出中，通常在其中写入文件/库/框架的名称、版本、作者姓名和许可证。它的默认值是一个空字符串。

为了让您了解缩小后的源代码是什么样子，我们假设您有以下 JavaScript 代码:

```
var MyApplication = function() {
   var data = 'hello';

   this.sum = function(first, second) {
      return first + second;
   }

   this.showData = function() {
      return data;
   }
};
```

缩小过程会将其转换为以下代码:

```
var MyApplication=function(){var a="hello";this.sum=function(a,b){return a+b},this.showData=function(){return a}};
```

该工具的配置示例如下所示:

```
uglify: {
   dist: {
      options: {
         sourceMap: true,
         banner: '/*! MyLib.js 1.0.0 | Aurelio De Rosa (@AurelioDeRosa) | MIT Licensed */'
      },
      files: {
         'dest/output.min.js': ['src/input.js'],
      }
   }
}
```

## 格朗特-伊比-综明

顾名思义， [grunt-contrib-cssmin](https://github.com/gruntjs/grunt-contrib-cssmin) 压缩 CSS 文件。像[咕哝贡献丑陋](https://github.com/gruntjs/grunt-contrib-uglify)任务一样，这个任务提供了一个`banner`选项。

此任务的一个简单配置是:

```
cssmin: {
   dist: {
      options: {
         banner: '/*! MyLib.js 1.0.0 | Aurelio De Rosa (@AurelioDeRosa) | MIT Licensed */'
      },
      files: {
         'dist/css/style.min.css': ['src/css/**/*.css']
      }
  }
}
```

本示例缩小存储在“src/css”及其子文件夹中的所有 CSS 文件，并将结果存储在一个名为“style.min.css”的样式表中，该样式表位于文件夹“dist/css”中。此外，该配置在缩小文件的顶部添加了一个横幅。

## 咕噜-uncss

另一个处理 CSS 的任务是 [grunt-uncss](https://github.com/addyosmani/grunt-uncss) 。此任务从项目中删除未使用的 CSS，因此它减小了最终 CSS 文件的大小，从而缩短了下载时间。如果您正在使用 Boostrap 或 Foundation 之类的框架进行开发，它尤其适合。这个任务有一些重要的限制，你可以在[的官方文档](https://github.com/addyosmani/grunt-uncss)上阅读。

值得一提的一些不错的选项是`ignore`，它允许我们指定不应删除的选择器列表，以及`ignoreSheets`，它允许我们指定要忽略的样式表。

此任务的使用示例如下所示:

```
uncss: {
   dist: {
      options: {
         ignore: [/js-.+/, '.special-class'],
         ignoreSheets: [/fonts.googleapis/],
      },
      files: {
         'dist/css/unused-removed.css': ['src/index.html', 'src/contact.html', 'src/service.html']
      }
   }
}
```

## 咕噜-贡献-htmlmin

本文中我想讨论的最后一个 Grunt 任务是 [grunt-contrib-htmlmin](https://github.com/gruntjs/grunt-contrib-htmlmin) ，这是一个缩减 HTML 代码的任务。它不会让你的网站速度加快很多，因为它通常只节省几千字节，如果你使用 gzip 压缩来提供你的内容，收益会更低。因此，如果你想缩小你的 HTML，嗯…赞美；这意味着你的网站已经非常优化了。

尽管如此，在处理 web 性能时要遵循的原则是每一个 Kb 都很重要。因此，让我们来看一个将该任务集成到我们的工作流中的简单配置:

```
htmlmin: {
   dist: {
      options: {
         removeComments: true,
         collapseWhitespace: true
      },
      files: [{
         expand: true,
         cwd: 'src',
         src: '**/*.html',
         dest: 'dist/'
      }]
   }
}
```

上面的代码处理放在“src”目录及其子文件夹中的所有页面。对于这些页面中的每一个，该任务删除所有的注释并折叠它找到的空间，将结果存储在“dist”目录中。

## 结论

在这篇文章中，我向您介绍了五个简单的任务来轻松提高您的网站的性能。它们如此简单，你真的没有借口去避免使用它们，并通过更快的服务为你的用户提供更好的体验。我希望您喜欢这篇文章，并且您将很快使用这些任务。

你用过吗？他们对你的网站改进了多少？有什么你喜欢的任务想和我们分享吗？

## 分享这篇文章