# Laravel 中的数据验证:正确的方法——定制验证器

> 原文：<https://www.sitepoint.com/data-validation-laravel-right-way-custom-validators/>

在[上一部分](https://www.sitepoint.com/data-validation-laravel-right-way/)中，我们学习了如何使用内置的数据验证包在 Laravel 中验证数据，以及如何将我们的数据验证抽象为实体特定的服务，以使代码在 DRY 原则上可重用。现在，我们可以轻松地为应用程序中的每个实体创建一个验证服务，使用各自的验证规则，在我们想要验证数据的任何地方注入它，并轻松地获取和显示错误等。

但是如果我们想要更多呢？

> *本教程的源代码可在[这里](https://github.com/coolamit/Data-Validations-in-Laravel)获得。您只需要运行`composer install`在项目目录中安装 Laravel 框架，然后就可以运行这段代码了。*

### 对更多的需求

Laravel 提供了许多有用的通用验证规则。但是如果我们想要更多呢？如果我们需要更具体的东西呢？在我们的例子中，在`TestFormValidator`中，我们使用了`alpha_dash`来验证名字，但是这对于验证全名来说并不理想。一般来说，一个人的全名由名和姓组成，可能还有中间名。所有这些都要用空格隔开。类似地，如果我们想验证表单中的 Pin 码，我们不能使用 Laravel 提供的`alpha_num`规则，我们也应该允许其中有空格。

好吧，Laravel 提供了轻松扩展其验证包并向其中添加自定义验证规则的选项。在`RocketCandy/Services/Validation/`中创建`ValidatorExtended.php`,并添加以下代码:

```
<?php

namespace RocketCandy\Services\Validation;

use Illuminate\Validation\Validator as IlluminateValidator;

class ValidatorExtended extends IlluminateValidator {

	private $_custom_messages = array(
		"alpha_dash_spaces" => "The :attribute may only contain letters, spaces, and dashes.",
		"alpha_num_spaces" => "The :attribute may only contain letters, numbers, and spaces.",
	);

	public function __construct( $translator, $data, $rules, $messages = array(), $customAttributes = array() ) {
		parent::__construct( $translator, $data, $rules, $messages, $customAttributes );

		$this->_set_custom_stuff();
	}

	/**
	 * Setup any customizations etc
	 *
	 * @return void
	 */
	protected function _set_custom_stuff() {
		//setup our custom error messages
		$this->setCustomMessages( $this->_custom_messages );
	}

	/**
	 * Allow only alphabets, spaces and dashes (hyphens and underscores)
	 *
	 * @param string $attribute
	 * @param mixed $value
	 * @return bool
	 */
	protected function validateAlphaDashSpaces( $attribute, $value ) {
		return (bool) preg_match( "/^[A-Za-z\s-_]+$/", $value );
	}

	/**
	 * Allow only alphabets, numbers, and spaces
	 *
	 * @param string $attribute
	 * @param mixed $value
	 * @return bool
	 */
	protected function validateAlphaNumSpaces( $attribute, $value ) {
		return (bool) preg_match( "/^[A-Za-z0-9\s]+$/", $value );
	}

}	//end of class

//EOF
```

在这里，我们的类扩展了 Laravel 的`Illuminate\Validation\Validator`类，并添加了两个新方法(`validateAlphaDashSpaces()`和`validateAlphaNumSpaces()`)来验证数据及其各自的错误消息，其中包含占位符`:attribute`，Laravel 将在运行时用被验证的数据字段的名称替换这些占位符。

现在要注意的是我们如何命名这些方法。所有验证规则方法名称都必须有前缀`validate`,其余部分必须是大写字母(当然没有空格)。验证规则将是方法名称的小写形式(没有前缀`validation`),每个单词将由下划线分隔。因此，如果我们想添加一个`alpha_dash_spaces`验证规则，那么我们相应的方法将被命名为`validateAlphaDashSpaces()`。

所以我们在这里添加了`alpha_dash_spaces`和`alpha_num_spaces`验证规则。`alpha_dash_spaces`允许字母、破折号(连字符和下划线)和空格，而`alpha_num_spaces`只允许字母、数字(数字 0-9)和空格。

我们还没有完成这个，这个类只是扩展了 Laravel 的验证类。我们仍然需要让 Laravel 认识到这一点，这样当我们在上面的 validation 服务中添加新规则时，Laravel 就会知道如何根据这些规则运行验证。

[Laravel docs](http://laravel.com/docs/validation#custom-validation-rules) 声明我们可以这样做:

```
Validator::resolver( function( $translator, $data, $rules, $messages ) {
	return new \RocketCandy\Services\Validation\ValidatorExtended( $translator, $data, $rules, $messages );
} );
```

并把它放在`app/start/global.php`中，或者在`app`目录下创建一个新文件，然后把这个文件加载到`app/start/global.php`中。但是这看起来不那么干净，修改我们不需要修改的文件，到处粘贴一些零碎的东西。不，我们宁愿将所有这些验证相关的代码放在一起，所以我们将创建一个[服务提供者](http://laravel.com/docs/ioc#service-providers)，并将我们的自定义验证规则粘贴到 Laravel 的验证包中。

在`RocketCandy/Services/Validation/`中创建`ValidationExtensionServiceProvider.php`，并添加以下代码:

```
<?php

namespace RocketCandy\Services\Validation;

use Illuminate\Support\ServiceProvider;

class ValidationExtensionServiceProvider extends ServiceProvider {

	public function register() {}

	public function boot() {
		$this->app->validator->resolver( function( $translator, $data, $rules, $messages = array(), $customAttributes = array() ) {
			return new ValidatorExtended( $translator, $data, $rules, $messages, $customAttributes );
		} );
	}

}	//end of class

//EOF
```

如果您之前已经在 Laravel 中创建了一个服务提供者，那么您通常会使用`register()`方法来完成您需要的任何绑定。这是我们在这里扩展的抽象类`Illuminate\Support\ServiceProvider`中唯一的`abstract`方法。我们不能将我们的验证扩展粘在`register()`中的原因是，一旦服务提供者被 Laravel 加载，它就会被触发，并且当 Laravel 稍后初始化它的验证包时，我们会遇到一连串的异常，所以我们会试图使用一个不存在的对象来扩展不存在的东西。另一方面,`boot()`方法是在请求被路由之前触发的，所以我们可以安全地将我们的东西粘贴到 Laravel 的验证包中。

现在我们只需要告诉 Laravel 加载这个服务提供者，我们就一切就绪了。打开`app/config/app.php`，在`providers`数组的末尾添加以下内容:

```
'RocketCandy\Services\Validation\ValidationExtensionServiceProvider',
```

现在，打开`app/RocketCandy/Services/Validation/TestFormValidator.php`并更新`$rules`属性，使其看起来像这样:

```
public $rules = array(
		'name' => array( 'required', 'alpha_dash_spaces', 'max:200' ),
		'email' => array( 'required', 'email', 'min:6', 'max:200' ),
		'phone' => array( 'required', 'numeric', 'digits_between:8,25' ),
		'pin_code' => array( 'required', 'alpha_num_spaces', 'max:25' ),
	);
```

我们用`alpha_dash_spaces`替换了`name`的`alpha_dash`验证规则，用`alpha_num_spaces`替换了`pin_code`的`alpha_num`。

现在，如果我们导航到`http://<your-project-domain>/dummy/create`，我们可以在姓名和 Pin 码字段中输入空格，不会出现任何问题，数据将在提交时通过验证。

### 摘要

因此，在这两部分的教程中，我们学习了:

1.  使用内置的数据验证包验证 Laravel 中的数据。
2.  使用面向对象的方法将数据验证抽象为它自己单独的服务(*单一责任*成就解锁)。
3.  创建我们自己的自定义异常，用于我们的数据验证服务(而不是使用`TRUE/FALSE`布尔值),以及如何存储和从中检索错误。
4.  在我们的控制器中注入我们的数据验证服务并使用它。
5.  使用我们的自定义验证规则扩展 Laravel 的验证包，并使用服务提供商自动加载它。

### 脚注

为了让本教程切中要点，我在控制器中注入了验证服务，并在那里使用了它。在实际项目中，您最有可能在其他需要处理数据清理和存储的地方使用验证服务。理想情况下，控制器应该是无脂肪的，并且具有最少的代码。

* * *

有想法吗？有问题吗？请在评论中畅所欲言。

## 分享这篇文章