---
layout: post
title: "Programming a Kinect Bounding Box"
---

<p>Source code: <a href="https://github.com/kindohm/kinect-bounding-box">https://github.com/kindohm/kinect-bounding-box</a></p>
<p>In my last post and screen cast I <a href="http://kindohm.com/2011/07/20/KinectMvvm.html"> showed how to implement the Kinect runtime as part of a Model-View-ViewModel (MVVM) pattern</a>. In this post and accompanying screen cast I will build on that application by adding a "bounding box" feature that limits control of the application depending on where the user is standing.</p>
<iframe width="560" height="349" src="http://www.youtube.com/embed/oRmZ9SOASN0" frameborder="0"> </iframe>
<p>Imagine a scenario where your Kinect-based application is running in an area where there is a lot of "noisy" user traffic in the background. It could be in a lobby, a trade show booth, a career fair, or a point of sale counter to name just a few scenarios. You obviously don't want users in the background to control your application. You only want users who <em>want</em> to control your application to  actually control it.</p>
<p style="text-align:center;"><img src="/hodsmedia/boundingboxexample.png" alt="example"/></p>
<p>There is more than one way to solve this problem. Perhaps the easiest way is to limit where a user is allowed to stand in order to control and interact with the application. I call this method <strong>The Bounding Box</strong> method. This method works great if you have <em>multiple instances</em> of the application running side by  side and if you don't want one instance to allow control by the user of the second instance.</p>
<p>Building on the ViewModel used in my previous example, all of the logic you need to determine if a user is within the bounding box can be executed with a few new properties and one method:</p>

<pre><code>double minDistanceFromCamera;
public double MinDistanceFromCamera
{
	get { return this.minDistanceFromCamera; }
	set
	{
		this.minDistanceFromCamera = value;
		this.OnPropertyChanged("MinDistanceFromCamera");
	}
}

double boundsWidth;
public double BoundsWidth
{
	get { return this.boundsWidth; }
	set
	{
		this.boundsWidth = value;
		this.OnPropertyChanged("BoundsWidth");
	}
}

double boundsDepth;
public double BoundsDepth
{
	get { return this.boundsDepth; }
	set
	{
		this.boundsDepth = value;
		this.OnPropertyChanged("BoundsDepth");
	}
}

bool userIsInRange;
public bool UserIsInRange
{
	get { return this.userIsInRange; }
	set
	{
		this.userIsInRange = value;
		this.OnPropertyChanged("UserIsInRange");
	}
}

bool GetUserIsInRange(
	Microsoft.Research.Kinect.Nui.Vector torsoPosition)
{
	return torsoPosition.Z &gt; this.MinDistanceFromCamera &amp;
		torsoPosition.Z &lt; (this.MinDistanceFromCamera + this.BoundsDepth)
		&amp; torsoPosition.X &gt; -this.BoundsWidth / 2 &amp;
		torsoPosition.X &lt; this.BoundsWidth / 2;
}
</code></pre>
<p>That <code>GetUserIsInRange</code> method is the key to this entire feature. It returns whether or not the user's position (assumed to be the user's torso or "spine" join) is within the bounding box. The bounding box is based on the X and Z coordinates in front of the Kinect sensor.</p>
<p>With these rules, you can control what the user can do in the application. For example, in my basic MVVM app I can control when the user has or does not have control of the ellipse on the screen:</p>
<pre><code>void kinectService_SkeletonUpdated(
	object sender, SkeletonEventArgs e)
{
	this.UserIsInRange = 
		this.GetUserIsInRange(e.TorsoPosition);

	// only perform this ViewModel logic if
	// user is in range
	if (this.UserIsInRange)
	{
		var midpointX = App.Current.MainWindow.Width / 2;
		var midpointY = App.Current.MainWindow.Height / 2;

		this.HandOffsetX = midpointX + 
			(e.RightHandPosition.X * 500);
		this.HandOffsetY = midpointY - 
			(e.RightHandPosition.Y * 500);
	}
}
</code></pre>
<p>Using this logic, you can make a useful UI configuration feature where the bounding box dimensions  can be configured in real time as you receive visual feedback about where you are with respect to the bounding limits:</p>
<p style="text-align:center;"><img src="/hodsmedia/KinectBoundingBox.png" alt="Kinect Bounding Box" /></p>
<p>In the screen shot above, the black rectangle represents a bird's eye view of the bounding box. The green circle is the user's position and it moves as the user moves in front of the Kinect camera. If the user steps outside the bounds, the green circle turns red. This type of a feature is highly valuable in a Kinect application when you need to configure limits on where a user is allowed to have control.</p>
<p>The source code and screen cast show how to implement the bounding logic in an MVVM pattern as well as how to implement the visual bounding box UI configuration screen.</p>
<p>Happy Kinect coding!</p>

