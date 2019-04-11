---
title: "Resizing cross-domain Iframes"
author: johan
date: 2010-05-25 15:08:04
updated: 2013-03-20 12:24:52
slug: resizing-cross-domain-iframes
tags: [html, javascript, ajax, xss]
---

 **UPDATE 2013-03-20**

 This example is very obsolete, now that we have HTML5 and [window.postMessage](https://developer.mozilla.org/en-US/docs/DOM/window.postMessage) for sending messages between windows. Unless you are specifically targeting antique browsers like IE7, you should use that instead of this pile of garbage.

 ----------------- **WARNING: OBSOLETE JUNK FOLLOWS** -------------------------

 Recently (last week, actually) I came across a problem that turned out to be quite a challenge for me. Possibly due to me lacking some javascript skillz. Anyway, I need to write it down for future reference, as I’m rather happy with the solution in the end.

 We have an application that is showed in an Iframe inside a SharePoint site. Initially it was deployed as an application within the Sharepoint site on the same server, but for various reasons we are moving it to another server. Now, since it is an iframe, and we don’t want that to be visible, we need to resize the iframe whenever the content changes size. Now, if the top window and the page in the iframe are on the same server (same domain, actually), this is not a problem. We just call a resize method in the top window on load of the iframe, or whenever we load something ajaxy, like this:



``` js 

//In the top window
resizeIframe = function(height) {
    var theFrame = document.getElementById('theFrame');
    theFrame.height = height;
};

//In the frame page, called whenever something changes the content
resize = function() {
    height = document.body.clientHeight + 50;
    top.resizeIframe(height);
};
```



 Piece of cake! When we move the application to another domain, however, this won’t work anymore. We are not allowed to call *top.resizeIframe* from the frame page anymore, since that is considered [cross-site scripting](http://en.wikipedia.org/wiki/Cross-site_scripting). What to do, what to do… Well, in [this excellent article](http://softwareas.com/cross-domain-communication-with-iframes), Michael Mahemoff lays out a few options for handling this. The one that seemed to have the most appeal is what he calls “the Marathon version”, which leverages the fact that you can open another iframe inside your iframe, loading a page on the server containing the top page. This new page is allowed to call functions and access the DOM in the top page, and by passing parameters to this page you can set the height. This is basically how it works:

 [![image](/images/johan_driessen_se/WindowsLiveWriter/ResizingcrossdomainIframes_A2E4/image_thumb_1.png "image")](/images/johan_driessen_se/WindowsLiveWriter/ResizingcrossdomainIframes_A2E4/image_4.png)

 Of course, the new inframe is invisible, and is also removed after a few seconds. So, my application now contains this instead:



``` js 

runXDomainScript = function(params) {
    var iframe = document.createElement("iframe");
    iframe.src = 'http://portal.example.com/xss.html?' + params;
    document.getElementById('xdomaincontainer').appendChild(iframe );        
    setTimeout("document.getElementById('xdomaincontainer').innerHTML = ''", 2000);
};

resize = function() {
    height = document.body.clientHeight + 50;
    runXDomainScript('height=' + height);
};
```



 And portal.example.com/xss.html is a really simple file that looks like this:



``` xml 

<html>
    <head>
        <meta http-equiv="cache-control" content="public">
        <script type="text/javascript">
            var h = getQuerystring('h');
            top.setFrameHeight(h);
        </script>
    </head>
</html>
```



 And yeah, getQuerystring is a utility method to read a parameter from the querystring which I will not clutter the code with here. It’s in the example files at the end of this already very long post. Now, when I want to resize my iframe, I just create a new iframe with the .src set to xss.html, with the height as a querystring parameter. And since xss.html is located on the same server as the top window, it is allowed to change the height!

 Great! Only, there was another problem. In our application, we are using modal dialogs (taking advantage of the excellent [nyroModal](http://nyromodal.nyrodev.com/) library). Now, these dialogs are automatically positioned in the center of the window, which works fine if you’re not in an iframe. So what we need to do is find out how far the user has scrolled the top window, and then move the modal dialog (let’s just call it the div) to the visible portion of the screen. So, we try a function like this:



``` js 

//In the framed application
moveModal = function(id) {
    var offsetHeight = top.window.pageYOffset || top.document.documentElement.scrollTop || top.document.body.scrollTop;
    var topPos = Math.max(offsetHeight - 100, 20);
    document.getElementById(id).style.top = topPos + 'px';
};
```



 Now, as you might guess, this won’t work. Why? Because we are not allowed to get the pageYOffset property from top.window, because that would be cross-site scripting (not documentElement.scrollTop or document.body.scrollTop either, those are there to make this cross-browser, which I hate). So, now we have a double problem. We need to

1.  Call a function whenever a modal dialog is shown.
2.  Read a property from the top window
3.  Do some calculations
4.  Set a property in the framed page

 1 can only be done in the framed application, 2 can only be done in the top window domain (portal.example.com), 3 can be done anywhere and 4 can only be done in the framed application. How do we solve this? Why, the same way as we solved the resizing, only in more steps. Enter the xsscallback page! Here is how it will work:

 [![image](/images/johan_driessen_se/WindowsLiveWriter/ResizingcrossdomainIframes_A2E4/image_thumb_2.png "image")](/images/johan_driessen_se/WindowsLiveWriter/ResizingcrossdomainIframes_A2E4/image_6.png)

 Confusing? A little. But this is the same as earlier, only once we’re done with calculating the offset in xss.html on the portal site, instead of just quitting, we open another iframe, *back to the framed site*, and open another static html file there, sending the result of the calculation back in the querystring. This html file, xsscallback.html, then can call the function in the original framed page to actually move the div. Code.



``` js 

//In the framed page
centerModal = function(id) {
    runXDomainScript('cmd=offset&id=' + id);
};

moveModalTo = function(id, offsetHeight) {
    var topPos = Math.max(offsetHeight - 100, 20);
    document.getElementById(id).style.top = topPos + 'px';
};
```




``` js 

//In xss.html in the portal site
callback = function(params) {
    var url = decodeURI(getQuerystring('url'));
    var iframe = document.createElement("iframe");
    iframe.src = url + '?' + params;
    document.getElementById('xDomainScriptContainer').appendChild(iframe);
}

if (cmd == 'offset') {
    var offsetHeight = top.window.pageYOffset || top.document.documentElement.scrollTop || top.document.body.scrollTop;
    var id = getQuerystring('id');
    callback('cmd=offset&h=' + offsetHeight + '&id=' + id);
}
```



 And finally, the xsscallback.html file:



``` xml 

<html>
    <head>
        <meta http-equiv="cache-control" content="public">
        <script type="text/javascript">
            var cmd = getQuerystring('cmd');
            if ( cmd == 'offset') {
               var h = getQuerystring('h');
               var id = getQuerystring('id');
               parent.parent.centerModalDialog(id, parseInt(h));
            }
        </script>
    </head>
</html>
```



 And voìla – the div gets moved. In order to make this work flawlessly, you should also make sure that the xss html files are cacheable and served as quickly as possible. But from my experience, this seems to work fine even on slow machines and slow networks. You could also increase the time until the iframes get removed.

 Since I have omitted quite a few details in this post, I’ll end by giving you some working code. These are meant to be deployed under different domains in you web server, for example portal.local and app.local. They are not exactly like my examples here, but close enough.

 [Get the examples](http://dl.dropbox.com/u/215007/blogfiles/iframe-xss.rar)

 By the way, I’ll leave it as an exercise for the reader to figure out why I couldn’t simply use *position: fixed* for the modal dialog.

<div class="wlWriterEditableSmartContent" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:6dfc0ec8-6d64-4f94-92b6-69dbc142e8b3" style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px">
	Tags: [html](/tags/html), [javascript](/tags/javascript), [ajax](/tags/ajax), [xss](/tags/xss)</div>
