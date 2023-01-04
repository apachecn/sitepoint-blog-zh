# 你从 ActiveJob 中得不到什么

> 原文：<https://www.sitepoint.com/dont-get-activejob/>

![ressort inox](img/b455ef30b1056949a4ef277f5551bc56.png)

Rails 4.2 包含了 *ActiveJob* ，这是一个统一的 API，用于队列和编写后台作业。ActiveJob 为 Rails 开发人员在编写后台作业时面临的两个问题提供了简单的解决方案:排队作业(通过 ActiveJob API)和 ActiveRecord 对象的序列化(通过 GlobalID)。

然而，它对第三个问题没有帮助:编写可以在生产环境中生存的有弹性的工作。这就是这篇文章的由来。

首先，我们简单回顾一下什么是 ActiveJob，它提供了什么。

## 主动工作给了你什么

ActiveJob 是各种作业队列的适配器，如 Delayed Job、Resque 或 Sidekiq。虽然这允许您“替换”作业队列系统，但是 ActiveJob 的真正强大之处在于它是一个单一的 API，您可以围绕它开发额外的库、工具和实践。

例如，您可以开发一个 gem 来对作业进行排队，确信它可以在任何 Rails 应用程序中工作。

让我们看一个例子。

假设我们有一个向客户收费的工作，叫做`PurchaseJob`。可以通过`perform_later`对作业进行排队。

```
PurchaseJob.perform_later(customer,amount)
```

要实现这项工作，只需子类化`ActiveJob::Base`并提供一个`perform`方法。

```
class PurchaseJob < ActiveJob::Base
  def perform(customer,amount)
    # job logic here
  end
end
```

通常，作为工作的第一步，您必须将要操作的记录的 ID(在本例中是一个`Customer`)排队，并对其进行反序列化。ActiveJob 的 GlobalID 为您处理所有这一切。

使用 ActiveJob 为后台处理提供了良好的基础，有助于避免一些常见错误。但是，在处理后台处理中最困难的部分:管理失败时，您仍然只能靠自己。

## 失败不是一个选项

即使在小范围内，您的后台作业也会失败。由于在后台作业中运行代码的主要原因是因为代码是长时间运行的，所以后台作业往往会“吸引”失败。像网络剥落，系统重启(Heroku dynos，有人吗？)和超时都会显著影响后台作业。

让我们通过想象上一节中我们工作的一个实现来看看如何实现。该实现将找到一个客户的信用卡，要求我们的第三方支付处理器(通过互联网的联网调用)在卡上收取一定数量的钱，最后用收费的结果创建一个购买记录。

```
class PurchaseJob < ActiveJob::Base
  def perform(customer,amount)
    credit_card = customer.credit_card

    result = PaymentProcessor.charge(credit_card.token,amount)

    if result.success?
      customer.purchases.create!(amount: amount,
                                success: true)
    else
      customer.purchases.create!(amount: amount,
                                success: false,
                                 reason: result.error_message)
    end
  end
end
```

假设在调用`PaymentProcessor.charge`的过程中，我们遇到了网络超时，并且引发了一个异常。因为我们不知道费用最终是否通过，所以我们不能再次执行这个作业，否则客户可能会被重复收费。但是，我们需要以某种方式完成购买过程。

鉴于我们工作的设计方式，必须有人手动干预才能完成购买。这显然是不可扩展的。问题是，我们的工作设计时并没有考虑到这种失败，然而这种失败很可能会定期发生。

ActiveJob 不提供默认的故障处理策略。它只是遵从底层作业队列要做的事情。即使*提供了故障处理策略，比如自动重试失败的任务，在这里也帮不了我们。*

我们仍然需要我们的工作能够处理失败。

有三种常见的技术可以应用到我们的作业设计中来处理失败:等幂作业、分解作业和失败无关紧要的作业。

## 幂等作业

