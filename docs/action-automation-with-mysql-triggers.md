# 使用 MySQL 触发器实现动作自动化

> 原文：<https://www.sitepoint.com/action-automation-with-mysql-triggers/>

我们编写的大部分代码都是为了执行一个动作。无论是数据库查询、文件操作、数据处理等等。，这一切都是使我们的脚本功能达到既定目的所必需的。但是，您有没有注意到，有时为了验证之前的一些操作，您不得不写出大量的代码？

我最近的一个项目涉及到一个相当麻烦的问题，这个问题让我使用了无数次查询，仅仅是为了确保在每次操作之后，我的表中的所有数据都是同步的。它一点也不优雅，原本应该是一个相当简单的脚本变成了一个复杂的查询页面。从维护的角度来看，这是不可行的，当我想更新页面的部分功能时，这简直是一场噩梦。这就是 MySQL 触发器进入我的项目的地方。

通过让 MySQL 通过触发器做更多的工作，我的项目的 PHP 部分得到了极大的简化。因此，本文的目的是让您对 MySQL 触发器的创建和使用有所了解，以便在阅读完本文后，您可以在自己的项目中使用它们。

## 它们是什么？

触发器是在 MySQL 版本 5.0.2 中引入的，它只是 MySQL 增加的功能之一，可以帮助我们的开发人员更加轻松。在对表执行一个操作(插入、更新、删除)之前或之后，它们会被自动调用。

您需要有适当的权限来创建触发器。在 MySQL 5.1.6 之前，您需要超级权限，但是在 5.1.6 中，这种情况发生了变化，您需要触发器权限。一般来说，没有共享主机计划会允许你超级，因为它很容易被滥用，所以你可能只能在你有更多权限的服务器上使用这些，如(虚拟)专用服务器或本地主机，这取决于你使用的 MySQL 版本。

以下是关于触发器的一些其他快速注释:

*   它们在创建它们的数据库中必须具有唯一的(不区分大小写)名称。
*   每个表只允许一个具有相同事件(更新/插入/删除)*和*计时(之前/之后)的触发器。
*   删除表时，与之关联的触发器也会被删除。
*   您不能用`ALTER`语句显式地改变触发器(与事件不同)。你需要放下扳机重新创建它。
*   只有在执行了原始 SQL 语句时才会触发触发器；例如，外键关系删除不会激活触发器。

现在，让我们通过将其简化为原始形式，来更仔细地了解触发器的基本语法:

```
CREATE TRIGGER TrigName [BEFORE|AFTER] [INSERT|UPDATE|DELETE] ON tableName
FOR EACH ROW
BEGIN
     #action(s) to perform
END
```

创建触发器时，您可以选择它是在动作发生之前还是之后触发；你选择哪一个将完全取决于你使用它们的情况。如果您希望修改进入数据库的输入数据，那么就需要`BEFORE`。但是，如果您希望执行一个动作是因为之前的一个动作，那么应该使用`AFTER`语句。触发触发器的动作可以是`INSERT`、`UPDATE`或`DELETE`，因为只有这三个语句会修改表中的数据。

## 将触发器应用于一种情况

在很多情况下，触发器会派上用场。一个众所周知的用法是在一组表中维护数据完整性，当没有使用外键时触发删除陈旧记录。它们还可以用于在新的插入/删除操作时自动递增或递减统计表，记录对数据库内部数据所做的更改，保持表与其他表同步，等等。

在本文中，我们将使用它们来预处理一些计算。场景是，我们有一家公司以每小时 30 英镑的价格出租其大厅。他们在`events`表中记录每个活动的名称、开始时间和结束时间，然后在`revenue`表中计算时间和费用。

活动的名称和开始时间最初被插入到`events`表中，以便预先预订大厅，然后只有在活动结束后，租赁费用才会在该行中更新。需要计算活动的持续时间(以分钟为单位)，其中将从结束时间中减去开始时间，然后将总时间乘以 0.5(每小时 30 是每分钟 50p)来计算租赁费用。

