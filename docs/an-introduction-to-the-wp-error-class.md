# WP_Error 类简介

> 原文：<https://www.sitepoint.com/an-introduction-to-the-wp-error-class/>

在任何开发过程中，错误都是不可避免的，无论您在编写代码时有多么称职和彻底。

作为一名 WordPress 开发者，你的工作是确保你的代码错误被正确处理，而不影响最终用户。WordPress 附带了一个基本的错误处理类，即`WP_Error`,它可以被用来集成到你的代码中进行基本的错误处理。

在本教程中，我们将看看`WP_Error`类的基本结构，它是如何工作的，最重要的是，我们将介绍如何在我们的应用程序中集成`WP_Error`类。

![WordPress WP_Error Class](img/f54b9d979c8f03eeeeb6fc00b8deda9e.png)

## 里面是什么

`WP_Error`类的结构实际上非常简单，但是足够强大，可以用作插件的错误处理机制。

`WP_Error`类的完整源代码可以在 [`wp-includes/class-wp-error.php`](https://core.trac.wordpress.org/browser/tags/4.1.1/src/wp-includes/class-wp-error.php#L0) 文件中找到。让我们看看它的属性和可用的方法。

### 性能

`WP_Error`只有两个私有属性，分别是`$errors`和`$error_data`。`$errors`用于存储相关的错误信息，而`$error_data`可以选择性地用于存储您以后想要访问的相关数据。`WP_Error`使用一个简单的键-值对将相关的错误和数据存储到对象中，因此`WP_Error`中定义的键必须是唯一的，以避免它覆盖先前定义的键。

### 方法

带有各种方法，最终修改它包含的两个属性。让我们来看看其中的几个。

*   `get_error_codes`–返回该特定`WP_Error`实例的所有可用错误代码。如果你只需要第一个错误代码，还有一个单独的函数叫做`get_error_code`。

*   `get_error_messages( $code )`–如果没有提供`$code`，该函数将简单地返回该特定`WP_Error`实例中所有存储的消息。同样，如果您只需要返回特定错误代码的消息，只需使用`get_error_message( $code )`。

*   `add( $code, $message, $data )`–当您想要修改存储在实例化中的错误时，此功能特别有用。`WP_Error`对象。注意，即使`$message`和`$data`不是强制的，变量`$errors`仍然会被填充。

*   `add_data( $data, $code)`–如果您只想修改`$error_data`属性，可以使用该功能。注意，`$code`参数排在第二位，与`add`方法相反。如果没有提供`$code`，错误数据将被添加到第一个错误代码中。

*   这是最近在 WordPress 4.1 中增加的一个相当新的方法，从特定的键中删除所有相关的错误信息和数据。

### 功能

如何知道函数返回的特定变量或数据是否是`WP_Error`的实例？有一个实用函数，你可以简单地检查它，叫做`is_wp_error`，它根据给定的变量返回真或假。

*   `is_wp_error( $thing )`–如果`$thing`是一个`WP_Error`实例，返回 true，否则返回 false。

## 实现您自己的

仅仅知道`WP_Error`内部如何工作是不够的，您还需要学习如何在您自己的应用程序中很好地实现它。让我们通过几个例子来更好地了解它是如何工作的。

### 处理 WordPress 核心函数返回的错误

WordPress 提供了许多实用功能，可以用来加速我们的开发过程。大多数函数还配备了我们可以使用的基本错误处理功能。

例如，`wp_remote_post`是一个非常有用的函数，我们可以用它向特定的 URL 发出远程 POST 请求。然而，我们不能期望远程 URL 总是可访问的或者我们的请求总是成功的。我们从[抄本页面](http://codex.wordpress.org/Function_Reference/wp_remote_post)中知道的是，这个函数将在失败时返回`WP_Error`。这些知识将帮助我们在应用程序中正确地实现错误处理。

看看这个片段。

```
// Make a responseo to remote url $url
$response = wp_remote_post( $url, array(
	'timeout' => 30,
	'body' => array( 'foo' => 'bar' )
    )
);

if ( is_wp_error( $response ) ) {
   echo 'ERROR: ' . $response->get_error_message();
} else {
	// do something
}
```

如您所见，我们正在对一个`$url`执行远程 POST 请求。然而，我们并不是简单地按原样接受`$response`数据，而是用我们之前提到的方便的`is_wp_error`函数做一些检查。如果一切正常，那么只有我们可以继续用我们的`$response`做我们想做的事情。

### 在您自己的应用程序中返回自定义错误

假设您有一个名为`handle_form_submission`的自定义函数来处理联系人表单提交。假设我们在某个地方有一个自定义表单设置，让我们看看如何通过实现我们自己的错误处理功能来使功能更好。

```
function handle_form_submission() {
	// do your validation, nonce and stuffs here

	// Instantiate the WP_Error object
	$error = new WP_Error();

	// Make sure user supplies the first name
	if ( empty( $_POST['first_name'] ) ) {
		$error->add( 'empty', 'First name is required' );
	}

	// And last name too
	if ( empty( $_POST['last_name'] ) ) {
		$error->add( 'empty', 'Last name is required' );
	}

	// Check for email address
	if ( empty( $_POST['email'] ) ) {
		$error->add( 'empty', 'Last name is required' );
	} elseif ( ! is_email( $_POST['email'] ) ) {
		$error->add( 'invalid', 'Email address must be valid' );
	}

	// Lastly, check for the message
	if ( empty( $_POST['message'] ) ) {
		$error->add( 'empty', 'Your message is required' );
	}

	// Send the result
	if ( empty( $error->get_error_codes() ) ) {
		return $error;
	}

	// Everything is fine
	return true;
}
```

当然，您还需要在该函数中实现自己的净化和验证，但这超出了本教程的范围。现在，知道我们正确地返回了出错的`WP_Error`实例，我们可以使用它向最终用户提供更有意义的错误消息。

假设您的应用程序中有一个特定的部分来显示表单提交错误，您可以这样做:

```
$result = handle_form_submission();

if ( is_wp_error( $result ) ) {
	echo '<p>Ops, something went wrong. Please rectify this errors</p>';
	echo '<ul>';
		echo '<li>' . implode( '</li><li>', $result->get_error_messages() ) . '</li>';
	echo '</ul>';
} else {
	// Everything went well
}
```

## 外部参考

*   WordPress Codex 上的类 Referece/WP _ Error
*   [Pippin Williamson 对 WP_Error 类的介绍](https://pippinsplugins.com/intro-to-wp-error/)
*   [为什么瑞恩·麦库埃的 WP_Error 很烂](http://journal.ryanmccue.info/165/why-wp_error-sucks/)

## 结论

完善你的软件开发工艺也意味着知道当你的代码不能完成它应该做的事情时该做什么，并确保你的应用程序可以优雅地处理它。

就 WordPress 而言，使用附带的`WP_Error`类提供了一个相当简单但功能强大的错误处理实现，您可以将它集成到您的应用程序中。

你有什么想法？您在日常开发过程中是如何使用`WP_Error`的？请在下面分享给我们。

## 分享这篇文章