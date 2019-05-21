---
title: Properly configuring NLog and ILogger in ASP.NET Core 2.2
author: johan
date: 2019-05-21 15:32:37
tags:
    - asp.net
    - logging
    - .net
    - nlog
---
Ever since we started using dotnet core a couple of years ago, both for new projects and porting old projects, we've been struggling with configuration. Especially regarding logging. The official documentation has been -- to put it mildly -- confusing and inconsistent, and to make matters worse, we've been wanting to use [NLog](https://nlog-project.org/) as well. In the old days (e.g. when we used .NET Framework 4.x) using NLog was pretty easy, we just added a NLog configuration section to web.config (or a separate file if we were being fancy), and then just accessed the static instance of NLog with `LogManager.GetCurrentClassLogger()`. This, however, does not work particularly well in dotnet core, for the following reasons:

- Dotnet Core does not like static accessors
- Dotnet Core really would prefer if we used the ILogger interface to log stuff
- We don't have a web.config anymore

So, over the last years I've tried different approaches to this, without ever being fully happy with the result. But with recent versions of dotnet, and multiple more or less ugly attempts, I feel I finally have a pretty good grasp of how to set everything up properly, so I though I'd better write it down for future reference before it slips my mind again (my mind is very good at remembering release years for old movies, but not so great at remembering dotnet configuration syntax).

So, first things first. We have an asp.net core web app targeting netcoreapp2.2, and in order to use NLog for the logging, we need two additional package references:

```xml
<PackageReference Include="NLog.Extensions.Logging" Version="1.5.0" />
<PackageReference Include="Nlog.Web.AspNetCore" Version="4.8.2" />
```

Then, we need to configure the app configuration in `Program.cs`. In older versions of dotnet core most of this setup was done in `Startup.cs`, but it has since mostly been moved to the Program class.. Besides setting up the logging, we also configure the rest of the app configuration here, e.g. setting up `appsettings.json`. For more fundamental information about the `Program.cs` and `Startup.cs` classes, see [docs.microsoft.com](https://docs.microsoft.com/en.us/aspnet/core/fundamentals/startup?view=aspnetcore-2.2).

```csharp
//This method is called from Main
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
            .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var env = hostingContext.HostingEnvironment;

            //Read configuration from appsettings.json
            config
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddJsonFile($"appsettings.{env.EnvironmentName}.json",
                             optional: true, reloadOnChange: true);
            //Add environment variables to config
            config.AddEnvironmentVariables();

            //Read NLog configuration from the nlog config file
            env.ConfigureNLog($"nlog.{env.EnvironmentName}.config");
        })
        .ConfigureLogging(logging =>
        {
            logging.ClearProviders();
            logging.AddDebug();
            logging.AddConsole();
            logging.AddNLog();
        });
```

The key here is of course the `env.ConfigureNLog($"nlog.{env.EnvironmentName}.config")` statement, which allows us to read the NLog configuration from a standard NLog configuration file, just as we did in the old .NET Framework. The ConfigureNLog extension method is provided by the `Nlog.Web.AspNetCore` package. In my example I have different nlog config files for different environments, just as I have different appsettings for different environments. The `nlog.*.config` files are automagically copied to the publish directory, just as the appsetting files. We also configure the different loggers, and add a Debug, a Console and an NLog logger, which all will the receive the same logging data.

This also has the additional benefit of getting rid of a very annoying warning that you get if you still use the old method of adding loggers in `Startup.cs`:

`ConsoleLoggerExtensions.AddConsole(ILoggerFactory)' is obsolete: 'This method is obsolete and will be removed in a future version. The recommended alternative is AddConsole(this ILoggingBuilder builder).`

And with this, we're pretty much finished. All setup regarding logging and app configuration can be removed from `Startup.cs` unless you need to do other fancy stuff there. Since `IConfiguration` and `ILoggerFactory` is already configured in `Program.cs`, you may have to inject them in Startup. This can be done in either the constructor or in the ConfigureServices or Configure methods. I really can't say which is best.

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env, IConfiguration config)
    {
        //I guess you could store config as a field here and access it in the other methods
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env,
                          ILoggerFactory loggerFactory, IConfiguration configuration)
    {
        //You can inject both ILoggerFactory and IConfiguration directly
        //into the configuration methods as well
    }
}
```

If you are using the standard asp.net core dependency resolution, this is it! You can inject `ILogger` or (preferably) the generic `ILogger<FooBar>` anywhere you want to log stuff, and just log away. In our case, we use [Nancy](https://github.com/NancyFx/Nancy) and [TinyIoC](https://github.com/grumpydev/TinyIoC) (or frequently [Autofac](https://autofac.org/)) for dependency injection, which makes things a little more complicated, but that will make for an excellent post of its own!