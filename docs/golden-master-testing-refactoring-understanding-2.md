# 金主测试:更多重构，更多理解

> 原文：<https://www.sitepoint.com/golden-master-testing-refactoring-understanding-2/>

![Robot of the metal parts on a dark grungy background](img/393851eb879943043b865526111bcec2.png)

在经历了乏味的视图视图和 T2 控制器之后，我们终于进入了重构的冒险阶段:抓住救命稻草。事后批评一切。痴迷地运行测试。培养微妙的希望，即我们新发现的理解确实是洞察力，而不仅仅是另一个失败的假设。

在[上一篇文章](https://www.sitepoint.com/golden-master-testing-refactoring-understanding/)中，我们剖析了`compute`方法的第一行，小心谨慎地追溯了那一行。我们发现图表与日期有关，在过去的一年中，很可能以某种周为基础。

接下来，我们将解开代码，将原始数据处理成图表使用的正确格式。

## 从前

```
module Stats
  class RunningTimeData
    include Rails.application.routes.url_helpers

    attr_reader :title, :dates, :key, :today,
                :y_legend, :y2_legend, :x_legend, :values,
                :links, :values_2, :x_labels, :y_max

    def initialize(title, timestamps, key, now)
      @title = title
      @dates = timestamps.map(&:to_date)
      @key = key
      @today = now.to_date
    end

    def compute
      @actions_running_per_week_array = convert_to_weeks_from_today_array

      # cut off chart at 52 weeks = one year
      @count = [52, total_weeks_of_data].min

      # convert to new array to hold max @cut_off elems + 1 for sum of actions after @cut_off
      @actions_running_time_array = cut_off_array_with_sum(@actions_running_per_week_array, @count)
      @max_actions = @actions_running_time_array.max

      # get percentage done cumulative
      @cumulative_percent_done = convert_to_cumulative_array(@actions_running_time_array, dates.count )

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

    def total_weeks_of_data
      weeks_since(dates.last)
    end

    def weeks_since(date)
      days_since(date) / 7
    end

    def days_since(date)
      (today - date).to_i
    end

    def convert_to_weeks_from_today_array
      convert_to_array(dates, total_weeks_of_data+1) {|date|
        [weeks_since(date)]
      }
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

因此，故事从一个名为`@actions_running_per_week_array`的变量开始。

*动作运行*是我们之前见过的，可以翻译成*未完成的 TODOs* 。

问题是，我们不在乎他们是托多。我们从 TODOs 中提取了`created_at`时间戳，将它们转换成日期，这就是图表感兴趣的全部内容。简单数据点。

似乎也没有必要将类型名编码到名称中。

```
def datapoints_per_week
  convert_to_weeks_from_today_array(dates, total_weeks_of_data+1)
end
```

`convert_to_weeks_from_today_array`接受两个参数，这两个参数在整个类中都可用。两者都没必要过。去掉它们会给我们留下一个虚假的抽象概念:

```
def datapoints_per_week
  convert_to_weeks_from_today_array
end
```

内联该方法会产生一种模糊的似曾相识的感觉。

```
def datapoints_per_week
  convert_to_array(dates, total_weeks_of_data + 1) {|date|
    [weeks_since(date)]
  }
end
```

整个方法体是对另一个方法的调用，该方法接受对整个类都可用的参数。

内嵌它！

退后一步，想想我们刚刚内联的方法。事情是这样开始的:

```
def datapoints_per_week
  convert_to_weeks_from_today_array
end

def convert_to_weeks_from_today_array
  convert_to_array
end

def convert_to_array
  # several lines of complicated logic
end
```

如果一个方法定义包含一行，而这一行只是另一个方法调用，那么这两个方法名最好是值得的。

将上面的方法与下面的方法进行对比，我们在前面的重构过程中定义了这个方法。

```
def total_weeks_of_data
  weeks_since(dates.last)
end
```

在后一个例子中，两个名字都有意义。它们处于不同的抽象层次，它们一起讲述了一个好故事。

## 发现数据点

让我们用我们的`test_stuff`测试戳一下`datapoints_per_week`，弄清楚数据是如何构造的。

```
def test_stuff
  now = Time.utc(2014, 1, 2, 3, 4, 5)
  timestamps = [
    now - 5.minutes,
    now - 1.day,
    now - 7.days,
    now - 8.days,
    now - 8.days,
    now - 30.days,
  ]
  stats = Stats::RunningTimeData.new("title", timestamps, "key", now)
  assert_equal "something", stats.datapoints_per_week
end
```

这里的数据传播从几分钟前到一个月前。我特别感兴趣的是在没有任何数据的情况下计算出一周内会发生什么。

失败看起来是这样的:

```
Expected: "something"
  Actual: [2, 3, 0, 1]
```

数组的索引表示几周前的数据，值是那一周发生的数据点数。

下面是数据点数组的产生方式:

```
def datapoints_per_week
  a = Array.new(total_weeks_of_data+1, 0)
  dates.each {|date|
    [weeks_since(date)].each {|i|
      a[i] += 1 if a[i]
    }
  }
  a
end
```

孩子们，护住眼睛，我们有嵌套迭代！

内部循环迭代一个元素的数组。这似乎有点没有意义。我们只需要一个索引:

```
dates.each {|date|
  i = weeks_since(date)
  a[i] += 1 if a[i]
}
```

没有办法让`weeks_since`返回`nil`或`false`，所以我们可以删除后缀`if`。说到这里，让我们把`a`改名为`frequencies`。

```
def datapoints_per_week
  frequencies = Array.new(total_weeks_of_data + 1, 0)
  dates.each {|date|
    frequencies[weeks_since(date)] += 1
  }
  frequencies
end
```

数据点已经按周分组。接下来，我们似乎要截断它。

```
# cut off chart at 52 weeks = one year
@count = [52, total_weeks_of_data].min
```

注释表明我们只想显示某个日期的数据。这有点道理，但是您可能想知道为什么 SQL 查询没有简单地根据截止日期限制数据。

也许还有更多。

如果我们看看`@count`是如何使用的，它会变得更加令人困惑。

```
# convert to new array to hold max @cut_off elems + 1
# for sum of actions after @cut_off
@actions_running_time_array = cut_off_array_with_sum(datapoints_per_week, @count)
```

假设`@cut_off`实际上指的是`@count`，这个评论和前面的有矛盾。

要么是在一年内切断数据，要么是将截止日期以外的日期汇总为一个条目。我不明白怎么会两者兼而有之。

我们需要了解一下到底发生了什么。

```
def test_stuff
  chart = Stats::RunningTimeData.new("title", [], "key", Time.now)
  per_week = [1, 0, 3, 4, 5]
  assert_equal [], chart.cut_off_array_with_sum(per_week, 1)
  assert_equal [], chart.cut_off_array_with_sum(per_week, 2)
  assert_equal [], chart.cut_off_array_with_sum(per_week, 3)
  assert_equal [], chart.cut_off_array_with_sum(per_week, 4)
  assert_equal [], chart.cut_off_array_with_sum(per_week, 5)
  assert_equal [], chart.cut_off_array_with_sum(per_week, 6)
end
```

一次一个，失败填补空白。

```
# datapoints_per_week = [1, 0, 3, 4, 5]
[1, 12] # cut off at 1
[1, 0, 12] # cut off at 2
[1, 0, 3, 9] # cut off at 3
[1, 0, 3, 4, 5] # cut off at 4
[1, 0, 3, 4, 5, 0] # cut off at 5
[1, 0, 3, 4, 5, 0, 0] # cut off at 6
```

cutoff 方法不丢弃数据，它将所有溢出批处理到单个槽中。此外，它还用 0 填充所需时间段内的任何空槽。

似乎是一个过于通用的名字。它真正告诉我们的是，无论原始数据中有多少周的数据，图表中将显示多少周的数据。

将`count`改名为`total_weeks_in_chart`，将`actions_running_time_array`改名为`datapoints_per_week_in_chart`讲述了一个稍微更连贯的故事。这很冗长，但是在我们梳理完所有的概念之前，很难挑选一个更好的名字。

```
def datapoints_per_week_in_chart
  cut_off_array_with_sum(datapoints_per_week, total_weeks_in_chart)
end
```

请注意熟悉的模式:参数是全局可用的，方法定义由对另一个方法的单个调用组成。

内联`cut_off_array_with_sum`给了我们:

```
def datapoints_per_week_in_chart
  a = Array.new(total_weeks_in_chart + 1) { |i| datapoints_per_week[i]||0 }
  a[total_weeks_in_chart] += datapoints_per_week.inject(:+) - a.inject(:+)
  a
end
```

这看起来有点可怕，但可以简化一些:

```
def datapoints_per_week_in_chart
  frequencies = Array.new(total_weeks_in_chart) {|i|
    datapoints_per_week[i].to_i
  }
  frequencies << datapoints_per_week.inject(:+) - frequencies.inject(:+)
end
```

接下来，代码引入了一个全新的术语:`cumulative_percent_done`。

我真的不确定*完成*是我们要找的词。我们在处理*未完成的*待办事项，而不是*完成的*待办事项。而且即便如此，**我们也不在乎**。

我们把它改名为`cumulative_percentages`:

```
def cumulative_percentages
  convert_to_cumulative_array(datapoints_per_week, dates.count)
end
```

像往常一样，我们可以内联包含的方法:

```
def cumulative_percentages
  a = Array.new(datapoints_per_week_in_chart.size) {|i|
    datapoints_per_week_in_chart[i]*100.0/dates.count
  }
  1.upto(timestamp_counts.size-1) {|i| a[i] += a[i-1]}
  a
end
```

这看起来不仅仅是有点吓人。它做了很多。

给概念命名会很有帮助，所以让我们提取一些方法。

第一部分是按周创建百分比数组。

```
def percentages_per_week
  Array.new(datapoints_per_week_in_chart.size) {|i|
    datapoints_per_week_in_chart[i]*100.0/dates.count
  }
end
```

想想这是在做什么。

1.  它正在创建一个与现有数组长度相同的新数组，并且
2.  它从原始数组中的相应值派生出数组中的每个值。

另一个说法是*地图*。

```
def percentages_per_week
  datapoints_per_week_in_chart.map {|count|
    count * 100.0 / dates.count
  }
end
```

这个方法也做了很多。命名该块会使它变得非常清晰。

```
def percentages_per_week
  datapoints_per_week_in_chart.map(&percentage)
end

def percentage
  Proc.new {|count| count * 100.0 / dates.count}
end
```

完全重构的`cumulative_percentages`逻辑如下所示:

```
def cumulative_percentages
  running_total = 0
  percentages_per_week.map {|percentage|
    running_total += percentage
  }
end
```

这远没有刚才看起来那么可怕。

## 清除

其余的`compute`可以通过简单的提取被炸掉。生成的代码如下所示。

它甚至比原著还要长，但是那些原本隐藏在奇怪抽象层次的神秘名字后面的概念，现在已经浮出了水面。名字对吗？大概不会。这不是一个创建良好代码排列的重构，而是一个发现存在什么的重构。

内嵌，内嵌，提取，内嵌，提取，提取。

当 codebase 还没有找到正确的抽象时，这就是重构的节奏。

这种重构的结果是理解，而不是好的代码。

为此，我们将进行最后一次测试。我们会问这个问题*如果这是两件事，它们会是什么？如果进行这种重构，一个微小的、内聚的抽象将会出现。*

* * *

```
module Stats
  class RunningTimeData
    include Rails.application.routes.url_helpers

    attr_reader :title, :dates, :key, :today

    def initialize(title, timestamps, key, now)
      @title = title
      @dates = timestamps.map(&:to_date)
      @key = key
      @today = now.to_date
    end

    def compute
      # FIXME: delete call from controllers
    end

    def y_legend
      I18n.t('stats.running_time_legend.actions')
    end

    def y2_legend
      I18n.t('stats.running_time_legend.percentage')
    end

    def x_legend
      I18n.t('stats.running_time_legend.weeks')
    end

    def values
      datapoints_per_week_in_chart.join(",")
    end

    def links
      url_labels.join(",")
    end

    def values_2
      cumulative_percentages.join(",")
    end

    def x_labels
      time_labels.join(",")
    end

    def y_max
      # add one to @max for people who have no actions completed yet.
      # OpenFlashChart cannot handle y_max=0
      1 + datapoints_per_week_in_chart.max + datapoints_per_week_in_chart.max/10
    end

    private

    def url_labels
      urls = Array.new(total_weeks_in_chart) {|i|
        url(i, key)
      }
      urls << url(total_weeks_in_chart, "#{key}_end")
    end

    def url(index, id)
      options = {
        :controller => 'stats',
        :action => 'show_selected_actions_from_chart',
        :index => index,
        :id=> id,
        :only_path => true
      }
      url_for(options)
    end

    def time_labels
      labels = Array.new(total_weeks_in_chart) {|i|
        "#{i}-#{i+1}"
      }
      labels[0] = "< 1"
      labels[total_weeks_in_chart] = "> #{total_weeks_in_chart}"
      labels
    end

    def total_weeks_in_chart
      [52, total_weeks_of_data].min
    end

    def total_weeks_of_data
      weeks_since(dates.last)
    end

    def weeks_since(date)
      (today - date).to_i / 7
    end

    def cumulative_percentages
      running_total = 0
      percentages_per_week.map {|count| running_total += count}
    end

    def percentages_per_week
      datapoints_per_week_in_chart.map(&percentage)
    end

    def percentage
      Proc.new {|count| (count * 100.0 / dates.count)}
    end

    def datapoints_per_week_in_chart
      frequencies = Array.new(total_weeks_in_chart) {|i|
        datapoints_per_week[i].to_i
      }
      frequencies << datapoints_per_week.inject(:+) - frequencies.inject(:+)
    end

    def datapoints_per_week
      frequencies = Array.new(total_weeks_of_data + 1, 0)
      dates.each {|date|
        frequencies[weeks_since(date)] += 1
      }
      frequencies
    end
  end
end
```

## 分享这篇文章