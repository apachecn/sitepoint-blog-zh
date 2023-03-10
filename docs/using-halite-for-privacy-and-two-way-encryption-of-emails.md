# 使用 Halite 保护隐私和双向加密电子邮件

> 原文：<https://www.sitepoint.com/using-halite-for-privacy-and-two-way-encryption-of-emails/>

密码学是一件复杂的事情。事实上，有一条黄金法则:

**不要自己实现加密**

其原因是，在实施过程中，有太多的事情可能会出错，最轻微的错误都会产生漏洞，如果您不注意，您宝贵的数据可能会被其他人读取。虽然这不是一个详尽的列表，但在使用加密技术时，有几个重要的准则需要遵循:

*   不要用同一个密钥加密所有东西
*   不要直接使用生成的密钥来加密
*   当生成不希望被猜测的值时，使用加密安全的伪随机数生成器( [CSPRNG](https://en.wikipedia.org/wiki/Cryptographically_secure_pseudorandom_number_generator) )
*   加密，然后 MAC(或[密码毁灭法则](https://moxie.org/blog/the-cryptographic-doom-principle/))
*   Kerckhoffs 的原则:一个加密系统应该是安全的，即使除了密钥之外，这个系统的一切都是公开的

![Picture of keys hanging on strings](img/dbb081c50973b18d0b1b7698cebcad21.png)

本文中使用的一些加密术语可以定义如下:

*   [Key](https://goo.gl/30VUZ7) :决定密码算法功能输出的一条信息。
*   [CSPRNG](https://en.wikipedia.org/wiki/Cryptographically_secure_pseudorandom_number_generator) :也称为确定性随机位生成器，是一种生成数字序列的算法，其性质近似于随机数(或字节)序列的性质。为了保证密码安全，PRNG 必须:
    *   通过统计随机性测试
    *   在严重攻击下保持良好，即使攻击者可以获得它们的部分初始或运行状态。
*   [MAC](https://en.wikipedia.org/wiki/Message_authentication_code) :是一小段信息，用于确认消息来自指定的发送方(其真实性)，并且在传输过程中没有被更改(其完整性)。它接受作为输入的秘密密钥和任意长度的待认证消息，并输出 MAC。

要进一步了解加密技术并有更好的理解，您可以看看下面的页面:

*   [NetAction 加密软件使用指南](http://www.netaction.org/encrypt/terms.html)
*   [密码学基本原理介绍](http://www.thegeekstuff.com/2012/07/cryptography-basics/)
*   [你不会把 64 作为密码的基础——密码术解码](https://paragonie.com/blog/2015/08/you-wouldnt-base64-a-password-cryptography-decoded)

有些库实现了密码学原语和操作，并把很多决定权留给了开发人员。这些例子有 [php 自己的加密库](http://php.net/manual/en/refs.crypto.php)，或者 Defuse 的 [php-encryption](https://github.com/defuse/php-encryption) 。一些 PHP 框架实现了他们自己的加密，如 Zend Framework 的 [zend-crypt](http://framework.zend.com/manual/current/en/modules/zend.crypt.introduction.html) 或 [Laravel](https://laravel.com/docs/5.1/encryption) 。

尽管如此，除了使用[libna 库](https://github.com/jedisct1/libsodium)之外，有一个库因其简单性而脱颖而出，并承担了开发人员在最佳实践方面的大量责任。在本文中，我们将探索[石盐](https://paragonie.com/project/halite)。

![halite](img/13087925fb295f0d93b4012160154299.png)

假设您已经安装并运行了 PHP 7、一个 web 服务器和一个 MySQL 服务器。你可能想看看[家园改善](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)这样的环境。为了能够使用 Halite，我们的系统必须有 lib 钠库，以及 lib 钠 PHP 扩展。这些可以通过使用以下命令来安装。*一如既往，根据您的系统配置和安装的软件包，您的里程可能会有所不同*:

## 安装在 Ubuntu 中

```
sudo apt-get install build-essential
wget https://github.com/jedisct1/libsodium/releases/download/1.0.10/libsodium-1.0.10.tar.gz
tar -xvf libsodium-1.0.10.tar.gz
cd libsodium-1.0.10/
sudo ./configure
sudo make
sudo make install
sudo apt-get install php-pear php7.0-dev
sudo pecl install libsodium 
```

## 安装在 CentOS 中

```
sudo yum groupinstall "Development tools"
wget https://github.com/jedisct1/libsodium/releases/download/1.0.10/libsodium-1.0.10.tar.gz
tar -xvf libsodium-1.0.10.tar.gz
cd libsodium-1.0.10/
sudo ./configure
sudo make
sudo make install
yum install php-pear php-devel
sudo pecl install libsodium 
```

您必须编辑您的`php.ini`文件以包含`extension=libsodium.so`行，并重启您的 web 服务器或`php-fpm`。

## 应用程序

为了展示 Halite 的一些基本特性，让我们为一个简单的“类似电子邮件”的消息传递应用程序创建一组 RESTful 服务，其中我们希望对用户之间发送的消息进行加密。请考虑到这将只是一个微小而简单的例子，许多标准的电子邮件功能将会丢失。如果你想浏览完整的源代码，请看看 [github repo](https://github.com/sitepoint-editors/sp-halite) 。

数据库表具有以下模式:

![Database schema](img/c9628ccd0a1131ba536f8535928381b4.png)

最相关的字段是:

| 桌子 | 领域 | 目的 |
| --- | --- | --- |
| 用户 | 盐 | 为每个用户保存一个随机二进制字符串 |
| 信息 | 用户 id | 消息要发送到的用户 id |
| 信息 | from 用户 Id | 发送消息的用户 id |

让我们首先初始化一个`composer.json`文件，然后使用 Composer 声明我们的项目对 Halite 库的依赖性。

```
composer init
composer require paragonie/halite 
```

对于这个例子，我将使用 [silex](http://silex.sensiolabs.org/) 将请求路由到 MVC 风格的应用程序中， [phpdotenv](https://github.com/vlucas/phpdotenv) 存储和加载环境变量， [doctrine orm](http://www.doctrine-project.org/projects/orm.html) 使用 php 对象与数据库交互。

```
composer require silex/silex:~2.0 vlucas/phpdotenv doctrine/orm 
```

成功下载库后，`composer.json`文件应该修改成如下所示:

```
{
    "name": "yourName/projectName",
    "description": "Sitepoint tutorial for Halite",
    "authors": [
        {
            "name": "yourName",
            "email": "yourEmail"
        }
    ],
    "require": {
        "silex/silex": "^1.3",
        "vlucas/phpdotenv": "^2.2",
        "paragonie/halite": "^2.1",
        "doctrine/orm": "^2.5"
    },
    "autoload": {
        "psr-4": {"Acme\\": "src/"}
    }
} 
```

Acme 名称空间将保存我们的自定义类。

我们的示例应用程序中的 RESTful 服务具有以下结构:
![RESTful services](img/2caeb2c8b35dc6bdde7191d552609da8.png)

| 方法 | 全球资源定位器(Uniform Resource Locator) | 描述 |
| --- | --- | --- |
| 得到 | /用户 | 获取用户列表 |
| 得到 | /users/{userid} | 获取给定用户的详细信息 |
| 得到 | **/用户/{用户标识}/消息** | 获取发送给给定用户的消息列表 |
| 邮政 | **/用户/{用户标识}/消息** | 向用户发送消息 |
| 得到 | **/用户/{用户标识}/消息/{消息标识}** | 从给定用户检索消息 |

对于这个应用程序，我们希望加密主题和消息，所以用**粗体**表示的 URL 是使用 Halite 特性的 URL。

对于本文，我们将使用对称加密。也就是说，我们使用相同的密钥来加密和解密消息。

假设我们在系统中有以下用户(通过调用用户列表服务):

```
#Request
GET /users HTTP/1.1
Host: yourhost.dev
Cache-Control: no-cache

#Response
[
    {
        "id": 1,
        "name": "John Smith"
    },
    {
        "id": 2,
        "name": "Jane Doe"
    }
] 
```

当我们想从简那里给约翰发送一条消息时，那么我们应该发送下面的请求:

```
POST /users/1/messages HTTP/1.1
Host: yourhost.dev
Cache-Control: no-cache

{
  "from": "2",
  "subject": "This is my subject",
  "message": "super secret information!!!"
} 
```

示例项目使用 Silex 和 Doctrine 来完成一些繁重的工作，但是，展示它们是如何工作的不是本文的范围。为此，请参见[这篇关于 Silex](https://www.sitepoint.com/introduction-silex-symfony-micro-framework/) 和[我们的一些教义文章](https://www.sitepoint.com/laravel-doctrine-best-of-both-worlds/)。

粗略地说，Silex 会将 HTTP 请求转发给一个控制器。控制器将调用一个服务，事情开始变得有趣起来。让我们看看来自`\Acme\Service\Message::save`方法的代码。

```
<?php
namespace Acme\Service;

use Acme\Model\Message as MessageModel;
use Acme\Model\Repository\Message as MessageRepository;
use Acme\Model\User as UserModel;
use Doctrine\ORM\EntityManager;
use Doctrine\ORM\EntityRepository;
use ParagonIE\Halite\KeyFactory;
use ParagonIE\Halite\Symmetric\AuthenticationKey;
use ParagonIE\Halite\Symmetric\Crypto;
use ParagonIE\Halite\Symmetric\EncryptionKey;

/**
 * Class Message
 * @package Acme\Service
 */
class Message
{

// ... SOME CODE HERE ...

    /**
         * @param $from
         * @param $to
         * @param $subject
         * @param $message
         * @return string
         * @throws \ParagonIE\Halite\Alerts\InvalidSalt
         */
        public function save($from, $to, $subject, $message)
        {
            /*
             * create a new model object to hold the message
             */
            $messageModel = new MessageModel;

            /** @var EntityRepository $userRepository */
            $userRepository = $this->em->getRepository('Acme\Model\User');

            /*
             * search for the sender and recipient users
             */
            /** @var UserModel $fromUserModel */
            $fromUserModel = $userRepository->find($from);

            /** @var UserModel $toUserModel */
            $toUserModel = $userRepository->find($to);

            if (!$fromUserModel || !$toUserModel) {
                throw new \InvalidArgumentException('From or to user does not exist');
            }

            /*
             * create a placeholder for data, in order to generate a message id, used later to encrypt data.
             */
            $messageModel->setFromUser($fromUserModel)->setToUser($toUserModel);

            $this->em->persist($messageModel);
            $this->em->flush();

            /*
             * Retrieve the salts for both the sender and the recipient
             */
            $toUserSalt = $toUserModel->getSalt();

            /*
             * create encryption keys concatenating user's salt, a string representing the target field to be
             * encrypted, the message unique id, and a system wide salt.
             */
            $encryptionKeySubject = KeyFactory::deriveEncryptionKey(
                base64_decode($toUserSalt) . 'subject' . $messageModel->getId(),
                base64_decode(getenv('HALITE_ENCRYPTION_KEY_SALT')));
            $encryptionKeyMessage = KeyFactory::deriveEncryptionKey(
                base64_decode($toUserSalt) . 'message' . $messageModel->getId(),
                base64_decode(getenv('HALITE_ENCRYPTION_KEY_SALT')));

            /*
             * encrypt the subject and the message, each with their own encryption key
             */
            $cipherSubject = Crypto::encrypt($subject, $encryptionKeySubject, true);
            $cipherMessage = Crypto::encrypt($message, $encryptionKeyMessage, true);

            $messageModel->setSubject(base64_encode($cipherSubject))->setMessage(base64_encode($cipherMessage));

            $this->em->persist($messageModel);
            $this->em->flush();

            return $messageModel->getId();
        }
} 
```

首先，创建一个表示来自 messages 表的记录的对象，用于教条。这将使向数据库写入数据变得更加容易。该对象具有 1:1 映射到消息表的属性。

接下来，为 users 表创建一个理论存储库。存储库用于轻松地将数据检索到模型对象中。语句`$userRepository->find($from)`和`$userRepository->find($to)`显示了检索数据的简易性，无需编写任何 SQL 命令。

产生的模型然后被分配给我们的消息模型，然后通过`$this->em->persist($messageModel);`和`$this->em->flush();`语句使用 Doctrine 的实体管理器持久化到数据库。这样做是为了可以检索到为该记录创建的主键，并在以后用于加密数据。*关于持续/刷新的双重性质的更多信息，见[这篇文章](https://www.sitepoint.com/laravel-doctrine-best-of-both-worlds/)。*

我们数据库中的每个用户都拥有一个唯一的随机字节字符串，称为“salt”。这个 salt 是 base64 编码的，以便作为一系列可打印的字符存储在数据库中，看起来像这样:

![users table data](img/0060c11bed8bb65a364f9b1ce9bd5c85.png)

我们需要检索发送方和接收方的 salt 来加密消息。你可能已经猜到了，这可以通过上面加载了`$toUserSalt = $toUserModel->getSalt();`和`$fromUserSalt = $fromUserModel->getSalt();`的模型轻松完成。

终于，真正的乐趣开始了。是时候使用 Halite 库了。密码学的一个规则是永远不要直接使用预生成的密钥来加密数据，也不要多次使用同一个密钥。我们将使用`\ParagonIE\Halite\KeyFactory`类来生成加密密钥。请仔细看下面的陈述:

```
$encryptionKeySubject = KeyFactory::deriveEncryptionKey(base64_decode($toUserSalt) . 'subject' . $messageModel->getId(), base64_decode(getenv('HALITE_ENCRYPTION_KEY_SALT'))); 
```

`\ParagonIE\Halite\KeyFactory::deriveEncryptionKey`方法接收两个参数:密码和一个 salt。请注意，我们使用收件人的 salt(必须首先进行 base64 解码)、字符串“subject”和消息的唯一 ID 的连接作为密码参数；这将确保“密码”对于此收件人/此记录/此字段是唯一的。由于 dotenv 库，第二个参数将保存应用程序范围的 salt 集作为环境变量。这个 salt 存储在一个用 base64 编码的`.env`文件中(和数据库凭证一起)。`.env`文件保存的盐如下:

```
HALITE_ENCRYPTION_KEY_SALT="/t4xwLjjkG0RoRHGkmOQVw==" 
```

这个 salt 是一个 16 字节(根据 Halite 的密钥派生函数中使用的 [argon2](https://en.wikipedia.org/wiki/Argon2) 算法的要求)随机二进制字符串。这个字符串可以用`base64_encode(random_bytes(16))`生成。为了保证这种方法的安全性，应用服务器和数据库服务器应该位于不同的硬件上。

如果两台服务器都在同一台机器上，并且如果攻击者能够访问它，他们将拥有解密信息所需的一切。另一种方法是使用在服务启动时提供和/或保存在内存中的密钥来加密应用程序的 salt。只要这个密钥没有被截获，攻击者就没有办法解密任何东西，即使 web 服务器和数据库服务器在同一个硬件上。

现在可以进行真正的加密了

```
$cipherSubject = Crypto::encrypt($subject, $encryptionKeySubject, true);
$cipherMessage = Crypto::encrypt($message, $encryptionKeyMessage, true); 
```

`\ParagonIE\Halite\Symmetric\Crypto::encrypt`的第一个参数是明文，第二个参数是上面导出的加密密钥。第三个参数将告诉 Halite 返回原始的二进制字符串。默认行为是返回十六进制编码的表示。这个当然要看你的喜好了。

目前我们已经加密了一条消息，所以乍一看不会知道内容是什么。Halite 已经公开了这样一个事实，它将在密码文本中添加一个认证码，以确保消息的内容没有被篡改。它还将使用不同的初始化向量来确保用相同密钥加密的相同明文将产生不同的密码文本。

现在我们准备将密码文本分配给我们的模型(首先对它们进行 base64 编码)`$messageModel->setSubject(base64_encode($cipherSubject))->setMessage(base64_encode($cipherMessage));`，以便能够将它持久化到我们的数据库中。

让我们看看完整的请求和响应

**请求**

```
POST /users/1/messages HTTP/1.1
Host: yourhost.dev
Cache-Control: no-cache

{
  "from": "2",
  "subject": "This is my subject",
  "message": "super secret information!!!"
} 
```

**响应**

```
{
    "messageId": 1
} 
```

如果我们直接查询数据库，记录将如下所示:

```
*************************** 1\. row ***************************
        id: 1
  users_id: 1
fromUserId: 2
   subject: MUICAV13/brmlurlUIF6TOmhD6duztBI7vYzd4PGrJu8TinhAm69Kzv5QVFpNO55mssxsthPqmwb7l/py1iTCl0tSHUcB5Wsep0bcYDztIPhZ3g7VOcKKqu+YBTcuWprMvM22nvVdzcismGvCjkVW5hqCuNaCJPaUY+7VKRqCLg6FPY4WLMOdbY2yotQ4Q==
   message: MUICAaSGjcjwaDUORzctiK8rDla+w2Wm/6Z75EP7LJsRZbCk5zVHC/R6oxaJ6VrVbaB6WG1k9xtUcCt9fGN40r3zjFxp4M24QEdVu18t7A8N4wbiwd1W7LqbqjieziBchtKIJjE4oM68BlKxJMGO020GSnwNBuXIaz1b1MRVQDEsm1eT/oTx1oeLvwG1X94raKpHmK7D 
```

如果出于任何原因，我们要发送完全相同的请求:

```
POST /users/1/messages HTTP/1.1
Host: yourhost.dev
Cache-Control: no-cache

{
  "from": "2",
  "subject": "This is my subject",
  "message": "super secret information!!!"
} 
```

```
{
    "messageId": 2
} 
```

并再次查询数据库:

```
*************************** 1\. row ***************************
        id: 1
  users_id: 1
fromUserId: 2
   subject: MUICAV13/brmlurlUIF6TOmhD6duztBI7vYzd4PGrJu8TinhAm69Kzv5QVFpNO55mssxsthPqmwb7l/py1iTCl0tSHUcB5Wsep0bcYDztIPhZ3g7VOcKKqu+YBTcuWprMvM22nvVdzcismGvCjkVW5hqCuNaCJPaUY+7VKRqCLg6FPY4WLMOdbY2yotQ4Q==
   message: MUICAaSGjcjwaDUORzctiK8rDla+w2Wm/6Z75EP7LJsRZbCk5zVHC/R6oxaJ6VrVbaB6WG1k9xtUcCt9fGN40r3zjFxp4M24QEdVu18t7A8N4wbiwd1W7LqbqjieziBchtKIJjE4oM68BlKxJMGO020GSnwNBuXIaz1b1MRVQDEsm1eT/oTx1oeLvwG1X94raKpHmK7D
*************************** 2\. row ***************************
        id: 2
  users_id: 1
fromUserId: 2
   subject: MUICAeF/ci3fG7YWIhuLCU0pIxpbNDQ10KVWjSZF4G3K8lNVdV81LeveFyhAt/9PvJQy1ePLzl3EupJCROEQ+/L4nHUFRvCekEter2AQvnlyW03cxfjzZ6XwXBUkhzhZrT22JzxL7gSzpC6fPInAzsdDRIqhK/50wPhg/Wr29pH+PT/qwfuKr0rQWdyIfg==
   message: MUICAYOTzGO7DcpX7bVn4BMH4DKW+JCH1Eacj+lPx9I6eOHjNTA9jHT+u+VEz7cfdiitySU5UYYpBz+IBpV7b8hp/hwkqoLP7Durq/sPRJE/qMY9naKumaPXYW6XMhkysfwnwAIWQhjuiu/r9ARBwdTP3AgFNfVVc/jTlCuaPPZ8jw39xKCO5eNU3UyBrcXFhsGBnS6B
2 rows in set (0.00 sec) 
```

请注意，虽然两个消息中的纯文本实际上是相同的，但在数据库中它们看起来完全不同，因此即使攻击者能够直接访问数据库，如果没有加密密钥，他们甚至无法判断消息是相关的，更不用说是相同的，或者找出它们的内容。

现在是解密时间！

```
<?php
namespace Acme\Service;

use Acme\Model\Message as MessageModel;
use Acme\Model\Repository\Message as MessageRepository;
use Acme\Model\User as UserModel;
use Doctrine\ORM\EntityManager;
use Doctrine\ORM\EntityRepository;
use ParagonIE\Halite\KeyFactory;
use ParagonIE\Halite\Symmetric\AuthenticationKey;
use ParagonIE\Halite\Symmetric\Crypto;
use ParagonIE\Halite\Symmetric\EncryptionKey;

/**
 * Class Message
 * @package Acme\Service
 */
class Message
{

// ... SOME CODE HERE ...

        /**
         * @param $userId
         * @param $messageId
         * @return array
         * @throws \InvalidArgumentException
         */
        public function get($userId, $messageId)
        {
            /** @var MessageRepository $repository */
            $repository = $this->em->getRepository('Acme\Model\Message');
            /** @var MessageModel $message */
            if (($message = $repository->find($messageId)) == true) {
                $toUser = $message->getToUser();

                /*
                 * Verify that the message belongs to the intended user
                 */
                if ($toUser->getId() == $userId) {
                    $toUserSalt = $toUser->getSalt();
                    $fromUser = $message->getFromUser();

                    $encryptionKeySubject = KeyFactory::deriveEncryptionKey(base64_decode($toUserSalt) . 'subject' . $message->getId(),
                        base64_decode(getenv('HALITE_ENCRYPTION_KEY_SALT')));

                    $encryptionKeyMessage = KeyFactory::deriveEncryptionKey(base64_decode($toUserSalt) . 'message' . $message->getId(),
                        base64_decode(getenv('HALITE_ENCRYPTION_KEY_SALT')));

                    $plainSubject = Crypto::decrypt(base64_decode($message->getSubject()), $encryptionKeySubject, true);
                    $plainMessage = Crypto::decrypt(base64_decode($message->getMessage()), $encryptionKeyMessage, true);

                    return [
                        'id' => $message->getId(),
                        'subject' => $plainSubject,
                        'message' => $plainMessage,
                        'name' => $fromUser->getName(),
                    ];

                }
            }

            throw new \InvalidArgumentException('Message not found');
        }
} 
```

我们用`$repository = $this->em->getRepository('Acme\Model\Message');`获取消息表存储库，然后用`$message = $repository->find($messageId)`加载消息。为了解密，我们必须像第一次保存消息时一样导出加密密钥，只是这一次我们将使用`\ParagonIE\Halite\Symmetric\Crypto::decrypt`方法:

```
$plainSubject = Crypto::decrypt(base64_decode($message->getSubject()), $encryptionKeySubject, true);
$plainMessage = Crypto::decrypt(base64_decode($message->getMessage()), $encryptionKeyMessage, true); 
```

该方法接收 3 个参数:密码文本、加密密钥和标志，该标志告诉该方法应该在密码文本中接收原始的二进制字节。*(记得我们在数据库中存储的是 base64 编码，这也是我们需要 base64 解码的原因)*。现在，我们能够执行通过 ID 检索消息的请求:

**请求**

```
GET /users/1/messages/1 HTTP/1.1
Host: yourhost.dev
Cache-Control: no-cache 
```

**响应**

```
{
    "id": 1,
    "subject": "This is my subject",
    "message": "super secret information!!!",
    "name": "Jane Doe"
} 
```

你可以在这里看一下完整的源代码[。](https://github.com/sitepoint-editors/sp-halite/tree/sitepoint)

## 结论

用 Halite 加密和解密非常简单。这并不意味着上面的例子是超级安全的。仍然有很大的改进潜力，Halite 还有其他有趣的特性，可能会在以后的文章中展示。请记住，不要将上述实现视为生产就绪，这只是出于教育目的。

用 PHP 怎么做加密/解密？你用过石盐吗？让我们知道！

## 分享这篇文章