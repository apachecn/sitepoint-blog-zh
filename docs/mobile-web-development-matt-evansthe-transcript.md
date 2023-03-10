# Matt Evans 的移动网络开发:抄本

> 原文：<https://www.sitepoint.com/mobile-web-development-matt-evansthe-transcript/>

今天早上，我在[可学聊天室](https://learnable.com/twte "Learnable chatroom")主持了另一场[与专家对话系列](https://learnable.com/twte "Talk with the Experts")的现场聊天。如果你最近没有检查过，一定要检查一下。它经过了一点改造，看起来焕然一新。

今天的主题是**移动网络开发**，专家是 [Wave Digital](http://wavedigital.com.au/ "Wave Digital") 的 Matt Evans。会议中有许多新面孔，问题也很有趣。我们讨论了从平台到菜鸟失误的一切。如果你曾经想过编写(或委托)一个移动应用程序，请确保你已经阅读了所发生的事情。

如果您因为不知道而错过了今天的会议，您可以在此注册接收未来会议的电子邮件通知[。下周我们将讨论一旦你有了你的应用程序(虽然不一定是移动的)该做什么。为了不浪费太多宝贵的开发时间，支持 it 的最佳方法是什么？Michael de Wildt 将于 10 月 30 日(T5)周三下午 1:30 在**太平洋时间谈论**如何支持你的应用**。**](https://learnable.com/twte "Email notifications")

现在，如果你想知道今天早上到底发生了什么，这里有一份完整的文字记录供你阅读:

[20:29] <HAWK> Ok Matt – want to give us a quick intro of what you do?
**[20:29] <Matt> Morning all. I’m Matt, a mobile developer now based in Melbourne, originally from New Zealand.**
**[20:30] <Matt> Currently work across a range of web and mobile projects, so looking forward to helping answer some of your questions.**
[20:30] <HAWK> If anyone wants to kick off first with a question, you’ll have Matt’s full attention. ;)
[20:30] <Matt> :-)
[20:31] <Warrington> As a teacher I am looking at using game development for helping teach programming
[20:31] <PaulE> What is the most popular platform for mobile development?
[20:31] <Warrington> I was wondering what software would you recommend
[20:31] <Warrington> to teach students?
[20:32] <HAWK> Great questions. I’ll start queueing them for Matt so that we don’t miss anything.
[20:33] <PaulE> Should I write apps for Apple or Android?
[20:33] <adamqureshi> apple of course
[20:33] <Warrington> Also what software do you use for creating sprites?
**[20:33] <Matt> Warrington – Native game development? I would recommend looking into developing for iOS (XCode). [http://www.cocos2d-iphone.org/](http://www.cocos2d-iphone.org/) Is a popular game engine to get you started.**
[20:33] <PaulE> How difficult is it to write for both Apple and Android?
[20:33] <adamqureshi> what about that new HTML5 3D engine
[20:33] <adamqureshi> focus on 1
[20:33] <PaulE> Is it best to stick to just one platform?
[20:33] <adamqureshi> why not make a web mobile app / validate then move to native
[20:33] <adamqureshi> if you want you can make mine
[20:34] <adamqureshi> i have the prototype
[20:34] <adamqureshi> HTML/CSS
[20:34] <PaulE> Can Matt answer the questions please? ;)
**[20:34] <Matt> PaulE – The most popular mobile platform… I would say iOS, but I’m slightly biased.**
[20:34] <HAWK> Is it difficult to write for both?
[20:35] <MalCurtis> “popular” is an interesting metric. Most sold = android, most used & most engaged = iOS
**[20:35] <Matt> PaulE You have a couple of options when writing for both.**
[20:35] <PaulE> I like IOS too but I think Android is the bigger market :)
[20:35] <adamqureshi> i know you did not ask, but why dont you try [http://famo.us/](http://famo.us/)
[20:35] <MalCurtis> bigger market != profitable market
**[20:35] <Matt> 1) Develop both natively (two separate builds)**
[20:36] <adamqureshi> why not look at the evidence. then decide based on the facts?
[20:36] <PaulE> I thought this was a QA with Matt?
**[20:36] <Matt> 2) Use a framework like PhoneGap ([http://phonegap.com/)](http://phonegap.com/))**
[20:36] <Warrington> Any thoughts on using Gamemaker?
[20:36] <adamqureshi> titaniam
[20:36] <HAWK> It is PaulE – although I do encourage people to chat if they can help each other constructively
[20:37] <rokio> Do you use responsive web design when you build your mobile applications or since you are building specifically for mobile it is not necessary?
**[20:37] <Matt> 3) Roll your own hyrbid style solution (similar to PhoneGap), but you will have more control**
[20:37] <adamqureshi> mobile fist of course
[20:37] <PaulE> The comments aren’t constructive. I would prefer Matts input :)
[20:37] <HAWK> Fair call. Tone it down a bit please adamqureshi :)
[20:38] <adamqureshi> sorry. My enthusiam ;-)
[20:38] <adamqureshi> who is matt? lol
[20:38] <PaulE> That’s ok adamqureshi ;)
**[20:38] <Matt> @rokio We do use RWD here. It does depend on the project. We often find clients wanting apps for both platforms (iOS + Android) but not willing to make the large investment for two separate builds.**
[20:39] <PaulE> Good point
**[20:39] <Matt> rokio Nearly all web work we undertake here will always have a mobile component, normally responsive.**
[20:39] <PaulE> That explains many companies launching one or the other and then following up at a later date with the other…
**[20:40] <Matt> That’s right. Starting with a single platform to ‘test the water’ is a good way to go about it.**
[20:40] <adamqureshi> I am making a web / mobile app and only after i validate will i go native, my experience..
[20:40] <Jen> How about web apps html5
[20:40] <adamqureshi> I always VALIDATE the idea and see if its worth making
[20:41] <adamqureshi> I have an idea a min.
[20:41] <MalCurtis> Is adamqureshi a “lean startup” buzzword bot?
[20:41] <adamqureshi> no.
[20:41] <HAWK> Heh
[20:41] <adamqureshi> LOL
[20:41] <Speed> Have you ever built any iOS apps that talk to a database not located on the device itself? Would you use a cloud database for something like this?
[20:41] <Adnan> lol
[20:41] <adamqureshi> buzzword, is there an app for that
[20:41] <Adnan> Speed has a good question
**[20:41] <Matt> Jen – Good question! There’s a lot of opinions floating around about HTML5 apps…**
[20:42] <Adnan> +1
[20:42] <PaulE> like
**[20:43] <Matt> Some encourage the ‘write once’ approach of the HTML5 style applications, others prefer the control and better experience they can achieve with native builds.**
[20:43] <adamqureshi> speed is king with native, as you very well know..
[20:43] <adamqureshi> web is catching up.
[20:43] <adamqureshi> camera / GPS …
**[20:43] <Matt> Speed – Sure have. You’re app will want to talk to a database via an API**
[20:44] <Speed> Any recommendations on cloud database hosting?
**[20:44] <Matt> Speed If you have multiple end points the app needs to communicate with, I would encourage developing a middle layer that can control the flow of data between the device and the backend.**
**[20:45] <Matt> Speed – I’d have to recommend Amazons RDS – [http://aws.amazon.com/rds/](http://aws.amazon.com/rds/)**
[20:45] <Speed> Yes, I was thinking that. We are thinking of building a middle layer to manage billing as well.
[20:45] <Jen> so with html5 we can’t use some native features, but this seems to be a changing trend towards getting more mobile features on laptops and tablets..even we are have touch screen tablets now, desktops are old school
[20:46] <Jen> I just think in another year there wont be much value in native apps we can do everything through html5?
[20:46] <Speed> Thanks Matt. I’ll look into Amazon RDS.
[20:46] <adamqureshi> matt have you used PARSE?
[20:46] <Adnan> how about Google app Engine Matt
[20:47] <adamqureshi> for backend architecture?
**[20:48] <Matt> Jen – That’s right. The web is moving in the right direction. Some argue that HTML5 apps provide a degraded experience for the user (speed, etc). As mentioned above, I believe it comes down to a decision best made on a project by project basis.**
[20:48] <qoonik> What setup do you suggest uwith Phonegap (JQM or something else). Now I am using backbone+JQM.
[20:48] <HAWK> FYI I’m queueing all questions so don’t worry about anything getting missed. I’ll also post a full transcript up on SitePoint later today and email it to anyone on the TWTE mailing list
[20:49] <qnoox> first of all i love you hawk ur the best! but on a side note my question is matt do you have any tips and tricks when it comes to the look and feel about let’s for example say android app’s i find the code is easy buy making it look good is harder.
**[20:49] <Matt> adamqureshi – I’ve haven’t used PARSE. Looks nifty, will have to check that out!**
[20:49] <adamqureshi> matt: ok. but i think they have been aquired by FB ;-)
[20:49] <HAWK> Ha! Thanks qnoox.
[20:50] <adamqureshi> also there is a new solution which lets you build a messaging system called “layer”
[20:50] <adamqureshi> for native iOS apps
**[20:50] <Matt> @qoonik I think you’re on the right track using Backbone over JQM. I’ve personally never been a fan of JQM, feels rather clunky.**
[20:50] <adamqureshi> 10 lines of code
[20:51] <chughgaurav> for developing a web app for mobile & desktop, responsive design is being used, which i think takes a lot of bandwidth on mobiles. What can one do in such a scenario ? Angular Js ?
[20:51] <qoonik> Matt, so what do suggest use instead JQM?
[20:52] <adamqureshi> matt have you tried [http://famo.us/](http://famo.us/) as a fame engine for HTML 5
[20:52] <adamqureshi> game engine. lol
[20:52] <MalCurtis> chughgaurav angular js won’t solve your bandwidth issues. RWD isn’t going to fix those issues either. If you’ve got a large target market on mobile, dedicated mobile sites can still be beneficial
**[20:52] <Matt> qnoox – When it comes to HTML5 apps look-and-feel, I believe the trick is not make them platform specific. Too many times you will find Android apps, with an iOS lick of paint. Users expect an app designed for their device.**
[20:52] <adamqureshi> Matt best practice is to use “native controls” for ios apps? save time / cost?
[20:53] <chughgaurav> yes, the target audience is large, which platform will be good to develop the mobile web app, any recommendations ?
[20:53] <qnoox> thanks matt!
**[20:54] <Matt> chughgaurav – There’s many blog posts in the wild about that exact topic. There are optimisations to be made with RWD, but as Mal said, sometimes a dedicated m site is not necessarily the bad alternative.**
[20:55] <chughgaurav> Thanks MalCurtis & Matt
**[20:55] <Matt> @ adamqureshi – Absolutely. I’d encourage the use of native controls. Prime example, with the recent iOS7 update, any custom designed elements and controls will need to be reworked, as they don’t conform to the default**
[20:56] <adamqureshi> Matt gotcha!
[20:56] <qoonik> Ok so any suggestion how to make html5 app looks native on mobile device (except JQM :))?
[20:56] <HAWK> You’re doing a legendary job of staying on top of all the questions Matt. Is there anyone that hasn’t had an answer yet?
[20:57] <adamqureshi> chughgaurav also this CLANK: An open source HTML/CSS framework for prototyping native phone/tablet applications.
**[20:59] <Matt> qnoox – It’s a hard one, I would personally develop my own ‘hybrid’ style solution. This would entail building all controls natively (across the two platforms) and integrating your web elements as tightly as possible. Both Obj-C and Java allow you to talk through webviews via JS!**
[21:00] <adamqureshi> Matt wouldn’t it be better to build wireframes / clickable prototype get it right and then jump into dev?
[21:01] <qnoox> thanks matt that helped me a lot :)
[21:01] <Speed> Matt, what toolset do you use to build iOS apps?
**[21:01] <Matt> @adnan – Sorry, only just got to this one. I haven’t used Google App Engine as much as I’d like. Majority of the work we do has certain requirements around where the infrastructure should lay, etc. People I’ve spoken with using it, often use it for smaller applications, or one off style micro-sites.**
[21:01] <Jen> Matt – thanks it’s quite exciting
[21:01] <PaulE> Good call adamqureshi :)
**[21:01] <Matt> @adamqureshi Spot on. ALWAYS wireframe before typing code.**
[21:01] <qoonik> @Matt ok but sometimes you want to use some ready solution that just works.
**[21:02] <Matt> The don’t necessarily have to be interactive, paper often works just as well, but they allow you to highlight areas of risk, and often get people asking questions about your app that you may have over looked.**
[21:03] <adamqureshi> The issue is you will never “KNOW” what works, trial and error / observation / test …
[21:03] <Jen> @Matt I love making paper prototypes
**[21:03] <Matt> Speed – Xcode! There’s lots of frameworks and handy controls out there to give you a helping hand.**
[21:04] <adamqureshi> Jen paper prototypes are COOL and very low cost / useful , they provide a great insight into mental models of users
[21:05] <Jen> @adam yes then decide how to build/ outsource or build in house etc
**[21:05] <Matt> Speed – If you’re wanting to get started with iOS, I’d suggest looking into the Standford Lectures available for free on iTunesU (**
**[21:05] <Matt> [http://bit.ly/16vT6DJ](http://bit.ly/16vT6DJ))**
**[21:07] <Matt> Also, Ray Wenderlich provides some excellent walk-throughs for getting started.**
**[21:07] <Matt> [http://bit.ly/1aay53Z](http://bit.ly/1aay53Z)**
[21:08] <Matt> Those questions have slowed down a bit… anyone else?
[21:08] <adamqureshi> @jen I see. cool
[21:08] <adamqureshi> @matt how long before the web catches up to native?
[21:09] <adamqureshi> @matt full device control
[21:09] <qoonik> there will be oreilly webcast is someone interested goo.gl/TXKL1y
[21:09] <PaulE> @Matt thanks for the Stanford tip!
**[21:09] <Matt> adamqureshi – Wow. What a question that is. I don’t know I can provide you a proper answer on that one, but the web is certainly moving faster than it ever has.**
[21:10] <adamqureshi> Matt i’d say 5 years
[21:10] <adamqureshi> broadband penetration + mobile browser. Its only a matter of time
[21:11] <adamqureshi> Matt our limitation is the pipes / broadband. Once we have google fibor. it will be over for native apps
[21:11] <CaptainSin> Is an app a good idea for small business eCommerce shops with a small following?
**[21:12] <Matt> CaptainSin – I would have to say you’re probably suited better to a mobile site.**
[21:12] <Speed> Thanks Matt. Will do.
[21:12] <HAWK> What is the biggest mistake that people make when attempting to build their first app? What should a first timer avoid?
[21:13] <adamqureshi> haha
**[21:13]** **<Matt> For larger organisations a native app my come in handy, but for a small business, the lower investment of a mobile site would probably benefit you more.**
[21:13] <adamqureshi> I would say a mobile / webb app is enough for small business.
[21:13] <Mladen> Responsive web design or app. Which one is better? :)
[21:13] <CaptainSin> @ Matt Thanks. I wan’t sure what to tell clients. They want an app for everything now a days.
**[21:13]** **<Matt> HAWK – Good questions!**
[21:14] <adamqureshi> HAWK can i provide my view?
[21:14] <HAWK> Sure, go for it
[21:15] <adamqureshi> the biggest mistake is they think of technology first, when the correct solution is Psychology first / technology second
**[21:15] <Matt> 1) Trying to get everything into their app, v1\. Start small, add functionality later, see what users want. (Don’t ever remove functionality unless you’re 100% sure its not being used – I’ve learnt the hard way)**
[21:15] <HAWK> Yeah, makes sense
[21:15] <PaulE> Thanks Matt. Good point
[21:16] <HAWK> People get over excited
[21:16] <adamqureshi> They would be better off making wireframes and building a clickable prototype and have the users use it / and start to remove features , less is more, should be focused on “TASK” completion
[21:16] <bruce> but if we live in a area where there is not good mobile coverage then we need to lean more toward native apps and update native app when connectivity is good ???
[21:16] <Speed> Yes, I’ve learnt my lessons about removing or changing functionality too. If others want something different, make it configurable or an option.
[21:16] <qnoox> i should say build what you like at first then build what others like.
**[21:17] <Matt> Mladen – This has been one of the hottest questions on the Internet for awhile now. Again, I’m going to have to say it comes down to the customer and use case of what you’re trying to deliver.**
[21:17] <adamqureshi> Matt but there is a distinction between / Mobile first / RWD ?
[21:18] <Mladen> I think that web site mobile app is just wasting time and money
**[21:18] <Matt> In the case of one of our larger clients, we started with RWD, then over 1 year later, we finally made the move to native for them. The reason, we could offer native users added functionality and a better experience. If that wasnt the case, we wouldn’t have done it.**
[21:19] <adamqureshi> Mladen depends on if your developing your “own” app / start up or if its client work and that comes with constraints. budget / time / etc…
[21:19] <adamqureshi> Matt what’cha think?
[21:20] <adamqureshi> How you approach a problem depends on “what type of problem” it is, meaning if you want to make your own app / idea there are different constraints then doing client work.
[21:20] <Warrington> From a teaching perspective – we use the free version of Gamemaker to teach the basic concepts of programming. The full collection allows for developing for different platforms. I thought this might help inspire the girls further, any thoughts about Gamemaker Studio?
**[21:21] <Matt> bruce – Good question. Network connectivity often plays a part in users decisions to swing one way of the other (native or web). And you’re right, often, native is the way to go to handle offline capabilities.**
[21:21] <adamqureshi> Matt local storage is catching up? no?
[21:23] <Matt> Warrington – I’ve never used Gamemaker Studio, it cross compiles (similar to PhoneGap)?
[21:23] <adamqureshi> Warrington i’m curious if you’ve tried famo.us , its a HTML 5 gaming engine.
[21:23] <adamqureshi> Warrington famo.us: lets developers write fully 3D apps without having to depend on complex programming tricks or compiled apps
[21:24] <HAWK> There are ~5 minutes left in the session so if you’re sitting on a question, this is your last chance!
[21:24] <Warrington> Us it compiles to iOS, Android and HTML5
[21:24] <Warrington> Yes sorry
[21:25] <Warrington> never heard of famo.us
[21:25] <adamqureshi> [http://famo.us/](http://famo.us/)
**[21:25] <Matt> Warrington – Sounds like a great tool for your students to begin with. Hopefully it will inspire them some more to dive into mobile development some more.**
[21:25] <Warrington> I have to be careful that I use software that is achievable for a wide range of girls but is able to be scaled for the more talented students.
[21:26] <adamqureshi> It’s a lot like the interfaces you see in Iron Man or Minority Report, and it works on nearly any platform, including desktops, Android, and iOS
[21:26] <Warrington> Yes – I have some really talented programmers who I want to push further
[21:26] <Warrington> Looks cool – will check it out
**[21:27] <Matt> @adamqureshi – Yes, local storage has come leaps and bounds in recent times. There was a few questions raised about its performance, [https://hacks.mozilla.org/2012/03/there-is-no-simple-solution-for-local-storage/](https://hacks.mozilla.org/2012/03/there-is-no-simple-solution-for-local-storage/)**
[21:27] <adamqureshi> Matt interesting
**[21:27] <Matt> From which John Allsopp responded with [http://www.webdirections.org/blog/localStorage-perhaps-not-so-harmful/](http://www.webdirections.org/blog/localStorage-perhaps-not-so-harmful/)**
[21:28] <HAWK> It’s just about a wrap people. Speak now or forever hold your peace.
**[21:28] <Matt> :-)**

## 分享这篇文章