# Ruby & OpenCalais:语义标记任何东西

> 原文：<https://www.sitepoint.com/ruby-opencalais-semantically-tag-anything/>

![calais_logo](img/be3d36749e9d0108bfdb112f7e896029.png)

## 语义什么？？

如果你一直想知道关于语义网的所有大惊小怪是关于什么的，不要害怕——你不是唯一的一个！语义网不仅是一个时髦的术语，还有许多实际应用。在本教程中，我们将使用我们最喜欢的编程语言中的语义分析 Web 服务来探索其中的一些。

## OpenCalais 网络服务

简而言之，引用 [OpenCalais](https://www.opencalais.com/about) 创造者的话:

> OpenCalais 网络服务自动为您提交的内容创建丰富的语义元数据——不到一秒钟。使用自然语言处理(NLP)，机器学习和其他方法，Calais 分析你的文档并找到其中的实体。但是，Calais 远远超出了传统的实体识别，还返回隐藏在文本中的事实和事件。

OpenCalais 简单 XML 响应格式(我们将在本教程中使用)返回三种标签:[实体、事件](https://www.opencalais.com/documentation/calais-web-service-api/api-metadata/entity-index-and-definitions)和[主题](https://www.opencalais.com/documentation/calais-web-service-api/api-metadata/document-categorization)。 ***实体*** 是静态的“物”，如人物、地点等，以某种身份参与到文本语境中。OpenCalais 为每个实体分配一个*相关性*分数，以表明它在数据源一般主题的上下文中的相关性。 ***事件*** 是关于一个或多个实体的事实或动作。 ***主题*** 是数据源上下文的表征或一般描述。

我们可以使用这些元数据及其属性从数据中提取相关信息，或者从中得出有用的结论，我们将在接下来的几节中看到。

## 在我们开始之前…

*   要使用 OpenCalais Web 服务，您需要获得一个 OpenCalais API 密钥，这个密钥可以很容易地从 [OpenCalais 网站](https://www.opencalais.com/APIkey)获得。

*   为了通过我们的 Ruby 代码与 OpenCalais 交互，我们将使用 [DoverToCalais](https://rubygems.org/gems/dover_to_calais) gem。DoverToCalais 很好地包装了 OpenCalais API，并提供了一些有用的功能，如回调、多数据源格式和结果过滤。要使用 gem，只需将这一行添加到应用程序的 gem 文件中:

    ```
    gem 'dover_to_calais'
    ```

    然后执行:

    ```
    $ bundle
    ```

    或者自己安装为:

    ```
    $ gem install dover_to_calais
    ```

此外，请记住 DoverToCalais 需要有一个工作的 JRE 。

## 我们开始吧

我们将从分析一个 URL 开始，看看我们能得到什么。由于 DoverToCalais 使用 [EventMachine](https://rubyeventmachine.com/) 的能力，我们的代码必须放在 EM *reactor* 循环中:

```
1    require 'dover_to_calais'
2    
3    EM.run do
4    
5        # use Control + C to stop the EM
6        Signal.trap('INT')  { EventMachine.stop }
7        Signal.trap('TERM') { EventMachine.stop }
8    
9        # we need an API key to use OpenCalais
10       DoverToCalais::API_KEY =  'my-opencalais-api-key'
11       # create a new dover
12       dover =  DoverToCalais::Dover.new('https://www.bbc.co.uk/news/world-africa-24412315')
13       # parse the text and send it to OpenCalais
14       dover.analyse_this
15       puts 'do something....'
16       # set a callback for when we receive a response
17       dover.to_calais { |response| puts response.error ? response.error : response }
18    
19       puts 'do something else....'
20    
21    end
```

上面的内容非常简单:我们将数据源包装在一个 *Dover* 对象中(第 12 行)，设置一个回调来处理响应数据(第 17 行)，并且不要忘记发送我们的 Dover 进行标记(第 14 行)。这将产生以下结果:

![Alt text](img/eb7e5a44d5a20fb43c4d97506525f46a.png "Basic usage")

如您所见，OpenCalais 响应基本上是一大堆 XML。幸运的是，DoverToCalais 允许我们轻松地解析和过滤这个 XML，我们将在本教程中进一步看到。

还要记住，尽管我们将 URL 传递给了 DoverToCalais 构造函数，但是 gem 也可以处理大多数文件格式。我们可以很容易地将路径传递给任何(嗯，几乎任何)类型的文件，并对其内容进行标记。

为了更深入地了解 DoverToCalais 的实际用法，我们必须谈谈 Connor。

## 康纳的难题

康纳是一家北部报纸的崭露头角的记者。他的老板刚刚要求他写一篇一页纸的文章，介绍去年发生在(虚构的)奥尔德伍德镇的所有重大体育新闻。她给他发了一个网络文件夹的链接，里面包含了《奥尔德伍德公报》去年发表的每篇文章的扫描件(由 2006 年巨大挑战提供)。“在那里你会找到你需要的东西”，她说。"明天早上第一件事就是把你的那页纸放在我桌上！"。说完，她离开了办公室。康纳被留下来考虑他的选择:

1.  阅读每一篇文章，筛选出包含体育新闻的文章。不幸的是，可怜的康纳没有那么多时间，也没有这项任务所需的佛教僧侣般的精神专注。

2.  使用类似 grep 的工具在每篇文章中搜索与体育相关的关键词。起初，这似乎是一个好主意。然而，康纳很快意识到这毕竟不是一个实际的想法。他打算寻找什么运动？篮球，足球，棒球，谁知道奥尔德伍德的好居民热衷于什么？更具异国情调的运动呢，比如相扑或裸体约德尔？他怎么可能知道去年奥尔德伍德公报中提到的所有可能的体育相关活动，以便他可以搜索它们？！更不用说，当一篇不相关的文章提到一项运动时，他会得到错误的肯定。不，他想，grep 也不是答案。

幸运的是，康纳认识鲁比，也读过多弗托莱的书。突然，康纳的未来看起来一片光明！

## 理解大数据

康纳开始编写代码:

```
1   require 'dover_to_calais'
2   EM.run do
3    
4     # use Control + C to stop the EM
5     Signal.trap('INT')  { EventMachine.stop }
6     Signal.trap('TERM') { EventMachine.stop }
7    
8     DoverToCalais::API_KEY =  'my-opencalais-api-key'
9     data_dir = '/home/connor/data/Alderwood_News/'
10       
11    dovers = []
12    Dir.foreach(data_dir) do |filename|
13      next if filename == '.' or filename == '..'
14      dover = DoverToCalais::Dover.new(data_dir + filename) 
15      dovers << dover
16    end
17        
18    ##what now?
19  end
```

到目前为止，一切顺利。Connor 创建了一个他所有数据文件的列表，作为 Dover 对象。接下来，他必须分析这些物体。他将`##what now?`(第 18 行)注释替换为:

```
18  dovers.each do |dover|
19    dover.to_calais do |response|
20      if response.error
21        puts "*** Data source #{dover.data_src} error: #{response}"
22      else
23        topics = response.filter({:entity => 'Topic'})
24        puts "Data file: #{dover.data_src} is about #{topics.map{|x| x.value}.join(",")}"
25            
26      end #if
27    end #block
28        
29    dover.analyze_this
30  end #each
```

对于每个 dover 对象(即每篇新闻文章)，Connor 已经设置了一个回调(第 19 行),指定当他获得有效响应时要做什么，即提取“主题”实体，以便他可以看到文章的内容。然后，他将对象发送到 OpenCalais 进行标记(第 29 行)。

还要注意，为了只显示每个主题的名称，Connor 将每个主题的*值*属性映射并连接到一个字符串中。聪明的康纳！

现在他已经准备好运行代码了。但是，当他这样做的时候，他的脸变得苍白…一些错误信息开始在他的屏幕上弹出！

![Alt text](img/c3f6ef6835ddffe43e4a801553720cbf.png "403 Developer Over Qps error")

Connor 不应该惊慌，原因很简单:OpenCalais 将每个用户的并发请求数限制为每秒最多 4 个。当 Connor 调用`dover.analyze_this`时，DoverToCalais 向 OpenCalais 发出 HTTP 请求。由于这个请求在`dovers`数组的每次迭代中出现一次，这意味着许多请求同时被发送到 OpenCalais。难怪 OpenCalais 会抱怨！

康纳如何解决这个问题？

## 手放在油门上

Connor 可能想通过在`dover.analyze_this`前添加一个`sleep`语句来解决这个问题。然而，这是行不通的。Connor 忘记了他的代码是在 EventMachine 线程中运行的——原因超出了本教程的范围，并且与 EM 线程模型有关——使用`sleep`将是一个糟糕的主意！

这就是名副其实的 [EM 节流队列](https://rubygems.org/gems/em-throttled_queue)发挥作用的地方。节流队列允许我们控制从队列中取出项目的速度。

康纳修改了代码，如下所示:

```
1  require 'dover_to_calais'
2  require 'em/throttled_queue'
3  EM.run do
5    # use Control + C to stop the EM
6    Signal.trap('INT')  { EventMachine.stop }
7    Signal.trap('TERM') { EventMachine.stop }
8  
9    DoverToCalais::API_KEY =  'my-opencalais-api-key'
10   data_dir = '/home/connor/data/Alderwood_News/'
11       
12   # allow up to 2 dovers to be de-queued in a second
13   # that should keep us well within the OpenCalais 
14   # concurrency limit
15   queue = EM::ThrottledQueue.new(2, 1)
16
17   dovers = []
18   Dir.foreach(data_dir) do |filename|
19     next if filename == '.' or filename == '..'
20     dover = DoverToCalais::Dover.new(data_dir + filename) 
21     dovers << dover
22     # push the dover on our throttled queue as well
23     queue.push(dover)
24   end
25        
26   dovers.each do |dover|
27     dover.to_calais do |response|
28       puts "----------------------------------------"
29       if response.error
30         puts "*** Data source #{dover.data_src} error: #{response}"
31       else
32         topics = response.filter({:entity => 'Topic'})
33         puts "Data file: #{dover.data_src} is about #{topics.map{|x| x.value}.join(",")}"
34       
35       end #if
36     end #block
37        
38     # because we told the queue to pop a maximum of two dovers per second
39     # we're not exceeding the OpenCalais limit so we'll get no errors
40     dovers.length.times { queue.pop  { |dover| dover.analyze_this } }
41   end #each    
42 end
```

![Alt text](img/549bdd56d82c78a461c38e104a72029b.png "Throttled queue")

康纳满脸笑容。在很短的时间内，他的代码用主题标签标记了所有的文章，使他能够快速知道每篇文章是关于什么的。现在，他可以轻松地选择体育相关的文章进行进一步阅读，节省了几个小时的努力和担心。他可以只浏览相关的文章，然后及时为明天的版本写出他的文章。

## 收场白

在本教程中，我们看到了如何使用 OpenCalais 服务来标记和总结大量内容。然而，我们只是触及了该服务提供的丰富语义元数据的表面，以及我们如何使用它进行更精细的数据分析、挖掘和智能搜索。

## 分享这篇文章