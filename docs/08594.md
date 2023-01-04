# MySQL 主从复制:开始复制

> 原文：<https://www.sitepoint.com/mysql-master-slave-replication-starting-replication/>

在我的上一篇文章中，我带你经历了建立 MySQL 主从关系的第一阶段。

现在，我们让主 SQL Server 运行并为我们的客户端提供服务，这些客户端使用远程 IPs 进行连接。此外，主设备具有复制服务器 id(例如“100”)，并等待从设备与被允许复制的用户的连接(例如“repl”)。

我们还有几乎准备好启动的从属 SQL 节点。

我们现在要做的是:

1.  在 Master 上，
    1.  获取复制主机二进制日志坐标
    2.  使用 mysqldump 创建数据快照
    3.  在从机上传输数据

2.  在奴隶身上，
    1.  恢复数据快照
    2.  将从属服务器设置为开始复制。

## 详细操作

### 在主人身上

在下面的步骤 1 和 2 中，Master 数据库将被设置为只读模式。

1.  Get Master Coordinates
    1.  打开一个 MySQL 外壳，给:

        `mysql> FLUSH TABLES WITH READ LOCK;`

    2.  **重要的。**打开这个 shell，创建另一个 mysql 客户端 shell，即另一个会话:

    3.  **因此，在另一个会话**中，键入以下 mysql 命令:

        `mysql> SHOW MASTER STATUS;`

    记下列文件和位置的值。稍后在设置从属服务器以启动复制时，您将需要它们

2.  在命令提示符下，得到一个数据快照:

    `os-shell> mysqldump --all-databases --master-data > dbdump.db`

如果您有一个大型数据库，这可能需要相当长的时间。在我们的例子中，对于一个 25Gb 的数据库，大约需要 15 分钟。

4.  释放锁定，让你的主人玩

当您的数据转储完成时，只需关闭您在步骤 1 中打开的连接，您的主数据库服务器将继续为事务提供服务。

现在，您有了一个数据库文件，可以在从属服务器上使用它来恢复数据库。您必须将该文件传输到您的从属节点。在传输这个文件之前，先对其进行 tar 和 gzip 压缩，然后在从属节点上对其进行解压缩，这可能是一个好主意。

### 论奴隶

假设您已经将数据库转储文件传输到了 Slave，您将继续在此节点上工作，如下所示:

1.  Start MySQL server with `--skip-slave-start` option so that replication does not start. Here is the suggested way:

    在你的操作系统上命令行提示:

    `os-shell> mysqld_safe –-skip-slave-start`

2.  导入转储文件:

    在另一个操作系统上命令行提示:

    `os-shell> mysql –u root –p < dbdump.db`

    这将开始导入。如果您的转储文件很大，这将需要相当长的时间。

    **重要提示**:::在开始导入之前，确保 MySQL 的 datadir 和二进制日志目录上都有足够的空间。

3.  Stop MySQL Server

    Assuming that import has finished successfully, and assuming that on step 1 above you started the MySQL Server skipping slave start, you need to stop this and make sure that MySQL server is not running.

    #### 你现在在奴隶上可能会遇到的一些问题

    现在的问题是，您还恢复了系统数据库。这些来自你的主服务器，它有一个不同的 IP。这将意味着“root”用户现在可能无法从本地从机访问 MySQL 服务器。

    此外，“root”可能有不同的密码。在 Debian 机器上，这是在 MySQL 安装目录下的文件`debian.cnf`中加密的。

    您可以将`debian.cnf`文件从您的主机带到您的从机。或者您可以更改/重置从属机器上的“root”密码。

    提示:您可以更改/重置 MySQL 服务器上的“root”密码，如下所示:

    *   启动 MySQL，这样它就不会询问密码。另外，确保它没有开始复制:

        `os-shell> mysqld_safe –-skip-slave-start –skip-grant-tables`
    *   然后连接`mysql –u root`，发出命令更新‘root’密码，如下:

        `mysql> use mysql;`

        `mysql> update user set password=PASSWORD(‘new-password’) WHERE User = ‘root’;`

        `mysql> flush privileges;`

    *   停止 MySQL 服务器，跳过从启动表和授权表。

