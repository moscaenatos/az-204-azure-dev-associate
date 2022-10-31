# Exam Practice #6 - Table of contents

Objectives Percentage (Corrects/Number Questions)
Develop Azure compute solutions 56.25 % (9/16)
Develop for Azure storage 66.67 % (6/9)
Implement Azure security 75 % (6/8)
Monitor, troubleshoot, and optimize Azure solutions 100 % (5/5)
Connect to and consume Azure services and third-party services 100 % (9/9)
Customer Score: 74.47% - FAIL - 35/47 (0 Unanswered)

- [Exam Practice #6 - Table of contents](#exam-practice-6---table-of-contents)
  - [Develop Azure compute solutions](#develop-azure-compute-solutions)
    - [Implent IaaS solutions](#implent-iaas-solutions)
    - [Create Azure app Service Web Apps](#create-azure-app-service-web-apps)
    - [Implement Azure Functions](#implement-azure-functions)
  - [Develop for Azure Storage](#develop-for-azure-storage)
    - [Develop solutions that uses Cosmos DB storage](#develop-solutions-that-uses-cosmos-db-storage)
    - [Develop solutions that use blob storage](#develop-solutions-that-use-blob-storage)
  - [Implement Azure security](#implement-azure-security)

## Develop Azure compute solutions

### Implent IaaS solutions

- Http trigger binding
- Blob input binding

- acr

    ```az
    az acr import \

    --name myreg1 \
    --source mcr.microsoft.com/windows/servercore:latest \

    ```

    The az acr import command imports the image into the ACR and publishes it to myreg1. The image will be available for use in myreg1 after the
    import. The --name parameter identifies the ACR resource name in which the image is published.

### Create Azure app Service Web Apps

- create docker container

  ```az
    az group create --name $resourceGroup --location $location
    az appservice plan create --name $plan \
    --resource-group $resourceGroup --location $location --is-linux --sku S1
    az webapp create --name $appName --plan $plan \
    --resource-group $resourceGroup
    az webapp config container set \
    --docker-custom-image-name $dockerHubContainerPath \
    --name $appName --resource-group myResourceGroup

    ```

    You should create the resource group, App Service Plan, and the App Service Web App using the variable corresponding to each one as the --name
    parameter. This will create all the necessary resources.
    You should use the az webapp config container set command to configure the container image pushed to Docker Hub that will be used by the Web
    App. In this command, you should use the --docker-custom-image-name to reference the container image. To reference an image in Docker Hub
    registry, you should set the dockerHubContainerPath variable with company1/asp-app, using the pattern <organization/username>/<image>.

### Implement Azure Functions

- You should first change your code to use the connection string attribute in the Run method. The attribute with the functions framework is bound with properties set in the configuration section of the function or a value in the local.settings.json file. The framework provides the DbConfigurationType attribute to set the connection string.
- Next, you would need to set those values based on environments in the configuration settings portal user interface, and the file in local.settings.json file. When the code is executed in your machine, it will read the local.settings.json file. When it is run on Azure, the configuration is picked up from the values set in the configuration settings section of the function.

## Develop for Azure Storage

### Develop solutions that uses Cosmos DB storage

- Change feed is enabled bt defualt in Cosmos DB
- Change feed **do not** guarantee order across the partition key values

### Develop solutions that use blob storage

- You should use the **AzCopy** command to copy the blob. This is a command-line tool you can use to move data into and out of storage in Azure. The AzCopy command includes the ability to copy between blob storage accounts. You would specify the source and destination accounts when you run the command. AzCopy copies the blob, but it leaves the original blob in place. When moving a blob, you would delete the source blob after the copy is complete

## Implement Azure security
