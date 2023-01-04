# 掌握安排事件的 WordPress Cron

> 原文：<https://www.sitepoint.com/mastering-wordpress-cron/>

WordPress Cron 是最有用的特性之一，如果你像我一样，花大量时间使用 WordPress，你会想学习和理解它。

![WordPress Cron for scheduling events](img/30b4027243f3268709a7d5d1adafdbb2.png)

能够在一个紧凑的时间表上运行某些功能对于任何 CMS 来说都是必不可少的，WordPress 有一套功能可以让这个过程变得非常简单，几乎毫不费力。

在这篇文章中，我将介绍以下 WordPress Cron 特性:

*   WordPress Cron 和普通的 Cron 有什么不同
*   安排重复事件
*   安排单个事件
*   取消安排事件
*   指定自定义 Cron 间隔

## Cron 是什么？

你可能对术语“Cron”很熟悉，因为它与 Unix 系统中基于时间的调度程序有关，尽管 WordPress 的“Cron”是不同的；背后的主旨是一样的。

WordPress 如何在内部使用其 Cron 系统的一些例子是检查主题和插件更新，甚至检查是否有准备发布的帖子。

## WordPress Cron 有什么不同？

如果你熟悉 Unix 的 Cron，你可能会认为 WordPress 的 Cron 总是在寻找新任务，并在它们到来时运行它们。这与事实相差甚远，稍后我会解释原因。

WordPress 的 Cron 在页面加载时运行，无论是前端还是后端页面。换句话说，当一个页面加载到你的网站上时，WordPress 会检查是否有需要运行和执行的任务或事件。如果你认为这不理想，那你绝对是对的。

如果你碰巧有一个流量不太大的网站，而你有一个需要在精确时间执行的任务，WordPress 在有人访问你的网站之前不会知道这个任务到期了。哪怕恰好是一个搜索引擎 bot 在爬你的网站。

## 使用 Cron 安排事件

您可以用几行代码安排两种类型的 Cron 事件:

*   单个事件–仅运行一次，在重新安排之前不再运行。
*   重复事件–按计划运行，并设置为使用时间间隔无限期重复发生。

## 安排重复事件

安排重复事件需要您创建一个自定义“动作”,该动作也必须向 Cron 注册。一旦 Cron 运行，您之前创建的自定义“动作”所附带的函数就会被执行。

让我们来看看下面的例子，我们每天都要删除文章的修订。

首先，我们创建自定义的“Action ”,当 Cron 调用钩子时，它会附带我们想要运行的函数。

```
<?php
// delete_post_revisions will be call when the Cron is executed
add_action( 'delete_post_revisions', 'delete_all_post_revisions' );

// This function will run once the 'delete_post_revisions' is called
function delete_all_post_revisions() {

	$args = array(
		'post_type' => 'post',
		'posts_per_page' => -1,
		// We don't need anything else other than the Post IDs
		'fields' => 'ids',
		'cache_results' => false,
		'no_found_rows' => true
	);

	$posts = new WP_Query( $args );

	// Cycle through each Post ID
	foreach( (array)$posts->posts as $post_id ) {

		// Check for possible revisions
		$revisions = wp_get_post_revisions( $post_id, array( 'fields' => 'ids' ) );

		// If we got some revisions back from wp_get_post_revisions
		if( is_array( $revisions ) && count( $revisions ) >= 1 ) {

			foreach( $revisions as $revision_id ) {

				// Do a final check on the Revisions
				if( wp_is_post_revision( $revision_id ) ) {
					// Delete the actual post revision
					wp_delete_post_revision( $revision_id);
				}
			}
		}
	}
}
```

为了安排重复事件，我们使用了带 4 个参数的`wp_schedule_event( $timestamp, $recurrence, $hook, $args )`函数:

*   **$timestamp** — (integer)(必选)希望事件第一次发生的时间。这必须是 UNIX 时间戳格式。WP cron 使用 UTC/GMT 时间，而不是本地时间。使用 time()，在 WordPress 中总是 GMT。(当前时间('时间戳' )是 WordPress 中的本地时间。)
*   **$recurrence** — (string)(必选)事件重复发生的频率。有效值为“`hourly`”、“`twicedaily`”和“`daily`”。稍后我们将看到如何创建我们自己的时间间隔。
*   **$hook** — (string)(必选)要执行的动作挂钩的名称。
*   **$args** —(数组)(可选)传递给挂钩函数的参数。

首先，我们要确保该事件之前没有被安排过，如果没有，我们就继续安排它。

```
<?php
// Make sure this event hasn't been scheduled
if( !wp_next_scheduled( 'delete_post_revisions' ) ) {
	// Schedule the event
	wp_schedule_event( time(), 'daily', 'delete_post_revisions' );
}
```

请注意，您还可以将这段代码添加到一个操作中。如果你是一个插件编写者，你可以设置预定事件在第一次访问插件选项页面时运行。举一个简单得多的例子，我们将把它与 WordPress 的`init`动作联系起来。

