# 简单的 KVP 系统与亚马逊 S3

> 原文：<https://www.sitepoint.com/simple-kvp-system-with-amazon-s3/>

几周前，我写了关于亚马逊 PHP SDK 入门的文章。本文假设您熟悉使用 SDK 的基本概念。现在，我们将在这些知识的基础上创造一些很酷的东西:一个轻便但功能强大的数据存储系统，可以永远扩展。

当您想到云存储时，大多数时候您会想到媒体，如照片、视频和其他内容文件。但是云存储也是存储其他数据的好地方。

在本文中，我将描述如何创建一个高性能系统，使用云存储而不是数据库来存储无关的特定于项目的数据。

例如，假设您正在构建一个用户帐户系统。当然，你会有基本的，包括电子邮件地址，密码，名字和姓氏等。您可能有用户的许多详细信息，比如地址、生日等等。这些字段中有许多需要存储在数据库中，因为您希望能够搜索和过滤您的用户。

但是您可能想要添加的一些数据不需要是可搜索的，或者它可能太大而不能有效地存储在数据库中。例如，您可能希望允许用户自定义您的 web 应用程序的外观和感觉。他们将能够选择一些颜色，上传一个标志，并配置其他显示选项。这些选项不需要在数据库中可搜索，但是您确实需要一种方法在每个用户登录时轻松可靠地访问这些信息。

亚马逊的 S3 存储解决方案提供了一个极其简单的解决方案。以下是如何做到这一点:

## 要求

**S3** **服务**——你需要一个启用了 S3 的亚马逊 AWS 账户。

**S3 桶**——你需要创建一个 S3 桶(在我的[上一篇文章](/getting-started-with-the-aws-php-sdk/)中讨论过)。在我们的代码中，我们将这个桶称为“我的唯一桶名”。您将使用自己的存储桶名称。

虚拟主机-你需要一个普通的虚拟主机。然而，对于这个特殊的用例，建议将您的应用程序托管在 Amazon EC2 虚拟服务器上的云中。原因是，当你在亚马逊 EC2 实例上托管你的网站时，你的服务器和亚马逊 S3 之间的所有流量都是完全免费的。在其他地方托管会产生费用。对于测试和设置来说，这些费用是微不足道的(很可能属于 Amazon 的免费使用等级)，但从长远来看，在 EC2 上设置是一个好主意。

## 钻研代码

是时候弄湿我们的脚了…

**基础 PHP 类**

为了使这个系统尽可能简单，我们将构建一个简单的 PHP 类。这将使我们很容易将 KVP(键-值-对)概念用于几乎任何你能想到的事情。首先，创建一个最小类，1)接受一个名为“itemName”的参数，2)用 AWS SDK 自动创建一个亚马逊 S3 对象。同样，如果你不确定我们在哪里，就从阅读[这篇文章](/getting-started-with-the-aws-php-sdk/)开始。

```
class s3Kvp {

    private $itemName = Null;    // Property to store item name throughout the class.
    private $awsS3 = Null;       // Property to store an instance of the S3 Object

    // Construct runs when we create the class.
    public function __construct($itemName) {
        $this->itemName = $itemName;    // Store item name as class property.
        $this->awsS3 = new AmazonS3();  // Create s3 object as class property.
    }

}

// Create a new instance of the class
// Submitting 'user101' as the item name
// will allow us to store KVP data specifically
// for User with ID 101.
$myKvp = new s3Kvp('user101');

// We can create a separate instance of this class
// submitting a different itemName. In this case,
// data will be stored specifcially for User with ID 335.
$anotherKvp = new s3Kvp('user335');

// Of course, none of this does anything quite yet...
```

**加载数据**

现在，我们将向我们的类添加一些新的要素，包括两个新的属性:一个用于在使用类时存储实际数据，另一个用于告诉我们数据是否是脏的(如果自从我们加载它以来它已经被更改)。我们还将尝试从 S3 加载数据。这看起来有点复杂，但您可以关注我们添加的所有新功能的评论:

