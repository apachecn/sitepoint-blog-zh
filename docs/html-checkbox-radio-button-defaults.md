# 设置 HTML 复选框和 HTML 单选按钮默认值

> 原文：<https://www.sitepoint.com/html-checkbox-radio-button-defaults/>

如果你已经在网上写了几分钟以上的代码，你会使用一两个带有复选框和单选按钮的表单。考虑以下代码:

```
<form action="." method="post">
<label><input name="mycheckbox" type="checkbox" value="1" /> checkbox</label>
<label><input name="myradio" type="radio" value="1" /> radio 1</label>
<label><input name="myradio" type="radio" value="2" /> radio 2</label>
<button type="submit">Submit</button>
</form>

<!-- output -->
<p>Value of mycheckbox: <?php echo $_POST['mycheckbox']; ?></p>
<p>Value of myradio: <?php echo $_POST['myradio']; ?></p>
```

当您点击**提交**而没有勾选任何复选框时，mycheckbox 和 myradio 会显示什么值？

答案:*出现错误。*这将取决于您的 PHP 设置，但是，假设启用了严格的错误报告，您将看到两个字段都有**“注意:未定义的索引”**。

如果未选中复选框或未选择单选按钮，则不会为 HTTP POST 中的这些字段发送任何内容。与其他输入框不同，这里没有默认值——发布的字段根本不存在。为了让它正常工作，您需要检查它在服务器上是否存在，例如

```
<?php
$mycheckbox = isset($_POST['mycheckbox']);
$myradio = (isset($_POST['myradio']) ? (int) $_POST['myradio'] : 0);
?>
```

假设您知道表单中应该有哪些字段，这很好。但是，如果表单本身是以某种方式生成的，您无法通过查看提交的值来确定它包含哪些字段。

我从未质疑过这种状况，直到安迪·基尔克评论了我最近的文章 [*如何在 CSS3*](/css3-toggle-switch/) 中创建一个拨动开关。他制作了一个例子，使用与复选框和单选按钮同名的隐藏表单值来设置默认值(未选中的),例如

```
<input name="mycheckbox" type="hidden" value="0" />
<label><input name="mycheckbox" type="checkbox" value="1" /> checkbox</label>
<input name="myradio" type="hidden" value="0" />
<label><input name="myradio" type="radio" value="1" /> radio 1</label>
<label><input name="myradio" type="radio" value="2" /> radio 2</label>
```

因此，无论如何，mycheckbox 和 myradio 的值总是被提交。Andy 在 PHP Zend 框架中发现了它，并一直使用它。我不好意思承认我以前从来没见过这个窍门，也没自己试过。

## 好的解决方法？

隐藏字段默认的技巧很简单，虽然它让我有点不舒服。您正在向 HTML 页面添加不必要的元素，向 HTTP POST 添加数据，以解决特定于服务器的问题。或者，您可以使用 JavaScript 来定位所有复选框和单选按钮，然后创建隐藏字段，但是如果代码失败或被禁用，可能会导致服务器问题。

我的建议是:如果您知道表单包含哪些字段，就不需要隐藏字段来设置默认值。然而，在那些罕见的情况下，你需要一个可以分析任何形式的通用解析器，这是一个奇怪问题的合理解决方案。

这可能不是一个花哨的 HTML5 教程，但它是一个很好的技巧。谢谢安迪——你教了老狗一个新把戏！

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如真实世界的 [HTML5 & CSS3。](https://learnable.com/courses/html5-css3-for-the-real-world-1484?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)

对本文的评论已经关闭。对 HTML 有疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?52-(X)HTML?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章