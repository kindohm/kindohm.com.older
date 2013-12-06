---
layout: post
title: "Not Really Sealed Content Types in SharePoint 2007"
---

<p>In the object-oriented world, when I hear the word "sealed" I think of preventing inheritance.  If I create a sealed class named Car, it means that I don't want anyone else to inherit or derive from Car.  </p>

<p>In WSS 3.0, you can create new Content Types that derive from a parent Content Type (e.g. a Car Content Type that derives from a parent Vehicle Content Type).  You can also create sealed Content Types.  You can refer to the <a href="http://msdn2.microsoft.com/en-us/library/ms463449.aspx" target="_blank">Content Type Definition Schema</a> and the <a href="http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.spcontenttype.aspx" target="_blank">SPContentType class</a> for details.  </p>

<p>So what does it mean to create a sealed Content Type?  Not as much as I had hoped.  It turns out that a sealed Content Type is not analogous to a sealed class in OOP.  You can derive a new child Content Type from a sealed parent Content Type.  Let's dig in...</p>

<p>I created a Feature that creates a new sealed Content Type with the following elements definition:</p>

<pre><code>&lt;?xml version="1.0" encoding="utf-8" ?&gt;
&lt;Elements 
  xmlns="http://schemas.microsoft.com/sharepoint/"&gt;
  &lt;ContentType ID="0x0100AFF5F6F939DB46e2B0FA996BBD1B51FB"
    Name="MikeContentType"
	Group="Mike Content Types"
	Description="A Mike item"
	Sealed="TRUE"
	FeatureId="9BF7D516-A19E-4740-9566-53E1A70EA414"&gt;
  &lt;/ContentType&gt;
&lt;/Elements&gt;</code></pre>
<p>When you go to the edit screen for this new Content Type in WSS, you see the following:</p>

<p><a title="Photo Sharing" href="http://www.flickr.com/photos/kindohm/533098450/" target="_blank"><img height="419" alt="SealedContentType" src="http://farm2.static.flickr.com/1203/533098450_a3636d2ff9.jpg" width="500" border="0" /></a></p>

<p>WSS indicates that the Content Type is sealed (in parenthesis in the title). One thing to note is that all of the settings links and column options have been eliminated on this screen. The screen is basically empty.</p>

<p>So let's try and derive a new Content Type from this sealed Content Type. Here is what the new Content Type creation screen looks like:</p>

<p><a title="Photo Sharing" href="http://www.flickr.com/photos/kindohm/533195159/" target="_blank"><img height="500" alt="NewChildContentType01" src="http://farm2.static.flickr.com/1166/533195159_5257ad4be0.jpg" width="464" border="0" /></a></p>

<p>Notice in the Parent Content Type drop down list that I can select the sealed MikeContentType item. If you click ok and view the child content type, you see this:</p>

<p><a title="Photo Sharing" href="http://www.flickr.com/photos/kindohm/533195185/" target="_blank"><img height="492" alt="NewChildContentType02" src="http://farm2.static.flickr.com/1108/533195185_50c5e491b9.jpg" width="500" border="0" /></a></p>

<p>The child content type shows the sealed MikeContentType as its parent. "Sealed" isn't so "sealed" in WSS-land.</p>

<p>It appears that the only functionality that a sealed Content Type provides is the ability to lock down a content type's settings. If I modify the sealed Content Type definition to not be sealed, this is what its edit screen looks like:</p>

<p><a title="Photo Sharing" href="http://www.flickr.com/photos/kindohm/533098494/" target="_blank"><img height="500" alt="UnsealedContentType" src="http://farm2.static.flickr.com/1304/533098494_529adadd23.jpg" width="499" border="0" /></a></p>

<p>Now the previously-sealed Content Type can have its settings and columns modified through the UI. It appears as though that marking a Content Type as sealed only prevents editing it through the user interface - rather than preventing inheritance. </p>

<p>This is pretty unfortunate if you ask me. First of all, it's misleading. Second of all, it doesn't appear that you can prevent Content Type inheritance. <a href="http://www.justaddcode.com/blog" target="_blank">Neil</a> suggested to me that inheritance could possibly be prevented using a <a href="http://msdn2.microsoft.com/en-us/library/ms499244.aspx" target="_blank">custom policy</a>. You could probably swing that, but I still wonder why the word "sealed" was chosen for out-of-the-box functionality that doesn't appear to have a "sealed" behavior. </p>

<p class="tags" id="0767317B-992E-4b12-91E0-4F059A8CECA8:a918a79b-b04c-4f87-aaf6-d04f46c377f7">del.icio.us tags: <a href="http://del.icio.us/popular/sharepoint" target="_blank" rel="tag">sharepoint</a> <a href="http://del.icio.us/popular/wss" target="_blank" rel="tag">wss</a> <a href="http://del.icio.us/popular/contenttype" target="_blank" rel="tag">contenttype</a> <a href="http://del.icio.us/popular/content%20type" target="_blank" rel="tag">content type</a> <a href="http://del.icio.us/popular/sealed" target="_blank" rel="tag">sealed</a> <a href="http://del.icio.us/popular/programming" target="_blank" rel="tag">programming</a> <a href="http://del.icio.us/popular/oop" target="_blank" rel="tag">oop</a></p>

 
