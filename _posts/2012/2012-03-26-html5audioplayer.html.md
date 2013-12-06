---
layout: post
title: "Sample HTML5 Audio Player"
---

<script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.7.1/jquery.min.js"></script>
<p>I've recently been working on an HTML5-based audio player for my soon-to-be-released music library and wanted to show some of the more interesting aspects of how I've been building it. I've created a simplified version of this music library which <a href="/apps/html5audioplayer">you can try out here</a> or you can <a href="https://github.com/kindohm/html5-audio-player">view the demo app code on github</a>.</p>



<p>There are three main parts of how the audio player works:</p>



<ul>
<li><a href="http://buzz.jaysalvat.com/">buzz.js</a> -  used for detecting audio encoding support (e.g. MP3 vs. OGG)  and for controlling audio playback</li>
<li><a href="http://knockoutjs.com">knockout.js</a> -  used for MVVM binding between the HTML and JavaScript  objects</li>
<li>JSON for music library data</li>
</ul>
<h3>Why an HTML Audio Player?</h3>
<p>Besides the fact that I wanted to see if I could do it, it might actually be a good idea to use HTML5 [1] and JavaScript for your audio app! No plugins are required, and all major browsers, including iOS Safari and Android browsers, support HTML5 audio. I've streamed my own music library in my car with this app in an Android browser over 3G and it works great. With a single code base you can deliver an audio player to multiple platforms.</p>



<h3>JSON Data</h3>
<p>First let's start with the data. For this example app it's pretty basic:</p>



<pre><code>var CATALOG = (function () {

	var catalog = {
		songs : [
			{ 
				'id' : 1,
				'title' : 'Elegance',
				'basePath' : '/music/elegance', 
				'formats' : [ 'ogg', 'mp3' ]
			},
			{
				'id' : 2,
				'title' : 'Less Human, More Mine',
				'basePath' : 'lesshuman-moremine',
				'formats' : [ 'ogg', 'mp3' ]
			},
			{
				'id' : 3,
				'title' : 'Defragmentation : Fail',
				'basePath' : 'defrag',
				'formats' : [ 'ogg', 'mp3' ]
			}
		]
	};

	return catalog;
})();</code></pre>
<p>In my real music library I'm returning a similar JSON object  through a jQuery AJAX call, but for this simple app we can use a hard-coded JSON string like the one above.</p>



<p>You'll notice the <code data-language="javascript">basePath</code> property on each <code>song</code> object. The <code>basePath</code> is the  path to the audio file without the extension and the extensions are  defined separately by the <code>formats</code> array. We'll use the formats later with the buzz.js library to dynamically figure out which encodings are  supported by the browser.</p>



<h3>buzz.js / player.js</h3>
<p>I've found buzz.js to be a fun little libary to use for HTML5 audio. Buzz.js basically wraps the audio API of modern browsers so that you can write audio-based code consistently:</p>



<pre><code data-language="javascript">var sound = new buzz.sound( "/sounds/file", {
    formats: [ "ogg", "mp3" ]
});

sound.play();</code></pre>
<p>Notice that you can specify all audio formats you have available. Buzz.js figures out which format the user's browser supports and it  will automagically play the correct format. How awesome is that?!</p>



<p>You can call "supported" methods on the buzz object to determine ahead of time what types of audio are supported in the browser. This is useful if you want to give the user some feedback about what their browser can do:</p>



<pre><code data-language="javascript">buzz.isSupported(); // will audio work at all?
buzz.isMP3Supported(); // mp3
buzz.isOGGSupported(); // ogg</code></pre>
<p>Buzz.js also supports methods such as <code>pause()</code> and  <code>stop()</code>, as well as events such as <code>ended</code>.</p>



<p>I isolated the media playing features in a JavaScript object called <code>PLAYER</code>, which is defind in player.js. Player.js  wraps buzz.js and adds some specific features that I wanted in my library, such as continuous auto-play and scrubbing.</p>



<p>You use player.js as an API for adding, playing, and scrubbing tracks. You tell the player what song you want to control by  specifying the song's ID. Below is a partial implementation of player.js (<a href="https://github.com/kindohm/html5-audio-player/blob/master/js/player.js">full version here</a>).</p>



<pre><code data-language="javascript">player.addTrack = function (song) {
	var newTrack = Object.create(track);
	newTrack.id = song.id;
	newTrack.sound = new buzz.sound(
		song.basePath,
		{ formats: song.formats, preload: false });
	tracks.push(newTrack);

	newTrack.sound.bind('ended', function () {
		handleTrackEnded(newTrack);
	});
};

player.togglePlay = function (id) {	
	var track = findTrack(id);

	if (currentTrack === null) {
		currentTrack = track;
	}

	if (track === currentTrack) {
		track.sound.togglePlay();
		return;
	}

	currentTrack.sound.pause();
	currentTrack.sound.setTime(0);
	currentTrack = track;
	currentTrack.sound.togglePlay();
};

