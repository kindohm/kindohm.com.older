---
layout: post
title: "Programmatically create an InfoPath form instance from XSN template"
---

<p>There are lots of questions out on forums and blogs about how to programmatically create an InfoPath form from an InfoPath XSN template, but they all suggest the same thing: use the InfoPath application (or the Forms Server hosting page) to fill out a new, blank form by hand and then save the empty form xml somewhere.  Use the empty xml as a blank placeholder form that you can copy programmatically whenever you want.</p>



<p>This approach is valid, but it's a pain in the arse for deploying an InfoPath forms solution on a project.  Imagine you have ten InfoPath forms on a project that you need to deploy... that's ten forms you need to fill out by hand each time you do a deployment!  Plus, if the form design is ever updated, the blank placeholder form becomes invalid and it needs to be re-generated.</p>



<p>Now, the reason why folks are suggesting using a blank form is because doing it without a blank form is damn hard!  However, here's how you do it...</p>



<p>Prerequisites:</p>



<ol> 
<li>You can only do this within the context of a SharePoint site  </li>
<li>The SharePoint site must have the "Office SharePoint Server Enterprise Site Collection features" feature enabled.  </li>
</ol> 
<p><strong>Step 1: Create a custom ASP.NET web page with a PlaceHolder</strong></p>



<pre><code>&lt;%@ Page Language="C#" 
    AutoEventWireup="true" 
    Codebehind="FormXmlGenerator.aspx.cs"
    Inherits="MyWebApp.FormXmlGenerator" %&gt;
&lt;html xmlns="http://www.w3.org/1999/xhtml"&gt;
&lt;head runat="server"&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;form id="form1" runat="server"&gt;
            &lt;asp:PlaceHolder 
		ID="formHostPlaceholder" 
		runat="server" /&gt;
    &lt;/form&gt;
&lt;/body&gt;
&lt;/html&gt;</code></pre>
<p><strong>Step 2: Add the code behind</strong></p>



<p>In the code behind, you're going to create an instance of an XmlFormView control. An XmlFormView is an InfoPath Forms Services control that hosts an InfoPath form on any ASP.NET page (as long as you're running it in SharePoint). You can also host the XSN template, which is what this code behind will do. Make sure you reference the System.IO, Microsoft.Office.InfoPath.Server and Microsoft.Office.InfoPath.Server.Controls namespaces.</p>



<pre><code>using System;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Microsoft.Office.InfoPath.Server;
using Microsoft.Office.InfoPath.Server.Controls;
using System.IO;

namespace MyWebApp
{
    public partial class FormXmlGenerator : Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
            XmlFormView formView = new XmlFormView();
            formView.Initialize += 
		new EventHandler&lt;InitializeEventArgs&gt;(formView_Initialize);
            string url = "http://site/MyXSNLocation.xsn";
            this.formHostPlaceholder.Controls.Add(formView);
            formView.XsnLocation = url;
        }

        void formView_Initialize(object sender, InitializeEventArgs e)
        {
            XmlFormView formView = sender as XmlFormView;
            Stream stream = 
		formView.XmlForm.Template.OpenFileFromPackage("template.xml");
            StreamReader reader = new StreamReader(stream);
            string xml = reader.ReadToEnd();
            this.Response.Clear();
            this.Response.ContentType = "text/xml";
            this.Response.Write(xml);
            this.Response.End();
        }
    }
}
</code></pre>
<p>Note the use of the <a href="http://msdn2.microsoft.com/en-us/library/microsoft.office.infopath.formtemplate.openfilefrompackage(VS.80).aspx" target="_blank">OpenFileFromPackage</a> method in the Initialize event handler. That is a method that will extract the template.xml file (or any other file) from the XSN. The template.xml file is a "blank" form used to create a new instance of an InfoPath form from a template. </p>



<p>The rest of the code behind is cake. Just write out the contents of the xml file in the HttpResponse.</p>



<p><strong>Step 3: Deploy your ASP.Net page to SharePoint</strong></p>



<p>Copy your custom page somewhere into the 12/template/layouts folder on the SharePoint web front end box, and deploy the web dll to the bin folder of your web application.  You may need to modify the trust level of your web.config, use a custom security policy, and/or stronly name your web assembly for your page to work correctly.  This isn't an easy step, but it's details are outside the scope of this post [1].  </p>



<p><strong>Step 4: Make a web client call to the custom ASP.Net page in code</strong></p>



<p>Now all you need to do is point to your custom web page in code and download the xml. After that, do whatever you want with the xml... throw it in a document library, the file system, etc.</p>



<pre><code>WebClient client = new WebClient();
client.Credentials = CredentialCache.DefaultCredentials;
Stream stream = client.OpenRead("http://site/_layouts/XmlFormGenerator.aspx");
StreamReader reader = new StreamReader(stream);
string formXml = reader.ReadToEnd();</code></pre>
<p><strong>-- end example --</strong></p>



<p>I thought that maybe I could just peek at the implementation of that OpenFileFromPackage method and see what SharePoint does behind the scenes to get that template.xml file. I cracked open the Microsoft.Office.InfoPath assembly in <a href="http://www.aisto.com/roeder/dotnet/" target="_blank">Reflector</a>, but unfortunately found this:</p>



<p><a href="http://kindohm.com/localimages/posts/ProgrammaticallycreateInfoPathforminstan_13E73/Untitled.png"><img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="85" alt="Untitled" src="http://kindohm.com/localimages/posts/ProgrammaticallycreateInfoPathforminstan_13E73/Untitled_thumb.png" width="373" border="0" /></a></p>



<p>In summary, this approach is a bit hack-ish, but when <strong><em>doesn't</em></strong> Office development involve creativity, magic, a giant leap of faith, and some shamrocks?   In all seriousness, the implementation behind the scenes of producing the blank form xml is not pretty, but for the developer who consumes the custom web page it's pretty painless.  </p>



<p>There are other alternatives to solving this problem that involve performing CAB file extractions.  However, they involve either a) hosting extract.exe [2] in a process or b) writing your own .Net library that performs CAB extractions.  There is one .Net library out there (<a title="http://www.codeproject.com/KB/files/CABCompressExtract.aspx" href="http://www.codeproject.com/KB/files/CABCompressExtract.aspx">http://www.codeproject.com/KB/files/CABCompressExtract.aspx</a>) that extracts files from CABs, but it is not strongly named (bad because it can't be used within SharePoint) and it is written in C++ (bad for me because I don't understand C++).  There <strong><em>are</em></strong> ways around this, but my solution is fast to develop and more fun.  </p>



<p>PS - Thanks to <a href="http://www.iwkid.com" target="_blank">IWKID</a> for suggesting this approach to solving the problem</p>



<p>[1] If you don't know how to deploy a custom page onto your web front end, you probably shouldn't be trying out this whole idea anyway.  Just kidding.</p>



<p>[2] extract.exe is a part of the <a href="http://support.microsoft.com/kb/310618" target="_blank">Microsoft Cabinet SDK</a>.  </p>



<div class="tags" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:2ccd02e2-b9c6-4921-b188-fbee667babd3">Technorati Tags: <a href="http://technorati.com/tags/sharepoint" target="_blank" rel="tag">sharepoint</a> <a href="http://technorati.com/tags/infopath" target="_blank" rel="tag">infopath</a> <a href="http://technorati.com/tags/asp.net" target="_blank" rel="tag">asp.net</a> <a href="http://technorati.com/tags/programming" target="_blank" rel="tag">programming</a></div> 
