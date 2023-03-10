# 探索 Ruby 中的密码学基础

> 原文：<https://www.sitepoint.com/exploring-cryptography-fundamentals-in-ruby/>

![](img/13dcca82c6d3e94c20ffa8b0195506eb.png)

全世界都在使用加密技术来保护数据免受窥探。如果你想保护你自己的数据，你应该熟悉这个话题。

为了帮助您入门，我将向您展示您需要了解的主要概念&算法，并且我将包括一些 Ruby 示例，以便您可以看到这些概念的实际应用。

## 原始密码

在计算机出现之前，密码学就已经在使用了。例如，你可能听说过[凯撒密码](http://www.blackbytes.info/2015/03/caesar-cipher-in-ruby/)。

在凯撒密码中，字母被移动了 X 个位置。位置的数量是恢复原始消息所需的关键。

用计算机破解这个密码很简单，所以我们今天使用的算法利用了一些数学问题(如质因数分解),这些问题即使对于世界上最强大的计算机来说也很难解决。

现代密码算法可以分为三组:

*   对称密码
*   不对称密码
*   哈希函数

## 对称密码

对称算法使用一个密钥来编码和解码数据。这种算法可以用来保护计算机上的重要文件。

请注意，您不应该使用它来存储密码，因为如果您有密钥，对称算法很容易被逆转(如果您想在没有人工交互的情况下使用加密数据，密钥需要在系统上)。你想要用来存储密码的东西被称为“哈希函数”(本文后面会讲到)。

以下是一些流行的对称算法:

*   是吗
*   三重 DES
*   俄歇电子能谱
*   河豚

您可以使用`OpenSSL`库来处理 Ruby 中的许多对称密码。

**举例**:

```
require 'openssl'

cipher = OpenSSL::Cipher.new('aes256')
cipher.encrypt

key = cipher.random_key
iv  = cipher.random_iv

encrypted = cipher.update('test') + cipher.final

# You need to save both the IV (initialization vector) + the key 
```

如果想用自己的密钥而不是用`OpenSSL`生成的密钥，需要使用类似`PBKDF#2`的密码派生函数(基于密码的密钥派生函数 2)。你可以这样做:

```
require 'openssl'

cipher = OpenSSL::Cipher.new('aes256')
cipher.encrypt

iv  = cipher.random_iv

# Password derivation
salt = OpenSSL::Random.random_bytes(16)
key  = OpenSSL::PKCS5.pbkdf2_hmac_sha1('password', salt, 20_000, cipher.key_len)

cipher.key = key

encrypted = cipher.update('test') + cipher.final 
```

对于解密，您只需将`cipher.encrypt`更改为`cipher.decrypt` &，提供加密期间生成的 salt 和 IV。

## 不对称密码

非对称算法使用两个密钥:一个公钥和一个私钥。私钥用于解码和签名消息。公钥用于编码和验证。

一个实际应用是 SSL 连接中的初始交换。互联网是一个充满敌意的网络，客户端和服务器都需要就共享密钥达成一致，以便能够保护它们之间的通信。

问题是，这个密钥不能以明文形式发送，因为任何人都可以截获它，并用它来窥探加密的通信。

解决方案是使用非对称密码，如 RSA，或专用算法，如 DH ( [Diffie-Hellman 密钥交换](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange))。商定密钥后，安全 SSL 会话可以开始使用 AES 之类的常规对称密码。

您可以使用`openssl`命令生成一对公钥/私钥。像这样:

```
openssl genpkey -algorithm RSA -out private_key.pem -pkeyopt rsa_keygen_bits:2048
openssl rsa -pubout -in private_key.pem -out public_key.pem 
```

您也可以从 Ruby 生成一个密钥对:

```
key = OpenSSL::PKey::RSA.new(2048)

File.open('private_key.pem', 'w') { |f| f.write(key.to_pem) }
File.open('public_key.pem', 'w')  { |f| f.write(key.public_key.to_pem) } 
```

## 哈希函数

哈希函数接收输入并返回固定大小的输出。在加密哈希函数中，输出通常表示为十六进制字符串。

需要注意的是，散列函数并不意味着是反向的，它是一个单向函数。这就是它非常适合存储密码的原因。

哈希函数也可以用来比较两个文件是否相同。这是通过散列文件的内容&比较结果散列来完成的。

一些常见的哈希函数有:

*   讯息摘要 5
*   SHA1
*   SHA2
*   bcrypt

Ruby 标准库包含了`Digest`模块来帮助你生成前 3 个。

**下面是一个例子**:

```
require 'digest'

Digest::MD5.hexdigest('test')
# "098f6bcd4621d373cade4e832627b4f6"

Digest::SHA1.hexdigest('test')
# "a94a8fe5ccb19ba61c4c0873d391e987982fbbd3" 
```

相同的输入将总是产生相同的输出，但也有可能两个或更多的输入映射到相同的输出，这被称为“哈希冲突”。像 MD5 这样的低位函数更容易发生冲突，这就是为什么最好使用更强的函数的一个原因。

下面是`Digest`的文档:http://ruby-doc . org/STD lib-2 . 2 . 0/lib doc/digest/rdoc/digest . html

另一个选择是使用`bcrypt`(这是 Devise 所使用的):

```
require 'bcrypt'

BCrypt::Password.create("test")
# "$2a$10$zccXimeuNdA083RSDFy7VeVgs538d5XRQurRbqjdEd3h0kU7Q0j2e" 
```

使用`bcrypt`相对于 SHA-1 的优势在于`bcrypt`更慢，这是一件好事，因为它使生成的哈希更能抵御暴力攻击。

这是 https://rubygems.org/gems/bcrypt/宝石的文件

## 加入一点盐

哈希函数的一个问题是，它可以预先计算许多常见单词和密码的哈希值，这使得“破解”一组哈希比纯粹的暴力破解要快得多。事实上，你可以下载预先计算好的称为“彩虹表”的哈希表。

解决方案是使用我们称之为“盐”的值使你的所有散列都是唯一的。该值的用法如下:

```
sha1(salt + password) 
```

salt 与哈希密码一起保存，因此可以用于登录尝试。在`bcrypt`的情况下，salt 由哈希输出中包含的算法&生成。

> **注意**:如果你使用像 Devise 这样的东西，这已经为你做好了，但是知道这些还是好的:)

还有一点:确保每个密码都使用独特的 salt，否则你将无法获得 salt 的全部好处。

## 结论

密码术是一个复杂的世界，但是如果你处理重要的数据，你想要熟悉它。一句警告:不要试图耍小聪明&发明你自己的密码，你可能会搬起石头砸自己的脚。

## 分享这篇文章