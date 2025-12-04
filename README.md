# SOFE3650F25-Project
SOFE3650F25 Project Assesment
## 1. ATAM Utility Tree
 [Project Utility Tree.drawio.pdf](https://github.com/user-attachments/files/23920741/Project.Utility.Tree.drawio.pdf)
![Project Utility Tree](https://github.com/user-attachments/assets/3df89c29-288b-41db-bc56-c868b95013fb)

## 2. ATAM Risk Assessment Table

| **ID** | **Type** | **Description**                                                                                                                             | **Impacted QAs**          | Reasoning                                         |
| ------ | -------- | ------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------- | -------------------------------------------------------------------------------- |
| **R1** | **Risk** | Circuit breakers depend heavily on accurate timeout thresholds. Misconfiguration may cause premature fallback or delayed failure detection. | Availability, Performance | External system latency varies; poor tuning lowers reliability under load.       |
| **R2** | **Risk** | Caching may return stale academic/registration data if invalidation is not aggressive.                                                      | Performance, Correctness  | ADD Iteration 3 added caching but did not define freshness strategies.           |
| **R3** | **Risk** | Autoscaling only triggers on CPU/memory/queue depth; conversational load may not correlate to these metrics.                                | Scalability, Performance  | Traffic may spike faster than autoscaler reacts (cold-start problem).            |
| **R4** | **Risk** | Load balancer becomes a single bottleneck if its own capacity is insufficient.                                                              | Performance, Availability | ADD Iteration 3 assumes load balancer redundancy but does not describe failover explicitly. |
| **R5** | **Risk** | Message queue replication may increase coordination overhead, causing delays under heavy notification loads.                                | Scalability               | Multi-node synchronization may degrade throughput.                               |


## 3. ATAM Descriptions

## 3.1 Sensitivities
-S1: Concern over circuit-breaker timeout configuration — small threshold changes significantly affect performance and availability.

-S2: Concern over cache time-to-live (TTL) — short TTL increases load but improves freshness; long TTL improves performance but risks staleness.

-S3: In the event that autoscaling thresholds are not tuned properly, the system becomes extremely sensitive to sudden load spikes, causing slow responses before new replicas start.

-S4: Using replicated message queues increases reliability but makes the system sensitive to synchronization latency under heavy notification load.

-S5: Database connection pool size greatly affects request throughput — small increases or decreases have a large impact during peak load.


## 3.2 Tradeoffs
-T1: Performance (+) vs. Correctness (–) — caching improves responsiveness but increases risk of stale or inaccurate data from LMS and Registration.

-T2: Availability (+) vs. Cost (–) — replication of services provides fault tolerance but significantly increases operating cost and resource consumption.

-T3: Scalability (+) vs. Cost Stability (–) — aggressive autoscaling reacts quickly to spikes but causes unpredictable cloud costs.

-T4: Availability (+) vs. Data Freshness (–) — circuit-breaker fallback maintains uptime, but cached fallback responses may lack the most recent academic updates.

-T5: Modifiability (+) vs. Performance (–) — adding abstraction layers (API Gateway, Integration Service) improves maintainability but introduces latency overhead.

## 3.3 Risks
-R1: Circuit breaker thresholds may be misconfigured, creating instability by opening prematurely or too late during external service slowdown.

-R2: Cache invalidation strategy may be insufficient, resulting in stale or inconsistent academic data being presented to the user.

-R3: Autoscaling delay may cause slow responses during sudden load surges such as exam weeks or registration periods.

-R4: Load balancer may become a performance bottleneck or single point of failure if not deployed redundantly.

-R5: Replicated message queues introduce synchronization overhead, possibly slowing down high-volume notification delivery.

## 3.4 Non-Risks
-N1: Rolling updates ensure that service deployments do not interrupt active student sessions, maintaining required availability.

-N2: Service replication reliably supports failover and load distribution, eliminating single-instance failure concerns.

-N3: Caching significantly improves performance under normal load and safely reduces dependency on LMS/Registration latency.

-N4: Use of microservices ensures that individual components can scale independently without affecting overall system stability.

-N5: API Gateway abstraction promotes consistent integration with external systems and isolates internal services from protocol changes.
