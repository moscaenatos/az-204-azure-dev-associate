# Exam Practice #2 - Table of contents

Passing Score: 80% (80/100) Time Taken: 00:55:28 Date: 10/20/2022
Objectives Percentage (Corrects/Number Questions)
Develop Azure compute solutions 41.67 % (5/12)
Develop for Azure storage 33.33 % (3/9)
Implement Azure security 11.11 % (1/9)
Monitor, troubleshoot, and optimize Azure solutions 50 % (3/6)
Connect to and consume Azure services and third-party services 36.36 % (4/11)
Customer Score: 34.04% - FAIL - 16/47 (0 Unanswered)

- [Exam Practice #2 - Table of contents](#exam-practice-2---table-of-contents)
  - [Develop Azure compute solutions](#develop-azure-compute-solutions)
    - [Implent IaaS solutions](#implent-iaas-solutions)
    - [Create Azure app Service Web Apps](#create-azure-app-service-web-apps)
    - [Implement Azure functions](#implement-azure-functions)
  - [Develop for Azure Storage](#develop-for-azure-storage)
    - [Develop solutions that uses Cosmos DB storage](#develop-solutions-that-uses-cosmos-db-storage)
    - [Develop solutions that use blob storage](#develop-solutions-that-use-blob-storage)
  - [Implement Azure security](#implement-azure-security)
    - [Implement user authentication and authorization](#implement-user-authentication-and-authorization)
    - [Implement secure cloud solutions](#implement-secure-cloud-solutions)
  - [Monitor, troubleshoot, and optimize Azure solutions](#monitor-troubleshoot-and-optimize-azure-solutions)
    - [Troubleshoot solutions by using metrics and log data](#troubleshoot-solutions-by-using-metrics-and-log-data)
  - [Connect to and consume Azure services and third-party services](#connect-to-and-consume-azure-services-and-third-party-services)
    - [Implement API Management](#implement-api-management)
    - [Develop event-based solutions](#develop-event-based-solutions)
    - [Develop message based solutions](#develop-message-based-solutions)

## Develop Azure compute solutions

### Implent IaaS solutions

- To encrypt VHDS and create a virtual machine on Azure when the VHDS are on premise
    1. Encrypt the on-premses VHD usin bitlocker without TPM. Upload the VHD to an azure storage account
    2. Run the Set-AzVMOSDisk Azure Powershell command
    3. tun the New-AzVm Powershell command
- Use ACI Container Groups to monitor an application in the Azure Container Instance using standard set of agents installed on a separate container instance
- To create an ARM template based on the current state of a resource group named MyRG and its resource you should use
  - ```az group export --name myRG```

### Create Azure app Service Web Apps

- To request a client certificate from a trusted certificate authority in a ASP.NET Azure web Apps use HttpRequest.ClientCertificate property

### Implement Azure functions

- to wait for async operation to be done use ```await Task.WhenAll(parallelTasks)```
- Application patterns
  - When you have mulitple functions that execute in parallel and then wait for all functions to finish with aggregation work done on the results that are returned from the functions, you should use the **fan out/fan in** application pattern
  - When you ahve event data provided in batches by multiple soruces over a period of time that must be combined into a single, addressable entity, you should use the **aggregation** application pattern

## Develop for Azure Storage

### Develop solutions that uses Cosmos DB storage

- SELECT TOP 1 i.name, i.description FROM i
- Change feed in Cosmos DB:
  - it is enabled by default
  - does not caputer deletes
  - does not guarantees order across the partition key values

### Develop solutions that use blob storage

- comp query string of the PUT request URL is set to **lease** and represents a blob lease request. This prevents a blob from being deleter or overwritten for the amount of time specified in the x-ms-lease-duration (in seconds)

- comp qeuery stirng of the PUT request URL is set to **tier** and represend a blob tier change which prevents the blob from being read, modified, overwritter or having a snapshot taken.
  - after the request the blob is taken offline
  - the copy cannot be copied

## Implement Azure security

### Implement user authentication and authorization

- New-AzRoleAssignment cmdlet allows you to grant access based on RBAC to a security principal, user or group
- To add OAuth 2.0 to protect  web API in a Azure AD
  - create app registration
  - create an API Management instance

- BlobSasBuilder, resource ="c", BlobContainerSasPermissions
- To implement MFA for a group of employees that have Azure AD Basic license for a website hosted in Azure AD
  - Upgrade their license to Azure AD Premium P1
  - Create new conditional access policy in Azure AD

- in Microsoft Graph permission do not specifying constrains and setting User.Read, it means that the app can perform read operations only on the resources owned by the signed-in user

- To retrieve photo of each sifned in user with microsoft graph api use
  - ``` GET https://graph.microsoft.com/v1.0/me/photo/$value ```

### Implement secure cloud solutions

- you can only assign user-assigned managed identity to resources hosted in Azur

## Monitor, troubleshoot, and optimize Azure solutions

### Troubleshoot solutions by using metrics and log data

- to capture performance metrics from multiple geographies create a **URL ping test** in Application Insights
- Track custom events and page views
  - how does load time of web page affects product sales -> Impact
  - which events most influence users to return to the website -> Retention

- To set up an availability alert:
  1. Open your existing Application Insights resource in the Azure portal
  2. Open the Availabilty pane
  3. Use the Add Test option to create a new test
  4. Selec the Open Rules (Alert) page from the entry's details
  5. Set the action group for the generated availabilty alert

## Connect to and consume Azure services and third-party services

### Implement API Management

- To solve 'Access denied due to missing subscription key'
  - Add the API to a product in the Azure Portal
  - Include the Ocp-Apim-Subscription-Key header in the Http request

- To expose a set of operation to only a group of developers in an API in Azure Api Management gateway
  - Create a version and use the Header versioning scheme
- Use Validate JWT to ensure access control for
  - Use Azure API Management for all external access to these services
  - Configure claims-based backend authorization
  - User must be authorized to use the service

- Before importing a REST API running on-remises to the API Management instance:
  - create OpenAPI documentation for the API

### Develop event-based solutions

- az provider register --namespace Microsoft.EventGrid to enable the Azure subscription to send events to Event Grid

### Develop message based solutions

- To create a service bus queue
    You should complete the script as follows:

    1. ```rgName = "myResourceGroup" az group create --name $rgName --location eastus```
    2. ```nsName = myNS$RANDOM az servicebus namespace create --resource-group $rgName --name $nsName --location eastus```
    3. ```queueName = "myQueue" az servicebus queue create --resource-group $rgName --namespace-name $nsName --name $queueName```

- Correlation filters are used to filter messages based on a set of conditions that are matched agains one or more properties
