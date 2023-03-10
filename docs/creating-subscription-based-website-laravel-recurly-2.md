# 用 Laravel 和 Recurly 创建基于订阅的网站，第 2 部分

> 原文：<https://www.sitepoint.com/creating-subscription-based-website-laravel-recurly-2/>

在本系列的第一部分中，我们使用 Laravel 创建了一个基于订阅的网站的框架。我们还为站点设置了角色，并为新用户授予了一个待定角色。我们现在可以执行基本的注册、登录和注销。在第二部分中，我们将递归地集成来建立我们的付费会员计划。

## 入门指南

有两个 Recurly 库 PHP 客户端库，我们在本系列第一部分开始时通过 Composer 安装了它——和 Recurly JS。Recurly JS 是一个客户端库，用于动态集成安全处理卡片信息的表单。该卡信息被发布到 Recurly 的服务器上，而不是我们的 web 应用程序上，这大大减少了合规性问题。

下载 [Recurly JS 库](https://github.com/recurly/recurly-js/zipball/master)，将 recurly.min.js 从`build`文件夹复制到`public/js/libs`，并在关闭前添加到你的布局中

tag:

```
<script src="/js/libs/recurly.min.js"></script>
</body>
```

我们还需要显示付款表单时使用的 CSS 样式。创建目录`css/recurly`并将`themes`目录复制到其中，然后在布局的相关部分引用它:

```
<link href="/css/recurly/themes/default/recurly.css" rel="stylesheet">
```

当您在[创建 Recurly](http://recurly.com/) 帐户后首次登录时，您有机会创建您的订阅计划。在第一部分中，我们创建了三个级别:青铜级、白银级和黄金级。你可以随时更改它们或者在以后添加更多，但是现在做也没有坏处。

创建一个名为青铜的计划；确保计划代码设置为“青铜色”(全部小写)。设定价格——我设定为每月 4.99 英镑，但你可以自由选择你喜欢的任何金额和/或时间段。您可以选择设置一次性安装费用，甚至设置免费试用期。

重复这个过程两次以上，设置白银(计划代码:“白银”)和黄金(计划代码:“黄金”)计划——我已经将我的计划分别设置为每月 9.99 英镑和 14.99 英镑。

现在转到 Recurly 管理面板，在那里我们需要设置一些东西，并找到应用程序配置的相关信息。单击左侧的 API 访问。单击启用 API 访问。记下您的 API 密钥和子域。

现在转到左手边的 Recurly.js(在 Developer 下面)。单击启用 Recurly.js 和透明发布 API。记下您的私钥。

现在，在`app/config/recurly.php`中创建一个递归配置文件，用您刚刚记下的值以及代表您选择的默认货币(如 USD、GBP、AUD)的三位数代码替换这些值:

```
<?php
return array(
    'api_key'          => 'YOUR-API-KEY',
    'private_key'      => 'YOUR-PRIVATE-KEY',
    'subdomain'        => 'YOUR-SUBDOMAIN',
    'default_currency' => 'XYZ'
);
```

现在转到左侧的推送通知，然后单击配置。输入应用程序的 URL，并在其后追加 */recurly* ，例如:【http://www.example.com/recurly*。暂时将 HTTP 身份验证用户名和 HTTP 身份验证密码字段留空。*

 *## 注册页面

现在让我们创建一个注册页面，允许潜在客户选择他们的计划。创建`app/views/home/signup.blade.php`:

```
@extends('layouts.default')

@section('content')

  <h1>Signup</h1>

  <p>Please select your plan...</p>

  <div class="row-fluid pricing-table pricing-three-column">
    <div class="span4 plan">
      <div class="plan-name-bronze">
      <h2>Bronze</h2>
      <span>&pound;4.99 / Month</span>
      </div>
      <ul>
      <li class="plan-feature">Feature #1</li>
      <li class="plan-feature">Feature #2</li>
      <li class="plan-feature"><a href="/user/register/bronze" class="btn btn-primary btn-plan-select"><i class="icon-white icon-ok"></i> Select</a></li>
      </ul>
    </div>
    <div class="span4 plan">
      <div class="plan-name-silver">
      <h2>Silver <span class="badge badge-warning">Popular</span></h2>
      <span>&pound;9.99 / Month</span>
      </div>
      <ul>
      <li class="plan-feature">Feature #1</li>
      <li class="plan-feature">Feature #2</li>
      <li class="plan-feature"><a href="/user/register/silver" class="btn btn-primary btn-plan-select"><i class="icon-white icon-ok"></i> Select</a></li>
      </ul>
    </div>
    <div class="span4 plan">
      <div class="plan-name-gold">
      <h2>Gold</h2>
      <span>&pound;4.99 / Month</span>
      </div>
      <ul>
      <li class="plan-feature">Feature #1</li>
      <li class="plan-feature">Feature #2</li>
      <li class="plan-feature"><a href="/user/register/gold" class="btn btn-primary btn-plan-select"><i class="icon-white icon-ok"></i> Select</a></li>
      </ul>
    </div>
    </div>

@stop
```

当然，如果您想确保价格总是最新的，您可以通过 Recurly API 获取计划细节，并在模板中动态填充它们。

现在将以下内容添加到`css/style.css`:

```
.pricing-table .plan {
    background-color: #f3f3f3;
    text-align: center;
}

.plan:hover {
    background-color: #fff;
}

.plan {
    color: #fff;
    background-color: #5e5f59;
    padding: 20px;
}

.plan-name-bronze {
    background-color: #665D1E;
    color: #fff;
    padding: 20px;
}

.plan-name-silver {
    background-color: #C0C0C0;
    color: #fff;
    padding: 20px;
}

.plan-name-gold {
    background-color: #FFD700;
    color: #fff;
    padding: 20px;
} 

.pricing-table-bronze  {
    background-color: #f89406;
    color: #fff;
    padding: 20px;
}

.pricing-table .plan .plan-name span {
    font-size: 20px;
}

.pricing-table .plan ul {
    list-style: none;
    margin: 0;
}

.pricing-table .plan ul li.plan-feature {
    border-top: 1px solid #c5c8c0;
    padding: 15px 10px;
}

.pricing-three-column {
    margin: 0 auto;
    width: 80%;
}

.pricing-variable-height .plan {
    display: inline-block;
    float: none;
    margin-left: 2%;
    vertical-align: bottom;
    zoom:1;
    *display:inline;
}

.plan-mouseover .plan-name {
    background-color: #4e9a06 !important;
}

.btn-plan-select {
    font-size: 18px;
    padding: 8px 25px;
}
```

最后，在`app/routes.php`中创建路线:

```
Route::get('/signup', function() {
    return View::make('home/signup');
});
```

## 接受付款

现在是注册页面的下一个阶段——付款。首先，我们来修改一下`user/register` POST 回调；我们不是重定向到主页，而是将用户放在会话中并重定向到支付页面。更改以下内容:

```
return Redirect::to('/')->with(
    'success',
    'Welcome to the site, . Auth::user()->name . '!'
);
```

收件人:

```
Session::put('register_user', $user);
return Redirect::to('/user/register/payment');
```

我们需要扩展默认布局，以便可以在页脚中嵌入 JavaScript 代码。在最后一个脚本标记后添加以下行:

```
@yield('scripts')
```

现在，创建一条新路线:

```
Route::get('/user/register/payment', function() {
    Recurly_js::$privateKey = Config::get('recurly.private_key');
    $plan = 'bronze'; // todo: get this from vars
    $user = Session::get('register_user');
    $signature = Recurly_js::sign(array(
        'account'      => array(
            'account_code' => 'user_' . $user->id
        ),
        'subscription' => array(
            'plan_code'    =>  $plan,
            'currency'     => Config::get('recurly.default_currency')
        )
    ));

    return View::make('user/register')->with(array(
        'plan'      => $plan,
        'subdomain' => Config::get('recurly.subdomain'),
        'currency'  => Config::get('recurly.default_currency'),
        'signature' => $signature
    ));
});
```

关于这段代码的几点说明:

*   我们需要在`Recurly_js`类上设置私有密钥，这将从我们之前创建的配置文件中获取。
*   该计划应该从注册过程的前一阶段延续下来；我将这个实现部分作为用户练习。
*   我们需要使用几条信息为 Recurly.js 生成一个签名。其中，有一个用户的标识符，我们通过连接类(user)和用户的 ID 来创建它。
*   这个签名和其他一些必需的信息一起传递给视图。

支付页面视图分为两部分。首先，HTML 非常简单:

```
@extends('layouts.default')

@section('content')

  <div id="recurly-subscribe">
  </div>

@stop
```

Recurly.js 会将其客户端生成的支付表单注入到这个 div 中。

接下来，我们将一些 JavaScript 添加到视图中，在布局模板底部的部分将得到输出:

```
@section('scripts')
<script>
Recurly.config({
    subdomain: '{{ $subdomain }}',
    currency: '{{ $currency }}'
});

Recurly.buildSubscriptionForm({
    target: '#recurly-subscribe',
    // Signature must be generated server-side with a utility
    // method provided in client libraries.
    signature: '{{ $signature }}',
    successURL: '/user/register/confirm',
    planCode: '{{ $plan }}',
    distinguishContactFromBillingInfo: true,
    collectCompany: false,
    termsOfServiceURL: 'http://www.example.com/terms',
    acceptPaypal: true,
    acceptedCards: ['mastercard',
                    'discover',
                    'american_express', 
                    'visa'],
    account: {
        firstName: 'Joe',
        lastName: 'User',
        email: 'test@example.net',
        phone: '555-555-5555'
    },
    billingInfo: {
        firstName: 'Joe',
        lastName: 'User',
        address1: '123 somestreet',
        address2: '45',
        city: 'San Francisco',
        zip: '94107',
        state: 'CA',
        country: 'US',
        cardNumber: '4111-1111-1111-1111',
        CVV: '123'
    }
});
</script>
@stop
```

这就是神奇的地方——递归地构建支付表单并将其注入 ID 为`#recurly-subscribe`的 div，这需要我们传递给视图的一些信息，以及服务器生成的签名。

接下来，在成功提交表单时，回调递归地回发到上面的`successURL`参数中定义的表单:

```
Route::post('/user/register/confirm', function() {
    $recurly_token = Input::get('recurly_token');
    Recurly_js::$privateKey = Config::get('recurly.private_key');
    $result = Recurly_js::fetch($recurly_token);
    var_dump($result);
});
```

同样，我们用配置中的私钥初始化 Recurly.js，并使用它来获取 Recurly 作为 POST 变量(`recurly_token`)发送的令牌所表示的对象。它将是`Recurly_Subscription`的一个实例，从中我们可以提取各种信息。我已经用`var_dump()`输出给你看了。

让我们首先获取计划代码，这样我们就知道用户刚刚注册了什么订阅计划:

```
$plan_code = $result->plan->plan_code;
```

现在找到相应的角色；请注意，我们刚刚给了它们相同的名称(例如，“青铜”、“白银”和“黄金”)。

```
$role = Role::where('name', '=', $plan_code)->first();
```

然后从会话中获取用户(然后将其从会话中删除):

```
$user = Session::get('register_user');
Session::forget('register_user');
```

然后，我们向新用户授予适当的角色:

```
$user->roles()->attach($role);
```

然后，我们需要删除待定角色:

```
$role_pending = $role_pending = Role::where('name', '=', 'pending')->first();
DB::table('role_user')->where('user_id', '=', $user->id)->where('role_id', '=', $role_pending->id)->delete();
```

我们现在已经增强了注册流程，以便根据所选计划接受付款、创建订阅以及向新用户帐户应用角色。在下一部分中，我们将进一步了解用户帐户和订阅的管理。

## 帐户管理页面

让我们创建一个简单的页面，用户可以通过它来管理自己的帐户。显然，这需要用户登录，所以我们需要应用验证过滤器。我们可以将所有相关的路由放入一个组中，而不是为每个受保护的页面指定过滤器，如下所示:

```
Route::group(array('before' => 'auth'), function() {
    Route::get('/user/account', function() {
        // User must be logged in
    });
    Route::get('user/account/billing', function() {
        // User must be logged in
    });
});
```

帐户页面回调很简单:

```
Route::get('/user/account', function() {
    return View::make('user/account/index');
});
```

现在解决视图`app/views/user/account/index.blade.php`:

```
@extends('layouts.default')

@section('content')

    <h1>Your Account</h1>

    <ul>
	<li><a href="/user/account/edit">Edit your account information</a></li>
	<li><a href="/user/account/plan">Update your subscription plan</a></li>
    	<li><a href="/user/account/billing">Update your Billing information</a></li>
    </ul>

@stop
```

让我们从更新账单信息页面开始。当然，我们不会存储人们的账单信息，所以像支付页面一样，Recurly 会为您创建并填充表单，将新信息发布回 Recurly，而不会影响您的 web 应用程序。

```
Route::get('user/account/billing', function() {
    Recurly_js::$privateKey = Config::get('recurly.private_key');

    $account_code = 'user_' . Auth::user()->id;

    $signature = Recurly_js::sign(
        array('account' => array('account_code' => $account_code))
    );

    return View::make('user/account/billing')->with(array(      
        'subdomain'     => Config::get('recurly.subdomain'),
        'currency'      => Config::get('recurly.default_currency'),
        'account_code'  => $account_code,
        'signature'     => $signature
    ));
});
```

这与支付页面非常相似，因为我们正在初始化 Recurly.js 库，创建签名(尽管使用不同的信息)，并向视图传递一些参数。

视图`app/views/user/account/billing.blade.php`遵循与支付页面几乎相同的行:

```
@extends('layouts.default')

@section('content')

 <div id="recurly-billing">
 </div>

@stop

@section('scripts')
<script>
Recurly.config({
    subdomain: '{{ $subdomain }}',
    currency: '{{ $currency }}'
});

Recurly.buildBillingInfoUpdateForm({
    target: '#recurly-billing',
    successURL: '/user/account/billing/confirm',
    accountCode: '{{ $account_code }}',
    signature: '{{ $signature }}'
});
</script>
@stop
```

最后，当用户提交账单信息表单时，有一个非常简单的回调:

```
Route::post('user/account/billing/confirm', function()
{
    return Redirect::to('/user/account')->with('success', 'Your billing information has been updated.');
});
```

就这样，用户现在可以更新他们的账单信息了！

我还没有在这里实现编辑帐户功能，但是它非常简单。我把它留给你做练习。

## 推送通知

除了能够查询 Recurly 的 API 之外，该服务还可以“ping”您的应用程序，并在某个事件发生时发出通知。然而，这些推送通知不应该与移动通知混淆，它们是完全不同的。本质上，服务向您指定的 URI 发送 POST 请求，并发送 XML 文档作为请求的主体。您可以使用递归库提取相关信息，并采取相应的行动。递归文档中详细描述了这些推送通知。

让我们定义我们的回调，当收到推送通知时。

```
Route::post('recurly', function(){
    $xml = file_get_contents ("php://input");
    $notification = new Recurly_PushNotification($xml);

    switch ($notification->type) {
        // ... process notification
    }
});
```

你现在可能已经意识到，一旦有人注册并支付了服务费用，无论他们的订阅是否继续，他们都将永远是会员。因此，在本教程中，我们最感兴趣的通知是取消订阅通知。我们可以使用来自 Recurly 的通知来识别非活动订阅并撤销帐户上的相应角色。例如:

```
switch ($notification->type) {
    case 'canceled_subscription_notification':
        // get the account code
        $account_code = $notification->account->account_code;
        // extract the user ID (account_code format is user_ID)
        $user_id = intval(substr($account_code, (strpos($account_code, '_')+1)));   
        // find the user in question
        $user = User::find($user_id);
        // get the plan code
        $plan_code = $notification->subscription->plan->plan_code;
        // find the corresponding role...
        $role = Role::where('name', '=', $plan_code)->first();
        // ...and revoke it
        DB::table('role_user')->where('user_id', '=', $user->id)->where('role_id', '=', $role)->delete();
        break;

    // ... process notification
}
```

根据通知的类型，您还可以做许多其他事情。您可以使用基于订阅的通知(新的、更新的、取消的等等。)来创建订阅历史，跟踪成员数量，并分析取消情况。您还可以使用交易——无论是正数(付款)还是负数(退款)——来跟踪实际和预期的收入。

## 分享这篇文章*