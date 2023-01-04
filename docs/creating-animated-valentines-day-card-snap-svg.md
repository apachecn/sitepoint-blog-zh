# 用 Snap.svg 创建动画情人节贺卡

> 原文：<https://www.sitepoint.com/creating-animated-valentines-day-card-snap-svg/>

去年我向你展示了如何用 Snap.svg 库创建一个独立于分辨率的信息图。Snap 是一个 JavaScript 库，旨在简化 SVG 的使用。

虽然我们取得了良好的效果，但我们的创作基本上是一幅静态的图画，没有任何真正的华而不实。

今天我将向你展示如何实现一些很酷的效果和动画，同时我们创建一个动画 SVG 情人节爱情卡。您还将学习如何整合 Google 的 web 字体以及 SVG 绘图和一些高级文本操作。

虽然在许多图形程序中创建静态 svg 相对简单，但 Snap.svg 是创建动态交互式 SVG 的最佳方式之一。我们将介绍一些您应该能够应用于许多不同应用程序的技术。

让我们开始吧。

首先，打开您最喜欢的编辑器，创建一个新的 HTML 文档。然后，我们添加了对两种 Google web 字体和 Snap.svg 库的引用。

我们将把接下来的 3 行放在`head`标签中。这为我们设置了 Snap，并包含了一些谷歌字体。

```
<link href='http://fonts.googleapis.com/css?family=Niconne' rel='stylesheet' type='text/css'>
<link href='http://fonts.googleapis.com/css?family=Oleo+Script' rel='stylesheet' type='text/css'>
<script src="http://cdnjs.cloudflare.com/ajax/libs/snap.svg/0.3.0/snap.svg-min.js">
</script>
```

现在，在`body`标签中，我们创建了一个`script`标签，并将以下代码放入其中:

```
window.onload = function () {

var card = Snap(600, 400);
}
```

`windows.onload`函数确保页面在执行任何 JavaScript 之前被完全加载。`card`变量代表我们的 SVG 画布，其中`width`和`height`设置为 600 和 400 像素。

```
<!-- SVG Heart Shape -->

	<svg version="1.1" id="heart"  xmlns:xlink="https://www.w3.org/1999/xlink" x="0px" y="0px"
	     width="300px" height="200px" viewBox="0 0 300 200" enable-background="new 0 0 300 200" xml:space="preserve">
	<path fill-rule="evenodd" clip-rule="evenodd" fill="none" d="M149.95,43.749C111.115-25.583,0.729-9.406,0.002,71.063
	    c-0.402,44.195,52.563,60.716,87.829,78.384c34.196,17.135,58.534,40.574,62.347,50.553c3.266-9.777,30.373-33.88,62.028-51.032
	    c34.612-18.755,88.231-34.189,87.829-78.385C299.304-10.087,186.998-22.818,149.95,43.749z"/>
	</svg>
```

接下来，我们将上面的原始 SVG 代码放在`script`标签下面。这是心形的 SVG 路径，我们稍后会用到。我使用 Inkscape 创建路径数据。然后，我们回到`script`标签中，将下面几行代码放在`card`变量下面。

```
// Utilities

    var dropShadow = card.paper.filter(Snap.filter.shadow(0, 2, 3))

    var bgGradient = card.paper.gradient("r(0.5, 0.5, 0.5)#EE2C34-#821D2D");
    var barGradient = card.paper.gradient("l(0, 0.5, 1, 0.5)#00ADEF-#EC008B:75");
```

这是一些工具:一个阴影过滤器和两个梯度(一个径向和一个线性)。我们将在下面的代码中用到它们。

现在，我们准备开始构建实际的卡。我们开始添加一个温暖的红色旭日背景。

```
// Card's Background

    var background = card.paper.rect(0, 0, 600, 400).attr({fill: bgGradient, stroke: "none"})

    var rays = card.circle(300, 200, 300).attr({
      fill: "none",
      stroke: "red",
      strokeWidth: 580,
      strokeDasharray: "20 20",
      opacity: 0.2
    });
```

我们创建一个矩形，并用已经定义好的径向渐变填充它。接下来我们要做的是一束光线从卡片的中心向外扩散。这看起来很棘手，但是由于一个小的笔画技巧，我们可以很容易地达到这个效果。

首先，我们创建一个红色描边和填充设置为“无”的圆，然后，为了模仿光线的效果，我们将`strokeWidth`设置为极度勾号(填充整个画布)，并给`strokeDasharray`属性一个值“20 20”(产生一条虚线)。

好的。舞台已经搭好了！再加上演员吧。

```
// Hearts Shapes

    Snap.select("#heart").appendTo(card);

    var heartLeft = Snap.select("path").attr({
        fill: "#00ADEF",  
        filter: dropShadow, 
        transform: "t50,80",
        opacity: 0.9
    }).insertAfter(rays);

    var heartRight = heartLeft.clone().attr({
        fill: "#EC008B", 
        filter: dropShadow,
        transform: "t250,80", 
        opacity: 0.9
    });
```

