# Yii 路由、活动记录和缓存

> 原文：<https://www.sitepoint.com/yii-routing-active-record-caching/>

几乎所有的现代网络应用都有 3 个主要关注点:从数据库中简单有效地检索数据，缓存网络内容和 URL 重写以创建用户友好的 URL。Yii，像任何其他好的框架一样，为上述所有问题提供了简单易行的解决方案。在我之前的文章中，我介绍了用 Yii 构建一个简单 CRUD 应用的基础。在本教程中，我们将看看 Yii 如何通过其主动记录支持来大大简化数据库驱动网站的开发。另外，Yii 允许你通过实现用户友好的 URL 和强大的缓存技术来进一步改进你的网站。让我们开始吧！

## 进入数据库

我们将创建一个非常简单的 web 应用程序，用于使用 Yii 存储和检索不同智能手机的详细信息。

为了创建一个框架 Yii 应用程序，我们将使用 Yii 框架附带的命令行工具`yiic`。可以在`YiiRoot/framework`目录下找到。因为我在 windows 上，所以它在`C:\yii\framework`下。我建议将这个目录添加到您的系统路径中，这样您就可以从任何文件夹运行`yiic`。如果你在 Windows 上，你还需要把`php.exe`的路径添加到你的系统路径中。

只需转到保存所有 PHP 项目的文件夹(我在 Windows 上，所以在我的例子中是 C:\wamp\www)，然后运行命令:`yiic webapp project_name_here`。我将这个项目命名为**小配件商店**。这应该会创建一个新的 Yii 项目，带有必要的文件夹层次结构。

默认情况下，控制器中定义的 Yii 动作可以通过以下方式访问:

`http://localhost/gadgetstore/index.php?r=controller/action`

因为我们想成为用户友好的，我们不想要这种类型的网址。要更改这一点，打开配置文件，即`main.php`，取消对以下行的注释:

```
'urlManager'=>array(        
'urlFormat'=>'path',        
'rules'=>array(
'<controller:\w+>/<id:\d+>'=>'<controller>/view',
'<controller:\w+>/<action:\w+>/<id:\d+>'=>'<controller>/<action>',        '<controller:\w+>/<action:\w+>'=>'<controller>/<action>',
),
),
```

在第 2 行后加上`'showScriptName'=>false`，抑制来自 URL 的烦人的`index.php`。另外，不要忘记在项目的根目录下添加一个包含以下内容的`.htaccess`文件:

```
Options +FollowSymLinks
IndexIgnore */*
<IfModule mod_rewrite.c>
RewriteEngine on
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . index.php
</IfModule>
```

现在，可以用更简单的方式访问 URL:

`http://localhost/gadgetstore/controller/action`

将应用程序配置为使用数据库只需在配置中添加几行代码。只需打开您的`/protected/config/main.php`文件并取消对以下行的注释:

```
'db'=>array(
'connectionString' =>'mysql:host=localhost;dbname=testdb',
'emulatePrepare' => true,
'username' => 'root',
'password' => '',
'charset' => 'utf8',
),
```

在这里，我们基本上是配置我们的应用程序，以使用特定的 MySQL 或 MariaDB 数据库。在我的例子中，数据库名是`testdb`。相应地更改上面代码片段中的 dbname。其余的细节不言自明。

现在，让我们快速建立一个数据库表，其中将包含有关我们神奇的小工具的信息。

表格结构如下:

```
Table Name: phone

Column        Type    
id            int(10)    
name          varchar(65)    
price          int(6)    
memory          varchar(65)    
camera          varchar(65)    
screen_size   varchar(65)    
os          varchar(65)
```

目前，我们将只保留所有智能手机共有的 5 个简单属性，即价格、内存、摄像头、屏幕尺寸和操作系统。

现在，下一步是创建保存智能手机属性的活动记录类。每个 AR 类对应一个数据库表，每个 AR 实例代表该表中的一行。现在让我们创建一个名为 Phone 的 AR 类。为了生成这个类，我们将使用 gii，Yii 附带的自动代码生成工具。在浏览器中打开:`http://localhost/gadgetstore/gii`并进入模型生成器。提供表名(在我的例子中是`phone`)并在 model class 字段中输入 Phone。看看下面的截图。

