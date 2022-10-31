# General Knowledge

- Each virtual machine in your availability set is assigned an update domain and a fault domain by the underlying Azure platform. For a given availability set, five non-user-configurable update domains are assigned by default (Resource Manager deployments can then be increased to provide up to 20 update domains)
- A managed identity from Azure Active Directory allows your app to easily access other AAD-protected resources such as Azure Key Vault.
- When soft-delete is enabled, resources marked as deleted resources are retained for a specified period (90 days by default). The service further provides a mechanism for recovering the deleted object, essentially undoing the deletion.
- the Standard App Service Tier. The Standard tier supports auto-scaling, and we should minimize the cost. We can then enable autoscaling on the web app, add a scale rule and add a Scale condition.
- Metric alerts in Azure Monitor provide a way to get notified when one of your metrics cross a threshold. Metric alerts work on a range of multi-dimensional platform metrics, custom metrics, Application Insights standard and custom metrics.

- The API back end is hosted in an Azure App Service instance. You have implemented a RESTful service for the API back end.
You must configure back-end authentication for the API Management service instance.
  - You configure Basic gateway credentials for the HTTP(s) endpoint. **NO**
  - You configure Client cert gateway credentials for the Azure resource. **YES**
  - You configure Client cert gateway credentials for the HTTP(s) endpoint. **YES**
- API Management allows to secure access to the back-end service of an API using client certificates. Furthermore, the API back end is hosted in an Azure App
Service instance.

- You create the index in Azure Search. You now need to use the Azure Search .NET SDK to import the relevant data into the Azure Search service.
Which three actions should you perform in sequence?
    1. Create a SearchIndexClient object to connect to the search index
    2. Create an IndexBatch that contains the documents which must be added
    3. Call the Documents.Index method of the SearchIndexClient and pass the IndexBatch

- The following example shows how a pre-trigger is used to validate the properties of an Azure Cosmos DB item that is being created. In this example, we are leveraging the ToDoList sample from the Quickstart .NET API for NoSQL, to add a timestamp property to a newly added item if it doesn't contain one.

    ```JavaScript

    function validateToDoItemTimestamp() {
        var context = getContext();
        var request = context.getRequest();

        // item to be created in the current operation
        var itemToCreate = request.getBody();

        // validate properties
        if (!("timestamp" in itemToCreate)) {
            var ts = new Date();
            itemToCreate["timestamp"] = ts.getTime();
        }

        // update the item that will be created
        request.setBody(itemToCreate);
    }
    ```

- The purpose of the change feed is to provide transaction logs of all the changes that occur to the blobs and the blob metadata in your storage account. The change feed provides ordered, guaranteed, durable, immutable, read-only log of these changes. Client applications can read these logs at any time, either in streaming or in batch mode.
  There are four main components of implementing the change feed processor:
  1. The monitored container: The monitored container has the data from which the change feed is generated. Any inserts and updates to the monitored container are reflected in the change feed of the container.
  2. The lease container: The lease container acts as a state storage and coordinates processing the change feed across multiple workers. The lease container can be stored in the same account as the monitored container or in a separate account.
  3. The compute instance: A compute instance hosts the change feed processor to listen for changes. Depending on the platform, it could be represented by a VM, a kubernetes pod, an Azure App Service instance, an actual physical machine. It has a unique identifier referenced as the instance name throughout this article.
  4. The delegate: The delegate is the code that defines what you, the developer, want to do with each batch of changes that the change feed processor reads.

- Docker structure:
  - FROM
  - WORKDIR
  - COPY
  - RUN
  - CMD

- consistency level
  - Strong -
  Strong: Strong consistency offers a linearizability guarantee. The reads are guaranteed to return the most recent committed version of an item. A client never sees an uncommitted or partial write. Users are always guaranteed to read the latest committed write.
  - Bounded staleness -
  Bounded staleness: The reads are guaranteed to honor the consistent-prefix guarantee. The reads might lag behind writes by at most "K" versions (that is
  "updates") of an item or by "t" time interval. When you choose bounded staleness, the "staleness" can be configured in two ways:
  1. The number of versions (K) of the item
  2. The time interval (t) by which the reads might lag behind the writes
  - Eventual -
  Eventual: There's no ordering guarantee for reads. In the absence of any further writes, the replicas eventually converge.

ARM template:

- The Azure Resource Manager template (ARM template) test toolkit checks whether your template uses recommended practices. When your template isn't compliant with recommended practices, it returns a list of warnings with the suggested changes. By using the test toolkit, you can learn how to avoid common problems in template development.
- ARM template deployment what-if operation: Before deploying an Azure Resource Manager template (ARM template), you can preview the changes that will happen

Azure Blob Storage:

- Azure Blob index tags
  As datasets get larger, finding a specific object in a sea of data can be difficult. Blob index tags provide data management and discovery capabilities by using key- value index tag attributes. You can categorize and find objects within a single container or across all containers in your storage account.

Azure Cosmos DB:

- Azure Cosmos DB now provides a new RBAC role, Cosmos DB Operator. This new role lets you provision Azure Cosmos accounts, databases, and containers, but can't access the keys that are required to access the data.

Azure Key Vault:

- Soft Delete:
  When soft-delete is enabled, resources marked as deleted resources are retained for a specified period (90 days by default). The service further provides a mechanism for recovering the deleted object, essentially undoing the deletion.

- Purge protection -
  Purge protection is an optional Key Vault behavior and is not enabled by default. Purge protection can only be enabled once soft-delete is enabled.
  When purge protection is on, a vault or an object in the deleted state cannot be purged until the retention period has passed. Soft-deleted vaults and objects can still be recovered, ensuring that the retention policy will be followed.

- Security:
  Azure Active Directory Managed Service Identity (MSI) gives your code an automatically managed identity for authenticating to Azure services, so that you can keep credentials out of your code. (choose this when the credentials must not be sent through the API)

- Grant a virtual machine (VM) access to specific resource groups in Azure Resource Manager.
  You need to obtain an Azure Resource Manager access token.
  run the Invoke-RestMethod cmdlet to make a request to the local managed identity for Azure resources endpoint.

- website that will run as an Azure Web App. Users will authenticate by using their Azure Active Directory (Azure AD) credentials. You need to configure authorization.
  Azure AD application's manifest, set value of the groupMembershipClaims option to All. In the website, use the value of the groups claim from the JWT for the user to determine permissions.

- APIM gateway policies:
  - Inbound:
    - set-variable
    - cache-lookup
  - Outbound:
    - cache-store
    - find-and-replace

- Azure Identity Library, which allows to use the same code across different environments with different options to provide identity.

  ```C#
  string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME"); 
  var kvUri = "https://" + keyVaultName + ".vault.azure.net"; 
  var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
  ```

- Revoke a user delegation SAS
  If you believe that a SAS has been compromised, you should revoke it. You can revoke a user delegation SAS either by revoking the user delegation key, or by changing or removing RBAC role assignments for the security principal that's used to create the SAS.

- Azure key Vault:
  To perform a key transfer, a user performs following steps:
  - Generate KEK.
  - Retrieve the public key of the KEK.
  - Using HSM vendor provided BYOK tool - Import the KEK into the target HSM and exports the Target Key protected by the KEK.
  - Import the protected Target Key to Azure Key Vault.
