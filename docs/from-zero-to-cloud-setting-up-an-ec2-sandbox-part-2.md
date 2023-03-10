# 从零到云:设置 EC2 沙箱，第 2 部分

> 原文：<https://www.sitepoint.com/from-zero-to-cloud-setting-up-an-ec2-sandbox-part-2/>

这是将 EC2 设置为应用程序开发人员的沙箱的三篇系列文章的第二篇。我假设你在亚马逊有一个 AWS 账户；如果你没有，请阅读[第 1 部分](https://www.sitepoint.com/from-zero-to-cloud-1/)，了解注册有多简单。在这一期中，我们将看到如何配置我们的开发环境并安装 Amazon Machine Instance (AMI)来运行我们的应用程序。

## 配置您的开发环境

在我们得意忘形之前，让我们确保我们的本地客户准备好了。Amazon 提供了一组丰富的命令行工具，很好地包装了 AWS 管理控制台。如果您已经做到了这一步，那么您可能更喜欢命令行。

首先，我们需要下载我们的 X.509 证书，这是一个允许我们向 Amazon 发出安全 SOAP 请求的公钥，以及我们相应的私钥。你可以随时下载你的 X.509 证书，但是亚马逊只给你一次你的私钥，不存储。确保你保存它，而不是分享它，并且记住，如果它丢失了，就没有办法找回它。

点击顶部菜单中的账户；这将把我们带到您的帐户页面。然后，单击右列中的安全凭据。向下滚动访问凭据，选择 X.509 选项卡，然后单击创建新证书。将出现一个对话框，链接到上述两个文件，您的私钥和 X.509 证书(公钥)。请记住，这是您下载私钥的唯一机会。请务必这样做，并将其存放在安全的位置。

![X.509 Certificate Popup](img/db874cb9b55fc61bf9363883b4db35ce.png)

接下来，我们需要下载 Amazon EC2 API 工具，它是 Amazon EC2 web 服务的客户端接口。去 http://aws.amazon.com/developertools/。

![AWS Developer Tools page](img/c70b9df2344130d6570cc0de1e446a5b.png)

找到并点击 Amazon EC2 API 工具。解压下载文件，我们应该有以下文件。

![Unzipped EC2 API Tools folder](img/2f0462bee01187e068d88ce490a8ea3b.png)

接下来，让我们打开一个 shell，将我们的工作站配置为与 Amazon 对话，并与我们的 EC2 环境交互。

首先，在您的主目录中创建隐藏的`.ec2`目录。

```
Last login: Wed Aug 17 22:34:19 on ttys000
MacBook-Pro:~ john2$ mkdir ~/.ec2
```

接下来，将 X.509 证书、私钥文件和`ec2-api-tools`文件夹移动到`~/.ec2`中。

```
MacBook-Pro:~ john2$ cd .ec2
MacBook-Pro:.ec2 john2$ mv ../Downloads/*.pem .
MacBook-Pro:.ec2 john2$ mv ../Downloads/ec2-api-tools-1.4.4.0 .
MacBook-Pro:.ec2 john2$ ls -las
total 16
0 drwxr-xr-x   5 john2  staff  170 Aug 17 22:38 .
0 drwxr-xr-x+ 16 john2  staff  544 Aug 17 22:35 ..
8 -rw-r--r--@  1 john2  staff  916 Aug 17 22:28 cert-{YOUR-HASH}.pem
0 drwxr-xr-x@  7 john2  staff  238 Aug  3 04:24 ec2-api-tools-1.4.4.0
8 -rw-r--r--@  1 john2  staff  924 Aug 17 22:28 pk-{YOUR-HASH}.pem
MacBook-Pro:.ec2 john2$ ls -las ec2-api-tools-1.4.4.0/
total 120
 0 drwxr-xr-x@   7 john2  staff    238 Aug  3 04:24 .
 0 drwxr-xr-x    5 john2  staff    170 Aug 17 22:38 ..
96 -rw-r--r--@   1 john2  staff  46468 Aug  3 04:19 THIRDPARTYLICENSE.TXT
 0 drwxr-xr-x@ 510 john2  staff  17340 Aug  3 04:24 bin
 0 drwxr-xr-x@  33 john2  staff   1122 Aug  3 04:24 lib
16 -rw-r--r--@   1 john2  staff   4852 Aug  3 04:19 license.txt
 8 -rw-r--r--@   1 john2  staff    539 Aug  3 04:19 notice.txt
```

接下来，编辑`~/.bash_profile`为我们的机器提供必要的 EC2 路径是个好主意。

```
MacBook-Pro:.ec2 john2$ vi ~/.bash_profile
export EC2_HOME=~/.ec2/ec2-api-tools-1.4.4.0
export PATH=$PATH:$EC2_HOME/bin
export EC2_PRIVATE_KEY=pk-{YOUR-HASH}.pem
export EC2_CERT=cert-{YOUR-HASH}.pem
export JAVA_HOME=/System/Library/Frameworks/JavaVM.framework/Home/
```

然后，确保在当前会话中使用这些变量。

```
MacBook-Pro:.ec2 john2$ source ~/.bash_profile
```

现在，我们已经将本地开发环境配置为与 EC2 对话，并通过`ec2`客户端接口发出远程命令。下一步是创建一个密钥对，我们可以用它来启动实例；启动后，通过 ssh 连接。这是一组新的不同的密钥，与我们在将 SOAP 客户端配置到 Amazon EC2 本身时创建的密钥不同。

```
MacBook-Pro:.ec2 john2$ ec2-create-keypair john2.kp > john2.kp
MacBook-Pro:.ec2 john2$ ec2-describe-keypairs
KEYPAIR	john2.kp	42:a8:19:7d:85:fc:aa:e7:a8:13:cc:c2:2b:92:b9:ee:c6:6e:07:f5
MacBook-Pro:.ec2 john2$ chmod 600 john2.kp
MacBook-Pro:.ec2 john2$ cat john2.kp 
KEYPAIR	john2.kp	42:a8:19:7d:85:fc:aa:e7:a8:13:cc:c2:2b:92:b9:ee:c6:6e:07:f5
-----BEGIN RSA PRIVATE KEY-----
SUPER SECRET STUFF
-----END RSA PRIVATE KEY-----
```

如果您遇到任何 ec2-xxx-xxxx 命令未找到消息，请确保`~/.bash_profile`来源于该特定终端会话，并且其中的 ec2 特定路径是正确的。

## 寻找一个 AMI

下一步是找到一个 Amazon 机器实例(AMI)。AMI 是操作系统实例的现成映像。您可以找到从 Windows 到 Red Hat 的许多操作系统的各种 AMI 提供程序。一个好的起点是 http://aws.amazon.com/amis。

如果你对运行 Ubuntu 感兴趣，Alestic 提供了可靠的基于 Ubuntu 的 AMI。只需进入[http://alestic.com/](http://alestic.com/)，选择您所在的地区并浏览可用的图片。

![Ubuntu AMIs on alestic ](img/f191a8d526ed126ac65757dabc7c5e43.png)

我选`us-east-1 Ubuntu 11.04 Natty EBS boot server 64-bit`。我们可以使用`ec2-describe-images`来确保 Amazon 识别我们的图片 ID，并查看图片的细节。

```
MacBook-Pro:.ec2 john2$ ec2-describe-images ami-1aad5273
IMAGE	ami-1aad5273	099720109477/ebs/ubuntu-images/ubuntu-natty-11.04-amd64-server-20110426	099720109477	available	public		x86_64	machine	aki-427d952b			ebs	paravirtual	xen
BLOCKDEVICEMAPPING	/dev/sda1		snap-7d8f0f12	8
```

现在我们准备启动我们的第一个实例！我们将通过指定`-k`参数来使用我们之前创建的密钥。这很重要，因为在后续步骤中，这个相同的密钥将用于通过 ssh 连接到我们的实例。`--instance-type`的说法最终会省下一大笔钱；我们正在构建一个开发沙箱，我们不应该需要比微实例提供的更多的资源。最后，我们传递所选 AMI 的 ID，我们在 Amazon 的 EC2 云中有了一个正在运行的 Ubuntu 服务器！

```
MacBook-Pro:.ec2 john2$ ec2-run-instances -k john2.kp --instance-type t1.micro ami-1aad5273
RESERVATION	r-5be89134	582155754520	default
INSTANCE	i-c795bfa6	ami-1aad5273			pending	john2.kp	0		t1.micro	2011-08-22T00:21:03+0000	us-east-1b	aki-427d952b			monitoring-disabled					ebs				paravirtual	xen		sg-f5a0899c	default
MacBook-Pro:.ec2 john2$ ec2-describe-instances
RESERVATION	r-03751f6c	582155754520	default
INSTANCE	i-c97328a8	ami-1aad5273			terminated	john2.kp	0		t1.micro	2011-08-20T00:48:15+0000	us-east-1a	aki-427d952b			monitoring-disabled					ebs			paravirtual	xen		sg-f5a0899c	default
RESERVATION	r-5be89134	582155754520	default
INSTANCE	i-c795bfa6	ami-1aad5273	ec2-75-101-238-254.compute-1.amazonaws.com	ip-10-245-202-88.ec2.internal	running	john2.kp	0		t1.micro	2011-08-22T00:21:03+0000	us-east-1b	aki-427d952b			monitoring-disabled	75.101.238.254	10.245.202.88			ebs					paravirtual	xen		sg-f5a0899c	default
BLOCKDEVICE	/dev/sda1	vol-58923732	2011-08-22T00:21:26.000Z
```

在我们通过 ssh 连接之前，让我们停止我们的实例，再次启动它，同时查看一下 AWS 管理控制台。

当我们通过`ec2-run-instances`启动实例时，Amazon 返回了一个惟一的实例 ID。在上面的例子中，它返回了`i-c795bfa6`。这个实例 ID 是我们用来调用未来特定于实例的操作的，例如启动、停止、创建 ami 和终止(删除)我们的实例。在这里花一点时间停止和启动您的实例，并观察 AWS 管理控制台中该实例从运行到停止再到停止的状态列。

![AWS Management Console](img/24961f6853d3447855aafb3d5c99b9e5.png)

```
MacBook-Pro:.ec2 john2$ ec2-stop-instances i-c795bfa6
INSTANCE	i-c795bfa6	running	stopping
```

将来，您不需要通过命令行停止和启动实例，但是熟悉这个过程是有帮助的。这也有助于理解两个密钥对族之间的区别。前者 X.509 用于从命令行与 EC2 交互。这些密钥对让我们可以访问 124 个`ec2`命令。后者，`john2.kp`键让我们可以访问实例本身。为什么我们只看到了后者家的一把钥匙？我们看到了私钥，亚马逊保留了公钥。但是，在将来，我们可以使用 AWS 管理控制台执行命令行客户端提供的许多操作。要调用这些操作，请选中要对其执行操作的实例的复选框，然后在“我的实例”部分顶部的“实例操作”下拉列表中找到适当的操作。

让我们启动实例备份，并第一次通过 ssh 连接。还记得我们运行`ec2-create-keypair`时 Amazon 给我们的私钥，以及我们曾经通过`ec2-run-instances`启动我们的实例吗？我们需要它通过 ssh 连接到我们的实例。我们还需要这个实例的公共 DNS 条目。您可以在 AWS 管理控制台中所选实例的 EC2-Instance details 部分中找到这一信息。复制并粘贴到您的 ssh 命令中。我们需要使用`-i`(身份文件)标志第一次连接到我们的实例。不同的 ami 第一次连接有不同的规则，但是我们选择的 Ubuntu 镜像允许用户`ubuntu`连接。

在通过 ssh 连接之前，我们需要使用`ec2-authorize`打开必要的端口。

```
MacBook-Pro:.ec2 john2$ ec2-authorize default -p 22
MacBook-Pro:.ec2 john2$ ec2-authorize default -p 80
MacBook-Pro:.ec2 john2$ ssh -i john2.kp ubuntu@ec2-50-19-132-116.compute-1.amazonaws.com
```

这是关于在 Amazon EC2 上设置开发沙箱的 3 部分系列的第 2 部分。我们已经从一个全新的账户变成了一个正在运行的 Ubuntu 实例。请继续关注，享受在亚马逊运行服务器的回报。[接下来，我们将介绍](https://www.sitepoint.com/from-zero-to-cloud-3/)设置灯组和创建我们自己的图像！

图片 via[light spring](http://www.shutterstock.com/gallery-540784p1.html)/[Shutterstock](http://shutterstock.com)

## 分享这篇文章