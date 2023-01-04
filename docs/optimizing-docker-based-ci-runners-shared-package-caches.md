# 如何使用共享包缓存优化基于 Docker 的 CI 运行程序

> 原文：<https://www.sitepoint.com/optimizing-docker-based-ci-runners-shared-package-caches/>

**在 [Unleashed Technologies](https://www.unleashed-technologies.com) ，我们使用 [Gitlab CI 和 Docker runners](https://docs.gitlab.com/ce/ci/docker/using_docker_images.html#using-docker-images) 进行持续集成测试。我们在加快构建执行速度方面付出了巨大的努力。我们进行的优化之一是在所有 CI 作业之间共享一个缓存卷，允许它们共享文件，如包下载缓存。**

配置 Docker runner 非常简单——我们只需将`volumes = ["/srv/cache:/cache:rw"]`放到我们的`config.toml`文件中:

```
concurrent = 6
check_interval = 0

[[runners]]
  name = "ut-ci01"
  url = "https://gitlab.example.com/"
  token = "xxxxxxxxxxxxx"
  executor = "docker"
  [runners.docker]
    tls_verify = false
    image = "unleashed/php:7.1"
    privileged = false
    disable_cache = false
    volumes = ["/srv/cache:/cache:rw"]
  [runners.cache] 
```

因此，所有 CI 作业都将有一个可用的`/cache`目录(该目录映射到 Docker 主机上的`/srv/cache`)。

下一步是让包管理器在作业运行像`composer install`或`yarn install`这样的命令时使用这个缓存目录。幸运的是，这些包管理器允许我们使用环境变量来配置它们的缓存目录:

*   作曲:`COMPOSER_CACHE_DIR`
*   纱线:`YARN_CACHE_FOLDER`
*   npm: `NPM_CONFIG_CACHE`
*   鲍尔:`bower_storage__packages`
*   RubyGems: `GEM_SPEC_CACHE`
*   匹普:`PIP_DOWNLOAD_CACHE`

所以我们简单地在基本映像的`Dockerfile`中添加了这些`ENV`指令:

```
ENV COMPOSER_CACHE_DIR /cache/composer
ENV YARN_CACHE_FOLDER /cache/yarn
ENV NPM_CONFIG_CACHE /cache/npm
ENV bower_storage__packages /cache/bower
ENV GEM_SPEC_CACHE /cache/gem
ENV PIP_DOWNLOAD_CACHE /cache/pip 
```

现在，每当一个任务需要安装一个包时，它将从我们的本地缓存中提取，而不是从远程服务器下载！这为我们的构建提供了显著的速度提升。

* * *

*这个快速提示最初发表在科林的博客上[，经作者许可，在此重新发布。](https://www.colinodell.com/blog/201704/optimizing-dockerbased-ci-runners-shared-package-caches)*

## 分享这篇文章