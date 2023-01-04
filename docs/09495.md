# Firebug 1.8 的新特性

> 原文：<https://www.sitepoint.com/firebug-18-whats-new/>

如果没有 Firebug，Firefox 不一定是最受欢迎的 web 开发浏览器。也就是说，如果没有 Firebug，我们就不会有像 webkit Inspector、Opera 蜻蜓或 IE 的开发者工具这样优秀的开发工具。

该工具充满了惊人的功能。我努力跟上发展，但是很容易错过一些精华，比如检查 CSS :active 和:hover 状态。Firebug 1.8 刚刚发布，它应该会在你的浏览器中自动更新。或者，前往 getfirebug.com[获得下载链接和安装说明。](http://getfirebug.com/)

## 仅限 Firefox 5.0

如果你还在用火狐 3.x 或者 4.0，恐怕你还在用 Firebug 1.7.3。Firebug 的开发反映了 Mozilla 的快速更新时间表，所以你需要最新的版本来使用新功能…

## DOM 面板选项

到目前为止，Firebug 已经展示了所有 DOM 对象属性，包括原型链中的属性。增加了两个附加选项:

*   **仅显示自己的属性**删除父对象的属性
*   **仅显示可枚举属性**

![Firebug DOM Panel options](img/6d9f3e32424bd90b9f448f6386b6c1f6.png)

## CSS 颜色工具提示

Firebug 现在显示 hex、rgb、rgba、hsl 和 hsla 颜色预览，就像它的几个竞争控制台一样:

![Firebug CSS color tooltips](img/19404c5a39ec6aa15b488f244a5cb832.png)

## CSS3 盒子尺寸

CSS3 为元素引入了一个框大小属性。它可以设置为:

*   **内容框**:默认的 CSS2.1 模型，尺寸由宽度、高度、填充和边框的总和决定。
*   **border-box** :强制元素渲染到指定的宽度和高度，包括填充和边框。

Firebug 1.8 显示了所使用的盒子大小模型:

![Firebug CSS3 box-sizing](img/68dcf7dcd450bb834297a312b7508a3b.png)

## 控制台.时间戳

Firebug 的脚本分析工具通过新的`console.timeStamp([msg])` API 命令得到了增强。当在您的代码中遇到`console.timeStamp();`时，一个时间轴标记被添加到网络面板:

![console.timeStamp](img/97152da194fac482a8114183c3474a2b.png)

当评估通过追加`<script>`节点或使用 XMLHttpRequest 方法动态加载的 JavaScript 文件的 HTTP 请求的性能时，该特性特别有用。

更多信息请参见[Firebug 1.8:console . timestamp()](http://www.softwareishard.com/blog/firebug/firebug-1-8-console-timestamp/)。

## 网络面板中的 IP 地址

继续使用 Net 面板，您现在可以查看所有 HTTP 请求的本地和远程 IP 地址。这应该有助于您发现由缓慢的服务器或 cdn 引起的问题。

![Firebug Net panel IP address](img/05d1a145a46cd820f5cd7bc181c792db.png)

## 网络面板中的 HTML 预览

如果您使用 Ajax 下载 HTML 代码片段，您会很高兴听到 Net Panel 还允许您预览呈现的代码:

![Firebug Net panel HTML preview](img/a0280d8868341a5b14119974a04ebbeb.png)

## 外部编辑器的附加选项

我错过的另一个特性是对外部编辑器的支持。除了传递%url 和%file 参数，1.8 版还引入了%line number 参数:

![Firebug external editors](img/66ab83c0993ed2d8949800744f2787e4.png)

## 命令行持久性

命令行指令现在在重新加载时仍然有效，因此可以在不同的页面上执行相同的表达式。

我希望你喜欢 Firebug 的新工具和设施。在[版本 1.9](http://getfirebug.com/wiki/index.php/Firebug_1.9_Feature_Wish_List) 中还会有更多的内容…

## 分享这篇文章