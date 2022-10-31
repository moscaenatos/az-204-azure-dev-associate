# Develop message-based solutions - Table of contents

- [Develop message-based solutions - Table of contents](#develop-message-based-solutions---table-of-contents)
  - [Discover Azure message queues](#discover-azure-message-queues)
    - [Choose a message queue solution](#choose-a-message-queue-solution)
      - [Consider using Service Bus queues](#consider-using-service-bus-queues)
      - [Consider using Storage queues](#consider-using-storage-queues)
    - [Explore Azure Service Bus](#explore-azure-service-bus)
    - [Discover Service Bus queues, topics, and subscriptions](#discover-service-bus-queues-topics-and-subscriptions)
      - [Queues](#queues)
      - [Receive modes](#receive-modes)
      - [Topics and subscriptions](#topics-and-subscriptions)

## Discover Azure message queues

Azure supports two types of queue mechanisms: Service Bus queues and Storage queues.

- Service Bus queues are part of a broader Azure messaging infrastructure that supports queuing, publish/subscribe, and more advanced integration patterns. They're designed to integrate applications or application components that may span multiple communication protocols, data contracts, trust domains, or network environments.

- Storage queues are part of the Azure Storage infrastructure. They allow you to store large numbers of messages. You access messages from anywhere in the world via authenticated calls using HTTP or HTTPS. A queue message can be up to 64 KB in size. A queue may contain millions of messages, up to the total capacity limit of a storage account. Queues are commonly used to create a backlog of work to process asynchronously.

### Choose a message queue solution

#### Consider using Service Bus queues

As a solution architect/developer, you should consider using Service Bus queues when:

- Your solution needs to receive messages without having to poll the queue. With Service Bus, you can achieve it by using a long-polling receive operation using the TCP-based protocols that Service Bus supports.
- Your solution requires the queue to provide a guaranteed first-in-first-out (FIFO) ordered delivery.
- Your solution needs to support automatic duplicate detection.
- You want your application to process messages as parallel long-running streams (messages are associated with a stream using the session ID property on the message). In this model, each node in the consuming application competes for streams, as opposed to messages. When a stream is given to a consuming node, the node can examine the state of the application stream state using transactions.
- Your solution requires transactional behavior and atomicity when sending or receiving multiple messages from a queue.
- Your application handles messages that can exceed 64 KB but won't likely approach the 256-KB limit.

#### Consider using Storage queues

As a solution architect/developer, you should consider using Storage queues when:

- Your application must store over 80 gigabytes of messages in a queue.
- Your application wants to track progress for processing a message in the queue. It's useful if the worker processing a message crashes. Another worker can then use that information to continue from where the prior worker left off.
- You require server side logs of all of the transactions executed against your queues.

### Explore Azure Service Bus

Microsoft Azure Service Bus is a fully managed enterprise integration message broker. Service Bus can decouple applications and services. Data is transferred between different applications and services using messages. A message is a container decorated with metadata, and contains data. The data can be any kind of information, including structured data encoded with the common formats such as the following ones: JSON, XML, Apache Avro, Plain Text.

### Discover Service Bus queues, topics, and subscriptions

#### Queues

Queues offer First In, First Out (FIFO) message delivery to one or more competing consumers. That is, receivers typically receive and process messages in the order in which they were added to the queue. And, only one message consumer receives and processes each messag

#### Receive modes

You can specify two different modes in which Service Bus receives messages: Receive and delete or Peek lock.

- Receive and delete
    In this mode, when Service Bus receives the request from the consumer, it marks the message as being consumed and returns it to the consumer application.

- Peek lock
    In this mode, the receive operation becomes two-stage, which makes it possible to support applications that can't tolerate missing messages.
  - Finds the next message to be consumed, locks it to prevent other consumers from receiving it, and then, return the message to the application.
  - After the application finishes processing the message, it requests the Service Bus service to complete the second stage of the receive process. Then, the service marks the message as being consumed

#### Topics and subscriptions

In contrast to queues, topics and subscriptions provide a one-to-many form of communication in a publish and subscribe pattern. It's useful for scaling to large numbers of recipients.
