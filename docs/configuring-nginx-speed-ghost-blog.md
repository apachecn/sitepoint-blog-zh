# 配置 Nginx 加速你的 Ghost.io 博客

> 原文：<https://www.sitepoint.com/configuring-nginx-speed-ghost-blog/>

Ghost 是一个热门的新博客平台。它比 WordPress 更简单，专注于博客(而不是像 WordPress 一样是一个完整的 CMS)。像 WordPress 一样，你可以注册并在他们的网站上托管你的博客(付费)，或者你可以[自己下载并托管 Ghost](http://docs.ghost.org/) 。我们将重点讨论后者。

## 为什么是幽灵？

我最近在我的虚拟服务器上为我的妻子(她写我们的旅行)建立了一个博客。我说 VPS 与很多其他应用程序共享，所以我想保持效率。服务博客最有效的方式是使用静态站点生成器，如[中间人](http://middlemanapp.com/)或[杰基尔](http://jekyllrb.com/)，并使用 web 服务器如 [Nginx](http://nginx.org/) 提供静态资源。这对我来说很好——我的个人博客使用中间人，我用 Git post-receive 挂钩更新了它——但是我妻子不太喜欢命令行，所以我认为 Ghost 可能更适合。

所说的服务器也服务于许多其他的小网站和应用，所以我需要保持它的轻量级。

Ghost 是一款用 Node.js 编写的制作非常精良、非常好看的软件——但这并不意味着它是完美的。Node 在很多方面都很棒，但是在提供静态文件时，使用 Nginx 可以更有效地处理这项任务。Nginx 是一种快速、轻量级的 web 服务器，越来越受欢迎，它最擅长的事情之一是在其他应用程序面前充当代理。

今天，我将向您展示我是如何配置我的服务器的，以便:

*   Ghost 提供管理页面(唯一的动态页面)
*   Nginx 服务于静态资产(图像、JavaScript、CSS)
*   Nginx 缓存了所有非管理页面，进一步降低了负载

所有这些都会让我的服务器资源走得更远。另外，由于 Ghost 最终几乎不做任何工作，我可以将它配置为使用 [SQLite 3](http://www.sqlite.org/) 而不是更适合并行连接的东西，这是一个不错的好处。

## 在我们继续之前

安装 Nginx 和 Ghost 的过程超出了本文的范围。对于 Nginx，你可以试试 [Nginx 的文档](http://nginx.org/en/docs/)；对于 Ghost，你可以[按照 Ghost 的安装说明](http://docs.ghost.org/installation/)。

将 Ghost 配置为在端口 2368 上运行。我们将用 Nginx 将端口 80(即默认的 http 端口)代理给它。我们还将假设您运行在`example.com`上。

## 配置 Nginx 来服务静态资产

首先，您应该有这个基本的 Nginx 配置:

```
server{
    listen 80;
    server_name example.com www.example.com;
}
```

首先，我们将确保为运行在端口 2368 上的博客提供服务。为此，只需添加以下内容:

```
location / {
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header Host $http_host;
    proxy_pass http://127.0.0.1:2368;
}
```

现在，确保 Ghost 正在运行，并重启 Nginx。当你浏览你在 example.com 的主页时，你应该会看到一个空白的 Ghost 博客。

我们希望用 Nginx 提供图像和资产。Ghost 把这些藏在几个不同的地方，所以你需要化名。添加以下内容(适当地更改 Ghost 安装和主题的路径):

```
location /content/images {
    alias /path/to/ghost/content/images;
}

location /assets {
    alias /path/to/ghost/content/themes/<mytheme>/assets;
}

location /public {
    alias /path/to/ghost/core/built/public;
}

location /ghost/scripts {
    alias /path/to/ghost/core/built/scripts;
}
```

## 设置静态站点样式的缓存

为了使用尽可能少的周期，我们希望在缓存方面更加积极。因此，对于所有非管理页面，我们将配置 Nginx 完全忽略缓存控制头，只缓存每个页面一个小时。

取出另一个`proxy_pass`块。我们需要把它分成两份。第一个将代理请求到管理后端，我们不能缓存。第二个将是其余的，这将是一个复仇缓存。看起来是这样的:

```
location /ghost {
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header Host $http_host;
    proxy_pass http://127.0.0.1:2368;
}

location / {
    proxy_cache STATIC;
    proxy_cache_valid 200 60m;
    proxy_ignore_headers X-Accel-Expires Expires Cache-Control;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header Host $http_host;
    proxy_pass http://127.0.0.1:2368;
}
```

忽略头部是可选的，但是如果确实需要的话，我们不介意不时地自己清理缓存。

说到这里，您还需要配置 Nginx 的缓存路径。上面，我们已经调用了我们的配置`STATIC`，所以我们需要将这一行添加到我们的 Nginx 配置的`http`块中(我将我的放在了`/etc/nginx/nginx.conf`):

```
proxy_cache_path /data/nginx/cache levels=1:2 keys_zone=STATIC:10m inactive=24h max_size=512m;
```

这告诉 Nginx 将缓存文件保存在路径`/data/nginx/cache`中，并设置级别和各种配置。如果您想清除缓存，只需执行`rm -r /data/nginx/cache/*`来移除所有缓存的文件。

## 一切都在一起

以下是整体配置，供参考:

```
server{
    listen 80;
    server_name example.com www.example.com;

    location /content/images {
        alias /path/to/ghost/content/images;
    }

    location /assets {
        alias /path/to/ghost/content/themes/<mytheme>/assets;
    }

    location /public {
        alias /path/to/ghost/core/built/public;
    }

    location /ghost/scripts {
        alias /path/to/ghost/core/built/scripts;
    }

    location /ghost {
        proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:2368;
    }

    location / {
        proxy_cache STATIC;
        proxy_cache_valid 200 60m;
        proxy_ignore_headers X-Accel-Expires Expires Cache-Control;
        proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:2368;
    }

}
```

这就是它的全部内容。有了这个设置，你应该能够从一个小型的私人服务器为成千上万的访问者提供服务。

## 分享这篇文章