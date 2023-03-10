# 使用 UserApp.io 在 Symfony2 中进行用户身份验证

> 原文：<https://www.sitepoint.com/user-authentication-symfony2-userapp-io/>

UserApp.io 是一个方便的用户管理工具和 API。它提供了一个 web 界面来处理用户帐户(以及这涉及到的许多特性)，并提供了一个 API 来将它们连接到您自己的 web 应用程序中。该服务的目的是通过在您自己的服务器上不必担心这一点，使管理用户身份验证变得更加容易和安全。

![Tablet Security theme](img/75cce4e0a1cdaf1af7b36b501f075055.png)

它有许多编程语言和框架的 SDK 和各种包装器，而且价格合理。是的，它是有代价的，但是你可以自由地开始使用相当多的东西。我建议查看他们的特色页面以获得更多信息。此外，创建一个帐户并尝试创建用户、向他们的个人资料添加属性等非常容易，所以我建议您如果还没有这样做的话，也去看看。

在本文中，我们将了解如何实现利用 UserApp.io 的 Symfony2 认证机制。我们编写的代码也可以在我创建的这个[小库](https://github.com/upchuk/userapp-symfony)中找到，您可以试用一下。要将它安装在你的 Symfony 应用程序中，只需按照 GitHub 上的说明进行操作。

## 属国

为了与 UserApp.io 服务通信，我们将使用他们的 [PHP 库](https://github.com/userapp-io/userapp-php)。请确保您在 Symfony 应用程序的 composer.json 文件中要求这样做，正如 GitHub 页面上所指示的那样。

## 身份验证类别

为了使用我们的 Symfony 应用程序验证 UserApp.io 用户，我们将创建几个类:

*   用于使用 UserApp.io API 执行身份验证的表单身份验证器类
*   一个自定义的用户类，用于通过从 API 收集的信息来表示我们的用户
*   一个用户提供者类，用于检索用户并将他们转换成我们的用户类的对象
*   用于表示 Symfony 身份验证令牌的令牌类
*   负责从 UserApp.io 服务注销的注销处理程序类。
*   如果 UserApp.io 用户没有设置任何权限，我们可以抛出一个简单的异常类(我们将转换为 Symfony 角色)

一旦我们创建了这些类，我们将把其中一些声明为服务，并在 Symfony 安全系统中使用它们。

## 表单验证器

首先，我们将创建最重要的类，表单认证器(在我们的最佳实践的一个名为`AppBundle`的`Security/`文件夹中)。下面是代码，我会在后面解释:

```
<?php

/**
 * @file AppBundle\Security\UserAppAuthenticator.php
 */

namespace AppBundle\Security;

use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\Security\Core\Authentication\SimpleFormAuthenticatorInterface;
use Symfony\Component\Security\Core\Authentication\Token\TokenInterface;
use Symfony\Component\Security\Core\Exception\AuthenticationException;
use Symfony\Component\Security\Core\User\UserProviderInterface;
use UserApp\API as UserApp;
use UserApp\Exceptions\ServiceException;

class UserAppAuthenticator implements SimpleFormAuthenticatorInterface
{

  /**
   * @var UserApp
   */
  private $userAppClient;

  public function __construct(UserApp $userAppClient) {
    $this->userAppClient = $userAppClient;
  }

  /**
   * {@inheritdoc}
   */
  public function authenticateToken(TokenInterface $token, UserProviderInterface $userProvider, $providerKey)
  {

    try {
      $login = $this->userAppClient->user->login(array(
        "login" => $token->getUsername(),
        "password" => $token->getCredentials(),
        )
      );

      // Load user from provider based on id
      $user = $userProvider->loadUserByLoginInfo($login);
    } catch(ServiceException $exception) {
      if ($exception->getErrorCode() == 'INVALID_ARGUMENT_LOGIN' || $exception->getErrorCode() == 'INVALID_ARGUMENT_PASSWORD') {
        throw new AuthenticationException('Invalid username or password');
      }
      if ($exception->getErrorCode() == 'INVALID_ARGUMENT_APP_ID') {
        throw new AuthenticationException('Invalid app ID');
      }
    }
    return new UserAppToken(
      $user,
      $user->getToken(),
      $providerKey,
      $user->getRoles()
    );
  }

  /**
   * {@inheritdoc}
   */
  public function supportsToken(TokenInterface $token, $providerKey)
  {
    return $token instanceof UserAppToken
    && $token->getProviderKey() === $providerKey;
  }

  /**
   * {@inheritdoc}
   */
  public function createToken(Request $request, $username, $password, $providerKey)
  {
    return new UserAppToken($username, $password, $providerKey);
  }
}
```

如你所见，我们实现了`SimpleFormAuthenticatorInterface`，因此有 3 个方法和一个构造函数。后者将一个依赖项作为实例化的 UserApp.io 客户端(使用服务容器传递，稍后将详细介绍)。

当用户试图登录应用程序并进行身份验证时，Symfony 会使用该类。首先发生的是`createToken()`被调用。这个方法需要返回一个结合了提交的用户名和密码的认证令牌。在我们的例子中，它将是我们马上要定义的`UserAppToken`类的一个实例。

然后调用`supportToken()`方法来检查这个类是否支持由`createToken()`返回的令牌。在这里，我们只是确保我们为令牌类型返回 true。

最后，`authenticateToken()`被调用并试图检查令牌中的凭证是否有效。在这里，我们使用 UserApp.io PHP 库，尝试登录，如果失败，则抛出 Symfony 身份验证异常。如果身份验证成功，负责的用户提供者将用于构建我们的用户对象，然后基于后者创建并返回另一个令牌对象。

我们将在快速创建简单的`UserAppToken`类之后编写我们的用户提供者。

## 令牌类

```
<?php

/**
 * @file AppBundle\Security\UserAppToken.php
 */

namespace AppBundle\Security;

use Symfony\Component\Security\Core\Authentication\Token\UsernamePasswordToken;

class UserAppToken extends UsernamePasswordToken {

}
```

如您所见，这只是为了命名更加准确而对`UsernamePasswordToken`类的扩展(因为我们存储的是令牌而不是密码)。

## 用户提供商

接下来，让我们看看身份验证器是如何与用户提供者一起工作的，所以也是时候创建后者了:

```
<?php

/**
 * @file AppBundle\Security\UserAppProvider.php
 */

namespace AppBundle\Security;

use Symfony\Component\Security\Core\Exception\AuthenticationException;
use Symfony\Component\Security\Core\Exception\UsernameNotFoundException;
use Symfony\Component\Security\Core\User\UserProviderInterface;
use Symfony\Component\Security\Core\User\UserInterface;
use Symfony\Component\Security\Core\Exception\UnsupportedUserException;
use UserApp\API as UserApp;
use UserApp\Exceptions\ServiceException;
use AppBundle\Security\Exception\NoUserRoleException;
use AppBundle\Security\UserAppUser;

class UserAppProvider implements UserProviderInterface
{
  /**
   * @var UserApp
   */
  private $userAppClient;

  public function __construct(UserApp $userAppClient) {
    $this->userAppClient = $userAppClient;
  }

  /**
   * {@inheritdoc}
   */
  public function loadUserByUsername($username)
  {
    // Empty for now
  }

  /**
   * {@inheritdoc}
   */
  public function refreshUser(UserInterface $user)
  {
    if (!$user instanceof UserAppUser) {
      throw new UnsupportedUserException(
        sprintf('Instances of "%s" are not supported.', get_class($user))
      );
    }

    try {
      $api = $this->userAppClient;
      $api->setOption('token', $user->getToken());
      $api->token->heartbeat();
      $user->unlock();
    }
    catch (ServiceException $exception) {
      if ($exception->getErrorCode() == 'INVALID_CREDENTIALS') {
        throw new AuthenticationException('Invalid credentials');
      }
      if ($exception->getErrorCode() == 'AUTHORIZATION_USER_LOCKED') {
        $user->lock();
      }
    }

    return $user;
  }

  /**
   * {@inheritdoc}
   */
  public function supportsClass($class)
  {
    return $class === 'AppBundle\Security\UserAppUser';
  }

  /**
   *
   * Loads a user from UserApp.io based on a successful login response.
   *
   * @param $login
   * @return UserAppUser
   * @throws NoUserRoleException
   */
  public function loadUserByLoginInfo($login) {

    try {
      $api = $this->userAppClient;
      $api->setOption('token', $login->token);
      $users = $api->user->get();
    } catch(ServiceException $exception) {
      if ($exception->getErrorCode() == 'INVALID_ARGUMENT_USER_ID') {
        throw new UsernameNotFoundException(sprintf('User with the id "%s" not found.', $login->user_id));
      }
    }

    if (!empty($users)) {
      return $this->userFromUserApp($users[0], $login->token);
    }
  }

  /**
   * Creates a UserAppUser from a user response from UserApp.io
   *
   * @param $user
   * @param $token
   * @return UserAppUser
   * @throws NoUserRoleException
   */
  private function userFromUserApp($user, $token) {

    $roles = $this->extractRolesFromPermissions($user);

    $options = array(
      'id' => $user->user_id,
      'username' => $user->login,
      'token' => $token,
      'firstName' => $user->first_name,
      'lastName' => $user->last_name,
      'email' => $user->email,
      'roles' => $roles,
      'properties' => $user->properties,
      'features' => $user->features,
      'permissions' => $user->permissions,
      'created' => $user->created_at,
      'locked' => !empty($user->locks),
      'last_logged_in' => $user->last_login_at,
      'last_heartbeat' => time(),
    );

    return new UserAppUser($options);  
 }

  /**
   * Extracts the roles from the permissions list of a user
   *
   * @param $user
   * @return array
   * @throws NoUserRoleException
   */
  private function extractRolesFromPermissions($user) {
    $permissions = get_object_vars($user->permissions);
    if (empty($permissions)) {
      throw new NoUserRoleException('There are no roles set up for your users.');
    }
    $roles = array();
    foreach ($permissions as $role => $permission) {
      if ($permission->value === TRUE) {
        $roles[] = $role;
      }
    }

    if (empty($roles)) {
      throw new NoUserRoleException('This user has no roles enabled.');
    }

    return $roles;
  }
}
```

与 form authenticator 类类似，我们使用依赖注入将 UserApp.io 客户端注入到该类中，并实现了`UserProviderInterface`。后者要求我们有 3 种方法:

*   因为我们不需要它，所以我们暂时把它空着
*   `refreshUser()`–在每个认证请求中被调用
*   `supportsClass()`–决定这个用户提供者是否与我们的(尚未创建的)用户类一起工作。

让我们回头看一下我们的 authenticator 类，看看当使用 UserApp.io 的身份验证成功时会发生什么:我们调用 user provider 类上的自定义`loadUserByLoginInfo()`方法，该方法从 API 获取成功的登录结果对象，并使用其身份验证令牌从 API 请求回登录的用户对象。结果通过`userFromUserApp()`和`extractRolesFromPermissions()`助手方法包装到我们自己的本地`UserAppUser`类中。后者是我自己实现的一种方法，将 UserApp.io 中的`permissions`概念翻译成 Symfony 中的`roles`。如果 UserApp.io 没有为用户设置权限，我们会抛出自己的`NoUserRoleException`。因此，请确保您在 UserApp.io 中的用户拥有您想要映射到 Symfony 中的角色的权限。

异常类是默认 PHP `\Exception`的简单扩展:

```
<?php

/**
 * @file AppBundle\Security\Exception\NoUserRoleException.php
 */

namespace AppBundle\Security\Exception;

class NoUserRoleException extends \Exception {

}
```

再次回到我们的验证器，我们看到如果使用 UserApp.io 的验证成功，那么用户提供者将构建一个包含用户所有必要信息的`UserAppUser`类对象。有了这个对象，我们需要将它添加到`UserAppToken`类的新实例中并返回它。

基本上，这种情况从用户尝试登录时就开始了:

1.  我们用提交的凭证(`createToken()`)创建一个令牌
2.  我们尝试对这个令牌中的凭证进行身份验证，如果失败，就会抛出一个身份验证异常
3.  如果身份验证成功，我们将创建一个包含用户对象和一些其他信息的新令牌
4.  我们返回这个令牌，Symfony 将使用它在会话中存储用户。

用户提供者上的`refreshUser()`方法也很重要。该方法负责在每次经过身份验证的页面刷新时检索当前登录用户的新实例。因此，每当经过身份验证的用户访问防火墙内的任何页面时，这个方法都会被触发。要点是用存储中可能同时发生的任何变化来水合用户对象。

显然，我们需要将 API 调用保持在最低限度，但这是一个通过发送心跳请求来增加 UserApp.io 认证时间的好机会。默认情况下(但可配置)，每个经过身份验证的用户令牌的有效期为 60 分钟，但通过发送心跳请求，有效期会延长 20 分钟。

这也是执行另外两个功能的好地方:

1.  如果令牌同时在 UserApp.io 中过期，我们会得到一个值为`INVALID_CREDENTIALS`的异常，因此通过抛出一个 Symfony `AuthenticationException`,我们也会在 Symfony 中注销用户。
2.  尽管心跳请求被做得尽可能便宜(这意味着不检索真实的用户数据)，但是用户`locked`状态确实以异常的形式被传输回来。所以我们可以借此机会将我们的用户对象也标记为锁定。然后，可以在应用程序中使用`locked`状态，例如，如果用户被锁定，可以对照它进行检查并拒绝访问各个部分。

如果你愿意的话，你可以发出一个 API 请求，用 UserApp.io 中的数据更新用户对象，但是我发现这对于大多数用例来说没有多大意义。当用户注销并在下一次返回时，数据可以被更新。但是根据需要，这在这里可以很容易地完成。尽管要记住对 UserApp.io 的许多 API 调用的性能影响和成本。

这基本上是我们的认证逻辑的关键。

## 用户类别

让我们也创建我们之前讨论过的`UserAppUser`类:

```
<?php

/**
 * @file AppBundle\Security\UserAppUser.php
 */

namespace AppBundle\Security;

use Symfony\Component\OptionsResolver\OptionsResolver;
use Symfony\Component\Security\Core\User\UserInterface;

class UserAppUser implements UserInterface {

  private $id;
  private $username;
  private $token;
  private $firstName;
  private $lastName;
  private $email;
  private $roles;
  private $properties;
  private $features;
  private $permissions;
  private $created;
  private $locked;

  public function __construct($options)
  {
    $resolver = new OptionsResolver();
    $this->configureOptions($resolver);

    $params = $resolver->resolve($options);
    foreach ($params as $property => $value) {
      $this->{$property} = $value;
    }
  }

   /**
   * Configures the class options
   *
   * @param $resolver OptionsResolver
   */
  private function configureOptions($resolver)
  {
    $resolver->setDefaults(array(
      'id' => NULL,
      'username' => NULL,
      'token' => NULL,
      'firstName' => NULL,
      'lastName' => NULL,
      'email' => NULL,
      'roles' => array(),
      'properties' => array(),
      'features' => array(),
      'permissions' => array(),
      'created' => NULL,
      'locked' => NULL,
      'last_logged_in' => NULL,
      'last_heartbeat' => NULL,
    ));

    $resolver->setRequired(array('id', 'username'));
  }

  /**
   * {@inheritdoc}
   */
  public function getRoles()
  {
    return $this->roles;
  }

  /**
   * {@inheritdoc}
   */
  public function getToken()
  {
    return $this->token;
  }

  /**
   * {@inheritdoc}
   */
  public function getSalt()
  {
  }

  /**
   * {@inheritdoc}
   */
  public function getUsername()
  {
    return $this->username;
  }

  /**
   * {@inheritdoc}
   */
  public function eraseCredentials()
  {
  }

  /**
   * {@inheritdoc}
   */
  public function getPassword() {

  }

  /**
   * @return mixed
   */
  public function getId() {
    return $this->id;
  }

  /**
   * @return array
   */
  public function getProperties() {
    return $this->properties;
  }

  /**
   * @return mixed
   */
  public function isLocked() {
    return $this->locked;
  }

  /**
   * Locks the user
   */
  public function lock() {
    $this->locked = true;
  }

  /**
   * Unlocks the user
   */
  public function unlock() {
    $this->locked = false;
  }

  /**
   * @return mixed
   */
  public function getFirstName() {
    return $this->firstName;
  }

  /**
   * @return mixed
   */
  public function getLastName() {
    return $this->lastName;
  }

  /**
   * @return mixed
   */
  public function getEmail() {
    return $this->email;
  }

  /**
   * @return mixed
   */
  public function getFeatures() {
    return $this->features;
  }

  /**
   * @return mixed
   */
  public function getCreated() {
    return $this->created;
  }

  /**
   * @return mixed
   */
  public function getPermissions() {
    return $this->permissions;
  }
}
```

这里没有什么特别的，我们只是从 UserApp.io 映射一些数据，并实现接口所需的一些方法。此外，我们添加了`locked/unlocked`标记。

## 注销

我们需要创建的最后一个类是当用户退出 Symfony 时，处理用户从 UserApp.io 退出的类。

```
<?php

/**
 * @file AppBundle\Security\UserAppLogout.php
 */

namespace AppBundle\Security;

use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Security\Core\Authentication\Token\TokenInterface;
use Symfony\Component\Security\Http\Logout\LogoutHandlerInterface;
use UserApp\API as UserApp;
use UserApp\Exceptions\ServiceException;

class UserAppLogout implements LogoutHandlerInterface {

  /**
   * @var UserApp
   */
  private $userAppClient;

  public function __construct(UserApp $userAppClient) {
    $this->userAppClient = $userAppClient;
  }

  /**
   * {@inheritdoc}
   */
  public function logout(Request $request, Response $response, TokenInterface $token) {
    $api = $this->userAppClient;
    $user = $token->getUser();
    $api->setOption('token', $user->getToken());
    try {
      $api->user->logout();
    }
    catch (ServiceException $exception) {
      // Empty for now, error probably caused by user not being authenticated which means
      // user is logged out already.
    }
  }
}
```

这里我们再次注入了 UserApp.io PHP 客户端，因为我们实现了`LogoutHandlerInterface`，所以我们需要有一个`logout()`方法。我们在其中所做的就是让用户从 UserApp.io 中注销，如果他们仍然登录的话。

## 把所有东西都装好

现在我们有了自己的类，是时候将它们声明为服务并在我们的认证系统中使用它们了。以下是我们基于 YML 的服务声明:

```
user_app_client:
       class: UserApp\API
       arguments: ["%userapp_id%"]
user_app_authenticator:
       class: AppBundle\Security\UserAppAuthenticator
       arguments: ["@user_app_client"]
user_app_provider:
       class: AppBundle\Security\UserAppProvider
       arguments: ["@user_app_client"]
user_app_logout:
       class: AppBundle\Security\UserAppLogout
       arguments: ["@user_app_client"]
```

第一个是 UserApp.io PHP 库，我们将应用程序 ID 以参数引用的形式传递给它。您需要一个名为`userapp_id`的参数，带有您的 UserApp.io 应用程序 ID。

另外三个是我们之前写的表单验证器、用户提供者和注销类。正如您所记得的，每一个都在其构造函数中接受一个参数，其形式是定义为第一个服务的 UserApp.io 客户端。

接下来，是时候在我们的安全系统中使用这些服务了，所以编辑`security.yml`文件并执行以下操作:

1.  在`providers`键下，添加以下内容:

    ```
    user_app:
    		id: user_app_provider
    ```

    在这里，我们指定我们的应用程序也有这个用户提供者，所以它可以使用它。

2.  在`firewall`键下，添加以下内容:

```
secured_area:
	    pattern:    ^/secured/
	    simple_form:
	        authenticator: user_app_authenticator
	        check_path: security_check
	        login_path: login
	    logout:
	        path:   logout
	        handlers: [user_app_logout]
	        target: _home
	    anonymous: ~
```

这里发生的事情是，我们定义了一个简单的安全区域，它使用我们的认证器进行`simple_form`类型的认证。在`logout`键下，我们正在添加一个被调用的处理程序(我们的`UserAppLogout`类被定义为服务)。剩下的就是[常规的 Symfony 安全设置](http://symfony.com/doc/current/book/security.html)，所以确保你有一个登录表单显示在`login`路线上，等等。查看关于这方面的文档以获得更多信息。

仅此而已。通过将`simple_form`认证与我们的定制表单认证器和用户提供者(以及可选的注销处理程序)一起使用，我们实现了我们自己的基于 UserApp.io 的 Symfony 认证机制。

## 结论

在本文中，我们看到了如何使用 UserApp.io 服务和 API 作为用户提供者来实现自定义 Symfony 表单身份验证。我们已经经历了相当多的代码，这意味着对代码本身的一个非常简短的解释。相反，我试图通过构建一个自定义解决方案来解释 Symfony 的身份验证过程，该解决方案考虑了我们与 UserApp.io 交互的方式。

如果您跟随并在您的包中实现了这个方法，并希望像这样使用它，请继续。你也可以选择使用我创建的[库](https://github.com/upchuk/userapp-symfony)，它有一个在 GitHub 页面上描述的非常快速简单的设置。我推荐后者，因为我计划开发和维护它，这样如果任何错误被删除或者引入了新的特性，你总是可以得到一个更新的版本(希望不是相反)。

如果你想为它做贡献，我们非常欢迎你。如果您发现任何问题或认为有更好的方法来完成类似的目标，我也很高兴让我知道。

## 分享这篇文章