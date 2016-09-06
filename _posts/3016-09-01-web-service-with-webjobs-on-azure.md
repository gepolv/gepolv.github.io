---
layout: post
title:  ' Create a web service with Webjobs as backend on Azure '
categories: [azure web service, webjobs, azure blob, azure queue, Visual Studio 2015, .net 5]
---

In this post, I will show how to build a webservice with Azure webjobs as backend engine on Azure platform. 

My development platform is Visual Studio 2015 using .NET 5. Since Visual Studio and .NET evolve quickly, this post is effective as of 09/01/2016. 

Before we start off, since we are making use of Azure a lot, I assume you have an active Azure account and already set up your storage account.

Let us start off. 
1: we create a Web API project by "New->Project->Visual C#->Web->ASP.NET Web Application"

[Figure1]

Then select ASP.NET 5 -> WEB API. Note his project requires "no authentication" and uncheck "host in the cloud", as shown in picture.
[Figure2]

Now we already have a project ready. Let us take a look by pressing Ctrl+F5:
[Figure 3]

You may notice the address bar shows the current URL as "localhost:38229/api/values" instead of "localhost:38229". Why do we have a suffix "/api/values" in our address bar by default? It is controlled by sepcifying "launchUrl" in "launchSetting.json".
[Figure 4]

The actual handling of route "/api/values" is in a controller defined in "ValuesController.cs". It is a naming convention to name the route as a substring  



