# Access Monitor Service data using the OData v4 endpoint in Citrix Cloud

You can now query the Monitor Service data using the OData Version 4 endpoint based on ASP .Net Web API. Customers can now run aggregation queries on the Monitor Service data; this feature was not available in OData Version 3 or earlier. 

A Citrix Cloud customer can access the data with the V4 endpoint after authentication using the Citrix Cloud username and authentication token or the bearer token.


!!!tip "Note" 
        To ensure optimal performance and resource utilization of the Delivery Controller, one OData query is permitted per customer at a time. Query time out is 30 seconds.

## Generate Citrix Cloud Bearer Token

You can obtain the bearer token using either of the following methods: 

### Method 1: XenApp and XenDesktop Remote PowerShell SDK

1.	Download the Remote PowerShell SDK from [here](http://download.apps.cloud.com/CitrixPoshSdk.exe).
2.	Install the SDK on any computer in your resource location.
3.	Open a PowerShell command prompt. (administrator rights are not required)
4.	Add the Citrix snap-ins: `asnp citrix*`.
5.	Execute the cmdlet, `Get-XdAuthentication`, it prompts you for Citrix Cloud authentication.
6.	On successful authentication, the bearer token is stored in the current PowerShell session.
7.	Execute the cmdlet, `Get-Variable` to list all the variables in the current PowerShell session.
8.	The list contains a variable, `GLOBAL:XDAuthToken` containing bearer token as value.
9.	Copy the bearer token value to be used in the OData query.

### Method 2: Citrix Cloud API:

You may obtain the bearer token using the Citrix Cloud APIs. For more information on the steps, see [https://developer.cloud.com/getting-started](https://developer.cloud.com/getting-started)

For the details of the Monitor Service schema, see [Monitor Service Schema](https://developer-docs.citrix.com/projects/monitor-service-odata-api/en/7.16/#monitor-service-schema).

To determine the values returned by the Monitor Service OData API, see [Citrix.Monitor.Model](https://developer-docs.citrix.com/projects/monitor-service-odata-api/en/7.16/api-reference/Monitor.Model/).

The list of URLs for available data sets is available at [URLs for Available Data Sets](https://developer-docs.citrix.com/projects/monitor-service-odata-api/en/7.16/#urls-for-available-data-sets). Replace {dc-host} with "{YourCustomerId}.xendesktop.net".

# Access methods
## Access using MS Excel PowerQuery 

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
"https://<YourCustomerId>.xendesktop.net/Citrix/monitor/odata/v4/data/Machines", 
                          null, 
                          [
                          	Headers = 
                                    [
                                    	Authorization = "<YourToken>",
                                     	Customer = "<YourCustomerId>"
                                    ]
                          ]
               )
in
	Source
```

![Advanced editor](./advanced-query.png)

Replace `<YourCustomerId>` and `<YourToken>` with the values of CustomerID and bearer token you obtained as described in the Generate the Citrix Cloud Bearer Token section. Make sure that the <YourToken> is in the format, “CWSAuth bearer= <bearer token>”. Once the **No Syntax errors have been detected** is displayed in the bottom of the window, click **Done**.
&#53; In case of first login, a Please specify how to connect message is displayed. Click Edit Credentials, the Access an OData feed window appears. Make sure that the Anonymous tab and the base URL are selected and click Connect.

&#54; Upon successful authentication, the results are displayed as a table. In this example, the table lists all the Machines of the CustomerId. If the authentication fails, either the bearer token or the CustomerId might be invalid. Please verify the same and try again.

## Access using C# Client Library

Use a Simple.OData.Client to create a C# client for an OData v4 endpoint. For information about installation of its Nuget Package, see [https://github.com/object/Simple.OData.Client/wiki/Getting-started-with-Simple.OData.Client](https://github.com/object/Simple.OData.Client/wiki/Getting-started-with-Simple.OData.Client).

Install the package using the Nuget Package Manager:

```
Install-Package Simple.OData.Client
```
After installation, use the package with the following command:

```
using Simple.OData.Client;
```

As the OData query in the Cloud requires the Citrix Cloud authentication, the OData clients must pass the bearer token (as documented in the **Generate Citrix Cloud Bearer Token** section) and the CustomerId. Set up the headers as follows, before creating the OData query:

```js
ODataClientSettings settings = new ODataClientSettings();
settings.BeforeRequest += request => 
{
    request.Headers.Add("Authorization", "<BearerToken>");
    request.Headers.Add("Customer", "<customerId>");
};

settings.BaseUri = new Uri("https://<customerId>.xendesktop.net/Citrix/monitor/odata/v4/data");
client = new ODataClient(settings);
```

Data can be retrieved by using async methods. This example lists all the Machines

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
## Access using PowerShell

Below is a sample OData query triggered from PowerShell with the headers initialized and the Raw XML output redirected to a file:

```powershell
PS C:\> $headers = @{"Authorization" = "<BearerToken>”; "Customer" = "<Your Customer Id>"}

PS C:\> $url = https://<Your Customer Id>.xendesktop.net/Citrix/Monitor/OData/v4/Data/Users

PS C:\> $result = Invoke-WebRequest -Uri $url -Headers $headers

PS C:\> $result.Content > <Path-to-Output-File>
```
## Access using other REST clients (Ex. Postman Chrome Ext.)

1.	Install Postman from Chrome extensions. See [https://chrome.google.com/webstore](https://chrome.google.com/webstore).
2.	Launch the application.
3.	Select GET in the dropdown and type in the URL to request. ![Postman](./postman.png)
4.  Insert headers with your token and customerId. Click **Send**.
1.  If authentication is successful, response data is seen in the Response section of Postman.

# Aggregation Queries in Odata v4

Aggregation queries were not supported in OData Version 3 and earlier. It is supported in OData Version 4. As per [OData documentation](http://docs.oasis-open.org/odata/odata-data-aggregation-ext/v4.0/cs01/odata-data-aggregation-ext-v4.0-cs01.html), aggregation can be done using the `$apply=aggregate()` field. Below are few examples that show data aggregation. Use them after **https://{customer-id}.xendesktop.net/Citrix/Monitor/Odata/v4/Data/<TableName>**

`?$apply=aggregate`([column to aggregate] with [aggregation type] as [new column name])

**Aggregation Types:**

* Count : `Machines?$count=true`

This gives the total number of machines.

* Sum : `Sessions?$apply=aggregate(SessionType with sum as Total)`

This gives the sum of session types in Sessions.

* Min : `Machines?$apply=aggregate(LifecycleState with min as MinimumLCstate)`

This gives the minimum of all the life cycle states.
