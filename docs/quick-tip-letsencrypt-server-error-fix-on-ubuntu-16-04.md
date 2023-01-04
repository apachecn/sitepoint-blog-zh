# 快速提示:Ubuntu 16.04 上的 LetsEncrypt“服务器”错误修复

> 原文：<https://www.sitepoint.com/quick-tip-letsencrypt-server-error-fix-on-ubuntu-16-04/>

我最近不得不为我的服务器更新 HTTPS 证书，并且遇到了麻烦。

![Vector icon of server with X mark, indicating a failed state, like unrenewed Letsencrypt certificates](img/06696101114ba6baba3670fb37b7d127.png)

命令`sudo letsencrypt renew`产生的错误如下:

```
Processing /etc/letsencrypt/renewal/bitfalls.com.conf
2017-02-06 07:43:08,126:WARNING:letsencrypt.cli:Attempting to renew cert from /etc/letsencrypt/renewal/bitfalls.com.conf produced an unexpected error: 'server'. Skipping.
Processing /etc/letsencrypt/renewal/test.bitfalls.com.conf
2017-02-06 07:43:08,408:WARNING:letsencrypt.cli:Attempting to renew cert from /etc/letsencrypt/renewal/test.bitfalls.com.conf produced an unexpected error: 'server'. Skipping. 
```

为了节省您的一些搜索和实验，该错误是由更新配置文件中缺少的`server`配置条目引起的。要解决这个问题，您可以完全重新开始(即删除`/etc/letsencrypt`文件夹并重新生成所有内容)，或者手动插入丢失的配置条目。下面是你如何做后者。

## 更新 LetsEncrypt 的配置文件

如果您进入`/etc/letsencrypt/renewal`，您将看到您当前服务器的证书更新文件:

```
ls /etc/letsencrypt/renewal 
```

你们大多数人只会有一个，除非你定义了子域，在这种情况下，你应该看到你通过 HTTPS 服务的每个子域一个证书。将以下字符串复制到剪贴板:

```
server = https://acme-v01.api.letsencrypt.org/directory 
```

这个模拟服务器条目值需要放入`/etc/letsencrypt/renewal`中的每个文件，但是在`[[webroot_map]]`条目的之前放入**(如果它存在，否则放在末尾)。**

这里有一个快捷方式脚本，你可以粘贴到`renewal`文件夹中，它会自动将这个字符串插入到每个文件的正确位置:

```
sed -i "/[[webroot_map]]/i server = https://acme-v01.api.letsencrypt.org/directory" *.conf 
```

编辑并保存文件后，运行 renewal 命令应该可以了:

```
sudo letsencrypt renew 
```

请记住，如果您使用的是旧的手动安装的 LetsEncrypt 版本，请使用以下命令安装 apt 版本:

```
sudo apt-get install letsencrypt 
```

希望这给你省了些麻烦！

## 分享这篇文章