```
class s3Kvp {

    private $itemName = Null;
    private $awsS3 = Null;

    private $data = Null;                        // We'll store our item data here.
    private $isDirty = false;                    // We'll track whether or not anything changed here.
    private $bucket = 'my-unique-bucket-name';   // This is the "bucket", or domain where your data is stored.

    public function __construct($itemName) {
        $this->itemName = $itemName;
        $this->awsS3 = new AmazonS3();

        // Here we're trying to get an object matching our item name
        // located in the folder 'kvpdata'.
        // Start by Creating a Secure URL to Access Your Data
        $url = $this->awsS3->get_object_url('my-unique-bucket-name', 'kvpdata/'.$this->itemName, '10 Days');

        // Then grab the contents of the URL
        // The @ will allow the script to continue
        // if the URL cannot be reached.
        $responseData = false;
        $responseData = @file_get_contents($url);

        // If we got something back in our response data,
        // load it into the "data" property of this class.
        if ($responseData) {

            // We're going to store our data as an array encoded JSON,
            // so if we get anything we know we can
            // decode it back to an array.
            $responseAsArray = json_decode($responseData, true);

            // Check if the data is valid.
            if (is_array($responseAsArray)) {

                // It's valid! Load it up.
                $this->data = $responseAsArray;

            } else {

                // Not valid! Load an empty array.
                // A good app would do some error handling here.
                $this->data = array();

            }

        } else {

            // If we don't get anything back,
            // it means this item is new so we're just going to
            // load 'data' as an empty array.
            $this->data = array();

        }

    }

}
```

**对象重载**

PHP 中的重载提供了动态“创建”属性和方法的手段。这真的很有趣，我们将使用它来动态存储项目数据。

我们将在我们的类中创建两个新方法。其中一个名为“__get”，它允许我们轻松地检索存储在项目数据中的信息。另一个是“__set”，它允许我们更新现有数据和存储新数据。

它是这样工作的:

如果您创建了一个名为$myKvp 的 s3Kvp 类的新实例，并且想要访问 customBackgroundColor 属性(因此$myKvp->customBackgroundColor)，您将会得到一个错误，因为该属性不存在。

但是，如果我们在类中创建一个名为“__get”的方法，PHP 将在出错之前运行该函数。这允许我们动态地访问数据。同样的事情也适用于“__set”。如果一个属性不存在，但是你试图设置它，那么“__set”方法将被调用，如果它存在的话。

代码如下:

```
class s3Kvp {

    // This function will be called anytime
    // we try to RETRIEVE properties of our class
    // that do not exist.
    public function __get($propertName) {

        // Check to see if we have that property in our
        // data array.
        if (isset($this->data[$propertName])) {

            // If we have it, return it.
            // Simple, right!?
            return $this->data[$propertName];

        } else {

            // If not, return false;
            return false;

        }
    }

    // This function will be called anytime
    // we try to SET properties of our class
    // that do not exist.
    public function __set($propertName, $propertyValue) {

        // Simply set the propertyName submitted
        // in our data array to the value submitted.
        $this->data[$propertName] = $propertyValue;

        // One more thing...
        // We want to know if something is ever "set"
        // so we can mark the data as "dirty".
        // We'll use this later...
        $this->isDirty = true;

    }

    private $itemName = Null;
    private $awsS3 = Null;
    private $data = Null;
    private $isDirty = false;
    private $bucket = 'my-unique-bucket-name';

    public function __construct($itemName) {
        $this->itemName = $itemName;
        $this->awsS3 = new AmazonS3();
        $url = $this->awsS3->get_object_url('my-unique-bucket-name', 'kvpdata/'.$this->itemName, '10 Days');
        $responseData = false;
        $responseData = @file_get_contents($url);
        if ($responseData) {
            $responseAsArray = json_decode($responseData, true);
            if (is_array($responseAsArray)) {
                $this->data = $responseAsArray;

            } else {
                $this->data = array();
            }
        } else {
            $this->data = array();
        }
    }
```

**拯救那个婴儿**

现在我们班初具规模。我们可以从 S3 加载数据，我们可以设置和检索数据值。但是仍然缺少一些东西——保存数据。我们需要把它送回 S3。

