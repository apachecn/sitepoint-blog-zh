# PHP 中的定点数学与 BCMath，精度损失情况

> 原文：<https://www.sitepoint.com/fixed-point-math-php-bcmath-precision-loss-cases/>

当处理定点数时，你必须非常小心——尤其是如果你用 PHP 和 MySQL 开发的话。在本文中，描述了使用 PHP BCMath 扩展、MySQL 定点表达式处理和将定点数据从 PHP 持久化到 MySQL 的障碍和微妙之处。尽管出现了障碍，我们还是试图找出如何处理定点数，并且不丢失一个数字。

## bcmath 有问题

BCMath 文档说:

> 对于任意精度的数学，PHP 提供了二进制计算器，它支持任意大小和精度的数字，表示为**字符串**。

所以 BCMath 函数参数应该用字符串表示。将数值传递给`bcmath`会导致错误的结果，这与我们将 double 值视为 string 时的精度损失相同

### 案例 1

```
echo bcmul(776.210000, '100', 10) . PHP_EOL;
    echo bcmul(776.211000, '100', 10) . PHP_EOL;
    echo bcmul(776.210100, '100', 10) . PHP_EOL;

    echo bcmul(50018850776.210000, '100', 10) . PHP_EOL;
    echo bcmul(50018850776.211000, '100', 10) . PHP_EOL;
    echo bcmul(50018850776.210100, '100', 10) . PHP_EOL;
```

结果是:

```
77621.00
    77621.100
    77621.0100
    5001885077621.00
    5001885077621.100
    5001885077621.00 //here we can see precision loss
```

**不要将数值传递给 BCMath 函数，只传递代表数字的字符串值**。即使不处理浮点，BCMath 也会输出奇怪的结果:

### 案例 2

```
echo bcmul('10', 0.0001, 10) . PHP_EOL;
	echo bcmul('10', 0.00001, 10) . PHP_EOL;
	echo 10*0.00001 . PHP_EOL;
```

结果是:

```
0.0010
	0 // thats really strange!!!
	0.0001
```

这样做的原因是 BCMath 将其参数转换为字符串，并且存在数字的字符串表示具有指数符号的情况。

### 案例 3

```
echo bcmul('10', '1e-4', 10) . PHP_EOL; //outputs 0 as well
```

PHP 是一种弱类型语言，在某些情况下你不能严格控制输入——你想处理尽可能多的请求。

例如，我们可以通过应用`sprintf`转换来“修复”*情况 2* 和*情况 3* :

```
$val = sprintf("%.10f", '1e-5');
	echo bcmul('10', $val, 10) . PHP_EOL;
	// gives us 0.0001000000
```

但是应用相同的变换可能会破坏*情况 1* “适当的”行为:

```
$val = sprintf("%.10f", '50018850776.2100000000');
	echo bcmul('10', $val, 10) . PHP_EOL;
	echo bcmul('10', 50018850776.2100000000, 10) . PHP_EOL;
	500188507762.0999908450 //WRONG
	500188507762.10 //RIGHT
```

