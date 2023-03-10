# Rails 2.1 中的时区

> 原文：<https://www.sitepoint.com/timezones-in-rails-21/>

时区支持是 web 应用程序的一个非常重要的组成部分，这并不奇怪，因为 web 确实是一个全球性的媒介。如果您正在构建一个必须处理时间的应用程序，比如发送日历提醒，甚至是像正确显示时间戳这样简单的事情，您可能需要注意不同的用户生活在不同的时区。听起来容易吗？然后继续读下去…

一个快速的背景课:显然，世界上不是每个地方都同时处于白天，当澳大利亚是中午时，美国是半夜。为了确保正午在珀斯和在亚特兰大的意义相同，世界被分成围绕协调世界时(UTC)的时区，该时区贯穿英国格林威治(这就是为什么 UTC 也被称为格林威治标准时间或 GMT)，范围从-12 GMT 到+14 GMT。

因为每个时区都是根据这个假想的时区时间定义的，所以在我们的时区编码冒险中使用这个时区时间作为基础是有意义的。在 Rails 中，您可以通过在/config/environment.rb 中设置以下值来告诉 ActiveRecord 将所有日期保存为 UTC:

```
 config.time_zone = 'UTC' 
```

计算本地时间最明显和简单的方法是将时差加到当前的 GMT 时间上——如果你只需要这么做，我现在就可以结束这篇文章，但是，唉，由于夏令时的原因，这并不总是那么容易。

夏令时(DST)背后的理论是将当地时间提前一段时间(通常是一个小时)，让太阳看起来升起和落下更晚，这意味着下班后你有更多的时间做事情。不幸的是，这仅仅意味着你要花更长的时间去解决所有的时区问题。如果每个国家都在同一天采用夏令时，情况不会那么糟糕，但现实是，并非所有国家都采用夏令时，每个国家在不同时间采用夏令时(事实上，一个国家内的地区在不同时间采用夏令时并不罕见)，也并非所有国家都将夏令时提前一个标准小时。

那我们该怎么办？令人欣慰的是，有一组名为 zoneinfo 数据库(或 Olson 数据库)的库，基本上记录了每个国家的每个 DST 转换。gem tzinfo 是该库的 Ruby 版本，在 Rails 2.1 之前，您需要使用该库(可能需要插件的帮助)来使时区工作，但现在它被集成到 Rails 2.1 和更高版本中。

第一步是存储用户的时区信息。最简单的方法是根据他们的用户详细信息存储他们的时区字符串，我喜欢使用分组的 select 标记来实现这一点:

```
 <select name="user[timezone]" id="user_timezone">
  <%= option_groups_from_collection_for_select TZInfo::Country.all.sort, "zones", "name", "identifier", "friendly_identifier", @user ? @user.timezone : nil %>
 </select&gt 
```

这将保存像“澳大利亚/墨尔本”或“美国/洛杉矶”这样的条目。接下来，您只需要告诉 Rails 每个请求所在的时区，现在使用 Time.zone=方法非常简单。在 application.rb 的 before_filter 中调用此方法，它将为每个请求设置时区:

```
 def timezone_manager
  Time.zone = current_user.timezone if current_user && current_user.timezone
end 
```

很好很容易！每次你使用或打印出一个时区，它将在用户的本地区域。

如果您想了解更多关于 Rails 2.1 时区支持的内容，请查看这个精彩的教程:[http://mad . ly/2008/04/09/Rails-21-time-zone-support-an-overview/](http://mad.ly/2008/04/09/rails-21-time-zone-support-an-overview/)

## 分享这篇文章