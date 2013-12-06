---
layout: post
title: "Kinect Sensor in an MVVM App"
---


<p>Source code: <a href="https://github.com/kindohm/kinect-mvvm">https://github.com/kindohm/kinect-mvvm</a></p>
<p>Now that the official Kinect SDK is out I've been looking for good patterns when incorporating it into Windows 7 applications. The Model-View-ViewModel (e.g. MVVM) pattern is my pattern of choice for WPF applications and I wanted to demonstrate how I incorporate the Kinect runtime within the MVVM pattern.</p>
<p>I've created a screencast walk-through of how you can incorporate the Kinect runtime into MVVM (or read on below for a brief description of the approach).</p>
<iframe width="560" height="349" src="http://www.youtube.com/embed/Rs7sQqljr3Q" frameborder="0"> </iframe>
<p>The main concept of incorporating the Kinect runtime into MVVM is to abstract all of its behaviors out into its own service interface:</p>
<pre><code>public interface IKinectService { }</code></pre>
<p>In my approach, a simple event is defined in the interface that is used to signal when some Kinect runtime data has become available, such as new skeleton information:</p>
<pre><code>public interface IKinectService
{
	event EventHandler&lt;SkeletonEventArgs&gt; SkeletonUpdated;
}</code></pre>
<p>Basically, the Kinect service just fires events whenever it wants (probably hundreds of times per second).</p>
<p>At this point in the Kinect SDK's lifetime, failing to "uninitialize" it causes problems and can sometimes require a reboot. Thus, putting some methods on the interface to allow us to deal with starting up and  shutting down the runtime would be helpful:</p>
<pre><code>public interface IKinectService
{
	event EventHandler&lt;SkeletonEventArgs&gt; SkeletonUpdated;
	void Initialize();
	void Uninitialize();
}</code></pre>
<p>From there we can implement a basic KinectService:</p>

<pre><code>public class ConcreteKinectService : IKinectService
{
	Runtime runtime;

	public void Initialize()
	{
		runtime = new Runtime();
		runtime.SkeletonFrameReady += 
			new EventHandler&lt;SkeletonFrameReadyEventArgs&gt;(runtime_SkeletonFrameReady);
		runtime.Initialize(RuntimeOptions.UseSkeletalTracking);
	}

	void runtime_SkeletonFrameReady(object sender, SkeletonFrameReadyEventArgs e)
	{
		var skeleton =
		   e.SkeletonFrame.Skeletons
		   .Where(
				s =&gt; s.TrackingState == SkeletonTrackingState.Tracked)
		   .FirstOrDefault();

		if (skeleton == null) return;

		var position = skeleton.Joints[JointID.HandRight].Position;
		if (this.SkeletonUpdated != null)
		{
			this.SkeletonUpdated(this, 
				new SkeletonEventArgs() 
				{ 
					RightHandPosition = position 
				});
		}
	}

	public void Cleanup()
	{
		if (runtime != null)
		{
			runtime.Uninitialize();
		}
	}

	public event EventHandler&lt;SkeletonEventArgs&gt; SkeletonUpdated;
}</code></pre>

<p>This IKinectService can now be injected into a ViewModel whenever it is created. Using the "Locator" pattern from the MVVM Light toolkit, this is done when the locator constructs its ViewModels:</p>
<pre><code>public class ViewModelLoader
{
	static ViewModel viewModelStatic;
	static IKinectService kinectService;

	public ViewModelLoader()
	{
		kinectService = new ConcreteKinectService();
		var isInDesignMode = ...; // detect if running in VS designer
		if (!isInDesignMode) kinectService.Initialize();
	}

	public static ViewModel ViewModelStatic
	{
		get
		{
			if (viewModelStatic == null)
			{
				viewModelStatic = 
					new ViewModel(kinectService);
			}
			return viewModelStatic;
		}
	}

	public ViewModel ViewModel
	{
		get
		{
			return ViewModelStatic;
		}
	}

	public static void Cleanup()
	{
		if (viewModelStatic != null)
		{
			viewModelStatic.Cleanup();
		}
		kinectService.Cleanup();
	}
}</code></pre>
<p>A static copy of the ViewModel is used so that it can be "cleaned up" easily from any point in the application. This cleanup eventually unhooks events and uninitializes the Kinect runtime further down the call stack.</p>
<p>There are a variety of benefits to this approach:</p>
<ol>
<li>The Kinect runtime logic is de-coupled from the ViewModel and View/UI</li>
<li>Multiple types of Kinect services could be implemented. Internally, a concrete IKinectService could use either event-based or polling-based skeleton tracking. With an IKinectService you could swap out one for the other and the application won't care.</li>
<li>The Kinect service can be mocked during unit testing.</li>
</ol>
<p>Hopefully this approach can help make your MVVM-based Kinect apps more fun to code! Enjoy!</p>

