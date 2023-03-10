# 在 iOS 中授权 Twitter

> 原文：<https://www.sitepoint.com/authorizing-twitter/>

## 介绍

在我的[上一篇文章](https://www.sitepoint.com/handling-the-twitter-api-in-ios5/)中，我们创建了一个简单的应用程序，它可以与 Twitter API 交互，并从公共时间轴中下载一条推文。在一个典型的应用程序中，我们希望从用户的时间线中提取推文。

iOS5 提供了一个 API，用于集中保护授权信息，并向任何需要的应用程序提供对这些信息的访问。这意味着，如果用户希望取消授权或修改账户详情，他们只有一个地方可去。干净利落。

## 证明文件

Accounts 框架对于 iOS 框架的文档非常少。然而，它使用起来相对简单。API 是有文档记录的，但是在撰写本文时没有开发人员使用它的指南。

我们将暂时忽略如何向认证存储添加新帐户，但是您可以在 ACAccountStore 类参考中找到关于如何完成此操作的详细信息

## 建立在早期作品的基础上

### 添加帐户框架

在帐户框架中使用代码之前，我们需要将它添加到我们的应用程序中。向下滚动到项目摘要部分的“链接的框架和库”。

[![](img/c9ab44bc715eec1089a53e0945b36349.png "screenshot_12")](https://www.sitepoint.com/wp-content/uploads/2012/01/screenshot_1.png)

[![](img/1fed049584e6c5d258d8af43463a8c5f.png "screenshot_2")](https://www.sitepoint.com/wp-content/uploads/2012/01/screenshot_2.png)

添加帐户框架

点击“添加”就完成了。

## 修改标题

我们将向 viewController 添加一个新属性“store”。这是一个 ACAccountStore 的句柄，我们在应用程序生命周期的几个点上都需要它。

修改 TWAPIExampleViewController.h，如下所示:

```
#import &lt;UIKit/UIKit.h&gt;
    #import &lt;Accounts/Accounts.h&gt;
    #import &lt;Twitter/Twitter.h&gt;

    @interface TWAPIExampleViewController : UIViewController
        @property (nonatomic, retain) ACAccountStore *store;
        @property (nonatomic, retain) UILabel *tweetLabel;

    -(void)getTweet;

    @end
```

我们为 Accounts 框架添加了一个#import 语句，并添加了 store @property。

通常，当我们添加一个@属性时，我们应该@合成它的访问器和赋值器方法——在 TWAPIExampleViewController.m 中，修改如下代码行:

```
@synthesize tweetLabel;
```

成为:

```
@synthesize store, tweetLabel;
```

第一

## 获得帐户存储的访问权限

当应用程序加载时，我们需要访问帐户存储。账户框架将通过弹出窗口要求用户授权。如果用户接受，我们将可以访问存储在帐户存储中的帐户(在一个空白的 iOS 模拟器实例上，将是…无！)

请记住，我们正在使用我写的上一篇文章中的代码，你可以通过 BuildMobile 的 GitHub 账户访问[。](https://github.com/buildmobile/twitter_api_example)

在 TWAPIExampleViewController.m 中，我们将修改 viewDidLoad，如下所示:

```
- (void)viewDidLoad
    {
        [super viewDidLoad];
        // Do any additional setup after loading the view, typically from a nib.

        self.store = [[ACAccountStore alloc] init];
        ACAccountType *twitterAccountType = [self.store accountTypeWithAccountTypeIdentifier:ACAccountTypeIdentifierTwitter];

        [self.store requestAccessToAccountsWithType:twitterAccountType withCompletionHandler:^(BOOL granted, NSError *error) {
            if(!granted) {
                abort();
                // We didn't get access, so we'll show an error and exit.
                // TODO: Show an error screen to the user and re-request permission!
            }
            return;
        }];

        self.tweetLabel = (UILabel*)[self.view viewWithTag:1];

        [self getTweet];
    }
```

看到我们如何使用 abort()了吗？在你的代码中调用 abort()可能意味着你的应用程序不能进入应用商店。您需要为用户提供一个漂亮的错误屏幕来代替这个调用，让他们知道哪里出错了，并给他们一个修复的方法。

这超出了我想在这里演示的范围，所以像任何优秀的作家一样，我会偷懒跳过它。

## 获取推文

现在我们需要修改我们的 getTweet 方法，以利用我们(应该)拥有的授权访问。

用于访问用户时间表的 Twitter API URL 是“https://API . Twitter . com/1/status/home _ timeline . JSON”。因此，我们将相应地修改我们正在访问的 URL。

```
-(void)getTweet
    {

        NSURL *url = [NSURL URLWithString:@"https://api.twitter.com/1/statuses/home_timeline.json"];   
        NSDictionary *parameters = [NSDictionary dictionaryWithObjectsAndKeys:@"1", @"count", nil];

        TWRequest *tweetRequest = [[TWRequest alloc] initWithURL:url parameters:parameters requestMethod:TWRequestMethodGET];    

        ACAccountType *twitterAccountType = [self.store accountTypeWithAccountTypeIdentifier:ACAccountTypeIdentifierTwitter];
        NSArray *accounts = [self.store accountsWithAccountType:twitterAccountType];

        if([accounts count] == 0) {
            // We need to get the user to add an account!
            // This is pretty cheap, but we'll zap them out to the Settings app, where they can add an account.
            // When they return, we'll re-run viewDidLoad to get access to the account.
            // TODO: Show a nice UI to your users to add/select which account they wish to use.
            [[UIApplication sharedApplication] openURL:[NSURL URLWithString:@"prefs:root=TWITTER"]];
            [self viewDidLoad];
            return;
        }

        ACAccount *account = [accounts objectAtIndex:0];
        //TODO: We should let the user choose which account they're using

        tweetRequest.account = account;

        [tweetRequest performRequestWithHandler:^(NSData *responseData, NSHTTPURLResponse *urlResponse, NSError *error) {
            // Request completed and we have data
            // Output it!

            NSError *jsonError = nil;
            id timelineData = [NSJSONSerialization JSONObjectWithData:responseData options:NSJSONReadingMutableContainers error:&amp;jsonError];

            if(timelineData == NULL) {
                NSString *myString = [[NSString alloc] initWithData:responseData encoding:NSUTF8StringEncoding];
                NSLog(@"Conversion to object failed.");
                NSLog(@"%d", [urlResponse statusCode]);
                NSLog(@"%@", myString);
                NSLog(@"%@", [jsonError localizedDescription]);
                abort();
                // TODO: Show a graceful error message here
            }

            NSDictionary *timelineDict = (NSDictionary*) timelineData;
            NSLog(@"%@", timelineDict);
            self.tweetLabel.text = [[(NSArray*)timelineDict objectAtIndex:0] objectForKey:@"text"];
        }];
    }
```

仍然是一个繁忙的功能，但它甚至更忙！和上次一样，在实践中你真的想把这个功能分解一点，不要做一些俗气的事情，比如重新调用 viewDidLoad。为了便于说明，我试图将我的代码压缩成尽可能少的函数。

所以，从顶部开始，我们是:

1.  定义通过 Twitter API 访问的新 URL(该文件的第 88 行)
2.  获取商店中的 Twitter 帐户列表(第 93 & 94 行)
3.  如果没有配置帐户，将用户交给设置应用程序(第 96 行)。(我们还再次调用 viewDidLoad，当用户重新进入应用程序时，它将重新启动我们的授权过程。这是不好的形式。)
4.  我们将获得堆栈中的第一个帐户(第 106 行)
5.  我们在 tweet 请求上设置帐户(第 109 行)。

然后，像以前一样，我们在一个标签中显示我们得到的 tweet。

## 结论

这是一个非常基本的例子，说明了如何将帐户框架与 twitter 框架结合使用，以访问用户的 Twitter 帐户。实际上，您可能希望使用 OAuth 库来允许用户在您的应用程序中创建帐户(在 AccountStore 中),并选择要使用的帐户。

这个例子的代码可以在 GitHub 的[分支上找到。](https://github.com/buildmobile/twitter_api_example/tree/authorisation)

## 分享这篇文章