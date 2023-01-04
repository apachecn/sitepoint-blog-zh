# 在 WordPress 中安排任务:插件开发者指南

> 原文：<https://www.sitepoint.com/wordpress-scheduling/>

Scheduling the execution of certain functions at a time in the futureis a key feature of the WordPress API. It was added to the developers’ API in WordPressversion 2.0\. It’s also a topic that’s poorly understood by many developers,as it’s only briefly covered in WordPress’s documentation. Apart fromfacilitating maintenance tasks, scheduling code to be executed in the futureopens up a lot of interesting possibilities for plugin developers.Here are some real-world scenarios where the ability to scheduleexecution of code will be useful for developing plugins:

*   一个网站分析插件应该能够产生一个网站使用统计每周报告，然后发送电子邮件给网站管理员。
*   电子邮件简讯插件可能需要在有每小时发送限制的服务器上发送电子邮件。该插件将不得不排队发出电子邮件，每小时发送一个给定的数字。
*   图表图像生成器可以从数据库中收集数据，生成图像，并将其保存到缓存中，而不是在每次请求时都生成图像，这将避免数据库上不必要的负载。
*   一个备份插件将需要一个功能，每周激活一次，这样它就可以生成数据库备份，并向网站管理员发送电子邮件。
*   一个会员网站插件将需要在一个月底到期订阅的能力。
*   一个 Twitter 侧边栏插件，每小时只向 Twitter 服务发出一次请求，以获取和加载您的最新 tweets。

