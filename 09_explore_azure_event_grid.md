# Explore Azure Event Grid - Table of Contents

- [Explore Azure Event Grid - Table of Contents](#explore-azure-event-grid---table-of-contents)
  - [Concepts in Azure Event Grid](#concepts-in-azure-event-grid)
    - [Discover event schemas](#discover-event-schemas)
    - [Explore event delivery durability](#explore-event-delivery-durability)
      - [Retry policy](#retry-policy)
      - [Output batching](#output-batching)
      - [Dead-letter events](#dead-letter-events)
    - [Built-in roles](#built-in-roles)
      - [Endpoint validation with Event Grid events](#endpoint-validation-with-event-grid-events)
    - [Filter events](#filter-events)
  - [Discover Azure Event Hubs](#discover-azure-event-hubs)
    - [Key concepts](#key-concepts)
    - [Explore Event Hubs Capture](#explore-event-hubs-capture)
      - [How Event Hubs Capture works](#how-event-hubs-capture-works)
      - [Capture windowing](#capture-windowing)
      - [Scaling to throughput units](#scaling-to-throughput-units)
    - [Control access to events](#control-access-to-events)

Azure Event Grid is deeply integrated with Azure services and can be integrated with third-party services. It simplifies event consumption and lowers costs by eliminating the need for constant polling. Event Grid efficiently and reliably routes events from Azure and non-Azure resources, and distributes the events to registered subscriber endpoints.
Azure Event Grid is an eventing backplane that enables event-driven, reactive programming. It uses the publish-subscribe model. Publishers emit events, but have no expectation about how the events are handled. Subscribers decide on which events they want to handle.

## Concepts in Azure Event Grid

There are five concepts in Azure Event Grid you need to understand to help you get started, and are described in more detail below:

- Events - An event is the smallest amount of information that fully describes something that happened in the system.
- Event sources - Where the event took place. Event sources are responsible for sending events to Event Grid.
- Topics - The endpoint where publishers send events.
- Event subscriptions - The endpoint or built-in mechanism to route events, sometimes to more than one handler. Subscriptions are also used by handlers to intelligently filter incoming events.
- Event handlers - The app or service reacting to the event.

### Discover event schemas

Events consist of a set of four required string properties.
When posting events to an event grid topic, the array can have a total size of up to 1 MB. Each event in the array is limited to 1 MB. If an event or the array is greater than the size limits, you receive the response 413 Payload Too Large

```json
[
  {
    "topic": string,
    "subject": string, - REQUIRED
    "id": string, - REQUIRED
    "eventType": string, - REQUIRED
    "eventTime": string, - REQUIRED
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

### Explore event delivery durability

Event Grid provides durable delivery. It tries to deliver each event at least once for each matching subscription immediately
If a subscriber's endpoint doesn't acknowledge receipt of an event or if there is a failure, Event Grid retries delivery based on a fixed retry schedule and retry policy. By default, Event Grid delivers one event at a time to the subscriber, and the payload is an array with a single event.
Event Grid doesn't guarantee order for event delivery, so subscribers may receive them out of order.

The following table describes the types of endpoints and errors for which retry doesn't happen:

| Endpoint        | Type Error codes                                                                              |
| --------------- | --------------------------------------------------------------------------------------------- |
| Azure Resources | 400 Bad Request, 413 Request Entity Too Large, 403 Forbidden                                  |
| Webhook         | 400 Bad Request, 413 Request Entity Too Large, 403 Forbidden, 404 Not Found, 401 Unauthorized |

If the error returned by the subscribed endpoint isn't among the above list, Event Grid waits 30 seconds for a response after delivering a message. After 30 seconds, if the endpoint hasn’t responded, the message is queued for retry. Event Grid uses an exponential backoff retry policy for event delivery.

#### Retry policy

You can customize the retry policy when creating an event subscription by using the following two configurations. An event will be dropped if either of the limits of the retry policy is reached.

- Maximum number of attempts - The value must be an integer between 1 and 30. The default value is 30.
- Event time-to-live (TTL) - The value must be an integer between 1 and 1440. The default value is 1440 minutes

#### Output batching

You can configure Event Grid to batch events for delivery for improved HTTP performance in high-throughput scenarios. Batching is turned off by default and can be turned on per-subscription via the portal, CLI, PowerShell, or SDKs.

Batched delivery has two settings:

Max events per batch - Maximum number of events Event Grid will deliver per batch. This number will never be exceeded, however fewer events may be delivered if no other events are available at the time of publish. Event Grid doesn't delay events to create a batch if fewer events are available. Must be between 1 and 5,000.

Preferred batch size in kilobytes - Target ceiling for batch size in kilobytes. Similar to max events, the batch size may be smaller if more events aren't available at the time of publish. It's possible that a batch is larger than the preferred batch size if a single event is larger than the preferred size. For example, if the preferred size is 4 KB and a 10-KB event is pushed to Event Grid, the 10-KB event will still be delivered in its own batch rather than being dropped.

#### Dead-letter events

When Event Grid can't deliver an event within a certain time period or after trying to deliver the event a certain number of times, it can send the undelivered event to a storage account. This process is known as dead-lettering. Event Grid dead-letters an event when one of the following conditions is met.

- Event isn't delivered within the time-to-live period.
- The number of tries to deliver the event exceeds the limit.
  
By default, Event Grid doesn't turn on dead-lettering. To enable it, you must specify a storage account to hold undelivered events when creating the event subscription. You pull events from this storage account to resolve deliveries.

### Built-in roles

Event Grid provides the following built-in roles:

| Role                               | Description                                               |
| ---------------------------------- | --------------------------------------------------------- |
| Event Grid Subscription Reader     | Lets you read Event Grid event subscriptions.             |
| Event Grid Subscriptio Contributor | Lets you manage Event Grid event subscription operations. |
| Event Grid Contributor             | Lets you create and manage Event Grid resources.          |
| Event Grid Data Sender             | Lets you send events to Event Grid topics.                |

If you're using an event handler that isn't a WebHook (such as an event hub or queue storage), you need write access to that resource. This permissions check prevents an unauthorized user from sending events to your resource.

You must have the Microsoft.EventGrid/EventSubscriptions/Write permission on the resource that is the event source. You need this permission because you're writing a new subscription at the scope of the resource.

- System topics:
  - Need permission to write a new event subscription at the scope of the resource publishing the event. The format of the resource is: /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}
- Custom topics:
  - Need permission to write a new event subscription at the scope of the event grid topic. The format of the resource is: /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}

#### Endpoint validation with Event Grid events

If you're using any other type of endpoint, such as an HTTP trigger based Azure function, your endpoint code needs to participate in a validation handshake with Event Grid.
Starting with version 2018-05-01-preview, Event Grid supports a manual validation handshake. If you're creating an event subscription with an SDK or tool that uses API version 2018-05-01-preview or later, Event Grid sends a validationUrl property in the data portion of the subscription validation event. To complete the handshake, find that URL in the event data and do a GET request to it. You can use either a REST client or your web browser.

The provided URL is valid for 5 minutes. During that time, the provisioning state of the event subscription is AwaitingManualAction. If you don't complete the manual validation within 5 minutes, the provisioning state is set to Failed. You'll have to create the event subscription again before starting the manual validation.

This authentication mechanism also requires the webhook endpoint to return an HTTP status code of 200 so that it knows that the POST for the validation event was accepted before it can be put in the manual validation mode. In other words, if the endpoint returns 200 but doesn't return back a validation response synchronously, the mode is transitioned to the manual validation mode. If there's a GET on the validation URL within 5 minutes, the validation handshake is considered to be successful.

### Filter events

When creating an event subscription, you have three options for filtering:

- Event types:

    ```json
        "filter": {
            "includedEventTypes": [
                "Microsoft.Resources.ResourceWriteFailure",
                "Microsoft.Resources.ResourceWriteSuccess"
            ]
        }
    ```

- Subject begins with or ends with:

    ```json
        "filter": {
                "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
                "subjectEndsWith": ".jpg"
                }
    ```

- Advanced fields and operators:

    ```json
        "filter": {
            "advancedFilters": [
                {
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key1",
                "value": 5
                },
                {
                "operatorType": "StringContains",
                "key": "Subject",
                "values": ["container1", "container2"]
                }
            ]
            }
    ```

## Discover Azure Event Hubs

Azure Event Hubs represents the "front door" for an event pipeline, often called an event ingestor in solution architectures. An event ingestor is a component or service that sits between event publishers and event consumers to decouple the production of an event stream from the consumption of those events. Event Hubs provides a unified streaming platform with time retention buffer, decoupling event producers from event consumers.

- Fully managed PaaS: Event Hubs is a fully managed Platform-as-a-Service (PaaS) with little configuration or management overhead, so you focus on your business solutions. Event Hubs for Apache Kafka ecosystems gives you the PaaS Kafka experience without having to manage, configure, or run your clusters.
- Real-time and batch processing: Event Hubs uses a partitioned consumer model, enabling multiple applications to process the stream concurrently and letting you control the speed of processing.

### Key concepts

Event Hubs contains the following key components:

- An Event Hub client is the primary interface for developers interacting with the Event Hubs client library. There are several different Event Hub clients, each dedicated to a specific use of Event Hubs, such as publishing or consuming events.
- An Event Hub producer is a type of client that serves as a source of telemetry data, diagnostics information, usage logs, or other log data, as part of an embedded device solution, a mobile device application, a game title running on a console or other device, some client or server based business solution, or a web site.
- An Event Hub consumer is a type of client which reads information from the Event Hub and allows processing of it. Processing may involve aggregation, complex computation and filtering. Processing may also involve distribution or storage of the information in a raw or transformed fashion. Event Hub consumers are often robust and high-scale platform infrastructure parts with built-in analytics capabilities, like Azure Stream Analytics, Apache Spark, or Apache Storm.
- A partition is an ordered sequence of events that is held in an Event Hub. Partitions are a means of data organization associated with the parallelism required by event consumers. Azure Event Hubs provides message streaming through a partitioned consumer pattern in which each consumer only reads a specific subset, or partition, of the message stream. As newer events arrive, they are added to the end of this sequence. The number of partitions is specified at the time an Event Hub is created and cannot be changed.
- A consumer group is a view of an entire Event Hub. Consumer groups enable multiple consuming applications to each have a separate view of the event stream, and to read the stream independently at their own pace and from their own position. There can be at most 5 concurrent readers on a partition per consumer group; however it is recommended that there is only one active consumer for a given partition and consumer group pairing. Each active reader receives all of the events from its partition; if there are multiple readers on the same partition, then they will receive duplicate events.
- Event receivers: Any entity that reads event data from an event hub. All Event Hubs consumers connect via the AMQP 1.0 session. The Event Hubs service delivers events through a session as they become available. All Kafka consumers connect via the Kafka protocol 1.0 and later.
- Throughput units or processing units: Pre-purchased units of capacity that control the throughput capacity of Event Hubs.

### Explore Event Hubs Capture

Azure Event Hubs enables you to automatically capture the streaming data in Event Hubs in an Azure Blob storage or Azure Data Lake Storage account of your choice, with the added flexibility of specifying a time or size interval.

#### How Event Hubs Capture works

Event Hubs is a time-retention durable buffer for telemetry ingress, similar to a distributed log. The key to scaling in Event Hubs is the partitioned consumer model. Each partition is an independent segment of data and is consumed independently. Over time this data ages off, based on the configurable retention period. As a result, a given event hub never gets "too full."

Captured data is written in Apache Avro format

#### Capture windowing

Event Hubs Capture enables you to set up a window to control capturing. This window is a minimum size and time configuration with a "first wins policy," meaning that the first trigger encountered causes a capture operation.
The storage naming convention is as follows:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

#### Scaling to throughput units

Event Hubs traffic is controlled by throughput units. A single throughput unit allows 1 MB per second or 1000 events per second of ingress and twice that amount of egress. Standard Event Hubs can be configured with 1-20 throughput units, and you can purchase more with a quota increase support request.

### Control access to events

Azure Event Hubs supports both Azure Active Directory and shared access signatures (SAS) to handle both authentication and authorization. Azure provides the following Azure built-in roles for authorizing access to Event Hubs data using Azure Active Directory and OAuth:

- Azure Event Hubs Data Owner: Use this role to give complete access to Event Hubs resources.
- Azure Event Hubs Data Sender: Use this role to give send access to Event Hubs resources.
- Azure Event Hubs Data Receiver: Use this role to give receiving access to Event Hubs resources.

[review](https://learn.microsoft.com/en-us/training/modules/azure-event-hubs/5-event-hubs-authentication-authorization)
