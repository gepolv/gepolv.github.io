---
layout: post
title:  ' Create a web service with Webjobs as backend on Azure '
categories: [azure web service, webjobs, azure blob, azure queue, Visual Studio 2015, .net 5]
---

In this post, I will show how to build a webservice with Azure webjobs as backend engine on Azure platform. 

My development platform is Visual Studio 2015 (Update 2) using .NET 5. Since Visual Studio and .NET evolve quickly, this post is effective as of 09/01/2016. 

Before we start off, since we are making use of Azure a lot, I assume you have an active Azure account and already set up your storage account.

Let us start off. 
1: we create a Web API project by "New->Project->Visual C#->Web->ASP.NET Web Application"

[Figure1]

Then select ASP.NET 5 -> WEB API. Note his project requires "no authentication" and uncheck "host in the cloud", as shown in picture.
[Figure2]

Now we already have a project ready. Let us take a look by pressing Ctrl+F5:
[Figure 3]

You may notice the address bar shows the current URL as "localhost:38229/api/values" instead of "localhost:38229". Why do we have a suffix "/api/values" in our address bar by default? It is specified as "launchUrl" in "launchSetting.json".
[Figure 4]

The actual handling of route "/api/values" is in a controller defined in "ValuesController.cs". The route is specified by an annotation "[Route("api/[controller]")]" which is the underneath king controlling the routing.  It is a naming convention to name the route as a substring  of the file name defining the controller. For example, if your have controller file "AbcController.cs", then your route is "../Abc". So in our case, the file name is "ValuesController.cs", so the route is "../values" (case insensitive). See the following picture for detailed explanation.
[Figure 5]

Now combined with the above knowledge, let us tweak it a bit.
First, let us change the default launching webpage. We first find this line in "launchSetting.json"

<code>"launchUrl": "api/values"</code>

change it to :

<code>"launchUrl": ""</code>

Now you press "Ctrl+F5", you will get a "404" page.

[Picture 6]

Do not be surprised. It is expected because this project is a WEB API project without view.

Let us play the routing:
find the following function in "ValuesController.cs"

<code>
public string Get(int id)
{
  return "value";
}
</code>

changed to:

<code>
public string Get(int id)
{
  return "value: "+id;
}
</code>

Then Ctrl+F5, input "/api/values/1" in your address bar, see what is the result.


Since we are creating Azure Webjobs, we need use "Azure queue". To do that, we need have our Azure storage ready and configure the connection string correctly. But before that, we need install some packages using NuGet.

Right-click project name in Solution Explorer and choose Manage NuGet Packages.
Search "WindowsAzure.Storage" and install.
Search "ConfigurationManager" and install.

[Picture 7]

Please pay attention to the red rectangles.

After I installed the packages, I got an error in the dependency list:

NU1002 The dependency WindowsAzure.Storage 7.2.0 in project github does not support framework DNXCore,Version=v5.0.

[Picture 8]

Tried many approaches and no luck. So I have to manually remove it from my project.
[Picture 9]
Afert removal of "dnxcore50", it looks like this:
[Picture 10]

[link: http://stackoverflow.com/questions/39156147/configuring-azure-blob-access-in-asp-net-5/39156417?noredirect=1#comment65695019_39156417] shows how to do the configuration work for .net 5. 

1: create an item in "appsettings.json" and put the correct information there which you can get from your Azure portal "Storage Account".

[Picture 11]

2: Modify "Startup.cs":
[Picture 12]

3: modify ValuesController.cs to configure Azure queue.

using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
using Microsoft.WindowsAzure.Storage.Queue; // Namespace for Blob storage types

using Microsoft.Extensions.OptionsModel;
using Newtonsoft.Json;

public class ValuesController : Controller
{
      private AzureStorageConfig _storageConfig;
      public ValuesController(IOptions<AzureStorageConfig> config)
      {
          _storageConfig = config.Value;
      }

      // GET: api/values
      //usage: api/values?v1=10&v2=5 in your browser's address bar
      [HttpGet]
      public string Get(int v1, int v2)
      {
          CloudStorageAccount storageAccount;
          storageAccount = new CloudStorageAccount(new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey), false);
          // Next, create a queue client
          CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
          // Then retrieve a reference to a queue – here’s where you give your queue a name
          CloudQueue queue = queueClient.GetQueueReference("numberqueue");

          // Create the queue if it doesn’t already exist
          queue.CreateIfNotExists();

          // Penultimate step – create a message and add it to the queue.
          var messageItem = new { add1 = v1, add2 = v2 };
          CloudQueueMessage message = new CloudQueueMessage(JsonConvert.SerializeObject(messageItem));

          // Finally, add your message to the queue
          queue.AddMessage(message);
            
          return "result: "+ (v1 + v2);
      }
      
      ////......
}


Here is what the above happens. When you issue "yourwebsite.com/api/values?v1=10&v2=5", the handling is in this function "public string Get(int v1, int v2)". We first set up Azure queue and put the values of "v1" and "v2" as an object into queue which will be consumed by Azure Webjobs later. After that, we return the result to the browser.


Next,
create a webjob project. 
[Picture 13]

Specify where the Webjobs should retrieve message.

"App.config"
<add name="AzureWebJobsDashboard" connectionString="" />
<add name="AzureWebJobsStorage" connectionString="" />

There are two connection string placeholders. One is for Azure storage and the other one is for dashboard displaying on Azure Portal. To get the connection string for "AzureWebJobsStorage", please refer to  https://azure.microsoft.com/en-us/documentation/articles/storage-configure-connection-string/. They can share the same connection string. But the "AzureWebJobsStorage" also need to be configured on Azure Portal.

[Figure 14]


THen let us take a look at the code which does the real job. Open file "Function.cs" under Webjob project.

[Figure 15]

For more information about parameter of webjob functions, please refer to :https://azure.microsoft.com/en-us/documentation/articles/websites-dotnet-webjobs-sdk-storage-queues-how-to/

Then publish the two projects by right click the project names.

last, we can see the webjob on Azure portal.
[Figure 16]

if everything is correct, you can see the result by checking the log of a webjob.
[Figure 17].

However if you see:
[Figure 18]

It means your "AzureWebJobsDashboard" connection string is not set correctly.

