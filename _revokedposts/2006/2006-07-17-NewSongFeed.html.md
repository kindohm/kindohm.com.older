---
layout: post
title: "New Song Feed"
---

<p>I added a <a href="http://www.kindohm.com/MusicLibrary/SongFeed.aspx">new rss song feed</a> to my <a href="http://www.kindohm.com/musiclibrary">music library</a> tonight. Instead of blogging about every little experimental tune that I upload, now you can find out about them for yourselves, you greedy readers you. </p>

<p><a href="http://www.kindohm.com/MusicLibrary/SongFeed.aspx">http://www.kindohm.com/MusicLibrary/SongFeed.aspx</a></p>

<p>The only catch was that I needed to modify my domain model to include a "date created" field on songs. How do you assign a new date created value to 250 existing tracks? </p>

<pre><code>UPDATE my_secret_table SET DateCreated = GetDate()</code></pre>
<p>Yes, unfortunately each track had to get its date created value set equal to each other's. However, I bumped up a few of the good tunes to have newer dates so those will show up first in the feed. </p>

<p>Let me know if you encounter any problems with the feed.</p>

 
