# Exam Practice #4 - Table of contents

Passing Score: 80% (80/100) Time Taken: 00:44:38 Date: 10/21/2022
Objectives Percentage (Corrects/Number Questions)
Develop Azure compute solutions 53.33 % (8/15)
Develop for Azure storage 87.5 % (7/8)
Implement Azure security 87.5 % (7/8)
Monitor, troubleshoot, and optimize Azure solutions 50 % (3/6)
Connect to and consume Azure services and third-party services 90 % (9/10)
Customer Score: 72.34% - FAIL - 34/47 (1 Unanswered)

- [Exam Practice #4 - Table of contents](#exam-practice-4---table-of-contents)
  - [Develop Azure compute solutions](#develop-azure-compute-solutions)
    - [Implent IaaS solutions](#implent-iaas-solutions)
    - [Create Azure App Service Web Apps](#create-azure-app-service-web-apps)
  - [Develop for Azure storage](#develop-for-azure-storage)
    - [Develop solutions that use Cosmos Db Storage](#develop-solutions-that-use-cosmos-db-storage)
  - [Implement Azure security](#implement-azure-security)
    - [Implement user authentication and authorization](#implement-user-authentication-and-authorization)
  - [Monitor, troubleshoot, and optimize Azure solutions](#monitor-troubleshoot-and-optimize-azure-solutions)
    - [Implement caching for solutions](#implement-caching-for-solutions)
    - [Troubleshoot solutions by using metrics and log data](#troubleshoot-solutions-by-using-metrics-and-log-data)
  - [Connect to and consume Azure services and third-party services](#connect-to-and-consume-azure-services-and-third-party-services)
  - [Develop event-based solutions](#develop-event-based-solutions)

## Develop Azure compute solutions

### Implent IaaS solutions

- App Service Standard Plan is th emost cost-efficient plan that supports automatic scaling

- To implement a function in Azure functions that is reposnsible for processing orders from  ServiceBusTrigger

```C#
  [FunctionName("OrderProcessor")]
  public static void Run(
  [ServiceBusTrigger("OrdersTopic")] string myQueueItem,
  [Blob("orders/{id}", FileAccess.Write)] Stream order,
  ILogger log)
  {
  ...
  }

  ```

- to build an asp.net core api application running in a container

```Dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app
COPY *.sln .
COPY checkout/. ./checkout/
WORKDIR /app/checkout
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/checkout/out ./
ENTRPOINT ["dotnet","checkout.dll"]
```

- When using a blob storage, the storace account **must** be in the same region as the App Service

### Create Azure App Service Web Apps

- ASP.NET applications **do not** support storage to a NoSQL data store
- ASP.NET Memory Session State Provider does minimize latency but **do not** makes the session state available to all the applications
- Premium Tier Azure Cache for Redis is a distributed memory session state provider, it minimize latency and it provides persisting session state data as snapshot

## Develop for Azure storage

### Develop solutions that use Cosmos Db Storage

- Autoscale throughput on a db can be used in scenarios where you do not know yet the traffic pattern for the application, but you want to prepare the application to scale in unpredicatbale traffic spikes.

## Implement Azure security

### Implement user authentication and authorization

- New-AzRole Assignment lets you grant access based on RBAC to a secuirty principal including a user or a group.

## Monitor, troubleshoot, and optimize Azure solutions

### Implement caching for solutions

- Shared vs Private caching:
  - Shared caching **is not faster** than private caching.
  - Private caching **does not guarantee** that all application instances see the same view of cached data.
  - Shared caching provides better scalability than private caching.

### Troubleshoot solutions by using metrics and log data

- To provide behavior analytics you should capture these two telemetry:
  - User Id
  - Session Id

- To set up an availability alert:
  1. Open your existing Application Insights resource in the Azure portal
  2. Open the Availabilty pane
  3. Use the Add Test option to create a new test
  4. Selec the Open Rules (Alert) page from the entry's details
  5. Set the action group for the generated availabilty alert

## Connect to and consume Azure services and third-party services

## Develop event-based solutions

- The IEventProcessor interface defines a method named ProcessEventAsync that passes a collection of EventData instances represengint the messages in the Event Hub
