# Exam Practice #8 - Table of contents

Objectives Percentage (Corrects/Number Questions)
Develop Azure compute solutions 71.43 % (10/14)
Develop for Azure storage 85.71 % (6/7)
Implement Azure security 83.33 % (10/12)
Monitor, troubleshoot, and optimize Azure solutions 100 % (4/4)
Connect to and consume Azure services and third-party services 80 % (8/10)
Customer Score: 80.85% - PASS - 38/47 (0 Unanswered)

- [Exam Practice #8 - Table of contents](#exam-practice-8---table-of-contents)
  - [Develop Azure compute solutions](#develop-azure-compute-solutions)
    - [Implent IaaS solutions](#implent-iaas-solutions)
  - [Create Azure App Service Web Apps](#create-azure-app-service-web-apps)
  - [Develop for Azure storage](#develop-for-azure-storage)
    - [Develop solutions tht use Cosmos DB storage](#develop-solutions-tht-use-cosmos-db-storage)
  - [Implement Azure security](#implement-azure-security)
    - [Implement secure cloud solutions](#implement-secure-cloud-solutions)

## Develop Azure compute solutions

### Implent IaaS solutions

- To generate a container image:
    You should use the az acr build command. After a successful build, the image is automatically pushed to your container registry
- To publish the most recent Windows Core image in a Azure Container Registry named myreg1:
    az acr import --name myreg1 --source mcr.microsoft.com/windows/servercore:latest
    The az acr import command imports the image into the ACR and publishes it to myreg1. The image will be available for use in myreg1 after the
    import. The --name parameter identifies the ACR resource name in which the image is published.

## Create Azure App Service Web Apps

- Metric source =  Service Bus queue it is used to specify that you want to collect the logs from  that service

- Filesystem and Blob storage options are not supported for both Windows and Linux apps.
  - Filesystem and Blob storage are supported for Windows apps.
  - Filesystem storage is the only storage option supported for Linux apps.

## Develop for Azure storage

### Develop solutions tht use Cosmos DB storage

- When querying Cosmos Db storage always use container alyases such as
  - SELECT * FROM root r WHERE r.category = "Lodging"
  - SELECT * FROM receipts r WHERE r.category = "Lodging"

## Implement Azure security

### Implement secure cloud solutions
