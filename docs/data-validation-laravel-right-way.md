# Laravel 中的数据验证:正确的方法

> 原文：<https://www.sitepoint.com/data-validation-laravel-right-way/>

如果一个应用是一个世界，那么数据就是它的货币。每个应用程序，不管它的目的是什么，都是在处理数据。几乎每种类型的应用都需要用户输入，这意味着它需要来自用户的一些数据，并据此采取行动。但是需要对这些数据进行验证，以确保其类型正确，并且用户(怀有恶意)不会试图破坏或破解您的应用程序。如果您正在开发一个需要用户输入的应用程序，这就是为什么您需要在对数据做任何事情之前编写代码来验证数据。

### 从前

在某个时间点，您可能会像这样进行数据验证:

```
<?php
$errors = array();

if ( empty( $_POST['name'] ) || ! is_string( $_POST['name'] ) ) {
    $errors[] = 'Name is required';
} elseif ( ! preg_match( "/^[A-Za-z\s-_]+$/", $_POST['name'] ) ) {
    $errors[] = 'Name can have only alphabets, spaces and dashes';
}

if ( empty( $_POST['email'] ) || ! is_string( $_POST['email'] ) ) {
    $errors[] = 'Email is required';
}

//...........
//.... some more code here
//...........

//display errors
if ( ! empty( $errors ) ) {
    for ( $i = 0; $i < count( $errors ); $i++ ) {
        echo '<div class="error">' . $errors[ $i ] . '</div>';
    }
}
```

对你来说那是石器时代。幸运的是，现在我们有了更好更复杂的验证包(在 PHP 中已经有一段时间了)。

如果您使用任何应用程序框架作为您的应用程序的基础，它很可能会有自己的或推荐的第三方数据验证包，特别是如果它是一个全栈框架。因为 Laravel 是一个全栈框架，所以它有自己的验证包。和往常一样，您不一定要使用它，您可以使用您想要的任何其他数据验证包。然而，出于本教程的目的，我们将坚持使用 Laravel 的默认特性。

### 数据验证:Laravel 方式

