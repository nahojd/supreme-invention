---
title: "Do NOT forget to close() your WCF ServiceClients!"
author: johan
date: 2010-03-16 18:59:36
updated: 2010-03-16 18:59:36
slug: do-not-forget-to-close-your-wcf-serviceclients
tags: [WCF, structuremap, dependency injection, asp.net mvc]
---

We had a very disturbing problem today. Our client was doing acceptance testing on the application we’re building for them, and had assembled a team of 15 people to do it. The application ran smoothly, until after a few minutes it came to a grinding halt and simply stopped responding. We checked the server, and the CPU was fine. For a while, after a minute or so it went up to 100%.

The application is an [ASP.NET MVC](http://asp.net/mvc) web client, talking to [WCF](http://msdn.microsoft.com/en-us/netframework/aa663324.aspx "Windows Communication Foundation") services, which are talking to an SQL Server. Everything running on [.NET 4 RC](http://msdn.microsoft.com/en-us/vstudio/dd582936.aspx). Well, we recycled the application pool, and everything was fine again, for a few minutes…

Some profiling on the server told us that not only did the application freeze after a few minutes, and the CPU went to 100%, but we also saw a shitload of exceptions. Well, to cut to the chase, some more testing revealed that the only page that was still responsive was the only page that didn’t talk to the WCF service at all. Aha!

In our application, we use the StructureMapControllerFactory from [MvcContrib](http://www.codeplex.com/MVCContrib) to let [StructureMap](http://structuremap.github.com/structuremap/index.html) inject dependencies into our controllers. And our controllers are depending on repositories, that are in turn depending on [ServiceClients](http://msdn.microsoft.com/en-us/library/ms735103.aspx) for the WCF services. So basically, we have a repository like this:

``` csharp
public class FooRepository: IFooRepository
{
    private ServiceClients.IFooService client;

    public FooRepository(ServiceClients.IFooService client)
    {
       this.client = client;
    }

    public Bar GetBar(int id)
    {
       return client.GetBar(id);
    }
}
```

And a StructureMap configuration like this:

``` csharp
ForRequestedType<ServiceClients.IFooService>()
.TheDefault.Is.ConstructedBy(() => new ServiceClients.FooServiceClient());

```

See the problem? Yep, we never close the client. And I can even remember thinking to myself, back in December when we wrote this: “I wonder if the fact that we don’t close the clients will cause a problem… Well, we’ll cross that bridge when we get to it.”

Today we got to the bridge. And it was under enemy fire. So why didn’t we notice this earlier? This is my guess. We have finite number of available connections to the WCF Service. Let’s say 200. And every time we make a request, we create a new client, and use one of these. But, after a while, usually 1 minute, the connection times out and closes. So we need to make enough requests to use all the available connections before they time out. And what happens then? The requests end up in a queue waiting for a connection, and the application stops responding.

So what is the solution? In the WCF textbook, you are supposed to use a service client like this (and some try and catch and other things, of course):

``` csharp
public Bar GetBar(int id) {
    var client = new ServiceClients.FooServiceClient();
    var bar = client.GetBar(id);
    client.Close();
    return bar;
}
```

But that doesn’t work too good for us, because we want to inject the service client into the constructor, in order to be able to mock it away when testing. So how do we ensure that the service client gets closed? Why, the [destructor](http://www.c-sharpcorner.com/UploadFile/chandrahundigam/UnderstandingDestructors11192005021208AM/UnderstandingDestructors.aspx), of course! I added a destructor to each of my repositories, closing the client:

``` csharp
~FooRepository()
{
    var clientObject = client as ServiceClients.FooServiceClient;
    if (clientObject != null && clientObject.State == System.ServiceModel.CommunicationState.Opened)
    {
        clientObject.Close();
    }
}
```

First, we need to cast the client to the underlying class, because the interface doesn’t have the State property, or the close method. Then, we check that is not already closed. And then we closed it.

I was actually not sure that this would work, because I wasn’t sure that the repositories would be garbage collected in time. Buy they were, and it did. So now we’re happy again!

And why did the CPU go up to 100%? Well, when the requests started timing out, we started to get lots of exceptions, which our logger couldn’t handle. We’ll check on that tomorrow. :-)