我们可以在更新活动信息时用 PHP 计算活动持续时间和费用(通过选择插入的数据，计算持续时间和租赁费用，然后插入或更新收入表),或者我们可以简单地使用一个触发器来自动化这个过程，并删除一些 PHP。

让我们设置两个基本表，并在`events`中插入一些虚拟预订数据。

```
CREATE TABLE events (
    id INTEGER NOT NULL AUTO_INCREMENT,
    event_name VARCHAR(50) NOT NULL,
    event_start TIMESTAMP NOT NULL DEFAULT 0,
    event_end TIMESTAMP NOT NULL DEFAULT 0,
    PRIMARY KEY (id)
) ENGINE=INNODB;

CREATE TABLE revenue (
    id INTEGER NOT NULL AUTO_INCREMENT,
    event_id INTEGER NOT NULL,
    hire_time INTEGER NOT NULL,
    hire_fees FLOAT NOT NULL,
    PRIMARY KEY (id),
    FOREIGN KEY (event_id) REFERENCES events(id) ON DELETE CASCADE,
    UNIQUE (event_id)
)ENGINE=INNODB;

INSERT INTO events VALUES
    (NULL, 'Birthday Party', '2012-11-08 14:30:00', 0),
    (NULL, 'Wedding', '2012-12-02 13:00:00', 0);
```

一旦我们设置了两个表，我们就可以继续创建触发器，我们称之为`CostCalc`。触发器被设置为在`events`表发生更新后触发，然后执行前面提到的计算。然后，它插入或更新(如果已经设置了一个预先存在的事件 ID)T2 表。

```
DELIMITER ^^
CREATE TRIGGER CostCalc AFTER UPDATE ON events
FOR EACH ROW
BEGIN
    DECLARE rows BOOL DEFAULT 0;
    DECLARE time INT DEFAULT 0;

    SELECT COUNT(id) INTO rows FROM events
        WHERE id = NEW.id
        AND (OLD.event_start != NEW.event_start
            OR OLD.event_end != NEW.event_end)
        AND NEW.event_end != 0;

    IF(rows = 1) THEN
        SET time = TIMESTAMPDIFF(MINUTE, NEW.event_start, NEW.event_end);
        REPLACE INTO revenue VALUES (NULL, NEW.id, time, time * 0.5);
    END IF;
END
^^
```

当创建触发器时，我们需要做的第一件事是指定一个新的定界符，它表示触发器的结束。这是通过关键字`DELIMITER`完成的，后跟一个自定义符号(或多个符号),这是作为一个整体执行触发器而不是 MySQL 单独执行内部语句所必需的。

然后我们指定触发器名称、它的计时、事件，以及它将被设置在哪个表上触发。在本例中，我们将触发器定时为执行`AFTER`语句，因为我们只想在成功更新后执行触发器；否则，我们将重复该事件的先前记录。接下来，我们使用`BEGIN...END`复合语句来容纳触发器的功能。

触发器的主体从声明两个变量开始:`rows`和`time`。我们从`events`表中选择行数，其中 ID 引用刚刚被修改的行，其中`event_start`和`event_end`时间中的一个(或两个)被修改，以及事件结束时间不等于零。这是为了澄清更新后的行信息实际上是否需要对`revenue`表进行任何处理，因为只有通过这些更改才能计算租赁费用。一旦我们知道可以继续计算时间和费用，我们就将时间变量设置为事件从开始到结束的分钟数。将这个数字乘以 0.5，我们就得到了租赁成本。因为`event_id`列是惟一的，所以我们只能有一个 ID 对应于 events 表；因此，我们使用`REPLACE`用新数据更新表中预先存在的行，或者在新行不存在的情况下插入新行。

