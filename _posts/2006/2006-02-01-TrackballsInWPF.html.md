---
layout: post
title: "3D Trackballs in WPF"
---

<p>The next thing I want to accomplish in my demographics project is to build a nice clean 3D viewing UI. What I mean by that is that the user (me) should be able to easily pan, zoom, and change the viewing angle of the camera inside the 3D space. In my initial experimentation a few months ago I was using slider controls to accomplish this. It was a quick approach, but was annoying to use because I needed a slider for each direction or "degree of freedom" that I wanted to move. At minimum I needed six sliders - a slider each for the X, Y, and Z positions of the camera and a slider each for the X, Y, and Z vectors of the camera look direction.  </p>
<p> </p>
<p>I desparately needed a trackball. I needed a way to click and hold down a mouse button to control the position and direction of the camera. A Google search yielded the following result on a GotDotNet workspace: <a href="http://workspaces.gotdotnet.com/3DTools" target="_blank">http://workspaces.gotdotnet.com/3DTools</a>. Isn't the Intarweb great? Someone already developed a trackball for me. How thoughtful :)</p>
<p>The code is pretty good. There are just a couple of methods that do the dirty work of calculating angles, and the classes are small, neat, and tidy. The only problem was that the code only allowed you to zoom or rotate and didn't allow you to pan. So, I was able to easily extend the trackball class to allow panning too. Just left-click to pan, right-click to zoom, and Shift+left-click to rotate. </p>
<p>My version of the code can be downloaded at <a href="http://www.kindohm.com/files/3DTools-Enhanced.zip">http://www.kindohm.com/files/3DTools-Enhanced.zip</a>. Details about the trackball implementation can be found at <a href="http://viewport3d.com/trackball.htm" target="_blank">http://viewport3d.com/trackball.htm</a>. </p>
<p>Here's a screenie:</p>
<p><img alt="trackball" src="http://www.kindohm.com/localimages/trackball.jpg" border="0" width="300" /></p>
<p>So, here's another interesting tidbit. The 3DTools example uses an external XAML file for the tiger model. This got me thinking: instead of always pulling state, county, city, and zip code boundary data from SQL server and building meshes from scratch in my demographics project every time I want to display something, why not build and export XAML files for them instead? It'd be a lot more efficient. I might look in to this. XAML files would eliminate the need to take coordinates, build triangle meshes, calculate and apply normals, and calculate and apply texture coordinates. All of that could be dumped to a XAML file and just loaded in one shot later. </p>
 
