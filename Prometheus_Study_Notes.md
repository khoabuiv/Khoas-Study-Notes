# Prometheus Study Notes

#### Core system parameters for monitoring:
- Rate of incoming request
- Latency of the requests
- Rate of request handling errors
- The usage and saturation of resources
- Physical conditions

#### Purpose of monitoring:
- Find the most suitable approach to getting insight into your systems and acting on it

#### Observability
- generally describes what facilities a system offers to be observed and understood in its behavior

#### Methods for continuous systems monitoring:
- Check-based fault detection
- Log-based monitoring
- Metrics-based monitoring
- Request tracing

#### Ad hoc monitoring:
- Attach a debugger to the process
- Profiling a program
- Tracing the execution details

#### Check-based fault detection:
- A type of monitoring in which the monitoring system executes periodic check scripts on hosts to ensure that the hosts and services running on them are currently up, running, and healthy.
- Little emphasis on historical data or basing fault detection on flexibly-derived view of an entire service over time and across machines. 
- Probe the service from the outside rather than inside the service. 
- Designed for static computing systems with designated purpose.
- Nagios is a open source Check-Based Fault Detection system monitoring service.

#### Event-based monitoring
- Collecting detailed information about all relevant events that happen in a system and processing them either for general insight or for alerting.
- Involves processing pipelines that ship and transform logs or structured events to a central log-storage system.
- Provides extremely details insight, but the pipeline becomes expensive when scales. Often unrealistic to base your main production monitoring on logs alone

#### Metrics-based monitoring
- Periodically samples the current numeric value of an evolving measurement and appends the resulting sample to a time series database.
- Metrics lose per-event granularity and only represent pre-aggregated numeric data. Make for a great source of actionable and precise alerts. It's often a good idea to base your main monitoring on metrics for this reason.

#### Request tracing
- A special form of event logging that focuses on tracing individual requests across processing stages or service boundaries.

#### Prometheus:
- An integrated monitoring system and time series database that aims to: exposing metrics from processes, machines, and other components; collecting and storing that metrics data; querying, alerting, and dashboarding based on the collected data
- Specially designed to work well with dynamic cloud environments
- Goals that Prometheus does not solve: event-based monitoring or tracing; automatic anomaly detection; automatic horizontal scaling; data durability; user management and authentication

#### Time series:
- Every series is identified by a metric name and a set of key-value pairs called labels: metric name identifies the overall aspect of a system that is being measured; labels differentiate sub-dimensions or facets of the metric.

#### PromQL:
- Prometheus based functional language that is optimized for evaluating flexible and efficient computations on time series data.
- Only used for reading data, not for inserting, updating, or deleting data

#### Main ways of configuring Prometheus
- Using YAML-based configuration file
- Using flag-based settings for simple settings

#### Graph view
- View a graph from a PromQL expression over time

#### Table view
- View the output of PromQL at one point in time

#### How to tell Prometheus is scraping a target correctly
- Shows a target as UP on /targets page
- Prometheus records a successful scrape

#### Naming convention
- metric names are internally represented as just another label with the special label \__name__

#### Prometheus functions:
- rate(): calculates the per-second increase of a counter as averaged over a specified window of time
- irate(): only considers the last two points under the provided window and calculates an instantaneous rate from them.
- increase(): query the total increase over a given time window
- delta(): query the raw increase of gauge metrics. Only considers the first and the last data point under the provided time window and disregards overall trends in the intermediate data points
- deriv(): calculate the per-second derivative over the same time window using full linear regression over all available data points under the same window
- predict_linear(): is an extension to deriv() that allows you to predict what the value of a gauge will be in a given amount of time in the future.
- without(): an operator to exclude for aggregation functions
- by(): an operator to include for aggregation functions
- group_left() and group_right(): an operator to exlude the dimension to the left or right of the time-series vector
- sort() and sort_desc(): sort query results by their sample value.
- up(): function that create a synthetic metric with 1 being succeeded scrape and 0 being scrape failed
- histogram_quantile(): requires the le label
- topk() and bottomk(): the total number of series displayed in the graph can be different from the specified k value

#### Node Exporter:
- Exposes metrics about the Linux or Unix host it is using on.
- cAdvisor: an exporter that exposes those metrics about every container running on a system
- groups: the Linux kernel subsystem does cAdvisor use to read container metrics.

#### Metric types:
- Gauges metric type: metric type that tracks values that can go up or down
- Counter metric type: metric type that tracks values that can only ever go up
- Summary metric type: calculate client-side-calculated quantiles from a set of observations, like request latency percentiles. They also track the total count and total sum of observations.
- Histogram metric type: track bucketed histograms of a set of observations like request latencies.

