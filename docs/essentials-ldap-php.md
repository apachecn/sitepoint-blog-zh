# 用 PHP 实现 LDAP 的要点

> 原文：<https://www.sitepoint.com/essentials-ldap-php/>

曾经想要一种简单的方法来存储地址簿样式的信息和网络信息，实际上就在任何种类的有序信息旁边吗？

如果是这样的话，那么有一项自 1993 年以来就存在的技术，尽管没有像 *Node.js* 和 *Go* 这样的技术那样酷，但它可以让你做到这一点。**它叫 LDAP！**

## 什么是 LDAP？

LDAP 是[轻量级目录访问协议](http://en.wikipedia.org/wiki/Lightweight_Directory_Access_Protocol)的缩写，由*蒂姆·霍维斯*、*史蒂夫·基尔*、*科林·罗宾斯*和*翁一荣*于 1993 年左右在密西根大学开发。

简而言之，LDAP 是名为 [X.500](http://en.wikipedia.org/wiki/X.500) 的早期协议的互联网版本，该协议由国际电信联盟(ITU)在 80 年代开发，用于管理电话目录和目录服务。

虽然 LDAP 在技术上指的是协议，但这个名称通常也适用于客户机和服务器。如果有帮助的话，你可以把它想象成 SQL 是用于数据库服务器的；它是用于与 LDAP 服务器交互的语言。

周围有许多支持 LDAP 的服务器，其中最常见的是[微软的 ActiveDirectory](http://msdn.microsoft.com/en-us/library/aa746492%28v=vs.85%29.aspx)；自从 Windows 2000 发布以来，这种技术已经渗透到他们的产品系列中。

还有一种开源选择，我们将在本系列文章中使用，称为 [OpenLDAP](http://www.openldap.org) 。它对您使用的模式或存储的信息不做任何假设。

在本系列的第一部分，我将:

1.  带您了解设置 OpenLDAP 的基础知识
2.  向您展示如何加载一组记录
3.  向您展示如何连接到它并执行一些基本操作

## 术语

在此之前，我们需要了解一些术语。继续 SQL 类比，有几个术语您需要知道，并且我将在整个系列中使用，您可以在下表中找到它们。

| LDAP 术语 | 描述 |
| --- | --- |
| （同 down）向下的 | dn 或识别名是记录的唯一标识符。这很像关系数据库中的主键。 |
| 目录模式(或只是模式) | 在 LDAP 目录中，条目值由目录模式控制。目录模式是一组关于目录信息结构的定义和约束。 |
| 进入 | 条目很像数据库中的记录，包含存储条目数据的属性。 |
| 属性 | 属性很像数据库中关联数组或列中的元素。它指定了可以为该属性存储的信息类型，以及其他标准，如排序和搜索规则、区分大小写等。 |
| 通信网络（Communicating Net 的缩写） | cn 是通用名的缩写。“约翰·史密斯”就是一个例子 |
| 锡 | sn 是姓氏的简称。 |

术语相当复杂，所以我无法在这里全部涵盖。但是希望这些基础知识足以让你开始。

欲了解更多详细信息，请查看奥赖利的本指南，或[维基百科上的 LDAP 条目](http://en.wikipedia.org/wiki/Lightweight_Directory_Access_Protocol#Schema)。

## 安装 LDAP 服务器

我从来没有发现 OpenLDAP 的安装和配置特别简单，网上的许多信息可能会误导人，或者是针对不同版本的服务器。这里是我最好的，最简洁的步骤，基于使用基于 Debian 的服务器。

首先，通过运行以下命令安装核心服务器和实用程序:

```
sudo apt-get install slapd ldap-utils
```

这些命令将询问您一系列问题，安装服务器，并将其设置为在引导时启动。完成后，运行以下命令，这将有助于我们更好地配置安装:

```
dpkg-reconfigure slapd
```

这将提出一系列问题，以下是回答这些问题的指南:

*   **省略 OpenLDAP 服务器配置？**否
*   **DNS 域名:** homestead.localdomain
*   **贵公司名称:** …随便什么&公司
*   **管理员密码:**
*   **确认密码:**
*   好
*   BDB
*   清除 slapd 时，是否希望删除您的数据库？否
*   **移动旧数据库？**是的
*   **允许 LDAPv2 协议吗？**否

## 验证安装

完成后，让我们通过运行以下命令来快速验证一切正常:

```
ldapsearch -x -b dc=homestead,dc=localdomain
```

您应该不会收到错误，但如果是这样，请确保 OpenLDAP 正在运行；您可以通过运行以下命令来实现这一点:

```
sudo netstat -tlnp | grep slapd
```

您应该会看到如下所示的输出(为便于阅读而进行了格式化):

```
tcp    0   0 0.0.0.0:389  0.0.0.0:*    LISTEN    6556/slapd      
tcp6   0   0 :::389       :::*         LISTEN    6556/slapd
```

## 推广数据库

现在服务器已经设置好了，我们需要向它加载数据。创建一个名为`users.ldif`的新文件，并在其中添加以下记录:

```
dn: cn=Sheldon Cooper,ou=People,dc=homestead,dc=localdomain
cn: Sheldon Cooper
objectClass: person
objectClass: inetOrgPerson
sn: Cooper

dn: cn=Leonard Hofstadter,ou=People,dc=homestead,dc=localdomain
cn: Leonard Hofstadter
objectClass: person
objectClass: inetOrgPerson
sn: Hofstadter

dn: cn=Howard Wolowitz,ou=People,dc=homestead,dc=localdomain
cn: Howard Wolowitz
objectClass: person
objectClass: inetOrgPerson
sn: Wolowitz

dn: cn=Rajesh Koothrappali,ou=People,dc=homestead,dc=localdomain
cn: Rasjesh Koothrappali
objectClass: person
objectClass: inetOrgPerson
sn: Koothrappali
```

保存文件后，运行以下命令将信息加载到数据库中:

```
ldapadd -x -W -D "cn=admin,dc=homestead,dc=localdomain" -f users.ldif
```

这将提示您输入之前设置的密码。输入它，您应该会看到如下所示的输出:

```
adding new entry "cn=Sheldon Cooper,ou=People,dc=homestead,dc=localdomain"

adding new entry "cn=Leonard Hofstadter,ou=People,dc=homestead,dc=localdomain"

adding new entry "cn=Howard Wolowitz,ou=People,dc=homestead,dc=localdomain"

adding new entry "cn=Rajesh Koothrappali,ou=People,dc=homestead,dc=localdomain"
```

### 验证记录是否存在

现在让我们快速检查一下记录是否可用。从命令行运行:

```
ldapsearch -x -b "dc=homestead,dc=localdomain" -s sub "objectclass=*"
```

这应该会显示类似于下面的输出，出于可读性的考虑，我对其进行了截断:

```
# extended LDIF
#
# LDAPv3
# base <dc=homestead,dc=localdomain> with scope subtree
# filter: objectclass=*
# requesting: ALL
#

# homestead.localdomain
dn: dc=homestead,dc=localdomain
objectClass: top
objectClass: dcObject
objectClass: organization
o: homestead
dc: homestead
```

## 与 PHP 交互

完成所有这些步骤后，我们现在就可以使用 PHP 来查询服务器了。为了简单起见，我们使用来自 Zend Framework 2 的 Zend-Ldap 组件。

有许多可用的 PHP LDAP 库，但这是我发现最有效和最直接的一个。诚然，我是一个 Zend 框架的传播者，但老实说，这是我发现的最简单的库。

为了处理这种依赖性，就像现在几乎所有的 PHP 项目一样，我们将使用 [Composer](https://www.sitepoint.com/php-dependency-management-with-composer/) 使其可用。在项目目录中，创建一个 composer.json 文件，如下所示。

```
{
    "require": {
        "php": ">=5.3.0",
        "zendframework/zend-ldap": "2.3.*@dev"
    }
}
```

之后，运行`composer install`创建供应商目录并引入依赖项。

## 连接到 LDAP 服务器

完成后，在项目目录的根目录下创建一个名为`index.php`的新文件；在那里添加下面的代码:

```
<?php
require 'vendor/autoload.php';

$baseDn = 'dc=homestead,dc=localdomain';
$options = array(
    'host' => '192.168.10.10',
    'password' => 'homestead',
    'bindRequiresDn' => true,
    'baseDn' => 'dc=homestead,dc=localdomain',
    'username' => "cn=admin,$baseDn"
);
$ldap = new Zend\Ldap\Ldap($options);
$ldap->bind();
```

这将首先通过 composer 生成的自动加载文件使`Zend\Ldap`可用。接下来，我定义了一个变量`$baseDn`，以使代码在本文中更具可读性。

接下来，我创建了一个名为`$options`的数组，它存储了我们将用于初始化`Zend\Ldap\Ldap`对象的配置设置。

在其中，我指定了*主机名*、*口令*、 *basedn* 和*用户名*。我们可以跳过用户名和密码，但是因为我们将执行认证操作，所以现在添加它们是最简单的。

初始化新的`Zend\Ldap\Ldap`对象后，我调用 bind 方法来连接服务器。

## 搜索数据库

现在让我们在 LDAP 服务器上执行第一个也是最简单的操作:搜索。下面的代码将搜索`ou=People,dc=homestead,dc=localdomain`中的每一条记录，这将返回我们之前加载的四条记录。

第一个参数`(objectclass=*)`指定了一个类似 SQL 的过滤器。这相当于运行一个`SELECT *`。最后一个参数执行查找所有记录的搜索。

有许多不同的搜索类型，我们将在下一篇文章中介绍。目前，这种类型足以满足我们的需求。

```
$result = $ldap->search(
   '(objectclass=*)',
   "ou=People,$baseDn",
   Zend\Ldap\Ldap::SEARCH_SCOPE_SUB
);
```

接下来，我们将以两种方式迭代记录。首先，我们将在`$result`对象上调用`toArray()`方法，并将其传递给`json_encode`。我这样做是为了简单地显示所有可用的信息。

```
print json_encode($result->toArray());
```

或者，我们可以使用如下 foreach 迭代数据集。在这个例子中，我们已经显示了每条记录的`dn`和`cn`元素。

```
foreach ($result as $item) {
    echo $item["dn"] . ': ' . $item['cn'][0] . '<br />';
}
```

## 更新条目

既然我们已经看了基本的记录搜索和迭代，那么让我们看一下更新记录。这需要三个步骤:

1.  获取记录
2.  更新现有属性或设置新属性
3.  将记录保存回 LDAP 服务器

```
$hm = $ldap->getEntry(
    "cn=Rajesh Koothrappali,ou=People,$baseDn"
);
Zend\Ldap\Attribute::setAttribute(
    $hm, 'mail', 'koothrappalir@homestead.localdomain'
);
$ldap->update(
    "cn=Rajesh Koothrappali,ou=People,$baseDn", $hm
);
```

在上面的代码中，我们通过调用 getEntry 方法检索了一个条目，并传入了记录`dn`。我们接下来调用`setAttribute()`方法，指定记录对象、我们想要设置的属性，然后是属性值。

最后，我们调用了`update()`方法，记录中的激情`dn`和记录对象。如果一切顺利，记录将被更新。

## 删除条目

现在我们可以搜索和更新记录了，让我们通过逐步删除记录来结束。为此，我们调用 delete 方法，传入我们想要删除的记录的`dn`。

由于操作可能会失败，在本例中，我将调用包装在一个 try/catch 块中，如果抛出了一个`LdapException`，它将捕获并打印出失败的原因。

引发异常的原因可能有很多，比如记录不存在，以及我们已经验证的用户没有足够的权限。

```
try {
    $ldap->delete("cn=Hans Meier,ou=People,$baseDn");
} catch (\Zend\Ldap\Exception\LdapException $e) {
    print $e->getMessage();
}
```

## 包扎

这就是如何在 PHP 中设置 LDAP 服务器——特别是 OpenLDAP——并与之交互。我希望你喜欢这个快速浏览如何做到这一点。在下一篇文章中，我们将通过以下方式更深入地探索 LDAP:

*   执行更复杂的搜索
*   插入记录
*   移动记录
*   建立安全连接

如果你想了解更多关于我们今天所学内容的信息，在延伸阅读部分有很多链接，可以满足你的好奇心。

## 进一步阅读

*   [https://www . digital ocean . com/community/tutorials/how-to-install-and-configure-a-basic-LDAP-server-on-an-Ubuntu-12-04-VPS](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-a-basic-ldap-server-on-an-ubuntu-12-04-vps)
*   [http://www . Linux . com/learn/tutorials/376144-manage-LDAP-from-the-command-line](http://www.linux.com/learn/tutorials/376144-manage-ldap-from-the-command-line)
*   [http://charithmahawatta . blogspot . de/2011/03/LDAP bind-unable-to-bind-to-server . html](http://charithmahawatta.blogspot.de/2011/03/ldapbind-unable-to-bind-to-server.html)
*   [http://www . debuntu . org/how-to-set-up-a-LDAP-server-and-its-clients/](http://www.debuntu.org/how-to-set-up-a-ldap-server-and-its-clients/)
*   [http://www . debuntu . org/how-to-set-up-a-LDAP-server-and-its-clients/](http://www.debuntu.org/how-to-set-up-a-ldap-server-and-its-clients/)
*   [http://www . Linux . com/learn/tutorials/376144-manage-LDAP-from-the-command-line](http://www.linux.com/learn/tutorials/376144-manage-ldap-from-the-command-line)
*   [http://framework . Zend . com/manual/2.3/en/modules/Zend . LDAP . usage . html # updating-the-LDAP](http://framework.zend.com/manual/2.3/en/modules/zend.ldap.usage.html#updating-the-ldap)
*   [http://server fault . com/questions/295200/LDAP-attribute-given name-is-not-allowed](http://serverfault.com/questions/295200/ldap-attribute-givenname-is-not-allowed)
*   [http://www.zytrax.com/books/ldap/ape/](http://www.zytrax.com/books/ldap/ape/)
*   [http://www.tldp.org/HOWTO/LDAP-HOWTO/utilities.html](http://www.tldp.org/HOWTO/LDAP-HOWTO/utilities.html)
*   [http://en . Wikipedia . org/wiki/Lightweight _ Directory _ Access _ Protocol](http://en.wikipedia.org/wiki/Lightweight_Directory_Access_Protocol)
    https://github.com/zendframework/Component_ZendLdap

## 分享这篇文章