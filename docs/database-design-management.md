# 数据库设计和管理的概念

> 原文：<https://www.sitepoint.com/database-design-management/>

找到创建数据库的在线教程并不困难。不难找到如何创建查询的教程。事实上，不难找到关于 SQL 语法、数据类型差异，甚至哪个数据库最适合什么用途的教程。然而，很难找到关于设计、开发和维护高质量数据库的理论的好教程。

数据库设计和管理并不困难。比我们聪明得多的人设计了一些非常有序和合理的规则来遵循，并将这些规则发展成了所谓的**规范化过程**。

使用这一过程，您可以创建全新的、功能齐全的、微调过的数据库，或者使用当前的数据库表，通过这些步骤运行它们，并得到运行良好的表。无论您如何使用这些步骤，它们都是高质量数据库设计的基础。

##### 函数相关

在我们进入正常化过程之前，我应该后退一步，澄清一些事情。首先，这并不特定于任何一种类型的数据库。这些是在使用任何数据库系统时都应该遵循的规则，无论是 Oracle、MySQL、PostgreSQL、SQL Server 等。

让我们首先讨论函数依赖，这对于理解规范化过程是至关重要的。这仅仅是一个相对简单的概念的一个大术语。为了说明这一点，让我们看一个小样本表。

| **销售代表编号** | **工资等级** | **利率** |
| 001 | one | .05 |
| 002 | one | .05 |
| 003 | one | .05 |
| 004 | Two | .07 |
| 005 | one | .05 |
| 006 | three | .09 |

这个相对简单的表格是函数依赖的一个很好的例子，它也可以用来说明一个观点。

定义:如果一个值“A”在任一时刻确定了“B”的单个值，则该列在功能上依赖于另一列。

听起来很困惑？让我解释一下。“费率”字段在功能上依赖于“工资等级”字段。换句话说，工资等级决定工资率。

要确定函数依赖，您可以这样想:给定字段 A 的一个值，您能确定 B 的单个值吗？如果 B 依赖于 A，则称 A 在函数上决定 B。

采用与上面相同的表，让我们添加到它。

| **名称** | **销售代表编号** | **工资等级** | **利率** |
| 病房 | 001 | one | .05 |
| 格言 | 002 | one | .05 |
| 手杖 | 003 | one | .05 |
| 比彻姆 | 004 | Two | .07 |
| 科林斯 | 005 | one | .05 |
| 罐头工厂 | 006 | three | .09 |

现在，让我们看看这个表，找到更多的函数依赖关系。我们已经知道，工资等级决定工资率。我们也可以说销售代表编号决定姓氏。每个姓氏只有一个销售代表编号。这符合函数依赖的定义。

但是姓氏在功能上决定了什么吗？乍一看，有些人可能会说是的，然而，这不是真的。目前，你可以说沃德只会给你一个销售代表号码，但是，如果我们用沃德这个名字雇用另一个人呢？那么您的销售代表编号将有两个值，然后姓氏在功能上不再决定任何事情。

##### 一键

既然我们知道了什么是函数依赖，我们就可以阐明键了。现在，如果您正在数据库中工作，您可能已经知道什么是主键。但是，你能定义它们吗？

**定义:列 A 是表 T 的主键，如果:**
属性 1。T 中的所有列在功能上都依赖于属性 2。表 T 中没有列的子集合也具有属性 1。

这很有道理。如果数据库中的所有字段都依赖于且仅依赖于一个字段，那么该字段就是键。现在，偶尔属性 2 被破坏，并且两个字段是主键的候选。这些键称为候选键。从这些候选键中，选择一个键，其他的称为备用键。

例如，在与之前相同的表中:

| **名称** | **销售代表编号** | **工资等级** | **利率** |
| 病房 | 001 | one | .05 |
| 格言 | 002 | one | .05 |
| 手杖 | 003 | one | .05 |
| 比彻姆 | 004 | Two | .07 |
| 科林斯 | 005 | one | .05 |
| 罐头工厂 | 006 | three | .09 |

