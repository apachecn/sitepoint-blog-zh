# 安迪·霍索恩和格伦·古德里奇的 rails 4-抄本

> 原文：<https://www.sitepoint.com/rails-4-with-andy-hawthorne-glenn-goodrich-the-transcript/>

又到了一周的这个时候——我发现自己和专家们称兄道弟的时候。本周提到的专家有安迪·霍桑——《Jump Start Rails》的作者和 T2 ruby source 的格伦·古德里奇。我们聊到了 Rails——更确切地说是 Rails 4。这是一次有趣的会议；与会者比与专家交谈的平均人数要少，但交谈很充实，从中得出了一些有趣的提示。

我整理了一个小时后的资源列表，给那些不想浏览整个文稿的人。

**Andy 的代表作:**
[Jump Start Rails](https://learnable.com/books/jsrails1 "Jump Start Rails")
[打造你的第一个 Rails App](https://learnable.com/courses/build-your-first-rails-app-2784 "Build Your First Rails App")
**一些有帮助的 RubySource 文章:** [Streaming with Rails 4](https://www.sitepoint.com/streaming-with-rails-4/ "Streaming with Rails 4") [让你的 App 准备好 Rails 4](https://www.sitepoint.com/get-your-app-ready-for-rails-4/ "Get your app ready for Rails 4") [PHP to Sinatra](https://www.sitepoint.com/php-to-sinatra/ "PHP to Sinatra")
**其他一些有用的资源:**
[升级到 Rails 4](http://railscasts.com/episodes/415-upgrading-to-rails-4 "Upgrading to Rails 4") 

如果你因为不知道而错过了今天早上的会议，确保你[注册了电子邮件通知](https://experts.learnable.com/ "Talk with the Experts")。下周是与 SitePoint 博客和全能内容天才乔治娜·莱德劳的**内容策略**。那场比赛将于 8 月 6 日星期二太平洋时间下午 2:30 在**举行。你可以在这里找到你那里的时间。**

如果你想知道今天到底发生了什么，这里有完整的文字记录供你阅读:

[21:26] <Glenn> RAILS ALL THE THINGS! :0
[21:27] <HAWK> So for anyone that doesn’t know, Glenn is our editor at RubySource, and Andy wrote JumpStart Rails [https://learnable.com/books/jsrails1](https://learnable.com/books/jsrails1)
[21:27] <HAWK> I think you have a course or two on Learnable as well, don’t you Andy?
[21:28] <Andy> I have a Rails course on Learnable
[21:29] <johnlacey> Build Your First Rails App: [https://learnable.com/courses/build-your-first-rails-app-2784](https://learnable.com/courses/build-your-first-rails-app-2784)
[21:29] <HAWK> Welcome to those of you that have just joined
[21:30] <joe> @ Andy I am a beginner, i want to know the benefits of rails please
[21:31] <Andy> joe that’s a big question. Are you a beginner with Rails, but you’ve developed for the web before?
[21:31] <HAWK> Good question to kick off with joe :)
[21:32] <joe> i am a beginner with web designing, i enrolled in on this site just 2 days back
[21:33] <joe> But i was curious to know how this section is, so i joined
[21:33] <HAWK> Sweet – welcome.
[21:33] <joe> but from your conversation, i am totally lost here
[21:33] <joe> that was why i asked that
[21:33] <lj> Is rails a good choice for a literary magazine project?
[21:35] <Glenn> @lj I am not sure what the requirements of a literary magazine project are, but Rails is good choice for just about anything that has a database on the back of it. What are your requirements?
[21:35] <Andy> @ joe ok, well, we all have to start somewhere! Rails does have a bit of a learning curve. But it’s a good place to start because with a bit of effort you’ll be building web apps that are secure and robust
[21:35] <Krenski> Going along with @joe’s question – It seems like some of the JS frameworks are taking over the web app space. Any thoughts on when to choose Rails vs something more direct like Node?
[21:35] <lj> poems and images of art, a table of contents, a search, issues
[21:36] <Glenn> @lj, sure. Rails would do well with that.
[21:36] <Andy> @lj sounds like a Rails project to me…
[21:36] <saqib> Glenn what about realtime apps ?
[21:36] <Glenn> @Krenski, I am not sure I agree that Node is “more direct”, it’s just a different approach.
[21:37] <lj> would the course rails 4 help me get started with that? I have already built 2-3 simple rails projects
[21:37] <Glenn> Plus, you’d likely need a web framework in Node (like Express, which is good)
[21:37] <joe> @ Andy okay, so meaning rails is not integrated in web pages?
[21:38] <Andy> joe Rails is a framework for developing web apps. It helps you create the web pages for your project in a structured and organised way.
[21:38] <Glenn> @Krenski, Rails, right now, is miles ahead on some of the conventions in just handles (CSRF, security, tons of gems) If you like Ruby, Rails is for you. If you are more of a JS person, I would look into Node, Express, etc.
[21:38] <lj> You mean rails plus node.js??or Express? I don’t know anything about those. I do know html and css and have worked with bootstrap sass and a grid
[21:39] <Glenn> @lj, I was talking to @Krenski….sorry
[21:39] <lj> oh ok…
[21:39] <kinduff> Ij, what are you trying to built?
[21:39] <james> Hi! Does RoR have steep learning curve? Do I need to have an extensive background and knowledge about Ruby in order to succeed at RoR?
[21:40] <lj> A magazine with poems and art and about 10 issues Id need to convert from html
[21:40] <HAWK> Hey james – welcome. Good question.
[21:40] <Andy> james it does have learning curve, but not so steep that you can’t get something up and running quickly
[21:40] <Glenn> @saqib Rails 4 does add some streaming/live capabilities, but they are young. We just had a post on Rubysource about it [https://www.sitepoint.com/streaming-with-rails-4/](https://www.sitepoint.com/streaming-with-rails-4/)
[21:40] <saqib> james yes you to know about ruby to start working in Rails..
[21:40] <Andy> james you can learn Ruby while you learn Rails
[21:41] <saqib> should*
[21:41] <kinduff> james I didn’t had idea of what Ruby was when I started with Rails, but now I’m thinking about it, my advice is: learn good and clean Ruby first
[21:41] <Glenn> @james I would ad that starting with Rails lets you learn “web stuff” while you are learning Ruby.
[21:41] <joe> Oh God, am lost here!!
[21:42] <lj> A learnable ruby course would be good…I’m a beginner with that
[21:42] <HAWK> It’s all good joe – hang around for a bit. FYI we often have less technical chats, and beginner sessions. :)
[21:42] <Glenn> @james, I started with Rails (not Ruby) and it was fine. Learning Ruby first won’t hurt, but it’s not a requirement.
[21:42] <james> I see. I’m actually reading Learn Ruby the Hard Way, and I already finished the Try Ruby in 15 minutes by codeschool.
[21:42] <saqib> But guys i believe before jumping into any framework we should have a little knowledge about programming language which framework is using
[21:42] <kinduff> Glenn +1
[21:42] <HAWK> joe – have you checked the schedule of upcoming sessions? Next week’s content one might be more up your alley if you’re a complete beginner.
[21:42] <james> I don’t want to rush anything but I just thought that I might be wasting some time
[21:42] <kinduff> saqib that’s when the learning curve gets tricky
[21:43] <molona> To be honest, wtih so many frameworks out there and all of them are promoted as the next big thing, I find hard to see why I shoud choose RoR instead of any other (Ruby based or not)
[21:43] <kinduff> james take the course from codeacademy, the one from ruby, it’s a good way to start with that
[21:43] <lj> I would love to have some simple projects to build with Ruby to practice
[21:43] <Andy> @james no, you won’t be wasting your time. It’s possible to get up and running with Rails quite quickly. Especially if you’ve sone some web dev stuff already
[21:43] <RanjitSingh> james, you need to learn “the patience” sir
[21:43] <saqib> But one thing which bothers me alot which is Rails magic
[21:44] <joe> @ Hawk, no i have not
[21:44] <saqib> things are too much abstract ..
[21:44] <Andy> @james *done some web dev
[21:44] <joe> I will be glad to join
[21:44] <kinduff> saqib that magic is for the regular user, but once you learn this “magic” ror has, it gets pretty fun
[21:44] <HAWK> joe – if you refresh this page and leave the session, you’ll see the schedule. :) Then you can rejoin.
[21:44] <Glenn> @molina, if you like Ruby and need a web app, Rails is a good choice. Sinatra is another. I do not believe Rails is the only game in town, it’s just a good one.
[21:45] <saqib> kinduff if you want to change something .. it took a while to manipulate things according to need
[21:45] <james> so when I can already say that I’m confident in the basic of Ruby, I could just jump into RoR 4?
[21:45] <Glenn> saqib Rails magic is good when you’re learning, I think.
[21:45] <Andy> For those debating whether to start with Rails, I started with Sinatra projects first (for developing with Ruby) and it helped
[21:45] <Glenn> james Sure.
[21:46] <james> I had a short time experience with PHP btw
[21:46] <molona> Well I played with Sinatra a bit and some RoR but with my level of knowledge (or should I say ignorance) it is hard to see the advantages of one over the other depending on the project.
[21:46] <saqib> james have you tried Laravel 4 php framework?
[21:46] <kinduff> saqib Glenn it is, but It’s pretty hard once you want to think out of the box
[21:46] <Glenn> I think it’s important to point out that Rails is based on a “convention over configuration” If you like Rails conventions (routing, mvc, etc) then you can do great things with it.
[21:47] <james> saqib Not yet. I’m still confused on my field right now actually.
[21:47] <Andy> Glenn just nailed it.
[21:47] <kinduff> there you go
[21:47] <joe> @ Hawk i just did
[21:47] <HAWK> Cool :)
[21:47] <saqib> Glenn kinduff what do you think about backward compatibility or Rails?
[21:47] <Glenn> @moina Sinatra is much closer to the metal. Rails has “more magic”
[21:48] <kinduff> Rails -> automagical
[21:48] <HAWK> Glenn – what are the main differences in Rails 4 (over the previous versions)?
[21:48] <Glenn> saqib Backward compatibility is a noble aim. However, in some cases it’s impossible or impractical. The jump to Rails 3 is a great example.
[21:49] <molona> @glenn with more magic, do you mean that development really faster (well, if you know it, of course) than if you use Sinatra?
[21:50] <Glenn> @molina Yes. You can get up and going much faster with your “standard” site
[21:50] <Glenn> HAWK We have a great article on Rubysource about that [https://www.sitepoint.com/get-your-app-ready-for-rails-4/](https://www.sitepoint.com/get-your-app-ready-for-rails-4/)
[21:52] <HAWK> Glenn – cheers, I’ll have a read
[21:53] <lj> I like the fact that I could build it modularly with Rails…and make global changes
[21:54] <HAWK> Andy – you wrote Jump Start Rails with no real previous experience of Rails, yeah? What was the hardest bit to get your head around?
[21:54] <Glenn> lj That leads to a great point that a good architecture is more important than what framework you choose. You can be modular with Rails, or you can write a ball of mud.
[21:54] <lj> since I am actually generating the pages each time I make changes?
[21:55] <Andy> HAWK yes, that’s true. I guess all the “conventions” was the trickiest part.
[21:55] <kinduff> I heard jumping from Rails 2.3 to 3.0 was kinda hard, how is the jump from 3.0 to 4.0?
[21:56] <Andy> HAWK coming from spending a long time developing with PHP, Rails frustrated me at first. It kept getting in the way…
[21:56] <Glenn> @kinduff, I don’t think it’s as bad. There is a great Railscast on it [http://railscasts.com/episodes/415-upgrading-to-rails-4](http://railscasts.com/episodes/415-upgrading-to-rails-4)
[21:57] <Andy> HAWK now I an say that working with PHP frameworks, it’s those that get in the way. Rails by comparison, is… there when you need it
[21:57] <molona> @andy Not sure that I understand what you mean
[21:57] <HAWK> Yeah, I’ve heard that from many people that make the jump, Andy
[21:57] <Glenn> kinduff you can grab gems to bridge the gap to rails 4 and fix code as you have time. Both the links I posted in this chat talk about that.
[21:58] <kinduff> Glenn thanks for the info, time to migrate some stuff
[21:58] <Glenn> kinduff No worries! Good luck!
[21:58] <Andy> HAWK molona to be clear, when building a web app you are designing a solution to a problem. You want tools that help you do that, not ones that need a lot of attention to make them work…
[21:59] <molona> @andy that’s much clearer, thank you
[22:00] <HAWK> We have a gap. Does anyone have a question that hasn’t been answered yet?
[22:01] <Glenn> Is anyone currently working on a Rails app? Any pain points?
[22:02] <molona> Well, I wonder if there’s a type of project I should start with
[22:02] <lj> Is Heroku the best way to host the app?
[22:02] <james> yeah and to add to that. What if I’m just learning Rails and don’t have any project. What can I do to practice the things I learn? I mean just do the usual way like blog, etc?
[22:03] <Glenn> lj Heroku is great, for sure. It has some constraints, though, so you need to understand what those are.
[22:03] <kinduff> lj I love Heroku but it’s not the best
[22:03] <Andy> lj it’s a great place start with.
[22:03] <lj> It is slow though if the site doesnt get continual traffic to keep it awake
[22:04] <Andy> james re-build your own site using Rails maybe?
[22:04] <MalCurtis> @lj I have a cron job on my personal hosting that just pings all my little apps every hour
[22:04] <MalCurtis> That way they all stay alive on the free teir :D
[22:04] <Andy> @lj Pingdom can fix that problem
[22:04] <Glenn> james Pick a site and reproduce it in Rails. Find some good Ruby resources and go through their tuts/articles. I would like to suggest [https://www.sitepoint.com](https://www.sitepoint.com) of course :)
[22:04] <lj> MalCurtis from Heroku?
[22:04] <MalCurtis> I have hosting with MediaTemple that allows me to set up cron jobs
[22:05] <MalCurtis> so I run ‘wget [http://cspisawesome.com’](http://cspisawesome.com') every hour
[22:05] <MalCurtis> that’s on of my heroku free apps
[22:05] <MalCurtis> I beleive the timeout is every hour
[22:05] <MalCurtis> But Glenn’s idea of Pingdom is just as good
[22:05] <MalCurtis> they test for your website being up
[22:05] <MalCurtis> so it will receive traffic
[22:05] <Glenn> lj I think if you sign up for the free version of the New Relic add-on, it will keep your site up.
[22:06] <Glenn> MalCurtis Pingdom credit goes to Andy :)
[22:06] <lj> I tried new relic that does this…not sure yet if its working
[22:06] <MalCurtis> Oops, sorry Andy
[22:06] <HAWK> Seeya kinduff – thanks for joining us
[22:07] <lj> With MT do you use rsync?
[22:07] <Glenn> @lj [https://coderwall.com/p/u0x3nw](https://coderwall.com/p/u0x3nw)
[22:07] <lj> comment was for MalCurtis
[22:08] <MalCurtis> wget
[22:09] <MalCurtis> it just retrieves the url I give it
[22:09] <lj> Good idea to try the magazine in rails…thanks for the inspiration and push
[22:09] <lj> MalCurtis but I mean just to upload your work from you r local machine…rsync?
[22:10] <lj> I would hate to have to make a change to 300 pages one at a time
[22:10] <MalCurtis> Oh, I put up a blog on my personal website years ago and haven’t really changed anything since
[22:10] <MalCurtis> I still ftp to it if I need to make changes, which I never do :D
[22:11] <MalCurtis> It’s wordpress so most content is dynamic
[22:11] <lj> so you arent blogging on it anymore?
[22:11] <lj> Oh ok so it isnt rails
[22:11] <MalCurtis> Yea
[22:11] <MalCurtis> I did it before I became a rails guy
[22:12] <lj> I tried an Octopress blog which have me a little ruby practice
[22:12] <MalCurtis> Nice, yea – if I were to do anything these days it would be managed with GIt
[22:13] <lj> I had trouble managing the github pages so I used rsync with my host Site5
[22:13] <lj> worked very well
[22:15] <molona> MalCurtis Did you ever build another one in RoR?
[22:15] <MalCurtis> A blog?
[22:15] <molona> yep
[22:15] <MalCurtis> Nope. Not really interested
[22:15] <MalCurtis> There’s so many great static site generators out there
[22:16] <lj> OK so then what would you use rails for?
[22:16] <lj> for example
[22:16] <MalCurtis> I wrote learnable.com
[22:16] <MalCurtis> Which is a rather large rails app
[22:16] <lj> in rails? right?
[22:16] <lj> yes great job
[22:16] <MalCurtis> Yea, initially it was PHP, Rails and some Python
[22:17] <lj> amazing
[22:17] <MalCurtis> now it’s just Rails
[22:17] <MalCurtis> I actually don’t work on it any more
[22:17] <MalCurtis> The Sitepoint devs take care of it
[22:17] <lj> but does it make sense to use it for a smaller project when as you say “there are so many…”
[22:17] <MalCurtis> Dynamic content = use rails
[22:17] <MalCurtis> Static content = maybe use rails if you want practice, but probably no need
[22:18] <HAWK> Does anyone have questions for our experts? Glenn and Andy are probably going to sleep? ;)
[22:18] <Glenn> If I have a static or nearly static content app, I like to start with Sinatra.
[22:18] <molona> When did you decide to make learnable an only Rails app
[22:19] <MalCurtis> It was a company wide decision to move away from PHP, and Learnable was already half written
[22:19] <MalCurtis> So as things needed updating, we rewrote them into rails
[22:19] <MalCurtis> Flippa have done the same thing
[22:19] <HAWK> Welcome to those of you that have just joined. If you have a question, please jump in
[22:19] <MalCurtis> as they make updates / add new features, they get written in Rails
[22:19] <MalCurtis> I think they’re nearly all Rails now
[22:19] <HAWK> I’ll post the full transcript up on SitePoint later today so you can see what you’ve missed
[22:19] <lj> Glenn I wonder how you would “start” the project with Sinatra…how you would proceed
[22:20] <lj> I know that’s a big question
[22:21] <Glenn> lj Well, Sinatra is really just a file if you use the inline views feature. Makes it nice to get going if I don’t need complicated auth, css/asset handling, etc.
[22:21] <eip56> Suggestions for picking up Rails? I am coming from a strong PHP background.
[22:21] <Glenn> lj if the app starts to get too big and you’ve written your business logic in a somewhat modular way, crank up a Rails app and pull it over.
[22:22] <Andy> eip56 build something using Rails. It sounds like a simple answer I know. But it is the best way.
[22:23] <Glenn> eip56 Andy’s book is aimed somewhat at people making the jump [https://learnable.com/books/jump-start-rails](https://learnable.com/books/jump-start-rails)
[22:23] <eip56> Andy. Always a good way :-). We will follow up with a more specific way. What are your favorite resource books/ sites
[22:24] <eip56> Awesome thank you
[22:24] <eip56> andy I look forward to reading your book
[22:24] <HAWK> Aren’t there quite a few articles on RubySource on that subject as well Glenn?
[22:24] <Andy> [https://www.sitepoint.com/](https://www.sitepoint.com/) ;-)
[22:24] <HAWK> Hehe
[22:24] <HAWK> Ok everyone – there is 5 mins remaining in the session. Speak now or forever hold your peace.
[22:24] <Glenn> HAWK Why, yes, I do believe there are some Rails articles over there.
[22:25] <Andy> eip56 cheers! It’ll get you started anyway.
[22:25] <HAWK> :p I meant specifically pertaining to making the switch from PHP, Glenn
[22:26] <jamesMccoy> why would you favour Rails over, say, Python with Flask?
[22:26] <Glenn> oooooh…right. eip56 Here’s one on PHP to Sinatra [https://www.sitepoint.com/php-to-sinatra/](https://www.sitepoint.com/php-to-sinatra/)
[22:26] <eip56> In your view what does Rails have over PHP project wise I guess. What would make you choose one over the other?
[22:26] <Andy> HAWK I found that in the end, it was best to forget all about PHP…
[22:27] <molona> @andy so either you program with Rails or with PHP but not with both? lol
[22:27] <HAWK> I’m yet to meet a ex-PHP dev that made the switch and DID look back, Andy
[22:27] <Glenn> jamesMccoy I would favor it b/c I don’t really know Python :P
[22:28] <Glenn> jamesMccoy But, at that point it’s more of a language preference issue, as opposed to a Rails vs Flask issue
[22:28] <jamesMccoy> I’d have thought it was a performance issue,
[22:28] <Andy> eip56 Rails does the full job when it comes to web apps. PHP was never originally intended to be stuffed into the MVC pattern…
[22:28] <jamesMccoy> rails has a massive overhead in comparison to flask/python
[22:28] <jamesMccoy> I was wondering if the rails experts could give some insight
[22:29] <jamesMccoy> from their perspectives (I am a Python man, if you couldn’t tell…)
[22:29] <HAWK> Andy and Glenn ARE the Rails experts, jamesMccoy :)
[22:29] <Glenn> jamesMccoy I don’t know flask, sorry. Rails definitely has some overhead, it’s the price you pay for so much convention.
[22:29] <jamesMccoy> Andy / Glenn, what commercial apps have you written in Ruby?
[22:29] <jamesMccoy> specifically, rails?
[22:29] <Andy> jamesMccoy flask/Python compares with Ruby/Sinatra. Rails is comparable with Django
[22:29] <Glenn> jamesMccoy if you like what those conventions give you, and I do, then it’s great.
[22:30] <Glenn> I spent much of the past year or so working on [http://social.kyck.com](http://social.kyck.com)
[22:31] <jamesMccoy> hey that looks cool. and Andy?
[22:31] <Glenn> We are currently working on another large commercial Rails app, but its still cooking.
[22:31] <HAWK> Tease
[22:31] <Andy> jamesMccoy I’m currently re-building a corporate intranet.
[22:31] <lj> Can you give an example of the conventions and what they give you?
[22:32] <HAWK> Anyway guys, that’s technically a wrap. Feel free to stick around for as long as you like, but I’m cutting our experts free when they’re ready.
[22:32] <redmuB> i think i am in the wrong chat
[22:32] <HAWK> And I’d like to say a HUGE thanks for them both for their time
[22:32] <jamesMccoy> so where did you gain your experience for authoring your book, if not from engineering?
[22:32] <molona> @glenn, cool… I guess it uses Geotargetting because the page displayed in Spanish for me
[22:32] <eip56> Hey the few minutes as always have been a pleasure @ HAWk Andy and Glenn
[22:32] <HAWK> What were you looking for redmuB?
[22:32] <jamesMccoy> thanks to all!
[22:33] <Glenn> lj Sure. Real quick (it’s dinner time :)) Routing DSL, mapping of url to controller action to view, asset handling (js and css, minified, etc)
[22:33] <molona> Thanks to both :D
[22:33] <Andy> jamesMccoy from building my own site: andyhawthorne.herokuapp.com, it’s what made me think about the book in the first place.
[22:33] <lj> Sounds good Glenn Thanks
[22:33] <Glenn> ActiveRecord gives a mapper to your RDMBS tables. Rails mitigates CSRF attacks. There’s loads more.
[22:34] <lj> That is my main problem now is routing..suppose I had to change all those routes in all those pages
[22:34] <Glenn> @molina, Rails has a cool i18n convention too :)
[22:34] <molona> @Glenn Cool. Thanks :)
[22:34] <Glenn> Soooo, gotta run folks. Thanks for dropping by. I hope it was fun/useful.
[22:35] <jamesMccoy> don’t you ever feel with Rails that you’re not so much engineering as just sticking blocks together?
[22:35] <jamesMccoy> genuine question — I have built a few apps using the platform, I didn’t feel like I was in control
[22:35] <jamesMccoy> Glenn calls it convention, is it necessarily a good thing?
[22:35] <RanjitSingh> Anyone need a taxi?

## 分享这篇文章