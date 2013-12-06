---
layout: post
title: "Bug with Subtext Search"
---

<p>I took off the Search feature of the site this morning after discovering a bug.  Basically the Url of each search result item is invalid.  It comes back in the format <code>http://www.kindohm.com//archive/etc....</code>.  I checked out the <a title="Subtext" href="http://www.subtextproject.com" target="_blank">Subtext</a> search control in Reflector, and I can see where that extra forward-slash is being added.  It's added if the blog is hosted in a subfolder under the root of the domain, which mine is not.  There's a conditional IF statement that checks this, and my site looks correctly configured in the Subtext database, so I'm not sure what's going wrong. </p> 
