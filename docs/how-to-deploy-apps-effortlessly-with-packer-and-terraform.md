# 如何使用 Packer 和 Terraform 轻松部署应用程序

> 原文：<https://www.sitepoint.com/how-to-deploy-apps-effortlessly-with-packer-and-terraform/>

*本文原载于[阿里云](https://int.alibabacloud.com/m/1000013625/)。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

**你认为充分利用阿里云服务有更好的建议吗？请告诉我们，并有机会赢取 Macbook Pro(以及其他很酷的东西)。[点击](https://www.sitepoint.com/alibaba-competition)了解更多信息。**

阿里云发布了一份非常整洁的关于 DevOps 的白皮书，读起来非常有趣。它展示了" *DevOps 是一种超越简单实施敏捷原则来管理基础设施的模型。John Willis 和 Damon Edwards 用术语 CAMS 定义了 DevOps:文化、自动化、度量和共享。DevOps 寻求促进开发和运营团队之间的协作*。

这大致意味着，在一个旨在连接软件开发和基础设施管理的团队中，有一个新的角色或心态。这个角色需要了解这两个领域，并利用如今越来越重要的云范例。但是 DevOps 实践并不局限于大型企业。作为开发人员，我们可以轻松地将 DevOps 融入到我们的日常任务中。通过本教程，您将会看到只使用几个配置文件来编排整个部署是多么容易。我们将在阿里云[弹性计算服务(ECS)](https://int.alibabacloud.com/m/1000012785/) 实例上运行我们的应用程序。

## 什么是 Packer？

Packer 是 Hashicorp 开发的开源 DevOps 工具，用于从单个 JSON 配置文件创建图像，这有助于长期跟踪其变化。该软件是跨平台兼容的，可以并行创建多个映像。

如果你有自制软件，只需输入`brew install packer`即可安装。

它基本上创建了*随时可用的*映像，其中包含操作系统和一些额外的软件，可随时用于您的应用程序，*就像创建您自己的发行版*。假设您想要 Debian，但是默认情况下您内置了一些定制的 PHP 应用程序。有了 Packer，这很容易做到，在本指南中，我们将创建一个。

## 什么是 Terraform？

部署时，我们有两大任务要完成。一种是将实际的应用程序打包到一个合适的环境中，创建一个映像。另一个大任务是在应用程序将要驻留的地方创建底层基础设施，这是托管它的实际服务器。

为此，由包装公司 Hashicorp 制造的 Terraform 作为一种非常有趣和强大的工具出现了。基于与 Packer 相同的原则，Terraform 让你只需使用一个配置文件(这次是 TF 格式的)就可以在阿里云中构建基础设施，同时还有助于版本控制和清楚地了解所有位如何在你的应用程序下工作。

要安装 Terraform 和阿里云官方提供商，请按照另一篇文章中的说明进行操作。

## 我们想要达到的目标

正如本文开头提到的，我们将以纯 DevOps 的方式创建和部署一个简单的 PHP 应用程序——也就是说，负责部署的每个部分，从运行它的软件到支持它的底层基础设施。

## 步伐

出于 KISS 原则的考虑，我们将创建一个基于 **docker-compose** 的应用程序，使用其 ICAO 机场代码从机场检索 [METAR](https://en.wikipedia.org/wiki/METAR) 天气数据，并从美国国家气象局提取数据。然后，我们将使用 Ubuntu 通过 Packer 创建映像，并使用 Terraform 的映像部署基础架构。

### PHP 应用程序

为了简单起见，我创建了现成的应用程序。你可以在这里找到源代码[来看看，它包括一个 index.php，2 个 JavaScript 文件来解码 METAR 数据和一点 CSS 和一个 PNG 图像来使它不那么无聊。它甚至能指示风向！但是不要担心，此时您不需要克隆存储库。](https://github.com/roura356a/metar)

该应用程序是基于 **docker-compose** 的，是我们稍后将作为 Packer 的一个依赖项安装的。

### 使用 Packer 构建图像

让我们把手弄脏吧！为此，我们需要在电脑的某个地方创建一个文件夹，比如说`~/metar-app`。然后让`cd`进入并创建一个名为`metar-build.json`的文件，其内容如下:

```
{
  "variables": {
    "access_key": "{{env `ALICLOUD_ACCESS_KEY`}}",
    "region": "{{env `ALICLOUD_REGION`}}",
    "secret_key": "{{env `ALICLOUD_SECRET_KEY`}}"
  },
  "builders": [
    {
      "type": "alicloud-ecs",
      "access_key": "{{user `access_key`}}",
      "secret_key": "{{user `secret_key`}}",
      "region":"{{user `region`}}",
      "image_name": "metar_app",
      "source_image": "ubuntu_16_0402_64_20G_alibase_20180409.vhd",
      "ssh_username": "root",
      "instance_type": "ecs.t5-lc1m1.small",
      "internet_charge_type": "PayByTraffic",
      "io_optimized": "true"
    }
  ],
  "provisioners": [
    {
      "type": "shell",
      "script": "base-setup"
    }
  ]
} 
```

紧挨着它的是一个名为`base-setup`的文件，其内容如下:

```
#!/usr/bin/env bash

apt-get update && apt-get install -y apt-transport-https ca-certificates curl git-core software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
apt-get update && apt-get install -y docker-ce docker-compose
curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-`uname -s`-`uname -m` -o /usr/bin/docker-compose

mkdir /var/docker
git clone https://github.com/roura356a/metar.git /var/docker/metar 
```

一旦准备好这两个文件，就可以运行`packer build metar-build.json`并等待它完成。请注意，要实现这一点，您的机器中需要有 3 个环境变量，并为您指定相关的值:`ALICLOUD_REGION`、`ALICLOUD_ACCESS_KEY`和`ALICLOUD_SECRET_KEY`。这一步需要一段时间，因为它会创建一个 ECS，在其中安装所有软件，然后停止实例，创建它的快照，最后创建整个系统的映像。

图像完成后，打包机将输出`==> Builds finished`。很好，我们有一个可以使用的图像了！我们准备继续下一步。

### 使用 Terraform 部署基础设施

是时候创建 ECS 实例了。为此，在同一个文件夹中，我们将创建一个名为`main.tf`的文件，其内容如下:

```
provider "alicloud" {}

data "alicloud_images" "search" {
  name_regex = "metar_app"
}

data "alicloud_instance_types" "search" {
  instance_type_family = "ecs.xn4"
  cpu_core_count = 1
  memory_size = 1
}

data "alicloud_security_groups" "search" {}

data "alicloud_vswitches" "search" {}

resource "alicloud_instance" "app" {
  instance_name = "metar_app"
  image_id = "${data.alicloud_images.search.images.0.image_id}"
  instance_type = "${data.alicloud_instance_types.search.instance_types.0.id}"

  vswitch_id = "${data.alicloud_vswitches.search.vswitches.0.id}"
  security_groups = [
    "${data.alicloud_security_groups.search.groups.0.id}"
  ]
  internet_max_bandwidth_out = 100

  password = "Test1234!"

  user_data = "${file("user-data")}"
}

output "ip" {
  value = "${alicloud_instance.app.public_ip}"
} 
```

在它的右边，用下面的代码创建一个名为`user-data`的文件:

```
#!/usr/bin/env bash

cd /var/docker/metar && docker-compose up -d 
```

为了清楚起见，此时我们应该有这样一个文件结构:

```
metar_app/
├── metar-build.json
├── base-setup
├── main.tf
└── user-data 
```

准备部署。运行`terraform init`，然后`terraform plan`检查一切正常，运行`terraform apply`启动流程。

构建基础设施时，Terraform 将输出新创建的 ECS 实例的 IP。比方说，`111.111.111.111`。

## 测试

如果一切顺利，你将能够前往`http://111.111.111.111/LESO`并看到来自圣塞瓦斯蒂安机场的最新天气报告，该机场位于西班牙北部，拥有世界上最美丽的进近之一。

## 包扎

看，几乎不费吹灰之力，您就为您的应用创建了一个**完整的 DevOps 部署**。这意味着你和你的团队维护任何与它的发布周期、基础设施更新相关的东西将会变得更加容易，并且会提高它的正常运行时间。在日常生活中，不再需要直接摆弄主机和 Linux 命令。

## 分享这篇文章