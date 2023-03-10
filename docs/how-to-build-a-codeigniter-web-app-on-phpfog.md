# 如何在 PHPFog 上构建 CodeIgniter Web 应用程序

> 原文：<https://www.sitepoint.com/how-to-build-a-codeigniter-web-app-on-phpfog/>

PHPFog 是一个 PHP 应用程序的云托管解决方案。它提供了一个免费的共享云，包括访问 MySQL 数据库，以及向您的帐户添加 3 个应用程序的能力。PHPFog 背后的主要思想是，它为您照看所有的服务器需求，让您自由地设计和编写您的应用程序。

在本文中，我将解释如何用 PHPFog 创建一个帐户，并设置您的第一个 CodeIgniter 应用程序。我将解释如何通过 Git 源代码管理系统管理应用程序的源代码来实现这一点。

## 创建帐户

要开始，你需要做的就是注册一个帐户。这需要几秒钟的时间来完成，并且可以访问应用程序控制台。

![signing for an account on PHPFog](img/b0a6cdd677a1c0481178253cb3dbd46e.png)

注册您的帐户后，您将看到“创建您的应用程序”屏幕。这分为两部分:或者是一个预定义的应用程序，比如 WordPress，或者是一个框架:

### 创建应用程序

![create an app on PHPFog](img/ff365e986ce0492a1da8fd5be1ad8a7b.png)

### 使用框架安装创建应用程序

![create a framework installation on PHPFog](img/f87224e2ff5a0d3ea47c76c3697eafed.png)

## 创建应用程序

在本文中，我将指导您如何使用 CodeIgniter 框架创建一个框架应用程序。因此，在窗口的下半部分，单击 CodeIgniter 图标:

![CodeIgniter on PHPFog](img/c45071afe2c0cb4d9d7cba3fe81df94c.png)

您的应用程序将立即创建，您将看到如下屏幕:

![app being deployed on PHPFog](img/10a495423d5ae8941f0f17ec7a3c342c.png)

请注意，您可以从这里启动 phpMyAdmin，您需要的数据库连接信息也会显示给您。

应用程序完成部署后，您可以单击应用程序控制台右上角的“查看实时站点”按钮。您应该会在新的浏览器选项卡中看到默认的 CodeIgniter 欢迎页面:

![CodeIgniter up and running on PHPFog](img/0507b71b11b8b80fc145368d9647fbbf.png)

## 创建一个 SSH 密钥

