# 使用 Heroku 和脸书 SDK 在云中构建您的应用

> 原文：<https://www.sitepoint.com/build-your-app-in-the-cloud-with-heroku-and-the-facebook-sdk/>

当我第一次遇到 Heroku 时，它是一个纯 Ruby 的云服务。我不是 Ruby 开发者，所以很快就忘记了。但后来他们与脸书合作，你可以用脸书 PHP-SDK 在 Heroku 上创建一个脸书应用程序，只需点击几下鼠标。

现在的问题是:有没有可能用 Heroku 创建一个 PHP 应用程序，在脸书内外都可以运行？答案是肯定的，我会告诉你怎么做。

在本文中，您将创建一个简单的链接共享应用程序。它的用户可以共享一个带有标题和可选描述的 URL。每个用户都有一个名字和电子邮件地址(保密)。如果用户在脸书运行应用程序，名字和电子邮件字段会从他或她的个人资料中删除。

## 创建您的 Heroku 帐户

开始使用 Heroku 只需要创建一个免费帐户；只需要 5 分钟和一个邮箱地址。

Heroku 的基本使用是免费的。正如平台文档中所述，“您创建的每个应用程序每月都可以免费使用 750 个 dyno 小时和 5MB 的数据库。”dyno 是响应 HTTP 请求并运行您的代码的单个 web 进程。

如果你想利用 HTTPS，你需要用信用卡验证你的 Heroku 账户。如果你想通过 HTTPS 提供你的脸书应用程序，这是必需的，但是如果你使用基本的 SSL 插件，你将不会被计费。

## 安装行业工具

Heroku 的开发过程是基于 Ruby 和 Git 的——Heroku 命令行实用程序是 Ruby 的瑰宝，每次你用 Git 推送代码，应用程序都会实时更新。因此，您需要:

1.  安装 Ruby(在 OSX 已经安装了 1.8.x 版本，没问题)。
2.  Install Git.
3.  安装 Heroku gem(就像 sudo gem 安装 Heroku 一样简单)。
4.  使用 Heroku 凭证和 SSH 密钥配置您的环境。

做所有这些事情的最好方法是遵循 Heroku 快速入门指南。

此外，对于这个项目，你要安装 Taps gem 来帮助转移数据库。
[source code language = " bash "]Vitos-Laptop:~ vito $ sudo gems 安装抽头[/sourcecode]

## 

## 创建您的应用

要创建应用程序，您将在本地编写代码，初始化 Git 存储库，然后将代码推送到 Heroku。

**在本地创建您的应用**

为您的应用程序创建一个空目录，并设置您的本地 web 服务器。
【source code language = " bash "】
Vitos-Laptop:~ vito＄CD/Users/Shared/web server/Sites
Vitos-Laptop:Sites vito＄mkdir hero links
[/source code]
用一个新的站点配置您的本地 web 服务器，该站点使用类似 http://herolinks.local 的 URL 指向该目录

**初始化你的 Git 库**

在应用程序目录中初始化您的存储库:
[source code language = " bash "]
Vitos-Laptop:Sites vito $ CD hero links
Vitos-Laptop:hero links vito $ git init
初始化空的 Git 存储库。git/
Vitos-Laptop:hero links vito $ cat
Vitos-Laptop:hero links vito $ git add。
Vitos-Laptop:hero links vito $ git commit-m " hero links 应用程序已创建。"
[/源代码]

## 

## 在 Heroku 上创建应用程序

默认情况下，Heroku 为其托管的应用程序使用 Ruby 环境。要创建一个 PHP 应用程序，您需要定制命令的参数，以使用名为 cedar 的堆栈。创建一个应用程序的命令看起来像:
[source code language = " bash "]Vitos-Laptop:hero links vito $ heroku create hero links–stack cedar
创建 herolinks… done，stack is cedar
http://herolinks.herokuapp.com/ | Git @ heroku . com:hero links . Git
Git remote heroku added
[/source code]

使用这个命令，Heroku 创建应用程序和远程存储库，然后返回 URL 和应用程序细节。

您使用以下内容发布您的应用程序:

[source code language = " bash "]Vitos-Laptop:hero links vito $ git push heroku master[/source code]

您必须创建并提交至少一个文件，否则该命令将失败。我创建了一个带有欢迎信息的 index.php 文件。

