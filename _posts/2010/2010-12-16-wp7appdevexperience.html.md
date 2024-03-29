---
layout: post
title: "Reflections on Developing a Windows Phone 7 App"
---


<p>Last weekend my <a href="http://kindohm.com/warblr">Warblr</a> app was certified and added to the Windows Phone 7 marketplace.  Developing and preparing my application took more time and effort than I estimated, and in this post I want to share my experiences along with some technical details of the application.</p>
<p>It would be helpful for you to understand what Warblr does. This screen cast helps in that regard:</p>


<object width="480" height="385">
<param name="movie" value="http://www.youtube.com/v/Oeyi3rQU-gQ?fs=1&amp;hl=en_US&amp;rel=0&amp;color1=0x2b405b&amp;color2=0x6b8ab6"></param>
<param name="allowFullScreen" value="true"></param>
<param name="allowscriptaccess" value="always"></param>
<embed src="http://www.youtube.com/v/Oeyi3rQU-gQ?fs=1&amp;hl=en_US&amp;rel=0&amp;color1=0x2b405b&amp;color2=0x6b8ab6" type="application/x-shockwave-flash" allowscriptaccess="always" allowfullscreen="true" width="480" height="385"></embed></object>

<h3>Warblr Audio - the heart of the app</h3>
<p>The audio synthesis and sound-generating features of Warblr were conceived well-before Warblr saw its first VCS commit. In fact, Warblr is  a full-featured successor to a MIX 10k coding challenge entry I posted in early 2010:  <a href="http://mix10k.visitmix.com/Entry/Details/153">http://mix10k.visitmix.com/Entry/Details/153</a>. In addition to the MIX challenge entry, I already had a fair amount of Silverlight digital audio synthesis experience under my belt. You can see examples of that work on my <a href="/code.html">Code/Programming page</a>, such as kSynth, OffTheAir, SilverlightSampler, and MicSampler.  </p>
<h3>Application Layout and Controls</h3>
<p>As you can see in the screen cast, the app uses the WP7 Pivot control. Pivot felt like a natural choice to quickly access  different groups of features. The "navigation" between Pivot child items was automatically provided using the header text links - rather than introducing my own buttons or links on the layout.</p>
<p>The app uses the Slider control extensively. The Slider control felt like the perfect choice for the type of user input I needed for the  application. I wanted users to be able to quickly set a non-exact value for features such as the volume, speed, or modulation properties. Text box input would have been completely inadequate, and "Numeric Up Down" controls didn't seem right.  Sliders inherently tell the user the minimum and maximum values they can set and they <em>should be</em> easy to manipulate.</p>
<p><strong>As much as the Slider felt right, it caused a lot of frustration and problems with user experience</strong>. If you choose to use a Slider in your WP7 app, be prepared to deal with some quirks.</p>
<p>First of all, the Slider by default does not have a "thumb position" marker. Note the following two examples where the one on the left has a Slider with no "thumb" and the Slider on the right does:</p>
<p style="text-align: center; "><img src="/hodsmedia/NoThumb.png" alt="No Thumb" style="width: 250px;margin: 10px;"/> <img src="/hodsmedia/ThumbNearMax.png" alt="Thumbs" style="width: 250px; margin: 10px;"/></p>
<p>In order for the thumbs to show up, you will need to edit the Slider's control template with a custom style. Something like this:</p>

<pre style="height: 500px;overflow: scroll;"><code>
&lt;ControlTemplate x:Key="PhoneSimpleRepeatButton" TargetType="RepeatButton"&gt;
	&lt;Rectangle Fill="Transparent"/&gt;
&lt;/ControlTemplate&gt;
&lt;ControlTemplate x:Key="PhoneSimpleThumb" TargetType="Thumb"&gt;
	&lt;Rectangle Fill="Transparent"/&gt;
&lt;/ControlTemplate&gt;
&lt;ControlTemplate x:Key="VerticalThumb" TargetType="Thumb"&gt;
	&lt;Rectangle Fill="{StaticResource PhoneForegroundBrush}"/&gt;
