# 使用 Parse 为您的 iOS 应用程序创建云后端

> 原文：<https://www.sitepoint.com/creating-cloud-backend-ios-app-using-parse/>

应用程序通常需要一个后端来保存数据，与其他用户共享数据，并在用户的设备之间同步数据。

构建这个后端可能需要时间、不同的技能和其他资源(例如，服务器、推送通知服务等)。幸运的是，有几个平台提供现成的可定制后端，您可以将其与您的应用程序集成。这些被称为“后端即服务”，简称为 *BaaS* 。

## 使用 BaaS 的利弊

### 赞成的意见

*   节省您的时间和资源。
*   在一个包中提供不同的服务。大多数可用的 BaaS 供应商不仅为您的数据提供后端云存储，还提供推送通知、分析、社交网络集成等服务。
*   迎合缩放。BaaS 平台是为扩展而构建的，作为一名开发者，如果你获得了大量的用户，你不需要做任何额外的工作，只需要支付更多的费用。
*   容易做出改变。您可以轻松地更改应用程序的功能，而无需对其后端进行大量重写。使用平台的仪表板可以很容易地更改数据库模型。这对任何遵循迭代“精益启动方法”的人来说都很方便，这种方法可以根据使用数据来发布和改进应用程序。

### 骗局

*   使用 BaaS 可能会很昂贵。虽然这些服务中的大多数为每月一定数量的请求提供免费套餐。如果你的应用要取得成功并迅速获得许多用户，事情可能会突然变得很昂贵。
*   您可能会遇到供应商锁定，很难切换到另一个平台。这不再是一个问题，因为现在大多数服务都允许更容易的迁移。您可能不得不放弃一些功能，因为并非所有的 BaaS 平台都提供相同的功能。
*   在使用 BaaS 之前要考虑的一个巨大缺点是你的应用依赖于第三方服务。提供商可能会对其服务进行更改(例如，更改定价计划)，您必须接受或寻找其他解决方案。服务也有可能被关闭。这是一个巨大的不便，因为你将被迫迁移你的数据到另一个供应商或建立自己的后端。这方面的一个例子是最近 StackMob 的关闭，这迫使开发人员迁移他们的数据。

## 输入解析

