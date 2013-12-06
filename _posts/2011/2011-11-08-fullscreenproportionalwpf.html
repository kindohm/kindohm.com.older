---
layout: post
title: "Viewbox for Full Screen Proportional WPF Apps"
---

<p>Over the last year while developing a large handfull of Kinect-based WPF apps, I've had to deal with the issue of making sure the app content consumes  the same real estate on the target display as it does on my dev machine during development. One bonus challenge is that my apps have had to run on multiple target machines with <em>different resolutions</em>. I'm going to show how I've used the  WPF Viewbox to get my apps to look the same on my dev machine as they do on the final display.</p>
<p>Let me start with an example to drive home the real problem. My dev  machine runs at a resolution of  1920 x 1200 pixels (16:10 ratio). For those of you with poor eyesight this results in  excrutiatingly small text, but that is a different story. Let's say I'm working on an application that will run at a 10:16 portrait orientation, but I don't necessarily know what the actual pixel resolution will be of the target machine (or machines).  Let's take that a step further - I want my app to look good in a portrait orientation on <em>any</em> 10:16 display - whether that's 1200x1920, 900x1440, or even 375x600.</p>
<p>I already have two issues here:</p>
<ol>
<li>My dev machine doesn't run in portrait orientation</li>
<li>I need to support an unknown number of target 10:16 resolutions</li>
</ol>
<p>Let's see what this looks like on the dev machine and on a target machine. First,  here's the app under development on my dev machine:</p>

<pre style="overflow: auto; max-height: 500px;"><code>&lt;Window x:Class="Stretchy.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="350" Width="525"
        WindowState="Maximized" WindowStyle="None"&gt;
    &lt;Grid&gt;
        &lt;Grid.RowDefinitions&gt;
            &lt;RowDefinition/&gt;
            &lt;RowDefinition/&gt;
        &lt;/Grid.RowDefinitions&gt;
        &lt;Grid.ColumnDefinitions&gt;
            &lt;ColumnDefinition/&gt;
            &lt;ColumnDefinition/&gt;
        &lt;/Grid.ColumnDefinitions&gt;

        &lt;Border
            Grid.Row="0"
            Grid.Column="0"
            Background="#66FF0000"&gt;
            &lt;TextBlock 
                Text="Upper Left" 
                HorizontalAlignment="Center"
                VerticalAlignment="Center" 
                FontSize="20"/&gt;
        &lt;/Border&gt;

        &lt;Border
            Grid.Row="0"
            Grid.Column="1"
            Background="#6600FF00"&gt;
            &lt;TextBlock 
                Text="Upper Right" 
                HorizontalAlignment="Center"
                VerticalAlignment="Center" 
                FontSize="20"/&gt;
        &lt;/Border&gt;

        &lt;Border
            Grid.Row="1"
            Grid.Column="0"
            Background="#660000FF"&gt;
            &lt;TextBlock 
                Text="Lower Left" 
                HorizontalAlignment="Center"
                VerticalAlignment="Center" 
                FontSize="20"/&gt;
        &lt;/Border&gt;

        &lt;Border
            Grid.Row="1"
            Grid.Column="1"
            Background="#66FFFF00"&gt;
            &lt;TextBlock 
                Text="Lower Right" 
                HorizontalAlignment="Center"
                VerticalAlignment="Center" 
                FontSize="20"/&gt;
        &lt;/Border&gt;

    &lt;/Grid&gt;
&lt;/Window&gt;</code></pre>


<p> <img src="/hodsmedia/wpf-stretch-01.png" alt="VS wpf app"/> </p>
<p>Now let's see what this looks like at runtime on a portrait display at 1200 x 1920 pixels:</p>
<p> <img src="/hodsmedia/wpf-stretch-01-real.png" alt="VS wpf app"/> </p>
<p>The difference of how the window looks like in Visual Studio compared to how it actually renders in Windows at runtime is very different. Obviously the orientation and window size  is different, but more importantly things like the font size and the proportions and sizes of the colored backgrounds are different. </p>
<p>I could certainly change the window size in Visual Studio to match my 1200 x 1900 10:16 portrait orientation, but then my content size hasn't been changed to  match this overall size change (the text is tiny): </p>
<pre><code>&lt;Window x:Class="Stretchy.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="1920" Width="1200"
        WindowState="Maximized" WindowStyle="None"&gt;
  &lt;!-- ... --&gt;
&lt;/Window&gt;</code></pre>
<p> <img src="/hodsmedia/wpf-stretch-02.png" alt="VS wpf app"/> </p>
<p>I could continue to tweak the content sizes (text, images, grids, borders, panels,  text boxes, rows, columns... the list goes on!!)  to match my new app window size, but this won't work for us. Remember that I want to support <em>any</em> 10:16 ratio resolution?  I could potentially create a new set of  styles to match each resolution and load the correct styles at runtime, but that's just  crazy talk if you ask me. This solution just doesn't scale.</p>
<p>The solution is to use a Viewbox and to size its child element with the same ratio of your target display. At runtime your Viewbox will make sure your content <em>appears</em> at the same proportion at design time as it does at runtime <em>on any size display.</em></p>
<p>Let's take a look at the new XAML code with the Viewbox:</p>
<pre><code>&lt;Window x:Class="Stretchy.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow"
        WindowState="Maximized" WindowStyle="None"&gt;
  &lt;Viewbox&gt;
    &lt;Grid Width="375" Height="600"&gt;
      ...
    &lt;/Grid&gt;
  &lt;/Viewbox&gt;
&lt;/Window&gt;</code></pre>
<p> <img src="/hodsmedia/wpf-stretch-03.png" alt="VS wpf app"/> </p>
<p>Notice that in Visual Studio I've sized the Grid inside the Viewbox to be only 375 x 600. It's still a 10:16 ratio, just really small. I'm making it small here to  make a point that you can design and develop your app at <em>one</em> size and have it render the "same" but at a completely <em>different</em> size at runtime.</p>
<p>Also notice above the size of the text. It's big enough to read.</p>
<p>Now let's see what this app with the Viewbox looks like on a 1200 x 1900  display:</p>
<p> <img src="/hodsmedia/wpf-stretch-03-real.png" alt="VS wpf app"/> </p>
<p>Viola![1] The app now appears <em>the same</em> at runtime as it does at design time! Your life as a developer has been made easy! You can now place text, images, panels, or any content you want in your app and it will retain its proportional look when viewed at run time.</p>
<p>Just to prove that this works at any screen resolution. Here is a screen shot of the same app running at 900 x 1440 (10:16) with no code change.  I realize that I can't prove that I'm <em>really</em> changing my screen resolution to obtain this screen shot, but just trust me:</p>
<p> <img src="/hodsmedia/wpf-stretch-03-real2.png" alt="VS wpf app"/> </p>
<p>The app has retained its overall proportions even though the screen size has changed and no code changes were required. The Viewbox is the silver bullet here as it stretches its content to fit whatever the size of its parent is.  It's parent in this case is the main window, which is maximized with no border.</p>
<div>Traditionally I've seen the Viewbox used a lot for graphics, but it comes in handy for other WPF content too. Happy coding! <br/><br/></div>
<p>[1] The correct word is "voil&#224;", but in a tribute to my friend Sarah, who phonetically says "viola" when she means "voil&#224;", I used the name of the instrument here.</p>


