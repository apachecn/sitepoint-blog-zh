# 什么是液体设计？

> 原文：<https://www.sitepoint.com/liquid-design/>

互联网作为一种媒介仍处于起步阶段，但是，慢慢地——非常缓慢地——设计师们开始接受它。嗯，反正是设计师的小意外。

令人沮丧的事实是，你会遇到的绝大多数网站，尤其是中小型商业网站，最好做成印刷小册子。

你知道我的意思:一个 720×400 像素的“窗口”坐在显示器中间，看起来孤独而无人爱。可能带有“大块按钮”“闪光旋钮”和传说中的“旋转标志”。

本文将向您介绍使用 CSS 和表格创建流畅的网页布局的基本概念。如果你以前没有真正探索过这个领域，现在是时候了！

##### 卷筒纸不是印刷的

发现自己面临创建或委托网站任务的人倾向于从印刷的角度思考。毕竟，这是我们所知道的。事实上，互联网可以通过许多不同的浏览器、平台、设置甚至设备来访问，而这些，我们潜在的网页设计客户，却完全忽略了这一点。

您的访问者几乎可以使用任何东西来访问您的信息，从流行主题的简单变化，如:

*   使用大字体的浏览器
*   关闭了 Javascript 和图像的浏览器
*   “另类”浏览器，如 Opera、Mozilla 或 Netscape

…更极端的情况，例如:

*   网络电视
*   纯文本浏览器
*   屏幕阅读器和盲文浏览器
*   手持设备

因此，试图在这样一个流动的、不可预测的媒介上强加严格的、僵化的、像素完美的设计是徒劳的。

##### 什么是液体设计？

术语“流动”意味着一个网站应该流畅地进入给定的任何空间。如果您使用高分辨率显示器，这可能意味着您需要稍微调整浏览器的大小，大多数人在这种情况下都会这样做。如果你有一个低分辨率的显示器，你仍然可以看到信息，只是更紧凑一点。

如果你做好了流动设计，你应该能够让你的页面显示在几乎任何东西上，并且对用户来说仍然有意义。

但这不仅仅是让页面在浏览器窗口中“流动”。液体设计的原则与可及性的原则是密切相关的。

不是每个人都有完美的视力，你的许多潜在客户可能确实是盲人。如果你用相对字体单位和基于百分比的宽度来构建你的网站，你已经让一部分访问者的生活变得更容易了。甚至可能是很多。

##### 理论说够了！

好的，那么我们如何在我们的网站上实现液体设计原则呢？嗯，你需要做的第一件事是改变你对网络的看法。整个液体设计概念是一种心态…

*   抛弃让你的页面在每个设备上看起来完全一样的需求
*   准备好妥协你的理想
*   在你设计的时候开始考虑可访问性问题
*   昂起你的头，你让网络变成一个更好的地方！

解决液体设计有两种主要方法:

1.  带表

3.  使用 CSS-P

我是 CSS(层叠样式表)的忠实拥护者，对于易访问性和流畅的设计(更不用说速度、良好的标记和 SEO 问题)来说，它是一个明显的赢家。干净整洁的标记不仅会让你的页面加载更快，而且你会发现编写流畅布局的整个过程更加直观。

….在任何人开始叫嚣 NN4 之前，是的，我知道:如果你的观众中有很大一部分是 NN4 用户，那么你最好使用基于表格的方法。

##### 将液体设计付诸实践

那么我们该怎么做呢？好吧，让我们从一个典型的三栏页面布局开始。首先，我们将在 CSS 中查看这一点，然后是表格。

***一个“裸骨”3 列 CSS 布局***

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "xhtml11.dtd">  

<?xml version="1.0" encoding="iso-8859-1"?>  

<html  xml:lang="en">  

    <head>  

        <title>Bare bones 3 column CSS layout</title>  

        <style type="text/css">  

            #leftpanel {  

                position: absolute;  

                top: 140px; /* resize these bits to liking */  

                left: 0;  

                width: 200px; /* works best with fixed   

                width right - left divs*/  

            }      

            #rightpanel {  

                position: absolute;  

                top: 140px; /* resize these bits to liking */  

                right: 0;  

                width: 200px; /* works best with fixed   

                width right - left divs*/  

            }  

            #content {  

                position: absolute;  

                top: 140px;  

                padding-left: 220px; /* 20px to play with */  

                padding-right: 220px; /* 20px to play with */  

                }  

            </style>  

    </head>  

    <body>  

        <h1>Bare bones 3 column CSS layout</h1>  

        <!-- Content here -->  

        <div id="content">  

        <p>You can put your content here, under the space   

        for a header, or you can re-arrange the   

        divs in the html to put your right or left column first.</p>  

        </div>  

        <!-- Right column here -->  

        <div id="rightpanel">  

        <p>This is a good place to pop nice optimized   

        text into, especially if your #content div   

         is full of images and little text. I like to   

        put an 'editors note' here: Just an excuse to   

        squeeze optimized copy into the html flow without   

        bothering the user ;-)</p>  

        </div>  

        <!-- Left navigation etc -->  

        <div id="leftpanel">  

        <p>Links, come last, presuming your 'header' has   

        a 'main sections' navigation with regular text links   

        this is just fine for SE's and magic for   

        optimization.</p>  

        </div>  

    </body>  

