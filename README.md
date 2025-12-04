# SOFE3650F25-Project
SOFE3650F25 Project Assesment
## 1. ATAM Utility Tree
 [Project Utility Tree.drawio.pdf](https://github.com/user-attachments/files/23920741/Project.Utility.Tree.drawio.pdf)
![Project Utility Tree](https://github.com/user-attachments/assets/3df89c29-288b-41db-bc56-c868b95013fb)

## 2. ATAM Risk Assessment Table (for just the 3 most important scenarios)
## Scenario Table 1 — Performance (P1)
| **Analysing Scenario** | **P1**                                                |
| ---------------------- | ----------------------------------------------------- |
| **Scenario**           | Conversational query must return within 2 seconds     |
| **Attributes**         | Performance                                           |
| **Stimulus**           | Student submits an AI query                           |
| **Environment**        | Normal operating load                                 |
| **Response**           | AI Service returns generated response through gateway |
| **Response Measure**   | Avg response time ≤ 2s                                |

| **Architecture Decision** | **Sensitivity** | **Tradeoff** | **Risk** | **Nonrisk** |
| ------------------------- | --------------- | ------------ | -------- | ----------- |
| AD1 Caching Layer         | S1, S2          | T1           | R2       | N3          |
| AD2 Load Balancing        | S3              | —            | R3       | N2          |
| AD3 Circuit Breaker       | S1              | T4           | R1       | —           |
| AD4 Autoscaling           | S3              | T3           | R3       | N4          |
| AD5 API Gateway Routing   | S5              | —            | —        | N5          |

## Scenario Table 2 — Availability (A1)
| **Analysing Scenario** | **A1**                                     |
| ---------------------- | ------------------------------------------ |
| **Scenario**           | Failover when an AI Service instance fails |
| **Attributes**         | Availability                               |
| **Stimulus**           | A service replica unexpectedly crashes     |
| **Environment**        | Normal operation with failure of one node  |
| **Response**           | Traffic reroutes to another replica        |
| **Response Measure**   | Failover < 1 second; no dropped sessions   |

| **Architecture Decision**       | **Sensitivity** | **Tradeoff** | **Risk** | **Nonrisk** |
| ------------------------------- | --------------- | ------------ | -------- | ----------- |
| AD1 Service Replication         | S5              | T2           | R4       | N2          |
| AD2 Load Balancer Health Checks | S1              | —            | R4       | N1          |
| AD3 Circuit Breaker             | S1              | T4           | R1       | —           |
| AD4 Rolling Updates             | —               | T2           | —        | N1          |
| AD5 Gateway Abstraction         | —               | —            | —        | N5          |


## Scenario Table 3 — Scalability (S1)
| **Analysing Scenario** | **S1**                                              |
| ---------------------- | --------------------------------------------------- |
| **Scenario**           | System handles traffic spike situations             |
| **Attributes**         | Scalability                                         |
| **Stimulus**           | Large surge of concurrent student queries           |
| **Environment**        | Peak academic load (registration/exam periods)      |
| **Response**           | Autoscaler adds new replicas; throughput maintained |
| **Response Measure**   | Response time < 2s under spike                      |

| **Architecture Decision**       | **Sensitivity** | **Tradeoff** | **Risk** | **Nonrisk** |
| ------------------------------- | --------------- | ------------ | -------- | ----------- |
| AD1 Autoscaling Policies        | S3              | T3           | R3       | N4          |
| AD2 Replicated Message Queues   | S4              | T2           | R5       | —           |
| AD3 Microservice Independence   | —               | T5           | —        | N4          |
| AD4 Caching Layer               | S2              | T1           | R2       | N3          |
| AD5 Database Connection Pooling | S5              | —            | —        | —           |

                            
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
