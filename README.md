#### observability:
+ It’s the ability to understand what’s happening inside your systems (apps, servers, containers, clusters) by looking at the signals they produce — like **logs, metrics, and traces.**
  + **Monitoring(Metrics):** involves tracking system metrics like CPU usage, memory usage, and network performance. Provides alerts based on predefined thresholds and conditions. `Monitoring tells us what is happening`
  + **Logs:** involves the collection of log data from various components of a system. `Logging explains why it is happening`
    + Logs are records of discrete events that have occurred within a system. Each log entry typically includes details such as the timestamp of the event, the source of the log message, and a description of the event.
  + **Traces:** involves tracking the flow of a request or transaction as it moves through different services and components within a system.
    + `Tracing shows how it is happening.`
   + For example in a car, You don’t see the engine directly, but you look at the dashboard → speedometer, fuel gauge, engine light.
  + From those signals, you understand what’s happening inside the car without opening the hood.
    + Monitoring has **metrics**, display on the **dashboard** and send the alerts using **alert manager** based on the rules
    + observability allows you to understand why a system is behaving the way it is.
+ **Without observability, when something breaks:**
  + You only know “app is down” but not why.
  + You spend hours guessing — is it CPU? memory? DB connection? network latency?
+ **With observability:**
  + You can quickly detect, debug, and fix issues.
  + You can even predict problems before users notice (e.g., memory leak, disk filling).
  + It helps ensure reliability, performance, and availability.
+ **Monitoring tells you “something is wrong”.**
+ **Observability tells you “why it went wrong”.**
+ **Metrics:**
  + Metrics are numerical data that measure various aspects of system performance and health over time. They are typically used to monitor systems continuously  
  + Types of metrics include:
  + **Host metrics:** Memory, disk and CPU usage
  + **Network performance metrics:** uptime, latency, throughput
  + **App metrics:** response times, request and error rates
  + **Server pool metrics:** total instances, number of running instances
  + **External dependency metrics:** availability, service status
    
![image](https://github.com/Dinakar-Dasari/private/blob/806e49da06d1311ff25f77348720022dc29d53d1/images/prometheus-architecture-diagram.jpg)

+ **Prometheus:**
  + Prometheus is an open-source monitoring and observability toolkit for collecting, storing, and analyzing metrics from applications and infrastructure
  + It can be configured and set up on both bare-metal servers and container environments like Kubernetes.
+ **Architecture:**
  + It consists of several core components, each responsible for a specific aspect of the monitoring process.
  + **Prometheus server:**
    + The Prometheus server is the brain of the metric-based monitoring system. The main job of the server is to collect the metrics from various targets using pull model.
    + The general term for collecting metrics from the targets using Prometheus is called **scraping**
    + It scrapes metrics from designated sources (targets) at regular intervals, stores them in the built-in time series database (TSDB), and makes them available for querying and alerting.
    + Prometheus periodically scrapes the metrics, based on the scrape interval that we mention in the Prometheus configuration file.
    + It consists of,
    + **Data Retrieval Worker:**
      + This component is responsible for scraping metrics data from targets. It sends HTTP requests to the designated endpoints—typically at /metrics—to retrieve data, which is then forwarded for storage.
    + **Time Series Database**
      + Collected metrics are stored in a time series database. This database maintains all numerical data, allowing for efficient retrieval and analysis over time.
    + **HTTP Endpoint:**
      + The built-in HTTP endpoint serves dual purposes:
        + It allows users to execute queries using PromQL
        + It supports visualization and further analysis through tools like the Prometheus web UI and Grafana.
    + Prometheus scrapes metrics by sending requests to the `/metrics` endpoint of each target.
    + Some targets might not expose the /metric path for those to collect metrics we use exporters
    + **Exporters:**
      + Exporters in Prometheus are used when a target (like a database, hardware device, network equipment, or certain programs) cannot natively expose metrics via the /metrics HTTP endpoint that Prometheus expects to scrape.
      + **Exporters act as intermediaries:** they collect data from such systems in their original format, convert it into Prometheus-compatible metrics, and then expose those metrics on their own /metrics endpoint, which Prometheus can scrape.
        + The **Node Exporter** exposes metrics from the operating system.
        + **MySQL Exporter** (for database metrics)
        + **kube-state-metrics** is used for Kubernetes that exposes detailed metrics about the state of various Kubernetes resources,
          + It generates metrics about the "state" of cluster objects like Deployments, Pods, Services, Nodes, persistent volumes, namespaces, and more.
      + **instrumentation:**
        + Instrumentation means adding code or libraries into your application so it can emit telemetry data (metrics, logs, and traces).
        + This is what makes your application “observable” — without instrumentation, Prometheus, Grafana, or Jaeger won’t have anything to collect.
        + Prometheus Metric Types:
          + **Counter**:
            + Only increases (or resets to 0).
            + Use for counting events like total requests, errors, jobs completed. 
          + **Gauge**:
            + Can go up and down.
            + Use for values that fluctuate like CPU usage, memory consumption, temperature, concurrent users. 
          + **Histogram**:
            + Observes values and puts them into buckets.
            + Useful for request duration or payload size. 
          + **Summary**: Similar to a Histogram, a Summary samples observations and provides a total count of observations, their sum, and configurable quantiles (percentiles).
        + These metrics are usually exposed via an HTTP endpoint (e.g., /metrics) in a format Prometheus can scrape
        + **How it works:**
          + Developers use vendor-specific **APIs & SDKs** (for example, Prometheus client libraries, OpenTelemetry SDK, Datadog agent SDK, etc.).
          + This code exposes telemetry data:
            + Metrics → /metrics endpoint for Prometheus to scrape (e.g., request counts, error rates).
            + Logs → structured logs sent to systems like ELK/Fluentd/CloudWatch.
            + Traces → span/context data sent to tracing backends like Jaeger or Tempo.
        + **Why is OpenTelemetry preferred over vendor-specific SDKs for instrumentation?”**
          +  **Problem with vendor-specific SDKs**
            +  If developers instrument code with vendor-specific libraries (e.g., Datadog, New Relic, Prometheus clients), the telemetry is tightly coupled to that vendor.
            +  Later, if the company wants to switch from Grafana → Jaeger → Datadog, all instrumentation code must be rewritten.
            +  This is time-consuming, error-prone, and locks you into one vendor.
          + **How OpenTelemetry solves this**
            + OpenTelemetry (OTel) is a CNCF standard that provides vendor-neutral APIs and SDKs for metrics, logs, and traces.
            + Developers instrument once with OTel, and then telemetry data can be exported to any backend (Prometheus, Grafana, Jaeger, Datadog, etc.).
            + OTel pipeline works in 3 stages:
              + **Receiver** – Collects data (e.g., metrics from app).
              + **Processor** – Processes/aggregates data (e.g., batching, filtering).
              + **Exporter** – Sends data to chosen observability backend (configured in YAML/JSON, no code changes needed).
