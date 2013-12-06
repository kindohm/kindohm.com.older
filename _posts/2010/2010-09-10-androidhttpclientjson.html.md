---
layout: post
title: "Android dev newbie - use HTTP client to get json data from a local web server"
---


<p>I&#8217;ve been doing mild development for Android for about a week now. Up until last night, it&#8217;s been all very basic Hello Werld kind of coding - things like basic UI layout tasks and event wiring. Last night I decided it was time to see if I could actually pull some data from a local test web server into an Android app.</p>
<p>In hindsight, this task was pretty easy. However, the Android and Eclipse environments are completely foreign to me and most of my issues were related to those environments. I wanted to write a quick post to document the steps I took to learn. Maybe there are other Java and Android newbies out there like me who can benefit.</p>
<p>My goal:</p>
<p>1) Create a simple ASP .NET web end point that served out JSON data (e.g. provide the current server date/time):</p>
<p>http://localhost/ServerDateTime</p>
<p>2) Create a simple Android app that lets the user click a button and get the server date/time:</p>
<p><img src="/hodsmedia/1097012742_1.png" alt=""/></p>
<p>3) Wire up the button to go and fetch the date/time in JSON format</p>
<p>4) Deserialize the JSON data into an object and display the date/time value in the Android app.</p>
<p>I won&#8217;t get too deep in to step #1 - that was the easy part! I created an ASP.NET MVC 2 app that just served out a basic Foo object, serialized as JSON:</p>

    public class FooController : Controller
    {
        public ActionResult ServerDateTime()
        {
            return Json(
                new Foo()
                {
                    Now = DateTime.Now.ToString()
                },
                JsonRequestBehavior.AllowGet);
        }
    }

    public class Foo
    {
        public string Now { get; set; }
    }

<p>This code conveniently runs on the localhost IIS web server (127.0.0.1).</p>
<p>The Android app UI was trivial (goal #2).</p>
<p>Next I needed to figure out how to go out and talk to a web server. I found Jose Gomez&#8217;s code that wraps all of the core Java/Apache HTTP libraries into a simple class that satisfied my needs: <a href="http://www.josecgomez.com/2010/04/30/android-accessing-restfull-web-services-using-json" target="_blank"><a href="http://www.josecgomez.com/2010/04/30/android-accessing-restfull-web-services-using-json" target="_blank">http://www.josecgomez.com/2010/04/30/android-accessing-restfull-web-services-using-json</a></a>. This article also addressed the last goal of my app, which was to deserialize the JSON into a &#8220;Foo&#8221; object. The crux of that biscuit is to use the Google GSON library: <a href="http://code.google.com/p/google-gson" target="_blank"><a href="http://code.google.com/p/google-gson" target="_blank">http://code.google.com/p/google-gson</a></a>.</p>
<p>The code in the Android app that uses the above resources is straightforward:</p>

    String url = 
        "http://localhost/ServerDateTime";
    WebService webService = new WebService(url);

    //pass the parameters if needed
    Map<string string> params = new HashMap<string string>();
    params.put("var", "");

    //Get JSON response from server
    String response = webService.webGet("", params);

    //deserialize into a Foo object. Foo is defined
    //in other code not shown here
    Type fooType = new TypeToken<foo>(){}.getType();
    Foo foo = new Gson().fromJson(response, fooType);
    dateTimeLabel.setText(foo.toString());

<p><strong>NOTE</strong>: the above code won&#8217;t work! I&#8217;ll explain why in a few paragraphs&#8230;</p>
<p>Right away, I ran in to problems with connecting to the web server. The first was a SocketException with a Permission Denied message. I found the solution to that here: <a href="http://www.anddev.org/upload_files_to_web_server-t443-s60.html" target="_blank"><a href="http://www.anddev.org/upload_files_to_web_server-t443-s60.html" target="_blank">http://www.anddev.org/upload_files_to_web_server-t443-s60.html</a></a>. The solution was to add a &#8220;uses-permission&#8221; to the AndroidManifest.xml:</p>

    &lt;uses-permission android:name="android.permission.INTERNET"&gt;&lt;/uses-permission&gt;

<p>Next, I was encountering ConnectExceptions and timeouts. First, I must state that I was running my app on the Android emulator at this point. The emulator is really just a small virtual machine. When researching this problem, I came across <a href="http://stackoverflow.com/questions/2301560/java-net-connectexception-127-0-0-18080-an-android-emulator" target="_blank">a post on ConnectExceptions with the Android emulator</a> which talks about how you cannot use &#8220;localhost&#8221; or the local IP (127.0.0.1) to access a local dev web server. This was so obvious when I read it. Of course, localhost would refer back to the Android emulator itself. If you look at my code above, I was trying to access a localhost url. By changing the url to the IP address of my local dev machine, the app worked:</p>
    String url = 
        "http://192.168.0.103/ServerDateTime";
    WebService webService = new WebService(url);

<p><img src="/hodsmedia/1097012742_2.png" alt=""/></p>
<p>The next step is to debug this thing on my <em>real</em> Android device.</p>
<p>I&#8217;m sure these tasks would be trivial to experienced Java devs&#8230; but it&#8217;s been a blast so far to completely submerge myself into unknown territory with Android, Eclipse, and Java. It reminds me of when I developed my first ASP.NET app on .NET 1.0.</p>

