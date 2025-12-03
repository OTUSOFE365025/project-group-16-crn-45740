# Iteration 3 – Addressing Quality Attribute Scenario Drivers

The outcomes of the tasks completed during the third iteration of the ADD design process for the AIDAP system are shown in this section. Building on the architectural foundations created in iterations 1 and 2, we now focus on refining the system to guarantee that it can meet three highly essential quality attribute scenarios. In contrast to prior iterations that relied on functional structure, this iteration focuses on the architectural mechanisms required to ensure performance, availability, and scalability in a cloud-native environment.
<!-- :contentReference[oaicite:0]{index=0} -->

---

## Step 2 – Establish Iteration Goal by Selecting Drivers

For this iteration, the architect focuses on the following quality attribute scenarios: :contentReference[oaicite:1]{index=1}  

### QA-1 — Performance  
*A student sends a conversational query during normal load. The system returns a response within an average of 2 seconds.*  
(Iteration 1 driver)

### QA-2 — Availability  
*The system remains operational 99.5% of the time each month. Deployments or external service failures do not interrupt active sessions.*  
(Iteration 1 driver)

### QA-6 — Scalability  
*The system must be capable of handling increased load during peak periods such as exam weeks by allocating additional resources automatically.*  
(Iteration 1 driver)

---

## Step 3 – Choose One or More Elements of the System to Refine

For an availability scenario of this type, the elements refined in this iteration are the physical nodes previously identified in iteration 1. These include: :contentReference[oaicite:2]{index=2}  

- Application services:
  - AI Service  
  - Dashboard Service  
  - Notification Service  
  - Integration Service  
- API Gateway  
- Load balancing layer  
- AIDAP database tier  
- Message queue for background tasks and notifications  
- External system integration connectors (LMS, Registration, Calendar, Email)

---

## Step 4 – Choose Design Concepts That Satisfy the Selected Drivers
The design concepts used in this iteration are the following:

| **Design Decisions and Location** | **Rationale and Assumptions** |
|----------------------------------|-------------------------------|
| **Introduce Active Redundancy Across All Application Services (QA-2, QA-6)** | Replicating the core components of AIDAP ensures that the system continues to function even if individual service instances fail unexpectedly or require maintenance. If one instance of the AI Service, Dashboard Service, or Integration Service fails, the other replicas continue to serve traffic uninterrupted. This directly improves availability by preventing downtime (QA-2), while also facilitating scalability by allowing new copies to be deployed as demand increases (QA-6). Replication is critical for student-lecturer exchanges that cannot be interrupted, such as conversational questions in UC-1 and academic update retrieval in UC-3. This approach also complies with the cloud deployment standards for continuous uptime (CON-3). |
| **Introduce a Load-Balanced Cluster for Gateway and Services (QA-1, QA-2, QA-6)** | Incoming requests are distributed equally among healthy service replicas by adding a load-balancing layer. This helps the system maintain low response latency under typical and peak academic loads by preventing any one instance from getting overloaded (QA-1). Additionally, the load balancer improves overall system stability by identifying broken instances and rerouting traffic (QA-2). This enables use cases such as UC-1 (student asks a question) and UC-2 (notifications), which rely on speedy and reliable request handling. Constraint CON-5, which calls for an average response time of two seconds, is likewise benefitted to the design. |
| **Introduce a Replicated Message Queue (QA-1, QA-6)** | Adding a replicated message queue provides a buffer for high-volume background tasks such as notifications, synchronization with LMS or registration systems, and some AI functions. By queuing these operations instead of performing them simultaneously, the system remains responsive even during moments of intense stress (QA-1, QA-6). By ensuring that no messages are lost during node failures, queue replication strengthens availability (QA-2). This supports UC-2 (notifications) and is consistent with CON-8, which demands that notifications be sent reliably depending on student preferences. |
| **Introduce Caching to Improve Response Time (QA-1)** | Introducing caching allows AIDAP to retain previously created AI responses and commonly accessible academic material, such as schedules, deadlines, or announcements. This considerably avoids repetitive calls to other systems and lowers service processing time, boosting responsiveness (QA-1). Additionally, it improves system scalability indirectly by lessening the strain on the database and integration connections (QA-6). Caching supports UC-1, where conversational requests must respond promptly, and aligns with CON-5, which demands that responses come in within 2 seconds under typical load. |
| **Introduce Autoscaling Policies Based on CPU, Memory, and Request Queues (QA-6)** | Autoscaling ensures that AIDAP can deploy new service replicas during periods of strong traffic, such as registration week or exam season. When load drops, replicas can be decreased to minimize expense. This dynamic scaling ensures that performance maintains steady during peak demand (QA-1) and that sufficient capacity is always available (QA-6). Autoscaling also supports availability (QA-2) because sudden load spikes will not overwhelm the system. This supports UC-1 and UC-3, which both undergo load surges during academic events, and coincides with CON-3, which requires a cloud-based implementation. |
| **Introduce Rolling Updates for Zero-Downtime Deployment (QA-2)** | Rolling deployments enable the system to replace or upgrade service instances without stopping the overall program. While other replicas continue to fulfill requests, only one is taken offline at a time. This increases availability by preventing updates from interfering with ongoing sessions (QA-2). It also allows model updates, version changes, and maintenance activities required by system maintainers in UC-6. This fits with CON-9, which promotes continuous availability and system observability during updates. |
| **Introduce Circuit Breakers for External Systems (QA-2, QA-1)** | Circuit breakers help prevent failures in external academic systems (LMS, Registration, Calendar) from compromising the availability and performance of AIDAP. The circuit breaker opens and AIDAP uses cached or partially accessible data in the event that an external service slows down or ceases responding. This keeps the platform responsive and avoids cascading delays (QA-1). It also guarantees that the platform remains up during external outages (QA-2). UC-1 and UC-3, which both rely significantly on outside data, are directly supported by this ruling. It also addresses CON-1, which demands safe integration with university systems. |
