# 如何搜索安全加密的数据库字段

> 原文：<https://www.sitepoint.com/how-to-search-on-securely-encrypted-database-fields/>

*这篇文章最初发表在 [ParagonIE 博客](https://paragonie.com/blog/2017/05/building-searchable-encrypted-databases-with-php-and-sql)上，经他们允许后转载于此。*

* * *

[![Security Image](img/d3865852c6c999f1386fb0489e93eed0.png)](https://www.sitepoint.com/wp-content/uploads/2017/05/1495982600security.jpg)

我们(ParagonIE)被问及同样的问题很多(或者是它的一些混合)。

这个问题不时出现在[开源加密库的错误追踪器](https://github.com/defuse/php-encryption/issues/348#)中。这是在[我在 B-Sides Orlando](https://github.com/paragonie/bsidesorl-2017) (标题为*构建怪异问题的可防御解决方案*)的演讲中涉及的“怪异问题”之一，我们之前在我们的白皮书的[中专门有一小部分是关于它的。](https://paragonie.com/white-paper/2015-xsecure-php-data-encryption#index-encrypted-information)

你知道如何[搜索数据库字段](https://www.sitepoint.com/premium/screencasts/improving-your-ability-to-search-for-and-update-mysql-records)，但问题是，**我们如何安全地加密数据库字段，但仍然在搜索查询中使用这些字段？**

我们的安全解决方案相当简单，但是在大多数团队提出这个问题和发现我们简单的解决方案之间的道路充满了危险:糟糕的设计、学术研究项目、误导性营销和糟糕的[威胁建模](https://adamcaudill.com/2016/07/20/threat-modeling-for-applications/)。

如果你很急，请随意[跳到解决方案](#solution)。

## 走向可搜索加密

让我们从一个简单的场景开始(这可能与许多地方政府或医疗保健应用程序特别相关):

*   您正在构建一个需要从用户那里收集社会保险号(SSN)的新系统。
*   法规和常识都规定用户的 SSN 应该在静态时加密。
*   鉴于他们的 SSN，工作人员将需要能够查找用户的账户。

让我们先用这个问题显而易见的答案来探究一下其中的破绽。

## 不可靠(或不明智)的答案

### 非随机化加密

对大多数团队(尤其是没有安全或加密专家的团队)来说，最明显的答案是这样做:

```
<?php
class InsecureExampleOne
{
    protected $db;
    protected $key;

    public function __construct(\PDO $db, string $key = '')
    {
        $this->db = $db;
        $this->key = $key;
    }

    public function searchByValue(string $query): array
    {
        $stmt = $this->db->prepare('SELECT * FROM table WHERE column = ?');
        $stmt->execute([
            $this->insecureEncryptDoNotUse($query)
        ]);
        return $stmt->fetchAll(\PDO::FETCH_ASSOC);
    }

    protected function insecureEncryptDoNotUse(string $plaintext): string
    {
        return \bin2hex(
            \openssl_encrypt(
                $plaintext,
                'aes-128-ecb',
                $this->key,
                OPENSSL_RAW_DATA | OPENSSL_ZERO_PADDING
            )
        );
    }
} 
```

在上面的代码片段中，当用相同的密钥加密时，相同的明文总是产生相同的密文。但是 ECB 模式更令人担心的是，每一个 16 字节的块都被单独加密，这可能会产生一些非常不幸的后果，比如 T2。

从形式上看，这些结构在语义上并不安全:如果您加密一条大消息，您将看到密文中有重复的块。

为了安全起见，任何没有解密密钥的人都不能将加密与随机噪声区分开来。不安全模式包括具有静态(或空)静脉注射的 ECB 模式和 CBC 模式。

您需要非确定性加密，这意味着每条消息使用一个唯一的 nonce 或初始化向量，该向量对于给定的密钥从不重复。

### 实验性学术设计

有很多学术研究进入了诸如[同态](https://crypto.stanford.edu/~dwu4/fhe-project.html)、[揭示顺序](https://crypto.stanford.edu/~dwu4/ore-project.html)和[保持顺序](http://www.cc.gatech.edu/~aboldyre/papers/bclo.pdf)加密技术这样的主题。

尽管这项工作很有趣，但目前的设计还远远不够安全，无法在生产环境中使用。

例如，[揭示顺序的加密泄露了足够的数据来推断明文](https://eprint.iacr.org/2016/786.pdf)。

同态加密方案通常将漏洞(实际的选择密文攻击)重新包装为特征。

*   无填充 RSA 相对于乘法是同态的[。](https://gist.github.com/paragonie-scott/3ea5ea4b03eba8c66122386d0d7e9c70)
    *   如果你把一个密文乘以一个整数，你得到的明文将等于原始消息乘以同一个整数。对于未填充的 RSA 有[几种可能的攻击，这就是为什么现实世界中的 RSA 使用填充(虽然经常](https://crypto.stackexchange.com/a/20087/24405)[一种不安全的填充模式](https://framework.zend.com/security/advisory/ZF2015-10))。
*   计数器模式下的 AES 与 XOR 同态[。](https://3v4l.org/8e4J0)
    *   这就是为什么 nonce-reuse 会破坏 CTR 模式(以及一般的非 NMR 流密码)中消息的机密性。

正如我们在之前的博客文章中提到的[，当涉及到现实世界的密码学时，**没有完整性的保密性和没有保密性是一样的**。如果攻击者获得了对数据库的访问权，修改了密文，并研究了应用程序在解密时的行为，会发生什么？](https://paragonie.com/blog/2015/05/using-encryption-and-authentication-correctly)

正在进行的加密研究有可能在某一天产生一种创新的加密设计，这种设计不会取消几十年来对安全加密原语和加密协议设计的研究。然而，我们还没有到那一步，你不需要投资一个不必要的复杂的研究原型来解决这个问题。

### 不光彩的一提:解密每一行

我不指望大多数工程师不带一丝讽刺地得出这个解决方案。这里的坏主意是，因为您需要安全加密(见下文)，您唯一的求助是查询数据库中的每个密文，然后遍历它们，逐个解密它们，并在应用程序代码中执行您的搜索操作。

如果沿着这条路走下去，您的应用程序将面临拒绝服务攻击。对你的合法用户来说会很慢。这是一个愤世嫉俗者的答案，你可以做得更好，我们将在下面演示。

## 轻松实现安全的可搜索加密

让我们从一次性避免不安全/不明智一节中概述的所有问题开始:所有密文都将是一个**认证加密**方案的结果，最好带有大量随机数(由[安全随机数生成器](https://paragonie.com/blog/2016/05/how-generate-secure-random-numbers-in-various-programming-languages)生成)。

使用认证的加密方案，密文是不确定的(相同的消息和密钥，但是不同的随机数，产生不同的密文)，并且由认证标签保护。一些合适的选项包括:XSalsa20-Poly1305，XChacha20-Poly1305，以及(假设在 CAESAR 结束之前没有坏)NORX64-4-1。如果你使用 NaCl 或 libsodium，你可以在这里使用`crypto_secretbox`。

因此，我们的密文无法从随机噪声中分辨出来，并且可以抵御 T2 选择密文攻击。无聊的加密应该如此安全。

然而，这提出了一个直接的挑战:我们不能只是加密任意消息，并查询数据库以匹配密文。幸运的是，有一个巧妙的解决方法。

### 重要:威胁模型您的加密使用

在开始之前，请确保加密确实使您的数据更加安全。需要强调的是，“加密存储”并不是保护易受 SQL 注入攻击的 CRUD 应用的解决方案。解决实际问题(即[防止 SQL 注入](https://paragonie.com/blog/2015/05/preventing-sql-injection-in-php-applications-easy-and-definitive-guide))是唯一的出路。

如果加密是一种适合实现的安全控制，这意味着用于加密/解密数据的密钥对于数据库软件来说是不可访问的。在大多数情况下，将应用服务器和数据库服务器放在不同的硬件上是有意义的。

## 实现加密数据的文字搜索

> 可能的用例:存储社会安全号码，但仍然能够查询它们。

为了存储加密信息并在选择查询中仍然使用明文，我们将遵循一个我们称为**盲索引**的策略。一般的想法是在单独的列中存储明文的密钥散列(例如 HMAC)。重要的是，盲索引密钥必须不同于加密密钥，并且不为数据库服务器所知。

对于非常敏感的信息，除了简单的 HMAC 之外，您还需要使用密钥拉伸算法(PBKDF2-SHA256，scrypt，Argon2 ),将密钥作为静态 salt，以降低枚举的速度。在这两种情况下，我们都不担心离线暴力攻击，除非攻击者能够获得密钥(该密钥不能存储在数据库中)。

因此，如果您的表模式如下所示(PostgreSQL 风格):

```
CREATE TABLE humans (

    humanid BIGSERIAL PRIMARY KEY,
    first_name TEXT,
    last_name TEXT,
    ssn TEXT, /* encrypted */
    ssn_bidx TEXT /* blind index */
);
CREATE INDEX ON humans (ssn_bidx); 
```

您可以将加密的值存储在`humans.ssn`中。明文 SSN 的盲索引将进入`humans.ssn_bidx`。一个简单的实现可能如下所示:

```
<?php
/* This is not production-quality code.
 * It's optimized for readability and understanding, not security.
 */

function encryptSSN(string $ssn, string $key): string
{
    $nonce = random_bytes(24);
    $ciphertext = sodium_crypto_secretbox($ssn, $nonce, $key);
    return bin2hex($nonce . $ciphertext);
}

function decryptSSN(string $ciphertext, string $key): string
{
    $decoded = hex2bin($ciphertext);
    $nonce = mb_substr($decoded, 0, 24, '8bit');
    $cipher = mb_substr($decoded, 24, null, '8bit');
    return sodium_crypto_secretbox_open($cipher, $nonce, $key);
}

function getSSNBlindIndex(string $ssn, string $indexKey): string
{
    return bin2hex(
        sodium_crypto_pwhash(
            32,
            $ssn,
            $indexKey,
            SODIUM_CRYPTO_PWHASH_OPSLIMIT_MODERATE,
            SODIUM_CRYPTO_PWHASH_MEMLIMIT_MODERATE
        )
    );
}

function findHumanBySSN(PDO $db, string $ssn, string $indexKey): array
{
    $index = getSSNBlindIndex($ssn, $indexKey);
    $stmt = $db->prepare('SELECT * FROM humans WHERE ssn_bidx = ?');
    $stmt->execute([$index]);
    return $stmt->fetchAll(PDO::FETCH_ASSOC);
} 
```

更全面的概念验证包含在我的 B 端奥兰多 2017 演讲的[补充材料中。它是在 Creative Commons CC0 许可下发布的，对大多数人来说，这意味着与“公共领域”相同的东西。](https://github.com/paragonie/bsidesorl-2017/blob/882a3ccb56070668ca82e0d02c1baed82a8db015/src/EncryptedSearch.php#L119-L153)

### 安全性分析和限制

根据您的确切威胁模型，此解决方案留下了两个问题，在采用之前必须回答:

1.  用起来安全吗，还是像筛子一样漏数据？
2.  它的有用性有什么限制？(这个问题已经有答案了。)

在上面的例子中，假设您的加密密钥和盲索引密钥是分开的，两个密钥都存储在 web 服务器中，并且数据库服务器无法获得这些密钥，那么任何只破坏数据库服务器(而不是 web 服务器)的攻击者将只能知道几行是否共享一个社会安全号，但是*不知道*共享的 SSN 是什么。为了使索引成为可能，这种重复条目泄漏是必要的，这反过来允许从用户提供的值进行快速选择查询。

此外，如果攻击者能够在观察数据库中存储的盲索引的同时，作为应用程序的普通用户观察/更改明文，他们就可以利用这一点进行*选择明文攻击*，作为用户迭代每个可能的值，然后与结果盲索引值相关联。这在 HMAC 的情况下比在 Argon2 的情况下更实际。对于高熵值或低灵敏度值(不是 SSN)，[蛮力的物理学](https://pthree.org/2016/06/19/the-physics-of-brute-force/)可以站在我们这边。

对于这种罪犯来说，更实际的攻击是将值从一行替换到另一行，然后正常访问应用程序，这将暴露明文*，除非*使用了不同的每行密钥(例如，`hash_hmac('sha256', $rowID, $masterKey, true)`在这里甚至可以是一种有效的缓解措施，尽管其他方式更好)。这里最好的防御是使用 AEAD 模式(将主键作为附加的关联数据传递),以便将密文绑定到特定的数据库行。(这不会阻止攻击者删除数据，这是[一个更大的挑战](https://www.reddit.com/r/netsec/comments/6denka/guide_to_building_searchable_encrypted_databases/di2rlwt/?context=1)。)

与其他解决方案泄露的信息量相比，大多数应用程序的威胁模型应该会发现这是一个可以接受的权衡。只要您使用认证加密进行加密，并且使用 HMAC(用于非敏感数据的盲索引)或[密码哈希算法](https://paragonie.com/blog/2016/02/how-safely-store-password-in-2016)(用于敏感数据的盲索引)，就很容易推断出您的应用程序的安全性。

然而，它有一个非常严重的限制:它只对精确匹配有效。如果两个字符串毫无意义地不同，但总是产生不同的加密哈希，那么搜索其中一个将永远不会产生另一个。如果您需要进行更高级的查询，但是仍然希望您的解密密钥和明文值不受数据库服务器的控制，我们将不得不发挥创造性。

还值得注意的是，虽然 HMAC/Argon2 可以防止不拥有密钥的攻击者了解存储在数据库中的明文值，但它可能会泄露关于真实世界的元数据(例如，两个看似不相关的人共享一个街道地址)。

## 实现加密数据的模糊搜索

> 可能的用例:加密人们的合法姓名，并能够只搜索部分匹配。

让我们在前一节的基础上，构建一个盲索引，允许您查询数据库以获得精确匹配。

这一次，我们不是将列添加到现有的表中，而是将额外的索引值存储到一个连接表中。

```
CREATE TABLE humans (
    humanid BIGSERIAL PRIMARY KEY,
    first_name TEXT, /* encrypted */
    last_name TEXT, /* encrypted */
    ssn TEXT, /* encrypted */
);
CREATE TABLE humans_filters (
    filterid BIGSERIAL PRIMARY KEY,
    humanid BIGINT REFERENCES humans (humanid),
    filter_label TEXT,
    filter_value TEXT
);
/* Creates an index on the pair. If your SQL expert overrules this, feel free to omit it. */
CREATE INDEX ON humans_filters (filter_label, filter_value); 
```

这种变化的原因是为了规范我们的数据结构。您可以通过向现有的表中添加列来解决问题，但是这很可能会变得混乱。

下一个变化是，我们将为我们需要的每种不同的查询(每种查询都有自己的键)在每一列存储一个单独的、不同的盲索引。例如:

*   需要忽略空白的不区分大小写的查找吗？
    *   存储一个`preg_replace('/[^a-z]/', '', strtolower($value))`的盲索引。
*   需要查询他们姓氏的第一个字母？
    *   存储一个`strtolower(mb_substr($lastName, 0, 1, $locale))`的盲索引。
*   需要匹配“存在以这个字母，结束以那个字母”？
    *   存储一个`strtolower($string[0] . $string[-1])`的盲索引。
*   需要查询自己姓氏的前三个字母和名字的第一个字母？
    *   你猜对了！基于部分数据建立另一个索引。

每个索引都需要有一个不同的键，应该尽力防止明文子集的盲索引将真正的明文值泄露给擅长纵横字谜的罪犯。只为重要的业务需求创建索引，并积极地记录对应用程序这些部分的访问。

### 用记忆换取时间

到目前为止，所有的设计主张都倾向于允许开发人员编写经过仔细考虑的 SELECT 查询，同时尽量减少调用解密子例程的次数。一般来说，这是火车停下来的地方，也是大多数人的目标实现的地方。

但是，在某些情况下，如果这意味着节省大量磁盘空间，搜索查询中轻微的性能损失是可以接受的。

这里的技巧很简单:将你的盲索引截短为 16、32 或 64 位，并将它们视为一个[布隆过滤器](https://en.wikipedia.org/wiki/Bloom_filter):

*   如果查询中涉及的盲索引匹配给定的行，那么数据*可能*匹配。
    *   您的应用程序代码将需要对每个候选行执行解密，然后只提供实际的匹配。
*   如果查询中涉及的盲索引与给定的行不匹配，那么数据*肯定不*匹配。

如果您的数据库服务器最终会更有效地存储这些值，那么将这些值从字符串转换为整数也是值得的。

## 结论

我希望我已经充分地展示了不仅可以构建一个使用安全加密同时允许快速查询的系统(对特权攻击者的信息泄漏最小)，而且可以简单地构建这样一个系统，使用现代密码术库提供的组件，只需很少的粘合剂。

如果您有兴趣在您的软件中实现加密数据库存储，我们很乐意为您和您的公司提供我们的咨询服务。如果你感兴趣，请联系 ParagonIE 。

## 分享这篇文章