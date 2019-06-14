---
title: Resolving ILogger with Nancy and TinyIoC
author: johan
tags:
  - asp.net
  - dotnet core
  - logging
  - nancy
  - dependency injection
  - ioc
date: 2019-06-14 14:43:16
---


This is a shorter follow-up post to my recent post about {% post_link 'Properly-configuring-NLog-and-ILogger-in-ASP-NET-Core-2-2' 'configuring NLog and ILogger in ASP.NET Core' %}. As I mentioned there, since we're using [Nancy](https://nancyfx.org) for our project, we can't just use the built-in dependency resolver in ASP.NET Core, since Nancy uses it's own dependency resolution.

In most cases, we use [Autofac](https://autofac.org) and the [Nancy Autofac bootstrapper](https://github.com/nancyfx/nancy.bootstrappers.autofac), but in this case, we were using the default [TinyIoC](https://github.com/grumpydev/TinyIoC) implementation, so that's what I'll write about in this post. I might write another follow-up post when I implement this for Autofac.

First of all, we need to pass the `ILoggerFactory` that we configured in the previous post. Since this is available in `Startup.Configure` we can just pass it on to our Nancy bootstrapper.

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostingEnvironment env,
                          ILoggerFactory loggerFactory, IConfiguration configuration)
    {
        app.UseOwin(x => x.UseNancy(new NancyOptions
        {
            Bootstrapper = new CustomBootstrapper(env, configuration, loggerFactory)
        }));
    }
}
```

Now, if we were content with just resolving then non-generic version of `ILogger` this wouldn't be much of a problem, we could just create a default logger, and register that. But since we want to use the generic `ILogger<T>`, it's a little more complicated.

So we can use this custom bootstrapper:

```csharp
public class CustomBootstrapper : DefaultNancyBootstrapper
{
    //Of course we have a constructor that takes the arguments passed from Startup
    //and sets them as fields, but that seems obvious.

    protected override void ApplicationStartup(TinyIoCContainer container, IPipelines pipelines)
    {
        base.ApplicationStartup(container, pipelines);

        //Fallback for non-generic logger
        var defaultLogger = loggerFactory.CreateLogger("Default");
        container.Register<ILogger>(defaultLogger);
        //The generic constructor for Logger needs ILoggerFactory
        container.Register<ILoggerFactory>(loggerFactory);
        //Register generic logger as multi instance
        container.Register(typeof(ILogger<>), typeof(Logger<>)).AsMultiInstance();
        //TinyIoC cannot resolve ILogger<> directly in modules for some reasons,
        //so we have to register this one manually.
        container.Register<ILogger<API.Modules.FooBarModule>>(
            (c, an) => loggerFactory.CreateLogger<API.Modules.FooBarModule>());
    }
}
```

Now, there a couple of things that are important here:

- We need to register `ILoggerFactory` even though we aren't going to use it, since the generic constructor to `ILogger` needs it.
- The generic logger needs to be registered with `.AsMultiInstance()`, otherwise it will be resolved only the first time, and the same (and wrong) generic instance will be re-used after that.
- For some reason it seems the resolution of `ILogger<>` doesn't work in the modules themselves. This might have something to do with how Nancy auto discovers the modules, or it might have something to do with TinyIoC, I don't know. But since generally we do very little logging in the modules themselves, we just manually register the loggers that we need for the modules. Other options would be to for example
  - Use the non-generic `ILogger` in the modules
  - Use the `ILoggerFactory` instead in the module instead, and manually create a generic logger with `loggerFactory.CreateLogger<FooBarModule>`

I'm sure there are other, and probably better ways to this, but this seems to work well enough.
