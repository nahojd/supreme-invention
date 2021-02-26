---
title: Loading private certificates from a Dotnet Core app in an Azure Linux App Service
author: johan
date: 2021-02-26 20:09:42
tags:
	- azure
	- dotnet core
	- linux
---

This is somewhat a follow up to my previous post {% post_link Calling-the-Swish-Payment-API-from-Azure-AppService %}, although it is not about Swish at all. It is, however, about certificates in Azure. It is also way too long, if you're the impatient type, I've added a [TL;DR](#TL-DR)

Today, I was implementing authentication and signing with [BankID](https://www.bankid.com/utvecklare/rp-info), a popular electronic identification system in Sweden[^1], for a client. I was using a library called [Active Login](https://github.com/ActiveLogin/)[^2], which makes it a whole lot easier to implement. That is not really relevant to this post, though. What _is_ relevant is that in order to call the BankID API, you need to use a certificate issued by your bank.

I had previously only worked with private certificates in Azure in Windows-based App Services. This time, however, we're using Linux-based App Services. I had foolishly assumed that loading certificates in them would work the same way, since we're still using a dotnet core application. Obviously, this turned out to be a false assumption[^3].

When you want to use private certificates in Azure, you probably don't want to include them with your code[^4]. Instead you upload them to your App Service, either using the Azure Portal, or using the command line[^5].

{% asset_img azure-app-service-private-certificates.png 'This is a picture showing the place where you handle your certificates in an Azure App Service. Really, really exciting stuff.' %}

In a Windows-based App Service, when you upload private certificates to your app service, they end up in the Certificates Store, more specifically in the Personal Certificates store for the CurrentUser. They can then be loaded with these few lines of code:

```csharp
var certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadWrite);
var certs = certStore.Certificates.Find(X509FindType.FindByThumbprint, "THUMBPRINT OF CERTIFICATE", false);
```

This, however does not work in Linux. Correction: it does not work in a Linux-based App Service[^6], because there is no certificate store in Linux (not in the Windows way, at least). But wait, you say, you can still upload certificates through the Azure Portal in a Linux-based App Service! Where do they end up? Have no fear, child, that is what I am here to tell you!

If you, or rather I, had bothered to read the documentation, I, or perhaps you, would have found a page called [Use SSL cert in code, and maybe even the heading &ldquo;Load certificate in Linux/Windows containers&rdquo;](https://docs.microsoft.com/en-us/azure/app-service/configure-ssl-certificate-in-code#load-certificate-in-linuxwindows-containers). Here, it clearly states that private certificates in Linux containers[^7] are stored in `/var/ssl/private`, as long as you remember to set the environment variable `WEBSITE_LOAD_CERTIFICATES` to the thumbprint of your certificate (or `*` to load all of them).

{% asset_img app-service-cert-locations.png 'Had I found this table earlier, I would not have been able to charge as many hours today. So, yeah.' %}

Alright, so I opened a SSH session to my App Service, and typed in `ls /var/ssl/private`. Nothing. In fact, the directory did not even exist. Fortunately, my many hours of banging my head against the Swish implementation bare fruit, and I suddenly remembered that changes to the certificates _often does not apply unless you stop and start the App Service_. Restart is not enough, you have to Stop it! And then start it again. I checked again, and heureka! My certificate was there! In form of a name called `THUMBPRINTINCAPITALLETTERS.p12`[^8].

So, to answer the question that I may have promised to answer by naming this post &ldquo;Loading private certificates from a Dotnet Core app in an Azure Linux App Service&rdquo;:

You just load the file. They are not even encrypted anymore, so you don't need a password:

```csharp
var cert = new X509Certificate2("/var/ssl/private/THUMBPRINTINCAPITALLETTERS.p12");
```

So there. Easy, peasy!

Wow, I did not expect this post to be this long. I'd better add a TLDR. I obviously find writing way to fun. Also, I may have broken my previous number-of-footnotes-record!

## TL;DR

- Private certificates in Linux-based App Services are stored in `/var/ssl/private` and named as their `thumbprint.p12`.
- In order to load them from code, you can just pass the path to the constructor of `X509Certificate2`. It is not password protected.

[^1]: In fact, it is so popular that it is the de facto standard. Many therefore assume that it is governement run, but in reality it is run by the largest banks in Sweden!
[^2]: Active Login is created by Active Solution, which is actually a previous employer of mine!
[^3]: Otherwise, I would probably not have written this blog post.
[^4]: You also do not want to include api keys and passwords in your code, unless you want to be embarrassed on Twitter!
[^5]: Or so I am told. I have not tried it. Uploading certificates seems to be something I do quite rarely, so I have not really felt the need to automate it.
[^6]: As it turns out, you can use the certificate store in dotnet core running in Linux, you just have to add the certificates in code. Also, the cert store is internal to dotnet core, and I assume that is basically just there for Windows compatibility. You can read a lot more about it in [the App Service documentation](https://docs.microsoft.com/en-us/dotnet/standard/security/cross-platform-cryptography#x509-certificates).
[^7]: Which I guess is that Linux based App Services really are...
[^8]: It was not actually called _THUMBPRINTINCAPITALLETTERS.p12_, but I hope you get the point. I do, however, find it funny that you __MAY NOT__ upload a certificate with the .p12 extension, it __HAS TO BE__ .pfx, but after you uploaded it, it gets saved as .p12 anyway.
