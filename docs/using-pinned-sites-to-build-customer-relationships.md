# 使用固定网站建立客户关系

> 原文：<https://www.sitepoint.com/using-pinned-sites-to-build-customer-relationships/>

你可能还记得我们最近看了一下用 Internet Explorer 9 和 Windows 7 固定的网站。最近 [Craig Buckler 文章](../ie9-review/)中关于该功能的评论让我陷入思考。Craig 说“有一些好的功能，但我怀疑许多开发者会有时间或倾向使用它们，除非其他浏览器实现类似的功能”。

正如许多扩展功能的开发一样，不管涉及的是什么浏览器，我认为这是正确的。然而，对我来说，使用这些特性的决定与其说是由时间和倾向驱动，不如说是由客户需求驱动。如果你的客户或老板希望用户能够做一些事情，比如把他们的网站钉在任务栏上，问题不是*是否*做这件事，而是*如何*做这件事，并且这样做有助于满足你客户的目标。

微软显然希望 Internet Explorer 9 不仅仅是一个网站的窗口，而是一种在帮助网站所有者实现目标的同时提供出色用户体验的手段。钉住网站提供的是一种在网站所有者和网站用户——如果是商业网站，就是客户——之间建立关系的方式，而没有浏览器的阻碍。

随着 IE9 浏览器份额的增长，就用户参与度而言，这有可能为网站所有者带来数百万的价值。据[BuildMyPinnedSite.com](http://www.buildmypinnedsite.com/)报道，一些大网站已经权衡了这一价值:

*   [Business Insider](http://www.businessinsider.com/)–阅读页面增加 57%，在网站上花费的时间增加 95%
*   [Flixster](http://windowsteamblog.com/ie/b/ie/archive/2011/03/30/flixster-dev-q-amp-a-users-that-pin-read-34-more-stay-57-longer.aspx)–多阅读 34%的页面，多花 57%的时间在网站上
*   [Gilt](http://windowsteamblog.com/ie/b/ie/archive/2011/01/11/gilt-redefining-the-designer-shopping-experience-with-pinned-sites.aspx)–访问次数增加 39%，购物车中的商品增加 160%，完成的销售交易增加 45%
*   [杀手](http://www.thekillersmusic.com/)——多阅读 65%的页面，多花 103%的时间在网站上
*   [hi5](http://windowsteamblog.com/ie/b/ie/archive/2011/01/19/hi5-thinking-outside-the-browser-box-with-social-games.aspx)–响应 200%以上的社交请求(如好友、游戏、通知)
*   [赫芬顿邮报](http://windowsteamblog.com/ie/b/ie/archive/2011/01/07/huffington-post-reinventing-the-big-news-experience-with-ie9-pinned-sites.aspx)——花 49%的时间在网站上

降低浏览器角色的想法很普遍——每个人都在减少浏览器 chrome、工具栏、滚动条所占的屏幕空间，以便提供更多的内容。IE9 在这方面更进了一步，它传递基于浏览器的信息，而用户根本不必使用浏览器，或者至少在他们需要时才使用浏览器。

钉住的网站是这种方法的关键部分，它使任务栏图标不仅仅是一个特定网站或应用程序的快速链接。图标本身可以用来传递信息并赋予用户权力，而无需在浏览器中加载页面。目的是当内容允许时，让用户返回浏览器。简单、智能，而且可能非常强大。

在这篇文章中，我们将看看如何做到这一点。如果你需要刷新一下关于钉住网站的基础知识，回头看看我们之前的文章，或者去 BuildMyPinnedSite.com 的 T2，在几分钟内建立你自己的网站。

现在，让我们开始吧。

### 为什么要使用钉住的网站？

钉住网站是将网站功能扩展到桌面的一种方式。用户可以将你的网站固定在任务栏、开始菜单或桌面上，一旦他们这样做了，你就可以解锁一些你可能无法访问的功能，比如图标、跳转列表、通知和缩略图工具栏。

这些功能允许您自定义网站通过任务栏(或桌面，或开始菜单)图标提供给用户的信息。

让我们直接进入主题，看看通知是如何工作的。

### 通知——让用户知道某些事情已经改变

通知是不言自明的:它们允许你通知用户你的网站的变化。使用通知的两个例子是改变固定站点图标覆盖的外观，以及闪烁固定站点图标以引起用户的注意。让我们来看看改变图标覆盖。

**图标覆盖图**

默认情况下，创建固定站点时，默认图标是网站中的收藏夹图标。添加一个图标覆盖创建一个视觉效果，用户立即注意到。

你必须检查的第一件事是这个站点是否是作为一个固定站点启动的。通过调用 [msIsSiteMode](http://msdn.microsoft.com/en-us/library/ff976310%28v=VS.85%29.aspx) 很容易发现这一点。如果不是，它将返回 undefined。

创建图标覆盖很简单。调用 [msSiteModeSetIconOverlay](http://msdn.microsoft.com/en-us/library/ff976316%28v=VS.85%29.aspx) 将图标覆盖添加到锁定的站点任务栏按钮。 [msSiteModeSetIconOverlay](http://msdn.microsoft.com/en-us/library/ff976316%28v=VS.85%29.aspx) 有两个参数，它们是:

*   `bstrIconUrl`–图标的绝对 URL 的必需字符串值。
*   `bstrDescription`–可选字符串值，提供图标覆盖的可访问描述。

下面的例子展示了我想要关注的功能。当网站作为固定站点启动时，它每四秒钟更新一次图标覆盖，显示交通灯是开还是关。

```
$(function () {
   if (window.external.msIsSiteMode()) {
       	pin.clearOverlay();
             setInterval(pin.activateOverlays, 4000);
   }
});

pin = function () {
var trafficLightFlag = false;

       function isTrafficLightOn() {
           return trafficLightFlag;
       }

       function setTrafficLight() {
           trafficLightFlag = !trafficLightFlag;
       }

       return {
            clearOverlay: function () {
             		window.external.msSiteModeClearIconOverlay();
            },
            activateOverlays: function () {
             		if (!isTrafficLightOn()) {
                    		window.external.msSiteModeSetIconOverlay("traffic-light-on.ico", "Traffic lights on!");
                        	setTrafficLight();
                    } else {
                        	window.external.msSiteModeSetIconOverlay("favicon.ico", "Traffic lights off!");
                        	setTrafficLight();
                    }
            }
      }
} ();
```

下图显示了交通灯打开时的图标覆盖。

![Traffic Lights On](img/48c1c84588fdef64690f5389188cee83.png)

**图一。交通灯亮了**

下图显示了交通灯关闭时的图标覆盖。

![Traffic Lights Off](img/331ec3171a54f993dc0ac21f95b98fd0.png)

**图二。交通灯关闭**

**闪烁的固定站点按钮**

如果图标覆盖没有引起用户的注意，那么闪烁固定的站点按钮会引起用户的注意。启用此功能就像调用 [msSiteModeActivate](http://msdn.microsoft.com/en-us/library/ff975166%28v=VS.85%29.aspx) 一样简单。该函数不需要参数。为了演示如何使用这个特性，我创建了一个包含一个输入按钮的示例页面。如果四秒钟后用户没有点击按钮，固定的站点按钮将会闪烁。

```
$(function () {
    if (window.external.msIsSiteMode()) {
       	pin.startTimer();
    }
});

pin = function () {
      var buttonClicked = false;
      var timeoutId = 0;

      function hasClicked() {
           return buttonClicked;
      }

      function setFlag() {
          buttonClicked = true;
      }

      return {
          startTimer: function () {
             		$("#SayHello").click(function (e) {
                    		e.preventDefault();
                        	setFlag();
                    });
                    timeoutId = setTimeout(pin.hasClickedButton, 4000);
          },
          hasClickedButton: function () {
             		if (!hasClicked()) {
                    		clearTimeout(timeoutId);
                        	window.external.msSiteModeActivate();
                    }
          }
    }
} ();
```

下图显示了处于前景中的锁定站点。

![Pinned Site Not Active](img/1a66745994c210c4fcf7d9ffc884e215.png)

**图 3。固定站点未激活**

这张图片显示了被钉住的站点位于另一个窗口的后面。

![Pinned Site Active](img/ddc7d1793415dd8a53a2d63c4d343a0f.png)

**图 4。固定站点活动**

当你使用这个的时候需要注意的是，固定的站点必须在另一个窗口的后面。如果固定的站点在前台，这个方法什么也不做，你可能会想为什么什么也没发生。

**使用缩略图工具栏按钮**

使用图标覆盖很酷，但是为了让你的网站更有格调，你可以使用缩略图工具栏按钮。简而言之，缩略图工具栏可让您创建交互式控制，以补充网页的行为。当用户点击一个按钮时， [onmsthumbnailclick](http://msdn.microsoft.com/en-us/library/ff975582%28v=VS.85%29.aspx) 被引发，这意味着您可以对用户正在做的事情做出反应。要创建缩略图工具栏，您可以调用[mssitemodeadthumbbarbutton](http://msdn.microsoft.com/en-us/library/ff976313%28v=VS.85%29.aspx)*函数。mssitemodeadthumbbarbutton 有两个参数，它们是:*

 **   `bstrIconUrl`–图标的绝对 URL 的必需字符串值。
*   `bstrTooltip`–按钮名称的必需字符串值，显示为工具提示。

一旦你定义了你的工具栏按钮，你需要调用[mssitemodeshothumbbar](http://msdn.microsoft.com/en-us/library/ff976319%28v=VS.85%29.aspxhttp:/msdn.microsoft.com/en-us/library/ff976319%28v=VS.85%29.aspx)。这将启用锁定站点的缩略图工具栏预览。工具栏上最多可以包含七个按钮。一旦你调用[mssitemodeshowtumbar](http://msdn.microsoft.com/en-us/library/ff976319%28v=VS.85%29.aspxhttp:/msdn.microsoft.com/en-us/library/ff976319%28v=VS.85%29.aspx)，新的按钮就不能添加到工具栏中。以下示例演示了如何启动并运行缩略图工具栏。

```
$(function () {
    if (window.external.msIsSiteMode()) {
       	pin.addThumbBar();
    }
});

pin = function () {
        return {
       	    addThumbBar: function () {
             	    window.external.msSiteModeAddThumbBarButton("apple.ico", "Apples!");
                    window.external.msSiteModeAddThumbBarButton("grape.ico", "Grape!");
                    window.external.msSiteModeAddThumbBarButton("orange.ico", "Oranges!");
                    window.external.msSiteModeShowThumbBar();                    
            }
     }
} ();
```

下图显示了最终结果。

![Pinned Site Not Active](img/5cf3c420c3ff459d0db4d9d07009205e.png)

**图 5。固定站点未激活**

本例中的工具栏按钮是苹果、葡萄和桔子。我想扩展这个例子，添加当用户点击工具栏按钮时做出反应的功能。下面是实现这一点的代码。

```
$(function () {
    if (window.external.msIsSiteMode()) {
       	pin.addThumbBar();
    }
});

pin = function () {
    var fruit = ["Apple", "Grape", "Oranges"];

    return {
       	addThumbBar: function () {
             	    window.external.msSiteModeAddThumbBarButton("apple.ico", "Apples!");
                    window.external.msSiteModeAddThumbBarButton("grape.ico", "Grape!");
                    window.external.msSiteModeAddThumbBarButton("orange.ico", "Oranges!");
                    window.external.msSiteModeShowThumbBar();

                    $(document).bind("msthumbnailclick", function (button) {
                        pin.thumbBarClickEvent(button);
                    });
        },
        thumbBarClickEvent: function (button) {                    
             		alert("You clicked " + fruit[button.originalEvent.buttonID - 1]);
 	}
    }
} ();
```

当一个按钮被点击时，事件被引发，你可以通过按钮的 *buttonID* 找到哪个按钮被点击了。这是一个与按钮位置相对应的数字。

你也可以通过调用[msSiteModeUpdateThumbBarButton](http://msdn.microsoft.com/en-us/library/ff976320%28v=VS.85%29.aspx)来改变按钮的状态。[msSiteModeUpdateThumbBarButton](http://msdn.microsoft.com/en-us/library/ff976320%28v=VS.85%29.aspx)有三个参数，它们是:

*   `uiButtonID`–一个必需的整数值，指定被单击按钮的 ID。
*   `fEnabled`–一个必需的布尔值，它决定按钮是否被禁用。
*   `fVisible`–决定按钮是否可见的必需布尔值。

下面的示例向您展示了如何在单击按钮后禁用它。

```
$(function () {
    if (window.external.msIsSiteMode()) {
       	pin.addThumbBar();
    }
});

pin = function () {            
       return {
       	addThumbBar: function () {
             	    window.external.msSiteModeAddThumbBarButton("apple.ico", "Apples!");
                    window.external.msSiteModeAddThumbBarButton("grape.ico", "Grape!");
                    window.external.msSiteModeAddThumbBarButton("orange.ico", "Oranges!");
                    window.external.msSiteModeShowThumbBar();

                    $(document).bind("msthumbnailclick", function (button) {
                        pin.disableButton(button);
                    });
        },               
        disableButton: function (button) {
                   window.external.msSiteModeUpdateThumbBarButton(button.originalEvent.buttonID, false, true);
        }
    }
} ();
```

这是最终结果。

![Disabling Toolbar Buttons](img/2005a52fa17a5d82df91db2a16ae4247.png)

**图 6。禁用工具栏按钮**

如果你想做的不仅仅是禁用按钮，你还可以通过调用 [msSiteModeAddButtonStyle](http://msdn.microsoft.com/en-us/library/ff976311%28v=VS.85%29.aspx) 来更新按钮上的图标。这为指定的按钮定义了一个替代图标图像和工具提示。 [msSiteModeAddButtonStyle](http://msdn.microsoft.com/en-us/library/ff976311%28v=VS.85%29.aspx) 有三个参数，它们是:

*   `uiButtonID`–一个必需的整数值，指定被单击按钮的 ID。
*   `bstrIconUrl`–图标的绝对 URL 的必需字符串值。
*   `pvarTooltip`–工具提示所需的字符串值。

MSDN 上的参数`pvarTooltip`说这是可选的，但是我的经验是它*是*必需的。 [msSiteModeAddButtonStyle](http://msdn.microsoft.com/en-us/library/ff976311%28v=VS.85%29.aspx) 返回新按钮样式的 ID。该 ID 必须传递给[msSiteModeShowButtonStyle](http://msdn.microsoft.com/en-us/library/ff976317%28v=VS.85%29.aspx)以应用样式。[msSiteModeShowButtonStyle](http://msdn.microsoft.com/en-us/library/ff976317%28v=VS.85%29.aspx)有两个参数，它们是:

*   `uiButtonID`–一个必需的整数值，指定被单击按钮的 ID。
*   `uiStyleID`–从*[msSiteModeAddButtonStyle](http://msdn.microsoft.com/en-us/library/ff976311%28v=VS.85%29.aspx)返回的必需整数值。*

 *以下示例演示了如何使用此功能。在这个例子中，当按钮被按下五次时，图标从一个水果变成一辆汽车。

```
$(function () {
            if (window.external.msIsSiteMode()) {
                pin.addThumbBar();
            }
        });

        pin = function () {
            var style = 0;
            var fruitClicked;
            var fruitConst = ["Apple", "Grape", "Orange"]

            var fruit = {
                "Apple": { TimesClicked: 0 },
                "Grape": { TimesClicked: 0 },
                "Orange": { TimesClicked: 0 }
            }

            function clickedFruit(button) {
                return fruit[fruitConst[button.originalEvent.buttonID - 1]];
            }

            function howManyTimes(button) {
                fruitClicked = clickedFruit(button);
                return fruitClicked.TimesClicked;
            }

            function addToClickCount(button) {
                fruitClicked = clickedFruit(button);
                fruitClicked.TimesClicked = fruitClicked.TimesClicked + 1;
            }

            return {
                addThumbBar: function () {
                    window.external.msSiteModeAddThumbBarButton("apple.ico", "Apples!");
                    window.external.msSiteModeAddThumbBarButton("grape.ico", "Grape!");
                    window.external.msSiteModeAddThumbBarButton("orange.ico", "Oranges!");
                    window.external.msSiteModeShowThumbBar();

                    $(document).bind("msthumbnailclick", function (button) {
                        pin.addToClick(button);
                    });
                },
                addToClick: function (button) {
                    if (howManyTimes(button) < 4) {
                        addToClickCount(button);
                    } else {
                        pin.updateButtonStyle(button);
                    }
                },
                updateButtonStyle: function (button) {
                    style = window.external.msSiteModeAddButtonStyle(button.originalEvent.buttonID, "warning.ico", "desc");
                    window.external.msSiteModeShowButtonStyle(button.originalEvent.buttonID, style);
                }
            }
        } ();
```

下图显示了五次点击后的最终结果。

![Updating Button Styles](img/7f835f4d2bd9585b5d3684c5369ea173.png)

**图 7。更新按钮样式**

这一切会在哪里结束？

即使这篇文章实际上只是触及了您可以对固定站点做些什么的皮毛，但我希望它能让您对如何设置 Internet Explorer 9 提供的扩展功能有所了解，并了解为什么网站所有者可能希望他们的开发人员实现该功能。

我甚至还没有分析你可以用跳转列表做什么——这本身就是一篇全新的文章。

我不认为有太多的疑问，不仅是 IE9，其他浏览器在不久的将来也会进一步发展。让浏览器不再碍事将是建立网站所有者和网站用户之间关系的关键部分。

从这里出发，最好的去处是查看 MSDN 或 [BuildMyPinnedSite](http://www.buildmypinnedsite.com/) 上的官方[文档。](http://msdn.microsoft.com/en-us/library/ms533053%28v=VS.85%29.aspx)

你怎么想呢?你能看到钉住网站的潜力吗？你能承受忽略这种功能吗？或者你认为有更好的方法来建立这些关系？

> ### SitePoint 内容合作伙伴
> 
> 本教程是在微软的支持下完成的。我们与微软合作，由 SitePoint 独立编写，努力共同开发对您最有用、最相关的内容。** 

## **分享这篇文章**