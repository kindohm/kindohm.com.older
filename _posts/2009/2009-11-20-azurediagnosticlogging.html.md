---
layout: post
title: "Azure Diagnostic Logging"
---


<p>In my <a href="http://hodnick.com/post/235271466/azurelessons" target="_blank">Azure Lessons From the Trenches</a> post I discussed using a logging library such as log4net for diagnostic logging.  At the time, Azure&#8217;s logging capabilities weren&#8217;t very great.</p>


<p>With the release of the latest Azure SDK (November 2009), we now have the ability to use an Azure trace listener to perform basic logging to Azure Table Storage:</p>


<pre><code>&lt;system.diagnostics&gt;
  &lt;trace&gt;
    &lt;listeners&gt;
      &lt;add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener, 
              Microsoft.WindowsAzure.Diagnostics, 
              Version=1.0.0.0, 
              Culture=neutral, 
              PublicKeyToken=31bf3856ad364e35"
                name="AzureDiagnostics"&gt;
          &lt;filter type="" /&gt;
      &lt;/add&gt;
    &lt;/listeners&gt;
  &lt;/trace&gt;
&lt;/system.diagnostics&gt;</code></pre>
<p>You can read more about configuring logs at <a href="http://msdn.microsoft.com/en-us/library/ee843890.aspx" target="_blank">Initializing and Configuring Diagnostic Data Sources</a> in the Azure SDK.  The above config XML fragment was taken from that Url.</p>


<p>Table storage is &#8220;cheap&#8221; in Azure (compared to SQL at least) - so this is a cost-effective way of writing <i>diagnostic</i> data.  I put emphasis on <i>diagnostic</i> because this isn&#8217;t data you will really want to report on in SQL anyway.  This is a fire-hose of informational data that can be easily retrieved or deleted through the <a href="http://msdn.microsoft.com/en-us/library/dd179423.aspx" target="_blank">Azure storage API</a>.</p>


