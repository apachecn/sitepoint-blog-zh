# jQuery 个人资料图片抓取插件

> 原文：<https://www.sitepoint.com/jquery-profile-picture-grabber/>

SitePoint 会不时删除存放在不同 HTML 页面上的多年前的演示。我们这样做是为了降低过时代码的风险，因为暴露的漏洞会给我们的用户带来风险。谢谢你的理解。

## 这是什么？

jQuery Profile Picture Grabber 插件可以从您最喜欢的社交网络中抓取您的个人资料图片，这可以在为 web 应用程序设置个人资料图片或在网站上注册时，比上传图片节省大量时间。

1.只需选择您的网络。
![choose profile pic](img/96889dd14a21e53b3748f7d968d1a2ab.png "choose profile pic")

2.输入任何网络详细信息。
![choose profile pic2](img/34021d56a3459d0d45fe000d67804be4.png "choose profile pic2")

3.您的个人资料图片将自动更新。
![choose profile pic3](img/59dd76f3cf765a6cc4fdc07eb0bfaf32.png "choose profile pic3")

你可以自定义链接，这是一个添加图标而不是文本的例子。
![choose profile pic4](img/014bb657c68be7dcdf42716a7c2734a9.png "choose profile pic4")

## 使用

把一些代码放到你的 HTML HEAD 标签中。配置您的选项，然后离开。简单。你可以定制你在链接上看到的文字，甚至为网络添加图标。

### Java Script 语言

```
<script src="jqProfPicGrab.js"></script>
          <script type="javascript">
          $(document).ready(function()
          {
              //initialise plugin with no options
              W.myProfPicGrab = new jqProfPicGrab();
          });
          </script>
```

### 超文本标记语言

```
<div id="profile-container">
            <img class="profile-pic" src="img/profile-default.jpg" />
        </div>
```

### 使用默认值配置特定网络

```
<script src="jqProfPicGrab.js"></script>
          <script type="javascript">
              $(document).ready(function()
              {
                  //initialise plugin with specific networks and defaults
                  W.myProfPicGrab = new jqProfPicGrab({
                      container: '#demo2',
                      showUrl: true,
                      networks:
                      {
                          gravatar:
                          {
                              'show': true,
                              'text': 'Use Gravatar',
                              'default': 'http://www.gravatar.com/avatar/03490f81e70d7e43a5769a0a886e0314'
                          },
                          facebook:
                          {
                              'show': true,
                              'text': 'Use Facebook',
                              'default': 'http://graph.facebook.com/samuelleedeering/picture?type=large'
                          },
                          twitter:
                          {
                              'show': true,
                              'text': 'Use Twitter',
                              'default': 'http://api.twitter.com/1/users/profile_image?screen_name=samdeering&size=original'
                          },
                          googleplus:
                          {
                              'show': true,
                              'text': 'Use Google Plus',
                              'default': 'https://plus.google.com/s2/photos/profile/samdeering'
                          },
                          directurl:
                          {
                              'show': true,
                              'text': 'My Blog Logo',
                              'default': 'http://jquery4u.cimg/logo.png'
                          },
                          'default':
                          {
                              'show': true,
                              'text': 'Use Default',
                              'default': ''
                          }
                      }
                  });
              });
          </script>
```

## 分享这篇文章