player.scrub = function (id, percent) {
	var track = findTrack(id);
	if (track == currentTrack) {
		track.sound.setPercent(percent);
	}
};</code></pre>
<p>In the code above you can see that I'm using methods of the buzz.js <code>sound</code> object such as <code>togglePlay()</code>, <code>setPercent()</code>, <code>pause()</code>, and <code>setTime()</code>.</p>



<h3>Knockout.js and the HTML</h3>
<p>Each song in the app is presented with the following view:</p>



<pre><code data-language="html">
&lt;div class="song"&gt;
  &lt;button class="toggleButton" data-bind="click: buttonClick"&gt;
    &lt;img class="buttonImage" data-bind="attr: { src : buttonImage }"&gt;&lt;/img&gt; 
  &lt;/button&gt;
  
&lt;div class="content"&gt;
    
&lt;h2 data-bind="text: title" class="songTitle"&gt;&lt;/h2&gt;
    
&lt;p&gt;&lt;a data-bind="attr: { href: mp3, class: mp3Class }"&gt;mp3&lt;/a&gt;
      &lt;a data-bind="attr: { href: ogg, class: oggClass  }"&gt;ogg&lt;/a&gt;&lt;/p&gt;



    
&lt;div class="progress" data-bind="click: progressClick"&gt;
      &lt;span class="meter" 
        data-bind="style: { width: percentComplete() }"&gt;&lt;/span&gt;&lt;/div&gt;
  &lt;/div&gt;
  
&lt;div class="clear"&gt;&lt;/div&gt;
&lt;/div&gt;</code></pre>

<p>This HTML produces a view that looks something like this:</p>



<p><img src="/hodsmedia/songview.png" alt="Song View" /></p>



<p>With this view the user can play or pause the song by clicking the main button on the left. They can also click the links to directly download the ogg and mp3 files. While I don't show it here, the ogg or mp3 link will be hidden if the user's browser does not support the respective audio encoding type.</p>



<p>That template is bound to a simple <code>SongViewModel</code>:</p>



<pre><code>function SongViewModel (model) {
	this.id = model.id;
	this.title = model.title;
	this.mp3 = makePath(model, 'mp3');
	this.ogg = makePath(model, 'ogg');
	this.buttonImage = ko.observable(playImage);
	this.buttonClick = buttonClickHandler;
	this.percentComplete = ko.observable('0%');
	this.progressClick = progressClickHandler;
	this.mp3Class = PLAYER.isMP3Supported() ? 
		'linkVisible' : 'linkInvisible';
	this.oggClass = PLAYER.isOGGSupported() ? 
		'linkVisible' : 'linkInvisible'; 
}</code></pre>



<p>Perhaps the most interesting aspect of the <code>SongViewModel</code> and corresponding HTML code is how the progress bar is updated and used.</p>



<p><img src="/hodsmedia/songviewprogress.png" alt="Song View Progress"/></p>



<p>The <code>&lt;span&gt;</code> tag with the <code>meter</code> class defines how much width to take up to indicate progress. The <code>SongViewModel</code>'s <code>percentComplete</code> property is bound to the meter's width. I use a simple <code>setTimeout</code> call to update the percent complete value:</p>



<pre><code data-language="javascript">var updatePercentComplete = function () {
	if (currentSong != null) {
		currentSong.percentComplete(
			PLAYER.getPercentComplete(currentSong.id));
	}

	setTimeout(updatePercentComplete, 100);
};

setTimeout(updatePercentComplete, 100);</code></pre>
<p>The <code>
&lt;div&gt;</code> tag that wraps the meter is "clickable" so that the user can click in the progress bar to skip to a certain part of the song. The click event calls the following handler to perform a "scrub" on the song  (e.g. advance or reverse to a certain point in the song):</p>



<pre><code data-language="javascript">var progressClickHandler = function (viewModel, event) {
	if (viewModel === currentSong) {
		var div = $(event.currentTarget);
		var width = $(div).width();
		var percent = event.offsetX * 100 / width;
		PLAYER.scrub(viewModel.id, percent);
	}
};</code></pre>
<h3>Summary</h3>
<p>My main goal was to create an audio player in the simplest way possible, and I think I came pretty close to that goal because I was able to code it up in a couple of nights without too much effort. Granted, I had prior knowledge of knockout.js, which could be a learning curve for someone who hasn't used it before.</p>



<p>Buzz.js was really the key to making this audio player easy to code. It wraps the HTML5 audio features nicely and I especially like the audio format support that it provides. Buzz.js could also be used for sound effects rather than entire song playback.</p>



<p>[1] This is one of the rare cases when "HTML5" can be used validly as a buzz word. I've  stated before that when most folks say "HTML5" they are really talking about capabilities that have existed for years (before the HTML5 spec) with good old HTML and JavaScript. The  audio API in HTML5 is something that is not possible in prior versions of HTML.</p>




