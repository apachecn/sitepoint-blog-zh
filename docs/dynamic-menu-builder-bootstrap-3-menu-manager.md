# Bootstrap 3 的动态菜单生成器:菜单管理器

> 原文：<https://www.sitepoint.com/dynamic-menu-builder-bootstrap-3-menu-manager/>

使用 Twitter Bootstrap 创建菜单和导航栏从未如此简单。我们可以轻松地创建时尚的导航栏，而不需要太多的努力。虽然这对于某些项目来说已经足够了，但是您可能会遇到需要对菜单中的链接和项目进行更多控制的情况。假设您需要从数据库表中加载项目。如果您需要将项目限制在拥有所需权限的一组用户中，该怎么办？
这就是静态解决方案无法提供太多帮助的地方，因此，我们应该考虑更动态的方法。

在本教程中，我将向你展示如何用 PHP 创建你自己的动态菜单生成器。这是一个由两部分组成的系列，第一部分关注演示代码和菜单类，第二部分关注其他类和使用示例。

## 定义目标

在我们开始之前，让我们简要概述一下我们需要完成的任务。就我个人而言，我喜欢少写多得，我相信你也一样。

我们应该能够以简单的方式创建菜单，但代码应该保持干净和专业，以现代面向对象的风格编写，就像这样:

```
//create the menu
$menu = new Menu;

//Add some items
$menu->add('Home', '');
$menu->add('About', 'about');
$menu->add('Services', 'services');
$menu->add('Portfolio', 'portfolio');
$menu->add('contact', 'contact');
```

我们应该能够以语义的方式添加子项，而不是为每个子项明确定义一个`pid`:

```
//...
$about = $menu->about('about', 'About');
    $about->add('Who we are?', 'who-we-are');
    $about->add('What we do?', 'what-we-do');
//...
```

我们应该提供一种向项目添加 HTML 属性的方法:

```
//...
$menu->add('About', array('url' => 'about', 'class' => 'about-li active', 'id' => 'about-li'));
//...
```

如果我们可以轻松地在锚点上附加或预先添加一些内容，比如插入符号或图形图标，这不是很棒吗？

```
//...
$about = $menu->add('About', array('url' => 'about', 'class' => 'about-li active', 'id' => 'about-li'));
$about->link->append('<b class="caret"></b>')
            ->prepend('<span class="glyphicon glyphicon-user"></span>');
//...
```

我们还应该提供一种过滤项目的方法:

```
$menu = new Menu;

$menu->add('Home', '');
$menu->add('About', 'about');
$menu->add('Services', 'services');
$menu->add('Portfolio', 'portfolio');
$menu->add('contact', 'contact');

$menu->filter( function($item){
    if( statement ){
        return true;
    }
    return false;    
});
```

菜单应该呈现为 HTML 实体，如列表、div 或我们需要的任何样板代码:

```
//...
    // render the menu as an unordered list
    echo $menu->asUl();

    // render menu as an ordered list
    echo $menu->asOl();

    // render menu as an ordered list
    echo $menu->asDiv();

    //...
```

你觉得我们能成功吗？

## 创建菜单生成器

我将把我们的菜单构建器分成三个独立的组件:

*   **菜单管理器**管理菜单项。它创建、修改和呈现项目。
*   **项**将菜单项表示为对象。它存储每个项目的标题、链接、属性和额外数据。
*   **链接**将链接表示为对象。

我们将在三个独立的类定义中实现这些组件:`Menu`、`Item`和`Link`。

这些是我们将要创建的方法，并简要说明了它们的作用和返回结果:

菜单

