# 随机 Ruby 之旅

> 原文：<https://www.sitepoint.com/tour-random-ruby/>

[![blue abstract numbers](img/fc79a4fa0f71469d86b84b80335b12dd.png)](https://www.sitepoint.com/wp-content/uploads/2013/09/Fotolia_53649479_Subscription_XL.jpg)

本文涵盖了使用 Ruby 生成随机(通常是伪随机)信息的各种方法。随机信息在很多方面都很有用，特别是在测试、内容生成和安全性方面。我使用了 Ruby 2.0.0，但是 1.9 应该会产生相同的结果。

## 内核#rand 和 Random

在过去，随机范围的数字可能被描述为

```
rand(max - min) + min
```

例如，如果您想生成一个介于 7 和 10 之间的数，您应该写:

```
rand(4) + 7
```

Ruby 通过将一个 Range 对象传递给 Kernel#rand，让您以一种可读性更好的方式做到这一点。

```
>> rand(7..10) 
=> 9
>> rand(1.5..2.8)
=> 1.67699693779624
```

Kernel#srand 为 Kernel#rand 设置种子。这可用于生成可再现的数字序列。如果您试图隔离/重现一个 bug，这可能会很方便。

```
>> srand(333)
>> 10.times.map { rand(10) }
=> [3, 3, 6, 3, 7, 7, 6, 4, 4, 9]
>> 10.times.map { rand(10) }
=> [7, 5, 5, 8, 8, 7, 3, 3, 3, 9]

>> srand(333)
>> 10.times.map { rand(10) }
=> [3, 3, 6, 3, 7, 7, 6, 4, 4, 9]
>> 10.times.map { rand(10) }
=> [7, 5, 5, 8, 8, 7, 3, 3, 3, 9]
```

如果您需要多个生成器，那么您可以通过 Random 访问 Ruby 的 PRNG(伪随机数生成器)的完整接口。

```
>> rng = Random.new
>> rng.rand(10) 
=> 4
```

Random#new 可以接受一个种子值作为参数。如果两个随机对象具有相同的内部状态(它们以相同的种子开始，并且处于同一代)，则#==运算符将返回 true。

```
>> rng1 = Random.new(123)
>> rng2 = Random.new(123)
>> rng1 == rng2
=> true
>> rng1.rand
=> 0.6964691855978616
>> rng1 == rng2
=> false
>> rng2.rand
>> 0.6964691855978616
>> rng1 == rng2
=> true
```

## 随机数组元素

如果你想要一个数组中的随机元素，你可以像这样给数组传递一个随机索引:

```
>> arr = [1, 2, 3, 4, 5]
>> arr[rand(arr.size)]
=> 1
```

没必要这样。从 Ruby 1.9 开始，可以使用 Array#sample。它以前被称为数组#选择。

```
>> [1, 2, 3, 4, 5].sample
=> 4
```

不保证两个连续的#sample 调用是不同的。您可以传递想要#sample 的唯一随机元素的数量。

```
>> [1, 2, 3, 4, 5].sample(2)
=> [4, 1]
```

因为#sample 只适用于 Array，所以对于其他集合，您需要或者用老办法，或者先将它们转换成 Array。

## 实际上是随机数

有时候伪随机数[不够好](http://en.wikipedia.org/wiki/Random_number_generator_attack)。如果它们是基于可预测的东西，它们就可能被攻击者预测和利用。

[RealRand](http://realrand.rubyforge.org/) 是 3 个真正的随机数生成器服务的包装器:

*   [random.org](http://www.random.org/):从大气噪声中产生随机性
*   使用放射性衰变
*   random.hd.org(EntropyPool):声称使用各种来源，包括本地进程/文件/设备、网页点击和远程网站。

**注意**:在撰写本文时，realandr 主页上似乎包含了 1.x 的示例，其中 realandr 的类被分组在 Random 模块下。最新版本的 gem (2.0.0)将类分组到 RealRand 模块下，如以下示例所示。

```
$ gem install realrand

>> require 'random/online'

>> rorg = RealRand::RandomOrg.new
>> rorg.randbyte(5)
=> [197, 4, 205, 175, 84]

>> fourmi = RealRand::FourmiLab.new
>> fourmi.randbyte(5)
=> [10, 152, 184, 66, 190]

>> entropy = RealRand::EntropyPool.new
>> entropy.randbyte(5)
=> [161, 98, 196, 75, 115]
```

对于 RandomOrg 类，您还可以使用#randnum 方法，该方法允许您指定随机数的数量以及范围。

```
>> rorg.randnum(5)
=> [94, 3, 94, 56, 97]

>> rorg.randnum(10, 3, 7)
=> [7, 7, 7, 5, 7, 4, 4, 5, 6, 7]
```

## 随机证券

Ruby 附带了 SecureRandom，用于生成 UUIDs(通用唯一标识符)、会话令牌等。

```
>> require 'securerandom'

>> SecureRandom.hex
=> "e551a47137a554bb08ba36de34659f60"

>> SecureRandom.base64
=> "trwolEFZYO7sFeaI+uWrJg=="

>> SecureRandom.random_bytes
=> "\x10C\x86\x02:\x8C~\xB3\xE0\xEB\xB3\xE7\xD1\x12\xBDw"

>> SecureRandom.random_number
=> 0.7432012014930834
```

“安全”大概取决于你是谁。SecureRandom 使用以下随机数生成器:

*   openssl
*   /dev/天王星
*   Win32

[扫一眼代码](https://github.com/ruby/ruby/blob/trunk/lib/securerandom.rb)就会发现它默认为`OpenSSL::Random#random_bytes`。每当 PID 改变时，PID 和进程时钟时间(纳秒)似乎被用于熵。
我怀疑这对于大多数事情来说已经足够了，但是如果你需要一个额外的保护层，你可以使用 RealRand 来获得额外的熵。不幸的是，SecureRandom 没有任何类似于`#seed`的方法，所以您需要直接播种 OpenSSL。注意:OpenSSL 种子是字符串。

```
>> require 'openssl'
>> require 'random/online'
>> rng = RealRand::RandomOrg.new
>> OpenSSL::Random.random_add(rng.randbyte(256).join, 0.0)
```

[你可以在这里阅读我为什么使用 0.0](http://bugs.ruby-lang.org/issues/6928)。根据补丁讨论，作为`#random_add`的第二个参数的 0.0 是估计熵的量。之前被高估了，所以数字改成了 0.0。然而，根据 [OpenSSL 文档](http://www.openssl.org/docs/crypto/RAND_add.html),`RAND_add`的第二个参数是要混合到 PRNG 状态中的字节数，第三个参数是估计的熵值。`OpenSSL::Random#random_add`只接受 2 个参数(而不是 3 个),但是如果他们第二个参数错了，0 个字节的种子混了进去，那么 SecureRandom 可能在没有修复的情况下对任何重要的东西都没有价值。如果你对此有所了解，请留言评论。

## 基于概率分布的随机数

假设你想要生成随机的、真实的人类质量(比如非帝国的重量)。一个天真的尝试可能是这样的:

```
>> 10.times.map { rand(50..130) }
=> [92, 84, 77, 55, 95, 127, 120, 71, 105, 94]
```

现在，虽然你**可以**找到体重 50 公斤(110 磅)的人，你**可以**找到一些体重 130 公斤(286 磅)的人，但大多数人都没有那么极端，使得上述结果不太可能是完全随机的样本(大多数不是麦当劳匿名会员和职业摔跤手)。

一种选择是忽略极端范围:

```
>> 10.times.map { rand(55..85) }
=> [58, 80, 55, 65, 58, 70, 71, 82, 79, 60]
```

通常得到的数字现在好了一点，但它们仍然不接近现实。您需要一种方法，使大多数随机数落在一个较小的范围内，而较小的百分比则落在一个大得多的范围内。

你需要的是一个概率分布。

唉，Ruby 数学系不强。我遇到的大多数统计解决方案都是复制/粘贴算法、几乎没有文档的无人维护的库/绑定，以及利用像 r 这样的数学环境的黑客。它们还倾向于假设对统计有令人不安的深入了解(好吧，可能需要一个学期，但我仍然不应该回到大学去根据概率分布生成随机数)。

这份文件声称，像体重和身高这样的人类特征是正态分布的。实际上，相当多的东西出现在[正态分布](http://www.mathsisfun.com/data/standard-normal-distribution.html)中。

Rubystats 是我遇到的最简单的库之一。它可以从正态、二项式、贝塔和指数分布中生成随机数。

对于这个例子，我使用了一个平均质量为 68 公斤，标准偏差为 12 公斤的正态分布(只是猜测，不要当作科学)。

```
$ gem install rubystats

>> require 'rubystats'
>> human_mass_generator = Rubystats::NormalDistribution.new(68, 12)
>> human_masses = 50.times.map { human_mass_generator.rng.round(1) }
=> [62.6, 75.4, 62.1, 66.2, 50.9, 58.9, 70.8, 51.4, 60.9, 63.5, 72.0,
    48.2, 62.3, 63.0, 75.3, 62.6, 103.0, 62.3, 46.6, 66.2, 62.7, 92.2, 
    76.1, 85.1, 77.5, 75.9, 57.1, 68.3, 63.8, 53.3, 51.6, 75.4, 61.9, 
    67.7, 58.2, 64.2, 83.3, 69.0, 75.5, 68.8, 60.4, 83.8, 76.2, 81.0, 
    60.9, 61.2, 55.5, 53.1, 61.4, 79.0]
```

对于那些对这些数字不感兴趣的人来说，一公斤相当于 2.2 美元。

```
>> human_weights = human_masses.map { |i| (i * 2.2).round(1) }
=> [137.7, 165.9, 136.6, 145.6, 112.0, 129.6, 155.8, 113.1, 134.0, 139.7,
    158.4, 106.0, 137.1, 138.6, 165.7, 137.7, 226.6, 137.1, 102.5, 145.6, 
    137.9, 202.8, 167.4, 187.2, 170.5, 167.0, 125.6, 150.3, 140.4, 117.3, 
    113.5, 165.9, 136.2, 148.9, 128.0, 141.2, 183.3, 151.8, 166.1, 151.4, 
    132.9, 184.4, 167.6, 178.2, 134.0, 134.6, 122.1, 116.8, 135.1, 173.8]
```

如果这是你的拿手好戏，你可能还想看看 [gsl](http://rb-gsl.rubyforge.org/) 、[分布](https://github.com/clbustos/distribution)和[统计 2](https://github.com/abscondment/statistics2)

## 随机字符串

在 stackoverflow 上有一个[很好的页面，里面有几个生成随机字符串的解决方案。我喜欢这些:](http://stackoverflow.com/questions/88311/how-best-to-generate-a-random-string-in-ruby)

```
>> (0...8).map { (65 + rand(26)).chr }.join
=> "FWCZOUOR"

>> (0...50).map{ ('a'..'z').to_a[rand(26)] }.join
=> ygctkhpzxkbqggvxgmocyhvbocouylzfitujyyvqhzunvgpnqb
```

## 韦伯斯特

Webster 是一个英语/听起来像英语的单词生成器。这对于在西方本地化中生成确认码可能是有用的。

```
$ gem install webster

>> require 'webster'
>> w = Webster.new
>> w.random_word
=> "unavailed"

>> 20.times.map { w.random_word }
=> ["bombo", "stellated", "kitthoge", "starwort", "poleax", "lacinia",
    "crusty", "hazelly", "liber", "servilize", "alternate", "cembalist", 
    "dottore", "ullage", "tusculan", "tattlery", "ironness", "grounder", 
    "augurship", "dupedom"]
```

## 随机词

随机词 gem 声称使用大规模的 [wordnet](http://wordnet.princeton.edu/) 字典作为它的方法。有人指责过你使用“大词”吗？这些都是随机词产生的词。

```
$ gem install random-word

>> require 'random-word'

>> 10.times.map { RandomWord.adjs.next }
=> ["orthographic", "armenian", "nongranular", "ungetatable", 
    "magnified", "azimuthal", "geosynchronous", "platitudinous", 
    "deep_in_thought", "substitutable"]

>> 10.times.map { RandomWord.nouns.next }
=> ["roy_wilkins", "vascular_tissue", "bygone", "vermiform_process",
    "anamnestic_reaction", "engagement", "soda_niter", "humber", 
    "fire_salamander", "pyridoxamine"]

>> 10.times.map { RandomWord.phrases.next }
=> ["introvertive glenoid_cavity", "sugarless reshipment", 
    "anticipant cyclotron", "unheaded ligustrum_amurense", 
    "dauntless contemplativeness", "nativistic chablis", 
    "scapular typhoid_fever", "warlike dead_drop", 
    "pyrotechnic varicocele", "avionic cyanite"]
```

如果您想去掉这些下划线，只需添加一个 gsub:

```
>> 10.times.map { RandomWord.nouns.next.gsub('_', ' ') }
=> ["litterbug", "nebe", "business sector", "stochastic process",
    "playmaker", "esthesia", "granny knot", "purple osier", 
    "sterculia family", "ant cow"]
```

## 骗子

[Faker](http://faker.rubyforge.org/) 用于生成测试数据。它有相当大的数据库，所以你也可以生成程序化的游戏内容。

```
$ gem install faker

>> require 'faker'

>> 20.times.map { Faker::Name.name }
=> ["Gilberto Moen", "Miss Caleb Emard", "Julie Daugherty", 
    "Katelin Rau", "Sheridan Mueller", "Cordell Steuber", 
    "Sherwood Barrows", "Alysson Lind II", "Kareem Toy", 
    "Allison Connelly", "Orin Nolan", "Dolores Kessler", 
    "Kassandra Hackett Jr.", "Mikayla Spencer II", "Lonie Kertzmann", 
    "Emile Walsh V", "Tara Emmerich", "Mrs. Beryl Keeling", 
    "Jerry Nolan DVM", "Linnie Thompson"]

>> 10.times.map { Faker::Internet.email }
=> ["catherine.schamberger@toy.net", "eleonore@heaney.net",
    "toni@colliermoore.org", "merl_miller@pfeffer.net",
    "florine_dach@gusikowski.net", "bernadine@walter.net",
    "stevie.farrell@crooks.net", "janick@satterfield.name",
    "leanna.lubowitz@bogisich.biz", "rey@kutch.info"]

>> 10.times.map { Faker::Address.street_address } 
=> ["3102 Jasen Haven", "8748 Huel Parks", "1886 Gutkowski Creek", 
    "837 Jennie Spurs", "4921 Carter Coves", "7714 Ida Falls", 
    "8227 Sawayn Bypass", "269 Kristopher Village", "31185 Santos Inlet", 
    "96861 Heaney Street"]

>> 10.times.map { Faker::Company.bs }
=> ["aggregate extensible markets", "repurpose leading-edge metrics",
    "synergize global channels", "whiteboard virtual platforms", 
    "orchestrate ubiquitous relationships", "enable interactive e-services", 
    "engineer end-to-end convergence", "deploy enterprise e-services", 
    "benchmark wireless solutions", "generate impactful eyeballs"]
```

印象深刻吗？Faker 还提供了多个地区的数据。例如，也许你正在制作一个发生在德国的游戏，你想要一个德国种类的随机角色名。

```
>> Faker::Config.locale = :de
>> 10.times.map { Faker::Name.name }
=> ["Mara Koehl", "Penelope Wagner", "Karolina Kohlmann", "Melek Straub",
    "Marvin Kettenis", "Lyn Behr", "Karina Deckert", "Janne Damaske", 
    "Sienna Freimuth", "Lias Buder"]
```

或者你可能想要西班牙语的公司流行语。

```
>> Faker::Config.locale = :es
>> 5.times.map { Faker::Company.catch_phrase }
=> ["adaptador interactiva Extendido", "lÃ­nea segura tangible Distribuido",
    "superestructura asÃ­ncrona Diverso", "flexibilidad bidireccional Total",
    "productividad acompasada Re-implementado"]
```

当然，也有 Lorem Ipsum 的东西。

```
>> Faker::Lorem.paragraph
=> "Sit excepturi et possimus et. Quam consequatur placeat fugit aut et
    sint. Sint assumenda repudiandae veniam iusto tenetur consequatur."
```

确保你检查了文档，看看它还能做什么。此外，如果这真的是你的东西，看看 Faker 的功能前身，[伪造](https://github.com/sevenwire/forgery)。它已经不再使用，但似乎很容易适应。

## 随机数据

Faker 的一个缺点是，它似乎不提供特定性别的名称生成。 [random_data](https://github.com/tomharris/random_data) gem 可以，尽管它可能需要一些工作(从版本 1.6.0 开始)。

```
$ gem install random_data

>> require 'random_data'
>> 20.times.map { Random.first_name_female }
=> ["Donna", "Sharon", "Anna", "Nancy", "Betty", "Margaret", "Maria",
    "Helena", "Carol", "Cheryl", "Donna", "Cheryl", "Sharon", "Jennifer", 
    "Helena", "Cheryl", "Jessica", "Elizabeth", "Elizabeth", "Sandra"]

>> 20.times.map { Random.first_name_male }
=> ["Richard", "William", "Arthur", "David", "Roger", "Daniel", "Simon",
    "Anthony", "Adam", "George", "George", "David", "Christopher", 
    "Steven", "Edgar", "Arthur", "Richard", "Kenneth", "Philip", "Charles"]
```

看着这些名字，他们有点…好吧，就说没有“神尼魁”

公平地说，它确实有一些非常酷的日期和位置方法。`Random#date`似乎选择当前日期附近的日期。

```
>> 10.times.map { Random.date.strftime('%a %d %b %Y') }
=> ["Mon 16 Sep 2013", "Sat 21 Sep 2013", "Tue 24 Sep 2013", 
    "Sat 28 Sep 2013", "Thu 03 Oct 2013", "Fri 20 Sep 2013", 
    "Mon 23 Sep 2013", "Tue 24 Sep 2013", "Sun 29 Sep 2013", 
    "Thu 03 Oct 2013"]

>> 30.times.map { Random.zipcode }
=> ["33845", "87791", "27961", "94156", "40897", "24887", "51985", "12099",
    "82247", "33015", "77437", "93497", "35269", "94426", "58919", "50170", 
    "99952", "62229", "73271", "34316", "17547", "24590", "99613", "52954", 
    "95117", "38454", "70195", "84415", "97096", "58282"]

>> 30.times.map { Random.country }
=> ["Fiji", "Sudan", "Cambodia", "Belgium", "Rwanda", "Czech Republic",
    "Marshall Islands", "Georgia", "Saudi Arabia", 
    "United Arab Emirates", "Switzerland", "Uganda", "Uruguay", "Somalia", 
    "Ukraine", "Canada", "Jamaica", "Cape Verde", "Indonesia", "Sudan", 
    "Malaysia", "Virgin Islands (U.S.)", "Turkmenistan", "Libya", "Sweden", 
    "St. Vincent and the Grenadines", "Korea, Dem. Rep.", "Faeroe Islands", 
    "Myanmar", "Zimbabwe"]
```

注意:根据 random_data github 页面，“邮政编码完全是随机的，可能不是真实的邮政编码。”

## 七巧板

这个 [raingrams](https://github.com/postmodern/raingrams) 宝石可能是本教程中最有趣的东西。它可以根据提供的文本生成随机的句子或段落。例如，如果你是某种病态、堕落的 YouTube 评论鉴赏家，你可以创建一个怪物，产生几乎无限的 YouTube 评论，在你前进的过程中用最差的评论重新训练模型，刮去荒谬的深度，直到你得到类似这样的东西:

“不，每一次与民主党人的对话都像奈尔·德葛拉司·泰森基本上是任天堂的卡尔·萨根黑色版当我在你这个年龄的时候，我认为贪婪的公司是这样工作的这个评论已经被删除，因为这个视频与这个妈妈每天赚 30 美元无关填写理查德·道金斯调查仍然是一个比暮光之城更好的爱情故事。“

根据[维基百科](http://en.wikipedia.org/wiki/Ngram)，“n 元语法是来自给定文本或语音序列的 n 个项目的连续序列。根据应用，项目可以是音素、音节、字母、单词或碱基对。”

Raingrams 将自己描述为“用 Ruby 编写的灵活且通用的 ngrams 库”。它通过基于成对、三元组等出现的文本构建模型来生成文本内容——您可以使用的模型的复杂性似乎没有限制，但是包括的模型类从 BigramModel 到 classica model。

```
$ gem install raingrams
```

创建和训练模型很容易。

```
require 'raingrams'
model = Raingrams::BigramModel.new
model.train_with_text "When you are courting a nice girl an hour seems like a second. When you sit on a red-hot cinder for a second that seems like an hour. That's relativity."

model.random_sentence
=> "When you sit on a nice girl an hour."
```

如果包含 Raingrams 模块，则不需要将其用作名称空间。

```
include Raingrams
model = BigramModel.new
```

Raingrams 的一个非常好的地方是可以用文件或网页来训练它，而不仅仅是字符串。Raingrams 提供以下培训方法:

*   `Model#train_with_paragraph`
*   `Model#train_with_text`
*   `Model#train_with_file`
*   `Model#train_with_url`

我惊喜地发现`#train_with_url`很好用……相当不错！它并不完美，它可以创建被截断的句子，但编写一个过滤器来丢弃破碎的句子可能比为您想要用来训练模型的每个站点编写一个刮刀更容易。

二元模型可以处理非常小的数据集，但是它们倾向于产生不一致的结果。

```
>> require 'raingrams'
>> include Raingrams
>> model = BigramModel.new
>> model.train_with_url "http://en.wikipedia.org/wiki/Central_processing_unit"
>> model.random_sentence
=> "One notable late CPU decodes instructions rather than others before him
    such as pipelining and 1960s no arguments but still continued by eight 
    binary CPU register may not see 4."
```

几乎令人信服的连贯性似乎始于四元图。不幸的是，为了产生“随机”文本，四元图需要相当多的数据。

```
>> model = QuadgramModel.new
>> model.train_with_url "http://en.wikipedia.org/wiki/Central_processing_unit"
>> model.random_sentence
=> "Tube computers like EDVAC tended to average eight hours between failures
    whereas relay computers like the slower but earlier Harvard Mark I which was
    completed before EDVAC also utilized a stored-program design using punched 
    paper tape rather than electronic memory."
```

如果你想创建一个[“H . P . love craft-sounding”](https://www.sitepoint.com/seeking-lovecraft-part-1-an-introduction-to-nlp-and-the-treat-gem/)散文生成器，你可以根据他的故事训练 n-grams 模型。

```
>> model = QuadgramModel.new
>> model.train_with_url "http://www.dagonbytes.com/thelibrary/lovecraft/mountainsofmaddness.htm"
>> model.random_sentence
=> "Halfway uphill toward our goal we paused for a momentary breathing 
    spell and turned to look again at poor Gedney and were standing in a 
    kind of mute bewilderment when the sounds finally reached our 
    consciousness the first sounds we had heard since coming on the camp 
    horror but other things were equally perplexing."

>> model.random_sentence
=> "First the world s other extremity put an end to any of the monstrous
    sight was indescribable for some fiendish violation of known natural 
    law seemed certain at the outset."
```

“world s”中丢失的撇号不是打印错误，它在原文中是存在的。你需要留意类似的东西。

## 结论

谈到随机数据，Ruby 有很多优点。更重要的是，许多这样的库很容易修改或改进。如果你是 Ruby 的新手，你想参与进来，这是一个很好的机会。

## 分享这篇文章