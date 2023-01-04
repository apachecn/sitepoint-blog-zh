# 闪电般的速度与 PHP-FPM 和 nginx

> 原文：<https://www.sitepoint.com/lightning-fast-wordpress-with-php-fpm-and-nginx/>

托管服务器速度很慢。他们在 Apache 的旧版本上运行旧版本的 PHP，厌恶 Digg 效应(或任何类似的流量突然涌入)。在本教程中，我将逐步展示如何构建一个能够承受首页 Digg 位置的服务器。这将意味着您的业务在最重要的时候保持在线，也就是在所有人都在关注的时候。

我们将经历为 WordPress 构建一个超级快速、防弹的定制 web 服务器的过程。我们将使用的技术栈是 Ubuntu、nginx、PHP-FPM 和 MySQL。在以后的文章中，我们将研究如何将 memcached 添加到组合中，以进一步提高性能。

## 为什么选择 VPS？

VPS 代表虚拟专用服务器。基本上，你以较低的月租价获得一台大型昂贵机器的一部分。你支付了保证数量的内存，并获得了一定数量的 CPU 能力。

一旦你有了几个网站并开始运行，这可能是一个比托管好得多的交易。然而，你必须自己管理一切，并在出现问题时照顾好你的服务器。

## 为什么选择 nginx？

