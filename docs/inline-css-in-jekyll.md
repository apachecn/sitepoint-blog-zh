# Jekyll 中的内联 CSS

> 原文：<https://www.sitepoint.com/inline-css-in-jekyll/>

我一直是哲基尔的粉丝。它有一些缺陷，并不总是这项工作的最佳工具，但是，在某些情况下，它可能是一个很好的工具。我已经记不清用它建了多少网站了。

最近，我用 Jekyll 制作了另一个网站，这次是为[简化的 JavaScript 行话](http://jargon.js.org)制作的，我发现自己面临一个不那么典型的问题——在`<head>`中的内联样式。

## 需求

你可能听说过临界 CSS。这个概念背后的想法是尽快向浏览器提供关键样式(负责页面顶部和主要内容区域外观的样式)，以便在访问内容之前没有延迟。

有一个常见的规则是，最好在 14kb 以下发送呈现页面顶部所需的内容，因为这大致是服务器在一次往返中可以处理的量。Google PageSpeed Insights 在他们的文档中提供了更多关于这方面的信息，所以如果你想知道为什么会这样，请随意查看。

在这种程度上，如果你的 CSS 足够小(就像对 [SJSJ](http://jargon.js.org) 来说一样)，你可以将它们全部内联到`<head>`中，然后在第一次往返中一起发送，甚至不需要外部样式表。这种情况并不常见，但当它出现时，就非常棒了。

## 回到哲基尔

所以我的想法是在文档头的一个`<style>`标签中包含样式，并完全跳过外部样式表。我的第一个赌注是构建站点，然后将生成的 CSS 文件移动到`_includes`文件夹中，这样它就可以包含在`{% include %}`块中。它工作了，但是作为一个过程有点乏味。

然后我意识到，如果我能把我所有的风格都放在页面的顶部，那是因为我没有太多的风格，所以我完全可以反过来解决这个问题。

我可以直接在文件夹中创建样式，而不是在构建后将样式移动到`_includes`文件夹中。然后，我可以将一个 CSS 文件从那里导入到文档的头部。

```
/* _includes/styles.css */

.foo-bar {
  color: pink;
} 
```

然后:

```
<!-- _includes/head.html -->

<style> {% include styles.css %} </style> 
```

Tada！它给了我们想要的东西:

```
<!-- … -->
<style> .foo-bar {
  color: pink;
} </style>
<!-- … --> 
```

## 萨斯呢？

好吧，你可能会想“是的，但这意味着我们不能再用 Sass 了。”是也不是。基本上，我们已经把 Jekyll 的整个 Sass 管道完全去掉了，但还是有办法的。

如果你曾经读过 Jekyll 的[文档，你可能会注意到有一个`scssify`和一个`sassify`过滤器。文档称这使我们能够:](https://jekyllrb.com/docs/templates/)

> 将萨斯或 SCSS 格式的字符串转换成 CSS。

很好。这意味着我们仍然可以使用 Sass 通过管道把我们的整个文件放入这个东西。唯一的问题是我们不能像`{% include %}`一样在一个块上应用过滤器。诀窍是在一个变量中捕获文件的内容(感谢`{% capture %}`)，然后在输出它时将我们的过滤器应用到这个变量。

```
<!-- _includes/head.html -->
{% capture styles %}
{% include styles.css %}
{% endcapture %}

<style> {{ styles | scssify }} </style> 
```

Tada(又一次)！

## 缩小呢？

这个`scssify`过滤器的好处是它尊重您从`_config.yml`开始的 Sass 配置。因此，如果在配置文件中将输出样式设置为`compressed`，过滤器会将 Sass 编译成压缩的 CSS。

```
# _config.yml

sass:
  style: compressed 
```

Tada(再来一次)！

```
<!-- … -->
<style> .foo-bar{color:pink} </style>
<!-- … --> 
```

## 最后的想法

如您所见，这篇文章没有什么突破性的内容。然而，我必须说，在我前几天花时间考虑这个问题之前，我从来没有真正想到过我可以直接在`_includes`文件夹中写我的风格。

当然，当处理比 14kb 大得多的样式表时，这种想法是不够的，在这种情况下，您需要使用某种工具来[提取关键的 CSS。但是对于小页面和网站来说，它非常方便！](https://css-tricks.com/authoring-critical-fold-css/)

如果您想看看它在实际项目中是如何工作的，您可以查看 [SJSJ 存储库](https://github.com/HugoGiraudel/SJSJ/blob/gh-pages)上的文件:

*   [T2`_includes/styles.css`](https://github.com/HugoGiraudel/SJSJ/blob/gh-pages/_includes/styles.css)
*   [T2`_includes/head.html`](https://github.com/HugoGiraudel/SJSJ/blob/gh-pages/_includes/head.html#L9-L14)

希望有帮助，编码愉快！

## 分享这篇文章