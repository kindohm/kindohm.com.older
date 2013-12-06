---
layout: post
title: "Visual Studio 2005 on Vista"
---

<p>I recently installed Windows Vista on my new work laptop, and have been having trouble getting Visual Studio 2005 Team Edition with the Team Foundation client to work correctly.  Without installing some required service packs and updates, I encountered Package Load Failure errors - or VS 2005 decided to not work at all.  </p>
  
<p>Here's a list of steps you need to take to get VS 2005 with the Team Foundation client working on Vista:</p>
  
<ol>   
<li>Install VS 2005</li>   
<li>Install VS 2005 SP1: <a href="http://www.microsoft.com/downloads/details.aspx?familyid=bb4a75ab-e2d4-4c96-b39d-37baf6b5b1dc" target="_blank">download</a></li>   
<li>Install VS 2005 SP1 Update for Vista: <a href="http://www.microsoft.com/downloads/details.aspx?familyid=fb6bb56a-10b7-4c05-b81c-5863284503cf" target="_blank">download</a></li>   
<li>Install the Team Foundation client</li>   
<li>Install the VS 2005 SDK: <a href="http://www.microsoft.com/downloads/details.aspx?FamilyID=7e0fdd66-698a-4e6a-b373-bd0642847ab7&amp;DisplayLang=en" target="_blank">download</a></li> 
</ol>  
<p>In my case, I missed the last step, and as a result I had problems loading the Source Control "package" part of the Team Foundation client.  It's weird (and annoying) that the TFC would have a dependency on the SDK, but so be it.  Follow these steps and you should be on your way to VS on Vista wonderfulness.</p>
  
<p class="tags">tags: <a href="http://technorati.com/tag/visualstudio" target="_blank" rel="tag">visualstudio</a> <a href="http://technorati.com/tag/vista" target="_blank" rel="tag">vista</a> <a href="http://technorati.com/tag/code" target="_blank" rel="tag">code</a> <a href="http://technorati.com/tag/programming" target="_blank" rel="tag">programming</a> <a href="http://technorati.com/tag/.net" target="_blank" rel="tag">.net</a> <a href="http://technorati.com/tag/configuration" target="_blank" rel="tag">configuration</a>  </p>
 
