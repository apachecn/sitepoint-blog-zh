# jQuery 平滑页面链接。平滑锚点()

> 原文：<https://www.sitepoint.com/jquery-smoothanchor-function-smooth-page-scrolling/>

jQuery smoothAnchor 函数为你提供了一个**轻量级脚本，可以让你的页内锚点链接平滑滚动**。有几个插件做同样的事情，但这是我的轻量级版本，我知道它在所有主要浏览器中都能正常工作。因此，如果您在快速的代码片段之后平滑地滚动到部分标题，这个 2kb 的脚本可以很好地完成这项工作！

## 演示

*   [*转到第 1 节*](#section-1)
*   [*转到第二节*](#section-2)
*   [*转到第三节*](#section-3)

#### 第一节

Lorem ipsum 疼痛静坐 amet，结果肥胖精英。没有任何元素能使重力 id mollis lacus sagittis。克拉斯想要快乐而毛里斯发酵软木塞渴维 sapien。克拉苏·阿赞安诺 vestibulum est，sed suspicious felicis digniim a . donec ut turpi neque，at volutpat free .没那么容易。没什么可大惊小怪的，id lobortis enim egestas id .阿利奇坐着阿美自由自在地预示着发酵的时间。Ut aliquet 投票箱想要暂时的风险 ut maleficent who maleficent .香精发酵剂。Donec 在 enim 课程。口渴的老李。唐克加入 metus 和我的甜味剂。什么都没有。我想吃 sagittis 调味品。我不想让 lorem 知道。

vestibulum consequat，tortor in elementum laoreet，我讨厌 tellus dictum ipsum，at eleifend lorem purus et dui .柯萨斯·康莫德斯自由爱琴海奥奇·奥奇斯·费格福特。"我们渴望快乐地为普鲁伯特和 nec sapien 干杯"。阿利奇·布兰蒂的胡言乱语向康莫德斯问好。口渴的修辞官祝爱琴海杜十娘调味品摆。相曲霉 ac mauris diam，携带者 risus。口渴的女王带来痛苦。乌兹涅克·乌兹诺。公爵高兴地走进了努纳·奥创的大门。渴 id iaculis mauris。没有课程告诉 ipsum possesser mollis。我们吃了一个汗味的炸面圈。

[返回页首](#demo-title)

#### 第二节

Lorem ipsum 疼痛静坐 amet，结果肥胖精英。没有任何元素能使重力 id mollis lacus sagittis。克拉斯想要快乐而毛里斯发酵软木塞渴维 sapien。克拉苏·阿赞安诺 vestibulum est，sed suspicious felicis digniim a . donec ut turpi neque，at volutpat free .没那么容易。没什么可大惊小怪的，id lobortis enim egestas id .阿利奇坐着阿美自由自在地预示着发酵的时间。Ut aliquet 投票箱想要暂时的风险 ut maleficent who maleficent .香精发酵剂。Donec 在 enim 课程。口渴的老李。唐克加入 metus 和我的甜味剂。什么都没有。我想吃 sagittis 调味品。我不想让 lorem 知道。

vestibulum consequat，tortor in elementum laoreet，我讨厌 tellus dictum ipsum，at eleifend lorem purus et dui .柯萨斯·康莫德斯自由爱琴海奥奇·奥奇斯·费格福特。"我们渴望快乐地为普鲁伯特和 nec sapien 干杯"。阿利奇·布兰蒂的胡言乱语向康莫德斯问好。口渴的修辞官祝爱琴海杜十娘调味品摆。相曲霉 ac mauris diam，携带者 risus。口渴的女王带来痛苦。乌兹涅克·乌兹诺。公爵高兴地走进了努纳·奥创的大门。渴 id iaculis mauris。没有课程告诉 ipsum possesser mollis。我们吃了一个汗味的炸面圈。

[返回页首](#demo-title)

#### 第三节

Lorem ipsum 疼痛静坐 amet，结果肥胖精英。没有任何元素能使重力 id mollis lacus sagittis。克拉斯想要快乐而毛里斯发酵软木塞渴维 sapien。克拉苏·阿赞安诺 vestibulum est，sed suspicious felicis digniim a . donec ut turpi neque，at volutpat free .没那么容易。没什么可大惊小怪的，id lobortis enim egestas id .阿利奇坐着阿美自由自在地预示着发酵的时间。Ut aliquet 投票箱想要暂时的风险 ut maleficent who maleficent .香精发酵剂。Donec 在 enim 课程。口渴的老李。唐克加入 metus 和我的甜味剂。什么都没有。我想吃 sagittis 调味品。我不想让 lorem 知道。

vestibulum consequat，tortor in elementum laoreet，我讨厌 tellus dictum ipsum，at eleifend lorem purus et dui .柯萨斯·康莫德斯自由爱琴海奥奇·奥奇斯·费格福特。"我们渴望快乐地为普鲁伯特和 nec sapien 干杯"。阿利奇·布兰蒂的胡言乱语向康莫德斯问好。口渴的修辞官祝爱琴海杜十娘调味品摆。相曲霉 ac mauris diam，携带者 risus。口渴的女王带来痛苦。乌兹涅克·乌兹诺。公爵高兴地走进了努纳·奥创的大门。渴 id iaculis mauris。没有课程告诉 ipsum possesser mollis。我们吃了一个汗味的炸面圈。

[返回页首](#demo-title)

## jQuery 函数

```
var JQUERY4U = {}
JQUERY4U.UTIL = {
	/*
	*	Utility function used to make anchor links animate smoothly instead of jumping.
	*/
	smoothAnchor: function (anchorClass) 
	{
		$('a.'+anchorClass).click(function () {	
			elementClick = $(this).attr("href")
			destination = $(elementClick).offset().top;
			$("html:not(:animated),body:not(:animated)").animate({ scrollTop: destination}, 1100 );
			return false;
		})
	}
} 
```

据我所知，它不能被用作链接元素，因为“return this”会使它断断续续，所以我建议将其作为一个独立的函数。

## 使用

将“smoothAnchor”类包含到任何锚元素中，然后在该锚的 href 中简单地引用您希望转到的元素的 id。

锚定链接:

```
 [*how much can I borrow?*](#pagelink-1)
```

目标元素:

```
 未来的改进可能是使“返回顶部”链接动画平滑，这不会太难实现。 
```

## 分享这篇文章