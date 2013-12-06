---
layout: post
title: "Radial Network Graph in Silverlight"
---

<p>Download Source Code: <a href="http://www.kindohm.com/files/SilverlightRadialGraph.zip">SilverlightRadialGraph.zip [582 KB]</a> </p>
  
<p>Recently I've been looking into some challenges with data visualization - particularly with viewing relationships between a large number of entities.  Social Networking is a classic example.  A great example of how to visualize a large network of "friends" on <a href="http://www.facebook.com" target="_blank">Facebook</a> is to use the <a href="http://nexus.ludios.net/" target="_blank">Nexus application</a> (developed by Ivan Kozik):</p>
  
<p><a href="http://www.kindohm.com/localimages/posts/RadialNetworkGraphinSilverlight_14C67/5a00e7ab212ddac63439e4af10aaaa69.dark.simple.382.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="244" alt="5a00e7ab212ddac63439e4af10aaaa69.dark.simple.382" src="http://www.kindohm.com/localimages/posts/RadialNetworkGraphinSilverlight_14C67/5a00e7ab212ddac63439e4af10aaaa69.dark.simple.382_thumb.png" width="224" border="0" /></a>     <br /><em>Click to enlarge</em></p>
  
<p>This is very close to what I know as a "radial graph".  I couldn't find a good definition of a radial graph anywhere, but here is an academic publication that at least describes the concept: <a title="http://bailando.sims.berkeley.edu/papers/infovis01.htm" href="http://bailando.sims.berkeley.edu/papers/infovis01.htm">http://bailando.sims.berkeley.edu/papers/infovis01.htm</a></p>
  
<p>I decided to try and implement this type of radial graph using Silverlight.  Given a set of entities that are connected to each other, I figured it should be easy enough to come up with a basic algorithm to put this on a screen.</p>
  
<p>The general idea was to start with an initial entity and then compute and render its first "ring" of connections.  Then for each entity in the first ring, find <em>its</em> connections and create a second ring.  Entities can only be added to the graph once, so if an entity in the first ring is a connection of an entity in a fourth ring, the entity in the first ring is <em>not</em> added to a fifth ring.  </p>
  
<p>I used Silverlight user controls to represent an entity on the screen.  Each user control is placed using basic trigonometry computation and an constant radian increment on each ring for the spacing along the ring.  "Basic" trigonometry computation may be a contradiction in terms, but that's a different topic :)</p>
  
<p>Here's what it looks like:</p>
  
<p><a href="http://www.kindohm.com/localimages/posts/RadialNetworkGraphinSilverlight_14C67/silverlight2.png"><img style="border-right: 0px; border-top: 0px; border-left: 0px; border-bottom: 0px" height="484" alt="silverlight2" src="http://www.kindohm.com/localimages/posts/RadialNetworkGraphinSilverlight_14C67/silverlight2_thumb.png" width="535" border="0" /></a> </p>
  
<p>The direct connections between each entity are drawn with a line.  When there are a lot of connections, it can be difficult to read or becomes a distraction.  There is a bit of eye candy in there though to help make the connection visualization easier: when you hover over an entity its direct connections are highlighted.  You can see this by looking at the selected (yellow) entity and its connections highlighted in green.</p>
  
<p>The model relies on a central entity to start with (highlighted in blue).  When you click on another entity, the screen re-draws and the graph re-builds around the selected entity.</p>
  
<p>I know you're probably looking for code, but I need to clean up some .NET namespaces first in order to protect the innocent :)  Stay tuned for an update...</p>
  
<p><strong><font color="#ff0000">--UPDATE--</font></strong></p>
  
<p>Here is the source code as promised:</p>
  
<p>Download Source Code: <a href="http://www.kindohm.com/files/SilverlightRadialGraph.zip">SilverlightRadialGraph.zip [582 KB]</a> </p>
  
<div class="tags" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:8c811775-3fdb-4c7f-9ef2-cb706737475c">Technorati Tags: <a href="http://technorati.com/tags/silverlight" target="_blank" rel="tag">silverlight</a> <a href="http://technorati.com/tags/facebook" target="_blank" rel="tag">facebook</a> <a href="http://technorati.com/tags/programming" target="_blank" rel="tag">programming</a> <a href="http://technorati.com/tags/code" target="_blank" rel="tag">code</a></div> 
