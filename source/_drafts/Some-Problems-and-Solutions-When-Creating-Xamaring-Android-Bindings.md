---
title: Some Problems and Solutions When Creating Xamaring Android Bindings
author: johan
tags:
    - android
    - xamarin
    - adyen
---

As announced in {% post_link Announcing-Xamarin-Android-Bindings-for-Adyen-Checkout 'my last post' %}, we recently created Xamarin Bindings for the Adyen Android SDK. In this post, I thought I would share som experiences in creating those bindings, like what I kind of problems we ran into, and how we fixed them.

The process of creating Xamaring bindings can be a bit tricky. The process is documented at [docs.microsoft.com](https://docs.microsoft.com/en-us/xamarin/android/platform/binding-java-library/), but I struggled quite a while in getting it to work. 

First of all, you need the actual Android libraries that you want to create the bindings for. These are (at least in this case) available at [jcenter](https://jcenter.bintray.com/com/adyen/checkout). Then you need to figure out exactly which libraries you need. In order to do this you can look in the `*.pom` file for a specific library to find out what other libraries it depends on. 

Adyen advocates the use of their [Drop-in solution](https://docs.adyen.com/checkout/android/drop-in) which includes all supported payment types, but this also means that we would have to create bindings for all those libraries. This would amount to about __25__ different libraries! However, many of the payment types supported were not interesting to us, at least not right now. So instead we opted to use only the Card Component and the Redirect Component, which would only require us to create bindings for 7 libraries[^1].

There are a couple of different ways to create bindings, but as Adyen provides `AAR` files, I basically followed the steps on the [Binding an .AAR page](https://docs.microsoft.com/en-us/xamarin/android/platform/binding-java-library/binding-an-aar). This means creating a separate Xamarin Bindings Library for each AAR file, and the easiest way is to start at the "bottom", and create a binding for the library that does not have any other java dependencies, in this case [adyen-cse](https://github.com/apprch/adyen-xamarin-android-binding/tree/master/AdyenCse) and work you way up, adding references to the other bindings as you go along. The Android dependencies in the POM files can simply be added as NuGet package references. Then you compile the project.

## It won't compile!

Right. Most of the time, when you create a binding, add the AAR file and try to compile, it won't work the first time. This could be due to a number of problems, but in this project, I've mainly had a handful of problems, which I'll elaborate further below.

### Problem 1 - Wrong return type

Sometimes the generated code will have the wrong return type. This is often because of the difference between how interfaces and generics work in Java and C#. 

For example, in the [original code for LogoConnection in base-v3](https://github.com/Adyen/adyen-android/blob/master/base-v3/src/main/java/com/adyen/checkout/base/api/LogoConnection.java), the `call()` method returns a `BitmapDrawable`, which is ok, since the class implements the interface `java.util.concurrent.Callable<T>`, which is a generic interface, so you can have call() return a specific type. 

In Xamarin, however, the interface `java.util.concurrent.Callable` is not generic (I don't know why), and thus `LogoConnection.Call()` must have a return type of `Java.Lang.Object`. In the generated code, however, the return type is still BitmapDrawable. Fortunately, this is an easy fix!

Every generated method and class has method/class reference as a comment above it. This can be used to modify the generated code in the `Metadata.xml` file. On of the modifications that can be made is to change the return type. The following node changes the return type of the call method to `Java.Lang.Object`:

```xml
<attr path="/api/package[@name='com.adyen.checkout.base.api']/class[@name='LogoConnection']/method[@name='call' and count(parameter)=0]" name="managedReturn">Java.Lang.Object</attr>
```

The path is just copied from the comment above the method in the generated code, but it is pretty straight forward anyway. 

### Problem 2 - Wrong parameters

Another problem that can occur, and that is related to the previous one is that sometimes generated methods have the wrong parameter types. This is not quite as easily fixed, as I have not found a way to modify the parameters of a method solely by a Metadata.xml node. 

Example: In [com.adyen.checkout.base.ui.adapter.ClickableListRecyclerAdapter](https://github.com/Adyen/adyen-android/blob/master/base-ui/src/main/java/com/adyen/checkout/base/ui/adapter/ClickableListRecyclerAdapter.java), the `onBindViewHolder` method takes a generic `ViewHolderT` as the first parameter. But in the generated code, `ClickableListRecyclerAdapter` is no longer generic, so `OnBindViewHolder` instead takes a `Java.Lang.Object`, as can be seen in the snippet below:

```csharp
// Metadata.xml XPath method reference: path="/api/package[@name='com.adyen.checkout.base.ui.adapter']/class[@name='ClickableListRecyclerAdapter']/method[@name='onBindViewHolder' and count(parameter)=2 and parameter[1][@type='ViewHolderT'] and parameter[2][@type='int']]"
[Register ("onBindViewHolder", "(Landroid/support/v7/widget/RecyclerView$ViewHolder;I)V", "GetOnBindViewHolder_Landroid_support_v7_widget_RecyclerView_ViewHolder_IHandler")]
public override unsafe void OnBindViewHolder (global::Java.Lang.Object viewHolderT, int position)
{
    const string __id = "onBindViewHolder.(Landroid/support/v7/widget/RecyclerView$ViewHolder;I)V";
    IntPtr native_viewHolderT = JNIEnv.ToLocalJniHandle (viewHolderT);
    try {
        JniArgumentValue* __args = stackalloc JniArgumentValue [2];
        __args [0] = new JniArgumentValue (native_viewHolderT);
        __args [1] = new JniArgumentValue (position);
        _members.InstanceMethods.InvokeVirtualVoidMethod (__id, this, __args);
    } finally {
        JNIEnv.DeleteLocalRef (native_viewHolderT);
    }
}
```

However, since ClickableRecyclerAdapter inherits from `Android.Support.V7.Widget.RecyclerView.Adapter`, `OnBindViewHolder` needs to take a `RecyclerView.ViewHolder` as its first argument. The solution to this problem - and many others - is to remove the generated method in the Metadata.xml, and add a modified version in the `Additions` folder:

```xml
<remove-node path="/api/package[@name='com.adyen.checkout.base.ui.adapter']/class[@name='ClickableListRecyclerAdapter']/method[@name='onBindViewHolder' and count(parameter)=2 and parameter[1][@type='ViewHolderT'] and parameter[2][@type='int']]" />
```

```csharp
//Namespace should match that of the generated class
namespace Com.Adyen.Checkout.Base.UI.Adapter
{
    //Note that this is a partial class
    public partial class ClickableListRecyclerAdapter
    {
        //This code is identical to the generated code above,
        //except for the type of the first parameter
		[Register("onBindViewHolder", "(Landroid/support/v7/widget/RecyclerView$ViewHolder;I)V", "GetOnBindViewHolder_Landroid_support_v7_widget_RecyclerView_ViewHolder_IHandler")]
		public override unsafe void OnBindViewHolder(RecyclerView.ViewHolder viewHolderT, int position)
		{
			const string __id = "onBindViewHolder.(Landroid/support/v7/widget/RecyclerView$ViewHolder;I)V";
			IntPtr native_viewHolderT = JNIEnv.ToLocalJniHandle(viewHolderT);
			try
			{
				JniArgumentValue* __args = stackalloc JniArgumentValue[2];
				__args[0] = new JniArgumentValue(native_viewHolderT);
				__args[1] = new JniArgumentValue(position);
				_members.InstanceMethods.InvokeVirtualVoidMethod(__id, this, __args);
			}
			finally
			{
				JNIEnv.DeleteLocalRef(native_viewHolderT);
			}
		}
	}
}
```

### Problem 3 - Missing method

In at least one case, the generated code was simply missing a method that was required by the base class or interface. The method for fixing this is pretty much like described above, although you obviously don't need to remove it in metadata.xml. You also have to figure out how the method should be implemented, but that is not as difficult as it sounds, as all implementations follow the same pattern.

In my case, the generated class `Com.Adyen.Checkout.Card.CardListAdapter` was missing the `OnBindViewHolder` method, which is required by the `RecyclerView.Adapter` base class, and is obviously present in the [original code](https://github.com/Adyen/adyen-android/blob/master/card-ui/src/main/java/com/adyen/checkout/card/CardListAdapter.java). 

This solution, then, is to add a partial CardListAdapter class in the Additions folder, and add the OnBindViewHolder implementation in it. In this case it was very easy, since I could just basically copy the OnBindViewHolder implementation from ClickableListRecyclerAdapter above (or any other class that has it).

### Problem 4 - Other unfixeable problem -> Kill it!

Sometimes you will get another problem, that is not as easy to fix, for whatever reason. In many cases, you can solve this problem just by _removing the offending method altogether_. If it is not a method that you need to call directly from the app, and not a method that is required for implementing an Interface or an abstract base class, you can probably remove it with a `remove-node` line in Metadata.xml and be done with it.

The reason for this is, of course, that once the call to a native method has been made, for example with `InvokeVirtualVoidMethod` as above, subsequent calls will be completely native, so it doesn't matter if the methods have .NET wrappers or not. At least that is my understanding of it. 

## Bug in the AAR file

When I tried to use the Card Component in the Demo App, I got the build error `Multiple substitutions specified in non-positional format; did you mean to add the formatted="false" attribute?`. Turns out there is (at least at the time of writing) a bug in [strings.xml in the card-ui library](https://github.com/Adyen/adyen-android/blob/master/card-ui/src/main/res/values/strings.xml).

```xml
<resources>
    <!-- snip -->
    <string name="expires_in">%s/%s</string>
    <!-- snip -->
</resources>
```

Turns out you can't have multiple `%s` in a string resource [because of reasons](https://stackoverflow.com/questions/4414389/android-xml-percent-symbol/4417333#4417333). If you do, you need to add `formatted="false"` to the node. I fixed this by editing the AAR file (it's just a zip file, really), and adding the attribute in `/res/values/values.xml` (which is a squashed version of all xml files in the res folder).

Unfortunately, this means I had to check in the modified AAR file. For the rest of the files, I have a [Cake](https://cakebuild.net/) build script that just downloads all the AAR files from jcenter. But hopefully it will be fixed in the next release of card-ui.

I hope someone who has to create Xamarin Bindings will find this rather long and unstructured post useful. If nothing else, it will help me remember the problems I had and how I solved them for the next time. 

[^1]: Actually, I finished the first version of these bindings in June. Unfortunately, just as I though I was done, I noticed that the Adyen developer documentation] had changed substantially. While I was working on this they had release an RC version of version 3.0, which was totally different from version 2.4.5 that I had been working on. So I basically had to start all over again and create new bindings for v3. The old bindings are available at github (tag: 2.4.5), and also at NuGet (Approach.Adyen.UI.Droid), should anyone be interested. But it's probably better to use the new ones.