我们的主键是销售代表号，因为它符合主键的定义。表中的所有内容都依赖于销售代表编号，其他任何内容都不能声称相同。现在，让我们更进一步，假设我们在表中也有雇员的社会保险号。

| **名称** | **销售代表编号** | **工资等级** | **利率** | **Soc 秒号** |
| 病房 | 001 | one | .05 | 133-45-6789 |
| 格言 | 002 | one | .05 | 122-46-6889 |
| 手杖 | 003 | one | .05 | 123-45-6999 |
| 比彻姆 | 004 | Two | .07 | 113-75-6889 |
| 科林斯 | 005 | one | .05 | 121-44-6789 |
| 罐头工厂 | 006 | three | .09 | 111-45-9339 |

现在，我们有两个候选键，销售代表号和社会保险号。因此，我们必须决定使用哪个字段，因为两者都是唯一的。最后，出于各种原因，最好将销售代表编号作为主键。

##### 第一范式

既然我们已经澄清了这些概念，让我们进入规范化过程。第一范式被定义为不包含重复组的表。

例如:

| **订单编号** | **订单日期** | **零件号** | **订购数量** |
| Five thousand two hundred and forty-five | 3/02/99 | Thirty-five thousand four hundred and thirty-six | Twenty-three |
| Five thousand two hundred and forty-six | 3/02/99 | Thirty-two thousand one hundred and sixteen | Eleven |
| Five thousand two hundred and forty-seven | 4/02/99 | Thirty-two thousand one hundred and thirty-three | fifty-two |
| – | – | Twelve thousand three hundred and eleven | Ten |
| Five thousand two hundred and forty-eight | 4/02/99 | Forty-six thousand five hundred and sixty-three | one |

现在，从这个例子中，你应该很容易发现问题。首先，在这个设置中，Order Number 被认为是主键，但这并不完全正确。真正的主键是订单号和零件号。在每个订单中，一个零件只能订购一次(尽管订购数量可以大于 1)。但是，可以在一个订单编号中订购多个零件。所以，当我们重组这张表时，我们得到了这个:

| **订单编号** | **订单日期** | **零件号** | **订购数量** |
| Five thousand two hundred and forty-five | 3/02/99 | Thirty-five thousand four hundred and thirty-six | Twenty-three |
| Five thousand two hundred and forty-six | 3/02/99 | Thirty-two thousand one hundred and sixteen | Eleven |
| Five thousand two hundred and forty-seven | 4/02/99 | Thirty-two thousand one hundred and thirty-three | fifty-two |
| Five thousand two hundred and forty-seven | 4/02/99 | Twelve thousand three hundred and eleven | Ten |
| Five thousand two hundred and forty-eight | 4/02/99 | Forty-six thousand five hundred and sixty-three | one |

这更容易理解，是一个比前一个表更好的设计。这一次，主键既是订单号又是零件号，表中的所有内容都依赖于该键。

这是第一个范式，或 1NF。大多数人可以正确地做到这一点，然而，我已经看到一些设计时没有考虑到这一点的表格，有一段时间，看起来一切都很好，但当你开始用大量信息填充表格时，它会变得非常繁琐。当然，这不是一个人职业生涯中的闪光点，但确实是最有价值的一课。

##### 第二范式

首先，让我们这么说。如果表的主键只包含一列，则该表自动为 2NF。那很容易，不是吗？但是，如果您的主键有多列，请继续阅读。

让我们直接跳到这里的表格。主键是订单号(订单号)和零件号(零件号)。

