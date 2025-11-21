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

| ID | Quality Attribute | Reason |
|----|------------------|--------|
| **QA-1: Performance** | Must handle 5,000+ concurrent queries with \< 2s latency. |
| **QA-2: Availability** | System must recover from node failure within 30 seconds (≈99.5% uptime). |
| **QA-3: Security** | SSO authentication, audit logging, and protected student data. |
| **QA-4: Modifiability** | Add new data sources or AI models in \< 2 days of effort. |
| **QA-6: Scalability** | Auto-scale to 10,000 sessions during peak periods. |
| **QA-7: Reliability** | Must provide cached/fallback data when LMS is unavailable. |

---

### 1.4 Constraints (All Are Drivers)

| ID | Constraint |
|----|------------|
| **CON-1** Must integrate with LMS/Registration systems using REST/GraphQL APIs. |
| **CON-2** Must use institution’s SSO for authentication. |
| **CON-3** Must be deployed on a cloud environment. |
| **CON-4** All data must be encrypted in transit and at rest. |
| **CON-5** System must maintain \< 2s average response time under normal load. |
| **CON-6** Only authorized users can access protected data; all access must be logged. |
| **CON-7** Automatic backups must be enabled for interactions, configurations, and data. |
| **CON-8** Notifications must respect user preferences and language settings. |
| **CON-9** Real-time system monitoring must be available to admins/maintainers. |
| **CON-10** UI must be responsive across web/mobile and meet accessibility standards. |

---

### 1.5 Architectural Concerns

| ID | Concern |
|----|---------|
| **CRN-1** Integrating multiple external systems and ensuring data consistency and fault recovery. |
| **CRN-2** Protecting sensitive user data and complying with privacy regulations. |
| **CRN-3** Managing AI model versions and updates without impacting availability. |
| **CRN-4** Using cloud-based patterns (auto-scaling, load balancing, messaging) to improve scalability and availability. |
| **CRN-5** Maintaining a multilingual responsive UI with fast interactions and good usability. |

---

## Step 2: Establish Iteration Goal by Selecting Drivers

### Iteration Goal
**Establish an overall system structure for AIDAP.**

This iteration focuses on:
- Selecting reference architectures
- Identifying main tiers and components
- Designing cloud-native deployment structure
- Ensuring support for early drivers: availability, scalability, security, integration, and performance

### Most Relevant Drivers for This Iteration

- **Quality Attributes:** QA-1, QA-2, QA-3, QA-4, QA-6, QA-7  
- **Constraints:** CON-1, CON-2, CON-3, CON-5  
- **Concerns:** CRN-1, CRN-2, CRN-3, CRN-4  

These strongly influence technology and structural choices.

---

## Step 3: Choose Element to Refine

Since AIDAP is a **greenfield system**, the element to refine in Iteration 1 is:

> **The entire AIDAP system as one coarse-grained architectural element.**

Later iterations will refine specific components and use cases.

---

## Step 4: Choose Design Concepts

### 4.1 Reference Architecture

**Chosen:** **Service-Based Architecture (Microservices)**

**Rationale:**
- Supports **autoscaling** of individual services (QA-6).
- Enables independent deployment and updates for AI, notifications, dashboards (QA-4, CRN-3).
- Provides fault isolation to improve **availability** (QA-2).
- Aligns with **cloud deployment** requirement (CON-3, CRN-4).
- Clear boundaries for integration services working with external systems (CON-1, CRN-1).

**Discarded Alternatives:**

| Alternative | Reason Discarded |
|------------|------------------|
| **Monolithic Web Application** | Poor scalability for 5,000–10,000 users; tightly coupled integrations; harder to evolve AI components. |
| **Desktop Rich Client Application** | Not suitable for multi-device, browser-based access required by students and staff (CON-10). |
| **Pure Serverless Architecture** | Cold start delays can hurt 2s latency requirement (QA-1); complex coordination for long-running AI tasks. |

---

### 4.2 Deployment Pattern

**Chosen:** **Cloud-native 3-tier architecture with autoscaling**

**Tiers:**
1. **Presentation Tier:** Web and mobile clients (browser, mobile app).
2. **Application Tier:** Microservices (API Gateway, AI, Dashboard, Notifications, etc.).
3. **Data Tier:** Databases, caches, backup storage, object storage.

**Rationale:**
- Enforces separation of concerns.
- Allows independent scaling of UI, services, and data layers.
- Fits naturally with cloud load balancers and managed databases (CON-3, CRN-4).

---

### 4.3 Supporting Design Concepts

