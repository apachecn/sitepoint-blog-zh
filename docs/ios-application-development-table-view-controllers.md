# iPhone 应用程序开发–表格视图控制器

> 原文：<https://www.sitepoint.com/ios-application-development-table-view-controllers/>

在这篇文章中，我们将通过一个例子来试验我们在以前的文章中学到的一些概念。我们还将学习表格视图。

我已经从 Appstore 下载了带有 iOS 7 SDK 的 XCode 5。我建议您下载 XCode 5，这样我们就可以在开始创建示例应用程序之前达成一致。

现在让我们创建一个简单的列表应用程序。

使用“空应用程序”模板创建新的应用程序“SampleListApp”。

右键单击导航器中的项目。选择“新建文件”选项。

选择“目标-C 级”模板

![template](img/2b420f46fbda6adba50585735ea8013c.png)

输入自定义视图控制器的名称“SPTTableViewController”。

我们将在这个视图中显示一个列表。因此，自定义视图控制器应该是“UITableViewController”的子类。

选择“使用 XIB 作为用户界面”选项，然后单击“下一步”

![options](img/75214c6466e00fe6205ddd5387cfa1b2.png)

选择文件位置，然后单击“创建”。

现在，视图控制器源文件和 XIB 将被创建。

记住，用户界面可以通过编程或使用 XIB 或故事板来创建。在 iOS 5 中引入故事板后，故事板的使用变得普遍而简单。但是通过 XIB 创建 UI 的知识也很重要。因此，我们将使用 XIB 来创建用户界面，直到我们了解故事板。

让我们创建一个对象数组，并在表视图中进行填充。

将此行添加到“SPTTableViewController.h”文件中

```
@property (nonatomic,strong) NSArray *arrItems;
```

将此行添加到“SPTTableViewController.m”文件中

```
@synthesize arrItems;
```

在“viewDidLoad”方法中初始化数组

```
- (void)viewDidLoad
{
    [super viewDidLoad];
    arrItems = [[NSArray alloc] initWithObjects:@"Item 1",@"Item 2",@"Item3", nil];
}
```

选择“SPTTableViewController.xib”。请注意，tableview 是作为“SPTTabeViewController”类的视图出口创建和添加的。

![tableview created](img/d0c5a0023c6a18accce74ea15e8a9b13.png)

请注意，数据源和代理出口也映射到文件所有者。

这意味着“SPTTableViewController”类负责定义表格视图中的行数或节数，并决定当选择或单击列表项或表格视图行时应该发生什么。

协议用于声明独立于任何类的方法和属性。如果“对象 A”将某些动作委托给“对象 B ”,那么对象 B 应该符合对象 A 的协议。

在我们的示例中，tableview 已将某些操作委托给“SPTTableViewController ”,因此控制器应符合“UITableViewDataSource”和“UITableViewDelegate”协议。从技术上讲，“SPTTableViewController”应实现“UITableViewDataSource”和“UITableViewDelegate”协议方法。

这些方法由 XCode 自动生成，并添加到 SPTTableViewController.m 文件中。

## UITableViewDataSource

1.方法来定义表视图中的节数

我们将创建一个包含一个部分的表格视图。

```
#pragma mark - Table view data source

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView
{
    // Return the number of sections.
    return 1;
}
```

2.方法来定义每一节中的行数

该部分中的行数应该是数组“arrItems”中的对象数

```
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
{
    // Return the number of rows in the section.
    return [arrItems count];
}
```

3.“NSIndexpath”是指表示 tableview 中的节和行的对象。下面的方法用于配置给定索引路径的单元格。

当我们创建一个 tableviewcell 时，我们给出一个标识符来表示单元格。如果单元的布局不同，我们通常给出不同的单元标识符。否则，使用单个单元标识符来表示具有相似布局的单元。

如果一个表格视图有数千行，并且只有十行可见，那么只有 10 个单元格对象将被创建。当用户滚动时，“dequereusableidentifier”方法尝试使用给定的单元格标识符重用预先存在的单元格，从而优化内存使用。例如，如果表格视图中有 10 个单元格可见，并且用户滚动查看第 11 个单元格，则第一个单元格此时将不可见，因此第 11 个单元格不是新创建的，而是第一个单元格被重用。

如果没有要重用的单元格(应该是前 10 个单元格)，则使用默认样式创建一个新单元格，并设置一个标识符来表示该单元格。

有四种现成的 tableviewcell 样式。其中之一是“UITableViewStyleDefault ”,它将在左侧有一个标签和一个可选的图像。

我们将用数组“arrItems”中的对象填充单元格标签

```
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    static NSString *CellIdentifier = @"Cell";
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:CellIdentifier];
    if (cell == nil) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier];
    }

    // Configure the cell...
    cell.textLabel.text = [arrItems objectAtIndex:indexPath.row];
    return cell;
}
```

## UITableViewDelegate

当您在表格视图中选择或单击一个项目时，此方法用于导航到下一个视图。

```
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath
{
    // Navigation logic may go here, for example:
    // Create the next view controller.
    <#DetailViewController#> *detailViewController = [[<#DetailViewController#> alloc] initWithNibName:@"<#Nib name#>" bundle:nil];

    // Pass the selected object to the new view controller.

    // Push the view controller.
    [self.navigationController pushViewController:detailViewController animated:YES];
}
```

请检查“SPTTableViewController.m”文件中的其他协议方法及其说明。

现在，让我们创建一个导航控制器，并添加上面创建的表视图控制器作为它的根视图控制器。我们将把这个导航控制器添加到 appdelegate 的窗口对象中。

如下所示，更改您的 appdelegate 头文件和实现文件

**SPTAppDelegate.h**

```
#import <UIKit/UIKit.h>
@class SPTTableViewController;
@interface SPTAppDelegate : UIResponder <UIApplicationDelegate>

@property (strong, nonatomic) UIWindow *window;
@property (strong, nonatomic) UINavigationController *navController;
@property (strong, nonatomic) SPTTableViewController *vController;
@end
```

**SPTAppDelegate.m**

```
#import "SPTAppDelegate.h"
#import "SPTTableViewController.h"
@implementation SPTAppDelegate
@synthesize navController,vController;
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    // Override point for customization after application launch.
    self.window.backgroundColor = [UIColor whiteColor];
    vController = [[SPTTableViewController alloc] initWithNibName:@"SPTTableViewController" bundle:nil];
    navController = [[UINavigationController alloc] initWithRootViewController:vController];

    self.window.rootViewController = navController;
    [self.window makeKeyAndVisible];
    return YES;
}
```

现在在 iOS 7 模拟器上运行应用程序

![ios7 simulator](img/5fd5a035a83a81fbe3b9fdd05c179809.png)

现在将节数改为“2”并运行应用程序。

![add section 2](img/38d03badb10b42471486cdb98f800c0d.png)

indexpath 对象将具有如下的节和行值

第一节(0，0)，(0，1)，(0，2)
第二节(1，0)(1，1)，(1，2)

因为我们基于 indexpath 的 row 属性显示数组对象，所以这些值是重复的。

分部在哪里？为了区分表格视图中的各个部分，我们应该改变表格视图的样式。

更改中的样式。xib 文件，并在模拟器中运行应用程序。

![change xib file](img/fe4b213a8cf0ddbdc98f340f1dfd459c.png)

![run simulator again](img/9941d0a2303278ce523cb7aea2b4864a.png)

## 结论

我们已经了解了表格视图及其代表和协议。在我的下一篇文章中，我们将扩展这个例子，看看如何在视图控制器之间导航。

## 分享这篇文章