| **订单编号** | **订单日期** | **零件号** | **部分 Desc。** | **订购数量** | **价格** |
| Five thousand two hundred and forty-five | 3/02/99 | Thirty-five thousand four hundred and sixty-five | 气体键 | one | $10 |
| Five thousand two hundred and forty-six | 3/02/99 | Sixty-five thousand four hundred and sixty-six | 草坪躺椅 | four | $25 |
| Five thousand two hundred and forty-six | 3/02/99 | Sixty-five thousand four hundred and seventy-three | 野餐桌 | one | $30 |
| Five thousand two hundred and forty-seven | 3/02/99 | Forty-four thousand six hundred and fifty-four | 草坪飞镖 | one | $45 |
| Five thousand two hundred and forty-eight | 4/02/99 | Forty-four thousand six hundred and sixty-five | 排球 | three | $20 |

乍一看，这张桌子一切正常。然而，有一些问题需要解决。让我们首先规划出功能依赖关系。

```
Order Number -> Order Date

Part Number -> Part Description

Order Number, Part Number -> Number Ordered, Price, Order Date, Part Description
```

正如你所看到的，这是一个大混乱，需要得到处理。事实上，这张桌子还有其他问题。

首先，更改此表中的部件描述也意味着更改其他表，如产品表。这显然很难编码，也很难运行。

第二，你很可能对同一个零件有不同的描述。这在商业环境中是不可接受的。

这是因为零件描述仅取决于主键的一部分，即零件号。因此，零件描述是非关键属性。非键属性只是一个不属于主键的列。

这与 2NF 定义特别相关，它是一个在 1NF 中的表，没有非键属性仅依赖于主键的一部分。这当然重申了这样一个事实:如果表的主键中只包含一列，那么它就是第二范式。

那么，我们如何解决这个问题呢？好了，上面这个问题我们已经解决了。下面是上一个表的三个新表，第一部分是主键，第二部分是表中的其他字段。

```
Order Number -> Order Date

Part Number -> Part Description

Order Number, Part Number -> Number Ordered, Price
```

那些是我们的新桌子。注意，我去掉了订单日期和零件描述，因为不需要使用已经存储在其他表中的数据。还要注意，每个零件只有一个描述。高质量的数据库设计不需要冗余数据。

##### 第三范式

那么，你认为你的桌子现在是干净的。你认为它为大联盟做好准备了吗？不…还没有。3NF 已经准备好接受新的挑战。让我们直接看我们的示例表:

| **客户号** | **名称** | **地址** | **信用** | **销售代表编号** | **销售代表姓名** |
| Two thousand three hundred and forty-three | 上下移动 | 威街 2343 号。 | $1000 | 03 | 戴夫 |
| Two thousand three hundred and forty-four | 丹尼尔（男子名） | 哇街 1230 号。 | $3000 | 04 | 徐嘉琳 |
| Two thousand three hundred and forty-five | 凯特 | 苏荷区 33 号 | $5000 | 03 | 戴夫 |
| Two thousand three hundred and forty-six | 较少的 | 99 更是 | $1000 | 07 | 加勒特（m.） |
| Two thousand three hundred and forty-seven | 但 | 少 1 个 St。 | $500 | 09 | 彼得（男子名） |
| Two thousand three hundred and forty-eight | 布鲁诺 | 糖果街 45 号。 | $2000 | 03 | 戴夫 |

现在，你可能认识到一些问题，但请放心，这是 2NF。每个字段都取决于客户编号。例如，客户编号 2345 只有一个与之关联的姓名、地址、信用、销售代表编号和销售代表姓名。然而，这并不意味着桌子已经准备好迎接黄金时间了。

首先，我们需要将行列式定义为确定另一列的任何列或列的集合。根据这个定义，主键或任何候选键都将是一个行列式。此外，这将使销售代表号成为一个决定因素，但它不是候选键或主键。

那么，这和 3NF 有什么关系呢？3NF 的定义是一个符合 2NF(当然还有 1NF)的表，如果它包含的唯一决定因素是候选键。这当然包括主键。

现在，您已经发现了这个表的问题:销售代表数量的决定因素。那么，你如何着手摆脱它呢？

