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

Then select ASP.NET 5

