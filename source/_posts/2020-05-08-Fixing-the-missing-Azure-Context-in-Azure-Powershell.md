---
title: Fixing the missing Azure Context in Azure Powershell
author: johan
date: 2020-05-08 14:24:54
tags: 
    - Azure
    - Powershell
---

So, there I was trying to run some command in [Azure Powershell](https://docs.microsoft.com/en-us/powershell/azure/?view=azps-3.8.0), when suddenly I was greeted with the following error:

`> Some-Az-Action-That-Is-None-Of-Your-Business`

    Your Azure credentials have not been set up or have expired, please
    run Connect-AzAccount to set up your Azure credentials.

Alright, I thought, maybe by credentials actually have expired, better do what it says!

`> Connect-AzAccount`

    WARNING: To sign in, use a web browser to open the page 
    https://microsoft.com/devicelogin and enter the code DVS4FGXQR to authenticate.

Said, and done, surely it will work now!?

`> Some-Az-Action-That-Is-None-Of-Your-Business`

    No tenant found in the context.  Please ensure that the credentials you provided
    are authorized to access an Azure subscription, then run Connect-AzAccount to login.

Now, this is weird. I'm, pretty sure I just signed in with the correct Azure account. What just happened?

`❯ Get-AzContex`

    Name                                     Account    SubscriptionName Environment TenantId
    ----                                     -------    ---------------- ----------- --------
    Default                                  jd@approa…                  AzureCloud

Hey, that's not correct! Where is my subscription and my tenant? This is getting curiouser and curiouser! I will spare you a couple of rounds of trying to sign in with different accounts, trying to manually set the tenant and duckduckgoing the shit out of this. Suffice to say that in the end it seems this is something that happens, your Azure Context becomes corrupted in some way. Fortunately, there is an easy way to fix it:

```PowerShell
> Clear-AzContext
> Connect-AzAccount
```

Simply clear the Azure context and re-connect, and it should all start working again. Now, if you run `Get-AzContext` again you will hopefully see something more like this:

    Name                                     Account    SubscriptionName Environment TenantId
    ----                                     -------    ---------------- ----------- --------
    Betala per användning (0058c7f9-344f-4d… jd@approa… Betala pe…       AzureCloud  77eb242d…

Weirdly enough, this happened to me on two different computers, just two days apart. It didn't take nearly as long to fix it the second time, but at least it made me write this blog post!
