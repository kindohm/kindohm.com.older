---
layout: post
title: "Execute SharePoint Content Deployment (and Quick Deploy) Jobs Programmatically"
---

<p>This is arguably one of the easiest tasks you can perform in code as a SharePoint developer, but not very well known or documented.  Start off by referencing the Microsoft.SharePoint.Publishing assembly.  The rest is cake:</p>
  
	// assume we're using the
	// Microsoft.SharePoint.Publishing.Administration namespace

	ContentDeploymentJobCollection jobs = ContentDeploymentJob.GetAllJobs();

	foreach (Job job in jobs)
	{
	  Console.WriteLine(job.Name);
	  job.Run();
	}

<p>The ContentDeploymentJob class also has a GetAllQuickDeployJobs() method to return only Quick Deploy jobs.</p>
  
<p>The ContentDeploymentJob class hosts the expected methods and properties you'd find on a job, such as Name, Description, LastStatus, Run(), and Test() (among others).</p>

