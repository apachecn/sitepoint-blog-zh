# 用 ROS 在阿里云上快速部署 WordPress & phpMyAdmin

> 原文：<https://www.sitepoint.com/quickly-deploy-wordpress-phpmyadmin-on-alibaba-cloud-with-ros/>

*本文原载于[阿里云](https://int.alibabacloud.com/m/1000013627/)。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

**你认为充分利用阿里云服务有更好的建议吗？请告诉我们，并有机会赢取 Macbook Pro(以及其他很酷的东西)。[点击](https://www.sitepoint.com/alibaba-competition)了解更多信息。**

这篇文档展示了如何使用 [ROS](https://int.alibabacloud.com/m/1000012787/) 模板，通过单击来部署 WordPress 站点和 phpMyAdmin 应用程序。

## 介绍

许多用户没有建立和管理网站的内部技术能力。他们的团队可以管理内容，但不能管理随之而来的基础架构。当前可用的解决方案适用于有限时期的基本需求，但是一旦它们需要定制、高可用性和可伸缩性，就需要支持扩展的附加解决方案。

本文中讨论的 ROS 栈模板([WordPress cluster-phpmyadmin . ROS](https://int.alibabacloud.com/m/1000012788/?spm=a1z389.11499242.0.0.7b1c24134aji0Q))有助于解决这类用户的高可用性和可伸缩性需求。只需点击一下，它就可以创建整个 [VPC](https://int.alibabacloud.com/m/1000012789/) 栈、[服务器负载均衡器](https://int.alibabacloud.com/m/1000012790/)、[自动伸缩](https://int.alibabacloud.com/m/1000012791/)、 [ECS](https://int.alibabacloud.com/m/1000012792/) 、 [RDS](https://int.alibabacloud.com/m/1000012793/) 等实例，部署 WordPress 和 phpMyAdmin，并配置自动伸缩，以保证无需人工干预即可添加和配置任何新实例。

## 先决条件

*   OSS 存储桶的可用性。
*   对 OSS 存储桶具有读写权限的 RAM 用户。
*   了解 ROS，能够从控制台创建 ROS 堆栈模板和堆栈。

## 架构概述

![Architecture](img/698b13cbcc6858f078a5352c76db78bb.png)

该图提供了基于模板[WordPress cluster-phpmyadmin . ROS](https://int.alibabacloud.com/m/1000012788/?spm=a1z389.11499242.0.0.7b1c24134aji0Q)生成的部署架构的概述。

三种类型的用户访问基础架构:

**终端用户**，他们通过一个解析为公共服务器负载平衡器实例的 URL 访问 WordPress 上的网站。

WordPress 托管在 Apache web 服务器上。服务器将它们的文档根设置为`/wwwroot`，该文档位于使用 OSSFS(阿里云官方提供的基于 FUSE 的文件系统)的跨 web 服务器共享的 OSS bucket 上。

拥有 OSS bucket 访问权限的 RAM 用户可以在 ECS 实例上挂载 OSS bucket。

MySQL 数据库的 RDS 保存了 WordPress 的内容，可以通过 intranet 连接字符串从 web 服务器访问。

**系统管理员**，可以通过 SSH 登录 JumpBox (bastion 主机)访问 VPC 环境。

JumpBox 拥有弹性 IP，可通过互联网访问。

通过 JumpBox 的访问是为了管理 VPC 内部的实例。

phpMyAdmin 安装在 JumpBox 上，可以通过互联网访问。

这样，管理员可以管理 RDS 数据库。

**内容管理者**，他们可以通过互联网访问 WordPress 管理控制台。

对所有这些服务的访问可以由安全组控制，并且可以根据环境进行配置。

## 模板概述

点击[WordPress cluster-phpmyadmin . ros](https://int.alibabacloud.com/m/1000012788/?spm=a1z389.11499242.0.0.7b1c24134aji0Q)下载 ROS 栈模板使用。

**注:**在模板中，ZoneId 设置为 **eu-central-1a** ，ImageId 为 **m-gw8efmfk0y184zs0m0aj** 。您可以根据 ROS 控制台中支持的区域和映像修改 ZoneId 和 ImageId。登录 [ROS 控制台](https://ros.console.aliyun.com "ROS console")，点击 **ECS 实例信息**，选择一个区域，点击 **ECS 区域**或 **ECS 图像**查看该区域支持的所有区域或图像。

基于 WordPress cluster-phpmyadmin . ROS 堆栈模板，系统为 JumpBox 创建 VPC、服务器负载平衡器、VSwitch、NAT 网关、安全组和 ECS 实例，为 JumpBox 创建弹性 IP，为 ECS 实例创建自动扩展，并创建 RDS 实例。

需要以下输入参数才能使资源栈足够通用，可以部署给任何地区的任何用户。

![parameters1](img/9b07cb0bdc6d5405c937c201371b76eb.png)

![parameter2](img/62b12c88f0c516aa94c88a4984f1573b.png)

根据模板，系统在 JumpBox 上安装 httpd、mysql-client、PHP、OSSFS、phpMyAdmin 和 WordPress，并在 ALIYUN::ECS::Instance 资源的 UserData 部分配置它们。

下面是 JumpBox 的 UserData 部分的一个片段。

```
 "ossbucketendpoint=",
   {
   "Ref": "OSSBucketEndPoint"
   },
   "\n",
   "DatabaseUser=",
   {
   "Ref": "MasterUserName"
   },
   "\n",
   "DatabasePwd=",
   {
   "Ref": "MasterDBPassword"
   },
   "\n",
   "DatabaseName=",
   {
   "Ref": "DBName"
   },
   "\n",
   "DatabaseHost=",
   {
   "Fn::GetAtt": ["Database", "InnerConnectionString"]
   },
   "\n",
   "yum install -y curl httpd mysql-server php php-common php-mysql\n",
   "yum install -y php-gd php-imap php-ldap php-odbc php-pear php-xml php-xmlrpc\n",
   "yum install -y phpmyadmin\n",
   "sed -i \"s%localhost%$DatabaseHost%\" /etc/phpMyAdmin/config.inc.php\n",
   "sed -i \"s%Deny,Allow%Allow,Deny%\" /etc/httpd/conf.d/phpMyAdmin.conf\n",
   "sed -i \"s%Deny from All%Allow from All%\" /etc/httpd/conf.d/phpMyAdmin.conf\n",
   "sed -i \"/&lt;RequireAny&gt;/a Require all Granted\" /etc/httpd/conf.d/phpMyAdmin.conf\n",
   "chkconfig httpd on\n",
   "service httpd stop\n",
   "wget  
   https://github.com/aliyun/ossfs/releases/download/v1.80.3/ossfs_1.80.3_centos6.5_x86_64.rpm\n",
   "yum install -y ossfs_1.80.3_centos6.5_x86_64.rpm\n",
   "echo $ossbucket:$ossbucketaccesskey:$ossbucketsecret &gt;&gt; /etc/passwd-ossfs\n",
   "chmod 600 /etc/passwd-ossfs\n",
   "mkdir $ossbucketmountpoint\n",
   "chmod -R 755 $ossbucketmountpoint\n",
   "echo #This script will automount the ossbucket\n",
   "echo umount $ossbucketmountpoint &gt;&gt; /usr/local/bin/ossfs-automount.sh\n",
   "echo #Mounting OSS Bucket\n",
              "echo ossfs $ossbucket $ossbucketmountpoint -ourl=http://$ossbucketendpoint -o allow_other -o mp_umask=0022 -ouid=48 -ogid=48 &gt;&gt; /usr/local/bin/ossfs-automount.sh\n",
"chmod 755 /usr/local/bin/ossfs-automount.sh\n",
"echo /usr/local/bin/ossfs-automount.sh &gt;&gt; /etc/rc.d/rc.local\n",
"chmod +x /etc/rc.d/rc.local\n",
"/usr/local/bin/./ossfs-automount.sh\n",
"wget http://WordPress.org/latest.tar.gz\n",
"tar -xzvf latest.tar.gz\n",             
"sed -i \"s%database_name_here%$DatabaseName%\" WordPress/wp-config-sample.php\n",
"sed -i \"s%username_here%$DatabaseUser%\" WordPress/wp-config-sample.php\n",
"sed -i \"s%password_here%${DatabasePwd:-$DatabasePwdDef}%\" WordPress/wp-config-sample.php\n",
"sed -i \"s%localhost%$DatabaseHost%\" WordPress/wp-config-sample.php\n",
"mv WordPress/wp-config-sample.php WordPress/wp-config.php\n",
"cp -a WordPress/* $ossbucketmountpoint\n",
"chmod -R 755 /wwwroot/*\n",
"rm -rf WordPress*\n",
"service httpd start\n",
"done\n" 
```

UserData 部分在 OSS bucket 上部署 WordPress，OSS bucket 可以安装到使用自动缩放创建的 web 服务器上。这保证了 web 服务器拥有来自文档根目录的更新内容。

web 服务器通过自动缩放启动。httpd、PHP 和 ossutil 的安装和配置、DocumentRoo 的挂载以及服务的启动都是在自动缩放配置的 UserData 部分完成的。

以下是 web 服务器自动缩放配置的 UserData 部分的一个片段。

```
 "DatabaseHost=",
              {
                "Fn::GetAtt": ["Database", "InnerConnectionString"]
              },
              "\n",
              "yum install -y curl httpd mysql-server php php-common php-mysql\n",
              "yum install -y php-gd php-imap php-ldap php-odbc php-pear php-xml php-xmlrpc\n",
              "chkconfig httpd on\n",
              "service httpd stop\n",
              "DocumentRoot='/var/www/html'\n",
              "sed -i \"s%$DocumentRoot%$ossbucketmountpoint%\" /etc/httpd/conf/httpd.conf\n",
              "Directory='/var/www'\n",
              "sed -i \"s%$Directory%$ossbucketmountpoint%\" /etc/httpd/conf/httpd.conf\n",
"wget https://github.com/aliyun/ossfs/releases/download/v1.80.3/ossfs_1.80.3_centos6.5_x86_64.rpm\n",
              "yum install -y ossfs_1.80.3_centos6.5_x86_64.rpm\n",
              "echo $ossbucket:$ossbucketaccesskey:$ossbucketsecret &gt;&gt; /etc/passwd-ossfs\n",
              "chmod 600 /etc/passwd-ossfs\n",
              "mkdir $ossbucketmountpoint\n",
              "chmod -R 755 $ossbucketmountpoint\n",
              "echo #This script will automount the ossbucket\n",
              "echo umount $ossbucketmountpoint &gt;&gt; /usr/local/bin/ossfs-automount.sh\n",
              "echo #Mounting OSS Bucket\n",
              "echo ossfs $ossbucket $ossbucketmountpoint -ourl=http://$ossbucketendpoint -o allow_other -o mp_umask=0022 -ouid=48 -ogid=48 &gt;&gt; /usr/local/bin/ossfs-automount.sh\n",
              "chmod 755 /usr/local/bin/ossfs-automount.sh\n",
              "echo /usr/local/bin/ossfs-automount.sh &gt;&gt; /etc/rc.d/rc.local\n",
              "chmod +x /etc/rc.d/rc.local\n",
              "/usr/local/bin/./ossfs-automount.sh\n",
              "chmod -R 755 /wwwroot/*\n",
              "service httpd start\n",
              "done\n"
            ] 
```

对于寻求端到端环境的用户来说，这是一个很好的解决方案，对于解决方案架构师来说，这也是一个很好的解决方案，可以在 POC 中支持他们的客户。

## 进一步阅读

*   [创建堆栈](https://www.alibabacloud.com//help/doc-detail/28890.htm)。
*   使用可视化编辑器编辑堆栈模板。
*   [使用 RAM 控制资源访问](https://www.alibabacloud.com//help/doc-detail/48754.htm)。
*   [用样本模板创建资源栈](https://www.alibabacloud.com//help/doc-detail/48749.htm)。

## 分享这篇文章