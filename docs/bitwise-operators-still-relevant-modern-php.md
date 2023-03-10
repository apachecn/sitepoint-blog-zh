# 在现代 PHP 中，按位运算符仍然有用吗？

> 原文：<https://www.sitepoint.com/bitwise-operators-still-relevant-modern-php/>

很多人可能在读到这个标题时挠头了。“比特什么？”

在本文中，我们将看看什么是按位运算符，以及它们的使用在当今的计算时代是否仍然相关。

![Ones and zeros stock picture](img/4588ff34ffbaec210d5c159ab4eedb02.png)

## 示例使用案例

这里的列出了[位运算符，但是为了更好地理解这个例子，我们将只关注其中一个:位运算符*和* ( `&`)。一个例子让我眼前一亮。这就是我们要做的——直接看一个例子。](http://php.net/manual/en/language.operators.bitwise.php)

假设您有一个网站，给定的用户可以拥有特定的权限。比如像 SitePoint 这样的杂志:

*   作者可以编写草稿，编辑他们的个人资料。
*   除此之外，编辑还可以编辑 CRUD 草稿和完成的文章，以及 CRUD 作者简介。
*   除此之外，管理员还可以添加管理员权限。

因为一个用户可以有多个权限，所以有几种方法可以在数据库和使用它的系统中定义权限。

### 双重连接

添加角色、添加权限、将权限附加到连接表中的角色，然后创建另一个连接表并将一些角色绑定到一些用户。

这种方法创建了四个额外的表:

*   许可
*   角色
*   权限角色
*   角色用户

相当多的开销。想象一下，你必须编辑这些内容，或者定期将它们列在应用程序中的一些经常访问的列表中。只有大量缓存才能避免这个应用程序在高负载下崩溃。

不过，一个优点是，通过用复杂的权限很好地定义角色，您只需要将用户绑定到角色中，这样就很好了——这使得连接表变得又轻又快。

### 单一连接

添加权限，添加一个连接表，给一些用户附加一些权限

这种方法创建了两个额外的表:

*   许可
*   权限用户

与前面的例子相比，开销要小得多，但是在连接表中有更多的条目，因为一个用户可以有很多权限(仅用于起草的 CRUD 本身就有 4 个权限)。有很多用户和很多权限，这个表会很快变得很重。

### 纵队仓皇逃窜

为每个权限在 users 表中添加一列，然后使其数据类型为 tinyint(1)(基本上是一个[布尔值](https://www.sitepoint.com/boolean-data-type/))来检查权限是“开”还是“关”。

为用户设置权限将如下所示:

```
UPDATE `users` SET `editProfile` = 1, `deleteProfile` = 0, `createDraft` = 1, `publishDraft` = 0 ... WHERE `id` = 5 
```

这种方法没有添加额外的表，但是不必要地将表扩展成巨大的宽度，并且每次添加新的权限时都需要修改数据库。当您知道在可预见的将来您最多有两三个权限时，这是一个很好的方法，但是不应该用于任何更多的事情。

但是，因为从远处看时，列的列表类似于一个二进制数(1010)，所以这种方法是一种很好的 segway 到另一个…

## 逐位方法

在我们深入研究这种方法之前，让我们先来上一堂二进制速成课。

### 二进制数

所有计算机都以二进制形式存储数据:0 或 1。所以，数字 14 实际上存储为:1110。为什么

计算二进制数的值时，从右向左计算，就像实数一样。所以数字 1337 意味着:

*   1 x 7
*   + 3 x 10
*   + 3 x 100
*   + 1 x 1000

因为十进制系统(以 10 为基数)中的每个数字都要乘以 10。第一个是 1，第二个是 10，第三个是 100，第四个是 1000，以此类推。

在二进制中，基数是 2，所以每个数字都要乘以 2。因此，数字 1110 是:

*   0 x 1
*   + 1 x 2
*   + 1 x 4
*   + 1 x 8

那就是 2 + 4 + 8，也就是 14。

是的，把二进制数转换成十进制数就是这么简单。

因此，当我们查看 1010 之前的权限列时，也可以将它看作是以二进制形式书写的数字 10。嗯，也许我们找到了些什么。

如果我们的权限为 1010，这意味着第 2 位和第 4 位被设置，而第 1 位和第 3 位没有设置(因为它们是 0)。

在二进制的说法中，我们实际上说第 0 和第 2 位没有设置，因为它们是从 0 开始计数的，就像数组一样。这是因为它们的序数(1、2、3)对应于它们的指数。第 0 位实际上是 2 的 0 次幂(2^0 ),等于 1。第一位是 2 的 1 次幂(2^1)，即 2。第二个是 2 的平方(2^2 ),等于 4，等等。这样就很容易记住了。

那么这对我们有什么帮助呢？

### 逐位方法

嗯，通过从远处观察权限，我们可以用一个二进制数同时表示所有列的状态。如果我们可以用一个二进制数一次表示所有的列，这意味着当转换成十进制数时，我们也可以用一个整数来表示它！

如果我们有一个包含值`14`的单个`permissions`列，我们现在会知道这实际上是`1110`，并且我们会知道我们有四分之三的权限！但是四个中的哪三个？

想象下面的权限映射:

| 更改权限 | 配置文件创建 | 个人资料编辑 | 简档删除 | 草稿创建 | 草稿编辑 | 草稿删除 | 草稿发布 | 完成编辑 | 完成删除 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Five hundred and twelve | Two hundred and fifty-six | One hundred and twenty-eight | Sixty-four | Thirty-two | Sixteen | eight | four | Two | one |

二进制中的数字 14 是 1110，但是左边的零的数量无关紧要，所以我们可以填充它，直到达到表中的权限数:0000001110。这仍然是 14，仅代表上表中的权限。实际上，0000001110 === 1110。

据此，我们看到权限为`14`的账户拥有权限:`DRAFT_DELETE`、`DRAFT_PUBLISH`、`FINISHED_EDIT`。当然，这并不完全代表真实世界的权限设置，但这只是一个例子，通过它我们可以推断，如果一个人拥有 11111111111，他将拥有所有的权限(可能是一个管理员用户)。在十进制中，这是 1023。因此，在`permissions`列中值为`1023`的人拥有所有权限。

但是我们如何在代码中检查这一点呢？换句话说，我们如何知道一个权限的位是设置为(1)还是没有设置为(0)，特别是当一个数字存储为十进制而不是二进制时？

这就是按位运算符的用途——尤其是单个“与”符号`&`，也称为*按位“与”*。

MySQL 是这样支持它的:

```
SELECT * FROM user WHERE id = 5 AND 512 & permissions 
```

这字面上翻译为“选择 ID 为 5 并且列`permissions`的 *512 位*设置为 1 的所有用户”。您可以通过更改其他位的值来检查它们:256、128、64、32、16、8、4、2 或 1。

* * *

### [可选]“让我们了解技术”旁注

如果你不想知道这个操作符或类似的操作符是如何工作的，而只是对继续这个例子感兴趣，就跳过这一部分。

当我们说`AND 512 & permissions`时，我们在寻找后面的部分，并且是真的，因为这就是 SQL 查询的操作方式——它们评估条件并返回那些根据要求返回真的行。

因此，`512 & permissions`必须评估为 true。我们知道，任何非零值，无论是整数、表示“真”的布尔值还是非空的字符串，实际上都被认为是“真”。所以 512 是真的。1 是真的。0 是假的。128 是真的。等等。

512 是一个基数为 10 的整数，`permissions`是一个可以包含基数为 10 的整数的列。*按位 and* 实际上查看这两个数字的横截面，并返回这两个数字中设置为(1)的位。因此，如果数字 512 是 1000000000，如果权限值是 1023，那么转换为二进制时就是 1111111111。这些的横截面返回 1000000000，因为在这两个数字中只有最左边的位被设置。当我们把这个转换回十进制时，那就是 512，被认为是`true`。

这些实际上是逻辑运算符，而不是算术运算符，因为它们基于一个条件来检查真实性。如果我们有数字 1110 和 1010，这是给定不同的按位运算符后它们产生的结果:

| – | & | &#124; | ^ | ~ |
| --- | --- | --- | --- | --- |
| 操作数 A | One thousand one hundred and ten | One thousand one hundred and ten | One thousand one hundred and ten | One thousand one hundred and ten |
| 操作数 b | One thousand and ten | One thousand and ten | One thousand and ten | / |
| 结果 | One thousand and ten | One thousand one hundred and ten | 0100 | 0001 |

*   `&`返回一个二进制数，其中设置了两个操作数中设置的所有位。
*   `|`返回一个二进制数，其所有位都设置在任一操作数中。
*   `^`返回一个二进制数，该数的所有位都设置在任一操作数中，但不同时设置在两个操作数中。
*   `~`返回相反的结果——所有未在原始操作数中设置的操作数现在都已设置。

还有按位移位运算符:左移`<<`和右移`>>`。通过将所有设置的位向右或向左移动一个位置，这些操作极大地改变了二进制数的值。它们在我们上下文中的使用是有问题的，所以我们不会在这里讨论它们。

* * *

在 PHP 中，我们可以测试一个位是否像这样设置:

```
if (1023 & 1) {

} 
```

但是这真的真的很难理解——仅仅看原始数据是不可读或不可理解的。因此，在 PHP 中，最好使用常量将权限定义为位，并从列中获取权限的整数值。然后，你会得到这样的结果:

```
if ($user->permissions & \MyNamespace\Role::FINISHED_DELETE) {
  // 
} 
```

这里我们假设已经定义了一个`\MyNamespace\Role`类，并加载了如下常量:

```
const FINISHED_DELETE = 1;
const FINISHED_EDIT = 2;
const DRAFT_PUBLISH = 8;
...
const CHANGE_PERMISSIONS = 512; 
```

突然间，您有了一种非常简单的方法来存储每个用户的多个权限，而不需要使用额外的表和产生不必要的开销。

那么，当权限改变时，我们如何将它存储在数据库中呢？只需将权限相加，并存储为整数即可！根据上面的常量，能够`FINISHED_DELETE`和`FINISHED_EDIT`的人拥有权限 1 和 2。因此，要保存他们的权限，只需将他们相加(1+2=3)并将 3 保存到`permissions`列中。没有其他方法*可以用二进制组合得到数字 3——数字 3 只能用 0011 表示——所以你可以 100%确定数字 3 总是意味着用户拥有权限 1 和权限 2，对应于它们在常量中的值。*

 *这个好像太简单实用了吧？有什么条件？

## 警告

有两个主要的警告:

1.  在计算下一个权限的位值时，需要记住使用 2 的幂。因此，如果您需要添加一个新的权限，如果您已经有 512 个权限，就不能随意选择 543——必须是 1024 个。随着数字越来越大，这就变得有点复杂了。
2.  因为我们的计算机在 64 位 CPU 上运行 64 位操作系统(大多数情况下，有些甚至停留在 32 位！)，这意味着一个数最多只能有 64 位。这意味着对于给定的用户，您只能存储最多 64 个权限的排列。对于中小型网站来说，这已经足够了，但是对于大型网站来说，这可能会成为一个问题。这里的解决方案是为不同的权限上下文(`draft_permissions`、`account_permissions`等)使用不同的列。).每一栏都可以包含 64 种权限的排列，这对于要求最苛刻的网站来说已经足够了。

## 结论

位运算在现代编程中仍然有一席之地。虽然使用看起来如此复杂的东西可能违反直觉(实际上并不复杂——它不像现代的连接表那样熟悉),但这种方法带来了许多好处——尤其是在数据大小(存储在数据库中并随后提取的信息少得多)和速度(用户对象可以预取其权限值——它只是一个 int——因此可以随时检查它)方面的性能大幅提升。

这里展示的那些包当然会使事情变得简单，但前提是你还不知道更简单的选择，比如上面展示的那些。

你觉得使用按位操作符检查权限和这种存储权限的方法怎么样？有什么明显的利弊吗？让我们知道你是怎么做的，为什么！

## 分享这篇文章*