| Concept | Purpose / Driver |
|--------|-------------------|
| **API Gateway** | Single entry point; handles SSO integration, routing, rate limiting, request validation (CON-1, CON-2, QA-3). |
| **Message Queue (e.g., Kafka/SQS)** | Handles asynchronous jobs such as notifications, logging, heavy AI tasks (QA-1, QA-2, QA-6). |
| **Caching Layer (Redis/Memcached)** | Reduces latency and provides cached data when external systems are down (QA-1, QA-7). |
| **Load Balancer** | Distributes requests across service instances, improving availability and scalability (QA-2, QA-6). |
| **Central Logging & Monitoring** | Exposes metrics to admins/maintainers, supports error diagnosis (CON-9, UC-6). |

---

## Step 5: Instantiate Architectural Elements and Responsibilities

### 5.1 Client Tier (Presentation Layer)

| Component | Responsibilities |
|----------|------------------|
| **Web/Mobile UI** | Provides conversational interface, dashboards, and management screens for all roles. |
| **Input/Voice Handler** | Captures text/voice input, supports multi-language, packages requests for the backend. |
| **SSO Authentication UI** | Redirects to university SSO, handles login/logout and token retrieval. |

---

### 5.2 Application Tier (Backend Microservices)

| Component | Responsibilities |
|----------|------------------|
| **API Gateway** | Authenticates requests via SSO tokens, enforces rate limiting, routes requests to internal services, performs basic validation. |
| **AI Processing Service** | Interprets natural-language queries, calls AI models, applies context from user history, returns structured responses. |
| **User Profile Service** | Manages user profiles, preferences (language, notifications), and conversation history. |
| **Dashboard Service** | Aggregates and formats deadlines, grades, upcoming events, and analytics for the Academic Dashboard (UC-2). |
| **Course Management Service** | Supports lecturers in uploading, updating, and managing course materials and analytics (UC-3). |
| **Integration Service** | Connects to external LMS, Registration, Calendar, and Email systems via REST/GraphQL (CON-1, CRN-1). |
| **Notification Service** | Sends notifications and announcements based on user preferences and language (UC-4, CON-8). |
| **Monitoring & Logging Service** | Collects metrics, audit logs, error reports; exposes dashboards for maintainers (UC-6, CON-9, QA-3). |
| **Model Management Service** | Manages AI model versions, configuration, and rollout with minimal downtime (CRN-3, QA-4). |

---

### 5.3 Data Tier

| Component | Responsibilities |
|----------|------------------|
| **Cloud Database (SQL/NoSQL)** | Stores users, roles, preferences, interactions, dashboard data, and configuration. |
| **Object Storage** | Stores large artifacts such as course files, exported reports, and media. |
| **Redis Cache** | Caches frequently accessed data (e.g., schedules, grades) and holds fallback information when external systems are unavailable (QA-7). |
| **Backup Storage** | Holds automatic backups of database and configuration data to support disaster recovery (CON-7). |

---

## Step 6: Sketch Views and Record Design Decisions

### Table 6.1 – Module View Elements and Responsibilities

| Element | Layer | Responsibilities |
|---------|--------|------------------|
| **Client Tier** | — | Container for all user-facing applications (web/mobile). Sends requests to backend and renders responses. |
| **Student Web Client** | Client | Browser UI for students. Provides AI assistant, academic dashboard, and basic course views. |
| **Lecturer Web Client** | Client | Browser UI for lecturers. Access to course dashboards and management screens based on role. |
| **Admin Console** | Client | Web UI for system administrators to configure integrations, policies, and model settings. |
| **Mobile Client (optional)** | Client | Mobile interface for students to access chat and dashboard. Uses same backend APIs. |
| **API Gateway** | Application | Entry point for client requests. Validates SSO tokens, enforces RBAC, rate limits, logs requests, and routes to backend services. |
| **Conversation Service** | Application | Manages AI assistant flows: sessions, turns, calls AI Orchestrator, composes replies. Supports UC-1. |
| **Dashboard Service** | Application | Aggregates academic data for dashboard view (courses, assignments, announcements). Supports UC-2. |
| **Course Management Service** | Application | Lecturer operations: posting announcements, managing course content. Supports UC-3. |
| **Admin Service** | Application | System admin functionality: integrations, policies, model settings. Supports UC-5. |
| **AI Orchestrator** | Application | Handles prompt creation, model selection, inference, external data merging. |
| **Notification Service (planned)** | Application | Sends notifications based on events and preferences. |
| **LMS Adapter** | Integration | Wraps LMS API. Translates calls between AIDAP and LMS. |
| **SIS Adapter** | Integration | Interfaces with Registration/SIS system for enrolment/records. |
| **Calendar/Email Adapter** | Integration | Connects to institutional messaging systems for notifications. |
| **Operational Database** | Data | Stores domain data: users, profiles, conversations, course data, policies, configs. |
| **Analytics Database** | Data | Stores aggregated/historical data for reporting and metrics. |
| **Cache Store** | Data | In-memory cache for performance (e.g., dashboard and conversation context). |
| **Object Storage** | Data | Stores large binary artifacts (attachments, exports). |
| **Backup Storage** | Data | Long-term backups and retention for databases and objects. |

