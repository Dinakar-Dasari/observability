**Explain how you can obtain central logs from any pod using Loki, Prometheus, and Grafana.**
+ **Pod-level metrics:**
  + Prometheus scrapes metrics exposed by each pod (usually via /metrics endpoint).
  + Metrics can include CPU, memory, request counts, errors, latency, etc.
  + Prometheus stores these metrics centrally and allows querying with PromQL.
  + You can filter by namespace, pod name, container name, etc.
  + This gives visibility into how each application container is performing.
+ **Node-level metrics:**
  + Nodes expose metrics via kubelet’s /metrics/cadvisor endpoint.
  + Metrics include:
    + CPU capacity and usage per node
    + Memory capacity and usage per node
    + Pod count per node
    + Disk usage and network I/O for the node
  + Prometheus scrapes these and stores them centrally, so you can monitor cluster-wide resource usage.
  + How it connects in Grafana:
    + Grafana dashboards can combine pod metrics and node metrics in the same view.
    + Example dashboards:
      + Pod CPU/memory usage per namespace
      + Node resource utilization and which pods are consuming resources
      + Alerts if pod or node exceeds thresholds
  + “Prometheus scrapes metrics both at the pod level and node level. Pod-level metrics give visibility into each container’s CPU, memory, network, and restart behavior. Node-level metrics provide resource usage for the entire node, including CPU, memory, disk, and network. Together, this allows centralized monitoring of both application and infrastructure resources, which we visualize in Grafana dashboards.” 
