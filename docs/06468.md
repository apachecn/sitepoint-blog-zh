# WordPress 多环境:设置站点点

> 原文：<https://www.sitepoint.com/wordpress-multi-environment-setting-up-sitepoint/>

如果你曾经安装过 WordPress，你会知道它安装起来超级简单快捷。然而，如果你曾经尝试过在多个服务器上设置 WordPress，从一个存储库自动部署或者在不同的环境下运行，你就会知道这类任务是另一回事。

在本文中，我将解释我们如何设置 WordPress 来构建和部署新的 SitePoint，以适应各种环境，即开发、试运行和生产。

## 问题是

WordPress 的配置有一个中心位置:`wp-config.php`。这个文件包含了博客运行的所有基本信息，比如如何连接到数据库。现在，当你下载 WordPress 的时候，你会得到一个名为`wp-config-sample.php`的文件，这个文件需要被复制到`wp-config.php`，并根据你的设置进行调整。如果您只有一个环境(您没有在服务器上编辑您的文件，对吗？)或者注意不要用开发配置覆盖生产配置。如果您有一个额外的暂存设置，管理起来会非常棘手。

理想情况下，我们希望每个环境都有不同的配置文件，由 WordPress 自动加载。此外，如果没有文件实际上包含任何密码或其他敏感数据，以便文件可以安全地存储在存储库中，那就太好了。

## 解决方案

让我们从为每个环境创建一个配置文件开始。通常，你至少有三个阶段:开发、试运行和生产。我把这些叫做`wp-config-production.php`、`wp-config-staging.php`和`wp-config-development.php`。这些文件中的每一个都将包含特定环境的配置。我们稍后会谈到文件的内容，首先我们需要告诉 WordPress 如何为环境找到正确的配置文件。

这里的问题是 WordPress 不知道加载哪个，它只是加载`wp-config.php`。我们不想改变 WordPress 的行为，所以我们必须包含来自`wp-config.php`的环境特定的配置文件。为了做到这一点，我们将修改`wp-config.php`,如下所示:

```
< ?php
/**
 * The base configurations of the WordPress.
 */

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
  define('ABSPATH', dirname(__FILE__) . '/');

/** Include environment specific configuration. */
define('WP_ENV', (getenv('WP_ENV') ?: 'production'));
require_once(ABSPATH . 'wp-config-' . WP_ENV . '.php');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH . 'wp-settings.php');
```

基本上，我们删除了从`ABSPATH`到`wp-settings.php`之间的所有内容。在这之间，我们加载正确的配置文件。

这是如何工作的？

环境的名称存储在所谓的环境变量中。环境变量在虚拟主机配置中设置，可能如下所示:

```
<VirtualHost *:80>
        SetEnv WP_ENV "development"
        ServerName  blog.vm
        ServerAlias blog.vm

        <Directory /var/www/blog>
                ...
        </Directory>

</VirtualHost>
```

`WP_ENV`可以通过`getenv('WP_ENV')`用 PHP 读取。在我们的例子中，我们将该值存储在一个名为`WP_ENV`的常数中。`require_once(ABSPATH . 'wp-config-' . WP_ENV . '.php');`将加载正确的配置文件。

我们现在需要将所有设置(除了`ABSPATH`和包含的`wp-settings.php`)从`wp-config-sample.php`复制到每个配置文件中。

但是，正如我前面提到的，最好不要直接在文件中输入敏感数据。我们有两个选择:

## 选项 1:环境变量

我们已经使用了一个环境变量来确定我们所处的环境。同样，我们可以设置数据库密码等。并通过`getenv`读入它们。例如:

```
define('DB_NAME', getenv('DB_NAME') ?: 'blog');
```

这从环境中读取`DB_NAME`，并返回使用`blog`。当与其他开发人员共享代码库时，提供后备尤其有用。如果他们使用缺省值建立数据库，一切都将“正常工作”，不需要做任何改变。

另一方面，如果他们喜欢不同的设置，就可以很容易地更改环境值，而不必接触代码，如果您想要在相同的代码基础上工作，这是至关重要的。

## 选项 2:在构建时编写

如果使用构建系统来部署代码，也可以使用在构建过程中替换的占位符，例如:

```
define('DB_NAME', '##DB_NAME##');
```

但是，该选项仅适用于登台和生产配置，不适合您的开发配置文件。

当您选择在任何地方使用环境变量时，您甚至可以只使用一个通过`getenv`读取所有值的配置文件。然而，我更喜欢有单独的文件。这使得查看哪个环境使用了哪个配置变得更加容易。此外，您通常有一些只在开发中需要的常量，例如调试:

```
define('WP_DEBUG', true);
```

这涵盖了不同配置文件的设置。我发现在处理多种环境时非常有用的另一件事是在配置文件中设置`WP_HOME`和`WP_SITEURL`，而不是从数据库中读取。

## 结论

总结一下，这种设置的优点如下:

*   很容易看出特定环境是如何配置的
*   可以轻松添加新环境
*   环境可以使用不同的常数
*   所有配置文件都可以安全地提交给存储库
*   使用环境变量 setup，任何时候都不会在文件中存储任何凭据

## 分享这篇文章