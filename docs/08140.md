# 用 Django 模型做更多事情

> 原文：<https://www.sitepoint.com/doing-more-with-your-django-models/>

所以你有一个 Django 应用程序，但是有时候你会发现 Django 模型太局限了。我们将指导您使用 Django 模型来获得更多信息。这是一个中级教程，假设您对 Django 有所了解。例如，我们假设您知道如何编写一个基本的 Django 模型，您知道如何覆盖 Python 方法，以及`.filter`和`.exclude`如何工作。

我们将谈论这些话题

1.  代理模型
2.  超越`.save`
3.  使用信号
4.  使用`.extra`优化您的数据库访问
5.  使用 Q 对象的高级查找
6.  聚合和注释
7.  使用 F()表达式

让我们看看您可能希望使用 Django 执行的一些常见操作，以及上述 Django 功能将如何帮助您实现这些操作。

### 如何获得同一个数据库表的两个 Python 表示？

您可能希望两个模型类对应于一个数据库表。例如，`admin.site.register`只允许一个模型注册一次。但是，您可能希望同一个模型在管理区出现两次。代理模型可以帮助你做到这一点！

```
from django.contrib.auth.models import User

class NewUser(User):
    class Meta:
        proxy = True 
```

现在，在您的`admin.py`中，您可以再次注册新用户并定制您的模型管理员。(例如，如果您只想显示部分字段，请添加自定义排序等)。

### 如何在将模型保存到数据库之前采取措施？

有时您可能有一些非规范化数据。考虑这个模型:

```
class Poll(models.Model):
    ###...
    num_choices = models.PositiveIntegerField()

class Choice(models.Model):
    poll = models.ForeignKey(Poll)
    ###... 
```

您希望在保存`Choice`之前增加`num_choices`。你可以像这样覆盖`.save`来做到这一点。

```
def save(self, *args, **kwargs):
    self.poll.num_choices += 1
    self.poll.save()
    super(Choice, self).save(*args, **kwargs) 
```

### 如果我没有编写模型，如何在将模型保存到数据库之前采取措施？

当你编写所有的模型时，重写`.save`是很棒的。然而，举例来说，你有一个`Subscription`模型，当有人唱起来，他们被分配一个订阅。然而，因为你没有写`User`模型，你不能覆盖。保存模型。

Django 在采取任何行动前都会发出信号。当*有趣的*事情发生时，你可以将你的功能与信号联系起来采取行动。Django 有两个信号
`pre_save`和`post_save`可以连接。

```
from django.db.models.signals import pre_save
from django.contrib.auth.models import User

def subscription_handler(**kwargs):
    #Do something with the Subscription model

pre_save.connect(subscription_handler, sender=User, dispatch_uid="subscription_handler") 
```

### 如何在不多次命中数据库的情况下获取相关对象？

假设我们有这些模型:

```
class Subject(models.Model):
    ###...

class Score(models.Model):
    ###...
    subject = models.ForeignKey(Subject)
    score = models.PositiveIntegerField() 
```

现在，您正在对 Subject queryset 进行迭代，并且您想要对当前对象拥有外键的所有 Score 对象进行求和。您可以通过获取单个的`Score`对象，然后在 Python 中对它们求和来实现，但是在数据库中实现会更快。Django 有一个方法`.extra`,允许您在 queryset 生成的 sql 中插入任意子句。比如这里你可以做

```
Subject.objects.extra(select={"total_scores": "select sum(score) from poll_score where poll_score.subject_id = poll_subject.id"}) 
```

假设应用程序名为`poll`，其表的默认名称是`poll_subject`和`poll_score`。

### 怎么能组合 OR，NOT 和其他 SQL 操作呢？

默认情况下，Django 会将所有标准传递给过滤方法。如果要使用 OR/NOT 运算符，就需要使用 Q 对象。

我们有这样一个模型:

```
class Score(models.Model):
    ###...
    subject = models.ForeignKey(Subject)
    score = models.PositiveIntegerField()
    date = models.DateField() 
```

所以，如果你想要所有分数大于 95 或者在 2012 年的物理对象的分数。

```
 criteria = Q(subject__name="Physics") & (Q(score__gt=95)|Q(date__year=2012)) 
```

我们使用双下划线符号来应用过滤器，并使用布尔运算符将它们连接在一起。你可以把它们传给`.filter`。(或至`.exclude`

```
Score.objects.filter(criteria) 
```

### 我如何获得 group_by 操作类型？

Django 在其查询集上提供了两种方法—`.aggregate`和`.annotate`。聚合将字典中的查询集转换为名称、值对。

例如，如果您想要`Score`个对象的最大值、最小值和平均值。您可以通过以下方式获得

```
from django.db.models import Avg, Max, Min

Score.objects.all().aggregate(Max('score'), Avg('score'), Min('score')) 
```

有关更多信息，请参见关于[聚合](https://docs.djangoproject.com/en/dev/topics/db/aggregation/)的指南

### 如何在行内进行比较？

Django 提供了用于创建行内比较查询的`F`对象。

我们有一个这样的模型:

```
class Department(models.Model):
    ##...
    num_employees = models.PositiveIntegerField()
    num_managers = models.PositiveIntegerField() 
```

您希望找到经理人数多于员工人数的所有部门。

```
from django.db.models import F  
Department.objects.filter(num_managers__gt=F('num_employees')) 
```

对象支持加、减、乘、除，所以你可以做这样的事情

```
Department.objects.filter(num_employees__lt=F('num_managers')*2) 
```

## 分享这篇文章