推送的输出应该类似于:
[source code language = " bash "]
计数对象:3，完成。
书写对象:100% (3/3)，253 字节，完成。
总计 3(增量 0)，重复使用 0(增量 0)

——> Heroku 接收推送
—>PHP app 检测到
—>绑定 Apache v 2 . 2 . 19
—>绑定 PHP v 5 . 3 . 6
—>发现进程类型
Procfile 声明类型—>(无)
PHP 默认类型—>web
—>编译的 slug 大小为 21.5 MB
—>启动…完成

to git @ heroku . com:hero links . git
*【新分支】master->master
[/source code]

## 

## 开始编码

我不想通过从头开始编写每一个细节来转移本文的重点，即赫罗库和脸书。相反，我使用一些预制的材料:

*   bootstrap——Twitter 的 CSS 工具包(用于界面样式)
*   slim——一个轻量级但功能强大的 PHP 框架(用于主控制器)
*   从 CakePHP 框架中提取的几个库(用于数据验证和净化)
*   一个简单的 PDO 包装数据库类(自制)

从下面的目录结构可以看出，应用程序非常简单。

![Directory structure of Heroku application](img/eb7bc7a762ae10099f73d87abe2113ae.png)

安。htaccess 文件将所有流量重定向到 index.php 页面，该页面是主应用程序文件，包含所有业务逻辑。

lib 目录包含 PHP 库，名为 templates 的文件夹包含接口文件。我有三个页面:一个显示所有共享链接列表的主页(home.php)，一个包含提交新链接所需表单的页面(new.php)，以及一个搜索结果页面(search.php)。

## 数据库访问

Heroku 平台为每个 Ruby 应用程序提供了一个共享的 5MB PosgreSQL 数据库。对于 PHP 应用程序，共享数据库不是默认创建的，必须使用命令添加:
[source code language = " bash "]Vitos-Laptop:hero links vito $ heroku addons:add shared-DATABASE[/source code]
每个应用程序都可以使用包含连接字符串的特殊环境变量 DATABASE_URL 来访问数据库。可以通过$ _ ENV[“DATABASE _ URL”]从 PHP 代码中访问它。

如果您购买了诸如 Heroku Postgres 之类的专用数据库插件，您可以使用 psql 控制台应用程序来管理您的数据库。对于共享数据库，您必须手动管理它，db:push 和 db:pull 导入/导出实用程序除外。

