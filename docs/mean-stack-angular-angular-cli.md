# 平均堆栈:使用 Angular 和 Angular CLI 构建应用程序

> 原文：<https://www.sitepoint.com/mean-stack-angular-angular-cli/>

在本教程中，我们将了解如何在 MEAN 堆栈中管理用户认证。我们将使用最常见的架构，即使用由 Node、Express 和 MongoDB 构建的 REST API 来开发一个有角度的单页应用程序。

当考虑用户身份验证时，我们需要解决以下问题:

1.  让用户注册
2.  保存用户数据，但绝不直接存储密码
3.  让返回的用户登录
4.  在页面访问之间保持登录用户的会话活动
5.  有一些只有登录用户才能看到的页面
6.  根据登录状态更改屏幕输出(例如，“登录”按钮或“我的个人资料”按钮)。

在深入研究代码之前，让我们花几分钟时间从较高层面了解一下身份验证在 MEAN 堆栈中是如何工作的。

*要获得更深入的 JavaScript 知识，请阅读我们的书， [JavaScript:新手到忍者，第二版](https://www.sitepoint.com/premium/books/javascript-novice-to-ninja-2nd-edition?utm_source=blog&utm_medium=articles)。*

## 平均堆栈认证流程

那么身份验证在 MEAN stack 中是什么样子的呢？

仍然保持高水平，这些是流程的组成部分:

*   用户数据存储在 MongoDB 中，密码经过哈希处理
*   CRUD 函数内置于 Express API 中—创建(注册)、读取(登录、获取配置文件)、更新、删除
*   Angular 应用程序调用 API 并处理响应
*   Express API 在注册或登录时生成一个 [JSON Web 令牌](https://www.sitepoint.com/using-json-web-tokens-node-js/) (JWT，发音为“Jot”)，并将其传递给 Angular 应用程序
*   角度应用程序存储 JWT，以维持用户的会话
*   角度应用程序在显示受保护视图时检查 JWT 的有效性
*   当调用受保护的 API 路由时，Angular 应用程序将 JWT 传递回 Express。

在维护浏览器中的会话状态时，jwt 优于 cookies。使用服务器端应用程序时，Cookies 更适合维护状态。

## 示例应用程序

本教程的代码可以在 GitHub 上找到。要运行这个应用程序，你需要安装[node . js](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)，以及 [MongoDB](https://www.sitepoint.com/an-introduction-to-mongodb/) 。(关于如何安装的说明，请参考 [Mongo 的官方文档——Windows、Linux、macOS](https://docs.mongodb.org/manual/administration/install-community/) )。

### Angular 应用程序

为了使本教程中的例子简单，我们将从一个有四个页面的 Angular 应用程序开始:

1.  主页
2.  注册页面
3.  登录页面
4.  个人资料页面

这些页面非常简单，看起来像这样:

![Screenshots of the app](img/9ebfff6bc2245aad6fe91c5464ceb39b.png)

只有经过身份验证的用户才能访问个人资料页面。Angular 应用程序的所有文件都在 Angular CLI 应用程序内名为`/client`的文件夹中。

我们将使用 Angular CLI 来构建和运行本地服务器。如果您不熟悉 Angular CLI，请参考[使用 Angular CLI 构建 Todo 应用程序](https://www.sitepoint.com/angular-2-tutorial/)教程开始学习。

### REST API

我们还将从用 Node、Express 和 MongoDB 构建的 REST API 的框架开始，使用[mongose](http://mongoosejs.com/)来管理模式。这个 API 最初应该有三条路线:

1.  `/api/register` (POST)，处理新用户注册
2.  `/api/login` (POST)，处理返回用户登录
3.  `/api/profile/USERID` (GET)，当给定一个`USERID`时返回配置文件细节

我们现在就开始吧。我们可以使用 [express-generator](https://expressjs.com/en/starter/generator.html) 工具为我们创建大量的锅炉板。如果这对你来说是新的，我们这里有一个关于使用它的[教程](https://www.sitepoint.com/create-new-express-js-apps-with-express-generator/)。

用`npm i -g express-generator`安装。然后，创建一个新的 Express 应用程序，选择 [Pug](https://www.sitepoint.com/a-beginners-guide-to-pug/) 作为视图引擎:

```
express -v pug mean-authentication 
```

当生成器运行后，转到项目目录并安装依赖项:

```
cd mean-authentication
npm i 
```

在撰写本文时，这是一个过时版本的 Pug。让我们来解决这个问题:

```
npm i pug@latest 
```

我们还可以安装 Mongoose:

```
npm i mongoose 
```

接下来，我们需要创建我们的文件夹结构。

*   移除`public`文件夹:`rm -rf public`。
*   创建一个`api`目录:`mkdir api`。
*   在`api`目录:`mkdir -p api/{controllers,models,routes}`中创建一个`controllers`、一个`models`和一个`routes`目录。
*   在`controllers`目录下创建一个`authenication.js`文件和一个`profile.js`文件:`touch api/controllers/{authentication.js,profile.js}`。
*   在`models`目录下创建一个`db.js`文件和一个`users.js`文件:`touch api/models/{db.js,users.js}`。
*   在`routes`目录下创建一个`index.js`文件:`touch api/routes/index.js`。

完成后，事情应该是这样的:

```
.
└── api
    ├── controllers
    │   ├── authentication.js
    │   └── profile.js
    ├── models
    │   ├── db.js
    │   └── users.js
    └── routes
        └── index.js 
```

现在让我们添加 API 功能。用以下代码替换`app.js`中的代码:

```
require('./api/models/db');

const cookieParser = require('cookie-parser');
const createError = require('http-errors');
const express = require('express');
const logger = require('morgan');
const path = require('path');

const routesApi = require('./api/routes/index');

const app = express();

// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'pug');

app.use(logger('dev'));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public')));

app.use('/api', routesApi);

// catch 404 and forward to error handler
app.use((req, res, next) => {
  next(createError(404));
});

// error handler
app.use((err, req, res, next) => {
  // set locals, only providing error in development
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  // render the error page
  res.status(err.status || 500);
  res.render('error');
});

module.exports = app; 
```

将以下内容添加到`api/models/db.js`:

```
require('./users');
const mongoose = require('mongoose');
const dbURI = 'mongodb://localhost:27017/meanAuth';

mongoose.set('useCreateIndex', true);
mongoose.connect(dbURI, {
  useNewUrlParser: true,
  useUnifiedTopology: true
});

mongoose.connection.on('connected', () => {
  console.log(`Mongoose connected to ${dbURI}`);
});
mongoose.connection.on('error', (err) => {
  console.log(`Mongoose connection error: ${err}`);
});
mongoose.connection.on('disconnected', () => {
  console.log('Mongoose disconnected');
}); 
```

将以下内容添加到`api/routes/index.js`:

```
const ctrlAuth = require('../controllers/authentication');
const ctrlProfile = require('../controllers/profile');

const express = require('express');
const router = express.Router();

// profile
router.get('/profile/:userid', ctrlProfile.profileRead);

// authentication
router.post('/register', ctrlAuth.register);
router.post('/login', ctrlAuth.login);

module.exports = router; 
```

将以下内容添加到`api/controllers/profile.js`:

```
module.exports.profileRead = (req, res) => {
  console.log(`Reading profile ID: ${req.params.userid}`);
  res.status(200);
  res.json({
    message : `Profile read: ${req.params.userid}`
  });
}; 
```

将以下内容添加到`api/controllers/authentication.js`:

```
module.exports.register = (req, res) => {
  console.log(`Registering user: ${req.body.email}`);
  res.status(200);
  res.json({
    message : `User registered: ${req.body.email}`
  });
};

module.exports.login = (req, res) => {
  console.log(`Logging in user: ${req.body.email}`);
  res.status(200);
  res.json({
    message : `User logged in: ${req.body.email}`
  });
}; 
```

确保 Mongo 正在运行，最后用`npm run start`启动服务器。如果一切都配置正确，您应该在终端中看到一条消息，说明 Mongoose 已经连接到了`mongodb://localhost:27017/meanAuth`，现在您应该能够向 API 发出请求，并从 API 获得响应。你可以用诸如[邮递员](https://www.postman.com/)这样的工具来测试。

## 用 Mongoose 创建 MongoDB 数据模式

接下来，让我们给`api/models/users.js`添加一个模式。它定义了对电子邮件地址、名称、散列和 salt 的需求。将使用散列和 salt 来代替保存密码。`email`被设置为 unique，因为我们将使用它作为登录凭证。以下是模式:

```
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
  email: {
    type: String,
    unique: true,
    required: true
  },
  name: {
    type: String,
    required: true
  },
  hash: String,
  salt: String
});

mongoose.model('User', userSchema); 
```

### 管理密码而不保存它

保存用户密码是一大禁忌。如果黑客得到了你的数据库，你要确保他们不能用它来登录账户。这就是哈希和盐进来的地方。

salt 是一个字符串，对每个用户都是唯一的。哈希是通过将用户提供的密码和 salt 组合在一起，然后应用单向加密来创建的。由于哈希无法解密，验证用户身份的唯一方法是获取密码，将其与 salt 结合，然后再次加密。如果这个的输出与哈希匹配，那么密码一定是正确的。

为了设置和检查密码，我们可以使用 Mongoose 模式方法。这些基本上是添加到模式中的函数。他们都将利用 [Node.js 加密模块](https://nodejs.org/api/crypto.html)。

在`users.js`模型文件的顶部，需要加密以便我们可以使用它:

```
const crypto = require('crypto'); 
```

不需要安装任何东西，因为加密是节点的一部分。加密本身有几种方法；我们感兴趣的是用 [randomBytes](https://nodejs.org/api/crypto.html#crypto_crypto_randombytes_size_callback) 创建随机 salt，用 [pbkdf2Sync](https://nodejs.org/api/crypto.html#crypto_crypto_pbkdf2sync_password_salt_iterations_keylen_digest) 创建 hash。

#### 设置密码

为了保存对密码的引用，我们可以在接受密码参数的`userSchema`模式上创建一个名为`setPassword`的新方法。然后，该方法将使用`crypto.randomBytes`来设置 salt，使用`crypto.pbkdf2Sync`来设置 hash:

```
userSchema.methods.setPassword = function(password) {
  this.salt = crypto.randomBytes(16).toString('hex');
  this.hash = crypto
    .pbkdf2Sync(password, this.salt, 1000, 64, 'sha512')
    .toString('hex');
}; 
```

我们将在创建用户时使用这种方法。不用将密码保存到一个`password`路径，我们可以将它传递给`setPassword`函数来设置用户文档中的`salt`和`hash`路径。

#### 检查密码

检查密码是一个类似的过程，但是我们已经有了 Mongoose 模型中的 salt。这一次我们只想加密 salt 和密码，看看输出是否与存储的散列匹配。

向`users.js`模型文件添加另一个新方法，名为`validPassword`:

```
userSchema.methods.validPassword = function(password) {
  const hash = crypto
    .pbkdf2Sync(password, this.salt, 1000, 64, 'sha512')
    .toString('hex');
  return this.hash === hash;
}; 
```

### 生成 JSON Web 令牌(JWT)

Mongoose 模型需要做的另一件事是生成一个 JWT，这样 API 就可以将它作为响应发送出去。Mongoose 方法在这里也很理想，因为这意味着我们可以将代码保存在一个地方，并在需要时调用它。当用户注册和登录时，我们需要调用它。

为了创建 JWT，我们将使用一个名为 [jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken) 的包，它需要安装在应用程序中，所以在命令行上运行这个包:

```
npm i jsonwebtoken 
```

然后在`users.js`模型文件中要求:

```
const jwt = require('jsonwebtoken'); 
```

这个模块公开了一个`sign`方法，我们可以用它来创建一个 JWT，只需向它传递我们希望包含在令牌中的数据，以及哈希算法将使用的一个秘密。数据应该作为 JavaScript 对象发送，并在一个`exp`属性中包含一个截止日期。

向`userSchema`添加一个`generateJwt`方法以返回一个 JWT，如下所示:

```
userSchema.methods.generateJwt = function() {
  const expiry = new Date();
  expiry.setDate(expiry.getDate() + 7);

  return jwt.sign(
    {
      _id: this._id,
      email: this.email,
      name: this.name,
      exp: parseInt(expiry.getTime() / 1000)
    },
    'MY_SECRET'
  ); // DO NOT KEEP YOUR SECRET IN THE CODE!
}; 
```

注意:保密很重要:只有原始服务器知道它是什么。最佳实践是将这个秘密设置为一个环境变量，不要放在源代码中，尤其是如果您的代码存储在版本控制中的某个地方。

这就是我们需要对数据库做的一切。

## 设置 Passport 来处理快速身份验证

[Passport](http://www.passportjs.org/) 是一个节点模块，简化了在 Express 中处理认证的过程。它提供了一个通用的网关来处理许多不同的身份验证“策略”，比如用脸书、Twitter 或 Oauth 登录。我们将使用的策略称为“本地”，因为它使用本地存储的用户名和密码。

要使用 Passport，首先安装它和策略，并将它们保存在`package.json`:

```
npm i passport passport-local 
```

### 配置 Passport

在`api`文件夹中，创建一个新文件夹`config`，并在其中创建一个名为`passport.js`的文件。这是我们定义战略的地方:

```
mkdir -p api/config
touch api/config/passport.js 
```

在定义策略之前，这个文件需要 Passport、策略、Mongoose 和`User`模型:

```
const mongoose = require('mongoose');
const passport = require('passport');
const LocalStrategy = require('passport-local').Strategy;
const User = mongoose.model('User'); 
```

对于本地策略，我们基本上只需要在`User`模型上编写一个 Mongoose 查询。这个查询应该找到一个具有指定电子邮件地址的用户，然后调用`validPassword`方法来查看哈希值是否匹配。

只有一个关于护照的问题需要解决。在内部，Passport 的本地策略需要两个数据，称为`username`和`password`。然而，我们使用`email`作为我们的唯一标识符，而不是`username`。这可以在策略定义中使用一个带有`usernameField`属性的选项对象中进行配置。之后，就轮到 Mongoose 查询了。

总而言之，策略定义将如下所示:

```
passport.use(
  new LocalStrategy(
    {
      usernameField: 'email'
    },
    function(username, password, done) {
      User.findOne({ email: username }, function(err, user) {
        if (err) {
          return done(err);
        }
        // Return if user not found in database
        if (!user) {
          return done(null, false, {
            message: 'User not found'
          });
        }
        // Return if password is wrong
        if (!user.validPassword(password)) {
          return done(null, false, {
            message: 'Password is wrong'
          });
        }
        // If credentials are correct, return the user object
        return done(null, user);
      });
    }
  )
); 
```

注意如何在`user`实例上直接调用`validPassword`模式方法。

现在只需要将 Passport 添加到应用程序中。所以在`app.js`中，我们需要 Passport 模块，需要 Passport 配置，并将 Passport 初始化为中间件。所有这些项目在`app.js`中的位置非常重要，因为它们需要符合一定的顺序。

Passport 模块应该被要求放在文件的顶部，带有其他一般的`require`语句:

```
const cookieParser = require('cookie-parser');
const createError = require('http-errors');
const express = require('express');
const logger = require('morgan');
const passport = require('passport');
const path = require('path'); 
```

在需要模型的之后*应该需要配置，因为配置引用了模型。*

```
require('./api/models/db');
require('./api/config/passport'); 
```

最后，应该在添加 API 路由之前将 Passport 初始化为 Express 中间件，因为这些路由是第一次使用 Passport:

```
app.use(passport.initialize());
app.use("/api", routesApi); 
```

我们现在已经建立了模式和护照。接下来，是时候将这些应用到 API 的路径和控制器中了。

## 配置 API 端点

使用 API，我们要做两件事:

1.  让控制器发挥作用
2.  保护`/api/profile`路线，以便只有经过验证的用户才能访问它

### 编写注册和登录 API 控制器

在示例应用程序中，注册和登录控制器位于[/API/controllers/authentic ation . js](https://github.com/sitepoint-editors/mean-authentication/blob/master/api/controllers/authentication.js)中。为了让控制器工作，该文件需要 Passport、Mongoose 和用户模型:

```
const mongoose = require('mongoose');
const passport = require('passport');
const User = mongoose.model('User'); 
```

#### 注册 API 控制器

寄存器控制器需要执行以下操作:

1.  从提交的表单中获取数据，并创建一个新的 Mongoose 模型实例
2.  调用我们之前创建的`setPassword`方法，将 salt 和 hash 添加到实例中
3.  将实例作为记录保存到数据库中
4.  生成 JWT
5.  在 JSON 响应中发送 JWT

在代码中，所有这些看起来都像这样。这将取代我们之前编写的虚拟`register`函数:

```
module.exports.register = (req, res) => {
  const user = new User();

  user.name = req.body.name;
  user.email = req.body.email;

  user.setPassword(req.body.password);

  user.save(() => {
    const token = user.generateJwt();
    res.status(200);
    res.json({
      token: token
    });
  });
}; 
```

这利用了我们在 Mongoose 模式定义中创建的`setPassword`和`generateJwt`方法。看看模式中的代码如何使这个控制器更容易阅读和理解。

不要忘记，在现实中，这段代码会有许多错误陷阱，验证表单输入并捕捉`save`函数中的错误。这里省略它们是为了突出代码的主要功能，但是如果你想重温一下，可以看看 Node.js 和 Express 的“[表单、文件上传和安全性”。](https://www.sitepoint.com/forms-file-uploads-security-node-express/)

#### 登录 API 控制器

登录控制器将几乎所有的控制权交给 Passport，尽管您可以(也应该)预先添加一些验证来检查必需的字段是否已经发送。

为了让 Passport 发挥其魔力并运行配置中定义的策略，我们需要调用如下所示的`authenticate`方法。这个方法将使用三个可能的参数`err`、`user`和`info`调用回调。如果定义了`user`，它可以用来生成一个 JWT 返回给浏览器。这将取代我们之前定义的虚拟`login`方法:

```
module.exports.login = (req, res) => {
  passport.authenticate('local', (err, user, info) => {
    // If Passport throws/catches an error
    if (err) {
      res.status(404).json(err);
      return;
    }

    // If a user is found
    if (user) {
      const token = user.generateJwt();
      res.status(200);
      res.json({
        token: token
      });
    } else {
      // If user is not found
      res.status(401).json(info);
    }
  })(req, res);
}; 
```

### 保护 API 路由

在后端要做的最后一件事是确保只有经过认证的用户才能访问`/api/profile`路线。验证请求的方法是通过再次使用秘密来确保与请求一起发送的 JWT 是真实的。这就是为什么你应该保密，不要把它放在代码中。

#### 配置路由身份验证

首先我们需要安装一个名为 [express-jwt](https://www.npmjs.com/package/express-jwt) 的中间件:

```
npm i express-jwt 
```

然后我们需要它，并在定义路由的文件中配置它。在示例应用程序中，这是 [/api/routes/index.js](https://github.com/sitepoint-editors/mean-authentication/blob/master/api/routes/index.js) 。配置就是告诉它这个秘密，并且可选地告诉它要在保存 JWT 的`req`对象上创建的属性的名称。我们将能够在与路由相关联的控制器中使用该属性。属性的默认名称是`user`，但是这是我们的 mongose`User`模型的一个实例的名称，所以我们将它设置为`payload`以避免混淆:

```
// api/routes/index.js

const jwt = require('express-jwt');

const auth = jwt({
  secret: 'MY_SECRET',
  userProperty: 'payload'
});

... 
```

还是那句话，*不要把秘密藏在代码里！*

#### 应用路由身份验证

要应用这个中间件，只需引用要保护的路由中间的函数，如下所示:

```
router.get('/profile', auth, ctrlProfile.profileRead); 
```

请注意，我们已经将`/profile/:userid`更改为`/profile`，因为 ID 将从 JWT 获得。

如果现在有人试图在没有有效 JWT 的情况下访问该路由，中间件将抛出一个错误。为了确保我们的 API 运行良好，通过将以下内容添加到主`app.js`文件的 error handlers 部分，捕获这个错误并返回 401 响应:

```
// catch 404 and forward to error handler
app.use((req, res, next) => { ... });

// Catch unauthorised errors
app.use((err, req, res) => {
  if (err.name === 'UnauthorizedError') {
    res.status(401);
    res.json({ message: `${err.name}: ${err.message}` });
  }
}); 
```

此时，您可以尝试使用诸如 Postman 之类的工具获取`/api/profile`端点，或者在您的浏览器中获取[，您应该会看到 401 响应。](http://localhost:3000/api/profile)

#### 使用路由身份验证

在这个例子中，我们只希望人们能够查看他们自己的个人资料，所以我们从 JWT 获取用户 ID，并在 Mongoose 查询中使用它。

这条路线的控制器在[/API/controllers/profile . js](https://github.com/sitepoint-editors/mean-authentication/blob/master/api/controllers/profile.js)中。该文件的全部内容如下所示:

```
const mongoose = require('mongoose');
const User = mongoose.model('User');

module.exports.profileRead = (req, res) => {
  // If no user ID exists in the JWT return a 401
  if (!req.payload._id) {
    res.status(401).json({
      message: 'UnauthorizedError: private profile'
    });
  } else {
    // Otherwise continue
    User.findById(req.payload._id).exec(function(err, user) {
      res.status(200).json(user);
    });
  }
}; 
```

当然，这应该用更多的错误捕获来充实——例如，如果没有找到用户——但是这段代码保持简短以演示该方法的要点。

后端到此为止。数据库已配置，我们有用于注册和登录的 API 端点，可以生成并返回 JWT，还有一个受保护的路由。

到前端去！

## 初始化角度应用程序

在本节中，我们将使用 Angluar CLI，因此在您继续之前，请确保它已全局安装:

```
npm install -g @angular/cli 
```

然后，在项目的根目录中，运行:

```
ng new client

? Would you like to add Angular routing? Yes
? Which stylesheet format would you like to use? CSS
...
✔ Packages installed successfully.
    Successfully initialized git. 
```

这将生成一个新的带有`AppModule`和`AppRoutingModule`的`client`目录。通过回答“是”到“你想添加角度路由吗”，自动创建`AppRoutingModule`并导入到`AppModule`中。

因为我们将使用 Angular 表单和 Angular 的 HTTP 客户端，所以我们需要导入 Angular 的[表单模块](https://angular.io/api/forms/FormsModule)和[HTTP client 模块](https://angular.io/api/common/http/HttpClientModule)。像这样修改`client/src/app/app.module.ts`的内容:

```
import { BrowserModule } from "@angular/platform-browser";
import { NgModule } from "@angular/core";

import { AppRoutingModule } from "./app-routing.module";
import { AppComponent } from "./app.component";
import { FormsModule } from "@angular/forms";
import { HttpClientModule } from "@angular/common/http";

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [BrowserModule, AppRoutingModule, FormsModule, HttpClientModule],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule {} 
```

## 创建角度认证服务

前端的大部分工作可以放入 Angular 服务中，创建方法来管理:

*   将 JWT 保存在本地存储中
*   从本地存储器读取 JWT
*   从本地存储中删除 JWT
*   调用注册和登录 API 端点
*   检查用户当前是否登录
*   从 JWT 获取登录用户的详细信息

我们需要创建一个名为`AuthenticationService`的新服务。使用 CLI，这可以通过运行以下命令来完成:

```
$ cd client
$ ng generate service authentication
CREATE src/app/authentication.service.spec.ts (397 bytes)
CREATE src/app/authentication.service.ts (143 bytes) 
```

在示例应用程序中，这位于文件[/client/src/app/authentic ation . service . ts](https://github.com/sitepoint-editors/mean-authentication/blob/master/client/src/app/authentication.service.ts)中:

```
import { Injectable } from "@angular/core";

@Injectable({
  providedIn: "root"
})
export class AuthenticationService {
  constructor() {}
} 
```

### 本地存储:保存、读取和删除 JWT

为了让用户在两次访问之间保持登录，我们在浏览器中使用`localStorage`来保存 JWT。另一种方法是使用`sessionStorage`，它将只在当前浏览器会话期间保留令牌。

首先，我们想创建一些接口来处理数据类型。这对我们的应用程序进行类型检查很有用。概要文件返回一个格式化为`UserDetails`的对象，登录和注册端点在请求期间期望一个`TokenPayload`并返回一个`TokenResponse`对象:

```
export interface UserDetails {
  _id: string;
  email: string;
  name: string;
  exp: number;
  iat: number;
}

interface TokenResponse {
  token: string;
}

export interface TokenPayload {
  email: string;
  password: string;
  name?: string;
} 
```

该服务使用 Angular 的`HttpClient`服务向我们的服务器应用程序(我们稍后会用到)发出 HTTP 请求，并使用`Router`服务以编程方式导航。我们必须将它们注入我们的服务构造函数:

```
constructor(private http: HttpClient, private router: Router) {} 
```

然后我们定义了四个与 JWT 令牌交互的方法。我们实现了`saveToken`来处理将令牌存储到`localStorage`和`token`属性中，实现了`getToken`方法来从`localStorage`或`token`属性中检索令牌，实现了`logout`函数来移除 JWT 令牌并重定向到主页。

需要注意的是，如果您使用服务器端渲染，这段代码不会运行，因为像`localStorage`和`window.atob`这样的 API 是不可用的。在 Angular 文档中有关于解决服务器端渲染的[解决方案的细节。](https://angular.io/guide/universal)

到目前为止，这给了我们:

```
import { Injectable } from "@angular/core";
import { HttpClient } from "@angular/common/http";
import { Router } from "@angular/router";
import { Observable } from "rxjs";
import { map } from "rxjs/operators";

export interface UserDetails {
  _id: string;
  email: string;
  name: string;
  exp: number;
  iat: number;
}

interface TokenResponse {
  token: string;
}

export interface TokenPayload {
  email: string;
  password: string;
  name?: string;
}

@Injectable({
  providedIn: "root"
})
export class AuthenticationService {
  private token: string;

  constructor(private http: HttpClient, private router: Router) {}

  private saveToken(token: string): void {
    localStorage.setItem("mean-token", token);
    this.token = token;
  }

  private getToken(): string {
    if (!this.token) {
      this.token = localStorage.getItem("mean-token");
    }
    return this.token;
  }

  public logout(): void {
    this.token = "";
    window.localStorage.removeItem("mean-token");
    this.router.navigateByUrl("/");
  }
} 
```

现在让我们添加一个方法来检查这个令牌——以及令牌的有效性——以确定访问者是否登录。

#### 从 JWT 获取数据

当我们为 JWT 设置数据时(在`generateJwt`mongose 方法中),我们在一个`exp`属性中包含了截止日期。但是如果你看一个 JWT，它似乎是一个随机的字符串，就像下面这个例子:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJfaWQiOiI1NWQ0MjNjMTUxMzcxMmNkMzE3YTRkYTciLCJlbWFpbCI6InNpbW9uQGZ1bGxzdGFja3RyYWluaW5nLmNvbSIsIm5hbWUiOiJTaW1vbiBIb2xtZXMiLCJleHAiOjE0NDA1NzA5NDUsImlhdCI6MTQzOTk2NjE0NX0.jS50GlmolxLoKrA_24LDKaW3vNaY94Y9EqYAFvsTiLg 
```

那么你如何解读 JWT 呢？

JWT 实际上是由三个单独的字符串组成的，用一个点(`.`)隔开。这三个部分是:

1.  *Header* :一个编码的 JSON 对象，包含使用的类型和散列算法
2.  *Payload* :一个编码的 JSON 对象，包含数据，即令牌的真正主体
3.  *签名*:报头和有效载荷的加密散列，使用服务器上设置的“秘密”。

这是我们感兴趣的第二部分——有效载荷。注意，这是*编码的*而不是加密的，这意味着我们可以*解码*它。

现代浏览器中有一个名为 [atob](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/atob) 的函数，它可以解码这样的 Base64 字符串。

所以我们需要获取令牌的第二部分，解码并解析为 JSON。然后我们可以检查有效期还没过。

最后，`getUserDetails`函数应该返回一个`UserDetails`类型或`null`的对象，这取决于是否找到有效的令牌。放在一起，看起来是这样的:

```
public getUserDetails(): UserDetails {
  const token = this.getToken();
  let payload;
  if (token) {
    payload = token.split(".")[1];
    payload = window.atob(payload);
    return JSON.parse(payload);
  } else {
    return null;
  }
} 
```

所提供的用户详细信息包括关于用户名、电子邮件和令牌到期时间的信息，我们将使用这些信息来检查用户会话是否有效。

### 检查用户是否登录

向服务添加一个名为`isLoggedIn`的新方法。它使用`getUserDetails`方法从 JWT 令牌获取令牌细节，并检查是否还没有过期:

```
public isLoggedIn(): boolean {
  const user = this.getUserDetails();
  if (user) {
    return user.exp > Date.now() / 1000;
  } else {
    return false;
  }
} 
```

如果令牌存在，当用户以布尔值登录时，该方法将返回。现在，我们可以构造 HTTP 请求来加载数据，使用令牌进行授权。

### 构建 API 调用

为了便于进行 API 调用，将`request`方法添加到`AuthenticationService`中，它能够根据特定的请求类型构造并返回适当的 HTTP 请求观察值。这是一个私有的方法，因为它只被这个服务使用，并且存在只是为了减少代码重复。这将使用有棱角的`HttpClient`服务。如果还没有的话，记得把这个注入到`AuthenticationService`中:

```
private request(
  method: "post" | "get",
  type: "login" | "register" | "profile",
  user?: TokenPayload
): Observable<any> {
  let base$;

  if (method === "post") {
    base$ = this.http.post(`/api/${type}`, user);
  } else {
    base$ = this.http.get(`/api/${type}`, {
      headers: { Authorization: `Bearer ${this.getToken()}` }
    });
  }

  const request = base$.pipe(
    map((data: TokenResponse) => {
      if (data.token) {
        this.saveToken(data.token);
      }
      return data;
    })
  );

  return request;
} 
```

它确实需要来自 RxJS 的`map`操作符，以便在 API login 或 register 调用返回令牌时拦截并将其存储在服务中。现在我们可以实现调用 API 的公共方法。

### 调用注册和登录 API 端点

只需添加三种方法。我们需要 Angular 应用程序和 API 之间的接口，来调用`login`和`register`端点并保存返回的令牌，或者调用`profile`端点来获取用户详细信息:

```
public register(user: TokenPayload): Observable<any> {
  return this.request("post", "register", user);
}

public login(user: TokenPayload): Observable<any> {
  return this.request("post", "login", user);
}

public profile(): Observable<any> {
  return this.request("get", "profile");
} 
```

每个方法返回一个 observable，它将处理我们需要进行的 API 调用之一的 HTTP 请求。完成服务；现在是时候将 Angular 应用程序中的所有内容联系在一起了。

## 对 Angular 应用程序应用身份验证

我们可以通过多种方式使用 Angular 应用程序中的`AuthenticationService`来提供我们想要的体验:

1.  连接注册表和签到表
2.  更新导航以反映用户的状态
3.  仅允许登录用户访问`/profile`路线
4.  调用受保护的`/api/profile` API 路由

首先，我们使用 Angular CLI 生成我们需要的组件:

```
$ ng generate component register
CREATE src/app/register/register.component.css (0 bytes)
CREATE src/app/register/register.component.html (23 bytes)
CREATE src/app/register/register.component.spec.ts (642 bytes)
CREATE src/app/register/register.component.ts (283 bytes)
UPDATE src/app/app.module.ts (458 bytes)

$ ng generate component profile
CREATE src/app/profile/profile.component.css (0 bytes)
CREATE src/app/profile/profile.component.html (22 bytes)
CREATE src/app/profile/profile.component.spec.ts (635 bytes)
CREATE src/app/profile/profile.component.ts (279 bytes)
UPDATE src/app/app.module.ts (540 bytes)

$ ng generate component login
CREATE src/app/login/login.component.css (0 bytes)
CREATE src/app/login/login.component.html (20 bytes)
CREATE src/app/login/login.component.spec.ts (621 bytes)
CREATE src/app/login/login.component.ts (271 bytes)
UPDATE src/app/app.module.ts (614 bytes)

$ ng generate component home
CREATE src/app/home/home.component.css (0 bytes)
CREATE src/app/home/home.component.html (19 bytes)
CREATE src/app/home/home.component.spec.ts (614 bytes)
CREATE src/app/home/home.component.ts (267 bytes)
UPDATE src/app/app.module.ts (684 bytes) 
```

### 连接注册和登录控制器

现在我们的组件已经创建好了，让我们看看注册和登录表单。

#### 注册页面

首先，让我们创建注册表单。它有附加到字段的`NgModel`指令，所有指令都绑定到在`credentials`控制器属性上设置的属性。该表单还有一个处理提交的`(submit)`事件绑定。在示例应用程序中，它位于[/client/src/app/register/register . component . html](https://github.com/sitepoint-editors/mean-authentication/blob/master/client/src/app/register/register.component.html)中，如下所示:

```
<form (submit)="register()">
  <div class="form-group">
    <label for="name">Full name</label>
    <input
      type="text"
      class="form-control"
      name="name"
      placeholder="Enter your name"
      [(ngModel)]="credentials.name"
    />
  </div>
  <div class="form-group">
    <label for="email">Email address</label>
    <input
      type="email"
      class="form-control"
      name="email"
      placeholder="Enter email"
      [(ngModel)]="credentials.email"
    />
  </div>
  <div class="form-group">
    <label for="password">Password</label>
    <input
      type="password"
      class="form-control"
      name="password"
      placeholder="Password"
      [(ngModel)]="credentials.password"
    />
  </div>
  <button type="submit" class="btn btn-default">Register!</button>
</form> 
```

控制器中的第一个任务是确保我们的`AuthenticationService`和`Router`通过构造函数被注入并可用。接下来，在表单提交的`register`处理程序中，调用`auth.register`，传递表单中的凭证。

`register`方法返回一个可观察对象，我们需要订阅它来触发请求。可观察对象将发出成功或失败，如果有人成功注册，我们将设置应用程序将他们重定向到个人资料页面或在控制台中记录错误。

在示例应用程序中，控制器位于[/client/src/app/register/register . component . ts](https://github.com/sitepoint-editors/mean-authentication/blob/master/client/src/app/register/register.component.ts)中，如下所示:

```
import { Component } from "@angular/core";
import { AuthenticationService, TokenPayload } from "../authentication.service";
import { Router } from "@angular/router";

@Component({
  templateUrl: "./register.component.html",
  styleUrls: ["./register.component.css"]
})
export class RegisterComponent {
  credentials: TokenPayload = {
    email: "",
    name: "",
    password: ""
  };

  constructor(private auth: AuthenticationService, private router: Router) {}

  register() {
    this.auth.register(this.credentials).subscribe(
      () => {
        this.router.navigateByUrl("/profile");
      },
      err => {
        console.error(err);
      }
    );
  }
} 
```

#### 登录页面

登录页面在本质上与注册页面非常相似，但是在这个表单中，我们不需要输入姓名，只需要输入电子邮件和密码。在示例应用程序中，它位于[/client/src/app/log in/log in . component . html](https://github.com/sitepoint-editors/mean-authentication/blob/master//client/src/app/login/login.component.html)中，如下所示:

```
<form (submit)="login()">
  <div class="form-group">
    <label for="email">Email address</label>
    <input
      type="email"
      class="form-control"
      name="email"
      placeholder="Enter email"
      [(ngModel)]="credentials.email"
    />
  </div>
  <div class="form-group">
    <label for="password">Password</label>
    <input
      type="password"
      class="form-control"
      name="password"
      placeholder="Password"
      [(ngModel)]="credentials.password"
    />
  </div>
  <button type="submit" class="btn btn-default">Sign in!</button>
</form> 
```

同样，每个输入都有表单提交处理程序和`NgModel`属性。在控制器中，我们想要与注册控制器相同的功能，但是这次要调用`AuthenticationService`的`login`方法。

在示例应用程序中，控制器位于[/client/src/app/log in/log in . component . ts](https://github.com/sitepoint-editors/mean-authentication/blob/master/client/src/app/login/login.component.ts)中，如下所示:

```
import { Component } from "@angular/core";
import { AuthenticationService, TokenPayload } from "../authentication.service";
import { Router } from "@angular/router";

@Component({
  templateUrl: "./login.component.html",
  styleUrls: ["./login.component.css"]
})
export class LoginComponent {
  credentials: TokenPayload = {
    email: "",
    password: ""
  };

  constructor(private auth: AuthenticationService, private router: Router) {}

  login() {
    this.auth.login(this.credentials).subscribe(
      () => {
        this.router.navigateByUrl("/profile");
      },
      err => {
        console.error(err);
      }
    );
  }
} 
```

现在，用户可以注册并登录该应用程序。请再次注意，表单中应该有更多的验证，以确保在提交之前填写所有必填字段。这些例子被保持在最低限度，以突出主要功能。

### 根据用户状态更改内容

在导航中，如果用户没有登录，我们希望显示**登录**链接，如果用户登录了，我们希望显示他们的用户名和到个人资料页面的链接。导航栏位于`App`组件中。

首先，我们来看看`App`组件控制器。我们可以将`AuthenticationService`注入到组件中，并在模板中直接调用它。在示例应用程序中，该文件位于[/client/src/app/app . component . ts](https://github.com/sitepoint-editors/mean-authentication/blob/master/client/src/app/app.component.ts)中，如下所示:

```
import { Component } from "@angular/core";
import { AuthenticationService } from "./authentication.service";

@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"]
})

export class AppComponent {
  constructor(public auth: AuthenticationService) {}
} 
```

现在，在关联的模板中，我们可以使用`auth.isLoggedIn()`来决定是显示登录链接还是个人资料链接。要将用户名添加到配置文件链接中，我们可以访问`auth.getUserDetails()?.name`的 name 属性。请记住，这是从 JWT 获取数据。`?.`操作符是一种特殊的方法，用于访问可能未定义的对象的属性，而不会抛出错误。

在示例应用程序中，文件位于[/client/src/app/app . component . html](https://github.com/sitepoint-editors/mean-authentication/blob/master/client/src/app/app.component.html)中，更新后的部分如下所示:

```
<ul class="nav navbar-nav navbar-right">
  <li *ngIf="!auth.isLoggedIn()"><a routerLink="/login">Sign in</a></li>
  <li *ngIf="auth.isLoggedIn()">
    <a routerLink="/profile">{{ auth.getUserDetails()?.name }}</a>
  </li>
  <li *ngIf="auth.isLoggedIn()"><a (click)="auth.logout()">Logout</a></li>
</ul>

<router-outlet></router-outlet> 
```

### 仅为登录用户保护路由

在这一步中，我们将看到如何通过保护`/profile`路径，使一条路由只对登录用户可用。

Angular 允许您定义一个路由保护，它可以在路由生命周期的几个点运行检查，以确定是否可以加载路由。我们将使用`CanActivate`钩子告诉 Angular 只有在用户登录时才加载概要文件路径。

为此，我们需要创建一个路由保护:

```
$ ng generate guard auth
? Which interfaces would you like to implement? CanActivate
CREATE src/app/auth.guard.spec.ts (331 bytes)
CREATE src/app/auth.guard.ts (456 bytes) 
```

它必须实现`CanActivate`接口和相关的`canActivate`方法。该方法从`AuthenticationService.isLoggedIn`方法返回一个布尔值(主要检查令牌是否找到，并且仍然有效)，如果用户无效，还将他们重定向到主页。

在`auth.guard.ts`中:

```
import { Injectable } from "@angular/core";
import {
  CanActivate,
  ActivatedRouteSnapshot,
  RouterStateSnapshot,
  UrlTree,
  Router
} from "@angular/router";
import { Observable } from "rxjs";
import { AuthenticationService } from "./authentication.service";

@Injectable({
  providedIn: "root"
})
export class AuthGuard implements CanActivate {
  constructor(private auth: AuthenticationService, private router: Router) {}

  canActivate(
    next: ActivatedRouteSnapshot,
    state: RouterStateSnapshot
  ):
    | Observable<boolean | UrlTree>
    | Promise<boolean | UrlTree>
    | boolean
    | UrlTree {
    if (!this.auth.isLoggedIn()) {
      this.router.navigateByUrl("/");
      return false;
    }
    return true;
  }
} 
```

要启用这种保护，我们必须在路由配置中声明它。有一个名为`canActivate`的路由属性，它接受一组在激活路由之前应该调用的服务。路线是在[批准模块](https://github.com/sitepoint-editors/mean-authentication/blob/master/client/src/app/app-routing.module.ts)中定义的，它包含了您在这里看到的路线:

```
const routes: Routes = [
  { path: "", component: HomeComponent },
  { path: "login", component: LoginComponent },
  { path: "register", component: RegisterComponent },
  { path: "profile", component: ProfileComponent, canActivate: [AuthGuard] }
]; 
```

整个文件应该如下所示:

```
import { NgModule } from "@angular/core";
import { RouterModule, Routes } from "@angular/router";
import { HomeComponent } from "./home/home.component";
import { LoginComponent } from "./login/login.component";
import { RegisterComponent } from "./register/register.component";
import { ProfileComponent } from "./profile/profile.component";
import { AuthGuard } from "./auth.guard";

const routes: Routes = [
  { path: "", component: HomeComponent },
  { path: "login", component: LoginComponent },
  { path: "register", component: RegisterComponent },
  { path: "profile", component: ProfileComponent, canActivate: [AuthGuard] }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule {} 
```

有了路由保护，现在如果未经验证用户试图访问配置文件页面，Angular 将取消路由更改并重定向到主页，从而防止未经验证的用户访问主页。

### 调用受保护的 API 路由

已经建立了`/api/profile`路由来检查请求中的 JWT。否则，它将返回 401 未授权错误。

为了将令牌传递给 API，它需要作为请求的头发送，称为`Authorization`。下面的代码片段显示了主要的数据服务函数，以及发送令牌所需的格式。`AuthenticationService`已经处理了这个，但是你可以在[/client/src/app/authentic ation . service . ts](https://github.com/sitepoint-editors/mean-authentication/blob/master/client/src/app/authentication.service.ts)中找到这个:

```
base$ = this.http.get(`/api/${type}`, {
  headers: { Authorization: `Bearer ${this.getToken()}` }
}); 
```

请记住，在发出请求时，后端代码通过使用只有发布服务器知道的秘密来验证令牌是真实的。

为了在配置文件页面中使用它，我们只需要在示例应用程序的[/客户端/src/app/profile/profile . component . ts](https://github.com/sitepoint-editors/mean-authentication/blob/master/client/src/app/profile/profile.component.ts)中更新控制器。这将在 API 返回一些数据时填充一个`details`属性，这些数据应该与`UserDetails`接口匹配:

```
import { Component, OnInit } from "@angular/core";
import { AuthenticationService, UserDetails } from "../authentication.service";

@Component({
  templateUrl: "./profile.component.html",
  styleUrls: ["./profile.component.css"]
})
export class ProfileComponent implements OnInit {
  details: UserDetails;

  constructor(private auth: AuthenticationService) {}

  ngOnInit() {
    this.auth.profile().subscribe(
      user => {
        this.details = user;
      },
      err => {
        console.error(err);
      }
    );
  }
} 
```

然后，当然只是更新视图中绑定的情况([/src/app/profile/profile . component . html](https://github.com/sitepoint-editors/mean-authentication/blob/master/client/src/app/profile/profile.component.html))。同样，`?.`是一个安全操作符，用于绑定第一次渲染时不存在的属性(因为必须先加载数据):

```
<div class="form-horizontal">
  <div class="form-group">
    <label class="col-sm-3 control-label">Full name</label>
    <p class="form-control-static">{{ details?.name }}</p>
  </div>
  <div class="form-group">
    <label class="col-sm-3 control-label">Email</label>
    <p class="form-control-static">{{ details?.email }}</p>
  </div>
</div> 
```

## 运行 Angular 应用

要运行 Angular 应用程序，我们需要将任何对`/api`的请求路由到运行在`http://localhost:3000/`上的 Express 服务器。为此，在`client`目录中创建一个`proxy.conf.json`文件:

```
touch proxy.conf.json 
```

还增加了以下内容:

```
{
  "/api": {
    "target": "http://localhost:3000",
    "secure": false
  }
} 
```

最后，更新`client/package.json`中的`start`脚本:

```
"start": "ng serve --proxy-config proxy.conf.json", 
```

现在，确保 Mongo 正在运行，使用`npm start`从我们的项目根目录中启动 Express 应用程序，并使用相同的命令从`client`目录中启动 Angular 应用程序。

然后，访问 [http://localhost:4200](http://localhost:4200) ，看(差不多)成品。尝试在[http://localhost:4200/register](http://localhost:4200/register)注册一个账户并登录，确保一切正常。

### 一些最后的润色

毫无疑问，你会注意到，最终的应用程序没有任何风格。由于这是一个有点冗长的教程，我没有在这里包括他们。但是如果你看一下 GitHub 上完成的代码，你可以从那里抓取任何东西。要查看的文件有:

*   [/client/src/index.html](https://github.com/sitepoint-editors/mean-authentication/blob/master/client/src/index.html) —我在这里引入引导程序。
*   [/client/src/app/app . component . html](https://github.com/sitepoint-editors/mean-authentication/blob/master/client/src/app/app.component.html)
*   [/client/src/app/home/home . component . html](https://github.com/sitepoint-editors/mean-authentication/blob/master/client/src/app/home/home.component.html)
*   [/client/src/app/log in/log in . component . html](https://github.com/sitepoint-editors/mean-authentication/blob/master/client/src/app/login/login.component.html)
*   [/client/src/app/profile/profile . component . html](https://github.com/sitepoint-editors/mean-authentication/blob/master/client/src/app/profile/profile.component.html)
*   [/client/src/app/register/register . component . html](https://github.com/sitepoint-editors/mean-authentication/blob/master/client/src/app/register/register.component.html)

如果您从这些文件中复制额外的标记，您应该会得到这样的结果:

![Screenshot of the profile page](img/b06ece34d4fd5128cca10c1fa8f00d21.png)

这就是如何在 MEAN 栈中管理认证，从保护 API 路由和管理用户细节到使用 jwt 和保护路由。

## 分享这篇文章