[等幂是](http://en.wikipedia.org/wiki/Idempotence):

> 数学和计算机科学中某些运算的性质，可以多次应用而不改变最初应用后的结果。

这几乎与我们面临的问题一模一样。如果一个作业可以被执行多次而不改变最初执行后的结果，我们可以简单地不断重试直到它完成。

下面是一个幂等作业的简单示例，它生成一个 PDF 格式的收据。

```
class ReceiptPdfJob < ActiveJob::Base
  def perform(customer_name,customer_email,amount,amount_tax)
    File.open("/receipts/#{purchase.id}.pdf","w") do pdf
      ReceiptPdf.new(for: "#{customer_name} (#{customer_email})"
                subtotal: amount,
                     tax: amount_tax,
             grand_total: amount + amount_tax).write!(pdf)
    end
  end
end
```

无论我们执行这个代码多少次，它都会将相同的内容写入到相同的位置。如果我们在这个作业的任何一点遇到失败，我们可以安全地重新执行这个作业。

然而，我们的`PurchaseJob`并不允许这样做。

为了使*和*工作等幂，我们需要从根本上重新设计我们如何向客户收费。我们需要在每一步“保存我们的工作”,这样我们就可以从我们停止的地方继续。

要做到这一点，我们需要更多的簿记来存储我们在流程中的位置。如果我们被打断，我们可以查看我们正在进行的交易记录，以找出在哪里重新开始。

我们的工作将是这样的:

1.  在我们的数据库中创建一个记录，称为`Transaction`，记录我们将要进行的收费。
2.  尝试用我们的支付处理器收费，包括我们的`Transaction`的 id。
3.  充电完成后，用结果更新我们的`Transaction`。
4.  如果我们被打断，我们可以找到未完成的`Transaction`,并询问支付处理者是否在他们那里完成了。
5.  如果是，我们获取结果并更新我们的数据库。如果没有，我们重新尝试收费。

有点棘手。

```
class PurchaseJob < ActiveJob::Base
  def perform(customer,amount)
    # See if there is an incomplete transaction for
    # this customer and amount
    transaction = Transaction.where(customer: customer,
                                      amount: amount,
                                    complete: false).first

    if transaction.present?
      # if there is, find the analagous transaction
      # in our payment processor's system
      existing_payment = PaymentProcessor.payment(
        custom: { transaction_id: transaction.id }
      )
    else
      # if there isn't, create an in-progress one that we can find later
      transaction = Transaction.create!(customer: customer,
                                          amount: amount,
                                        complete: false)
    end

    unless existing_payment.present?
      # if there was no existing payment, actually charge the customer,
      # passing along our transaction ID as a customer data element
      existing_payment = PaymentProcessor.charge(
          credit_card.token,
          amount,
          custom: { transaction_id: transaction.id })
    end

    # complete the transaction and update the purchase record
    transaction.complete!(existing_payment)
    customer.purchases.create_from_transaction!(transaction)
  end
end
```

天哪！我们简单的收费过程变成了一大堆代码。但是，它现在是防弹的。

您可以在脑海中浏览每一行代码，无论发生什么情况，重新启动都会正确地从失败的作业停止的地方继续。这种心理练习是检查你的工作是否可以重复的一个很好的技巧。假设每一行代码都会爆炸，执行将在例程的顶部重新开始，就好像每一行后面都跟有`rescue retry`。

如果代码在所有情况下都能工作，那么你就可以开始了。如果没有，你需要重新设计它，让它可以。

这可能看起来很痛苦，但对于至关重要的工作，比如向人收钱，你必须格外小心。

关于这一变化，您会注意到一件事，那就是我们采取了一个简单的操作(向信用卡收费),并将其分解为几个更小的步骤。我们可以用另一种方式来使用这种技术，让我们的工作从失败中恢复过来。

## 将作业分解成更小的作业

当面对复杂的代码时，一种常见的技术是将代码分解成可重用的函数，这使得整个例程更容易遵循。我们的后台工作也可以做到这一点。

我们新推出的弹性购买收费代码的“快乐之路”如下:

1.  创建正在进行的事务
2.  在我们的支付处理器上向客户收费
3.  更新进行中的交易以显示已完成的费用

如果我们不创建一个复杂的作业，而是创建三个不同的作业，每个作业排队等待下一个作业，并且每个作业都被设计成可以自己重试，那会怎么样？

首先，我们会有一个开始这个过程的工作。此作业的目的是为客户和金额查找或创建正在进行的交易。

```
class StartPurchaseTransactionJob < ActiveJob::Base
  def perform(customer,amount)
    transaction = Transaction.where(customer: customer,
                                      amount: amount,
                                    complete: false).first

    unless transaction.present?
      transaction = Transaction.create!(customer: customer,
                                          amount: amount,
                                        complete: false)
    end

    ChargeTransactionJob.perform_later(transaction)
  end
end
```

最终，这个作业会排队等待一个`ChargeTransactionJob`。该作业的目的是从支付处理器获得完整的收费，或者通过查询现有的收费，或者通过实际向客户收费。

```
class ChargeTransactionJob < ActiveJob::Base
  def perform(transaction)
    payment = PaymentProcessor.payment(
      custom: { transaction_id: transaction.id }
    )

    unless payment.present?
      payment = PaymentProcessor.charge(
          transaction.credit_card.token,
          transaction.amount,
          custom: { transaction_id: transaction.id })
    end

    transaction.complete!(payment)

    CompletePurchaseJob.perform_later(transaction)
  end
end
```

最后，`CompletePurchaseJob`处理一旦购买完成后该做什么的“业务逻辑”。

```
class CompletePurchaseJob < ActiveJob::Base
  def perform(transaction)
    transaction.customer.purchases.create_from_transaction!(transaction)
  end
end
```

您会注意到，每个作业都是等幂的，如果失败，可以安全地重放。围绕“从我们停止的地方继续”的逻辑仍然存在，但现在我们有三个简单的工作，每个都很容易理解。这些作业也是可重复使用的。如果我们要求向客户收取订阅服务的月费，`ChargeTransactionJob`很容易被重用，因为它唯一的输入是一个`transaction`。

还有第三种应对失败的方法，那就是设计我们的工作，让失败可以被简单地忽略。

## 忽视失败的工作

忽视工作的失败似乎不是个好主意。这些作业已经排队，因为它们需要执行和完成。我们怎么能无视他们的失败呢？

如果一份工作可以

a)找出哪些工作需要自己完成，b)定期运行

