---
title: "Most misleading error message ever?"
author: johan
date: 2009-07-08 21:37:00
updated: 2009-10-10 00:13:48
slug: most-misleading-error-message-ever
tags: [errors, wtf, asp.net]
---

*This post was originally published on *[*http://labs.dropit.se/blogs*](http://labs.dropit.se/blogs)*.*

Sometimes .NET error messages are helpful, sometimes they are not. But worst of all are when they are outright misleading! Like the one I came across today.

I was trying to programmatically register a [ASP.NET Ajax ScriptManager](http://www.asp.net/Ajax/Documentation/Live/overview/ScriptManagerOverview.aspx) in a web control, and as the webcontrol was already overriding OnPreRender, I figured “what the heck, I’ll just put it there”. So, I basically wrote this code:

``` csharp
protected override void OnPreRender( EventArgs e )
{
    RegisterScriptManager();
    base.OnPreRender( e );
}
private void RegisterScriptManager()
{
    if (ScriptManager.GetCurrent( Page ) == null)
    {
        Page.Form.Controls.Add( new ScriptManager() );
    }
}
```

Now, as it turns out, OnPreRender is not a particularly good place to try to modify the control collection of the page. When I tried to run the page, I was greeted by this friendly error message:

[![image.axd](/images/blog_driessen_se/subtext/WindowsLiveWriter/Mostmisleadingerrormessageever_12C7F/image.axd_thumb.png "image.axd")](/images/blog_driessen_se/subtext/WindowsLiveWriter/Mostmisleadingerrormessageever_12C7F/image.axd_2.png)

Ok, so the control collection cannot be modified during DataBind, Init, Load, PreRender or Unload phases. Really? What events does that leave us with? Lets see, override…

[![image2.axd](/images/blog_driessen_se/subtext/WindowsLiveWriter/Mostmisleadingerrormessageever_12C7F/image2.axd_thumb.png "image2.axd")](/images/blog_driessen_se/subtext/WindowsLiveWriter/Mostmisleadingerrormessageever_12C7F/image2.axd_2.png)

Whoops, turns out the only events available are OnDataBinding, OnInit, OnLoad, OnPreRender and OnUnload. Exactly the ones that the error message told me not to use. Well, since the error message explicitly told me not to use them, I suppose I can’t.

The next 30 minutes or so I spent trying to find another (not too complicated) way to put my ScriptManager in the page, I also googled (actually, [binged](http://www.bing.com/search?q=The+control+collection+cannot+be+modified+during+DataBind%2C+Init%2C+Load%2C+PreRender+or+Unload+phases.)) quite a lot, and found a post that seemed to modify the control collection during the Init phase… That wouldn’t work, would it? I said to myself, what the hell, it can’t hurt to try, and changed my code to this:

``` csharp
protected override void OnInit( EventArgs e )
{
    RegisterScriptManager();
    base.OnInit( e );
}
```

Ctrl-Shift-B. Alt-Tab. F5. Wait. Wait some more… Lo and behold, it works!

So, it seems you *can* modify the control collection during the Init phase, *even though the error messages explicitly tells you that you cannot!* WTF! Thank you very much .NET Error Message Team, whoever you are, for wasting 2 hours of my day (yeah, after I had wasted 30 minutes on this error, I wasted another 1½ hour writing this blog post :-))