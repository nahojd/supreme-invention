---
title: "Why Workflow Services Storing Their Physical Location In The Xamlx File Is A Very Bad Idea"
author: johan
date: 2010-02-25 19:11:33
updated: 2010-02-25 19:30:06
slug: why-workflow-services-storing-their-physical-location-in-the-xamlx
tags: [WF4, .net 4]
---

Just now I was trying to debug a [Workflow Service in WF4](http://msdn.microsoft.com/en-us/library/dd456788(VS.100).aspx), that a colleague of mine had created. Strange thing was, even though I set a breakpoint the debugger didn’t stop, it just returned the answer as if I wasn’t debugging at all. In the same projects, I have other Workflow Services, and I hade no problem debugging those (except that debugging workflows is slooow, but that’s beside the point).

I started looking a the Debug output, when this line caught my eye:

[![Instrumentation for debugger fails. Reason: Could not find file 'C:\TFS\Butler_WCF\EducationWorkflowServices\AktivitetService.xamlx'](/images/johan_driessen_se/WindowsLiveWriter/WhyWorkflowServicesStoringTheirPhysicalL_FD47/image1_thumb.png "Instrumentation for debugger fails. Reason: Could not find file 'C:\TFS\Butler_WCF\EducationWorkflowServices\AktivitetService.xamlx'")](/images/johan_driessen_se/WindowsLiveWriter/WhyWorkflowServicesStoringTheirPhysicalL_FD47/image1.png) 

“C:\TFS\..” – hey, that’s not were I keep my project files! We, of course, use a source code repository for our code ([TFS](http://msdn.microsoft.com/en-us/teamsystem/dd408382.aspx "Team Foundation Server"), actually), and every developer checks out the project to a location of his or her discretion. I, for example, use “C:\@Projects” as the root folder. My colleague, let’s call her Inger, because that’s her name, uses “C:\TFS". But how would the debugger know that, and try to use her structure, just because she created the file, you might wonder.

So did I. A little investigation came up with this. In the Xamlx file for the Workflow Service, right at the top, I found this little nugget:

[![Why is the physical location of the xamlx file stored IN the file?](/images/johan_driessen_se/WindowsLiveWriter/WhyWorkflowServicesStoringTheirPhysicalL_FD47/image_thumb_3.png "Why is the physical location of the xamlx file stored IN the file?")](/images/johan_driessen_se/WindowsLiveWriter/WhyWorkflowServicesStoringTheirPhysicalL_FD47/image_8.png) 

Yes, it is true. WF4 keep the physical location of the Workflow Service Xamlx in an attribute called *sad:XamlDebuggerXmlReader.Filename* in the Xamlx file itself! Naturally, my first instinct was just to remove the the sad:XamlDebuggerXmlReader.Filename attribute. No luck, debugging didn’t work at all. So I changed the attribute to point to my file, in “C:\@Projects”. And behold – debugging works.

What the were you thinking, Microsoft? Do you actually believe that every developer on a project has the same physical structure on their machines? Or do you think that there is always only one developer on a WF4 project? Do you think it’s a good idea that we have to remember to change the *sad:XamlDebuggerXmlReader.Filename* attribute every time we need to debug a Workflow Service?

All workflows and no play makes Johan a dull boy. But actually, I think I’ll write a tutorial to Workflow Foundation 4 soon. I’ll call it “how to actually use it”.
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:6fa4f10f-c595-4387-947b-f93e44005475" class="wlWriterSmartContent">Tags: [WF4](/tags/WF4), [.net 4](/tags/.net%204)</div>
