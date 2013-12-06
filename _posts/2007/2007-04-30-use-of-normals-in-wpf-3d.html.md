---
layout: post
title: "Use of Normals in WPF 3D"
---

<p>Download the source code for this post: <a href="http://www.kindohm.com/files/normals3d.zip">Normals3D.zip</a> (100 KB)</p>
  
<p>One of the questions brought up during my WPF 3D talk at the Code Camp this weekend was what happens when you don't use normals in your meshes.  The answer is that it depends.  When you use a complete, discrete set of points for each flat surface in your mesh, the the effects of not using normals isn't that significant.  However, if you are "lazy" and use the fewest amount of points possible for all triangles in your mesh, then the effects of not using normals is more noticeable.  The reason for this is that each point in your mesh can only be assigned one normal.  Thus, if you have a vertex in your mesh that is the intersection of multiple triangles - and if you only use one position in your mesh to define that vertex - you can only use one normal to define what direction light will reflect off of all of the triangles at that point.  </p>
  
<p>Take the corner of a cube, for example.  Six different triangles all converge at the same corner point on a cube (three sides all meet at one point, and each side has two triangles).  If you use one shared position for all six of these triangles, you can only use one normal.  If you need a good looking model, then you should use six separate points and define normals for each.  In the case of a cube, you could get away with three positions because two triangles on each side will likely have the same normal vector.</p>
  
<p>Take this example:</p>
  
<p style="text-align: center;"><a target="_blank" href="http://www.flickr.com/photos/kindohm/478510631/" title="Photo Sharing"><img src="http://farm1.static.flickr.com/183/478510631_6691d59381_o.png" width="404" height="352" alt="WPF 3D Normals" border="0" /></a></p>
  
<p>In the "front" are three cubes - each created with 24 discrete positions.  Each side of the cube does not share any positions with another side.  The first cube has no normals, the second cube has normals that extend in perpendicular directions from the cube faces, and the third cube has "angled" normals that point at non-perpendicular angles from their faces.  One observation is that the cube without normals appears the same as the cube with perpendicular normals.  Honestly, I don't know why this happens.  My guess is that normals are automatically generated for a mesh (probably using a cross product of the first two sides of the triangle in the mesh) if none are explicitly assigned. The second observation you can make is that a normals have a significant effect on lighting - the non-perpendicular angles in the 3rd cube cause a very different lighting effect [1].</p>
  
<p>In the "back" are three more cubes - which were created in a "lazy" manner with only eight discrete positions.  Each position is shared by converging triangles in the mesh.  You can see right away how using fewer points has an impact on the mesh's appearance under light.  In the 4th cube (1st lazy cube), no normals are assigned.  WPF came up with the normals/lighting itself.  In the 5th cube, I picked an arbitrary direction for the normals.  In the 6th cube, I used arbitrary normals that are not perpendicular.  </p>
  
<p>The effect of normals on your model varies depending on how you build the positions in your mesh.  It all comes down to how much detail and "realism" you're going for.  The expected down side is that you need to do a little more work to get more detail.</p>
  
<p>Download the source code: <a href="http://www.kindohm.com/files/normals3d.zip">Normals3D.zip</a> (100 KB)</p>
  
<p>[1] I'm using both a diffuse and specular material in these cubes, so the cubes appear to have a shiny reflection.</p>
  
<p class="tags"> tags: <a href="http://technorati.com/tags/wpf" target="_blank" rel="tag">wpf</a> <a href="http://technorati.com/tags/3d" target="_blank" rel="tag">3d</a> <a href="http://technorati.com/tags/code" target="_blank" rel="tag">code</a> <a href="http://technorati.com/tags/normals" target="_blank" rel="tag">normals</a> <a href="http://technorati.com/tags/lighting" target="_blank" rel="tag">lighting</a>  </p>
 