则可以忽略个别作业失败。这是因为下次运行该作业时，它将“弥补”失败作业丢失的工作。

考虑一下我们在上一节是如何分解我们的采购流程的。第一个作业`StartPurchaseTransactionJob`创建一个“进行中”的事务，而第二个作业`ChargeTransactionJob`完成这样的事务。

我们可以修改`ChargeTransactionJob`，让它处理*所有*未完成的事务，而不仅仅是某个特定的事务。按照这种方式设计，我们可以简单地安排作业定期运行，并忽略任何一个作业的失败。

```
class ChargeIncompleteTransactionsJob < ActiveJob::Job
  def perform
    Transaction.incomplete.find_each do |transaction|
      payment = PaymentProcessor.payment(
        custom: { transaction_id: transaction.id }
      )

      unless payment.present?
        payment = PaymentProcessor.charge(
            transaction.credit_card.token,
            transaction.amount,
            custom: { transaction_id: transaction.id })
      end
      transaction.complete!(payment)

      CompletePurchaseJob.perform_later(transaction)
    end
  end
end
```

假设`ChargeIncompleteTransactionsJob`被周期性地排队，它将处理*所有*未完成的事务，甚至是那些由于前一个作业失败而未被处理的事务。只要作业不经常失败，所有未完成的事务最终都会被处理。

并不是每项任务都可以用这种方式实现，但是当数据库查询可以确定哪些工作需要完成以及哪些工作不需要及时完成时，这是一个很好的模式。例如，可以通过查询自作业上次运行以来更新的所有项目来刷新缓存。

## 最后

ActiveJob 通过提供用于排队和写入作业的单一 API，消除了写入后台作业中涉及的一些摩擦，但是处理失败的艰苦工作仍然是开发人员必须解决的问题。

在为后台处理设计流程时，请记住所有这些。将内联代码提取到后台作业时要特别小心。通过使用这里讨论的技术，您的后台作业可以安全、安静地运行，经受住生产使用的严酷考验。

## 分享这篇文章