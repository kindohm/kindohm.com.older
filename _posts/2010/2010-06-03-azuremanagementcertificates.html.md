---
layout: post
title: "Azure Management API, &amp;quot;Internal Server Error&amp;quot;, and Powershell Azure Commandlets"
---


<p>If you are using the <a href="http://code.msdn.microsoft.com/azurecmdlets" target="_blank">Azure management commandlets for Powershell</a>, be careful to <em>correctly</em> create your certificates in Windows otherwise you will receive an unhelpful Internal Server Error:</p>
















































  <script src="http://gist.github.com/423826.js?file=badcert"></script>
<p>You will get this same error with csmanage.exe if you try to use the same certificate thumbprint.</p>
















































  
<p>It turns out that this error means that there is &#8220;something wrong&#8221; with your certificate. It probably wasn&#8217;t generated correctly. I&#8217;ve always used makecert.exe for my certs, but apparently there is something about makecert that Azure doesn&#8217;t like. I just don&#8217;t recommend using makecert.exe anymore.</p>
















































  
<p>Instead, use IIS7 to make yourself a certificate. IIS7 is just the starting point though.  There are quite a few hoops to jump through but it is a sure way of getting it to work. I haven&#8217;t found many great articles on how to go through this process so I figured I&#8217;d document them here.  Here is everything you need to do:</p>
















































  
<p>First, open up IIS7 and open up Server Certificates:</p>
















































  
<p><img src="/hodsmedia/659886406_1.png" alt=""/></p>
















































  
<p>Next, click Create Self Signed Certificate:</p>
















































  
<p><img src="/hodsmedia/659886406_2.png" alt=""/></p>
















































  
<p>Type in a great name for your certificate and click OK:</p>
















































  
<p><img src="/hodsmedia/659886406_3.png" alt=""/></p>
















































  
<p>You will now see your certificate in your Server Certificates list:</p>
















































  
<p><img src="/hodsmedia/659886406_4.png" alt=""/></p>
















































  
<p>Next, export this certificate by right-clicking on it and choosing Export. Enter a path to export the certificate to and enter a password:</p>
















































  
<p><img src="/hodsmedia/659886406_5.png" alt=""/></p>
















































  
<p>Locate the exported certificate file (.pfx), then open it (or right-click on it and choose to install it). You&#8217;ll be prompted with a Certificate Import Wizard. Click Next through all of the screens except for the one where you enter the certificate password. Make sure to mark the certificate as exportable:</p>
















































  
<p><img src="/hodsmedia/659886406_6.png" alt=""/></p>
















































  
<p>Click Next through the rest of the Wizard.</p>
















































  
<p>Now, open the Management Console (mmc.exe) and add the Certificates snap-in. Under Personal -&gt; Certificates you should now see your imported certificate:</p>
















































  
<p><img src="/hodsmedia/659886406_7.png" alt=""/></p>
















































  
<p>Right-click on this certificate, select All Tasks, then select Export. Enter the path to export the final certificate to and click Next through the export Wizard:</p>
















































  
<p><img src="/hodsmedia/659886406_8.png" alt=""/></p>
















































  
<p>At last you will have a .cer file that you can now upload to Azure for your management API commandlets:</p>
















































  
<p><img src="/hodsmedia/659886406_9.png" alt=""/></p>
















































  
<p><img src="/hodsmedia/659886406_10.png" alt=""/></p>
















































