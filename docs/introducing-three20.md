# 介绍三个 20

> 原文：<https://www.sitepoint.com/introducing-three20/>

一个面向 iPhone 开发者的 Objective-C 库。

我喜欢 iPhone 的一点是，大多数应用程序与操作系统的外观和感觉是一致的，同时保留了它们独特的特征。然而，我不知道如何复制一些更好的功能，直到我发现了这个库。

Three20 是一个开源的 Objective-C 库，最初是为脸书应用程序开发的，但后来被许多其他知名品牌和项目使用。它提供了强大的视图控制器，并且是模块化的，因此您可以选择包含哪些元素，或者创建扩展。

在本文中，我将介绍制作一个简单的 RSS 阅读器所需的步骤，该阅读器实现了许多 iPhone 应用程序中常见的酷的*拉取-重新加载*特性。我们将安装 Three20 库，然后逐步完成实现它所需的代码示例。

### 装置

安装 Three20 库需要相当长的时间，但是按照说明操作很简单。您应该有一个存储项目和实验的好地方，为此，我在我的主目录中创建了一个“项目”文件夹，我假设您已经做出了类似的决定。

首先，打开一个终端窗口并导航到您的项目目录(`cd`来改变目录，因此键入`cd Projects`或您的文件夹名称)。输入以下命令下载并安装 Three20 所需的库。如您所见，我们正在克隆一个 git 存储库。

```
git clone git://github.com/facebook/three20.git
```

没那么糟吧？下载所有文件可能需要几分钟的时间，所以在此期间，我们将设置您的 Xcode 项目。启动 Xcode，点击“开始一个新项目”，然后选择“iOS 项目”，最后选择“基于视图的项目”，然后点击“下一步”。

您需要输入一个名称。我使用了“BuildMobileThree20 ”,如果你也这样做的话，你会更容易理解，特别是如果你还在学习 Xcode 的话。项目建立后，我们需要关闭 Xcode 并完成 Three20 库的安装。

返回到终端，检查 Three20 library 是否已经完成下载。在终端窗口中运行以下命令，这将安装基本的 Three20 库，以及一个非常有用的 XML 扩展，它将帮助我们阅读 BuildMobile RSS 提要。

```
python three20/src/scripts/ttmodule.py -p BuildMobileThree20/BuildMobileThree20.Xcodeproj Three20 
python src/scripts/ttmodule.py -p ../BuildMobileThree20/BuildMobileThree20.Xcodeproj/ extThree20XML
```

现在您可以再次打开 Xcode 项目，您应该会看到三个 20 库已经包含在您的构建路径中。安装完成后，我们都准备好了，可以继续构建各个部分了。

### 构建部件

现在来看看有趣的东西，构建一些小部件，将它们组合在一起，制作我们聪明的小应用程序。我喜欢库的一点是它们引导你遵循良好编程实践的温和方式，Three20 也不例外。我们建造的作品是漂亮整洁的，有组织的个体。

我们需要做的第一件事是一个提要条目。提要条目实际上只不过是用来表示 RSS 提要中的一篇文章或一个条目的变量集合。使用带有子类“NSObject”的 Objective-C 类创建一个新文件

**FeedItem.h**

```
@interface FeedItem : NSObject {
    NSString *title;
    NSString *description;
    NSDate *publishedDate;
    NSString *link;
}

@property (nonatomic, copy) NSString *title;
@property (nonatomic, copy) NSString *description;
@property (nonatomic, copy) NSString *link;
@property (nonatomic, retain) NSDate *publishedDate;

@end
```

**FeedItem.m**

```
@implementation FeedItem

@synthesize title, description, link, publishedDate;

- (void)dealloc {
    TT_RELEASE_SAFELY(title);
    TT_RELEASE_SAFELY(description);
    TT_RELEASE_SAFELY(link);
    TT_RELEASE_SAFELY(publishedDate);
    [super dealloc];
}

@end
```

注意使用了`TT_RELEASE_SAFELY`而不是调用 release。`TT_RELEASE_SAFELY`是一种方便的方法，它会安全地释放一个变量，然后将该变量设置为`nil`以防止您意外地再次调用它。这非常有用，因为它可以防止小错误变成通宵调试会话。

### 模型

现在我们有了一个保存“FeedItem”信息的基类，我们需要另一个对象，它将负责下载 BuildMobile RSS 提要并将其转换为“feed item”的集合。这个类当然是我们的“FeedModel”。