为此，我们将利用 PHP 类内置的 __destruct 方法。这个方法，如果存在的话，就在 PHP 终止类之前被调用。所以如果我们创建了这个方法，一旦 PHP 知道我们已经完成了，我们每次使用这个类的实例时都会调用这个方法。

在 __destruct 方法中，我们将检查我们的类是否“是无效的”，如果是，我们将保存我们对 S3 的更改。然而，在保存之前，我们要将数据转换成 JSON。这使得它易于存储，并且如果我们需要的话，易于被其他系统访问。

```
class s3Kvp {

    // The __destruct is called by PHP as
    // each instance of this class is destroyed.
    public function __destruct() {

        // Check if "isDirty" is true,
        // which tells us that at some point
        // during the script execution
        // data was changed.
        if ($this->isDirty) {

            // Encode our data as JSON.
            $dataToSave = json_encode($this->data);

            // Use "create_object" to upload the data
            // back to S3\. If the object already exists,
            // this will simply replace it.
            $options = array('body' => $dataToSave);
            $this->awsS3->create_object('my-unique-bucket-name', 'kvpdata/'.$this->itemName, $options);

        }
    }

    public function __get($propertName) {
        if (isset($this->data[$propertName])) {
            return $this->data[$propertName];
        } else {
            return false;
        }
    }

    public function __set($propertName, $propertyValue) {
        $this->data[$propertName] = $propertyValue;
        $this->isDirty = true;
    }

    private $itemName = Null;
    private $awsS3 = Null;
    private $data = Null;
    private $isDirty = false;
    private $bucket = 'my-unique-bucket-name';

    public function __construct($itemName) {
        $this->itemName = $itemName;
        $this->awsS3 = new AmazonS3();
        $url = $this->awsS3->get_object_url('my-unique-bucket-name', 'kvpdata/'.$this->itemName, '10 Days');
        $responseData = false;
        $responseData = @file_get_contents($url);
        if ($responseData) {
            $responseAsArray = json_decode($responseData, true);
            if (is_array($responseAsArray)) {
                $this->data = $responseAsArray;

            } else {
                $this->data = array();
            }
        } else {
            $this->data = array();
        }
    }

}
```

## 使用类

这是最后一门课:

```
class s3Kvp {

    private $itemName = Null;
    private $awsS3 = Null;
    private $data = Null;
    private $isDirty = false;
    private $bucket = 'my-unique-bucket-name';

    public function __construct($itemName) {
        $this->itemName = $itemName;
        $this->awsS3 = new AmazonS3();
        $url = $this->awsS3->get_object_url('my-unique-bucket-name', 'kvpdata/'.$this->itemName, '10 Days');
        $responseData = false;
        $responseData = @file_get_contents($url);
        if ($responseData) {
            $responseAsArray = json_decode($responseData, true);
            if (is_array($responseAsArray)) {
                $this->data = $responseAsArray;

            } else {
                $this->data = array();
            }
        } else {
            $this->data = array();
        }
    }

    public function __get($propertName) {
        if (isset($this->data[$propertName])) {
            return $this->data[$propertName];
        } else {
            return false;
        }
    }

    public function __set($propertName, $propertyValue) {
        $this->data[$propertName] = $propertyValue;
        $this->isDirty = true;
    }

    public function __destruct() {
        if ($this->isDirty) {
            $dataToSave = json_encode($this->data);
            $options = array('body' => $dataToSave);
            $this->awsS3->create_object('my-unique-bucket-name', 'kvpdata/'.$this->itemName, $options);

        }
    }

}
```

当然，现在我们做了所有的工作，我们想用这个东西！这里有几个例子:

**用户账户系统**

如果您正在构建一个用户帐户系统，您可能希望存储特定于用户的大量信息。在这个例子中，我们假设你有一个分类购物界面。您允许用户决定他们希望每页查看多少产品。当他们做出选择时，您使用我们创建的特定于用户的 KVP 类来更新他们的首选项:

