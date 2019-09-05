---
title: Announcing Xamarin Android Bindings for Adyen Checkout
author: johan
tags:
  - android
  - xamarin
  - adyen
date: 2019-09-04 11:11:33
---

I've been working on implementing [Adyen payments](https://www.adyen.com) for a customer lately. They have been using another PSP for many years, but are now switching to Adyen. This is super easy on the web site, but as it turns out, not so easy in the mobile app.

Adyen offers a lot of sdk's, including an [Android SDK](https://docs.adyen.com/checkout/android/components). The app I'm working on, however, is developed in Xamarin, and unfortunately, Adyen does not offer a Xamarin SDK. That means that in order to use the Android SDK, we have had to create Xamaring bindings for the java SDK.

We have created a set of Xamarin Android Bindings for the Adyen Checkout components. So far, we have only implemented the Card Component and the Redirect Component, because that was all we needed at the time. 

The components are available as NuGet packages:

- [Adyen Checkout Card UI](https://www.nuget.org/packages/Approach.Adyen.CardUI.Droid/)
- [Adyen Checkout Redirect](https://www.nuget.org/packages/Approach.Adyen.Redirect.Droid/)

The source code is available at [our Github account](https://github.com/apprch/adyen-xamarin-android-binding), should you want to build your own components, or maybe fix a bug or two. There is also a Demo app in the github repository, which should help you use the components. So yeah, that's our first official public open source project!

I have published {% post_link Some-Problems-and-Solutions-When-Creating-Xamaring-Android-Bindings 'a follow up post' %} where I dwelve a little deeper into the problems I ran into while creating Xamarin Bindings for Android, and how to fix some of them. So check that out as well, if you're into that kind of stuff!