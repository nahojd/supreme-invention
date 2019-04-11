---
title: "Testing EPiServer sure takes a lot of mocking…"
author: johan
date: 2010-08-11 18:06:14
updated: 2010-08-11 18:06:14
slug: testing-episerver-sure-takes-a-lot-of-mockinghellip
tags: [episerver, tdd]
---

A quick follow up to [my last post](/archive/2010/08/11/unit-testing-saving-pages-in-episerver-cms-6.aspx). Even with [EPiAbstractions](http://github.com/joelabrahamsson/EPiAbstractions), testing takes **a lot** of mocking and setup code…

Testing this
  

``` csharp 
public void CreateOrtkoppling( int verksamhetsId )
{
    var ortContainer = GetOrtContainer() ?? CreateOrtContainer();

    CreateVerksamhetPage( ortContainer, verksamhetsId );
}

public PageData GetOrtContainer()
{
    return (from p in _dataFactory.GetChildren( _page.PageLink )
            where p.PageName == OrtContainerPageName
            select p).SingleOrDefault();
}

private void CreateVerksamhetPage( PageData ortContainer, int verksamhetsId )
{
    var verksamhet = _verksamhetRepository.Get( verksamhetsId );
    if (verksamhet == null)
        throw new ArgumentException( "Det finns ingen verksamhet med id " + verksamhetsId );

    var page = _dataFactory.GetDefaultPageData( ortContainer.PageLink, OrtPageTypeId );
    page.PageName = string.Format( "{0}, {1}", verksamhet.Ort, verksamhet.Besöksadress );
    page.Property[VerksamhetsIdProperty].Value = verksamhetsId;
    _dataFactory.Save( page, EPiServer.DataAccess.SaveAction.Publish, EPiServer.Security.AccessLevel.NoAccess );
}
```





took me this much code:




``` csharp 
private Mock<IDataFactoryFacade> dataFactoryMock;
private Mock<IVerksamhetRepository> verksamhetRepositoryMock;
private Mock<PagePropertyUtils> pagePropertyUtilsMock;
private Mock<IPageReferenceFacade> pageReferenceMock;
private PageData utbildningsPage;
private UtbildningPageHandler utbildning;

[SetUp]
public void Setup()
{
    int utbildningsPageId = 4711;
    utbildningsPage = CreatePageDataObject( "Testutbildning", utbildningsPageId );
    verksamhetRepositoryMock = new Mock<IVerksamhetRepository>();
    pagePropertyUtilsMock = new Mock<PagePropertyUtils>();
    pageReferenceMock = new Mock<IPageReferenceFacade>();
    dataFactoryMock = new Mock<IDataFactoryFacade>();
    dataFactoryMock.Setup( d => d.GetPage( It.Is<PageReference>( p => p.ID == utbildningsPageId ) ) )
        .Returns( utbildningsPage );
    utbildning = new UtbildningPageHandler( dataFactoryMock.Object, verksamhetRepositoryMock.Object, pagePropertyUtilsMock.Object, 
        pageReferenceMock.Object, utbildningsPageId );
}

private int MockOrtContainerPage()
{
    var pageName = GetPrivateField<string>( utbildning, "OrtContainerPageName" );
    int containerPageId = 17;

    var containerPage = CreatePageDataObject( pageName, containerPageId );

    dataFactoryMock.Setup( d => d.GetChildren( It.Is<PageReference>( p => p.ID == utbildningsPage.PageLink.ID ) ) ).Returns(
        new PageDataCollection( new List<PageData> {
                                containerPage
                            } )
    );
    return containerPageId;
}

protected static PageData CreateDefaultPageDataObject()
{
    var page = new PageData();
    page.Property.Add( "PageName", new PropertyString() );
    var propertyPageReference = new PropertyPageReference( PageReference.EmptyReference );
    SetPrivateField<Guid>( propertyPageReference, "_pageGuid", Guid.NewGuid() );
    page.Property.Add( "PageLink", propertyPageReference );
    page.ACL = new EPiServer.Security.AccessControlList();
    return page;
}

protected static T GetPrivateField<T>( object o, string fieldName )
{
    Type type = o.GetType();
    BindingFlags privateBindings = BindingFlags.NonPublic | BindingFlags.Instance | BindingFlags.Static;
    FieldInfo match = type.GetField( fieldName, privateBindings );
    if (match != null)
    {
        return (T)match.GetValue( o );
    }
    return default(T);
}

[Test]
public void CreateOrtkoppling_Will_Create_Page_And_Save_VerksamhetsId_As_Property()
{
    var containerPageId = MockOrtContainerPage();
    var verksamhet = new Verksamhet { Id = 17, Besöksadress = "Gatan 1", Ort = "Staden", Postadress = "123 45" };
    verksamhetRepositoryMock.Setup( v => v.Get( verksamhet.Id ) ).Returns( verksamhet );
    var defaultPageData = CreateDefaultPageDataObject();
    var propertyName = GetPrivateField<string>( utbildning, "VerksamhetsIdProperty" );
    defaultPageData.Property.Add( propertyName, new PropertyNumber() );
    var ortPageTypeId = 19;
    pagePropertyUtilsMock.Setup( p => p.GetPropertyValue<int>( It.IsAny<PageReference>(), "UtbildningsOrtPageType" ) ).Returns( ortPageTypeId );
    dataFactoryMock.Setup( d => d.GetDefaultPageData( It.Is<PageReference>( p => p.ID == containerPageId ), ortPageTypeId ) )
        .Returns( defaultPageData );
    dataFactoryMock.Setup(d => d.Save(defaultPageData, EPiServer.DataAccess.SaveAction.Publish, EPiServer.Security.AccessLevel.NoAccess))
        .Callback<PageData, EPiServer.DataAccess.SaveAction, EPiServer.Security.AccessLevel>( ( pageToSave, saveAction, accessLevel ) =>
        {
            var id = (int)pageToSave.Property[propertyName].Value;
            Assert.That( id, Is.EqualTo( verksamhet.Id ) );
            Assert.That( pageToSave.PageName, Is.EqualTo( string.Format( "{0}, {1}", verksamhet.Ort, verksamhet.Besöksadress ) ) );
        } );

    utbildning.CreateOrtkoppling( verksamhet.Id );

    dataFactoryMock.VerifyAll();
    verksamhetRepositoryMock.VerifyAll();
}
```





Now, most of that code (everything except for the actual test) is reused in other tests, but still! It’s a bit exhausting!


<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:f8c4448c-a559-49fa-b0b3-12ef18ded15f" class="wlWriterEditableSmartContent">Tags: [episerver](/tags/episerver), [tdd](/tags/tdd)</div>