*   `Item add(string $title, mixed $options)`添加一个项目。
*   `array roots()`返回根级别的项目(没有父级的项目)。
*   `array whereParent(int $parent)`返回具有给定父 id 的项目。
*   `Menu filter(callable $closure)`通过用户传递的调用过滤项目。
*   `string render(string $type)`以 HTML 格式呈现菜单项。
*   `string getUrl(array $options)`从用户`$options`处提取 URL。
*   `array extractAttr(array $options)`从用户`$options`提取有效的 HTML 属性。
*   `string parseAttr(array $attributes)`生成一串 key="value "对(用空格分隔)。
*   `int length()`统计菜单中的所有项目。
*   将菜单呈现为无序列表。
*   将菜单呈现为有序列表。
*   `string asDiv(array $attributes)`将菜单呈现为 HTML。

**项目**

*   `Item add(string $title, mixed $options)`增加一个子项。
*   `int id()`为物品生成唯一的 Id。
*   `int get_id()`返回项目的 Id。
*   `int get_pid()`返回项目的`pid`(父 Id)。
*   `boolean hasChilderen()`检查该项目是否有子项目。
*   `array childeren()`返回项目的子项目。
*   `mixed attributes(string $key [, string $value])`设置或获取项目的属性。
*   `mixed meta(string $key [, string $value])`设置或获取项目的元数据。

**链接**

*   `string get_url()`返回链接 URL。
*   `string get_text()`返回链接文本。
*   `Link prepend(string $content)`在链接文本的开头插入内容。
*   `Link append(string $content)`向链接文本追加内容。
*   设置或获取链接的属性。

好了，现在我们有了纸上的原型，我们可以开始建造它们了。当您在项目中不可避免地使用我们的最终产品时，您也可以使用上面的列表作为快速文档备忘单。

### 菜单

运行您最喜欢的文本编辑器，创建一个新文件并命名为`menu.php`。

**menu.php**

```
class Menu {

	protected $menu = array();
	protected $reserved = array('pid', 'url');

	//...

}
```

`Menu`有两个受保护的属性:

*   `$menu`包含类型为`Item`的对象。
*   `$reserved`包含保留选项。

`$menu`存储注册项目的数组。

好吧，`$reserved`呢？到底是什么？

当用户注册一个项目时，他/她通过`add`方法传递一组选项。有些选项是 HTML 属性，有些只是包含我们在类中需要的信息。

```
//...
	$menu->add('About', array('url' = 'about', 'class' => 'item'));
   //...
```

在这个例子中，`class`是一个 HTML 属性，但是`url`是一个保留键。
我们这样做是为了区分 HTML 属性和其他数据。

#### **添加(标题，选项)**

此方法创建一个项目:

```
public function add($title, $options)
{
	$url  = $this->getUrl($options);
	$pid  = ( isset($options['pid']) ) ? $options['pid'] : null;
	$attr = ( is_array($options) ) ? $this->extractAttr($options) : array();

	$item = new Item($this, $title, $url, $attr, $pid);

	array_push($this->menu, $item);

	return $item;
}
```

`add()`接受两个参数，第一个是**标题**，第二个是**选项**。

**选项**可以是一个简单的字符串或选项的关联数组。
如果`options`只是一个字符串，`add()`假设用户想要定义 URL，没有任何其他选项。

要创建一个`Item`，我们需要提供以下数据:

*   `$title`物品的标题
*   `$url`物品的链接网址
*   `$pid`项的`pid`(如果是子项)
*   `$attr`项目的 HTML 属性
*   `$manager`对`Menu`对象的引用(菜单管理器)

`$url`是从我们稍后要创建的`getUrl()`方法中获得的；所以不管它是如何工作的，让我们只获取 URL。

接下来，我们将检查`$options`是否包含键`pid`，如果包含，我们将它存储在`$pid`中。

我们还为每个条目存储了一个对`Menu`对象的引用。这个引用允许我们在`Item`上下文中使用菜单管理器的方法(我们将在后面讨论)。

最后，我们创建一个条目，将它推送到`$menu`数组并返回`Item`。

我们走吧！我们创造了第一个方法。

#### **根()**

通过调用我们稍后将创建的方法`whereParent(parent)`在根级别获取项目。