现在，我们需要放在`body`标签末尾的 SVG 代码。首先，我们通过它的 ID `#heart`选择它，并把它附加到画布上。然后，我们选择实际的路径，赋予它所需的属性，并确保它被插入到光线之后(这意味着在它们之上)。接下来，我们通过克隆第一个心形并改变`fill`和`transform`属性来创建第二个心形。

我们有两颗可爱的心，现在我们想在上面写上“我爱你”。这里我们将调用谷歌的网络字体“油性字”。

```
// Hearts Texts

    var iuText = card.paper.text(185,200, ['I','U']).attr({
        fill: "#B5DD25", 
        fontFamily: "Oleo Script", 
        fontSize: "72px", 
        filter: dropShadow,
        opacity: 0
    }).animate({opacity: 1}, 2000);

        iuText.select("tspan:nth-child(2)").attr({dx: "160px"});

    var loveText = card.paper.text(290,85, ["L", "O", "V", "E"]).attr({
        fill: "#B5DD25", 
        fontFamily: "Oleo Script", 
        fontSize: "24px", 
        filter: dropShadow,
        opacity: 0
    }).animate({opacity: 1}, 2000);

        loveText.selectAll("tspan").attr({x: 290, dy: "1.2em"});
```

第一段文本创建了两个字符串“I”和“U”。默认情况下，它们被放置在一起，但我们希望它们分开。我们通过选择“U”字符串并将其移动 160 像素(通过使用`dx`属性)来做到这一点。

第二个文本创建了四个字符串:每个字符串代表单词“love”的一个字母。最后一行代码通过选择每个字母并给它一个相同的坐标`x`，并将`dy`属性设置为“1.2em”，使文本垂直显示。

现在，是时候移动到我们卡片的底部了。

```
var ribbon = card.paper.rect(0, 300, 600, 70).attr({fill: "#6F2570", filter: dropShadow});

    var bar = card.paper.rect(0, 300, 600, 10).attr({fill: barGradient});
```

在这里，我们创建了一个顶部有一个条的 ribbon。我们将投影效果应用到色带上，并使用与心形相匹配的颜色填充线条。接下来我们要添加的是一个很酷的旋转文本。这次我们将使用谷歌的网络字体“Niconne”。

```
// Rotating Wish Text

    setTimeout(function(){

      var wishText = "Happy Valentine's Day \u2763";  // create the wish text

      var charsGroup = card.g();  // create a group for the individual characters

      var charsArray = wishText.split('');  // split the wish text to characters 
      var wishTextChars = charsGroup.text(110, 355, charsArray).attr({  // create a text element with an individual string for each character
          fill: "#D6DF23",
          fontFamily: "Niconne", 
          fontSize: "42px",
          filter: dropShadow,
          opacity : 1, 
      });

      var singleChars = charsGroup.selectAll("tspan").attr({opacity: 0});  // select all strings/characters and hide them initially

      function charsAnimation( element ) {  // a function for animating each character
        Snap.animate(0,1, function( value ) {
          element.attr({ opacity: value, rotate: (value * 360) });
        }, 2000 );
      }

      for( var i=0; i < singleChars.length; i++ ) {  // go through each character and animate it
        setTimeout( 
          charsAnimation.bind(null, singleChars[i]) , i * 300);
      }

    }, 2000);
```

为了完成我们的创作，我们将添加一个随机雨心效果。这在标准的 SVG 图形程序中很难实现。

我们将随机选择这场雨的四个特征。两个不同的心形字符将以随机的大小、颜色和时间呈现。

```
// Hearts Rain Effect

    setTimeout(function(){

      function randomNums(min, max) {  // a function generating random numbers

        return Math.floor( Math.random() * ( 1 + max - min ) ) + min;

      }

      setInterval(function () {  // create new hearts set every 2 seconds

        for (var i = 1; i < 29; i++) {

          var hearts = ["\u2764", "\u2665"];  // unicode characters for hearts
          var colors = ["#D691BF","#D74498", "#A54A9C"];  // different colors for the hearts

          var heart = randomNums(0,1);
          var color = randomNums(0, 2);
          var size = randomNums(3, 6);
          var time = randomNums(5, 10);

          var mutableX = i * 20;

          var heartsRain = card.paper.text(mutableX, -50, hearts[heart]).attr({
              fill: colors[color],
              fontSize: size * 5, 
              opacity: 0.3
          }).animate({transform: "t0,500", opacity: 0.9}, time * 1500, mina.linear);

        };

      }, 2000);

    }, 7000);
```

现在我们结束了。你可以在这里看到最后的结果:

在 [CodePen](http://codepen.io) 上看到笔[vEpQOr](http://codepen.io/anon/pen/vEpQOr/)by([@ Secret Sam](http://codepen.io/anon))。