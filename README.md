# SOFE3650F25-Project
SOFE3650F25 Project Assesment
## 1. ATAM Utility Tree
 [Project Utility Tree.drawio.pdf](https://github.com/user-attachments/files/23920741/Project.Utility.Tree.drawio.pdf)

## 2. ATAM Risk Assessment Table

| **ID**  | **Type** | **Description**                                                                           | **Quality Attributes Affected** |
| ------- | -------- | ----------------------------------------------------------------------------------------- | ------------------------------- |
| **R1**  | Risk     | Circuit breaker thresholds may be misconfigured, opening too early or too late.           | Availability, Performance       |
| **R2**  | Risk     | Caching may return stale academic data if invalidation is weak.                           | Performance, Correctness        |
| **R3**  | Risk     | Autoscaling may react too slowly for sudden conversational spikes (cold start).           | Scalability, Performance        |
| **R4**  | Risk     | Load balancer could become a bottleneck if redundancy isn’t implemented properly.         | Availability, Performance       |
| **R5**  | Risk     | Replicated message queue synchronization may cause throughput delays under heavy load.    | Scalability                     |
| **NR1** | Non-Risk | Rolling deployments ensure updates do not cause downtime.                                 | Availability                    |
| **NR2** | Non-Risk | Microservice replication ensures no single service instance is a single point of failure. | Availability, Scalability       |
| **NR3** | Non-Risk | Caching successfully reduces LMS load and improves response times during normal load.     | Performance                     |


## 3. ATAM Descriptions

## 3.1 Sensitivities
S1: Concern over circuit-breaker timeout configuration — small threshold changes significantly affect performance and availability.
S2: Concern over cache time-to-live (TTL) — short TTL increases load but improves freshness; long TTL improves performance but risks staleness.
S3: In the event that autoscaling thresholds are not tuned properly, the system becomes extremely sensitive to sudden load spikes, causing slow responses before new replicas start.
S4: Using replicated message queues increases reliability but makes the system sensitive to synchronization latency under heavy notification load.
S5: Database connection pool size greatly affects request throughput — small increases or decreases have a large impact during peak load.

## 3.2 Tradeoffs
T1: Performance (+) vs. Correctness (–) — caching improves responsiveness but increases risk of stale or inaccurate data from LMS and Registration.
T2: Availability (+) vs. Cost (–) — replication of services provides fault tolerance but significantly increases operating cost and resource consumption.
T3: Scalability (+) vs. Cost Stability (–) — aggressive autoscaling reacts quickly to spikes but causes unpredictable cloud costs.
T4: Availability (+) vs. Data Freshness (–) — circuit-breaker fallback maintains uptime, but cached fallback responses may lack the most recent academic updates.
T5: Modifiability (+) vs. Performance (–) — adding abstraction layers (API Gateway, Integration Service) improves maintainability but introduces latency overhead.

## 3.3 Risks
R1: Circuit breaker thresholds may be misconfigured, creating instability by opening prematurely or too late during external service slowdown.
R2: Cache invalidation strategy may be insufficient, resulting in stale or inconsistent academic data being presented to the user.
R3: Autoscaling delay may cause slow responses during sudden load surges such as exam weeks or registration periods.
R4: Load balancer may become a performance bottleneck or single point of failure if not deployed redundantly.
R5: Replicated message queues introduce synchronization overhead, possibly slowing down high-volume notification delivery.

## 3.4 Non-Risks
N1: Rolling updates ensure that service deployments do not interrupt active student sessions, maintaining required availability.
N2: Service replication reliably supports failover and load distribution, eliminating single-instance failure concerns.
N3: Caching significantly improves performance under normal load and safely reduces dependency on LMS/Registration latency.
N4: Use of microservices ensures that individual components can scale independently without affecting overall system stability.
N5: API Gateway abstraction promotes consistent integration with external systems and isolates internal services from protocol changes.
