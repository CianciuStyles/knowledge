# Prometheus

## The Complete Hands-On for Monitoring and Alerting - Notes

Link to course: [https://www.udemy.com/course/prometheus-course/](https://www.udemy.com/course/prometheus-course/)

### Introduction

* Prometheus is an open-source monitoring and alerting toolkit
* It collects metrics by scraping HTTP endpoints on the target application
* By using Prometheus, you can understand and analyze how your application is performing
* Mostly written in Go
* It uses a multi-dimensional data model with time series
* Example of a metric: `http_requests_total{method="get"}`
  * `http_requests_total` is the _metric name_
  * `method` is the _key_
  * `get` is the _value_
* To read the data from the time-series DB, Prometheus uses a read-only language called PromQL
* Works on a single node rather than a distributed system
* Includes Alertmanager for alerting

### Alternatives to Prometheus

* Graphite
  * Merely storage and graphing framework
  * Separate component (Carbon) that passively listens for data
* InfluxDB
  * Separate component (Kapacitor) for alerting
* OpenTSDB
* Nagios
* Sensu

### Basic terminology

* Monitoring: process of collecting and recording activities, to check whether the target achieves its objectives or not
* Alert: outcome of an alerting rule that is actively firing
* Target: object whose metrics are to be monitored
* Instance: endpoint you can scrape (usually)
* Job: collection of instances with the same purpose
* Sample: single value (64-bit float) of a time series

### Architecture

![Prometheus architecture](<../.gitbook/assets/Prometheus Architecture.png>)

* Prometheus Server:
  * Retrieval: scrapes data from target
  * TSDB / Storage: HDD/SDD to store collected values
  * HTTP Server: exposes data from the DB to clients (e.g., Grafana)
* Push gateway: allows short-lived jobs to push metrics to Prometheus rather than Prometheus pulling metrics from them
* Service discovery: makes Prometheus aware of all the targets to monitor and pull metrics from
* Prometheus Web UI: request and graph raw data using PromQL
* Grafana / API clients
* Alertmanager: receives and groups alerts coming from Prometheus, and relays them to PagerDuty / Slack / emails

### Prometheus Life-Cycle

* Identify where targets reside
* Pull metrics from target with HTTP request
* Data is stored in the TSDB
* Data can be fetched from clients via the HTTP server
* If alerts are firing, alerts are pushed to Alertmanager

### Exporters

* They are used when direct instrumentation of the target is not feasible
* Node Exporter: exposes kernel-level and machine-level metrics (e.g., CPU, memory, disk space) for Unix systems
* WMI Exporter: exposes kernel-level and machine-level metrics for Windows systems

### Data Types in PromQL

* Instant vector: a set of single samples per time series, all sharing the same timestamp (e.g., `prometheus_http_requests_total`)
* Range vector: a set of ranges of data points over time for each time series (e.g., `prometheus_http_requests_total[1m]`)
* Scalar: a simple numeric floating point number
* String: a simple string value (currently unused)

### Selectors & Matchers

* Matcher: filtering condition(s) that allow to consider some metrics and ignore others (e.g., in the expression `process_cpu_seconds_total{job='node_exporter'}`, the `{job='node_exporter'}` is a matcher, because it will filter out all the `process_cpu_seconds_total` metrics for different jobs)
* Specifying multiple matchers in a selector will AND them together (i.e., only metrics which satisfy all filters will be returned)
* A PromQL expression can be associated to a SQL statement
* Matcher types:
  * `=` (equality matcher)
  * `!=` (negative equality matcher)
  * `=~` (regular expression matcher)
  * `!~` (negative regular expression matcher)

### Binary operators

Take two operands and perform the specified calculations

* Arithmetic:
  * addition +
  * subtraction -
  * multiplication \*
  * division /
  * modulo %
  * exponentiation ^
  * are defined for scalar/scalar, vector/scalar, and vector/vector
* Comparison:
  * equal ==
  * not equal !=
  * greater than >
  * less than <
  * greater or equal >=
  * less or equal <=
  * are defined between scalar/scalar, vector/scalar, and vector/vector value pairs
* Logical / set:&#x20;
  * and
  * or
  * unless
  * defined between instant vectors only
  * ignoring: allows to ignore certain labels when trying to match - e.g., `prometheus_http_requests_total and ignoring(handler) promhttp_metric_handler_requests_total`
  * on: specifies the label onto which the matching should be performed - e.g., `promhttp_metric_handler_requests_total and on(code) prometheus_http_requests_total`
  * Vector/scalar operations apply the operator between each sample in the vector and the scalar

### Aggregation operators

* Special mathematical functions used to combine information
* sum - `sum(prometheus_http_requests_total) by (code)`
* min
* max
* avg
* stddev
* stdvar
* count: count number of elements
* count\_values: count number of different values
* bottomk
* topk
* quantile

### Functions

* rate: how fast a counter is increasing per-second of the time series in the range vector
* irate: the instant rate of increase of the time series in the range vector (taking the last two samples into account)
* changes: how many times a gauge has changed over time
* deriv: how quickly a gauge is changing
* predict\_linear: predicts a future value of a gauge based on previous values
* \*\_over\_time: applies an aggregation operation on each time series in the range vector
* sort/sort\_desc: sorts values in an instant vector
* time: current time from Epoch in UNIX timestamp

### Metric types

* Counter: cumulative metric that can only increase (or reset to zero on restart)
* Gauge: single numeric value that can go up or down
* Summary: tracks size and number of events (e.g., basename\_sum, basename\_count)
* Histogram: counts observation in configurable buckets (to calculate quantiles)

### What to instrument

* Services:
  * online-serving: request rate, latency, error rate, in-progress requests (both client and server side)
  * offline-processing: items coming in, in progress, error, last process time (both individual items and batches)
  * batch jobs: runtime, time of last completion (using push gateway)
* Libraries:
  * internal errors
  * latency time within library

### Recording rules

* Allow you to precompute frequently needed or compute expensive expressions, and save them as a new time series
* Querying the precomputer result is much faster than computing it on-the-fly
*   Recording rules are defined in YAML files as follows:

    ```yaml
    groups:
    - name: my-rules # name of the rule group
      rules:
      - record: job:node_cpu_seconds:avg_idle # name of the first rule
        expr: avg without(cpu) (rate(node_cpu_seconds_total{mode="idle"}[5m])) # PromQL expression
      - record: job:node_cpu_seconds:avg_not_idle # name of the second rule
        expr: avg without(cpu, mode) (rate(node_cpu_seconds_total{mode!="idle"}[5m])) # PromQL expression
    - name: my-rules-new:
      rules:
        ...
    ```
* Avoid rules for long vector ranges, as such queries tend to be expensive, and running them regularly can cause performance problems
* Use rules to store metrics data for long-term (months / years)

### Alerting

* Alerts are conditions in the form of PromQL expressions that continuously get evaluated and fire when the conditions are met
*   Similarly to recording rules, they are defined in YAML files:

    ```yaml
    groups:
    - name: my-rules # name of the rule group
      rules:
      - alert: NodeExportedDown
        expr: up{job="node_exporter"} == 0
        for: 1m

      - record: job:app_response_latency_seconds:rate1m
        expr: rate(app_response_latency_seconds_sum[1m]) / rate(app_response_latency_seconds_count[1m])

      - alert: AppLatencyAbove5sec
        expr: job:app_response_latency_seconds:rate1m >= 5
        for: 2m
        labels:
          severity: critical
        annotations:
          summary: 'Python app latency is over 5 seconds'
          description: 'app latency of instance {{ $labels.instance }}' of job {{ $labels.job }} is {{ $value }} for more than 2 minutes'
          app_link: 'http://localhost:8000/'

      - alert: AppLatencyAbove2sec
        expr: 2 < job:app_response_latency_seconds:rate1m < 5
        for: 2m
        labels:
          severity: warning
    ```
* The ALERTS metric will report a time series for each alert that has fired
* The `for` clause instructs Prometheus to keep the alert in the PENDING state for at least the time specified, and then fire if the condition has been met for the whole observation period
* By assigning labels to alerts, we can handle them in different ways (e.g., send a page for critical alert and an email for non-critical ones)

### Alertmanager

```yaml
route:
  receiver: admin

receivers:
- name: admin
  email_configs:
    - to: 'mail@example.com'
      from: 'example@gmail.com'
      smarthost: smtp.gmail.com:587
      auth_username: 'example@gmail.com'
      auth_identity: 'example@gmail.com'
      auth_password: *****
```

### Blackbox Exporter

![](<../.gitbook/assets/Prometheus Blackbox Exporter.bmp>)

* Allows to monitor network endpoint such as HTTP, HTTPS, DNS, ICMP, or TCP
* It can be used when we have no knowledge of the system internals, or to measure response times, availability, and network health
* The http prober by default uses IPv6
* The `/metrics` endpoint will return the metrics about the Blackbox Exporter itself, the metrics retrieved by the Blackbox Exporter for the target are exposed on the `/probe` endpoint

### Pushgateway

* It is used to handle the exposition of metrics pushed from short-lived or batch jobs
* To push metrics to Pushgateway, we need to send an HTTP POST request to `http://{address}:{port}/metrics/job/{job_name}/{label1_name}/{label1_value}/.../{labelN?_name}/{labelN_value}`
* If a Pushgateway collecting metrics goes down, we'll lose monitoring for all the targets linked to it
* Metrics pushed to Pushgateway are _not_ deleted automatically

### Service Discovery

* It is a mechanism to automatically discover and monitor targets and services
* Prometheus contains built-in integrations for Consul, Kubernetes, Azure, and Amazon EC2
* A static way to discover services is to fill the scrape\_config section of the prometheus.yaml configuration file
* For custom configurations, the file-based Service Discovery can be used: the service discovery mechanism writes the target to the file\_sd file, and Prometheus will read it and add the new instances to its target list
* The file\_sd can be written in either JSON or YAML syntax

### Prometheus HTTP API

* It is accessible at `http://{host}:{port}/api/v1/`
* The main endpoints are:
  * `query` to retrieve the metrics for a PromQL expression
  * `targets` to list the targets tracked by Prometheus
  * `rules` to list the recording rules and alerts currently loaded
  * `alerts` to list all active alerts
  * `status` to expose the current Prometheus information

### When does Prometheus fit?

* when recording any purely numeric time series
* for reliability
* in the world of micro-services

### When does Prometheus NOT fit?

* event logs or individual events
* for 100% accuracy of data
* high cardinality data
* for dashboarding (use Grafana)

## Resources

### Websites

* [PromQL for Humans](https://timber.io/blog/promql-for-humans/)
