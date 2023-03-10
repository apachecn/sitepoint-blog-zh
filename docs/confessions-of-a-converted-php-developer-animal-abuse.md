# 一个改头换面的 PHP 开发者的自白:虐待动物

> 原文：<https://www.sitepoint.com/confessions-of-a-converted-php-developer-animal-abuse/>

有时，您正在使用的一个库或一组类的功能对于工作来说是 99%完美的，但是最后的 1%需要修改代码中的一些核心假设。修改代码会导致维护上的挫折，而扩展代码会立刻导致维护上的挫折，但是 Ruby 为我们提供了一种灵活的方式来修改代码，而损害更小。

在转换到 Ruby 的过程中，我意识到 PHP 的类层次结构有多严格。这让我很惊讶，因为我一直认为 PHP 是一种非常松散的语言，主要是因为它松散的类型和缺乏正式的代码结构指南。在我看来，后者本质上是由于许多开发人员学习语言的方式。我发现大多数 PHP 开发人员都是从使用内联 PHP 作为动态 web 语言的入门起点开始学习的，然后再学习更全面、更复杂的应用程序。

使用 PHP，您的类层次结构在很大程度上是线性发展的，偶尔会从侧面注入。我的意思是，你将从一个类开始，可能声明为抽象的，然后一个接一个地扩展，直到一个最终的类，在这个过程中，你可能会实现一些接口(这是从侧面注入的)。这是一个非常直接的结构，没有任何方法可以动态地改变这个过程。

Ruby 提供了相同的扩展继承结构，但是它也允许你用各种不那么严格的方式来改变类——就像我们在过去看到的 mixin 结构一样。但是 Ruby 走得更远，允许你重新声明类。如果你在 PHP 中这样做，你会受到相当严厉的指责。

```
<?PHP
class foo
{
	public function hello_world()
	{
		echo "Hi";
	}
}

#somewhere else in my codebase
class foo
{
	public function hello_world()
	{
		echo "Hello world!";
	}
}
```

首先，我用一个`hello_world`方法创建了一个名为 foo 的类。但是在我代码的其他地方，我想改变我的 foo 类和它的`hello_world`方法来输出稍微不同的消息。不幸的是，上面的代码将输出`PHP Fatal error: Cannot redeclare class foo`。不太好。为了获得这种行为，我们需要扩展我们的类，并且在这样做的时候，引入更多的复杂性。

```
<?PHP
class bar extends foo
{
	public function hello_world()
	{
		echo "Hello world!";
	}
}
```

瞧，我们现在有了一个名为 bar 的类，它有正确的`hello_world`方法。这是完美的是吗？不，不是的。

这种编码风格的问题是，为了达到我们想要的结果，我们必须创建一个全新的类来使用。如果我们已经有了一个使用 foo 类的代码库，我们就陷入了困境，不得不在整个代码库中寻找/替换。虽然这对于小代码库来说可能是微不足道的，但对于更大的代码库来说，这可能会变成一个相当大的练习。让我们来看一个常见的请求/响应场景——忽略这样一个事实:这段代码调用的函数显然不存在，即使存在也不会工作！

```
<?PHP
class API_Request
{
	public function get($resource)
	{
		$content = magically_get_resource_content($resource);
		$status_code = magically_get_resource_status_code($resource);
		return	$this-&gt;_build_response($content, $status_code);
	}

	protected function _build_response($content, $code)
	{
		return new API_Response($content, $code);
	}
}

class API_Response
{
	protected $_response;
	protected $_status_code;
	public function __construct($content, $status_code)
	{
		$this-&gt;_response = json_decode($content);
		$this-&gt;_status_code = $status_code;
	}

	public function is_successful()
	{
		return $this-&gt;_status_code == 200;
	}
}
```

这里我们可以用一个 URL 资源字符串调用`API_Request`上的`get`，并用一个`is_successful`方法取回一个`API_Response`的实例。这个方法将告诉我们 http 请求的状态代码是否是 200。现在，假设我们想要改变`API_Response`类的功能以匹配 HTTP/1.1 的状态代码定义，并告诉我们任何 200 的状态代码都是成功的。为此，我们必须编辑底层的`API_Response`类，或者扩展它，并扩展`API_Request`类来调用新扩展的`API_Response`类。对于这样一个简单的改变，所有这些都是大量的工作，特别是当有可能必须在整个项目中更新使用这些类的代码时。

在这里，我将这样做，用`API_Better_Response`代替`API_Response`，用`API_Better_Request`代替`API_Request`。

```
<?PHP
class API_Better_Response
{
	public function is_successful()
	{
		return $this-&gt;_status_code &gt;= 200 &amp;&amp; $this-&gt;_status_code &lt;= 299;
	}
}

class API_Better_Request
{
	protected function _build_response($content, $code)
	{
		return new API_Better_Response($content, $code);
	}
}
```

现在我们已经覆盖了`_build_response`方法以正确返回`API_Better_Response`类，但是我们还必须检查我们的应用程序代码，找到`API_Request`的所有实例并用`API_Better_Request`替换它。

## 红宝石拯救:猴子补丁

Ruby 能够在任何地方重新打开一个类，并覆盖或添加方法。这是一个非常简单的过程——您只需再次声明该类。让我们看看 Ruby 中等价的请求和响应类——再次忽略不存在的神秘代码。

```
module API
	class Request
		def get(resource)
			content = magically_get_resource_content(resource)
			status_code = magically_get_resource_status_code(resource)
			API::Response.new(content, status_code)
		end
	end

	class Response
		def initialize(content, status_code)
			@response, @status_code = ActiveSupport::JSON.decode(content), status_code
		end

		def is_successful
			@status_code == 200
		end
	end
end
```

这里我们得到了相同的代码，使用了相同的不正确的`is_successful`方法。我们不需要像在 PHP 中那样做任何烦人的类扩展和修改，我们可以简单地重新打开类并改变方法来做我们真正想让它做的事情。

```
module API
	class Response
		def is_successful
			(200...300) === @status_code
		end
	end
end
```

现在，如果返回任何 2xx http 状态代码，那么`is_successful`将返回 true。我们问的是范围 200-300(不包括 300)是否大致等于请求的状态代码。请注意，我们没有触及任何其他方法，它们仍然是原来的形式——我们只是重写了`is_successful`方法。

这种类型的动态类变更通常被称为猴子补丁，或者，根据维基百科，鸭子打孔。显然命名是以[动物][过去分词动词]的形式。你可以在维基百科上阅读[这个名字的来源。](http://en.wikipedia.org/wiki/Monkey_patch)

我们已经看到，Ruby 允许在运行时简单灵活地动态修改类，这非常有用。您还可以创建多个 monkey 补丁，但是只有对补丁后的代码的调用才会受到影响。

我必须指出，猴子补丁通常是一种快速解决方案，可能会给未来的开发人员(或您自己，如果您的记忆和我一样)带来麻烦，原因如下:

*   期望在原始文件中的类逻辑，不再在期望的地方——并且很难追踪。
*   如果基础库或代码被更新，您修补的方法被移除，或者它们的行为被改变，那么您修补的代码可能不再工作。

我猜我在这里想说的是:使用风险自担！你可能会发现，只要稍加思考和重构，你就能以一种更简洁的方式实现同样的结果，并让未来的*你*或未来的*其他开发人员*免于一路上的一些麻烦。

你在代码中使用猴子补丁吗？不惜一切代价避免？欢迎在下面评论你的理由。

## 分享这篇文章