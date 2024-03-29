---
layout: post
title: "Additive-Harmonics: an interactive audio visualization"
---

<p>I recently published an open-source sample project on GitHub called <a href="http://github.com/kindohm/additive-harmonics">additive-harmonics</a>. </p>
<p>Try it out now! Go to <a href="http://kindohm.github.com/additive-harmonics/">http://kindohm.github.com/additive-harmonics/</a></p>

<p><strong>EDIT:</strong> the audio features in the example above will only work (as of today) in Firefox, Chrome, or Safari. Internet Explorer and Opera do not support the HTML5 audio API required to play the sample.</p>
<p>The project is an idea I've had for a while: create a plugin-less web page where you can adjust the level of harmonics in a multi-wave tone, view the resulting wave form on a graph, and also hear the resulting wave form through your speakers:</p>
<p><a href="/hodsmedia/sawtooth.png"><img src="/hodsmedia/sawtooth.png" alt="sawtooth" title="sawtooth"  class="alignnone size-full wp-image-1346" /></a></p>
<p><a href="/hodsmedia/square.png"><img src="/hodsmedia/square.png" alt="square" title="square"  class="alignnone size-full wp-image-1347" /></a></p>
<p>If you are interested in how this all works, then head on over to the <a href="http://github.com/kindohm/additive-harmonics">additive-harmonics repo</a> to take a look. If you want a more verbose explanation of some of the interesting parts of the app, then read on.</p>
<h3>The Plotter API</h3>
<p>To start this whole project, I wrote some tests for the plotter API using <a href="http://pivotal.github.com/jasmine/">Jasmine</a>. The "plotter" is what calculates function values, translates those values to the right place for an HTML5 canvas, and then draws the translated values on the canvas. In my test code, you can see how the API is used:</p>

<p>
<pre><code>var calculator1 = Object.create(additive.Calculator);
calculator1.calculate = function (x) {
	return 3 * x + 2;
};

var calculator2 = Object.create(additive.Calculator);
calculator2.calculate = function (x) {
	return -2 * x;
};

var plotter = Object.create(additive.Plotter);
var width = 400;
var height = 300;
var result = plotter.generatePlots( [calculator1, calculator2], 
    width, height);

// expect a plot for each calculator
expect(result.plots.length).toBe(2);</code></pre></p>
<p>The two functions above are linear functions that produce straight lines (and are very easy to test).</p>
<p>You can see the use of <code>additive.*</code> here. <code>additive</code> is the name of the module that wraps the API. In the code above, two <code>Calculator</code> objects are created with custom calculation functions. These calculators are fed into a <code>Plotter</code> object's <code>generatePlots</code> method. <code>generatePlots</code> produces an array of plots and a sum of all the plots. A "plot" is basically an object that wraps an array of points.</p>
<p>Continuing with the test code, we can verify that the plots have correct function values calculated for each value of x:</p>

<p>
<pre><code>var plot1 = result.plots[0];
expect(plot1.points[0]).toBe(2);
expect(plot1.points[1]).toBe(5);
expect(plot1.translatedPoints[0]).toBe(148);
expect(plot1.translatedPoints[1]).toBe(145);

var plot2 = result.plots[1];
expect(plot2.points[0]).toBe(0);
expect(plot2.points[1]).toBe(-2);
expect(plot2.translatedPoints[0]).toBe(150);
expect(plot2.translatedPoints[1]).toBe(152);</code></pre></p>
<p>The <code>translatedPoints</code> from the plotter are the functions translated to plot correctly on a canvas. Since we're dealing with sine functions, we will need to support showing both positive and negative y values. This means the x-axis will need to be in the vertical center of the canvas. The plotter adds <code>canvas.height / 2</code> to the value of all the points to accomplish this. In addition, the y-axis is flipped since the canvas draws from the top down. Here is the plotter's translation code:</p>

<p>
<pre><code>var translatePoints = function (points, height) {
	var i;
	var newPoints = new Array(points.length);
	var halfHeight = height / 2;
	for (i = 0; i &lt; points.length; i++){
		newPoints[i] = -points[i] + halfHeight;
	}
	return newPoints;
};
</code></pre></p>
<p>Last, in the test we make sure the results from the two functions were summed correctly:</p>

<p>
<pre><code>expect(result.sum.length).toBe(width);
expect(result.sum[0]).toBe(2);
expect(result.sum[1]).toBe(3);

expect(result.translatedSum.length).toBe(width);
expect(result.translatedSum[0]).toBe(148);
expect(result.translatedSum[1]).toBe(147);
</code></pre></p>
<p>In another test, I wanted to test the plotter to make sure it draws to the canvas. However, this isn't possible to do directly:</p>

<p>
<pre><code>var canvas = document.createElement('canvas');
canvas.width = width;
canvas.height = height;
document.body.appendChild(canvas);
plotter.draw(result, canvas, width, height);
expect(canvas != null).toBe(true);

