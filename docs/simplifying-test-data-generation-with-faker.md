# 使用 Faker 简化测试数据生成

> 原文：<https://www.sitepoint.com/simplifying-test-data-generation-with-faker/>

测试是开发过程中的一个迭代部分，我们执行它来确保代码的质量。其中很大一部分需要编写测试用例，并使用随机测试数据测试我们应用程序的每个单元。

当我们将应用程序发布到生产环境中时，我们的应用程序的实际数据就会出现，但是在开发过程中，我们需要与真实数据相似的假数据来进行测试。流行的开源库 [Faker](https://github.com/fzaninotto/Faker "fzaninotto/Faker - GitHub") 为我们提供了生成适合各种场景的不同数据的能力。

在这里，我们将关注使用 Faker 生成随机测试数据来测试我们的测试用例。

## Faker 如何工作

Faker 附带了一组内置的数据提供程序，可以很容易地访问它们来生成测试数据。此外，我们可以定义自己的测试数据类型，使其高度可扩展。但是首先，让我们看一个基本的例子来说明 Faker 是如何工作的:

```
<?php
require "vendor/autoload.php";

$faker = FakerFactory::create();

// generate data by accessing properties
for ($i = 0; $i < 10; $i++) {
    echo "<p>" . $faker->name . "</p>";
    echo "<p>" . $faker->address . "</p>";
}
```

该示例假设 Faker 是使用 Composer 安装的[，并使用 Composer autoloader 使类定义可用。如果你没有使用 Composer，你也可以通过从](https://packagist.org/packages/fzaninotto/faker "fzaninotto/Faker - Packagist")[的 GitHub 库](https://github.com/fzaninotto/Faker "fzaninotto/Faker - GitHub")中克隆 Faker 并使用其自带的自动加载器来使用它。

要使用 Faker，我们首先需要从`FakerFactory`获得一个实例。所有默认的数据提供者都被自动加载到`$faker`对象中。然后我们通过调用一个格式化程序名来生成随机数据。上述代码的最终输出将列出来自可用数据源的 10 个随机的人名和地址。

提供程序是保存数据和必要的数据生成格式化程序方法的类。格式化程序是提供程序类中的方法，它直接从源生成测试数据，或者使用其他格式化程序的组合来生成测试数据。Faker 自带以下内置提供者:`Person`、`Address`、`PhoneNo`、`Company`、`Lorem`、`Internet`、`DateTime`、`Miscellaneous`和`UserAgent`。

让我们看一看`Person`类，以便更好地理解 Faker 提供者的结构。

```
<?php
namespace FakerProvider;

class Person extends FakerProviderBase
{
    protected static $formats = array(
           "{{firstName}} {{lastName}}",
    );
    protected static $firstName = array("John", "Jane");
    protected static $lastName = array("Doe");

    public function name() {
           $format = static::randomElement(static::$formats);
        return $this->generator->parse($format);
    }

    public static function firstName() {
        return static::randomElement(static::$firstName);
    }
}
```

`Person`充当提供者，扩展基本提供者类`FakerProviderBase`。`firstName()`是一个格式化程序，直接从内部`firstName`数据数组中检索随机数据元素。格式化程序可以组合其他格式化程序，并以特定的格式返回数据，这就是`name()`所做的。所有的提供者和格式化者都基于这个结构工作。

内置提供程序包含数据非常有限的基本格式化程序。如果您使用 Faker 来自动化生成测试数据的过程，您可能需要通过扩展基本提供者来创建您自己的数据集和格式化程序实现。

```
<?php
namespace FakerProvider;

class Student extends FakerProviderPerson
{
    protected static $formats = array(
        "{{lastName}} {{firstName}}",
        "{{firstName}} {{lastName}}"
    );
    protected static $firstName = array("Mark", "Adam");
    protected static $lastName = array("Clark", "Stewart");
    private static $prefix = array("Mr.", "Mrs.", "Ms.", "Miss", "Dr.");

    public static function prefix() {
        return static::randomElement(static::$prefix);
    }

    public static function firstName() {
        return static::prefix() . " " .
            static::randomElement(static::$firstName);
    }
}
```

由于`Student`不是默认的提供者，我们必须手动将其添加到 Faker 生成器中。如果在多个提供程序上定义了相同的方法，则最新添加的提供程序优先于其他提供程序。

```
<?php
$faker = new FakerGenerator();
$faker->addProvider(new FakerProviderStudent($faker));

echo $faker->firstName; // invokes Student::firstName()
```

## 一个更复杂的例子

内置的提供者包含用于测试的基本数据类型，但是真实世界的用例通常需要更多的复杂性。在这种情况下，我们需要创建自己的数据提供者和定制数据集来自动化测试过程。让我们从零开始构建一个 Faker 提供者来迎合真实世界的场景。

假设我们正在开发一个电子邮件营销服务，它从客户那里发送数千封包含各种广告的电子邮件。测试时我们需要哪些数据字段？基本上，我们需要一个电子邮件，主题，名称。和内容来测试电子邮件。

我们还假设有三种类型的电子邮件模板:

*   基于文本/HTML 内容的广告
*   带有单一全尺寸图像的广告
*   包含其他网站链接的广告

内容字段将是这些模板之一，所以我们还需要测试字段文本内容，图像和链接。

理解了主要需求之后，我们可以如下创建提供者:

```
<?php
namespace FakerProvider;

class EmailTemplate extends FakerProviderBase
{
    protected static $formats = array(
        '<p>Hello {{name}} </p>
        <p>{{text}}</p>
        <p>Newsletter by Exmaple</p>',

        '<p>{{adImage}}</p>
        <p>Newsletter by Exmaple</p>',

        '<p>Hello {{name}} </p>
        <p>{{link}}</p>
        <p>{{link}}</p>
        <p>{{link}}</p>
        <p>Newsletter by Exmaple</p>'
    );
    protected static $toEmail = array(
        "test@example.com",
        "test1@example.com"
    );
    protected static $name = array("Mark", "Adam");
    protected static $subject = array("Subject 1", "Subject 2");
    protected static $adImage = array("img1.png", "img2.jpg");
    protected static $link = array("link1", "link2");
    protected static $text = array("text1", "text2");

    public static function toEmail() {
        return static::randomElement(static::$toEmail);
    }

    public static function name() {
        return static::randomElement(static::$name);
    }

    public function template() {
        $format = static::randomElement(static::$formats);
        return $this->generator->parse($format);
    }
}
```

我们定义了三种格式来匹配三种不同的模板，然后我们为测试数据生成过程中使用的每个字段创建了数据集。所有字段都应该包含类似于上面代码中的`toEmail()`和`name()`的格式化方法。`template()`方法随机选择一种格式，并使用格式化程序填充必要的数据。

我们可以使用下面的代码获取测试数据，并将其传递给我们的电子邮件应用程序。

```
<?php
$faker = new FakerGenerator();
$faker->addProvider(new FakerProviderEmailTemplate($faker));

$email = $faker->toEmail; 
$subject =  $faker->subject;
$template = $faker->template;
```

上述技术的优点是，我们可以通过直接的格式化程序函数调用，使用单个提供者随机测试所有三种格式。但是，如果这些格式方法中的一个被破坏了，或者我们有一个场景，我们只需要连续测试其中一种格式，该怎么办呢？注释掉或手动移除格式并不是一个有吸引力的选择。

在这种情况下，我建议为每种格式创建单独的实现。我们可以用一种格式和所有的格式器方法定义一个基本的`EmailTemplate`类，然后通过扩展它创建三个不同的子实现。子类将只包含唯一的格式，格式化程序将从父类继承。然后，我们可以通过将每个电子邮件模板分别加载到 Faker 生成器来不同地使用它。

## 测试数据的一致性

通常我们会多次运行测试，并记录数据和结果。当遇到错误时，我们检查数据库或日志文件来找出相应的数据。一旦我们修复了错误，使用导致错误的相同数据运行测试用例是很重要的。Faker 使用播种，所以我们可以通过播种它的随机数生成器来复制以前的数据。

考虑以下代码:

```
<?php
$faker = FakerFactory::create();
$faker->seed(1000);
$faker->name;
```

我们将种子值指定为 1000。现在，无论我们执行上面的脚本多少次，对于所有的测试，名字都是相同的随机值序列。

在应用程序测试中，你应该为每个测试用例分配一个种子，并记录在你的日志中。一旦错误被修复，您可以获得导致错误的测试用例的种子编号，并使用种子编号用相同的数据再次测试它，以使它一致。

## 结论

您应该自动生成测试数据，以避免不必要的时间浪费。Faker 是一个简单而强大的生成随机测试数据的解决方案。Faker 的真正强大之处在于它能够扩展默认功能以适应更复杂的实现。

那么你的测试数据生成策略是什么呢？你喜欢用 Faker 自动生成测试数据吗？通过评论区让我知道。

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章