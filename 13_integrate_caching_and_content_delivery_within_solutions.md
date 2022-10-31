# Integrate caching and content delivery within solutions - Table Contents

- [Integrate caching and content delivery within solutions - Table Contents](#integrate-caching-and-content-delivery-within-solutions---table-contents)
  - [Develop for Azure Cache for Redis](#develop-for-azure-cache-for-redis)
    - [Configure Azure Cache for Redis](#configure-azure-cache-for-redis)
  - [Develop for storage on CDNs](#develop-for-storage-on-cdns)
    - [Requirements](#requirements)
    - [Limitations](#limitations)
    - [Control cache behavior on Azure Content Delivery Networks](#control-cache-behavior-on-azure-content-delivery-networks)
      - [Controlling caching behavior](#controlling-caching-behavior)

## Develop for Azure Cache for Redis

Azure Cache for Redis provides an in-memory data store based on the Redis software. Redis improves the performance and scalability of an application that uses backend data stores heavily. It's able to process large volumes of application requests by keeping frequently accessed data in the server memory, which can be written to and read from quickly. Redis brings a critical low-latency and high-throughput data storage solution to modern applications.

Azure Cache for Redis offers both the Redis open-source (OSS Redis) and a commercial product from Redis Labs (Redis Enterprise) as a managed service.

Service tiers
Azure Cache for Redis is available in these tiers:

| Tier             | Description                                                                                                                                                                                                                       |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Basic            | An OSS Redis cache running on a single VM. This tier has no service-level agreement (SLA) and is ideal for development/test and non-critical workloads.                                                                           |
| Standard         | An OSS Redis cache running on two VMs in a replicated configuration.                                                                                                                                                              |
| Premium          | High-performance OSS Redis caches. This tier offers higher throughput, lower latency, better availability, and more features. Premium caches are deployed on more powerful VMs compared to the VMs for Basic or Standard caches.  |
| Enterprise       | High-performance caches powered by Redis Labs' Redis Enterprise software. This tier supports Redis modules including RediSearch, RedisBloom, and RedisTimeSeries. Also, it offers even higher availability than the Premium tier. |
| Enterprise Flash | Cost-effective large caches powered by Redis Labs' Redis Enterprise software. This tier extends Redis data storage to non-volatile memory, which is cheaper than DRAM, on a VM. It reduces the overall per-GB memory cost.        |

### Configure Azure Cache for Redis

You can create a Redis cache using the Azure portal, the Azure CLI, or Azure PowerShell.

There are several parameters you will need to decide in order to configure the cache properly for your purposes.

- Name: The Redis cache will need a globally unique name. The name has to be unique within Azure because it is used to generate a public-facing URL to connect and communicate with the service.
- Location: You will need to decide where the Redis cache will be physically located by selecting an Azure region. You should always place your cache instance and your application in the same region. Connecting to a cache in a different region can significantly increase latency and reduce reliability. If you are connecting to the cache outside of Azure, then select a location close to where the application consuming the data is running.
  - Put the Redis cache as close to the data consumer as you can.
- Pricing tier:three pricing tiers available for an Azure Cache for Redis.
  - Basic: Basic cache ideal for development/testing. Is limited to a single server, 53 GB of memory, and 20,000 connections. There is no SLA for this service tier.
  - Standard: Production cache which supports replication and includes an SLA. It supports two servers, and has the same memory/connection limits as the Basic tier.
  - Premium: Enterprise tier which builds on the Standard tier and includes persistence, clustering, and scale-out cache support. This is the highest performing tier with up to 530 GB of memory and 40,000 simultaneous connections.
    - Virtual Network support
    - Clustering support

## Develop for storage on CDNs

A content delivery network (CDN) is a distributed network of servers that can efficiently deliver web content to users. CDNs' store cached content on edge servers in point-of-presence (POP) locations that are close to end users, to minimize latency.

Azure Content Delivery Network (CDN) offers developers a global solution for rapidly delivering high-bandwidth content to users by caching their content at strategically placed physical nodes across the world. Azure CDN can also accelerate dynamic content, which cannot be cached, by leveraging various network optimizations using CDN POPs.
The benefits of using Azure CDN to deliver web site assets include:

Better performance and improved user experience for end users, especially when using applications in which multiple round-trips are required to load content.
Large scaling to better handle instantaneous high loads, such as the start of a product launch event.
Distribution of user requests and serving of content directly from edge servers so that less traffic is sent to the origin server.

### Requirements

To use Azure CDN you need to create at least one CDN profile, which is a collection of CDN endpoints. Every CDN endpoint represents a specific configuration of content deliver behavior and access. To organize your CDN endpoints by internet domain, web application, or some other criteria, you can use multiple profiles.
Azure CDN pricing is applied at the CDN profile level,

### Limitations

Each Azure subscription has default limits for the following resources:

The number of CDN profiles that can be created.
The number of endpoints that can be created in a CDN profile.

The number of custom domains that can be mapped to an endpoint.

### Control cache behavior on Azure Content Delivery Networks

Because a cached resource can potentially be out-of-date or stale (compared to the corresponding resource on the origin server), it is important for any caching mechanism to control when content is refreshed. A cached resource is considered to be fresh when its age is less than the age or period defined by a cache setting.

#### Controlling caching behavior

Azure CDNs provide two mechanisms for caching files. However, these configuration settings depend on the tier you've selected.

- Caching rules. Caching rules can be either global (apply to all content from a specified endpoint) or custom. Custom rules apply to specific paths and file extensions.
- Query string caching. Query string caching enables you to configure how Azure CDN responds to a query string. Query string caching has no effect on files that can't be cached.