```
// Include the class we just created.
require_once 's3kvp.php';

// Create an instance of s4Kvp
// using the current User's ID;
$userId = 12301; //This might be stored in cookie or session variable.
$userKvp = new s3Kvp('user'.$userId);

// In this example,
// We're setting the "prefsItemsPerPage"
// equal to whatever we received in the
// "prefsItemsPerPage" querystring variable;
$submittedPreference = $_GET['prefsItemsPerPage'];
$userKvp->prefsItemsPerPage = $submittedPreference;
echo 'test';
```

```
// Include the class we just created.
require_once 's3kvp.php';

$userId = 12301;
$userKvp = new s3Kvp('user'.$userId);

echo $userKvp->prefsItemsPerPage;
```

**购物车系统**

我构建的整个购物车系统只使用了这样一个 KVP 类。随着谷歌分析的出现，我不再需要跟踪“部分”或“不完整”的订单。使用这样的系统，我可以存储大量的购物细节，包括购物车商品、促销、结账数据等大量信息。所有这些都存储在云存储中，直到结账时，我将需要搜索的特定数据复制到数据库中。除了最终订单，我从来不需要担心增加数据库的大小。

当然，对于这种类型的例子，我需要创建一个更健壮的 KVP 类——一个可以处理项目列表和其他特性的类。但想法是一样的:在云存储中存储特定于项目的数据。

## 我们现在在哪里？

在不到 50 行代码中，我们创建了一个健壮的系统来存储特定于项目的数据；该系统速度快且可扩展。

自从我使用这样的系统以来，我已经能够保持我的数据库表非常轻便，只存储我知道需要在搜索、过滤或汇总报告中使用的信息。我不必担心我的数据库表会变得非常大，有了 S3，我再也不用担心存储空间不足或备份数据。

**要记住的几件事**

如果不做一些修改，我不推荐像这样的系统存储在高负载下会被多个用户读取的数据。我确实在高负载下使用了这个概念，但是我在我的 KVP 类中添加了一个缓存层。

此外，您应该将数据存储在没有公共访问权限的 S3 存储桶中；这对安全性至关重要。

## 下一步是什么？

现在您已经有了这个基本类，您可以添加它来满足您的需要。以下是我过去在自己的应用程序中添加的一些内容:

**加密**–为了增加一层安全性，我在这个类中添加了加密。我将它插入 _ _ 构造和 __destruct 中，这样每当我加载或保存数据时，我都在解密和加密。

亚马逊 SimpleDB–这种类型的系统与 simple db 结合使用效果很好。Amazon SimpleDB 是一个高度可用、灵活且可伸缩的非关系数据存储，它减轻了数据库管理的工作。结合使用 SimpleDB 和 S3 KVP 存储概念，您可以创建可扩展的、灵活的系统，消除对性能或数据备份的担忧。

**默认数据**–在很多情况下，当没有找到特定的用户数据时，您不希望只返回“false”。您可以轻松地为各种属性指定默认值，并在 __get 函数中返回它们。由于它们是默认值，您甚至不需要将它们存储在 S3 的特定于项目的数据中，这使得您的数据更加简洁。

**缓存**–我一直都在使用缓存。像 memcached 这样的系统允许您在 web 服务器上存储需要频繁访问的数据。添加缓存层与添加加密层非常相似。在加载时，您只需检查您要寻找的数据是否在缓存中，如果在，就从缓存中加载它；否则，从 S3 装载。保存时，会更新缓存版本和 S3。

**验证**–在很多情况下，你会想在你的代码中添加数据验证。有时，您会将它添加到这个 KVP 类中，以确保数据至少格式正确。在接口方面，无论用户输入何时提交，在使用这个 KVP 类保存之前，您都需要对其进行验证。

**版本控制**–S3 提供了一种强大的方法来跟踪数据的版本。这对于创建回滚功能非常有用。如果您在 bucket 中打开了版本控制，Amazon 将在您每次保存时存储一个备份版本。然后，您可以随时回滚到任何以前的版本。我经常简单地将它用作备份——如果我不小心编写了删除数据的代码，如果我启用了版本控制，我可以恢复数据。还有很多其他用途。然而，版本控制确实会增加存储需求，所以只有在真正需要的时候才使用它。

## 有问题吗？

如果您对这个概念有任何问题或想法，请在下面开始对话。我很乐意回应！

## 分享这篇文章