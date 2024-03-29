---
layout: post
title: "Mike&#39;s New Music Library"
---

<p>This has been a long time in the works, but I now have a significantly improved music library: <a href="http://www.kindohm.com/MusicLibrary">http://www.kindohm.com/MusicLibrary</a>. My old "library" was basically a bunch of static content. The new library offers:</p>
<ul> 
<li>Tags. Yeah, I got trendy and implemented tags. Tracks might be tagged "heavy" or "weird" so you can listen to all my "heavy" and "weird" stuff.  </li>
<li>Simple track searching. Search by tag or by title.  </li>
<li>Track and album browsing. Get your mouse on and click click click away.  </li>
<li>Dynamic playlist generation. After performing a search or while viewing an album, you can download or start playing an m3u playlist of the songs you're viewing - all dynamically generated in an instant.  </li>
<li>Song and album history/notes. Each album and track has a notes section so you can get the details on what the hell I was thinking when I came up with such crap.  </li>
<li>A pretty user interface. </li></ul> 
<p>The folks at WebHost4Life.com (my web host) were so very nice to me and hooked up a special IIS application mapping for the m3u file extension. I wasn't sure they'd actually do it, but they pulled through and allowed me to provide the feature. </p>
<p>Notes and history on all the albums and tracks has barely even started. There are about 250 songs total - I don't intend to add notes to everything but there's a lot to write about.</p>
<p>I've also got a rough start to the tags. There's a good start there but it'll change as time goes on.</p>
<p>The library is a custom SQL/ASP .Net app that I whipped up quickly. The focus was on quick development and a high-performance site. I put little effort into the domain model code because honestly I really didn't care about it. I was more interested in creating a fast site with a few cool user features - and finally creating an improved music library for my stuff. I don't have a very nice object model to work with, but I don't need one anyway. When the day comes that I need to extend my app to do crazy stuff, I'll consider improving it.</p>
<p>Hope you like it... and I hope it doesn't break :)</p>
 
