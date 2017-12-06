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

You may obtain the bearer token using the Citrix Cloud APIs. For more information on the steps, see [https://developer.cloud.com/authenticate_api_client.html](https://developer.cloud.com/authenticate_api_client.html)

For the details of the Monitor Service schema, see [Monitor Service Schema](https://developer-docs.citrix.com/projects/monitor-service-odata-api/en/7.16/#monitor-service-schema).

To determine the values returned by the Monitor Service OData API, see [Citrix.Monitor.Model](https://developer-docs.citrix.com/projects/monitor-service-odata-api/en/7.16/api-reference/Monitor.Model/).

