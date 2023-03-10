# 评论驱动的开发

> 原文：<https://www.sitepoint.com/comment-driven-development/>

你可能听说过[测试驱动开发](http://en.wikipedia.org/wiki/Test-driven_development)，一种专注于[测试用例的编程方法，作为设计和开发代码的框架](https://www.sitepoint.com/ptest-php-unit-tester-in-9-lines-of-code/)。你可能也听说过[行为驱动开发](http://en.wikipedia.org/wiki/Behavior_driven_development)，这是一种更全面的方法，关注于开发过程之前和期间的应用程序行为。

让我告诉你我的编程方法——**注释驱动开发**。首先用简单的语言写一个注释，解释一段代码应该做什么，以及为什么。然后写代码。

有时这是解决问题最直接的方法；如果它很容易解释，但很难用逻辑表达，你用这种方式写下来的内部话语可以帮助你理清思路，找到一个合乎逻辑的解决方案。相反，有时编写代码很容易，但解释起来却困难得多；在这种情况下，写注释可以帮助你阐明代码的目的，使它对其他人来说是可解释的。撰写注释的行为就像文档的前身。

事实上，有时当我写评论时，我发现我对有问题的代码的想法发生了变化。因为这个解释听起来很可笑，或者因为我必须解释一个缺陷或不可取的妥协。向其他人解释它的行为——这是一个好的评论应该做的——帮助你自己更好地理解它，有时这导致改变或完全废弃它。

当我编写可访问的拖放脚本时，有几次我根本不知道下一步该做什么——如何用逻辑表达看上去和感觉上正确的行为。所以我采用了这种技术，从一个解释开始:

```
//if - the direction of movement is positive; and
//	clone left/top plus clone width/height is greater than box left/top; and
//	clone left/top is less than box left/top
//or - the direction of movement is negative; and
//	clone left/top is less than box left/top; and
//	clone left/top plus clone width/height is greater than box left/top
```

一旦我做到了这一点，就只需要编写代码来实现我所解释的内容了:

```
if
(
	(this.positive
		&& cloneprops.xy + cloneprops.wh > boxprops.xy
		&& cloneprops.xy < boxprops.xy)
	||
	(!this.positive
		&& cloneprops.xy < boxprops.xy
		&& cloneprops.xy + cloneprops.wh > boxprops.xy)
)
```

相反，有些情况下，我立即知道要写什么代码，但我也知道我会很快忘记它的用途:

```
var sibling = dbx.getSiblingBox(this.box, 'nextSibling');
if(this.box == sibling || this.boxes[i] == sibling) { return; }
```

于是就有了一个解释:

```
//look for a next sibling of this box
//and don't continue if there isn't one,
//or it's the same as the box we're inserting before
//so that we're not doing an action that would result in no change
//this filtering improves efficiency generally,
//and is necessary specifically to stabilize the animation
//otherwise the multiple unecessary calls would overload the animation timers
//and the result would be snap movement with no apparent transition
```

我已经痛苦地认识到，一次又一次地回到剧本上，却不知道它是如何运作的，这是多么令人沮丧。请看这个例子，这是我多年前写的一个象棋分析脚本:

```
sqR=new Array(vmr,vmr);
for(j=0;j<sqid[vmr][1];j++){
	if(sqid[(vmr-1)]&&(sqid[vmr][1]-sqid[(vmr-1)][1]==1)){
		sqR[2]=vmr-1;
		com(vmn,sqR[2]);
		}
	if(sqid[(vmr-9)]&&(sqid[vmr][1]-sqid[(vmr-9)][1]==1&&sqid[vmr][0]-sqid[(vmr-9)][0]==1)){
		sqR[3]=vmr-9;
		com(vmn,sqR[3]);
		}
	}
```

不要问我它在干什么，我不知道！

所以现在我的注释就像是过时了，每一行代码至少有一行注释，通常是两三行。有时候我的评论是迷你史诗，就像这个怪物:

```
/*
use offleft positioning instead of display/visibility,
so that the menus are still accessible to screenreaders
the height and overflow:scroll is to reduce the amount of rendered output,
which speeds up the onload process
(using clip or overflow:hidden would also hide them from screenreaders)
but there are rendering or positioning problems
in mac/ie5, old gecko and opera < 7.5
in fact it screws up opera 7.23 completely - parts of the browser UI freeze up (!?)
so the styles are hidden from opera < 7.5 using the html[xmlns] selector
http://www.dithered.com/css_filters/css_only/xmlns_attribute_selector.html
this means that it won't happen on pages without an xmlns attribute
in other words - on HTML 4 pages, any browser-based screenreader using Opera 7.5-8 won't get the submenus
opera 9 and other browsers aren't affected because all are included in later selectors somewhere
opera's own speech/reading capabilities are not affected either,
because it generates events that open the menus, resetting all of this
and afaik there isn't any other browser-based reader that uses opera
so the likelihood is that this hole won't affect anybody at all
nonetheless it's still unfortunate, but it's practicably unavoidable -
the only other way to differentiate opera versions like this is the selector:lang(xx) pseudo-class,
but then we'd need a new custom varible to specify the user's language code
the styles are also hidden from old gecko builds using the commented selector hack
and from konqueror < 3.2 using the backslash hack, because it makes the navbar collapse
*/
```

所有这些都记录了一行 CSS:

```
html/**/[xmlns] .udm u\l{position:absolute;left:-10000px;height:0;overflow:scroll;}
```

所以，好吧，这是开玩笑的。但是没有人会因为你写了太多评论而抱怨你！我的同事甚至称赞我评论的质量和透彻性。也许称之为方法论有点牵强，但这仍然是个好主意。

## 分享这篇文章