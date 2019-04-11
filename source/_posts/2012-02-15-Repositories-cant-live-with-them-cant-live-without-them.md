---
title: "Repositories, can't live with them, can't live without them?"
extid: posts/9249
author: johan
date: 2012-02-15 00:19:21
updated: 2012-02-15 00:35:14
slug: Repositories-cant-live-with-them-cant-live-without-them
tags: [alpha, ravendb, architecture, code]
---

I always struggle with design. Systems design, that is, not graphical design (well, ok, that too…). One thing I never seem to be really happy with is [Repositories](http://msdn.microsoft.com/en-us/library/ff649690.aspx "I have not read this article, and I never will!"). They often seem to be a lot of work, and never really seem to fit.

Once upon a time, I used to follow a strict chain of command in the design:
**Controller –> Service –> Repository –> Database**
This naturally led to quite a lot of useless service methods, that did nothing but forward the call to the repository. Does this seem familiar?

``` csharp
public List<Bar> GetBarList() {
    return repository.GetBarList();
}

public void SaveFoo(Foo foo) {
    repository.SaveFoo(foo);
}
```

Not particularly useful. So when I started working on this blog engine (Alpha) almost a year and a half ago, I made the bold choice to allow the controller to speak directly to the repository, as well as the service! If nothing else, it spared me from writing a few brain-dead methods. The downside was that the responsibilities became a little less clear.

I also tried to avoid repeating myself in the repositories by creating a base repository class (EntityRepository), and letting the specific repositories inherit from than and extend it if necessary. And by the way, I’m using [RavenDB](http://ravendb.net/) for Alpha, so `Session` is an instance of `IDocumentSession`. And `IEntity` just makes sure that the entities have an Id property. Anyway, it looked something like this ([check out the Alpha source at revision dc0837bac57e for the real code](https://bitbucket.org/nahojd/alpha/src/dc0837bac57e/Alpha.Models/Repositories "Repositories as they used to look...")):

``` csharp
public interface IEntityRepository<T> where T : IEntity
{
    void Save(T entity);
    T GetById(string id);
}

public abstract class EntityRepository<T> : IEntityRepository<T> where T: IEntity
{
    //Ignoring how Session got there...

    public virtual void Save(T entity)
    {
        Session.Store(entity);
        Session.SaveChanges();
    }

    public virtual T GetById( string id )
    {
        return Session.Load<T>( id );
    }
}

public interface IFooRepository : IEntityRepository<Foo>
{
    Foo GetByBar(Bar bar);
}

public class FooRepository : EntityRepository<Foo>, IFooRepository
{
    public Foo GetByBar(Bar bar)
    {
        return Session.Query<Post>().SingleOrDefault(x => x.Bar == bar);
    }
}
```

Now, that’s a little bit nicer than writing the exact same code for every different entity. But we still have these repositories, that frankly doesn't do much. They are just ceremony. Sure, they abstract away the actual persistence method, the interfaces could look the same if I used SQL Server. Except that they wouldn't, since I would be using an int instead of a string for Id. And I have never ever had to change the data store in a project. And if I did, I would probably rewrite a lot of stuff. There's really no point in abstracting away your data store, it only forces you to use the lowest common denominator.

So, what to do? Well, for now, I'm just getting the rid of the repositories. I'm letting the controllers access the IDocumentSession directly. Since querying RavenDB is more or less the same as querying a generic list, I feel pretty safe letting the controllers do that, at least for the simple stuff. The more complicated stuff I'm moving to the services. So it will basically be the same architecture as before, just without the repositories.

But that's just the first step! After that, the services will have to go as well, in favor of intelligent queries and commands, in a more CQRS-ish way. I'm thinking something in line of [what Rob Ashton outlined in a great blog post last summer](http://codebetter.com/robashton/2011/06/13/finding-a-balance-with-asp-net-mv/). We'll see how it goes.

So far I've managed to get rid of all Repositories except the PostRepository. I'd give it a week, tops, then it's gone. So I guess you can live without them.

*Disclaimer: I realize that there are scenarios where Repositories are useful. For example, in the project I'm currently getting paid to work on (unlike Alpha), we get our data from services, which we use WCF to communicate with. This means a lot of mapping between data contracts and entities, error handling and other fun stuff. In this case, the Repositories actually do stuff, and can be justified.*