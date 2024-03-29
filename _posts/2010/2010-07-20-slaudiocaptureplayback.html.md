---
layout: post
title: "Capture and Play Back Audio with a Microphone in Silverlight"
---
  
<p><a href="https://github.com/kindohm/silverlight-mic-capture" target="_blank">View/download the source code</a></p>
  
<p><img src="/hodsmedia/838893081_1.png" alt=""/></p>

<p>In a previous post I talked about capturing video from a web cam in Silverlight. In this article I&#8217;m going to talk about capturing <em>audio</em>. In addition, I&#8217;m going to cover how to keep the captured audio in memory and play it back using a custom MediaStreamSource object.</p>


<p>Just like with video, capturing audio begins with a CaptureSource and an AudioCaptureDevice:</p>



  <script src="http://gist.github.com/483048.js?file=gistfile1.cs"></script>
<p>The above code assumes you&#8217;ve gained permission from the user to use the device.</p>


<p>As you can expect, the CaptureDeviceConfiguration class has a GetAvailableAudioCaptureDevices method that you can use to get all audio devices on the user&#8217;s computer.</p>


  
<p>Calling Start() on the CaptureSource begins the capture process, but it doesn&#8217;t actually store the audio data anywhere. That is where a handy little class in Silverlight called AudioSink comes in. AudioSink is a class you use to handle incoming audio samples. It is an abstract class so you will need to create your own custom implementation and override four abstract methods: OnCaptureStarted, OnCaptureStopped, OnFormatChanged, and OnSamples:</p>

  <script src="http://gist.github.com/483055.js?file=gistfile1.cs"></script>
<p>To use a custom AudioSink, you wire it up to a CaptureSource by setting the AudioSink&#8217;s CaptureSource property equal to a CaptureSource instance:</p>


  <script src="http://gist.github.com/483079.js?file=gistfile1.cs"></script>
<p>In the most simple form you can just create a custom AudioSink that captures the samples in a MemoryStream. You can optionally read this stream into a byte array if you so choose:</p>


  <script src="http://gist.github.com/483063.js?file=gistfile1.cs"></script>
<p>At this point, you&#8217;re <em>almost</em> ready to start playing back the audio you&#8217;ve captured. However, we cannot ignore the OnFormatChanged method and the audio format of the incoming data.</p>


<p>The AudioCaptureDevice class has a SupportedFormats property which is a collection of AudioFormat objects. AudioFormat describes the samples per second, bits per sample, and number of channels for the captured audio. These properties are <em>crucial</em> in being able to correctly play back your audio. AudioCaptureDevice also has a DesiredFormat property. When set, DesiredFormat will use that format to capture audio.</p>


<p>To take a step back here - you have an available set of AudioCaptureDevices, and each device has a set of SupportedFormats. You will either need to use a set of defaults or maintain two lists (one dependent on the other) for the user to pick from.</p>

  
<p>OnFormatChange in the AudioSink class gets called for the first time just before the samples start rolling in. However, it is possible that it may get called again. In advanced scenarios you may want to support a changed audio format. I just prefer to throw an exception in my AudioSink and not deal with it. My app must then be constrained to prevent the user from changing the audio format at capture time.</p>

  
<p>Once the audio format is set, you can keep this format stored in the AudioSink for future use:</p>

  <script src="http://gist.github.com/483074.js?file=gistfile1.cs"></script>
<p>When you call Stop() on CaptureSource, recording will stop and the AudioSink&#8217;s OnCaptureStopped method will get called.</p>


<p>Next we need to deal with playing back the audio. You will need to create a custom MediaStreamSource object that can describe the media stream in terms of the audio format captured in the AudioSink. Then, you will use that MediaStreamSource to send out the audio samples captured in the AudioSink. Basically you&#8217;ll want to pass in the AudioFormat and byte[] data you captured in your AudioSink into your custom MediaStreamSource.</p>


<p>I don&#8217;t want to get too deep into the MediaStreamSource implementation because <em>so much</em> of it is verbose boilerplate code. However, your constructor, OpenMediaAsync(), and GetSampleAsync() methods for the MediaStreamSource will look something like this:</p>


  <script src="http://gist.github.com/483090.js?file=gistfile1.cs"></script>
<p>The above code is incomplete but it should give you a taste of what you need to do with a MediaStreamSource. There are plenty of resources out there on how to set up and use MediaStreamSource objects. The above code basically starts at the beginning of the audio data and plays through to the end, then starts at the beginning again. The source code example with this post has a complete implementation.</p>

<p>One quirk I&#8217;ve found while having a few friends test out this demo app is that audio recording and playback seem to fail if no default audio capture device exists within the Silverlight configuration. This is an operating-system level configuration setting and not an application setting. If this scenario occurs, it can be remedied by having the user right-click on the Silverlight application, select &#8220;Silverlight&#8221;, then select the Webcam/Mic tab. From there, the user can select an audio capture device as their &#8220;default&#8221; on their operating system. This seems to clear up the problem (so far).</p>


<p>After you get these mechanics working, you can slap on a fun UI and start hacking with samples!</p>

<p>Enjoy!</p>

