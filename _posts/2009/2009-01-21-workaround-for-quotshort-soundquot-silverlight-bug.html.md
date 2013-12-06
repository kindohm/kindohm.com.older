---
layout: post
title: "Workaround for &amp;quot;Short Sound&amp;quot; Silverlight Bug"
---

<p>Silverlight 2 has a bug with very short audio files (e.g. less than 1s).  When you need to play a short audio file more than once, the beginning of the audio is clipped off each time after the first time it is played.  </p>

<p>The code below is an example of how to reproduce the problem.  The only way you can play audio in Silverlight is with the MediaElement class.  The code below adds a MediaElement that plays a sound to a container (e.g. a user control or control such as a Canvas).   The code also contains a method that we can assume is called many times:</p>

<pre><code>MediaElement media;
void Load()
{
  media = new MediaElement();
  media.Source = new Uri("click.mp3", UriKind.Relative);
  media.AutoPlay = false;
  this.Children.Add(media);
}

void HandleMouseEnter(object sender, MouseEventArgs e)
{
  media.Stop();
  media.Play();  
}</code></pre>
<p>This code results in a clipped audio sound at the beginning of the audio file.</p>

<p>The problem appears to be with adding the MediaElement only once to the container. To get around this problem, you can add a MediaElement to the container each time you want to play the sound. It is highly inefficient code, but addressed the clipped audio issue:</p>

<pre><code>void HandleMouseEnter(object sender, MouseEventArgs e)
{
  MediaElement media = new MediaElement();
  media.Source = new Uri("click.mp3", UriKind.Relative);
  media.AutoPlay = false;
  this.Children.Add(media);
  media.Play();
  this.Children.Remove(media);
}</code></pre>
<p>Hopefully this issue will be resolved in Silverlight 3. </p>

<div class="tags" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:6a9a856c-d98b-4e4b-b816-e4bcf7e1cadc">Technorati Tags: <a href="http://technorati.com/tags/silverlight" rel="tag">silverlight</a>,<a href="http://technorati.com/tags/code" rel="tag">code</a></div> 
