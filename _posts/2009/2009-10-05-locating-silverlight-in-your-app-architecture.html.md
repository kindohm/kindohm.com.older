---
layout: post
title: "Locating Silverlight in your App Architecture"
---

<p>I've been seeing many variants these days in high-level architecture diagrams concerning where Silverlight apps are placed.  Often they look something like this:</p>
  
<p><a href="http://www.kindohm.com/LocalImages/posts/LocatingSilverlightinyourAppArchitecture_735F/NotBetter2.png"><img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="NotBetter2" border="0" alt="NotBetter2" src="http://www.kindohm.com/LocalImages/posts/LocatingSilverlightinyourAppArchitecture_735F/NotBetter2_thumb.png" width="423" height="189" /></a> </p>
  
<p>Now, this is just a picture.  As they say in football, you can look at lineups and stats all you want, but football isn't played on paper, it's played on the field (or as I believe Don Cherry said of hockey, it's not played on paper - it's played on television sets [1]).  Similarly, the picture above is just a picture.  Anyone writing a Silverlight app <strong><em>knows</em></strong> that it does not execute on the web server [2].  When you make an async web client call from Silverlight to some service to get data, <strong>it goes through the internet over HTTP</strong>.  Silverlight apps are written in code, not in an architecture diagram.</p>
  
<p>However, the above diagram is invalid and should be changed - despite how obvious it is that a dev will not write Silverlight code to run on the web server.</p>
  
<p>You may say, "but your diagram says 'UI Tier' and Silverlight is definitely a part of the UI".  That may be true - but then why are the Browser and Internet cloud in the diagram at all?  In that case you're making the Browser just a dummy terminal, and the Internet is... well...  just a bunch of H<sub>2</sub>0 in a gas state.    Still, the diagram above is invalid as soon as the Silverlight app has to do anything useful and get data.</p>
  
<p>The diagram becomes even more obscene (yes, obscene) when you consider that Silverlight could run in its "sandbox mode" <em>outside of a Browser. </em>Silverlight really is a client app running on the desktop (by way of the Browser, usually).  Would you put a Windows WPF app in the Web Server box?</p>
  
<p><a href="http://www.kindohm.com/LocalImages/posts/LocatingSilverlightinyourAppArchitecture_735F/wpf.png"><img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="wpf" border="0" alt="wpf" src="http://www.kindohm.com/LocalImages/posts/LocatingSilverlightinyourAppArchitecture_735F/wpf_thumb.png" width="427" height="201" /></a> </p>
  
<p>A much better diagram would be to put Silverlight where it is actually executing: in the Browser.</p>
  
<p><a href="http://www.kindohm.com/LocalImages/posts/LocatingSilverlightinyourAppArchitecture_735F/Better.png"><img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="Better" border="0" alt="Better" src="http://www.kindohm.com/LocalImages/posts/LocatingSilverlightinyourAppArchitecture_735F/Better_thumb.png" width="472" height="188" /></a> </p>
  
<p>Now things actually make sense.  It is clear where the physical and trust boundaries are.  Plus, it's just the truth.  I've extended the diagram to include other code executing in the Browser, such as Javascript.  </p>
  
<p>I'm not a big architecture guy, but it makes sense to do enough architecture to make sure your project team all agrees on how you will write code.  Misplacing a key part of the architecture could result in confusion for junior developers and an absolute mess among more experienced developers who may not challenge the architecture.  In any case, make sure that Silverlight app is on the Browser side of the picture.</p>
  
<p>[1] I couldn't actually find the Don Cherry quote to reference.</p>
  
<p>[2] I'm sure there are devs out there who don't know that Silverlight is <em>not</em> executing on the web server, and for that I am truly scared.  I hope you're reading this, unknowing Silverlight dev!</p>
  
<div id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:8d7dfcac-6c63-4cc4-bce1-ab01f5720dd7" class="tags">Tags: <a href="http://technorati.com/tags/silverlight" rel="tag">silverlight</a>,<a href="http://technorati.com/tags/architecture" rel="tag">architecture</a></div> 
