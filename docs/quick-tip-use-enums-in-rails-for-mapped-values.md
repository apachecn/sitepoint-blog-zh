# 快速提示:在 Rails 中使用枚举来映射值

> 原文：<https://www.sitepoint.com/quick-tip-use-enums-in-rails-for-mapped-values/>

![](img/b25b54cda81a5268345873412929e4c9.png)

当我在呼叫中心工作时，我们会用不同的状态标记案例。这使得上层管理人员能够掌握案例的进展情况、瓶颈是什么以及通话流程。谢天谢地，我已经很久没有在呼叫中心工作了，但是我一直在思考如何用 Rails 来完成这项任务。幸运的是，Rails 有一个名为**enum**的解决方案。

当您向模型添加状态时，您可能会尝试使用字符串来设置状态。这非常有意义，因为不同的状态往往被命名为“待定”、“审查中”和“已完成”。

然而，Rails 鼓励您利用枚举来用整数替换这些字符串。乍一看似乎有悖常理，但实际上非常适合这个问题。

枚举允许您将字符串值映射到数据库中的整数，以便可以按名称查询它们:

```
class CustomerCase < ApplicationRecord
  enum status: [:open, :closed,:under_review, :pending]
end 
```

现在，在 Rails 控制台中，我们可以执行以下操作:

```
$ customer_case.open? 
```

我们甚至可以使用示波器:

```
$ CustomerCase.open 
```

为了让我们能够真正练习使用枚举，我强烈建议创建一个小的 Rails 应用程序:

```
$ rails new call_center 
```

切换到该目录，生成一个包含许多属性的快速脚手架:

```
$ rails generate scaffold CustomerCase title:string description:text status:integer agent:string 
```

注意，`status`字段是一个*整数*。

迁移数据库:

```
$ rails db:migrate 
```

打开**app/models/customer _ case . Rb**，输入以下内容:

```
class CustomerCase < ApplicationRecord
  enum status: [:open, :closed, :under_review, :pending]
end 
```

现在我们有一个小的应用程序启动并运行。继续在 rails 控制台(`rails c`)中创建几个具有不同属性的案例:

```
$ CustomerCase.create(title: "Case 1", description: "Our first case", status: :open, agent: "Me")
=> #<CustomerCase id: 1, title: "Case 1"...status: "open"
$ CustomerCase.create(title: "Case 2", description: "Our second case", status: :pending, agent: "Me")
=> #<CustomerCase id: 2, title: "Case 2"...status: "pending" 
```

更改**app/views/customer*case/*form . html . erb**中对应于枚举的行也是一个好主意，这样您就可以选择实际的状态:

```
...
</div>
<div class="field">
  <%= f.label :status %><br>
  <%=  f.collection_select :status, CustomerCase.statuses.map{ |a| [a.first,a.first] },  :first, :second %>
</div> 
```

我建议创建尽可能多的不同案例，并从 Rails 控制台测试不同的可能性:

```
$ bundle exec rails c
$ cse = CustomerCase.create(title:"Case Title", description:"what has happend", status: "open" )
$ cse.open?
=> true
$ cse.closed?
=> false
$ cse.pending?
=> false
$ CustomerCase.open.to_a
CustomerCase Load (0.1ms)  SELECT "customer_cases".* FROM "customer_cases" WHERE "customer_cases"."status" = ?  [["status", 0]]
=> #<ActiveRecord::Relation.. 
```

最后一个调用`open`作用域的例子显示了用于查询数据库的 SQL。该查询使用`0`作为值，而不是预期的`open`。

您还可以更改案例的状态:

```
$ cse.pending! 
```

(0.1ms)开始交易
SQL (1.0ms)更新“客户*案例”设置“状态”=？，“更新后的*在”=？其中“客户*例”。" id" =？[["status "，3]，["updated* at "，2016-10-05 13:34:28 UTC]，["id "，3]]
(0.6ms)提交事务
= >真

如果使用得当，枚举可以极大地增加代码的可读性。既然我们已经研究了 Enum 和一个可能的用例，我希望它能使在 Rails 应用程序中实现其他 Enum 相关的特性变得更加容易。

## 分享这篇文章