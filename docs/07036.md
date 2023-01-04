# SitePoint 代码挑战:CSS–翻转卡片

> 原文：<https://www.sitepoint.com/sitepoint-code-challenge-css-flip-a-card/>

小时候玩过记忆游戏吗？这是一种纸牌游戏——你把所有的牌都面朝下，轮到你时，你翻两张牌。目标是获得匹配对。今天的挑战使用 CSS & JavaScript 来重现那个偷走了我很多童年时光的游戏。在我继续之前，我需要指出，我有点疏忽，没有将这些挑战的产生归功于论坛导师[麦克·莫里斯](https://www.sitepoint.com/forums/member.php?230575-Michael-Morris "Michael Morris profile")。

这是我们的 HTML:

```
<!DOCTYPE html>
<html>
<head>
  <title>Memory</title>
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js"></script>
  <script type="text/javascript">
    $('.card').click(function(){
	  $(this).toggleClass('down');
	});
  </script>
</head>
<body>
  <div id="Playfield">
    <div data-face="1"></div>
    <div data-face="2"></div>
    <div data-face="3"></div>
    <div data-face="4"></div>
    <div data-face="5"></div>
    <div data-face="6"></div>
    <div data-face="7"></div>
    <div data-face="8"></div>
    <div data-face="9"></div>
    <div data-face="10"></div>
    <div data-face="11"></div>
    <div data-face="12"></div>
    <div data-face="1"></div>
    <div data-face="2"></div>
    <div data-face="3"></div>
    <div data-face="4"></div>
    <div data-face="5"></div>
    <div data-face="6"></div>
    <div data-face="7"></div>
    <div data-face="8"></div>
    <div data-face="9"></div>
    <div data-face="10"></div>
    <div data-face="11"></div>
    <div data-face="12"></div>
  </div>
</body>
</html>
```

现在，这个挑战分成两部分。挑战的 CSS 方面是做以下事情:

**初级**
风格牌的形状、背面和正面。响应 Javascript 切换“向下”的存在-如果类存在，卡片应该面朝下。
您的样式必须允许 Javascript 改变卡片在 DOM 中的位置或卡片的绝对位置。

当玩家成功匹配一对牌时，Javascript 也会对牌应用一个“匹配”类。你需要为此设计风格(也许通过改变边框颜色)。为了测试你的风格，你需要手动编辑 HTML(或者[做 JS 挑战](https://www.sitepoint.com/forums/showthread.php?991431-Java-Script-Challenge-A-Game-of-Memory "Related Javascript challenge"))。

**高级和专家级**
使用 3D 过渡制作翻转动画。

如果你有 Javascript 编程技能，在 [Javascript 论坛](https://www.sitepoint.com/forums/forumdisplay.php?15-JavaScript-amp-jQuery "Javascript forum")中有一个挑战。如果没有，为什么不找一个有经验的人，让它成为一个团队挑战呢？

注意——data-face 属性的存在是为了使 JS 代码更容易编写，尽管它对于最终的 CSS 类名来说是多余的。可以对属性进行样式化，但是浏览器对这种技巧的支持并不可靠。两者都是为了降低挑战的难度。

您可以在这里关注挑战并获得解决方案。

## 分享这篇文章