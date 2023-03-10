# 一个转化的 PHP 开发人员的自白:像面包师一样混合

> 原文：<https://www.sitepoint.com/confessions-of-a-converted-php-developer-mixing-like-a-baker/>

在 PHP 中，有一件事总是让我不寒而栗，那就是无法以一种简单的方式实现泛型类混合。作为一名开发人员，避免多次编写代码的能力对我来说很重要，因为它使我的工作效率更高，所以我可以在相同的时间内完成更重要的代码，或者在保持相同代码输出的同时更加懈怠(这主要取决于我在任一时间的心情)。

如果每次我不得不写一组我知道我以前写过，但也知道无法避免的函数时，我有一美元，我会…嗯…不是真的有那么多，但至少足够支付一个晚上。请原谅我，这里有相当多的 PHP 代码。(为了便于记录，我通常会找到这些函数并复制粘贴它们。)

这是一个带有一组基本函数的视图类，这些函数允许我实现类似于 [mixin 设计模式](http://en.wikipedia.org/wiki/Mixin)的东西。

```
class HtmlView {
 	protected $_helpers = array();
	public function addHelper($obj)
  	{
 		array_push($this-&gt;_helpers, $obj);
		return $this;
	}

	public function __call($name, $params)
	{
		foreach($this-&gt;_helpers as $helper)
		{
			if(is_callable(array($helper, $name )))
				return call_user_func_array(array($helper, $name), $params);
		}

		throw new Exception('Attempted to call nonexistent method ' . $name . ' on ' . __CLASS__);
	}
}
```

我现在可以创建一个 helper 类，它包含一系列我希望能够从我的 HtmlView 中访问的方法。

```
class TagViewHelper{
	public function linkFor($url, $name)
	{
		return sprintf('<a href="%s">%s', $url, $name);
	}
	# the rest of my bunch of methods here...
}
```

我可以在实例化时将 TagViewHelper 添加到我的 HtmlView 类中，或者将其弹出到构造函数中。

```
public function __construct()
{
 	$this->addHelper(new TagViewHelper);
	# Add other view helpers here
}
```

现在，我能够从实例化的 HtmlView 对象调用 helper 类中的方法。

```
<!-- somewhere deep inside code being called by our HtmlView object--&gt;
Check out my awesome &lt;?PHP echo $this-&gt;linkFor('http://malstaxidermy.com', 'Stuffed Mongooses'); ?&gt;.
</code>
```

这将很乐意为我的毛绒猫鼬网站创建一个链接。

## 人群变得疯狂…

这是很常见的东西，而你们中的一些人可能会盯着屏幕，尖叫着“为什么不创建一个抽象的 GenericMixin 类，然后从它扩展呢？”我想先说“哇，仁慈的先生！我说的有道理！”

虽然有很多原因说明这不是一个理想的解决方案，但我只挑两个。

1.  90%的时候，我无法控制我扩展的对象。我们使用的 PHP 框架由相当多的子模块组成，这些子模块都以古怪而奇妙的方式工作，这在编辑根类时令人头疼。或者，这可能是我们正在使用的库，如果可能的话，通常您希望避免编辑外部依赖项。
2.  如果我想添加经常使用的不同功能，该怎么办？flyweight /对象缓存实现的下降？PHP 不处理多重继承，所以在这一点上我们又回到了第一点——将代码直接放在我们的类中，或者用一些荒谬的基本抽象类实现尽可能多的模式。

## 你想说什么？我是来拿红宝石的！

好消息是，像许多语言一样，Ruby 内置了对 mixins 的支持。这就像定义一个模块，然后将它包含在我们的类定义中一样简单。Ruby 中的`include`语句与 PHP 中的`include`语句完全不同。在 PHP 中，这将包括一个文件，而我们在 Ruby 中所做的是将模块中的所有方法拉入类中。

让我们在 Ruby 中重新创建我们的 HtmlView 功能。

```
module TagViewHelper
	def tag_for(url, title)
		"<a href='%s'&gt;%s&lt;/a>" % [ url, title ]
	end
end

class HtmlView
	include TagViewHelper
end
```

现在，我可以从我的 HtmlView 实例中的代码调用`tag_for(“http://malstaxidermy.com”, “Solid Snakes”)`。我从来不需要担心 Mixins 的处理，因为这是由 Ruby 处理的。

你必须承认，这比以前少了很多代码。Mixin 模块是 Ruby 的伟大特性之一，它允许我们将常见的行为整合到一组共享对象中。

您可能想知道如果有几个同名的方法会发生什么。Ruby 通过简单地用添加的最后一个方法覆盖所有以前的方法来处理这个问题。

关于 PHP 或 Ruby 中的 mixins，你有什么故事或建议吗？

注意:这是一个非常基本的例子，旨在展示如何使用 Ruby 快速轻松地在 PHP 中重建一些需要大量代码的功能。

## 分享这篇文章