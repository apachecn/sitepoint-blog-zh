# 快速提示:在流浪者号上安装泽菲尔和法尔孔 2

> 原文：<https://www.sitepoint.com/quick-tip-install-zephir-phalcon-2-vagrant/>

这个将向您展示如何在一个 [Homestead 改进版](https://github.com/Swader/homestead_improved)实例上安装 Phalcon，并帮助您启动和运行一个示例 Phalcon 应用程序。

我们将在这个快速技巧中使用的 Phalcon 版本将是 2.0——一个预发布版本。如果你在 Phalcon 已经处于成熟的 2.x 阶段时读到这篇文章，请告诉我们，我们会更新帖子。要安装稳定的 1.x 版本，只要运行`sudo apt-get install php5-phalcon`就应该可以了。

## 步骤 1:嗨

通过[这个快速提示](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)让 HI(家园改善)启动并运行。

将另一个站点添加到 vhost 配置中，使您的站点块如下所示:

```
sites:
    - map: homestead.app
      to: /home/vagrant/Code/Laravel/public
    - map: test.app
      to: /home/vagrant/Code/phalcon_sample/public
```

## 第二步:安装 Zephir

泽菲尔语是驱动新法尔孔的语言。Phalcon 2 不是用 C 语言编写的，而是用一种称为 Zephir 的中介语言编写的，专门用于构建 PHP 扩展。点击了解更多信息[。](https://www.sitepoint.com/zephir-build-php-extensions-without-knowing-c/)

```
sudo apt-get update
sudo apt-get install gcc make re2c libpcre3-dev

cd ~/Code
git clone https://github.com/json-c/json-c.git
cd json-c
sh autogen.sh
./configure
make && sudo make install

git clone https://github.com/phalcon/zephir
cd zephir
./install -c
```

## 步骤 3:安装扩展

如果虚拟机尚未启动，运行`vagrant up`，然后通过`vagrant ssh`进入虚拟机。

```
cd ~/Code

git clone http://github.com/phalcon/cphalcon
cd cphalcon
git checkout 2.0.0
zephir build
```

这将安装扩展。您仍然需要通过将它包含在您的`php.ini`文件中来激活它。

```
sudo su
echo "extension=phalcon.so" >> /etc/php5/fpm/conf.d/20-phalcon.ini
echo "extension=phalcon.so" >> /etc/php5/cli/conf.d/20-phalcon.ini
exit
```

让我们检查一下它是否安装正确。重启 nginx 和 php5-fpm。运行`php -i | grep Phalcon`。如果您得到任何输出(例如“Author = > Phalcon Team”)，那么安装就成功了。

## 步骤 4:安装示例站点

我们将使用 Phalcon 主页作为一个示例站点。

```
cd ~/Code
git clone https://github.com/phalcon/website phalcon_sample
```

打开`/app/var/config/config.php`，将`baseurl`改为`/`，将`debug`改为`1`。由于未知原因，BaseURL 配置错误，而需要激活调试来停用当前正常工作有问题的缓存组件。这个问题将在未来得到解决，所以如果你在 2014 年 8 月之后的任何时候阅读这篇文章，请尝试使用默认设置。

## 步骤 5:更改 Nginx 配置

Phalcon 的 Nginx 配置有些不确定，因此需要进行一些定制。只需将`/etc/nginx/sites-available/test.app`(或 x.app，其中 x 是 Homestead.yaml 中定义的虚拟主机的名称)的内容替换为以下内容:

```
server {

    listen   80;
    server_name test.app;

    set $root_path '/home/vagrant/Code/phalcon_sample/public';
    root $root_path;

    index index.php index.html index.htm;

    try_files $uri $uri/ @rewrite;

    location @rewrite {
        rewrite ^/(.*)$ /index.php?_url=/$1;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    access_log off;
    error_log  /var/log/nginx/test.app-error.log error;

    error_page 404 /index.php;

    sendfile off;

    location ~ \.php$ {
	fastcgi_split_path_info       ^(.+\.php)(/.+)$;
	fastcgi_pass unix:/var/run/php5-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;

        fastcgi_param PATH_INFO       $fastcgi_path_info;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

    location ~* ^/(css|img|js|flv|swf|download)/(.+)$ {
        root $root_path;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

用`sudo service nginx restart`保存、退出并重启 Nginx。

## 步骤 6:禁用 XDebug[可选]

如果在步骤 5 之后运行应用程序时出现 502 错误，那么 Xdebug 仍然有一个未解决的 bug，它会阻止 Phalcon 2 正常工作。它正在大量开发中，这意味着它将很快得到解决，但与此同时，只需进入`/etc/php5/mods-available/xdebug.ini`并注释掉内容，然后用`sudo service php5-fpm restart`重启就可以解决问题。

## 包扎

就是这样。你现在可以通过运行`http://test.app:8000`(或者你定义的任何其他虚拟主机 URL，如果你没有像我上面那样使用`test.app`)在你的主机浏览器中访问这个站点。

![](img/4188f1a8ce1c7f90ec705f2b7d1c32a1.png)

现在就开始尝试 Zephir 和 Phalcon 2.0 代码吧！

## 分享这篇文章