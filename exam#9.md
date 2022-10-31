# Exam Practice #9 - Table of contents

Objectives Percentage (Corrects/Number Questions)
Develop Azure compute solutions 76.92 % (10/13)
Develop for Azure storage 66.67 % (4/6)
Implement Azure security 100 % (10/10)
Monitor, troubleshoot, and optimize Azure solutions 66.67 % (4/6)
Connect to and consume Azure services and third-party services 91.67 % (11/12)
Customer Score: 82.98% - PASS - 39/47 (0 Unanswered)

- [Exam Practice #9 - Table of contents](#exam-practice-9---table-of-contents)
  - [Develop Azure compute solutions](#develop-azure-compute-solutions)
    - [Implent IaaS solutions](#implent-iaas-solutions)
  - [Create Azure App Service Web Apps](#create-azure-app-service-web-apps)
  - [Develop for Azure storage](#develop-for-azure-storage)
    - [Develop solutions tht use Cosmos DB storage](#develop-solutions-tht-use-cosmos-db-storage)
    - [Develop solutions tht use blob storage](#develop-solutions-tht-use-blob-storage)
  - [Monitor, troubleshoot, and optimize Azure solutions](#monitor-troubleshoot-and-optimize-azure-solutions)
    - [Implement caching for solutions](#implement-caching-for-solutions)
    - [Troubleshoot solutions by using metrics and log data](#troubleshoot-solutions-by-using-metrics-and-log-data)
  - [Connect to and consume Azure services and third-party services](#connect-to-and-consume-azure-services-and-third-party-services)
    - [Implement API Management](#implement-api-management)

## Develop Azure compute solutions

### Implent IaaS solutions

- to build and publish the app1 container image to an Azure Container Registry (ACR) named registry1.

    1. Build the Dockerfile with docker build.
    2. Tag the image as registry1.azurecr.io/app1.
    3. Login in registry1 with az acr login and push the image to registry1

- to create a generalized image named myNewVM for provisioning additional instances

  1. Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
  2. Set-AzVM -ResourceGroupName $rgName -Name $vmName -Generalized
  3. $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
  4. $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id
  5. New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName

## Create Azure App Service Web Apps

- to monitor the CPU usage of Webapp1 and create an alert if the usage percentage is above 70%.
    1. You should use the CPU Percentage metric. This metric contains CPU usage over time for any given App Service Plan. You need to use this metric to configure the alert properly.
    2. You should use the plan1 resource. To monitor the CPU usage of an App Service, you need to choose the App Service Plan resource, which is the plan1 resource in this case. An App Service Plan resource contains the metrics related to virtual machine (VM) usage, like CPU Percentage, Memory Percentage, and Disk Queue Length.

## Develop for Azure storage

### Develop solutions tht use Cosmos DB storage

- You cannot write triggers, stored procedures, and user-defined functions (UDFs) in a Cosmos DB database running Table API. You can write code
with custom business logic in Javascript that is executed inside the database engine using the SQL API only. Other Cosmos DB APIs do not support
triggers, stored procedures, and UDFs.
- You cannot write stored procedures in Cosmos DB using C# code. Cosmos DB only supports stored procedures, triggers, and UDFs written in
Javascript. You can register and use Cosmos DB server-side programming custom logic with supported SDKs, including C#, Java, Javascript, and
Python, but the code that is executed by the database engine must be written in Javascript

### Develop solutions tht use blob storage

- If you omit the default request location mode, PrimaryOnly mode will be used. PrimaryOnly is the default blob client mode

## Monitor, troubleshoot, and optimize Azure solutions

### Implement caching for solutions

- You should use the volatile-lru eviction policy. When the memory limit is reached, this policy dictates that the Azure cache for Redis instance should make space for the new data by removing the LRU keys, but only from the set of keys that have their expiry values defined.
- You should not use the allkeys-lru eviction policy. With this policy, Azure Cache for Redis tries to remove the LRU keys first. However, it does not consider whether the keys have an expire set or not.

### Troubleshoot solutions by using metrics and log data

- Azure function app. You enable Application Insights for the app. You need the app to send informational log data to Application Insights.

  - You should store the name of the instrumentation key in an app setting named APPINSIGHTS_INSTRUMENTATIONKEY. This is required so that Azure knows which Application Insights resource to use for logging telemetry data.
  - You should also call the LogInformation method of the ILogger instance. An instance of this interface can be passed as a parameter to each function in a function app. The LogInformation method allows you to log informational messages

## Connect to and consume Azure services and third-party services

### Implement API Management

- You should check the Set usage quota by key policy. This policy can set an access quota based on renewable or lifetime call volume on bandwidth. When the quota is exceeded, a 403 Forbidden status response code is returned.
- You should not check the Limit call rate by key policy. This policy is used to prevent traffic spikes by limiting the call rate. If access is blocked by this policy, a 429 Too Many Requests status response code is returned
