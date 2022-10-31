# Table of contents - Implement infrastructure as a service solutions

- [Table of contents - Implement infrastructure as a service solutions](#table-of-contents---implement-infrastructure-as-a-service-solutions)
  - [Provision virtual machines in Azure](#provision-virtual-machines-in-azure)
    - [Availability zones](#availability-zones)
    - [Availability sets](#availability-sets)
    - [Load balancer](#load-balancer)
    - [Determine appropriate virtual machine size](#determine-appropriate-virtual-machine-size)
  - [Create and deploy Azure Resource Manager templates](#create-and-deploy-azure-resource-manager-templates)
    - [Explore conditional deployment](#explore-conditional-deployment)
    - [Set the correct deployment mode](#set-the-correct-deployment-mode)
    - [Manage container images in Azure Container Registry](#manage-container-images-in-azure-container-registry)
      - [Build and manage containers with tasks](#build-and-manage-containers-with-tasks)
    - [Run container images in Azure Container Instances](#run-container-images-in-azure-container-instances)
      - [Container groups](#container-groups)
      - [Mount an Azure file share in Azure Container Instances](#mount-an-azure-file-share-in-azure-container-instances)

## Provision virtual machines in Azure

An Azure virtual machine gives you the flexibility of virtualization without having to buy and maintain the physical hardware that runs it. However, you still need to maintain the VM by performing tasks, such as configuring, patching, and installing the software that runs on it.

- VM disks: There are two components that make up this area. The type of disks which determines the performance level and the storage account type that contains the disks. Azure provides two types of disks:
  - Standard disks: Backed by HDDs, and delivers cost-effective storage while still being performant. Standard disks are ideal for a cost effective dev and test workload.
  - Premium disks: Backed by SSD-based, high-performance, low-latency disk. Perfect for VMs running production workload.
- Two options for the disk storage:

  - Managed disks: Managed disks are the newer and recommended disk storage model and they are managed by Azure. You specify the size of the disk, which can be up to 4 terabytes (TB), and Azure creates and manages both the disk and the storage. You don't have to worry about storage account limits, which makes managed disks easier to scale out than unmanaged discs.
  - Unmanaged disks: With unmanaged disks, you’re responsible for the storage accounts that hold the virtual hard disks (VHDs) that correspond to your VM disks. You pay the storage account rates for the amount of space you use. A single storage account has a fixed-rate limit of 20,000 input/output (I/O) operations per second. This means that a storage account is capable of supporting 40 standard VHDs at full utilization. If you need to scale out with more disks, then you'll need more storage accounts, which can get complicated.

### Availability zones

Availability zones expands the level of control you have to maintain the availability of the applications and data on your VMs.
There are three Availability Zones per supported Azure region. An Availability Zone in an Azure region is a combination of a fault domain and an update domain.
Azure services that support Availability Zones fall into two categories:

- Zonal services: Where a resource is pinned to a specific zone (for example, virtual machines, managed disks, Standard IP addresses), or
- Zone-redundant services: When the Azure platform replicates automatically across zones (for example, zone-redundant storage, SQL Database).

### Availability sets

An availability set is a logical grouping of VMs that allows Azure to understand how your application is built to provide for redundancy and availability. An availability set is composed of two additional groupings that protect against hardware failures and allow updates to safely be applied - fault domains (FDs) and update domains (UDs).

- Fault domains:
    A fault domain is a logical group of underlying hardware that share a common power source and network switch, similar to a rack within an on-premises datacenter.
    This approach limits the impact of potential physical hardware failures, network outages, or power interruptions.

- Update domains:
    An update domain is a logical group of underlying hardware that can undergo maintenance or be rebooted at the same time.
    As you create VMs within an availability set, the Azure platform automatically distributes your VMs across these update domains. This approach ensures that at least one instance of your application always remains running as the Azure platform undergoes periodic maintenance

### Load balancer

Combine the Azure Load Balancer with an availability zone or availability set to get the most application resiliency.

### Determine appropriate virtual machine size

You can resize the VM - as long as your current hardware configuration is allowed in the new size
Be cautious when resizing production VMs - they will be rebooted automatically which can cause a temporary outage and change some configuration settings such as the IP address.

| VM Type                  | Description                                                                                                                                                                                     |
| ------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| General Purpose          | Balanced CPU-to-memory ratio. Ideal for testing and development, small to medium databases, and low to medium traffic web servers.                                                              |
| Compute Optimized        | High CPU-to-memory ratio. Good for medium traffic web servers, network appliances, batch processes, and application servers.                                                                    |
| Memory Optimized         | High memory-to-CPU ratio. Great for relational database servers, medium to large caches, and in-memory analytics.                                                                               |
| Storage Optimized        | High disk throughput and IO ideal for Big Data, SQL, NoSQL databases, data warehousing and large transactional databases.                                                                       |
| GPU                      | Specialized virtual machines targeted for heavy graphic rendering and video editing, as well as model training and inferencing (ND) with deep learning. Available with single or multiple GPUs. |
| High Performance Compute | Our fastest and most powerful CPU virtual machines with optional high-throughput network interfaces (RDMA).                                                                                     |

## Create and deploy Azure Resource Manager templates

Azure Resource Manager is the deployment and management service for Azure. It provides a management layer that enables you to create, update, and delete resources in your Azure subscription.

Azure Resource Manager templates advantages:

- Declarative syntax: Azure Resource Manager templates allow you to create and deploy an entire Azure infrastructure declaratively.
- Repeatable results: Repeatedly deploy your infrastructure throughout the development lifecycle and have confidence your resources are deployed in a consistent manner. Templates are idempotent,
- Orchestration: You don't have to worry about the complexities of ordering operations. Resource Manager orchestrates the deployment of interdependent resources so they're created in the correct order.

With Resource Manager, you can create a template (in JSON format) that defines the infrastructure and configuration of your Azure solution. By using a template, you can repeatedly deploy your solution throughout its lifecycle and have confidence your resources are deployed in a consistent state.

- Azure portal
- Azure CLI
- PowerShell
- REST API
- Button in GitHub repository
- Azure Cloud Shell

### Explore conditional deployment

Sometimes you need to optionally deploy a resource in an Azure Resource Manager template (Azure Resource Manager template). Use the condition element to specify whether the resource is deployed. The value for the condition resolves to true or false. When the value is true, the resource is created. When the value is false, the resource isn't created. The value can only be applied to the whole resource.
Conditional deployment doesn't cascade to child resources. If you want to conditionally deploy a resource and its child resources, you must apply the same condition to each resource type.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "newOrExisting": {
      "type": "string",
      "defaultValue": "new",
      "allowedValues": [
        "new",
        "existing"
      ]
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[equals(parameters('newOrExisting'), 'new')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

When the parameter newOrExisting is set to new, the condition evaluates to true. The storage account is deployed. However, when newOrExisting is set to existing, the condition evaluates to false and the storage account isn't deployed.

### Set the correct deployment mode

When deploying your resources, you specify that the deployment is either an incremental update or a complete update. The difference between these two modes is how Resource Manager handles existing resources in the resource group that aren't in the template. The default mode is incremental.

- Complete mode
    In complete mode, Resource Manager deletes resources that exist in the resource group that aren't specified in the template.

- Incremental mode
    In incremental mode, Resource Manager leaves unchanged resources that exist in the resource group but aren't specified in the template.

### Manage container images in Azure Container Registry

Azure Container Registry (ACR) is a managed, private Docker registry service based on the open-source Docker Registry 2.0. Create and maintain Azure container registries to store and manage your private Docker container images.

Pull images from an Azure container registry to various deployment targets:

- Scalable orchestration systems that manage containerized applications across clusters of hosts, including Kubernetes, DC/OS, and Docker Swarm.
- Azure services that support building and running applications at scale, including Azure Kubernetes Service (AKS), App Service, Batch, Service Fabric, and others.

Azure Container Registry is available in multiple service tiers. These tiers provide predictable pricing and several options for aligning to the capacity and usage patterns of your private Docker registry in Azure.
Grouped in a repository, each image is a read-only snapshot of a Docker-compatible container.
Use Azure Container Registry Tasks (ACR Tasks) to streamline building, testing, pushing, and deploying images in Azure.
ACR Tasks enables automated builds triggered by source code updates, updates to a container's base image, or timers.

| Tier     | Description                                                                                                                                                                                                                                                                                                                                                                                 |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Basic    | A cost-optimized entry point for developers learning about Azure Container Registry. Basic registries have the same programmatic capabilities as Standard and Premium (such as Azure Active Directory authentication integration, image deletion, and webhooks). However, the included storage and image throughput are most appropriate for lower usage scenarios.                         |
| Standard | Standard registries offer the same capabilities as Basic, with increased included storage and image throughput. Standard registries should satisfy the needs of most production scenarios.                                                                                                                                                                                                  |
| Premium  | Premium registries provide the highest amount of included storage and concurrent operations, enabling high-volume scenarios. In addition to higher image throughput, Premium adds features such as geo-replication for managing a single registry across multiple regions, content trust for image tag signing, and private link with private endpoints to restrict access to the registry. |

Every Basic, Standard, and Premium Azure container registry benefits from advanced Azure storage features like encryption-at-rest for image data security and geo-redundancy for image data protection.

#### Build and manage containers with tasks

ACR Tasks supports several scenarios to build and maintain container images and other artifacts.

- Quick task - Build and push a single container image to a container registry on-demand, in Azure, without needing a local Docker Engine installation. Think docker build, docker push in the cloud.
- Automatically triggered tasks - Enable one or more triggers to build an image:
  - Trigger on source code update
  - Trigger on base image update
  - Trigger on a schedule
- Multi-step task - Extend the single image build-and-push capability of ACR Tasks with multi-step, multi-container-based workflows.

Each ACR Task has an associated source code context - the location of a set of source files used to build a container image or other artifact. Example contexts include a Git repository or a local filesystem.

### Run container images in Azure Container Instances

Azure Container Instances (ACI) offers the fastest and simplest way to run a container in Azure, without having to manage any virtual machines and without having to adopt a higher-level service.

#### Container groups

The top-level resource in Azure Container Instances is the container group. A container group is a collection of containers that get scheduled on the same host machine. The containers in a container group share a lifecycle, resources, local network, and storage volumes.
Multi-container groups currently support only Linux containers. For Windows containers, Azure Container Instances only supports deployment of a single instance.
Multi-container groups are useful in cases where you want to divide a single functional task into a small number of container images. These images can then be delivered by different teams and have separate resource requirements.

![example-container-group](https://learn.microsoft.com/en-us/training/wwl-azure/create-run-container-images-azure-container-instances/media/container-groups-example.png)

This example container group:

- Is scheduled on a single host machine.
- Is assigned a DNS name label.
- Exposes a single public IP address, with one exposed port.
- Consists of two containers. One container listens on port 80, while the other listens on port 5000.
- Includes two Azure file shares as volume mounts, and each container mounts one of the shares locally.

There are two common ways to deploy a multi-container group: use a Resource Manager template or a YAML file. A Resource Manager template is recommended when you need to deploy additional Azure service resources (for example, an Azure Files share) when you deploy the container instances. Due to the YAML format's more concise nature, a YAML file is recommended when your deployment includes only container instances.

When you create a container group in Azure Container Instances, you can specify one of three restart policy settings.

| Restart policy | Description                                                                                                                                                                                |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Always         | Containers in the container group are always restarted. This is the default setting applied when no restart policy is specified at container creation.                                     |
| Never          | Containers in the container group are never restarted. The containers run at most once.                                                                                                    |
| OnFailure      | Containers in the container group are restarted only when the process executed in the container fails (when it terminates with a nonzero exit code). The containers are run at least once. |

Azure Container Instances starts the container, and then stops it when its application, or script, exits. When Azure Container Instances stops a container whose restart policy is Never or OnFailure, the container's status is set to Terminated.

Objects with secure values are intended to hold sensitive information like passwords or keys for your application. Using secure values for environment variables is both safer and more flexible than including it in your container's image.
Set a secure environment variable by specifying the secureValue property instead of the regular value for the variable's type.

#### Mount an Azure file share in Azure Container Instances

By default, Azure Container Instances are stateless. If the container crashes or stops, all of its state is lost. To persist state beyond the lifetime of the container, you must mount a volume from an external store.
Azure Container Instances can mount an Azure file share created with Azure Files. Azure Files offers fully managed file shares in the cloud that are accessible via the industry standard Server Message Block (SMB) protocol.

- You can only mount Azure Files shares to Linux containers.
- Azure file share volume mount requires the Linux container run as root.

You must provide the storage account name and key for it to work. It can be either mounted through an yaml file or with the ```az container create``` command
