---
layout: post
title: "3D Performance"
---

<p>Early this morning I played around a bit with the <a href="http://kindohm.com/archive/2006/02/02/USAInDDD.aspx">3D USA model</a> and I wasn't happy at all with the performance.  The performance appears to depend on how many discrete 3D geometries are loaded into the entire viewport.  For example, when I increase the granularity step when I'm looping through latitude/longitude points then fewer points are used.  Increasing the granularity makes the model perform faster when panning, zooming, and angling the camera.  If I decrease the granularity step, more points are used, and the camera moves more slowly because the screen takes longer to re-draw.</p>
  
<p>In my design, I create a discrete 3D model for every triangle in a mesh.  I then group all triangles together in a model group.  So, a model group might represent the state of Minnesota, for example.  The state of Minnesota has about 5200 individual latitude/longitude points, which means 5200 triangles on the top surface, 5200 on the bottom surface, and about 10,400 on the bottom surface.  That's 20,800 discrete triangle models I'm loading into the group.  <a href="http://www.thoughtstoblog.com" target="_blank">Jake</a> and I discussed that it might be better to treat the entire state as a single model rather than compose the state as a group of many smaller models.  That way when I load all 50 states, I'll be loading 50 models instead of 1,000,000 (50 states * ~ 20,000 triangles per state) models.  </p>
  
<p>If I get some free time this weekend I'm definately going to try this out.</p>
  
