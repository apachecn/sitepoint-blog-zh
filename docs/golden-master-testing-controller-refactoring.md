# 金主测试:控制器重构

> 原文：<https://www.sitepoint.com/golden-master-testing-controller-refactoring/>

![Abstract background of a variety of metal parts](img/a2b6e4c71ac72a4f670bf6e03efd46d8.png)

在[之前的文章](https://www.sitepoint.com/golden-master-testing-refactor-complicated-views/)中，我们使用了 golden master 技术将 HTTP 响应的整个主体转储到一个文件中。这个文件快速验证了我们的更改没有改变系统的行为。

测试就绪后，我们从视图模板中删除了逻辑，并最终删除了整个模板，从两个不同的控制器操作中调用一个模板。这种重构改进了视图层，但是在这个过程中，它增加了更多的逻辑和对一些已经很简单的控制器动作的更多复制。

在本文中，我们将把注意力转向控制器。

## 盘点

我们有两个控制器动作，每个动作有 40 行代码。

这 80 行代码包括查询、计算、简单的数据查找、对助手方法的调用和 18 个实例变量赋值。

振作起来，我们要面对的是:

```
def actions_visible_running_time_data
  @title = t('stats.current_running_time_of_incomplete_visible_actions')
  @actions_running_time = current_user.todos.not_completed.not_hidden.not_deferred_or_blocked.
    select("todos.created_at").
    reorder("todos.created_at DESC")

  @max_weeks = difference_in_weeks(@today, @actions_running_time.last.created_at)
  @actions_running_per_week_array = convert_to_weeks_from_today_array(@actions_running_time, @max_weeks+1, :created_at)

  # cut off chart at 52 weeks = one year
  @count = [52, @max_weeks].min

  # convert to new array to hold max @cut_off elems + 1 for sum of actions after @cut_off
  @actions_running_time_array = cut_off_array_with_sum(@actions_running_per_week_array, @count)
  @max_actions = @actions_running_time_array.max

  # get percentage done cumulative
  @cumulative_percent_done = convert_to_cumulative_array(@actions_running_time_array, @actions_running_time.count )

  @url_labels = Array.new(@count){ |i| url_for(:controller => 'stats', :action => 'show_selected_actions_from_chart', :index => i, :id=> "avrt", :only_path => true) }
  @url_labels[@count]=url_for(:controller => 'stats', :action => 'show_selected_actions_from_chart', :index => @count, :id=> "avrt_end", :only_path => true)

  @time_labels         = Array.new(@count){ |i| "#{i}-#{i+1}" }
  @time_labels[0] = "< 1"
  @time_labels[@count] = "> #{@count}"

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

  render :actions_running_time_data, :layout => false
end

def actions_running_time_data
  @title = t('stats.running_time_all')
  @actions_running_time = current_user.todos.not_completed.select("created_at").reorder("created_at DESC")

  # convert to array and fill in non-existing weeks with 0
  @max_weeks = difference_in_weeks(@today, @actions_running_time.last.created_at)
  @actions_running_per_week_array = convert_to_weeks_from_today_array(@actions_running_time, @max_weeks+1, :created_at)

  # cut off chart at 52 weeks = one year
  @count = [52, @max_weeks].min

  # convert to new array to hold max @cut_off elems + 1 for sum of actions after @cut_off
  @actions_running_time_array = cut_off_array_with_sum(@actions_running_per_week_array, @count)
  @max_actions = @actions_running_time_array.max

  # get percentage done cumulative
  @cumulative_percent_done = convert_to_cumulative_array(@actions_running_time_array, @actions_running_time.count )

  @url_labels = Array.new(@count){ |i| url_for(:controller => 'stats', :action => 'show_selected_actions_from_chart', :index => i, :id=> "art", :only_path => true) }
  @url_labels[@count]=url_for(:controller => 'stats', :action => 'show_selected_actions_from_chart', :index => @count, :id=> "art_end", :only_path => true)

  @time_labels         = Array.new(@count){ |i| "#{i}-#{i+1}" }
  @time_labels[0] = "< 1"
  @time_labels[@count] = "> #{@count}"

  # Normalize all the variable names
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

  render :layout => false
end
```

如果你的眼睛变得呆滞，你想呜咽，爬上床，用手电筒看漫画书，那么，请放心，你感到的痛苦是完全正常的。

然而，如果你鼓起勇气，一行一行地比较这两种方法，你会发现这两种方法几乎完全一样。

*差不多*。

总共有三个区别。

1.  用于填充`@title`的键
2.  将事物存储到`@actions_running_time`中的查询
3.  `url_for`中的`:id`

我真正想要的是这个:

```
def something_something_data
  title = t('some.title.key')
  records = Stats::SomeStuff.for(current_user)
  @something = Stats::Something.new(title, records, "a_key")
  render :something, layout: false
end
```

烂摊子还在，躲在`Stats::Something`里面。我们不会担心使代码*可读*，我们只是要移动它与我们以前使用的金主测试的安全性。

## 试探性的开端

首先，我们需要确保测试通过:

```
ruby -Itest test/functional/lockdown_test.rb
```

如果不是，那是因为*在咕哝着*时区，你应该把`received.html`文件移到`approved.html`文件上面。

接下来，我们将稍微重新排列一下代码，以便在我们的两个方法中将*不同的*与*相同的*分开。

将`url_for`中使用的`:id`提取到一个实例变量中，把它和其他区别贴在方法的顶部。

如果你想的是另一个实例变量？？！？那我为你的直觉鼓掌。我们仍然要这样做，因为这将让我们更容易地移动代码。

再次运行测试，以确保提取变量重构工作正常。

现在我们在两个方法的顶部有 3 行不同之处，后面是 37 行在两个地方完全相同的代码。

注意，最后一行，`render`，是完全合法的 controller-y 代码，我们不会去碰它。

在良好的重构形式中，我们将**复制**，而不是**剪切**所有相同的代码(除了渲染)，并将其粘贴在`app/models/stats/running_time_data.rb`:

```
module Stats
  class RunningTimeData
    def initialize(title, actions_running_time, key)
      @title = title
      @actions_running_time = actions_running_time
      @key = key
    end

    def compute
      # 36 lines of scary code
    end
  end
end
```

控制器仍然有它所有的代码，测试应该通过。

现在在一个控制器动作中实例化`Stats::RunningTimeData`,并对其调用 compute。现在我们不打算使用结果，我们只是想看看代码是否能运行。

```
def actions_running_time_data
  @title = t('stats.running_time_all')
  @actions_running_time = current_user.todos.not_completed.select("created_at").reorder("created_at DESC")
  @key = "art"
  @data = Stats::RunningTimeData.new(@title, @actions_running_time, @key)
  @data.compute
  # 37 more lines of code
end
```

测试失败，并显示以下消息:

```
NoMethodError: undefined method `difference_in_weeks' for #<Stats::RunningTimeData:0x007fd367920d28>
```

## 追踪依赖关系

我们缺少一个辅助方法。我们将不得不把它从控制器复制到我们新的`Stats::RunningTimeData`类中。

当我们这样做时，我们得到一个新的错误消息。

```
NoMethodError: undefined method `difference_in_days' for #<Stats::RunningTimeData:0x007fba3dfedec8>
```

当我们把那个拿过来的时候，测试会抱怨:

```
NoMethodError: undefined method `utc' for nil:NilClass
```

下面是爆炸的代码:

```
# assumes date1 > date2
def difference_in_days(date1, date2)
  return ((date1.utc.at_midnight-date2.utc.at_midnight)/SECONDS_PER_DAY).to_i
end
```

所以，要么`date1`要么`date2`为零。通过跟踪代码，我们发现`date1`是由一个实例变量`@today`表示的，这个变量在我们的新类中没有定义。

控制器已在 before 过滤器中定义了该值。

确保`RunningTimeData`接受该值并将其存储在名为`@today`的实例变量中，然后在控制器中实例化对象时传递该值:

```
@data = Stats::RunningTimeData.new(@title, @actions_running_time, @key, @today)
```

这修复了一个错误信息，并给了我们一个新的。这一次，我们遗漏了常数`Stats::RunningTimeData::SECONDS_PER_DAY`。

将它复制进来，并跟随`NoMethodError`消息的踪迹，直到您将以下所有内容从控制器拖到新类中。

*   `convert_to_weeks_from_today_array`
*   `convert_to_array`
*   `cut_off_array_with_sum`
*   `convert_to_cumulative_array`

请记住，您需要复制。如果您从控制器中删除任何代码，将需要几个小时才能再次通过。

猜猜我是怎么知道的。

最后一个错误是另一个`NoMethodError`，但这不是控制器中定义的方法，而是`url_for`，它是 Rails url 助手之一。

没关系，我们可以包括他们！

```
module Stats
    class RunningTimeData
      include Rails.application.routes.url_helpers
      # …
    end
  end
```

这最终使测试通过。

## 看起来怎么样？

嗯，我们本来打算将 40 行代码隔离到一个新类中，现在我们有 100 行代码。

此外，所有这些代码行仍然存在于控制器中。

事情并没有真正好转。

也就是说，这让我们对问题的规模有了一个概念，控制器代码最终会消失，所以我要称之为胜利。

一场非常小，非常混乱的胜利。

## 回避控制器

控制器有一个新的`Stats::RunningTimeData`对象的实例存储在`@data`中，这意味着视图可以访问它。

在视图的第一行，我们使用@title:

```
&title=<%= @title -%>,{font-size:16},&
```

如果我们在`Stats::RunningTimeData`中添加一个`attr_reader :title`，那么我们可以将视图改为:

```
&title=<%= @data.title -%>,{font-size:16},&
```

当然，现在测试失败了，因为我们有两个使用相同视图的动作，其中一个动作还没有一个`@data`变量。

最简单的方法就是用一些`@data`来设置另一个动作。将这两行复制到另一个操作中，测试应该会再次通过。

现在，一次一个，为视图中需要的值引入一个`attr_reader`，并交换视图以从`@data`对象中读取。

记住对每个变更都要运行测试。

这次*应该是*一帆风顺，但事实并非如此。在第二行，当我们换入`@data.y_legend`时，我们得到一个具有最特殊差异的失败:

```
< &y_legend=&amp;lt;p&gt;stats.running_time_legend.actions&lt;/p&gt;,10,0x736AFF&
---
> &y_legend=Actions,10,0x736AFF&
```

响应体以前说`Actions`，现在说:`<p>stats.running_time_legend.actions</p>`。

这到底是怎么回事？

从`Stats::RunningTimeData#compute`内部调用`pry`,这样我们可以询问方法`t`是在哪里定义的。

```
binding.pry
@y_legend = t('stats.running_time_legend.actions')
```

在 pry 提示符下，键入`t`，它将展开，提供一个方便的堆栈跟踪:

```
[1] pry(#<Stats::RunningTimeData>)> t
ArgumentError: wrong number of arguments (0 for 1)
from /Users/kytrinyx/.gem/ruby/2.1.0/gems/RedCloth-4.2.9/lib/redcloth/erb_extension.rb:16:in `textilize'
```

红布！这不是我所期望的。模型中的`t()`是红布的`textilize()`，而控制器 I18n 的`translate`中的`t()`，两者都有简写`t()`。

我们可以通过在`Stats::RunningTimeData#compute`内部的`I18n`上显式调用`t`来解决这个问题:

```
@y_legend = I18n.t('stats.running_time_legend.actions')
@y2_legend = I18n.t('stats.running_time_legend.percentage')
@x_legend = I18n.t('stats.running_time_legend.weeks')
```

测试再次通过，我们可以回到单调乏味的过程，一个接一个地在`Stats::RunningTimeData`上暴露`attr_reader` s。

```
attr_reader :title, :y_legend, :y2_legend, :x_legend, :values, :links, :values_2, :x_labels, :y_max
```

没有更多的惊喜，我们可以削减控制器动作:

```
def actions_visible_running_time_data
  title = t('stats.current_running_time_of_incomplete_visible_actions')
  actions_running_time = current_user.todos.not_completed.not_hidden.not_deferred_or_blocked.
    select("todos.created_at").
    reorder("todos.created_at DESC")
  @data = Stats::RunningTimeData.new(title, actions_running_time, "avrt", @today)
  @data.compute
  render :actions_running_time_data, :layout => false
end

def actions_running_time_data
  title = t('stats.running_time_all')
  actions_running_time = current_user.todos.not_completed.select("created_at").reorder("created_at DESC")
  @data = Stats::RunningTimeData.new(title, actions_running_time, "art", @today)
  @data.compute
  render :layout => false
end
```

## 结论

这样好点了吗？

控制器和视图现在只共享一个实例变量。这绝对是一个进步。

控制器动作本身减少到 5 行，这比 40 行要好，但仍然不理想。这些查询仍然很难看。我们可以把它们藏在其他东西里。

我们不得不在数据上调用`compute`真烦人。

还有`Stats::RunningTimeData`怎么样？

实际上，很糟糕。它有 100 行长。它没有任何重复，但是它有一个庞大的计算方法和一些不可理解的辅助方法。

换句话说，我们还没有完成。卷起袖子，拿起点心，准备重构那个模型！

## 分享这篇文章