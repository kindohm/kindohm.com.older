---
layout: post
title: "New Online Music Library - HodsAudio.net"
---

<p>For those of you familiar with me and/or my web site, you probably know that I've hosted all of my own music that I've recorded over the years on kindohm.com.  Things are changing for a few reasons:</p>
  
<ol>   
<li>Coming out of the closet with artist names </li>    
<li>The old app needed a technology overhaul </li> 
</ol>  
<p>If you just want to go check out the new site, feel free: <a href="http://HodsAudio.net">http://HodsAudio.net</a>.  But if you want to learn more about my motivation for creating the new site, read on.</p>
  
<p>So first, what is this "coming out of the closet" business about?  I've been semi-secretly maintaining an alternate identity as another band named The Skill Druids.  Right now, you can check out <a href="http://www.skilldruids.net">http://www.skilldruids.net</a> and see what that is all about.  In short, I've grown tired of maintaining this Skill Druids alternate reality.  What's sad is that I barely even did much with that alternate reality.  </p>
  
<p>I had big plans for The Skill Druids: a phony band, phony web site, band bio and back story, blog, viral internet marketing campaign, Twitter account (twitter.com/skilldruids), MySpace page, Facebook page, etc, etc, etc.  I pulled off a few of those things, but it was hard to keep anything current because of my <em>real</em> life.  </p>
  
<p>The positive side is that I actually succeeded in producing one album, The Drow, by the Skill Druids.  I'll let skilldruids.net and hodsaudio.net speak for that work, but I do like how it turned out.  I manufactured a few CD's, complete with artwork, by hand.  I think I have a few left if anybody wants one...  </p>
  
<p>In any case, the "fake" Skill Druids is no more.  skilldruids.net will die when the domain name expires.</p>
  
<p>There are a few people out there who I <strong><em>know</em></strong> want to collaborate with me musically. For keeping the Skill Druids a secret and working selfishly on my own music - I am sort of a little kind of sorry.  Sort of.  Going forward I see musical collaboration at this point in my life as being a challenge anyway.  I had big plans to get a lot of people involved with playing instruments on The Drow, but it got to the point where it was going to take a lot more time to accomplish and I was starting to experience some project fatigue.</p>
  
<p>There is also a mix of electronic and rock music I've done under the "Kindohm" moniker in the past.  These days I'm doing quite a bit of electronic composition, and I wasn't happy with spreading the Kindohm name so thin with everything from grungy rock to 200 BPM breakcore.  It was difficult navigating through all the Kindohm stuff even without introducing the Skill Druids to the mix.</p>
  
<p>With the launch of HodsAudio.net, I'm also launching a new identity for my electronic endeavors: Parser.  In fact, the first Parser compilation, <em>Robots Stole My Computer</em>, can be viewed here: <a title="http://hodsaudio.net/Album/Details/12" href="http://hodsaudio.net/Album/Details/12">http://hodsaudio.net/Album/Details/12</a>.  It's a small set of the best electronic stuff I've put out to date.  I'm not planning on making a physical CD.</p>
  
<p>So I have Kindohm, The Skill Druids, and Parser.  Is this getting more confusing?  Hopefully not.  </p>
  
<ul>   
<li>Kindohm - all my old stuff </li>    
<li>The Skill Druids - any new rock music I do from this day forward </li>    
<li>Parser - any new electronic music I do from this day forward </li> </ul>  
<p>Next, what about this "technology overhaul"?  My old music library app couldn't support three "artists".  It would have needed some big feature upgrades to be able to re-categorize all of the mp3's on my site.  The old app was also created with some database and programming conventions I wouldn't use now for this type of an app (ASP.NET web forms and GUIDS for unique identifiers are two examples).  </p>
  
<p>I decided to just re-write the app and migrate data from old database tables to the new ones.  I wrote the app in small chunks of time over a period of two weeks.  It uses ASP.NET MVC 1.0, jQuery, and has a brand new database table schema.  I thought it was funny when <a href="http://www.thoughtstoblog.com" target="_blank">Jake</a> told me earlier today, "welcome to 2004 with MVC on the web".  I had no comeback.</p>
  
<p>I'm actually using LINQ-to-SQL for data access - and before any l337 programming geeks laugh about that:</p>
  
<ol>   
<li>It's a very simple database schema (Artist -&gt; Album -&gt; Song) </li>    
<li>It's a small amount of data (the Song table is a few hundred rows) </li>    
<li>If you really, honestly think the site is slow then too bad for you </li>    
<li>I have a day job, 4-yr old, and two dozen other hobbies calling my name and wanted to get this done as quickly as possible </li>    
<li>We're not talking about an enterprise app here, nor a site that gets <strong><em>that</em></strong> much traffic. </li> 
</ol>  
<p>I'm utilizing <a href="http://mediaplayer.yahoo.com/" target="_blank">Yahoo's Media Player</a> to allow music playback directly on the site.  Yahoo's player has been good to me.  I considered some Silverlight-based players but they just aren't as simple and dynamic as Yahoo's.  </p>
  
<p>I also considered implementing my music library 100% in Silverlight as a "RIA app".  What a bad idea that would have been.  I wouldn't want to put that kind of pain on users by forcing them to download a plugin when all I want them to do is quickly hear my music.  There are also users out there (some good friends of mine, as a matter of fact) with older operating systems that aren't supported under Silverlight yet.  Most of all, developing a RIA app just would have taken more time.</p>
  
<p>So, that's the story.  In closing, I want to address an issue that I know most of you are hugely concerned about.  Simply stated: Get On Your Knees and Bark Like a Dog is included in the music library:</p>
  
<p><a title="http://hodsaudio.net/Song/Details/204" href="http://hodsaudio.net/Song/Details/204">http://hodsaudio.net/Song/Details/204</a></p>
  
<p>Enjoy.</p>
  
<div id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:b83350e4-844f-480a-aaec-05f57ce8a8d5" class="tags">Tags: <a href="http://technorati.com/tags/music" rel="tag">music</a>,<a href="http://technorati.com/tags/mvc" rel="tag">mvc</a>,<a href="http://technorati.com/tags/code" rel="tag">code</a>,<a href="http://technorati.com/tags/skilldruids" rel="tag">skilldruids</a>,<a href="http://technorati.com/tags/web" rel="tag">web</a></div> 
