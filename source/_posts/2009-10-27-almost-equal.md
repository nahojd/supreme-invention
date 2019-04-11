---
title: "Almost equal"
author: johan
date: 2009-10-27 01:12:32
updated: 2009-10-27 01:17:26
slug: almost-equal
tags: [.net, sql]
---

Had another interesting problem today. A test that really should have worked started failing most of the time. The test was designed to make sure that a certain DateTime value did not change on update under a special circumstance. It was designed something like this:
  

``` csharp 
[TestMethod]
public void Updating_Foo_Will_Not_Update_ChangeDate()
{
    //Create a Foo to work with
    Foo foo = new Foo()
    {
        Name = "Update test foo",
    };

    foo = repository.SaveFoo( foo );
    DateTime lastChanged = foo.Changed.Value;

    foo.Bar = new Bar { Id = 1 };
    repository.SaveFoo( foo );

    var updatedFoo = repository.GetAllFoo().WithId( foo.Id );

    Assert.AreEqual( lastChanged, updatedFoo.Changed.Value );
}
```





This assertion, as you might imagine, was not fulfilled. Stepping through the code showed that the *SaveFoo* method in the repository did not change the value of foo.Changed, as is was not supposed do. It did, however save the foo to the database. On a first inspection, the two dates also seemed to be exactly the same, so the assertion should have been correct. 

Or so I thought. When I looked closer at the dates, it turned out they weren’t exactly the same. Close, but not exactly. More specifically, the *Ticks* value of them differed. The value of *lastChanged* was 633922004866809617, while the value of *updatedFoo.Changed.Value* (that had made a round trip to the database) was 633922004866800000. Not a huge difference, less than 1 ms, but enough to make the test fail. How stupid I was, assuming that the precision of DateTime in .NET, and DateTime in SQL Server was the same! Further reading revealed that SQL Server has a DateTime precision of about 3.33 ms, while .NET has a precision of 100 ns. So everytime I saved the time to the database, it would change it! 

The solution? Well, since I didn’t really care about differences of a few milliseconds, I decided to extend the DateTime struct with a brilliant new comparison method:




``` csharp 
public static bool IsAlmostEqualTo( this DateTime dateTime1, DateTime dateTime2 )
{
    var diff = dateTime1 - dateTime2;
    return Math.Abs( diff.TotalMilliseconds ) <= 5;
}
```





Because, hey, if I can’t feel the difference, there is no difference! This also made the Assert a little prettier: 




``` csharp 
Assert.IsTrue( lastChanged.IsAlmostEqualTo( updatedFoo.Changed.Value );
```





Problem solved! :-)


<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:36a690d3-2017-4ddf-abed-0fd6f93590f9" class="wlWriterEditableSmartContent">Tags: [.net](/tags/.net), [sql](/tags/sql)</div>
