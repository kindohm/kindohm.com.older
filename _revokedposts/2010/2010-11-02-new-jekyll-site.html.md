---
layout: post
title: "Linux, meet Kindohm.com"
---

<p><span style="float: right;margin: 10px;border: solid 1px #333;"> <img src="/hodsmedia/amoeba17.jpg" style="margin: 5px; width: 220px;" alt="amoeba"/></span> You are the amoeba.</p>
  
<p>My main web domains, kindohm.com and hodnick.com, are mere petri dishes on the internet for me to experiment with. This is sometimes unfortunate for you, the web site visitor and blog reader. Nonetheless, I go forward with my web experiments. Despite what it may look like, I care deeply about you, dear user. I care deeply.</p>
  
<p>I'll start with the most major change:</p>
  
<p><em><strong>I've moved from my Windows web host to my own virtual Ubuntu Linux server in  <a href="http://rackspacecloud.com">The Rackspace Cloud</a></strong></em>. <a href="http://kindohm.com">kindohm.com</a> now runs on Ubuntu Linux.</p>
   
<p>I couldn't stand <a href="http://webhost4life.com">webhost4life</a> any more.  Their performance, service, support, and features have deteriorated significantly since I signed up with them back in 2005. I wanted to move to a host that would be fast and had great features. Ideally, I also wanted more control over my web site. I've also been ready to take a deep and significant dive into new technology. Thus, a Linux virtual server host was a perfect option.</p>
  
<p>Being on my own Linux server with Apache has been a liberating experience so far. I have full control over my entire web server for $10 a month. I'll never look at Internet Information Server (IIS) the same way again - working with Apache has been surprisingly simple. I'm not saying IIS is  necessarily complex, but Apache has just been more intuitive to me so far.</p>
  
<p>This leads to the question: "Mike, what on earth did you do with all the ASP.NET apps you had on your old host"?  I actually only had one app running on ASP.NET - my "VS Developers" app. Sadly, I made the choice to say goodbye to that app. Nobody used it anyway. There are two other ASP.NET  services I was hosting (one for my g20 Silverlight quiz and one for my TANKS high scores). Those services have definitely broken. I made the choice to let them break because, again, those apps don't get much traffic. When I get the time and motivation, I'll get those services back up and running.</p>
  
<p>My Silverlight work is still hosted on kindohm.com. Apache is serving out my .xap files just fine. I may continue to do some more Silverlight work to be hosted here... we'll see. My eye is really on HTML5 though. My HTML work may still need services and data to pull from - and I'll lean on either my new Linux box (PHP, Rails, or Django), Azure (ASP.NET), or Google apps (Django) for those new services.</p>
  
<p>My hodsaudio.net web site has been rendered obsolete by my Bandcamp sites  (visit <a href="http://kindohm.com/music.html">kindohm.com/music.html</a> for those details).</p>
  
<p>Given my new control with Apache, I've chosen to host hodnick.com on my Linux box  (goodbye <a href="http://tumblr.com">Tumblr</a>) and redirect all hodnick.com urls to archived HTML files to my new kindohm.com web site. Google and blog readers won't even notice because of the graceful, permanent  redirects. Similarly, I've resurrected my old kindohm.com posts from my ASP.NET Subtext engine. The old Subtext .aspx urls are redirected to archived HTML posts. Everything falls under a consistent style.</p>
  
<p>This leads to the next question: "Mike, how are you going to author new blog posts on Linux"? The answer is <a href="http://github.com/mojombo/jekyll">Jekyll</a>. Jekyll is a Ruby-based blog/site generating app that processes input posts in Markdown or HTML form, runs them through HTML templates, and generates a static HTML site. My site (including Markdown and HTML posts) is 100% under git source control and I just upload my new content  when it is ready. Jekyll also spins up a lightweight web server so I can preview my entire site before publishing.</p>
  
<p>At first, I struggled with this idea (generating and handling static files). However, being free from any application or runtime to host my web site means complete freedom. I'll never be married to any particular platform and I can pack up my site and go somewhere else at any time without worry. Plus, static HTML is blazing fast on Apache and through Rackspace's pipeline. I'll add that my web server is only running on 256 MB  of memory and it out-paces my webhost4life host by far.</p>
  
<p>It's been really fun getting this set up over the past 10 days. I think what has been most fun is getting out of my comfort zone of Windows and .NET. Moving to a foreign platform is a small gamble, but like I said before, this is just one big petri dish for me to experiment with - and you're the amoeba.</p>
   