在本教程中，我们将看一下[解析](https://parse.com/)来为 iOS 应用程序提供后端。我们将简要地看一下它提供了什么，然后构建一个应用程序，使用它进行用户注册、身份验证和用户数据存储。

Parse 是比较流行的后端即服务平台之一。该服务在一个包中提供三种产品:解析核心、解析推送和解析分析。

Parse Core 通常处理数据保存和社交媒体集成。它还使您能够编写自己的代码，这些代码将在云中运行，从而提供定制的服务器端逻辑。

解析推送用于发送推送通知。它使开发人员能够定制、调度和发送推送通知给所有注册用户或一组选定的用户。

Parse Analytics 使您能够跟踪应用程序的数据。您可以跟踪使用数据，如安装，活跃用户，用户保留，推送通知打开率等。

首先，[下载](https://github.com/sitepoint-examples/parse_ios)项目文件，其中包括一个起始项目和一个完成的最终项目。该应用程序是一个简单的笔记应用程序，使用户能够将笔记保存到云中并从中检索，从而在不同的设备上维护相同的数据。

该项目由登录、注册、注释表视图和添加/编辑注释视图组成，如下所示。

![Our app overview](img/ab352e61f2cd9d62cc3d6f18c3f8c667.png)

在运行应用程序时，您会看到一个空的表格视图，导航栏上有注销和添加按钮。应用程序应该要求用户在使用它之前登录。如果用户已登录，它会直接将他们带到显示已保存笔记列表的表格视图。否则将会显示登录视图。如果用户没有注册，那么他们可以选择注册。

首先，如果你还没有账户，你应该先在[parse.com](https://parse.com/)上创建一个账户。登录后，进入[仪表盘](https://www.parse.com/apps)，在那里你可以创建一个新的应用程序，还可以看到你所有应用程序的列表。创建一个名为 *NoteApp* 的应用。

![Create a new app in Parse](img/059df64eb79e7f2dcf71b095dc02b820.png)

创建应用程序时，您会看到一个包含应用程序 id 和密钥的窗口。这些将在 iOS 应用程序中使用。

![App overview in Parse](img/d759198d2abbdf1d2e58e594aa532a51.png)

点击下载解析 SDK [。解压缩文件并将框架拖到项目的框架组文件夹中。](https://www.parse.com/downloads/ios/parse-library/latest)

![Frameworks folder in iOS project](img/25a3a907d9be92c722b17da5c9653466.png)

接下来，我们需要向项目中添加一些库。在项目导航器上选择您的项目，确保选择了项目目标，然后单击“构建阶段”选项卡。展开“将二进制文件与库链接”。

![Add frameworks to your iOS App](img/bbfce38d85ac8dc58fbf43335ed6e0a3.png)

单击所列框架底部的添加图标，并添加以下库。

*   音频工具箱.框架
*   CFNetwork.framework
*   CoreGraphics.framework
*   CoreLocation.framework
*   莉比·迪莉比
*   MobileCoreServices.framework
*   QuartzCore.framework
*   安全性.框架
*   StoreKit.framework
*   系统配置.框架

打开`AppDelegate.m`文件，将以下导入添加到文件顶部，并对`application:didFinishLaunchingWithOptions:`进行以下修改，将方法放入您的应用 id 和客户端密钥中。

```
#import <Parse/Parse.h>

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    [Parse setApplicationId:@"YOUR APP ID"
                  clientKey:@"YOUR CLIENT KEY"];
    return YES;
}
```

为了测试 SDK，将下面几行粘贴到在`application:didFinishLaunchingWithOptions:`中设置应用程序 id 和客户端密钥的方法调用下面。

```
PFObject *testObject = [PFObject objectWithClassName:@"TestObject"];
testObject[@"foo"] = @"bar";
[testObject saveInBackground];
```

运行应用程序并导航至浏览器上的解析仪表盘。选择您的应用程序，然后单击数据浏览器选项卡。您应该会看到上面创建的对象的表格数据。

![App details in Parse dashboard](img/1b71a6eafb388065668dadcc11de293e.png)

在仪表板中，您可以找到添加/删除行和列、设置权限、导出类甚至删除整个类的控件。丢弃`TestObject`类，因为我们的应用程序不需要它。点击*更多*按钮并选择*掉落等级*即可掉落。还要删除应用程序启动时创建`TestObject`的代码。

您不仅可以像我们上面所做的那样以编程方式创建对象，还可以使用仪表板来这样做。我们将创建一个`Post`类和一些将加载到我们的应用程序中的测试帖子。

点击*新建类*按钮，并将类命名为`Post`。将类型保留为*自定义*。增加两个字符串列:`title`和`content`。添加几行数据，只需填写标题和内容字段。

除了`title`和`content`列，还生成了其他列——*objectId*、 *createdAt* 、 *updatedAt* 和 *ACL* 。ACL 代表访问控制列表。这些用于指定用户和/或角色对特定对象的访问控制。

回到我们的应用程序，我们将为 notes 表视图创建一个视图控制器。Parse 有一个方便的类，它是名为`PFQueryTableViewController`的`UITableViewController`的子类。我们将使用它提供的一些功能，如拉刷新和分页(这使您能够设置每个查询获得的结果数)。

创建一个新类。我给我的取名`JKENotesListViewController`。应该是`PFQueryTableViewController`的子类。

James Yu，Parse [的创建者之一，为一个`PFQueryTableViewController`模板写了一个要点](https://gist.github.com/jamesyu/ba03c1a550f14f88f95d)，我用它来代替从头开始写所有的代码。我已经用`initWithCoder:`替换了模板的初始化器`initWithStyle:`，因为我们使用的是故事板。

以下是`JKENotesListViewController.m`中的代码

```
//  JKENotesListViewController.m
//  NoteApp
//
//  Created by Joyce Echessa on 6/5/14.
//  Copyright (c) 2014 Joyce Echessa. All rights reserved.
//

#import "JKENotesListViewController.h"

@interface JKENotesListViewController ()

@end

@implementation JKENotesListViewController

    - (id)initWithCoder:(NSCoder *)aDecoder
    {
        self = [super initWithClassName:@"Post"];
        self = [super initWithCoder:aDecoder];
        if (self) {
            // The className to query on
            self.parseClassName = @"Post";

            // Whether the built-in pull-to-refresh is enabled
            self.pullToRefreshEnabled = YES;

            // Whether the built-in pagination is enabled
            self.paginationEnabled = YES;

            // The number of objects to show per page
            self.objectsPerPage = 15;
        }
        return self;
    }

    #pragma mark - UIViewController

    - (void)viewDidLoad {
        [super viewDidLoad];

        // Uncomment the following line to preserve selection between presentations.
        // self.clearsSelectionOnViewWillAppear = NO;

        // Uncomment 
        // self.navigationItem.rightBarButtonItem = self.editButtonItem;
    }

    #pragma mark - PFQueryTableViewController

    // Override to customize the look of a cell representing an object. The default is to display
    // a UITableViewCellStyleDefault style cell with the label being the textKey in the object,
    // and the imageView being the imageKey in the object.
    - (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath object:(PFObject *)object {
        static NSString *CellIdentifier = @"Cell";

        PFTableViewCell *cell = (PFTableViewCell *)[tableView dequeueReusableCellWithIdentifier:CellIdentifier];
        if (cell == nil) {
            cell = [[PFTableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier];
        }

        NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];
        [dateFormatter setDateFormat:@"EEEE, MMMM d yyyy"];
        NSDate *date = [object createdAt];

        // Configure the cell
        cell.textLabel.text = [object objectForKey:@"title"];
        cell.detailTextLabel.text = [dateFormatter stringFromDate:date];
        cell.imageView.image = [UIImage imageNamed:@"note"];

        return cell;
    }

    #pragma mark - UITableViewDelegate

    - (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
        [super tableView:tableView didSelectRowAtIndexPath:indexPath];
    }
@end
```

要将该类标识为场景的视图控制器，请从故事板中选择表格视图控制器。在“身份检查器”选项卡上，从“类别”菜单中选择`JKENotesListViewController`。

![Adding a custom class](img/309d02dd91e8f05a6e28fea2f114e7ea.png)

运行该应用程序，您应该会看到一个列表，显示您在 Parse dashboard 上创建的笔记的标题。

![Notes screen listing](img/c68600da8eabfa3ec1c169c8c9aafe79.png)

就像在任何数据库查询语言中一样，您可以指定您得到的结果。在上面的例子中，默认查询按照文章保存的顺序返回文章，没有任何过滤器。稍后，我们将只获得登录用户发布的帖子。

在上面的代码中，`initWithCoder:`用与表相关联的 PFObjects 的类名初始化，在我们的例子中，它是我们创建的`Post`类。我们还设置了一些参数，通过下拉列表和激活分页来刷新表数据，分页将每个查询返回的最大结果数设置为 15。

如果您希望表格单元格的样式不同于默认样式，可以覆盖`tableView:cellForRowAtIndexPath:object:`方法。在上面的例子中，我们将每个单元格配置为显示图像、笔记标题和创建日期(作为副标题)。如果您下载了 starter 项目，则使用的图像位于支持文件组文件夹中。在故事板文件中，表格原型单元格有一个标识符`Cell`，它的附件被设置为`Disclosure Indicator`，样式被设置为`Subtitle`。这些设置位于属性检查器中。

![Editing Table cell view](img/4ad7047cdb40caf2322a5c4d452529c9.png)

我们现在可以保存笔记了。在导航栏上，有一个“添加”按钮，单击该按钮将显示“添加/编辑”视图。为了保持应用程序的简单，我将使用相同的视图来添加、编辑和查看笔记。

为视图创建一个控制器。我给我的取名`JKENotesViewController`。它是`UIViewController`的子类。通过选择故事板中的添加/编辑视图控制器并将自定义类设置为“身份检查器”选项卡上的`JKENotesViewController`，将该类设置为添加/编辑注释场景的视图控制器。

在编辑新类之前，将以下内容添加到`JKENotesListViewController.m`文件中。如果你看一下你的故事板，有两个推片段通向添加/编辑视图控制器。我给它们取名为`addNote`和`showNote`。`addNote`连接到添加导航栏按钮而`showNote`连接到表格视图中的一个单元格。

下面的代码检查哪个 segue 被激活了，如果是【segue，它将与所选单元格相关联的`PFObject`实例传递给`JKENotesViewController`。这样，您可以在视图控制器之间传递数据。

```
- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender
{
    if ([segue.identifier isEqualToString:@"showNote"]) {
        NSIndexPath *indexPath = [self.tableView indexPathForSelectedRow];
        PFObject *object = [self.objects objectAtIndex:indexPath.row];

        JKENotesViewController *note = (JKENotesViewController *)segue.destinationViewController;
        note.note = object;
    }
}
```

将以下属性添加到“JKENotesViewController.h”文件中

```
@property (nonatomic, strong) PFObject *note;
```

将以下网点添加到`JKENotesViewController`的实施文件中。`titleTextField`用于添加/编辑视图控制器上的文本字段，而`contentTextView`用于文本视图。从“保存”按钮创建操作连接。我给我的取名`saveWasPressed`。(我假设读者知道如何做到这一点。如果不是[，查看本指南中的行动](https://developer.apple.com/library/ios/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingAction.html)和本指南中的插座。

```
@interface JKENotesViewController ()

    @property (weak, nonatomic) IBOutlet UITextField *titleTextField;
    @property (weak, nonatomic) IBOutlet UITextView *contentTextView;

@end
.
.
@implementation JKENotesViewController
    - (IBAction)saveWasPressed:(id)sender {
    }
@end
```

我对`JKENotesViewController.m`文件做了如下修改。

```
//
//  JKENotesViewController.m
//  NoteApp
//
//  Created by Joyce Echessa on 6/6/14.
//  Copyright (c) 2014 Joyce Echessa. All rights reserved.
//

#import "JKENotesViewController.h"

@interface JKENotesViewController ()

    @property (weak, nonatomic) IBOutlet UITextField *titleTextField;
    @property (weak, nonatomic) IBOutlet UITextView *contentTextView;

@end

@implementation JKENotesViewController

    - (void)viewDidLoad
    {
        [super viewDidLoad];

        // Check to see if note is not nil, which let's us know that the note
        // had already been saved.
        if (self.note != nil) {
            self.titleTextField.text = [self.note objectForKey:@"title"];
            self.contentTextView.text = [self.note objectForKey:@"content"];
        }
    }

    - (IBAction)saveWasPressed:(id)sender {

        NSString *title = [self.titleTextField.text stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]];

        if ([title length] == 0) {
            UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:@"Error!"
                                                        message:@"You must at least enter a title"
                                                       delegate:nil cancelButtonTitle:@"OK" otherButtonTitles:nil];
            [alertView show];
        }
        else {

            if (self.note != nil) {
                [self updateNote];
            }
            else {
                [self saveNote];
            }
        }

    }

    - (void)saveNote
    {

        PFObject *newNote = [PFObject objectWithClassName:@"Post"];
        newNote[@"title"] = self.titleTextField.text;
        newNote[@"content"] = self.contentTextView.text;

        [newNote saveInBackgroundWithBlock:^(BOOL succeeded, NSError *error) {
            if (succeeded) {
                [self.navigationController popViewControllerAnimated:YES];
            } else {
                UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:@"Error!"
                                                            message:[error.userInfo objectForKey:@"error"]
                                                           delegate:nil cancelButtonTitle:@"OK" otherButtonTitles:nil];
                [alertView show];
            }
        }];

    }

    - (void)updateNote
    {

        PFQuery *query = [PFQuery queryWithClassName:@"Post"];

        // Retrieve the object by id
        [query getObjectInBackgroundWithId:[self.note objectId] block:^(PFObject *oldNote, NSError *error) {

            if (error) {
                UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:@"Error!"
                                                            message:[error.userInfo objectForKey:@"error"]
                                                           delegate:nil cancelButtonTitle:@"OK" otherButtonTitles:nil];
                [alertView show];
            }
            else {
                oldNote[@"title"] = self.titleTextField.text;
                oldNote[@"content"] = self.contentTextView.text;

                [oldNote saveInBackgroundWithBlock:^(BOOL succeeded, NSError *error) {
                    if (succeeded) {
                        [self.navigationController popViewControllerAnimated:YES];
                    } else {
                        UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:@"Error!"
                                                                    message:[error.userInfo objectForKey:@"error"]
                                                                   delegate:nil cancelButtonTitle:@"OK" otherButtonTitles:nil];
                        [alertView show];
                    }
                }];
            }

        }];

    }

@end
```

在`viewDidLoad`中，检查`self.note`是否为空。如果用户点击了一个注释，那么 post 对象通过前面讨论的`prepareForSegue:sender:`方法被传递给这个视图控制器。如果传入了一个帖子，那么我们用对象的`title`和`content`数据分别设置标题*文本字段*和内容*文本视图*。

当按下保存按钮时，进行类似的检查，以确定是否将通过调用`saveNote`创建新便笺，或者是否将通过调用`updateNote`更新现有便笺。

在`saveNote`中，一个`PFObject`被创建并保存，以便用`saveInBackgroundWithBlock:`方法进行解析。此方法异步保存数据，因此在保存完成之前，您的应用不会被阻止。如果网络很慢，您可以导航回 notes 表视图，数据仍然会被保存。Parse 还有一个`saveEventually`方法，如果您不需要知道保存何时完成，可以使用这个方法。如果没有网络连接，`saveEventually`将在设备上存储更新，直到网络连接重新建立，如果应用程序在连接恢复前关闭，Parse 将在下次打开应用程序时再次尝试。

在`updateNote`方法中，我们使用其 *objectId* 检索要更新的 Post 对象，并使用更新后的数据调用`saveInBackgroundWithBlock:`。

当保存笔记时，应用程序会返回到笔记表格视图，但您会注意到表格不会更新以显示新创建的笔记。您可以下拉列表来刷新视图，但是我们希望用户看到创建的注释，而不需要手动刷新视图。

在`JKENotesListViewController.m`中添加以下内容，一旦表格视图回到视图中，这些内容将重新加载对象。

```
- (void)viewWillAppear:(BOOL)animated {
    [super viewWillAppear:animated];
    [self loadObjects];
}
```

运行应用程序，现在当添加或编辑注释时，将在表格视图中看到更改。

该应用程序正在保存和检索帖子，但任何人都可以使用它，并看到后端保存的所有笔记。我们将应用程序的使用限制为登录用户，也使用户只能访问他们的笔记。

对`JKENotesListViewController.m`中的`viewDidLoad`进行如下修改

```
- (void)viewDidLoad {
    [super viewDidLoad];

    PFUser *currentUser = [PFUser currentUser];
    if (currentUser) {
        NSLog(@"Current user: %@", currentUser.username);
    }
    else {
        [self performSegueWithIdentifier:@"showLogin" sender:self];
    }
}
```

由于创建用户帐户是应用程序中的一个常见需求，Parse 提供了`PFUser`类，该类自动处理用户帐户管理所需的大部分功能。

在上面的例子中，我们检查用户是否登录。`currentUser`方法从磁盘获取当前登录的用户，并返回它的一个实例。如果用户登录，我们记录他们的用户名，视图照常加载。如果用户没有登录，将会显示登录视图。在 starter 项目中，我已经创建了一个登录视图控制器的 segue，并给了它一个标识符`showLogin`。

创建一个新类，并使其成为`UIViewController`的子类。我给我的取名`JKELoginViewController`。在故事板上选择登录视图控制器，并在 Identity Inspector 选项卡上，将其自定义类设置为`JKELoginViewController`。

从用户名和密码文本字段创建插座连接，从登录按钮创建操作连接。下面显示了对`JKELoginViewController.h`文件的更改。

```
@interface JKELoginViewController : UIViewController

    @property (weak, nonatomic) IBOutlet UITextField *usernameTextField;
    @property (weak, nonatomic) IBOutlet UITextField *passwordTextField;

    - (IBAction)login:(id)sender;

@end
```

然后，我对`JKELoginViewController.m`文件做了如下修改。

```
//
//  JKELoginViewController.m
//  NoteApp
//
//  Created by Joyce Echessa on 6/6/14.
//  Copyright (c) 2014 Joyce Echessa. All rights reserved.
//

#import "JKELoginViewController.h"
#import <Parse/Parse.h>

@interface JKELoginViewController ()

@end

@implementation JKELoginViewController

    - (void)viewDidLoad
    {
        [super viewDidLoad];

        self.navigationItem.hidesBackButton = YES;
    }

    - (IBAction)login:(id)sender {
        NSString *username = [self.usernameTextField.text stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]];
        NSString *password = [self.passwordTextField.text stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]];

        if ([username length] == 0 || [password length] == 0) {
            UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:@"Error!"
                                                        message:@"You have to enter a username and password"
                                                       delegate:nil cancelButtonTitle:@"OK" otherButtonTitles:nil];
            [alertView show];
        }
        else {

            [PFUser logInWithUsernameInBackground:username password:password block:^(PFUser *user, NSError *error) {
                if (error) {
                    UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:@"Error!"
                                                                message:[error.userInfo objectForKey:@"error"]
                                                               delegate:nil cancelButtonTitle:@"OK" otherButtonTitles:nil];
                    [alertView show];
                }
                else {
                    [self.navigationController popToRootViewControllerAnimated:YES];
                }
            }];
        }
    }

@end
```

在`viewDidLoad`中，我们隐藏了导航栏的后退按钮。我们不希望用户能够导航回 notes 表视图。

`login:`方法检查用户名和密码字段是否已经填写，然后尝试验证用户。如果认证成功，将调用根视图控制器。在我们的例子中，这是显示注释列表的表格视图控制器。

我们现在将创建一个控制器来处理用户注册。创建一个`UIViewController`子类。我将 mine 命名为`JKESignupViewController`，并将其设置为 Identity Inspector 选项卡上的注册视图控制器的自定义类。

创建以下出口和动作连接。在`JKESignupViewController.h`

```
@interface JKESignupViewController : UIViewController

    @property (weak, nonatomic) IBOutlet UITextField *usernameTextField;
    @property (weak, nonatomic) IBOutlet UITextField *emailTextField;
    @property (weak, nonatomic) IBOutlet UITextField *passwordTextField;

    - (IBAction)signup:(id)sender;

@end
```

在`JKESignupViewController.m`中进行以下更改。

```
//
//  JKESignupViewController.m
//  NoteApp
//
//  Created by Joyce Echessa on 6/6/14.
//  Copyright (c) 2014 Joyce Echessa. All rights reserved.
//

#import "JKESignupViewController.h"
#import <Parse/Parse.h>

@interface JKESignupViewController ()

@end

@implementation JKESignupViewController

    - (void)viewDidLoad
    {
        [super viewDidLoad];
        // Do any additional setup after loading the view.
    }

    - (IBAction)signup:(id)sender {
        NSString *username = [self.usernameTextField.text stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]];
        NSString *password = [self.passwordTextField.text stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]];
        NSString *email = [self.emailTextField.text stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]];

        if ([username length] == 0 || [password length] == 0 || [email length] == 0) {
            UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:@"Error!"
                                                        message:@"You have to enter a username, password, and email"
                                                       delegate:nil cancelButtonTitle:@"OK" otherButtonTitles:nil];
            [alertView show];
        }
        else {
            PFUser *newUser = [PFUser user];
            newUser.username = username;
            newUser.password = password;
            newUser.email = email;

            [newUser signUpInBackgroundWithBlock:^(BOOL succeeded, NSError *error) {
                if (error) {
                    UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:@"Error!"
                                                                message:[error.userInfo objectForKey:@"error"]
                                                               delegate:nil cancelButtonTitle:@"OK" otherButtonTitles:nil];
                    [alertView show];
                }
                else {
                    [self.navigationController popToRootViewControllerAnimated:YES];
                }
            }];
        }
    }

@end
```

`signup:`方法检查是否所有字段都已填充，然后创建一个`PFUser`对象并保存它，以便用`signUpInBackgroundWithBlock:`方法解析。如果注册成功，用户将被带到带有注释列表的表格视图。用一些数据运行并测试应用程序。

现在，我们将允许用户能够注销。从注销按钮添加动作连接。

在`JKENotesListViewController.h`

```
@interface JKENotesListViewController : PFQueryTableViewController

    - (IBAction)logout:(id)sender;

@end
```

在`JKENotesListViewController.m`中添加以下内容。

```
- (IBAction)logout:(id)sender {
    [PFUser logOut];
    [self performSegueWithIdentifier:@"showLogin" sender:self];
}
```

现在用户可以注销了。

我们将创建帖子和用户之间的关系。在`JKENotesViewController.m`中，在`saveNote`方法中设置了`newNote[@"content"]`之后添加以下语句。

```
newNote[@"author"] = [PFUser currentUser];
```

添加新便笺时，当前登录的用户将被保存为其作者。在第一次运行时，author 列将在保存数据之前在 Parse 中自动创建，因此在使用它之前，您不需要使用仪表板来创建它。

在`JKENotesListViewController.m`文件中添加以下方法，定制发送给 Parse 的查询，以便只检索登录用户的帖子。

```
// Override to customize what kind of query to perform on the class. The default is to query for
	// all objects ordered by createdAt descending.
	- (PFQuery *)queryForTable {

    	// Create a query
    	PFQuery *query = [PFQuery queryWithClassName:self.parseClassName];

    	// Follow relationship
    	if ([PFUser currentUser]) {
        	[query whereKey:@"author" equalTo:[PFUser currentUser]];
    	}
    	else {
        	// I added this so that when there is no currentUser, the query will not return any data
        	// Without this, when a user signs up and is logged in automatically, they briefly see a table with data
        	// before loadObjects is called and the table is refreshed.
        	// There are other ways to get an empty query, of course. With the below, I know that there
        	// is no such column with the value in the database.
        	[query whereKey:@"nonexistent" equalTo:@"doesn't exist"];
    	}

    	return query;
	}
```

运行并测试应用程序。您保存的任何新笔记现在都将与您相关联，并且只有您能够看到您的笔记。

我们的 app 可以保存和编辑笔记，但是不能删除。我不会添加删除功能，但是如果你想删除任何对象，你可以使用`deleteInBackground`方法。

```
[myObject deleteInBackground];
```

## 结论

我们已经了解了如何使用 Parse 作为应用程序的 BaaS 系统。使用这种现成的后端服务有许多优点，但它也有一些缺陷，在决定是从头构建还是使用 BaaS 解决方案时，应该根据这些优点来衡量这些缺陷。

即使您计划构建自己的后端，也值得从 BaaS 开始，以减少应用程序上市的时间，并在市场上测试应用程序，并根据使用数据快速验证功能。

除了 Parse，还有几个这样的解决方案/服务，比如 [Apigee](http://apigee.com/about/) 、 [Backendless](https://backendless.com/) 、 [Kii](http://en.kii.com/) 、[build . io](https://www.built.io/)、 [Firebase](https://www.firebase.com/) 。这些以不同的价格提供不同的服务，值得看看和比较不同的产品来决定什么最适合你的需要。

## 分享这篇文章