![](img/6af8450bfa6c6661301a72e3ebf08520.png)

现在点击 preview 并点击 generate 来创建模型类。你可以在`protected/models`目录中找到它。

AR 类生成后，您可以在任何地方实例化它，并访问数据库表属性作为 AR 实例的属性。这是通过`__get()`魔法方法实现的。例如，做以下事情是完全合法的:

```
$model=new Phone;   //creates new model instance
$model->name="Samsung Galaxy Note 3"; //sets name property
$model->price=299;    //sets price property
$model->os="Android 4.3"; //sets os property
```

现在，要保存模型，您需要做的就是对它调用 save()。

```
$model->save(); //saves the phone to the database.
```

更新现有的行也非常简单。

```
$model=Phone::model()->findByPK(10);  //phone with id 10
$model->price=300;
$model->save(); //save the updates in DB.
```

要删除一行:

```
$model=Phone::model()->findByPK(10);  
$model->delete(); //gone from the table
```

Active Record 基本上提供了一种简单的方法来执行 CRUD 操作，这些操作通常涉及简单的 SQL 命令。对于复杂的查询，你可能想切换到 [Yii DAO](http://www.yiiframework.com/doc/guide/1.1/en/database.dao) 。

在生成的模型中只需要一个小的改变。打开模型类电话，在`rules()`功能中找到下面一行:

```
array('id, name, price, memory, camera, screen_size, os', 'safe', 'on'=>'search')
```

现在将上面一行中的`search`替换为`insert`。我们这样做的原因将在随后的章节中变得清楚。

现在您已经准备好 AR，我们需要创建一个控制器，它将使用 AR 类在数据库中实际执行插入/更新(也称为 upsert)。只需在 protected/controllers 中创建一个文件`PhoneController.php`。在文件中创建一个空的 PhoneController 类。

```
class PhoneController extends Controller{

}
```

现在让我们给这个类添加一个函数`actionAdd()`,如下所示:

```
public function actionAdd(){
    $model=new Phone;
    if(isset($_POST['Phone'])) //line 3
    {
        $model->attributes=$_POST['Phone']; //line 5
        if($model->validate()){
          $model->save();
          $this->redirect("view/$model->id"); //line 6
        }
    }
    $this->render('add',array('model'=>$model));
}
```

在这个函数中，我们向表中添加了一个新行。但在此之前，我们需要创建一个视图文件，显示一个表单，通过它可以输入手机的各种属性值。通过 gii 的表单生成器可以非常容易地生成表单。您可以在浏览器中打开 gii 并进入表单生成器。只需输入模型名称(电话)和视图名称(在本例中为电话/添加),然后单击 generate。它将在`protected/views/phone`中创建一个视图文件`add.php`。

在上面的代码片段中，我们首先检查请求是否是 POST。如果没有，那么我们只显示用户可以输入值的表单。但是如果是回发，我们需要将数据存储在表中。为了捕获传入的数据，我们执行以下操作:

```
$model->attributes=$_POST['Phone'];
```

上述操作被称为大规模赋值。这里，模型的所有属性都被赋予了在请求中接收到的值。还记得我们之前是如何在`Phone`类中将场景从搜索改为插入的吗？因为这个巨大的任务。每当我们实例化一个新的模型时，场景就会被插入。因此，如果我们声明属性只在搜索场景中是安全的，那么这个大规模的赋值将会失败。这就是我们将`Phone`的属性声明为安全插入的原因。

接下来，我们检查是否有验证错误，如果没有，我们继续保存模型。然后，用户被重定向到一个 URL，在那里他可以看到添加的智能手机。更新和查看功能以类似的方式实现。

简单说明一下:你可以[下载演示应用](https://github.com/phpmasterdotcom/SandeepPanda-yiiplus)并查看源代码。在这里，您可以看到 PhoneController 的附加功能和视图是如何实现的。

## 用户友好的网址总是好的

目前，我们用于查看新添加智能手机的 URL 使用以下格式:

```
http://localhost/gadgetstore/phone/view/[id]
```

但是让它更吸引人一点怎么样？也许我们可以通过在 URL 中显示智能手机的名称来打动用户？也许是类似`http://localhost/gadgetstore/phones/samsung-galaxy-s4`的东西？

要实现这种类型的 URL，只需将下面一行添加到`protected/config/main.php`中的 urlManager 规则。

```
'phones/<name:[\w\-]+>'=>'phone/show'
```

总的来说，所有规则如下:

```
'urlManager'=>array(
'urlFormat'=>'path',
'showScriptName'=>false,
'rules'=>array(
'phones/<name:[\w\-]+>'=>'phone/show', //rule 1
'<controller:\w+>/<id:\d+>'=>'<controller>/view',
'<controller:\w+>/<action:\w+>/<id:\d+>'=>'<controller>/<action>',
'<controller:\w+>/<action:\w+>'=>'<controller>/<action>',
),
),
```

规则 1 的意思是任何以“phones/”开头的 url 都应该由 PhoneController 类的 actionShow()函数来处理。此外,' phones/'后面的部分将作为名为' name '的 GET 请求参数传递给`actionShow()`函数。通过这样做，我们可以捕获请求参数，并利用它按名称查找所需的智能手机！

`actionShow()`功能实现如下:

```
public function actionShow(){
  if(isset($_GET['name'])){
    $name=$_GET['name'];
    $name=implode(' ',explode('-',$name));
    $phone=Phone::model()- >find('name=:name',array(':name'=>$name));
    if($phone==null)
    throw new CHttpException(404,'Page Not Found');
     $this->render('view',array('phone'=>$phone));
   }
   else
      throw new CHttpException(404,'Page Not Found');
}
```

记住 Yii 的 URL 管理是相当庞大的，你可以用它创建真正令人印象深刻的 URL 模式。这只是一个基础教程，展示了 URL 管理模块的一部分功能。

此外，请注意，虽然通过名称选择条目是完全合法的，但您应该始终通过唯一的 slug(URL 优化字符串)来选择它们，以防一些条目具有相同的名称。这是通过在每次插入时生成唯一的基于名称的 slug 来实现的。例如，如果插入三星 Galaxy S4，slug 可能是`samsung-galaxy-s4`。然而，如果另一款三星 Galaxy S4 型号出现，新的 slug 应该类似于`samsung-galaxy-s4-01`，只是与第一款不同。

## 缓存内容以获得更好的性能

Yii 的缓存实现有很多种类型。在最简单的场景中，我们可能通过查询缓存来完成任务。

使用活动记录时，我们可以指定将检索到的数据放入缓存，然后使用缓存而不是命中数据库。在我们的应用程序中，可以通过以下代码片段实现查询缓存:

```
$phones=Phone::model()->cache(2000,null,2)->findAll();
```

上面的代码从数据库中检索数据，并将其添加到缓存中。第一个参数指定缓存将存在的秒数。第二个参数是依赖关系，在我们的例子中为空。第三个参数表示要缓存的后续查询的数量。由于我们将 2 指定为第三个参数，接下来的 2 个查询将被缓存。因此，下两次请求到来时，将在缓存中搜索内容，而不是访问数据库。如果您每秒收到的请求太多，这显然会提高性能。

还有其他高级类型的缓存，但超出了本文的范围。如果你想知道更多细节，请在下面的评论中告诉我们。

## 结论

上面讨论的模块的能力确实是巨大的。由于不可能在一个教程中涵盖所有方面，所以省略了一些要点。所以，除了让我们知道你想多读些什么之外，这里有你应该在本教程之后开始阅读的前三件事。

*   Yii 的 AR 提供了一个非常好的 API 来以不同的方式从数据库中选择数据。您可以通过属性、主键和您自己的搜索条件来查找数据。请点击上面的链接了解更多信息。

*   您应该开始阅读如何在配置文件中的 URL 规则中使用不同的模式。这让你有能力在你的应用程序中创建令人印象深刻的网址。除此之外，创建自己的 URL 规则类也很有用。

*   查询缓存并不是 Yii 中唯一可用的缓存机制。还有其他几种实现方式。Yii 文档有一个关于 Yii 缓存的很好的教程。

感谢您的阅读，不要忘记查看[源代码](https://github.com/phpmasterdotcom/SandeepPanda-yiiplus)以获取更多信息！

## 分享这篇文章