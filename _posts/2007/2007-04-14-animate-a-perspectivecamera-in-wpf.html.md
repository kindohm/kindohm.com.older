---
layout: post
title: "Animate a PerspectiveCamera in WPF"
---

<p>This app started out as an experiment with animating a PerspectiveCamera in a <a title="WPF" href="http://msdn.microsoft.com/windowsvista/" target="_blank">WPF</a> 3D scene.  Basically, I wanted to achieve the effect of "flying" through a model.  WPF's 3D animations are just too tempting to me though, and after achieving the camera animation I couldn't help but busy-up the app with rotating videos, a background <a href="http://www.edgey.net" target="_blank">Edgey</a> track and some extra keyboard commands to have fun with.  I haven't done anything useful in a WPF app yet :)</p>
  
<p>Anyway, here's the source code: <a href="http://www.kindohm.com/files/MoveCamera.zip">MoveCamera.zip (8MB)</a>.  The zip file is big because the media files are included.</p>
  
<p>And here's a screenshot:</p>
  
<p><a target="_blank" href="http://www.flickr.com/photos/kindohm/458366540/" title="Photo Sharing"><img src="http://farm1.static.flickr.com/197/458366540_d59e7c1a9f.jpg" border="0" width="500" height="391" alt="MoveCamera" /></a> </p>
  
<p>The camera animation code is fairly easy to implement.  The basic algorithm is to 1) calculate the next random camera position, 2) calculate the "look at direction" vector based on the new position and the desired look-at point (always near the origin of Cartesian space, 3) animate the position and look-at vector, and 4) handle the animation Completed event to repeat the process:</p>
  
	Point3D nextPosition = GetNextCameraPosition();
	Vector3D nextLookDirection = GetNextLookDirection(nextPosition);
	Point3D currentPosition = camera.Position;
	Vector3D currentLookDirection = camera.LookDirection;
	int duration = GetNextFlightDuration();

	Vector3DAnimation lookAnimation = new Vector3DAnimation(
		currentLookDirection, nextLookDirection, 
		TimeSpan.FromMilliseconds(duration));

	Point3DAnimation positionAnimation = 
	   new Point3DAnimation(currentPosition, nextPosition, 
	   TimeSpan.FromMilliseconds(duration));

	lookAnimation.Completed += new EventHandler(lookAnimation_Completed);

	camera.BeginAnimation(
		PerspectiveCamera.LookDirectionProperty, lookAnimation);
	camera.BeginAnimation(
		PerspectiveCamera.PositionProperty, positionAnimation);

  
<p class="tags">tags: <a href="http://technorati.com/tag/wpf" target="_blank" rel="tag">wpf</a> <a href="http://technorati.com/tag/3d" target="_blank" rel="tag">3d</a> <a href="http://technorati.com/tag/animation" target="_blank" rel="tag">animation</a> <a href="http://technorati.com/tag/camera" target="_blank" rel="tag">camera</a> <a href="http://technorati.com/tag/code" target="_blank" rel="tag">code</a> <a href="http://technorati.com/tag/programming" target="_blank" rel="tag">programming</a>  </p>
 
