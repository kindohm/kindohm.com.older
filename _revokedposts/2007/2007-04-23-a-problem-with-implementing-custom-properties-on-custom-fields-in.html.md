---
layout: post
title: "A Problem With Implementing Custom Properties on Custom Fields in WSS"
---

<p>Creating your own custom field types in Windows SharePoint Service v3 is an awesome thing.  Instead of relying on the out-of-the-box types (text, number, Url, etc), you can create your own fields that incorporate whatever logic and data you want.  You can derive from the SPField class or a specific type of field (such as SPFieldText, SPFieldNumber, etc).  </p>

  
<p>A custom field can have some custom properties.  For example, if you have a custom field named Car, you could have a custom property named Color on the Car field.  The user could select a  color from a list of choices when creating their Car column in SharePoint.   This is accomplished by implementing your custom property itself as a custom field.  That's right - both Car and Color would inherit from SPField in order for Car to have a custom property of the Color type.</p>

  
<p>There's a great MSDN forum thread that covers this topic: <a href="http://forums.microsoft.com/MSDN/ShowPost.aspx?PostID=1109290&amp;SiteID=1" target="_blank">Custom Field Type Properties</a>.  Thanks to  <a href="http://www.justaddcode.com" target="_blank">Neil</a> for providing me the link earlier.</p>

  
<p>If you use an out-of-the-box field type for your custom property, everything will work fine:</p>

  
<pre><code>&lt;!-- ...partial field type definition --&gt;
&lt;propertyschema&gt;
    &lt;fields&gt;
        &lt;field name="MyCustomProperty" 
        displayname="My Custom Property" 
        type="Text"&gt;
        &lt;/field&gt;
    &lt;/fields&gt;
&lt;/propertyschema&gt;</code></pre>
<p>However, if you use a custom field for your custom property, AND if your custom field inherits directly from SPField, it won't work:</p>

  
<pre><code>&lt;!-- ...partial field type definition --&gt;
&lt;propertyschema&gt;
    &lt;fields&gt;
        &lt;field name="MyCustomProperty" 
        displayname="My Custom Property" 
        type="Color"&gt;
        &lt;/field&gt;
    &lt;/fields&gt;
&lt;/propertyschema&gt;</code></pre>
<pre><code>//Color class
public class ColorField : SPField 
{ ... }</code></pre>
<p>This results in a generic "Unknown Error" message in your browser when you attempt to create your Car field.</p>

  
<p>The answer is to override and implement the FieldValueType property on your custom field class:</p>

  
<pre><code>//Color class
public class ColorField: SPField
{
	public override Type FieldValueType {
		get {
			//return your fancy type
			return typeof(string);
		}	
	}
}</code></pre>
<p>Upon inspecting the SPField class in Reflector, the FieldValueType getter returns null all the time.  Specific implementations of SPField (such as SPFieldText) return a non-null value.  If you choose to inherit from a specific SPField type such as SPFieldText or SPFieldNumber, you shouldn't run in to this problem.</p>

  
<p class="tags">tags: <a href="http://technorati.com/tag/wss" target="_blank" rel="tag">wss</a> <a href="http://technorati.com/tag/customfields" target="_blank" rel="tag">customfields</a> <a href="http://technorati.com/tag/code" target="_blank" rel="tag">code</a> <a href="http://technorati.com/tag/programming" target="_blank" rel="tag">programming</a>  </p>

 
