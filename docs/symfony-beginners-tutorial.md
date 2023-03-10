# 学习 symfony:初学者教程

> 原文：<https://www.sitepoint.com/symfony-beginners-tutorial/>

随着 symfony 1.0 的发布，是时候让那些还没有尝试过的人看看这个漂亮的框架里面有什么了。symfony 是稳定的，有完整的文档，并在开源 MIT 许可下发布，被数百个网站使用，包括一些非常大的网站(Yahoo！拥有 2000 万用户群的书签是用 symfony 构建的)。如果你还没有花时间看 symfony 项目网站上的介绍性截屏，这个简单的教程将带你了解基础知识。

学习和理解 symfony 的最好方法是使用它，因此本文将带您使用这个框架创建一个相册应用程序。你已经知道这样一个应用程序应该提供的基本功能:上传照片、描述和标记照片的能力，以及访问者浏览和评论你的照片的能力。

Symfony 是用 PHP 编写的模型-视图-控制器(MVC)框架，旨在构建 web 应用程序。如果你已经熟悉 MVC 范式，你就不会对 symfony 组织脚本的方式感到惊讶。如果您不熟悉 MVC，您只需要理解将代码分成三个部分——逻辑代码(称为模型)、表示代码(视图)和请求处理代码(控制器)——是确保代码可维护性和可重用性的好方法。

Symfony 不仅是 PHP 中的 MVC 实现，它还集成了许多方便 web 应用程序开发的对象——并用一致的语法将它们集成在一起。智能 URL、代码生成、简易模板、国际化、缓存、自动化表单验证和 Ajax 是 symfony 最受赞赏的特性。使用 symfony 开发应用程序与使用任何其他框架或者根本不使用任何框架来构建略有不同。它速度更快，效率更高，而且简单有趣。但是说够了，让我们看看一些代码。

##### 初始化应用程序

首先，你需要得到 symfony 的文件。幸运的是，该框架的一个发行版打包了一个空的应用程序和一个已经配置好的 SQLite 数据库，并且开箱即用。symfony 只需要一个带 PHP5 和 shell 访问的 web 服务器就可以工作了。

