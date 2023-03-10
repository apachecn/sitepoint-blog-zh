# 整理 JavaScript 数组排序

> 原文：<https://www.sitepoint.com/javascript-array-sorting/>

考虑以下 JavaScript 代码:

```
var a = [30,2,1,9,15];a.sort();alert(a);
```

输出会是什么？如果你期待 1，2，9，15，30，你会惊讶地听到实际结果是 1，15，2，30，9。不要放弃 JavaScript 一旦你知道如何使用数组排序，它就会变得非常强大。这是怎么回事？当没有向 sort 方法传递任何内容时，每个值都被转换为一个字符串，并按字典顺序排序，即“15”将出现在“2”之前。“10”和“19999999”也会。要解决这个问题，我们需要向 sort()方法传递一个比较函数。该函数必须有两个参数——我们将其命名为 a 和 b——并返回:

*   如果 a 小于 b，则该值小于零
*   如果 a 大于 b，则该值大于零
*   如果 a 和 b 相等，则为零

因此，最简单的数字比较函数是:

```
function compare(a, b) {	return a - b;}
```

我们可以将 compare 函数作为参数传递给 sort 方法，或者将其内联写入。

```
var a = [30,2,1,9,15];a.sort(function(a,b) { return a-b; });alert(a);
```

JavaScript 的一大优点是我们的比较函数可以根据任何属性对任何类型的对象进行排序。例如，我们将定义一个位置和家庭坐标列表:

```
// location co-ordinatesvar locations = [	{ name: "shops", x:3, y:4 },	{ name: "library", x:5, y:3 },	{ name: "pub", x:1, y:2 }];// home co-ordinatesvar home = { name: "home", x:0, y:0 };
```

接下来，我们将为计算两点间距离的函数添加一点毕达哥拉斯公式:

```
// distance between 2 co-ordinatesfunction distance(p1, p2) {	return Math.sqrt(Math.pow(p1.x-p2.x,2)+Math.pow(p1.y-p2.y,2));}
```

我们现在可以根据离家的距离对位置进行排序，从最短到最远:

```
// sort by shortest distance homelocations.sort(	function(a, b) {		return distance(home,a)-distance(home,b);	});// locations sorted: pub, shops, library
```

或者，我们可以通过反转参数，从最远到最短的距离进行排序:

```
// sort by furthest distance homelocations.sort(	function(b, a) {		return distance(home,a)-distance(home,b);	});// locations sorted: library, shops, pub
```

或者我们可以按字母顺序排列地名:

```
locations.sort(	function(a, b) {		if (a.name < b.name) return -1;		if (a.name > b.name) return 1;		return 0;	});// locations sorted: library, pub, shops
```

很容易开发一系列可重用的排序函数，这些函数可以应用于包含相似属性名的任何对象。在这方面，JavaScript 排序比许多其他语言更容易、更灵活。

**note:**Want more?

如果你想阅读更多 Craig 的文章，请订阅我们每周的科技极客时事通讯， *[《科技时报》](https://www.sitepoint.com/newsletter/)* 。

## 分享这篇文章