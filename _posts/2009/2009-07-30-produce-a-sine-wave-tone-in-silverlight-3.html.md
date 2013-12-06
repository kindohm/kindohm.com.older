---
layout: post
title: "Produce a Sine Wave Tone in Silverlight 3"
---

<p>Source Code: <a href="http://www.kindohm.com/files/Sine.zip" target="_blank">Sine.zip</a> (12 kb)</p>

<p>I haven't really found a nuts-and-bolts explanation of how to produce (synthesize) sound with Silverlight 3.  Through trial and error and referencing other more complex examples I was able to finally figure it out.  My hope here is to lay things out so that folks can get their hands on the "boilerplate" Silverlight 3 code required to get an audio stream working and how to also assemble the audio stream bytes into something coherent (such as a sine wave).</p>

<p>First, let's talk about the definition of the audio stream.  The audio stream has a few important characteristics that will influence how the sine wave tone is generated:</p>

<ul>
<li>Sample - a single point in the audio stream.  </li>
<li>Channel Count - this defines whether the sound is in mono or stereo.  One channel = mono, two channels = stereo. </li>
<li>Bits Per Sample - this defines the granularity of each sample calculation in your audio.  16 bits is typical.  You could perform calculations of 8 bits or 24 bits per sample if you'd like.  16 bits equals two bytes - so that'd be two bytes per sample.  </li>
<li>Sample Rate - CD-quality audio is 44100 samples per second.  </li>
<li>Byte Rate - this is the speed at which bytes should be read from the stream.  It is a function of the sample rate, channel count, and bits per sample. </li>
</ul>
<p>The "boilerplate" audio stream code that sets up these parameters isn't something I want to spend much time on.  There are a number of examples out there that explain it.  You can reference <a href="http://community.irritatedvowel.com/blogs/pete_browns_blog/archive/2009/03/23/Creating-Sound-using-MediaStreamSource-in-Silverlight-3-Beta.aspx" target="_blank">Pete Brown's article</a> or <a href="http://blogs.msdn.com/gillesk/archive/2009/03/23/playing-back-wave-files-in-silverlight.aspx" target="_blank">Gilles Khouzam's example</a> for how to get started.  Basically, you need to create a custom class that derives from System.Windows.Media.MediaStreamSource.  In this class you will set up the definition of your audio stream when overriding the OpenMediaAsync() method.  This will include how many channels the audio has, the sample rate, and the bits per sample.</p>



<p>Understand that I'm breezing through the <em>implementation</em> of this code - not the importance of it.  </p>



<p>The meat of your audio-producing code is in your custom MediaStreamSource's GetSampleAsync() method.  It will look something like this:</p>



	protected override void GetSampleAsync(
		MediaStreamType mediaStreamType)
	{
		int numSamples = 128; //a number you choose...
		int bufferByteCount = channelCount * 
			BitsPerSample / 
			8 * numSamples;

		//WRITE DATA TO MEMORY STREAM HERE...
		//memoryStream.WriteByte(...);

		//emptySampleDict set up in boilerplate code...
		MediaStreamSample mediaStreamSample =
			new MediaStreamSample(
				mediaStreamDescription, 
				memoryStream, 
				currentPosition,
				bufferByteCount, 
				currentTimeStamp, 
				emptySampleDict);  

		//typical code for stream position/seeking:
		currentTimeStamp += bufferByteCount * 
			10000000L / byteRate;
		currentPosition += bufferByteCount;
		ReportGetSampleCompleted(mediaStreamSample);
	}


<p>Again, this is pretty much "boilerplate" code, but let's stop and take a look at a few things here.  First, the numSamples field.  In my example above, it is set to a value of 128.  In theory, this value can be set to almost any positive integer value.  The purpose of this value though is to control how many samples are written to the memory stream whenever the Silverlight client app requests more samples.  It really comes down to how responsive you want your audio stream to be, or how much of a "buffer" your code needs to catch up.  A larger value will allow your code to load more data into the stream and it won't have to execute as often.  In my experience, a value between 128 and 384 seems to work well.  You'll need to find a good value that works for your own code.</p>



