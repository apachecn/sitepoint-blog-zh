# 再次介绍 Jenkins:流水线自动化测试

> 原文：<https://www.sitepoint.com/re-introducing-jenkins-automated-testing-with-pipelines/>

*这篇文章由[克里斯托弗·托马斯](https://www.sitepoint.com/author/cthomas/)和[彼得·奈森](https://www.sitepoint.com/author/pnijssen/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

* * *

随着我们的应用程序变得越来越复杂——包括 Composer 依赖项、Webpack 构建脚本和每个环境的变量——我们不可避免地会遇到这样一种情况，测试所有这些不同的复杂性变得缓慢而费力，尤其是当您为每次测试拆除并重建整个环境时。同样，代码风格可能会随着时间的推移而发生变化，创建生产就绪部署档案需要遵循一组特定的步骤。

假设，为了测试您的应用程序，您需要执行以下步骤:

1.  运行`composer install`以确保安装了依赖项
2.  对代码库中的每个 PHP 文件运行`php -l`来检查语法错误
3.  运行 PHPUnit 进行单元测试
4.  运行功能测试的代码

![Jenkins logo](img/084289f6fd70a40645c5635f919f9e08.png)

如果这些步骤中的任何一步失败，那么在错误得到解决之前，应该认为该软件不适合部署。随着您的软件变得越来越复杂，测试的数量也随着时间的推移而增加，运行完整的套件可能需要花费很多时间，这会减慢您的开发人员，进而减慢发布过程。

为了帮助克服这个问题，您可以在开发工作流中引入一个构建服务器。构建服务器运行一个软件，使您能够在后台反复运行一系列步骤，如果其中一个步骤失败，构建服务器会通知您问题。如果你对一个开源项目有所贡献，你可能会看到像 [TravisCI](https://travisci.org) 或 [CircleCI](https://circleci.com/) 这样的构建服务器在运行。例如，TravisCI 测试了 [Laravel 项目](http://github.com/laravel/framework)上的每个 pull 请求，以确保更改不会破坏任何测试，然后 [StyleCI](https://styleci.io) 确保代码更改与项目代码风格相匹配。

![laravel-checks.png](img/84d8e9e1c9466f7957e430ee6353fe31.png)

Jenkins 是一个流行的开源构建服务器，今年发布了 2.0 版本。这个新版本的主要特性之一是包含了(以前可选的)pipelines 插件作为核心特性。

管道是一组完全可定制的步骤，可以运行这些步骤来测试和构建您的代码。管道是用 Groovy 脚本语言编写的，它的语法非常简单，很容易上手。例如，如果您想将前面描述的测试步骤描述为一个管道，它可能看起来像这样:

```
node {
    stage("composer_install") {
        sh 'composer install'
    }

    stage("php_lint") {
        sh 'find . -name "*.php" -print0 | xargs -0 -n1 php -l'
    }

    stage("phpunit") {
        sh 'vendor/bin/phpunit'
    }

    stage("codeception") {
        sh 'vendor/bin/codecept run'
    }
} 
```

`node`语句告诉 Jenkins 分配一个构建节点(Jenkins 可以在单服务器模式下运行，也可以在多节点设置下运行)。节点块中有多个阶段，每个阶段执行一个特定的操作。Jenkins 将依次运行每个阶段，如果其中任何一个阶段失败，那么整个构建都会失败，Jenkins 将停止执行。

从这个简单的例子中，您可以很容易地添加其他测试的额外阶段，告诉 Jenkins 为成功或失败的构建发送一个 Slack 通知，将成功测试的代码推入一个发布分支，或者将一个 pull 请求标记为可以合并。

## 安装 Jenkins

Jenkins 很容易安装，在本教程中，我们将使用 Laravel Homestead 来提供一个一致的虚拟环境，这样您就可以在本地与 Jenkins 一起玩了。

第一步是安装 Laravel Homestead——这里有一个[有用的指南](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)。您只需要启动并运行虚拟机，我们不需要在`homestead.yaml`文件中配置任何站点。

随着您的 Homestead 虚拟机的启动和运行，使用`vagrant ssh`SSH 进入它。

Homestead 附带了我们需要的所有依赖项，即 Git、PHP 和 Composer，所以我们只需要安装 Jenkins 本身。按照 [Jenkins Debian 包指南](https://pkg.jenkins.io/debian-stable/)我们需要执行以下步骤。

运行`wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -`。这个命令将代码签名密钥添加到 Aptitude，这样来自这个存储库的 Jenkins 包将被信任。

接下来，需要更新包源列表，以便 Aptitude 了解 Jenkins 包存储库。执行`echo "deb https://pkg.jenkins.io/debian-stable binary/" | sudo tee -a /etc/apt/sources.list`。

最后，我们需要 Aptitude 更新其可用包的缓存，然后安装 Jenkins:

```
sudo apt-get update && sudo apt-get install jenkins 
```

安装 Jenkins 大约需要五分钟，因为它有相当多的依赖项也需要安装。

一旦安装了 Jenkins，在你的浏览器中打开[http://192 . 168 . 10 . 10:8080](http://192.168.10.10:8080)(或者你的虚拟主机配置的 URL 如`homestead.app`)，你应该会看到一个标题为“解锁 Jenkins”的页面。

![unlock-jenkins.png](img/378f38df412b87ca0423aba3cbb9499b.png)

在 VM 中，运行下面的命令–`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`,一个随机的数字和字母字符串将被打印到控制台。将该字符串复制并粘贴到浏览器的文本字段中，然后按下*“继续”*。

![customise-jenkins.png](img/9176961130505de87bb7e97e624a47d6.png)

您将看到两个选项——选择*“安装建议的插件”*,然后等待几分钟，等待插件被下载和安装。

![create-admin-user.png](img/857db3d3bec84c55663f3651ca70faff.png)

在下一个屏幕上，输入管理员用户的详细信息，然后按*“保存并完成”*。

![jenkins-dashboard.png](img/54b1b48260d1f14fe58ae6ed30a7caf7.png)

Jenkins 现在已经安装并配置好了！

## 创造你的第一份工作

设置好 Jenkins 后，我们将创建一个新的构建作业，该作业将执行以下操作:

1.  查看`laravel/framework`的最新版本
2.  安装编写器依赖项
3.  运行 PHPUnit

点击仪表板上的*“创建新任务”*链接(或“新项目”链接-它们做同样的事情)。

![create-new-item.png](img/a5c9352eb09d8e8b3dfe41fc67536726.png)

输入名称`laravel`，选择“管道”，然后按*“确定”*。

现在，您将看到该作业的配置编辑器。向下滚动到管道部分，并输入以下脚本:

```
node {
    stage('preparation') {
        // Checkout the master branch of the Laravel framework repository
        git branch: 'master', url: 'https://github.com/laravel/framework.git'
    }
    stage("composer_install") {
        // Run `composer update` as a shell script
        sh 'composer install'
    }
    stage("phpunit") {
        // Run PHPUnit
        sh 'vendor/bin/phpunit'
    }
} 
```

按“保存”。您将被重定向到作业概述页面，几秒钟后，您将看到作业正在启动。如果没有，只需点击左侧菜单中的“立即构建”链接。

![first-build.png](img/4dd4162e0e3ed9fb8d04cf5d4557161a.png)

随着我们在管道脚本中定义的每个阶段的开始和结束，阶段视图将会更新。

如果您单击某个阶段，将会出现一个覆盖图，显示该阶段的日志输出，这对调试非常有用。或者，您可以点击左侧菜单中的作业，然后点击*“控制台”*。

所有 Jenkins 作业都有自己的工作区，在这个工作区中，您可以安全地创建任何临时文件，这些文件可能是您构建区域的一部分。该作业的工作区位置可在`/var/lib/jenkins/workspace/laravel`中找到。默认情况下，Jenkins 不会为每个构建清除这个工作区。如果需要这样做，可以在管道脚本的开头添加一个阶段，如下所示:

```
stage('cleanup') {
    // Recursively delete all files and folders in the workspace
    // using the built-in pipeline command
    deleteDir()
} 
```

## 詹金斯档案

对于我们的第一个作业，我们在作业设置界面中编辑了管道脚本。然而，将这个脚本与我们的代码一起放在版本控制中会更有用。

有了 Jenkins 的管道，你可以在你的代码库的根目录下将你的管道写成一个`Jenkinsfile`脚本，它将被发现并运行。它在概念上类似于您可能已经熟悉的`.travis.yml`或`.circleci.yml`文件。

在 [laravel/framework Github 项目页面](https://github.com/laravel/framework)上，点击右上角的*“fork”*按钮，然后在弹出的页面上点击你的个人资料。

创建一个名为`jenkinsfile`的新分支，然后创建一个新文件(你可以直接在 Github 中这样做，如果你不想的话可以不克隆这个库)。

![create-jenkinsfile.png](img/d1fc6ada2cd782c65daccffce8530d3c.png)

输入以下脚本并提交文件:

```
node {
    stage("composer_install") {
        // Run `composer update` as a shell script
        sh 'composer install'
    }
    stage("phpunit") {
        // Run PHPUnit
        sh 'vendor/bin/phpunit'
    }
} 
```

现在回到 Jenkins，点击左侧菜单中的*“配置”*按钮。向下滚动到管道部分，并将“定义”下拉菜单更新为`Pipeline script from SCM`。

![pipeline-from-scm.png](img/f1b7ad8b37db3496d06fcd98b6059e48.png)

输入分叉存储库的详细信息，然后点击*“保存”*。

![second-build.png](img/69d6eda6b58a7f2ff29e4ef51caf7a5a.png)

在工单概览屏幕上，点击左侧菜单中的*“立即构建”*。你应该有一个成功的构建。

## 多分支管道项目

大多数软件项目利用存储库中的多个分支来定义项目的各个阶段。例如:

*   `master`分支代表当前的生产版本
*   `develop`分支代表当前的分级发布
*   `feature/new-profile-page`分支可以表示正在进行的工作特征。

对于每一个分支，您可能想要运行许多定制的动作——例如，每当有一个`master`或者`develop`分支的提交，您想要运行一个完整的单元和集成测试的弹幕，而在所有其他分支上，仅仅单元测试就足够了。

`Jenkinsfile`是用 Groovy 编程语言编写的，这意味着我们有一个完整的脚本语言可以使用。

在这个例子中，我添加了一个 if 语句来检查分支名称；如果是`master`或`develop`，那么詹金斯应该运行一个`integration_tests`阶段。

接下来是一个 switch 语句，它根据分支名称创建一个不同的部署档案，并将其注册到代码部署服务中(在本例中，我将演示如何使用 Amazon Web Service 的 CodeDeploy 服务)。

```
node {
    stage("composer_install") {
        // Run `composer update` as a shell script
        sh 'composer update'
    }
    stage("phpunit") {
        // Run PHPUnit
        sh 'vendor/bin/phpunit'
    }

    // If this is the master or develop branch being built then run
    // some additional integration tests
    if (["master", "develop"].contains(env.BRANCH_NAME)) {
        stage("integration_tests") {
            sh 'vendor/bin/behat'
        }
    }

    // Create new deployment assets
    switch (env.BRANCH_NAME) {
        case "master":
            stage("codedeploy") {
                sh "aws deploy push --application-name My_App_Production --s3-location s3://my-app-production/build-${env.BUILD_NUMBER}.zip"
            }
            break
        case "develop":
            stage("codedeploy") {
                sh "aws deploy push --application-name My_App_Staging --s3-location s3://my-app-staging/build-${env.BUILD_NUMBER}.zip"
            }
            break
        default:
            // No deployments for other branches
            break
    }
} 
```

多分支管道作业同样易于设置——创建项目时，选择*“多分支管道”*作为类型:

![multibranch-pipeline.png](img/31de37bcd1d2e00f4120534bf2452304.png)

然后，在配置屏幕中，将管道定义下拉菜单设置为`Pipeline script from SCM`，并输入存储库的详细信息。

每当运行*“分支索引”*选项时(它取代了作业中的*“立即构建”*菜单项)，Jenkins 将递归地迭代存储库中的每个分支，并且对于具有`Jenkinsfile`并且最近已经改变的每个分支，将运行该分支的新构建。

## 更进一步

Jenkins 将它的流行归功于广泛的插件生态系统。

在 Jenkins 主屏幕上，点击左侧菜单中的“管理 Jenkins ”,然后点击“插件管理器”。

![jenkins-plugins.png](img/47243b3ef654b97e30e9530ebb454ead.png)

您可以安装和配置许多插件，从测试插件到与云服务的集成。

例如，如果您的团队使用 Slack，您可以启用 [Slack 通知插件](https://wiki.jenkins-ci.org/display/JENKINS/Slack+Plugin)。在主 Jenkins 设置屏幕中配置它(*“管理 Jenkins”*->*“配置系统”*)，然后您可以发送 Slack 消息作为您的管道脚本的一部分:

```
node {

    slackSend color: '#4CAF50', channel: '#devops', message: "Started ${env.JOB_NAME} (<${env.BUILD_URL}|build ${env.BUILD_NUMBER}>)"

    try {

        stage("composer_install") {
            // Run `composer update` as a shell script
            sh 'composer update'
        }
        stage("phpunit") {
            // Run PHPUnit
            sh 'vendor/bin/phpunit'
        }

        slackSend color: '#4CAF50', channel: '#devops', message: "Completed ${env.JOB_NAME} (<${env.BUILD_URL}|build ${env.BUILD_NUMBER}>) successfully"

    } catch (all) {
        slackSend color: '#f44336', channel: '#devops', message: "Failed ${env.JOB_NAME} (<${env.BUILD_URL}|build ${env.BUILD_NUMBER}>) - <${env.BUILD_URL}console|click here to see the console output>"
    }
} 
```

一旦 Jenkins 在服务器上启动并运行，您就可以轻松地配置一个作业，以便在提交到存储库时自动构建(或运行分支索引)。

对于 Github 项目，确保安装并启用了 [Github 插件](https://wiki.jenkins-ci.org/display/JENKINS/Github+Plugin)，然后在您的 Github 存储库设置中启用与“Jenkins (Github 插件)”服务的服务集成。

对于 Bitbucket 来说，这稍微复杂一些，我已经在我的博客中详细描述了所需的步骤。

您还应该确保您的 Jenkins 服务器得到适当保护——您可以在 Jenkins 中手动创建用户，使用 OAuth 将身份验证委托给 Github 或 Bitbucket，或将 Jenkins 挂接到您组织的 LDAP 目录。同样，您应该始终通过 SSL 运行 Jenkins，这样您对 Jenkins 的访问就不会被轻易拦截。

您的 Jenkins 安装还附带了一个“Snippets Generator”，可以让您查看所有启用的管道函数(比如我们已经使用过的`sh`、`slackSend`、`deleteDir()`和`scm`)。您可以在作业概述屏幕的左侧菜单中找到它的链接。

## 摘要

Jenkins 是一个非常强大的工具，可以帮助您的软件更加可靠，并为您和您的团队节省大量时间。人类真的不擅长一次又一次地执行重复的任务，然而，计算机擅长于此。

如果你的团队被超过五分钟的测试拖慢了，那么把测试任务交给 Jenkins。

如果您希望在新的生产或试运行版本可用时自动生成部署归档，Jenkins 可以做到这一点。

如果你想关注代码质量，在你的客户之前发现回归，那么 Jenkins 可以提供帮助。

请在下面留下任何问题或评论，告诉我们你最有创意的 Jenkins 管道！

**如果你喜欢这篇文章，你可能会发现下面的内容很有用:**

*   Cypress 测试:运行 Web 应用程序测试的指南
*   [使用 unittest 和 pytest 进行 Python 单元测试的介绍](https://www.sitepoint.com/python-unit-testing-unittest-pytest/)
*   [如何使用 Jest 测试 React 组件](https://www.sitepoint.com/test-react-components-jest/)
*   [使用木偶师学习端到端测试](https://www.sitepoint.com/puppeteer-end-to-end-testing/)
*   [3 种免提连续测试方法](https://www.sitepoint.com/3-methods-for-hands-free-continuous-testing/)

## 分享这篇文章