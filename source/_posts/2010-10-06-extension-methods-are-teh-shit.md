---
title: "Extension methods are teh shit!"
author: johan
date: 2010-10-06 14:05:45
updated: 2010-10-08 10:44:05
slug: extension-methods-are-teh-shit
tags: [c#, code, extension methods, silly]
---

I’m really starting to like [extension methods](http://msdn.microsoft.com/en-us/library/bb383977.aspx)! I must admit I was a bit sceptical at first when they appeared in C# 3.0 (wow, that was 3 years ago!). I was worried that they would make the code hard to follow, and thought that you could just make a static helper class instead (which is, after all, what extension methods really are).
  

``` csharp 
//After all, what's wrong with this?
cleanString = StringHelper.RemoveHtml(htmlString);

//On the other hand, this does look nicer...
cleanString = htmlString.RemoveHtml();
```





So at first, I didn’t use it for much, but now I’ve really started to like it, and I find myself extract anything that operates on classes I can’t change to extension methods. For example, don’t you hate that you can’t use `Action.ForEach` on `IEnumerable`? Don’t worry, there’s an extension for that:




``` csharp 
public static void ForEach<T>( this IEnumerable<T> enumeration, Action<T> action )
{
    foreach (var item in enumeration)
    {
        action( item );
    }
}
```





And the ugly `!string.IsNullOrEmpty(someStringThatMayBeNullOrEmpty)`? Well, how about




``` csharp 
public static bool IsNullOrEmpty(this string @string, bool trim = false)
{
    if (@string == null)
        return true;
    return String.IsNullOrEmpty(trim ? @string.Trim() : @string);
}
```





And suddenly you can call it like this instead: `!someStringThatMayBeNullOrEmpty.IsNullOrEmpty()`. And yes, this works even if the string is null, since you're not actually calling a method on the string object, but rather a static method with the string as a parameter!

I’m also very fond of a generic extension that lets me parse strings to enums with an optional fall-back value if the string doesn’t parse: `enumString.ParseToEnum<MyEnum>(MyEnum.DefaultValue)`. I expect that if this keeps up, all logic will be in extension methods by the end of the year.

For some reason, my colleagues thought that I went a bit to far with this one:




``` csharp 
public static bool ContainsSomething( this string str )
{
    return !str.IsNullOrEmpty();
}
```





But I think they're just jealous.

Update: Fixed some bad formatting…


<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:5f87b259-26c4-44db-8c18-4b9e543470b0" class="wlWriterEditableSmartContent">Tags: [c#](/tags/c%23), [code](/tags/code), [extension methods](/tags/extension%20methods), [silly](/tags/silly)</div>