&lt;/ControlTemplate&gt;
&lt;Style x:Key="SynthSlider" TargetType="Slider"&gt;
	&lt;Setter Property="Height" Value="500"/&gt;
	&lt;Setter Property="Orientation" Value="Vertical"/&gt;
	&lt;Setter Property="BorderThickness" Value="0"/&gt;
	&lt;Setter Property="BorderBrush" Value="Transparent"/&gt;
	&lt;Setter Property="Maximum" Value="10"/&gt;
	&lt;Setter Property="Minimum" Value="0"/&gt;
	&lt;Setter Property="Value" Value="0"/&gt;
	&lt;Setter Property="Background" Value="{StaticResource PhoneContrastBackgroundBrush}"/&gt;
	&lt;Setter Property="Foreground" Value="{StaticResource PhoneAccentBrush}"/&gt;
	&lt;Setter Property="Template"&gt;
		&lt;Setter.Value&gt;
			&lt;ControlTemplate TargetType="Slider"&gt;
				&lt;Grid Background="Transparent"&gt;
					&lt;VisualStateManager.VisualStateGroups&gt;
						&lt;VisualStateGroup x:Name="CommonStates"&gt;
							&lt;VisualState x:Name="Normal"/&gt;
							&lt;VisualState x:Name="MouseOver"/&gt;
							&lt;VisualState x:Name="Disabled"&gt;
								&lt;Storyboard&gt;
									&lt;DoubleAnimation Duration="0" To="0.1" Storyboard.TargetProperty="Opacity" Storyboard.TargetName="HorizontalTrack"/&gt;
									&lt;DoubleAnimation Duration="0" To="0.1" Storyboard.TargetProperty="Opacity" Storyboard.TargetName="VerticalTrack"/&gt;
									&lt;ObjectAnimationUsingKeyFrames Storyboard.TargetProperty="Fill" Storyboard.TargetName="HorizontalFill"&gt;
										&lt;DiscreteObjectKeyFrame KeyTime="0" Value="{StaticResource PhoneDisabledBrush}"/&gt;
									&lt;/ObjectAnimationUsingKeyFrames&gt;
									&lt;ObjectAnimationUsingKeyFrames Storyboard.TargetProperty="Fill" Storyboard.TargetName="VerticalFill"&gt;
										&lt;DiscreteObjectKeyFrame KeyTime="0" Value="{StaticResource PhoneDisabledBrush}"/&gt;
									&lt;/ObjectAnimationUsingKeyFrames&gt;
								&lt;/Storyboard&gt;
							&lt;/VisualState&gt;
						&lt;/VisualStateGroup&gt;
					&lt;/VisualStateManager.VisualStateGroups&gt;
					&lt;Grid x:Name="HorizontalTemplate" Margin="{StaticResource PhoneHorizontalMargin}"&gt;
						&lt;Grid.ColumnDefinitions&gt;
							&lt;ColumnDefinition Width="Auto"/&gt;
							&lt;ColumnDefinition Width="0"/&gt;
							&lt;ColumnDefinition Width="*"/&gt;
						&lt;/Grid.ColumnDefinitions&gt;
						&lt;Rectangle x:Name="HorizontalTrack" Grid.ColumnSpan="3" Fill="{TemplateBinding Background}" Height="12" IsHitTestVisible="False" Margin="0,22,0,50" Opacity="0.2"/&gt;
						&lt;Rectangle x:Name="HorizontalFill" Grid.Column="0" Fill="{TemplateBinding Foreground}" Height="12" IsHitTestVisible="False" Margin="0,22,0,50"/&gt;
						&lt;RepeatButton x:Name="HorizontalTrackLargeChangeDecreaseRepeatButton" Grid.Column="0" IsTabStop="False" Template="{StaticResource PhoneSimpleRepeatButton}"/&gt;
						&lt;RepeatButton x:Name="HorizontalTrackLargeChangeIncreaseRepeatButton" Grid.Column="2" IsTabStop="False" Template="{StaticResource PhoneSimpleRepeatButton}"/&gt;
						&lt;Thumb x:Name="HorizontalThumb" Grid.Column="1" Margin="-1,0,0,0" RenderTransformOrigin="0.5,0.5" Template="{StaticResource PhoneSimpleThumb}" Width="1"&gt;
							&lt;Thumb.RenderTransform&gt;
								&lt;ScaleTransform ScaleY="1" ScaleX="32"/&gt;
							&lt;/Thumb.RenderTransform&gt;
						&lt;/Thumb&gt;
					&lt;/Grid&gt;
					&lt;Grid x:Name="VerticalTemplate" Margin="{StaticResource PhoneVerticalMargin}"&gt;
						&lt;Grid.RowDefinitions&gt;
							&lt;RowDefinition Height="*"/&gt;
							&lt;RowDefinition Height="0"/&gt;
							&lt;RowDefinition Height="Auto"/&gt;
						&lt;/Grid.RowDefinitions&gt;
						&lt;Rectangle x:Name="VerticalTrack" Fill="{TemplateBinding Background}" IsHitTestVisible="False" Margin="12,0" Opacity="0.2" Grid.RowSpan="3" Width="12"/&gt;
						&lt;Rectangle x:Name="VerticalFill" Fill="{TemplateBinding Foreground}" IsHitTestVisible="False" Margin="12,0" Grid.Row="2" Width="12"/&gt;
						&lt;RepeatButton x:Name="VerticalTrackLargeChangeDecreaseRepeatButton" IsTabStop="False" Grid.Row="0" Template="{StaticResource PhoneSimpleRepeatButton}"/&gt;
						&lt;RepeatButton x:Name="VerticalTrackLargeChangeIncreaseRepeatButton" IsTabStop="False" Grid.Row="2" Template="{StaticResource PhoneSimpleRepeatButton}"/&gt;
						&lt;Thumb x:Name="VerticalThumb" Height="1" Margin="0,-1,0,0" Grid.Row="1" RenderTransformOrigin="0.5,0.5" Template="{StaticResource VerticalThumb}"&gt;
							&lt;Thumb.RenderTransform&gt;
								&lt;ScaleTransform ScaleY="32" ScaleX="1"/&gt;
							&lt;/Thumb.RenderTransform&gt;
						&lt;/Thumb&gt;
					&lt;/Grid&gt;
				&lt;/Grid&gt;
			&lt;/ControlTemplate&gt;
		&lt;/Setter.Value&gt;
	&lt;/Setter&gt;