我的本地机器上没有安装 PosgreSQL，所以我的代码在本地使用 SQLite，数据目录包含我的数据库。下面是我在代码中如何在 PostgreSQL 和 SQLite 之间切换:
【source code language = " PHP "】<？php
//如果$_ENV["DATABASE_URL"]为空，那么这个应用就没有在 Heroku
上运行 if(empty($ _ ENV[" DATABASE _ URL "]){
$ config[" db "][" driver "]= " SQLite "；
$ config[" db "][" URL "]= " SQLite://"。realpath(" data/my . db ")；
}
else {
//将数据库 URL 转换为 PDO 友好的 DSN
$ URL = parse _ URL($ _ ENV[" DATABASE _ URL "])；
$ config[" db "][" driver "]= $ URL[" scheme "]；
$ config[" db "][" URL "]= sprintf(
" pgsql:user = % s；密码= % s；主机= % s；dbname=%s "，
$url["user"]，$url["pass"]，$url["host"]，
trim($url["path"]，"/)；
}[/sourcecode]
首先，如果$_ENV["DATABASE_URL"]为空，我设置默认的 SQLite 数据库，因为这意味着该应用程序没有在 Heroku 上运行。如果该应用程序运行在 Heroku 上，那么我解析数据库 URL 并将其转换为 PDO 友好的连接字符串。URL 是这样的:
【source code language = " PHP "】postgres://username:password @ host/database[/source code]
转换后是这样的:
【source code language = " PHP "】pgsql:user = username；密码=密码；主机=主机；dbname =数据库[/源代码]

## 

## 主应用程序控制器

index.php 文件是主要的应用程序控制器。在数据库设置之后，有脸书配置，加载其他 PHP 库，然后创建一个新的瘦应用程序对象实例。
【source code language = " PHP "】<？php
//加载核心 Slim 框架……
require _ once“lib/Slim/Slim . PHP”；

//…添加其他附件库
require _ once“lib/db/db . class . PHP”；
require _ once " lib/cake/sanitize . PHP "；
require _ once " lib/cake/validation . PHP "；

//然后脸书 SDK
require _ once“lib/Facebook/Facebook . PHP”；

//创建新的 Slim 应用程序
$ app = new Slim()；[/sourcecode]
在这里，我使用它的默认设置创建了这个瘦应用程序，我也可以传递一个设置的关联数组，但是默认设置已经足够了。

我使用 Slim 将应用程序的 URL 映射到 PHP 函数。该应用程序总共有四个 URLs(根)、/new、/search 和/install。

使用 Slim，您可以将一个 URL 映射到处理不同 HTTP 方法的不同函数，也可以将同一个函数映射到一个或多个 HTTP 方法。我希望 GET 和 POST 方法可以访问我的根 URL，因为当它在脸书的 iframe 元素中运行时，它会从托管环境接收一些 POST 数据，称为签名请求。下面是怎么做的:
【source code language = " PHP "】<？php
$app- > map("/"，function () use ($app，$config) {
//做点什么
})- > via("GET "，" POST ")；
[/源代码]

该代码将根 URL 映射到一个只允许 GET 和 POST HTTP 方法的匿名函数。每当用户使用 GET 或 POST 请求应用程序根 URL 时，都会调用该函数。

## 应用程序安装程序

我通常会构建一个跨环境的安装程序，但是 SQLite 和 PosgresSQL 之间存在一些差异，这超出了本文的范围。相反，我只在应用程序的本地版本中启用安装程序，然后使用 Heroku 的数据库实用程序将数据复制到远程数据库。

与/install URI 相关联的函数首先对全局 config 变量执行检查，以决定在哪个环境中运行。如果您在本地系统上，那么使用标准的 PDO 语句执行表创建查询。
【source code language = " PHP "】<？php
$app- > get("/install "，function()use($ app){
global $ config；
//检查驱动程序并只为 SQLite/local
IF($ config[" Db "][" driver "]= " SQLite "){
IF($ Db = Db::getConnection()){
$ query = " CREATE TABLE IF NOT EXISTS links(
id 整数主键，
url VARCHAR(255)，
title VARCHAR (100)，
description VARCHAR(512)，
username VARCHAR(50)，
useremail
try {
$ stmt = $ d b->prepare($ query)；
$ stmt->execute()；
$app- > flash("info "，"应用安装成功！");
$app- >重定向(“/”)；
}
catch(PDO exception $ e){
$ app->flash now(" error "，"无法安装应用程序:"。
$ e->getMessage())；
}
}
else {
$ app->flashNow(" error "，"无法打开 DB ")；
}
}
else {
$ app->flash now(" info "，" Install 命令只针对 local/SQLite，尝试运行<code>heroku db:push SQLite://data/my . db</code>代替！");
}
$ app->render(" default . PHP "，array(" action " =>" install "))；
})；

[/sourcecode]

$db 变量是一个 PDO 对象，静态方法 Db::getConnection()负责管理连接并在出错时返回 NULL。

flash()方法在当前会话中存储一条消息，给它一个关键字标签“info”。然后，该消息可用于下一个请求(即，下一页)。

我还有一个 flashNow()方法；使用此方法存储的消息在$flash 变量内的当前请求中可用。

使用适当的关键字(即错误、信息、警告等)。)我可以使用 Bootstrap 预先制作的 CSS 样式对消息进行相应的样式化，例如:
【source code language = " PHP "】
<？php
如果(！空($ flash[" error "]){
？>
<div class = " alert-message error ">
<？php echo $flash["error"]？>
< /div >
<？php
}

[/sourcecode]

render()方法有两个参数:

*   要呈现的 PHP(或 HTML)视图文件
*   传递给视图文件的变量的关联数组

在本例中，我传递了一个名为“action”的变量，其中包含值“install”；default.php 文件将有一个本地$action 变量可以使用。默认情况下，视图文件放在 templates 目录中，但是这个路径可以使用 Slim 的配置 API 进行定制(更多信息请参见 Slim 的手册)。一旦您的数据库在本地运行，您就可以使用 Heroku 的工具将模式(和数据，如果找到的话)导入远程数据库。

[source code language = " PHP "]Vitos-Laptop:hero links vito $ heroku db:push SQLite://data/my . db[/source code]

输出应该类似于:
[source code language = " PHP "]Loaded Taps v 0 . 3 . 23
警告:应用程序“herolinks”中的数据将被覆盖，并且不可恢复。

！警告:潜在的破坏性动作
！这个命令会影响 app: herolinks
！要继续，请键入“herolinks”或使用–confirm hero links[/source code]重新运行此命令

键入您的应用名称以确认导入，您的数据库将被导入到 Heroku 中。

## 应用流程

应用程序的其余部分很简单，遵循类似于模型-视图-控制器模式的方法，只是没有模型。

主页使用一个普通的 SQL 查询从数据库中获取最后 10 个链接。结果存储在 links 数组中，并通过 render()方法传递给视图文件(home.php)。
【source code language = " PHP "】
<？php
$app- > map("/"，function()use($ app){
$ page title = "最新链接"；
$ action = " home "；
$ links = array()；
if($ Db = Db::getConnection()){
$ query = " SELECT * FROM links ORDER BY created desc LIMIT 10 "；
try {
foreach($ d b->query($ query)as $ link){
$ links[]= $ link；
}
}
catch(PDO exception $ e){
$ app->flash now(" error "，$ e->getMessage())；
}
}
else {
$ app->flash now(" error "，"无法打开 DB ")；
}
$ app->render(" home . PHP "，array(
" page title " =>$ page title，
"action" = > $action，
" links " =>$ links))；
})- > via("GET "，" POST ")；

[/sourcecode]

【source code language = " PHP "】
<？php
if(！空($链接)){
？>
< h2 >最新链接</H2>
<table class = " linklist 斑马纹" summary= "最新提交的链接">
<tr>
<th>站点< /th >
< th >描述</th><>用户< /th >PHP
foreach($ links as $ link){
？>
<tr>
<TD><a href = "<？php echo $link["url"]？> " rel="external" > <？php echo $link["title"]？></a></TD>
<TD><？php echo $link["description"]？></TD>
<TD><？php echo $link["用户名"]？></TD>
<TD><？php echo date("d F Y H:i "，strtotime($link["created"]))？></TD>
/tr>
<？php
}
？>
</表>
<？php
}
else {
？>
<div class = " alert-message block-message info ">
<p>对不起，链接数据库为空！</p>
<div class = " alert-actions "><a class = " BTN primary " href = "/new ">现在添加链接</a></div>
</div>
<？php
}

[/sourcecode]

添加链接页面使用 Slim 的 request()->isPost()和 request()->post()方法检索表单数据。使用 CakePHP 框架中的 Sanitize 和 Validation 类对数据进行清理和验证。
【source code language = " PHP "】
<？php
$app- > map("/new "，function()use($ app){
$ page title = "添加新链接"；
$ action = " new "；
$ data = array()；
$ errors = array()；
if($ app->request()->isPost()){
$ data = $ app->request()->post()；
$data = Sanitize::clean($data，array(" escape " =>false))；

$ valid = Validation::getInstance()；
如果(！$ valid->email($ data[" user email "]){
$ errors[" user email "]= "无效的电子邮件地址"；
}
如果(！$valid- > notEmpty($data["用户名"]){
$ errors["用户名"] = "请插入您的姓名"；
}
如果(！$ valid->notEmpty($ data[" title "]){
$ errors[" title "]= "请插入标题"；
}
如果(！$ valid->URL($ data[" URL "]){
$ errors[" URL "]= "无效或空的 URL "；
}

if(empty($ errors)){
if($ Db = Db::getConnection()){
$ query = " INSERT INTO links(url，title，description，username，useremail)值(:URL，:title，:description，:username，:user email)"；
try {
$ stmt = $ d b->prepare($ query)；
$stmt- > bindParam(":url "，$ data[" URL "])；
$ stmt->bind param(":title "，$ data[" title "])；
$ stmt->bind param(":description "，$ data[" description "])；
$stmt- > bindParam(":用户名"，$data["用户名"])；
$ stmt->bind param(":user email "，$ data[" user email "])；
$ stmt->execute()；
$app- > flash("info "，"链接添加成功！");
$app- >重定向(“/”)；
}
catch(PDO exception $ e){
$ app->flash now(" error "，"无法保存您的 URL:"。$ e->getMessage())；
}
}
else {
$ app->flash now(" error "，"无法打开 DB ")；
}
}
}

$ app--> render(" new . PHP "，array(
" page title " =>$ page title，
"action" = > $action，
"data" = > $data，
" errors " =>$ errors))；

})->via("GET "，" POST ")；

[/sourcecode]

错误存储在控制器和视图都使用的$errors 数组中。控制器检查错误，以确定是否应该将数据插入数据库和视图，以便在每个表单域附近显示错误消息。

[source code language = " PHP "]
<form action = " " method = " post " accept-charset = " utf-8 ">
<fieldset>
<div class = " clear fix<？php if(！空($errors["url"]))回显“错误”？>">
<label for = " URL ">站点 URL</label>
<div class = " input ">
<input type = " text " size = " 30 " name = " URL " id = " URL " class = " xlarge " value = "<？php echo(！空($data["url"])？$ data[" URL "]:" "；？>>
<？PHP
$ field = " URL "；
如果(！空($ errors[$ field]){
？>
<span class = " help-inline "><？php echo $errors[$field]？【T42</span>
<？php
}
？>
</div>
</div>
<–此处其他字段->
</字段集>
</表单>

[/sourcecode]

PDO 准备好的语句用于将数据插入到数据库中，包装在一个 try-catch 块中。flashNow()方法显示任何 PDO 错误消息(尽管在生产环境中显然应该避免这种情况)。

搜索页面类似于主页，但不同之处在于在执行 SQL 查询之前处理搜索词。
【source code language = " PHP "】
<？PHP
$ app->get("/Search(/:key)"，function($ key = null)use($ app){
$ page title = "链接搜索"；
$ action = " search "；
$ links = array()；
if($ app->request()->isGet()){
if(empty($ key)){
$ key = $ app->request()->get(" key ")；
}
$ key = Sanitize::clean($ key，array(" escape " =>false))；
}

if($ Db = Db::getConnection()){
$ query = " SELECT * FROM links WHERE(title LIKE:key 或 URL LIKE:key)ORDER BY created desc "；
try {
$ stmt = $ d b->prepare($ query)；
$needle = "% "。$key。"%";
$stmt- > bindParam(":key "，$needle，PDO::PARAM _ STR)；
$ stmt->execute()；
while($ link = $ stmt->FETCH(PDO::FETCH _ ASSOC)){
$ links[]= $ link；
}
}
catch(PDO exception $ e){
$ app->flash now(" error "，"无法执行搜索:"。$ e->getMessage())；
}
}
else {
$ app->flash now(" error "，"无法打开 DB ")；
}

$ app--> render(" search . PHP "，array(
" page title " =>$ page title，
"action" = > $action，
"key" = > $key，
" links " =>$ links))；
})；

[/sourcecode]

语法$app->get("/search(/:key)"，function($key = null)意味着/search 操作名称之后的任何字符串都被视为搜索项，并存储在函数的$key 参数中。在任何情况下,$key 变量在使用前都是经过净化的。

## 添加脸书功能

该应用程序在独立模式下运行良好，但是为了在脸书内部使用它并利用用户数据，您需要:

*   从 GitHub 下载脸书 PHP SDK
*   从开发者页面开始，在脸书注册您的应用

我将 SDK 的 src 目录复制到我的 app 的 lib 目录中，并将其重命名为 facebook。

要注册脸书应用程序，您必须提供一些详细信息，如应用程序的 URL(如 http://myapp.heroku.com)和画布的名称(如 myapp)，这是该应用程序在脸书的标识符(如 https://apps . Facebook . com/myapp/)。

注意:可以省略 HTTPS 网址。这样，您的应用程序只能在禁用安全模式选项的情况下使用(请检查您的脸书帐户设置以了解更多信息)。

一旦您的应用注册到脸书，您就可以在 index.php 控制器中提供配置细节:
【source code language = " PHP "】<？PHP
$ config[" Facebook "][" appId "]= " YOUR _ APP _ ID "；
$ config[" Facebook "][" SECRET "]= " YOUR _ APP _ SECRET "；
$ config[" Facebook "][" canvas "]= " your-app-canvas "；【"https://apps.facebook.com/your-app-canvas/"】$ config[" Facebook "][" canvas _ URL "]= T4；[/源代码]

还需要提供另一个配置设置，传递给授权 API 的 home URL:
【source code language = " PHP "】<？PHP
$ config[" app "][" home "]= " https://your-app . heroku . com "；[/源代码]

与脸书的集成发生在两个代码部分。第一个创建一个新的脸书对象作为$app 对象的属性。这种方法的优点是，只要您可以访问应用程序对象，就可以访问这个连接。设置 facebookCanvas 属性，告诉应用程序它正在脸书提供的 iframe 中运行。
【source code language = " PHP "】<？PHP
$ app = new Slim()；
$app- >脸书=新脸书($ config[" Facebook "])；
$ app->Facebook user profile = null；
$ app->Facebook canvas = isrunningside Facebook()；

[/sourcecode]

第二个集成点是函数 isRunningInsideFacebook()，它告诉应用程序它是否在脸书上运行(本质上，它检查通过 HTTP POST 方法发送的 signed_request 变量)。

[sourcecode language="php"] 函数 isrunningsidesfacebook(){
return！空($ _ REQUEST[" signed _ REQUEST "])；
}[/sourcecode]

facebookInit()函数负责所有其他的事情。例如，它检查是否有正确的授权。使用脸书 SDK 搜索 user_id 字段来解析签名的请求。如果它没有这个字段，那么应用程序就没有得到用户的授权，它会将用户重定向到授权页面。
【source code language = " PHP "】
<？php
函数 face book init(){
global $ app；
global $ config；

if ($fbData = $app->脸书->getSignedRequest()) {
//如果用户 ID 不在请求中则重定向到脸书授权
if(empty($ fbData[" User _ ID "]){
$ auth _ URL = "https://www.facebook.com/dialog/oauth？client_id="
。$config["facebook"]["appId"]。"& redirect_uri= "。urlencode($ config[" app "][" home "])
。"&scope = email "；
echo '<script>top . location . href = " '。$auth_url。";</脚本>’；
退出；
}
}

if ($fbAccessToken = $app->脸书-> getaccessstoken()){
//如果代码存在，则它是由 auth 请求发送的，因此重定向回脸书应用程序页面
if($ code = $ App->request()->get(" code ")){
$ App->redirect($ config[' Facebook '][" canvas _ URL "])；
}
$user = $app- >脸书->getUser()；
if ($user) {
try {
//知道您有一个已经过身份验证的登录用户
$ app->face book user profile = $ app->脸书->API("/me ")；
$ app->Facebook user profile[" logout "]= $ app->脸书->getLogoutUrl()；
}
catch(Facebook API exception $ e){
$ app->flash now(" error "，$ e->getMessage())；
$ user = null；
}
}
}
}

[/sourcecode]

如果应用程序被授权查看用户的数据，它会在成功授权后检查脸书的 OAuth 服务传递的代码参数。该服务将用户重定向到 iframe 之外的应用程序的 URL，所以我选择重定向回框架版本。如果由于某种原因授权被拒绝，那么查询字符串中会传递不同的参数，但是在本文中我忽略了它们。

然后，代码使用脸书 API 请求用户的配置文件，该文件保存在＄app-> Facebook user profile 属性中。

但是这些代码到底是什么时候执行的呢？我稍微编辑了主要 URL 的路由代码以适应这一点:
【source code language = " PHP "】
<？php
$app- > map("/"，" facebookInit "，function () use ($app，$config) {
//其他代码此处
})- > via("GET "，" POST ")；

[/sourcecode]

在 Slim 的术语中，这被称为中间件。在处理路由的匿名函数之前调用 facebookInit()函数。通过使用此功能，每个页面都可以访问和使用脸书数据。

## 摘要

在本文中，我向您展示了一个利用 Heroku、脸书 SDK 和其他免费组件构建的简单应用程序的示例。我希望它已经激发了您的好奇心，以及您在 Heroku 的云中构建您的下一个应用程序的愿望。如果你想克隆和探索这个应用程序，你可以在 CloudSpring 的 GitHub 账户上找到它的完整源代码。编码快乐！

## 分享这篇文章