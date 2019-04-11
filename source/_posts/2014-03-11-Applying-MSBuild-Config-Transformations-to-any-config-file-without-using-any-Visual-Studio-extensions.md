---
layout: posts.hbs
title: Applying MSBuild Config Transformations to any config file without using any Visual Studio extensions
extid: posts/9601
author: johan
date: 2014-03-11 17:00:42
changed: 2014-03-26 15:41:51
slug: Applying-MSBuild-Config-Transformations-to-any-config-file-without-using-any-Visual-Studio-extensions
tags: [visual studio, msbuild, teamcity]
---

As I have mentioned in [previous posts](http://johan.driessen.se/posts/Visual-Studio-feature---Preview-web.config-transforms), I frequently use a setup where our TeamCity servers creates deploy packages that are semi-automatically deployed to the web servers. A great help in achieving that is Visual Studios Web.config transformations. However, frequently we have the need to transform other config-files as well, either because we're not in a web project, or because we simple have multiple config files.

I've had some success using a Visual Studio plug-in called [SlowCheetah](http://visualstudiogallery.msdn.microsoft.com/69023d00-a4f9-4a34-a6cd-7e854ba318b5). Unfortunately it does not really play well with TeamCity. Sometimes it works, sometimes not. More the latter than the former. So recently I made an effort to solve this without using SlowCheetah, or any other extension. As it turns out, you can. And it's not even particularly difficult.

First of all, you need to have the Visual Studio Web Application build targets installed on your build server. This can be achieved either by installing an express version of Visual Studio or the [Visual Studio Shell Redistributable Package](http://www.microsoft.com/en-us/download/details.aspx?id=40777).

Then, create an App.config file in your project. I placed in in a folder called "Config", to avoid any automatic behaviour from Visual Studio. Then, create your transform files, App.Debug.config, App.Release.config and whatever you need (I usually don't use those, but rather Test, Prod, Acceptance etc). Now, these will all be placed beside App.config, and not be linked to it as with Web.config transforms. Not to worry, we'll fix that shortly!

Next, unload your project, and edit the .csproj file. First we'll fix the linking of the files. This is done simply by adding a DependentUpon element inside each Item. Let's say you have this:

```xml
<None Include="Config\App.config" />
<None Include="Config\App.Debug.config" />
```

Simply change it to this:

```xml
<None Include="Config\App.config" />
<None Include="Config\App.Debug.config">
  <DependentUpon>App.config</DependentUpon>
</None>
```

Now, let's move on the real trick. In order to make msbuild transform your config file, we need to att a build event. At the end of the file, add

```xml
<UsingTask TaskName="TransformXml"   
           AssemblyFile="$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v12.0\Web\Microsoft.Web.Publishing.Tasks.dll" />
<Target Name="AfterBuild">
  <TransformXml Source="Config\App.config"
                Transform="Config\App.$(Configuration).config"
                Destination="$(OutputPath)\$(AssemblyName).$(OutputType).config" />
</Target>
```

You need to make sure that the path in the second import matches the version of your Visual Studio build targets.

In this example, I have a console application, so I want the result of my transformation to end up in the output directory, and be named as AssemblyName.exe.config, e.g. bin\Debug\MyConsoleApplication.exe.config. In a web application where I have other config files, I would use something like

```xml
<TransformXml Source="Config\NLog.config"
              Transform="Config\NLog.$(Configuration).config"
              Destination="NLog.config" />
```

<div class="right">
![](http://johan.driessen.se/images/johan_driessen_se/WindowsLiveWriter/PersistanceinWF4beta2_E4AD/works-on-my-machine-starburst_thumb.png)
</div>

And if you have more than one config file that you would like transformed, you can of course add several TransformXml-lines. After you're done, just reload the project, and hopefully everything works. At least it works on my machine!

Finally, I should add that I found another Visual Studio extension that seems to work better than SlowCheetah (at least sometimes) called [Configuration Transform](http://visualstudiogallery.msdn.microsoft.com/579d3a78-3bdd-497c-bc21-aa6e6abbc859) and make this entire post unnessecary. On the other hand, this way there is less magic and more control, which I personally like. And if your extension suddenly breaks after an update, it might come handy!

_UPDATE 2014-03-20_ -- I realised that unless your destination file is included in the project, or rather has a suitable Build Action, it will not be included in the deploy package, or deployed at all. Usually the build action should be "Content". You don't have to worry about the content of the destination file, though, as it will be replaced on every build. I prefer not to have it checked in to source control, though, since it would be pretty pointless to check in every change to an auto-generated file.