#### Exporter: 
- a process that sits between Prometheus and a third-party component you want to monitor that does not have direct support for Prometheus metrics.
- Good practices for exporter architecture: fetch metrics for third party system upon every scrape; run one exporter per process of the service you want to monitor.

#### Metrics Translation Best Practices:
- Create "throw-away" metrics during each scrape that are set to the current value upon creation and are forgotten after the scrape.

#### Set operations on two vectors:
- and
- or
- unless

#### Histogram bucket series:
- Metric name has a _bucket suffix
- Upper bucket boundary is stored in the le label
- Buckets are cumulative

#### Topics that can be relabel:
- Discovered scrape targets
- Individual samples from scrape targets
- Alerts sent from AlertManager
- Samples written to remote storage systems

#### Service discovery support services:
- Cloud and VM providers
- Cluster schedulers
- Generic mechanisms
- File-based custom service discovery

#### Blackbox monitoring:
- Monitoring a system's or service's health from the outside.
- The Blackbox Exporter: a Prometheus component that allows probing a remote service in various ways: HTTP(S); DNS; TCP; and ICMP probes
- Steps to create a blackbox exporter:
  1. Move the address of the discovered target into a __param_target label
  2. Explicitly set the instance label to the value of that target query parameter
  3. Set the address that Prometheus should scrape (__address__ label) to the address of the Blackbox Exporter
 
#### Pull versus push monitoring:
- Pull monitoring: a monitor system that actively obtains indicators. Prometheus is a pull-monitoring system
- Push monitoring: a monitor system that does not actively obtain data. Prometheus can be turn into push monitoring with PushGateway
- Pushgateway: a core Prometheus component that runs as a separate server and allows other jobs to push groups of metrics to it over HTTP using Prometheus's text-based metrics exposition format.
- Pushgateway is used for batch jobs 
- Pushgateway details:
  - Pushgateway does not act as an aggregator or event counter
  - It only remembers the last value of each metric that was pushed to it.
  - Multiple jobs can push metrics to the Pushgateway without interfering with each other by defining grouping labels
 
#### Alerting:
- Alerting best practices:
  1. Alert on user-visible service-level symptoms
  2. Alert on imminent dangers to your service
  3. Alert in a way that is actionable
  4. Still collect cause-based metrics as supplementary information to help you debug incidents
  5. Err on the side of removing noisy alerts to reduce on-call fatigue
- Contains Prometheus and AlertManager. One AlertManager for many Prometheus servers for multiple services.
- Routes: determine how alerts are grouped into notifications, and what kind of notification should finally be sent out for them.
- Silences: allow you to pause notifications for a given set of alerts for a specified period of time
- Inhibition rules: allow modeling of service dependencies.
- Notification types: built-in notification types for services such as email, phone, PagerDuty, and OpsGenie
- Notification templating: allows you to customize the style and content of the actual alert notifications that get sent out.

#### Prometheus high availability:
- Alertmanager's clustered highly available:
  - Prometheus sends alerts to all instances of an Alertmanager cluster
  - The Alertmanager instances gossip the notification state of each alert group among each other
  - The Alertmanager instances establish an ordering among themselves and wait progressively longer before sending out notifications along that order.
- Querying options for high availability:
  - Always query only one server of the pair
  - Load-balance between the servers
  - Load-balance with default and fallback backends
 
#### Recording rules:
- To speed up expensive queries
- To create shorter, more convenient metrics names for long expressions
- To pre-record aggregated data for use in federation

#### Prometheus system federation models:
- Functional sharding: deploying different Prometheus servers for different services, teams, functions, and so on.
- Hierarchical Federation: deploy different Prometheus servers for different clusters, regions, or similar natural sharding boundaries of a single service, then use federation to pull in aggregate metrics into a global Prometheus server.

#### Meta monitoring:
- Metrics-Based Meta-Monitoring: a separate meta-monitoring Prometheus server (or set of them) that does nothing else than monitoring all of your other Prometheus servers.
- What to monitor on a Prometheus server?
  1. Where Prometheus spends the most CPU time
  2. Where Prometheus allocates the most memory
  3. What all goroutines are currently doing and whether they are blocked on something
 
#### Prometheus data storage: 
- Prometheus interfaces for integrating remote long-term storage:
  1. Prometheus can write all samples that it ingests to a remote URL in a standardized format, in near-time
  2. Prometheus can read back sample data from a remote URL in a standardized format
 - Thanos: A popular alternative for providing durable long-term storage for Prometheus that does not require integrating with Prometheus's remote write protocol.
 - Prometheus local time series database optimization:
  1. Appending current samples
  2. read ranges of data from a small subset of series
