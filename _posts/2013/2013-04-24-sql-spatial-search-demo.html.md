---
layout: post
title: "Demo: SQL Spatial Search With Entity Framework 5"
---

<p>I've built a quick app to demonstrate how to work with basic spatial
	geography data in SQL Server (2012), and perform distance searches
	on lat/long points with LINQ queries in Entity Framework 5.</p>

<p>There are already some decent resources out there on the web showing how
	to work with SQL Geography types and Entity Framework 5,
	but I felt it would be valuable to throw
	together an entire ASP.NET MVC application to demonstrate the whole
	kit <em>and</em> the caboodle.</p>

<p>You can get the app at 
	<a href="https://github.com/kindohm/spatial-search-demo">github.com/kindohm/spatial-search-demo</a>.
It's an ASP.NET MVC app that will automatically load up some United States
geographic data from a flat file. Just hit F5 and go. It'll create the database
automatically with EF5 code first. It will use 
(LocalDb)\v11.0 for the database by default, so if you want to use something
else, just change the connection string.</p>

<p>There are three interesting parts of this demo:</p>

<ol>
	<li>Inserting data into Geography type fields</li>
	<li>Searching for records within a given distance from a lat/long point</li>
	<li>Displaying a record's distance from a given lat/long point</li>
</ol>

<h3>Inserting Lat/Long Data</h3>

<p>Before I dive in, I should state that the SQL Geography type is special.
	SQL Server can do some pretty nifty math with Geography types, such as
	distance searches (among other things). To take advantage of this, add
	a <code>geography</code> column to your SQL table.</p>

<p>Given that you have a SQL table with a field of type <code>geography</code>:</p>

<p><img src="/hodsmedia/geography-field.png" alt="Geography Field"/></p>

<p>The extra Latitude and Longitude columns in the example above aren't really
	necessary to keep in the table, but in the demo code I kept them around
	to make it more human-readable if you inspect the data. The <code>geography</code>
	column doesn't really make much sense to look at unless you're a computer:</p>

<p><img src="/hodsmedia/geography-display.png" alt="Geography Display"/></p>

<p>Storing a simple {latitude, longitude} point in this <code>geography</code>
column with	EF5 is trivial (once you know how to do it):</p>

<pre><code>var latitude = 47.38919d;
var longitude = -121.38917d;
var pointStringFormat = "POINT({1} {0})";

var pointString = string.Format(pointStringFormat, 
	latitude.ToString(), longitude.ToString());

var location = DbGeography.FromText(pointString);

var myNewEntity = new MyEntity();
myNewEntity.Location = location;
context.SaveChanges();</code></pre>

<p>This code is slightly unintuitive. You need to create an Entity Framework 
	<code>DbGeography</code> type, and this type is created from a helper method 
	on the <code>DbGeography</code> class named <code>FromText()</code>. There are
	other helper methods on <code>DbGeography</code> for creating other types
	of geography values (such as a 2D area, etc), but I won't get in to those
	now.</p>

<p>Basically, you need to create the <code>DbGeography</code> type from a string
	that looks something like this:</p>

<pre><code>POINT(-121.38917 47.38919)</code></pre>

<p><strong>Notice that the longitude is specified first!</strong></p>

<h3>Searching For Entities By Distance</h3>

<p>The most common thing you'll do with lat/long data is search for rows that
	have a location within a given distance from a lat/long point. In EF5, you can
	do this by creating a <code>DbGeography</code> instance of the lat/long
	you want to use as your origin point of interest, then use the
	<code>DbGeography.Distance()</code> instance method to calculate the
	entity's distance from that origin.</p>

<p>For example, let's say we want to find all places that have a location within
	ten miles of {lat,long}:{37.2819, -101.3828}:</p>

<pre><code>var originLat = 37.2819d;
var originLong = -101.3828d;

var originPointString = string.Format("POINT({1} {0})", 
	originLat.ToString(), originLong.ToString());

var origin = DbGeography.FromText(originPointString);

var distanceMiles = 10d;
var distanceMeters = distanceMiles * 1609.34d;

var results = context.Places
    .Where(p => p.Location.Distance(startPoint.Location) &lt;= distanceMeters);</code></pre>

<p>By default, SQL Server server will calculate distances in <em>meters</em>
	(thus the miles-to-meters conversion above). It
	is possible to use and specify other systems of measurement in SQL Server, but
	that is a different topic.</p>

<h3>Displaying a Location's Distance</h3>

<p>Finding locations within a certain distance of another location is cool, 
	but you might also want to find out
	<em>how far away</em> the location is from the origin. You can do this
	by selecting into a new object after your first <code>Where()</code>
	condition:</p>

<pre><code>var results = context.Places
    .Where(p => p.Location.Distance(origin.Location) &lt;= meters)
    .Select(p => new 
    {
        Name = p.Name,
        Distance = (p.Location.Distance(startPoint.Location) / 1609.34d).Value
    })</code></pre>
<p>Of course you can sort by distance as well by tacking on an <code>OrderBy()</code></p>
<pre><code>.OrderBy(p => p.Distance);</code></pre>
