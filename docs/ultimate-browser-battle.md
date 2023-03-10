# 最终浏览器大战谁赢了？

> 原文：<https://www.sitepoint.com/ultimate-browser-battle/>

汤姆的硬件最近完成了有史以来最彻底的浏览器测试之一。在[网络浏览器大奖赛](http://www.tomshardware.com/reviews/web-browser-performance-standard-html5,3013.html)中，IE9、Firefox 6、Chrome 13、Safari 5.1 和 Opera 11.5 在使用相同硬件的 Windows 7 和 Mac OS X 上相互竞争。

总共记录了 32 个测试，迭代 3 到 5 次，以评估加载时间、渲染速度、插件性能、内存使用、标准符合性和可靠性。结果应该会引起 web 开发人员和任何经常使用浏览器的人的兴趣。

## 各就各位。准备好。走吧。

[启动时间](http://www.tomshardware.com/reviews/web-browser-performance-standard-html5,3013-5.html)是用一个标签(谷歌主页)和八个标签(谷歌、脸书、YouTube、雅虎、Twitter、MSN、亚马逊和维基百科)。就单个标签而言，Windows 7 上的 Chrome 只需 0.8 秒，比 OS X 上的 Safari 略快。

Opera 是打开单个标签页最慢的浏览器，但只落后半秒钟。此外，Opera 打开八个标签页的速度最快——仅用了 1.46 秒，而 Windows 7 上的 Safari 则用了 7.93 秒。

## 页面加载五项全能

下一个测试评估了[页面加载时间](http://www.tomshardware.com/reviews/web-browser-performance-standard-html5,3013-6.html)。Chrome 在两种操作系统上都是赢家，在 Windows 上的平均时间为 778 毫秒，在 OS X 上为 692 毫秒。不出所料，基于 webkit 的 Safari 仅落后几毫秒。

IE9 表现不错，以略低于 100 毫秒的成绩名列第三。在 Window 7 上，紧随其后的是 Opera 和 Firefox，时间为 1，129 毫秒。在 OS X，Firefox 的 953 毫秒比 Opera 的 1190 毫秒要好。

## 性能台式压力机

执行了大量的浏览器基准测试，包括 T2 的未来标记和平卫士、T4 的 webkit 的 SunSpider、微软的迷宫解算器、Mozilla 的北海巨妖和 Dromaeo。

Chrome 在大多数情况下表现良好，但令人惊讶的是，它在 webkit 自己的 SunSpider 测试中被 IE、Firefox 和 Opera 击败。如果你希望大获全胜，你会失望的。浏览器可以在一个基准测试中领先，然后在另一个基准测试中落后。唯一令人震惊的结果是 Firefox 在 Maze Solver 中的 CSS 性能——在 Windows 上平均耗时 73.3 秒，在 OS X 上平均耗时 94.0 秒，而 Chrome 在这两种操作系统上平均耗时 3 秒。

以下测试评估了 [Flash、Java 和 Silverlight](http://www.tomshardware.com/reviews/web-browser-performance-standard-html5,3013-8.html) 、 [HTML5](http://www.tomshardware.com/reviews/web-browser-performance-standard-html5,3013-9.html) 、[硬件加速和 WebGL](http://www.tomshardware.com/reviews/web-browser-performance-standard-html5,3013-10.html) 性能。

你会期望大多数插件以相似的速度运行，Silverlight 也是如此。然而，Chrome 可以在 Flash 联盟的底部找到，并被大多数其他浏览器击败——尤其是 Opera。Firefox 高居 Java 榜首。

HTML5 和硬件加速基准测试由 Firefox 主导，IE 紧随其后。

## 记忆大师

[内存使用](http://www.tomshardware.com/reviews/web-browser-performance-standard-html5,3013-11.html)在高级用户中引起了一些最大的分歧。对于单个标签，IE9 使用的资源最少——只有 25.7Mb。在 Windows 上，紧随其后的是 Chrome (30.2Mb)、Safari (41.7Mb)、Firefox (57.9Mb)和 Opera (67.3Mb)。

OS X 浏览器需要更多的内存。Opera 效率最高，为 79.7Mb，其次是 Safari (96.6Mb)、Chrome (109.1Mb)和 Firefox (128.1Mb)。

然后用 40 个打开的拉环进行重载试验。Safari 的效率最高，Windows 上为 644.4Mb，OS X 上为 731.2Mb。在 Windows 上，其次是 Firefox (745.4Mb)、Opera (789.2Mb)、IE (858.5Mb)和 Chrome (991.8Mb)。最大的冲击来自 OS X 版本的 Firefox 和 Chrome:它们分别需要 1,185.7Mb 和 1,802.3Mb。我听过 Mac 用户的抱怨，但没有意识到它有那么糟糕。

下一个测试[关闭了 39 个标签](http://www.tomshardware.com/reviews/web-browser-performance-standard-html5,3013-12.html)，并监控五分钟后内存回收的情况。不出所料，Chrome 表现最好:它的单页单进程模型几乎返回所有的内存。

在 Windows 上，IE9 保持在 196.4Mb，其次是 Safari (324.1Mb)、Firefox (390.3Mb)和 Opera (399.9Mb)。OS X 用户再次受到重创——Safari 保留了 618.4Mb，而 Firefox 只释放了 135Mb 多一点。

## 标准拍摄

[一致性基准](http://www.tomshardware.com/reviews/web-browser-performance-standard-html5,3013-14.html)使用 [ACID3](http://acid3.acidtests.org/) 、[HTML5Test.com](http://html5test.com/)、 [CSS3 选择器](http://tools.css3.info/selectors-test/test.html)和 [ECMAScript test262](http://test262.ecmascript.org/) 检查 W3C 标准。

浏览器之间几乎没有。Chrome、Firefox 和 Safari 获得了相似的分数，Opera 和 IE9 排在最后。所有这些都提供了良好的标准支持，跨浏览器兼容性问题越来越少。

## 谁拿黄金？

评审提供[排名表](http://www.tomshardware.com/reviews/web-browser-performance-standard-html5,3013-15.html)、[分析表](http://www.tomshardware.com/reviews/web-browser-performance-standard-html5,3013-16.html)、[宣布获奖者](http://www.tomshardware.com/reviews/web-browser-performance-standard-html5,3013-17.html) …

Windows 7 冠军:

1.  金色:**Chrome 13**——一大堆胜利低估了弱点
2.  银牌:**火狐 6** —非中奖强分最高数
3.  铜牌:**IE9**——尽管随着竞争对手的浏览器更新更快，它越来越落后
4.  Opera 11.5 —它非常接近 IE9，但由于糟糕的内存管理和没有硬件加速而令人失望
5.  **Safari 5.1**——赢的次数最少，输的次数最多，主要是因为 Windows 版本没有跟上 OS X 的步伐。

OS X 冠军:

1.  金色: **Safari 5.1** —全方位的卓越性能
2.  silver:**Chrome 13**——缺乏其 Windows 同类产品的速度、可靠性和内存管理
3.  青铜级:**Opera 11.5**——不能完全匹配 webkit 浏览器
4.  Firefox 6——接近 Opera，但内存使用量惊人

也许最令人吃惊的结果是五个竞争的浏览器如此接近。除了一些可疑的 OS X 内存管理，所有的浏览器都提供了良好的标准支持和性能。

然而，每个人的体验会有所不同，这取决于他们的硬件、他们通常运行的应用程序的数量以及他们打开的标签的数量。把它们都试一试，挑一个最适合你的。

## 分享这篇文章