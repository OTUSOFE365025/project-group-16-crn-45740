# ADD Iteration 1 – Establishing Overall System Structure  
**AI-Powered Digital Assistant Platform (AIDAP)**

---

## Step 1: Review Inputs

### 1.1 Design Purpose
The purpose of Iteration 1 is to establish the **overall architectural structure** for AIDAP. This includes selecting reference architectures, deployment patterns, major system layers, and the foundational components that will support the system’s functional and non-functional requirements.

AIDAP must:
- Integrate with multiple university systems (LMS, Registration, Calendar, Email)
- Provide conversational AI interactions via text/voice
- Support high scalability (5,000–10,000 concurrent users)
- Maintain strong security and privacy controls
- Be cloud-native and highly available

---

### 1.2 Primary Functional Requirements (Use Cases)

The primary use cases driving architectural decisions in this iteration are:

| Use Case | Description |
|---------|-------------|
| **UC-1: Student AI Assistant** | Students ask academic questions using AI and receive personalized answers. |
| **UC-2: Academic Dashboard** | Displays grades, deadlines, schedules, and analytics. |
| **UC-3: Course Management** | Lecturers upload/update course content and view analytics. |
| **UC-5: System Administration** | Administrators define policies, manage integrations, and oversee announcements. |
| **UC-6: System Maintenance** | Maintainers monitor logs, manage updates, and oversee system health. |

These use cases span all user roles and require external data access, security, and AI processing.

---

### 1.3 Prioritized Quality Attribute Scenarios (Drivers)

The following QAs have the highest impact and difficulty and are selected as **drivers**:

| **Scenario**          | **Importance to the Customer** | **Difficulty of Implementation (Architect)** |
|-----------------------|--------------------------------|---------------------------------------------|
| QA-1: Performance     | High                           | High                                        |
| QA-2: Availability    | High                           | High                                        |
| QA-3: Security        | High                           | Medium                                      |
| QA-4: Modifiability   | High                           | Medium                                      |
| QA-5: Usability       | Medium                         | Medium                                      |
| QA-6: Scalability     | High                           | High                                        |
| QA-7: Reliability     | Medium                         | Medium                                      |
| QA-8: Interoperability| High                           | Medium                                      |
| QA-9: Maintainability | Medium                         | Medium                                      |

---

### 1.4 Constraints (All Are Drivers)

| ID | Constraint |
|----|------------|
 **CON-1** | The AIDAP platform must connect securely with existing university systems such as registration, calendar, and LMS using standard REST or GraphQL APIs to exchange data in real time. |
| **CON-2** | All users are required to authenticate through the institution’s Single Sign-On (SSO) before using any of the assistant’s features. |
| **CON-3** | The system has to be deployed on a cloud environment to allow flexible scaling and continuous availability, even when updates or patches are applied. |
| **CON-4** | User and institutional data must be encrypted during storage and transmission, and handled according to the university’s privacy and security standards. |
| **CON-5** | The AI assistant should respond to user queries with an average latency of around 2 seconds under normal load. |
| **CON-6** | Only authorized users can access data relevant to their roles (student, lecturer, or admin), and unauthorized attempts must be logged for auditing. |
| **CON-7** | The system must automatically perform backups of user interactions, model configurations, and system data to ensure recovery in case of a failure. |
| **CON-8** | Notifications, alerts, and announcements must reach the correct users based on their preferences and chosen language. |
| **CON-9** | System performance and availability metrics must be tracked in real time and displayed on a dashboard accessible only to maintainers and administrators. |


---

### 1.5 Architectural Concerns

| ID | Concern |
|:----------------|--------------|
| **CRN-1** |Maintaining multiple integrated external systems and other data resources , along with ensuring data consistency,synchronization and fault recovery when the systems experience downtime.
| **CRN-2** | Protection of user’s data that is sensitive and ensure compliance with privacy regulations, since the AIDP system saves historical interactions of its users. 
| **CRN-3** |Managing AI model updates and configuration by supporting configuration of AI model versions and API keys,and ensuring that model updates do not interrupt service availability  
| **CRN-4** |Having cloud-based architecture patterns(auto-scaling, message queues, etc.) to improve scalability and availability 
| **CRN-5** |Maintaining intuitive, multi-language UI with fast responses (<2 s) during input and output, focusing on usability and accessibility across devices

---

## Step 2: Establish Iteration Goal by Selecting Drivers

### Iteration Goal
**Establish an overall system structure for AIDAP.**

