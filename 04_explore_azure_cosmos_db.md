# Table of contents

- [Table of contents](#table-of-contents)
  - [Explore Azure Cosmos DB](#explore-azure-cosmos-db)
    - [Elements in an Azure Cosmos account](#elements-in-an-azure-cosmos-account)
      - [Azure Cosmos databases](#azure-cosmos-databases)
      - [Azure Cosmos containers](#azure-cosmos-containers)
      - [Azure Cosmos items](#azure-cosmos-items)
    - [Explore consistency levels](#explore-consistency-levels)
    - [Discover request units](#discover-request-units)
  - [Implement partitioning in Azure Cosmos DB](#implement-partitioning-in-azure-cosmos-db)
    - [Logical partitions](#logical-partitions)
    - [Physical partitions](#physical-partitions)
    - [Choose a partition key](#choose-a-partition-key)
      - [Partition keys for read-heavy containers](#partition-keys-for-read-heavy-containers)
      - [Using item ID as the partition key](#using-item-id-as-the-partition-key)
    - [Create a synthetic partition key](#create-a-synthetic-partition-key)
    - [Create stored procedures](#create-stored-procedures)
      - [Writing stored procedures](#writing-stored-procedures)
    - [Create triggers and user-defined functions](#create-triggers-and-user-defined-functions)

## Explore Azure Cosmos DB

Azure Cosmos DB is a globally distributed database system that allows you to read and write data from the local replicas of your database and it transparently replicates the data to all the regions associated with your Cosmos account.
Your application can perform near real-time reads and writes against all the regions you chose for your database. Azure Cosmos DB internally handles the data replication between regions with consistency level guarantees of the level you've selected.

### Elements in an Azure Cosmos account

An Azure Cosmos container is the fundamental unit of scalability. You can virtually have an unlimited provisioned throughput (RU/s) and storage on a container. Azure Cosmos DB transparently partitions your container using the logical partition key that you specify in order to elastically scale your provisioned throughput and storage.

#### Azure Cosmos databases

You can create one or multiple Azure Cosmos databases under your account. A database is analogous to a namespace. A database is the unit of management for a set of Azure Cosmos containers. The following table shows how an Azure Cosmos database is mapped to various API-specific entities:

| Azure Cosmos entity   | SQL API  | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API | Table API |
|-----------------------|----------|---------------|---------------------------------|-------------|-----------|
| Azure Cosmos database | Database | Keyspace      | Database                        | Database    | NA        |

#### Azure Cosmos containers

An Azure Cosmos container is the unit of scalability both for provisioned throughput and storage. A container is horizontally partitioned and then replicated across multiple regions. The items that you add to the container are automatically grouped into logical partitions, which are distributed across physical partitions, based on the partition key.

#### Azure Cosmos items

Depending on which API you use, an Azure Cosmos item can represent either a document in a collection, a row in a table, or a node or edge in a graph. The following table shows the mapping of API-specific entities to an Azure Cosmos item:

| Cosmos entity     | SQL API | Cassandra API | Azure Cosmos DB API for MongoDB | Gremlin API  | Table API |
|-------------------|---------|---------------|---------------------------------|--------------|-----------|
| Azure Cosmos item | Item    | Row           | Document                        | Node or edge | Item      |

### Explore consistency levels

With Azure Cosmos DB, developers can choose from five well-defined consistency models on the consistency spectrum. From strongest to more relaxed, the models include:

- strong
- bounded staleness
- session
- consistent prefix
- eventual
Each level provides availability and performance tradeoffs

### Discover request units

With Azure Cosmos DB, you pay for the throughput you provision and the storage you consume on an hourly basis. Throughput must be provisioned to ensure that sufficient system resources are available for your Azure Cosmos database at all times.
The cost of all database operations is normalized by Azure Cosmos DB and is expressed by request units (or RUs, for short). A request unit represents the system resources such as CPU, IOPS, and memory that are required to perform the database operations supported by Azure Cosmos DB.
The cost to do a point read, which is fetching a single item by its ID and partition key value, for a 1KB item is 1RU.

There are three modes in which you can create an account:

- Provisioned throughput mode: In this mode, you provision the number of RUs for your application on a per-second basis in increments of 100 RUs per second. To scale the provisioned throughput for your application, you can increase or decrease the number of RUs at any time in increments or decrements of 100 RUs. You can make your changes either programmatically or by using the Azure portal. You can provision throughput at container and database granularity level.

- Serverless mode: In this mode, you don't have to provision any throughput when creating resources in your Azure Cosmos account. At the end of your billing period, you get billed for the amount of request units that has been consumed by your database operations.

- Autoscale mode: In this mode, you can automatically and instantly scale the throughput (RU/s) of your database or container based on it's usage. This mode is well suited for mission-critical workloads that have variable or unpredictable traffic patterns, and require SLAs on high performance and scale.

## Implement partitioning in Azure Cosmos DB

Azure Cosmos DB uses partitioning to scale individual containers in a database to meet the performance needs of your application.

In partitioning, the items in a container are divided into distinct subsets called logical partitions. Logical partitions are formed based on the value of a partition key that is associated with each item in a container. All the items in a logical partition have the same partition key value.

### Logical partitions

A logical partition consists of a set of items that have the same partition key. For example, in a container that contains data about food nutrition, all items contain a foodGroup property. You can use foodGroup as the partition key for the container.

### Physical partitions

Unlike logical partitions, physical partitions are an internal implementation of the system and they are entirely managed by Azure Cosmos DB.
The number of physical partitions in your container depends on the following:

- The number of throughput provisioned (each individual physical partition can provide a throughput of up to 10,000 request units per second). The 10,000 RU/s limit for physical partitions implies that logical partitions also have a 10,000 RU/s limit, as each logical partition is only mapped to one physical partition.
- The total data storage (each individual physical partition can store up to 50GB data).

### Choose a partition key

A partition key has two components: partition key path and the partition key value.
Once you select your partition key, it is not possible to change it in-place. If you need to change your partition key, you should move your data to a new container with your new desired partition key.

For all containers, your partition key should:

- Be a property that has a value which does not change. If a property is your partition key, you can't update that property's value.
- Have a high cardinality. In other words, the property should have a wide range of possible values.
- Spread request unit (RU) consumption and data storage evenly across all logical partitions. This ensures even RU consumption and storage distribution across your physical partitions.

#### Partition keys for read-heavy containers

- For large read-heavy containers you might want to choose a partition key that appears frequently as a filter in your queries. Queries can be efficiently routed to only the relevant physical partitions by including the partition key in the filter predicate.

- if your container is small, you probably don't have enough physical partitions to need to worry about the performance impact of cross-partition queries. If your container could grow to more than a few physical partitions, then you should make sure you pick a partition key that minimizes cross-partition queries.

#### Using item ID as the partition key

If your container has a property that has a wide range of possible values, it is likely a great partition key choice. One possible example of such a property is the item ID. For small read-heavy containers or write-heavy containers of any size, the item ID is naturally a great choice for the partition key.

### Create a synthetic partition key

It's the best practice to have a partition key with many distinct values, such as hundreds or thousands. The goal is to distribute your data and workload evenly across the items associated with these partition key values. If such a property doesn’t exist in your data, you can construct a **synthetic partition key**.

- You can form a partition key by concatenating multiple property values into a single artificial partitionKey property. These keys are referred to as synthetic keys.
- Another possible strategy to distribute the workload more evenly is to append a random number at the end of the partition key value. When you distribute items in this way, you can perform parallel write operations across partitions.
- The random suffix strategy can greatly improve write throughput, but it's difficult to read a specific item. You don't know the suffix value that was used when you wrote the item. To make it easier to read individual items, use the pre-calculated suffixes strategy.

### Create stored procedures

#### Writing stored procedures

Stored procedures can create, update, read, query, and delete items inside an Azure Cosmos container. Stored procedures are registered per collection, and can operate on any document or an attachment present in that collection.

### Create triggers and user-defined functions

Azure Cosmos DB supports pre-triggers and post-triggers. Pre-triggers are executed before modifying a database item and post-triggers are executed after modifying a database item. Triggers are not automatically executed, they must be specified for each database operation where you want them to execute. After you define a trigger, you should register it by using the Azure Cosmos DB SDKs.

- Pre-triggers cannot have any input parameters.
- Pre-triggers can be used to conform data before it's added to the container.
- The post-trigger runs as part of the same transaction for the underlying item itself. An exception during the post-trigger execution will fail the whole transaction. Anything committed will be rolled back and an exception returned.