<p>Next, there is all the code below the "//WRITE DATA TO MEMORY STREAM HERE" comment.  All that code below is also "boilerplate" code that just sends your memory stream out to the client.  </p>



<p>The real question is: what bytes do you write to the memory stream?</p>



<p>To begin answering this question, we need to refer back to the audio stream definition.  Specifically, the bits per sample and number of channels.  </p>



<p>Let's say we have a single-channel (a.k.a. mono) audio stream with 16 bits per sample.  That means that each sample you write will contain two bytes.  An Int16 or "short" value type is just this size - so you can actually use an Int16 as the basis for audio calculations.  Once you calculate an Int16 value, you can write it's two bytes of data out to the stream.  However, you'll need to reverse the byte order of the Int16 value to make sure Silverlight consumes the stream happily.  The code below shows you how:</p>



	short myValue = 12345;
	memoryStream.WriteByte(
		(byte)(myValue &amp; 0xFF)); //2nd byte
	memoryStream.WriteByte(
		(byte)(myValue >> 8)); //1st byte
<p>Now let's change this up and add a 2nd channel to make it a stereo signal.  For a 2-channel stream, you write one 16-bit sample first for the left channel, then write another 16-bit sample for the right channel.  If you want the same sound to be played in both the left and right channels, you can just write the same data twice:</p>



	short myValue = 12345;
	memoryStream.WriteByte(
		(byte)(myValue &amp; 0xFF)); //2nd byte left
	memoryStream.WriteByte(
		(byte)(myValue >> 8)); //1st byte left
	memoryStream.WriteByte(
		(byte)(myValue &amp; 0xFF)); //2nd byte right
	memoryStream.WriteByte(
		(byte)(myValue >> 8)); //1st byte right

<p>Referring back to the GetSampleAsync() method, you'll wrap the code above in your loop that executes for the number of samples you choose:</p>



	int numSamples = 128;
	for (int i = 0; i < numSamples; i++)
	{
		short sample = this.GetNextSampleFromSomewhere();

		stream.WriteByte(
			(byte)(sample &amp; 0xFF));
		stream.WriteByte(
			(byte)(sample >> 8));
	}

<p>Now, the next big question: how do you calculate the sample for a sine wave?</p>



<p>I fear I might be going into a little too much detail here, but in order to answer that question I want to cover some basic trigonometry.  You're still friends with ?, right?</p>



<p>A sine wave is a function of a radian value.  If you look at a <a href="http://en.wikipedia.org/wiki/Unit_circle" target="_blank">Unit Circle</a>, it is made up of 2 x pi radians (360 degrees = 2 x pi, 180 degrees = pi).  The unit circle below shows some example points in degrees and the corresponding radian and sin values:</p>



<p><a href="http://www.kindohm.com/LocalImages/posts/ProduceaSineWaveToneinSilverlight3_D128/unitcircle_examples.png"><img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="unitcircle_examples" border="0" alt="unitcircle_examples" src="http://www.kindohm.com/LocalImages/posts/ProduceaSineWaveToneinSilverlight3_D128/unitcircle_examples_thumb.png" width="463" height="470" /></a> </p>



<p>Plotting the Sin values at increments of .25 radians for two passes around the unit circle produces this:</p>



<p><a href="http://www.kindohm.com/LocalImages/posts/ProduceaSineWaveToneinSilverlight3_D128/singraph.png"><img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="singraph" border="0" alt="singraph" src="http://www.kindohm.com/LocalImages/posts/ProduceaSineWaveToneinSilverlight3_D128/singraph_thumb.png" width="504" height="350" /></a> </p>



<p>Now, given how a Sin function works, and given what you now know about producing Int16 value samples in the audio stream, producing sine wave samples is as simple as computing the next angle (a.k.a. "phase") at which to calculate a Sin value.  </p>



<p>Next you need to understand what a Frequency (in Hertz, or Hz) is.  A musical tone plays at a certain Frequency.  For example, a string orchestra tunes to a Frequency of 440 Hz, or an "A".  The Frequency is the number of times per second that a full unit circle rotation is made.  In other words, when an oboe plays an "A" for an orchestra to tune to, 440 full cycles of the unit circle are completed per second.  </p>