The goal of this iteration is to establish an overall system structure for AIDAP. This will also go a long way to achieving CRN-1 which focuses on maintaining the multiple integrated external systems and other data resources, thus it will be our main iteration goal.
In the design of the overall system is the architecture should be mindful of : 
  -all the selected Quality Attribute Scenario drivers : QA-1, QA-2, QA-3, QA-4, QA-6, QA-8
  -all the selected Use Case drivers: UC-1, UC-2, UC-3, UC-5
  -CON-1: The AIDAP platform must connect securely with existing university systems such as registration, calendar, and LMS using                 standard REST or GraphQL APIs to exchange data in real time.
  -CON-3: The system has to be deployed on a cloud environment to allow flexible scaling and continuous availability, even when updates           or patches are applied.
   -CON-7: The system must automatically perform backups of user interactions, model configurations, and system data to ensure recovery            in case of a failure.

**CONTEXT DIAGRAM:**

---

## Step 3: Choose Element to Refine

Since AIDAP is a **greenfield system**, the element to refine in Iteration 1 is:

> **The entire AIDAP system as one coarse-grained architectural element.**

This refers to the whole AIDAP system that entails user workstations and the external university data source systems. In this case, refinement is performed through decomposition of the overall AIDAP system into its major client-side, server-side, and integration elements.


---

## Step 4: Choose Design Concepts

| **Design Decision** | **Rationale** |
|---------------------|---------------|
| Logically structure the client part of the system using a Rich Internet Application (RIA) reference architecture | This decision centralizes access control, SSO authentication, routing, and rate limiting, ensuring consistent enforcement of system security policies. It supports the need for high availability and performance under load by optimizing request handling and reducing unnecessary communication to downstream services. This architectural choice also simplifies integration with external university systems by providing a stable facade for all client interactions. |
| Logically structure the server part of the system using a Service Application reference architecture | Service applications do not provide a UI; they expose services consumed by clients. This fits AIDAP’s layered backend where Conversation, Dashboard, Course Management, and Admin services are accessed via HTTP APIs. It supports QA-4 (modifiability) and QA-8 (interoperability). |
| Physically structure the application using a three-tier deployment pattern (client–app–data) | A three-tier deployment fits the use of web and mobile clients (CON-2), a separate application tier (cloud compute), and a managed database tier (CON-3). It leaves room for later replication of the app and data tiers to address QA-2 and QA-6. |
| Introduce an API Gateway at the edge of the application tier | Centralizes SSO token validation (CON-2), RBAC enforcement (CON-6), rate limiting, and logging (QA-3, CRN-2). It also simplifies versioning and routing between services, helping QA-4 and QA-8. |
| Use adapter components for each external academic system | A cloud-native deployment model satisfies the system constraints for continuous availability and flexible scaling as demanded by academic activity cycles. It enables rapid provisioning of resources during peak load events, minimizing response times and downtime. This model also improves resilience through built-in monitoring, health checks, and multi-zone redundancy. |
| Plan to use auto-scaling and stateless services for core application components | Stateless micro-services (Conversation, Dashboard, Course Management, Admin) are easier to scale horizontally, addressing QA-1 (under load) and QA-6 (scalability spikes). |
| Separate an AI Orchestrator component from conversational and business services | Keeps AI model integration concerns isolated, allowing CRN-3 (model upgrades) and QA-4 (modifiability) to be handled cleanly without impacting most of the system. |

---
**Discarded Alternatives:**
| **Alternative** | **Rationale** |
|-----------------|---------------|
| Traditional Web Application (server-rendered HTML only) | Would simplify deployment but make it harder to deliver rich, interactive chat and dashboard UIs comparable to RIA frameworks; less aligned with long-lived conversational UI needed for UC-1 and multi-panel dashboards in UC-2. |
| Thick Desktop Client | Not aligned with CON-2 (web access, multi-OS environment) and institutional deployment realities; increases maintenance complexity compared to browser-based clients. |
| Mobile-only Application | Would not cover lecturers and administrators who are expected to use desktops or laptops; dashboards and admin consoles would require large screens. |


## Step 5: Instantiate Architectural Elements and Responsibilities

| **Design Decision and Location** | **Rationale** |
|----------------------------------|---------------|
| Remove local persistence in clients; treat them as “thin” in terms of data | Network connectivity in campus environments is generally reliable, and session state will be primarily maintained on the server side. This simplifies client updates and avoids data consistency issues. |
| Create a dedicated Integration layer in the Service Application architecture | Introduces LMSAdapter, SISAdapter, CalendarAdapter, EmailAdapter modules in the server data layer. These abstract external APIs and directly contribute to QA-8 and CON-1. |
| Introduce a separate AIOrchestrator module in the server business logic layer | Encapsulates logic for building prompts, choosing models, and merging external data into responses. This supports UC-1 and QA-4 and addresses CRN-3 by providing a clear point for model upgrades. |
| Introduce distinct backend services for Conversation, Dashboard, CourseManagement, and Admin | Splitting services along primary use cases supports QA-4 (modifiability) and prepares for later scaling decisions per service (QA-6). |

