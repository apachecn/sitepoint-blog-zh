# 用数组配置是不是很难闻？

> 原文：<https://www.sitepoint.com/is-configuration-with-arrays-a-bad-smell/>

这是我在 PHP 中经常看到的，涉及到 OOP /类；

 `$options = array (
'name' => 'Harry Fuecks',
'favoritecolors' => array ('red','green','blue'),
'favoritefoods' => array (
'breakfast' => 'English Breakfast',
'lunch' => 'Steak and chips',
'dinner' => 'Tandori Chicken',
),
'etcetc' => 'aarrrgh!'
);`

`$person = new Person($options);`

换句话说，这是一个用来“配置”对象运行时行为的巨型数组。

对我来说，这是一种难闻的气味。首先，这通常意味着在某个地方有一些巨大的类方法来处理“解析”数组并做出相应的反应。更重要的是，这对于用户来说是一场噩梦——数组很难文档化，并且当你手工编写数组时很容易出错。

我更希望看到这样的东西:

 `$person = new Person();
$person->name('Harry Fuecks');
$person->addFavColor('red');
$person->addFavColor('blue');
$person->addFavColor('green');
$person->addFavFood('breakfast','English Breakfast');
$person->addFavFood('lunch','Steak and Chips');
$person->addFavFood('dinner','Tandori Chicken');`

这些方法很容易记录(用 [phpDocumentor](http://www.phpdoc.org) ),而且，在我看来，测试/使用要容易得多。API 使得正在发生的事情显而易见。

否则，明智的使用 [parse_ini_file()](http://www.php.net/parse_ini_file) 。PHP 可以将一个 ini 文件解析成变量*比解析和执行一个等价的 PHP 脚本更快*。

大声抱怨。

## 分享这篇文章