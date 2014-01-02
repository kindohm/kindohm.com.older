---
layout: post
title: "COM Exception on Programmatic WSS Workflow Execution"
---

<p>Late last week I was running in to problems while attempting to programmatically execute a Windows SharePoint Workflow using the <a href="http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.workflow.spworkflowmanager.aspx" target="_blank">SPWorkflowManager</a> <a href="http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.workflow.spworkflowmanager.startworkflow.aspx" target="_blank">StartWorkflow</a> method:</p>
	//this is a very, very simplified example
	SPSite site = 
		new SPSite("http://server/sites/site");
	SPWeb web = site.RootWeb;
	SPList list = web.Lists[0];
	SPWorkflow workflow = list.WorkflowAssociations[0];
	SPListItem item = list.Items[0];
	SPWorkflowManager workflowManager = site.WorkflowManager;

	SPWorkflow startedWorkflow = workflowManager.StartWorkflow(
		item, workflow, workflow.AssociationData);

<p>When execution reached the StartWorkflow method, I received the following exception:</p>

	Microsoft.SharePoint.SPException: 
	Exception from HRESULT: 0x8102009B ---&gt; 
	System.Runtime.InteropServices.COMException (0x8102009B): 
	Exception from HRESULT: 0x8102009B
	
<p>Goog'ling didn't yield much help.</p>
<p>By accident, I discovered that the workflow was being started for a list item that already had the same workflow started - but had errored during its execution. SharePoint decides to keep the workflow in a "running" state until a user (or developer) explicitly chooses to terminate it. </p>
<p>For example, here is a screen shot of a list item that has an "errored" workflow in progress:</p>
<p><a title="Photo Sharing" href="http://www.flickr.com/photos/kindohm/631506068/" target="_blank"><img height="500" alt="running" src="http://farm2.static.flickr.com/1348/631506068_39acaefa6a.jpg" width="395" border="0" /></a></p>
<p>By clicking on the "errored" workflow, you can terminate it on the next screen:</p>
<p><a title="Photo Sharing" href="http://www.flickr.com/photos/kindohm/630642821/" target="_blank"><img height="500" alt="terminate" src="http://farm2.static.flickr.com/1413/630642821_a444273951.jpg" width="422" border="0" /></a></p>
<p>There is also a <a href="http://msdn2.microsoft.com/en-us/library/microsoft.sharepoint.workflow.spworkflowmanager.cancelworkflow.aspx" target="_blank">CancelWorkflow</a> method on the SPWorkflowManager class that you can use to terminate the workflow programmatically.</p>
  
<p>As for why the workflow stays in an "error" state and doesn't terminate, I'm not sure yet.  </p>
 
