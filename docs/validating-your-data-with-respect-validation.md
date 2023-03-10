# 使用尊重验证来验证您的数据

> 原文：<https://www.sitepoint.com/validating-your-data-with-respect-validation/>

验证是每个应用程序与数据交互的一个重要方面。社区合作开发了一些有用的软件包，如 Symfony、Laravel、Zend 等，而不是每次都重新发明轮子。在本文中，我们将介绍一个不太为人所知的包，叫做[尊重验证](https://github.com/Respect/Validation)，它提供了一些不错的新特性。让我们开始吧。

![](img/f89274f141057fd057b88a81460d5163.png)

## 流行的验证包

使用框架时，很有可能会附带一个验证组件。Symfony 验证器组件提供了一组您的应用程序需要的标准验证规则。

```
class UserSubscriptionForm
{
    protected $email;

    public static function loadValidatorMetadata(ClassMetadata $metadata)
    {
        $metadata->addPropertyConstraint('email', new \Assert\Email([
            'message' => 'Invalid email.'
        ]));
    }
}
```

我相信你的订阅表格不仅仅包含一封电子邮件，但是让我们保持简单。在您的控制器中，您必须像这样触发验证:

```
public function store(Request $request){
    $userSubscriptionForm = UserSubscriptionForm::fromInput($request->all());
    $validator = $this->get('validator');
    $errors = $validator->validate($userSubscriptionForm);

    if (count($errors) > 0) {
        // redirect with errors
    }

    // return success
}
```

在从用户输入创建一个新的`UserSubscriptionForm`之后，我们将触发验证并收集验证错误(如果我们发现任何错误的话)。

Laravel 还配有[照明验证包](https://github.com/illuminate/validation)。它做同样的工作，但方式不同。让我们使用之前的验证示例，并将其转换为 Laravel。

```
class UserSubscriptionController extends Controller
{
    public function store(Request $request)
    {
        $validator = \Validator::make(
            [
                'email' => $request->get('email')
            ],
            [
                'email' => 'required|email'
            ]
        );

        if($validator->fails()){
            $errors = $validator->messages();

            // redirect with errors
        }

        // return success
    }
}
```

Zend validator 包与其他包没有太大区别。对于上一个示例，我们可以执行以下操作:

```
class UserSubscriptionController extends Controller
{
    public function store(Request $request)
    {
        $validator = new Zend\Validator\EmailAddress();

        if(!$validator->isValid($request->get('email'))){
            $errors = $validator->getMessages();

            // redirect with errors
        }

        // return success
    }
}
```

我相信你已经熟悉了上面提到的至少一个包。在本文中，我们将介绍[尊重/验证](https://github.com/Respect/Validation)，并且我们将强调与其他包的主要区别。

## 尊重验证

尊重验证器引入了一种验证数据的简单方法。让我们从完成前面的例子开始。

```
class UserSubscriptionController extends Controller
{
    public function store(Request $request)
    {
        $emailValidator = \Respect\Validation\Validator::email();

        if (!$emailValidator->validate($email)) {
            // redirect with errors
        }

        // return success
    }
}
```

所以，没什么新鲜的！您将注意到的第一件事是，在验证失败后，我们没有检索错误列表。为了检索错误列表，我们需要使用`assert`方法，该方法抛出一个包含错误消息列表的异常。

```
class UserSubscriptionController extends Controller
{
    public function store(Request $request)
    {
        $emailValidator = \Respect\Validation\Validator::email();

       try{
           $emailValidator->assert($request->get('email'));
       }
       catch(\Respect\Validation\Exceptions\NestedValidationExceptionInterface $ex){
           $errors = $ex->getMainMessage();
           // redirect with errors
       }

        // return success
    }
}
```

我们还能够通过链接方法向同一个值添加多个规则。

```
Validator::string()->noWhitespace()->length(4, 10);
Validator::numeric()->between(5, 10);
Validator::date()->between(5, 10);
```

验证多个规则的另一种方法是使用接受规则列表的`allOf`规则。

```
$inputValidator = \Respect\Validation\Validator::allOf(
    new String(),
    new Length(4, 10)
);
```

您可能需要验证一些数据，以匹配至少一个业务规则。让我们以登录表单为例，用户可以输入他们的电子邮件地址或用户名。用户名必须是字母数字，长度在 4 到 16 个字符之间。您可以查看[文档](http://documentup.com/Respect/Validation/#feature-guide/chained-validation)以获得关于可用规则列表的更多详细信息。

```
$usernameOrEmailValidator = \Respect\Validation\Validator::oneOf(
    new \Respect\Validation\Rules\Email(),
    \Respect\Validation\Validator::string()->alnum()->noWhitespace()->length(4, 16)
);
```

您在验证包中不常看到的一个特性是规则否定功能。例如，您可以指定您**不想**匹配的规则。

```
$inputValidator = \Respect\Validation\Validator::not(\Respect\Validation\Validator::numeric());
```

## 自定义错误消息

如前所述，当抛出断言异常时，您可以使用以下方法之一获得错误消息。

*   `getFullMessage`:返回一条常规错误消息，并列出失败的规则。断言`Validator::email()->assert('notAValidEmail')`将抛出以下消息。

```
\-These rules must pass for "notAValidEmail"
  \-"notAValidEmail" must be valid email
```

*   `getMainMessage`:返回一般错误信息，不指定失败规则。电子邮件示例返回`These rules must pass for "notAValidEmail`。
*   `findMessages`:接受一个数组作为参数，该数组包含失败规则的消息列表。

```
$this->messages = [
    'alpha'                 => '{{name}} must only contain alphabetic characters.',
    'alnum'                 => '{{name}} must only contain alpha numeric characters and dashes.',
    'numeric'               => '{{name}} must only contain numeric characters.',
    'noWhitespace'          => '{{name}} must not contain white spaces.',
    'length'                => '{{name}} must length between {{minValue}} and {{maxValue}}.',
    'email'                 => 'Please make sure you typed a correct email address.',
    'creditCard'            => 'Please make sure you typed a valid card number.',
    'date'                  => 'Make sure you typed a valid date for the {{name}} ({{format}}).',
    'password_confirmation' => 'Password confirmation doesn\'t match.'
];

// inside the try catch block

try {
    $this->rules[$input]->assert($value);
} catch (\Respect\Validation\Exceptions\NestedValidationExceptionInterface $ex) {
    dump($ex->findMessages($this->messages));
}
```

如果我们的消息数组中存在一个失败的规则，这将返回一个自定义消息。您可以在[文档](http://documentup.com/Respect/Validation/#feature-guide/getting-messages)中阅读更多关于自定义消息的信息。

为了让事情变得更加实际，我们将验证一个用户订阅表单。该表单包含一组用于标识用户的字段，以及一个用于计费信息的小部分。

## 用户订阅验证

我将使用 Laravel 作为我的例子，但我认为这并不重要，因为我们没有使用任何 Laravel 魔法。您可以从使用 composer: `composer require respect/validation`添加包开始。用户订阅表单将如下所示:

![User Subscription Form](img/ce1e901b8ade5bdceb0d2a604596ad2f.png)

```
// resources/views/home.blade.php

<form action="/send" method="POST">
    <legend>Profile</legend>

    <input class="form-control" name="username" type="text" autofocus="" value="{{\Input::old('username')}}">

    <input class="form-control" name="email" type="email" value="{{\Input::old('email')}}">

    <input class="form-control" name="password" type="password" value="">

    <input class="form-control" name="password_confirmation" type="password" >

    <legend>Billing</legend>

    <input class='form-control' size='4' type='text' name="cardHolderName" value="{{\Input::old('cardHolderName')}}">

    <input class='form-control' size='20' type='text' name="cardNumber" value="{{\Input::old('cardNumber')}}">

    <input class='form-control' size='20' type='text' name="billingAddress" value="{{\Input::old('billingAddress')}}">

    <input class='form-control' size='4' type='text' name="cvc" value="{{\Input::old('cvc')}}">

    <input class='form-control' size='2' type='text' name="expirationMonth" value="{{\Input::old('expirationMonth')}}">

    <input class='form-control' size='4' type='text' name="expirationYear" value="{{\Input::old('expirationYear')}}">

    <button class="btn btn-primary">Join</button>
</form>
```

```
// app/Http/routes.php

Route::get('/', 'UserSubscriptionController@index');
Route::post('/send', 'UserSubscriptionController@send');
```

我们的`UserSubscriptionController`类将有两个方法。一个用于打印表单，另一个用于处理表单提交。我们将创建另一个单独的类来处理表单验证。

```
// app/Http/Controllers/UserSubscriptionController

class UserSubscriptionController extends Controller
{
    protected $userSubscription;

    public function __construct(UserSubscriptionValidator $userSubscription)
    {
        $this->userSubscription = $userSubscription;
    }

    public function index()
    {
        return view('home');
    }

    public function send(Request $request, Redirector $redirector)
    {
        $inputs = $request->all();
        $isValid = $this->userSubscription->assert($inputs);

        if (!$isValid) {
            return $redirector->back()->withInput()->with('errors', $this->userSubscription->errors());
        }

        return "Everything is Good!";
    }
}
```

`App\Validation\UserSubscriptionValidator`类将通过两种方法公开(`assert`和`errors`)。

```
// app/Validation/UserSubscriptionValidator.php

use \Respect\Validation\Validator as V;

class UserSubscriptionValidator
{
    /**
     * List of constraints
     *
     * @var array
     */
    protected $rules = [];

    /**
     * List of customized messages
     *
     * @var array
     */
    protected $messages = [];

    /**
     * List of returned errors in case of a failing assertion
     *
     * @var array
     */
    protected $errors = [];

    /**
     * Just another constructor
     *
     * @return void
     */
    public function __construct()
    {
        $this->initRules();
        $this->initMessages();
    }

    /**
     * Set the user subscription constraints
     *
     * @return void
     */
    public function initRules()
    {
        $dateFormat = 'd-m-Y';
        $now = (new \DateTime())->format($dateFormat);
        $tenYears = (new \DateTime('+10 years'))->format($dateFormat);

        $this->rules['username'] = V::alnum('_')->noWhitespace()->length(4, 20)->setName('Username');
        $this->rules['password'] = V::alnum()->noWhitespace()->length(4, 20)->setName('password');
        $this->rules['email'] = V::email();
        $this->rules['cardHolderName'] = V::alpha()->setName('Card holder name');
        $this->rules['cardNumber'] = V::creditCard()->setName('card number');
        $this->rules['billingAddress'] = V::string()->length(6)->setName('billing address');
        $this->rules['cvc'] = V::numeric()->length(3, 4)->setName('CVC');
        $this->rules['expirationDate'] = V::date($dateFormat)->between($now, $tenYears)->setName('expiration date');
    }
}
```

我们的构造函数调用设置验证约束的`initRules`方法。`setName`方法值被注入到错误消息模板中。`initMessages`方法设置定制消息的列表；如果你想在另一个表单中再次使用它们，可以把它移到类之外。

```
// app/Validation/UserSubscriptionValidator.php

/**
 * Set user custom error messages
 *
 * @return void
 */
public function initMessages()
{
    $this->messages = [
        'alpha'                 => '{{name}} must only contain alphabetic characters.',
        'alnum'                 => '{{name}} must only contain alpha numeric characters and dashes.',
        'numeric'               => '{{name}} must only contain numeric characters.',
        'noWhitespace'          => '{{name}} must not contain white spaces.',
        'length'                => '{{name}} must length between {{minValue}} and {{maxValue}}.',
        'email'                 => 'Please make sure you typed a correct email address.',
        'creditCard'            => 'Please make sure you typed a valid card number.',
        'date'                  => 'Make sure you typed a valid date for the {{name}} ({{format}}).',
        'password_confirmation' => 'Password confirmation doesn\'t match.'
    ];
}
```

将使用先前使用`setName`方法传递的相应名称来更新`{{name}}`标签。一些规则包含更多的变量，比如`{{minValue}}`和`length`规则的`{{maxValue}}`。同样的事情也适用于`date`规则。下一步是定义我们的`assert`方法来执行实际的验证。

```
/**
 * Assert validation rules.
 *
 * @param array $inputs
 *   The inputs to validate.
 * @return boolean
 *   True on success; otherwise, false.
 */
public function assert(array $inputs)
{
    $expirationMonth = array_get($inputs, 'expirationMonth');
    $expirationYear = array_get($inputs, 'expirationYear');
    $inputs['expirationDate'] = '01-' . $expirationMonth . '-' . $expirationYear;

    foreach ($this->rules as $rule => $validator) {
        try {
            $validator->assert(array_get($inputs, $rule));
        } catch (\Respect\Validation\Exceptions\NestedValidationExceptionInterface $ex) {
            $this->errors = $ex->findMessages($this->messages);
            return false;
        }
    }

    $passwordConfirmed = $this->assertPasswordConfirmation($inputs);

    return $passwordConfirmed;
}
```

第一步是根据到期月份和年份创建一个有效日期。这将有助于我们根据实际日期值轻松验证它。我们遍历验证规则列表，测试用户输入是否与定义的规则匹配。如果断言失败，我们设置错误消息属性并将`false`返回给控制器。

目前，该软件包没有提供自动验证密码确认的规则。`assertPasswordConfirmation`方法将只测试传递的值是否相等，并在失败时设置错误消息。

```
public function assertPasswordConfirmation(array $inputs)
{
    $passwordConfirmation = array_get($inputs, 'password_confirmation');
    if ($inputs['password'] !== $passwordConfirmation) {
        $this->errors['password_confirmation'] = $this->messages['password_confirmation'];
        return false;
    } else {
        return true;
    }
}
```

最后一部分是在失败的情况下将错误返回给用户，我们还需要更新我们的`home`模板，以便在失败的情况下打印错误。

```
public function errors()
{
    return $this->errors;
}
```

```
// home.blade.php

@if(\Session::has('errors'))
    <div class="alert alert-danger">
        @foreach(\Session::get('errors') as $error)
            @if(!empty($error))
                <p>{{$error}}</p>
            @endif
        @endforeach
    </div>
@endif
```

![Validation test](img/ffd1bcd6ab332dbde921facda3c73f0f.png)

## 创建自己的规则

您可以创建自己的业务规则，并使用`with`方法将您的名称空间添加到查找数组中。你可以查看[文档](http://documentup.com/Respect/Validation/#feature-guide/custom-rules)来获得更多关于创建你自己的约束的细节。

```
Respect\Validation\Validator::with('App\\Validation\\User\\Rules\\');
```

如果您发现您尝试使用的规则存在于 Zend 或 Symfony 验证包中，您可以使用以下方法之一直接使用它们。

```
Respect\Validation\Validator::sf('Language')->validate('Arabic');
Respect\Validation\Validator::zend('EmailAddress')->validate('me@gmail.com');
```

## 结论

本文简要介绍了尊重验证包。我们看到了 API 有多简单，以及它如何与其他验证器(如 Symfony 和 Zend)集成。在验证过程中扩展和添加新规则也很容易。如果你以前从未用过，我真的鼓励你试一试。如果你已经在你的一些项目中使用了它，我真的很想知道你对它的看法，以及它如何适合你的长期用例——请在下面的评论中告诉我们！

## 分享这篇文章