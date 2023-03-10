# 用 DOM 脚本制作下拉菜单

> 原文：<https://www.sitepoint.com/making-a-dropdown-with-dom-scripting/>

亚伦·古斯塔夫森正在整理一系列关于如何让精选元素具有风格的文章。本质上，他的工作是从 DOM 中取出选择，并用一个 ul 替换它，然后添加脚本和 CSS，使那个 ul 像下拉列表一样工作。这种做法的优势对于那些曾经抱怨选择不够时尚的人来说是显而易见的。这个系列才刚刚开始，还存在一些问题——新元素不能使用 tab 键，也不能响应键盘事件——但是 Aaron 承诺第 2 部分将讨论可访问性问题。

该脚本使用了与 [Suckerfish dropdowns](http://htmldog.com/articles/suckerfish/) 类似的技术，将“hover”和“selected”类应用到 ul 的各个部分，以提供用于样式化的钩子(因为 IE 不支持:悬停在非链接的东西上)。这应该是一个值得一看的有用技术！

## 分享这篇文章