*本教程的源代码可在[这里](https://github.com/coolamit/Data-Validations-in-Laravel)获得。您只需要运行`composer install`在项目目录中安装 Laravel 框架，然后就可以运行这段代码了。*

现在继续前面的例子，假设我们有一个表单，它有一个名称和一个电子邮件字段，我们希望在保存之前验证该数据。以下是之前验证的初步尝试如何转化为 Laravel。

```
<?php
$validation = Validator::make(
    array(
        'name' => Input::get( 'name' ),
        'email' => Input::get( 'email' ),
    ),
    array(
        'name' => array( 'required', 'alpha_dash' ),
        'email' => array( 'required', 'email' ),
    )
);

if ( $validation->fails() ) {
    $errors = $validation->messages();
}

//...........
//.... some more code here
//...........

//display errors
if ( ! empty( $errors ) ) {
    foreach ( $errors->all() as $error ) {
        echo '<div class="error">' . $error . '</div>';
    }
}
```

在上面的代码中，我们通过使用验证器 [facade](http://laravel.com/docs/facades) 来验证数据验证。我们传递了一组我们想要验证的数据和一组验证规则，根据这些规则我们想要验证数据。我们得到一个返回给我们的对象，然后我们检查我们的数据验证是否失败。如果失败了，那么我们获取错误消息对象(Laravel 的 MessageBag 类的一个对象),然后遍历它，打印出所有的错误消息。我们使用的验证规则内置在验证包中，有许多可用的，有些甚至会检查数据库。

现在，经常会遇到数据验证被放在奇怪位置的代码，比如在控制器方法或数据模型中。数据验证代码不属于这两个地方，因为这样的位置违背了**单一责任**和**干**的概念(不要重复自己)。

> **单一责任:**一个类应该有且只有一个任务要执行。控制器的工作是充当业务逻辑和客户之间的粘合剂。它应该获取请求并将其传递给能够处理请求的人，而不应该自己开始处理请求。
> 
> 类似地，模型的工作是充当数据源和应用程序其余部分之间的代理。它应该只接受用于保存的数据，并在被请求时提供数据。
> 
> 关于这一点，有不止一个学派；有些人会在模型中添加数据验证，但即使是这些人也不会将实际的数据验证代码放在模型类中。它很可能被外包给另一个类，该类将接受数据并判断数据是否有效。

那么，我们把进行数据验证并且可以在应用程序的任何地方使用的代码放在哪里呢？

### 验证即服务

理想的选择是将验证代码转移到单独的类中，这些类可以在需要时使用。

继续我们之前的代码示例，让我们将它移到自己的类中。在`app`目录中创建一个名为`RocketCandy`的目录。这是我们的主目录(或域目录),我们将在其中放置所有的自定义内容(服务、异常、实用程序库等)。现在在`RocketCandy`中创建`Services/Validation`目录结构。在`Validation`目录下，创建`Validator.php`。

现在，在我们继续下一步之前，打开您的`composer.json`，在`autoload`节点中的`classmap`之后，添加用于 PSR-4 自动加载的`RocketCandy`名称空间。它看起来会像这样:

```
"autoload": {
		"classmap": [
			"app/commands",
			"app/controllers",
			"app/models",
			"app/database/migrations",
			"app/database/seeds",
			"app/tests/TestCase.php"
		],
		"psr-4": {
			"RocketCandy\\": "app/RocketCandy"
		}
	},
```

然后在您的终端中，运行`composer dump-autoload -o`，以便`composer`可以为我们的`RocketCandy`名称空间生成自动加载程序。

现在打开`RocketCandy/Services/Validation/Validator.php`。在我们从上面移动验证代码之后，它将看起来像这样:

```
<?php

namespace RocketCandy\Services\Validation;

class Validator {

    public function validate() {
        $validation = \Validator::make(
            array(
                'name' => \Input::get( 'name' ),
                'email' => \Input::get( 'email' ),
           ),
            array(
                'name' => array( 'required', 'alpha_dash' ),
                'email' => array( 'required', 'email' ),
            )
        );

        if ( $validation->fails() ) {
            return $validation->messages();
        }

        return true;
    }

}   //end of class

//EOF
```

现在我们可以用它来表示:

```
<?php

$validator = new \RocketCandy\Services\Validation\Validator;
$validation = $validator->validate();

if ( $validation !== true ) {
    //show errors
}
```

这比我们之前所做的要好一些，但仍不理想，原因如下:

1.  我们的验证课仍然不够枯燥。我们需要将所有这些验证代码复制到另一个类中，以验证另一个实体的数据。
2.  为什么我们要在验证类中获取输入数据？没有理由在那里这样做，因为它会限制我们可以验证哪些数据。只有当数据来自表单输入时，我们才能验证这些数据。
3.  没有办法覆盖验证规则，它们是固定不变的。
4.  我们用来知道数据是否有效的机制是不干净的。当然，它服务的目的，但这肯定可以改进。
5.  我们使用一个伪静态调用来调用 Laravel 的验证包。这也是可以改进的。

#### 解决方案？

我们进一步抽象出验证代码，并利用异常。

首先，让我们创建自己的自定义异常。在`RocketCandy`下创建`Exceptions`目录，并创建`BaseException.php`。打开它，把下面的代码放进去。

```
<?php

namespace RocketCandy\Exceptions;

use Exception;
use Illuminate\Support\MessageBag;

abstract class BaseException extends Exception {

	protected $_errors;

	public function __construct( $errors = null, $message = null, $code = 0, Exception $previous = null ) {
		$this->_set_errors( $errors );

		parent::__construct( $message, $code, $previous );
	}

	protected function _set_errors( $errors ) {
		if ( is_string( $errors ) ) {
			$errors = array(
				'error' => $errors,
			);
		}

		if ( is_array( $errors ) ) {
			$errors = new MessageBag( $errors );
		}

		$this->_errors = $errors;
	}

	public function get_errors() {
		return $this->_errors;
	}

}	//end of class

//EOF
```

这里我们创建了一个抽象类，所有的自定义异常都将继承这个类。构造函数的第一个参数是我们所关心的，所以让我们来看看。我们利用 Laravel 的`MessageBag`来存储我们的错误(如果它们还不在里面的话),这样我们就有一个统一的方法来遍历和显示这些错误，而不管异常是由验证服务还是其他什么引发的。因此，`_set_errors()`方法检查是传递了一个字符串形式的错误消息还是传递了一组错误消息。相应地，它将它们存储在一个`MessageBag`对象中(除非它已经在里面了，在这种情况下它将被原样存储)。我们有一个 getter 方法`get_errors()`，它只是原样返回我们的类变量的内容。

现在，在同一个目录中创建`ValidationException.php`，它的代码将是:

```
<?php

namespace RocketCandy\Exceptions;

class ValidationException extends BaseException {
}	//end of class

//EOF
```

就是这样，我们在这里不需要任何其他东西，它将是一个空壳，因为我们需要做的所有事情都将由`BaseException`来处理。

现在，我们继续重组我们的`Validator`类。我们需要抽象出验证代码，对错误抛出`ValidationException`,并允许覆盖验证规则。所以它看起来像这样:

```
<?php

namespace RocketCandy\Services\Validation;

use Illuminate\Validation\Factory as IlluminateValidator;
use RocketCandy\Exceptions\ValidationException;

/**
 * Base Validation class. All entity specific validation classes inherit
 * this class and can override any function for respective specific needs
 */
abstract class Validator {

	/**
	 * @var Illuminate\Validation\Factory
	 */
	protected $_validator;

	public function __construct( IlluminateValidator $validator ) {
		$this->_validator = $validator;
	}

	public function validate( array $data, array $rules = array(), array $custom_errors = array() ) {
		if ( empty( $rules ) && ! empty( $this->rules ) && is_array( $this->rules ) ) {
			//no rules passed to function, use the default rules defined in sub-class
			$rules = $this->rules;
		}

		//use Laravel's Validator and validate the data
		$validation = $this->_validator->make( $data, $rules, $custom_errors );

		if ( $validation->fails() ) {
			//validation failed, throw an exception
			throw new ValidationException( $validation->messages() );
		}

		//all good and shiny
		return true;
	}

} //end of class

//EOF
```

在这个抽象类中，我们有:

1.  抽象出验证码。它可以用于验证任何实体的数据。
2.  从类中移除数据提取。验证类不需要知道数据来自哪里。它接受一组数据作为参数进行验证。
3.  从此类中删除了验证规则。每个实体可以有自己的一组验证规则，或者在类中定义，或者作为数组传递给`validate()`。*如果你想在子类中定义规则，并想确保它们存在，我曾经写过在 PHP 中模拟[抽象属性。](http://blog.igeek.info/2014/abstract-properties-in-php/)*
4.  改进了确定验证失败的机制。如果数据验证失败，验证服务将抛出`ValidationException`，我们可以从中获得错误，而不是检查返回的数据类型或值等。这也意味着如果没有定义验证规则，我们可以抛出另一个异常。这将是一个不同的例外，我们会立即知道我们在某个地方搞砸了。
5.  删除了数据验证的静态调用。在这里，我们现在在类构造函数中注入 Laravel 的验证类。如果我们从 Laravel 的 IoC 容器中解析我们的验证服务(我们会这样做),那么我们就不必担心依赖注入到构造函数中。

现在我们将为表单创建一个验证类，它将扩展这个抽象类。在同一个目录中创建`TestFormValidator.php`,并在其中添加以下代码:

```
<?php

namespace RocketCandy\Services\Validation;

class TestFormValidator extends Validator {

	/**
	 * @var array Validation rules for the test form, they can contain in-built Laravel rules or our custom rules
	 */
	public $rules = array(
		'name' => array( 'required', 'alpha_dash', 'max:200' ),
		'email' => array( 'required', 'email', 'min:6', 'max:200' ),
		'phone' => array( 'required', 'numeric', 'digits_between:8,25' ),
		'pin_code' => array( 'required', 'alpha_num', 'max:25' ),
	);

}	//end of class

//EOF
```

这是我们将实例化以验证表单数据的类。我们已经在这个类中设置了验证规则，所以我们只需要在它的对象上调用`validate()`方法，并将我们的表单数据传递给它。

> **注意:**如果你没有将项目目录中的`artisan`工具设为可执行文件，那么你需要在本教程中将`artisan`命令中的`./artisan`替换为`/path/to/php artisan`。
> 
> 建议您将`artisan`设为可执行文件，这样可以省去在每个命令前加上前缀`php`的麻烦。

让我们适当地制作一个控制器和一个表单来进行一下测试。在终端中，导航到项目目录并运行

```
./artisan controller:make DummyController --only=create,store
```

它会用两种方法创建`app/controllers/DummyController.php`—`create()`和`store()`。打开`app/routes.php`并添加以下路线指令。

```
Route::resource( 'dummy', 'DummyController', array(
	'only' => array( 'create', 'store' ),
) );
```

这将设置我们的控制器以 RESTful 方式工作；`/dummy/create/`将接受`GET`个请求，而`/dummy/store/`将接受`POST`个请求。*我们可以从 route 和 artisan 命令中删除`only`指令，控制器也会接受`PUT`和`DELETE`请求，但对于当前的练习，我们不需要它们。*

现在我们需要将代码添加到控制器中，所以打开`app/controllers/DummyController.php`。它将是一个带有`create()`和`store()`方法的空壳。我们需要让`create()`渲染一个视图，所以让它像这样:

```
/**
	 * Show the form for creating a new resource.
	 *
	 * @return Response
	 */
	public function create() {
		return View::make( 'dummy/create' );
	}
```

我们现在需要在这里呈现的视图，它将呈现我们的表单。

首先让我们创建一个布局文件，我们可以把 HTML 样板代码。在`app/views`中创建`layouts`目录，并在其中创建`default.blade.php`。*注意这里视图名称的`.blade`后缀。它告诉 Laravel，这个视图使用了 Laravel 的刀片模板语法，并且应该这样解析。*打开它，向其中添加以下样板代码:

```
<!DOCTYPE html>
<html lang="en">
	<head>
		<meta charset="utf-8">
		<meta http-equiv="X-UA-Compatible" content="IE=edge">
		<meta name="viewport" content="width=device-width, initial-scale=1">

		<title>Advanced Data Validations Demo</title>

		<!-- Bootstrap core CSS -->
		<link rel="stylesheet" href="//netdna.bootstrapcdn.com/bootstrap/3.1.1/css/bootstrap.min.css">

		<!-- HTML5 shim and Respond.js IE8 support of HTML5 elements and media queries -->
		<!--[if lt IE 9]>
			<script src="https://oss.maxcdn.com/libs/html5shiv/3.7.0/html5shiv.js"></script>
			<script src="https://oss.maxcdn.com/libs/respond.js/1.4.2/respond.min.js"></script>
		<![endif]-->
	</head>

	<body>
		<div class="row">
			<h1 class="col-md-6 col-md-offset-3">Advanced Data Validations Demo</h1>
		</div>
		<p>&nbsp;</p>

		<div class="container">
			@yield( "content" )
		</div><!-- /.container -->

		<!-- Bootstrap core JavaScript -->
		<script src="//ajax.googleapis.com/ajax/libs/jquery/1.11.0/jquery.min.js"></script>
		<script src="//netdna.bootstrapcdn.com/bootstrap/3.1.1/js/bootstrap.min.js"></script>
	</body>
</html>
```

这是一个使用[引导](http://getbootstrap.com/)的 HTML 页面的简单样板代码。这里唯一需要注意的是占位符，我们将在这里注入视图代码；`@yield( "content" )`会告诉 Laravel 的 Blade 解析器，我们视图的`content`段代码应该在这个地方注入。

现在创建`app/views/dummy/create.blade.php`并添加以下代码:

```
@extends( "layouts/default" )

@section( "content" )
	<div class="row">
		<h3 class="col-md-6 col-md-offset-2">Test Form</h3>
	</div>
	<p>&nbsp;</p>
	@if ( ! $errors->isEmpty() )
	<div class="row">
		@foreach ( $errors->all() as $error )
		<div class="col-md-6 col-md-offset-2 alert alert-danger">{{ $error }}</div>
		@endforeach
	</div>
	@elseif ( Session::has( 'message' ) )
	<div class="row">
		<div class="col-md-6 col-md-offset-2 alert alert-success">{{ Session::get( 'message' ) }}</div>
	</div>
	@else
		<p>&nbsp;</p>
	@endif

	<div class="row">
		<div class="col-md-offset-2 col-md-6">
			{{ Form::open( array(
				'route' => 'dummy.store',
				'method' => 'post',
				'id' => 'test-form',
			) ) }}
				<div class="form-group">
					{{ Form::label( 'name', 'Name:' ) }}
					{{ Form::text( 'name', '', array(
						'id' => 'name',
						'placeholder' => 'Enter Your Full Name',
						'class' => 'form-control',
						'maxlength' => 200,
					) ) }}
				</div>
				<div class="form-group">
					{{ Form::label( 'email', 'Email:' ) }}
					{{ Form::text( 'email', '', array(
						'id' => 'email',
						'placeholder' => 'Enter Your Email',
						'class' => 'form-control',
						'maxlength' => 200,
					) ) }}
				</div>
				<div class="form-group">
					{{ Form::label( 'phone', 'Phone:' ) }}
					{{ Form::text( 'phone', '', array(
						'id' => 'phone',
						'placeholder' => 'Enter Your Phone Number',
						'class' => 'form-control',
						'maxlength' => 25,
					) ) }}
				</div>
				<div class="form-group">
					{{ Form::label( 'pin_code', 'Pin Code:' ) }}
					{{ Form::text( 'pin_code', '', array(
						'id' => 'pin_code',
						'placeholder' => 'Enter Your Pin Code',
						'class' => 'form-control',
						'maxlength' => 25,
					) ) }}
				</div>
				<div class="form-group">
					{{ Form::submit( '&nbsp; Submit &nbsp;', array(
						'id' => 'btn-submit',
						'class' => 'btn btn-primary',
					) ) }}
				</div>
			{{ Form::close() }}
		</div>
	</div>
@stop
```

在这个视图中，我们首先告诉 Laravel 我们想要使用指令`@extends( "layouts/default" )`来使用`default.blade.php`布局。然后我们创建`content`部分，因为这是我们已经设置为注入布局的部分。该视图使用 Laravel 的 form builder 呈现一个包含姓名、电子邮件、电话和 Pin 码字段的表单(*我们可以在这里使用 HTML5 字段，并启用基本的浏览器验证，比如对于电子邮件，我们可以使用`Form::email()`，但是因为我们想要检查我们的服务器端验证，所以我们使用普通的文本字段进行输入*)。同样在表单上方，我们检查在`$errors`变量中是否有任何内容，如果有任何错误，就显示出来。此外，我们还会检查我们可能收到的任何紧急消息。

如果我们现在导航到`http://<your-project-domain>/dummy/create` ( *这里的假设是你已经为这个项目*在你的开发栈上设置了一个域，那么我们将会有表单呈现给我们。现在，我们需要能够接受来自这个表单的数据，并对数据进行验证。所以回到我们的`DummyController`中，我们将在构造函数中注入我们的`TestFormValidator`，在`store()`中接受数据并验证它。因此，控制器现在应该是这样的:

```
<?php

use RocketCandy\Exceptions\ValidationException;
use RocketCandy\Services\Validation\TestFormValidator;

class DummyController extends BaseController {

	/**
	 * @var RocketCandy\Services\Validation\TestFormValidator
	 */
	protected $_validator;

	public function __construct( TestFormValidator $validator ) {
		$this->_validator = $validator;
	}

	/**
	 * Show the form for creating a new resource.
	 *
	 * @return Response
	 */
	public function create() {
		return View::make( 'dummy/create' );
	}

	/**
	 * Store a newly created resource in storage.
	 *
	 * @return Response
	 */
	public function store() {
		$input = Input::all();

		try {
			$validate_data = $this->_validator->validate( $input );

			return Redirect::route( 'dummy.create' )->withMessage( 'Data passed validation checks' );
		} catch ( ValidationException $e ) {
			return Redirect::route( 'dummy.create' )->withInput()->withErrors( $e->get_errors() );
		}
	}

}	//end of class

//EOF
```

Laravel 将负责构造函数中的依赖注入，因为默认情况下，所有控制器都从 IoC 容器中解析出来，所以我们不必担心这一点。在`store()`方法中，我们获取 var 中的所有表单输入，在`try/catch`中，我们将数据传递给我们的验证服务。如果数据有效，那么它会将我们重定向回带有成功消息的表单页面，否则它会抛出`ValidationException`,我们将捕捉它，捕捉错误并返回表单以显示哪里出错了。

### 摘要

在第一部分中，我们学习了如何以 Laravel 方式进行数据验证，以及如何将验证抽象为一个服务，该服务可用于验证应用程序中每个实体的数据。在下一个也是最后一个部分，我们将学习如何扩展 Laravel 的验证包来拥有我们自己的自定义规则。

* * *

有想法吗？有问题吗？请在评论中畅所欲言。

## 分享这篇文章