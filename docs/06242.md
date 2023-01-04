# 与专家讨论角度-文字记录

> 原文：<https://www.sitepoint.com/talk-angularjs-experts-transcript/>

今天早上，我们与 SitePoint group 的开发人员 Mal Curtis 和 Brad Barrow 讨论了强大的 AngularJS 框架。这一小时出现了很多资源，我把它们都整理成了一个列表，提供给那些不想仔细阅读文字记录的人。

**Angular js in Action**
[Angular-UI](http://angular-ui.github.io/ "AngularUI")
[UI 路由器演示](http://angular-ui.github.io/ui-router/sample "UI Router demo")
[Web 方向南方](http://webdirections.org/wds13/  "Web Directions South")
[Mgonto Restangular](https://github.com/mgonto/restangular "Restangular")

**学习资源**
[使用指令和数据绑定构建 app](https://www.sitepoint.com/angularjs-tutorial-build-an-app-using-directives-and-data-binding/ "Angular tutorial")
[Angular Yeoman 生成器](https://github.com/yeoman/generator-angular "Yeoman generator")

**杂项资源**
[Angular 宣布镖](http://news.dartlang.org/2013/11/angular-announces-angulardart.html "Dart")
[Angular 镖](https://github.com/angular/angular.dart "Angular Dart")
[Mozilla 的砖块](http://mozilla.github.io/brick/ "Brick")
[存储模块](https://github.com/gsklee/ngStorage "Storage Module")
[Web 方向视频](http://www.youtube.com/user/webdirections/videos "Web Directions")
[Angular 的 Google+page](https://plus.google.com/+AngularJS "Angular's Google+")
[Angular 的 YouTube 频道](http://www.youtube.com/user/angularjs "Angular's YouTube Channel")
[Google Groups](https://groups.google.com/forum/#!forum/angular "Google Groups")

如果您因为不知道而错过了会议，请确保在此注册接收未来会议的通知[。下周我们将和](https://learnable.com/twte "Talk with the Experts")[电子邮件奇才](http://emailwizardry.nightjar.com.au/ "Email Wizardry")的妮可·梅林一起讨论 **HTML 电子邮件**。太平洋标准时间 11 月 13 日星期三下午 12:30 开始，您可以在这里参加会议[。](https://learnable.com/twte "Talk with the Experts")

对于那些错过了演讲并想知道发生了什么的人，你可以在这里阅读全文:

**[22:05] <BradBarrow> AngularJS is one of many Javascript frameworks available at the moment. What sets it apart from the rest is it’s close integration with the DOM/HTML of your site**
***[22:06] <MalCurtis> I’d AngularJS has one of the highest learning curves of the frameworks out there, mainly because it does things a very different way.***
***[22:06] <MalCurtis> But the payoff is that once you’re up and running, it’s extremely powerful***
**[22:07] <BradBarrow> Exactly right MalCurtis, whilst other frameworks aim to provide a structure for your javascript, Angular seeks to use the power of javascript to make HTML more dynamic which is both groundbreaking and incredibly powerful**
**[22:07] <BradBarrow> Consider a button that, when pressed takes a value from a text field and uses it to display an alert message**
[22:07] <HAWK> Is anyone using Angular currently?
**[22:08] <BradBarrow> With Javascript/Jquery you’d need to write code to find the button, find the text field and find the message container…then you’d need code to listen to all of the events, to extract the values and insert them**
[22:08] <Randyman5775> Mostly Jquery for me
**[22:08] <BradBarrow> All of that code sits in a file quite far away from the markup it’s interacting with**
[22:08] <Good> Just learning it and thinking how it could change the industry of web development
***[22:08] <MalCurtis> For most sites, where you’re just adding in bits of dynamic content, Angular is not required***
[22:09] <HAWK> If anyone has a question, now would be a good time to jump in.
***[22:09] <MalCurtis> Angular is a great framework for SPA, Single Page Applications – so full client side apps***
[22:09] <nanda> Why is it restricted to SPA only
[22:09] <Randyman5775> Is there any server side set-up?
***[22:09] <MalCurtis> I’m not saying it’s restricted, I’m just saying it might be overkill for when you just need to add small bits of interactivity***
***[22:10] <MalCurtis> Randyman5775 no and yes***
***[22:10] <MalCurtis> No, in that it’s a full client side framework***
**[22:10] <BradBarrow> The main reason you’d want to use angular on SPA apps compared to full stack apps is that Angular wants to own routing**
***[22:10] <MalCurtis> Yes, in that if you want data – you’ve got to get it from somewhere***
***[22:10] <MalCurtis> but you can also look at using a ‘backed as a service’, such as Parse, Firebase, or (yup, I’m gonna say it) Azure***
***[22:11] <MalCurtis> where you interact with data over the wire, completely in their service***
[22:11] <miked> Does Angular work with jQuery UI widgets?
**[22:11] <BradBarrow> Angular wants to look at the browser’s url and decide which pages to render, so if you’re using it inside of something like Ruby on Rails, the two frameworks are going to contest for the use of the routing structure**
***[22:11] <MalCurtis> @miked You’ll find that often jQuery and Angular don’t work together***
***[22:11] <MalCurtis> They work in very different ways***
[22:11] <nanda> So it is not advised to use Angular with a web framework then.
**[22:11] <MalCurtis> But you’ll also find people who’ve solved the problem for you**
**[22:12] <MalCurtis> @nanda Angular IS a web framework**
[22:12] <Good> And what about videogames? Do you know if anyone in the AngularJS team has any thoughts about opening AngularJS for easying HTML5 videogames as a game development framework?
[22:12] <miked> alternatives to the popular widgets like tabs etc.?
**[22:12] <BradBarrow> @miked that being said, [http://angular-ui.github.io/](http://angular-ui.github.io/) is a great project that’s pushing the boundaries when it comes to angular. They’ve implemented many popular ui tools in a true angular fashion.**
***[22:12] <MalCurtis> @Good, Game development is a whole other world, and not the problem Angular tries to solve***
[22:12] <Good> Ok, thank you
***[22:13] <MalCurtis> You could happily run a game site with angular***
**[22:13] <BradBarrow> @nanda I’ve used AngularJS in two Ruby on Rails projects and it’s possible, but the overhead can sometimes not feel worth it.**
***[22:13] <MalCurtis> But the actual game itself would need to use a proper game library***
[22:13] <phil> What are your thoughts on Angular testing with mocks
[22:13] <phil> I have some custom directives that are reasonably complex
[22:13] <Good> How much would you charge for a SPA development now that we can use AngularJS and reduce ETAs?
[22:13] <bloopjs> What is your favorite site/example built with Angular?
[22:13] <phil> and the tests fall over quite a bit and require restarting.
***[22:13] <MalCurtis> @Good How long is a piece of string?***
[22:14] <nanda> What would be the disadvantages of using AngularJS instead of other server side frameworks?
[22:14] <phil> Is this a common experience (they work after the restart)
**[22:14] <BradBarrow> @Good Alex Smith wrote a neat angular article that teaches you how to create a naughts and crosses game. It’s not a complicated game, but it shows off some of angular’s abilities [https://www.sitepoint.com/angularjs-tutorial-build-an-app-using-directives-and-data-binding/](https://www.sitepoint.com/angularjs-tutorial-build-an-app-using-directives-and-data-binding/)**
***[22:14] <MalCurtis> @phil Complexity often is the cause of testing issues***
***[22:14] <MalCurtis> Reduce coupling, perhaps split your directive into multiple sub directives etc.***
[22:15] <Good> BradBarrow, neat! I’ll give it a read
[22:15] <Good> seems interesting! thank you
***[22:15] <MalCurtis> I generally find that if I’m having to stub / mock too much in a test, it means I probably need to rethink how it works***
***[22:15] <MalCurtis> and often it is highly coupled, not loosely coupled***
[22:15] <pamelasue101> Hi everyone
**[22:15] <BradBarrow> @phil I agree with Mal re testing. Ensure that your code is as modular and de-coupled as possible. Writing integration/acceptance tests can often help cover areas where unit testing get’s complicated.**
[22:15] <Randyman5775> Can it be used along with something like Google Alerts or RSS?
***[22:15] <MalCurtis> i.e. it expects an object with a whole lot of attributes from a parent scope, rather than several different variables from the parent scope, which are more explcit***
[22:15] <phil> Even when I simplify things, the tests can fall over – but it’s more that Karma has got itself into a certain state that it requires a restart.it seems
***[22:16] <MalCurtis> @phil Odd, each test should be reset to a blank state. Perhaps your build up and tear down isn’t clearing things efficiently or you’re trying to do much in oe test***
***[22:16] <MalCurtis> one****
[22:17] <Good> Is it true that AngularJS recommends using libaries like Ember.js, otherwise it can’t manage states and so on, isn’t it?
***[22:17] <MalCurtis> Randyman5775 not sure what you mean, if you mean can you get that data, sure – if a service has an API available via JS (i.e. supports Cross Origin Resource Sharing) then yeap***
***[22:17] <MalCurtis> @Good pretty sure AngularJS recommending Ember.js is like Toyota recommending you buy a Ford***
**[22:17] <BradBarrow> @Good I’ve not heard of that before. The Angular UI team has a great solution to state driven development including nested views [http://angular-ui.github.io/ui-router/sample](http://angular-ui.github.io/ui-router/sample)**
[22:17] <phil> @MacCurtis – that sounds worth a try. If you’re not experiencing similar issues then I’ll work on it. Thanks
***[22:18] <MalCurtis> I back what BradBarrow says about ui-router. It’s excellent, and solves all the routing / state problems you may run into***
***[22:18] <MalCurtis> supports nested views , ala ember, and also has multiple siblings views etc.***
[22:18] <Randyman5775> Yes, I’m looking to incorporate aggregated content based on keywords for daily updated content
***[22:18] <MalCurtis> Runs via state rather than routes, which is excellent***
***[22:19] <MalCurtis> Randyman5775 Yea, if you can get the data via JS you’ll have no issues getting it into Angular.***
**[22:19] <BradBarrow> @bloopjs I really liked the use of angular in the web directions south site here [http://webdirections.org/wds13/](http://webdirections.org/wds13/)**
**[22:19] <BradBarrow> @bloopjs there were some great directives used for things like touch events to make it mobile accessible**
***[22:19] <MalCurtis> If you’ve not used Angular before, you should know that Directives are what makes it powerful***
***[22:20] <MalCurtis> You can define new tags (or classes etc) that encapsulate entire chunks of functionality***
[22:20] <Good> ok, nice link, it’s true that all info moves to “json document” format and there are more and more nosql db with documents which angularjs can handle both in client and server side…
***[22:20] <MalCurtis> Making reuse very easy, as well as testing etc.***
**[22:20] <BradBarrow> To add to that, dependency injection means you can create resources that are accessible throughout your app. This promotes decoupling which as we saw earlier makes testing much easier**
***[22:21] <MalCurtis> ‘dependency injection’ = easily get access to your classes etc without worrying about how to access them or polluting the global scope***
[22:22] <HAWK> Welcome if you’ve recently joined. Feel free to jump in with a question at any point.
**[22:22] <BradBarrow> Randyman5775 if you’re interested in consuming RESTful API’s, [https://github.com/mgonto/restangular](https://github.com/mgonto/restangular) is a great little module that provides some helpful tools to do so**
***[22:22] <MalCurtis> i.e. when you create a controller, you can create a function: function(MyClass, MyOtherClass){ … } and based on the parameter names, Angular will supply those classes (as long as they’re registered) to your controller***
[22:23] <Randyman5775> Tanks BradBarrow – I’ll check into that.
[22:23] <Good> Good point, now that you bring up the global scope. Any advices on realizing when it’s time to not include any more variables into $scope … or how big can it big (how many KB/MB can it handle?)
***[22:24] <MalCurtis> @Good It should be very obvious when to put something in $scope or not***
***[22:24] <MalCurtis> If you need a variable only in your controller, it’s not required in $scope***
***[22:24] <MalCurtis> If you need it in a template, you need it in $scope***
**[22:24]** **<BradBarrow> @Good there is a google chrome developer extension called the Angular Batarang, it helps you inspect the footprint of angular in the browser. It can be helpful for debugging and keeping track of stray variables that could be refactored**
[22:25] <Good> Ok, as the scope is the glue between views and models… but what if we need to move lots of data into the view? Has scope ever gave you any size problems in real-time?
[22:25] <Good> BradBarrow, thanks I’ll have a look on it, I’ve written it down in the TOREAD list
***[22:25] <MalCurtis> Interesting question. I don’t believe there’s any more ‘weight’ in having things in scope than out of scope, except when angular has to deal with changes***
***[22:26] <MalCurtis> If you’re putting A LOT of changing data into the scope that doesn’t need to be, then you might be causing processing overhead, but not ‘memory’ footprint***
**[22:26] <BradBarrow> @Good I recently created an API endpoint and an Angular factory to consume it. It pulled down a json structure with nearly 2000 records each with about 40 properties. These went straight onto a scope variable and angular was able to sort/filter the collection in real time without a problem**
[22:26] <phil> How do you all see Angular developing in the next couple of years. It already seems incredibly powerful and there are big libraries of widgets built on top of the directive capability. What are your thoughts?
**[22:26] <MalCurtis> i.e. Angular knows when things changes, but it has to do that by observing function values and scope variables, so if you’re changing those values a lot, then it needs tocheck more often**
[22:26] <BradBarrow> That said, it was a prototype and I recommend using a paginated approach when dealing with large data sets
***[22:26] <MalCurtis> if you can move preprocessing into the controller, and just the output into the scope, then that will reduce processing***
[22:27] <nanda> When would you use Angular and when wouldn’t you use it?
[22:27] <Good> BradBarrow ok, I understand, thank you for sharing that experience, now I’m aware of some numbers… thank you
***[22:27] <MalCurtis> @phil I wouldn’t be surprised if we see more of the community projects getting bundled into core, or at least a more modular system – such as ui-router***
**[22:27] <BradBarrow> @phil I think they’ll move more and more towards a Javascript OOP approach and slightly further away from their own “language” as it’s been referred to. Already you can bind properties to the angular controllers themselves using the ‘this’ keyword rather than $scope meaning that javascript developers will feel more at home with the language.**
**[22:28] <BradBarrow> (that’s in the unstable release)**
[22:28] <Maciek> How would you define AngularJS in comparison to Sencha’s Ext? I mean purpose, scope and features.
[22:28] <phil> @bradbarrow – do you see it moving towards the likes of dart, then?
[22:29] <phil> (for the OO bit, I mean)
***[22:29] <MalCurtis> Maciek Angular is more framework, less widget library***
***[22:29] <MalCurtis> @phil AngularJS core just announced a Dart compatible port of nG***
***[22:29] <MalCurtis> ng***
***[22:29] <MalCurtis> Ng***
***[22:29] <MalCurtis> whatever the abbrev is***
***[22:30] <MalCurtis> [https://github.com/angular/angular.dart](https://github.com/angular/angular.dart)***
[22:30] <phil> that answers my question I suppose!
***[22:30] <MalCurtis> ;)***
***[22:30] <MalCurtis> [http://news.dartlang.org/2013/11/angular-announces-angulardart.html](http://news.dartlang.org/2013/11/angular-announces-angulardart.html)***
[22:31] <Good> Any thoughts on compatibility between AngularJS and this other project of Google which is a web development framework? I don’t recall the name but it was something like projector or prototype or something like that
[22:31] <phil> I can sort of see it getting to the stage where the DOM becomes a bit like the assember language of browsers
[22:31] <phil> assembler
**[22:31] <BradBarrow> @good you may be thinking of polymer?**
***[22:31] <MalCurtis> @good Google Web Developer or something like that?***
[22:31] <Good> Yeah, that’s it
***[22:31] <MalCurtis> s/developer/designer***
[22:31] <Good> thank you, polymer
[22:31] <Good> Are they compatible with each other?
**[22:32] <BradBarrow> Polymer borrows a lot of angular’s idealogies, but I’d say they’re two separate solutions to one problem.**
***[22:32] <MalCurtis> Oh, hadn’t seen Polymer. Feel a little bit like that Mozilla one***
***[22:32] <MalCurtis> [http://mozilla.github.io/brick/](http://mozilla.github.io/brick/)***
**[22:33] <BradBarrow> With the rise of prominence around the Shadow Dom, developers are becoming more comfortable with the likes of Angular’s directives – encapsulated UI components with dynamic behaviour right in the DOM**
***[22:33] <MalCurtis> this ^***
[22:33] <Good> Ok, thank you for your link MalCurtis I didn’t know about Brick, I’ll give it a read
**[22:35] <BradBarrow> It’s a move away from the current segregation between javascript application logic and presentation. The old adage of “keep logic out of the view” still stands – but we can certainly benefit from making the view more declaritive thereby saving developer hours and creating more robust applications**
***[22:35] <MalCurtis> Anyone have any questions that haven’t been answered?***
[22:35] <Good> I wish AngularJS helped in unifying browser behaviour… all into Google Chrome or Mozilla Firefox
[22:35] <Good> I mean I hope it does
***[22:36] <MalCurtis> In terms of creating a single ‘interface’ that works across all browsers?***
***[22:36] <MalCurtis> Or making less people use IE and Safari…?***
[22:36] <Good> lol
[22:36] <Good> IE …
[22:37] <Good> I hope they catch up with AngularJs … otherwise I don’t know how it’s gonna end up form the IE guys
***[22:37] <MalCurtis> I’m ok with IE as of this week***
***[22:37] <MalCurtis> Current + Last = ok***
***[22:37] <MalCurtis> AngularJS on 10+ should be fine***
[22:37] <Good> Ok.
**[22:37] <BradBarrow> @Good Google has just dropped support for IE9 with regards to their Docs platform and a few others. They’re trend setters so we can look forward to using IE10+ which is actually ok**
***[22:37] <MalCurtis> 10 was the first IE which finally just accepted standards across almost all of the places you use them***
***[22:37] <MalCurtis> including CORS etc.***
[22:38] <BradBarrow> If anyone is looking to get started quickly with Angular I’d strongly suggest taking a look at the AngularJS Yeoman Generator maintained by Brian Ford: [https://github.com/yeoman/generator-angular](https://github.com/yeoman/generator-angular)
**[22:40] <BradBarrow> It helps you get set up with a static site, AngularJS and a snappy development environment in minutes. I use it to rapid prototype Angular apps rather than things like JSfiddle/CodePen**
[22:40] <HAWK> If anyone has a question that hasn’t been answered, now would be a good time to ask it.
[22:41] <HAWK> Welcome to those of you that have just joined :)
[22:41] <RogerC> hello
[22:41] <Mikef> Hi
[22:41] <Jayfang> Howdy
**[22:42] <BradBarrow> Hi folks, have any of you used AngularJS before? What’s your experience/opinions so far? Have any questions for us?**
[22:42] <Good> Ok, thank you. What would you say AngularJS can’t beat or can’t develop easily than the others. Many people don’t want to move away from LAMP when I thing the MEAN profile is gonna be the future… or it seems like it, but I’d like to know if there are any drawbacks with AngularJS that Misjo is not telling us :D
[22:42] <Good> I think there aren’t.
***[22:43] <MalCurtis> @Good You don’t need to move away from LAMP to run Ng***
***[22:43] <MalCurtis> LAMP is your backend however.***
***[22:43]** **<MalCurtis> Angular’s biggest***
[22:43] <Good> ah, sorry
***[22:43] <MalCurtis> ‘problem’ – is that it’s got a steep learning curve***
***[22:44] <MalCurtis> So it’s hard to beat the ‘simplicity’ of jquery***
**[22:44] <BradBarrow> @Good I think it’ll always depend on the application itself. Mongo vs Mysql – they’re incredibly different solutions. Node vs Apache..again one specialises in real time server behaviour. It’s about choosing the right tool for the right application.**
[22:45] <phil> What do you think of a MEAN stack as an alternative to LAMP. Or are you thinking of them as similar?
[22:45] <Good> Ok. And… regarding Promises and the use of them in AngularJS. Do you think it’s worthwhile for me to learn to apply promises to my backend code instead of having the dreadful identation piramid?
**[22:45] <BradBarrow> We have to remember that developers will flock to the latest and greatest so MEAN is certainly popular…but that doesn’t mean you can’t integrate Angular into an existing stack that you’re comfortable with RoR/LAMP**
**[22:46] <MalCurtis> @Good that assumes your backend code is in a callback soup language, e.g. JS**
[22:46] <Good> I read some article of the Q library developer saying why node should use them or something like that… I don’t recall precisely… sorry
[22:46] <Good> ok
[22:47] <Mikef> Hi I am doing some stuff with canvas via fabric.js running inside Angular, so far it has been smooth running, which is good news as I am not a JS expert. I need to start sharing data between views and was wondering whether parameters or shared service was the way to go.
**[22:47] <BradBarrow> @phil I think a MEAN stack is great for smaller snappier and highly interactive applications. It makes the use of Socket.IO and the like much smoother making applications such as this chat app we’re using right now a breeze to build (right Mal?). LAMP and RoR stacks on the other hand are tried and test…convention over configuartion and are know t**
**[22:47] <BradBarrow> o be reliable with high traffic loads.**
***[22:47] <MalCurtis> @mikef Where is the data, and where are the views?***
***[22:48] <MalCurtis> Generally a view has a controller attached, which uses the $scope to bind them together***
[22:48] <Mikef> At the moment the data is in local storage and the views are partials in their own files being switched by a router module.
**[22:48] <BradBarrow> @Good personally I think promises are one of the best developments in recent years when it comes to asynchronous languages and I find that they’re more versatile, readable and powerful than callbacks. Any opportunity to use them I’d jump on it**
***[22:49] <MalCurtis> Ok, so you need to have a controller which does some massaging there***
[22:49] <Good> Thank you BradBarrow
***[22:49] <MalCurtis> In your views you can access anything you add to the $scope of the attached controller***
***[22:49] <MalCurtis> So your controller might grab stuff from localstorage, then shove it in $scope.myData***
***[22:49] <MalCurtis> and your view might iterate on myData (it doesn’t need the scope bit)***
***[22:50] <MalCurtis> such as <ul><li ng-repeat=”data in myData”>{{data.someProperty}}</li></ul>***
***[22:50] <MalCurtis> Angular knows when the data changes, so you don’t need to worry about telling it when your call for data is complete***
**[22:51] <BradBarrow> A nifty module for working with local/session storage: [https://github.com/gsklee/ngStorage](https://github.com/gsklee/ngStorage)**
[22:53] <HAWK> We have about 5 minutes left in the session. Does anyone have any last questions?
***[22:53] <MalCurtis> BradBarrow Just saw your comment, yea it was total breeze to build this app. No swearing involved…***
***[22:53] <MalCurtis> /sarcasm***
**[22:54] <BradBarrow> There are some great videos in this channel around Javascript development including Node’s asynchronous nature, promises and REST apis for those of you who are interested (@Good) [http://www.youtube.com/user/webdirections/videos](http://www.youtube.com/user/webdirections/videos)**
[22:54] <Good> I just wanted to thank you for your sharing your knowledge. Know that I’ll be sending good karma/positive karme to some stars here in Spain so that get to you in the US! :D
[22:54] <Mikef> Thanks guys for the advice :0)
***[22:55] <MalCurtis> I’m in NZ ;) Brad’s in AU***
***[22:55] <MalCurtis> Brad****
**[22:55] <BradBarrow> No problem! The more people who use Angular the more supportive the community will become :)**
[22:55] <Good> Sorry ’bout that. So to NZ and AU they go!
***[22:55] <MalCurtis> :D***
[22:55] <Jayfang> Thanks, I’ll definitely grab the transcript learnt stuff even in the last 10 minutes! :)
[22:55] <HAWK> Cool. I’ll put the transcript up later today.
**[22:56] <BradBarrow> I highly recommend keeping an eye on AngularJS’s google+ page: [https://plus.google.com/+AngularJS](https://plus.google.com/+AngularJS)**
***[22:56] <MalCurtis> If anyone came here wondering if they should ‘try’ angular, the answer is flat out – yes: Give it a shot. Remember that it has a bit of alearning curve though so persist.***
**[22:56] <BradBarrow> Youtube channel: [http://www.youtube.com/user/angularjs](http://www.youtube.com/user/angularjs)**
[22:56] <HAWK> Any last questions before I cut our experts loose?
**[22:56] <BradBarrow> And google groups: [https://groups.google.com/forum/#!forum/angular](https://groups.google.com/forum/#!forum/angular)**
***[22:56] <MalCurtis> A good first tutorial is Alex’s one @ Sitepoint: [https://www.sitepoint.com/angularjs-tutorial-build-an-app-using-directives-and-data-binding/](https://www.sitepoint.com/angularjs-tutorial-build-an-app-using-directives-and-data-binding/)***
[22:57] <Jayfang> MalCurtis 100% agree, used after emberjs – harder to learn but far “superior”
**[22:57] <BradBarrow> \m/**
[22:57] <HAWK> Awesome. Well thanks heaps for your time this morning MalCurtis and BradBarrow
**[22:58] <BradBarrow> No problem – it’s been fun :)**
***[22:58] <MalCurtis> np***
[22:58] <Good> Applauses
[22:58] <HAWK> And thanks to everyone that joined and asked questions. Have a great day/night.
[22:59] <Good> You too, thank you
**[22:59] <BradBarrow> Bye!**

## 分享这篇文章