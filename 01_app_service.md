# Table of Contents

- [Table of Contents](#table-of-contents)
  - [App Service (PaaS)](#app-service-paas)
    - [App Service plan](#app-service-plan)
    - [Deploy to App Service](#deploy-to-app-service)
    - [Explore authentication and authorization in App Service](#explore-authentication-and-authorization-in-app-service)
    - [Discover App Service networking features](#discover-app-service-networking-features)
    - [Configure application settings](#configure-application-settings)
    - [Enable diagnostic logging](#enable-diagnostic-logging)
    - [Configure security certificates](#configure-security-certificates)
    - [Manage app features](#manage-app-features)
    - [Scale apps in Azure App Service](#scale-apps-in-azure-app-service)
    - [Explore Azure App Service deployment slots](#explore-azure-app-service-deployment-slots)

## App Service (PaaS)

Azure App Service is an HTTP-based service for hosting web applications, REST APIs, and mobile back ends. You can develop in your favorite programming language, be it .NET, .NET Core, Java, Ruby, Node.js, PHP, or Python. Applications run and scale with ease on both Windows and Linux-based environments.

### App Service plan

In App Service, an app (Web Apps, API Apps, or Mobile Apps) always runs in an App Service plan. An App Service plan defines a set of compute resources for a web app to run.

Each App Service plan defines:

- Region (West US, East US, etc.)
- Number of VM instances
- Size of VM instances (Small, Medium, Large)
- Pricing tier (Free, Shared, Basic, Standard, Premium, PremiumV2, PremiumV3, Isolated)
- The consumption tier is only available to function apps. It scales the functions dynamically depending on workload.
-

In the Free and Shared tiers, an app receives CPU minutes on a shared VM instance and can't scale out.

Your App Service plan can be scaled up and down at any time. It is as simple as changing the pricing tier of the plan. If your app is in the same App Service plan with other apps, you may want to improve the app's performance by isolating the compute resources. You can do it by moving the app into a separate App Service plan.

Isolate your app into a new App Service plan when:

- The app is resource-intensive.
- You want to scale the app independently from the other apps in the existing plan.
- The app needs resource in a different geographical region.

### Deploy to App Service

Azure supports automated deployment directly from several sources. The following options are available:

- Automated deployment
  - Azure DevOps
  - GitHub
  - Bitbucket
- Manual deploymeny:
  - Git
  - **CLI: az webapp up can create a new - App Service web app for you if you haven't already created one.**
  - Zip deploy
  - FTP/S

Whenever possible, use deployment slots when deploying a new production build. When using a Standard App Service Plan tier or better, you can deploy your app to a staging environment and then swap your staging and production slots. The swap operation warms up the necessary worker instances to match your production scale, thus eliminating downtime.

### Explore authentication and authorization in App Service

App Service uses federated identity, in which a third-party identity provider manages the user identities and authentication flow for you. The following identity providers are available by default:

| Provider                    | Sign-in endpoint            |
| --------------------------- | --------------------------- |
| Microsoft Identity Platform | /.auth/login/aad            |
| Facebook                    | /.auth/login/facebook       |
| Google                      | /.auth/login/google         |
| Twitter                     | /.auth/login/twitter        |
| Any OpenID Connect provider | /.auth/login/<providerName> |

The authentication flow is the same for all providers, but differs depending on whether you want to sign in with the provider's SDK.

Without provider SDK: The application delegates federated sign-in to App Service. This is typically the case with browser apps, which can present the provider's login page to the user. The server code manages the sign-in process, so it is also called server-directed flow or server flow.

With provider SDK: The application signs users in to the provider manually and then submits the authentication token to App Service for validation. This is typically the case with browser-less apps, which can't present the provider's sign-in page to the user. The application code manages the sign-in process, so it is also called client-directed flow or client flow. This applies to REST APIs, Azure Functions, JavaScript browser clients, and native mobile apps that sign users in using the provider's SDK.

### Discover App Service networking features

By default, apps hosted in App Service are accessible directly through the internet and can reach only internet-hosted endpoints.

There are two main deployment types for Azure App Service. The multitenant public service hosts App Service plans in the Free, Shared, Basic, Standard, Premium, PremiumV2, and PremiumV3 pricing SKUs. There is also the single-tenant App Service Environment (ASE) hosts Isolated SKU App Service plans directly in your Azure virtual network.

The roles that handle incoming HTTP or HTTPS requests are called **front ends**. The roles that host the customer workload are called **workers**. All the roles in an App Service deployment exist in a multi-tenant network. Because there are many different customers in the same App Service scale unit, you can't connect the App Service network directly to your network.

Azure App Service scale units support many customers in each deployment. The Free and Shared SKU plans host customer workloads on multitenant workers. The Basic and higher plans host customer workloads that are dedicated to only one App Service plan. If you have a Standard App Service plan, all the apps in that plan will run on the same worker. If you scale out the worker, all the apps in that App Service plan will be replicated on a new worker for each instance in your App Service plan.

To find the outbound IP addresses currently used by your app in the Azure portal, click Properties in your app's left-hand navigation.
You can find the same information by running the following command in the Cloud Shell. They are listed in the Additional Outbound IP Addresses field.

```az
az webapp show \
    --resource-group <group_name> \
    --name <app_name> \ 
    --query outboundIpAddresses | possibleOutboundIpAddresses  \
    --output tsv
```

### Configure application settings

- For Linux apps and custom containers, App Service passes app settings to the container using the --env flag to set the environment variable in the container.
- Application settings can be accessed by navigating to your app's management page and selecting Configuration > Application Settings.
  - If you are using deployment slots you can specify if your setting is swappable or not.
- For ASP.NET and ASP.NET Core developers, setting app settings in App Service are like setting them in <appSettings> in Web.config or appsettings.json, but the values in App Service override the ones in Web.config or appsettings.json.
  - f you are using deployment slots you can specify if your setting is swappable or not.
  - You can keep development settings (for example, local MySQL password) in Web.config or appsettings.json, but production secrets (for example, Azure MySQL database password) safe in App Service.
- Connection strings are always encrypted when stored (encrypted-at-rest).
  - If you are using deployment slots you can specify if your setting is swappable or not.

- In the Configuration > General settings:
  - Stack settings
  - Platform settings
    - Always On: Keep the app loaded even when there's no traffic. By default, Always On is not enabled and the app is unloaded after 20 minutes without any incoming requests. It's required for continuous WebJobs or for WebJobs that are triggered using a CRON expression.
    - Debugging: Enable remote debugging for ASP.NET, ASP.NET Core, or Node.js apps. This option turns off automatically after 48 hours

- In the Configuration > Path mappings section you can configure handler mappings, and virtual application and directory mappings.
  - Windows apps (uncontainerized)
    - Each app has the default root path (/) mapped to D:\home\site\wwwroot, where your code is deployed by default. If your app root is in a different folder, or if your repository has more than one application, you can edit or add virtual applications and directories.
    - To mark a virtual directory as a web application, clear the Directory check box.
  - Linux and containerized apps
    - You can add custom storage for your containerized app.

### Enable diagnostic logging

[diagnostic logging table](./app_service_diagnostic_logging.md)

- Enable application logging (Windows):
    1. Select On for either Application Logging (**Filesystem**) or Application Logging (Blob), or both.
       - The **Filesystem** option is for **temporary** debugging purposes, and **turns itself off in 12 hours**.
       - The **Blob** option is for**long-term logging**, and needs a blob storage container to write logs to.
    2. set the Level of details (DEBUG, ERROR, etc)
    3. Save
- Enable application logging (Linux/Container)
    1. In App Service logs set the Application logging option to File System.
    2. In Quota (MB), specify the disk quota for the application logs. In Retention Period (Days), set the number of days the logs should be retained.
    3. When finished, select Save.

- Enable web server logging
    1. For Web server logging, select Storage to store logs on blob storage, or File System to store logs on the App Service file system.
    2. In Retention Period (Days), set the number of days the logs should be retained.
    3. When finished, select Save.

- Stream logs
  Before you stream logs in real time, enable the log type that you want. Any information written to files ending in .txt, .log, or .htm that are stored in the /LogFiles directory (d:/home/logfiles) is streamed by App Service.
  - Azure portal - To stream logs in the Azure portal, navigate to your app and select Log stream.
  - Azure CLI - To stream logs live in Cloud Shell, use the following command:

    ```az
      az webapp log tail --name appname --resource-group myResourceGroup
    ```

  - Local console - To stream logs in the local console, install Azure CLI and sign in to your account. Once signed in, follow the instructions for Azure CLI above.
  
- Access log files
  If you configure the Azure Storage blobs option for a log type, you need a client tool that works with Azure Storage.

  For logs stored in the App Service file system, the easiest way is to download the ZIP file in the browser at:

  Linux/container apps: https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip
  Windows apps: https://<app-name>.scm.azurewebsites.net/api/dump
- In the App Service file system, these log files are the contents of the /home/LogFiles directory.

### Configure security certificates

A certificate uploaded into an app is stored in a deployment unit that is bound to the app service plan's resource group and region combination (internally called a webspace). This makes the certificate accessible to other apps in the same resource group and region combination.

### Manage app features

Here are several new terms related to feature management:

- Feature flag: A feature flag is a variable with a binary state of on or off. The feature flag also has an associated code block. The state of the feature flag triggers whether the code block runs or not.
- Feature manager: A feature manager is an application package that handles the lifecycle of all the feature flags in an application. The feature manager typically provides additional functionality, such as caching feature flags and updating their states.
- Filter: A filter is a rule for evaluating the state of a feature flag. A user group, a device or browser type, a geographic location, and a time window are all examples of what a filter can represent.
An effective implementation of feature management consists of at least two components working in concert:

- An application that makes use of feature flags.
- A separate repository that stores the feature flags and their current states.

The feature manager supports appsettings.json as a configuration source for feature flags. The following example shows how to set up feature flags in a JSON file:

```json
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

To use feature flags effectively, you need to externalize all the feature flags used in an application. This approach allows you to change feature flag states without modifying and redeploying the application itself.

Azure App Configuration is designed to be a centralized repository for feature flags.

### Scale apps in Azure App Service

All App Service Plan pricing tiers support autoscaling. By default, an App Service Plan only implements manual scaling. Selecting Custom autoscale reveals condition groups you can use to manage your scale settings. Not all pricing tiers support autoscaling. The development pricing tiers are either limited to a single instance (the F1 and D1 tiers), or they only provide manual scaling (the B1 tier). If you've selected one of these tiers, you must first scale up to the S1 or any of the P level production tiers.

Autoscaling is a cloud system or process that adjusts available resources based on the current demand. Autoscaling performs scaling in and out, as opposed to scaling up and down.

Autoscaling doesn't have any effect on the CPU power, memory, or storage capacity of the web servers powering the app, it only changes the number of web servers.

Autoscaling isn't the best approach to handling long-term growth. Autoscaling has an overhead associated with monitoring resources and determining whether to trigger a scaling event.

- Autoscaling by metric requires that you define one or more autoscale rules. An autoscale rule specifies a metric to monitor, and how autoscaling should respond when this metric crosses a defined threshold. The metrics you can monitor for a web app are:

  - CPU Percentage. This metric is an indication of the CPU utilization across all instances. A high value shows that instances are becoming CPU-bound, which could cause delays in processing client requests.
  - Memory Percentage. This metric captures the memory occupancy of the application across all instances. A high value indicates that free memory could be running low, and could cause one or more instances to fail.
  - Disk Queue Length. This metric is a measure of the number of outstanding I/O requests across all instances. A high value means that disk contention could be occurring.
  - Http Queue Length. This metric shows how many client requests are waiting for processing by the web app. If this number is large, client requests might fail with HTTP 408 (Timeout) errors.
  - Data In. This metric is the number of bytes received across all instances.
  - Data Out. This metric is the number of bytes sent by all instances.

Autoscaling works by analyzing trends in metric values over time across all instances. Analysis is a multi-step process.

In the first step, an autoscale rule aggregates the values retrieved for a metric for all instances across a period of time known as the time grain. Each metric has its own intrinsic time grain, but in most cases this period is 1 minute. The aggregated value is known as the time aggregation. The options available are Average, Minimum, Maximum, Total, Last, and Count.

An autoscale rule performs a second step that performs a further aggregation of the value calculated by the time aggregation over a longer, user-specified period, known as the Duration. The minimum Duration is 5 minutes. If the Duration is set to 10 minutes for example, the autoscale rule will aggregate the 10 values calculated for the time grain.

When an autoscale rule detects that a metric has crossed a threshold, it can perform an autoscale action. An autoscale action can be scale-out or scale-in. An autoscale action has a cool down period, specified in minutes. During this interval, the scale rule won't be triggered again.
Scale-out operations will trigger if any of the rule conditions are met.

You can use the Run history chart in conjunction with the metrics shown on the Overview page to correlate the autoscaling events with resource utilization.

### Explore Azure App Service deployment slots

In Azure App Service, you can use a separate deployment slot instead of the default production slot when you're running in the Standard, Premium, or Isolated App Service plan tier. Deployment slots are live apps with their own host names. App content and configurations elements can be swapped between two deployment slots, including the production slot.

To scale your app to a different tier, make sure that the target tier supports the number of slots your app already uses. For example, if your app has more than five slots, you can't scale it down to the Standard tier, because the Standard tier supports only five deployment slots.
