---
layout: post
title: "Three Lessons on Kinect Cursor Control with NITE"
---

<p> <a href="/files/KinectCursorSmoothing.zip">Download Sample Code - KinectCursorSmoothing.zip</a> [100kb] </p>
<p> <span style="margin-top: 5px; margin-left: 10px;border: solid 1px #999;float: right;padding: 5px;"> <img  src="/hodsmedia/lobby.jpg"  alt="Avtex Lobby"  style="width: 250px;"/> </span> At <a href="http://www.avtex.com">Avtex</a> we've been working on a variety of  Kinect-based WPF applications for use at trade shows and our office lobby.  One aspect of developing these apps has been getting cursor control through hand tracking and gestures to "feel right". At best, an app with unnatural hand-based cursor  control results in angry users who curse in disgust. At worst, an app has the risk  of becoming a failure.</p>
<p>First let's go over a short list of what can feel "wrong" about hand-based cursor control with the Kinect:</p>
<ol>
<li>Cursor moves too slow or fast with respect to hand movements</li>
<li>Cursor appears to shake or "jerk", especially when the user's hand is still</li>
<li>User has to reach too high or too low to reach controls on the screen</li>
</ol>
<p>With a little bit of love, understanding, and code, each of these  characteristics can be easily cured. </p>
<p> Before I get in to the solutions, I want to describe the approach I've taken for cursor control with Kinect using the open source NITE libraries. I'm not going to get in to how to set up NITE or use the Kinect device classes here, but instead  focus on the important interactions between the Kinect data and the code  responsible for moving the mouse pointer around.</p>
<p style="text-align: center;"> <img  src="/hodsmedia/kinectcursormodel.png"  alt="Kinect Cursor Model" style="width: 400px; margin-bottom: 10px;"/> </p>
<p>You can see how this model is implemented in the source code (download link at the top) available with this post.</p>
<p>Quite simply, I use a Kinect class that handles all of the Kinect device logic. This class uses a Wave gesture to initiate a session with a user, track their hand, and update their hand "point" in 3D space. This is all set up by my Kinect class's Run() method. PointUpdated() fires whenever the Kinect reads the user's new hand position. The Point attribute exposes the user's hand position to any other interested code. The Run() method runs infinitely on its own thread, continuously updating the user's hand position.</p>
<p>The MainWindow class (or any class that wants to update the mouse cursor position) uses a WPF DispatcherTimer to read the user's hand position at a given interval (e.g. every 100 milliseconds). The MainWindow class reads the hand Point and then uses a Win32 call to move the mouse to a screen position relative to the Kinect's hand position.</p>
<h3>Dealing With Cursor Speed</h3>
<p>The Kinect provides hand coordinates using the point directly in front of the Kinect lenses as the origin of Cartesian space (e.g. {0, 0, 0}). Moving your hand up or down results in a positive or negative y-axis value, respectively. Moving your hand right and left results in a positive or negative x-axis value. You can translate these Kinect coordinates to your WPF application's window coordinates with some simple math:</p>

	var x = window.Width / 2 +
		kinectPoint.X +
		window.Left;
	var y = window.Height / 2 -
		kinectPoint.Y +
		window.Top;
	MoveCursor(x, y)

<p>The above code aligns the center of your WPF window with the center of the Kinect space in the x/y plane and translates the Kinect x/y hand position to  the same x/y pixel position in your WPF window. The above code also accounts for your top-left window position relative to the top-left corner of your desktop by using the Left and Top window properties.</p>
<p>The problem with the above code is that "Kinect space" is smaller than "WPF window space". In other words, the cursor feels like it moves very slowly with respect to your hand movements. By introducing a movement scale factor you can increase the speed of the cursor until it "feels right":</p>

<pre><code>double movementScale = 2.0d;
var x = window.Width / 2 +
	kinectPoint.X * movementScale +
	window.Left;
var y = window.Height / 2 -
	kinectPoint.Y * movementScale +
	window.Top;
MoveCursor(x, y)</code></pre>

