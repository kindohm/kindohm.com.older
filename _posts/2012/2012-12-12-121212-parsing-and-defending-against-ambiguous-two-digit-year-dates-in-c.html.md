---
layout: post
title: "&quot;12/12/12&quot;  - parsing and defending against ambiguous two-digit year dates in C#"
---

<p>Today is "12/12/12". Except for this post you will never (and by <em>never</em> I mean <em>rarely</em>) see me use a two-digit value for the year. "12/12/2012" is much cooler. "12/12/12" just isn't cool because it is ambiguous. To us programmers, ambiguity is death. Ambiguity means confusion and unclear instructions. Ambiguity means we don't know what to tell a computer to do. Reading a text value of "12/12/12" is going to result in a parsed date value of "12/12/2012". Or is it? What do you actually <em>want</em> it to be parsed as? What if you're working on an app that parses a lot of birth dates for a population of people born near the beginning of the last century? Parsing a birth date string of "12/12/12" as an actual date value of "12/12/2012" is going to be a problem, isn't it?</p>



<p>So how do we deal with this in C#? In the <code>System.Globalization</code> namespace in the .NET Framework there is a handy class named <code><a href="http://msdn.microsoft.com/en-us/library/system.globalization.cultureinfo.calendar.aspx">Calendar</a></code> with a handy property named <code>TwoDigitYearMax</code>. This property is the key to parsing date strings with two-digit dates properly. The <code>Calendar</code> class is a property on the <code>DateTimeFormatInfo</code> class, which can be passed in as an argument to <code>DateTime.Parse()</code>.</p>




<p>
<pre><code>dateTimeFormatInfo.Calendar.TwoDigitYearMax = 1800;
var date = DateTime.Parse("12/12/12", 
    dateTimeFormatInfo);

//prints "12/12/1712"
Console.WriteLine(date.ToShortDateString());</code></pre></p>



<p>One small issue is that you cannot set the <code>TwoDigitYearMax</code> property on the current culture:</p>




<p>
<pre><code>CultureInfo
    .CurrentCulture
    .Calendar
    .TwoDigitYearMax = 2000;
</code></pre></p>



<p>If you try this, you'll get an <code>InvalidOperationException</code>. The instance of the <code>Calendar</code> on the current culture is read-only.</p>



<p>The <code>Calendar</code> class's constructor isn't public, so instead you may create a clone of the <code>Calendar</code> and <code>DateTimeFormatInfo</code> from the current culture to retrieve instances that are <em>not</em> read-only:</p>




<p>
<pre><code>var culture = CultureInfo.CurrentCulture;
var formatInfo = (DateTimeFormatInfo)culture
    .DateTimeFormat.Clone();
formatInfo.Calendar = (Calendar)formatInfo
    .Calendar.Clone();
formatInfo.Calendar.TwoDigitYearMax = 2100;
</code></pre></p>



<p>With this approach, now we can write a small app to parse dates with two-digit years for any century:</p>




<p>
<pre><code>internal class Program
{
    private static CultureInfo culture;

    private static void Main(string[] args)
    {
        culture = CultureInfo.CurrentCulture;
        var dateString = "12/12/12";

        Console.WriteLine("Date string: {0}", dateString);
        Console.WriteLine("Default TwoDigitYearMax: {0}", 
                culture
                    .Calendar
                    .TwoDigitYearMax
                    .ToString());

        ParseDate(dateString);

        for (var i = 1800; i &lt;= 2300; i += 100)
        {
            ParseDate(dateString, i);
        }

        Console.ReadLine();
    }

    private static void ParseDate(string dateString)
    {
        ParseDate(dateString, 
            culture.Calendar.TwoDigitYearMax);
    }

    private static void ParseDate(string dateString, 
        int twoDigitYearMax)
    {
        var formatInfo = (DateTimeFormatInfo)culture
                .DateTimeFormat.Clone();
        formatInfo.Calendar = (Calendar)formatInfo
            .Calendar.Clone();
        formatInfo.Calendar.TwoDigitYearMax 
            = twoDigitYearMax;

        var date = DateTime.Parse(dateString, 
            formatInfo);
        Console.WriteLine();
        Console.WriteLine("TwoDigitYearMax: {0}", 
                twoDigitYearMax.ToString());
        Console.WriteLine("Parsed value: {0}", 
            date.ToShortDateString());
    }
}
</code></pre></p>



<p>This program produces the following output:</p>




<p>
<pre><code>Date string: 12/12/12
Default TwoDigitYearMax: 2029

TwoDigitYearMax: 2029
Parsed value: 12/12/2012

TwoDigitYearMax: 1800
Parsed value: 12/12/1712

TwoDigitYearMax: 1900
Parsed value: 12/12/1812

TwoDigitYearMax: 2000
Parsed value: 12/12/1912

TwoDigitYearMax: 2100
Parsed value: 12/12/2012

TwoDigitYearMax: 2200
Parsed value: 12/12/2112

TwoDigitYearMax: 2300
Parsed value: 12/12/2212</code></pre></p>



<p>Notice that the output shows that the default <code>TwoDigitYearMax</code> value is "2029". This is <em>on my machine at the time of this writing</em>. Your value on your machine, or on any target machine, could be different. From MSDN (<a href="http://msdn.microsoft.com/en-us/library/system.globalization.calendar.twodigityearmax.aspx">http://msdn.microsoft.com/en-us/library/system.globalization.calendar.twodigityearmax.aspx</a>):</p>



<blockquote>
<p>The initial value of this property is derived from the settings in the regional and language options portion of Control Panel. However, that information can change during the life of the AppDomain. The Calendar class does not detect changes in the system settings automatically. If the calendar is not supported in the regional and language options, the initial value of this property is the default value defined by the Calendar class.</p>



</blockquote>
<p>Sure enough:</p>



<p><a href="/hodsmedia/twodigityear.png"><img src="/hodsmedia/twodigityear.png" alt="" title="twodigityear" width="632" height="714" class="aligncenter size-full wp-image-1483" /></a></p>



<p>So there you have it. Parse those ambiguous two-digit year dates with ease!</p>



<p>If you enjoyed this, I encourage you to read <a href="http://infiniteundo.com/post/25326999628/falsehoods-programmers-believe-about-time">Falsehoods Programmers Believe About Time</a></p>