在这篇文章中，我将向你展示如何使用 WordPress API 来安排任务在特定的时间或间隔运行。假设你已经熟悉了构建 WordPress 插件的基础知识；否则，通读一下 WordPress Codex 上的[编写插件页面，以获得一些开始指针。](http://codex.wordpress.org/Writing_a_Plugin)

## WordPress 如何触发预定任务

在我们看如何在你的 WordPress 插件中设置计划任务之前，你需要理解 WordPress 是如何在幕后处理计划任务的。每当有人访问网站的一个页面时，WordPress 都会检查是否有任何预定的功能需要执行。这基于自上次执行以来的时间间隔或任务被调度的时间。WordPress 使用`fsockopen` PHP 函数请求位于 WordPress 安装目录中的`wp-cron.php`文件。为了确保网站访问者不受此调用的影响，请求的超时时间非常短(0.1 秒)。发起请求后，WordPress 不等待响应，继续为用户提供页面服务。0.1 秒的超时足以启动`wp-cron.php`文件的执行。`wp-cron.php`文件执行的第一条语句是:

```
ignore_user_abort(true);
```

这确保了即使请求页面的客户端关闭了连接，执行仍会继续(否则会终止执行)。该脚本将根据当前时间和上次执行时间检查任何应该运行的任务。例如，假设您有一个计划每小时运行一次的任务，最后一次运行是在下午 4:13。当用户在下午 5:11 从您的站点请求页面时，`wp-cron.php`将跳过您的任务，因为它最后一次运行是在不到一个小时之前。几分钟后，在下午 5:15，当另一个用户查看一个页面时，`wp-cron.php`将运行您的任务。

## 计划任务的类型

为了安排一项功能的运行，无论是作为一次性任务还是作为重复任务，您只需将它附加到一个操作挂钩上。这允许几个函数被分配给同一个调度钩子。如果你对 WordPress 动作钩子不熟悉，让我们先快速回顾一下，看看它们是如何应用于日程安排的。如果你已经熟悉了 WordPress action hooks，请随意跳过这一部分，直接进入[名为“计划任务”](#section_scheduling "Scheduling Tasks")的部分。

### 什么是 WordPress 动作钩子？

动作挂钩是在给定点执行的一组命名函数。当动作被执行时，任何附加到给定动作挂钩的功能都将被执行。WordPresscore 定义了许多动作，这些动作依次在 WordPress core 代码的许多地方被触发。让我们看一个例子:WordPress 的核心包括`wp_head`函数，你可能很熟悉。在该函数中，您会发现这行代码:

```
do_action('wp_head');
```

在 WordPress 主题中，`header.php`文件的`<head>`部分调用了`wp_head`函数。当该函数被调用时，上面的代码行将触发任何已经链接到`wp_head`动作钩子的函数的执行。区分`wp_head`方法和`wp_head`动作钩子是很重要的:它们有相同的名字，但是不同。如果我们想在不编辑`header.php`文件的情况下向所有页面添加样式表，我们只需创建一个我们自己的函数来生成所需的`<link/>`标签，然后将这个函数附加到`wp_head`动作。下面的代码实现了这一点:

```
//This function generates the link tagfunction myplugin_generatestylesheet(){   //Form the URL to the css file stored in our plugin directory   $pluginURL = get_bloginfo('home').'/wp-content/plugins/myplugin/mycss.css';   //Write it to the browser   echo '<link href="'.$pluginURL.'" type="text/css" rel="stylesheet" />';}//Now we add this function to the wp_head action.add_action('wp_head','myplugin_generatestylesheet');
```

`add_action`函数是这里的关键:它将我们的`myplugin_generatestylesheet`函数附加到`wp_head`动作钩子上。现在，当`wp_head`方法调用`do_action('wp-head')`时，我们的函数将被调用，链接标签将被输出。您可以定义自己的操作，并向该操作添加任意多的功能。这是 WordPress 日程安排功能的关键，我们很快就会看到。

## 调度任务

在 WordPress 中，我们可以用两种不同的方式安排函数的调用:

*   一次性任务—在指定时间执行一次的任务
*   周期性任务—定期执行的任务

我将依次讨论它们。

### 安排一次性任务

为了安排在特定时间执行一次并且不再执行的动作，我们使用了`wp_schedule_single_event`函数。首先，我们定义想要调度的函数:

```
function my_function(){  // Do something. Anything.}
```

接下来，我们将这个函数添加到我们的操作钩子中。等等，我们需要先创建动作钩子，对吗？不对。事实证明我们没有。只需用新的名称将函数添加到一个动作中，就可以创建新的动作。因此，在这一步中，我们创建了`my_action`动作，并向该动作添加了`my_function`函数:

```
add_action('my_action','my_function');
```

最后，我们安排 action 钩子在期望的时间运行:

```
wp_schedule_single_event($timestamp,'my_action');
```

在上面的代码行中，`$timestamp`是我们希望动作运行的时间，格式为 Unix 时间戳。

### 计划周期性任务

使用`wp_schedule_event`函数调度定期调用的函数。定义周期性任务的步骤与一次性任务的步骤非常相似。同样，我们首先定义一个包含我们希望定期执行的代码的函数:

```
function my_periodic_function(){  // Do something regularly.}
```

我们将该函数添加到新的操作挂钩中:

```
add_action('my_periodic_action','my_periodic_function');
```

最后，我们计划以所需的频率执行操作:

```
wp_schedule_event($timestamp, 'hourly', 'my_periodic_action');
```

这个函数有一个新参数:频率(上例中的`'hourly'`)。`$timestamp`是动作钩子第一次运行的时间，之后将以第二个参数决定的频率运行。最后一个参数，和`wp_schedule_single_event`一样，是与时间表挂钩的动作。WordPress 定义了三种重复频率:每小时、每天和每两天。然而，如果你需要一个不同的频率，不要害怕！您可以定义自己的重复频率。

## 创建您自己的重复频率

如果您需要一个更短的重复周期，比如每五分钟一次，或者更长，比如每个月一次，该怎么办？在这些情况下，我们可以定义自己的重复频率，并将其导出到 WordPress。这是通过在`cron_schedules`过滤器上附加一个函数来实现的:

```
add_filter('cron_schedules','my_cron_definer');    function my_cron_definer($schedules){  $schedules['monthly'] = array(      'interval'=> 2592000,      'display'=>  __('Once Every 30 Days')  );  return $schedules;}
```

完成后，我们现在可以在调用`wp_schedule_event`时使用新的频率:

```
wp_schedule_event(time(),'monthly','my_action');
```

这将从当前时间开始，每 30 天调用一次`my_action`。

## 清除预定动作

假设管理员在使用我们的数据库备份插件时，发现他们的收件箱不断被备份邮件填满。考虑到他们博客上适度的活跃程度，也许每日备份有点过头了。因此管理员登录到 WordPress 仪表板，将备份频率从每天改为每周。在这种情况下，数据库插件应该改变备份例程的频率。这里我们遇到了一点障碍:WordPress 的调度 API 不允许我们改变已经被调度的钩子的周期。我们需要首先删除每日日程表，然后附加一个新的每周挂钩。

### 取消周期性事件的计划

要删除重复动作的所有未来实例，应使用`wp_clear_scheduled_hook`功能。语法再简单不过了:

```
wp_clear_scheduled_hook('name_of_hook');
```

### 取消安排一次性事件

使用`wp_unschedule_event`功能对一次性事件进行计划外安排。使用这个函数没有取消 recurringevents 的调度简单。语法如下:

```
wp_unschedule_event($timestamp, $hook, $arguments);
```

这里，`$timestamp`参数是事件计划运行的时间。由于您可能不知道动作被安排在什么时候运行，您可以使用`wp_next_scheduled`函数获取时间，然后将其传递给`wp_unschedule_event`:

```
$whenNext = wp_next_scheduled('my_action');wp_unschedule_event($whenNext,'my_action');
```

## 任务应该安排在哪里？

现在，您已经知道了如何计划和取消计划任务，剩下的问题是如何在代码中设置这个计划。如果你的插件应该在激活后立即开始执行任务，那么任务必须在 activationhook 中注册。激活钩子由 WordPress 提供，当插件第一次被激活时被触发。当您的插件被停用时，您的插件所调度的所有任务都应该是未调度的。这确保了如果你的插件被重新激活，WordPress 不会安排重复的任务。这也确保了任何其他使用您的插件所调度的任务的插件不会产生不希望的行为。这个非常简单的插件演示了当插件被激活时任务的调度，以及当插件被停用时任务的删除:

```
<?php   /*   Plugin Name: My Plugin   Plugin URI: https://www.sitepoint.com/   Description: A plugin to learn crontasks.   Version: 2.9.2   Author: Raj Sekharan   Author URI: http://www.krusible.com   *//* The activation hook is executed when the plugin is activated. */register_activation_hook(__FILE__,'myplugin_activation');/* The deactivation hook is executed when the plugin is deactivated */register_deactivation_hook(__FILE__,'myplugin_deactivation');/* This function is executed when the user activates the plugin */function myplugin_activation(){  wp_schedule_event(time(), 'hourly', 'my_hook');}/* This function is executed when the user deactivates the plugin */function myplugin_deactivation(){  wp_clear_scheduled_hook('my_hook');}/* We add a function of our own to the my_hook action.add_action('my_hook','my_function');/* This is the function that is executed by the hourly recurring action my_hook */function my_function(){   //do something.}?>
```

## 列出当前计划的所有任务

在开发使用调度任务的插件时，能够看到任务是否确实被调度，以及它是否被分配了正确的频率通常是很有帮助的。这可以通过使用[控制核心](http://wordpress.org/extend/plugins/core-control/)插件来完成。控制核心插件通过提供对 WordPress 核心的某些方面的控制来帮助插件开发者的工作。控制核心向管理面板添加一个部分，显示当前计划的所有任务。

## 警告

这种计划任务的方法带有一些警告标签:

Be aware that it’s not 100% precise

For those of you accustomed to system-based cron jobs, oneobvious disadvantage of the WordPress scheduling API is that it’simpossible to ensure that the action you scheduled will run at anexact time. Although the scheduled functions will definitely beexecuted, they may run a little later than you’d like them to. Thisis because WordPress relies on website traffic to triggertasks.In cases where a website may lack adequate traffic, ascheduled task might not execute at the proper times. While anaction was scheduled to run at 12 a.m., it may actually run at 12:45a.m., because no one visited the website that late at night.

Use appropriate frequencies

Some time ago, I created a plugin that generated some contentpages for a blog by querying the Yahoo Search web service and theTwitter web service. The script was scheduled to run every fiveminutes. I wrote the plugin and configured it to run on myblog.The next day, my web hosting account was temporarily suspendedfor using excessive resources. It turned out that the scriptsometimes ran for far more than five minutes. This meant that a newtask would often start before the previous one had completed; aftercouple of hours there were 12 instances of the script running. Soonthere was no more memory left over for any other process, and thescript started to affect other website owners on the shared hostingserver. The lesson here is that you should ensure that you choosethe recurrence interval for your resource-consuming procedurescarefully.

Use flags when using shared resources

Scheduled tasks will often make use of the file system, orcertain database tables. Simultaneous access or operations on theseresources can corrupt or destroy the integrity of the data. This canhappen when your plugin has two or more tasks that rely on the samedata file or database table. Be sure to use read/write locks toprotect access to your resources.

## 最终注释

虽然不用 WordPress 调度 API 也可以创建执行周期性任务的 WordPress 插件，但是那样做是不利的。设置插件的过程将变得更加复杂，因为用户将不得不手动设置 crons。复杂的程序只会减少能从使用你的插件中获益的博客数量。插件的设置过程应该像点击 WordPressadministration 面板插件部分的 Activate 一样简单。通过利用 WordPress 的内置调度功能，你可以给你的插件提供复杂的特性，而不会让它们使用起来更复杂。

## 分享这篇文章