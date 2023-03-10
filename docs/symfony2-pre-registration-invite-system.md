# Symfony2 预注册和邀请系统

> 原文：<https://www.sitepoint.com/symfony2-pre-registration-invite-system/>

我们在之前的 [SitePoint 文章](https://www.sitepoint.com/blog/)中讨论了 Symfony 2 的开发，我们构建了我自己的个人 Symfony 应用程序的克隆(部分 [1](https://www.sitepoint.com/building-a-web-app-with-symfony-2-bootstrapping/) 、 [2](https://www.sitepoint.com/building-web-app-symfony-2-development/) 和 [3](https://www.sitepoint.com/building-web-app-symfony-2-finalizing/) )。但是 Symfony 2 是一个巨大的框架，我们可以涵盖更多的主题。

![symfony-logo](img/4da4539c9103ac97779e74fa172c9ed0.png)

在这个由两部分组成的系列文章中，我们将讨论 web 应用程序开发的一个非常重要的领域:**认证和授权**。Symfony2 在[的官方文档](http://symfony.com/doc/current/book/security.html)中对这两个主题有非常详细的阐述。鼓励任何认真研究这个问题的人在从本教程中吸取精华后通读该官方文档。

典型的用户管理流程可能有以下任务:

*   应用程序安装时将生成一个内置用户，并授予其等同于`root`的权限。
*   任何新用户都可以通过表单注册，或者只能通过邀请注册(这是本文讨论的方法)。
*   注册后，用户记录被存储到底层数据库/表中。
*   或者，该应用程序会将这个新用户置于“待定”状态，并发送一封确认电子邮件。只有当用户点击带有确认令牌的电子邮件中的链接时，他们才会被“激活”。这种方法在本文中没有使用，因为我们是在邀请用户，而且这个网站是一个“封闭圈”网站。
*   用户登录。该应用程序将验证用户名和密码。
*   可选地，应用程序可以做一些登录后的活动。在这种情况下，我们将在数据库中更新用户的最后登录日期/时间，并重定向他们。
*   用户可以明确地选择注销。

## 底层`user`表

尽管 Symfony 支持内存中的用户认证，但在实际应用中并不推荐这样做。大多数时候，我们会利用其他资源(数据库、 [LDAP](https://www.sitepoint.com/essentials-ldap-php/) 等)来保存用户凭证。我们将在我们的应用程序中使用 SQL 数据库。

首先，让我们创建一个表来存储用户信息:

```
CREATE TABLE `user` ( 
	`id` INT( 255 ) AUTO_INCREMENT NOT NULL, 
	`username` VARCHAR( 255 ) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL, 
	`password` VARCHAR( 255 ) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL, 
	`email` VARCHAR( 255 ) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL, 
	`created` DATETIME NOT NULL, 
	`logged` DATETIME NULL, 
	`roles` VARCHAR( 25 ) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL, 
	`gravatar` VARCHAR( 255 ) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL, 
	`active` TINYINT( 1 ) NOT NULL, 
	`homepage` VARCHAR( 255 ) CHARACTER SET utf8 COLLATE utf8_general_ci NULL,
	 PRIMARY KEY ( `id` )
```

在这些定义的字段中，`username`和`password`用于允许/拒绝登录请求。这就是所谓的认证。

成功登录后，用户将被授予一个由`roles`字段定义的`ROLE`。访问 URIs 时，不同的角色将拥有不同的访问权限。这叫授权。

`username`、`password`和`roles`共同构成了我们安全体系的基石。

## 配置:`security.yml`

Symfony 使用一个`security.yml`文件来保存所有与应用安全相关的设置和配置。

下面是我们的`security.yml`文件(位于`app/config`)的内容:

```
security:
    providers:
        administrators: 
            entity: { class: AppBundle:User, property: username }
    encoders:
        AppBundle\Entity\User: 
            algorithm: bcrypt
            cost: 12    

    firewalls:
        dev:
            pattern: ^/(_(profiler|wdt|error)|css|images|js)/
            security: false

        default:
            anonymous: ~
            http_basic: ~
            form_login:
                login_path: /login
                check_path: /login_check    
            logout:    
                path: /logout
                target: /login

    access_control:
        - { path: ^/login, roles: IS_AUTHENTICATED_ANONYMOUSLY }
        - { path: ^/register, roles: IS_AUTHENTICATED_ANONYMOUSLY }
        - { path: ^/preregister, roles: IS_AUTHENTICATED_ANONYMOUSLY }            
        - { path: ^/create, roles: IS_AUTHENTICATED_ANONYMOUSLY }                
        - { path: ^/invite, roles: [ROLE_ADMIN] }
        - { path: ^/, roles: [ROLE_USER, ROLE_ADMIN] }
```

在`security`下，我们有四个子部分:

### `providers`:负责提供用户凭证的人员

我们将把用户信息存储在一个表中，在 Symfony 中作为一个实体(一个 PHP 类)反映出来，用必要的 getter/setter 来操作属性(表字段)。

在这种情况下，实体是来自`src/AppBundle/Entity/User.php`的`AppBundle:User`，我们用来验证登录请求的属性来自`username`。

Symfony 支持使用其他字段或几个字段中的一个作为用户的标识符。一个常见的场景是用户能够使用`username`或`email`字段登录。如果你想了解更多，请参考 Symfony 的官方文档。

### 密码是如何编码的:`encoders`

以纯文本形式存储密码从来都不是一个好主意。在我们的例子中，我们指定使用有 12 个循环的`bcrypt`方法。这将以合理的成本(机器计算时间——生成密码哈希花费的时间越长，暴力攻击就越难成功)生成一个相当强的哈希。

### 登录和注销功能:`firewalls/default`

我们必须指定如何登录。Symfony 可以使用 HTTP 基本登录(弹出一个输入用户名/密码的对话框)或登录表单。在我们的例子中，我们使用一种形式:

```
form_login:
	login_path: /login
    check_path: /login_check
```

当然，我们让用户可以选择显式注销:

```
logout:    
	path: /logout
    target: /login
```

这里的`target`设置告诉应用程序在用户退出后去哪里(在这种情况下回到`login`页面)。

为了使上述设置正常工作，Symfony 要求在`routing.yml`文件中有三条路线:

```
logout:
    path: /logout

login:
    path: /login
    defaults: { _controller: AppBundle:Security:login}    

login_check:
    path: /login_check
```

特别是，`/login`路径必须与控制器(`AppBundle:Security:login`)中的适当动作相关联。另外两个可以用名称和路径来定义。

### `access_control`

在这一小节中，我们定义了 URI 模式和所需访问级别之间的映射。

一般来说，我们有 3 个不同的级别:

*   这意味着任何人。使用此访问权限访问 URI 时，将不应用任何访问控制。
*   `ROLE_USER`–通过登录验证的普通用户可以访问该 URI。
*   拥有一些特殊权限的超级用户可以访问这个 URI。

名字(`ROLE_USER`、`ROLE_ADMIN`)都是人为的。开发者可以在应用中使用他们自己的名字(例如，使用`USER`和`ADMIN`也可以)。

在定义访问控制时，一个常见的缺陷是使控制过于严格和不可用。

无论应用程序希望访问控制有多严格，应用程序**必须**至少允许匿名访问`login`页面。就像 Symfony 的路线图一样，我们**必须**更早地定义更具体的规则。

我们必须释放对所有与用户注册(`register`、`preregister`、`create`)和登录(`login`)相关的路由的访问，以允许匿名访问。首先定义这些路线。

为了将邀请功能限制到用户(`invite`)，我们声明这项工作只能由管理员完成。

最后，我们为除了“内部人员”(`ROLE_USER`或`ROLE_ADMIN`)之外的所有人“关闭”整个站点(`^/`，除了之前定义的那些 URI 模式)。

## `AppBundle:User`实体

我倾向于通过导入一个现有的数据库结构来生成`AppBundle:User`实体。这个实体本身还不能被身份验证过程使用。我们需要一些进一步的修改:

```
<?php

namespace AppBundle\Entity;

use Symfony\Component\Security\Core\User\UserInterface;

/**
 * User
 */
class User implements UserInterface, \Serializable
{

    /**
     * @var integer
     */
    private $id;

//All the table properties follow...

    public function getId()
    {
        return $this->id;
    }

//All the getters/setters follow...

    /**
     * Get roles
     *
     * @return [string]
     */
    public function getRoles()
    {
        return [$this->roles];
    }

    public function eraseCredentials()
    {
        return;
    }

    public function serialize()
    {
        ...
    }

    public function unserialize($serialized)
    {
        ...
    }

    public function getSalt()
    {
        ...
    }

}
```

`User`类必须实现两个接口:`Symfony\Component\Security\Core\User\UserInterface`和`\Serializable`。这将要求我们定义额外的方法，包括`eraseCredentials`、`serialize`、`unserialize`和`getSalt`。

关于`getRoles`方法定义的注释。`roles`是我们的`user`表中的一个字段，所以 getter 是这样自动生成的:

```
public function getRoles()
    {
        return $this->roles;
    }
```

这是因为我们将`roles`字段定义为一个简单的`VARCHAR(255)`，它将返回我们存储的字符串。然而，Symfony 需要数组格式的角色，因此尽管我们目前只为用户保存了一个基于字符串的角色，但我们通过分解这个字符串来适应多角色系统和角色作为数组的要求。这样，如果我们希望一个用户拥有多个角色，我们可以将它们保存为逗号分隔的列表(`ROLE_ADMIN, ROLE_BLOGGER, ROLE_ACCOUNTANT`)。因此，我们像这样调整方法:

```
public function getRoles()
    {
        return explode(',', $this->roles);
    }
```

是时候进入逻辑了，但是在此之前，让我们创建第一个拥有最终权限的根用户。

很容易为用户填充`user`表中的所有字段，除了一个字段:`password`字段。我们正在使用`bcrypt`散列我们的密码，所以我们不能把一个纯文本密码。为了帮助我们完成这项繁琐的任务，我们使用这个[在线工具](https://www.dailycred.com/blog/12/bcrypt-calculator)。我的示例 root 用户的 SQL INSERT 语句(为密码“test”计算的散列)如下:

```
INSERT INTO `user` 
(`username`, `password`, `email`, `roles`, `active`) 
VALUES ('root', '$2a$12$3INF/uK0.pmZAMFW0In0uOrfpq.yaVIK0xwmWO8Yjxhs4m8CC1Ei2', 'root@somewhere.com', 'ROLE_ADMIN', '1');
```

### 你收到了管理员的邀请

当管理员访问`/invite`页面时，他们可以输入电子邮件地址来邀请某人。该应用程序然后将建立一个电子邮件并发送它。该电子邮件将包含收件人注册的链接和唯一的邀请码，以验证邀请来自可信来源。仅此而已。在后端，收件人的电子邮件，邀请码和截止日期将被存储在数据库表中。

这样做的方法代码如下:

```
public function doinviteAction(Request $req)
    {

        $email  = $req->get('email');
        $userid = $req->get('user');

        $hash = $this->setInvite($userid, $email);
        $this->sendMail($email, $hash);

        $url = $this->generateUrl('invite');
        return $this->redirect($url);
    }

	private function setInvite($userid, $email)
    {
        $em        = $this->getDoctrine()->getManager();
        $user_repo = $em->getRepository('AppBundle:User');
        $user      = $user_repo->find($userid); //The user who initiates the invitation

        $invite = new Invite();
        $invite->setInvited($email);
        $invite->setWhoinvite($user);

        $now = new \DateTime();
        $int = new \DateInterval('P1D');
        $now->add($int);
        $invite->setExpires($now); //Set invitation expirary 

        $random = rand(10000, 99999);

        $invite->setHash($random); //A random number is used but stricter method to create a verification code can be used. 

        $em->persist($invite);
        $em->flush();

        return $random;
    }

    private function sendMail($email, $hash)
    {
        $mailer = $this->get('mailer');

        $message = $mailer->createMessage()
                ->setSubject('Someone invites you to join thisdomain.com')
                ->setFrom('root@thisdomain.com')
                ->setTo($email)
                ->setBody(
                $this->renderView('AppBundle:Default:email.html.twig', ['email' => $email, 'hash' => $hash]), 'text/html'
        );

        $mailer->send($message);
    }
```

我们得到发送邀请的用户(`$userid`)和他们打算邀请的用户(`$email`)。然后，我们创建邀请记录，并生成一个与此邀请相关联的随机数作为邀请代码。发送邀请所需的所有信息(包括邀请代码)都将被保存和持久化。最后，我们返回邀请码。

保存邀请后，我们会发送电子邮件。我使用默认的 Symfony 邮件服务来做这件事。请注意，对于邮件正文，我使用了一个 Twig 模板来呈现丰富的内容。

因为只有管理员才能邀请其他人，所以我们需要根据用户授权来控制页面内容。Symfony 中使用的默认模板引擎 Twig 有一个助手:

```
{% if is_granted('ROLE_ADMIN') %}
	<li><a href="{{path('invite')}}"><i class="large-icon-share"></i>&nbsp;&nbsp;Invite</a></li>
{% endif %}
```

在这个模板中，我们使用`is_granted`助手来限制只为管理员用户显示菜单项。

### 注册开始

当收件人收到管理员的邀请时，他们可以单击链接并验证邀请。

有效邀请必须满足以下条件:

1.  邮件其实是邀请的。
2.  邀请未过期。
3.  确认码被验证并与该电子邮件配对。

以上都是在各自的控制器动作中完成的:

```
public function preregisterAction(Request $req)
    {
        $email = $req->get('email');
        $invitationCode   = $req->get('code');

        try
        {
            $this->verify($email, $invitationCode);
        }
        catch (\Exception $e)
        {
            $this->addFlash('error', $e->getMessage());
            return $this->redirectToRoute('error');
        }

        $registration = new User();
        $form         = $this->createForm(new RegistrationType(), $registration, ['action' => $this->generateUrl('create'), 'method' => 'POST']);

        return $this->render('AppBundle:Default:register2.html.twig', ['form' => $form->createView(), 'email' => $email]);
    }

    private function verify($email, $code)
    {
        $repo    = $this->getDoctrine()->getManager()->getRepository('AppBundle:Invite');
        $invites = $repo->findBy(['invited' => $email]);

        if (count($invites) == 0)
        {
            throw new \Exception("This email is not invited.");
        }
        $invite = $invites[0];

        $exp = $invite->getExpires();
        $now = new \DateTime();
        if ($exp < $now)
        {
            throw new \Exception("Invitation expires.");
        }

        if ($invite->getCode() !== $code)
        {
            throw new \Exception('Wrong invitation code.');
        }
    }
```

`verify`方法进行验证，并在出错时抛出异常。在`preregisterAction`方法中，我们将捕获该异常，并在错误页面中添加一条 flash 消息来提醒用户。但是，如果验证顺利，注册过程将通过显示注册表单进入下一步。

## 结论

这将我们带到 Symfony 2 安全管理的第 1 部分的结尾，它涵盖了应用程序设置(数据库和`security.yml`)和预注册阶段，其中用户通过应用程序验证他们确实被邀请了。

在我们的第 2 部分中，我们将研究接下来的两个步骤:注册和登录。我们还将开发我们的登录后处理程序，在用户成功登录后执行一些特定于应用程序的操作。

敬请期待！

## 分享这篇文章