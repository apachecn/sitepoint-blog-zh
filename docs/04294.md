# 快速提示:用 Laravel 4 的方式开发 Laravel 5 包

> 原文：<https://www.sitepoint.com/quick-tip-develop-laravel-5-packages-laravel-4-way/>

Laravel 5 出来了，你可能已经注意到旧的包开发流程[变了](http://laravel.com/docs/5.0/packages)。在这个快速技巧中，我们将看到如何将 Laravel 5 之前的包开发的乐趣带到最新的版本中。

## 用拉弗尔 4 号的方式

在这一部分中，我们将加载并配置旧方法，以便与 Laravel 5 一起工作。按照以下步骤开始:

*   要求`composer.json`中的`"illuminate/workbench": "dev-master"`。

    ```
    // composer.json

    	"require": {
    		"laravel/framework": "5.0.*",
                    "illuminate/workbench": "dev-master"
    	}
    ```

*   将`"workbench"`目录添加到您的`autoload > classmap`数组中，并运行`composer update`来反映这些更改。

    ```
    // composer.json

    	"autoload": {
            "classmap": [
                "database",
                "workbench",
                "..."
            ]
        }
    ```

*   在你的`config`文件夹中创建一个`workbench.php`，并定义你的名字和电子邮件。

    ```
    // config/workbench.php

    	return [
      		"name"    => "John Doe",
      		"email"   => "john@gmail.com"
    	];
    ```

*   将`'Illuminate\Workbench\WorkbenchServiceProvider',`添加到您的`config/app.php`提供商。

    ```
    // config/app.php

    	"providers" => [
    		'...',
    		'Illuminate\Workbench\WorkbenchServiceProvider',
    	],
    ```

现在你已经准备好遵循官方的 Laravel 4 [文档](http://laravel.com/docs/4.2/packages)。

创建包一直是我们应用程序开发生命周期的一部分，所以有一个平滑的方式来创建包是有意义的。Laravel 4 已经包含了这一部分，我希望 Laravel 5 将工作台包恢复到默认安装中。

## 分享这篇文章