</code></pre></p>
<p>As you can see, I'm only testing to expect that the <code>canvas</code> is not null. It isn't possible to extract line data from the canvas and verify it against expected results. This could be mitigated with some kind of canvas drawing proxy, but I didn't do that in this project.</p>
<h3>Oscillator Calculations</h3>
<p>On the consumer side of the <code>Plotter</code> I have what I call a <code>ViewModel</code>. The <code>ViewModel</code> relies on a basic <code>Harmonic</code> model to define a frequency and amplitude:</p>

<p>
<pre><code>var harmonic = {
	frequency: 1,
	amplitude: 1
}
</code></pre></p>
<p>The <code>ViewModel</code> finally ties all of the <em>real</em> math together by using some <code>audiolib.js</code> oscillators for the calculation functions:</p>

<p>
<pre><code>for (var i = 0; i &lt; this.harmonics.length; i++){
	var harmonic = this.harmonics[i];

	// create an audiolib.js oscillator. set its
	// sample rate equal to the canvas width so that
	// it starts and ends at the canvas bounds.
	var osc = audioLib.Oscillator(width, harmonic.frequency);

	// create a calculator object with a custom calculation
	// function for this specific oscillator.
	var calc = Object.create(module.Calculator, { 
		calculate: { 
			value: function (x) { 
				this.oscillator.generate();
				var sample = this.oscillator.getMix() * this.amplitude;
				return sample;
			} 
		},
		oscillator:	{
			value: osc
		},
		amplitude: {
			value: harmonic.amplitude
		}
	});

	calculators.push(calc);
}

var result = plotter.generatePlots(calculators, width, height);
plotter.draw(result, canvas, width, height);</code></pre></p>
<p>Notice my use of <code>Object.create(...)</code> and property descriptors. I realize other popular JavaScript frameworks out there make it easier to create objects based on other prototype objects than how I'm doing it with property descriptors, but I didn't see the need to introduce a larger framework for such a small project like this.</p>
<h3>Playing Audio</h3>
<p>Whenever the UI changes something in the harmonics, the <code>ViewModel</code> is asked to update the audio source:</p>

<p>
<pre><code>viewModel.updateAudio = function (){
	
	var createOscillators = oscillators.length === 0;
	var i, oscillator, harmonic;

    // constant value to base everything off of 400 Hz
	var frequencyConstant = 400 / module.startHz;

	for (i = 0; i &lt; this.harmonics.length; i++){
		harmonic = this.harmonics[i];
		if (createOscillators) {
			oscillator = audioLib.Oscillator(
                44100, harmonic.frequency * frequencyConstant);
			oscillators.push(oscillator);
		} 
		else {
			oscillator = oscillators[i];
		}
		oscillator.amplitude = 
            harmonic.amplitude / module.amplitudeScale;
	}
};
</code></pre></p>
<p>The <code>ViewModel</code> maintains a list of oscillators. When an update is requested, it creates the oscillators if they don't exist. To play the audio, it's just boilerplate audiolib.js code:</p>

<p>
<pre><code>var audioCallback = function (buffer, channelCount){
	if (!playing || oscillators.length === 0) return;
	var length = buffer.length,
		oscLength = oscillators.length,
		sample, 
		note, 
		channel, 
		current, 
		oscIndex, 
		oscillator;

	// loop through the buffer length. skip every other sample
	// if this is two channels (stereo);
	for (current = 0; current &lt; length; current += channelCount){					
		sample = 0;

		// loop through oscillators
		for (oscIndex = 0; oscIndex &lt; oscLength; oscIndex++){
			oscillator = oscillators[oscIndex];

			// generate oscillator sample and add it to the current
			// sample value.
			oscillator.generate();
			sample += oscillator.getMix() * oscillator.amplitude;
		}

		// Fill buffer for each channel
		for (channel = 0; channel &lt; channelCount; channel++){
			buffer[current + channel] = sample;
		}		
	}	
};</code></pre></p>
<h3>Bootstrapper</h3>
<p>Last, there is some bootstrap code that kicks everything off. It basically sets up the <code>ViewModel</code> and the <code>jQuery</code> sliders. The sliders' <code>slide</code> event changes the harmonic amplitude values and requests and update of the <code>ViewModel</code>:</p>

<p>
<pre><code>slider = slider.slider({ 
	min: 0,
	max: additive.amplitudeScale,
	step: 1,
	value: harmonic.amplitude,
	slide: function(event, ui) {
		harmonic.amplitude = ui.value;
		viewModel.draw(canvas);
		viewModel.updateAudio();
	}
});
</code></pre></p>
<p>That's it! You may get more mileage out of visiting the repo directly rather than just relying on my brief descriptions of snippets here. </p>

