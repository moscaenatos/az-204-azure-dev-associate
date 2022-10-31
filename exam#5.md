# Exam Practice #5 - Table of contents

Passing Score: 80% (80/100) Time Taken: 00:23:47 Date: 10/21/2022
Objectives Percentage (Corrects/Number Questions)
Develop Azure compute solutions 68.75 % (11/16)
Develop for Azure storage 50 % (3/6)
Implement Azure security 100 % (9/9)
Monitor, troubleshoot, and optimize Azure solutions 100 % (6/6)
Connect to and consume Azure services and third-party services 70 % (7/10)
Customer Score: 76.6% - FAIL - 36/47 (0 Unanswered)

- [Exam Practice #5 - Table of contents](#exam-practice-5---table-of-contents)
  - [Develop Azure compute solutions](#develop-azure-compute-solutions)
    - [Implent IaaS solutions](#implent-iaas-solutions)
    - [Create Azure App Service Web Apps](#create-azure-app-service-web-apps)
  - [Develop for Azure storage](#develop-for-azure-storage)
    - [Develop solutions that use Cosmos Db Storage](#develop-solutions-that-use-cosmos-db-storage)
  - [Connect to and consume Azure services and third-party services](#connect-to-and-consume-azure-services-and-third-party-services)
    - [Implement API ManagementExplanation](#implement-api-managementexplanation)

## Develop Azure compute solutions

### Implent IaaS solutions

- To build and publish the app1 container image to Azure Container registry named registry1

  1. Build the Dockerfile with docker build.
  2. Tag the image as registry1.azurecr.io/app1.
  3. Login in registry1 with az acr login and push the image to registry1.

- Virtual Machine
  - Azure Disk Encryption is supported for both generation 1 and generation 2 VMs.
  - You must convert the VHDX file to VHD before uploading. The VHDX file format is not supported.
  - You must convert the dynamic disk to a fixed-size disk before uploading.

- to save a customized VM image to be later used to support the provisioning of additional instance, you must first **stop** the vm

### Create Azure App Service Web Apps

- to minotr the CPU usa oge an App Service, you need to choose the App Service Plan resource as it contains hte metrics related to VM usage such as CPU and memory percentage and Disk Queue Length

## Develop for Azure storage

### Develop solutions that use Cosmos Db Storage

- To guarantee the highest possible availability and lowest latency for data reads use the **Eventual consistency level**

## Connect to and consume Azure services and third-party services

### Implement API ManagementExplanation

- To succesfully call an Api in the APIM
  1. You should add the API to a product in the Azure portal. Every API must be part of a product before it can be consumed through APIM.
  2. You should also include the Ocp-Apim-Subscription-Key header in the HTTP request. This header must specify the subscription key assigned to you when you subscribe to the product. This allows APIM to determine whether or not the subscriber has permission to call a particular API.

- To cache:
  - You should enable operation caching and do not enter a value for the Vary by query string parameters field, allowing the entire URL to be used to cache responses. When a URL is used to return data, subsequent responses for that same URL return data from the cache, reducing the load on the company's database servers.

- A revision allows you to add non-breking changes to a web API. Users can access the revision by using a different query string at the same endpoint
- A version allows you to expose brekaing changes for a web API. A version **requires** publishing
