---
layout: post
title: "Use 3DTools for Generating Texture Coordinates in WPF"
---

<p>I've always struggled with (or have completely blown off) generating texture coordinates for <a title="WPF" href="http://msdn.microsoft.com/windowsvista/" target="_blank">WPF</a> 3D meshes.  Texture coordinates are what allow you to map 2D resources (images, videos, or any visual WPF element) on to a 3D surface.  </p>
  
<p><a href="http://www.codeplex.com/3DTools" target="_blank">3DTools</a> out on CodePlex takes care of this for you.  Included with 3DTools is a mesh utilities class that will generate texture coordinates for a variety of basic surfaces, including planes, spheres, and cylinders.</p>
  
<p>3DTools also contains a bunch of other goodies (to be discussed at a later point in time).</p>
  
<p>I can't mention 3DTools for the first time without stating something very annoying about it.  The developers have chosen to name the root namespace of 3DTools as "_3DTools".  The underscore is present because you can't begin a .Net namespace with a numeric character.  I wish the developers would choose to follow the .Net 3.0 3D namespace conventions and name it "Tools3D".  That underscore results in an unintuitive namespace name, and it just looks damn ugly :)</p>
  
<p>Download 3DTools and enjoy.</p>
<p class="tags">tags: <a href="http://technorati.com/tag/3d" target="_blank" rel="tag">3d</a> <a href="http://technorati.com/tag/wpf" target="_blank" rel="tag">wpf</a> <a href="http://technorati.com/tag/3dtools" target="_blank" rel="tag">3dtools</a> <a href="http://technorati.com/tag/code" target="_blank" rel="tag">code</a> <a href="http://technorati.com/tag/programming" target="_blank" rel="tag">programming</a> </p>
 
