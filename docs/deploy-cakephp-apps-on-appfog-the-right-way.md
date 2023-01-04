# 以正确的方式在 AppFog 上部署 CakePHP 应用程序

> 原文：<https://www.sitepoint.com/deploy-cakephp-apps-on-appfog-the-right-way/>

这篇文章分享自我们的姐妹网站之一， [CloudSpring](https://www.sitepoint.com/ "CloudSpring - Learn how to manage your website in the Cloud") 。如果你觉得有帮助，一定要给他们一个访问！

本文涵盖了两个主题:将应用程序部署到 AppFog 云提供商，以及结构良好的 PHP 应用程序的最佳实践。

首先，我们将使用 CakePHP 及其自动化烘焙命令构建一个简单的笔记应用程序。然后，我将向您展示一种构建应用程序的方法，将它的组件分为:

*   应用代码
*   应用程序数据和设置
*   库和供应商代码

该应用程序将最终注册并发布到 AppFog。

## 注册 AppFog

AppFog 是一个平台即服务(PaaS)提供商，构建在开源的 [Cloud Foundry](http://cloudfoundry.org/ "Cloud Foundry Open Source | Cloud Foundry") 项目之上。它支持多种编程语言，并允许我们将应用部署到多个基础设施提供商，如亚马逊 AWS、惠普 Openstack、微软 Azure 和 Rackspace DFW。

注册 AppFog 非常简单；所有你需要的是一个电子邮件地址和一个密码，你可以使用他们的免费计划，提供 2GB 的内存，10 项服务(例如。数据库)，每个数据库的最大容量为 100MB，数据传输限制为 50GB。免费服务这么多！此外，正如他们所说，你可以将你的应用程序转移到任何兼容 Cloud Foundry 的服务上。

继续操作并[创建您的免费 AppFog 帐户](https://console.appfog.com/signup "Signup For AppFog")，然后我们将继续。

## 创建本地应用程序

下载[最新版本的 CakePHP](https://github.com/cakephp/cakephp "cakephp/cakephp") 并在你的开发机器上解压，然后重命名为你的应用(我选 FogNotes)。通过友好的 URL(例如 http://fognotes.local)，文档根目录应该是`app/webroot`目录，并创建一个空的 MySQL 数据库(例如。`fognotes_local`)。

在您的浏览器中，您应该会看到 CakePHP 提供的默认主页。不要担心警告，我们很快就会处理它们。

现在创建一个名为`data`的目录和其中两个名为`config`和`logs`的子目录。复制(或移动)在`data`中的`app/tmp`目录，并使这个目录和`logs`目录对 web 服务器可写。最后，在应用程序的根目录下创建 CakePHP 控制台命令`cake`的别名。

```
~/FogNotes$ ln -s lib/Cake/Console/cake cake
```

您的目录结构应该如下所示:

```
FogNotes/
    app/
    data/
        config/
        logs/
        tmp/
            cache/
            logs/
            sessions/
            tests/
    lib/
        Cake/
    plugins/
    vendors/
    cake
    index.php
```

通过这样做，我们将特定的应用程序代码(`app`)从框架(`lib/Cake`)和库(`plugins`和`vendors`)中分离出来。此外，应用程序的数据和设置都在一个单独的地方(`data/*`)。现在，我们可以单独更新、备份和维护每个组件。这是现实应用程序中非常需要的！

有了目录结构，让我们告诉 CakePHP 使用它。我们必须设置自定义的临时目录，所以编辑`app/webroot/index.php`并将这段代码粘贴在`define('ROOT',...)`语句之后:

```
<?php
// Custom TMP directory
if (!defined('TMP')) {
    define('TMP', ROOT . DS . 'data' . DS . 'tmp' . DS);
}
```

现在谈谈数据库。通常，数据库连接设置存储在名为`database.php`的文件中的`app/Config`目录中。我们将修改这个设置，以便让框架在`data/config`目录中查找一个以我们当前环境命名的文件(例如`data/config/local.php`)。

复制文件`app/Config/database.php`。默认为`app/Config/database.php`，并用以下代码替换`DATABASE_CONFIG`类:

```
<?php
class DATABASE_CONFIG
{
    public $default = null;
    public $test    = null;
    public $env     = null;

    /**
     * Switch configuration based on environment
     */
    function __construct() {
        // Check that a suitable environment is defined
        if (!defined('APP_ENV')) {
            return false;
        }

        $this->env = APP_ENV;

        // Try to read database settings from the current ENV file
        $config = Configure::read('Database.config');
        if (!is_array($config)) {
            return false;
        }

        // Load all config database profiles
        foreach ($config as $name => $data) {
            $this->$name = $data;
        }

        // Throw an error if there is no suitable configuration
        if (empty($config['default']) || empty($this->default)) {
            return false;
        }
    }
}
```

这个技巧改编自 eimermusic 发表在 CakePHP 开发者社区的一篇[文章。将使用引导文件或读取 AppFog 提供的环境变量来定义环境，并将加载相关的配置文件。](http://bakery.cakephp.org/articles/eimermusic/2009/02/18/one-core-one-app-multiple-domains "One core, one app, multiple domains :: The Bakery :: Everything CakePHP")

`app/Config/bootstrap.php`文件是应用程序的启动文件。在文件底部附近，在`Configure::write('Dispatcher.filters'...)`语句之后，插入以下代码，该代码向 CakePHP 注册了`data/config`路径:

```
<?php
// Define a custom reader to search for config files
App::uses('PhpReader', 'Configure');
Configure::config('default',
    new PhpReader(ROOT . DS . 'data' . DS . 'config' . DS));
// Determine the environment
if ($env = getenv('APP_ENV')) {
    if (!defined('APP_ENV')) define('APP_ENV', $env);
}
else {
    if (!defined('APP_ENV')) define('APP_ENV', 'local');
}
```

AppFog 会让我们设置`APP_ENV`变量。如果没有设置，则应用程序认为自己处于本地模式。

现在跳到文件的末尾，粘贴以下代码:

```
<?php
try {
    Configure::load(APP_ENV);
}
catch (ConfigureException $e) {
    // do something, for example exit application
}
```

`Configure::load()`语句告诉 CakePHP 在`data/config`目录中搜索一个像当前环境(exs。`local.php`或`prod.php`。在这个文件中，我们可以覆盖应用程序先前定义的所有设置。

一个示例`local.php`配置文件可以是:

```
<?php
// Load base configuration. Any of the core settings can be
// overridden here. Don't delete the $config var!
$config = array(
    'debug' => 1
);

// If declared in the $config array, String::UUID() crashes
Configure::write('Security.salt', 'SomeSuperSecretLongString');
Configure::write('Security.cipherSeed', 'SomeSuperSecretLongNumber');

// Database settings
Configure::write('Database.config', array(
    'default' => array(
        'datasource' => 'Database/Mysql',
        'persistent' => false,
        'host' => 'localhost',
        'login' => 'root',
        'password' => 'secret_password',
        'database' => 'fognotes_local',
        'prefix' => '',
        'encoding' => 'utf8',
        'unix_socket' => '/tmp/mysql.sock',
    )
));

// Custom Log file settings to ROOT/data/logs/
CakeLog::config('default', array(
    'engine' => 'FileLog',
    'path'   => ROOT . DS . 'data' . DS . 'logs' . DS
));
```

所有定制就绪后，默认主页中的警告消息应该会消失。

## 在 AppFog 中创建应用程序

登录 AppFog 控制台并创建一个新的应用程序。一定要选择 PHP 作为应用程序类型，您最喜欢的基础设施提供商(我选择了 AWS Europe)，并给它起个名字。然后点击“创建应用程序”，几秒钟后，系统会将你发送到应用程序的“任务控制”。

带有 AppFog 的应用程序最初由一个带有“Hello World”代码的`index.php`文件组成；我们必须上传本地代码。但是在此之前，我们需要绑定一个数据库服务。选择“服务”面板，点击 MySQL 按钮，并给它一个名称(例如。`fognotes_prod`)。连接到服务的凭证存储在环境变量`VCAP_SERVICES`中。

我们还需要去“环境变量”面板，并设置我们的自定义变量。创建一个名为`APP_ENV`的新变量，值为“prod ”,然后保存。

制作一个`data/config/local.php`的副本，命名为`data/config/prod.php`，用你的编辑器打开。我们将按照 AppFog 文档提供的指南提取数据库设置。编辑配置文件，使其如下所示:

```
<?php
// ...other code

// AppFog Extraction
$services_json = json_decode(getenv('VCAP_SERVICES'),true);
$af_mysql_config = $services_json['mysql-5.1'][0]['credentials'];

// Database settings
Configure::write('Database.config', array(
    'default' => array(
        'datasource' => 'Database/Mysql',
        'persistent' => false,
        'host' => $af_mysql_config['hostname'],
        'login' => $af_mysql_config['username'],
        'password' => $af_mysql_config['password'],
        'database' => $af_mysql_config['name'],
        'prefix' => '',
        'encoding' => 'utf8',
    )
));

// ...other code
```

我们还可以将应用程序绑定到一个自定义的域或子域(免费！)但这里不是这样。

现在我们几乎准备好推出我们的代码了！在“更新源代码”面板中，找到在您的系统上安装`af`实用程序的说明。打开一个终端会话并导航到您的应用程序根目录。按照说明安装 CLI 工具，然后登录，但不要下载远程项目代码。相反，使用命令`af update FogNotes`将本地代码推送到远程服务器。不到一分钟，您的应用程序就部署好了，您应该会看到与本地版本相同的主屏幕。这意味着应用程序可以识别生产环境和远程数据库。

## 添加一些收尾工作

我们部署了一个空的应用程序，但我们必须为我们的应用程序添加至少一些代码和数据。我们会让 CakePHP 来做艰苦的工作。

创建包含以下内容的文件`data/config/notes.sql`:

```
CREATE TABLE notes (
    id INT NOT NULL AUTO_INCREMENT PRIMARY KEY ,
    title VARCHAR( 255 ) NOT NULL ,
    content TEXT NOT NULL ,
    created DATETIME NULL DEFAULT NULL ,
    modified DATETIME NULL DEFAULT NULL ,

    INDEX (title, created, modified)
)
ENGINE = INNODB;
```

通过命令行或您喜欢的 MySQL 工具将文件导入到您的本地数据库中。

接下来，从应用程序的根目录运行命令:

```
~/FogNotes$ ./cake bake
```

按照说明做(真的很简单！)并为数据库中唯一的表“Notes”创建模型、视图和控制器。CakePHP 将为您创建合适的文件。然后，打开文件`app/Config/routes.php`，用以下代码替换第一个`Router::connect()`语句，将主页重新链接到笔记列表:

```
<?php
Router::connect('/', 
  array('controller' => 'notes', 'action' => 'index'));
```

重新加载本地主页，您应该有一个功能完整的应用程序。现在是发布变更的时候了。命令`af update FogNotes`将推送新代码。

如果你现在重新加载远程应用程序，你会看到一个讨厌的错误页面。这是因为我们还没有在远程数据库上创建数据表。哇哦！

导入表最简单的方法是通过[用`af tunnel`命令创建一个隧道](http://docs.appfog.com/services/mysql "AppFog - Documentation MySQL")，然后使用隧道本身提供的信息连接到远程数据库。在控制台窗口中打开隧道，选择“none”作为客户端类型，然后可以使用另一个终端窗口导入 SQL 文件:

```
mysql --protocol=TCP --host=localhost --port=10000 --user=RemoteUserName 
 --password=RemotePassword RemoteLongDifficultDBName < /path/to/notes.sql
```

你也可以使用 [Sequel Pro](http://www.sequelpro.com/ "SequelPro") 如果你在 Mac 或 [MySQL Workbench](http://www.mysql.com/products/workbench/ "MySQL :: MySQL Workbench 5.2") 上，只要隧道是打开的。

一旦表就位，远程应用程序将再次平稳运行。

## 摘要

我在这里向您展示的示例应用程序的组织只是您可以用来改进 CakePHP 开发过程的许多技术之一。例如，您可以将会话存储到本地机器中的一个文件中，并在 AppFog 生产实例中使用 MongoDB。

AppFog 是一个相对年轻的 PaaS 服务，但前景看好。它又快又好用，而且一直在进化。AppFog 还允许您克隆整个应用程序，这有助于创建一个临时或错误修复环境。剩下的就看你的了，所以编码快乐！

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章