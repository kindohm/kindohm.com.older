---
layout: post
title: "Handle Route to Classic .asp Path in ASP.NET MVC 4"
---

<p>Source code: <a href="https://github.com/kindohm/mvc-handle-classic-asp-routes">github.com/kindohm/mvc-handle-classic-asp-routes</a></p>
<p>I recently completed a feature where I needed to support a Url to a classic .asp file in our MVC project. We are re-writing an old, classic ASP site; another site (not under our control) is posting to a classic ASP page on the site we are re-writing. The external site posts to the classic ASP page like this:</p>


<pre><code>&lt;form 
    method="post" 
    action="http://site.com/Path/To/classic.asp"&gt;
    &lt;input id="theInput" name="theInput" /&gt;&lt;br/&gt;
    &lt;input type="submit" value="Submit" /&gt;
&lt;/form&gt;</code></pre>

<p>So, in our MVC app, we needed to support a route to <code>path/to/classic.asp</code> and map it to an existing controller action.</p>

<p>The route is trivial:</p>

<pre><code>routes.MapRoute(
    name: "Classic",
    url: "Path/To/classic.asp",
    defaults: new { 
        controller = "Home", 
        action = "Results" 
    }
);</code></pre>

<p>As you can see, I'm mapping the target .asp Url to an action named <code>Results</code> on the Home controller. This controller action looks like this:</p>



<pre><code>[HttpPost]
public ActionResult Results(string theInput)
{
    dynamic viewModel = new ExpandoObject();
    viewModel.TheInput = string.IsNullOrEmpty(theInput) ? 
        "NOTHING" : theInput;
    return View(viewModel);
}</code></pre>

<p>Pretty basic. Notice that <code>POST</code> is the only HTTP verb supported on this action.</p>
<p>At this point, if you try and post data to this action with that route, you will get the following IIS 404 error:</p>
<p><a href="/hodsmedia/classic-asp-notfound.png"><img src="/hodsmedia/classic-asp-notfound.png" alt="not found" width="517" height="226" class="aligncenter size-full wp-image-1618" /></a></p>

<p>Pesky IIS! IIS is still looking for a physical .asp file.</p>
<p>There is one other step you must take to get IIS to handle the .asp route with MVC. By default, IIS uses a classic ASP handler to handle anything coming in to the web server with a ".asp" extension. We need to override that with a simple addition to web.config in our MVC app. We'll make the addition to the <code>system.webServer/handlers</code> section:</p>


<pre><code>&lt;add name="classic-asp"
   path="*.asp"
   verb="*"
   type="%windir%\Microsoft.NET\Framework\v4.0.30319\aspnet_isapi.dll"
   resourceType="File"
   preCondition="integratedMode" /&gt;</code></pre>

<p>Now the post to the .asp file will work!</p>
<p>A complete example of this is available on github: 
  <a href="https://github.com/kindohm/mvc-handle-classic-asp-routes">github.com/kindohm/mvc-handle-classic-asp-routes</a></p>

