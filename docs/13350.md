# 在没有文档的位置插入。写入

> 原文：<https://www.sitepoint.com/insert-in-place-without-documentwrite/>

所以情况是这样的:你想使用 JavaScript 来整合一些内容以获取数据(比如 AdWords 或类似的程序)。联合脚本不能知道它所使用的页面的任何信息——因此它不能依赖于特定元素的存在。然而，宿主页面需要能够控制内容的插入位置——联合脚本需要在任何有`<script>`标记的地方插入内容。

你是怎么做到的？

你可能会像谷歌一样使用`document.write`。但是`document.write`不是一个好技术，它有一些值得注意的问题:

*   `document.write`在 XHTML 模式下不起作用(在作为 XML 的 XHTML 页面上)
*   用`document.write`编写的内容可能不会出现在页面的 DOM 中，这意味着没有进一步的访问权限来以编程方式操作它，也没有访问可访问性 API 的权限
*   `document.write`在概念上是错误的，因为它将节点视为序列化文本——它们不是——它们是节点

但是有什么选择呢？DOM 创建技术需要使用现有的元素引用；甚至`innerHTML`需要知道*在哪里*写 HTML。在这两种情况下，如果您希望新内容与现有内容一起出现，简单地追加到`<body>`并不是一个选项。

几天前，我面临着这个困境，直到我想到了一个明显的解决方案——事实上我们有一个可预测的引用:元素本身！

我们所需要的是一种识别 DOM 中的 including】的方法，然后我们可以使用`insertBefore`方法在它之前直接追加新的 HTML。

因此，给定一个具有固定 ID 的联合脚本:

```
<script type="text/javascript" id="syndication" src="syndication.js"></script>
```

我们可以像这样从旧的污秽中走出来:

```
document.write('<p id="syndicated-content">Here is some syndicated content.</p>');
```

像这样的现代可爱:

```
var newcontent = document.createElement('p');
newcontent.id = 'syndicated-content';
newcontent.appendChild(document.createTextNode('Here is some syndicated content.'));

var scr = document.getElementById('syndication');
scr.parentNode.insertBefore(newcontent, scr);
```

我们甚至可以更进一步，删除`<script>` ID，但是在这种情况下，我们需要一个具体的方法来标识特定的元素。我们可以通过知道它的`SRC`来做到这一点:

```
var scripts = document.getElementsByTagName('script');
for(var i=0; i<scripts.length; i++)
{
    if(scripts[i].src == 'http://www.mydomain.com/syndication.js')
    {
        //scripts[i] is the one

        break;
    }
}
```

现在你有了它——一种简单而优雅的插入内容的方式，消除了对`document.write`的最后一丝需求！

## 分享这篇文章