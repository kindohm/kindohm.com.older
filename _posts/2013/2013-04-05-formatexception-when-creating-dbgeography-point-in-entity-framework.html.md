---
layout: post
title: "FormatException When Creating DbGeography Point in Entity Framework"
---

<p>I've been working with spatial data types in SQL Server and Entity Framework 5 to perform distance searches by zip codes. This has been a fun path of learning so far, but a simple misunderstanding of coordinate systems got me hung up for a while yesterday.</p>



<p>Two samples, <a href="http://msdn.microsoft.com/en-US/data/jj250903">Entity Framework Spatial - EF Designer</a> and <a href="http://msdn.microsoft.com/en-US/data/hh859721">Entity Framework Spatial - Code First</a>, both illustrate that you need to use one of the helpers on the <code>DbGeography</code> class to create a <code>DbGeography</code> type. The <code>DbGeography</code> type is the Entity Framework type that maps to the SQL <code>geography</code> type.</p>



<p>Easy, right? I encountered a <code>System.FormatException</code> with the following code:</p>



<pre><code>// coords for Hawaii
var latitude = 21.3114d;
var longitude = -157.7964d;
var point = string.Format(
	"POINT({0} {1})", 
	latitude.ToString(), 
	longitude.ToString());

// exception thrown here...
var location = DbGeography.FromText(point);</code></pre>
<p>This resulted in a FormatException:</p>



<blockquote>
<p>24201: Latitude values must be between -90 and 90 degrees.</p>


</blockquote>
<p>What the hell? Of course my latitude is within the required range!</p>



<p>Ah, but you see, the SQL <code>POINT</code> function uses a different coordinate system convention than we humans typically use for latitude and longitude. As far as I can remember, it's customary to say "Latitude-Longitude" in English. Latitude first, then longitude. { lat, long }. Right? That's not how <code>POINT</code> works. <code>POINT</code> expects latitude and longitude in the other order. If I switch the order of the <code>POINT</code> arguments my code works fine:</p>



<pre><code>// coords for Hawaii
var latitude = 21.3114d;
var longitude = -157.7964d;
var point = string.Format(
	"POINT({1} {0})", 
	latitude.ToString(), 
	longitude.ToString());

var location = DbGeography.FromText(point);

// the light is green, the trap is clean</code></pre>
<p>When you think about it, this makes sense. By convention, when we talk about {x, y} coordinates, the first number is the horizontal axis value, and the second number is the vertical axis value. Longitude is of course the horizontal, so it seems to make some sense that the <code>POINT</code> function requires the longitude to be in front of latitude. </p>




