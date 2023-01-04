# Magento 安装和升级数据脚本说明

> 原文：<https://www.sitepoint.com/magento-install-upgrade-data-scripts-explained/>

在 Magento 中开发定制模块时，我经常要处理安装和升级脚本的问题。通常，出现这些问题是因为命名约定中的错误、不正确的版本号或不正确的语法。在这篇文章中，我们将特别关注如何编写可靠的 Magento 安装和升级脚本。

![magento-ecommerce-square-logo](img/ca0a806e08de01f57de5e8bb7858a26c.png)

Magento 安装和升级脚本是模块开发的一部分，因此建议您在阅读本文之前对模块开发有一些基本的了解。

每当您安装或创建任何包含数据库交互的新模块时，您都会在该模块的代码目录中找到一个安装和升级脚本，一旦您点击该 URL，该脚本就会运行。Magento 的核心模块也遵循相同的安装和升级结构。如果你想看一些例子，打开`app/code/core/Mage/Catalog/sql/ catalog_setup`。在这里，您会发现几个具有适当命名约定的安装和升级脚本及其版本号。

## 安装脚本

要运行安装脚本，我们需要创建一个定制模块。我们不打算创建一个全新的模块，因为这超出了本文的范围；相反，我们假设我们已经创建了一个定制模块，它的包名为**站点点**，模块名为**文章**，它保存了数据库中所有已写文章的记录。

作为快速指南，让我们在这里定义基本模块组件:

*   **Blocks:** 前端模板的类实例。前端模板直接使用类函数。
*   **模型:**包含与典型 MVC 模式相同的业务逻辑。
*   **资源模型:**从数据库表中推和拉数据。
*   **控制器:**当一个 URL 被点击时加载布局块。
*   一个 XML 文件包含模块相关的配置。
*   **助手:**顾名思义，包含函数的类可以作为助手跨所有模块访问，而不考虑模块范围。
*   **sql:** 包含数据库升级和安装脚本的文件，用于创建和更新数据库模式。

模块的目录结构应该如下所示。

```
app
 --code
 ---local
 ----Sitepoint
 ------Articles
 --------Block
  --------controllers
   --------etc
    --------Model
     --------sql
```

转到`app/code/local/Sitepoint/Articles/etc`并打开一个文件`config.xml`。我们需要在这个文件中添加一些与安装脚本位置相关的配置。在`config.xml`中，在`global`标签下添加如下子标签:

```
<global>
    <models>
        <articles>
            <class>Sitepoint_Articles_Model</class> <!-- Model class files -->     
            <resourceModel>articles_mysql4</resourceModel> <!--Resource model -->
        </articles>
        <articles_mysql4>
            <class>Sitepoint_Articles_Model_Mysql4</class>
            <entities>
                <articles>
                    <table>articles</table>  <!-- Db table name  -->
                </articles>
            </entities>
        </articles_mysql4>
    </models>
    <resources>  
        <articles_setup>
            <setup>
                <module>Sitepoint_Articles</module>
            </setup>
            <connection>
                <use>core_setup</use>
            </connection>
        </articles_setup>
        <articles_write>
            <connection>
                <use>core_write</use>
            </connection>
        </articles_write>
        <articles_read>
            <connection>
                <use>core_read</use>
            </connection>
        </articles_read>
    </resources>
</global>
```

有一个非常重要的标签`articles_setup`，它位于`resources`标签下，告诉 Magento 我们的数据库设置文件位于`articles_setup`目录下。

转到`Articles/sql/articles_setup`并创建安装脚本`mysql4-install-0.1.0.php`

```
<?php
$installer = $this;
$installer->startSetup();
$installer->run("-- DROP TABLE IF EXISTS {$this->getTable('articles')};
CREATE TABLE {$this->getTable('articles')} (
	  `articles_id` int(11) unsigned NOT NULL auto_increment,
	  `title` varchar(255) NOT NULL default '',
	  `short_desc` text NOT NULL default '',
	  `long_desc` text NOT NULL default '',
	  `status` tinyint(2) NOT NULL default '0',
	  `created_time` datetime NULL,
	  `update_time` datetime NULL,
	  PRIMARY KEY (`articles_id`)
	) ENGINE=InnoDB DEFAULT CHARSET=utf8;
	");
	$installer->endSetup();
```

为了使我们的脚本跨数据库兼容，下面是使用 DDL 对象的另一种方法:

```
<?php
$installer = $this;
$installer->startSetup();
$table = $installer->getConnection()->newTable($installer->getTable('articles'))
    ->addColumn('articles_id', Varien_Db_Ddl_Table::TYPE_INTEGER, 11, array(
        'unsigned' => true,
        'nullable' => false,
        'primary' => true,
        'identity' => true,
        ), 'Article ID')
    ->addColumn('title', Varien_Db_Ddl_Table::TYPE_VARCHAR, 255, array(
        'nullable' => false,
        'default' => '', 
        ), 'Title')
    ->addColumn('short_desc', Varien_Db_Ddl_Table::TYPE_TEXT, null, array(
        'nullable' => false,
        'default' => '',
        ), 'Short Desc')
    ->addColumn('long_desc', Varien_Db_Ddl_Table::TYPE_TEXT, null, array(
        'nullable' => false,
        'default' => '',
        ), 'Long Desc')
    ->addColumn('status', Varien_Db_Ddl_Table::TYPE_TINYINT, 2, array(
        'nullable' => false,
        'default' => '0',
        ), 'Status')
    ->addColumn('created_time', Varien_Db_Ddl_Table::TYPE_DATE, null, array(
        'nullable' => true,
        'default' => null,
        ), 'Created Date')
    ->addColumn('update_time', Varien_Db_Ddl_Table::TYPE_DATE, null, array(
        'nullable' => true,
        'default' => null,
        ), 'Update Date')    
    ->setComment('Articles table');
	$installer->getConnection()->createTable($table);
	$installer->endSetup();
```

