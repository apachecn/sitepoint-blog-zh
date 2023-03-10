# Magento 基础知识、请求流程、标准和最佳实践

> 原文：<https://www.sitepoint.com/magento-basics-request-flow-standards-best-practices/>

如今，公司越来越多地将业务转移到网上。他们选择将注意力转向在线沟通，以便与潜在买家保持更近的距离，利用社交网站向人们介绍他们的产品/服务，并最终找到一种变通方法，为人们提供直接从家里、办公室或任何有互联网连接的地方进行购买的过程。

Magento 是获得许多公司信任的电子商务解决方案之一。它支持小型企业，但也可以扩展到更大的规模。

随着电子商务的发展，出现了对专家维护和定制特定商店基础设施功能的需求。

在本文中，我们将讨论开发人员需要完成的初始步骤，以便熟悉 Magento 的结构，并准备开始添加定制功能。

## Magento 的基础知识

Magento 社区版的免费版本可以从 Magento 官方网站 [here](http://www.magentocommerce.com/) 下载。

假设我们已经在本地环境中配置了一个虚拟主机，并将 Magento 解压缩到所需的文件夹中，那么在启动安装程序之前，需要正确设置文件权限。以下是步骤:

*   将应用程序中的所有目录和子目录设置为 775

*   将所有文件设置为 644

*   将目录 app/etc/及其所有文件和子目录设置为 777

*   将目录 var/及其所有文件和子目录设置为 777

*   将目录媒体及其所有文件和子目录设置为 777

在 linux 系统中，您可以通过在 Magento 文件夹中键入以下命令来实现这一切:

```
find . -type d -exec chmod 775 {} \;
find . -type f – exec chmod 644 {} \;
chmod 777 -R app/etc/
chmod 777 -R var/
chmod 777 -R media/` 
```

在安装程序完成工作后，出于安全原因，应该把来自`app/etc`的文件权限改回来——目录改为 775，文件改为 644。

### 代码池

模块位于现有代码池的`app/code/`中:核心、社区(不推荐)和本地。

每个模块在`app/etc/modules/`的`.xml`文件中都有相应的配置，并且定义了类似`<codePool>local</codePool>`的语法，从这里`Mage_Core_Model_Config` → `getModuleDir()`将指向模块的路径。

### 模块结构

#### 街区

该文件夹中的文件继承或加载数据，并将其从您的主题(。phtml 文件)。

#### 控制器

控制器代表业务逻辑，包含与给定请求相匹配的特定操作(dispatch()、preDispatch()、postDispatch()方法)并将命令委派给系统的其他部分。

#### 助手

在 Helper 目录中，您可以使用整个系统常用的实用方法、解析方法和其他通常对您的存储有用的方法来创建类文件。在帮助器中声明的方法可以从任何模板文件或块、模型或控制器中调用。Magento 正在返回助手类的单例实例。

#### 控制器

在该目录中，可以定义路由，用于基于请求匹配模块-控制器-动作、控制器类的模板层等。例如，参见“Paypal”模块。

#### 模型

通常包含对应于数据库表的类。模型可以用作使用两个资源类(一个用于读取，一个用于写入)与数据库通信的常规模型，用于为数据库通信准备查询数据的资源模型，用作定义流程流的独立类的服务模型，用作具有计算比常规助手更复杂算法的单独方法的独立类的助手模型，等等。

#### 等等

包含模块行为配置。每个模块必须至少有一个`config.xml`文件，并声明模型、模块、助手、路由器等的路径。

#### 结构化查询语言

每个模块都可以与 sql 安装程序一起准备数据库实体。安装程序在每个`module/etc/config.xml`中定义了一个组名，每个 sql 安装程序都有一个版本用于订单迭代。

#### 数据

与 sql 安装程序几乎相同的功能语句，但范围不同:不会创建/更新表方案和属性，而是会更新现有记录或用默认记录填充数据库表。

#### 文件

专用于文档和模块解释的文件夹。

### 模板和布局

Magento 中的布局保存在`app/design/`中，并且有一个定义良好的默认和自定义主题结构。加载优先级内的层次结构和文件存在从自定义主题开始。如果没有找到，它将在默认主题中搜索`.phtml`文件，如果仍然没有找到，最后的搜索路径将是基本主题。

Magento 在诸如 adminhtml(系统管理模板)、frontend(终端用户的可见模板)、installer(将自动配置商店的助手系统的模板)等高级领域建立主题结构。

每个主题都有一个“布局”文件夹。xml 文件(通常每个模块都有自己的引用布局配置文件),定义控制器动作的内容块。

自定义主题还可能包含一个名为“locales”的文件夹，该文件夹将保存一个文件“translate.csv ”,其中保存了所有自定义翻译，甚至覆盖了标准 Magento 中已定义的现有翻译。

### 皮肤和 Javascript

CSS 文件、图像和自定义 JS 位于目录`skin/`中，实现了与模板和布局相同的主题化结构和加载优先级(`skin/area_name/package_name`——可以定义一个自定义名称空间或者`base`或`default/theme_name`)。

### 类命名约定

Magento 应用了基本的、虽然已经过时的 Zend 类命名约定，并通过用目录分隔符替换名称中的' _ '来找到文件的包含路径，从而使用`Varien_Autoload::register()`来自动加载类。这是非常原始的，但是由于向后兼容性不能很快解决——Magento 2 将使用 ZF2，现代 PHP 和名称空间。

## 请求流

让我们看看 Magento 应用程序如何处理从主`index.php`文件开始的请求。

### 应用程序初始化

1)如果你使用 Nginx，你应该遵循[配置指南](https://www.magentocommerce.com/wiki/1_-_installation_and_configuration/configuring_nginx_for_magento)。如果您在 Apache 上，这些是主应用程序目录中`.htaccess`的重写规则，不应该丢失。

重写皮肤、媒体、JS 目录的规则，以允许加载文件，如果没有找到，返回 404。其他所有内容都被重写到 index.php:

```
<IfModule mod_rewrite.c>

############################################
## enable rewrites

    Options +FollowSymLinks
    RewriteEngine on

############################################
## always send 404 on missing files in these folders

    RewriteCond %{REQUEST_URI} !^/(media|skin|js)/

############################################
## never rewrite for existing files, directories and links

    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-l

############################################
## rewrite everything else to index.php

    RewriteRule .* index.php [L]

</IfModule> 
```

2)应用程序初始化时遵循以下步骤:

*   运行包含`app/Mage.php`的 index.php 文件

*   呼叫`Mage::run($mageRunCode, $mageRunType)`

*   在“运行”中，我们加载 Varien 事件集合、类`Mage_Core_Model_Config`和类`Mage_Core_Model_App`

*   `Mage_Core_Model_App::run()`被触发

*   从`app/etc/modules`加载配置文件

*   在模块`app/code/{code pool}/{module name}/etc/config.xml`中加载配置 xml 文件

*   初始化当前存储

*   初始化请求对象

*   运行 sql 和数据安装程序

*   在 core_resource 中更新/创建模块版本

*   前端控制器调度-`Mage_Core_Controller_Varien_Front::dispatch();`

*   按区域和路由代码从配置中收集路由器

*   使用路由来匹配控制器

*   如果控制器和动作匹配，控制器文件将被包含、实例化，动作方法将被调用

*   动作方法将根据请求实例化和调用模型上的方法

*   Varien 动作控制器将调用一个布局对象

*   该布局对象将根据一些请求变量和系统属性(也称为“句柄”)创建一个对该请求有效的块对象列表

*   模板文件(。phtml)在布局 xml 文件中被分配给每个块。

*   在基于动作布局 xml 结构的情况下，调用方法，在关联的内实例化特定块。phtml 文件

*   `_toHtml()`方法被触发生成 html

*   输出 html

### 前端控制器

1)前端控制器的属性和作用:

*   目录路径:`app/code/core/Mage/Core/Controller/Varien/Front.php`

*   基本角色:接收来自浏览器的所有请求并返回 HTML 代码

*   前端控制器使用系统的路由来匹配控制器类及其动作方法

*   默认 Magento 路由器有:标准(`Mage_Core_Controller_Varien_Router_Standard`级)、管理(`Mage_Core_Controller_Varien_Router_Admin`级)、默认(`Mage_Core_Controller_Varien_Router_Default`级)

以下是客户模块`/etc/config.xml`中已定义路线的示例:

```
<routers>
    <customer>
        <use>standard</use>
        <args>
            <module>Mage_Customer</module>
            <frontName>Customer</frontName>
        </args>
    </customer>
</routers> 
```

2)前端控制器触发的事件:

*   `controller_front_init_before`

*   `controller_front_init_routers`

*   `controller_front_send_response_before`

*   `controller_front_send_response_after`

### URL 重写

#### Magento 中的 URL 结构/处理。

Magento 中的链接具有以下结构:

`https://user:password@host:443/{base_path}/{base_script}/{storeview_path}/{module_front_name}/{controller_name}/{action_name}/{param1}/{value1}?{
query_param=query_value#fragment}`

*   `user:password@host:443/{base_path}/{base_script}`:运行 Magento 的脚本文件的路径。通常，它是一个`index.php`文件或者一个使用访问重写规则到`index.php`的路径

*   `{storeview_path}`:如果网站有多商店视图模型(例如，在 url 中标记的多语言支持)，将使用商店视图代码

*   `{module_front_name}/{controller_name}/{action_name}`:匹配模块控制器的路径和动作方式

*   `{param1}/{value1}`:URL 发送的参数名称和值

*   `?query_param=query_value#fragment`:通过标准 GET 请求发送的参数查询

上述链接将遍历以下文件，每个文件都有助于匹配 url 的模式，并调用正确的模块-控制器-操作来输出内容:

*   `app/Mage.php (Mage::app()->run())`

*   `app/code/core/Mage/Core/Model/App.php`

*   初始化和调度控制器`$this->getFrontController()->dispatch();`

*   `app/code/core/Mage/Core/Controller/Varien/Front.php`

*   从`config.xml`中选择定义的路由器以匹配模块-控制器-动作`$router->match($this->getRequest())`

    *   `app/code/core/Mage/Core/Controller/Varien/Router/Admin.php`
    *   `app/code/core/Mage/Core/Controller/Varien/Router/Standard.php`
    *   `app/code/core/Mage/Core/Controller/Varien/Router/Default.php`
*   `app/code/core/Mage/Core/Controller/Varien/Action.php`

*   调用动作方法(例如:`indexAction()`)

#### URL 重写过程

Url 在以下情况下会被重写:

*   核心 url 重写:优先于路由器，将根据数据库(`core_url_rewrite`表)中定义的行，尝试将定制的请求路径转换为标准的`module/controller/action`路径

*   模块名称重写:将在模块的`config.xml`中定义一个前置名称，以重写实际的路径和名称(例如:`Mage_Customer`将通过使用其定义的前置名称‘customer’来匹配 url)

*   定制路由器将重写 url(主要用于 SEO 目的):对于默认 Magento，一个例子是 Cms 模块，它将匹配 url 标识符(如`homepage.html`)到模块“Mage_Cms”、控制器“页面”、动作“视图”和参数 id(将匹配数据库中的 CMS 实体行)。

## 标准和最佳实践

### 示例配置 xml

这是模块 config.xml(路径:`{base_app_folder}/app/local/{module_name}/etc/config.xml`)的示例，包括主要配置:

```
<?xml version="1.0"?>
<config>
    <modules>
        <{{localnamespaceC}}_{{modulenameC}}>
            <version>0.1.0</version>
        </{{localnamespaceC}}_{{modulenameC}}>
    </modules>
    <global>
        <models>
            <{{localnamespaceL}}_{{modulenameL}}>
                <class>{{localnamespaceC}}_{{modulenameC}}_Model</class>
            </{{localnamespaceL}}_{{modulenameL}}>
        </models>
        <resources>
            <{{localnamespaceL}}_{{modulenameL}}_setup>
                <setup>
                    <module>{{localnamespaceC}}_{{modulenameC}}</module>
                </setup>
                <connection>
                    <use>core_setup</use>
                </connection>
            </{{localnamespaceL}}_{{modulenameL}}_setup>
        </resources>

        <blocks>
            <{{localnamespaceL}}_{{modulenameL}}>
                <class>{{localnamespaceC}}_{{modulenameC}}_Block</class>
            </{{localnamespaceL}}_{{modulenameL}}>
        </blocks>

        <helpers>
            <{{localnamespaceL}}_{{modulenameL}}>
                <class>{{localnamespaceC}}_{{modulenameC}}_Helper</class>
            </{{localnamespaceL}}_{{modulenameL}}>
        </helpers>
    </global>
    <frontend>
        <routers>
            <{{localnamespaceL}}_{{modulenameL}}>
                <use>standard</use>
                <args>
                    <module>{{localnamespaceC}}_{{modulenameC}}</module>
                    <frontName>{{modulenameL}}</frontName>
                </args>
            </{{localnamespaceL}}_{{modulenameL}}>
        </routers>
        <layout>
            <updates>
                <{{localnamespaceL}}_{{modulenameL}} module="{{localnamespaceC}}_{{modulenameC}}">
                   <file>{{modulenameL}}.xml</file>
                </{{localnamespaceL}}_{{modulenameL}}>
            </updates>
        </layout>
    </frontend>
</config> 
```

图例:

*   localnamespaceC 和 localnamespaceL 是您在 app/local/{namespace}中选择的命名空间目录的名称(C 表示单词的首字母大写，L 表示小写)

*   modulename 和 modulename 是您的模块在 app/local/{ namespace }/{ modulename }中的名称

### Magento 的工厂方法

Magento 利用工厂方法实例化模型、助手和块。这些方法是:

*   `Mage::getModel('{module}/{path to file in model directory}')`–返回模型目录中某个类的实例

*   `Mage::helper('{module}/{path to file in helper directory}')`–返回助手目录中某个类的单一实例

*   `Mage::getSingleton('{module}/path to file in model directory')`–返回模型目录中某个类的单一实例

*   `Mage::getResourceModel('{module}/{path to file in model/resource directory}')`–返回模型/资源目录中类的实例。

*   `Mage::getBlockSingleton('{module}/{path to file in block directory}')`–初始化控制器动作的布局后，返回块目录中某个类的单一实例。

### 重写核心

Magento 的架构提供了几种重写核心文件的方法。让我们看看它们是什么，并列出一些利弊:

a)通过在“本地”代码池中创建类似的目录路径和文件名来重写文件

