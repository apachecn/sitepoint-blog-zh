# 在 iOS 应用中接受支付

> 原文：<https://www.sitepoint.com/accepting-payments-ios-apps/>

在移动商务应用中，决定成功的一个最重要的方面是支付流程的设计。让用户准备好购物后，你的应用程序需要请求信用卡信息。然后，它将通过支付处理器处理信用卡，并在交易成功时交付购买的商品。

一个设计良好的应用支付流程应该考虑到移动设备屏幕空间的限制。在完成交易时，它应该需要用户最少的打字和输入，并通过消除重复步骤的需要来无缝地重复购买。

## App Store 支付指南和最佳实践

当选择如何在您的 iOS 应用程序中接受支付时，请记住 App Store 关于应用内购买的规则，这一点很重要。

为了在你的应用中销售数字产品或内容，苹果要求使用[应用内购买框架](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html#//apple_ref/doc/uid/TP40008267-CH1-SW1)。这意味着，通过 iOS 应用程序销售数字内容时，不能使用第三方支付处理器。好处是用户在你的应用程序中获得一次点击购买体验，使任何拥有苹果账户的人都可以通过每个 iOS 用户都熟悉的简单、原生的支付界面在你的应用程序中购买产品。

另一方面，如果你在销售实物商品或服务，App Store 规则要求你使用第三方支付处理器，如 [Stripe](http://stripe.com) 、 [Braintree](https://www.braintreepayments.com) 、[Authorize.net](https://www.authorize.net/)或 [Paypal](http://paypal.com) ，而不是苹果应用内购买框架。

如果您正在创建一个电子商务应用程序，在原生 iOS 应用程序中实现适当的支付信息安全和数据流与实现 web 应用程序是不同的。

一些移动商务应用通过将其现有提供商的 web UI 包装在一系列 web 视图中来解决这个问题，但这可能会导致非常差的用户体验。它也容易出现错误，因为它需要应用程序开发人员处理来自支付网关的重定向，并编写服务器端代码来增加应用程序的支付流程。

为了在你的应用程序中获得流畅的支付体验，我建议你选择一个提供原生 SDK 的支付处理器，你可以将它集成到你的应用程序的自定义视图中。一些受开发者欢迎的选项是 Stripe、Braintree 和 Paypal 提供的 iOS SDKs。

虽然提到的每个提供商都有自己的优势，但它们之间的主要区别是 Braintree 和 Paypal 包括整个支付流程的 UI 视图。这包括输入信用卡的详细信息，处理这些信息并向应用程序返回成功或失败的信息。这种方法的优点是开发人员不需要从头开始编写自定义视图。另一方面，Stripe 为您提供了对 UI 的细粒度控制，要求您使用库进行卡令牌化，并由您决定自定义视图。

## 集成 Stripe SDK 并从您的 iOS 应用程序向客户收费

在本节中，我们将展示如何将 Stripe SDK 集成到您的 iOS 应用程序中以收集信用卡信息，并使用后端服务器对其进行收费。

以下是我们需要遵循的步骤概述:

1.  创建一个 Stripe 帐户并访问您的 API 密钥
2.  下载 Stripe iOS SDK
3.  收集卡信息
4.  创建一次性令牌
5.  将令牌发送到服务器
6.  在服务器上，调用 Stripe 为卡充值并完成交易

### 创建一个 Stripe 帐户并访问您的公共 API 密钥

前往[stripe.com](http://stripe.com)注册一个新账户，如果你还没有的话。一旦你注册并进入你的仪表盘，点击右上角的*你的账户*，选择*账户设置*。

您将在标签为 *API 密钥*的选项卡下找到您的密钥。

因为我们这样做是为了测试目的，作为我们教程的一部分，每当你需要你的 API 键时，使用 **Test** 组键。当然，当项目投产时，你会想要使用你的 **Live** 键。

### 在 XCode 项目中下载并设置 Stripe SDK

实现这一点的简单方法是使用 git:

`git clone --recursive https://github.com/stripe/stripe-ios.git`

接下来，将条带绑定添加到 XCode 项目中:

1.  在菜单栏中，点击“文件”，然后“将文件添加到“项目”…”
2.  在下载的存储库中选择“条带”目录
3.  确保选中“将项目复制到目标组的文件夹中(如果需要)”
4.  点击“添加”

Stripe iOS 依赖于 Quartz 动画，这意味着您需要导入相关的框架。导航到应用程序的目标，向下滚动到“链接的框架和库”，并添加 QuartzCore.framework。

### 在应用程序中收集信用卡详细信息

下面的代码片段可以在 [Github](https://github.com/sitepoint-examples/StripeExample) 的完整示例中找到。

在你的 XCode 项目中创建一个新类(姑且称之为 ViewController)，方法是选择 File-> New -> File…然后选择“Objective-C Class”)，并确保它继承了 UIViewController。

在您的类头(ViewController.h)中，您需要像这样导入 STPView.h:

```
#import <UIKit/UIKit.h>;
#import "STPView.h"

@interface ViewController : UIViewController <STPViewDelegate>;

@property (nonatomic, strong) STPView* stripeView;

@end
```

在 ViewController.m 中，我们实例化 STPView 并将其添加为子视图:

```
- (void)viewDidLoad
    {
        [super viewDidLoad];

        self.stripeView = [[STPView alloc] initWithFrame:CGRectMake(15,80,290,55)
                                                  andKey:@"YOUR_PUBLISHABLE_KEY_HERE"];
        self.stripeView.delegate = self;
        [self.view addSubview:self.stripeView];
    }
```

请注意，您将需要在上面的代码片段中使用您自己的可发布 API 密钥。

### 创建一次性令牌

此时，您已经验证了卡输入，并准备将信用卡数据发送到 Stripe 的服务器以接收令牌，您将需要调用 createToken。将以下代码添加到 ViewController.m 中(假设您有一个简单的可单击的 Save 按钮):

```
- (IBAction)savePressed:(id)sender
{
    [[UIApplication sharedApplication] setNetworkActivityIndicatorVisible:YES];
    // Call 'createToken' when the user hits “Save”
    [self.stripeView createToken:^(STPToken *token, NSError *error) {
        [[UIApplication sharedApplication] setNetworkActivityIndicatorVisible:NO];
        if (error) {
            // Handle error
            NSLog(@"Error %@",error);
            [[[UIAlertView alloc] initWithTitle:@"Error" message:@"Credit Card check failed" delegate:self cancelButtonTitle:@"OK" otherButtonTitles:nil] show];
        } else {
            // Ok, step 5.
            [self hasToken:token];
        }
    }];
}
```

### 将令牌发送到您的服务器进行充电

如果对 createToken 的调用成功，最后一步是调用服务器上的一个端点，您将把在步骤 4 中获得的 Stripe 令牌传递给该端点。将以下代码添加到 ViewController.m 中:

```
- (void)hasToken:(STPToken *)token
{
    NSLog(@"Received token %@", token.tokenId);

    NSMutableURLRequest *request = [[NSMutableURLRequest alloc] initWithURL:[NSURL URLWithString:@"https://example.com"]];
    request.HTTPMethod = @"POST";
    NSString *body     = [NSString stringWithFormat:@"stripeToken=%@", token.tokenId];
    request.HTTPBody   = [body dataUsingEncoding:NSUTF8StringEncoding];

    [NSURLConnection sendAsynchronousRequest:request
                                       queue:[NSOperationQueue mainQueue]
                           completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {
                               [[[UIAlertView alloc] initWithTitle:@"Success!" message:@"Your card was charged successfully" delegate:self cancelButtonTitle:@"OK" otherButtonTitles:nil] show];
                           }];
}
```

### 在您的服务器上，调用 Stripe 为卡充值并完成交易

在您的服务器上，您需要检查是否有足够的库存来完成订单。假设库存检查成功，最后一步是从您的服务器端代码向卡收费，并向应用程序返回成功或失败。下面是 Python 代码示例，它使用传递给卡的令牌对卡进行收费。

```
# Set your secret key: remember to change this to your live secret key in production
# See your keys here https://manage.stripe.com/account
stripe.api_key = "MWvGLiJ66l72RxJqvwcoXeF9abflctah"

# Get the credit card details submitted by the form
token = request.POST['stripeToken']

# Create the charge on Stripe's servers - this will charge the user's card
try:
  charge = stripe.Charge.create(
      amount=1000, # amount in cents, again
      currency="usd",
      card=token,
      description="payinguser@example.com"
  )
except stripe.CardError, e:
  # The card has been declined
  pass
```

就这样——您已经成功地为客户在您的应用内购买的商品从他们的信用卡中收取了费用！

或者，您可以考虑使用我们的[MobStac iOS SDK for mobile commerce](http://developer.mobstac.com/)应用程序来为您完成这一切，并且还可以与 Magento 等电子商务后端集成。

## 良好支付流程的示例

如今，许多移动商务应用已经采用简单直观的移动支付体验来推动交易。拼车服务 Lyft 就是这样一款应用，它使用 Stripe 来快速处理移动支付。

![Lyft screenshots](img/c4003c9f2a2c4df48f38b60d124eafa8.png)

当用户第一次登录时，应用程序会提示他们输入信用卡信息。用户甚至可以添加多张信用卡并贴上标签。

当您完成一次乘车时，您可以使用该应用程序选择支付卡，调整小费金额，然后提交付款。如果你在支付提交屏幕上决定使用不同的卡，应用程序可以让你在它们之间切换，使支付过程尽可能顺利。

你在移动应用中见过或用过的最好的支付体验有哪些？

## 分享这篇文章