为了获得表名，我们使用`$this->getTable('articles')`,因为它会自动将表前缀(在 Magento 安装过程中配置)附加到表名上。

我们的安装脚本名称后面有一个版本号。该编号与`config.xml`文件中定义的相同。

```
<modules>
    <Sitepoint_Articles>
        <version>0.1.0</version>    <!-- Version of module -->
    </Sitepoint_Articles>
	</modules>
```

版本号告诉 Magento 最新的安装脚本。设置好安装脚本后，刷新你的 Magento URL 并查看数据库。如果您的安装脚本运行成功，您的表应该被创建。

现在转到您的 SQL 编辑器，打开 Magento 数据库中的`core_resource`表。在这里，您可以看到名为`articles_setup`且版本号为 0.1.0 的安装脚本条目。

每次你刷新一个 URL，Magento 都会根据你的配置文件和数据库`core_resource`表中的版本号来检查是否有安装脚本要运行。如果版本不匹配，它将寻找合适的版本文件来运行。假设您在您的`config.xml`中将版本更改为 0.1.1。Magento 会发现你的`core_resource`表包含版本号 0.1.0，并且当你提到一个新版本时，它会寻找版本号为 0.1.1 的脚本。

如果它在`articles_setup`目录中找到这个文件，它将运行它，并将`core_resource`表中的版本号升级到 0.1.1。这就是安装和升级脚本的工作方式。所有 Magento 核心模块都遵循这个版本系统。事实上，整个 Magento 升级过程都遵循同样的程序。

请注意，安装脚本只运行一次——当您创建它并刷新您的 URL 时。如果您想改变数据库模式，可以使用升级脚本来完成。一种快捷方式是从`core_resource`表中删除您的设置条目并刷新您的 URL。脚本将再次运行，您的新更改将反映在数据库表中。

## 升级脚本

当您希望根据新的数据库字段更新模块以提供新的功能时，您需要改变模式、更改字段数据类型、引入新的列等等。

这就是升级脚本派上用场的时候了。您不能通过运行行查询通过 SQL 编辑器进行任何直接更改，因为无论何时其他用户安装您的模块，新的数据库更改都不会在他们的设置中生效。Magento 建议使用升级脚本来改变数据库模式。

升级脚本类似于安装脚本，唯一的变化是名称和不同的版本号。让我们看一个例子。

假设我们想再添加一列，并更改另一列。为此，我们将在`Articles/sql/articles_setup`下创建一个名为`mysql4-upgrade-0.1.0-0.1.1.php`的升级脚本。添加以下代码。

```
<?php
$installer = $this;
$installer->startSetup();
$installer->run("
	ALTER TABLE {$this->getTable('articles')}
	CHANGE COLUMN `long_desc` `long_desc` text NULL,
	ADD COLUMN `sub_title` VARCHAR(45) NOT NULL AFTER `title`;
	");
$installer->endSetup();
```

下面是使用 DDL 实现这一点的另一种方法:

```
<?php 
$installer = $this;
$installer->startSetup();
$installer->getConnection()
    ->changeColumn($installer->getTable('articles'), 'long_desc', 'long_desc', array(
        'type' => Varien_Db_Ddl_Table::TYPE_TEXT,
        'nullable' => true,

    ))
    ->addColumn($installer->getTable('articles'), 'sub_title', array(
        'type' => Varien_Db_Ddl_Table::TYPE_VARCHAR,
        'nullable' => false,
        'comment' => 'Sub title'
    ));
$installer->endSetup();
```

现在我们需要告诉 Magento 我们已经准备好了一个新的升级脚本。为此，我们需要更改我们的`config.xml`文件中的版本号:

```
<modules>
	<Sitepoint_Articles>
	    <version>0.1.1</version>    <!-- Upgrade Version of module -->
	</Sitepoint_Articles>
</modules>
```

一旦你刷新了你的 URL，Magento 会发现你的`config.xml`文件包含了比存储在`core_resource`表中的版本更高的版本。它将寻找相同版本的升级脚本，然后运行它。刷新 URL 后，您应该能够看到数据库模式的更新。

## 结论

这就是 Magento 安装和升级脚本的工作方式。你可以通过创建一个新模块并运行脚本来尝试一下——我们在这里写的所有东西都可以在 Github 上找到[。如果你在开发安装或升级脚本时有任何问题，请在评论区提出来，我们会讨论的！感谢反馈！](https://github.com/sitepoint-examples/demo.sitepoint)

## 分享这篇文章