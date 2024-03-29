---
layout: post
title: "TFS Build output locations of Azure-hosted web apps"
---

<p>If you're hosting a web application as a Web Role in an Azure Cloud Service Visual Studio project, then you will see some unusual behavior with the build output if you are performing builds on a TFS build server.</p>
  
<p>Normally, ASP.NET web applications are output to a folder named <em>_PublishedWebsites</em> on the build server after a successful build.  However, if your Visual Studio solution is using an Azure Cloud Service project to host the web application as a web role, then you will find that the _PublishedWebsites folder does not exist in the build output folder on the TFS build server after a successful build.</p>
  
<p>Instead, the ASP.NET web application content is found within the Cloud Service output folder (it has a .csx suffix) on the build server:</p>
  
<div style="margin-left: 20px">   
<p>Normal output without Cloud Service:      <br />[out dir]\_PublishedWebsites\yourwebapp</p>
    
<p>With Cloud Service:      <br />[out dir]\CloudService.csx\roles\yourwebapp\</p>
</div>  
<p>If you would still like your web app content to be copied to _PublishedWebsites, simply create a 2nd Visual Studio solution that hosts your ASP.NET web application but do not add the Cloud Service project.  Then create a new TFS Build that targets the new solution and it will be built without the Cloud Service - and your web application content will be copied as expected to the _PublishedWebsites folder.</p>
  
<p>You could also hack some MSBuild targets to prevent the _PublishedWebsites folder from being deleted.  I personally prefer staying out of .target files, but there's no reason to not try that option as well.</p>
  
<div id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:7d6a4793-d835-46a5-b652-53b3bc25e361" class="tags">Tags: <a href="http://technorati.com/tags/azure" rel="tag">azure</a>,<a href="http://technorati.com/tags/web" rel="tag">web</a>,<a href="http://technorati.com/tags/code" rel="tag">code</a>,<a href="http://technorati.com/tags/tfs" rel="tag">tfs</a></div> 
