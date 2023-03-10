# WordPress 的自定义简码

> 原文：<https://www.sitepoint.com/custom-shortcodes-for-wordpress/>

![WordPress Custom Shortcodes](img/b14d7ba5becda89f848ded7277f2ac48.png)

## 什么是短码？

Shortcodes 是 WordPress 实现的一种方便的方法，它提供了一种从很少的输入中生成动态且复杂的功能的方法。

虽然开发者经常创建功能来为网站提供附加特征；它们通过调用 PHP 代码来执行。然而，用户通常不是开发人员，因此不建议让他们直接与 PHP 交互。解决这一问题的方法是让开发人员创建自定义的短代码。

短代码为用户提供了一定程度的动态内容，允许他们通过使用类似于宏的伪代码在其网站上创建强大的功能。调用时，shortcode 将获取其参数(如果有),然后运行一个函数，该函数定义了将出现的自定义功能。

例如，你可能有一个基本的短代码，它包裹在你的链接周围来创建一个漂亮的按钮:`[pretty_button] Read More! [/pretty_button]`

## 谁创建短代码？

短码必须在您的网站上注册才能使用。有几种方法可以将短代码添加到你的网站上，这里有一些常用的方法。

### WordPress 自己添加的

WordPress 实际上附带了一系列已经定义好的短代码。这些短代码通常用于图库、字幕、音频和视频元素，并且大多与 WordPress 的媒体方面相关。

例如，您可能希望在页面上显示嵌入式视频播放器。通过使用如下的视频短码 `[video src="video-source.mp4"]`，你可以定义一个视频播放器将被输出到你的页面/帖子上。

WordPress codex 页面上记录了几个短代码

### 嵌入主题中

很多主题开发者会提供一系列的短代码，你可以用它们来给你的网站增加很多额外的功能。这些短代码可以创建交互式元素，以视觉上令人惊叹的方式显示内容，或者非常具有信息性。

当购买主题或使用流行的免费主题时，开发人员通常会在他们的网站上提供一个页面，概述他们包含的短代码(以及如何使用它们和示例)。此外，主题甚至可能在内容编辑器上包含一个 shortcode 按钮(这允许一个简单的方法来插入您的 shortcode)

然而，一个主题也可能根本没有额外的短代码。

### 附带插件

大多数提供某种内容(比如表单、滑块、交互元素)的插件都有一个短代码，你可以根据具体情况进行配置(在所需页面的短代码中添加选项)，也可以全局配置(比如在插件主题菜单中)。

这些插件短代码通常在开发者网站或 WordPress 的插件库中有很好的文档记录，让你立刻知道你可以用你的短代码做什么。

