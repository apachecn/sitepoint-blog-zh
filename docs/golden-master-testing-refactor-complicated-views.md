# 金主测试:重构复杂视图

> 原文：<https://www.sitepoint.com/golden-master-testing-refactor-complicated-views/>

![Robot of the metal parts on a dark grungy background and metal frame](img/24ca6c6030b092863705e78441a0c331.png)

视图模板应该简单。事实上，它们应该非常简单，以至于没有必要为它们计算复杂度。然而，在成功的应用中，情况往往并非如此。当每个人的注意力都集中在别处的紧急事件上时，逻辑悄悄地溜进了模板。

当测试覆盖率很低时，重构模板可能会很棘手，大多数时候都是这样。理所当然！为什么要测试一个会定期改变并且不应该包含任何逻辑的东西呢？

那么，当您决定确实需要将逻辑从模板中移出并移到应用程序中更合适的位置时，就要找到一种方法在您背后竖起一堵墙。

## 现实世界的例子

Tracks 是一个时间管理工具，基于大卫·艾伦 2002 年的书《T2 完成任务》。

自从 Rails 处于测试阶段以来，该应用程序一直处于持续、积极的开发中，与 Rails 版本保持同步。正如许多已经存在了一段时间的应用程序一样，代码库的某些部分已经变得有点[笨拙和失控](https://codeclimate.com/github/TracksApp/tracks)。

这为我们提供了一个机会，使用真实世界的代码来研究 Golden Master 技术，而不是一个微小的、合成的示例，让读者不知道如何将经验转移到实际的生产代码中。

## 探索轨迹

仔细查看代码库，您会发现大型模型、大型控制器和复杂视图。例如，控制器和视图共享大量的实例变量。

所有这些都表明，可能存在缺失的抽象和未命名的概念，这使得对代码库进行推理变得更加容易。

[`StatsController`](https://github.com/TracksApp/tracks/blob/dbf1cb61ff4409dcf0a0eb3a1222a5d1239ef0e8/app/controllers/stats_controller.rb) 充分说明了这一点。该文件超过 450 行。它包含 37 个方法，其中只有 17 个是控制器动作。

这些动作有两种风格:

1.  普通 HTML 页面
2.  `key=value`对的纯文本集合

`key=value`对用于在 HTML 响应中呈现图表。这些模板进行一些计算，设置一些局部变量，并呈现在控制器中计算的大量实例变量。

此外，它们是重复的。

也就是说，它们不是完全复制的，这太容易了。他们很相似，但不完全相同。

在本练习中，我们将采用两个非常相似的模板— [`actions_visible_running_time_data.html.erb`](https://github.com/TracksApp/tracks/blob/dbf1cb61ff4409dcf0a0eb3a1222a5d1239ef0e8/app/views/stats/actions_visible_running_time_data.html.erb) 和[`actions_running_time_data.html.erb`](https://github.com/TracksApp/tracks/blob/dbf1cb61ff4409dcf0a0eb3a1222a5d1239ef0e8/app/views/stats/actions_running_time_data.html.erb)——并将所有差异提升一个级别到控制器中。最后，我们将删除其中一个视图。

> 等等，你说把东西拉进控制器是什么意思？控制器不是已经太大了吗？

嗯，是的，它是。

此外，它包含比模板更多的重复。

这是重构时常见的困惑。重构的行为经常会让事情暂时变得更糟。它增加了重复和复杂性。有时感觉就像你把杂乱的代码从一个垃圾抽屉推到另一个。但是随后你突然发现了一个有用的抽象概念，让你可以消除复杂性。

当逻辑展开时，这些抽象很难识别。一旦一切都在控制器中，我们就可以更好地了解我们在处理什么。

## 你背后的一堵墙

就这样，它开始了…

哦等等。并没有。我们需要确保我们有好的测试。

跑`rake test`大概需要 20 分钟。这对重构来说太慢了。我们可以将范围缩小到覆盖图表数据端点的测试。

找出哪些测试牵涉其中的最简单的方法是从我们想要重构的`erb`模板中`raise 'hell'`:

```
<%-
raise "hell"
-%>
```

大约 20 分钟后，我们可以确认三个测试爆炸。所有三个测试都在`StatsController`测试中，可以单独运行:

```
$ ruby -Itest test/controllers/stats_controller_test.rb
```

那需要 5 分 17 秒。速度不快，但也行。

测试有多好？让我们看看如果我们删除两个`erb`文件的全部内容，他们会抱怨什么。

```
24 runs, 126 assertions, 0 failures, 0 errors, 0 skips
```

我们需要更好的测试。

## 对重要的事情的全然不知

`StatsController`中的动作看起来很复杂。如果我们编写的测试做出错误或不完整的假设，我们可能会在重构时引入回归错误。

假设现在正在发生的事情正是 T2 应该发生的事情，这样就不会那么乏味和容易出错。如果我们可以捕获图表端点的完整的`key=value`输出，它可以被复制/粘贴到测试中，并用作断言。

我们甚至不需要了解发生了什么！

这种类型的测试是肮脏的、脆弱的，并且非常非常方便。Michael Feathers 在他的书《有效处理遗留代码中称之为*特性测试*。

表征测试是一种临时措施。您可能永远不会将它提交给源代码控制，它只是让您摆脱困境。有时你可以用更好的测试来代替它。其他时候，比如这些视图，测试只是为了简化视图，然后就可以一起删除了。

## 表征视图

测试必须:

1.  捕获我们调用控制器动作时生成的整个 HTML 响应。
2.  将它与我们之前定义为“好”的响应(即*金主*)进行比较。
3.  如果它们相同，测试通过。否则会失败。

虽然我们可以编写测试，断言响应是“LOLUNICORNS ”,观察它失败，然后从失败消息中复制/粘贴整个响应，但我们将使用 Llewellyn Falco 的[批准测试](http://approvaltests.sourceforge.net/)模式来半自动化这个过程。

事情是这样的:

1.  始终捕捉响应，如`received.html`。
2.  对比一下`approved.html`。
3.  如果`received`与`approved`不同，则失败。

在失败的情况下，通过在浏览器中打开两者或使用 diff 工具来观察差异。如果你喜欢`received`胜过`approved`，手动重命名文件。

这给了我们一个处理第一个金主规则的方法:运行测试，看到它失败(因为`approved`是空的)，将接收到的文件移动到待批准的位置。

瞧，测试现在通过了。

## 设置

我们需要做一些准备。首先，能够完全独立于任何其他测试运行这些测试是件好事，所以在`test/functional/lockdown_test.rb`中为它们创建一个新文件。

```
require File.expand_path(File.dirname(__FILE__) + '/../test_helper')

class LockdownTest < ActionController::TestCase
  # tell Rails which controller we're using
  tests StatsController

  def test_output_does_not_change
    # do some setup
    # call the endpoint
      # write the response to a file
      # compare the two files and make an assertion
  end
end
```

我们实际上需要两个测试，每个端点一个。在这两种情况下，除了调用端点的*步骤之外的所有步骤都完全相同，因此为所有 boiler-plate 创建一个助手方法:*

```
def with_golden_master(key)
  FileUtils.mkdir_p('.lockdown')
  FileUtils.touch(".lockdown/#{key}-approved.txt")

  login_as(:admin_user)

  Timecop.freeze(Time.utc(2014, 6, 5, 4, 3, 2, 1)) do
    yield
  end
  received = @response.body
  File.open(".lockdown/#{key}-received.txt", 'w') do |f|
    f.puts received
  end
  approved = File.read(".lockdown/#{key}-approved.txt")
  unless approved == received
    assert false, approval_message(key)
  end
end

def approval_message(key)
  <<-MSG
  FAIL:
    The output changed. Eyeball the differences with:

        diff .lockdown/#{key}-received.txt .lockdown/#{key}-approved.txt

    If you like the #{key}-received.txt output, then

        cp .lockdown/#{key}-received.txt .lockdown/#{key}-approved.txt
MSG
end
```

fixture 数据是相对于今天的日期生成的，因此您可能需要将 Timecop 值更改为未来一周或两周的时间，以便运行测试。

然后，实际调用控制器操作:

```
def test_visible_running_chart_stays_the_same
  with_golden_master('vrt') do
    get :actions_visible_running_time_data
  end
end

def test_all_running_chart_stays_the_same
  with_golden_master('art') do
    get :actions_running_time_data
  end
end
```

运行测试:

```
$ ruby -Itest test/functional/lockdown_test.rb
```

将`received`文件复制到`approved`文件上，然后重新运行测试，看它是否通过。

这些测试有多好？

那得看数据有多好。Tracks 有 fixtures，我们最终在图表中有不止一个数据点，所以让我们假设它是好的。

## 重构(终于！)

我们一次做一个。下面是*动作运行时间*视图:

```
<%-
url_labels = Array.new(@count){ |i| url_for(:controller => 'stats', :action => 'show_selected_actions_from_chart', :index => i, :id=> "art") }
url_labels[@count]=url_for(:controller => 'stats', :action => 'show_selected_actions_from_chart', :index => @count, :id=> "art_end")
time_labels         = Array.new(@count){ |i| "#{i}-#{i+1}" }
time_labels[0] = "< 1"
time_labels[@count] = "> #{@count}"
-%>
&title=<%=     t('stats.running_time_all') %>,{font-size:16},&
&y_legend=<%=  t('stats.running_time_all_legend.actions') %>,10,0x736AFF&
&y2_legend=<%= t('stats.running_time_all_legend.percentage') %>,10,0xFF0000&
&x_legend=<%=  t('stats.running_time_all_legend.running_time') %>,11,0x736AFF&
&y_ticks=5,10,5&
&filled_bar=50,0x9933CC,0x8010A0&
&values=<%=    @actions_running_time_array.join(",") -%>&
&links=<%=     url_labels.join(",") %>&
&line_2=2,0xFF0000&
&values_2=<%= @cumulative_percent_done.join(",") %>&
&x_labels=<%= time_labels.join(",") %> &
&y_min=0&
<%
  # add one to @max for people who have no actions completed yet.
  # OpenFlashChart cannot handle y_max=0
-%>
&y_max=<%=1+@max_actions+@max_actions/10-%>&
&x_label_style=9,,2,2&
&show_y2=true&
&y2_lines=2&
&y2_min=0&
&y2_max=100&
```

首先，因为这些值需要在视图和控制器之间共享，所以将`url_labels`转换成`@url_labels`，并看到测试通过。对`time_labels`也是如此。

当我们将`@url_labels`移入控制器时，测试失败。

```
# approved
/stats/show_selected_actions_from_chart/avrt?index=0

# received
http://test.host/stats/show_selected_actions_from_chart/avrt?index=0
```

控制器将`http://test.host`添加到生成的 URL 中。我们可以通过向它传递`:only_path => true`选项来强制它只返回路径:

```
url_for(:controller => 'stats', :action => 'show_selected_actions_from_chart', :index => i, :id=> "avrt", :only_path => true)
```

将`time_labels`移入控制器可以顺利工作。

接下来的变化甚至更容易。对于每个插值，确保控制器为最终值设置了一个实例变量:

```
# Normalize all the variable names
@title = t('stats.running_time_all')
@y_legend = t('stats.running_time_legend.actions')
@y2_legend = t('stats.running_time_legend.percentage')
@x_legend = t('stats.running_time_legend.weeks')
@values = @actions_running_time_array.join(",")
@links = @url_labels.join(",")
@values_2 = @cumulative_percent_done.join(",")
@x_labels = @time_labels.join(",")
# add one to @max for people who have no actions completed yet.
# OpenFlashChart cannot handle y_max=0
@y_max = 1+@max_actions+@max_actions/10
```

我们可以对*可见动作运行时间*做同样的改变。既然两个模板完全相同，那么两个端点都可以呈现相同的模板:

```
render :actions_running_time_data, :layout => false
```

删除不用的模板，庆祝一下。

然后拿一把铲子，一些嗅盐，准备攻击那个控制器。然而，这是另一篇文章的主题(即将发布，不要担心。)

## 分享这篇文章