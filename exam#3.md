# Exam Practice #3 - Table of contents

Passing Score: 80% (80/100) Time Taken: 00:44:55 Date: 10/20/2022
Objectives Percentage (Corrects/Number Questions)
Develop Azure compute solutions 72.73 % (8/11)
Develop for Azure storage 66.67 % (6/9)
Implement Azure security 55.56 % (5/9)
Monitor, troubleshoot, and optimize Azure solutions 66.67 % (4/6)
Connect to and consume Azure services and third-party services 75 % (9/12)
Customer Score: 68.09% - FAIL - 32/47 (0 Unanswered)

- [Exam Practice #3 - Table of contents](#exam-practice-3---table-of-contents)
  - [Develop Azure compute solutions](#develop-azure-compute-solutions)
    - [Implent IaaS solutions](#implent-iaas-solutions)
    - [Create Azure App Service Web Apps](#create-azure-app-service-web-apps)
  - [Develop for Azure storage](#develop-for-azure-storage)
    - [Develop solutions that use Cosmos Db Storage](#develop-solutions-that-use-cosmos-db-storage)
  - [Implement Azure security](#implement-azure-security)
    - [Implement user authentication and authorization](#implement-user-authentication-and-authorization)
    - [Implement secure cloud solutions](#implement-secure-cloud-solutions)
  - [Monitor, troubleshoot, and optimize Azure solutions](#monitor-troubleshoot-and-optimize-azure-solutions)
    - [Implement caching for solutions](#implement-caching-for-solutions)
  - [Connect to and consume Azure services and third-party services](#connect-to-and-consume-azure-services-and-third-party-services)
  - [Develop message-based solutions](#develop-message-based-solutions)

## Develop Azure compute solutions

### Implent IaaS solutions

### Create Azure App Service Web Apps

- Active Message Processed/Instance (Avg): to monitor the count of messages in a queue/topic.

- When using a blob storage, the storace account **must** be in the same region as the App Service

## Develop for Azure storage

### Develop solutions that use Cosmos Db Storage

- Appropriate consistency level for the database solution. The solution should meet these requirements:
  1. Guarantee that data writes are returned in order with no gaps.
  2. Process returned data by either the most current version or the prior version of the data.
  3. Minimize latency and adverse impact on data availability.
  4. Keep processing overhead to a minimum.
Should implement the **bounded staleness consistency level**. This consistency level includes the implementation of consistent prefix, which guarantees that data writes are returned in order. You can configure staleness, which determines the data version returned, based on either the number of versions or on time delay.
- after a blob has been moved to archive, you can still copy it but you have to wait for the rehydration period

## Implement Azure security

### Implement user authentication and authorization

- To access the Resource Manager API using the MSI from a VM, you should grant the **Reader** rolde to the identity at the subsciption scope

- IConfidentialClientApplication is used to perform request to Microsoft Graph API
- Authorization attributes
  - [Authorize(Roles="SubscriptionOwner")] [Authorize(Roles="SubscriptionUser")]
    Applying multiple Authorize attributes to the controller class ensures that only users that have both roles can execute actions present within the class.

  - [Authorize(Roles="SubscriptionOwner", "SubscriptionUser")]
    This would allow any user with either SubscriptionOwner or SubscriptionUser to execute actions in the class. This will not force a user to have both
    roles.
  - [Authorize(Policy="Subscribers", Roles="SubscriptionOwner")]
    This attribute ensures that user is validated against the policy defining the acceptable UserTier property to fall in either tier2 or tier3. You also ensure
    that the user should have a roles of SubscriptionOwner by specifying that in the Authorize attributes roles parameter.

### Implement secure cloud solutions

- Set-AzKeyVaultAccessPolicy grants or modifies permissions for a user, application or secuirty group to perform operations with a key vault

## Monitor, troubleshoot, and optimize Azure solutions

### Implement caching for solutions

- The following code:

    ```C#
    String connectionString = GetConnectionString();
    var obj = ConnectionMultiplexer.Connect(connectionString);
    var database = obj.GetDatabase();
    var tables = database.StringGet("Tables");
    ```

retrieves Data from an Azure Cache for Redis

- Shared vs Private caching:
  - Shared caching is not faster than private caching.
  - Private caching does not guarantee that all application instances see the same view of cached data.
  - Shared caching provides better scalability than private caching.

## Connect to and consume Azure services and third-party services

- A revision allows you to add non-breking changes to a web API. Users can access the revision by using a different query string at the same endpoint
- A version allows you to expose brekaing changes for a web API. A version **requires** publishing

## Develop message-based solutions

- Using Blob trigger directly from a storage might skip some blobs or not trigger the functionality immediately

- to send a message with service bus

```C#
static async Task SendMessage(string connectionString, string message)
{
await using (var client = new ServiceBusClient(connectionString))
{
ServiceBusSender sender = client.CreateSender("ordersTopic");
await sender.SendMessageAsync(new ServiceBusMessage(message));
}
}
```
