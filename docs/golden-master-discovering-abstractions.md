# 金主:发现抽象

> 原文：<https://www.sitepoint.com/golden-master-discovering-abstractions/>

![Grungy background with a metallic heart and rivets](img/247c51b5cfbc0e0616cbf32afcb2ee6c.png)

一开始是寻求驯服一些不守规矩的视图，结果变成了漫长而艰难的跋涉，经历了控制器、辅助方法、复制，最后是混乱。

目前的情况是，我们有一个模型对象，它封装了以前存在于视图和控制器中的所有逻辑。我们提取了方法并重命名了变量，找到了一些理解的方法。代码不再可怕或令人生畏，但它仍然没有什么值得夸耀的。

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
      Array.new(total_weeks_in_chart) { |i| url(i, key) } << url(total_weeks_in_chart, "#{key}_end")
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
      labels = Array.new(total_weeks_in_chart) { |i| "#{i}-#{i+1}" }
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

## 好的阴影

Steve Freeman 和 Nat pry ce,[Growing Object-Oriented Software Guided by Tests GOOS)](http://www.amazon.com/Growing-Object-Oriented-Software-Guided-Tests/dp/0321503627)的作者谈到了面向对象代码的四个可取特征:

1.  松散耦合
2.  高度内聚
3.  容易组合
4.  上下文无关

`Stats::RunningTimeData`对象在所有四个帐户上都失败了。

### 松散耦合

这段代码依赖于`I18n`库。

```
I18n.t('stats.running_time_legend.percentage')
```

更糟糕的是，如果没有 Rails，你不能实例化它！

```
include Rails.application.routes.url_helpers
```

想象一下，想要在 Sinatra 应用程序或独立的电子邮件应用程序中使用相同的功能，定期发送报告。它在目前的形式下是行不通的，因为你需要拖动 Rails。

### 高度内聚

代码当然不是内聚的。

它有一些方法都是关于描述一个图表的:`x_legend`、`y_legend`、`values`。它还有一堆和图表无关的方法。它们更多的是关于周:`weeks_since(date)`、`total_weeks_of_data`、`datapoints_per_week`。

凝聚力通常与单一责任原则(SRP)相关。这个对象做了太多不同的事情吗？它有很多不同的改变理由吗？

这里好像至少有两件不同的事情:*周统计*和*图表配置*。

### 容易组合

这是一个巨大的物体。你可以用它，也可以不要它，但你不会把它和其他东西结合在一起，变成有用的东西。

### 上下文无关

该类只能在它要处理的两个特定图表的上下文中使用。如果除了显示图表之外，您还想通过电子邮件发送相同数据的纯文本报告，那您就太不走运了。

## 就这样吧

我们需要从`Stats::RunningTimeData`模型中提取的方法有:

*   每周数据点
*   每周百分比
*   每周累积百分比

由于*每周*重复，因此将该部分作为课程的一部分似乎是合理的。

> 但是如果您也想要月度统计数据呢？

是啊，这是个公平的问题。我们可能会。但是现在我们没有，我们也不知道。

让我们假装我们不需要它。后来，如果我们看到重复，那么也许会有一个明显的抽象。目前来说，处理这里实际存在的事情比一些模糊的和假设的未来要容易得多。

## 介绍`WeeklyHistogram`

我们一直非常依赖金主测试。现在是重新开始的时候了:

```
mkdir test/models/stats
touch test/models/stats/weekly_histogram_test.rb
```

创建一个完全独立于上下文、松散耦合的类的最大好处之一是，在运行测试时，您不必等待整个框架加载完毕。

到目前为止，我们不得不为每个测试运行等待 3 秒钟，即使我们已经执行了单个文件。锁定测试会检查数据库、控制器、视图等一切。

三秒钟。这时间足够查推特了。

在新的测试中，我们需要的只是标准库中的`date`、`minitest`和(到目前为止还是假设的)`weekly_histogram`类。

```
require 'date'
require 'minitest/autorun'
require './app/models/stats/weekly_histogram'

class StatsWeeklyHistogramTest < Minitest::Test
end
```

这个过程不到 300 毫秒，感觉几乎是瞬间完成的。

## 实现直方图

本着一厢情愿编程的精神，下面是我想要的 API:

```
histogram.counts
histogram.percentages
histogram.cumulative_percentages
```

我们在前两篇文章中进行的重构揭示了一些关于统计数据的有趣特征:

1.  有一个*截止值*:作为独立统计数据包含的最大周数。
2.  如果截止日期比最早的数据点要早，那么您就不能填写截止日期之前的空周。
3.  如果您有比截止值更多周的数据，那么溢出中的所有内容都将被合并到一个桶中。
4.  如果我们在数据集中间没有给定一周的数据，它将被正确地计为`0`。

我们需要两个测试来覆盖这个:一个测试比截止时间少几周，另一个测试比截止时间多几周。测试应该包括一周的无数据，以确保我们不会意外丢失该行为。

下面是我最后完成的测试套件，涵盖了新对象的整个 API:

```
def test_fewer_weeks_than_cutoff
  today = Date.new(2014, 7, 1)
  cutoff = 10 # weeks
  dates = [
    Date.new(2014, 6, 30),
    Date.new(2014, 6, 27),
    Date.new(2014, 6, 23),
    Date.new(2014, 6, 23),
    Date.new(2014, 6, 22),
    # no data for 3 weeks ago
    Date.new(2014, 6, 9),
  ]
  histogram = Stats::WeeklyHistogram.new(dates, cutoff, today)
  assert_equal [2, 3, 0, 1], histogram.counts
end

def test_more_weeks_than_cutoff
  today = Date.new(2014, 7, 1)
  cutoff = 3 # weeks
  dates = [
    Date.new(2014, 6, 30),
    Date.new(2014, 6, 21),
    Date.new(2014, 6, 20),
    Date.new(2014, 6, 1), # June
    Date.new(2014, 5, 1), # May
    Date.new(2014, 4, 1), # April
    Date.new(2014, 3, 1), # March
  ]
  histogram = Stats::WeeklyHistogram.new(dates, cutoff, today)
  assert_equal [1, 2, 0, 4], histogram.counts
end

def test_percentages
  today = Date.new(2014, 7, 1)
  dates = [
    Date.new(2014, 6, 30),
    Date.new(2014, 6, 19),
    Date.new(2014, 6, 18),
    Date.new(2014, 6, 7),
  ]
  histogram = Stats::WeeklyHistogram.new(dates, 4, today)
  assert_equal [25.0, 50.0, 0.0, 25.0], histogram.percentages
end

def test_cumulative_percentages
  today = Date.new(2014, 7, 1)
  dates = [
    Date.new(2014, 6, 30),
    Date.new(2014, 6, 19),
    Date.new(2014, 6, 18),
    Date.new(2014, 6, 7),
  ]
  histogram = Stats::WeeklyHistogram.new(dates, 4, today)
  assert_equal [25.0, 75.0, 75.0, 100.0], histogram.cumulative_percentages
end
```

我们可以直接从`Stats::RunningTimeData`类中抄袭实现来通过测试。纯粹主义者可能想在这里从头开始重新实现一切。我真的不在乎这种或那种方式，只要结果是可读的，并做它需要做的。

```
module Stats
  class WeeklyHistogram
    attr_reader :dates, :cutoff, :today
    def initialize(dates, cutoff, today=Date.today)
      @dates = dates
      @cutoff = cutoff
      @today = today
    end

    def counts
      frequencies = Array.new(length) {|i|
        datapoints_per_week[i].to_i
      }
      frequencies << datapoints_per_week.inject(:+) - frequencies.inject(:+)
    end

    def percentages
      counts.map(&percentage)
    end

    def cumulative_percentages
      running_total = 0
      percentages.map {|count| running_total += count}
    end

    def length
      [cutoff, total_weeks_of_data].min
    end

    private

    def percentage
      Proc.new {|count| (count * 100.0 / dates.count)}
    end

    def weeks_since(date)
      (today - date).to_i / 7
    end

    def total_weeks_of_data
      weeks_since(dates.last)
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

## 这样更好吗？

是的，确实是。

它是松散耦合的:它所需要的只是要操作的数据集合。

是*高度凝聚*:班里所有人都关心那些日期。

它很容易组合:代码可以用于在独立的电子邮件程序中创建纯文本报告，或者为 Sinatra 应用程序或命令行应用程序生成统计数据。

这与上下文无关:这些日期来自哪里并不重要。当前应用程序中的上下文是 TODOs。具体来说，是未完成的待办事项。但那是附带的。现在我们可以用在完全不同的环境中:纸杯蛋糕销售。僵尸袭击。名人目击。

## 你背后的墙

一次又一次，我们做决定。这是我们的工作，大多数时候我们都是错的。我们无法知道未来。我们的代码库反映了我们的猜测、假设，尤其是我们的错误。

当所有最初的原因都不复存在时，金主测试给了我们一种改变想法的方法。当我们不再记得为什么会做出这样的选择。当我们不再确定代码是如何做的时候。当我们甚至不确定*代码做什么*的时候。

当没有明显的前进道路时，或者当前进实际上可能是倒退、迂回和令人费解时，这是一条出路。

金主技术不是设计、建筑或美的工具。金色让它听起来如此华丽，如此高贵。

它不是。

这是肮脏的，暂时的，完全实用的。

## 分享这篇文章