<p>In an audio stream we know the sample rate (e.g. 44100 samples/second).  Therefore, based on the frequency, we can calculate how many radians we should increment by each time we send out a sample.  In other words, each sample we send out will be a calculation based on the unit circle, and we'll be moving along the unit circle a little bit each for each sample.</p>



<p>The formula for determining the phase angle increment per sample is this (taken from <a href="http://www.charlespetzold.com/blog/2009/07/Simple-Electronic-Music-Sequencer-for-Silverlight.html" target="_blank">Charles Petzold's example</a>):</p>



<blockquote>
<p>Increments per Sample =      <br />Frequency (cycles/second) *       <br />Increments per Cycle /       <br />Sample Rate</p>



</blockquote>
<p>If you write out the calculation on paper and do the cancellations by hand, you'll see that you will end up with Increments per Sample.  </p>



<p><em>Increments per Cycle</em> can really be anything you want - but a good choice would be some nice round binary number, like an unsigned Int16.MaxValue.  Since we're dealing with two-byte Int16 values already, UInt16.MaxValue (65,534) will work perfectly.  That means we'll have 65,534 points on the unit circle to increment by.  </p>



<p>Example, with 440 Hz Frequency:</p>



<p>Increment Per Sample =    <br />440 * 65,534 / 44100 = 653.85 </p>



<p>Thus, for a 400 Hz frequency, we'll need to increment our phase angle, based on a UInt16 maximum value, by about 654 units.</p>



<p>So how does this work in terms of the Sin calculation?  Each increment of the unit circle is a ratio of the total cycle.  So each 654 increment represents 654/65,534 or about 0.99% around the circle.  If we multiply two-pi by that ratio, that will give us the number of radians at our sample point.  Then we just take the Sin of those radians, then finally multiply the Sin result by Int16.MaxValue (32,767) to get the final result.  We multiply by 32,767 because the Sin result is just a fraction of where we're at in the wave cycle - with the full possible value being equal to 1.00.  32,767 puts it in terms of our largest possible Int16 value - which is our sample size.</p>



<p>Here is an example C# class that does all of this:</p>



	public class SineWave{
		const int SampleRate = 44100;
		ushort phaseAngle;
		double frequency;
		ushort phaseAngleIncrement;

		public SineWave(){
			this.frequency = 440d;
			phaseAngleIncrement = (ushort)(frequency 
				* ushort.MaxValue / SampleRate);
		}

		short GetNextSample(){
			short sample = (short)(short.MaxValue * 
				Math.Sin(
					2 * Math.PI / ushort.MaxValue 
					* this.phaseAngle));
			this.phaseAngle += this.phaseAngleIncrement;
			return sample;
		}

		protected override void WriteSamplesToStream(
			MemoryStream stream, int numSamples){
			for (int i = 0; i < numSamples; i++)
			{
				short sample = this.GetNextSample();

				//single, mono channel
				stream.WriteByte(
					(byte)(sample &amp; 0xFF));
				stream.WriteByte(
					(byte)(sample >> 8));
			}
		}
	}

<p>A final implementation of a custom MediaStreamSource class's GetSampleAsync() method may look something like this:</p>



	protected override void GetSampleAsync(
		MediaStreamType mediaStreamType)
	{
		int numSamples = 128;
		int bufferByteCount = this.channelCount * 
			BaseMediaStreamSource.BitsPerSample / 
			8 * numSamples;

		mySineWave.WriteSamplesToStream(this.memoryStream, numSamples);

		MediaStreamSample mediaStreamSample =
			new MediaStreamSample(mediaStreamDescription, 
				memoryStream, currentPosition,
				bufferByteCount, currentTimeStamp, 
				emptySampleDict);

		currentTimeStamp += bufferByteCount * 
			10000000L / byteRate;
		currentPosition += bufferByteCount;

		ReportGetSampleCompleted(mediaStreamSample);
	}

<p>Once you implement a custom MediaStreamSource and some sine wave generating code, you can create an instance of the custom MediaStreamSource in Silverlight and call the SetSource() method of a MediaElement to start playing your sine wave.</p>



<p>Happy noise-making!</p>


