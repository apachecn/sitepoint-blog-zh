# Bootstrap 3 的动态菜单生成器:项目和链接

> 原文：<https://www.sitepoint.com/dynamic-menu-builder-bootstrap-3-item-link/>

在第 1 部分中，我们制作了最终产品的原型，并编写了主菜单类，它充当菜单管理器——一个保存所有子单元(条目和链接)的容器。在这一部分，我们将构建其余的类，并演示菜单构建器的用法。

### 项目

将我们的菜单项表示为独立的对象。

创建一个名为`item.php`的新文件，并粘贴以下代码:

**item.php**

```
class Item {

	protected $manager;
	protected $id;
	protected $pid;
	protected $meta;
	protected $attributes = array();

	public    $link;

	//...
```

*   `$manager`存储对菜单管理器的引用(`Menu`对象)。这使我们能够在`Item`上下文中使用菜单管理器方法。
*   `$id`存储物品的 id。
*   `$pid`存储项目的父 id(如果有),否则将被设置为`null`。
*   `$meta`为每一项存储额外数据的数组。
*   HTML 属性的数组。
*   `$link`存储类`Link`的一个实例。

#### **_ _ 构造(经理、职位、url、属性、pid)**

初始化属性。

```
public function __construct($manager, $title, $url, $attributes = array(), $pid = 0)
{
	$this->manager     = $manager;
	$this->id          = $this->id();
	$this->pid         = $pid;
	$this->title       = $title;
	$this->attributes  = $attributes;

	// Create an object of type Link
	$this->link        = new Link($title, $url);
}
```

#### **添加(标题，选项)**

类`Item`也有一个`add()`方法(就像菜单管理器一样)。事实上，这个方法不会自己创建项目。它获取参数，给`$options`添加一个`pid`键，并调用菜单管理器的`add()`。

```
public function add($title, $options)
{
	if( !is_array($options) ) {
			$options = array('url' => $options);
		}

	$options['pid'] = $this->id;

	return $this->manager->add( $title, $options );
}
```

这使我们能够以更具语义的方式创建子项，而不是显式定义一个`pid`:

```
$menu = new Menu;

	$about = $menu->add('About', 'about');

	// We write it this way
	$about->add('What we do?', 'what-we-do');

	// instead of:
	// $menu->add('What we do?', array('url' => 'what-we-do', 'pid' => $about->get_id()));
```

#### **id()**

为项目生成唯一的 id。我们稍后使用该标识符来指代该项目。

```
protected function id()
{
	return $this->manager->length() + 1;
}
```

> 事实上,`id()`调用菜单管理器的`length()`,并将其递增 1。

#### **get_id()**

我们还需要创建一个 getter 方法来在需要时返回 id:

```
public function get_id()
{
	return $this->id;
}
```

#### **get_pid()**

项目可能有`pid`(父 id)。`pid`值可能为空或另一个项目的 id。

> `pid`设置为空的项目是根级别的项目。

我们还需要创建一个 getter 来返回条目的`pid`:

```
public function get_pid()
{
	return $this->pid;
}
```

#### **hasChildren()**

检查该项是否有任何子级。

```
public function hasChildren()
{
	return (count($this->manager->whereParent($this->id))) ? true : false;
}
```

它通过管理器调用`whereParent()`。

#### **儿童()**

获取该项的子项。

```
public function children()
{
	return $this->manager->whereParent($this->id);
}
```

#### **属性(键，值)**

获取或设置该项的属性。

```
public function attributes()
{
	$args = func_get_args();

	if(is_array($args[0])) {
		$this->attributes = array_merge($this->attributes, $args[0]);
		return $this;
	}

	elseif(isset($args[0]) && isset($args[1])) {
		$this->attributes[$args[0]] = $args[1];
		return $this;
	} 

	elseif(isset($args[0])) {
		return isset($this->attributes[$args[0]]) ? $this->attributes[$args[0]] : null;
	}

	return $this->attributes;	
}
```

如您所见，`attributes()`根据给定的参数返回不同类型的结果:

*   如果给定了`$key`和`$value`，则**设置属性**。
*   如果`$key`是一个数组，则**设置属性**的数组。
*   如果只给定了`$key`，则**得到属性**。
*   如果没有给定参数，**获取所有属性**。

#### **meta($key，$value)**

Meta 存储关于项目的额外数据。它可以是从放置顺序到所需权限的任何类型的数据。

```
public function meta()
{
	$args = func_get_args();

	if(is_array($args[0])) {
		$this->meta = array_merge($this->meta, $args[0]);
		return $this;
	}

	elseif(isset($args[0]) && isset($args[1])) {
		$this->meta[$args[0]] = $args[1];
		return $this;
	} 

	elseif(isset($args[0])) {
		return isset($this->meta[$args[0]]) ? $this->meta[$args[0]] : null;
	}

	return $this->meta;
}
```