```
public function roots() 
{
	return $this->whereParent();
}
```

#### **whereParent(父母)**

返回给定级别的项目。

```
public function whereParent($parent = null)
{
	return array_filter($this->menu, function($item) use ($parent){

		if( $item->get_pid() == $parent ) {

			return true;
		}

		return false;
	});
}
```

在`whereParent()`中，我们利用了 PHP 在`$menu`数组上的内置函数`array_filter`。

> 如果不带参数调用该方法，它将返回在根级别没有父含义的项。这就是为什么 **roots()** 不带参数调用 **whereParent()** ！

#### **过滤(回调)**

根据用户提供的可调用项筛选项目。

```
public function filter($closure)
{
	if( is_callable($closure) ) {

		$this->menu = array_filter($this->menu, $closure);

	}

	return $this;
}
```

这不是很简单吗？它只接受一个 callable 作为参数，并在`$menu`数组上运行 PHP 的 array_filter。
`closure`取决于你喜欢如何限制你的菜单项。稍后我将向您展示如何使用它。

#### **渲染(类型，pid)**

该方法以 HTML 格式呈现项目。

```
public function render($type = 'ul', $pid = null)
{
	$items = '';

	$element = ( in_array($type, ['ul', 'ol']) ) ? 'li' : $type;

	foreach ($this->whereParent($pid) as $item)
	{
		$items .= "<{$element}{$this->parseAttr($item->attributes())}>";                  $items .= $item->link();

		if( $item->hasChildren() ) {

			$items .= "<{$type}>";

			$items .= $this->render($type, $item->get_id());

			$items .= "</{$type}>";
		}

		$items .= "</{$element}>";
	}

	return $items;
}
```

`$element`是将要包装每个项目的 HTML 元素。
你可能会问，那么我们经过`render()`的`$type`是什么？

在回答这个问题之前，让我们看看项目是如何呈现为 HTML 代码的:

如果我们用`ul`作为`$type`调用`render()`，输出将是:

```
<ul>
	<li><a href="about">About</a></li>
	<li><a href="services">Services</a></li>
	<li><a href="portfolio">Portfolio</a></li>
	<li><a href="contact">Contact</a></li>
</ul>
```

或者如果我们用`div`作为`$type`来调用它:

```
<div>
	<div><a href="about">About</a></div>
	<div><a href="services">Services</a></div>
	<div><a href="portfolio">Portfolio</a></div>
	<div><a href="contact">Contact</a></div>
</div>
```

一些 HTML 元素是一组类似列表的父/子标签。
所以当我调用`render('ul')`时，我希望它将每一项都包装在一个`<li>`标签中。

```
$element = ( in_array($type, ['ul', 'ol']) ) ? 'li' : $type;
```

该行检查`$type`是否在`array('ul', 'ol')`中；如果是，则返回`li`，否则返回`$type`。

接下来，我们用给定的`$parent`迭代`$menu`元素。如果`$parent`为空，将从根级别的项目开始。

在每一次迭代中，我们检查这个条目是否有任何子条目。如果是，该方法将调用自身来呈现子级和子级的子级。

在我们的菜单构建器中，我们有几个充当助手的方法:

#### **getUrl(选项)**

从`$options`中提取 URL。

```
public function getUrl($options)
{
	if( ! is_array($options) ) {
			return $options;
		} 

		elseif ( isset($options['url']) ) {
			return $options['url'];
		} 

		return null;
}
```

你可能会问为什么我们首先需要这样一个函数？用户注册物品时不提供`url`吗？

你说得对！但是如前所述，我们有两种方法来定义 URL:

```
...
	// This is the quick way
	$menu->add('About', 'about');

	// OR

	$menu->add('About', array('url' => 'about'));
	...
```

如果它只是一个简单的字符串，那么`getUrl()`会返回`$options`本身，如果它是一个数组，那么它会查找关键字“url”并返回值。

