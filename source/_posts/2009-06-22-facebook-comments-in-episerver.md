---
title: "Facebook Comments in EPiServer"
author: johan
date: 2009-06-22 16:07:00
updated: 2009-10-10 00:11:55
slug: facebook-comments-in-episerver
tags: [code, episerver, plugin, facebook]
---

*This post was originally published on *[*http://labs.dropit.se/blogs*](http://labs.dropit.se/blogs)*.*

[Facebook Comments](http://wiki.developers.facebook.com/index.php/Comments_Box) is a very useful widget for [Facebook Connect](http://developers.facebook.com/connect.php), the integration platform for Facebook, allowing you to place a comments box on your web pages. As it uses the Facebook connect platform, it lets the users sign in with their Facebook accounts as well as publish their comments to their FB feed, making this a powerful marketing tool as well.

Not that it is extremely difficult to integrate the comment box in your EPiServer site, but we thought it would be a good idea to make it even easier. So behold, the “Facebook Comments for EPiServer CMS” module (yeah, I know, I should work on the name) was born!

#### Instructions

In order to use the comments box, you first need to [create a basic Facebook App](http://www.facebook.com/developers/createapp.php). Name this anyway you like, for example JohanEPiComments. After you have created the app, set the callback url for your site (Canvas –> Canvas Callback URL). A public url is not needed, but if you are developing on your local machine, you can’t use “localhost”, but rather something like “myepisite.local”.

That’s it, your application is set up. Now, take note of your API key and your secret key. These are needed later.

Copy the binary files from the zip file below to your bin folder, and add the following lines to you web.config (or just put the highlighted line at the appropriate position):

``` xml
<system.web>
    <pages>
        <controls>
            <add tagPrefix="Dropit" namespace="Dropit.FacebookComments" assembly="Dropit.FacebookComments"/>
        </controls>
    </pages>
</system.web>
```

You also have to add a “cross-domain receiver file” to your application root folder (probably a good idea to include it in your project), in order to enable Facebook to run scripts on your page. Currently you can download this file here: [http://www.somethingtoputhere.com/xd_receiver.htm](http://www.somethingtoputhere.com/xd_receiver.htm).

After this, you have to add some properties to you start page in EPiServer:

[![image.axd](/images/blog_driessen_se/subtext/WindowsLiveWriter/FacebookCommentsinEPiServer_12B25/image.axd_thumb.png "image.axd")](/images/blog_driessen_se/subtext/WindowsLiveWriter/FacebookCommentsinEPiServer_12B25/image.axd_2.png)

The FacebookAPIKey and FacebookSecretKey properties are, of course, where you enter the API key and the Secret key from your application. The FacebookCommentXidPrefix is a prefix for the unique ids for every page comment box (xid). Facebook Comments normally uses the url of the page for this key, but this makes it difficult if your pages are accessible on several domains (i.e. www.dropit.se and dropit.se). Instead you could use the prefix “dropitse”, and make it domain independent.

After settings this properties, all you have to do is to add a web control to the pages that you want to have comments on:

``` html
<dropit:commentsblock runat="server" Width="500" NumPosts="10" />
```

The web control accepts all attributes that are described on [http://wiki.developers.facebook.com/index.php/Fb:comments_%28XFBML%29](http://wiki.developers.facebook.com/index.php/Fb:comments_%28XFBML%29), except “xid”, which is automatically created for the page. All are optional. Note: The commentsblock control automatically adds a script reference to the head-section of the page, but for this to work the head tag needs to be runat=”server”. If, for some reason, you don’t want your head-element to be a server control, you could add the script reference manually, like this:

``` html
<!-- head as a server control - no need for the script reference -->
<head runat="server">
...
</head>

<!-- or you can register the script manually -->
<head>
    <script src="http://static.ak.connect.facebook.com/js/api_lib/v0.4/FeatureLoader.js.php" type="text/javascript"></script>
</head>
```

An extra feature in this plug-in is the ability to get the number of comments for a specific page without actually loading the comments box. This is done by calling FacebookUtility.GetCommentCountForPage(), which takes a PageData object as variable. This feature uses the [Facebook Developer Toolkit](http://facebooktoolkit.codeplex.com/), which is included in the zip file.

#### Disclaimer and licensing

No guarantees whatsoever are made for the functionality or usability of this code. It might eat your code and format your hard drive, but probably not. That said, if you have any questions or opinions, please post them as comments to this post.

This is released under the WTFPL license. Please modify as much as you want.
[http://sam.zoy.org/wtfpl/](http://sam.zoy.org/wtfpl/)
[http://en.wikipedia.org/wiki/WTFPL](http://en.wikipedia.org/wiki/WTFPL)

#### Download

Binaries: [Dropit.FacebookComments_Bin.zip](http://labs.dropit.se/FBComments/Dropit.FacebookComments_Bin.zip)
Source code: [Dropit.FacebookComments_Source.zip](http://labs.dropit.se/FBComments/Dropit.FacebookComments_Source.zip)

***Update on repost on my new blog** – just in case Dropit would do something stupid, like move or even remove the files linked above, I also made them available from my site:
Binaries: [Dropit.FacebookComments_Bin.zip](/files/Dropit.FacebookComments_Bin.zip)
Source code: [Dropit.FacebookComments_Source.zip](/files/Dropit.FacebookComments_Souce.zip)*