`meta()`的工作原理与`attributes()`完全一样。

现在让我们转到`Link`类。

### 环

类`Link`是一个简单的类，由几个 getter 和 setter 方法组成。

`Link`有三个属性:

*   **文本**链接文本
*   **url** 链接 url
*   **属性**链接属性

**link.php**

```
class Link {

	public $text;
	public $url;
	public $attributes;

	//....
```

#### **_ _ 构造(文本、url、属性)**

当我们创建一个类型为`Link`的对象时，构造函数方法将参数绑定到上面列出的属性:

```
public function __construct($text, $url, $attributes = array())
{
	$this->text = $text;

	$this->url = $url;

	$this->attributes = $attributes;
}
```

#### string **get_url()**

返回链接 url。

```
public function get_url()
{
	return $this->url;
}
```

#### string **get_text()**

返回链接文本

```
public function get_text()
{
	return $this->text;
}
```

您可能会遇到这样的情况，需要在锚文本中添加或添加一些内容，比如下拉列表中的脱字符号或图形图标。为了实现这一点，我们将创建两个简单的函数。

#### **追加(内容)**

`append`向链接文本添加内容:

```
public function append($content)
{
	$this->text .= $content;

	return $this;
}
```

#### **前置(内容)**

`prepend`在链接前添加内容:

```
public function prepend($content)
{
	$this->text = $content . $this->text;

	return $this;
}
```

#### **属性(键，值)**

就像`Items`一样，如果我们能为锚定义 HTML 属性，那就太棒了。

```
public function attributes($key = null, $value = null)
{
	$args = func_get_args();

	if(is_array($args[0])) {
		$this->attributes = array_merge($this->attributes, $args[0]);
		return $this;
	}

	elseif(isset($args[0]) && isset($args[1])) {
		$this->attributes[$args[0]] = $args[1];
		return $this;
	} 

	elseif(isset($args[0])) {
		return isset($this->attributes[$args[0]]) ? $this->attributes[$args[0]] : null;
	}

	return $this->attributes;
}
```

我认为这个方法对您来说很熟悉，因为我们之前已经创建了它。

这样，我们的菜单生成器就完成了！

## 使用

我们通常为每个类定义创建一个 PHP 文件，因此，要使用我们的菜单构建器，我们需要在脚本的开头包含每个文件。

我将利用 PHP: `__autoload(string $class)`中的类自动加载特性，而不是包含所有这三个文件。这个特性有助于我们避免在每个脚本的开头写一个长长的包含列表。

> 如果你试图使用一个还没有定义的类或接口，会自动调用`__autoload()`。

`__autoload`接收类名作为参数。

我们将这样使用它:

```
function __autoload($class) {
	require_once(strtolower($class) . '.php');
}
```

将该文件命名为`autoload.php`，并将其包含在您的脚本中。

请注意，这可能不太理想。在实际项目中，您的自动加载需求将由 Composer 或框架的自动加载器负责。你可以在我们提供的 Github 链接上看到这一点——该项目在那里得到了充分的开发，并针对 Laravel 等进行了微调。

接下来，让我们创建一个菜单来测试我们的菜单生成器:

```
<?php
require_once('autoload.php');

$menu = new Menu;

$about = $menu->add('About', 'about');

// since this item has sub items we append a caret icon to the hyperlink text
$about->link->append(' <span class="caret"></span>');

// we can attach HTML attributes to the hyper-link as well
$about->link->attributes(['class' => 'link-item', 'target' => '_blank']);

// Adding an attribute to the item wrapper itself
$about->attributes('data-model', 'info');

$about->add('Who we are?', array('url' => 'who-we-are',  'class' => 'navbar-item whoweare'));
$about->add('What we do?', array('url' => 'what-we-do',  'class' => 'navbar-item whatwedo'));

$about->add('Goals', array('url' => 'goals', 'display' => false));

$menu->add('Portfolio', 'portfolio');
$menu->add('Contact',   'contact');

// we're only going to hide items with `display` set to **false**

$menu->filter( function($item){
	if( $item->meta('display') === false) {
		return false;
	}
	return true;
});

// Now we can render the menu as various HTML entities:

echo $menu->asUl( attribute('class' => 'ausomw-ul') );

//OR

echo $menu->asOl( attribute('class' => 'ausomw-ol') );

// OR

echo $menu->asDiv( attribute('class' => 'ausomw-div') );

?>
```

搞定了。

## Bootstrap 3 导航栏

最后一步是使用我们的菜单生成器来创建动态引导导航条。

