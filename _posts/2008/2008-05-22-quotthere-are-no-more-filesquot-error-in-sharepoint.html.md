---
layout: post
title: "There are no more files - error in SharePoint"
---

<p>Best SharePoint error ever:</p>
  
<p><a href="http://www.kindohm.com/localimages/posts/TherearenomorefileserrorinSharePoint_8750/error.png"><img style="border-right: 0px; border-top: 0px; border-left: 0px; border-bottom: 0px" height="423" alt="error" src="http://www.kindohm.com/localimages/posts/TherearenomorefileserrorinSharePoint_8750/error_thumb.png" width="569" border="0" /></a></p>
  
<p>Actually, I don't know that this is a SharePoint error at all.  The core exception thrown is a COMException.  Internet research has only yielded a small amount of forum posts relating to using System.IO.Directory and System.IO.DirectoryInfo, which as far as I can tell are not being used on this page.  </p>
  
<p>I cracked open Reflector hoping to learn more.  An internal SharePoint class just catches the COMException and throws a new SPException.  As you can guess, the SharePoint method that does the work is just wrapping a COM call.   </p>
  
<p>Unfortunately, I think we may resort to more drastic measures to save time (backup/restore from another environment) to eliminate this error rather than try to understand what the cause of the error is.  </p>
  
<p>But you have to admit, the exception message is totally awesome.  </p>
  
<div class="tags" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:76bf983b-a907-4ba9-95fe-bbacdae36113">Technorati Tags: <a target="_blank" href="http://technorati.com/tags/sharepoint" rel="tag">sharepoint</a> <a target="_blank" href="http://technorati.com/tags/error" rel="tag">error</a></div> 
