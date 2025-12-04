# Iteration 3 – Addressing Quality Attribute Scenario Drivers

The outcomes of the tasks completed during the third iteration of the ADD design process for the AIDAP system are shown in this section. Building on the architectural foundations created in iterations 1 and 2, we now focus on refining the system to guarantee that it can meet three highly essential quality attribute scenarios. In contrast to prior iterations that relied on functional structure, this iteration focuses on the architectural mechanisms required to ensure performance, availability, and scalability in a cloud-native environment.
<!-- :contentReference[oaicite:0]{index=0} -->

---

## Step 2 – Establish Iteration Goal by Selecting Drivers

For this iteration, the architect focuses on the following quality attribute scenarios: 

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

For an availability scenario of this type, the elements refined in this iteration are the physical nodes previously identified in iteration 1. These include: 

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

---

## Step 5 – Instantiate Architectural Decisions

| **Design Decisions and Location** | **Rationale and Assumptions** |
|----------------------------------|-------------------------------|
| Replicate all application services across multiple nodes | Service replication is achieved by running multiple copies of essential services such as the AI Service, Notification Service, and Integration Service. Each replica is associated with the system's service mesh or orchestration layer. This instantiation enhances availability by guaranteeing that the system continues to function even when individual replicas fail (QA-2), and it increases scalability by allowing the architecture to add more replicas as demand develops (QA-6). It helps UC-1 and UC-2 by assuring consistent response handling. This instantiation follows CON-3, which necessitates a cloud-native deployment capable of horizontal scaling. |
| Deploy a load balancer above the gateway and services | In the deployment view, the load balancer is created as a specialized component that is in charge of distributing incoming traffic and monitoring the health of service replicas on a constant basis. If a replica becomes slow or unresponsive, it will be removed from the routing table automatically. This instantiation supports CON-5 by increasing overall responsiveness and immediately improving performance (QA-1). It also improves availability (QA-2) by removing single points of failure. This is especially beneficial for UC-1, where users expect speedy, uninterrupted conversational engagements. |
| Deploy a replicated message queue on dedicated nodes | The message queue is created on a separate replicated node, ensuring that application tier failures do not disrupt background tasks. By buffering asynchronous requests, the queue keeps slowdowns from spreading throughout the system and lowers blocking during peak usage. This instantiation supports both CON-8 (reliable notification delivery) and CON-7 (proper data recovery and ordering). It directly increases performance (QA-1) and scalability (QA-6) by reducing load spikes. This is critical for UC-2, as real-time notifications rely on accurate event handling. |
| Add caching in Integration and AI Service modules | Caching components are created as in-memory storage within the AI Service and Integration Service. Each has interfaces for storing and retrieving cached entries. This instantiation minimizes external data lookups and improves system reaction times, making it suitable for both CON-5 and QA-1. Caching improves scalability indirectly by reducing overall system demand. It benefits UC-1 and UC-3 by providing faster access to information like course schedules and deadlines. |
| Introduce circuit breakers around LMS, Registration, Calendar APIs | Circuit breakers are implemented as wrappers for external API calls. If an external system becomes unresponsive, the circuit breaker blocks further calls and triggers fallback logic based on cached or partial data. This instantiation increases availability (QA-2) while improving performance (QA-1) by eliminating significant delays. It supports UC-3 and UC-5 because these applications rely heavily on external academic data. This instantiation is consistent with CON-1, which needs secure compatibility with university systems. |
| Enable cloud platform rolling deployments | The rolling deployment controller is created as part of the container orchestration system. It upgrades or rolls back service replicas in sequence without interrupting the entire system. This instantiation promotes availability (QA-2) and is consistent with CON-9 by ensuring continuous monitoring and safe software modifications. It strongly supports UC-6, which requires maintainers to use a non-disruptive updating approach. |
| Configure autoscaling rules for all microservices | Autoscaling is implemented as a cloud-managed component with thresholds for CPU, memory, and queue depth. This ensures that new service replicas are automatically introduced as load grows and removed when demand decreases. This instantiation directly fulfills scalability (QA-6), helps sustain availability during peak traffic (QA-2), and adheres to the cloud deployment requirements in CON-3. It is especially important for UC-1, which has a huge fluctuation in traffic. |

---

## Step 6 - Sketch Views and Record Design Decisions

## Deployment Diagram (figure 6.1)
<img width="747" height="729" alt="Screenshot 2025-12-03 222020" src="https://github.com/user-attachments/assets/7c1d4858-46e2-4e97-8901-704d2b6d850d" />

## Sequence Diagram: UC-1 (figure 6.2)
<img width="729" height="658" alt="Screenshot 2025-12-03 224254" src="https://github.com/user-attachments/assets/a1c1106e-1792-48a0-9d85-54e80494cbfe" />