可能您已经在计算机上创建了一个公钥/私钥对。但是如果你还没有，你需要创建一个。PHPFog 有一个[非常有用的指南](http://docs.phpfog.com/index.php/features/article/generating_a_ssh_key)解释如何做到这一点。它涵盖了 Mac 和 WindowsLinux 的过程与 Mac 的过程非常相似。

您需要 ssh 密钥，以便 PHPFog 服务器可以安全地与您的计算机通信。从账户窗口，点击 **SSH 键**。在这里，您可以添加刚刚创建的密钥的公共部分:

![add your public key on PHPFog](img/10a495423d5ae8941f0f17ec7a3c342c.png)

成功加载密钥后，您将看到成功消息:

![add your public key on PHPFog](img/1134a9108ca21396ad385fab8c7aca27.png)

## 将应用程序克隆到您的电脑

现在好戏才真正开始。我将假设您的计算机上已经安装了 Git。如果你没有它，你可以在这里找到它。

您可以使用 Git 来克隆为您创建的存储库 PHPFog。如果您查看应用程序控制台窗口的右上角，您会看到您需要使用的 Git 克隆 url:

![Git url on PHPFog](img/6a96fa461b46dba9d9c8fdc327d5945f.png)

显然，url 将反映您选择的域名/帐户名。

现在，您可以打开一个终端窗口，切换到要存储源代码的目录，然后键入 clone 命令:

```
git clone git@git01.phpfog.com:your-domain.phpfogapp.com your-project-name
```

您将看到 Git 将代码从您的 PHPFog 应用程序下载到您在 clone 命令末尾指定的目录中。现在，您可以在您最喜欢的 IDE/文本编辑器中将代码作为项目打开。接下来，我们将对框架的配置进行一些调整，并将更改推回到 PHPFog 存储库。

## 使用源代码

我们要做的第一件事是添加一个. htaccess 文件，这样我们就可以在我们的 CodeIgniter web 应用程序中使用漂亮的 url。这也可以作为将代码推回 PHPFog 库的快速测试。下面是一个可用于 CodeIgniter 的. htaccess 文件:

```
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteBase /
    RewriteCond %{REQUEST_URI} ^system.*
    RewriteRule ^(.*)$ /index.php?/$1 [L]

    #Checks to see if the user is attempting to access a valid file,
    #such as an image or css document, if this isn't true it sends the
    #request to index.php
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule ^(.*)$ /index.php?/$1 [L]
</IfModule>

<IfModule !mod_rewrite.c>
    # If we don't have mod_rewrite installed, all 404's
    # can be sent to index.php, and everything works as normal.
    # Submitted by: ElliotHaughin

    ErrorDocument 404 /index.php
</IfModule>
```

将它添加到项目根目录下的. htaccess 文件中。现在，回到您的终端窗口，您可以运行:

```
git status
```

回复会显示新添加的文件。做一个:

```
git add .
```

要暂存文件，然后提交更改:

```
git commit -am 'add a .htaccess file for pretty url's'
```

最后，要将该文件发送到服务器，您可以推送:

```
git push origin master
```

## 更新框架配置

在最后一步中，您已经更新了 PHPFog 服务器上的源代码。不过，你还看不到这种变化。此外，尽管您有数据库配置信息，您仍然需要将它添加到框架中，以便它知道如何与 MySQL 数据库对话。

### 更新数据库配置

打开 application/config/database . PHP 文件，添加为您创建的数据库配置选项 PHPFog:

```
$db['default']['hostname'] = 'mysql-shared-02.phpfog.com';
$db['default']['username'] = 'your username';
$db['default']['password'] = 'your password';
$db['default']['database'] = 'your database name';
$db['default']['dbdriver'] = 'mysql';
```

提交您的更改，并再次推送到 PHPFog 服务器。在浏览器中刷新站点，应该会出现 CodeIgniter 默认页面。您可能认为什么都没发生，但是如果刷新没有错误，这意味着 CodeIgniter 现在正在连接到您的数据库。

### 更新站点配置

接下来，我们将添加正确的网站 url，并从我们的 url 中删除 index.php 文件，因为我们使用的是. htaccess 文件。打开 application/config/config.php:

```
$config['base_url'] = 'http://your-name-here.phpfogapp.com/';
$config['index_page'] = '';
```

在此期间，我们将自动加载一些有用的资源。打开 application/config/autoload . PHP，更新库和助手部分:

```
$autoload['libraries'] = array('database');
$autoload['helper'] = array('url');
```

提交并推动您的更改。

## 证明事情是可行的

我们现在准备做一些编码，这些编码将以我们可以看到的方式影响网站。打开 application/views/welcome _ message . PHP，将内容复制到 views 文件夹中名为 message.php 的新文件中。然后打开 application/controllers/welcome . PHP，创建一个新方法:

```
public function page2()
{
    $this->load->view('page2');
}
```

然后，您可以对 application/views/welcome _ message . PHP 做一个小的更改:

```
<div id="body">
    <p><?=anchor(base_url() . 'welcome/page2', 'Go to page 2')?></p>
```

我们在这里使用 CodeIgniters 的 url 助手。您可以在 application/views/page2.php 中做类似的事情:

```
<div id="body">
    <p><?=anchor(base_url() . 'welcome', 'Go to the home page')?></p>
```

提交并推送您的更改，然后在浏览器中试用:

![CodeIgniter with code updates on PHPFog](img/311b301931adbc12a6cf02f46d33b4b2.png)

![CodeIgniter with code updates on PHPFog](img/4477a133c92f1bec2215f686665e9162.png)

在这里，我们可以看到我们简单的代码更新被推送到服务器，我们还可以看到我们漂亮的 URL 正在工作。

## 测试数据库

我通过 PHPFog 提供的 phpMyAdmin 安装创建了一个名为“notes”的简单表格。我添加了以下字段:id、标题、注释。然后我写了一些测试笔记。

回到编辑器中，在应用程序/模型中创建一个名为“notes_model.php”的新文件。然后添加一个检索所有笔记的方法:

```
<?php
class Notes_model extends CI_Model
{
    public function __construct()
    {
        parent:: __construct();
    }

    public function get_notes()
    {
        $data = array();
        $sql = "SELECT * FROM notes";
        $q = $this->db->query($sql);
        if($q->num_rows() > 0)
        {
            foreach($q->result() as $row)
            {
                $data[] = $row;
            }

            return $data;
        }
        else
        {
            return 0;
        }
    }
}
```

现在我们需要将模型连接到控制器中的方法(application/controllers/welcome . PHP):

```
public function page2()
{
    $this->load->model('notes_model');
    $data['notes'] = $this->notes_model->get_notes();
    $this->load->view('page2',$data);
}
```

在这里，您可以看到我们正在加载我们的 notes 模型，调用' get_notes()'方法，并将结果分配给一个可以传递给视图的数组($data)。

在 application/views/page2.php 中添加以下代码:

```
<div id="body">
    <p><?=anchor(base_url() . 'welcome', 'Go to the home page')?></p>
    <?php if($notes > 0):?>
    <?php foreach($notes as $n):?>
        <p>
            <?=$n->title?><br />
            <?=$n->note?>
        </p>
    <?php endforeach;?>
    <?php else:?>
        No notes found.
    <?php endif?>
```

添加、提交和推送您的更改，然后在浏览器中检查结果:

![CodeIgniter with MySQL on PHPFog](img/c2133e8d244efa08c242e73e59ac1190.png)

## 结论

PHPFog 使得设置和部署 PHP web 应用程序到云中变得非常容易。你不必担心服务器的设置，或者数据库，或者任何更新。一切都为你准备好了。在云上编码也可以简化你自己的开发工具链，因为你真正需要的只是一个文本编辑器和 Git。不再有 FTP 之痛！

[通过快门用烟点燃火柴](http://www.shutterstock.com/cat.mhtml?lang=en&search_source=search_form&version=llv1&anyorall=all&safesearch=1&searchterm=ignite&search_group=&orient=&search_cat=&searchtermx=&photographer_name=&people_gender=&people_age=&people_ethnicity=&people_number=&commercial_ok=&color=&show_color_wheel=1#id=62910382&src=b561ddd16b02e5a7ef10b5e3417c4fa2-1-0)

## 分享这篇文章