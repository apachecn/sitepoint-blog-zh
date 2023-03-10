# 通过 Stripe 和 Laravel 销售下载

> 原文：<https://www.sitepoint.com/selling-downloads-stripe-laravel/>

数字商品是越来越有价值的商品。无论你是销售模板或字体文件的设计师、对代码包收费的开发人员还是销售 MP3 的音乐人，在网上销售数字商品往往比实体商品容易得多——生产成本低得多，而且没有送货费。

在本文中，我将展示如何使用 PHP 和 Stripe 实现一个销售数字商品的简单商店，Stripe 是一个支付提供商，旨在使在线支付比以往任何时候都更容易，因为您不需要设置特殊的商家帐户或处理复杂的支付网关。

## 开始之前

你首先需要用 Stripe 设置一个账户[。请注意，该服务目前仅在美国、英国、爱尔兰和加拿大可用。您可能希望查看](https://stripe.com)[的博客](https://stripe.com/blog)或[在 Twitter](https://twitter.com/stripe) 上关注他们，以了解其他国家的最新可用性。

建立一个测试账户根本不需要时间，也不需要任何复杂的财务问卷或信息。您需要记下您的测试 API 密钥和可发布密钥，以备后用。

## 安装

对于这个示例应用程序，我将使用 [Laravel](http://laravel.com/) 。它会处理一些基本的事情，比如路由、模板和启动下载，但是这个例子不应该太难适应其他框架(或者实际上根本没有框架)。请注意，您可以从 [Github](https://github.com/phpmasterdotcom/LukasWhite-StripeAndLaravel "Github repo") 中克隆整个示例。

通过 [Composer](http://getcomposer.org/) 安装 Laravel 开始:

`composer create-project laravel/laravel stripe-downloads --prefer-dist`

在您的`composer.json`文件的`require`部分包含以下内容，并运行`composer update`:

`"abodeo/laravel-stripe": "dev-master"`

这为 Stripe SDK 提供了一个简单的包装器，允许从 Laravel 内部使用它，而不必担心使用合适的文件。

使用以下方式发布配置文件:

`php artisan config:publish abodeo/laravel-stripe`

然后在`app/config/packages/abodeo/laravel-stripe/stripe.php`中输入您的 API 密钥和可发布密钥

最后，将该软件包添加到您的服务提供商列表中(`app/config/app.php`):

`'Abodeo\LaravelStripe\LaravelStripeServiceProvider',`

## 设置数据库

配置并创建数据库，然后运行以下命令来创建迁移:

`php artisan migrate:make create_downloads_table`

下面是相关部分(属于`up()`函数)，为我们将要销售的下载创建一个表:

```
Schema::create('downloads', function($table)
{
    $table->increments('id')->unsigned();        
    $table->string('name', 255);      
    $table->string('filepath', 255);      
    $table->integer('price');            
    $table->timestamps();
}); 
```

注意`price`是一个整数，因为在内部我们只处理美分/便士。`Filepath`将指文件相对于`app/storage`目录的位置。

相应的模型非常简单:

```
// app/models/Download.php
class Download extends Eloquent {
    protected $fillable = array('name', 'filepath', 'price');
}
```

最后，让我们用一些示例下载来播种数据库:

```
class DownloadsSeeder extends Seeder {

    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        $data = array(
            array(
                'name'            =>    'Sample download 1',
                'filepath'    =>    'downloads/one.zip',
                'price'            =>    500,
            ),
            array(
                'name'            =>    'Sample download 2',
                'filepath'    =>    'downloads/two.zip',
                'price'            =>    1000,
            ),
        );

        foreach ($data as $properties) {

            $download = new Download($properties);
            $download->save();            

        }
    }

} 
```

## 简单的主页

让我们创建一个简单的主页，允许人们选择他们想要购买的下载:

```
Route::get('/', function()
{
    $downloads = Download::get();    
    return View::make('index', array('downloads' => $downloads));
});
```

和观点:

```
// app/views/index.blade.php
<h1>Select your download:</h1>

<table class="table table-bordered">
@foreach ($downloads as $download)
    <tr>
        <td>{{ $download->name }}</td>
        <td>&pound;{{ round($download->price/100) }}</td>
        <td><a href="/buy/{{ $download->id }}" class="btn btn-primary">Buy</a></td>
    </tr>
@endforeach
</table>
```

## 接受付款

Stripe 提供了两种获取卡详细信息的方法。第一个是一个简单的按钮，使用 Javascript 生成，在弹出窗口中启动一个支付表单，如下所示:

![image](img/eaaf18dfea22c5de4df57ef70a459e2d.png)

第二种也是更复杂的方法允许您自己创建表单。这就是我在这个例子中要用的方法。路线是这样的:

```
Route::get('/buy/{id}', function($id)
{
    $download = Download::find($id);    
    return View::make('buy', array('download' => $download));
});
```

和观点:

```
// app/views/buy.blade.php
<form action="" method="POST" id="payment-form" role="form">

  <div class="payment-errors alert alert-danger" style="display:none;"></div>

  <input type="hidden" name="did" value="{{ $download->id }}" />

  <div class="form-group">
    <label>
      <span>Card Number</span>
      <input type="text" size="20" data-stripe="number" class="form-control input-lg" />
    </label>
  </div>

  <div class="form-group">
    <label>
      <span>CVC</span>
      <input type="text" size="4" data-stripe="cvc" class="form-control input-lg" />
    </label>
  </div>

  <div class="form-group">  
    <label>
      <span>Expires</span>      
    </label>
    <div class="row">
      <div class="col-lg-1 col-md-1 col-sm-2 col-xs-3">
        <input type="text" size="2" data-stripe="exp-month" class="input-lg" placeholder="MM" />
      </div>  
      <div class="col-lg-1 col-md-1 col-sm-2 col-xs-3">
        <input type="text" size="4" data-stripe="exp-year" class="input-lg" placeholder="YYYY" />
      </div>
    </div>
  </div>

    <div class="form-group">
      <button type="submit" class="btn btn-primary btn-lg">Submit Payment</button>
  </div>
</form>
```

眼尖的人会注意到，与卡相关的输入缺少一个关键属性—`name`。如果没有这一点，当表单被提交时，任何数据都不可能被传递到我们的服务器。怎么会这样呢？

其实这是故意的。我们不希望任何信用卡数据被发送到我们的应用程序中。

我们要做的是使用 Javascript 拦截表单的提交。卡信息将从表单中提取——注意`data-stripe`属性——并与我们的可发布密钥一起安全地发送到 Stripe 服务器。然后 Stripe 负责验证这些细节，如果一切正常，返回给我们一个特殊令牌。一旦我们收到这个令牌，我们就可以将它注入到表单中，并像往常一样发布结果——减去卡的详细信息。

回到服务器上，我们可以将这个令牌与我们的私有(API)密钥结合使用，对用户的卡进行实际收费。如果这段代码被截获，没有我们的私钥就没有用了，所以黑客对它无能为力。

我们从初始化 Stripe Javascript 开始:

```
Stripe.setPublishableKey('@stripeKey');
```

在上面的代码中，`@stripeKey`是一个特殊的刀片扩展，它输出可发布的密钥。如果您没有使用 blade 模板库，您可能希望改为这样做:

```
Stripe.setPublishableKey('<?php print Config::get('stripe.publishableKey') ?>');
```

接下来，这是我们表单的提交处理程序:

```
$('#payment-form').submit(function(e) {
  var $form = $(this);

  $form.find('.payment-errors').hide();

  $form.find('button').prop('disabled', true);

  Stripe.createToken($form, stripeResponseHandler);

  return false;
});
```

处理 Stripe 响应的函数:

```
 function stripeResponseHandler(status, response) {
      var $form = $('#payment-form');

      if (response.error) {

        $form.find('.payment-errors').text(response.error.message).show();
        $form.find('button').prop('disabled', false);
      } else {

        var token = response.id;

        $form.append($('<input type="hidden" name="stripeToken" />').val(token));

        $form.get(0).submit();
      }
  };
```

注意令牌是如何通过动态创建一个名为`stripeToken`的新隐藏表单元素注入表单的。这是提交的唯一与支付相关的数据。

如果您检查来自 Stripe 服务器的响应，您会注意到除了令牌——`id`属性之外，还有一个`card`字典对象。显然，这不包含卡号或 CVC，但它包含类型 Visa、Mastercard 等-以及卡号的最后 4 位数字。这对于生成发票或收据很有用；包含这些信息以向客户表明他们使用的是什么卡是很常见的。

让我们构建 POST 表单处理程序:

```
 Route::post('/buy/{id}', function($id)
    {
        // Set the API key    
        Stripe::setApiKey(Config::get('laravel-stripe::stripe.api_key'));

        $download = Download::find($id);

        // Get the credit card details submitted by the form
        $token = Input::get('stripeToken');

        // Charge the card
        try {
            $charge = Stripe_Charge::create(array(
                "amount" => $download->price,
                "currency" => "gbp",
                "card" => $token,
                "description" => 'Order: ' . $download->name)
            );

            // If we get this far, we've charged the user successfully

        Session::put('purchased_download_id', $download->id);
        return Redirect::to('confirmed');

        } catch(Stripe_CardError $e) {
          // Payment failed
        return Redirect::to('buy/'.$id)->with('message', 'Your payment has failed.');        
        }
    }
```

让我们过一遍。

首先，我们为 Stripe 设置 API 键。当我们在客户端处理表单时，我们可以公开可发布的密钥，因为没有私有的 API 密钥，令牌是没有用的。

接下来，我们从 ID 中获取要购买的下载，这样我们就可以获得名称(在交易参考中使用)和价格(以美分/便士等为单位)。

$token 的值是我们在上面的 Javascript 中从 Stripe 得到的，然后我们将它注入到表单中。

接下来，我们使用`Stripe_Charge::create`对卡进行实际充电。$currency 设置不是必需的，但它将默认为 USD，即使您在美国以外设置了一个帐户。

如果支付成功，我们将所购买商品的 ID 放在会话中。我们一会儿会用这个来控制访问。

如果支付失败——通常是因为银行拒绝交易——就会抛出一个`Stripe_CardError`异常。

您可以使用虚拟卡号`4242-4242-4242-4242`，以及任何三位数的 CVC 和任何到期日期来测试支付过程——前提是在未来。

确认途径很简单:

```
Route::get('/confirmed', function()
{
    $download = Download::find(Session::get('purchased_download_id'));
    return View::make('confirmed', array('download' => $download));
});
```

该视图允许买方通过单击下载按钮来下载他们的文件:

```
// app/views/confirmed.blade.php
<h1>Your Order has been Confirmed</h1>

<p>You can now download your file using the button below:</p>

<p><a href="/download/{{ $download->id }}" class="btn btn-lg btn-primary">Download</a></p>
```

## 传递文件

最后，我们需要实现下载链接。我们不想简单地链接到一个公共文件，因为我们对访问收费。相反，我们将从应用程序的存储目录中获取文件——这是无法通过网络访问的——并且只有在当前用户已经成功付费的情况下才进行交付。

```
Route::get('/download/{id}', function($id)
{
    $download = Download::find($id);        
    if ((Session::has('purchased_download_id') && (Session::get('purchased_download_id') == $id))) {
        Session::forget('purchased_download_id');
        return Response::download(storage_path().'/'.$download->filepath);    
    } else {
        App::abort(401, 'Access denied');
    }
});
```

此链接只能使用一次；实际上，您可能希望以后能够再次下载文件，但是我会让您自己想出办法来实现这一点。

## 摘要

在本文中，我展示了使用 Stripe 进行支付是多么简单，而不必担心商家账户、支付网关或存储敏感的卡信息。我们已经为数字下载实现了一个可行的、简单的购买流程。希望您已经看到了足够多的内容，可以开始在您的项目中实现它。

## 分享这篇文章