5.  跳过从启动

    `os-shell> mysqld_safe –-skip-slave-start`
    启动 MySQL 服务器
6.  在从服务器上设置主服务器配置

    在 MySQL 提示符下执行以下命令:

    `mysql > CHANGE MASTER TO MASTER_HOST=’10.100.10.80’, MASTER_USER=’repl’, MASTER_PASSWORD=’slavepassword’, MASTER_LOG_FILE=’mysql-bin.000003’, MASTER_LOG_POS=106;` 

    这是告诉从服务器如何连接到主服务器以进行复制的方式。注意日志坐标。这些是你从上面第一步得到的坐标。

7.  停止在上面第 4 步中启动的 MySQL。

8.  正常启动 MySQL，例如在操作系统外壳上:

    `os-shell> /etc/ini.d/mysql start`

## 检查一切是否正常

启动从 MySQL 节点后，您可以登录并发出一些命令来确保从节点运行正常。

1.  On mysql prompt, give the following command:

    `mysql> show processlist;`

    The output should be something similar to the following:

    ```
    +----+-------------+-----------+-------+---------+------+-----------------------------------------------------------------------+------------------+

    | Id | User        | Host      | db    | Command | Time | State                                                                 | Info             |

    +----+-------------+-----------+-------+---------+------+-----------------------------------------------------------------------+------------------+

    |  1 | system user |           | NULL  | Connect |  232 | Has read all relay log; waiting for the slave I/O thread to update it | NULL             |

    |  2 | system user |           | NULL  | Connect |  232 | Waiting for master to send event                                      | NULL             |

    | 39 | root        | localhost | mysql | Query   |    0 | NULL                                                                  | show processlist |

    +----+-------------+-----------+-------+---------+------+-----------------------------------------------------------------------+------------------+

    3 rows in set (0.00 sec)

    ```

    你可以看到从 Master 获取数据的 SQL 线程(在上面的输出中是带有`Id 2`的线程)和在 Slave 上执行语句的 SQL 线程(在输出中是带有`Id 1`的线程)。

2.  在 mysql 提示符下，给出下面的命令

    `mysql> show slave status;`

    这将显示 slave 的当前状态。注意`*_Errno`和`*_Error`栏。通常，您不应该看到任何表明存在错误的内容。

3.  On mysql prompt, give the following command

    `mysql> show status like ‘Slave%’;`

    You should see an output like the following:

    ```
    +----------------------------+-------+

    | Variable_name              | Value |

    +----------------------------+-------+

    | Slave_open_temp_tables     | 0     |

    | Slave_retried_transactions | 0     |

    | Slave_running              | ON    |

    +----------------------------+-------+

    3 rows in set (0.00 sec)
    ```

    注意`Slave_running` 与值`ON`在一起。

## 关于二进制日志生存时间的重要说明

如前所述，您可以让 Slave 关闭，并在再次启动时立即重新同步。但是不要让它停止服务太长时间，因为那样的话，它的内容将无法与 Master 同步。这是因为 Master 上的二进制日志不会永远离开。

名为`expire_logs_days`的变量决定自动删除二进制日志文件的天数。看看这个。这个值应该是 10，这意味着如果您让您的从属服务器停机 10 天或更长时间，它将不能在您一启动它时就进行复制，并且您将不得不从头开始所有的操作。

## 结论

这就是我们关于如何或多或少地为 Fraudpointer 应用程序实现 MySQL 复制的故事。这些步骤可能并不完全适用于您的特定情况，但它们仍然可以为您提供一个良好的开端，并向您展示实现复制的方法，即它应该在您的配置中工作的方式。

非常欢迎你的评论。我们想要他们。我们需要改进这一流程，您的反馈对我们绝对有价值。

## 分享这篇文章