<p>In the above snippet I've increased the amount of movement by a factor of two.</p>
<p>A scale amount too low will result in the cursor moving too slow relative to the  user's hand. A scale value too high results in a cursor that moves way too fast. The  screen size and how far away the user is from the Kinect will affect the optimal scale value, so you will want to make this an adjustible runtime parameter in your app  if possible.</p>
<h3>The Dreaded Shaking Cursor</h3>
<p>Unfortunately, scaling the cursor movement introduces a side effect. As you scale the cursor movement, the small variations in the user's hand movements  are also magnified. When the user holds their hand still, the mouse cursor appears to "shake". This behavior is distracting and also gives observers the impression that the user is so scared that they are trembling (joke! kidding! geez!). If you happen to have small "hover points"  in your app (when the user is "selecting" something) then the cursor may not be able to stay hovering over the hover point as the cursor might "shake out" of the hover area.</p>
<p>Here is a quick example:</p>

<iframe title="YouTube video player" width="480" height="390" src="http://www.youtube.com/embed/_ZwCNoS-2kM?rel=0" frameborder="0"> </iframe>
<p>To deal with this you will need to smooth out the magnified Kinect noise. The easiest way to do this is to store the last few points that the Kinect has tracked in memory  (in an array) and return the average of the points as the new cursor point to move to. When a new Kinect point is detected the last point is popped off of the array, the new point is added in, and a new average is calculated:</p>

<pre><code>// store the last 5 hand points
static Point3D[] points = new Point3D[5];

public static Point3D RawPoint{ get; set; }
public static Point3D SmoothPoint{ get; set; }

static void control_PointUpdate(
	object sender, PointBasedEventArgs e)
{
	RawPoint = new Point3D()
	{
		X = e.Position.X,
		Y = e.Position.Y,
		Z = e.Position.Z
	};
	SmoothPoint = GetSmoothPoint();
}

static Point3D GetSmoothPoint()
{
	float x = 0;
	float y = 0;
	float z = 0;

	// shift array
	for (int i = points.Length - 1; i > 0; i--)
	{
		points[i] = points[i - 1];
	}

	points[0] = RawPoint;

	// final point is average of array points.
	// sum up array points and return the average.
	for (int i = 0; i &lt; points.Length; i++)
	{
		if (points[i] != null)
		{
			x = points[i].X;
			y = points[i].Y;
			z = points[i].Z;
		}
	}

	return new Point3D()
	{
		X = x / points.Length,
		Y = y / points.Length,
		Z = z / points.Length
	};
}</code></pre>

<p>Now the MainWindow's cursor-moving code can choose to use either the raw point or the smoothed point.</p>
<p>The smoothing code results in a much more pleasant user experience. The tradeoff is that the cursor movement lags behind the actual hand movement. If you increase the array size of the number of points used for the smoothing calculation, then the cursor will lag behind even more slowly but will produce a smoother experience.</p>
<p>You'll want to tweak the movement scale and smoothing array size parameters together to determine the best combination of values.</p>
<h3>Too Low, Too High</h3>
<p>The last usability factor to consider is how high or low the user needs to "reach" in your app. If you have hot spots or hover points in your app that are at the high or low extremes of your screen, the user may have to reach unnaturally high or low to hit those points depending on where the Kinect device is situated with respect to the user.</p>
<p>To adjust for this in real time without moving the Kinect itself, you can simply add a scalar value to your mouse movement code:</p>
<pre><code>double movementScale = 2.0d;
double xScalar = 0;
double yScalar = -200;

var x = window.Width / 2 +
	kinectPoint.X * movementScale +
	window.Left + xScalar;
var y = window.Height / 2 -
	kinectPoint.Y * movementScale +
	window.Top + yScalar;
MoveCursor(x, y)</code></pre>
<p>The above code simply translates the cursor 200 pixels in the "up" direction every time the cursor position is updated.</p>
<h3>Go Forth and Control Your Cursor!</h3>
<p>Movement scale, smoothing, and vertical reach - keep these three things in mind while developing your cursor-controlled Kinect app and you'll be able to focus on delighting your users with your killer app instead of dealing with their frustration!</p>

