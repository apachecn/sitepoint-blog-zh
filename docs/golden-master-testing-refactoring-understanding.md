# 金主测试:为理解而重构

> 原文：<https://www.sitepoint.com/golden-master-testing-refactoring-understanding/>

![Robot of the metal parts on a dark grungy background](img/393851eb879943043b865526111bcec2.png)

有时，人体会从事一种看似毫无意义的活动，称为“徒劳的循环”，其中*两条代谢途径同时向相反的方向运行，除了以热量的形式耗散能量* ( [维基百科](http://en.wikipedia.org/wiki/Futile_cycle))之外，没有任何整体效果。

前两篇文章中执行的重构看起来很像徒劳的循环。我们有将近 100 行混乱的代码，但是做了很多工作却没有明显的整体效果。

事务的当前状态可以在模型层中观察到，或多或少地被隔离在它属于模型层的地方:

```
module Stats
  class RunningTimeData
    include Rails.application.routes.url_helpers

    SECONDS_PER_DAY = 86400;

    attr_reader :title, :actions_running_time, :key, :today,
                :y_legend, :y2_legend, :x_legend, :values,
                :values_2, :links, :x_labels, :y_max

    def initialize(title, actions_running_time, key, today)
      @title = title
      @actions_running_time = actions_running_time
      @key = key
      @today = today
    end

    def compute
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

      @url_labels = Array.new(@count){ |i| url_for(:controller => 'stats', :action => 'show_selected_actions_from_chart', :index => i, :id=> @key, :only_path => true) }
      @url_labels[@count]=url_for(:controller => 'stats', :action => 'show_selected_actions_from_chart', :index => @count, :id=> "#{@key}_end", :only_path => true)

      @time_labels         = Array.new(@count){ |i| "#{i}-#{i+1}" }
      @time_labels[0] = "< 1"
      @time_labels[@count] = "> #{@count}"

      # Normalize all the variable names
      @y_legend = I18n.t('stats.running_time_legend.actions')
      @y2_legend = I18n.t('stats.running_time_legend.percentage')
      @x_legend = I18n.t('stats.running_time_legend.weeks')
      @values = @actions_running_time_array.join(",")
      @links = @url_labels.join(",")
      @values_2 = @cumulative_percent_done.join(",")
      @x_labels = @time_labels.join(",")
      # add one to @max for people who have no actions completed yet.
      # OpenFlashChart cannot handle y_max=0
      @y_max = 1+@max_actions+@max_actions/10
    end

    # assumes date1 > date2
    def difference_in_weeks(date1, date2)
      return difference_in_days(date1, date2) / 7
    end

    # assumes date1 > date2
    def difference_in_days(date1, date2)
      return ((date1.utc.at_midnight-date2.utc.at_midnight)/SECONDS_PER_DAY).to_i
    end

    def convert_to_weeks_from_today_array(records, array_size, date_method_on_todo)
      return convert_to_array(records, array_size) { |r| [difference_in_weeks(@today, r.send(date_method_on_todo))]}
    end

    # uses the supplied block to determine array of indexes in hash
    # the block should return an array of indexes each is added to the hash and summed
    def convert_to_array(records, upper_bound)
      a = Array.new(upper_bound, 0)
      records.each { |r| (yield r).each { |i| a[i] += 1 if a[i] } }
      a
    end

    # returns a new array containing all elems of array up to cut_off and
    # adds the sum of the rest of array to the last elem
    def cut_off_array_with_sum(array, cut_off)
      # +1 to hold sum of rest
      a = Array.new(cut_off+1){|i| array[i]||0}
      # add rest of array to last elem
      a[cut_off] += array.inject(:+) - a.inject(:+)
      return a
    end

    def convert_to_cumulative_array(array, max)
      # calculate fractions
      a = Array.new(array.size){|i| array[i]*100.0/max}
      # make cumulative
      1.upto(array.size-1){ |i| a[i] += a[i-1] }
      return a
    end
  end
end
```

很吓人，不是吗？

将代码从一个地方转移到另一个地方并不困难。随着[金主测试](https://www.sitepoint.com/golden-master-testing-refactor-complicated-views/)断言*一切都好，什么都没有改变*，它甚至不是特别可怕，只是乏味。但是我们不能再简单地铲了。唯一的方法是将代码分开。

要做到这一点，我们需要理解它，我们将通过梳理它来理解它。

以下是初步计划:给东西重新命名，问很多问题。

*这是什么？它代表了什么？数据结构看起来像什么？如何使用？我们为什么关心？为什么天空是蓝色的？我们到了吗？*

## 从头开始

问题的实质始于`compute`。这是第一行:

```
@max_weeks = difference_in_weeks(@today, @actions_running_time.last.created_at)
```

我唯一可以自信地说我理解的是`@today`。

这之前有一个注释，*转换为数组并用 0* 填充不存在的周，但这到底是什么意思呢？将*什么*转换成一个数组？一排*什么*？

嗯，当然是那个`@actions_running_time`。不管那是什么？

原来`@actions_running_time`是未完成的 TODOs 的集合。我们可以将其命名为`@unfinished_todos`，但不清楚在这种情况下这是否有意义。

## 剔除不必要的上下文

通过跟踪代码可以发现，我们只将`created_at`发送给 TODOs。

TODOs 是这个领域的核心。`Todo`模型有 420 行长，它有大量的数据和大量的关系。然而在这里，我们并不关心这些。对于这段代码来说，TODOs 未完成并不重要。

老实说，他们是不是 TODOs 可能都不重要。

换句话说，`unfinished_todos`不是我们要找的名字，因为在这种情况下，*未完成的*和*要做的*都不是有意义的区别。我们只关心时间戳。

理想情况下，我们甚至不应该将活动记录对象传入`Stats::RunningTimeData`。

控制器可以在将数据发送到`Stats::RunningTimeData`之前执行`map(&:created_at)`。

做出改变很简单——没有陷阱，没有惊喜。

几件好事发生了。我们可以忘记未完成的待办事项的复杂性。上下文更少，更难理解，Stats 对象变得更容易理解。这也使得命名集合变得容易。*什么事？*T0！

附带的好处是，我们现在可以轻松地构造简单的自动化测试，直接测试 Stats 对象的各个部分。

```
def test_stuff
  today = Time.utc(2014, 1, 2, 3, 4, 5)
  timestamps = [
    # ...
  ]
  stats = Stats::RunningTimeData.new("title", timestamps, "key", today)
  # assert_equal "whatever", stats.something
end
```

这不是单元测试。这更像是短暂的特征测试，其唯一目的是探索一些输出。

## (再)从头开始

我还是很好奇`@max_weeks`是关于什么的。将它提取到一个方法中允许我们用一个测试来测试它，看看返回值是什么。

尝试几个不同的值显示出`max_weeks`是对我们有多少周未完成的待办事项的计数。或者，如果你愿意，它是最老的未完成的待办事项有多少周了。

更好的名字可能是`total_weeks_of_data`。

```
def total_weeks_of_data
  difference_in_weeks(today, timestamps.last)
end
```

事后看来，这个名字似乎不言自明，但这个方法本身就让我觉得很有趣。

```
# assumes date1 > date2
def difference_in_weeks(date1, date2)
  return difference_in_days(date1, date2) / 7
end
```

`date1`值对应于`today`，`date2`是最老的时间戳。这些都是时间戳，但它们是以日期命名的。

一些明智的重命名给我们留下了`now`而不是`today`，以及下面的方法定义:

```
# assumes timestamp1 > timestamp2
def difference_in_weeks(timestamp1, timestamp2)
  difference_in_days(timestamp1, timestamp2) / 7
end
```

但是还有更多。

作为参数传递的`timestamp1`，是*总是* `now`，可供全班使用。没有理由把它当作论据来传递。

```
def difference_in_weeks(timestamp)
  difference_in_days(now, timestamp) / 7
end
```

这越来越奇怪了。一个论点的分歧是没有意义的。这是一只手拍手的声音。

更好的名字应该是`weeks_since(timestamp)`。

似乎我们可以对`difference_in_days()`做类似的改变。

```
# assumes date1 > date2
def difference_in_days(date1, date2)
  return ((date1.utc.at_midnight-date2.utc.at_midnight)/SECONDS_PER_DAY).to_i
end
```

哼。`at_midnight`。这是奇怪的递归。变量被命名为日期，但它们实际上是时间戳，但方法*真正关心的是日期。*

在 Ruby 中，`date + 1`中的 1 值一天，而`timestamp + 1`中的 1 值一秒。

强制使用`initialize`中的值，并再次将其重命名为*！*摆脱了所有的争分夺秒，极大地简化了方法:

```
def days_since(date)
  (today - date).to_i
end
```

`days_since`方法只在一个地方使用，所以我们也可以内联它:

```
def weeks_since(date)
  (today - date).to_i / 7
end
```

作为奖励，`SECONDS_PER_DAY`只是变得多余。

## 我们到了吗？

嗯，没有。我们甚至还没有看到`compute`方法的第 2 行，但是已经有了显著的改进。

我们从这里开始:

```
class RunningTimeData
  # ...
  SECONDS_PER_DAY = 86400;

  def compute
    @max_weeks = difference_in_weeks(@today, @actions_running_time.last.created_at)
    # 35 more lines
  end

  # assumes date1 > date2
  def difference_in_weeks(date1, date2)
    return difference_in_days(date1, date2) / 7
  end

  # assumes date1 > date2
  def difference_in_days(date1, date2)
    return ((date1.utc.at_midnight-date2.utc.at_midnight)/SECONDS_PER_DAY).to_i
  end
  # ...
end
```

沿着兔子洞一路走下去，我们最终找到了:

```
class RunningTimeData
  # ...
  def compute
    # 35 lines
  end

  def total_weeks_of_data
    weeks_since(dates.last)
  end

  def weeks_since(date)
    (today - date).to_i / 7
  end
  # ...
end
```

重构技术*移除参数*和*内联方法*有助于消除混乱，让概念浮现出来。使用*提取方法*、*重命名方法*和*重命名变量*的重构将公开的概念编码到代码库本身中。(注意:你可以在这里找到重构技术的目录[。)](http://refactoring.com/catalog/)

我们从一个名为*动作运行时间*的浑主意开始。我们简化了代码，出现了*时间戳*的概念。一旦`Time`和`Date`的迷雾散去，中心概念就是简单的*日期*。

这种简化使我们能够轻松地创建短暂的表征测试，这是一个强大的工具，用于探索被复杂性掩盖的意义。

对于整个`Stats::RunningTimeData`到底是怎么回事，仍然没有真正的概念。为此，我们将需要遵循更多的兔子路线，这将导致发现更多的概念。请继续关注下一期，在这一期，清晰度将会突飞猛进。

## 分享这篇文章