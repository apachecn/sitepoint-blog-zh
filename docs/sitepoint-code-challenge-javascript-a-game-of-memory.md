# SitePoint 代码挑战:Javascript——记忆的游戏

> 原文：<https://www.sitepoint.com/sitepoint-code-challenge-javascript-a-game-of-memory/>

这是昨天 CSS 挑战的第二部分。我们社区中的 CSS 大师们已经接受了挑战，要对记忆游戏的卡片进行设计和动画制作，这个挑战就是对游戏进行裁决。

以下是我们正在使用的 HTML:

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

昨天我们给 CSSers 提供了足够的 JS 来测试他们的风格——切换卡片的类别。这是挑战的其余部分，它的大部分逻辑可以与上面的点击事件联系起来。

这些卡片是成对的，他们最后的班级名字揭示了匹配。

当一张卡被点击时，需要检查是否有其他没有 CSS 类“匹配”的卡面朝上。如果有，两张卡的 data-face 属性值是否匹配？如果是这样，这两张牌就获得了“匹配”的 css 类别，否则将两张牌面都翻过来。

你还需要测试什么时候所有的牌都匹配，以给出“你赢了”的结果。

高级洗牌的位置。你可以用很多方法做到这一点——但是如果你选择改变数据表面的值，请确保你改变了相应的 css 类，因为这是样式如何给卡片它的特定图像。

**专家**
做一个双人游戏并记分。

在所有的关卡中，你都可以在 HTML 中添加额外的控件，比如重置游戏或者随机按钮，但是不要删除任何东西，否则你就有破坏为挑战而编写的样式表的风险。

在接下来的几天里，我们会在这个帖子里给出一个解决方案。

编码快乐！

感谢[麦克·莫里斯](https://www.sitepoint.com/forums/member.php?230575-Michael-Morris "Michael Morris")设计了这个挑战。

## 分享这篇文章