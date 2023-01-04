# 使用 Liquibase 对数据库进行版本控制

> 原文：<https://www.sitepoint.com/versioning-your-database-with-liquibase/>

我们开发的大多数应用程序都是使用某种版本控制系统来管理的。但是那些应用程序使用的数据库呢？更常见的是，我们手动地对我们的开发、测试和生产数据库进行更改。这种方法可能适用于只有一两个开发人员的应用程序，但是在一个有许多开发人员的大型团队中，很难与每个人共享变更。

在本文中，我们将讨论 Liquibase，这是一个用于管理和控制数据库模式变化的开源工具。它帮助我们将增量数据库更改组织成不同的更改集，并将它们应用于数据库。

Liquibase 不是唯一的数据库版本控制/迁移工具。有很多解决方案，比如 Doctrine 2 迁移、Rails AR 迁移、DBDeploy 等等。前两个选项是极好的解决方案，但是它们是特定于平台的。DBDeploy 相对简单，但不如 Liquibase 功能丰富。Liquibase 解决了许多不同数据库迁移工具无法解决的问题，比如支持多个开发人员、不同的 DBMS 系统、分支等等。

此外，大多数工具的一个严重缺点是它们不能感知变化。他们不是关注所做的更改，而是比较数据库模式的两个快照来生成迁移脚本。例如，重命名列被视为 drop+add 操作，这可能会导致数据丢失。Liquibase 能够感知变化。

让我们看看如何在我们的项目中使用 Liquibase。

## Liquibase 的工作原理

