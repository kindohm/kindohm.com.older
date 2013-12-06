---
layout: post
title: "Updated Silverlight Memory game code"
---

<p>Last night, I had Eva take the Memory game for a spin and she uncovered a ridiculous amount of bugs.  Amazing how a child uses software differently than an adult!  In addition, I quickly realized that the "random placement" of the cards on the screen made the game difficult to use.  While it was a cool feature from my perspective, the folks who tried out the app hated it.  Thus, the random placement was replaced by a standard grid of cards.  You can still move the cards around, though.  I also fixed the bugs that Eva found.</p>
  
<p>I also added a "low score" feature (score = how many match attempts you make in a game).  It keeps track of your all-time low score as you play the game - even across site/page visits.  It accomplishes this using .NET Isolated Storage, which to me is a super powerful feature considering this is an app running on the web.  I have <strong><em>not</em></strong> tested this feature on any operating system other than Vista.  I have tested it in IE and FireFox, and it works just fine (not surprisingly).  </p>
  
<p>Download the latest source code: <a href="http://kindohm.com/sl/memory/memorysource.zip">http://kindohm.com/sl/memory/memorysource.zip</a> (~600 KB)</p>
  
<p>Play the latest game: <a href="http://kindohm.com/sl/memory/memory.html">http://kindohm.com/sl/memory/memory.html</a></p>
  
<p><a href="http://www.kindohm.com/LocalImages/Posts/UpdatedSilverlightMemorygamecode_C4F3/Untitled.png"><img style="border-right: 0px; border-top: 0px; border-left: 0px; border-bottom: 0px" height="484" alt="Untitled" src="http://www.kindohm.com/LocalImages/Posts/UpdatedSilverlightMemorygamecode_C4F3/Untitled_thumb.png" width="571" border="0" /></a> </p>
  
<div class="tags" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:780fe846-64a9-4346-801b-2367feee9cb4">Technorati Tags: <a href="http://technorati.com/tags/silverlight" rel="tag">silverlight</a>,<a href="http://technorati.com/tags/code" rel="tag">code</a>,<a href="http://technorati.com/tags/programming" rel="tag">programming</a>,<a href="http://technorati.com/tags/game" rel="tag">game</a></div> 
