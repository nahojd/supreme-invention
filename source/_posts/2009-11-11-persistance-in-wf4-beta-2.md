---
title: "Persistance in WF4 beta 2"
author: johan
date: 2009-11-11 16:42:51
updated: 2009-11-11 16:42:51
slug: persistance-in-wf4-beta-2
tags: [.NET, WF4]
---

Ok, this is my follow-up post to [.Net Framework Profiles](/archive/2009/11/11/.net-framework-profiles.aspx), where I very naïvely though I persistence in WF4 was all about using the correct target framework. As it turned out, they changed the persistence model between beta 1 and beta 2, and the tutorial was apparently written for beta 1.

So here’s my changes to the Microsoft tutorial [How to: Create and Run a Long Running Workflow](http://msdn.microsoft.com/en-us/library/dd489452%28VS.100%29.aspx) in order to [![This works on my machine. It might not work on you rmachine.](/images/johan_driessen_se/WindowsLiveWriter/PersistanceinWF4beta2_E4AD/works-on-my-machine-starburst_thumb.png "This works on my machine. It might not work on you rmachine.")](/images/johan_driessen_se/WindowsLiveWriter/PersistanceinWF4beta2_E4AD/works-on-my-machine-starburst_2.png)make it work for Windows Workflow Foundation 4 beta 2. 

### The persistence database

First of all, since they have replaced the **SqlPersistanceProvider** with **SqlWorkflowInstanceStore**, you have to generate a different persistence database. Instead of the scripts mentioned in the tutorial, you should use *SqlWorkflowInstanceStoreSchema.sql* and *SqlWorkflowInstanceStoreLogic.sql* to generate your database. They are still found in *C:\Windows\Microsoft.NET\Framework\<current version>\sql\en*.

### Enable persistence in the workflow application

1.  Add the correct references. Disregard the references mentioned in the tutorial. Instead add references to **System.Runtime** and **System.Activities.DurableInstancing**. These are both included in the Client Profile, rendering my previous post completely obsolete (and it was only a couple of hours old). 
2.  Yep, you should still add a connection string to your persistence database. 
3.  Add a using statement for **System.Activities.DurableInstancing** instead of System.ServiceModel.Persistence. 
4.  Next, add a SqlWorkflowInstanceStore to the workflow application        

    ``` csharp 
 wfApp.InstanceStore = new SqlWorkflowInstanceStore(connectionString);
```

5.  In order to tell the workflow to persist when it goes idle, replace the Idle action with a PersistableIdle action. 
      

    ``` csharp 
//Remove this from the previous step
wfApp.Idle = delegate(WorkflowApplicationIdleEventArgs e)
{
    idleEvent.Set();
};

    //And replace it with this
wfApp.PersistableIdle = delegate(WorkflowApplicationIdleEventArgs e)
{
    idleEvent.Set();
    return PersistableIdleAction.Persist;
};
```

6.  Yeah, same, same 
7.  Since the database schema is different, instead of looking in the Instances table, look in the **[System.Activities.DurableInstancing].[InstancesTable] **table (yeah, redundancy is fun, always suffix your tables with table and your databases with database!). 



Now, it should work. At least it did for me. But just to be safe, I’ll decorate this post with the official Works On My Machine seal of awesomeness.


<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:1457d1ad-451f-4837-b206-d38608f8ed90" class="wlWriterEditableSmartContent">Tags: [.NET](/tags/.NET), [WF4](/tags/WF4)</div>
