# 备份 MySQL

> 原文：<https://www.sitepoint.com/backing-up-mysql/>

有几种方法可以备份 MySQL 数据，但是，自动化无疑会使这个过程更容易。确保您的备份数据存储在远程，进一步支持您的业务连续性，以防因硬件故障等原因需要将数据恢复到新系统上。

花了一些时间在我的网络上搜寻(不需要重新发明轮子来研究一个有效的备份程序)，我偶然发现了一些来自 PJ·多兰的备份脚本。

PJ (Patrick)是华盛顿 DC 地区的一名网页设计师，以前曾与卡托研究所网站有联系，他愿意分享他正在使用的两个脚本。我喜欢这些脚本，因为它们很简单，并且包括自动删除远程服务器上的备份以实现业务连续性的功能。

第一个用于标准 MySQL 备份，如下所示:

 `#!/bin/bash`

 `#########################配置变量# T0 # # # # # MySQL 配置变量# # # # T1 # # # # # # # # # # #

# MySQL 主机名
DBHOST='localhost '

# MySQL 用户名
DBUSER='root '

# MySQL 密码
DBPASSWD='password '

############## # # # # # # # # # # # #
# # # # # # # # # FTP 配置变量# # # # # # # # # # # # # #

# FTP 主机名
FTPHOST='www.example.com '

# FTP Username
FTPUSER='username'

# FTP 密码
FTPPASSWD='password '

#转储文件的本地目录
Local dir =/path/to/Local/Directory/

#用于异地备份的远程目录
Remote dir =/path/to/Remote/Directory/

#异地前缀。tar 文件备份
TARPREFIX=db1

##########################如有必要，请编辑以下内容#################

cd $LOCALDIR
后缀=`eval date +%y%m%d '

DBS = ' MySQL-u $ DBUSER-p $ DBPASSWD-h $ DBHOST-e "显示数据库" '

对于$DBS
中的数据库，如果[ $DATABASE！= "数据库"]；然后
FILENAME = $ SUFFIX-$ DATABASE . gz
MySQL dump-u $ DBUSER-p $ DBPASSWD-h $ DBHOST $ DATABASE | gzip-best>$ local dir $ FILENAME
fi
完成

chmod 400 $LOCALDIR*.gz

取-cf $ tarprefix- $ suffix.tar .取$后缀-*.gz

ftp -n $FTPHOST <

这是一个简单的脚本，在使用 ftp 将数据移动到远程服务器之前，使用 mysqldump 和 tar/gzip 压缩文件。如果您更喜欢通过 ssh 运行传输，可以很容易地修改这个脚本和下面的脚本来使用 scp。

第二个脚本被更新为使用 mysqlhotcopy，并且专门用于 ISAM/MYISAM 表。下面的脚本不能用于 InnoDB 表。

 `#!/bin/bash`

 `###配置变量

DBHOST = ' localhost '
DBUSER = ' root '
DBPASSWD = ' password '
FTP phost = ' FTP . example . com '
FTPUSER = ' username '
FTPPASSWD = ' password '
LOCALDIR =/path/to/local/
remote dir =/path/to/local/
tar prefix = db1

###不要编辑该行以下的任何内容

cd $LOCALDIR
后缀=`eval date +%y%m%d '

DBS = ' MySQL-u $ DBUSER-p $ DBPASSWD-h $ DBHOST-e "显示数据库" '

对于$DBS
中的数据库，如果[ $DATABASE！= "数据库"]；然后
FILENAME = $ SUFFIX-$ DATABASE . tar . gz
mysqlhotcopy-u $ DBUSER-p $ DBPASSWD $ DATABASE $ LOCALDIR
tar-czf $ LOCALDIR $ FILENAME $ LOCALDIR $ DATABASE
RM-RF $ LOCALDIR $ DATABASE
RM-RF $ LOCALDIR $ DATABASE-replicate
fi
完成

chmod 400 $LOCALDIR*.tar.gz

取-cf $塔尔前缀$后缀。取$后缀-*.tar.gz

ftp -n $FTPHOST <

建议对这些 shell 脚本使用 700 权限，以将它们限制为 root 用户，因为一旦使用您的信息对它们进行更新，它们就会包含有关用户名、密码和多台服务器路径的大量信息。

然后可以将它们添加到 cron 中，按照您喜欢的时间表运行。`` 

## `分享这篇文章`