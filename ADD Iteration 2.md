# Iteration 2

## Step 2: Establish Iteration Goal
The goal of this iteration is to address the general architectural concern of identifying structures to support primary functionality.

---

## Primary Functional Requirements

| Use Case | Reason |
|---------|--------|
| **UC-1: Student AI Assistant** | Core student-facing functionality; students ask academic questions and receive AI-generated personalized answers. |
| **UC-2: Academic Dashboard** | Central to system viewing needs; shows grades, deadlines, schedules, analytics. |
| **UC-3: Course Management** | Critical for lecturers; upload/update content and view analytics. |
| **UC-5: System Administration** | Important for institutional governance; admins manage policies, integrations, announcements. |

---

## Quality Attribute Scenarios (Prioritized)

| Scenario | Importance | Difficulty |
|----------|------------|------------|
| **QA-1: Performance** | High | High |
| **QA-2: Availability** | High | High |
| **QA-3: Security** | High | Medium |
| **QA-4: Modifiability** | High | Medium |
| **QA-5: Usability** | Medium | Medium |
| **QA-6: Scalability** | High | High |
| **QA-7: Reliability** | Medium | Medium |
| **QA-8: Interoperability** | High | Medium |
| **QA-9: Maintainability** | Medium | Medium |

**Selected for this iteration:**  
**QA-1, QA-3, QA-4, QA-8**

---

## Constraints (Selected as Drivers)

| ID | Constraint |
|----|------------|
| **CON-1** | Secure connection with university systems via REST or GraphQL. |
| **CON-2** | Mandatory SSO authentication for all users. |
| **CON-3** | Cloud deployment for flexible scaling and continuous availability. |
| **CON-4** | Mandatory data encryption at rest and in transit. |
| **CON-5** | AI assistant must respond within ~2 seconds under normal load. |
| **CON-6** | Role-based access; unauthorized attempts logged. |
| **CON-7** | Automatic backups of interactions, configs, and system data. |
| **CON-8** | Notifications must reach correct users in their chosen language. |
| **CON-9** | System performance/availability metrics tracked in real time. |

---

## Architectural Concerns (Drivers)

| ID | Concern |
|----|---------|
| **CRN-1** | Maintaining integrations across multiple external systems; ensuring consistency, synchronization, and recovery from downtime. |
| **CRN-2** | Protect sensitive user data and comply with privacy regulations. |
| **CRN-3** | Manage AI model updates/configuration safely without affecting availability. |
| **CRN-4** | Use cloud architecture patterns (auto-scaling, message queues) for scalability/availability. |
| **CRN-5** | Maintain intuitive, multilingual UI with fast ( < 2s ) response times. |

---

## Step 3: Choose Elements to Refine
The elements refined in this iteration are the modules in the **client** and **server layers** defined in Iteration 1 through the RIA architecture and Service Application pattern.

This iteration focuses on refining those modules and identifying collaboration patterns that satisfy the main functional use cases.

---

## Step 4: Choose Design Concepts to Satisfy Drivers

### **Design Decision:** Create a Domain Model
- Identifies entities such as **User**, **Course**, **Enrollment**, **ConversationSession**, etc.
- Prevents ad-hoc architecture and ensures maintainability.

### **Design Decision:** Identify Domain Objects mapping to UC-1, UC-2, UC-3, UC-5
- Each functional area becomes a conceptual domain object.
- Supports clear responsibility boundaries.

### **Design Decision:** Decompose Domain Objects Across Layers
- Domain → UI → Business Logic → Data/Integration.
- Improves modularity, testability, and clarity.

### **Design Decision:** Use Dependency Injection / IoC
- Supports QA-9 (maintainability) and QA-4 (modifiability).
- Allows swapping AI models or integration sources easily.

---

## Step 5: Instantiate Architectural Elements & Define Interfaces

### **Domain Model (Central Domain Layer)**
Justification:
- Required foundation for stable architecture.
- FCAPS methodology mandates domain modeling before decomposition.

### **Domain Objects per Use Case**
Examples:
- UC-1 → *ConversationSession*, *ConversationTurn*
- UC-2 → *Enrollment*, *Course*, *Announcement*
- UC-3 → *Course*, *Assessment*, *Content*
- UC-5 → *Policy*, *ModelConfig*

### **Backend Modules Grouped by Use Case**
- **ConversationService**
- **DashboardService**
- **CourseManagementService**
- **AdminService**

### **Domain-Level Managers**
- **AIOrchestrator**
- **PolicyManager**
- **IntegrationManager**
- **ModelManager**

### **Integration Layer Modules**
- **LMSAdapter**
- **SISAdapter**
- **CalendarAdapter**
- **EmailAdapter**

These satisfy **CON-1**, **QA-8**, and CRN concerns.

### **Interfaces Identified Using Sequence Diagrams**
(Not included due to removal of images)

Examples include:
- `generateReply()`
- `postTurn()`
- `findEnrollments()`
- `updateLmsConfig()`

---

## Step 6: Views and Design Decisions
*(All diagrams removed as requested)*  
This step included:
- Domain Model  
- Domain Objects per Use Case  
- Module View  
- UC1, UC2, UC3, UC5 Sequence Diagrams  

All content references remain but visuals are omitted.

---

## Step 7: Coverage Table (Iteration 2)

| Status | Item | Design Decisions Made |
|--------|-------|------------------------|
| **Completely Addressed** | UC-1 | Modules across layers (ChatUI, ChatController, APIClient, ConversationAPI, ConversationService, AIOrchestrator, LMSAdapter, ConversationRepository). Interfaces identified. |
| **Completely Addressed** | UC-2 | Dashboard modules identified (DashboardUI, DashboardController, DashboardService, repositories). |
| **Completely Addressed** | UC-3 | CourseAdmin modules + repositories identified, responsibilities scoped. |
| **Completely Addressed** | UC-5 | AdminConsole, AdminService, PolicyManager, IntegrationManager, ModelManager identified. |
| **Partially Addressed** | QA-1 | Key modules identified; caching & concurrency tactics to be added later. |
| **Not Addressed** | QA-2 | Redundancy to be handled in a later iteration. |
| **Completely Addressed** | QA-3 | Enforcement points identified across APIs; RBAC paths clarified. |
| **Completely Addressed** | QA-4 | Domain model + modular decomposition supports modifiability. |
| **Completely Addressed** | QA-6 | Stateless services and boundaries support independent scaling. |
| **Completely Addressed** | QA-8 | Integration layer with specific adapters fully defined. |
| **Partially Addressed** | CON-1 → CON-9 | Most constraints supported; detailed mechanisms deferred. |
| **Partially Addressed** | CRN-1 → CRN-5 | Architecture aligned; runtime tactics (circuit breakers, autoscaling) deferred to next iteration. |

---

