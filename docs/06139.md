# 如何在 MySQL 和其他数据库中使用唯一索引

> 原文：<https://www.sitepoint.com/use-unique-indexes-mysql-databases/>

如果您使用数据库已经有一段时间了，您可能已经在大多数表中设置了主键。主键是每个记录的唯一标识符，例如

```
CREATE TABLE `phone` (
	`id` MEDIUMINT(8) UNSIGNED NOT NULL AUTO_INCREMENT,
	`country` DECIMAL(5,0) UNSIGNED NOT NULL,
	`area` DECIMAL(5,0) UNSIGNED NOT NULL,
	`number` DECIMAL(8,0) UNSIGNED NOT NULL,
	`extension` DECIMAL(5,0) UNSIGNED DEFAULT NULL,
	PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=0 DEFAULT CHARSET=utf8;
```

在本例中,“id”列是我们的主键。当我们`INSERT`电话号码时，如果我们不指定一个 id，一个`AUTO_INCREMENT`号码将通过在现有的最高 id 上加 1 来生成。

假设您已经添加了以下数据:

| 身份证明（identification） | 国家 | 区域 | 数字 | 延长 |
| one | one | Two hundred and thirty-four | Five hundred and sixty-seven thousand eight hundred and ninety | 空 |
| Two | forty-four | Nine thousand eight hundred and seventy-six | Fifty-four thousand three hundred and twenty-one | forty-two |
| three | Sixty-one | three | Ninety million nine hundred and eight thousand two hundred | 空 |

然后发出下面的`INSERT`:

```
INSERT INTO `phone`
(`id`, `country`, `area`, `number`)
(1, 1, 234, 567890);
```

数据库将拒绝添加新记录，因为已经存在一个 id 为 1 的记录。幸运的是，我们可以从我们的`INSERT`中省略 id 来自动生成一个:

```
INSERT INTO `phone`
(`country`, `area`, `number`)
(1, 234, 567890);
```

我们现在有四项记录:

| 身份证明（identification） | 国家 | 区域 | 数字 | 延长 |
| one | one | Two hundred and thirty-four | Five hundred and sixty-seven thousand eight hundred and ninety | 空 |
| Two | forty-four | Nine thousand eight hundred and seventy-six | Fifty-four thousand three hundred and twenty-one | forty-two |
| three | Sixty-one | three | Ninety million nine hundred and eight thousand two hundred | 空 |
| four | one | Two hundred and thirty-four | Five hundred and sixty-seven thousand eight hundred and ninety | 空 |

在 id 超出范围之前，我们可以添加将近 1700 万条记录。

很好—除了记录 1 和 4 是相同的。如果我们想确保所有电话号码都是唯一的，该怎么办？

## 唯一索引

唯一索引的工作方式与主键非常相似。尽管只能有一个主键，但是可以用任意数量的字段创建任意数量的唯一索引。

在我们的示例中，我们希望确保没有两个记录具有相同的国家、地区、号码和分机。我们可以通过改变我们的桌子来做到这一点:

```
ALTER TABLE `phone` 
ADD UNIQUE INDEX `ix_phone` (`country`, `area`, `number`, `extension`);
```

请注意，索引名称“ix_phone”是可选的。或者，我们可以重新创建我们的表:

```
DROP TABLE IF EXISTS `phone`;

CREATE TABLE `phone` (
	`id` MEDIUMINT(8) UNSIGNED NOT NULL AUTO_INCREMENT,
	`country` DECIMAL(5,0) UNSIGNED NOT NULL,
	`area` DECIMAL(5,0) UNSIGNED NOT NULL,
	`number` DECIMAL(8,0) UNSIGNED NOT NULL,
	`extension` DECIMAL(5,0) UNSIGNED DEFAULT NULL,
	PRIMARY KEY (`id`),
	UNIQUE KEY `ix_phone` (`country`, `area`, `number`, `extension`),
) ENGINE=InnoDB AUTO_INCREMENT=0 DEFAULT CHARSET=utf8;
```

大多数数据库支持唯一索引，但 SQL 语法可能会有所不同。

让我们尝试插入一个重复的记录，即使我们没有指定 id:

```
INSERT INTO `phone`
(`country`, `area`, `number`, `extension`)
(44, 9876, 54321, 42);
```

如果您使用 MySQL，将会产生以下错误:

```
Error Code: 1062
Duplicate entry '44-9876-54321-42' for key 'ix_phone'
```

如果你使用几乎任何数据库，你可以保证你的电话记录是唯一的，不管数据是如何插入的。

## MySQL 空值

我说*几乎任何数据库*，因为 MySQL 有一个奇怪的怪癖。NULL 被视为一个唯一的值——这就是为什么您不能使用像`value = NULL`这样的比较，而需要使用`value IS NULL`的原因。不幸的是，这也会影响唯一索引，并且没有实现任何逻辑来修复它。

我们可以多次执行原始的`INSERT`,每次都会创建一条新记录，因为扩展字段默认为 NULL，并且被认为是唯一的:

```
INSERT INTO `phone`
(`country`, `area`, `number`)
(1, 234, 567890);
```

是的，这太疯狂了。我没有意识到其他数据库中的问题，甚至如果你使用 BDB 存储引擎，MySQL 也能正常工作。据报道，这是 MySQL 的一个缺陷，但是目前还没有修复它的计划。

解决方案:*确保惟一索引中定义的所有字段都不能设置为 NULL* 。在本例中，我们可以通过设置 0 或 99999 这样的值来表示没有分机号码。或者，我们可以将该字段设为一个有符号的数字，并设置为-1。太可怕了，但会有用的。

尽管有这个问题，惟一索引在许多情况下还是很有用的，当其他程序员和用户不那么认真的时候，它可以帮助您保持数据的完整性！

## 分享这篇文章