首先，找出所有不是候选关键字的决定因素。然后，删除所有依赖于该决定键的字段，但保留表中的决定键。然后，将您删除的所有字段放入一个表中，主键是您留在主表中的决定性键。那么，这就意味着我们原来的
 `Customer #, Name, Address, Credit, Sales Rep #, Sales Rep Name` 
表会变成这两个表:
 `Customer #, Name, Address, Credit, Sales Rep #
Sales Rep #, Sales Rep Name` 
质量。

请记住，这两个新表还需要符合 1NF、2NF 和 3NF，所以请仔细检查它们，尽管如果您达到了这一点，您应该做得很好。

##### 第四范式

最后，我们到了大的那个。4NF 是形式之父，因为它是一切和一切的终结。这解决了可能出现的任何问题。让我们从定义一个非常重要的术语开始，多值依赖(MD)。如果 A 的每个值都与 B 的一个特定值列表相关联，并且这个集合独立于 c 的任何值，那么 MD 就是字段 B 依赖于 A。

让我们深呼吸一下，用一个简单的表格来说明这一点。让我们假设教员代表由学生号代表的任何数量的学生的指导顾问。此外，我们可以假设该教员将是任何数量的委员会的一部分，我们也希望存储这一点。这就是我们得出的结论。

| **教员** | **学号** | **委员会代码** |
| One thousand two hundred and forty-three | Two thousand three hundred and forty-three | 副词 |
| Â | Two thousand three hundred and forty-five | 每个 |
| Â | Â | 子宫输卵管造影 |
| One thousand five hundred and fifty-three | Three thousand two hundred and forty-three | 副词 |
| Four thousand and three | Three thousand four hundred and eight | 子宫输卵管造影 |
| Â | Four thousand and ninety-five | Â |
| Â | Four thousand four hundred and three | Â |

现在，将它传递给 1NF 会得到这样的结果:

| **教员** | **学号** | **委员会代码** |
| One thousand two hundred and forty-three | Two thousand three hundred and forty-three | 副词 |
| One thousand two hundred and forty-three | Two thousand three hundred and forty-five | 每个 |
| One thousand two hundred and forty-three | Two thousand three hundred and forty-five | 子宫输卵管造影 |
| One thousand five hundred and fifty-three | Three thousand two hundred and forty-three | 副词 |
| Four thousand and three | Three thousand four hundred and eight | 子宫输卵管造影 |
| Four thousand and three | Four thousand and ninety-five | 子宫输卵管造影 |
| Four thousand and three | Four thousand four hundred and three | 子宫输卵管造影 |

但是，你也可以清楚地看到问题的发展。这就是 MD。如果编号为 1243 的教师不再辅导编号为 2345 的学生，并且我们删除了数据，那么委员会信息也将被删除。这不是素质。

4NF 符合 3NF 标准，没有多值依赖关系。

您可以像在 2NF 和 3NF 中一样处理这个问题，方法是将表分成更小的表，每个表都包含对它们进行多重确定的字段，在这种情况下，该字段就是教员字段。因此，新表将是:
 `Faculty, Student Number
Faculty, Committee Code` 

**结论**
的确，这是一项艰巨的工作，但非常值得。确保表格得到优化是高质量设计的关键。在问题变成问题之前解决它对于管理数据库至关重要。

在开始设计数据库之前，收集所有要包含在数据库中的信息。我是说一切。到公司的每个部门转转(或者如果这只是给你的，就自己写出来)，在数据库里找出每个人想要的东西。一旦你有了一切，把它带回来，并创建一个巨大的桌子。

从这里开始，将该表分解为 1NF，然后 2NF，依此类推。回顾每张桌子，确保它们都在一起工作，并且都是 4NF 桌子。如果不是，那么可以肯定的是，这些桌子将来会出问题。

品质在于设计。对于那些知道的人来说，这有助于遵守真正关系数据库系统的 Codd 的前两条规则。

快乐编码。

## 分享这篇文章