要安装 symfony，只需下载 [sf_sandbox.tgz 文件](http://www.symfony-project.com/get/sf_sandbox.tgz)。

将它解压到服务器目录的 web 根目录下。您可以通过浏览这个 URL 来检查安装是否顺利进行(注意，它可能会因您的个人设置而有所不同):`http://localhost/sf_sandbox/web/frontend_dev.php`。

![1566_fig1](img/cbb3ca02ddc4ad26e7d8b1af2cedeeb0.png)

这个新项目的目录结构非常容易理解:

```
sf_sandbox/      // project directory  
apps/      // application files  
batch/     // batch process scripts  
cache/     // cache files  
config/    // configuration files  
data/      // data files and scripts  
doc/       // documentation files  
lib/       // vendor libraries and general purpose scripts  
log/       // log files  
plugins/   // plugins  
test/      // unit and functional tests  
web/       // public web directory
```

对于新的应用程序，这些目录中有许多是空的；这种目录结构可视为有效组织项目文件的指南。好消息是，一旦你理解了这个目录结构是如何工作的，你就能够理解任何 symfony 项目:它们都是以同样的方式构造的。

在大多数情况下，web 应用程序将数据存储在关系数据库中，因此数据结构必须被描述为一组通过外键链接的表中的列。对于相册应用程序，数据模式由三个表组成:`photo`、`tag`和`comment`。为了描述它们的列和关系，我们使用一种简单的格式，称为 YAML(发音为“yamel”)，如下所示:

```
propel:  photo:    id:          ~    file_path:   varchar(50)    description: longvarchar    created_at:  ~  tag:    photo_id:    ~    name:        varchar(50)  comment:    photo_id:    ~    author:      varchar(50)    body:        varchar(50)    created_at:  ~
```

YAML 是 XML 表示数据结构的替代方案。语法非常简单:结构通过缩进显示，键/值对用冒号分隔。第一行代表该模式的数据库连接的名称。Propel 是这个沙盒中的默认名称——它指的是包含的 SQLite 数据库。Propel 作为默认名称可能听起来很奇怪，但事实上，这个名称来自集成到 symfony 中的第三方组件。

在这个模式中，使用了波浪符号(`~`)而不是数据类型的显式声明，因为 symfony 可以从键的名称中推导出数据类型。例如，`photo`表的`id`列显然是主键，所以它的类型应该是整数，并且应该自动递增。`created_at`名称显然指的是一个日期，所以应该作为时间戳输入。这两个`photo_id`列看起来非常像照片表的`id`列的外键。

Symfony 知道 YAML，理解这个模式正如我们所描述的。打开`sf_sandbox directory` ( `sf_sandbox/config/schema.yml`)的主`config/`目录下的`schema.yml`文件，将上述 YAML 代码放入该文件中。Symfony 现在准备为该模式生成对象关系映射(ORM)。

什么是对象关系映射？设计良好的 web 应用程序不会使用 SQL 直接访问数据；相反，它们使用对象与数据库进行交互。Symfony 使用内部代码生成器，根据在`schema.yml`文件中定义的对象关系映射，创建对应于照片、标签和评论表的对象。要生成这些对象，您只需要从 shell 中调用一个命令。这是我们认识 symfony 命令行界面(CLI)的提示！

这是位于您的项目的根的 symfony 脚本:

```
$ cd sf_sandbox $ php symfony propel-build-all
```

`propel-build-all`命令(还是那个 propel 名称)在`lib/model/`目录下生成模型类(并在项目的 SQLite 数据库中创建相关的表)。这些类被命名为`Photo`、`Tag`和`Comment`，因为 symfony 对类名的约定是 CamelCase。像 symfony 中的大多数类一样，模型类是自动加载的，这意味着您可以使用它们，而无需在代码中手动“要求”它们的文件(使用 include 或 require 语句)。只要记得每次添加新类时调用 clear-cache symfony 脚本，以便 symfony 自动加载系统可以考虑它。由于您刚刚创建了一些类，现在继续调用该脚本:

```
$ php symfony clear-cache
```

我们稍后将回到这些类，但首先让我们看看照片发布接口。

##### 生成站点管理面板

我们将使用 symfony 框架的一个很棒的特性——管理生成器来创建一个站点管理面板。管理生成器使用关系模式作为创建数据操作界面的指导方针。我们的站点管理员需要编辑来自`Photo`类的数据，所以让我们为这个类生成一个管理面板:

```
$ php symfony propel-init-admin frontend photo Photo
```

Symfony 在应用程序和模块的层次结构中组织一个项目的页面。尽管这种结构将目录添加到项目的结构中，但它使维护变得更容易，尤其是对于大型站点。所以一个模块包含几个页面，一个应用包含几个模块，一个项目可以包含多个应用。您安装的沙箱是一个 symfony 项目，打包了一个名为 frontend 的空应用程序。这意味着上面清单中的命令读作:“在应用程序前端，在一个名为 photo 的模块中，基于模型类 Photo 创建一个站点管理”。要查看新模块的样子，请浏览到`http://localhost/sf_sandbox/web/frontend_dev.php/photo`。

![1566_fig3](img/637573b58da7d624aa2f3bcafd16d7f4.png)

生成管理的优点之一是，它允许您在项目的早期向数据库添加数据——甚至在编码之前。生成的管理由两个页面组成，我们称之为列表视图和编辑视图。Symfony 使用该模式构建表格、表单和表单处理脚本。尽管所有代码都已生成，但您可以随意覆盖它。然而，定制生成的管理的最佳方式不是编写 PHP 代码，而是使用`generator.yml`文件。这是一个控制生成的模块的配置文件，在这个文件中添加参数会相应地转换列表和编辑视图。让我们看一个例子。

默认情况下，列表视图的列对应于架构中定义的列。对应于主键的列(在本例中为`id`)带有到编辑视图的超链接。但是`id`列对于管理员来说没有任何用处，所以我们应该将其从列表视图中移除。为此，打开位于`apps/frontend/modules/photo/config/`的`generator.yml`文件，并在末尾添加以下代码:

```
generator:    class:              sfPropelAdminGenerator    param:      model_class:      Photo      theme:            default      **list:        display:        [file_path, description, created_at]        object_actions:          _edit:            name:       Edit picture properties**
```

请注意，缩进在 YAML 文件中是有意义的，所以尽量尊重这里显示的缩进。一个重要的建议是永远不要在 YAML 文件中使用制表符——总是使用双倍空格来缩进行。显示行告诉 symfony 在列表中显示哪些列，以什么顺序显示。至于`object_actions`键，它管理每行将显示的按钮。`_edit`按钮是预定义的，所以您不需要担心它调用的脚本。我们只是在这里自定义了按钮的名称。要查看结果，再次浏览到`photo`模块，看看列表视图是如何变化的。

![1566_fig5](img/b0e00deb1526c95b79074874c92a60f0.png)

输入文件路径上传照片用处不大。这个照片管理模块的理想状态是，用户能够浏览他们的文件系统，指向一个图片文件。这很容易做到，不需要任何代码。修改您的`generator.yml`如下:

```
generator:    class:              sfPropelAdminGenerator    param:      model_class:      Photo      theme:            default      list:        display:        [file_path, description, created_at]        object_actions:          _edit:            name:       Edit picture properties      **edit:        display:        [file_path, description]        fields:            file_path:            type:       admin_input_file_tag**
```

您已经知道了`display`键:它控制在视图中显示哪些字段。在编辑视图中，它控制表单中显示哪些字段。我们在这个视图中去掉了`created_at`列是有原因的:symfony 自己处理它。因为它的名字，symfony 将给予这个字段记录创建日期的值。如果你要添加一个`updated_at`栏，symfony 也会自己处理，每次你改变一条记录，它都会更新。

清单中要注意的第二件事是`fields`键，事实上`file_path`列被设置为显示类型为`admin_input_file_tag`的输入。这告诉 symfony 为这个列使用一个文件输入，但是也将相关文件上传到它的`web/uploads/`目录。尝试使用“编辑”视图向相册添加一些图片。

![1566_fig6](img/6d91f8086d0796997ff3b071a24d49e6.png)

如果该模块可以显示上传的照片就好了，这样你就可以检查你的上传是否成功。为此，我们将使用部分列。部分列是一段 PHP 代码，可以在生成的管理中用作列。它是这样工作的。首先，编辑`generator.yml`，将`_photo`列添加到两个视图中的显示键:

```
generator:    class:              sfPropelAdminGenerator    param:      model_class:      Photo      theme:            default      list:        display:        [_**photo**, description, created_at]        object_actions:          _edit:            name:       Edit picture properties      edit:        display:        [_**photo**, file_path, description]        fields:            file_path:            type:       admin_input_file_tag
```

Symfony 识别下划线，并不使用`Photo`对象的方法来获取列值，而是尝试在列中包含一个名为`_photo.php`的文件。在`/apps/frontend/modules/photo/templates`目录下的`Create _photo.php`，在文件内只放置了下面一行:

```
<?php echo image_tag('/uploads/'.$photo->getFilePath()) ?>
```

在加载这个文件之前，symfony 将当前的`Photo`对象放入`$photo`变量中。当您调用`propel-build-model`命令时，`getFilePath`方法是 symfony 基于`schema.yml`自动生成的方法之一。正如您可能已经猜到的，它返回对应于`$photo`对象的`file_path`列的值。在生成的`Photo`类中，每隔一列都有一个相应的 getter 和 setter 方法。`image_tag`函数是一个 symfony 助手——一个返回 HTML 代码的函数，旨在用于模板中。这个函数返回一个`<img>`标签，这正是我们想要的。它的第一个参数必须是相对于`web/`目录的文件路径。再次浏览照片模块，检查图像是否正确显示。

![1566_fig7](img/cac88c80f08ad6e7fd40b153b33d7c69.png)

##### 修改模型

到目前为止，本教程中还没有太多 PHP 代码。这是 symfony 的优势之一:它不会强迫你编写 symfony 可以基于简单数据自行生成的代码。然而，您将需要为模型中的标签编写几行 PHP 代码。首先，让我们探索模型的结构。

`propel-build-all`命令为`lib/model/`目录中的每个表生成两个文件。例如，对于`photo`表，生成的模型文件是`Photo.php`和`PhotoPeer.php`。如果你看看它们的代码，它们实际上包含了从位于`lib/model/om/`目录中的其他类(`BasePhoto`和`BasePhotoPeer`)继承的空类。例如，生成的`lib/model/Photo.php`内容如下:

```
<?php   /**   * Subclass for representing a row from the 'photo' table.   *   *     *   * @package lib.model   */     class Photo extends BasePhoto   {   }
```

当您使用命令行构建模型时，只有`lib/model/om/`目录中的类会被修改。看看它们，看看生成了多少代码。`lib/model/`中的类不会被代码生成器修改(代码生成器只是在第一次创建它们的空壳)；要扩展模型，您应该在这些空类中编写您的方法。如果您决定更改关系模式并重新构建模型，这种机制允许您扩展模型类，而没有丢失修改的风险。模型类系统是可扩展和可伸缩的。

我们将生成的代码分成两个类(`Photo`和`PhotoPeer`)，以区分链接到单个对象的方法和链接到该类而不是其对象的方法，或者，如果您愿意，也可以区分对象和静态方法。`PhotoPeer`类只包含用于检索类`Photo`对象的静态方法。你很快就会明白它是如何工作的。

我们希望能够在照片上添加或删除标签。照片和标记表之间的外键(标记结构中的`photo_id`列)通过生成的`getTags`方法在`Photo`对象中实例化。Symfony 自动生成了这个方法，并把它放在了`BasePhoto`类中，这样`Photo`类也可以使用它。所以，如果你有一个`Photo`对象，你可以像这样得到它的相关标签:

```
$tags = $photo->getTags();
```

没错，不需要用`WHERE`子句调用 SQL 查询——生成的`Base`类会自动执行。我们将使用同样的原理向`Photo`类添加新方法。打开`Photo.php`文件，添加以下方法:

```
// in lib/model/Photo.php   class Photo extends BasePhoto   {    public function getTagsString()    {      $tags = array();      foreach ($this->getTags() as $tag)      {        $tags[] = $tag->__toString();      }      return implode(' ', $tags);    }        public function setTagsString($tagPhrase)    {      // remove old tags      $this->deleteTags();            // set new tags      $tagNames = explode(' ', $tagPhrase);      foreach($tagNames as $tagName)      {        $tag = new Tag();        $tag->setPhoto($this);        $tag->setName($tagName);          $tag->save();      }    }        public function deleteTags()    {      $c = new Criteria();      $c->add(TagPeer::PHOTO_ID, $this->getId());      TagPeer::doDelete($c);    }   }
```

这三种方法有很多需要解释的地方。首先，正如您之前看到的，生成的`getTags`方法返回一个`Tag`对象的数组。以字符串形式返回一张照片的标签并不比调用`implode`函数难。

其次，`setTagsString`方法从一个字符串创建`Tag`对象，并将它们与当前的`Photo`对象相关联。这很好地说明了如何使用动态 setter 方法操作相关对象中的记录字段，这些方法也是由`propel-build-all`命令生成的。对`save`方法的调用触发了对数据库的`INSERT`查询，以基于对象的属性创建记录。最后，`deleteTags`方法调用`TagPeer`类中的静态常量和方法，以及`Criteria`对象中的`add`方法。我们现在不描述这些方法——您只需要理解`doDelete`调用触发数据库中的`DELETE`查询来移除与当前`Photo`相关的所有标签。

最后一件事:为了让`getTagsString`方法工作，类`Tag`的对象必须能够作为字符串输出。我们可以通过向`Tag`类添加一个神奇的`__toString`方法来实现这一点，如下所示:

```
// in lib/model/Tag.php   class Tag extends BaseTag   {    public function __toString()    {      return $this->getName();      }   }
```

添加一个新的 getter 和一个新的 setter 就足以模拟一个新的列。这意味着我们可以在我们的`generator.yml`的显示数组中添加`tags_string`列，就像它是一个实际的字段一样。这就是对象模型的美妙之处:

```
// in apps/frontend/modules/photo/config/generator.yml   generator:    class:              sfPropelAdminGenerator    param:      model_class:      Photo      theme:            default      list:        display:        [_photo, description, **tags_string**, created_at]        object_actions:          _edit:            name:       Edit picture properties      edit:        display:        [_photo, file_path, description, **tags_string**]        fields:              file_path:            type:       admin_input_file_tag          **tags_string:            name:       Tags            type:       input_tag**
```

我们需要定义用于模拟`tags_string`列的输入类型，因为 symfony 没有任何数据可以用来确定它应该为标签使用什么类型的输入(它没有在`schema.yml`中定义)。标准`input_tag`用于此目的。您可以通过向测试照片添加标签来检查照片模块现在是否能够正确处理标签。请确保用空格分隔标签。

![1566_fig8](img/32b08fe10d99fe6bb60170d51147f958.png)

##### 使用插件

目前的照片上传功能有一个主要缺点:大图片在列表和编辑视图中表现不佳。上传图片时，我们需要保存图片的缩略图。幸运的是，由于 sfThumbnail 插件，symfony 可以生成缩略图。插件是为给定应用程序安装的框架的扩展。要安装插件，只需用`symfony`命令调用插件安装任务，如下所示:

```
$ php symfony plugin-install http://plugins.symfony-project.com/sfThumbnailPlugin
```

这个命令使用 PEAR 下载并安装一个 symfony 插件包。如果没有安装 PEAR，可以[下载 sfThumbnailPlugin 包](http://plugins.symfony-project.com/sfThumbnailPlugin)并解压到`plugins/`文件夹下。清空缓存(使自动加载系统能够找到位于新的`sfThumbnailPlugin/`目录中的类)，插件就可以使用了。

```
$ php symfony clear-cache
```

我们将使用插件提供的`sfThumbnail`类来创建每个上传文件的缩略图。最好的地方是在`Photo`类的`setFilePath()`方法中。将以下内容添加到`Photo.php`文件中:

```
// in lib/model/Photo.php     public function setFilePath($value)     {      parent::setFilePath($value);      $this->generateThumbnail($value);     }     public function generateThumbnail($value)     {      parent::setFilePath($value);      $uploadDir = sfConfig::get('sf_upload_dir');      $thumbnail = new sfThumbnail(150, 150);      $thumbnail->loadFile($uploadDir.'/'.$this->getFilePath());      $thumbnail->save($uploadDir.'/thumbnail/'.$this->getFilePath(), 'image/png');     }
```

上面的`setFilePath`方法首先调用`Photo`类继承的`BasePhoto`类中定义的`setFilePath`方法。然后基于原始文件创建一个 150x150px 的缩略图，保存在`uploads/thumbnail/`目录下。在`generateThumbnail`类中，我们使用了`sfConfig`类来获取上传目录的路径。当前应用程序的上传目录可通过`sf_upload_dir`参数访问。注意 sfThumbnail 插件使用 GD 扩展，所以如果它还没有启用，你必须在你的`php.ini`中启用它。此外，为了让`save`方法工作，`uploads/thumbnail/`目录必须存在，所以在尝试上传新图片之前不要忘记创建它:

```
$ cd web/uploads     $ mkdir thumbnail     $ chmod 777 thumbnail
```

我们还希望`_photo`部分列使用缩略图而不是原始图片，因此编辑`_photo.php`如下:

```
// in apps/frontend/modules/photo/templates/_photo.php     <?php echo image_tag('/uploads/**thumbnail/**'.$photo->getFilePath()) ?>
```

现在尝试上传新图片，您将看到生成的照片模块显示缩略图，而不是全尺寸图片。

![1566_fig9](img/b41e1f0ea4ae28a506b9fb7260825c13.png)

我们还将使用一个插件来确保照片模块的安全。Symfony 已经有了内置的安全特性，只允许有特殊证书的认证用户访问一个模块，但是 sfGuard 插件自动完成所有的用户管理。安装它就像 sfThumbnail 插件一样，使用`plugin-install`命令(记得首先返回到根项目目录):

```
$ php symfony plugin-install http://plugins.symfony-project.com/sfGuardPlugin
```

该插件带有一个模块，但你不能访问该模块，除非它在应用程序设置中启用。此外，这个模块可以取代与 symfony 捆绑在一起的默认登录页面。打开`apps/frontend/config/settings.yml`文件，在注释行前添加以下内容(YAML 的注释以`#`开头):

```
// in apps/frontend/config/settings.yml     all:      .actions:        login_module:           sfGuardAuth        login_action:           signin      .settings:        enabled_modules:        [default, sfGuardAuth, sfGuardUser]
```

你还需要告诉 symfony 使用 sfGuardPlugin 的所有用户安全特性。为此，只需更改类`myUser`扩展的父类。`myUser`是 symfony 中处理用户会话的类。它通常会扩展`sfBasicSecurityUser`，所以将`myUser.class.php`文件修改如下:

```
// in apps/frontend/lib/myUser.class.php     class myUser extends sfGuardSecurityUser     {     }
```

您还必须告诉 symfony，照片模块的所有操作现在都需要验证。为此，在`modules/photo/config/`目录中创建一个名为`security.yml`的文件，并写入:

```
// in apps/frontend/modules/photo/config/security.yml     all:      is_secure: on
```

我们快到了！sfGuardPlugin 附带了一个用户管理模块，允许添加新用户，以及更改他们的 id 和密码。当然，密码并不存储在数据库的清晰视图中，因为这将带来严重的安全风险。用户管理模块依赖于一些数据库表和模型类。插件中包含了一个模式，所以我们所需要的就是用它来升级模型类和数据库的`propel-build-all`任务。但是要小心，这个任务从头开始重建数据库，删除所有现有的数据。您可能已经在数据库中有了数据(至少来自前面的测试)，所以键入以下三个命令将数据转储到一个 YML 文件中，清除缓存，构建模型和数据库，并再次将数据加载到数据库中:

```
> php symfony propel-dump-data frontend testdata.yml     > php symfony cc     > php symfony propel-build-all-load frontend
```

提示:如果此时命令行抛出致命错误“调用未定义的函数`imagecreatefromjpeg()`”，这意味着`gd`扩展是在 web 服务器使用的`php.ini`中激活的，而不是在命令行使用的`php.ini`(通常在`php/`目录中)。在两者中激活它，问题应该会消失。

就是这样；用户安全功能准备就绪。尝试浏览到`http://localhost/sf_sandbox/web/frontend_dev.php/photo`。

![1566_fig10](img/75df6604ca9fe73c9f64791b8ebc5375.png)

您将看到一个要求登录和密码的认证屏幕。sfGuardPlugin 附带了一个测试用户(id: `admin`，密码:`admin`)，因此即使您没有创建任何用户，也可以访问您的模块页面。使用这两个值，您可以再次进入照片模块。

如果您想要添加更多用户，或者更改默认 id 和密码，请通过输入以下 URL 向 sfGuardUser 模块发出请求:`http://localhost/sf_sandbox/web/frontend_dev.php/sfGuardUser`。

在我们关注终端用户界面之前，还有最后一句话。到目前为止，我们浏览的每个 URL 都包含文件名`frontend_dev.php`。这就是所谓的开发环境前端控制器。Symfony 能够为每个应用程序提供多种环境。例如，在开发环境中，每次请求时都会检查配置，在请求处理过程中会记录许多详细信息，这些详细信息可以在 web 调试工具栏(窗口右上角的灰色行)中找到。试着点击它的图标，看看这个工具在调试应用程序时有多有用。

默认情况下，前端应用程序附带了另一个环境:生产环境。这个环境针对速度进行了优化，因此 symfony 跳过了所有的日志记录和调试工作，直接进行页面的呈现。生产环境前端控制器名为`index.php`，因此您可以通过 URL `http://localhost/sf_sandbox/web/index.php/photo.`访问它

如果您尝试在对这一部分进行任何更改之前浏览生产环境，您可能需要通过发出 php symfony cc 命令来清除缓存。如果在您的服务器上启用了`mod_rewrite`，那么利用默认项目中捆绑的重写规则来调用下面的 URL，它相当于前面的一个`http://localhost/sf_sandbox/web/photo`。

尝试在生产环境中浏览生成的应用程序——它会感觉更快、响应更快。环境是一个很好的特性，在你需要的时候给你你所需要的:在你开发的时候给你开发工具，在你的应用程序需要响应真正的请求的时候给你速度。

##### 添加页面

这就是管理界面。现在，我们可以专注于最终用户页面。这一次，页面不是由生成器创建的——我们将手动编码它们，以便您理解如何将页面添加到您的应用程序中。

首先，我们将最终用户页面分组到一个公共模块中。symfony 命令行提供了一个有用的任务来初始化一个空模块的文件和目录:

```
$ php symfony init-module frontend public
```

这将在`apps/frontend/modules/`中创建一个新的`public/`目录，包含以下子目录:

```
**actions/**     config/     lib/     **template/**     validate/
```

文件结构遵循 MVC 模式提出的代码分离。在`actions/`中，你会发现处理请求的代码——控制器代码。`templates/`包含演示专用的代码——视图代码。`config/`目录应该包含配置文件，但是在最初创建模块时，它是空的。您可以将想要在这个模块中使用的类放到`lib/`目录中(将它们放在那里将提供自动加载的好处)。最后，`validate/`目录是用于表单验证文件的，我们不会在本教程中介绍。

我们如何添加一个新的页面？symfony 中的页面由两部分组成:一个动作和一个模板。操作代码在模板代码之前执行。事实上，该操作为模板准备数据。该模板使用很少的 PHP 代码，并保持可维护性，因为它严格用于表示，而不是应用程序逻辑。动作是放在模块 actions.class.php 中的方法，其名称以“`execute`”为前缀模板是与动作同名的文件，但带有后缀“`success`。”让我们通过一个例子来看看它们是如何一起工作的。让我们添加一个页面，显示所有上传图片的缩略图，按日期排序。

打开`apps/frontend/modules/public/actions/actions.class.php`文件。它包含一个空的索引操作(称为`executeIndex()`的方法)。当用户请求 URL `public/index`时，就会调用`executeIndex()`方法(在当前配置下，完整的 URL 应该是`http://localhost/sf_sandbox/web/frontend_dev.php/public/index`)。

此时，此操作显示默认的欢迎页面，因此用以下操作替换它:

```
// in apps/frontend/actions/actions.class.php     public function executeIndex()     {      **$c = new Criteria();      $c->addDescendingOrderByColumn(PhotoPeer::CREATED_AT);      $this->photos = PhotoPeer::doSelect($c);**     }
```

`Criteria`对象可能会让您想起我们之前在模型中看到的`deleteTags()`方法。事实上，它是 model 用来构建数据库查询的同一对象。这三行代码与下面的 SQL 代码同义:

```
SELECT * FROM photo ORDER BY created_at;
```

这里，`ORDER BY`子句决定了我们传递给`addDescendingByColumn`方法的内容。被赋予函数`doSelect`结果的类的名字来自于`FROM`子句。你可能认为用别的东西代替 SQL 是浪费时间，因为它迫使你学习一种新的语法。但是这个假设是不正确的，原因有二。

首先，不编写实际的 SQL 代码可以保护您的应用程序免受 SQL 注入攻击，并保持您的代码独立于数据库。如果您选择将应用程序从 SQLite 迁移到 Oracle 数据库，您所需要的只是配置文件中的一个参数，尽管两个数据库之间存在语法差异。其次，`doSelect()`调用不仅仅是向数据库发送一个查询——它实际上是基于结果集创建照片对象。没错，`$this->photo`的内容是一个`Photo`类的对象数组。您可以在这些对象上使用我们之前定义的所有方法，以及生成的方法。这就是 ORM 的最大好处。

`executeIndex`不仅仅是查询数据库和基于结果集构建对象。它实际上将`$photo`数组传递给模板。这就是`$this->`召集行动的目的。让我们看看如何在模板中检索这些数据。

当动作完成时，symfony 会为这个动作寻找一个模板。模板的名称应包含以操作终止状态为后缀的操作名称。默认的动作终止是一个“`Success`”，所以 symfony 在模块`templates/`目录中寻找一个`indexSuccess.php`文件。它存在，但为空，所以编辑如下:

```
// in apps/frontend/modules/public/templates/indexSuccess.php     <div id="main">      <h1>My pictures</h1>      <?php foreach($photos as $photo): ?>      <div class="photo">        <?php echo link_to(          image_tag('/uploads/thumbnail/'.$photo->getFilePath()),          'public/photo?id='.$photo->getId(),          'class=image title='.$photo->getDescription()        ) ?>        "<?php echo $photo->getDescription() ?>"          on <?php echo $photo->getCreatedAt('d/m') ?>,          tagged <?php echo $photo->getTagsString() ?>      </div>      <?php endforeach; ?>      <div id="footer">        powered by <?php echo link_to('symfony', 'http://www.symfony-project.com') ?>      </div>     </div>
```

现在，您将看到如何使用操作中准备的数据:模板使用 foreach 遍历`$photos`数组，并在每个`$photo`对象上调用`Photo`类的方法。您已经知道了`image_tag()`助手，所以是时候了解一下`link_to()`助手了，它输出一个到另一个动作的超链接。它需要至少两个参数:链接的承载者(这里是一个`<img>`标签)和链接的目标，用内部 URI 表示。内部 URI 是模块名和动作名(用斜杠分隔)以及一组参数的组合，编写方式与普通 URL 相同。第三个(可选的)参数是一串额外的标签属性，采用旧的 HTML 4.0 风格。但是不用担心 symfony helpers 输出的所有 HTML 代码都是符合 XHTML 的。这实际上是理解助手如何工作的最好方法:通过查看它们的输出。

模板的第一个`link_to()`的输出如下:

```
<a class="image" title="title" href="/frontend_dev.php/public/photo/id/3">      <img src="https://www.sitepoint.com/wp-content/uploads/thumbnail/68eda1eaf5a8eac4d4529fa85298c7de.jpg" />     </a>
```

注意，这个模板使用了另一种 PHP 语法(`<?php foreach(): ?><?php endforeach; ?>`)，而不是传统的语法(`<?php foreach() {} ?>`)。这有助于防止 PHP 代码与 HTML 代码的混合，并保持模板对非开发人员的可读性。如果您最终在模板中编写了花括号或回显 HTML，这通常是一个好迹象，表明您应该重构您的代码，并将一部分代码移到操作中。

整个模板代码的结果是，`public/index`动作显示了一个所有缩略图的列表，每个缩略图都带有一个到`public/photo`动作的链接(这还没有写出来——耐心！).但是在我们看它之前，我们应该给它一些风格。我们为本教程设计了一个简单的级联样式表。本文不再赘述，不过你可以[在这里](https://www.sitepoint.com/examples/symfony/frontend.zip)下载。你应该把它放在项目的`web/css/`目录下以便 symfony 找到它。当然，您还必须告诉 symfony 在响应中包含相关的`<link>`标记。`Response`对象有一个专门的方法:它被称为`addStylesheet`。由于使用了`getResponse`方法，`Actions`对象可以直接访问`Response`对象。然而，由于我们需要在每个页面上使用样式表，而不仅仅是这个特定动作的页面，我们将在动作的`preExecute`方法中调用它。该方法在模块的每个动作之前被调用，对于避免代码重复非常方便。所以把这个方法添加到`actions.class.php`:

```
// in apps/frontend/actions/actions.class.php     public function preExecute()     {      $this->getResponse()->addStylesheet('frontend');       }
```

就是这样；照片列表已经可以看了。向 URL `http://localhost/sf_sandbox/web/frontend_dev.php/public/index`发出请求。

![1566_fig11](img/d3d5126fe2a1dd7838e737b77bc09e20.png)

所以在 symfony 中创建页面就像创建一个动作(用于控制器代码)和一个模板(用于表示代码)一样简单。为了确保您理解这个想法，让我们创建一个新页面—照片详细信息页面。它将通过内部 URI `public/photo`访问，因此它的动作应该被称为`executePhoto()`。使用以下代码将其添加到操作文件中:

```
// in apps/frontend/actions/actions.class.php     public function executePhoto()     {      $photo = PhotoPeer::retrieveByPk($this->getRequestParameter('id'));      $this->forward404unless($photo);      $this->photo = $photo;     }
```

这个动作中有一些新的东西:`getRequestParameter`方法调用。这实际上是通过名称从动作中检索请求参数的方法。`PhotoPeer`模型类的`retrieveByPk`静态方法用于根据主键检索`Photo`对象。但是我们不希望这个动作被错误的参数调用。如果用户用一个不存在的`id`键入 URL，那么他或她将看到一个 404 错误页面，而不是一个错误页面。这就是`forward404unless`呼叫的目的，相当于:

```
if(!$photo)     {      $this->forward404();     }
```

现在，我们如何将`$photo`对象传递给模板？您已经在索引操作中看到了这一点，所以让我们切换到模板。在模块的`templates/`目录中创建一个`photoSuccess.php`模板，并在其中编写:

```
// in apps/frontend/modules/public/templates/photoSuccess.php     <div id="main">      <?php echo link_to('back to the photo list', 'public/index',            'style=display:block;float:right;') ?>      <h1>Picture details</h1>      <a href="/uploads/<?php echo $photo->getFilePath() ?>" title="click for the full-size version">        <?php echo image_tag('/uploads/'.$photo->getFilePath(), 'width=100%') ?>      </a><br/>      <p>          "<?php echo $photo->getDescription() ?>"          published on <?php echo $photo->getCreatedAt('d/m') ?>,            tagged <?php echo $photo->getTagsString() ?>      </p>      <div id="footer">        powered by <?php echo link_to('symfony', 'http://www.symfony-project.com') ?>      </div>     </div>
```

这里没有什么新东西，除了实际图片的链接没有使用`link_to()`帮助器。这是因为，在这种特殊的情况下(链接到一个上传的文件)，在没有助手的情况下编写它会更快。

![1566_fig13](img/4557d6a18e30aff17a03e6f34217ef10.png)

##### 重构

您可能注意到了`photoSuccess.php`和`indexSuccess.php`模板包含相同的代码部分，用于显示照片细节。如果你知道不要重复自己(D.R.Y .)原则，这是敏捷编程的支柱之一，你就会知道这意味着代码必须重构。

我们要做的是将公共代码移动到另一个脚本中，并将该脚本包含在两个模板中。同时，我们将对标签进行一些详细说明，以便用户可以点击每个标签来显示具有该标签的图片列表。因此，在`public/templates/`目录中创建一个文件`called _photo_description.php`，包含以下代码:

```
// in apps/frontend/modules/public/templates/_photo_description.php       "<?php echo $photo->getDescription() ?>"       published on <?php echo $photo->getCreatedAt('d/m') ?>,         tagged       <?php foreach($photo->getTags() as $tag): ?>        <?php $tag=$tag->getName(); echo link_to($tag, 'public/tag?tag='.$tag) ?>       <?php endforeach; ?>
```

这里，我们再次看到了`link_to()`助手(这一次，指向一个带有`tag`参数的`public/tag`动作)，我们使用了`$photo`对象的属性。但是，等等——为了让这个脚本访问 symfony 助手和模板变量，它必须比普通的包含脚本更智能。这就是为什么我们在`indexSuccess.php`和`photoSuccess.php`中不会用一个简单的`include()`来称呼它；相反，我们将使用如下的`include_partial()`:

```
// in apps/frontend/modules/public/templates/photoSuccess.php       <div id="main">        <?php echo link_to('back to the photo list', 'public/index',            'style=display:block;float:right;') ?>        <h1>Picture details</h1>        <a href="/uploads/<?php echo $photo->getFilePath() ?>" title="click for the full-size version">          <?php echo image_tag('/uploads/'.$photo->getFilePath(), 'width=100%') ?>        </a><br/>        <p>          **<?php echo include_partial('photo_description', array(            'photo' => $photo          )) ?>**        </p>        <div id="footer">          powered by <?php echo link_to('symfony', 'http://www.symfony-project.com') ?>        </div>       </div>
```

Symfony 将这种类型的代码片段称为部分代码。部分名称以下划线开头，以便在`templates/`文件夹中可以清楚地区分。需要显式地向分部传递它们可以访问的变量(以保持封装)。`include_partial()`调用的第一个参数是一个部分名称，它是没有前导下划线和尾随. php 的部分文件名。现在您明白了为什么当我们处理生成的管理时，照片模块的`_photo.php`脚本被称为部分列:该列只包含一个部分。

D.R.Y .极端分子可能会说，这两个模板仍然有一些共同的代码——即围绕内容的 main `<div id="main">`和页脚。的确如此，事实上这段代码不应该是模板的一部分，而应该是布局的一部分。在我们编写的模板中，没有提到`<html>`或`<head>`标签，但是它们出现在响应中。这是因为每个模板执行产生的代码都被插入到另一个模板中，称为全局模板或布局。这是模板的容器，或者在设计模式语言中，是“装饰器”。布局是存储全局导航、站点页眉和页脚、侧边栏等的好方法。看一下`apps/frontend/templates/layout.php`中的默认布局。我们将对其稍加修改，在内容周围添加`<div id="main">`，并添加页脚`div`:

```
// in apps/frontend/templates/layout.php       <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">       <html  xml:lang="en" lang="en">       <head>        <?php echo include_http_metas() ?>        <?php echo include_metas() ?>        <?php echo include_title() ?>        <link rel="shortcut icon" href="/favicon.ico" />       </head>       <body>       **<div id="main">**        <?php echo $sf_data->getRaw('sf_content') ?>        **<div id="footer">          powered by <?php echo link_to('symfony', 'http://www.symfony-project.com') ?>        </div>       </div>**       </body>       </html>
```

模板执行的结果被插入到`$sf_data->getRaw()`行；或者，我们也可以说布局“包装”了模板代码。现在你可以从两个模板中移除`<div id="main"></div>`和`<div id="footer"></div>`。当我们修改模板时，我们可以考虑一下`public/tag`页面，它应该显示具有给定标签的图片列表。图片列表？但这正是`public/index`行动所展示的！因此，我们不会创建新的模板，而是将现有的`indexSuccess.php`重新用于标记操作，如下所示:

```
// in apps/frontend/modules/public/actions/actions.class.php       public function executeTag()       {        $this->forward404Unless($tag = $this->getRequestParameter('tag'));        $c = new Criteria();        $c->addJoin(PhotoPeer::ID, TagPeer::PHOTO_ID);        $c->add(TagPeer::NAME, $tag);        $this->photos = PhotoPeer::doSelect($c);                $this->setTemplate('Index');       }
```

这段代码的第一行是请求参数验证和变量初始化的压缩版本。这应该会让你想起`executePhoto()`方法。然后，我们构建一个新的`Criteria`，以便检索链接到与请求参数同名的`Tag`对象的所有`Photo`对象。这相当于以下 SQL 查询:

```
SELECT * FROM photo, tag WHERE tag.NAME='$tag' AND photo.ID=tag.PHOTO_ID;
```

语法看起来可能还是有点奇怪，但是多尝试几次之后，你会发现它非常自然。无论如何，这个动作中重要的部分是最后一个语句:`setTemplate()`方法告诉 symfony 使用`indexSuccess.php`模板，而不是这个动作的默认模板(`tagSuccess.php`)。我们现在必须修改`indexAction.php`来处理标签动作。我们还将删除对布局进行重构的代码:

```
// in apps/frontend/modules/public/templates/indexSuccess.php       **<?php if($tag = $sf_params->get('tag')): ?>        <?php echo link_to('back to the photo list', 'public/index', 'style=display:block;float:right;') ?>       <?php endif; ?>**       <h1>        My pictures        **<?php if($tag): ?>        tagged "<?php echo $tag ?>"        <?php endif; ?>**       </h1>       <?php foreach($photos as $photo): ?>       <div class="photo">        <?php echo link_to(          image_tag('/uploads/thumbnail/'.$photo->getFilePath()),          'public/photo?id='.$photo->getId(),          'class=image title='.$photo->getDescription()        ) ?>        <?php echo include_partial('photo_description', array(          'photo' => $photo        )) ?>       </div>       <?php endforeach; ?>
```

在 action 类中，我们必须使用`getRequestParameter()`方法来检索请求参数。在模板中，对应的是`$sf_params->get()`。所以`public/tag`动作现在工作正常了——它甚至显示了一个指向索引动作的链接。

![1566_fig15](img/52c2ba4a053740a5bdaa5e9faa5505ee.png)

不要忘记删除被重构到`public/photo`模板布局中的代码。敏捷开发意味着大量的代码重构，symfony 为您提供了许多工具(包括布局和部分)来正确地完成它。一般来说，用 symfony 开发一个应用程序可以保证你的代码可以很容易地修改，即使你想添加一个不是一开始就设计好的特性。

##### 埃阿斯

如今，几乎每一个新推出的网络应用都有酷炫的视觉效果和 XMLHttpRequests。如果互联网看起来越来越像桌面应用，这并不是因为这在以前是不可能的。因为这个任务非常复杂。你必须写几千行 JavaScript 代码，为 IE 复制它，然后花几周时间在浏览器中调试你的代码。今天，一旦有了一个框架来处理 Ajax 效果，构建 Ajax 效果就变得非常简单。使用 symfony，甚至不需要编写 JavaScript 代码，您就可以创建一个兼容所有主流浏览器的 Ajax 表单，并且只需要一个函数调用。

我们将在处理相册的评论时说明这种技术。我们在本教程前面创建的模式已经有一个用于存储注释的注释表，但是我们需要创建一个界面来允许用户在查看图片时添加注释。我们将在`public/photo`页面中使用 Ajax 表单，而不是传统的方式。再次打开`photoSuccess.php`模板，并在其末尾添加以下代码:

```
// in apps/frontend/modules/public/templates/photoSuccess.php       
 ...       
 <?php use_helper('Javascript'); ?>       
 <div id="comments">       
  <h2>Comments</h2>       
  <?php foreach($photo->getComments() as $comment): ?>       
    <?php include_partial('comment', array('comment' => $comment)) ?>       
  <?php endforeach; ?>       
  <div id="updateDiv">       
    <?php echo link_to_function('Add a comment', visual_effect('toggle_blind', 'addComment')) ?>       
    <?php echo form_remote_tag(array(       
      'url'    => 'public/addComment',       
      'update' => 'updateDiv',       
      'complete' => visual_effect('highlight', 'updateDiv'),       
    ), 'id=addComment style=display:none;') ?>       
      <?php echo input_hidden_tag('photo_id', $photo->getId()) ?>       
      <?php echo label_for('author', 'Your name') ?>       
      <?php echo input_tag('author') ?><br />       
      <?php echo label_for('body', 'Your comment') ?>       
      <?php echo textarea_tag('body') ?><br />       
      <?php echo submit_tag('submit') ?>       
    </form>       
  </div>       
ho
```

## 分享这篇文章