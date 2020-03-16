---
title: Calling the Swish Payment API from Azure AppService
author: johan
tags:
  - azure
  - dotnet core
  - swish
date: 2020-03-16 10:44:58
---


For the last couple of months, I've been working on a new version of a site for a client that uses Swish for payments. This new version will be hosted as an Azure App Service. The [Swish API](https://www.swish.nu/developer#swish-for-merchants) is pretty nice and straight forward, but for some reason they have implemented authentication and security using client certificates instead of something like [OAuth 2](https://oauth.net/2/). This makes it a bit more difficult, especially in Azure, since neither the server certificate for the API or the client certificates are signed by trusted authorities.

In fact, during my research[^1] I found many claims that it simply does not work, that you have to use a virtual machine in order to make the calls to the Swish API work. However, I also [this answer on Stack Overflow](https://stackoverflow.com/a/60428617) that claimed that the trick was simply to upload all certificates to Azure, and this turned out to be true. 

So, in order to remember this for the next time, and hopefully help anyone else with the same problem, I decided to write a more comprehensive guide on how to get this working.

## 1. Download the simulator certificates

All examples will be based on the Swish Test Merchant certificates, which can be downloaded from [the Swish developer page](https://www.swish.nu/developer#swish-for-merchants) (click View Guide in the Simulator Guide box[^2]). Extract the downloaded file and locate the file named `Swish_Merchant_TestCertificate_1234679304.p12` (or whatever they may have changed it to), and change the extension to `pfx`, since that is the extension that Azure will expect later[^3].

## 2. Extract all the root certificates from the .p12/.pfx file

The _.pfx_ file contains the whole certificate chain, and when working on a Windows machine, it will be enough to install that to your CurrentUser och LocalMachine store (depending on how you run your application), but in Azure you will need to upload all certificates separately. It is therefore necessary to extract the certificates from the file. This can be done in a number of different ways. If you _are_ working on a Windows machine, you could just install the certificate, and then go into the certificate store and export the resulting certificates.

However, you can also do it from the command line with [openssl](https://www.openssl.org/). Now, I'm no expert on openssl, and I'm sure there is a better way to do this, [this answer on the Unix & Linux StackExchange](https://unix.stackexchange.com/a/393484) for example suggests piping the result through `sed`, but we're only doing this once, so this works good enough.

First, list the certs in .pfx file and send the results to a text file[^4].

`openssl pkcs12 -nokeys -info -in ./Swish_Merchant_TestCertificate_1234679304.pfx -passin pass:swish > all_the_certs.txt`

Then, open the text file in a text editor, and locate _the two last certificates_ (the first one is your actual client certificate, you can ignore that for now). Now, copy everything from and including `-----BEGIN CERTIFICATE-----` to (and still including) `-----END CERTIFICATE-----` and paste it to a new file called `Swedbank_Customer_CA1_v1_for_Swish_Test.cer`, which should then look like this:

```text
-----BEGIN CERTIFICATE-----
MIIFvzCCA6egAwIBAgIQbPUmsaJntOLs9L2jgZxlQzANBgkqhkiG9w0BAQ0FADBq
MSswKQYDVQQDDCJTd2VkYmFuayBSb290IENBIHYyIGZvciBTd2lzaCBUZXN0MREw
DwYDVQQFEwhTV0VEU0VTUzEbMBkGA1UECgwSU3dlZGJhbmsgQUIgKHB1YmwpMQsw
CQYDVQQGEwJTRTAeFw0xODAzMjAxMjU0MjJaFw0zODAzMjAxMjIyMTJaMGoxKzAp
BgNVBAMMIlN3ZWRiYW5rIEN1c3RvbWVyIENBMSB2MSBmb3IgU3dpc2gxETAPBgNV
BAUTCFNXRURTRVNTMRswGQYDVQQKDBJTd2VkYmFuayBBQiAocHVibCkxCzAJBgNV
BAYTAlNFMIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEAl/wQeoczfPad
DPNIYJvhvwavDxIxGyGnQ2WDsXO7LQH7hz/TWNx0Ava6jRYT5pUubpBsALk/xEuA
uKjUEUAY5uumPn/nMNVeMmpnqMHs7A3kfkr8F+o4AUM3mIgVe3inMF/mvlASfpGp
4TS0ZCLZLE+OiG5REecVmgjn0i/JorXtXMGSWoqbAZCpqgS9uD3MQb9ua7TRTvkQ
knUH30/GaX5i8KK/r45SRXBRLTxxk0ySk4AcUR21TLb2WOMV9BbBwdq336mSErgz
wMy6G5EGlNhety3g+QRoc1ou0+oMw9tLdgsrhIx9opHF/M+E8bXXje5WZ8d9eyF/
Eq0kIqvWm4c3TzLPS43DNhJCOBGO6GMV8neuXM0TKiBrub3/yli5BapCH62SENEF
ZJG/ZB9fnRupDsn5vlt3MwtbipjnNhH4umcKacF/YtBdCUh1+CQ3U6lx2OolCYZG
cn1YDD7DNzb5I71TTaWyC+FgbXFtGX/48UXBIrqc9A054o76A4eYKM+GQPNz3B27
vZwzRC7Xjryg4uimmYzNRdJ3jz1q9PllUsOq/ElNr0ALj3MLKqOOtItb91SyE+NP
gKbf0MiTYS2k4aTxpDuh972cz1UPgHFuixat3Zj42ZVRLyX4IcaPk7Vbh3GjkO66
1qvgR6xInJoUts8J9HHbXgtjllmsf2UCAwEAAaNhMF8wDwYDVR0TAQH/BAUwAwEB
/zARBgNVHQ4ECgQITvODy60zFEAwFAYDVR0gBA0wCzAJBgcqhXCBbQEBMBMGA1Ud
IwQMMAqACEemPD8kK8dIMA4GA1UdDwEB/wQEAwIBBjANBgkqhkiG9w0BAQ0FAAOC
AgEAdVwklqAdT3Ztn4RgLcg+hPKtu5wVYVqOnilVqQHSWx/ygcgUMspvZ+N6qYIQ
NgBXTKaIUX6vFK4wvTgEaZnBUkuyYR9p/ZrAz7AjfdeUi2RRt5OLFAWwdddUZHC7
qvRG6bzreUZvgrZ6lh0ercl07b9xSr8pc1lNy3ksbkW2lvTxBeRQDKJJQ53EYA9I
/fFJ7H6vsUhWdn4x3GRLuDoOp0x6BdKGuSR06p5kjgytPCXxJt1QCnCvCsWKUZfe
4XmASXG/Kku7dih9uDl2CWAfCWwu6eUSZxRLxWM0Htb1semJRo0AMsRW4Xb9uWtw
i3XKT9oZbRDjaOn5NPPfWfYufjzh6AodH6aftSWns7MKSvhif+2mBBcF8mVySD2u
SQZUNJ9YLBk26ii0jQ1k6ll5fCWutcEkvvdswq6R9cDm04DYKJionClg2isREy9m
9PGWUntX3qL17fRTAAvTP3I918QTnMDEjIq5PaOxAgcuhoepweVbFHmyWLO04xpf
DkHghRnId3UV2XELlKGcmuobuBsPWeSMEGDg96z49owzDBm2W6PFw0t7wwrbQN6J
VCSlxq1hRaehRdYfqIcnvCbRZtsR1p6oQMKUK17NUyyr7OLLm+BUBpi0g8fPuV5h
g7XJuK5Z3/D+uKRYfpw4SnSyk3s4RfDg4M2rgE80lplYnE8=
-----END CERTIFICATE-----
```

Repeat for the second certificate, but call the file `Swedbank_Root_CA_v2_for_Swish_Test.cer`.

## Upload the certificates to Azure and configure your App Service

Now you need to upload the certificates to you App Service in Azure. Log in to your Azure Portal and navigate to _TLS/SSL settings_ in you App Service. Click on the _Private Key Certificates (.pfx)_ tab, and then on _Upload Certificate_.

{% asset_img upload_private_cert_to_azure.png "In case you really can't understand the instructions, this is where you should click!" %}

Select the `Swish_Merchant_TestCertificate_1234679304.pfx` file[^5] and enter the password (which is just _swish_ for the simulator certificate). Click _Upload_ and you should see the certificate in the _Private Key Certificates_ list. If you do this right now, you will also get a warning that the certificate is about to expire, which is true, but there is no newer certs yet, so we'll just have to repeat this process in a month or two.

Next, you need to upload the public CA certs that you extracted earlier. Click on the _Public Key Certificates (.cer)_ tab, and then on _Upload Public Key Certificate_. Select the `Swedbank_Customer_CA1_v1_for_Swish_Test.cer` file, and give it a name, something like `Swedbank Customer CA1 v1 for Swish Test` maybe? Click _Upload_ and to the same for the other certificate.

{% asset_img public_certs_uploaded_to_azure.png %}

In order for you App Service to be able to access the certificates, you need to set the environment variable `WEBSITE_LOAD_CERTIFICATES`. The value should be either `*` if you want to load all certificates, or a comma separated list of thumbprints if you want to limit which certificates that are available to your application. So go to _Configuration_ -> _Application settings_ and click _New application setting_ to add the setting.

_NOTE: If you have multiple slots in your App Service, you have to do this for all slots!_

In order to make sure that the certificates are uploaded correctly, and available to your application, you can use the debug console. Go to _Advanced Tools_ (under the Development Tools heading), ang click _Go_ to open it in a new window. Select _Debug Console_ -> _PowerShell_ in the top menu.

```PowerShell
Kudu Remote Execution Console
Type 'exit' then hit 'enter' to get a new powershell process.
Type 'cls' to clear the console

PS D:\home> dir cert:/CurrentUser/my
dir cert:/CurrentUser/my

   PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\my

Thumbprint                                Subject
----------                                -------
B71A8C2A7E0535AB50E4D9C715219DEEEB066FA4  C=SE, O=Swedbank AB (publ), SERIAL...
88884499BFFEF7FA80AB2CE2336568AE49C6D313  C=SE, O=Swedbank AB (publ), SERIAL...
76B6E2CB1BBA1BBC8A0C276AEF882B16AC48E7E0  CN=1233782570, O=5564010055, C=SE
```

You should be able to see all the certificates you have uploaded here. Take note of the thumbprints, you will need them shortly!

## Use the client certificates when connecting to the Swish REST API

These examples will use the [HttpClient](https://docs.microsoft.com/en-us/dotnet/api/system.net.http.httpclient?view=netcore-3.1) in dotnet core 3.1, because that is what I've been using. What we need to do is create our `HttpClient` with a custom `HttpClientHandler` that includes all the client certificates. This also lets us handle the validation failure that will occur for the server certificate, since it is not signed by a trusted authority.

The first step is to load all the certificates. You will need to pass all the thumbprints, both for your private client certificate and for the public certs you extracted.

```csharp
public X509Certificate2Collection GetCertificates(string[] thumbprints)
{
    var certStore = new X509Store(StoreName.My, "CurrentUser");
    certStore.Open(OpenFlags.ReadOnly);

    var certificates = new X509Certificate2Collection();

    foreach(var thumbprint in thumbprints) {
        var certs = certStore.Certificates.Find(X509FindType.FindByThumbprint, thumbprint, false);
        certificates.AddRange(certs);
    }

    return certificates;
}
```

The next step is to create our HttpClient with the custom HttpClientHandler:

```csharp
public HttpClient CreateClient()
{
    var handler = new HttpClientHandler()
    {
        //The server certificate is not signed by a trusted authority,
        //so we need to override the validation
        ServerCertificateCustomValidationCallback = (sender, certificate, chain, sslPolicyErrors) =>
        {
            //If you want to, you can check that the server certificate
            //thumbprint matches the expected. However, just recently they
            //changed the certificate without notifying anyone, so this
            //may cause problems. You could also just return true and be done with it.
            var match = certificate?.Thumbprint == serverCertThumbprint;
            return match;
        }
    };

    handler.ClientCertificateOptions = ClientCertificateOption.Manual;

    var clientCerts = GetCertficates(/* Pass your array of thumbprints here */);
    foreach (var cert in clientCerts)
    {
        handler.ClientCertificates.Add(cert);
    }

    return new HttpClient(handler);
}

```

And that should be it! Unless I forgot something. If you follow this guide, and still have problems, please comment below, and I will try to update the guide. I do have it working now, so I should be able to figure out the problem!

[^1]: Yes, that is what we call googling now!
[^2]: This will almost certainly be incorrect within minutes of publishing this, since they seem to change it between every time I look!
[^3]: p12 and pfx are just two different extension for the same certificate format, PKCS12.
[^4]: The password for the simulator certificates really is just _swish_. For production certs, you will of course need to use the real password.
[^5]: You renamed it, remember?
