# Rails 2.1 候选版本 1 即将发布

> 原文：<https://www.sitepoint.com/rails-21-release-candidate-1-is-on-its-way/>

有消息称，Rails 2.1 RC1 已经在资源库中进行了标记，所以 gem 应该很快就可以使用了。作为一个点版本，变化不是很大——主要是错误修复和一些性能改进，但仍有一些新功能值得一看。

## 更新了时区支持

时区将最终成为 Rails 的一等公民。您可以设置时区，所有后续的时间调用都将在该时区内进行。

```
 Time.zone = "Australia/Perth"
Time.zone.now # will return something like Wed, 24 May 2008 22:56:00 WST +08:00 
```

## 更好的 Gem 依赖性

如果您在任何项目中依赖 gems(为什么不呢？代码重用和所有这些)，您可能遇到过 gem 版本控制的痛苦。Rails 2.1 将允许您规定您需要的每个 gem 的版本。

```
 config.gem "chronic", :version => '0.2.3' 
```

它还增加了一个耙子任务，可以自动为你安装合适的宝石。

```
 rake gems:unpack 
```

## 改进的缓存

以前，Rails 开发者拥有的唯一缓存选项是基于文件片段的，这对于单个服务器设置来说没问题——但是扩展到多个服务器可能会导致同步问题，导致缓存失效。为了解决这个问题，你现在可以使用许多其他的共享缓存系统，包括内存存储、drb 存储和 mem-cache 存储。

所有非常令人兴奋的东西，很值得一试。如果您可以从可信的终端窗口运行以下命令来下载测试版 gems:

```
 gem install rails –source http://gems.rubyonrails.com/ 
```

## 分享这篇文章