首先，我们需要创建一个函数，以一种引导友好的格式填充我们的项目，因为现有的`render`方法不能为我们做到这一点。

我将这个函数命名为`bootstrapItems()`(我实在想不出更好的名字了，请随意命名)。

你可以把这个函数放在任何你喜欢的文件中，只要它是在应用程序启动时加载的。或者，您可以扩展类`Menu`并将该方法添加到该类中。在这个例子中，我将它放在`autoloader.php`(作为一个帮助函数)中，以确保它总是对我可用。

```
function bootstrapItems($items) {

	// Starting from items at root level
	if( !is_array($items) ) {
		$items = $items->roots();
	}

	foreach( $items as $item ) {
	?>
		<li <?php if($item->hasChildren()): ?> class="dropdown" <?php endif ?>>
		<a href="<?php echo $item->link->get_url() ?>" <?php if($item->hasChildren()): ?> class="dropdown-toggle" data-toggle="dropdown" <?php endif ?>>
		 <?php echo $item->link->get_text() ?> <?php if($item->hasChildren()): ?> <b class="caret"></b> <?php endif ?></a>
		<?php if($item->hasChildren()): ?>
		<ul class="dropdown-menu">
		<?php bootstrapItems( $item->children() ) ?>
		</ul> 
		<?php endif ?>
		</li>
	<?php
	}
}
```

因为这只是出于教育目的，我没有在这里使用模板引擎，因为它超出了本教程的范围。您可以使用自己选择的模板引擎将逻辑与表示分离开来，使您的代码更具可读性。

让我们看看`BootstrapItems`在幕后做些什么。

首先，它检查给定的参数是否是一个数组。如果不是，它获取在**根**层的项目并遍历它们。在每次迭代中，它还检查当前元素是否有任何子元素，如果元素有子元素，它将调用自己，将元素的子元素作为参数传递。重复该过程，直到将所有项目渲染到最深层。

好了，现在我们能够以引导友好的格式生成项目，让我们注册一些项目:

```
<?php
require_once('autoload.php');
// $menu #1
$main = new Menu;

$main->add('<span class="glyphicon glyphicon-home"></span>', '');
$about = $main->add('about', 'about');
   $about->add('Who we are?', 'who-we-are?');
   $about->add('What we do?', 'what-we-do?');
$main->add('Services', 'services');
$main->add('Portfolio', 'portfolio');
$main->add('Contact', 'contact');

// menu #2
$user = new Menu;

$user->add('login', 'login');
$profile = $user->add('Profile', 'profile');
  $profile->add('Account', 'account')
          ->link->prepend('<span class="glyphicon glyphicon-user"></span> ');

  $profile->add('Settings', 'settings')
          ->link->prepend('<span class="glyphicon glyphicon-cog"></span> ');

?>
```

这是我们的样板代码:

```
<nav class="navbar navbar-default" role="navigation">
  <div class="container-fluid">
    <!-- Brand and toggle get grouped for better mobile display -->
    <div class="navbar-header">
      <button type="button" class="navbar-toggle" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1">
        <span class="sr-only">Toggle navigation</span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
      </button>
      <a class="navbar-brand" href="#">Sitepoint</a>
    </div>

    <!-- Collect the nav links, forms, and other content for toggling -->
    <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
      <ul class="nav navbar-nav">
        <?php echo bootstrapItems($main); ?>
      </ul>
      <form class="navbar-form navbar-left" role="search">
        <div class="form-group">
          <input type="text" class="form-control" placeholder="Search">
        </div>
        <button type="submit" class="btn btn-default">Submit</button>
      </form>
      <ul class="nav navbar-nav navbar-right">
         	<?php echo bootstrapItems($user); ?>
      </ul>
    </div><!-- /.navbar-collapse -->
  </div><!-- /.container-fluid -->
</nav>
```

因为我们有两个不同的菜单，所以我们在引导模板中调用了两次`BootstrapItems`。

在测试结果之前，不要忘记在页面中加载`jquery`和`bootstrap` CSS 和 JS 文件！

## 结论

为了灵活性，我们在三个类定义中实现了菜单管理器、项目和链接。我们还在每个项目中存储了对经理的引用。此引用允许我们从项目上下文中访问经理。

只要使用正确的方法，你可以用任何你喜欢的方式使用这个菜单生成器。

如果你正在使用 Laravel 4，你可以得到 [laravel-menu](https://github.com/sitepoint-examples/laravel-menu) ，它是基于本教程中描述的方法实现的，同时提供更多的功能，否则，请参见我们构建的菜单生成器的完整代码: [fleximenu](https://github.com/sitepoint-examples/fleximenu) 。

编码快乐！

## 分享这篇文章