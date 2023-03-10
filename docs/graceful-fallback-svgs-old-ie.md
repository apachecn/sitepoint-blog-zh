# 旧 IE 中 SVG 的一个优雅的回退

> 原文：<https://www.sitepoint.com/graceful-fallback-svgs-old-ie/>

使用 SVG 代替栅格化图像有很多好的理由。好处包括更好的图像质量、图像灵活性和更小的文件大小。但是你仍然可能面临的一个障碍是浏览器支持。除了 IE8 和更低版本的浏览器，所有现代浏览器[都支持 SVG。](http://caniuse.com/#feat=svg)

在接下来的几节中，我将概述如何通过在老版本的浏览器上使用 png 来优雅地处理这个问题。这将解决内联 SVG 和背景图像。

## 制作您的备用图像

第一步是为每个 SVG 生成一个 PNG 版本。你可以用你喜欢的图像编辑软件来做这个，但是如果你有很多 SVG 的话，这个过程会很慢。或者，您可以使用 [Grunt](http://gruntjs.com/) 和一个名为 [svg2png](https://github.com/dbushell/grunt-svg2png) 的 Grunt 模块。这是我将在以下步骤中描述的解决方案。

如果你不熟悉 Grunt，我建议你通读一下这个[入门指南](http://gruntjs.com/getting-started)。否则，您可以先准备一个 Grunt 项目，然后运行以下命令:

```
npm install grunt-svg2png --save-dev`
```

## 配置您的咕哝文件

使用以下附加内容配置您的 Grunt 文件:

```
grunt.loadNpmTasks('grunt-svg2png');

grunt.initConfig({
    svg2png: {
        all: {
            files: [
                { cwd: 'img/', src: ['**/*.svg'], dest: 'img/png/' }
            ]
        }
    }
});
```

确保将`cwd`属性指向包含 SVG 的目录。一旦完成，运行`grunt svg2png`,你现在应该有一个装满了你的 SVG 文件的 PNG 版本的文件夹。

## 处理内联 SVG

如果您使用 SVG 作为内嵌图像，最可靠的方法是向图像标签添加一个内嵌`onerror`事件，如下所示:

```
<img src="/img/image.svg" onerror="this.src='/img/image.png';this.onerror=null;">
```

如果 SVG 加载失败，这将把 SVG 换成 PNG 图像。我还在结尾额外加了一点:`this.onerror=null;`。这是为了防止事件再次触发，如果 PNG 的路径有问题，就会再次触发事件。这将导致事件调用的无限循环，这显然是最好避免的。

您可能想知道为什么我在这里建议一个内联事件，而不是一个不太显眼的方法，在一个单独的 JavaScript 文件中附加一个事件。这是因为由于时间问题，jQuery 方法`$("img").on("error", handler);`在 IE8 和更低版本中无法工作，而另一种方法是遍历所有图像并给它们附加一个错误事件，这对于所有浏览器来说都是一种资源消耗，除非您只是针对旧的浏览器。

## 将 SVG 作为背景处理

如果你在 CSS 中使用 SVG 作为背景图片，那么有一个简单的方法来使用 png。对 SVG 的支持大致符合对多背景图像的支持。这意味着您可以使用纯 CSS 解决方案，因此您只需要以下声明:

```
background: url('/img/image.png') no-repeat;
background: none,
            url('/img/image.svg') no-repeat;
```

这里我们将一个 PNG 设置为背景，然后用一个 SVG 覆盖它，以便浏览器能够在第二行支持多种背景。

IE8 和更低版本会将第二个声明视为错误，因此它会忽略整行，只使用第一个背景。

## 结论

这就是全部了。现在，我希望今后你会使用 SVG，即使是在仍然需要支持 IE 旧版本的项目中。这意味着，如果需要的话，它们可以优雅地降级为 png，这是安全的。

如果在 IE8 及更低版本中，你有其他处理 SVG 的自动化解决方案，我很想听听。

## 分享这篇文章