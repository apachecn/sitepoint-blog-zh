# Ruby 和 OpenCalais:将数据转化为信息

> 原文：<https://www.sitepoint.com/ruby-opencalais-transform-data-information/>

![calais_logo](img/be3d36749e9d0108bfdb112f7e896029.png)

几周前，我们看到了 OpenCalais 语义网服务如何帮助一位崭露头角的年轻记者 Connor 理解他的数据并减少他的工作量。在这样做的时候，Connor 仅仅浏览了 OpenCalais 提供的巨大分析潜力的表面。本周，我们将探索该服务提供的一些更复杂的功能。请坐稳第 2 部分:数据检测日！

## 安装

我们将再次使用 [DoverToCalais](https://rubygems.org/gems/dover_to_calais) gem，一个包装 OpenCalais API 的包装器，在它的袖子上有一些技巧。你现在应该知道该怎么做了:

```
$ gem install dover_to_calais
```

您应该还记得，上次我们使用 DoverToCalais 时，它使用的是简单的 XML 格式，这足以满足我们的标记需求。DoverToCalais 还可以与 OpenCalais rich [JSON 输出格式](http://www.opencalais.com/documentation/calais-web-service-api/interpreting-api-response/opencalais-json-output-format)一起使用，这为我们提供了新的数据财富，主要是实体间的关系。

当处理 rich JSON 格式的数据时，DoverToCalais 使用 [Redis](http://redis.io) 来存储它所处理的数据的数据模型。按照这里[的指示](http://redis.io/topics/quickstart)让 Redis 启动并运行。

默认情况下，DoverToCalais 将使用本地 Redis 实例 127.0.0.1，端口 6379，数据库#6(无密码)。如果这些被证明是不方便的，可以通过修改常量 *DoverToCalais::REDIS* 很容易地改变。

同样，不要忘记 DoverToCalais 需要一个工作的 JRE 来正常运行。

*注意:*丰富的数据分析功能仅在 DoverToCalais v0.2.1 及以上版本中提供，因此请确保您拥有最新版本！

## 新猫咪是什么？

正如汤姆·琼斯所说，当使用 *rich* 输出时，DoverToCalais 用法的主要区别是不再需要在回调( *#to_calais* 方法)中进行响应分析。回调现在只是让我们知道响应何时被处理。一旦回调返回，我们知道我们可以在 Redis 中找到所有建模良好的源数据，我们可以在外部独立于我们的 event machine*create->analyze->callback*循环访问它。

唯一不同的是，我们需要向我们的 *#analyze_this* 方法传递一个 *:rich* 符号。DoverToCalais 会做剩下的。

## 模型

不，不是经典的发电厂乐团曲目，傻瓜！这是 DoverToCalais 数据模型，许多 [Ohm](https://github.com/soveran/ohm) 模型对象存在于 Redis 数据存储中。一旦 DoverToCalais 处理了一个数据源，就会生成这些类型的对象。

![Alt text](img/04c4ef62bcadaf406a22e6331f28b0a8.png "data model ER diagram")

*DoverToCalais::entity model*具有以下属性

*   名称–实体名称，如克拉克·肯特、千禧体育场等。
*   类型–实体类型，如人员、位置等。
*   Calais _ id–由 OpenCalais 分配的唯一 id
*   关系–与实体相关的一组通用关系
*   事件–与实体相关的一组事件

*DoverToCalais::EntityModel::relation model*具有以下属性

*   主体——执行操作的实体
*   动词——一个动作
*   对象——接收操作的实体
*   检测–捕捉关系本质的文本
*   Calais _ id–由 OpenCalais 分配的唯一 id

*DoverToCalais::EntityModel::event model*具有以下属性

*   Calais _ id–由 OpenCalais 分配的唯一 id
*   info*Hash–动态创建的散列，包含事件的属性和值。由于属性的数量取决于事件的类型(例如，军事动作与电影释放的属性非常不同)，info* 散列是动态封装事件属性的好方法。

### 康纳的复出

还记得[康纳](https://www.sitepoint.com/ruby-opencalais-semantically-tag-anything/)吗？嗯，由于他在上次任务中表现出色，他在办公室的名声变成了“数据分析先生”！他的同事黛比是一名调查记者，她带着一个问题来找他:

“有传言说，奥尔德伍德住房管理局在财务上有一些违规行为，不知怎么的，市长也牵涉其中。但是我没有任何线索，所以不知道从哪里开始。见鬼，我甚至不知道奥德伍德的市长是谁！你有数据和技能，你能帮忙吗？”

“当然可以！”讽刺康纳和鞭打出他的信任的编辑。“首先要做的是对数据进行分析。我怀疑我们将需要*丰富的*分析能力！”

```
1  require 'dover_to_calais'
2  require 'em/throttled_queue'
3
4  DoverToCalais::flushdb
5   
6  EM.run do
7    # use Control + C to stop the EM
8    Signal.trap('INT')  { EventMachine.stop }
9    Signal.trap('TERM') { EventMachine.stop }
10  
11   DoverToCalais::API_KEY =  'my-opencalais-api-key'
12   data_dir = '/home/connor/data/Alderwood_News/'
13       
14   total_files = Dir[File.join(data_dir, '**', '*')].count { |file| File.file?(file) }
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
26   count = 0
27        
28   dovers.each do |dover|
29     dover.to_calais do |response|
30       if response.error
31         puts "*** Data source #{dover.data_src} error: #{response}"
32       else
33        count += 1
34        puts "finished #{count}"
35        puts "all done!" if (count >= total_files)
36       
37       end #if
38     end #block
39        
40     # because we told the queue to pop a maximum of two dovers per second
41     # we're not exceeding the OpenCalais limit so we'll get no errors
42     dovers.length.times { queue.pop  { |dover| dover.analyze_this(:rich)} }
43   end #each    
44 end
```

“这看起来就像你第一次做的那样，”黛比说。“差不多”，康纳回答。“现在唯一的区别是，我得到了要处理的文件总量(第 14 行),然后每当分析完一个文件，我就增加一个计数器(第 33 行)。当我的计数器达到总数时，我知道所有的东西都被处理了，所以 DoverToCalais 创建了它的数据模型，我可以开始挖掘数据了。哦，看看我是如何把一个参数传递给 *analyze_this* 的！”

“我明白了，”黛比点点头，“但是 4 号线发生了什么事？”“哦，那是，”康纳说，“我其实没必要做的事。只是每次你用 DoverToCalais 丰富地分析某个东西时，它都会被添加到同一个数据存储中。我喜欢从头开始，所以我告诉 DoverToCalais 清空它的数据存储，仅此而已。”

“现在让我们看看所有这些数据中都有什么！”

### 数据侦探黛比

Connor 创建了一个新文件:

```
1  require 'dover_to_calais'
2 
3  Ohm.redis = Redis.new(DoverToCalais::REDIS)
```

“从现在开始，我们的工作没有什么特别的了”，康纳说。“我们只是操纵标准的数组、散列和对象。首先，让我们找出谁是市长。”

```
4  all_events = DoverToCalais::EntityModel::EventModel.all.to_a
5  mayors = all_events.select {|v| /[Mm]ayor/.match(v.info_hash['position'].to_s)
6    
7  mayors.each do |event|
8    puts event.info_hash
9  end
```

“哇”，当代码运行时，黛比叫道。“太多的市长和太多的城镇！我们能再过滤一点吗？”“当然”，康纳回答道:

```
5  mayors = all_events.select {|v| /[Mm]ayor/.match(v.info_hash['position'].to_s) &&
6                            /[Aa]lderwood/.match(v.info_hash['city'].to_s) && 
7                            /current/.match(v.info_hash['status'].to_s) }
8    
9  mayors.each do |event|
10    puts event.info_hash
11 end
```

![Alt text](img/f1367a5a45f20b0372c979e7fa1f6a2c.png "Mayor records for Rex Luthor")

“那更好”，黛比大声说道。“我想我们可以有把握地假设雷克斯·卢瑟是奥德伍德市长。看看我们能从雷克斯身上找到什么。我能开车吗？”说完，黛比抓起键盘输入:

```
12  a_set = DoverToCalais::EntityModel.find(name: "Rex Luthor")
13  if a_set.size == 1
14    rex = a_set.first
15    rex.relations.each do |r|
16      puts "#{r.subject['name']} -- #{r.verb} -- #{r.object['name']} --    #{r.detection}"
17    end
18  else
19    puts "oh-oh, more than one Rex Luthors!"
20  end
```

![Alt text](img/ca29df2dfae4d4b787c2b25d026647d6.png "Rex Luthor relations")

“看这最后一行”黛比叫道。"看来市长力促这个叫冈萨雷斯的家伙去房管局任职。"“你说得对，”康纳说，“但首先让我美化一下格式，这会伤到我的眼睛”。康纳迅速安装了[列表](https://github.com/junegunn/tabularize)宝石，在文件顶部添加了一个`require 'tabularize'`，然后修改了代码。

```
12  a_set = DoverToCalais::EntityModel.find(name: "Rex Luthor")
13   if a_set.size == 1
14    rex = a_set.first
15
16    table = Tabularize.new
17    table << %w[Subject Verb Object Detection]
18    table.separator!
19
20    rex.relations.each do |r|
21         table << ["#{r.subject['name']}", 
22           "#{r.verb}",  
23          "#{r.object['name']}", 
24          "#{r.detection}" ]
25    end
26    puts table
27  else
28    puts "oh-oh, more than one Rex Luthors!"
29  end
```

![Alt text](img/1c4835abe84486d1367220d350758246.png "Rex Luthor relations formatted")

黛比说:“这看起来可以打印了，我还找到了我丢失的一环，连接市长和房管局的线索。我敢肯定，如果我用同样的技术来调查这个家伙，我会得出一吨关于他的信息！谢谢你，康纳，今晚我请你吃饭"

### 结论

康纳帮助了一位同事，同时也为自己争取到了一顿免费的晚餐。更重要的是，他展示了如何使用适当的工具将无意义的、非结构化的数据转化为合理的、可操作的信息。OpenCalais 和 DoverToCalais 是正在进行的工作，一直在改进。再加上 Ruby 及其生态系统的强大和灵活性，它们为数据挖掘和分析提供了一个很好的工具集。

## 分享这篇文章