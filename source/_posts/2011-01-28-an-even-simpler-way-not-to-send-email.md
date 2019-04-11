---
title: "An even simpler way not to send email"
author: johan
date: 2011-01-28 16:49:18
updated: 2011-01-28 16:49:35
slug: an-even-simpler-way-not-to-send-email
tags: [asp.net, tdd, email]
---

I thought this was to simple to blog about, but then I read [this post by my colleague Daniel Saidi](http://danielsaidi.wordpress.com/2011/01/28/c-easily-switch-between-sending-and-not-sending-out-e-mail-messages/), and I thought “what the hell”. 

Yes, when developing web applications you very often need to send emails. But during development, or testing for that matter, you often don’t actually want to send any emails. You could, of course, go the interface-injection-way, as in Daniel’s post. But there is actually an even easier way, build into .NET. Just edit the config for system.net/mailsettings.

Normally, your outgoing mail settings look something like this
  

``` xml 
<system.net>
    <mailSettings>
    <smtp deliveryMethod="Network">
        <network host="smtp.server.com" port="25" />
    </smtp>
    </mailSettings>
</system.net>
```


Now, if you don't want to send any emails, just change them to use a pickup directory instead. 



``` xml 
<system.net>
    <mailSettings>
    <smtp deliveryMethod="SpecifiedPickupDirectory">
        <specifiedPickupDirectory pickupDirectoryLocation="c:\temp\email"/>
    </smtp>
    </mailSettings>
</system.net>
```





And voilà, instead of sending email, you emails gets saved in a directory of your choice instead, which means that you could for example inspect the actual emails in your tests, and for you debug config just drop them in a directory that is automatically cleaned on every build. And for you deploy config, you just use the normal Network delivery method again. Piece of cake!


<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:74f06e90-fed6-4473-aa0b-3c8c38e1b400" class="wlWriterEditableSmartContent">Tags: [asp.net](/tags/asp.net), [tdd](/tags/tdd), [email](/tags/email)</div>