&lt;/Style&gt;
</code></pre>



<p>Personally I think it is mind-boggling why Microsoft did not include a thumb marker in the default Slider style.</p>
<p>Dave Relyea has also <a href="http://blogs.msdn.com/b/devdave/archive/2010/10/04/a-better-slider-for-windows-phone-7.aspx"> posted about this issue</a> with Sliders and he offers up a nice alternative, but to be honest I was never able to get it  to work very well. I just couldn't get the Slider to respond very accurately.</p>
<p>Even with my own template I still had one big problem. The thumb was being clipped off when it was scrolled to the  maximum value. Notice in these two examples what the thumb rectangle looks like when it is 1) near the maximum and 2) at the  maximum:</p>
<p style="text-align: center;"> <img src="/hodsmedia/ThumbNearMax.png" alt="thumb near max" style="margin: 10px; width:250px;" /> <img src="/hodsmedia/ThumbMax.png" alt="thumb at max" style="margin: 10px; width:250px;" /> </p>
<p>By the time I had gotten this far I was cursing at Microsoft for shipping such an awful control with a default style that  didn't seem to suit default needs.</p>
<p>To fix the clipping problem, I gave in to the crusty idea of limiting the maximum value that could be set in the ViewModel property that the Slider value was bound to:</p>

	public ushort NoteLength
	{
		get { return this.source.Tempo; }
		set
		{
			if (value < MaxNoteLength)
				this.source.Tempo = value;
			else
				this.source.Tempo = MaxNoteLength;
			this.OnPropertyChanged("NoteLength");
		}
	}

<p>In the snippet above, <code>MaxNoteLength</code> is slightly less than the maximum value of the "Note Length" Slider. This data binding property validation trick is the only way I could prevent the thumb clipping. With all of my XAML and Blend know-how I could not resolve this clipping issue without code.</p>
<p>There was <em>still</em> another Slider issue to deal with. Unless the user's thumb (or finger) slid the Slider in an exact vertical gesture, the Pivot control would take over and stroll to the previous or next Pivot child item. I resolved this by simply handling the <code>ManipulationStarted</code> and <code>ManipulationCompleted</code> events of the Sliders and  toggling the Pivot control's <code>IsHitTestVisible</code> property (hat tip to <a href="http://twitter.com/DarthObiwan">@DarthObiwan</a>):</p>

	void SliderManipulationStarted(object sender, ManipulationStartedEventArgs e)
	{
		MainPivot.IsHitTestVisible = false;
	}

	void SliderManipulationCompleted(object sender, ManipulationCompletedEventArgs e)
	{
		MainPivot.IsHitTestVisible = true;
	}