在 MySQL 语句中，您可能还注意到了在上面的`SELECT`和`REPLACE`语句中使用的关键字`OLD`和`NEW`，以及用于表示`time`变量值的表达式。你什么时候使用这两种武器将取决于你所处的情况，以及你开枪的时机。

*   `NEW`关键字用于将输入的数据访问到数据库中。这仅适用于`INSERT`和`UPDATE`报表。
*   在对记录进行任何修改之前，`OLD`关键字用于访问记录中的当前数据。这仅适用于`UPDATE`和`DELETE`报表。

用于触发我们的触发器的相应 PHP 脚本将包括一个类(名为`EventHandler`)和我们的客户端调用代码。该类将通过`PDO`连接到我们的 MySQL 数据库，并将包含一个方法`updateEvent()`，当事件的内容需要更新时将调用该方法。

```
<?php
class EventHandler
{
    protected $db;

    public function __construct(PDO $db) {
        $this->db = $db;
        $this->db->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    }

    public function updateEvent($type, $param, $id) {
        if (!in_array($type, array('name', 'start', 'end'), TRUE)) {
            throw new InvalidArgumentException(
                'No such column type exists.'
            );
        }

        $query = $this->db->prepare(
            "UPDATE events SET event_{$type} = :param WHERE id = :ID"
        );
        $query->bindParam(':param', $param, PDO::PARAM_STR);
        $query->bindParam(':ID', $id, PDO::PARAM_INT);
        $query->execute();

        if($query->rowCount() !== 1) {
            throw new InvalidArgumentException(
                'No such event ID exists.'
            );
        }
    }
}

$dsn = 'mysql:dbname=events';
$dbuser = 'dbuser';
$passwd = 'dbpassword';
$settings = array(PDO::MYSQL_ATTR_FOUND_ROWS => TRUE);

$eventHandler = new EventHandler(new PDO($dsn, $dbuser, $passwd, $settings));

// will not cause any inserts or updates to the revenue table
$eventHandler->updateEvent('name', 'Auction', 1);
$eventHandler->updateEvent('start', '2012-11-10 14:30:00', 1);

// causes a new insertion into the revenue table
$eventHandler->updateEvent('end', '2012-12-02 20:30:00', 2);
// causes an update on the revenue table
$eventHandler->updateEvent('end', '2012-12-02 21:30:00', 2);
```

我们首先创建我们的`EventHandler`类，其中属性`$db`被定义为保存通过构造函数方法设置的`PDO`类的实例。然后，我们继续使用三个已定义的参数来创建我们的`updateEvent()`方法。第一个参数指定了我们希望在我们的`events`表中更新的列，并允许三个值之一:name，start，end。第二个参数保存要插入的值，或更新列的当前值；而第三个参数保存要更新的元组的 ID。在确保列名有效之后，我们通过参数化查询来查询我们的表，最后检查是否有任何行被更新。

创建类之后，我们继续调用它。我们将`PDO`对象的实例化作为参数传递给`EventHandler`类。然后用不同的值调用`updateEvent()`方法四次，以显示我们的触发器将如何对我们的`events`表上的更新做出反应。前两个更新不会导致我们的触发器插入或更新一行，因为我们仍然没有所需的信息来计算事件的持续时间和租赁费用。我们所做的只是更新了活动名称，并将其开始时间推迟了两天。然而，接下来的两次更新将需要我们的触发器的功能，因为第一次更新定义了结束时间，第二次更新将结束时间重新定义为一小时后，导致持续时间的变化，从而导致租金费用的变化。这就是需要我们的`REPLACE`语句的地方，因为我们在创建时对表进行了约束，每个事件 ID 只能有一条记录。

## 结束语

如果使用得当，MySQL 触发器不仅可以对站点的性能产生积极的影响，还可以让您不必编写大量 PHP 代码来处理这些操作。我希望你会发现它们在你的项目中很有用，就像我在我的项目中一样，所以请尽情享受吧！

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章