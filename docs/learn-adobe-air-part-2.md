# 了解 Adobe AIR，第二部分:构建客户管理应用程序

> 原文：<https://www.sitepoint.com/learn-adobe-air-part-2/>

在之前的教程中，我们使用 HTML、CSS 和 JavaScript 创建了一个个人笔记存储数据库。在本教程中，我们将通过构建一个简单的客户关系管理(CRM)应用程序来探索 AIR 平台的 UI 特性。我们将使用这些相同的技术，以及一个本地 SQLite 数据库。

正如我们在第一部分所做的一样，将会有另一个 [**测验来测试你所学的**](https://www.sitepoint.com/quiz/air/part2/) 。同样，前 100 名参加测试的人将免费获得帖子中的 *Adobe AIR For JavaScript 开发者*袖珍指南。记住，这本书可以作为 PDF 文件 短期免费下载，所以开始破解吧！

##### 在 AIR 中构建用户界面

我们的标准 web 技术——HTML、CSS、JavaScript——为在浏览器中构建丰富的界面提供了坚实的基础。然而，有时我们需要扩展到浏览器之外；这样我们就可以利用用户习惯的与桌面应用程序的标准交互。这对于任何类型的数据输入和报告都是特别重要的——还有什么比使用业务数据输入应用程序更好的方法来探索这一点呢？

在本文中，我们将探索 AIR 提供的用户界面的可能性。我们将实现拖放功能，使用本机窗口，在窗口之间通信，构造菜单，等等。完成后，我们将构建一个简单而强大的应用程序来管理客户记录数据库。

##### 假定的知识

在整个教程中，我将假设您已经通读了本系列的第一篇文章，[学习 Adobe AIR，第 1 部分:构建笔记存储应用程序](https://www.sitepoint.com/article/learn-adobe-air-part-1)，或者至少已经习惯于在 [Adobe AIR 中创建简单的应用程序](http://ad.doubleclick.net/clk;205852848;28495102;m)。同样，你需要 [AIR SDK](http://ad.doubleclick.net/clk;205852848;28495102;m) 、Aptana、jQuery、AIR 菜单构建器和 AIR 自省器。如果您还没有设置这些，现在就设置吧。我在我的第一篇文章中介绍了如何做到这一点，在[一节中，这是一个构建 AIR 应用程序的强大工具](https://www.sitepoint.com/article/learn-adobe-air-part-1)。

##### 设置示例项目

要在家里玩，下载本教程的 [skeleton AIR 项目，并将其导入 Aptana。](https://www.sitepoint.com/examples/AdobeAIR/air2-crm-skeleton.zip)

如果您想从头开始创建项目，请确保通过选中新建项目向导中的导入 Javascript 库选项来导入 jQuery。创建项目后，您可以安全地删除以下文件:

*   *AIRLocalizer.js*
*   *AIRSourceViewer.js*
*   *jquery_sample* (注意有两个同名文件)
*   *CRMTest.html*(这个文件名可能会有所不同，这取决于您对项目的命名)
*   *LocalFile.txt*
*   *sample.css*

我们使用的是 jQuery 1.2.3，但是任何更新的 1.2.x 版本都可以正常工作。将 zip 文件的内容添加到项目目录中，刷新文件夹(在**项目**视图中右键单击项目名称并选择**刷新**)，您就可以开始了。

##### 在空气中运行

现在您已经有了示例项目设置，让我们开始吧。正如我提到的，我们将构建一个简单的 CRM 应用程序。这是该应用在 Windows Vista 中的外观:

![The final Customer Management example application](img/e34861e7a265bc014ec1db536d1e7553.png)

攻击计划如下:

*   构建我们的菜单
*   建立到 SQLite 数据库的数据库连接
*   阅读我们最初的客户记录
*   创建一个表单来查看每条记录
*   创建表单以创建新记录

在这个过程中，我们将学习如何手动构造菜单树、直接从文件系统读取、在 SQL 数据库上使用预准备语句，甚至实现本机拖放功能(这将在任何 AIR 支持的平台上工作)。

##### 创建菜单

在我们以前的 AIR 文章中，我们使用菜单构建器框架从 XML 文档自动创建菜单。然而，这种方法有一些限制。这一次，我们将利用本地菜单 API 来构造我们的菜单。

在这个级别构建菜单时，要考虑的主要因素是您将要创建的菜单的*位置*。选项包括位于 Windows 应用程序窗口顶部的**窗口菜单**，以及位于大多数 OS X 应用程序屏幕顶部的**应用程序菜单**。我们也可以使用相同的本地菜单系统来创建上下文菜单、停靠图标菜单(OS X)和系统托盘图标菜单(窗口)。幸运的是，不管我们想要构建哪种类型的菜单，语法都是相同的，这要归功于 JavaScript 的面向对象特性。在 Aptana 中，打开 *menus.js* 文件并添加以下代码:

```
$(document).ready(function(){ 

  if (air.NativeWindow.supportsMenu) { 

    nativeWindow.menu = new air.NativeMenu(); 

    targetMenu = nativeWindow.menu; 

  } 

  if (air.NativeApplication.supportsMenu) { 

    targetMenu = air.NativeApplication.nativeApplication.menu; 

  } 

}); 
```

上面的代码创建了一个名为`targetMenu`的对象。这指的是`nativeWindow`上的菜单(在 Win32 环境中是当前窗口)，或者全局应用程序窗口(OS X)。空中菜单是所有菜单项的对象集合，由一个主`air.NativeMenu`类管理。我们可以检查 AIR 窗口或应用程序对象的`supportsMenu`属性，以确定当前平台支持什么。请注意，在 OS X 上，应用程序菜单已经存在，不管窗口是否存在。Adobe AIR 手册中有[关于 AIR 菜单](http://help.adobe.com/en_US/AIR/1.1/devappshtml/WS5b3ccc516d4fbf351e63e3d118666ade46-7f53.html)的更多信息。

还记得我们使用的是 jQuery，每当页面的 DOM 完成加载时，它都会执行下面的代码行:

```
$(document).ready()
```

我们已经创建了这个函数；现在来补充一下吧。修改您的 *menus.js* 文件，如下所示(新行在下面以粗体显示):

```
$(document).ready(function(){ 

  if (air.NativeWindow.supportsMenu) { 

    nativeWindow.menu = new air.NativeMenu(); 

    targetMenu = nativeWindow.menu; 

 } 

  if (air.NativeApplication.supportsMenu) { 

    targetMenu = air.NativeApplication.nativeApplication.menu; 

 } 

 **var fileMenu; 

 fileMenu = targetMenu.addItem(new air.NativeMenuItem("File")); 

 fileMenu.submenu = new air.NativeMenu(); 

 newCustomer = fileMenu.submenu.addItem(new air.NativeMenuItem("Create   Record")); 

 newCustomer.addEventListener(air.Event.SELECT, Menu_NewCustomer); 

 newCustomer = fileMenu.submenu.addItem(new air.NativeMenuItem("Exit")); 

 newCustomer.addEventListener(air.Event.SELECT, Menu_Exit); 

});** 
```

这几行代码创建了一个简单的文件菜单，其中包含两个菜单项:*创建记录*和*退出*。我们手动为每个项目创建新对象，尽管这可以自动进行以适应应用程序。

这种方法有许多优点，包括添加自定义键盘快捷键的灵活性，以及在菜单项被选中时执行自定义操作。然而，更重要的是，我们现在已经为菜单准备好了一个基本的基础设施，可以很容易地动态添加、删除或修改菜单项。

最后一行指定了选择*退出*菜单项时将执行的回调函数。(请注意，当用户打开菜单但尚未选择它时，也会触发一个显示事件)。那些回调函数还不存在，所以让我们现在就开始编写它们:

```
function Menu_NewCustomer() { 

  document.location = "new_customer.html"; 

} 

function Menu_Exit() { 

  var event = new air.Event(air.Event.EXITING, false, true); 

  air.NativeApplication.nativeApplication.dispatchEvent(event); 

  if (!event.isDefaultPrevented()) { 

    air.NativeApplication.nativeApplication.exit(); 

  } 

} 
```

`Menu_Exit`方法只是检查应用程序的另一部分(或 AIR 运行时本身)是否试图延迟当前应用程序的终止。通过这种方式，它类似于我之前写的关于 Adobe AIR 的文章中的退出函数[。可以覆盖通过这种方法检测到的任何延迟，但不建议这样做；这是因为在这样一个高级框架中，尊重与你的代码并行执行的任何代码都是最佳实践。](https://www.sitepoint.com/article/learn-adobe-air-part-1)

##### 连接到 SQL 数据库

在骨架项目档案中，我包含了一个名为`crm.sqlite`的样本 SQLite 数据库。该数据库仅包含一个表，其中包含以下示例数据(要查看该数据库，我推荐使用火狐的扩展 [SQLite Manager](https://addons.mozilla.org/en-US/firefox/addon/5817) ):

| 身份证明 | 名字 | 电话 | 笔记 |
| one | 普通人 | (123) 456-7890 | = =订单= = nn 客户订购了… |
| Two | 简·多伊 | (123) 456-7899 | 1 月 1 日签署的客户。 |

我们将在本教程中构建的示例应用程序将为用户提供访问这些信息的界面。我们将构建查看单个记录、将数据导入到 *Notes* 列等功能。虽然完成的应用程序非常简单，但将其作为构建成熟的 CRM 应用程序的基础肯定是可行的。

正如我们在第一个教程中看到的，从一个窗口连接到一个本地数据库是相对容易的。然而，在这个应用程序中，我们使用了多个窗口，每个窗口都将独立运行。这意味着我们需要在整个应用程序的不同脚本之间共享这个数据库连接。

记住这一点，让我们建立一个标准的数据库连接——将以下代码添加到您的 *database.js* 文件中:

```
function SetupDB() { 

 var db = new air.SQLConnection(); 

 var dbFile = air.File.applicationStorageDirectory.resolvePath("crm.sqlite"); 

 //if (!dbFile.exists) { 

  var dbTemplate = air.File.applicationDirectory.resolvePath("crm.sqlite"); 

  dbTemplate.copyTo(dbFile, true); 

 //} 

 try 

 { 

  db.open(dbFile, air.SQLMode.UPDATE); 

 } 

 catch (error) 

 { 

  air.trace("DB error:", error.message); 

  air.trace("Details:", error.details); 

 } 

 return db; 

} 
```

上面的代码定义了一个简单的函数`SetupDB()`，它创建了一个到本地 SQL 数据库的连接。正如您所记得的，应用程序目录是只读的(在 Windows 机器上这是`Program Files`文件夹)。因此，您应该将框架数据库复制到**应用程序存储目录**(在 Windows 上这是`AppData`文件夹)。

在开发阶段，我们将对数据库进行潜在的破坏性更改，所以每次重新启动应用程序时，重新创建数据库是很方便的。当我们将应用程序部署到生产环境中时，我们可以简单地取消对`dbFile.exists`条件的注释，以防止我们的数据库被替换。

##### 检索我们的第一批记录

现在我们有了一个接口和一个数据源，是时候开始从我们的 CRM 数据库读取记录了。有趣的事情从*customers.html*文件开始，该文件包含在框架代码档案中，并包含一些示例静态输出。我们将使用 jQuery 删除这些静态数据，并用数据库中的真实记录替换它。打开您的*behavior . js*文件，插入以下代码:

```
$(document).ready(function(){  

  db = SetupDB();  

  read_customers();  

  $("#refresh").click(function(){ read_customers(); });  

});  

function read_customers() {  

  dbQuery = new air.SQLStatement();  

  dbQuery.sqlConnection = db;  

  dbQuery.text = "SELECT * FROM customers";  

  try { dbQuery.execute(); } catch (error) {  

    air.trace("Could not retrieve customers from DB.", error.message);  

  }  

  results = dbQuery.getResult();  

  $("#customerlist tbody").empty();  

  for (i = 0; i < results.data.length; i++) {  

    row = results.data[i];  

    $("#customerlist tbody").append('<tr><td>'+row.id+'</td><td>'  

      +row.name+'</td></tr>');  

  }  

  $("#customerlist tbody tr").each(function(){  

    $(this).click(function(){  

      view($(this).children("td:first").text())  

    })  

  });
```

这段代码的第一部分相当简单明了:

1.  调用我们之前定义的数据库连接函数。

3.  阅读我们的客户名单。

5.  对于每条记录，指示点击处理程序调用查看功能。

像这样将信息绑定到 DOM 中的元素总是有点棘手，所以我们使用了`td:first`选择器来获取第一个单元格的值——ID——并将其作为参数传递给`view`函数。该功能将启动一个新窗口，并为用户提供有关所选记录的更多详细信息，包括电话号码和一些相关注释。现在让我们来定义这个函数；将以下代码添加到 *behaviour.js* 文件的底部:

```
function view(id) {  

  current_id = id;  

  customerWindow = air.HTMLLoader.createRootWindow(true, null, true, new air.Rectangle(0,0,640,480));  

  customerWindow.addEventListener(air.Event.COMPLETE, passDbId);  

  customerWindow.load(new air.URLRequest("app:/view_customer.html"));  

}  

function create() {  

 createWindow = air.HTMLLoader.createRootWindow(true, null, true, new air.Rectangle(0,0,640,480));  

 createWindow.addEventListener(air.Event.COMPLETE, passDb);  

 createWindow.load(new air.URLRequest("app:/new_customer.html"));  

}  

function passDbId(event) {  

 event.target.window.receiveDb(db);  

 event.target.window.display_customer(current_id);  

}  

function passDb(event) { event.target.window.receiveDb(db); }
```

在上面的代码中，视图函数(以及相关的`create`函数)，与`passDb`和`passDbId`函数一起工作；然后它们会启动新的窗口，呈现适当的 HTML 页面。记住，我们需要跨多个页面共享我们的数据库连接，所以我们将在目标页面中定义一个`receiveDb`函数。然而，由于我们连接到数据库的自定义 JavaScript 函数是从页面中调用的，所以我们需要先等待父页面加载！

AIR 中的 HTML Loader APIs 允许这些类型的窗口逻辑非常容易。让我们来分解一下:

正如您在上面的代码中看到的，`createRootWindow`构造函数接受一些参数，包括一个代表要创建的窗口尺寸的`air.Rectangle()`对象。这个函数为我们提供了一个窗口对象，我们向其中添加了一个新的`COMPLETE`事件的事件监听器。每当新页面完成加载时，都会触发此事件侦听器。您会注意到在 view 方法中，我们在使用 URL 请求加载页面之前临时存储了当前 ID。页面加载后，回调函数会传递数据库连接，并指示新页面显示客户详细信息。

您可能还记得在上一篇文章中，我们在 URL 中使用了一个散列字符串在页面之间传递信息(例如，`view_customer.html#id/1`)。这是一个可靠的破解，因为 AIR 是一个不使用地址栏的受控环境——在这一点上，我们的魔力就会显现出来！也就是说，利用 HTML loader 系统——正如我们在这里所做的——是一种更加稳定和有用的方法，因为它允许我们根据需要传递整个对象。

##### 查看记录和接收信息

对！我们已经建立了一个从数据库中读取记录的系统，但是现在我们需要查看单个客户的记录。我们已经知道我们将需要两个函数——`receiveDB`和`display_customer`——这两个函数都从我们的主*customers.html*窗口接收信息。我们的`view_customer.html`文件也填充了样本数据，但是我们将再次使用 jQuery 来替换数据库中的真实信息。

编辑您的`view_behaviour.js`文件，如下所示:

```
dbConn = null;  

function receiveDb(dbFromParent) {  

  dbConn = dbFromParent;  

}  

function display_customer(id) {  

  dbQuery = new air.SQLStatement();  

  dbQuery.sqlConnection = dbConn;  

  dbQuery.text = "SELECT * FROM customers WHERE id=:id";  

  dbQuery.parameters[":id"] = id;  

  try {  

    dbQuery.execute();  

    dbQuery.clearParameters();  

  }   

  catch (error) {  

    air.trace("Error retrieving notes from DB:", error);  

    air.trace(error.message);  

    return;  

  }  

  var customerData = dbQuery.getResult();  

  $("#cust_name").val(customerData.data[0].name);  

  $("#cust_phone").val(customerData.data[0].phone);  

  $("#cust_notes").text(customerData.data[0].notes);  

}
```

这里我们定义了一个`receiveDb`函数，它简单地接受数据库连接的句柄作为参数，并将其存储在一个局部变量中。我们的`display_customer`函数是真正的工作发生的地方，检索有问题的记录并将其输出到页面。

注意，我们使用`textarea`来表示注释信息，而不是为个人姓名和电话字段创建输入字段。虽然我不会在本教程中介绍如何添加“编辑”功能，但逻辑与我们刚刚编写的记录检索非常相似；唯一的区别是，你显然会使用一个`UPDATE`语句，而不是`SELECT`语句。

***注意:AIR 和 SQL 的限制**
如果您发现您必须在本地 SQL 数据库中进行一些重要的信息操作，请记住 AIR 实现有许多限制—[Adobe AIR 语言参考有更多信息](http://help.adobe.com/en_US/AIR/1.1/jslr/index.html?localDatabaseSQLSupport.html)。*

数据库逻辑与我们检索所有客户记录的逻辑非常相似，只是这一次我们使用了参数化语句，如下面两行所示:

```
dbQuery.text = "SELECT * FROM customers WHERE id=:id";  

dbQuery.parameters[":id"] = id; 
```

这相当简单；首先我们定义一个命名参数`:id`，然后我们分别提供该参数的值。虽然我们可以在 SQL 查询中使用文字值达到同样的效果，但是像这样的预处理语句要可靠得多，所以我更喜欢使用它们。虽然对于本教程来说这可能有点过了，但是使用预处理语句更安全(它提供了开箱即用的 SQL 注入检验)并且更快(查询可以重用，而不必每次都重新编译它们)。更多详情，请查阅 AIR 手册，特别是关于使用声明中参数的[部分。](http://help.adobe.com/en_US/AIR/1.1/devappshtml/WS5b3ccc516d4fbf351e63e3d118666ade46-7d42.html)

##### 创建新记录

现在我们有了一个稳定的界面来查看我们的数据，我们需要能够创建记录。作为一家企业，能够将现有数据附加到客户记录中是非常重要的。AIR 的本地拖放功能非常适合这一点——使用纯 JavaScript，我们的应用程序可以非常容易地接受多个文件。一旦我们有了文件，我们就可以复制或移动它们，阅读它们，将它们上传到服务器，甚至在我们的应用程序中显示它们。在这个例子中，我们只是读取一个文本文件，但是我们可以根据需要轻松地扩展它，以处理更复杂的文件类型。

在上一篇文章中，我们构建了一个简单的创建表单作为我们的 Note Storage 应用程序的一部分，这个应用程序的逻辑基本相同。我们将从文本字段中读取数据，构造一个 SQL `INSERT`语句，并对我们的数据库执行它。然而，为了实现拖放功能，我们需要为我们的文件指定一个目标区域，或**拖放区**。拖放区是窗口上的一个区域，用于响应某些鼠标事件(例如一组文件被拖放到其上)。我们将构建这个拖放区，以便将接收到的数据存储在一个变量中供以后使用。

AIR APIs 允许我们显式地检查拖放到目标区域的文件(相对于纯文本、URL、HTML 或其他剪贴板数据)。然后这些文件就可以作为`air.File`对象供我们操作了。类似于我们操作数据库文件的方式，我们可以使用一组通用接口来操作这些“丢弃”的文件。

在编辑器中打开*new _ behavior . js*文件，并添加以下内容:

```
notes = "";   

dbConn = null;   

function receiveDb(dbFromParent) {   

  dbConn = dbFromParent;   

}   

$(document).ready(function(){   

  $("#create").click(function(){ create(); });   

  $("#clear").click(function(){ clear(); });   

  var dropbox = document.getElementById("filedrop");   

  dropbox.addEventListener("dragenter", dragEnterOver);   

  dropbox.addEventListener("dragover", dragEnterOver);   

  dropbox.addEventListener("drop", dropHandler);   

});
```

这段代码的重要部分是我们绑定到特定事件的各种与接口相关的函数。我们稍后会创建这些函数。现在，让我们建立我们的拖放区。

打开 new_customer.html 文件，快速查看一下文档的结构。您将会看到我们有一个带有“`filedrop`”的`id`的`div`，它包含一个空的`span`元素。当用户将文件拖动到应用程序页面上时，AIR 会询问构成页面一部分的每个 DOM 元素是否接受文件拖放；这给了我们的`div`一个告诉 AIR 它会的机会！

这里实际上有三个事件，但是前两个(`dragenter`和`dragover`)可以由同一个事件处理程序来处理。下面是该函数的代码:

```
function dragEnterOver(event) {   

  for (var t = 0; event.dataTransfer.types && t < event.dataTransfer.types.length; t++)   

  {   

    if (event.dataTransfer.types[t] == "application/x-vnd.adobe.air.file-list")   

    {   

      event.preventDefault();   

    }   

  }   

}
```

上面的代码将简单地确认拖放区正在接收文件列表并使用适当的 MIME 类型(在 Adobe 帮助页面上记录了一些其他选项[)。如果拖放区确实要接受任何拖到它上面的文件，我们需要首先禁用拒绝文件拖放的默认事件。这将允许操作系统显示一个漂亮的“允许拖放”可视指示器:在 Windows Vista 上，该指示器是一个蓝色加号(+)，而红色 X 表示不允许拖放。](http://help.adobe.com/en_US/AIR/1.1/devappshtml/WS7709855E-7162-45d1-8224-3D4DADC1B2D7.html)

接下来，我们需要处理实际的下降:

```
function dropHandler(event) {   

  var files = event.dataTransfer.getData("application/x-vnd.adobe.air.file-list");   

  for (var num = 0; num < files.length; num++) {   

    fileStream = new air.FileStream();   

    fileStream.addEventListener(air.Event.COMPLETE, readFile);   

    fileStream.openAsync(files[num], air.FileMode.READ);   

  }   

}   

function readFile(event) {   

  notes = fileStream.readMultiByte(fileStream.bytesAvailable, "iso-8859-1");   

  $("#filedrop span").text(notes.substr(0,100) + "...");   

}
```

正如我们对`HTMLLoader` `COMPLETE`事件所做的那样，我们为`FileStream`事件使用了一个处理程序，它指示文件的打开已经完成。从上面的代码中可以看出，我们正在异步监听这个事件。我们采用这种方法是因为读取一个文件可能需要一段时间，我们不想在此期间锁定应用程序。

正如您所看到的，我们使用事件监听器的回调将数据直接从文件读入一个字符串对象。手册中有关于[文件读/写工作流程](http://help.adobe.com/en_US/AIR/1.1/devappshtml/WS5b3ccc516d4fbf351e63e3d118666ade46-7dc8.html)和 [FileStream 打开模式](http://help.adobe.com/en_US/AIR/1.1/devappshtml/WS5b3ccc516d4fbf351e63e3d118666ade46-7db2.html)的更多信息。

我们需要在这里指定一个字符编码类型–对于纯文本，我们使用的选项(西欧 ISO)通常是合适的；查看 [AIR 手册的支持字符集列表](http://livedocs.adobe.com/flex/2/langref/charset-codes.html)。

给用户一些提示总是好的，表明他们的操作已经成功了。在本例中，我们显示了他们丢弃的文件示例(前 100 个字符)，让他们知道他们的文件被成功丢弃。

最后，我们需要实际的记录创建例程，以及我们前面提到的`clear`方法。将以下代码添加到您的*new _ behavior . js*文件的末尾:

```
function create() {   

 dbQuery = new air.SQLStatement();   

 dbQuery.sqlConnection = dbConn;   

 dbQuery.text = "INSERT INTO customers (name,phone,notes) VALUES (:name,:phone,:notes)";   

 dbQuery.parameters[":name"] = $("#name").val();   

 dbQuery.parameters[":phone"] = $("#phone").val();   

 dbQuery.parameters[":notes"] = notes;   

 try { dbQuery.execute(); } catch (error) {   

  air.trace("Could not create new customer record.");   

 }   

}   

function clear() {   

 notes = ""; $("#filedrop span").text("");   

} 
```

现在，您应该对这段代码的大部分相当熟悉了——一个带有命名参数的 SQL `INSERT`语句。请注意，我们在之前的 AIR 文章中使用了 JavaScript 转义例程；这些步骤在这里是不必要的，因为这个功能是通过使用命名参数来实现的。

为了测试这个应用程序，尝试将一个样本数据文件拖到拖放区:`customerdata.txt`，它存在于项目文件夹中，已经包含在框架代码归档中。

##### 我们完事了。

在 Aptana 中保存所有打开的文件，并从**运行**按钮菜单中选择您的项目名称，您的 CRM 应用程序应该会出现。尝试添加一个条目(通过**文件**菜单)并刷新主屏幕以查看您新创建的记录。

你可以在这里[下载我完成的项目文件(包括 Aptana 项目数据)。](https://www.sitepoint.com/examples/AdobeAIR/air2-crm-final.zip)

##### 进一步阅读

如果您有兴趣进一步探索本文中涵盖的主题，Adobe 有许多精彩的在线资源，您可能会觉得很方便:

*   [拖放](http://help.adobe.com/en_US/AIR/1.1/devappshtml/WS5b3ccc516d4fbf351e63e3d118666ade46-7e8a.html)
*   [使用原生窗口](http://help.adobe.com/en_US/AIR/1.1/devappshtml/WS5b3ccc516d4fbf351e63e3d118666ade46-7e17.html)
*   [读写文件](http://help.adobe.com/en_US/AIR/1.1/devappshtml/WS5b3ccc516d4fbf351e63e3d118666ade46-7dc2.html)
*   [AIR 1.1 HTML/AJAX 开发人员指南](http://help.adobe.com/en_US/AIR/1.1/devappshtml/)
*   [AIR 1.1 HTML/AJAX 语言参考](http://help.adobe.com/en_US/AIR/1.1/jslr/index.html)

如果您对本文中的任何代码示例都不太确定，请在下面发表评论，我会尽力帮助您。

##### 自我测试

**你可以通过[小测验](https://www.sitepoint.com/quiz/air/part2/)来测试你对本教程的理解程度，并且因为你的努力，你将获得免费的袖珍指南《面向 JavaScript 开发者的 Adobe AIR》。该指南将由 Adobe Systems 免费提供，但此优惠仅适用于前 100 人，所以赶快加入吧！(如果你确实错过了，你仍然可以[免费下载 PDF 格式的书](https://www.sitepoint.com/quiz/air/part2/)，时间有限。)**

**[参加小测验](https://www.sitepoint.com/quiz/air/part2/)！**

## 分享这篇文章