---

## Step 6: Sketch Views and Record Design Decisions
**MODULE VIEW DIAGRAM:**

### Table 6.1 – Module and Layer Descriptions

| Element              | Responsibility |
|----------------------|----------------|
| Client Side          | Represents the user-facing side of the system, running in the browser or client application. Hosts the presentation and client-side business logic. |
| Presentation-CS      | Client-side package that contains all UI components shown to the user. It renders chat, dashboards, and administration views. |
| ChatUI               | Provides the chat interface where users can view conversation history and enter new messages. |
| DashboardUI          | Displays dashboard information such as summaries, alerts, and key learning metrics for the user. |
| CourseAdminUI        | Offers screens for course administrators and instructors to manage course details and related actions. |
| AdminConsoleUI       | Presents administrative tools for managing system-level settings, users, and policies. |
| BusinessLogic-CS     | Client-side controllers that coordinate user actions from the UI, perform lightweight logic, and call the `APIClient`. |
| ChatController       | Handles events from `ChatUI`, prepares chat requests, and sends them to the server-side conversation APIs via the `APIClient`. |
| DashboardController  | Manages user interactions in `DashboardUI`, requesting updated dashboard data from the server. |
| CourseAdminController| Coordinates actions from `CourseAdminUI` such as viewing or updating course information and enrollment status. |
| AdminController      | Handles operations triggered from `AdminConsoleUI`, such as managing users and policies, and forwards them to admin services. |
| Data-CS              | Client-side data access package that encapsulates communication with the backend services. |
| APIClient            | Generic HTTP client on the client side. Manages API calls to the server (URLs, headers, authentication, error handling) so controllers don’t deal with protocol details. |
| Server Side          | Hosts all backend logic, APIs, integrations, and domain entities for the system. |
| Services-SS          | Package that exposes REST APIs to the client. Each API module focuses on a specific functional area. |
| ConversationAPI      | REST endpoints for sending messages, retrieving conversation history, and managing conversation sessions. |
| DashboardAPI         | REST endpoints that return aggregated dashboard data and related metrics. |
| CourseAdminAPI       | REST endpoints for course-administration operations such as course updates and enrollment management. |
| AdminAPI             | REST endpoints that support administrative operations like user and policy management. |
| BusinessLogic-SS     | Package that implements the core server-side business rules and workflows. |
| ConversationService  | Applies conversation logic, coordinates AI assistance via the `AIOrchestrator`, and persists conversation data. |
| DashboardService     | Aggregates data from domain entities and external systems to build the dashboard responses returned by `DashboardAPI`. |
| CourseManagementService | Implements course-related workflows such as creating courses, updating details, and managing enrollments. |
| AdminService         | Implements administrative workflows including user management, role changes, and policy updates. |
| AIOrchestrator       | Coordinates calls to AI models/LLMs, enriches prompts with relevant context, and returns processed responses to the services. |
| Integration/Data-SS  | Package containing adapters and repository components used to integrate with external institutional systems and the database. |
| SISAdapter           | Connects to the Student Information System (SIS) to read and update enrollment and registration data used by the services. |
| CalendarAdapter      | Integrates with institutional calendar services to read and modify scheduled events such as deadlines or reminders. |
| LMSAdapter           | Integrates with the Learning Management System (LMS) to retrieve course content, grades, and learning-related information. |
| EmailAdapter         | Handles communication with email/notification services to send system-generated messages. |
| Repository           | Provides persistence operations for domain entities, hiding database-specific details from the services. |
| DomainEntities-SS    | Package that defines the core domain model used throughout backend logic and APIs. |
| User                 | Represents a user of the system (student, instructor, or administrator), including identity and role information. |
| Course               | Represents a course, including metadata such as title, schedule, and configuration. |
| Enrollment           | Models the relationship between users and courses, tracking which users are enrolled in which offerings. |
| Policy               | Represents configuration rules and policies that control how the system behaves (e.g., access rules, limits). |
| ConversationSession  | Represents an ongoing chat session between a user and the system, including timestamps and linkage to the user. |
| ConversationTurn     | Represents a single message exchange within a conversation session, including user input and system response. |


**DEPLOYMENT VIEW DIAGRAM:**