## Sequence Diagram: UC-2 (figure 6.3)
<img width="1522" height="745" alt="usecase2" src="https://github.com/user-attachments/assets/e7fa3755-b7b2-4017-a4f8-7444d266d9c0" />

## Sequence Diagram: UC-3 (figure 6.4)
<img width="1774" height="1079" alt="usecase3" src="https://github.com/user-attachments/assets/adea3183-b6a3-4ff0-9ffb-de5f6c7ca0ee" />

  The updated deployment diagram (figure 6.1) for AIDAP shows how the design decisions from Steps 4 and 5 are realized in the actual structure of the system. The platform is accessed by instructors and students via a browser that establishes a connection with a cloud-native load balancer in front of the API Gateway. The AI Service, Dashboard Service, Notification Service, and Integration Service are hosted by two replicated application nodes behind the gateway, demonstrating the utilization of active redundancy across all crucial application services. A dedicated message queue cluster is deployed on its own node to handle asynchronous operations such as notifications and external data processing, while a replicated database cluster holds user profiles, conversational history, and aggregated academic data. Connectors from the Integration Service to university systems (LMS, Registration, Calendar, Email) highlight how external dependencies are isolated from core AIDAP functionality. Collectively, these deployment decisions support availability (QA-2), scalability (QA-6), and performance (QA-1) by eliminating single points of failure and enabling horizontal scaling under peak load.

   For the main use cases covered in this iteration, three complimentary sequence diagrams were made to show how this infrastructure operates at runtime. The UC-1 sequence diagram (figure 6.2) shows how a student request is routed through the load balancer and API Gateway to one of the replicated application nodes, where the AI Service loads user context, generates a response, updates conversation history in the database, and enqueues any notification jobs for asynchronous processing. This illustrates how asynchronous background work and synchronous database operations work together to facilitate follow-up tasks while maintaining minimal latency.

   An incoming event from a system like the LMS or Registration system is handled by the Integration Service, persisted, and then put in the message queue for later processing by a Notification Worker. This is where the UC-2 sequence diagram (figure 6.3) starts. The Notification Worker gets user preferences, composes an email, and sends it through the email server. This flow shows the necessity of asynchronous event processing, reliable queueing, and the decisions related with CON-8.

   Lastly, the UC-3 sequence diagram (figure 6.4) demonstrates how the Integration Service first checks a cache before gathering course, enrollment, and deadline data, storing a snapshot in the database, and returning the result to the user. It then uses a circuit breaker to safely call external systems when necessary. This demonstrates how performance and availability are supported by caching, fallback behavior, and regulated interaction with external APIs. When taken as a whole, these four diagrams show how the design choices made in iteration 3 meet those selected quality attribute situations and limitations.


---
## Step 7 – Perform Analysis of Current Design and Review Iteration Goal and Achievement of Design Purpose

| **Not Addressed** | **Partially Addressed** | **Completely Addressed** | **Design Decisions Made During Iteration** |
|-------------------|-------------------------|---------------------------|---------------------------------------------|
|                   | UC-1                    |                           | Replication, caching, and load balancing support conversational responsiveness and prevent failures from interrupting user queries. |
|                   | UC-2                    |                           | The replicated message queue ensures notifications are reliably processed even under high load or partial failures. |
|                   | UC-3                    |                           | No relevant decisions were made in this iteration. |
|                   | QA-1                    |                           | Caching, asynchronous processing, load balancing, and autoscaling improve response times. |
|                   | QA-2                    |                           | Replicated services, rolling deployments, and circuit breakers ensure the system remains operational during failures or maintenance. |
|                   | QA-3                    |                           | No relevant decisions were made in this iteration. |
|                   | QA-4                    |                           | No architectural mechanisms directly supporting modifiability were introduced in this iteration. |
|                   | QA-6                    |                           | Autoscaling and replicated components allow the system to increase capacity during peak academic periods. |
|                   | QA-7                    |                           | No relevant decisions were made in this iteration. |
|                   | CON-1                   |                           | Circuit breakers and fallback behavior support reliable integration during LMS/Registration/Calendar outages. |
|                   | CON-2                   |                           | No relevant decisions were made in this iteration. |
|                   | CON-4                   |                           | No relevant decisions were made in this iteration. |
| CON-5             |                         |                           | Only indirect improvements were made. |
|                   | CON-8                   |                           | Notification reliability is strengthened via asynchronous processing and replicated queue nodes. |
|                   | CON-9                   |                           | Rolling deployments and replication contribute to system observability and uptime. |
|                   | CRN-1                   |                           | This concern was not directly addressed in this iteration. |
|                   | CRN-2                   |                           | No new architectural decisions were made regarding security or sensitive-data handling. |
|                   | CRN-3                   |                           | Rolling deployments indirectly support safe versioning of AI models without downtime. |
|                   | CRN-5                   |                           | No relevant UI decisions were made in this iteration. |
