# Table of Contents

- [Table of Contents](#table-of-contents)
  - [Explore Azure Functions](#explore-azure-functions)
    - [Compare Azure Functions hosting options](#compare-azure-functions-hosting-options)
    - [Scale Azure Functions](#scale-azure-functions)
      - [Runtime scaling](#runtime-scaling)
      - [Azure Functions scaling in an App service plan](#azure-functions-scaling-in-an-app-service-plan)
    - [Explore Azure Functions development](#explore-azure-functions-development)
      - [Function app](#function-app)
      - [Create triggers and bindings](#create-triggers-and-bindings)
    - [Durable functions](#durable-functions)
      - [Application patterns](#application-patterns)
      - [Discover the four function types](#discover-the-four-function-types)
      - [Explore task hubs](#explore-task-hubs)
      - [Explore durable orchestrations](#explore-durable-orchestrations)
      - [Control timing in Durable Functions](#control-timing-in-durable-functions)

## Explore Azure Functions

Azure Functions are a great solution for processing data, integrating systems, working with the internet-of-things (IoT), and building simple APIs and microservices. Consider Functions for tasks like image or order processing, file maintenance, or for any tasks that you want to run on a schedule

Azure Functions supports triggers, which are ways to start execution of your code, and bindings, which are ways to simplify coding for input and output data

- Compare Azure Functions and Azure Logic Apps
  - Both Functions and Logic Apps enable serverless workloads. Azure Functions is a serverless compute service, whereas Azure Logic Apps provides serverless workflows. Both can create complex orchestrations. An orchestration is a collection of functions or steps, called actions in Logic Apps, that are executed to accomplish a complex task.
  - For Azure Functions, you develop orchestrations by writing code and using the Durable Functions extension. For Logic Apps, you create orchestrations by using a GUI or editing configuration files.
  
    |                   | Azure Functions                                                       | Logic Apps                                                                                             |
    | ----------------- | --------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
    | Development       | Code-first (imperative)                                               | Designer-first (declarative)                                                                           |
    | Connectivity      | About a dozen built-in binding types, write code for custom bindings  | Large collection of connectors, Enterprise Integration Pack for B2B scenarios, build custom connectors |
    | Actions           | Each activity is an Azure function; write code for activity functions | Large collection of ready-made actions                                                                 |
    | Monitoring        | Azure Application Insights                                            | Azure portal, Azure Monitor logs                                                                       |
    | Management        | REST API, Visual Studio                                               | Azure portal, REST API, PowerShell, Visual Studio                                                      |
    | Execution context | Can run locally or in the cloud                                       | Supports run-anywhere scenarios                                                                        |

- Compare Functions and WebJobs
  - Azure Functions is built on the WebJobs SDK, so it shares many of the same event triggers and connections to other Azure services.
  - Azure Functions offers more developer productivity than Azure App Service WebJobs does. It also offers more options for programming languages, development environments, Azure service integration, and pricing. For most scenarios, it's the best choice.
    |                                             | Functions                                                                                                                                       | WebJobs with WebJobs SDK                                                                                         |
    | ------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
    | Serverless app model with automatic scaling | Yes                                                                                                                                             | No                                                                                                               |
    | Develop and test in browser                 | Yes                                                                                                                                             | No                                                                                                               |
    | Pay-per-use pricing                         | Yes                                                                                                                                             | No                                                                                                               |
    | Integration with Logic Apps                 | Yes                                                                                                                                             | No                                                                                                               |
    | Trigger events                              | TimerAzure Storage queues and blobsAzure Service Bus queues and topicsAzure Cosmos DBAzure Event HubsHTTP/WebHook (GitHubSlack)Azure Event Grid | TimerAzure Storage queues and blobsAzure Service Bus queues and topicsAzure Cosmos DBAzure Event HubsFile system |

### Compare Azure Functions hosting options

When you create a function app in Azure, you must choose a hosting plan for your app. There are three basic hosting plans available for Azure Functions: Consumption plan, Functions Premium plan, and App service (Dedicated) plan.

| Plan             | Benefits                                                                                                                                                                                                                                    |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Consumption plan | This is the default hosting plan. It scales automatically and you only pay for compute resources when your functions are running. Instances of the Functions host are dynamically added and removed based on the number of incoming events. |
| Premium plan     | Automatically scales based on demand using pre-warmed workers, which run applications with no delay after being idle, runs on more powerful instances, and connects to virtual networks.                                                    |
| Dedicated plan   | Run your functions within an App Service plan at regular App Service plan rates. Best for long-running scenarios where Durable Functions can't be used.                                                                                     |

- App service plans support setting autoscaling rules based on predictive usage so it is best when predictive scaling and costs are required
- If you run on a Dedicated plan, you should enable the Always on setting so that your function app runs correctly. On an App Service plan, the functions runtime goes idle after a few minutes of inactivity, so only HTTP triggers will "wake up" your functions. Always on is available only on an App Service plan. On a Consumption plan, the platform activates function apps automatically.

- On any plan, a function app requires a general Azure Storage account, which supports Azure Blob, Queue, Files, and Table storage.

There are two other hosting options, which provide the highest amount of control and isolation in which to run your function apps.
| Hosting option | Details                                                                                                                                                                       |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ASE            | App Service Environment (ASE) is an App Service feature that provides a fully isolated and dedicated environment for securely running App Service apps at high scale.         |
| Kubernetes     | Kubernetes provides a fully isolated and dedicated environment running on top of the Kubernetes platform. For more information visit Azure Functions on Kubernetes with KEDA. |

### Scale Azure Functions

In the Consumption and Premium plans, Azure Functions scales CPU and memory resources by adding additional instances of the Functions host.
Each instance of the Functions host in the Consumption plan is limited to 1.5 GB of memory and one CPU.

#### Runtime scaling

Azure Functions uses a component called the scale controller to monitor the rate of events and determine whether to scale out or scale in.

Scaling can vary on a number of factors, and scale differently based on the trigger and language selected. There are a few intricacies of scaling behaviors to be aware of:

- Maximum instances: A single function app only scales out to a maximum of 200 instances. A single instance may process more than one message or request at a time though, so there isn't a set limit on number of concurrent executions.

- New instance rate: For HTTP triggers, new instances are allocated, at most, once per second. For non-HTTP triggers, new instances are allocated, at most, once every 30 seconds.

By default, Consumption plan functions scale out to as many as 200 instances, and Premium plan functions will scale out to as many as 100 instances. You can specify a lower maximum for a specific app by modifying the functionAppScaleLimit value. The functionAppScaleLimit can be set to 0 or null for unrestricted, or a valid value between 1 and the app maximum.

#### Azure Functions scaling in an App service plan

Using an App Service plan, you can manually scale out by adding more VM instances. You can also enable autoscale, though autoscale will be slower than the elastic scale of the Premium plan.

### Explore Azure Functions development

A function contains two important pieces - your code, which can be written in a variety of languages, and some config, the function.json file. For compiled languages, this config file is generated automatically from annotations in your code. For scripting languages, you must provide the config file yourself.

#### Function app

A function app provides an execution context in Azure in which your functions run. As such, it is the unit of deployment and management for your functions. A function app is comprised of one or more individual functions that are managed, deployed, and scaled together. All of the functions in a function app share the same pricing plan, deployment method, and runtime version.

#### Create triggers and bindings

Triggers are what cause a function to run. A trigger defines how a function is invoked and a function must have exactly one trigger. Triggers have associated data, which is often provided as the payload of the function.
A trigger defines how a function is invoked and a function must have exactly one trigger.

Binding to a function is a way of declaratively connecting another resource to the function; bindings may be connected as input bindings, output bindings, or both. Data from bindings is provided to the function as parameters.

Triggers and bindings are defined differently depending on the development language.

| Language                                | Triggers and bindings are configured by...              |
|-----------------------------------------|---------------------------------------------------------|
| C# class library                        | decorating methods and parameters with C# attributes    |
| Java                                    | decorating methods and parameters with Java annotations |
| JavaScript/PowerShell/Python/TypeScript | updating function.json schema                           |

### Durable functions

Durable Functions is an extension of Azure Functions that lets you write stateful functions in a serverless compute environment.

The durable functions extension lets you define stateful workflows by writing orchestrator functions and stateful entities by writing entity functions using the Azure Functions programming model. Behind the scenes, the extension manages state, checkpoints, and restarts for you, allowing you to focus on your business logic.

#### Application patterns

The primary use case for Durable Functions is simplifying complex, stateful coordination requirements in serverless applications. The following sections describe typical application patterns that can benefit from Durable Functions:

- Function chaining:
  - a sequence of functions executes in a specific order. In this pattern, the output of one function is applied to the input of another function
- Fan-out/fan-in:
  - you execute multiple functions in parallel and then wait for all functions to finish
- Async HTTP APIs:
  - addresses the problem of coordinating the state of long-running operations with external clients. A common way to implement this pattern is by having an HTTP endpoint trigger the long-running action.
- Monitor:
  - refers to a flexible, recurring process in a workflow. An example is polling until specific conditions are met.
- Human interaction:
  - The orchestrator uses a durable timer to request approval. The orchestrator escalates if timeout occurs. The orchestrator waits for an external event, such as a notification that's generated by a human interaction.

#### Discover the four function types

There are currently four durable function types in Azure Functions: orchestrator, activity, entity, and client. The rest of this section goes into more details about the types of functions involved in an orchestration.

- Orchestrator functions:
  - Orchestrator functions describe how actions are executed and the order in which actions are executed.
  - orchestrator functions must be deterministic: an orchestrator function will be replayed multiple times, and it must produce the same result each time.
  - An orchestrator function must not use any bindings, including even the orchestration client and entity client bindings. Always use input and output bindings from within a client or activity function. This is important because orchestrator functions may be replayed multiple times, causing nondeterministic and duplicate I/O with external systems.

- Activity functions:
  - Activity functions are the basic unit of work in a durable function orchestration.
  - These activity functions may be executed serially, in parallel, or some combination of both.

- Entity functions:
  - Entity functions define operations for reading and updating small pieces of state.
  - entity functions are functions with a special trigger type, entity trigger. They can also be invoked from client functions or from orchestrator functions
  - do not have any specific code constraints.
  - Entities are accessed via a unique identifier, the entity ID. An entity ID is simply a pair of strings that uniquely identifies an entity instance.
  - Operations on entities require that you specify the Entity ID of the target entity, and the Operation name, which is a string that specifies the operation to perform.

- Client functions:
  - What makes a function a client function is its use of the durable client output binding.
  - Any non-orchestrator function can be a client function.
  - If you want to test an orchestrator or entity function in the Azure portal, you must instead run a client function that starts an orchestrator or entity function as part of its implementation. For the simplest testing experience, a manual trigger function is recommended.

#### Explore task hubs

A task hub in Durable Functions is a logical container for durable storage resources that are used for orchestrations and entities. Orchestrator, activity, and entity functions can only directly interact with each other when they belong to the same task hub.
Task hubs in Azure Storage are identified by a name that conforms to these rules:

- Contains only alphanumeric characters
- Starts with a letter
- Has a minimum length of 3 characters, maximum length of 45 characters

The task hub name is declared in the host.json

The name is what differentiates one task hub from another when there are multiple task hubs in a shared storage account. If you have multiple function apps sharing a shared storage account, you must explicitly configure different names for each task hub in the host.json files. Otherwise the multiple function apps will compete with each other for messages, which could result in undefined behavior, including orchestrations getting unexpectedly "stuck" in the Pending or Running state.

#### Explore durable orchestrations

You can use an orchestrator function to orchestrate the execution of other Durable functions within a function app. Orchestrator functions have the following characteristics:

- Orchestrator functions define function workflows using procedural code. No declarative schemas or designers are needed.
- Orchestrator functions can call other durable functions synchronously and asynchronously. Output from called functions can be reliably saved to local variables.
- Orchestrator functions are durable and reliable. Execution progress is automatically checkpointed when the function "awaits" or "yields". Local state is never lost when the process recycles or the VM reboots.
- Orchestrator functions can be long-running. The total lifespan of an orchestration instance can be seconds, days, months, or never-ending.

- Orchestration identity
  Each instance of an orchestration has an unique instance identifier (also known as an instance ID) iwthing a task hub. By default, each instance ID is an autogenerated GUID. However, instance IDs can also be any user-generated string value.
  it is recommended to save generated instance IDs to some external location (for example, a database or in application logs) where they can be easily referenced later.

- Reliability
  Orchestrator functions reliably maintain their execution state by using the event sourcing design pattern. The Durable Task Framework uses an append-only store to record the full series of actions the function orchestration takes.

#### Control timing in Durable Functions

Durable Functions provides durable timers for use in orchestrator functions to implement delays or to set up timeouts on async actions.

- When you create a timer that expires at 4:30 pm, the underlying Durable Task Framework enqueues a message that becomes visible only at 4:30 pm.

Orchestrator functions have the ability to wait and listen for external events. This feature of Durable Functions is often useful for handling human interaction or other external triggers.
The listening orchestrator function declares the name of the event and the shape of the data it expects to receive.
