# 用认知控制群体(测试)

> 原文：<https://www.sitepoint.com/ruling-the-swarm-of-tests-with-codeception/>

如果你的测试比虫群中的 zergs 多，会发生什么？真的多得多。你应该找到控制和管理它们的方法。在这篇文章中，我将分享一些你可以用来编写验收测试的提示和最佳实践。我将使用 Codeception 测试框架来说明最佳实践，但是毫无疑问，它们可以移植到您用于测试的任何验收框架中。

## 变成共识

设计和构建一个优秀的 web 应用程序意味着我们的工作流程和架构允许对产品进行不断的改进。为了使我们的产品稳定，并且确信新的提交不会对现有代码产生负面影响，我们使用自动化测试实践。正如我们需要一个合适的应用架构一样，我们也需要为我们的测试平台设计一个合适的架构。

测试平台是我们测试的完整基础设施。当我说测试时，我不仅仅指单元测试。想得更广。该应用程序还需要在带有 Selenium 的浏览器(或替代浏览器)中进行测试。所以我们需要单元测试，Selenium 测试，可能还有 API 测试，所有这些都应该一起执行。不知何故。我将向您介绍 code ception——一个建立在 Symfony2 组件、Mink 和 PHPUnit 之上的现代 PHP 测试框架。

Codeception 包括 20 多个模块，用于测试流行的 PHP 框架、API(REST、SOAP)、数据存储(MySQL、PostgreSQL、MongoDB 等)和带有 Selenium WebDriver 的 web。在官方网站上查看:[codeception.com](http://codeception.com "Codeception - BDD-style PHP testing")

还有什么比在每次提交后在持续集成服务器上运行所有这些测试更好的方法呢？我推荐使用 Codeception 作为测试自动化平台的主要模块；Codeception 可以执行 Selenium 测试、API 测试和 PHPUnit 测试，并为 CI 系统生成完整的 HTML 和 XML 报告。它甚至可以为所有的测试生成一个完整的代码覆盖报告。

用 Codeception 开始编写测试非常容易。您可以教您的 QA 编写这样的测试，因为开始时只需要非常基础的 PHP 知识。我将让您自己去发现 Codeception 的基础知识，因为我在这里想告诉您的是 Codeception 如何帮助您构建一个更好的测试自动化平台。

## 大量的测试

仅仅说“我们正在编写测试”或者“我们实践 TDD/BDD”是不够的。你的目标应该是创建一个可靠的测试自动化平台。不管你是先写测试再写代码，还是先写代码再写测试，或者把测试描述成一个故事(以 BDD 的方式)，或者把测试描述成代码。真正重要的是你如何管理所有这些测试。

可能会有超过 1000 或 10000 或 100000 个测试，它们可能会运行三到四个小时。你需要保持你的测试代码简洁明了。测试应该易于重构(因为它们随着应用程序不断变化)，并且应该易于扩展。基本上，在编写测试代码时，您应该遵循与编写生产代码相同的最佳实践。

### 基本测试

这里有一个在 Codeception 中的验收测试示例。在这个场景中，我从一个网站向一个朋友发送邀请。为了测试这一点，我需要登录，生成一个邀请代码，然后尝试用代码注册一个新用户。以下是在 Codeception 中的场景:

```
<?php
$I = new WebGuy($scenario);
$I->wantTo('generate invitation code and sign up using it');
$I->amOnPage('/login');
$I->fillField('Username','tester1');
$I->fillField('Password','123456');
$I->click('Sign In');
$I->see('Hello, tester1');
$I->click('Invite');
$I->see('Copy this invitation code');
$code = $I->grabTextFrom('#invitation_code');
$I->click('Logout');
$I->amOnPage('/register');
$I->see('You can sign up if you have invitation code!', 'h1');
$I->fillField('Invitation Code', $code);
$I->fillField('Username','tester2');
$I->fillField('Password','654321');
$I->click('#invite_form input[type=submit]');
$I->see('Hello, tester2');
```

即使您不熟悉 Codeception，这个测试也应该非常易读；所有操作都是从用户的角度来描述的，这里没有使用特殊的技术术语。这个测试看起来简单明了，但是如果你有这样的测试，那么你应该考虑重构它。这就是为什么…

### 设计模式:页面对象

您应该尽可能避免在测试中直接使用硬编码的值，因为这将使测试对变化更加稳定。假设在其他测试中使用了类似的登录和注册表单。如果 username 字段被重命名为“Login ”,那么您需要重写使用 login 的测试。我们可以使用页面对象模式和 CSS/XPath 来定位页面上的元素。邀请页面也是如此。

页面对象是一个实体，代表你的站点的一个页面(或者几个典型的页面),可以写成一个具有静态属性和方法的类。

```
<?php
class InvitePage
{
    // URL of a page
    public static $URL = '/register';
    // This properties define a UI map for Login Page
    public static $codeField = "Invitation Code";
    public static $usernameField = "Username";
    public static $passwordField = "Password";
    public static $submitButton = "#invite_form input[type=submit]";
}
```

现在测试的一部分可以用这种方式重写:

```
<?php
$I->amOnPage(InvitePage::$URL);
$I->fillField(InvitePage::$codeField, $code);
$I->fillField(InvitePage::$usernameField,'tester2');
$I->fillField(InvitePage::$passwordField,'654321');
$I->click(InvitePage::$submitButton);
```

在这种情况下，所有访问 Invite 页面的测试都使用相同的变量来定位页面上的元素。如果任何元素改变了它的位置，您可以根据需要更新`InvitePage`类，而不用接触测试本身。

### 设计模式:控制器

我们已经从测试中移除了一些硬编码的变量，但是如果您将双因素身份验证引入到您正在开发的 web 应用程序中，会发生什么呢？这对你的测试意味着什么？这意味着您需要更新所有需要授权用户运行的测试。我要提醒你:可能有成千上万的测试。将一些场景逻辑移到一个类中是很自然的，这样我们就可以在其他测试中重用登录/退出和注册场景。

我问了一些自动化工程师朋友，他们是如何引用这样一个类的。他们使用了术语 Step Object(这在 BDD 中很常见)，但在这里我更愿意称之为控制器，因为它与我们在 web 应用程序中使用的控制器有相似之处。没有严格的规则规定页面对象或步骤对象应该是什么样子；你可以随心所欲地建造它们。下面是一个我会用于共知测试的控制器示例:

```
<?php
class UserController
{
    protected $user;

    public function __construct(WebGuy $I) {
        $this->user = $I;
    }

    public function login($username, $password) {
        $this->user->amOnPage(LoginPage::$URL);
        $this->user->fillField(LoginPage::$usernameField, $username);
        $this->user->fillField(LoginPage::$passwordField, $password);
        $this->user->click(LoginPage::$submitButton);
        $this->user->see("Hello, $username");
    }

    public function logout() {
        $this->user->click("Logout");
    }

    public function registerWithInviteCode($code, $username, $password) {
        $this->user->amOnPage(InvitePage::$URL);
        $this->user->fillField(InvitePage::$codeField, $code);
        $this->user->fillField(InvitePage::$usernameField, $username);
        $this->user->fillField(InvitePage::$passwordField, $password);
        $this->user->click(InvitePage::$submitButton);
    }
}
```

现在代码有了逻辑块，场景看起来更清晰了:

```
<?php
$I = new WebGuy($scenario);
$U = new UserController($I);
$I->wantTo('generate invitation code and sign up using it');
$U->login('tester1', '123456');
$I->click('Invite');
$I->see('Copy this invitation code');
$code = $I->grabTextFrom('#invitation_code');
$U->logout();
$U->registerWithInviteCode($code, 'tester2', '654321');
$I->see('Hello, tester2');
```

通过引入控制器和页面对象，我们已经摆脱了大多数硬编码的值和常见的场景步骤。它可以让用户从其他测试中登录，这非常有用，因为您的应用程序的大部分功能可能只对授权用户可用。你可以写 10 个、100 个甚至 1000 个测试，但是使用页面对象和控制器，它们不会造成混乱，并且易于支持。

### 向指挥官报告

由于重构，测试代码对于非开发人员来说可能失去了一些可读性。幸运的是，Codeception 对此也有帮助。Codeception 将向您显示运行测试时执行的所有场景步骤:

```
$ ./codecept.phar run acceptance --steps
Codeception PHP Testing Framework v1.5.5
Powered by PHPUnit 3.7.14 by Sebastian Bergmann.

Suite acceptance started
Trying to generate invitation code and sign up using it (InviteCept.php)
Scenario:
* I am on page '/login'
* I fill field 'Username','tester1'
* I fill field 'Password','123456'
* I click 'Sign In'
* I see 'Hello, tester1'
* I click 'Invite'
* I see 'Copy this invitation code'
* I grab text from '#invitation_code'
* I click 'Logout'
* I am on page '/register'
* I see 'You can sign up if you have invitation code!', 'h1'
* I fill field 'Invitation Code', 'iuSia89ak'
* I fill field 'Username','tester2'
* I fill field 'Password','654321'
* I click '#invite_form input[type=submit]'
* I see 'Hello, tester2'

Time: 10 seconds, Memory: 26.00Mb
OK (1 test, 4 assertions)
```

在这种情况下，输出是可读的。这个场景甚至可以展示给非技术人员、经理或业务分析师，他们会明白发生了什么。Codeception 允许您生成以类似方式编写的 HTML 报告。它们可以展示给经理或业务分析师，以展示您的应用程序是什么以及如何使用。

## 结论

如今，测试自动化工程师更喜欢 Java 和 Ruby，但是作为一名 PHP 开发人员，您无疑会对测试平台感兴趣，这些平台可以很好地与您的 PHP 代码兼容，并且是用 PHP 本身编写的。Codeception 是一个很好的工具，可以很容易地开始验收测试，它足够灵活，可以构建一个可靠的测试自动化平台。

但是无论你使用什么样的框架，记住仅仅写测试是不够的。为了使测试干净、稳定和可维护，使用你已经知道的“亲干”原则。像页面对象和控制器(步骤对象)这样的设计模式会在这方面帮到你。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章