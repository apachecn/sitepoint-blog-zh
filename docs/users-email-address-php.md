# 使用 PHP 验证用户的电子邮件地址

> 原文：<https://www.sitepoint.com/users-email-address-php/>

我们都目睹了这种事情的发生。

你在你的网站上放了一个注册页面，希望访问者会给你留下他们的电子邮件地址，这样当你有新产品出售时，你就可以和他们保持联系。或者他们可能感兴趣的新教程。或者你想给他们发送一些“来自我们与之保持战略关系的经过仔细筛选的第三方的信息”或者在你给他们你花了两个月完成的有价值的白皮书之前，你想要一些回报。

不管是什么原因，您很高兴地构建了您的注册页面，建立了一个数据库表来跟踪收到的电子邮件地址，并将其发布到现场。果然，注册开始进来了。

去 mickey@mouse.com。还有 donald@duck.com。还有 emailthis@hahaha.com。你明白了吧——用户在根本不存在的域名上用伪造的电子邮件地址注册。你不仅会把邮件发送到不存在的地址，而且它们会把你的数据库搞得乱七八糟，还会引起维护上的麻烦，因为它们需要被清理掉。

##### 确保它们至少是真实的

帮助解决这个问题的一个方法是确保用户的电子邮件地址实际上对应于一个真实的电子邮件域。使用 PHP，你可以检查域名注册记录，看看用户提交给你的站点的域名是否真实。为此，我们将使用 PHP 的`checkdnsrr`函数。

`checkdnsrr`函数查找给定类型和给定主机的 dns 记录。它的格式如下:

```
int checkdnsrr(string host [,string type]);
```

这个 PHP 函数检查给定主机的 DNS 记录，看是否有任何指定类型的记录。注意，type 参数是可选的，如果您不提供它，那么 type 默认为“MX”(这意味着邮件交换)。如果找到任何记录，该函数返回`TRUE`。否则返回`FALSE`。

要使用这个函数，您需要向它提交一个潜在的电子邮件地址并检查结果，如下所示:

```
// take a given email address and split it into the  

username and domain. 

list($userName, $mailDomain) = split("@", $email); 

if (checkdnsrr($mailDomain, "MX")) { 

  // this is a valid email domain! 

} 

else { 

  // this email domain doesn't exist! bad dog! no biscuit! 

} 
```

上面的代码采用“username@emaildomain.com”形式的字符串，并检查域是否真实。首先，代码调用`split()`函数将电子邮件字符串分成“username”和“emaildomain.com ”,因为我们只对域感兴趣。

一旦我们得到了域，代码调用`checkdnsrr`，用域字符串和“MX”作为参数。第二个参数告诉`checkdnsrr`要查找什么类型的 DNS 记录。因为我们只对给定的域是否能处理电子邮件感兴趣，所以我们使用“MX”参数，意思是“寻找邮件交换记录”。

如果`checkdnsrr`函数返回`TRUE`，那么我们知道我们得到了一个有效的电子邮件域(但是*不一定是*一个有效的用户名)。如果函数返回`FALSE`，那么给定的电子邮件域无效。

##### 抓住你了。–`checkdnsrr`还不支持 Windows

然而，如果你在 Windows 服务器上使用 PHP，有一个小问题。在 Windows 平台上没有实现`checkdnsrr`函数，所以如果你要在基于 Windows 的机器上部署这段代码，你需要自己做一些额外的工作。

解决这个问题的方法是编写自己版本的`checkdnsrr`。我们称我们的版本为`myCheckDNSRR`，代码如下:

```
function myCheckDNSRR($hostName, $recType = '') 

{ 

  if(!empty($hostName)) { 

    if( $recType == '' ) $recType = "MX"; 

    exec("nslookup -type=$recType $hostName", $result); 

    // check each line to find the one that starts with the host 

    // name. If it exists then the function succeeded. 

    foreach ($result as $line) { 

      if(eregi("^$hostName",$line)) { 

        return true; 

      } 

    } 

    // otherwise there was no mail handler for the domain 

    return false; 

  } 

  return false; 

}
```

我们版本的`checkdnsrr`函数通过利用 Windows 中可用的系统调用 nslookup 来工作，它执行本质上相同的功能。为了调用`nslookup`函数，我们的代码使用 PHP 的`exec`函数，它执行一个系统命令。它在`$result`参数中以字符串数组的形式返回命令的结果。

当`nslookup`函数成功找到给定域的一个条目时，输出如下所示:

```
Server: o1-sjc-ns1.o1.com 

Address: 66.81.7.158 

joemarini.com MX preference = 0, mail exchanger = smtp.joemarini.com
```

为了确定域的邮件处理程序是否存在，该函数循环遍历输出的每一行，搜索以给定主机名开始的行。如果找到这样一行，那么函数返回`TRUE`，否则返回`FALSE`。

##### 结论

虽然没有万无一失的方法来确保用户不会给你一个完全伪造的电子邮件地址，但你至少可以通过确保你的网站上给出的电子邮件地址至少对应于一个真实的域来帮助减少这个问题。使用 PHP 的`checkdnsrr`功能，在保存用户的电子邮件地址之前，你可以查找给定域名的注册记录，看看它是否是一个真实的域名。

[下载本文的代码](https://www.sitepoint.com/examples/emailverify/verifyemail.zip)。

## 分享这篇文章