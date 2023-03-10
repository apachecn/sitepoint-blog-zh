# 在 iOS 应用程序和 WatchKit 之间共享数据的 3 个选项

> 原文：<https://www.sitepoint.com/3-options-sharing-data-ios-apps-watchkit/>

在这个简短系列的最后一部分，我们看了 WatchKit 扩展和它们的主机 iOS 应用程序之间的通信选项。在这最后一部分，我们来看看这些选项的编码。

这将是一个很长的教程，所以你可能会发现参考 GitHub 上的[完整项目很有用。](https://github.com/sitepoint-editors/ios-watchkit-messge-passing)

打开 Xcode，新建一个*单视图应用*项目。给它一个相关的名字，并将语言设置为 *Objective-C* 。

![New Project](img/aff835dc6dfeeedc866040f649a2b1c7.png)

选择*文件- >新建- >目标*菜单选项。选择 *WatchKit app* 模板。

![New Target](img/7a3c9e1af4d6c3fad5c9bec3636f6713.png)

点击*下一步*，取消选择创建*通知场景*或*扫视场景*的选项。

在随后的对话框中点击*完成*和*激活*,向 Xcode 项目添加一个新的目标，为 WatchKit 应用和扩展创建新的组。

我们将从开发我们的父 iOS 应用程序开始。展开 iOS 应用程序的“类”组:

![iOS application classes group](img/64db3c4277f825749fe72fbcbb1df957.png)

我将首先把我的父节点`ViewController`从‘view controller’重命名为描述性的名称，比如‘ToDoListViewController’:

![refactoring](img/ede302e14ac536ebf4e2de8efe2251f1.png)

![refactoring](img/cc805ceaf932c3c9d17cda7f70c60509.png)

![refactoring](img/fb21525de6f82b9337ee251aca5c054e.png)

在这个应用中，我们将使用一个`TableView`来显示待办事项列表。为了使`TableView`的配置更容易，我们将从`UITableViewController`类中创建一个新的子类。通过拥有这个子类，Xcode 为我们需要的大多数方法插入了方法签名，用于`TableView`数据源和`TableView`委托。

首先删除当前的 *ToDoListViewController.h* 和 *ToDoListViewController.m* ，然后选择*文件- >新建- >文件*菜单选项。挑选*可可触摸类*模板:

点击*下一个*，调用类*ToDoListTableViewController*，并确认它是`UITableViewController`的子类:

打开新的*todolisttableviewcontroller . m*，你会看到 Xcode 自动创建的方法签名。这些方法应该被实现，因为`ToDoListTableViewController`类接口符合`UITableViewDataSource`和`UITableViewDelegate`协议。

```
- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
  #warning Incomplete implementation, return the number of sections
  return 0;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
  #warning Incomplete implementation, return the number of rows
  return 0;
}
```

为了简单起见，我不会为我们的待办事项使用任何持久数据存储。我将使用一个`NSMutableArray`来处理我们的待办事项列表中的项目。通过在*todolisttableviewcontroller . m*内的`ToDoListTableViewController()`接口内添加一个新属性来定义这个`NSMutableArray`:

```
@interface ToDoListTableViewController ()
@property (nonatomic, strong) NSMutableArray *toDoListItems;
@end
```

为初始化创建一个方法，并在`viewDidLoad`中调用它。这个方法叫做`initializeValues`，它初始化`toDoListItems NSMutableArray`:

```
- (void)viewDidLoad {
    [super viewDidLoad];
    [self initializeValues];
}

- (void)initializeValues {
    self.toDoListItems = [NSMutableArray array];
}
```

下一步是开始制作故事板。在 *Main.storyboard* 中，你会发现一个默认创建的普通`ViewController`。删除它，并从右侧面板(实用区域)拖动一个“表格视图控制器”到故事板场景中。

在表格视图控制器中嵌入导航控制器是一个很好的做法。这将允许一个导航栏，用于使用 segues 与新的视图控制器进行交互。要嵌入导航控制器，选择刚刚添加的“表格视图控制器”和*编辑器- >嵌入- >导航控制器*菜单项。

在故事板中，选择导航控制器，并从 Xcode 的右侧面板“实用程序面板”中打开“属性”标签。在“视图控制器”部分，你会发现一个名为“是初始视图控制器”的选项。将其标记为正确，使“TableViewController”成为应用程序启动时出现的第一个屏幕。

![Initial View Controller](img/d1f8ba678592629139a023530013e6ab.png)

在`Main.storyboard`中，通过*身份检查器*中的*类*文本框将`ToDoListTableViewController`类分配给表格视图控制器。你可以通过从故事板中选择“TableViewController”并打开“Utilities”右面板来实现。

![Assign Custom Class](img/6f3ecc75496a51ae0158dc9a9c4031f9.png)

为了添加一个新的待办事项，我们将使用一个`UITextField`来输入文本，当按下 *Done* 时，向表格视图添加一个新的任务。

`UITextField`在自定义视图中，将作为表格视图的标题。这将确保输入文本字段总是在表格视图的顶部，并使添加新的待办事项变得简单。

创建一个返回待办事项表格视图的标题视图的方法，然后定义一个常数来保存表格视图所需的单元格高度值。我们将对标题视图使用相同的高度，使整个 UI 保持一致。将此方法添加到*todolisttableviewcontroller . m*:

```
- (UIView*)toDoListTableViewHeader {
    UIView *tableViewHeader = [[UIView alloc] initWithFrame:CGRectMake(0, 0, self.tableView.frame.size.width, TABLE_VIEW_CELL_HEIGHT)];
    tableViewHeader.backgroundColor = [UIColor lightGrayColor];

    return tableViewHeader;
}
```

定义在开始的`#import`语句下面使用的常数`TABLE_VIEW_CELL_HEIGHT`:

```
#import "ToDoListTableViewController.h"
#define TABLE_VIEW_CELL_HEIGHT 40
```

将从`toDoListTableViewHeader`方法返回的`UIView`赋给`tableView`的`tableHeaderView`属性。我们将在`initializeValues`方法中这样做:

```
- (void)initializeValues {
    self.toDoListItems = [NSMutableArray array];
    self.tableView.tableHeaderView = [self toDoListTableViewHeader];
}
```

我将取消我的*todolisttableviewcontroller . m*类的`viewDidLoad`方法内的行`self.navigationItem.rightBarButtonItem = self.editButtonItem;`的注释。这在导航栏的右边创建了一个*编辑*按钮。该*编辑*按钮可用于修改待办表格视图。修改的一种方法是删除待办事项。我也将通过这篇文章谈论更多关于删除待办事项的内容。

```
- (void)viewDidLoad {
    [super viewDidLoad];

    // Uncomment the following line to preserve selection between presentations.
    // self.clearsSelectionOnViewWillAppear = NO;

    // Uncomment the following line to display an Edit button in the navigation bar for this view controller.
    self.navigationItem.rightBarButtonItem = self.editButtonItem;

    [self initializeValues];
}
```

这应该是运行项目后的结果:

![First App Run](img/081b9a1f1ce3ef21e0f349ea1f805342.png)

接下来，我们将设置每个部分的行数来保存`toDoListItems`数组的计数，并将部分的数量设置为 1。用以下方法替换当前方法:

```
- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    // Return the number of sections.
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    // Return the number of rows in the section.
    return self.toDoListItems.count;
}
```

然后在`ToDoListTableViewController()`接口中定义一个`UITextField`属性，初始化它并配置返回待办事项表视图头的方法。我们将使`ToDoListTableViewController`类头符合`UITextFieldDelegate`协议，这样它就可以与新的`UITextField`交互:

Inside *ToDoListTableViewController.h* :

```
@interface ToDoListTableViewController : UITableViewController <UITextFieldDelegate>

@end
```

通过修改这些现有函数，将*todolisttableviewcontroller . m*设置为该`UITextField`的代表:

```
@interface ToDoListTableViewController ()
@property (nonatomic, strong) NSMutableArray *toDoListItems;
@property (nonatomic, strong) UITextField *toDoInputTextField;
@end

…

- (void)initializeValues {
    self.toDoListItems = [NSMutableArray array];
    self.tableView.tableHeaderView = [self toDoListTableViewHeader];
    self.toDoInputTextField.delegate = self;
}

…

- (UIView*)toDoListTableViewHeader {
    UIView *tableViewHeader = [[UIView alloc] initWithFrame:CGRectMake(0, 0, self.tableView.frame.size.width, TABLE_VIEW_CELL_HEIGHT)];
    tableViewHeader.backgroundColor = [UIColor lightGrayColor];

    self.toDoInputTextField = [[UITextField alloc] initWithFrame:CGRectMake(0, 0, tableViewHeader.frame.size.width * 0.95, TABLE_VIEW_CELL_HEIGHT * 0.8)];
    self.toDoInputTextField.center = CGPointMake(tableViewHeader.center.x, tableViewHeader.center.y);
    self.toDoInputTextField.placeholder = @"Add an item..";
    self.toDoInputTextField.backgroundColor = [UIColor whiteColor];
    self.toDoInputTextField.borderStyle = UITextBorderStyleRoundedRect;
    self.toDoInputTextField.returnKeyType = UIReturnKeyDone;

    [tableViewHeader addSubview:self.toDoInputTextField];

    return tableViewHeader;
}
```

这将是添加此`UITextField`作为表格视图标题的子视图后的最终结果:

![Sub View](img/611d88114d059a1473d9895ca29891f5.png)

你可以开始输入任何待办事项，但是当你按下*完成*按钮时，什么也没有发生。键盘应该隐藏起来，并添加新的待办事项。为了实现这一点，我们将实现一个`UITextField`委托方法，使键盘在按下*完成*按钮时隐藏起来:

```
- (BOOL)textFieldShouldReturn:(UITextField *)textField {
    [textField resignFirstResponder];
    return YES;
}
```

我们将`UITextField`重新分配为第一响应者，这使得键盘隐藏起来。

为了简单起见，我们将使用现有的`textFieldShouldReturn:`方法，并编写在 to-dos 表视图中添加新的 to-do 项目的逻辑。我们将检查输入文本字段是否包含空文本，并将该文本作为对象添加到`toDoListItems`数组中。之后，重新加载表格视图数据，这将显示添加到`toDoListItems`数组中的所有项目:

```
- (BOOL)textFieldShouldReturn:(UITextField *)textField {
    if(![self textIsEmpty:textField.text]) {
        [self.toDoListItems addObject:textField.text];
        [self.tableView reloadData];
    }
    [textField resignFirstResponder];
    return YES;
}

- (BOOL)textIsEmpty:(NSString*)text {
    NSCharacterSet *whitespace = [NSCharacterSet whitespaceAndNewlineCharacterSet];
    NSString *trimmed = [text stringByTrimmingCharactersInSet:whitespace];
    if ([trimmed length] == 0) {
        return YES;
    }
    return NO;
}
```

在这个阶段，我们将通过为我们将要创建的模型创建一个新的 *ModelClasses* 组和一个 *ControllerClasses* 组来添加我们的`ViewController`文件，从而进行一些项目重组。

![Select ModelClasses folder](img/dd808c2e502a45b12e9fd353bcda5644.png)

创建一个名为 *ToDoListTableViewCell* 的新类，它是`UITableViewCell`的子类，并将其用作自定义单元格的模型类，我们将在`cellForRowAtIndexPath:`方法中返回该单元格:

![Create Subclass](img/611be8696a402b9a904fadb661b37439.png)

![Moving files to ModelClasses folder](img/ee182ad2d763e59e5b241692e5159b9a.png)

从 *Main.storyboard* 中将这个模型类分配给待办事项表视图的原型单元格:

![Assign custom class](img/5bb259ba60fdb88d5c5a3db28c10c697.png)

然后给这个原型单元格添加一个标签。这个标签将包含每个新待办事项的标题。

配置标签的运行时间约束:

![Configure label constraints](img/d997d5bc37bd3831122fd40b5cb6697c.png)

![Configure label constraints](img/7d2ad2174a85b6a818997d5408198d1e.png)

![Configure label constraints](img/3d3cb2855d1874a7f75a4316157de299.png)

为该单元格添加一个<q>重用标识符</q>，以便该单元格可以在运行时出队并再次重用，从而实现更平滑的滚动:

![Add identifier](img/b3b26c064c3461ec7b8229c9d4c247d8.png)

通过插座将此标签连接到`ToDoListTableViewCell`类:

![Connect label](img/2aa981dfe2205d5f18c99111b2b79544.png)

```
@property (weak, nonatomic) IBOutlet UILabel *toDoItemTitle;
```

要使用主`ToDoListTableViewController`中的`ToDoListTableViewCell`类，将`ToDoListTableViewCell`类头导入到*todolisttableviewcontroller . m*中:

```
#import "ToDoListTableViewController.h"
#import "ToDoListTableViewCell.h"
```

在`cellForRowAtIndexPath:`内设置待办事项标签的文本，使用`indexPath.row`作为从`toDoListItems`数组中检索待办事项的索引:

```
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    ToDoListTableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"ToDoListTableViewCell" forIndexPath:indexPath];
    cell.toDoItemTitle.text = self.toDoListItems[indexPath.row];
    return cell;
}
```

添加新的待办事项后，确保清除在`UITextField`中输入的文本，在`textFieldShouldReturn:`方法中执行此操作:

```
- (BOOL)textFieldShouldReturn:(UITextField *)textField {
    if(![self textIsEmpty:textField.text]) {
        [self.toDoListItems addObject:textField.text];
        [textField setText:@""];
        [self.tableView reloadData];
    }
    [textField resignFirstResponder];
    return YES;
}
```

编译并运行项目。添加新的待办事项现在很简单，只需输入文本并点击 *Done* 。

我们现在可以在列表中添加新的任务，但是删除一个现有的任务呢？

我之前在创建我的待办表格视图时已经启用了*编辑*按钮。需要重写两个重要的方法来删除项目。第一个是`tableView:canEditRowAtIndexPath:`，控制表格视图单元格是否可编辑。检查完索引后，您可以通过返回`NO`锁定对某些行的编辑操作，当按下*编辑*按钮时，它们不会受到影响。

```
- (BOOL)tableView:(UITableView *)tableView canEditRowAtIndexPath:(NSIndexPath *)indexPath {
    // Return NO if you do not want the specified item to be editable.
    return YES;
}
```

重写的第二个方法是`tableView:commitEditingStyle:forRowAtIndexPath:`,在这里相应的 todo 项被删除，一个动画被设置:

```
- (void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(NSIndexPath *)indexPath {
    if (editingStyle == UITableViewCellEditingStyleDelete) {
        [self.toDoListItems removeObjectAtIndex:indexPath.row];
        // Delete the row from the data source
        [tableView deleteRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationFade];
    }
}
```

编译和运行之后，删除表行现在应该可以工作了:

![Delete a Table row](img/3900bd796349cc261df79378685846ca.png)

![Delete a Table row](img/cdb3ed0c61235304e0884b383eaa207d.png)

*滑动删除*在这里起作用。在列表中的任何待办事项上滑动，就会出现一个红色的*删除*按钮。

父 iOS 应用程序现在已经准备好了。接下来是手表应用程序和不同的消息传递方法。

首先打开 WatchKit App 文件夹中的 *Interface.storyboard* 文件。

接下来将一个*表*拖到界面控制器上。当你拖动`Table`时，你首先看到的是一个*表格行控制器*。这类似于您在`UITableView`中看到的普通行:

![Adding table](img/991fbca706ec16562431a230fe792ea5.png)

将一个*标签*拖到*表格行控制器*上。这将保存待办事项列表项目的标题。将*水平位置*设置为*左侧*，将*垂直位置*设置为*中间*。相对于容器将标签宽度设置为*。*

![Adding label](img/dc538fdd29f41dfc2574993b59f97636.png)

在 WatchKit 扩展文件夹中，为名为`ToDoListWatchKitTableRow`的表格行控制器创建一个模型类，它是`NSObject`的子类。

![create model class](img/678ebbfbd0dded9a480f1988c36717ee.png)

然后我将把`ToDoListWatchKitTableRow`类分配给*接口控制器*下的*表格行控制器*。

![Assign class](img/093ba9e44d4650959fcb2ca5f186b223.png)

然后我会将这个*表格行控制器*的*标识符*设置为`ToDoListWatchKitTableRow`:

![Add identifier](img/bf40680508e77cc874b2cbbe1db47d16.png)

当您为我们刚刚拖到我们的*接口控制器*的*表*设置行数时，这个标识符很重要。我们稍后将对此进行更深入的探讨。

下一步是创建一个到刚刚创建的标签的出口连接，并将 *WatchKit.h* 包含在模型类中:

```
#import <Foundation/Foundation.h>
#import <WatchKit/WatchKit.h>

@interface ToDoListWatchKitTableRow : NSObject
@property (weak, nonatomic) IBOutlet WKInterfaceLabel *toDoListItemTitleLabel;

@end
```

我们要探索的第一种沟通方式是`openParentApplication:reply:`。

在此之前，在基础项目中创建一个 Singleton 类，它将保存之前在*todolisttableviewcontroller . m*中定义的`toDoListItemsList`数组。这是因为我们需要让`toDoListItemsList`数组的值可用，并且与项目中的任何类都相同。这种情况下的单例设计模式，因为我们需要从 *AppDelegate.m* 文件和*todolisttableviewcontroller . m*中访问这个数组。这个类将在 iOS 应用程序的目标下创建，而不是在 WatchKit 下创建。这意味着该类将被添加到“ToDoList”组下的“ModelClasses”组下。

将这个模型类*命名为 ToDoListData* ，并使其成为`NSObject`的一个子类。

在 *ToDoListData.h* 内添加:

```
#import <Foundation/Foundation.h>

@interface ToDoListData : NSObject
+ (NSMutableArray *)toDoListItems;
@end
```

而里面的 *ToDoListData.m* :

```
#import "ToDoListData.h"

@interface ToDoListData()
@property (nonatomic, strong) NSMutableArray* toDoListItems;
@end

@implementation ToDoListData

+ (ToDoListData *) sharedInstance
{
    static dispatch_once_t onceToken;
    static ToDoListData *singelton = nil;
    dispatch_once(&onceToken, ^{
        singelton = [[ToDoListData alloc] init];
    });
    return singelton;
}

- (id)init {
    self = [super init];
    if(self){
        self.toDoListItems = [NSMutableArray array];
    }
    return self;
}

+ (NSMutableArray*)toDoListItems {
    return [self sharedInstance].toDoListItems;
}
```

在*todolisttableviewcontroller . m*中使用这个 Singleton 类的新数组。在`InitializeValues`方法中，对`toDoListItemsProperty`进行如下更改:

```
- (void)initializeValues {
    self.toDoListItems = [ToDoListData toDoListItems];
    self.tableView.tableHeaderView = [self toDoListTableViewHeader];
    self.toDoInputTextField.delegate = self;
}
```

确保用`#import "ToDoListData.h"`导入 *ToDoListData.h* 。

现在我们可以开始添加处理 WatchKit 扩展和父 iOS 应用程序之间的通信所需的方法。

在 *InterfaceController.m* 中，我们使用`openParentApplication:reply:`向父 iOS 应用程序请求待办事项。将它添加到`willActivate`方法中。这将确保每次 Apple Watch 应用程序被带到前台时，Apple Watch 应用程序都会向父 iOS 应用程序请求数据。我将传递一个带有键和值的字典，我们将在父 iOS 应用程序的 *AppDelegate.m* 中检查该字典:

```
@interface InterfaceController()
@property (nonatomic, strong) NSMutableArray* toDoListItems;
@end

@implementation InterfaceController

- (void)willActivate {
    // This method is called when watch view controller is about to be visible to user
    [super willActivate];

    // Using openParentapplication: method
    [WKInterfaceController openParentApplication:@{@"action":@"gettoDoListItems"} reply:^(NSDictionary *replyInfo, NSError *error) {
        if(error) {
            NSLog(@"An error happened while opening the parent application : %@", error.localizedDescription);
        }
        else {
            self.toDoListItems = [replyInfo valueForKey:@"toDoListItems"];
        }
    }];
}

@end
```

在父 iOS 应用程序的 *AppDelegate.m* 中，我们检查与`openParentApplication:reply:`一起使用的键的值，然后使用`reply`块向父 iOS 应用程序发回一个响应，其中包含保存待办事项的数组的值。在`openParentApplication:reply:`内部检查是否有任何错误，并设置本地`toDoListItems`数组来保存从回复返回的数组值。使用此数组的计数来设置表格行数:

```
- (void)application:(UIApplication *)application handleWatchKitExtensionRequest:(NSDictionary *)userInfo reply:(void (^)(NSDictionary *))reply {
    if([[userInfo valueForKey:@"action"] isEqualToString:@"gettoDoListItems"]) {
        reply(@{@"toDoListItems":[ToDoListData toDoListItems]});
    }
}
```

确保包含`#import "ToDoListData.h"`。

因为我们正在使用模拟器，所以很难在运行 Apple Watch 应用程序之前运行 iOS 应用程序，所以为`toDoListItems`数组添加一个默认条目，以证明该方法正在工作(在 *ToDoListData.m* 中更改现有方法):

```
- (id)init {
    self = [super init];
    if(self){
        self.toDoListItems = [NSMutableArray array];
        [self.toDoListItems addObject:@"This is a default to-do."];
    }
    return self;
}
```

然后，从 *Interface.storyboard* 类中我的*界面控制器*下的*表*到我的*界面控制器 h* 做一个出口连接:

![Create Outlet](img/9dab13ce44200732efc01d3eb50dbf54.png)

*InterfaceController.h* 类应该是这样的:

```
#import <WatchKit/WatchKit.h>
#import <Foundation/Foundation.h>

@interface InterfaceController : WKInterfaceController
@property (weak, nonatomic) IBOutlet WKInterfaceTable *toDoListInterfaceTable;

@end
```

为了完成 WatchKit 扩展的待办事项表，在 *InterfaceController.m* 中创建一个`loadTable`方法:

```
- (void)loadTable {
    [self.toDoListInterfaceTable setNumberOfRows:self.toDoListItems.count withRowType:@"ToDoListWatchKitTableRow"];
    for(int i = 0; i < self.toDoListItems.count; i++) {
        ToDoListWatchKitTableRow* row = [self.toDoListInterfaceTable rowControllerAtIndex:i];
        row.toDoListItemTitleLabel.text = self.toDoListItems[i];
    }
}
```

在`openParentApplication:reply:`的回复块内调用`loadTable`方法。确保包括`#import "ToDoListWatchKitTableRow.h"`。

*InterfaceController.m* 类应该是这样的:

```
#import "InterfaceController.h"
#import "ToDoListWatchKitTableRow.h"

@interface InterfaceController()
@property (nonatomic, strong) NSMutableArray* toDoListItems;
@end

@implementation InterfaceController

- (void)awakeWithContext:(id)context {
    [super awakeWithContext:context];

    // Configure interface objects here.
}

- (void)willActivate {
    // This method is called when watch view controller is about to be visible to user
    [super willActivate];

    [WKInterfaceController openParentApplication:@{@"action":@"gettoDoListItems"} reply:^(NSDictionary *replyInfo, NSError *error) {
        if(error) {
            NSLog(@"An error happened while opening the parent application : %@", error.localizedDescription);
        }
        else {
            self.toDoListItems = [replyInfo valueForKey:@"toDoListItems"];
            [self loadTable];
        }
    }];
}

- (void)loadTable {
    [self.toDoListInterfaceTable setNumberOfRows:self.toDoListItems.count withRowType:@"ToDoListWatchKitTableRow"];
    for(int i = 0; i < self.toDoListItems.count; i++) {
        ToDoListWatchKitTableRow* row = [self.toDoListInterfaceTable rowControllerAtIndex:i];
        row.toDoListItemTitleLabel.text = self.toDoListItems[i];
    }
}

- (void)didDeactivate {
    // This method is called when watch view controller is no longer visible
    [super didDeactivate];
}

@end
```

此时在编译应用程序之前的一个重要步骤是打开 iOS 模拟器，从*硬件*菜单中选择*外部显示器*、 *Apple Watch* ，然后选择*38 毫米*或*42 毫米*。

选择目标作为 watchkit 应用程序，编译并运行。您应该会看到如下所示的内容:

![App](img/3a8c7ee1e5a3c65bb1bbffb9d730bff2.png)

![WatchKit App](img/0cea5c464a91945fe581d4433db08b80.png)

## 虫洞方法

接下来我们将尝试另一种通信方法，即 [MMWormhole](https://github.com/mutualmobile/MMWormhole) 库。首先使用 [CocoaPods](https://www.sitepoint.com/cocoapods-good/) 安装 MMWormhole。创建一个 *Podfile* 并添加:

```
target 'ToDoList' do
  platform :ios, '9.0'
  pod 'MMWormhole', '~> 1.1.1'
end

target 'ToDoList WatchKit Extension' do
  platform :watchos, '2.0'
  pod 'MMWormhole', '~> 1.1.1'
end
```

在这个 Podfile 中，我们将 MMWormhole 链接到两个目标，iOS 应用程序和 WatchKit 扩展。

在 Xcode 项目的*功能*选项卡中启用 *[应用组](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW61)* 选项。为 iOS 应用程序和 WatchKit 扩展创建一个新的应用程序组。确保两个应用程序组都处于活动状态:

![App Group](img/1ff34616cbacc37f9812051d884a9d54.png)

![App Group](img/557d330715bf643998eb9fafc7358814.png)

在与 *Podfile* 相同的位置，在命令行上运行`pod install`。安装完成后，关闭项目，打开生成的*。xcworkspace* 文件。

第一步是在*todolisttableviewcontroller . m*中创建一个 MMWormhole 类的实例。用应用程序组的标识符初始化此实例:

```
#import "ToDoListTableViewController.h"
#import "ToDoListTableViewCell.h"
#import "ToDoListData.h"
#import "MMWormhole.h"

#define TABLE_VIEW_CELL_HEIGHT 40

@interface ToDoListTableViewController ()
@property (nonatomic, strong) UITextField *toDoInputTextField;
@property (nonatomic, strong) NSMutableArray *toDoListItems;
@property (nonatomic, strong) MMWormhole *wormhole;
@end

...

- (void)initializeValues {
    self.toDoListItems = [ToDoListData toDoListItems];
    self.tableView.tableHeaderView = [self toDoListTableViewHeader];
    self.toDoInputTextField.delegate = self;
    self.wormhole = [[MMWormhole alloc] initWithApplicationGroupIdentifier:@"group.com.safwat.development.ToDoList" optionalDirectory:@"wormhole"];
}
```

定义一个可以发送虫洞消息的方法。我们将使用这个方法将`toDoListItems`数组从父 iOS 应用程序发送到 WatchKit 扩展:

```
- (void)passWormholeMessage:(id)messageObject {
    [self.wormhole passMessageObject:messageObject identifier:@"toDoListItems"];
}
```

然后我们可以调用这个方法对`toDoListItems`进行修改，比如添加或删除一个待办事项。在`textFieldShouldReturn:`和`tableView:commitEditingStyle:forRowAtIndexPath:`方法中调用这个方法:

```
- (BOOL)textFieldShouldReturn:(UITextField *)textField {
    if(![self textIsEmpty:textField.text]) {
        [[ToDoListData toDoListItems] addObject:textField.text];
        [textField setText:@""];
        [self.tableView reloadData];
        [self passWormholeMessage:[ToDoListData toDoListItems]];
    }
    [textField resignFirstResponder];
    return YES;
}

...

- (void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(NSIndexPath *)indexPath {
    if (editingStyle == UITableViewCellEditingStyleDelete) {
        [[ToDoListData toDoListItems] removeObjectAtIndex:indexPath.row];
        // Delete the row from the data source
        [tableView deleteRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationFade];
        [self passWormholeMessage:[ToDoListData toDoListItems]];
    }
}
```

为了让 WatchKit 扩展在第一次打开时显示`toDoListItems`的内容，发送一个虫洞消息，在*的`viewDidLoad`中保存数组对象:*

```
- (void)viewDidLoad {
    [super viewDidLoad];
    [self initializeValues];
    self.navigationItem.rightBarButtonItem = self.editButtonItem;
    [self passWormholeMessage:[ToDoListData toDoListItems]];
}
```

在 WatchKit 扩展组，打开 *InterfaceController.m* 。定义一个`MMWormhole`类的实例并初始化它。该类现在应该如下所示:

```
#import "InterfaceController.h"
#import "ToDoListWatchKitTableRow.h"
#import "MMWormhole.h"

@interface InterfaceController()
@property (nonatomic, strong) NSMutableArray* toDoListItems;
@property (nonatomic, strong) MMWormhole *wormhole;
@end

@implementation InterfaceController

- (void)awakeWithContext:(id)context {
    [super awakeWithContext:context];

    // Configure interface objects here.
    self.wormhole = [[MMWormhole alloc] initWithApplicationGroupIdentifier:@"group.com.safwat.development.ToDoList" optionalDirectory:@"wormhole"];
}

- (void)willActivate {
    // This method is called when watch view controller is about to be visible to user
    [super willActivate];

    [self.wormhole listenForMessageWithIdentifier:@"toDoListItems" listener:^(id messageObject) {
        NSLog(@"messageObject %@", messageObject);
        self.toDoListItems = messageObject;
        [self loadTable];
    }];
}

- (void)loadTable {
    [self.toDoListInterfaceTable setNumberOfRows:self.toDoListItems.count withRowType:@"ToDoListWatchKitTableRow"];
    for(int i = 0; i < self.toDoListItems.count; i++) {
        ToDoListWatchKitTableRow* row = [self.toDoListInterfaceTable rowControllerAtIndex:i];
        row.toDoListItemTitleLabel.text = self.toDoListItems[i];
    }
}

- (void)didDeactivate {
    // This method is called when watch view controller is no longer visible
    [super didDeactivate];
}

@end
```

尝试编译并运行项目，打开父 iOS 应用程序，然后打开 Apple Watch 应用程序，您应该会看到以下内容:

![WatchKit App](img/98a25dea28fcc1e8ec5d2ccefd494e43.png)

![WatchKit Extension](img/dacbc3c3270ee583f78b84b6a565d565.png)

尝试在 iOS 应用程序中添加一个新任务，您应该会看到结果立即反映在 Apple Watch 应用程序上:

![WatchKit App](img/b7982973b2355f0c6f7ae1eda57bb0c1.png)

![WatchKit Extension](img/1eb04a80f272b46faea9ce9786156249.png)

## NSUserDefaults。

我提到的第三种方法是使用`NSUserDefaults`。在*todolisttableviewcontroller . m*中创建一个`NSUserDefaults`类型的属性，并在同一个`initializeValues`方法中初始化它:

```
@interface ToDoListTableViewController ()
@property (nonatomic, strong) UITextField *toDoInputTextField;
@property (nonatomic, strong) MMWormhole *wormhole;
@property (nonatomic, strong) NSUserDefaults *userDefaults;
@end
```

```
- (void)initializeValues {
    self.tableView.tableHeaderView = [self toDoListTableViewHeader];
    self.toDoInputTextField.delegate = self;
    self.wormhole = [[MMWormhole alloc] initWithApplicationGroupIdentifier:@"group.com.safwat.development.ToDoList" optionalDirectory:@"wormhole"];
    self.userDefaults = [[NSUserDefaults alloc] initWithSuiteName:@"group.com.safwat.development.ToDoList"];
}
```

注释掉对之前定义的`passWormholeMessage:`方法的调用，我们将调用一个新方法来处理将对象保存到`NSUserDefaults`的操作:

```
- (void)saveToUserDefaults:(id)object {
    [self.userDefaults setObject:object forKey:@"toDoListItems"];
}

...

- (void)viewDidLoad {
    [super viewDidLoad];
    [self initializeValues];
    self.navigationItem.rightBarButtonItem = self.editButtonItem;
    //[self passWormholeMessage:[ToDoListData toDoListItems]];
    [self saveToUserDefaults:[ToDoListData toDoListItems]];
}

...

- (BOOL)textFieldShouldReturn:(UITextField *)textField {
    if(![self textIsEmpty:textField.text]) {
        [[ToDoListData toDoListItems] addObject:textField.text];
        [textField setText:@""];
        [self.tableView reloadData];
        //[self passWormholeMessage:[ToDoListData toDoListItems]];
        [self saveToUserDefaults:[ToDoListData toDoListItems]];
    }
    [textField resignFirstResponder];
    return YES;
}

...

- (void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(NSIndexPath *)indexPath {
    if (editingStyle == UITableViewCellEditingStyleDelete) {
        [[ToDoListData toDoListItems] removeObjectAtIndex:indexPath.row];
        // Delete the row from the data source
        [tableView deleteRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationFade];
        //[self passWormholeMessage:[ToDoListData toDoListItems]];
        [self saveToUserDefaults:[ToDoListData toDoListItems]];
    }
}
```

在 WatchKit 扩展 *InterfaceController.m* 内部，读取`NSUserDefaults`内部保存的数据，加载待办事项表。首先定义一个`NSUserDefaults`实例，然后在`awakeWithContext:`方法中初始化它。 *InterfaceController.m* 类应该如下所示:

```
#import "InterfaceController.h"
#import "ToDoListWatchKitTableRow.h"
#import "MMWormhole.h"

@interface InterfaceController()
@property (nonatomic, strong) NSMutableArray* toDoListItems;
@property (nonatomic, strong) MMWormhole *wormhole;
@property (nonatomic, strong) NSUserDefaults *userDefaults;
@end

@implementation InterfaceController

- (void)awakeWithContext:(id)context {
    [super awakeWithContext:context];

    // Configure interface objects here.
    self.wormhole = [[MMWormhole alloc] initWithApplicationGroupIdentifier:@"group.com.safwat.development.ToDoList" optionalDirectory:@"wormhole"];
    self.userDefaults = [[NSUserDefaults alloc] initWithSuiteName:@"group.com.safwat.development.ToDoList"];
}

- (void)loadTable {
    [self.toDoListInterfaceTable setNumberOfRows:self.toDoListItems.count withRowType:@"ToDoListWatchKitTableRow"];
    for(int i = 0; i < self.toDoListItems.count; i++) {
        ToDoListWatchKitTableRow* row = [self.toDoListInterfaceTable rowControllerAtIndex:i];
        row.toDoListItemTitleLabel.text = self.toDoListItems[i];
    }
}

- (void)willActivate {
    // This method is called when watch view controller is about to be visible to user
    [super willActivate];

    // Using openParentapplication: method
    /*
    [WKInterfaceController openParentApplication:@{@"action":@"gettoDoListItems"} reply:^(NSDictionary *replyInfo, NSError *error) {
        if(error) {
            NSLog(@"An error happened while opening the parent application : %@", error.localizedDescription);
        }
        else {
            self.toDoListItems = [replyInfo valueForKey:@"toDoListItems"];
            [self loadTable];
        }
    }];
    */

    // Using MMWormhole method
    /*
    [self.wormhole listenForMessageWithIdentifier:@"toDoListItems" listener:^(id messageObject) {
        NSLog(@"messageObject %@", messageObject);
        self.toDoListItems = messageObject;
        [self loadTable];
    }];
     */

    // Using NSUserDefaults method
    self.toDoListItems = [self.userDefaults valueForKey:@"toDoListItems"];
    [self loadTable];
}

- (void)didDeactivate {
    // This method is called when watch view controller is no longer visible
    [super didDeactivate];
}

@end
```

编译项目，运行 iOS 应用程序，然后运行 Apple Watch 应用程序，您应该会得到与我们之前使用`openParentApplication:reply:`和 *MMWormhole* 方法相同的结果。

### 结论

在开发您的应用程序时，iOS 应用程序与其 WatchKit 扩展之间的消息传递会带来大量有趣的编程挑战。任何通信方法都可以实现该目标，但这将取决于具体情况、利用该方法的应用程序以及是否需要实时数据更新。尝试测试一些 Apple Watch 应用，思考数据通信是如何实现的。

如果你对本教程或 Apple Watch 开发有任何问题，请在下面的评论中告诉我。

## 分享这篇文章