<p>Whew. That about did it for the UI decisions and issues I had to deal with.</p>
<h3>Twitter Integration</h3>
<p>With Warblr you can sign in to Twitter and post some custom Warblr text to your account (as seen in the screen cast). The Twitter oAuth integration looked like a daunting task at first. However, I came across Sudhindra Kovalam's  <a href="http://sudheerkovalam.wordpress.com/2010/09/05/a-windows-phone-twitter-application-part-2-of-2/">post on wiring up Twitter oAuth with a Windows Phone 7 app</a>. That post makes the task very, very easy. Sudhindra makes use of some trickery with the WebBrowser control to make the Twitter authentication experience smooth.</p>
<h3>The Certification Gauntlet</h3>
<p>If you're serious about developing and submitting a WP7 app to the marketplace, I urge you to become very familiar with their  certification guidelines: <a href="http://go.microsoft.com/?linkid=9730558">http://go.microsoft.com/?linkid=9730558</a>. It's 27 pages and a dry read but well worth your time. The guidelines cover everything from what .NET namespaces you can use to what content is considered vulgar (and therefore not allowed).</p>
<p>In terms of calendar days, I feel like I spent just as much time making sure my app met the marketplace's certification requirements and dealing with certification failures as I did writing all the code before certification.  My app failed certification three times because of three problems:</p>
<ol>
<li>I didn't prompt the user to ask if it was ok to stop playing their music</li>
<li>I didn't provide visual feedback indicating that my app wasn't "hanging"</li>
<li>I didn't provide a feature to control the volume</li>
</ol>
<p>For that first issue, devs are required to prompt the phone user and ask if it is ok to stop music that is playing from their phone's media library before the app starts making its own music or audio. If your app just plays sound effects, it's ok to let the user's music keep playing. However, if your app plays its own soundtrack or lengthy audio, you must check with the user first to make sure that's ok - but only if they already have music playing.</p>
<p>For the second issue, I had a number of operations that went out to the internet to get data or perform a task (e.g. search Twitter, oAuth calls, and posting to Twitter). For these types of operations you need to tell the user that the app isn't hanging or crashing. Something like an indeterminate progress bar does the trick, but the specific feedback you give is up to you. My certification failed because I didn't have any feedback displayed for the oAuth operation.</p>
<p>For the volume issue, it's exactly as I said: if your audio plays its own music you must include a feature for the user to control the volume in the app. This means that the phone device's volume buttons are not enough.</p>
<p>Microsoft will only tell you about one fail reason or bug with each certification failure. They don't thoroughly test your app and tell you <em>everything</em> that they found. As soon as they encounter one issue they stop and return you the single fail reason. Microsoft's test result reports include good feedback for how to reproduce the issues, and for that I am grateful.</p>
<p>Had I understood the certification requirements better before attempting to submit to the marketplace I would have been able to get certified the first time I tried. I think that the certification process is great for maintaining  high quality and UI consistency in all WP7 apps. Kudos to Microsoft on the certification process.</p>
<h3>Media Complications</h3>
<p>If you want to interact with <em>any</em> aspect of the phone's media library, which includes music and pictures, you will need to use the WPConnect tool to debug your app from Visual Studio. Without WPConnect, you cannot generate your own sounds or work with phone tasks such as taking a picture. I  <a href="http://kindohm.com/2010/11/29/WP7Prep.html">blogged about using WPConnect.exe</a> recently. </p>
<p>Another media issue I encountered was related to issue #1 above in the certification failures. I found that by merely checking the state of the phone's media player I was unable to play audio in my app when debugging. My audio code didn't even get invoked. If I commented out the check, I could play audio in my app when debugging. To check the media player's state, you can inspect the  static <code>State</code> property of the <code>Microsoft.XNA.Framework.Media.MediaPlayer</code> class. It may return an enumeration value of Playing, Stopped, or Paused. Note that this class is an <em>XNA</em> class. For Warblr, I'm using a <em>Silverlight</em> class called <code>MediaStreamSource</code> to play audio. My suspicion is that there is some low-level incompatibility between using the XNA and Silverlight media classes at the same time to inspect and control the phone's media. As you can imagine, this specific problem makes it impossible to debug any of my code that generates audio. If I can figure out the real cause and resolution to this I will certainly write about in the future.</p>
<h3>Final Thoughts</h3>
<p>Overall my experience with the WP7 dev tools has been great. Visual Studio, the emulator, and the WPConnect.exe tool have given me no headaches yet. There are issues with the Slider and the XNA/Silverlight media conflicts (as I already mentioned above) though. The media conflicts worry me a bit as I'm sure I'll work with audio again on WP7. As for the device itself and developer API limitations... that's out of scope for this post! </p>
<p>I have an idea in mind for another app that will hopefully be fun to use. Given what I now know about the certification guidelines the next app should be able to get through certification more quickly but the development will probably take a bit longer.</p>


