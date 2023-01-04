# 用 PHP 5.5 密码散列 API 散列密码

> 原文：<https://www.sitepoint.com/hashing-passwords-php-5-5-password-hashing-api/>

使用 bcrypt 是目前公认的散列密码的最佳实践，但是大量开发人员仍然使用旧的和较弱的算法，如 MD5 和 SHA1。一些开发人员甚至在散列时不使用盐。PHP 5.5 中新的哈希 API 旨在吸引人们对 bcrypt 的注意，同时隐藏其复杂性。在本文中，我将介绍使用 PHP 新散列 API 的基础知识。

新的密码哈希 API 公开了四个简单的函数:

*   `password_hash()`–用于散列密码。
*   `password_verify()`–用于根据哈希验证密码。
*   `password_needs_rehash()`–需要重新设置密码时使用。
*   `password_get_info()`–返回哈希算法的名称和哈希时使用的各种选项。

## 密码散列()

虽然`crypt()`函数是安全的，但许多人认为它太复杂，容易出现程序员错误。然后，一些开发人员使用弱盐和弱算法来生成哈希，例如:

```
<?php
$hash = md5($password . $salt); // works, but dangerous
```

但是`password_hash()`函数可以简化我们的生活，我们的代码可以是安全的。当您需要散列一个密码时，只需将它提供给函数，它将返回您可以存储在数据库中的散列。

```
<?php
$hash = password_hash($password, PASSWORD_DEFAULT);
```

就是这样！第一个参数是需要散列的密码字符串，第二个参数指定应该用于生成散列的算法。

目前的默认算法是 bcrypt，但是将来某个时候可能会添加一个更强的算法作为默认算法，并且可能会生成一个更大的字符串。如果您在项目中使用`PASSWORD_DEFAULT`,请确保将散列存储在容量超过 60 个字符的列中。将列大小设置为 255 可能是一个不错的选择。您也可以使用`PASSWORD_BCRYPT`作为第二个参数。在这种情况下，结果将总是 60 个字符长。

这里重要的是，您不必提供盐值或成本参数。新的 API 将为您处理所有这些问题。而且盐是杂碎的一部分，不用单独存放。如果您想提供自己的 salt(或 cost ),可以通过向函数传递第三个参数(一组选项)来实现。

```
<?php
$options = [
    'salt' => custom_function_for_salt(), //write your own code to generate a suitable salt
    'cost' => 12 // the default cost is 10
];
$hash = password_hash($password, PASSWORD_DEFAULT, $options);
```

通过这种方式，您可以随时了解最新的安全措施。如果 PHP 后来决定实现一个更强大的散列算法，您的代码可以利用它。

## password_verify()

既然您已经看到了如何使用新的 API 生成散列，那么让我们看看如何验证密码。请记住，您将散列存储在数据库中，但它是用户登录时获得的普通密码。
`password_verify()`函数将一个普通密码和散列字符串作为它的两个参数。如果哈希与指定的密码匹配，则返回 true。

```
<?php
if (password_verify($password, $hash)) {
    // Success!
}
else {
    // Invalid credentials
}
```

请记住，salt 是哈希密码的一部分，这就是为什么我们在这里没有单独指定它。

## password_needs_rehash()

如果您需要修改散列字符串的 salt 和 cost 参数，该怎么办？这是一个问题，因为您可能会决定通过添加更强的盐或更大的成本参数来提高安全性。此外，PHP 可能会改变哈希算法的默认实现。在所有这些情况下，您都需要重新设置现有的密码。

## password_needs_rehash()

帮助检查指定的哈希在创建时是否实现了特定的算法并使用了特定的选项，如 cost 和 salt。

```
<?php
if (password_needs_rehash($hash, PASSWORD_DEFAULT, ['cost' => 12])) {
    // the password needs to be rehashed as it was not generated with
    // the current default algorithm or not created with the cost
    // parameter 12
    $hash = password_hash($password, PASSWORD_DEFAULT, ['cost' => 12]);

    // don't forget to store the new hash!
}
```

请记住，当用户试图登录您的网站时，您需要这样做，因为这是您唯一可以访问普通密码的时间。

## 密码获取信息()

`password_get_info()`接受散列并返回由三个元素组成的关联数组:

*   `algo`–标识特定算法的常数
*   `algoName`–所用算法的名称
*   `options`–生成哈希时使用的各种选项

## 结论

新的密码散列 API 肯定比使用`crypt()`函数更容易操作。如果你的网站目前运行在 PHP 5.5 上，那么我强烈建议你使用新的哈希 API。那些使用 PHP 5.3.7(或更高版本)的人可以使用一个名为 [password_compat](https://github.com/ircmaxell/password_compat) 的库，它模拟 API，一旦 PHP 版本升级到 5.5 就会自动禁用自己。

## 分享这篇文章