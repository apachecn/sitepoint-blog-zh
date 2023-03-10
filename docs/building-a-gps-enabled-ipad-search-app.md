# 构建支持 GPS 的 iPad 搜索应用程序

> 原文：<https://www.sitepoint.com/building-a-gps-enabled-ipad-search-app/>

移动服务的部分吸引力在于它们与你现在所处的位置相关。大多数手机支持 GPS 和网络连接。当你把这些结合起来，你就会得到一个方便的基于位置的服务。在这个示例应用程序中，我们将在 PHP 中创建一个后端服务，前端的 iPad 应用程序将连接到该服务。这项服务将有一个 MySQL 数据库中的本地商店列表。欢迎您在后端和前端使用这个示例代码，作为您自己的基于地理位置的服务的入门工具包。在 BuildMobile 的 GitHub 上获取 [StoreFinder 的所有代码。](https://github.com/buildmobile/storefinder)

### 创建后端

构建后端首先要创建一个 MySQL 数据库，其中包含重要的位置数据。您可以在清单 1 中看到初始化脚本。

**清单 1。`db.sql`**

```
DROP TABLE IF EXISTS locations;
CREATE TABLE locations(
    lon FLOAT,
    lat FLOAT,
    name VARCHAR(255) );

INSERT INTO locations VALUES ( -122.035706, 37.332802, 'Starbucks' );
INSERT INTO locations VALUES ( -122.036133, 37.331711, 'Peets' );
INSERT INTO locations VALUES ( -122.033173, 37.336182, 'Chipotle' );
...
INSERT INTO locations VALUES ( -122.035919, 37.324341, 'Buy More' );
INSERT INTO locations VALUES ( -122.031410, 37.333176, 'Costco' );
```

该脚本做的第一件事是创建 locations 表，其中包括每个商店的纬度、经度和名称。然后，它会在苹果公司位于库比蒂诺的园区周围插入一系列测试记录。在测试这段代码时，为了得到任何结果，您可能需要在附近添加一些记录。

要将其投入生产，我们首先使用`mysqladmin`创建数据库，然后使用`mysql`命令运行脚本，如下所示:

```
$ mysqladmin --user=root create geo
$ mysql –user=root geo < db.sql
```

根据您的 MySQL 安装，您必须更改用户名以及添加密码(如果需要的话)。

数据库准备好了，是时候创建 PHP 脚本来执行搜索并以 XML 的形式返回结果了。这从查找最小和最大纬度和经度的边界框开始，如清单 2 所示。

**清单 2。`circle.php`算出包围盒**

```
<?php
define( 'LATMILES', 1 / 69 );
define( 'LONMILES', 1 / 53 );

$lat = 37.3328;
$lon = -122.036;
$radius = 1.0;

if ( isset( $_GET['lat'] ) ) { $lat = (float)$_GET['lat']; }
if ( isset( $_GET['lon'] ) ) { $lon = (float)$_GET['lon']; }
if ( isset( $_GET['radius'] ) ) { $radius = (float)$_GET['radius']; }

$minlat = $lat - ( $radius * LONMILES );
$minlon = $lon - ( $radius * LATMILES );
$maxlat = $lat + ( $radius * LONMILES );
$maxlon = $lon + ( $radius * LATMILES );
```

纬度的每一点是 69 英里，经度的每一点是 53 英里。这些被存储为脚本顶部的`LATMILES`和`LONMILES`常量。脚本使用这些来创建代表查询边界框的`minlat`、`maxlat`、`minlon`和`maxlon`变量。长方体的尺寸由传入的半径值决定。为了方便起见，这些值都默认为合理的值。

下一步是创建将在边界框内查找位置记录的查询。清单 3 显示了这方面的代码。

**清单 3。`circle.php`创建查询**

```
$dbh = new PDO('mysql:host=localhost;dbname=geo', 'root', '');

$sql = 'SELECT lat, lon, name FROM locations WHERE lat >= ? AND lat <= ? AND lon >= ? AND lon <= ?';

$params = array( $minlat, $maxlat, $minlon, $maxlon );

if ( isset( $_GET['q'] ) ) {
  $sql .= " AND name LIKE ?";
  $params []= '%'.$_GET['q'].'%';
}

$q = $dbh->prepare( $sql );
$q->execute( $params );
```

这段代码使用 PDO 库来连接数据库。然后格式化并执行一条 SQL 语句，该语句将返回的记录限制为清单 2 中创建的边界框内的记录。或者，如果指定了“q”参数，则只返回名称中包含指定值的记录。例如，用户可以指定他只希望看到标题中有名称“bucks”的商店，并且他只希望看到附近的星巴克商店。

这个脚本的最后一步是将结果格式化为 XML。清单 4 显示了这一点。

**清单 4。`circle.php`格式化结果**

```
$doc = new DOMDocument();
$r = $doc->createElement( "locations" );
$doc->appendChild( $r );

foreach ( $q->fetchAll() as $row) {
  $dlat = ( (float)$row['lat'] - $lat ) / LATMILES;
  $dlon = ( (float)$row['lon'] - $lon ) / LONMILES;
  $d = sqrt( ( $dlat * $dlat ) + ( $dlon * $dlon ) );
  if ( $d <= $radius ) {
    $e = $doc->createElement( "location" );
    $e->setAttribute( 'lat', $row['lat'] );
    $e->setAttribute( 'lon', $row['lon'] );
    $e->setAttribute( 'name', $row['name'] );
    $e->setAttribute( 'd', $d );
    $r->appendChild( $e );
  }
}

print $doc->saveXML();
?>
```

为了方便起见，我们使用 XML DOM 库在内存中创建一个 XML 文档，然后使用`saveXML`方法简单地写出它。这确保了 XML 将正确格式化特殊字符，如小于、大于、与号、引号等。它也比手写的 XML 代码更容易阅读。

这段代码还对数据应用了一个圆形过滤器。这意味着我们进一步将从原始盒子返回的行压缩成一个圆形。目前还不清楚用户是否会注意到这种改进，但这确实意味着如果用户指定距离该位置 5 英里，那么所有的结果都将位于他们当前位置周围 5 英里的范围内。

随着数据库的建立和脚本的准备，是时候测试它了。

### 测试后端

您可以在浏览器中查看结果，只需导航到该页面，并根据需要调整搜索参数。但是正如您在清单 5 中看到的，有时使用 CURL 更简单。

**清单 5。使用 CURL 运行脚本**

```
$ curl "http://localhost/circle.php?radius=1"
<?xml version="1.0"?>
<locations>
<location lat="37.3328" lon="-122.036" name="Starbucks" d="0"/>
<location lat="37.3317" lon="-122.036" name="Peets" d="0.075900000000068"/>
...
</locations>
$
```

CURL 是一个命令行工具，它获取指定的 URL 并将结果打印到`STDOUT`。从网址你可以看到，我还没有指定一个纬度和经度。这意味着搜索将集中在苹果的库比蒂诺总部，那里是大多数示例数据的中心。我所做的就是指定返回的商店必须在一英里的半径范围内，这稍微细化了结果。

构建了数据库，在服务器端运行查询的 PHP 脚本已经完成并经过测试。下一步是在上面放一个简单的 iPad 前端。

### 构建 iPad 应用程序

这个 iPad 应用程序将是一个“基于视图”的应用程序。这意味着我们从一个空白的画布开始，向其中添加控件。在这个应用程序中，我们只需要两个控件，一个是包含搜索查询项的`UISearchBar`(例如,‘bucks’查找所有的星巴克),另一个是显示所有返回记录的`UITableView`。您可以在清单 6 中看到这种定义。

**清单 6。`StoreFinderViewController.h`**

```
#import <UIKit/UIKit.h>
#import <CoreLocation/CoreLocation.h>

@interface StoreFinderViewController : UIViewController<UITableViewDataSource,
        CLLocationManagerDelegate,
        NSXMLParserDelegate,
        UISearchBarDelegate> {
    IBOutlet UISearchBar *searchBar;
    IBOutlet UITableView *tableView;

    CLLocationManager *locationManager;

    NSMutableArray *locations;

    float latitude;
    float longitude;
}

@property (nonatomic, retain) CLLocationManager *locationManager;  

@end
```

我们不仅有`searchBar`和`tableView`指针，还有一个`locationManager`，用于获取 GPS 数据。locations 数组存储我们找到的所有位置。以及存储最近的纬度和经度的纬度和经度值。

一旦我们在代码中定义了控制指针，我们需要将它们添加到显示定义文件中。将 UI 控件添加到显示器上从打开`StoreFinderViewController.XIB`文件开始。在那里，从视图>实用程序菜单中选择“文件检查”。右下角是一个对象面板，您可以将它拖到视图控制器区域。首先拖动一个`UISearchBar`并将其定位在窗口的顶部。然后抓起一个`UITableView`放到显示屏上。它应该填满整个内容，如果没有，就移动它直到填满为止。

接下来，我们需要将这些控件连接到`ViewController`。首先右键单击显示屏左侧对象面板中的`UISearchBar`项目。这将调出一个弹出菜单，其中有一个标签为“新参考插座”的项目。点击右边的小气泡，拖动线条到“文件的所有者”项，然后释放它。这将调出另一个选项菜单。将其附加到“搜索栏”项目。

现在对`UITableView`做同样的事情，将它连接到视图控制器中的`tableView`。最后再次右键单击`UITableView`，这次将`dataSource`气泡拖到`File's Owner`并释放。

这是连接 UI 所要做的全部工作。从现在开始一切都在代码里了。

为了包含一个`locationManager`,您需要将核心位置框架添加到项目中。为此，选择“StoreFinder”项目，然后选择“StoreFinder”目标，它有一个小画笔和标尺图标。从那里选择“构建阶段”标签，并旋转打开“链接二进制与库”部分。这将显示您当前包含的库。单击加号按钮并选择核心位置框架，将其添加到您的应用程序中。

该类还定义了许多委托。`UITableViewDataSource`意味着该对象将用于驱动表格视图的数据。`CLLocationManagerDelegate`表示物体将对 GPS 更新做出响应。`NSXMLParserDelegate`用于解析来自服务器的 XML 响应。最后，`UISearchBarDelegate`意味着对象将处理来自搜索栏的事件。特别是当用户点击搜索按钮或取消搜索时。

完成了类定义之后，是时候开始构建视图控制器类本身了。这从清单 7 所示的初始化代码开始。

**清单 7。`StoreFinderViewController.m`初始化**

```
#import "StoreFinderViewController.h"

@implementation StoreFinderViewController

- (void)dealloc
{
    [super dealloc];
    [self.locationManager release];
    if ( locations )
        [locations release];
}

- (void)viewDidLoad
{
    [super viewDidLoad];

    locations = NULL;

    self.locationManager = [[[CLLocationManager alloc] init] autorelease];
    self.locationManager.delegate = self;
    [self.locationManager startUpdatingLocation];
}

- (BOOL)shouldAutorotateToInterfaceOrientation:(UIInterfaceOrientation)interfaceOrientation
{
    return YES;
}
```

这里唯一真正有趣的事情是`locationManager`对象的创建和开始从 GPS 获取数据的请求。在清单 8 中，我们有处理来自`UITableView`的数据回调的代码。

**清单 8。StoreFinderViewController.m 表格处理**

```
- (NSInteger)tableView:(UITableView *)table numberOfRowsInSection:(NSInteger)section {
    if ( locations != NULL ) {
        return [locations count];
    }
    return 0;
}

- (UITableViewCell *)tableView:(UITableView *)tv cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"MyIdentifier"];

    if (cell == nil) 
    {
        cell = [[[UITableViewCell alloc] initWithFrame:CGRectZero reuseIdentifier:@"MyIdentifier"] autorelease];
    }

    NSDictionary *itemAtIndex = (NSDictionary *)[locations objectAtIndex:indexPath.row];
    UILabel *newCellLabel = [cell textLabel];

    [newCellLabel setText:[itemAtIndex objectForKey:@"name"]];

    return cell;
}
```

如果你以前没有看过这些东西，那会非常有趣。发生的情况是，控件只回调足够的数据，因为它需要呈现对用户可见的内容。第一个调用是对获取表格大小的`numberOfRowsInSection`的调用。然后`cellForRowAtIndexPath`返回给定索引的单元格对象。在这种情况下，代码使用 locations 数组来设置一个标签，该标签包含在给定的行索引中找到的商店的名称。

当然，如果没有一些数据来驱动，数据的表格显示是没有用的。清单 9 中的代码向服务器发出实际请求，并解析返回的数据。

**清单 9。`StoreFinderViewController.m` XML 请求和解析**

```
- (void)updateLocation:(CLLocation *)newLocation {
    if ( locations ) {
        [locations release];
    }
    locations = [[NSMutableArray alloc] init];

    if ( newLocation ) {
        latitude = newLocation.coordinate.latitude;
        longitude = newLocation.coordinate.longitude;
    }

    NSString *urlString = [NSString stringWithFormat:@"http://localhost/geo2/circle.php?lat=%g&amp;lon=%g&amp;radius=100&amp;q=%@", 
        latitude, longitude, searchBar.text ? searchBar.text : @""];

    NSXMLParser *locationParser = [[[NSXMLParser alloc] initWithContentsOfURL:[NSURL URLWithString:urlString]] autorelease];
    [locationParser setDelegate:self];
    [locationParser parse];

    [tableView reloadData];
}

- (void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName attributes:(NSDictionary *)attributeDict {

    if ( [elementName isEqualToString:@"location"]) {
        [locations addObject:[[NSDictionary alloc] initWithDictionary:attributeDict]];
    }
}
```

它从`updateLocation`方法开始，当 GPS 改变时或者当用户发出搜索请求时调用该方法。`updateLocation`方法创建一个`NSXMLParser`，然后将它指向服务器的 URL。在这种情况下，服务器位于 localhost 上，但是您可以将它放在您想要的任何地方。XML 解析器请求数据，获取结果并开始解析。

数据的解析触发回调方法，在本例中是`didStartElement`，这表明一个标签已经开始。这对这段代码来说已经足够了，因为位置标记包含了我们需要知道的属性中的所有数据，这些数据是通过 attributes 参数从 XML 解析器传入的。`didStartElement`只是查看标签，看它的名字是否是‘location ’,如果是，它将属性复制到 locations 数组中的一个新对象中。

在流程结束时，调用`reloadData`方法来刷新`UITableView`控件。

接下来要做的是处理 GPS 回调，如清单 10 所示。

**清单 10。`StoreFinderViewController.m` GPS 处理**

```
- (void)locationManager:(CLLocationManager *)manager
    didUpdateToLocation:(CLLocation *)newLocation
           fromLocation:(CLLocation *)oldLocation
{
    [self updateLocation:newLocation];
}

- (void)locationManager:(CLLocationManager *)manager
       didFailWithError:(NSError *)error
{
}
```

我们在这里所做的就是在新位置改变时用新位置调用`updateLocation`方法。

最后一小段代码处理搜索栏，如清单 11 所示。

**清单 11。`StoreFinderViewController.m`搜索栏处理**

```
- (void)searchBarSearchButtonClicked:(UISearchBar *)sb {
    [self updateLocation:NULL];
    [searchBar resignFirstResponder];
}

- (void)searchBarCancelButtonClicked:(UISearchBar *)sb
{
    [searchBar resignFirstResponder];
}

@end
```

这两种方法处理搜索栏中的搜索或取消。当用户选择 search 时，我们只需再次运行查询并使用`updateLocation`方法更新表视图，然后使用`resignFirstResponder`调用去掉键盘。cancel 方法只是去掉了键盘。

前端和后端写好了，是时候试试这只小狗了。

### 试用应用程序

当您启动应用程序时，首先会提示您允许应用程序使用您的当前位置，如图 1 所示。

[![Figure1](img/bcab939522edd215d50f2ade2acac514.png)](https://www.sitepoint.com/wp-content/uploads/2011/07/Figure1.png)

图 1

如果您想让代码工作，请单击“确定”按钮。如果你想避免以后的麻烦，你也应该选中“不要再问我”复选框。

通过位置检查，应用程序查询后端的 PHP 服务，并将返回的 XML 解析到 locations 数组中。然后这个数组显示在`UITableView`中，如图 2 所示。

[![Figure2](img/54d0954bf3afcf1d8070cdf3ad971c7a.png)](https://www.sitepoint.com/wp-content/uploads/2011/07/Figure2.png)

数字

好了，现在我们真的有进展了。因此，让我们确保不仅能找到我们周围的商店，还能找到符合我们搜索标准的商店。我们通过在搜索栏中单击来实现这一点，这会弹出如图 3 所示的键盘。

[![Figure3](img/8ba03a6b1d6fc70cd58d82a7d50abcc0.png)](https://www.sitepoint.com/wp-content/uploads/2011/07/Figure3.png)

数字

抬起键盘，我们现在可以输入一个搜索词，如“ice ”,如图 4 所示。

[![Figure4](img/73f25a886a38c3757e064297f2420325.png)](https://www.sitepoint.com/wp-content/uploads/2011/07/Figure4.png)

数字

然后，我们单击 search 按钮，结果被细化为如图 5 所示的溜冰场。

[![Figure5](img/1ec4088ffb4f9fdec5c42f925644f8cb.png)](https://www.sitepoint.com/wp-content/uploads/2011/07/Figure5.png)

数字

此外，键盘已经自动解除开放的整个屏幕上显示丰富的结果。

### 结论

构建这个 GPS 应用程序在前端和后端都非常容易。后端相当于一个非常简单的数据库，带有一个基本的有界查询。前端的 iPad 应用程序获取 GPS 数据，然后发出一个非常简单的 HTTP 请求，从后端获取数据并显示出来。这只是一个起点，从这里开始，您可以构建后端来添加更多的数据或更多的搜索支持。在前端，您可以添加映射或附加数据显示。

## 分享这篇文章