例如，你可能想在一个 WooCommerce 支持的网站上展示你的产品购物车，你可以通过使用他们的购物车短代码`[woocommerce_cart]`来实现。[这些短代码在它们的短代码文档页上有概述。](http://docs.woothemes.com/document/woocommerce-shortcodes/)

## 短码的类型

现在你知道了什么是短代码以及如何将它们添加到你的网站中，我们可以讨论两种不同类型的短代码以及它们之间的区别。

### 非封闭式短代码

非封闭式短代码是不包含或不包装其他内容的短代码。这些短码作为一个单独的自封闭单元存在。

例如，假设您想在帖子中显示一个图库，您可以使用`[gallery]`短代码。这个短代码将执行并显示一个将在页面上输出的图库。

看看短码是如何作为一个单元存在的，这是一个非封闭的短码。这些类型的短代码类似于自结束 HTML 元素(如`<br/>`标签)

### 封闭式短代码

封闭式短代码是包含其他元素或内容的短代码。这些短代码看起来类似于非封闭的短代码，但是它们有一个开始和结束元素。

例如，假设您希望页面上的一些文本突出显示。您可以定义一个 shortcode，它接受其包装的内容并应用一种设置的样式(比如使其更粗或更大)。

当应用于内容时，定义为`[big_and_bold]`的短代码可能看起来类似于这个`[big_and_bold] This is big and bold [/big_and_bold]`

注意我们有一个开始标签和一个结束标签？其间的所有内容都将被传递到短代码中，并以某种方式进行处理。

总的来说，当你处理短码时，你会处理这两种类型中的一种。

## 创建您自己的短代码

创建用于 WordPress 的简码相当简单。它涉及一个回调函数，该函数定义了短代码将做什么，并使用了`add_shortcode($name,$callback_function)`函数来注册要使用的短代码。

要注册你自己的短代码，你需要将它们放在你的子主题中的`functions.php`文件中(或者如果你有另一个可以访问 PHP 的文件，你也可以使用它)。

记住不要直接编辑父主题！

### 定义短代码回调函数

短代码执行的功能是在回调函数中定义的。

回调函数返回一个值，这个值就是在生成页面/帖子时显示给最终用户的内容。这个返回值可能只是一个包装在容器中用于样式化的值，也可能像输出一个动态表单元素或滑块一样复杂。

从最简单的意义上来说，回调函数的结构就是一个返回值的函数:

```
//defines the callback function used for our shortcode (does nothing)
function my_shortcode_callback_function(){
	return; 
}
```

#### 回调函数中的短码参数

而 shortcodes 只需要一个回调函数来操作；短代码可以接受参数(选项),这些参数可用于动态更改短代码的工作方式。

有三个主要参数可以传递给显示短代码的函数。这些变量都是可选的，但是经常使用。这些变量是`$atts`、`$content`和`$name`；它们被顺序调用到回调函数中，如下所示:

```
//defines the callback function used for our shortcode (does nothing). Takes in three arguments
function my_shortcode_callback_function($atts, $content, $name){
	return; 
}
```

#### $atts(选项)

`$atts`变量是您希望您的短代码能够访问的选项或设置的关联数组。

短代码通常会为用户提供一系列设置，用户可以将这些设置传递到短代码中，从而使他们能够更改输出。

例如，考虑内置的音频短代码`。`

 `这个短代码(从 WordPress 3.6 开始)让用户能够在页面中嵌入 HTML5 音频播放器。

默认情况下，您可以直接使用`短代码。[然而，你也可以通过传入参数](http://codex.wordpress.org/Audio_Shortcode)来定制它的选项，比如下面的`。这允许您将音频设置为页面加载时就预加载，同时自动触发音频(一旦音频结束，它将循环播放)。这些设置以及其他设置可以通过传入值来定制。``

 ``让我们看一个基本的例子，我们想要一个短代码，它接受一个选项，并根据它的选择有条件地输出内容:

```
//based on the passed in profession, return a statement 
//Shortcodes functions and information
function display_my_profession_callback($atts,$content,$tag){

	//collect values, combining passed in values and defaults
	$values = shortcode_atts(array(
		'profession_type' => 'other'
	),$atts);  

	//based on input determine what to return
	$output = '';
	if($values['profession_type'] == 'developer'){
		$output = 'You are an amazing developer!';
	}
	else if($values['profession_type'] == 'designer'){
		$output = 'You are an epic designer!';
	}
	else if($values['profession_type'] == 'other'){
		$output = 'You are a great person';
	}
	else{
		$output = 'I am not sure what you are'; 
	}

	return $output;

}
add_shortcode('display_profession','display_my_profession_callback');
```

首先你会看到我们调用了一个名为`shortcode_atts()`的函数。这个函数用于返回一个值的关联数组供您使用。这个函数接受您的`$atts`变量，还定义了您可以使用的默认选项(这消除了您自己合并数组和控制数据的麻烦)。

在我们的例子中，`$values`将访问一个名为`profession_type`的值，然而，如果我们定义了更多的默认值，我们也可以访问它们。

`profession_type`的默认值是`other`。通过简单地提供一个值，例如`[display_profession type='designer']`，这个值可以在短代码中被覆盖

一旦我们确定了`profession_type`的值，我们就格式化一个字符串并将其返回给最终用户显示。

#### $content(内容)

`$content`变量用于`enclosed shortcodes`,该值表示存在于短代码的开始和结束标签之间的任何内容。

例如，如果你有一个短代码，使选定的文本非常小，如`[small_text] This text will be small [/small_text]`。`$content`的值将是‘这个文本将是小的’。此变量为短代码提供了对内容的访问，这也是短代码如何将样式和功能直接应用到所选区域的方式。

让我们看一个编码示例来说明这一点。

我们可以创建一个短代码，它接受其包装的内容，并通过 span 标签对其进行格式化:

```
//take the wrapped around content of the shortcode and style it
function wrap_content_shortcode_callback($atts, $content, $tag){
	$output = '<span style="font-size: 120%;">' . $content . '</span>';
	return $output;
}
add_shortcode('wrap_shortcode','wrap_content_shortcode_callback');
```

返回的内容将采用不同的样式，由一个 span 包裹。

与此相关，如果您正在创建使用`$content`变量的短代码，您也可以使用`do_shortcode($content)`函数。该函数将获取内容并运行其中的任何其他短代码。如果你不包括这一点，而是将一个短代码包装在另一个短代码中，最里面的短代码将只是显示文本，而不是被转换。

#### $tag(简称)

`$tag`变量包含短代码本身的名称。这通常用于共享同一个回调函数的短代码。

当你定义多个短代码并使用同一个函数来处理它们时，你可以使用`$tag`变量来区分哪个短代码被调用。

让我们看一个实际的例子，其中一个回调函数用于两个短代码:

```
//this callback function performs many actions depending on which shortcode ($tag) calls it
function display_my_shortcode($atts, $content, $tag){

	//if called from the 'my_primary_shortcode' shortcode
	if($tag == 'my_primary_shortcode'){
		return 'This is the primary shortcode'; 
	}
	//if called from the 'my_secondary_shortcode' shortcode
	else if($tag == 'my_secondary_shortcode'){
		return 'This is the secondary shortcode'; 
	}
	//default 
	else{
		return 'This is something else'; 
	}
}
add_shortcode('my_primary_shortcode','display_my_shortcode');
add_shortcode('my_secondary_shortcode','display_my_shortcode');
```

根据您调用的短代码(或者是`[my_primary_shortcode]`或者是`[my_secondary_shortcode]`),内容会有所不同。

### 用 add_shortcode()函数注册短代码

要注册一个短代码，你需要做的就是调用`add_shortcode($name, $callback_name)`函数。

这个函数有两个参数:短代码的名称(将在编辑器中使用的名称)和返回输出(执行实际处理)的回调函数的名称

例如，请看以下内容:

```
//callback function for the 'clear_content' shortcode
function clear_content_callback_function($atts, $content, $tag){
	return '<div style="clear: both; float: left">' . $content '</div>';
}
//add the new 'clear_content' shortcode
add_shortcode('clear_content','clear_content_callback_function');
```

短代码名称本身应该全部小写，只有数字和下划线。

在短代码中使用连字符的问题由来已久，所以我会简单地定义没有连字符的短代码，但是如果你喜欢冒险，你也可以这样做。

## 最后

现在你已经彻底了解了短码是如何工作的，以及如何使用它们为非开发者提供可扩展的选项，你可以创建你自己的短码，并真正扩展你即将推出的 WordPress 网站。