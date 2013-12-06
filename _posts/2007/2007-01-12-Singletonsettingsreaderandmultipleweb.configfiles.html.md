---
layout: post
title: "Singleton settings reader and multiple web.config files"
---

<p>When I use a custom configuration section in a .Net app, I usually use an implementation of the <a href="http://en.wikipedia.org/wiki/Singleton_pattern" target="_blank">Singleton</a> pattern to create a class that accesses my custom configuration data.  In other words, there is only one class and one instance of that class in my app that accesses my settings.  That way once the settings are loaded into memory they don't need to be loaded again by my app. </p>










  
<p>I usually implement my Singleton settings reader so that it only loads settings once.  e.g. if it already loaded the settings then don't load them again:</p>










  <code>
<pre>private MyConfigData _myConfigData;

public CustomData MyConfigData
{
  get
  {
    if (_myConfigData == null)
      _myConfigData = GetConfigSectionSettings();
    return _myConfigData;
  }
}</pre></code>
<p>Consider the following ASP .Net web application folder structure:</p>










  <code>
<pre>WebRoot\
  |-- bin\
  |-- SubFolder1\
        |-- web.config
  |-- SubFolder2\
        |-- web.config
  |-- web.config</pre></code>
   
<p>SubFolder1 and SubFolder2 both rely on the same custom configuration section in their own web.config files, but each contains different configuration data.  For example:</p>










  <code>
<pre>&lt;!-- SubFolder1 Config --&gt;
&lt;customConfigData cacheTime="1000" logFolder="logs"/&gt;</pre></code>
<code>
<pre>&lt;!-- SubFolder2 Config --&gt;
&lt;customConfigData cacheTime="1525" logFolder="LogFiles"/&gt;</pre></code>
<p>In other words, each folder needs to be configured a little differently.  </p>










  
<p>Using a Singleton class that only loads settings once to access this custom configuration data results in a problem.  The subfolder that is accessed <em>first</em> in the application will have its settings loaded for the entire application.  In other words, subfolders accessed after the first subfolder will not have their custom settings loaded - and they will end up using the first-loaded subfolder's settings.  Why is this?  Since the settings reader is a Singleton and the settings are only loaded once, the settings reader doesn't load the settings a 2nd time when the 2nd subfolder is accessed.  </p>










  
<p>The moral of the story is to not implement it this way.  &lt;rimShot /&gt;  Seriously - there are two options that I can think of:</p>










  
<ol>   
<li>Don't use a singleton and create an instance of the settings reader every time you need to read settings.</li>   
<li>Implement a singleton that is intelligent about multiple web.config files.</li> 
</ol>  
<p>I'll be looking into option #2.  I don't like the idea of creating an instance every time I want to read out settings.</p>










 