#### **extractAttr(options)**

`extractAttr`获取`$options`数组并从中提取 HTML 属性。

```
public function extractAttr($options)
{
	return array_diff_key($options, array_flip($this->reserved));
}
```

如前所述，`$options`中的一些键是 HTML 属性，一些由类方法使用。例如`class`和`id`是 HTML 属性，但是`url`是我们在类内部使用的选项。

如前所述，我们的菜单管理器有一个名为`$reserved`的属性。我们将类似于`url`的类选项存储在`$reserved`数组中，以区别于 HTML 属性。

在`extractAttr()`中，我们使用了`array_diff_key()`来排除保留选项。

`array_diff_key()`将来自`$options`的密钥与来自`$reserved`的密钥进行比较，并返回差值。

结果将是一个包含来自`$options`的所有条目的数组，这些条目在`$reserved`中不可用。这就是我们如何从有效的 HTML 属性中排除保留键。

哦，等一下，`array_diff_keys()`使用键进行比较，但是`$reserved`有数字键:

```
protected $reserved = array('url', 'pid');

/*
 $reserved = array(0 => 'url', 1 => 'pid');
*/
```

你是正确的热心读者！这就是`array_flip()`来救援的时候了！

`array_flip()`交换数组中所有键及其相关值。所以`$reserved`会改成这样:

```
[ 'url' => 0, 'pid' => 1 ]
```

#### **parseAttr(属性)**

项目属性应该采用 property=value 格式，以便在 HTML 标记中使用。
`parseAttr`获取一个属性的关联数组，并将其转换成一个 property=value 对的字符串。

```
public function parseAttr($attributes)
{
    $html = array();
    foreach ( $attributes as $key => $value)
    {
        if (is_numeric($key)) {
        	$key = $value;
        }	

	    $element = (!is_null($value)) ? $key . '="' . $value . '"' : null;

        if (!is_null($element)) $html[] = $element;
    }

    return count($html) > 0 ? ' ' . implode(' ', $html) : '';
}
```

`parseAttr`遍历属性以生成字符串。在每次迭代中，我们检查当前元素是否有一个数字键；如果是这样，我们将假设键和值是相同的。

例如:

```
['class' => 'navbar item active', 'id' => 'navbar', 'data-show']
```

将被转换为:

```
class="navbar item active" id="navbar" data-show="data-show"
```

该方法将每一对推入一个数组，然后我们用一个空格将它们内爆，并将结果返回给调用者。

#### **长度()**

统计所有登记的项目:

```
public function length() 
{
	return count($this->menu);
}
```

#### **asUl(属性)**

`asUl`调用`render()`并将结果放入一个`<ul>`标签中。
它还接收一个属性数组，以防您需要向`<ul>`本身添加一些 HTML 属性。

```
public function asUl($attributes = array())
{
	return "<ul{$this->parseAttr($attributes)}>{$this->render('ul')}</ul>";
}
```

其他两种方法的工作方式与`asUl()`类似:

#### **asOl(属性)**

`asOl`调用`render()`并将结果放入一个`<ol>`标签中。
它还接收一个属性数组，以防您需要向`<ol>`本身添加一些 HTML 属性。

```
public function asOl($attributes = array())
{
	return "<ol{$this->parseAttr($attributes)}>{$this->render('ol')}</ol>";
}
```

#### **asDiv(属性)**

`asDiv`调用`render()`并将结果放入一个`<div>`标签中。
它还获得了一个属性数组，以防您需要向`<div>`本身添加 som HTML 属性。

```
public function asDiv($attributes = array())
{
	return "<div{$this->parseAttr($attributes)}>{$this->render('div')}</div>";
}
```

## 包扎

在这一部分，我们构建了菜单管理器——构建菜单的主类，它包含了我们接下来要构建的类的所有实例。你可以在这里看到所有类的完整源代码，或者等待明天发布的第 2 部分。

## 分享这篇文章