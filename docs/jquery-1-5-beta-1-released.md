# jQuery 1.5 Beta 1 发布

> 原文：<https://www.sitepoint.com/jquery-1-5-beta-1-released/>

jQuery 五岁生日快乐！jQuery 于 2006 年 1 月 14 日发布，今天是它开发的第五个年头！

为了庆祝这个时刻，我们发布了 jQuery 1.5 的第一个测试版！我们计划在 10 天内发布最终候选版本，并在月底发布最终版本。

我们还想宣布 jQuery 核心开发团队的三名新成员:朱利安·奥伯格、科林·斯诺弗和安东·马茨内勒。他们三个都是 1.5 版本的主要贡献者——提供了重要的代码贡献、错误修复和分类。请借此机会欢迎他们登机！

此外，我们想借此机会感谢 jQuery 社区的所有成员，他们帮助我们发布了这个测试版——尤其是 bug triage 团队的所有成员。

我们希望鼓励社区中的每一个人尝试并参与到对 jQuery core 的贡献中来。我们建立了一整页的信息，致力于更多地参与到团队中来。团队在这里，随时准备帮助您帮助我们！

所以废话不多说——jQuery 1.5 Beta 1！

jQuery 1.5 Beta 1

您可以从 jQuery CDN 获得代码:

* https://code . jquery . com/jquery-1.5 B1 . js

您可以通过将该代码放入您现有的应用程序来帮助我们，并让我们知道是否有任何东西不再工作。请提交一个 bug，并确保提到您正在测试 jQuery 1.5 Beta 1。
jQuery 1.5 Beta 1 变更日志

这是一个简明的变更日志——完整的发行说明将随最终的 1.5 版本一起发布。

* Rewrite of the Ajax module by Julian Aubourg. This is the most significant change in this release and brings a number of performance, stability, and feature improvements to $.ajax. More information can be found here #7195
* Subclassing in jQuery now supported #7901
* Removed the possibility of expando collisions when using noConflict() (V8 is fast!). The expando string now uses a random number + jQuery version to differentiate between instances of jQuery instead of millisecond clock time. #6842
* Deduplicated code in $.get and $.post. #7847
* When a native browser event is bubbling up the DOM, make sure that the correct isDefaultPrevented value is reflected by jQuery’s Event object. #7793
* No longer cache non-html strings in buildFragment to avoid possible collision with the names of Object methods like toString. Testing shows this may also provide modest performance improvements. #6779
* Updated cloneCopyEvent so that it does not create superfluous data objects when cloning elements. Exposes a new method, $.hasData, for determining whether or not an object has any data. #7165
* Use a for loop rather than for/in loop when copying events so that code will work with an augmented Array.prototype. #7809, #7817
* Fixed fadeIn not working properly with inline elements. #7397
* Rewrote IE’s clone function to function properly in all known cases. #4386, #5566, #6997
* Fixed IE breaking when dispatching a ‘submit’ event on plain JS objects. #6398
* Fixed a regression in 1.4 that caused cache control to be set incorrectly for script transport. #7578
* Improve performance of get() for negative indices. #5476
* hasClass, removeClass didn’t work in IE if the attribute contained a carriage return (r) character. #7673
* Fix a regresion in 1.4.4 where calling $.fn.data without arguments breaks on non-DOM elements. #7524
* Fixed memory leaks in IE caused by the custom abort function of $.ajax. #6242
* Prevent live events from firing on disabled elements in IE. #6911
* Fixed a regression in 1.4.3 that caused sending a Location object to $.ajax to no longer work. #7531
* Fixed an issue where some traversal methods performed an unnecessary uniqueness check. #7964
* We now support being able to specify callbacks to handle specific status codes#4964
* Fixed an issue where ?? wasn’t supported as a context-insensitive placeholder for the callback name of a JSONP request. #4897
* Data returned from dataFilter was not being passed to ajax complete() callbacks. We now use the jXHR’s promise interface to get the actual response. #4825
* We now ensure that buildFragment clones elements properly in all browsers. #6655 and #3879
* A memory leak caused when binding custom events in IE8 was fixed #7054
* Lines in form data are now delimited by CRLF when the form is submitted (as recommended by the W3C). #6876
* Ajax requests now abort on unload such that the event is only bound if the xhr transport is used. #5280
* We now support =? being detected even if it has been escaped during data serialization. #5812
* If the user uses the jsonpCallback setting we now automatically set the dataType to ‘jsonp’. #5803
* The crossDomain option now forces ajax to consider a request as cross-domain, even when its not. This is useful when servers issue redirects to cross-domain urls. #5955
* $.ajax(this) allowing retries without the recursion errors found in jQuery 1.4.3\. #7461
* Removed a patch for very early versions of Opera 9 that made it impossible to animate values smaller than -10000\. #7193
* ResponseText is now properly propagated for error callbacks. #7868
* Scripts onload handler passes event as first parameter so statusText is now passed as second argument for aborts. #7865
* With respect to xhr, setting contentType to false will now prevent the Content-Type header from being sent. #7465
* When serializing text, we now encode all line breaks as CRLF pairs per the application/x-www-form-urlencoded specification. #6876
* Fixed a bug with IE6 where certain event handlers were causing inter-page memory leaks. #7762
* Tests for cross-domain detection now include checking for protocol, hostname and port. #7465
* Fixed a problem where IDs containing a period would break find() without returning results. #7533
* The regression with next/adjacent selectors no longer working without the ‘prev’ element has been corrected. #7452
* Fixed the 1.4.3 regression which prevented the use of attr() on anything but DOM element nodes where the nodeType was 1 #7452, #7500,
* A bug where including jQuery 1.3.2 resulted in a border on the right-hand side of the screen in IE8 has been fixed. #5575
* We’ve fixed an issue where adding extra methods to Array.prototype and using jQuery.clone(true) to clone an element resulted in invalid event bindings. #6355
* Fixed an issue where the nth-child does not handle whitespace correctly in Internet Explorer. #7558
* We corrected a bug where mouseenter/leave behaved like mouseover/out when using live events #5821
* Fixed a regression in 1.4.3 where the eq() selector was no longer working with previous and adjacent selectors #7906
* Updated the documentation on event.currentTarget to address any confusion regarding jQuery.proxy. #7628
* Fixed an issue where xhr.setRequestHeader(‘Accept’,…) appended the value rather than replacing it. #6230
* An IE issue where ajax methods failed for content types ending in ‘+xml’ (eg. rss+xml) was fixed. #4958
* The updates to ajax now allow any request to be aborted. #3442
* A .slideUp() issue in FireFox 3.6.11 was fixed which previously hid the frameset border and legend but left any content uncovered by another element. #7308
* We now support cross-browser XML parsing. #6693
* Fixed a bug where when using dataType:’json’ in the .ajax() method, the data object was undefined in IE6 and 7\. #6106
* Corrected an issue where JSONP calls were not removing the script tag when the call completed. #7418
* Updated the documentation to reflect the behaviour supported when using delay() with show() if the duration is not specified. #7543

文章来源:[http://blog . jquery . com/2011/01/14/jquery-1-5-beta-1-released/](http://blog.jquery.com/2011/01/14/jquery-1-5-beta-1-released/)

## 分享这篇文章