# Instrument solutions to support monitoring and logging - Table of Contents

- [Instrument solutions to support monitoring and logging - Table of Contents](#instrument-solutions-to-support-monitoring-and-logging---table-of-contents)
  - [Explore Azure Monitor](#explore-azure-monitor)
  - [Explore Application Insights](#explore-application-insights)
    - [What Application Insights monitors](#what-application-insights-monitors)
    - [Discover log-based metrics](#discover-log-based-metrics)
      - [Log-based metrics](#log-based-metrics)
      - [Pre-aggregated metrics](#pre-aggregated-metrics)
    - [How to activate Application Insights](#how-to-activate-application-insights)
      - [Auto-instrumentation](#auto-instrumentation)
      - [Enabling via Application Insights SDKs](#enabling-via-application-insights-sdks)
    - [Select an availability test](#select-an-availability-test)
    - [Troubleshoot app performance by using Application Map](#troubleshoot-app-performance-by-using-application-map)

## Explore Azure Monitor

Azure Monitor helps you maximize the availability and performance of your applications and services.

Metrics, logs, and distributed traces are commonly referred to as the three pillars of observability.

| Pillar             | Description                                                                                                                                                                                                                     |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Metrics            | Metrics are numerical values that describe some aspect of a system at a particular point in time. They're collected at regular intervals and are identified with a timestamp, a name, a value, and one or more defining labels. |
| Logs               | Logs are events that occurred within the system. They can contain different kinds of data and may be structured or free-form text with a timestamp.                                                                             |
| Distributed traces | Traces are series of related events that follow a user request through a distributed system.                                                                                                                                    |
| Changes            | Changes are a series of events that occur in your Azure application and resources. Change Analysis tracks changes and is a subscription-level observability tool that's built on the power of Azure Resource Graph.             |

## Explore Application Insights

Application Insights is an extension of Azure Monitor and provides Application Performance Monitoring (also known as “APM”) features.

| Feature                            | Description                                                                                                                                 |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| Live Metrics                       | Observe activity from your deployed application in real time with no effect on the host environment.                                        |
| Availability                       | Probe your applications external endpoint(s) to test the overall availability and responsiveness over time.                                 |
| GitHub or Azure DevOps integration | Create GitHub or Azure DevOps work items in context of Application Insights data.                                                           |
| Usage                              | Reveals which features are popular with users and how users interact and use your application.                                              |
| Smart Detection                    | Provides automatic failure and anomaly detection through proactive telemetry analysis.                                                      |
| Application Map                    | Allows a high level top-down view of the application architecture and at-a-glance visual references to component health and responsiveness. |
| Distributed                        | Tracing Search and visualize an end-to-end flow of a given execution or transaction.                                                        |

### What Application Insights monitors

Application Insights collects Metrics and application Telemetry data, which describe application activities and health, as well as trace logging data.

### Discover log-based metrics

Application Insights log-based metrics let you analyze the health of your monitored apps, create powerful dashboards, and configure alerts. There are two kinds of metrics:

- Log-based metrics behind the scene are translated into Kusto queries from stored events.
- Standard metrics are stored as pre-aggregated time series.

#### Log-based metrics

Developers can use the SDK to either emit these events manually (by writing code that explicitly invokes the SDK) or they can rely on the automatic collection of events from auto-instrumentation. In either case, the Application Insights backend stores all collected events as logs, and the Application Insights blades in the Azure portal act as an analytical and diagnostic tool for visualizing event-based data from logs.

#### Pre-aggregated metrics

The pre-aggregated metrics are not stored as individual events with lots of properties. Instead, they are stored as pre-aggregated time series, and only with key dimensions. This makes the new metrics superior at query time: retrieving data happens much faster and requires less compute power. This consequently enables new scenarios such as near real-time alerting on dimensions of metrics, more responsive dashboards, and more.

### How to activate Application Insights

Application Insights is enabled through either Auto-Instrumentation (agent) or by adding the Application Insights SDK to your application code.

#### Auto-instrumentation

Auto-instrumentation is the preferred instrumentation method. It requires no developer investment and eliminates future overhead related to updating the SDK. It's also the only way to instrument an application in which you don't have access to the source code.

#### Enabling via Application Insights SDKs

You only need to install the Application Insights SDK in the following circumstances:

- You require custom events and metrics
- You require control over the flow of telemetry
- Auto-Instrumentation isn't available (typically due to language or platform limitations)

The Application Insights SDKs for .NET, .NET Core, Java, Node.js, and JavaScript all support distributed tracing natively.
Additionally, any technology can be tracked manually with a call to TrackDependency on the TelemetryClient.

### Select an availability test

After you've deployed your web app or website, you can set up recurring tests to monitor availability and responsiveness. Application Insights sends web requests to your application at regular intervals from points around the world. It can alert you if your application isn't responding or responds too slowly.

### Troubleshoot app performance by using Application Map

Application Map helps you spot performance bottlenecks or failure hotspots across all components of your distributed application. Each node on the map represents an application component or its dependencies; and has health KPI and alerts status.