### Table 6.2 – Deployment Elements and Responsibilities

| Deployment Element | Responsibilities |
|--------------------|------------------|
| **User Device (Browser / Mobile App)** | Renders UI, handles user input, sends HTTPS requests to backend. |
| **Public Network / Internet** | Secure transport layer (HTTPS/TLS) between client and cloud. |
| **Load Balancer / API Entry Node** | Routes traffic to AIDAP app servers, terminates TLS, handles blue/green rollouts. |
| **AIDAP App Server (xN, stateless)** | Hosts application tier: API Gateway, services, AI Orchestrator, integration adapters. Scales horizontally. |
| **Managed Database Cluster** | Durable, replicated storage of operational domain data. |
| **Cache Node(s)** | Distributed in-memory cache for performance. |
| **Object Storage Service** | Stores large assets (documents, attachments, exports). |
| **Backup / Archival Service** | Long-term storage of backups for disaster recovery. |
| **External LMS System** | Source of course materials, grades, assignments; accessed via LMS Adapter. |
| **External Registration / SIS System** | Provides enrolment and student information via SIS Adapter. |
| **External Calendar / Email System** | Delivers notifications, email, and event pushes. |

### Table 6.3 – Summary of Key Relationships Between Elements

| From Element | To Element | Relationship / Interaction |
|--------------|------------|-----------------------------|
| Student / Lecturer / Admin Clients | API Gateway | Sends HTTPS requests for chat, dashboard, course mgmt, and admin actions. |
| API Gateway | Backend Services | Validates requests, enforces RBAC, routes to proper service. |
| Backend Services | Operational Database | Reads/writes domain data (users, courses, conversations, announcements, policies). |
| Backend Services | Cache Store | Retrieves and stores frequently accessed data to improve performance. |
| Backend Services | Integration Adapters | Calls external systems securely (LMS, SIS, Calendar/Email). |
| Integration Adapters | External Systems | Executes institution’s official API calls and returns normalized data. |
| App Servers | Analytics DB / Object Storage / Backups | Sends logs, metrics, large objects, and backup data. |
| Load Balancer | App Servers | Distributes requests across stateless app instances. |

## Step 7: Analysis of Current Design (Kanban Progress)

The table below summarizes the progress made in Iteration 1 toward addressing all drivers (use cases, quality attributes, constraints, and architectural concerns).  
As expected for Iteration 1, all drivers are **partially addressed**, because this iteration focuses on establishing the overall system structure.

| Driver Type | Driver ID | Description | Not Addressed | Partially Addressed | Fully Addressed | Notes |
|-------------|-----------|-------------|---------------|---------------------|------------------|-------|
| **Use Case** | UC-1 | Student AI Assistant |  | ✓ |  | Supported by AI Service + API Gateway; detailed logic refined in later iterations. |
| **Use Case** | UC-2 | Academic Dashboard |  | ✓ |  | Dashboard Service included; aggregation logic to be refined later. |
| **Use Case** | UC-3 | Course Management |  | ✓ |  | Course Management Service defined; workflows refined in later iterations. |
| **Use Case** | UC-4 | Notifications |  | ✓ |  | Notification Service identified; rules and event triggers defined later. |
| **Use Case** | UC-5 | System Administration |  | ✓ |  | Admin functions supported through Gateway, Monitoring, Integration Services. |
| **Use Case** | UC-6 | System Maintenance |  | ✓ |  | Logging and Monitoring Service addresses maintainer needs. |
| **Quality Attribute** | QA-1 | Performance |  | ✓ |  | Autoscaling, caching, and async processing reduce latency. |
| **Quality Attribute** | QA-2 | Availability |  | ✓ |  | Multi-instance deployment + load balancing planned. |
| **Quality Attribute** | QA-3 | Security |  | ✓ |  | SSO, API Gateway, encryption planned; enforcement refined later. |
| **Quality Attribute** | QA-4 | Modifiability |  | ✓ |  | Microservices enable independent updates. |
| **Quality Attribute** | QA-6 | Scalability |  | ✓ |  | Cloud-native autoscaling supports growth. |
| **Quality Attribute** | QA-7 | Reliability |  | ✓ |  | Redis fallback + retry logic for external service failures. |
| **Constraint** | CON-1–10 | All system constraints |  | ✓ |  | All constraints addressed in high-level architecture. |
| **Concern** | CRN-1–5 | All architectural concerns |  | ✓ |  | Integration, security, model updates, scalability, UI concerns considered. |

### **Iteration Goal Status**
The goal of Iteration 1—**establishing the overall system structure for AIDAP**—has been successfully achieved.  
All drivers are **partially addressed**, which is appropriate for this stage of the ADD process. Further refinement and detailed behavior will be completed in Iteration 2.
