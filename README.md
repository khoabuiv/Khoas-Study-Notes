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
- 