```
<?php
// Add function to register event to WordPress init
add_action( 'init', 'register_daily_revision_delete_event');

// Function which will register the event
function register_daily_revision_delete_event() {
	// Make sure this event hasn't been scheduled
	if( !wp_next_scheduled( 'delete_post_revisions' ) ) {
		// Schedule the event
		wp_schedule_event( time(), 'daily', 'delete_post_revisions' );
	}
}
```

现在您已经知道了如何安排重复事件，让我们来看看如何创建一个在重新安排之前不会再次运行的事件。

## 安排单个事件

顾名思义，单个事件是指运行一次后就停止的事件。如果需要，这个单独的事件仍然可以被重新安排。

背后的概念和重复发生的事件是一样的。首先注册一个定制钩子，当 Cron 在服务器上运行时，它会调用这个钩子。一旦 Cron 调用钩子，它的功能就会被执行，这就是你完成任务的基本方式。

例如，我们将为帖子设置一个截止日期。帖子将在发布后 30 天过期。我们将连接到`publish_post`上，这样我们就可以在帖子发布和倒计时开始时安排我们的单个事件。

设置 30 天后删除文章的功能。

```
<?php

// delete_post_after_expiration will be called by Cron
// We are going to be passing the Post ID so we need to specify that 
// we'll need 1 argument passed to the function
add_action( 'delete_post_after_expiration', 'delete_post_after_expiration', 10, 1 );

// This function will run once the 'delete_post_after_expiration' is called
function delete_post_after_expiration( $post_id ) {

	// Takes care of deleting the specified Post
	wp_delete_post( $post_id, true );
}
```

很简单，对吧？现在，我们需要在帖子实际发布后安排活动。为了完成这个任务，我们需要使用带 3 个参数的`wp_schedule_single_event( $timestamp, $hook, $args )`函数。

*   **$timestamp** — (integer)(必选)您希望事件发生的时间。这必须是 UNIX 时间戳格式。
*   **$hook** — (string)(必选)要执行的动作挂钩的名称。
*   **$args** —(数组)(可选)传递给钩子函数的参数。

下面快速看一下所有这些动作和钩子是如何放在一起的。

```
<?php

// schedule_post_expiration_event runs when a Post is Published
add_action( 'publish_post', 'schedule_post_expiration_event' );

function schedule_post_expiration_event( $post_id ) {

	// Schedule the actual event
	wp_schedule_single_event( 30 * DAY_IN_SECONDS, 'delete_post_after_expiration', array( $post_id ) );	
}
```

我们正在使用 WordPress 已经准备好的一些时间常数来使我们的生活更容易。关于这些常数的更多信息，您可以使用时间常数进入[，但这里有一个快速概述:](http://codex.wordpress.org/Transients_API#Using_Time_Constants "WordPress' Time Constants")

*   `MINUTE_IN_SECONDS` = 60(秒)
*   `HOUR_IN_SECONDS` = 60 *分钟秒
*   `DAY_IN_SECONDS` = 24 *小时秒
*   `WEEK_IN_SECONDS` = 7 *天 _ 秒
*   `YEAR_IN_SECONDS` = 365 *天 _ 秒

## 取消安排事件

既然你已经知道了如何安排重复性的和单个的事件，那么知道如何取消这些事件也是很有用的。

您可能想知道，为什么要取消活动计划？有一个很好的理由，特别是如果你在插件中包含了一些排序调度事件。

Crons 存储在 wp_options 表中，只需简单地停用和删除你的插件。即使所述插件不再可用，WordPress 仍然会尝试运行你的活动。话虽如此，请确保您在插件或自定义实现中正确地取消了事件计划。

取消 Cron 事件的调度相对容易，您需要知道的只是钩子的名称，以及特定 Cron 下一次运行的预定时间。我们将使用`wp_next_scheduled()`来查找下一次发生的时间，只有到那时我们才能使用`wp_unschedule_event()`取消对它的调度。

考虑到我们的第一个例子，我们将按以下方式取消安排事件。

```
<?php

// Get the timestamp of the next scheduled run
$timestamp = wp_next_scheduled( 'delete_post_revisions' );

// Un-schedule the event
wp_unschedule_event( $timestamp, 'delete_post_revisions' );
```

### 自定义 Cron 间隔

可以设置自定义的 Cron 间隔，您可以在使用 Cron 安排事件时使用这些间隔。为此，我们只需连接到`cron_schedules`过滤器并添加我们自己的过滤器。让我们来看看如何添加一个自定义的间隔，设置为每 10 分钟运行一次。

```
<?php
// Add custom cron interval
add_filter( 'cron_schedules', 'add_custom_cron_intervals', 10, 1 );

function add_custom_cron_intervals( $schedules ) {
	// $schedules stores all recurrence schedules within WordPress
	$schedules['ten_minutes'] = array(
		'interval'	=> 600,	// Number of seconds, 600 in 10 minutes
		'display'	=> 'Once Every 10 Minutes'
	);

	// Return our newly added schedule to be merged into the others
	return (array)$schedules; 
}
```

## 结论

使用 WordPress 的 Cron 再简单不过了，它是一个非常好的有趣的工具，一定会帮助你使你的插件更加健壮。学习所有这些功能并将它们应用到现实世界的应用程序中是掌握 WordPress 的 Cron 来安排事件的最好方法。

## 分享这篇文章