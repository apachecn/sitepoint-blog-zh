# 如何使用 JsonSerializable 接口

> 原文：<https://www.sitepoint.com/use-jsonserializable-interface/>

在过去的几年里，JSON 已经成为数据交换格式之王。在 JSON 之前，XML 占据了主导地位。它在建模复杂数据方面很棒，但是很难解析并且非常冗长。JSON 确实随着丰富的 AJAX 驱动的站点的激增而腾飞，因为它是一种非常容易阅读的格式，解析起来很快，并且它简单的键/值表示去掉了 XML 的所有冗长。

我想我们都同意，编写更少的代码，从而需要更少的维护和引入更少的错误，是我们都希望实现的目标。在这篇文章中，我想向你介绍一个鲜为人知的接口，它是在 PHP 5.4.0 中引入的，叫做`JsonSerializable`。

在 JsonSerializable 接口可用之前，为消费服务返回对象的 JSON 编码表示意味着两件事之一。

### 丑陋的

第一种方法是在对象外部构造一个数据结构，其中包含我们想要公开的所有数据。

```
<?php

class Customer
{

    private $email = null;
    private $name = null;

    public function __construct($email, $name)
    {
        $this->email = $email;
        $this->name = $name;
    }

    public function getName()
    {
        return $this->name;
    }

    public function getEmail()
    {
        return $this->email;
    }
}

$customer = new Customer('customer@sitepoint.com', 'Joe');

$data = [
    'customer' => [
        'email' => $customer->getEmail(),
        'name' => $customer->getName()
    ]
];

echo json_encode($data);
```

我们在这里使用了一个数组来保存来自我们想要编码的`Customer`对象的数据，但是它也可能是一个`StdClass`。

这种方法非常灵活，在非常简单的情况下就能达到目的，在这种情况下，我们知道`Customer`对象不会改变，我们只需要在这个地方使用这种格式的`Customer`数据。如果需要，我们还可以选择从其他来源向该阵列添加数据。

然而，正如我们都曾经历过的那样，我们所做的假设可能会在一瞬间被证明是错误的。我们可能会收到要求我们向`Customer`类添加更多数据的请求。新数据需要返回给消费服务，我们希望在许多地方这样做。

可以想象，这种方法很快就变得很麻烦。我们不仅要在整个应用程序中复制这个数组代码，还必须记住在不可避免地出现更多变化时更新所有这些实例。不过，还有另一种方法可以帮助我们消除这些问题。

### 坏事

幸运的是，当第一个变更请求到来时，我们很聪明，我们意识到复制我们的数组将是一场噩梦，所以我们决定做的是将编码功能内在化到我们的对象中，消除维护问题并减少引入错误的可能性。

```
<?php

class Customer
{

    public $email = null;
    public $name = null;

    public function __construct($email, $name)
    {
        $this->email = $email;
        $this->name = $name;
    }

    public function getName()
    {
        return $this->name;
    }

    public function getEmail()
    {
        return $this->email;
    }

    public function toJson()
    {
        return json_encode([
            'customer' => [
                'email' => $this->getEmail(),
                'name' => $this->getName()
            ]
        ]);
    }
}

$customer = new Customer('customer@sitepoint.com', 'Joe');

echo $customer->toJson();
```

现在，如果有更多的变更请求进来，希望更多的数据被添加到`Customer`对象并从其返回，我们可以只更新`toJson`方法。

不过，这种方法也有自己的缺点。任何想使用我们的`Customer`方法的人都需要知道这个`toJson`方法，因为它不容易检查，所以我们需要准确的文档。我们还必须记住，这个方法*现在返回* JSON，(尽管我们可以将序列化移到方法之外)。这使得将`Customer`数据与其他来源的数据结合起来更加困难，因为我们必须小心不要再次编码这个方法的结果，因为那会导致一些讨厌的错误。

### 好人

最后进入`JsonSerializable`界面。这给了我们丑陋的 T2 场景的灵活性和糟糕的 T4 场景的可维护性。尽管要使用这个接口，你需要运行 PHP 5.4.0+,这也是你应该做的，因为它比旧版本有很多改进。

所以，说正事。

```
<?php

class Customer implements JsonSerializable
{

    private $name;
    private $email;

    public function __construct($name, $email)
    {
        $this->name = $name;
        $this->email = $email;
    }

    public function getName()
    {
        return $this->name;
    }

    public function getEmail()
    {
        return $this->email;
    }

    public function jsonSerialize()
    {
        return [
            'customer' => [
                'name' => $this->name,
                'email' => $this->email
            ]
        ];
    }
}

$customer = new Customer('customer@sitepoint.com', 'Joe');

echo json_encode($customer);
```

正如你所看到的，我们通过向我们的类添加接口，然后向我们的类体添加一个`jsonSerialize`方法来实现`JsonSerializable`，以满足接口契约。

在`jsonSerialize`方法中，我们构造并返回一个对象数据数组，就像我们在其他例子中所做的一样。如果有任何变化，我们可以更新这个方法。您会注意到`jsonSerialize`方法只返回一个数组。

神奇的是当你想触发这个方法时，我们现在所要做的就是 json 编码这个类的一个实例，这个方法将被自动调用，返回数据数组，然后编码！现在这个类实现了一个接口，我们可以检查这个类是否是`JsonSerializable`的实例。如果你愿意，你也可以在方法中输入提示来确保一个`JsonSerializable`接口被传递。

## 摘要

通过这个简单的实现，我们消除了重复，减少了维护量，并减少了引入错误的机会。我们还通过检查对象是否是`JsonSerializable`的实例，让其他人使用我们的代码来测试对象被编码的能力变得很简单。

上面的例子当然是人为的，但是，我希望我已经成功地展示了使用这个界面的好处，并激励你继续使用它。

## 分享这篇文章