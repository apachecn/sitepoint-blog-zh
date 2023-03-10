# 蛰伏的 Javascript，隐藏的 PHP [2]

> 原文：<https://www.sitepoint.com/crouching-javascript-hidden-php-2/>

继续昨天的[咆哮](https://www.sitepoint.com/blog/)，如果我能弄清楚它的走向，这一部分应该(希望)变得更有趣一点。

首先，对最后一部分已经有了一些好的反馈(谢谢！)，最中肯的是艾伦；

> 我可以告诉你，在使用过 SOAP、各种 RPC 层和简单的 GET/POST / XML 解析之后，大多数时候 RPC 往往会耗费大量时间，对于前 3 个测试调用来说是很好的，但是随着项目的增长，会有一个点，它不会像预期的那样工作。然后你进入地狱中最可怕的噩梦之一去调试。

> 当 javascript 出现时(特别是当你冒着生命危险试图让一个 MS 实现工作的时候)，坚持基础要容易得多..根据需要 KISS / POST/GET/ strings 和 XML..

同样有趣的是 [Danne Lundqvist 的](http://www.dotvoid.com/index.php)与 [Javascript Serializer](http://www.tekool.net/php/js_serializer/) 对脚本 src 属性所做的相似但不同的方法；使用他在这里描述的 DOM 操作属性。

【Javascript 怎么了

回到上次的话题:“现在是 2004 年。现在肯定有人已经一劳永逸地解决了这个问题。”(问题是我们如何从服务器把新数据拉进一个已经加载的页面)，认为有必要尝试理解(猜测)为什么“事情”没有进一步进展。

我认为这里的出发点是问“Javascript 是怎么回事？”这真的是一个谜。

如果你*真的*仔细看看 [ECMAScript](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 规范(长话短说:ECMAScript == Javascript ),并对像 [Mozilla 的](http://www.mozilla.org/js/spidermonkey/)这样的体面实现做一些认真的研究，你可能会得出一个惊人的结论——作为一种动态语言，这个设计非常出色。

尽管 Javascript 通常只应用于浏览器脚本的特定目的(因此它的设计中有很多问题从来没有真正考虑过)，你可能会问为什么 Perl 或 PHP 没有被很好地构建。比如用 PHP 试试这个；

 `try {
eval( someJsInThisVar );
} catch ( e ) {
alert ( "Syntax error "+e+" in "+someJsInThisVar);
}`

…或者，正如我在这里所说的，给基本对象附加新的功能，比如；

 `String.prototype.toPHP=function() {
var s = this;
return 's:'+s.length+':"'+s+'";';
};`

因此，如果我们说 Javascript 实际上是一种非常好的编程语言，为什么不被更广泛地使用呢？今天，它似乎仍然被降级为在网页上添加偶尔简单的噱头，同时被 W3 委员会的成员称为有史以来最糟糕的发明。

[道格拉斯·克洛克福特](http://www.crockford.com/)用[这种世界上最容易被误解的编程语言](http://www.crockford.com/javascript/javascript.html)进行了总结。我还会将 XML 解析添加到 Douglas 提到的原因中——DOM 通常不是最简单的方法——Javascript 需要一个原生 SAX 解析器(尽管你可以在这里找到一个纯 Javascript 实现、一个[拉解析器](https://www.sitepoint.com/blog/)和类似于[简单 XML](http://www.php.net/simplexml) IMO 的东西。

最终，Javascript 的采用归结为开发人员的良好判断力——在过去的,浪费无数时间在多种浏览器上编写和调试 Javascript 太容易了。如果将更多的时间转移到服务器端，就可以节省数小时，因为在服务器端，您有一个可以信赖的环境。只有[几个](http://www.bitflux.ch/produkte/bitfluxeditor.html)足够大胆(或疯狂)地用 Javascript 重载。

我说过去的是因为我认为 Mozilla 已经改变了这一点——我们现在有了一个可以信任的实现，加上像 [Venkman](http://www.mozilla.org/projects/venkman/) 这样的工具来帮助解决问题(附带说明——最近我发现单元测试消除了很多调试的需要—[jsunit](http://www.edwardh.com/jsunit/)似乎是 Javascript 的最佳选择)。

今天，如果你首先为 Mozilla 编写代码，微软最近的 JScript 实现已经足够接近，只需要稍作调整就能把事情做好。偶尔会有*啊啊啊！*像这样；

 `Foo.prototype = {
x: 1,
y: 2,
}`

IE(上次我看到的)不喜欢第二个逗号(我不得不说出来)。

**连接服务器和客户端**

我对客户端的基本看法一直是“我宁愿做服务器端”。我的部分动机可以归结为道格拉斯·克洛克福特的观点；历史上对 Javascript 的不信任。但 Mozilla 开始改变这种情况。

但是还有另一个更关键的问题，那就是用 Javascript 做一些非常奇怪的事情，我还没有看到详细的讨论。

如果我正在构建一个主要是服务器端的动态应用程序，这已经足够复杂了——在客户端用 Javascript 添加另一层逻辑是自找噩梦(正如 Alan 指出的)。

在我看来，最大的问题是*易损坏性*——如果我对服务器端进行更改，那么很容易在不经意间损坏客户端。这是因为我通常会或多或少地独立发展这两者。

虽然它们之间显然有关系，但是没有内置的连接，所以对服务器端的更改不能保证反映在客户端。

**易碎性**

回到前面的观点，在评估从服务器端获取更多信息的各种技术时，不需要重新加载整个页面，我认为评估它们相对价值的一个关键点是*易断性*(更多地滥用英语)。如果你在这里*做了改动*，你在那里*弄坏东西*的几率有多大？

采用 GMails 框架+密集型 Javascript 方法完成任务(一夜之间，我现在有了一个帐户——非常感谢您的提议！)，我把它看做是极易破碎的。对于谷歌来说，这可能不是问题，他们有足够的人员来做好这件事，但是从用户的角度来看，服务器端元素之间的关系已经深深地嵌入到了客户端。这可能是一个天真的问题，但是改变服务器端 GET 变量的名称会有什么影响呢？

当我想到 Livesearch 的时候，我也会有类似的想法。基于相同的方法构建和管理复杂的应用程序有多容易？这并不是说这不可能，但是我假设 Christian 将[客户端](http://blog.bitflux.ch/livesearch.js)与[服务器](http://blog.bitflux.ch/livesearch.php?s=PHP)分开维护。服务器使用 CSS 类，这里定义为。一方面 Livesearch 是 [KISS](http://www.catb.org/~esr/writings/taoup/html/ch01s07.html) 的缩影。另一方面，各种独立实体(Javascript、CSS 和 PHP)之间的依赖关系真的等于固有的复杂性吗？

所以我想问题归结为如何将服务器端的变化干净地传播到客户端？

**来自 SOAP 的教训**

我认为 SOAP 有一个(但不是更多)可取的特性。事实上，它不是由 SOAP 本身定义的，而是由 WSDL 定义的。WSDL 意味着访问像[迪特里希·阿亚拉的](http://dietrich.ganx4.com/)即将成为总统的服务与[梨::肥皂](http://pear.php.net/package/SOAP)可以减少到只是；

 `< ?php require_once 'SOAP/Client.php'; $WSDL = & new SOAP_WSDL('http://dietrich.ganx4.com/president/server.php?wsdl'); $Predictor = $WSDL->getProxy();`

 `$ Prediction = $ Predictor-> whoisgoingtobepresident()；` 

`echo 'Kerry: '.$Prediction->kerry.'
';
echo 'Bush: '.$Prediction->bush.'
';`

WSDL 提供了服务的精确定义，只需知道 WSDL 文档的 URL，就可以生成和使用服务的客户端。您不需要关心底层网络或允许数据在网络上传递所需的数据编码。

就昨天的咆哮而言，*机制*和*有效载荷*已经被处理好了，你不需要考虑它们。剩下的就是*人口*——以视觉上吸引人的方式显示数据。如果远程服务发生变化，当然会破坏您的 UI，但是您只需要考虑进行正确的 RPC 调用并调整显示结果的方式。

在我看来，连接 web 应用程序的服务器端和客户端需要这么简单。与此同时，只有 Mozilla SOAP 实现,上次我尝试的时候并不愉快。在我看来，SOAP 从根本上意味着膨胀——对于这个问题来说，它太复杂了。我们并不关心让全世界都能访问 web 服务——我们只对让 Javascript 为单个应用程序与服务器端对话感兴趣。

**Javascript 生成**

在我看来，另一种方法是去掉中间人(WSDL ),让服务器端生成准备好与服务器对话的 Javascript，只留下*人口*来考虑(为了一个好看的设计，我们可能需要一些水平的人类发明)。

一般来说，生成反映服务器端发生的事情的 Javascript 可以做得很好。

例如，PEAR::HTML_QuickForm 在验证方面做得很好。如果您指定一个服务器端表单验证规则，比如一个要求字母数字字符的正则表达式，QuickForm 也可以在输出 HTML 中嵌入与 Javascript 相同的规则，这样用户就不必在发现错误之前重新加载页面。作为开发人员，您不需要考虑 Javascript——它是自动生成的。改变 PHP 中的正则表达式，Javascript 也会随之改变。

我们已经在带有 [<inputautocomplete></inputautocomplete>](http://wact.sourceforge.net/examples/tags/form/inputautocomplete.php)标签的 [WACT](http://wact.sourceforge.net) 中使用了类似的技术，它执行客户端自动完成(尝试键入一个国家的名称)。没必要手动摆弄 Javascript。在你的 HTML 模板中你可能有；

 ``<form method="post" runat="server"><inputautocomplete id="countries"><input type="submit"></inputautocomplete> </form>`  `然后，在 PHP 中，您可以加载带有条目列表的标记，以便根据 like 自动完成；

 `$Page = & new Template('/form.html');
$CountryInput = & $Page->getChild('countries');
$CountryInput->setAutoCompleteList($listOfCountries);
$Page->display();`

在任何时候你都不会碰到 Javascript。

那么，我们如何应用代码生成，以一种有用的方式，将数据从服务器端拉进一个已经加载的页面呢？

记住 SOAP/WSDL，我的观点是生成一个 Javascript 客户端，它负责机制和有效负载，只需要被开发者调用。

为了避免长时间的讨论，从使用它的开发人员的角度来看，我认为它应该如何工作。

如果我有一个 PHP 类像；

 `class UserAdmin {
function & listUsers() {
return new DBC::NewRecordSet("SELECT * FROM users");
}`

`function deleteUser($username) {
$sql = "DELETE FROM users WHERE username='".DBC::escape($username)."'";
return DBC::execute($sql);
}
}`

您应该能够用 Javascript 脚本生成器注册它，可能类似于；

 `$Generator = & new Javascript_Generator();`

`if ( isset($_GET['class']) && $_GET['UserAdmin'] ) {
echo $Generator->generate('UserAdmin');
}`

无论它生成什么，我都可以直接用 Javascript 调用。

事情没那么简单——如果我们想对远程服务器使用异步调用，就需要定义回调，我还没有讨论 PHP 的“监听器”,它像“listUsers”一样响应请求。

但这是最基本的想法。生成的 Javascript 客户机是使用反射从我在 PHP 中定义的类构建的。换句话说，我可以使用 Javascript 中的一个对象，该对象根据 PHP 类定义进行操作。对于我用 PHP 和 Javascript 编写的代码来说，机制和有效负载是“不可见的”——这只是用数据填充 UI 的问题。有希望的是，潜在的“易断性”要低得多，并且变化可以很快传播到 Javascript。

无论如何——这就是我希望带着这个东西去的地方——现在时间不允许。在它变成漫长的时间浪费之前，它需要被拍摄。我当然希望听到更好的想法(或者更好的；你已经做过了)。

**更多的远程 Javascript**
整理完毕，给[杰森](http://blog.casey-sweat.us/index.php?p=20)更多的东西看；

–[用于脚本服务器端代理的 XML](http://xmljs.sourceforge.net/website/documentation-xmlproxies.html)；script src 属性的更多技巧和非常干净的实现(非常酷的 Javascript 库的一部分)。

–[JSRS](http://www.ashleyit.com/rs/)；使用 Javascript 生成的 iframes。如果我理解正确的话，这很酷，因为 iframes 从来没有被添加到文档中，只是用来获取远程数据。

–[使用 Javascript、IFrames 和 PHP 进行远程脚本编写](http://www.phpbuilder.com/columns/vance20040507.php3)(文章)

–[蓝鞋 JSRS](http://www.blueshoes.org/en/javascript/jsrs/)–似乎是一个来自 JSRS 的叉链接上面使用 WDDX。

还有别的吗？`` 

## ``分享这篇文章``