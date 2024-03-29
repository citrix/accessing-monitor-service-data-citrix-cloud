# How to access Citrix Monitor Service data using the OData v4 endpoint in Citrix Cloud

Use the OData version 4 endpoint based on ASP .Net Web API to access the Citrix Monitor Service data. It supports pagination and OData v4 endpoints return a maximum of 100 records per page with a link to the next 100 records. You can run aggregation queries on the Citrix Monitor Service data. This feature was not available in OData Version 3 or earlier.

To access the Citrix Monitor Service data, follow the prerequisites, use cases and walkthroughs of OData APIs and Powershell SDKs, and different access methods.

You can make API requests using the MS Excel PowerQuery, C#, PowerShell, or any tool that support invoking the REST API.

>**Note:**
>
> Accessing Citrix Monitor Service data using the OData v4 endpoint in Citrix Cloud is currently under preview.

## Prerequisites to access Citrix Monitor Service data using the OData v4 endpoint in Citrix Cloud

- Know the supported endpoints
- Generate Citrix Cloud bearer token. For more information, see [Get started with Citrix Cloud APIs](https://developer.cloud.com/getting-started/docs/overview) section.

>**Note:**
>
> To ensure optimal performance and resource utilization of the Delivery Controller, one OData query is permitted per customer at a time. Query time out is 30 seconds. If you exceed the limit of one request at a time, a **429 Too Many Requests** response status code is returned.

## Supported API Gateway endpoints

Use one of the following endpoints based on the geographical region you selected while accessing the Citrix Monitor Service data:

* US region: https://api-us.cloud.com/monitorodata
* EU region: https://api-eu.cloud.com/monitorodata
* AP-S region: https://api-ap-s.cloud.com/monitorodata
* Japan region: https://api.citrixcloud.jp/monitorodata

!!!tip "Note"
     * The "https://{Customer_Id}.xendesktop.net/Citrix/monitor/odata/v4/data" URL is replaced with "https://{ApiGatewayEndpoint}".
      The HTTP header "Customer" is now replaced with "Citrix-CustomerId". 
     * For Japan, the https://{Customer Id}.apps.citrixworkspacesapi.jp/Citrix/monitor/odata/v4/data URL is replaced with new Gateway Endpoint "https://api.citrixcloud.jp". The HTTP header "Customer" is now replaced with "Citrix-CustomerId".
     * For Government customers, the existing URLs and header name (Customer) remain unchanged.  
      
## HTTP error codes

See [HTTP error codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status) to know about the various error codes and their descriptions.

## Generate Citrix Cloud bearer token

You can obtain the bearer token using either of the following methods:

### Method 1: Citrix DaaS Remote PowerShell SDK

Generate Citrix Cloud bearer token using Citrix DaaS (formerly Citrix Virtual Apps and Desktops service) PowerShell SDK as follows:

1.	Download the Remote PowerShell SDK from [here](http://download.apps.cloud.com/CitrixPoshSdk.exe).
2.	Install the SDK on any computer in your resource location.
3.	Open a PowerShell command prompt. (administrator rights are not required)
4.	Add the Citrix snap-ins: `asnp citrix*`.
5.	Execute the cmdlet, `Get-XdAuthentication`, it prompts you for Citrix Cloud authentication.
6.	On successful authentication, the bearer token is stored in the current PowerShell session.
7.	Execute the cmdlet, `Get-Variable` to list all the variables in the current PowerShell session.
8.	The list contains a variable, `GLOBAL:XDAuthToken` containing bearer token as value.
9.	Copy the bearer token value to be used in the OData query.

### Method 2: Citrix Cloud API service

You may obtain the bearer token using the Citrix Cloud APIs. For more information on the steps, see [https://developer.cloud.com/getting-started](https://developer.cloud.com/getting-started)

For the details of the Monitor Service schema, see [Monitor Service Schema](https://developer-docs.citrix.com/projects/monitor-service-odata-api/en/7.16/#monitor-service-schema).

To determine the values returned by the Monitor Service OData API, see [Citrix.Monitor.Model](https://developer-docs.citrix.com/projects/monitor-service-odata-api/en/7.16/api-reference/Monitor.Model/).

The list of URLs for available data sets is available at [URLs for Available Data Sets](https://developer-docs.citrix.com/projects/monitor-service-odata-api/en/7.16/#urls-for-available-data-sets). Replace http://{dc-host}/Citrix/Monitor/OData/v4/Data with "https://{ApiGatewayEndpoint}".

### Use cases and walk-through of OData APIs ands PowerShell SDK

Here's a video explaining the various use cases of OData APIs and PowerShell SDK used in Citrix DaaS. 

[![OData APIs and PowerShell SDK use cases walk-through video](https://img.youtube.com/vi/OEik9pY9MeQ/0.jpg)](https://www.youtube.com/watch?v=OEik9pY9MeQ "OData APIs and PowerShell SDK use cases walk-through video")

# Different methods to access Citrix Monitor Service data using the OData v4 endpoint in Citrix Cloud

## Access Citrix Monitor Service data using MS Excel PowerQuery

Learn from the following example to access the Citrix Monitor Service data using MS Excel PowerQuery.

&#49;.	Open Excel (Excel 2016 has PowerQuery inbuilt. If you are using earlier versions of Excel, install PowerQuery, see [https://www.microsoft.com/en-in/download/details.aspx?id=39379)](https://www.microsoft.com/en-in/download/details.aspx?id=39379))

&#50;.	In Excel 2016, go to **Data** menu > **Get & Transform group** > **New Query** > **From Other Sources** > click **Blank Query**

![Blank Query](./blank-query.png)

&#51;. In the Query Editor window, go to **Home** menu > **Query group** > click **Advanced Editor**.

![Query editor window](./query-editor.png)

&#52;. In the Advanced Editor window, type the following query:

```js
let
    Source = OData.Feed
             (
"https://{ApiGatewayEndpoint}/Machines",
                          null,
                          [
                          	Headers =
                                    [
                                    	#”Authorization” = "<YourToken>",
                                     	#"Citrix-CustomerId" = "<YourCustomerId"
                                    ]
                          ]
               )
in
	Source
```

![Advanced editor](./advanced-query.png)

Replace `<YourCustomerId>` and `<YourToken>` with the values of CustomerID and bearer token you obtained as described in the Generate the Citrix Cloud Bearer Token section. Make sure that the `<YourToken>` is in the format, “CWSAuth bearer= <bearer token>”. Once the **No Syntax errors have been detected** is displayed in the bottom of the window, click **Done**.
	
&#53;. In case of first login, a Please specify how to connect message is displayed. Click Edit Credentials, the Access an OData feed window appears. Make sure that the Anonymous tab and the base URL are selected and click Connect.

&#54;. Upon successful authentication, the results are displayed as a table. In this example, the table lists all the Machines of the CustomerId. If the authentication fails, either the bearer token or the CustomerId might be invalid. Please verify the same and try again.

## Access Citrix Monitor Service data using C# client library

Learn from the following example to access the Citrix Monitor Service data using C#.

Steps to access Citrix Monitor Service data using C#
1.  Create a C# client for an OData v4 endpoint using a Simple.OData.Client. For information about installation of its Nuget Package, see [https://github.com/object/Simple.OData.Client/wiki/Getting-started-with-Simple.OData.Client](https://github.com/object/Simple.OData.Client/wiki/Getting-started-with-Simple.OData.Client).
1.  Install the package using the Nuget Package Manager:

```
Install-Package Simple.OData.Client
```

1.  After installation, use the package with the following command:

```
using Simple.OData.Client;
```

1.  As the OData query in the Cloud requires the Citrix Cloud authentication, the OData clients must pass the bearer token (as documented in the **Generate Citrix Cloud Bearer Token** section) and the CustomerId. Set up the headers as follows, before creating the OData query:

```js
ODataClientSettings settings = new ODataClientSettings();
settings.BeforeRequest += request =>
{
    request.Headers.Add("Authorization", "<BearerToken>");
    request.Headers.Add("Citrix-CustomerId", "<customerId>");
};

settings.BaseUri = new Uri("https://{ApiGatewayEndpoint}");
client = new ODataClient(settings);
```

1.  Use async methods to retrieve data. The following example lists all machines from Citrix DaaS site.

```js
public static async void GetMachines()
{
    var machines = await client.FindEntriesAsync("Machines");
    foreach(var machine in machines)
    {
        Console.WriteLine(machine["Id"]);
        }
}
```
## Access Citrix Monitor Service data using PowerShell

Learn from the following example to access the Citrix Monitor Service data using PowerShell.  This is a sample OData query triggered from PowerShell with the headers initialized and the Raw XML output redirected to a file:

```powershell
PS C:\> $headers = @{"Authorization" = "<BearerToken>”; "Citrix-CustomerId" = "<Your Customer Id>"}

PS C:\> $url = https://{ApiGatewayEndpoint}/Users

PS C:\> $result = Invoke-WebRequest -Uri $url -Headers $headers

PS C:\> $result.Content > <Path-to-Output-File>
```
## Access Citrix Monitor Service data using any REST API tool

Learn from the following example to access the Citrix Monitor Service data using any REST API tool, for example Postman Chrome Ext.

1.	Install Postman from Chrome extensions. See [https://chrome.google.com/webstore](https://chrome.google.com/webstore).
1.	Launch the application.
1.	Select GET in the dropdown and type in the URL to request. ![Postman](./postman.png)
1.  Insert headers with your token and customerId. Click **Send**.
1.  If authentication is successful, response data is seen in the Response section of Postman.

# Use OData V4 aggregation queries in Citrix Monitor Service data

Aggregation queries were not supported in OData Version 3 and earlier. It is supported in OData Version 4. As per [OData documentation](http://docs.oasis-open.org/odata/odata-data-aggregation-ext/v4.0/cs01/odata-data-aggregation-ext-v4.0-cs01.html), aggregation can be done using the `$apply=aggregate()` field. Below are few examples that show data aggregation. Use them after **https://{ApiGatewayEndpoint}/<TableName>**

`?$apply=aggregate`([column to aggregate] with [aggregation type] as [column to aggregate])

**Aggregation Types:**

* Count : `Machines?$count=true`

This gives the total number of machines.

* Sum : `Sessions?$apply=aggregate(SessionType with sum as SessionType)`

This gives the sum of session types in Sessions.

* Min : `Machines?$apply=aggregate(LifecycleState with min as LifecycleState)`

This gives the minimum of all the life cycle states.

# Use OData pagination in Citrix Monitor Service data 

Citrix Monitor supports OData pagination. All OData v4 endpoints return a maximum of 100 records per page with a link to the next 100 records. The following PowerShell script fetches applications in batches of 100 records using Citrix Cloud authentication following the **@odata.nextLink** property in the response.

```powershell
$customerId = "[customerid]"
$api = "https://{ApiGatewayEndpoint}"
$endpoint = "$api/Applications"

$bearer = "CWSAuth bearer=[token]"
$headers = @{'Citrix-CustomerId'=$customerId;'Authorization'=$bearer}

$results = Invoke-RestMethod $endpoint -Headers $headers -Verbose
Write-Host “Number of items returned in the first call : ”, $results.value.Count

while($results.'@odata.nextLink' -ne $null)
{
    $results = Invoke-RestMethod $results.'@odata.nextLink' -Headers $headers -Verbose
    Write-Host "Number of items returned in next call : ", $results.value.Count
}

```
