---
layout: post
title: "WPF 3D Manipulation with Kinect"
---

<p>Download source code: <a href="http://kindohm.com/files/Kinect3dManipulation.zip">Kinect3dManipulation.zip</a></p>
<p>I've created an implementation of a Kinect NUI Trackball which can be used for manipulating a camera around WPF 3D scenes. This "HandsTrackball" is adapted from the Trackball class included  in the source code for <a href="http://3dtools.codeplex.com">WPF 3DTools</a>. The original Trackball class allows you to use the mouse to rotate around a WPF 3D scene.</p>
<p>My HandsTrackball basically maps hand movements to what the original Trackball uses as  mouse points. Some new ground rules as well as as some Kinect depth-to-screen point calculations are applied in order to make this all work.</p>
<p>This video explains how the HandsTrackball is used:</p>
<iframe width="560" height="349" src="http://www.youtube.com/embed/fwegNqzmG0A" frameborder="0"> </iframe>
<p>The user's left or right hand can be used to rotate around the model. When the user moves their  hand, a transform is applied to the camera viewing the 3D scene (the camera moves and the objects in the scene do not move). The camera only moves if the user's hands are at least a minimum distance away from the user's torso. The user can only move the camera when their hand is deliberately extended out from their body. This is a key constraint in order to make the HandsTrackball usable. It allows the user to put their hand(s) down to stop changing the camera angle and prevents accidental movement.</p>
<p>The code used to determine whether the app can move or zoom is trivial:</p>

<pre><code>this.rightHandCanMove =
	Math.Abs(this.torso.Z - this.rightHand.Z) 
		&gt;= MinTorsoDistance &amp;
	Math.Abs(this.torso.Z - this.leftHand.Z) 
		&lt; MinTorsoDistance;

this.leftHandCanMove =
	Math.Abs(this.torso.Z - this.leftHand.Z) 
		&gt;= MinTorsoDistance &amp;
	Math.Abs(this.torso.Z - this.rightHand.Z) 
		&lt; MinTorsoDistance;

this.canZoom =
	Math.Abs(this.torso.Z - this.rightHand.Z) 
		&gt;= MinTorsoDistance &amp;
	Math.Abs(this.torso.Z - this.leftHand.Z) 
		&gt;= MinTorsoDistance;</code></pre>

<p>To zoom, both hands are extended. When both hands reach the minimum distance away from the torso, the user performs a "pinch" gesture with their hands and arms to zoom in and out. The right hand's X position is used to control the zoom amount as it moves back and forth.</p>

<p>Much of this code, such as mapping skeleton coordinates to screen coordinates, is borrowed from existing Kinect sample code which you can download from the  <a href="http://research.microsoft.com/en-us/um/redmond/projects/kinectsdk/">Kinect for Windows SDK beta site</a>.</p>

<p>The most interesting aspect of my NUI trackball approach was to use a combination of both event handling and polling to obtain and process the skeleton and depth data. I chose to use event handling for lightweight operations, such as refreshing Kinect data that must be always kept current. I used polling at timed intervals for operations that required more CPU, such as vector math and skeleton-to-screen mapping. In the source code, the Kinect class handles the event-based operations. HandsTrackball uses a DispatcherTimer to handle the polling at given intervals.</p>