谢天谢地，Three20 有这个用途的`TTURLRequestModel`。要实现一个模型，你需要做的就是编写一个触发异步 URL 请求的方法，使用它自己作为事件的委托。通过拦截`requestDidFinishLoad`方法，我们可以在依赖于模型的任何视图被刷新之前，快速地将我们的 RSS 提要转换成提要条目。

**FeedModel.h**

```
#import <Three20/Three20.h>

@interface FeedModel : TTURLRequestModel {
    NSString *rssFeed;
    NSArray *items;
}

@property (nonatomic, retain) NSArray *items;

@end
```

**FeedModel.m**

```
// Send off a request 
- (void)load:(TTURLRequestCachePolicy)cachePolicy more:(BOOL)more {
    if (!self.isLoading) {

        // We create a new Request for the Build Mobile RSS feed
        // requestDidFinishLoad will be called once the request has ended
        TTURLRequest *request = [TTURLRequest requestWithURL:@"https://www.sitepoint.com/feed/" delegate:self];
        request.cachePolicy = cachePolicy;
        request.cacheExpirationAge = TT_DEFAULT_CACHE_EXPIRATION_AGE;

        // This tells the URLRequest to return an XML Document (RSS)
        TTURLXMLResponse* response = [[TTURLXMLResponse alloc] init];
        response.isRssFeed = YES; // RSS needs to be handled differently
        request.response = response;
        TT_RELEASE_SAFELY(response);

        [request send];
    }

- (void)requestDidFinishLoad:(TTURLRequest *)request {
    NSMutableArray *feedItems = [[NSMutableArray alloc] init];

    // Our response should have an XML Object
    TTURLXMLResponse *response = (TTURLXMLResponse*) request.response;
    NSDictionary *feed = response.rootObject;

    NSDictionary *channel = [feed objectForKey:@"channel"];
    NSObject *channelItems = ;
    if ([channelItems isKindOfClass:[NSArray class]]) {
        for (NSDictionary *item in (NSArray*) channelItems) {
            // Get the Required Elements from the Dictionary
            NSDictionary *titleElement = [item objectForKey:@"title"];
            NSDictionary *descriptionElement = [item objectForKey:@"description"];
            NSDictionary *linkElement = [item objectForKey:@"link"];
            NSDictionary *pubDate = [item objectForKey:@"pubDate"];

            // Format the Recieved String into a Date
            NSDateFormatter* dateFormatter = [[NSDateFormatter alloc] init];
            [dateFormatter setTimeStyle:NSDateFormatterFullStyle];
            [dateFormatter setDateFormat:@"EEE, dd MMMM yyyy HH:mm:ss ZZ"];
            NSDate* date = [dateFormatter dateFromString:[pubDate objectForKey:@"___Entity_Value___"]];
            TT_RELEASE_SAFELY(dateFormatter);

            // Build the Feed Item
            FeedItem *feedItem = [[FeedItem alloc] init];
            feedItem.title = [titleElement objectForKey:@"___Entity_Value___"];
            feedItem.description = [descriptionElement objectForKey:@"___Entity_Value___"];
            feedItem.link = [linkElement objectForKey:@"___Entity_Value___"];
            feedItem.publishedDate = date;

            [feedItems addObject:feedItem];
            TT_RELEASE_SAFELY(feedItem);
        }
    }

    self.items = feedItems;
    TT_RELEASE_SAFELY(feedItems);

    [super requestDidFinishLoad:request];   
}
```

### 数据源

现在我们有了一个“FeedModel ”,它是独立的，没有连接到任何东西，我们需要通过构建一个“FeedDataSource”来解决这个问题。数据源负责保存模型，并将从模型返回的提要条目转换为 Three20 的视图组件所需的表格单元格。

这种内容与显示逻辑的分离使我们能够轻松地测试模型中的任何问题，或者快速地将一个数据源替换为另一个数据源，而无需更改任何复杂的显示逻辑。这种内容的分离是良好编程实践的产物。

我们需要创建另一个名为“FeedDataSource”的新对象，因为之前这是一个 Objective-C 类，带有 NSObject 的子类。我们所要做就是实现一个方法来返回我们的模型，一旦表重新加载了我们的模型，另一个方法就更新数据源项。

**FeedDataSource.h**

```
#import <Three20/Three20.h>

#import "FeedModel.h"

@interface FeedDataSource : TTListDataSource {
    FeedModel *feedModel;
}

@end
```

**FeedDataSource.m**