*   您需要复制整个类及其所有属性，即使有一行代码需要更改(缺点)

*   如果文件也在定制模块中被覆盖，对先前应用的更改的跟踪可能会丢失，因为它不遵守开发人员习惯的标准。

b)在自定义模块(`local/MyNamespace/MyCustomModule/`)中重写文件，用核心模块名创建子目录，并添加原来的子目录树(如 local/my namespace/MyCustomModule/Model/Core/rewrite . PHP)。

*   在自定义模块名中覆盖一个文件而不使用 Magento 的模块名也会使开发人员困惑，并且在不同的地方添加另一个重写可能会丢失对这些更改的跟踪。然而，通过添加模块配置(在`app/etc/modules/MyCustomModule.xml`中)来标记对`Mage_Core`模块的依赖，你可以给开发人员一个文件重写的提示。

c)用 Magento 的模块名和保留路径(如`local/MyNamespace/Core/Model/Rewrite.php`)重写自定义模块中的文件

*   这种重写方法是最清晰的，因为开发人员可以根据命名约定跟踪更改。

*   将鼓励在同一方面工作的其他开发人员扩展已经更改的功能，而不是覆盖它

*   这种重写方法的一个小缺点是，所选名称空间中的模块数量会随着每个被不同 Magento 模块覆盖的文件而增加。