</html>
```

将该代码粘贴到 vim、记事本或任何您感兴趣的地方，您会发现这确实是一个简单的例子。您会注意到，当您调整浏览器大小时，中间的栏会伸缩以弥补差异。左右列保持固定。

如果你想知道为什么我没有让它完全流畅，答案很简单:它只是看起来和工作起来更好，至少我喜欢这样。尝试将左右列宽调整为百分比:应该可以正常工作。

您必须调整 css 中的注释部分，并添加更多的表示规则，但这个基本布局应该给任何新手一个很好的基础。

更多 CSS 资源可以在这里找到:

*   [Westciv CSS 网站](http://www.westciv.com/)
*   [W3 学校 CSS 课程](http://www.w3schools.com/css/)
*   [CSS 布局技巧](http://www.glish.com/css/)
*   [蓝色机器人布置蓄水池](http://bluerobot.com/web/layouts/)

如果您想知道是否可以在您的网页上实现流畅的 CSS 布局，您可能也想看看这些示例:

*   第一个是一个小型私人粉丝网站:[消失了](http://www.gonetoground.com/)
*   还有这个来自[皮尔斯学院远程学习](http://www.pierce.ctc.edu/Distance/)部。

来自不同网站的液体布局的好例子！

***与现在同桌的***

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"   

      "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">   

<?xml version="1.0" encoding="iso-8859-1"?>   

<html  xml:lang="en" lang="en">   

    <head>   

        <title>Bare Bones 3 column Liquid Tables Layout</title>   

    </head>   

    <body bgcolor="#FFFFFF">   

        <table cellspacing="0" cellpadding="0"    

        border="0" height="120" width="100%">   

            <tr>   

                <td align="center">   

                <h1>Bare Bones 3 column Liquid Tables    

                Layout</h1>   

                </td>   

            </tr>   

        </table>   

        <table cellspacing="0" cellpadding="20"    

        border="0" width="100%">   

            <tr>   

                <!-- Left column -->   

                <td width="200"><p>This is your left panel.    

                Generally used for navigation and    

                with this kind of layout, getting in    

                the way of the content!</p></td>   

                <!-- Content column -->   

                <td><p>This is your content area    

                    and flow with your browser just the same    

                    as the CSS layout above. The content    

                    is pushed down the code flow a bit    

                    by the tables layout but the end result is    

                    preferable to a fixed width page. You    

                    can see I'm biased, right?</p>   

                </td>   

                <!-- Right column -->   

                <td width="200"><p>Right column - fixed width at 200 px   

                again for the purpose of this demo. Just adjust the    

                widths to suit and don't be afraid to experiment!</p>   

                </td>   

            </tr>   

        </tr>   

    </table>   

    </body>   

</html>
```

你可能知道，我不像 CSS 布局那样习惯于写表格布局。尽管有其局限性，但是，如果您喜欢或者需要使用表格来构建布局，上面的示例为您提供了另一个液体设计的构建块。

##### 字体呢？

说得好！嗯，如果我们坚持我们的液体设计原则，我们将需要使用相对字体单位。我们的选择是:

*   em’s
*   % s
*   前任的
*   CSS 关键字(较大、较小等)
*   可怕的标签

使用相对单位会给你的用户调整字体的机会。我必须承认，我并不总是遵循自己的建议，但在我的大多数网站上，我选择了 em。关于这个问题的详细讨论将会是一篇全新的文章，所以我现在让你来决定。

##### 液体设计:能做到吗？

是的，毫无疑问！固定布局正在慢慢成为过去。创建一个流动布局(而不是固定布局)的原则和考虑因素在你的网站上很容易实现。

然而，整个液体的想法确实需要转移焦点。不要再认为网络是一个你可以控制的媒介，不要再为你的用户着想，你会更开心，你的访问者会更开心，网络也会让每个人更开心。

如果你喜欢我提到的想法，但是不能全部实现，那么不要担心。我也不能一直这样。如果你喜欢这个想法，讨厌这个想法，或者觉得我在说废话(这是众所周知的…)，那么请点击底部的“讨论这篇文章”链接，告诉我们为什么。网络是一个很酷的地方——它有容纳各种观点和想法的空间，我很想听听你的观点和想法！玩得开心！

## 分享这篇文章