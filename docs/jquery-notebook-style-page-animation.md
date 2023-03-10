# 令人惊叹的 jQuery 笔记本翻页动画

> 原文：<https://www.sitepoint.com/jquery-notebook-style-page-animation/>

日常的 jQuery 因为它的有用性、灵活性和速度变得越来越流行。像“flash 页面布局”翻页动画这样的笔记本动画风格就是这些能力的一个极好的例子。这个花哨的 jQuery 笔记本皮肤使用了一个名为 jQuery Booklet Plugin 的 jQuery 动画插件。

## jQuery 笔记本动画的优点

*   轻量级(只有 17kb 缩小版)
*   易于安装，带有页面滑动凹槽
*   非常流畅的翻页动画和栩栩如生的页面效果

[![jquery-notebook-flash-animation-page-layout](img/07264992179bc77abe6d8a287c0c758b.png "jquery-notebook-flash-animation-page-layout")](http://tympanus.net/Tutorials/MoleskineNotebook/)

[观看演示](http://tympanus.net/Tutorials/MoleskineNotebook/)

## 如何安装

1.  下载 [jQuery MoleSkine 笔记本套装](http://tympanus.net/Tutorials/MoleskineNotebook/MoleskineNotebook.zip)
2.  添加下面的 HTML(为每个页面添加一个带有类“b-load”的 div)
3.  编辑 CSS 样式以满足您的需求
4.  编辑小册子 JS 选项来调整页面动画

**jQuery Moleskine 包包含:**

*   [jQuery 小册子插件](http://builtbywill.com/code/booklet/)
*   所有需要的图片: [photoshop 文件背景](http://dennern.deviantart.com/art/My-Moleskine-79177093)、[导航图片](http://www.premiumpixels.com/simple-little-arrows-psd/)等
*   jQuery 1.4.4 分钟 js
*   jQuery 宽松版 1.3.js
*   页面的自定义字体
*   HTML 模板示例

## HTML 代码

```
<div class="book_wrapper">
	<a id="next_page_button"></a>
	<a id="prev_page_button"></a>

	<div id="loading" class="loading">Loading pages...</div>

	<div id="mybook" style="display:none;">
		<div class="b-load">
			<div>
				<img src="images/1.jpg" alt=""/>
				<h1>Slider Gallery</h1>
				<p>This tutorial is about creating a creative gallery...
				<a href="#" class="article">Article</a>
				<a href="#" class="demo">Demo</a>
			</div>
			<div>
				...
			</div>
		</div>
	</div>
</div>
```

来源:http://tympanus.net/codrops/2010/12/14/moleskine-notebook/

## 分享这篇文章