```
#import "FeedDataSource.h"
#import "FeedItem.h"

#import <Three20Core/NSDateAdditions.h>
#import <Three20Core/NSStringAdditions.h>

@implementation FeedDataSource

- (id)init {
    if (self = [super init]) {
        feedModel = [[FeedModel alloc] init];
    }
    return self;
}

// Give the model to our TableView to use
- (id<TTModel>)model {
    return feedModel;
}

// Our Model has loaded items, Convert to Table Cells
- (void)tableViewDidLoadModel:(UITableView*)tableView {
    NSMutableArray *items = [[NSMutableArray alloc] init];

    // Convert Feed Items into TableCells
    for (FeedItem *feedItem in feedModel.items) {
        // Get the Heading, make it safe
        NSString *headingString = [feedItem.title stringByRemovingHTMLTags];
        NSString *descriptionString = [feedItem.description stringByRemovingHTMLTags];
        NSString *publishedString = [feedItem.publishedDate formatRelativeTime];

        NSString *content = [NSString stringWithFormat:@"<b>%@</b>n%@n<i>%@</i>",
                             headingString,
                             descriptionString,
                             publishedString];

        TTStyledText* styledText = [TTStyledText textFromXHTML:content lineBreaks:YES URLs:YES];
        [items addObject:[TTTableStyledTextItem itemWithText:styledText URL:feedItem.link]];
    }

    self.items = items;
    TT_RELEASE_SAFELY(items);
}

- (void)dealloc {
    TT_RELEASE_SAFELY(feedModel);
    [super dealloc];
}

@end
```

### 控制器

现在所有这些都完成了，我们可以创建一个控制器了。本质上，我们正在创建一个 TableView，所以我们应该从`TTTableViewController`开始扩展，并利用 3p 20 提供的很酷的特性。控制器只需要实现一个方法来创建视图使用的数据源。因为这是一个框架，所以所有的类都知道如何处理数据。

现在来看一小段 320 魔术。我们通过返回一个单独的`TTTableViewDragRefreshDelegate`来实现`createDelegate`。这一行代码为我们的表视图添加了拉取重载功能，这有多简单？和一点点魔法。

```
@implementation FeedListController

- (id)initWithNibName:(NSString *)nibNameOrNil bundle:(NSBundle *)nibBundleOrNil {
    if (self = [super initWithNibName:nibNameOrNil bundle:nibBundleOrNil]) {
        self.title = @"Build Mobile RSS";
        self.variableHeightRows = YES;
    }
    return self;
}

// Returns a FeedDataSource which will create a Model and define our Table View
- (void)createModel {
    self.dataSource = [[[FeedDataSource alloc] init] autorelease];
}

// Adds Pull to Reload to our table, sweet!
- (id<UITableViewDelegate>)createDelegate {
    return [[[TTTableViewDragRefreshDelegate alloc] initWithController:self] autorelease];
}

@end
```

### 应用程序代理

到目前为止，我们已经创建了一个“FeedItem”类，“FeedModel”，一个“FeedDataSource”，最后是一个“FeedListController”。现在所有这些都完成了，我们只需要改变我们的应用程序委托来使用我们的新控制器。这是最后一个阶段，我们将所有的部分整合在一起。

这个 AppDelegate 是在您创建最初的项目时创建的，如果您到目前为止一直按照说明操作，它将被命名为`BuildMobileThree20AppDelegate`。我们将使用`TTNavigator`来显示我们的控制器，并包含显示我们在 Web 视图中点击的任何链接的方法。

**buildmobilethree 20 app delegate . m**

```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {    
    TTNavigator* navigator = [TTNavigator navigator];
    navigator.persistenceMode = TTNavigatorPersistenceModeTop;
    navigator.window = window;

    TTURLMap *map = navigator.URLMap;
    [map from:@"*" toViewController:[TTWebController class]]; // Shows web addresses in a browser
    [map from:@"tt://buildmobile" toSharedViewController:[FeedListController class]];

    if (![navigator restoreViewControllers]) {
        [navigator openURLAction:[TTURLAction actionWithURLPath:@"tt://buildmobile"]];
    }   

    [self.window makeKeyAndVisible];
    return YES;
}
```

点击 Xcode 中的“播放”按钮或“运行”,你应该很快就能在自己的应用中阅读 BuildMobile 文章了。虽然这肯定不是 Three20 框架的全面指南，但我希望这足以让您开始尝试。

*   [GitHub 上的三个 20RSS 项目](https://github.com/buildmobile/three20rss)
*   [Three20 官网](http://three20.info/)
*   [Three20 API 文档](http://api.Three20.info/)

## 分享这篇文章