Nginx 是一个小型的轻量级网络服务器和反向代理。它运行在[排名前 100 万的网络服务器中的 5.2%上。尤其是，nginx 非常适合高流量的网站。与 Apache 相比，它的轻量级特性意味着 nginx 服务器可以在更小的内存空间中运行。这使得它成为那些希望从 VPS 解决方案中获得最大性能的人的首选 web 服务器。](http://w3techs.com/technologies/overview/web_server/all)

## 为什么是 PHP-FPM？

PHP 5 . 3 . 3 版本(今年 7 月发布)包含了一个新的 FastCGI 管理器，名为 PHP-FPM。PHP-FPM 是一个守护进程，它产生管理你的在线应用程序的进程。因此，现在你的 PHP 代码由 PHP-FPM 本地运行，而不是让你的 web 服务器运行插件来显示和处理你的 PHP 代码。

对于我们的示例 WordPress 安装，我们将设置一个 nginx 服务器来服务我们的静态文件。当用户请求 PHP 页面时，nginx 服务器会将请求转发给 PHP 本身。PHP-FPM 运行自己的服务器，等待用户请求他们的页面。

这种功能分离意味着您可以看到一些令人难以置信的速度提升。

## 为什么选择 MySQL？

基本上，因为没有其他选择。目前，WordPress 只支持 MySQL。我总是使用 memcached 来减轻 MySQL 的负担。正如我提到的，我将在以后的文章中介绍 memcached 的使用。

## 把所有的放在一起

由于所有这些软件都是相对前沿的，我们将继续从源代码开始构建几乎所有的东西。这意味着您需要在系统上安装 build-essential 或等效的软件包。我假设您对 Linux 和 SSH 有基本的了解。

如果你被困在 Windows 开发中，想尝试一下这个设置，我建议在免费的 VirtualBox 虚拟化应用程序中安装一个 Ubuntu 服务器。

### 第一步:安装 nginx

我建议从源代码下载并安装 nginx，因为大多数 Linux 发行版的包管理器版本都比我们希望的要旧。Nginx 是积极开发的，我们不妨利用一下开发者的辛苦。

我们将从获取依赖关系开始；然后我们将抓取 nginx 并构建它(查看[下载页面](http://nginx.org/en/download.html)获取最新版本):

```
sudo apt-get install libpcre3 libpcre3-dev libpcrecpp0 libssl-dev zlib1g-dev
cd ~/downloads
wget http://nginx.org/download/nginx-0.8.53.tar.gz
tar zxvf nginx-0.8.53.tar.gz
cd nginx-0.8.53
```

现在，在我们运行配置之前，我们需要设置一些首选项。具体来说，nginx 应该安装到哪里。我们还将设置 nginx 来使用 SSL 模块，因此 https 适用于我们的服务器:

```
./configure --pid-path=/var/run/nginx.pid --sbin-path=/usr/local/sbin --with-http_ssl_module
```

最后，我们将进行制作和制作安装:

```
make
sudo make install
```

我们将对位于`/usr/local/nginx/conf/nginx.conf`的 nginx 配置文件进行一些快速编辑。在该文件的顶部，您会看到这两行:

```
# user nobody;
worker_processes 1;
```

取消对用户行的注释，将 nobody 改为 www-data www-data，然后将 worker_processes 改为 2 而不是 1。看看文件的其余部分；有许多日志和其他选项的设置、一个示例服务器声明和一些注释掉的示例。我们稍后将回到这个文件，但现在你可以保存并关闭它。

nginx 没有提供系统启动时可以运行的 init 脚本，但是网上有很多好的脚本。让我们抓住一个并设置它:

```
 cd ~/downloads
wget http://nginx-init-ubuntu.googlecode.com/files/nginx-init-ubuntu_v2.0.0-RC2.tar.bz2
tar xfv nginx-init-ubuntu_v2.0.0-RC2.tar.bz2
sudo mv nginx /etc/init.d/nginx 
sudo update-rc.d -f nginx defaults 
```

Nginx 现在将在您的系统启动时启动，您可以使用以下命令控制它:

```
 sudo /etc/init.d/nginx stop
sudo /etc/init.d/nginx start
sudo /etc/init.d/nginx restart 
```

就是这样！此时，您应该能够启动 nginx，在浏览器中点击 http://localhost/页面。接下来，我们将安装 PHP 5.3.3，最后，配置一切。

### 第二步:安装 PHP 5.3.3

现在我们将从源代码安装 PHP。您可以根据需要随意更改任何`./configure`参数——对我们来说，重要的是`--enable-fpm`和`--with-fpm`线:

```
 sudo apt-get install autoconf2.13 libbz2-dev libevent-dev libxml2-dev libcurl4-openssl-dev libjpeg-dev libpng-dev libxpm-dev libfreetype6-dev libt1-dev libmcrypt-dev libmysqlclient-dev libxslt-dev mysql-common mysql-client mysql-server
cd ~/downloads
wget http://us3.php.net/get/php-5.3.3.tar.gz/from/us.php.net/mirror/
tar zxvf php-5.3.3.tar.gz
cd php-5.3.3
./buildconf --force
./configure 
	--prefix=/opt/php5 
	--with-config-file-path=/opt/php5/etc 
	--with-curl 
	--with-pear 
	--with-gd 
	--with-jpeg-dir 
	--with-png-dir 
	--with-zlib 
	--with-xpm-dir 
	--with-freetype-dir 
	--with-t1lib 
	--with-mcrypt 
	--with-mhash 
	--with-mysql 
	--with-mysqli 
	--with-pdo-mysql 
	--with-openssl 
	--with-xmlrpc 
	--with-xsl 
	--with-bz2 
	--with-gettext 
	--with-fpm-user=www-data 
	--with-fpm-group=www-data 
	--enable-fpm 
	--enable-exif 
	--enable-wddx 
	--enable-zip 
	--enable-bcmath 
	--enable-calendar 
	--enable-ftp 
	--enable-mbstring 
	--enable-soap 
	--enable-sockets 
	--enable-sqlite-utf8 
	--enable-shmop 
	--enable-dba 
	--enable-sysvmsg 
	--enable-sysvsem 
	--enable-sysvshm

make
sudo make install
```

接下来，我们将通过复制默认的`php.ini`和`php-fpm.conf`文件来配置 PHP，并设置 PHP-FPM 在系统启动时运行:

```
sudo mkdir /var/log/php-fpm
sudo chown -R www-data:www-data /var/log/php-fpm
sudo cp -f php.ini-production /opt/php5/etc/php.ini
sudo chmod 644 /opt/php5/etc/php.ini
sudo cp /opt/php5/etc/php-fpm.conf.default /opt/php5/etc/php-fpm.conf
sudo cp -f sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm
sudo chmod 755 /etc/init.d/php-fpm
sudo update-rc.d -f php-fpm defaults
```

不错！现在 PHP-FPM 将在启动时作为守护进程运行。

如果您在尝试启动 PHP-FPM 时遇到以下情况或类似情况:

```
Starting pfp-fpm ................................ failed.
```

确保您的`/etc/init.d/php-fpm`文件有到 PHP-FPM 的正确路径，并确保您接触到。pid 文件，以便该进程可以运行。

```
sudo touch /var/run/php-fpm.pid
```

成功！我们现在有一个正在运行的 nginx 服务器，和一个正在运行的 PHP-FPM 服务器。我们所要做的就是在我们的站点配置中将这两者结合起来。

### 第三步:建立我们的 WordPress 网站

接下来，我们将下载 WordPress，并将其安装在我们的 localhost 目录中进行测试。我假设我们已经为我们的安装专门设置了一个用户的 MySQL 数据库；无论你使用 nginx 和 PHP-FPM 还是传统的 Apache 和 PHP 堆栈，MySQL 的设置都是完全一样的。

首先，我们将设置本地主机安装所需的目录:

```
mkdir ~/public_html
mkdir ~/public_html/localhost/
mkdir -p ~/public_html/localhost/{public,private,logs,backup}
cd ~/downloads
wget http://wordpress.org/latest.zip
unzip latest.zip
mv wordpress/* ~/public_html/localhost/public
cd ~/public_html/localhost/public
vim wp-config-sample.php
```

将您的 MySQL 配置输入 wp-config-sample.php。然后，保存文件，并将其重命名为 wp-config.php。你现在只剩下一步了！让 nginx 和 PHP-FPM 识别新的 WordPress 服务器。

### 第四步:为 PHP-FPM 配置 nginx

我们将设置 nginx 与 Debian/Ubuntu 的普通`sites-available`和`sites-enabled`文件夹一起工作，进行逐站点配置。您在`sites-available`中创建站点配置文件，然后将这些文件符号链接到`sites-enabled`来激活它们。让我们首先创建这两个文件夹:

```
sudo mkdir /usr/local/nginx/sites-available
sudo mkdir /usr/local/nginx/sites-enabled 
```

您现在需要告诉 nginx 加载`sites-enabled`中的所有配置文件。为此，再次编辑您的`/usr/local/nginx/conf/nginx.conf`文件。在`http { ... }`块内部的某个地方，添加一行代码:

```
include   /usr/local/nginx/sites-enabled/*;
```

然后，删除`nginx.conf`中的整个`server { ... }`块——这是默认的服务器配置，我们将在`sites-available`用虚拟主机替换它。

现在是精彩的部分。我们将在`sites-available`中为我们的 WordPress 站点创建一个虚拟主机。在你的`sites-available`目录下创建一个名为`localhost`(或者`wordpress`，或者其他什么)的文件。以下是要放入的内容(用您的用户名替换“用户名”):

```
server { 
  listen 80; 
  server_name localhost; 

  access_log /home/username/public_html/localhost/logs/access.log; 
  error_log /home/username/public_html/localhost/logs/error.log; 

  location / { 
    root /home/username/public_html/localhost/public; 
    index index.php index.html index.htm; 

    if (-f $request_filename) { 
      expires 30d; 
      break; 
    } 

    if (!-e $request_filename) { 
      rewrite ^(.+)$ /index.php?q=$1 last; 
    } 
  } 

  location ~ .php$ { 
    fastcgi_pass   localhost:9000;  # port where FastCGI processes were spawned 
    fastcgi_index  index.php; 
    fastcgi_param  SCRIPT_FILENAME    /home/username/public_html/localhost/public/$fastcgi_script_name;  # same path as above 
    fastcgi_param PATH_INFO               $fastcgi_script_name;
    include /usr/local/nginx/conf/fastcgi_params;
  } 
} 
```

配置相对简单:首先，我们定义 web 根目录，并指定服务器应该查找哪些索引文件。然后，我们在静态文件上设置一个 30 天的过期标题，并将任何其他请求重定向到 WordPress 目录中的`index.php`。nginx 有太多可用的配置设置，无法在这里一一介绍，但是在 nginx wiki 上有一个完整的列表。

现在剩下的就是把你的新站点添加到`sites-enabled`中。我们通过一个链接做到这一点:

```
ln -s /usr/local/nginx/sites-available/localhost /usr/local/nginx/sites-enabled/localhost
sudo /etc/init.d/nginx restart
```

打开浏览器并进入`http://locahost/`(或您的服务器所在的任何地方)。你应该看到 WordPress 安装页面。像往常一样安装你的 WordPress。当在 WordPress admin 中设置干净的 URL 时，你需要从 permalink 结构中移除`index.php`。(例如，你会看到"example.com/2010/10/10/my-post/"，而不是 example . com/index . PHP/2010/10/10/my-post/。)

就是这样！现在你的 WordPress 站点有了一个精简的服务器。在下一篇文章中，我将会看到对 nginx 设置的一些额外的配置调整，并且我们还将在组合中添加 memcached。敬请期待！

## 分享这篇文章