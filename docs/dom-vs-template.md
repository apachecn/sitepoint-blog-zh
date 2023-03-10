# DOM 与模板

> 原文：<https://www.sitepoint.com/dom-vs-template/>

Fredrik Holmströ最近发布了一个基于 DOM 操作的小型模板引擎。虽然肯定有很多模板引擎，但我觉得这种方法很有趣。概念足够简单；模板被解析成一个对象模型( [DOM](http://en.wikipedia.org/wiki/Document_Object_Model) )，然后可以通过 PHP 代码给它们赋值。与传统模板引擎(如 [Smarty](http://www.smarty.net/) )的主要区别在于，模板本身没有任何强制性。事实上，模板甚至不需要写入模板引擎就可以使用——任何标记都可以用作源。

因为模板不能包含任何视图逻辑，所以它被放在一个单独的地方(在 PHP 代码中)。这使得表示和逻辑之间的分离无懈可击，这是模板引擎的主要思想。另一个好处是，因为没有字符串级别的操作，所以几乎不可能无意中出现注入类型的安全漏洞。

模板可能不知道视图逻辑，但相反的就不能说了。为了将值绑定到模板，视图逻辑需要知道模板的内部结构。这意味着如果模板改变，视图逻辑也必须改变。为了分离这种依赖性，我们需要某种抽象。

幸运的是，恰好有一个非常方便的机制来解决这个问题；元素 *id* 可以用来寻址标记中的中心节点。然而，它们有一个相当恼人的限制(对于这种用途)，[，即它们必须对文档](https://www.w3.org/TR/REC-html40/struct/global.html#adef-id)是全局唯一的。一个更好的选择是使用类(HTML 属性——我不是在说 PHP 类)来处理元素。

使用类的真正好处是它对标记来说非常不显眼。我们必须添加类，但是由于它们必须放在标记的中心元素上，它们将成为重用 CSS 规则和 Javascript 代码的修复点的主要候选对象。它们不是 HTML 代码中多余的标记，而是积极地帮助编写更好的标记。

这在理论上听起来不错，所以为了看看它在现实中如何坚持，我一起制作了一个小原型。即使使用非常有限的 API，它也有非常好的表现力:

#### 简单变量绑定

```
 $t = new Domling('<p class="hello"></p>');
$t->capture('hello')->bind("Hello World");
echo $t->render(); 
```

```
 <p class="hello">Hello World</p> 
```

#### 切换出一个块

```
 $t = new Domling('<p>Lorem Ipsum</p><p class="message">Hidden message</p>');
$t->capture('message');
echo $t->render(); 
```

```
 <p>Lorem Ipsum</p> 
```

#### 把它放回去

```
 $t = new Domling('<p>Lorem Ipsum</p><p class="message">Hidden message</p>');
$block = $t->capture('message');
$block->bind();
echo $t->render(); 
```

```
 <p>Lorem Ipsum</p>
<p class="message">Hidden message</p> 
```

#### 并且在一个块上循环

```
 $t = new Domling('<ul class="links"><li class="link"><a class="anchor" href="#">title</a></li></ul>');
$links = array(
  'Sitepoint' => 'http://www.sitepoint.com',
  'Example' => 'http://www.example.org?foo=bar&ding=dong');
foreach ($links as $title => $link) {
  $t->sequence('link', 'links')->bind(array('anchor:href' => $link, 'anchor' => $title));
}
echo $t->render(); 
```

```
 <ul class="links">
<li class="link"><a class="anchor" href="http://www.sitepoint.com">Sitepoint</a></li>
<li class="link"><a class="anchor" href="http://www.example.org?foo=bar&amp;ding=dong">Example</a></li>
</ul> 
```

如果你很好奇，你可以从这里获得上面例子的完整源代码:[http://php.pastebin.com/f76ba8d70](http://php.pastebin.com/f76ba8d70)

但是请记住，这只是一个概念验证；在将它用于生产之前，可能有一些问题需要解决。

## 分享这篇文章