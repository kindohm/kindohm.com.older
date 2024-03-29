---
layout: post
title: "SharePoint Site Backup on Deletion"
---

<p>Check out this WSS Feature that performs automatic backups of SharePoint sites upon their deletion: <a href="http://www.codeplex.com/governance" target="_blank">http://www.codeplex.com/governance</a>.  Handy.  From a developer's perspective, I doubt I'd use this myself, but I can't count the number of times a user has asked me if it is possible to restore a site they've accidentally deleted.  You don't get this functionality out-of-the-box with WSS 2.0/3.0, but this feature makes it possible.</p>
  
<p>All deletions and backups are logged to a folder of your choice and specific information such as the logged in user, deleted site, etc are all logged.  SQL backups are created and sites can be restored with standard STSADM.EXE operations (-restore for site collections and -import for sub sites).</p>
  
<p class="tags">tags: <a href="http://technorati.com/tag/sharepoint" target="_blank" rel="tag">sharepoint</a> <a href="http://technorati.com/tag/backup" target="_blank" rel="tag">backup</a> <a href="http://technorati.com/tag/delete" target="_blank" rel="tag">delete</a> <a href="http://technorati.com/tag/wss" target="_blank" rel="tag">wss</a>  </p>
 
