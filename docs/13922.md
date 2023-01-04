# MySQL 5.0 稳定版

> 原文：<https://www.sitepoint.com/mysql-50-stable-release/>

[MySQL 5 出了](http://www.mysql.com/news-and-events/news/article_976.html)。这个稳定版本现在被推荐用于生产，所以如果你的 MySQL 4.1 安装已经过时(或者更糟，如果你还在 4.0 上)，现在可能是时候考虑更新你的安装了。

MySQL 的这一版本有效地将它与 MS SQL Server 和 Oracle 等企业数据库相提并论，提供了许多期待已久的新功能:

Stored Procedures and SQL Functions

Embed a portion of your application’s business logic directly into your database to improve performance of frequently-needed data manipulaton operations.

Triggers

Further offload business logic from your application. Respond to changes in your database by executing custom operations in response to events like row insertions, deletions, updates.

Views

Define particular table columns or joins that are accessible to certain users without granting them full access to sensitive data in the relevant tables.

Cursors

The database can keep track of your application’s current position in a large result set, so that you don’t need to cache such large result sets in your application.

Information Schema

Access information about your database tables through the virtual `information_schema` database.

XA Distributed Transactions

Perform transactions (multi-step operations that must succeed completely or not happen at all) across multiple database servers, or even non-database systems.

SQL Mode

Switch modes on-the-fly to request stricter or more standards-compliant database behaviour (e.g. raise errors insteads of warnings for tables involved in transactions).

New Federated and Archive Storage Engines

The new Federated storage engine lets you access tables actually stored in other database servers as if they were stored locally. The Archive storage engine is good for storing large quantities of infrequently-accessed data without indexes with greatly reduced overhead.

此外，许多新工具可用于管理 MySQL 服务器和从竞争数据库服务器导入数据和相关结构。

[立即获取 MySQL 5.0](http://dev.mysql.com/downloads/mysql/5.0.html)，或者前往 [MySQL AB 文章索引](http://dev.mysql.com/downloads/mysql/5.0.html)阅读一些新特性。

## 分享这篇文章