所以`sprintf`解不适合 BCmath。假设所有的用户输入都是字符串，我们可以实现一个简单的验证器，捕捉所有的指数符号数并正确地转换它们。这项技术是在[PHP-bignomes](https://github.com/Litipk/php-bignumbers "php-bignumbers")中完成的，所以我们可以安全地传入像`1e-20`和`50018850776.2101`这样的参数，而不会损失精度。

```
echo bcmul("50018850776.2101", '100', 10) . PHP_EOL;
	echo bcmul(Decimal::create("50018850776.2101"), '100', 10) . PHP_EOL;

	echo bcmul(Decimal::create("1e-8"), '100', 10) . PHP_EOL;
	echo bcmul("1e-8", '100', 10) . PHP_EOL;

	echo bcmul(50018850776.2101, '100', 10) . PHP_EOL;
	echo bcmul(Decimal::create(50018850776.2101), '100', 10) . PHP_EOL;

	// Result
	// 5001885077621.0100
	// 5001885077621.0100
	// 0.00000100
	// 0
	// 5001885077621.00
	// 5001885077621.00982700
```

但是示例的最后两行向我们展示了输入解析无法避免浮点警告(这是完全符合逻辑的——我们无法处理 PHP 内部的双重表示)。

## BCMath 最终指南

千万不要用浮点数做定点运算参数。字符串转换没有帮助，因为我们不能以任何方式管理精度损失。

使用 BCMath 扩展运算时，请小心使用指数表示形式的参数。BCMath 函数不能正确处理指数参数(即“1e-8”)，因此您应该手动转换它们。小心，不要使用`sprintf`或类似的转换技术，因为它会导致精度损失。

您可以使用 [php-bignumbers](https://github.com/Litipk/php-bignumbers "php-bignumbers") 库，该库以指数形式处理输入参数，并为用户提供定点数学运算函数。然而，[它的性能比 BCMath 扩展](https://www.sitepoint.com/arbitrary-precision-big-numbers-php/ "Arbitrary Precision and Big Numbers in PHP")差，所以它是健壮的包和性能之间的一种折衷。

## MySQL 和定点数

在 MySQL 中，定点数用`DECIMAL`列类型处理。你可以阅读官方的 MySQL 文档，了解数据类型和[精确数学运算](http://dev.mysql.com/doc/refman/5.7/en/precision-math.html "Precision Math")。

最有趣的部分是 MySQL 如何处理表达式:

> 数值表达式的处理取决于表达式包含的值的种类:
> 
> 如果存在任何近似值，则表达式是近似的，并使用浮点运算进行计算。
> 
> 如果没有近似值，则表达式只包含精确值。如果任何精确值包含小数部分(小数点后的值)，表达式将使用十进制精确算术进行计算，精度为 65 位。术语“精确”受制于二进制所能表示的限度。例如，1.0/3.0 在十进制记数法中可以近似为. 333…，但不能写成精确的数字，因此(1.0/3.0)*3.0 的计算结果不是精确的 1.0。
> 
> 否则，表达式只包含整数值。该表达式是精确的，使用整数运算进行计算，其精度与 BIGINT (64 位)相同。
> 
> 如果数值表达式包含任何字符串，它们将被转换为双精度浮点值，并且该表达式是近似的。

下面是一个演示小数部分情况的简短示例:

```
mysql> CREATE TABLE fixed_point (
        ->   amount NUMERIC(40,20) NOT NULL
        -> ) engine=InnoDB, charset=utf8;
    Query OK, 0 rows affected (0.02 sec)

    mysql> INSERT INTO fixed_point (amount) VALUES(0.2);
    Query OK, 1 row affected (0.00 sec)

    mysql> SELECT amount, amount + 0.1, amount + 1e-1, amount + '0.1' FROM fixed_point;
    +------------------------+------------------------+---------------------+---------------------+
    | amount                 | amount + 0.1           | amount + 1e-1       | amount + '0.1'      |
    +------------------------+------------------------+---------------------+---------------------+
    | 0.20000000000000000000 | 0.30000000000000000000 | 0.30000000000000004 | 0.30000000000000004 |
    +------------------------+------------------------+---------------------+---------------------+
    1 row in set (0.00 sec)
```

这看起来很简单，但是让我们看看如何在 PHP 中处理它。

## PHP 和 MySQL 中的精确数学

所以现在我们必须将 PHP 中的定点值保存到 MySQL 中。正确的方法是在我们的查询中使用准备好的语句和占位符。然后我们进行参数绑定，一切都很安全。

```
$amount_to_add = "0.01";
	$stmt = $dbh->prepare("UPDATE fixed_point SET amount = amount + :amount");
	$stmt->bindValue("amount", $amount_to_add);
	$stmt->execute();
```

当我们将一个值绑定到一个语句占位符时，我们可以通过第三个参数`bindValue`指定它的类型。可能的类型由常量`PDO::PARAM_BOOL`、`PDO::PARAM_NULL`、`PDO::PARAM_INT`、`PDO::PARAM_STR`、`PDO::PARAM_LOB`和`PDO::PARAM_STMT`表示。所以问题是 PHP PDO 扩展没有用于绑定的十进制参数类型。因此，查询中的所有数学表达式都被视为浮点表达式，而不是定点表达式。

```
$dbh = new PDO("mysql:host=localhost;dbname=test", "root", "");
    $dbh->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    $sql = "
        CREATE TABLE IF NOT EXISTS fixed_point (
            amount DECIMAL(43,20)
        )
    ";
    $dbh->query($sql);

    $dbh->query("DELETE FROM fixed_point");
    $dbh->query("INSERT INTO fixed_point VALUES(0.2)");

    $amount_to_add = "0.1";
    $stmt = $dbh->prepare("UPDATE fixed_point SET amount = amount + :amount");
    $stmt->bindValue("amount", $amount_to_add);
    $stmt->execute();

    $stmt = $dbh->prepare("SELECT amount FROM fixed_point");
    $stmt->execute();
    var_dump($stmt->fetchColumn());

    //output is string(22) "0.30000000000000004000"
```

如果我们想利用准备好的语句并处理定点数，最好的方法是在 PHP 中执行所有的数学运算，并将结果保存到 MySQL。

```
$amount_to_add = "0.1";
	$stmt = $dbh->prepare("SELECT amount FROM fixed_point");
	$stmt->execute();
	$amount = $stmt->fetchColumn();
	$new_amount = bcadd($amount, $amount_to_add, 20);
	$stmt = $dbh->prepare("UPDATE fixed_point SET amount=:amount");
	$stmt->bindValue("amount", $new_amount);
	$stmt->execute();
	$stmt = $dbh->prepare("SELECT amount FROM fixed_point");
	$stmt->execute();
	$amount_after_change = $stmt->fetchColumn();
	echo $amount_after_change . PHP_EOL;
```

## 结论

我们得出了以下结论:

*   不要在 BCMath PHP 扩展函数中使用浮点数作为定点运算参数。只有字符串。
*   BCMath 扩展不适用于指数表示形式的字符串数字
*   MySQL 支持定点数表达式，但是所有操作数都必须是十进制格式。如果至少有一个参数是指数格式或字符串，则它被视为浮点数，表达式被计算为浮点数。
*   PHP PDO 扩展没有`Decimal`参数类型，所以如果您在包含定点操作数的 SQL 表达式中使用准备好的语句和绑定参数，您将不会得到精确的结果。
*   要在 PHP+MySQL 应用程序中执行精确的数学运算，你可以选择两种方法。第一个是处理 PHP 中的所有操作，只使用`INSERT`或`UPDATE`语句将数据持久化到 MySQL。在这种情况下，您可以使用预处理语句和参数绑定。第二个是手动构建 SQL 查询(仍然可以使用准备好的语句，但是必须自己对参数进行转义)所以所有的 SQL 数学表达式都是十进制数表示。

我个人最喜欢的方法是第一种:PHP 中的所有数学运算。我同意 PHP 和 MySQL 可能不是精确数学应用程序的最佳选择，但是如果您选择了这种技术堆栈，那么知道有一种正确的方式来处理它是很好的。

## 分享这篇文章