如果您使用的是带有 brew 的 Mac，安装 Liquibase 很简单。只要运行`brew install Liquibase`就大功告成了。Ubuntu 也是一样，`sudo apt-get install liquibase`会帮你做的。Liquibase 二进制文件是一个跨平台的 Java 应用程序，这意味着你可以[下载 JAR](http://www.liquibase.org/download "Liquibase | Database Refactoring | download") 并在 Windows、Mac 或 Linux 上使用它。最好将它保存在项目文件夹中，这样项目中的任何人都可以使用它，而无需进行任何安装。

使用 Liquibase 时，您将数据库更改存储在 XML 文件中，通常称为 changelog 文件。更改可以保存在单个文件中，也可以保存在多个文件中，以便包含在主 changelog 文件中。推荐第二个选项，因为它在组织变更时允许更大的灵活性。

在变更日志文件中，您可以在不同的变更集中组织变更。变更集可以包含一个或多个要应用于数据库的更改。使用 id 和 author 属性以及 changelog 文件的类路径，可以唯一地标识每个变更集。Liquibase 在您的数据库中创建一个表(`databasechangelog`)来跟踪成功应用的更改。Liquibase 逐个运行每个变更集，并通过比较`databasechangelog`表中的校验和来检查它们是否已经被应用。如果它还没有运行，或者如果它有一个`runAlways`标签，它将应用一个改变。

## 入门指南

出于演示的目的，我在本地 MySQL 服务器上创建了一个名为`application`的数据库，以及一个 changelog 文件。您可以将它保存在项目文件夹中或单独的位置，但是 changelog 文件应该受版本控制。

这是我们没有变更集的第一个版本的变更日志文件。

```
<?xml version="1.0" encoding="UTF-8"?> 
<!--db.changelog.xml-->
<databaseChangeLog

  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-2.0.xsd">

</databaseChangeLog>
```

在命令行中，导航到保存 changelog 文件的位置，并运行以下命令:

```
liquibase --driver=com.mysql.jdbc.Driver 
     --classpath=../lib/mysql-connector-java-5.1.21-bin.jar 
     --changeLogFile=db.changelog.xml 
     --url="jdbc:mysql://localhost/application" 
     --username=dbuser 
     --password=secret 
     update
```

如果 Liquibase 可以使用给定的用户名和密码连接到数据库，它应该在应用程序数据库中创建两个表，`DATABASECHANGELOG`和`DATABASECHANGELOGLOCK`，并显示以下输出:

```
INFO 8/2/12 10:19 AM:liquibase: Successfully acquired change log lock
INFO 8/2/12 10:19 AM:liquibase: Creating database history table with name: `DATABASECHANGELOG`
INFO 8/2/12 10:19 AM:liquibase: Reading from `DATABASECHANGELOG`
INFO 8/2/12 10:19 AM:liquibase: Reading from `DATABASECHANGELOG`
INFO 8/2/12 10:19 AM:liquibase: Successfully released change log lock
Liquibase Update Successful
```

在上述命令中，除了`classpath`之外的所有参数都是必需的。`driver`指定我们想要使用的数据库驱动程序的类名。`changeLogFile`是我们数据库变更日志的名字。`url`指定 JDBC 数据库连接字符串，包括服务器类型、主机名和数据库名。`classpath`是保存类的地方，比如 Liquibase 使用的数据库连接器。

不用每次运行 Liquibase 时都指定命令行参数，您可以将它们保存在同一个目录中名为`liquibase.properties`的 Java 属性文件中。然后你就可以运行`liquibase <command>`了，属性文件应该是这样的:

```
#liquibase.properties
driver: com.mysql.jdbc.Driver
classpath: ../lib/mysql-connector-java-5.1.21-bin.jar
url: jdbc:mysql://localhost/application
changeLogFile: db.changelog.xml
username: dbuser
password: secret
```

接下来，让我们通过向`db.changelog.xml`添加一个变更集来创建一个包含 ID、name 和 email 字段的用户表。下面是更新后的 XML:

```
<?xml version="1.0" encoding="UTF-8"?>
<!--db.changelog.xml-->
<databaseChangeLog

  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-2.0.xsd">
 <changeSet id="1" author ="shameer">
  <createTable tableName="user">
   <column name="id" type="int">
    <constraints primaryKey="true" nullable="false" />
   </column>
   <column name="name" type="varchar(50)">
    <constraints nullable="false" />
   </column>
   <column name="email" type="varchar(128)">
    <constraints unique="true" nullable="false" />
   </column>
  </createTable>
 </changeSet>
</databaseChangeLog>
```

`createTable`将`tableName`属性中的表名和列作为子标签。您用标签`column`指定这个表中的列，并把标签`name`和`datatype`作为必需属性。`column`支持许多其他有用的属性，这些属性可能在所有情况下都没有意义。例如，您可以使用`autoIncrement="true"`将一列设置为自动递增。

任何列约束都用`constraints`标签指定。在我们的例子中，`id`列有主键和非空约束，`email`列有唯一约束。

运行 Liquibase 并查看结果:

```
liquibase update

INFO 8/4/12 7:16 AM:liquibase: Successfully acquired change log lock
INFO 8/4/12 7:16 AM:liquibase: Creating database history table with name: `DATABASECHANGELOG`
INFO 8/4/12 7:16 AM:liquibase: Reading from `DATABASECHANGELOG`
INFO 8/4/12 7:16 AM:liquibase: Reading from `DATABASECHANGELOG`
INFO 8/4/12 7:16 AM:liquibase: ChangeSet db.changelog.xml::1::shameer ran successfully in 74ms
INFO 8/4/12 7:16 AM:liquibase: Successfully released change log lock
Liquibase Update Successful
```

如果查看数据库，您会看到一个具有以下结构的`user`表:

```
*************** 1\. row ***************
  Field: id
   Type: int(11)
   Null: NO
    Key: PRI
Default: NULL
  Extra: 
*************** 2\. row ***************
  Field: name
   Type: varchar(50)
   Null: NO
    Key: 
Default: NULL
  Extra: 
*************** 3\. row ***************
  Field: email
   Type: varchar(128)
   Null: NO
    Key: UNI
Default: NULL
  Extra: 
```

您是否注意到我们在表中没有自动递增列？我们可以添加第二个变更集来更改表，进行以下更改:

*   将`id`栏改为`auto_increment`
*   将列`name`重命名为`fullname`
*   添加新列`age`

```
<changeSet id="2" author="shameer">
 <addAutoIncrement tableName="user" columnName="id" columnDataType="int" />
 <renameColumn tableName="user" oldColumnName="name" newColumnName="fullname" columnDataType="varchar(100)"/>
 <addColumn tableName="user">
  <column name="age" type="SMALLINT"/>
 </addColumn>
</changeSet>
```

这里，`addAutoIncrement`标签将使`id`列自动递增。`renameColumn`将通过接受`oldColumnName`属性中要更改的列名和`newColumnName`中的新名称来重命名列。

让我们再次运行 Liquibase，然后查看`user`表。您应该会看到两个修改过的列和一个新的`age`列。

## 正在生成 Changelog 文件

如果您已经在没有 Liquibase 的情况下开始了您的项目，然后您意识到如果没有一些自动化机制，将很难处理数据库更改，该怎么办？Liquibase 附带了一个方便的特性，可以从现有的模式中生成 changelog 文件。使用以下命令生成名称在`liquibase.properties`中指定的 changelog 文件。

```
liquibase generateChangeLog
```

生成的 changelog 将具有针对数据库中所有表的 create 语句。

当我们在本地机器上开发应用程序时，直接在 MySQL 中进行更改比为每次更改创建 changelog 文件更容易。但是我们需要在团队中的多个开发人员之间进行这些改变。幸运的是，Liquibase 还提供了一个工具来比较两个数据库并从中生成 changelogs。

以下是为数据库中新创建的表生成更改日志文件的步骤:

1.  使用`mysqldump`对数据库进行备份
2.  创建临时数据库
3.  将转储导入临时数据库
4.  删除原始数据库中的所有更改
5.  使用 Liquibase 生成 changelog。

```
liquibase diffChangeLog 
--referenceUrl=jdbc:mysql://localhost/application_temp 
--referenceUsername=dbuser 
--referencePassword=secret
```

这将在`db.changelog.xml`中追加一个新的变更集，用于创建数据库中缺少的表。

```
<changeSet author="shameerc (generated)" id="1344051849388-1">
 <createTable tableName="temp">
  <column autoIncrement="true" name="id" type="INT">
   <constraints nullable="false" primaryKey="true"/>
  </column>
  <column name="name" type="VARCHAR(200)"/>
  <column name="value" type="TEXT"/>
 </createTable>
</changeSet>
```

一旦生成，您应该在将它提交到版本控制之前，对照您的本地数据库进行检查和验证。生成这样的 changelog 文件时也要小心，因为 Liquibase 会将重命名的列视为 drop+add 列，这会导致数据丢失。

## 回滚更改

到目前为止，我们只研究了对数据库模式的更改。但是，如果我们需要在将更改应用到数据库之后撤销更改，该怎么办呢？我们可以使用 rollback 命令轻松回滚通过 Liquibase 应用的更改。

像`createTable`和`renameColumn`这样的重构标签，大部分都会自动生成回滚语句。如果这不能满足您的需要，您可以向变更集添加自定义回滚语句。您可以回滚到一定数量的以前的变更集、给定的日期或您以前创建的特定标记。

```
liquibase rollbackCount 1
```

Liquibase 允许我们通过标记数据库的当前状态来添加检查点。这有助于我们通过简单地调用它的标签轻松地回滚到特定的状态。

```
liquibase tag checkpoint_1
```

在进行一些更改后，您可以通过发出以下命令回滚到此状态。

```
liquibase rollback checkpoint_1
```

有时，您可能希望检查用于回滚更改的 SQL。为此，用`rollbackSql`替换`rollback`，用`rollbackCountSql`替换`rollbackCount`。Liquibase 会将结果打印到标准输出中，您可以将其重定向到一个文件中，并保存以供以后执行。

您可以在每个 changelog 的`rollback`标签中指定要执行的自定义语句。当您执行回滚时，Liquibase 将应用这些语句，而不是默认的回滚 SQL。

```
<changeSet id="6" author="shameer">
 <modifyDataType columnName="value" newDataType="TEXT" tableName="temp"/>
 <rollback>
  <dropTable tableName="temp"/>
 </rollback>
</changeSet>
```

您可以通过在应用变更集后创建回滚 SQL 来验证它。

```
liquibase rollbackCountSql 1

-- Lock Database
-- Rolling Back ChangeSet: db.changelog.xml::6::shameer::(Checksum: 3:29343b94088e34367e51a6633f572b81)
DROP TABLE `temp`;

DELETE FROM `DATABASECHANGELOG`  WHERE ID='6' AND AUTHOR='shameer' AND FILENAME='db.changelog.xml';

-- Release Database Lock
Liquibase Rollback Successful
```

## 有条件地应用更改

有时，您可能希望在执行变更集之前检查某个条件，例如在删除某个表之前确保它不包含任何数据。`preConditions`是一个有用的标签，可以让你检查数据库中的各种条件。

```
<changesSet id="8" author="shameer">
 <preConditions onFail="CONTINUE">
  <sqlCheck expectedResult="0">select count(*) from user</sqlCheck>
 </preConditions>
 <dropTable tableName="user"/>
</changesSet>
```

在这种情况下，Liquibase 将首先检查用户表是否包含任何记录，如果包含，那么它将继续而不执行这个变更集。如果情况危急，您可以通过设置`onFail="HALT"`而不是`CONTINUE`来停止执行。

先决条件还有许多其他方便的特性，比如检查哪个数据库系统正在使用，当前用户的用户名等。你可以[在他们的手册中读到更多关于](http://www.liquibase.org/manual/preconditions "Liquibase | Database Refactoring | manual:preconditions")的内容。

## 摘要

在本文中，我们讨论了 Liquibase，一个数据库重构和变更管理工具。Liquibase 最重要的用途是与一个团队中的多个开发人员同步数据库更改；当您在本地数据库中进行一些更改时，其他开发人员可以将这些更改应用到他们的机器中。您的更改将在每个变更集中进行版本控制，如果出现问题，您可以轻松地回滚到以前的状态。除此之外，您还可以使用 Liquibase 进行数据库迁移和部署。在这篇文章中，我没有涵盖 Liquibase 的所有伟大特性，但是 Liquibase 有一本非常好的手册，你一定要读一读。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章