### Table 6.2 – Deployment Elements and Responsibilities

| Element                     | Responsibility |
|-----------------------------|----------------|
| Client Tier                 | The user’s device (e.g., browser or mobile client) that runs the client-side UI and sends HTTPS requests to the web/app tier. |
| Web/App Tier                | Hosts the load balancer and application servers. Delivers web content, executes application logic, and coordinates calls to the database and external systems. |
| Load Balancer               | Entry point for client traffic. Distributes incoming HTTPS requests across the available AIDAP application servers. |
| AIDAP App Server 1          | Application server instance that runs the APIs, business logic, and integration components for handling user requests. |
| AIDAP App Server 2          | Second application server instance providing redundancy and horizontal scaling for the backend. |
| Object Storage              | Service used by the app servers to store and retrieve large or unstructured artifacts (e.g., documents, logs, exports). |
| Database Tier               | Dedicated tier for persistent data storage used by the system. |
| DB Cluster                  | Clustered database that stores domain entities, conversation history, user data, and configuration. |
| External Systems            | Logical grouping of institutional and support systems that the web/app tier communicates with. |
| Cache                       | External caching system used to store frequently accessed data and improve response times. |
| LMS                         | Institutional Learning Management System that provides course and learning-related information. |
| SIS                         | Student Information System that manages official enrollment and registration data. |
| Calendar/Email              | External services used for managing calendar events and sending email or notification messages. |


### Table 6.3 – Deployment Relationships

| Relationship                                      | Description |
|--------------------------------------------------|-------------|
| Between client tier and web/app tier             | Communication uses HTTPS to send user requests from the user device to the load balancer and application servers, and to deliver responses back to the client. |
| Between web/app tier and database tier           | Application servers communicate with the DB Cluster using database protocols (e.g., JDBC/SQL) to read and write persistent data. |
| Between web/app tier and external systems        | Application servers access external systems (Cache, LMS, SIS, Calendar/Email) using REST APIs or service-specific endpoints to retrieve and update institutional data. |

## Step 7: Analysis of Current Design (Kanban Progress)

The table below summarizes the progress made in Iteration 1 toward addressing all drivers (use cases, quality attributes, constraints, and architectural concerns).  
As expected for Iteration 1, most drivers are **partially addressed**, because this iteration focuses on establishing the overall system structure.

| **Item** | **Not Addressed** | **Partially Addressed** | **Completely Addressed** | **Design Decisions Made During Iteration** |
|----------|--------------------|--------------------------|---------------------------|---------------------------------------------|
| **UC-1** |                    |        ✓                    |                        | Selection of RIA + Service Application architecture; introduction of ConversationService and AIOrchestrator to support conversational flows. |
| **UC-2** |                    |         ✓                  |                         | Introduction of DashboardService and module view that aggregates data via adapters. |
| **UC-3** |                    |           ✓                |                         | Introduction of CourseManagementService and separation of client/server layers to support lecturer workflows. |
| **UC-5** |                    |               ✓            |                        | Introduction of CourseManagementService and separation of client/server layers to support lecturer workflows. |
| **QA-1** |          ✓           |                    |                           | No detailed performance tactics yet; stateless services and three-tier structure selected as base. |
| **QA-2** |                    | ✓                        |                           | Three-tier deployment with potential replication of app and data tiers; decisions on redundancy postponed to later iterations. |
| **QA-3** |                    |      ✓                     |                    | Introduction of API Gateway with SSO and RBAC enforcement; admin console separated from student UI. |
| **QA-4** |                    |      ✓                     |                      | Use of layered services, adapters for external systems, and an AIOrchestrator module for model changes. |
| **QA-6** |                    |       ✓                    |                       | Stateless services and module boundaries identified; they can be independently scaled. |
| **QA-8** |                    |                       |      ✓                  | Explicit Integration layer with LMS/SIS/Calendar/Email adapters. |
| **CON-1 to CON-9** |          | ✓                        |                           | Architectural structure supports constraints (cloud, SSO, RBAC, secure APIs, separate admin paths). Detailed policies left for later iterations. |
| **CRN-1** |                   |                          | ✓                         | Overall system structure has been established. |
| **CRN-2 to CRN-5** |          | ✓                        |                           | Architecture supports future security, model, scalability, and UI decisions but does not fully specify them yet. |


### **Iteration Goal Status**
The goal of Iteration 1—**establishing the overall system structure for AIDAP**—has been successfully achieved.  
Most of drivers are **partially addressed**, which is appropriate for this stage of the ADD process. Further refinement and detailed behavior will be completed in Iteration 2.