### **模块间的依赖关系**

a)扩展 Magento 核心模块时，需要配置模块之间的依赖关系

例如`app/etc/modules/MyNamespace_Customer.xml`:

```
<MyNamespace_Customer>
    <active>true</active>
    <codePool>local</codePool>
    <priority>1</priority>
    <depends>
        <Mage_Customer/>
    </depends>
</MyNamespace_Customer> 
```

b)当在更新 Magento 核心模块实体的定制模块中创建 sql 安装程序或数据安装程序时，需要创建依赖关系

例如，MyCustomModule 中的安装程序:

```
$installer = $this;
$installer->startSetup();

$sqlQuote = 'ALTER TABLE ' . $this->getTable('sales_flat_quote') .
    ' ADD `is_urgent` TINYINT UNSIGNED NOT NULL DEFAULT 0';

$installer->run($sqlOrder);
$installer->run($sqlQuote);

$installer->endSetup(); 
```

例如，依赖关系配置(销售报价实体被更改，需要添加与“销售”模块的依赖关系):

```
<MyNamespace_MyCustomModule>
    <active>true</active>
    <codePool>local</codePool>
    <priority>1</priority>
    <depends>
        <Mage_Sales/>
    </depends>
</MyNamespace_MyCustomModule> 
```

c)从 Magento 核心模块扩展/重写文件(模型、助手、块、控制器)需要模块之间的依赖，否则最后更改的版本将是任意的，您将失去对重写的控制。

模块依赖对于 sql 语句的优先顺序非常重要，在全新安装时，它们将避免冲突、崩溃或错误的最终数据。

## 结论

在本文中，我介绍了 Magento 的一些基础知识，这些知识将有助于开发人员理解基本架构，并为构建他们的定制功能提供一个起点。如果您想了解 Magento 的具体升级，或者有您感兴趣的用例，请告诉我们。请继续关注 Magento 2 的教程和解释！

## 分享这篇文章