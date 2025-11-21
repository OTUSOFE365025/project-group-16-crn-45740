# Iteration 2:

## Step 2: Establish Iteration Goal
The goal of this iteration is to address the general architectural concern of identifying structures to support primary functionality.

---

## Primary Functional Requirements

The primary use cases were determined to be:

| Primary Functional Requirements | Details |
|--------------------------------|---------|
| **UC-1: Student AI Assistant** | Because it is a core service for student use. Students ask academic questions using AI and receive personalized answers. |
| **UC-2: Academic Dashboard** | Because it is a core functionality for the system view. Displays grades, deadlines, schedules, and analytics. |
| **UC-3: Course Management** | Because it is a core functionality for lecturer & faculty operations. Lecturers upload/update course content and view analytics. |
| **UC-5: System Administration** | Because it is important for institutional governance. Administrators define policies, manage integrations, and oversee announcements. |

---

## Quality Attribute Scenarios

The scenarios have been prioritized as follows:

| Scenario | Importance to the Customer | Difficulty of Implementation according to the Architect |
|----------|----------------------------|----------------------------------------------------------|
| **QA-1: Performance** | High | High |
| **QA-2: Availability** | High | High |
| **QA-3: Security** | High | Medium |
| **QA-4: Modifiability** | High | Medium |
| **QA-5: Usability** | Medium | Medium |
| **QA-6: Scalability** | High | High |
| **QA-7: Reliability** | Medium | Medium |
| **QA-8: Interoperability** | High | Medium |
| **QA-9: Maintainability** | Medium | Medium |

From this list, only are selected as the prioritized QA’s for this iteration:  
**QA-1, QA-3, QA-4, QA-8**

---

## Constraints

The following constraints have been selected as drivers:

| Constraint ID | Details |
|---------------|---------|
| **CON-1** | The AIDAP platform must connect securely with existing university systems such as registration, calendar, and LMS using standard REST or GraphQL APIs to exchange data in real time. |
| **CON-2** | All users are required to authenticate through the institution’s Single Sign-On (SSO) before using any of the assistant’s features. |
| **CON-3** | The system has to be deployed on a cloud environment to allow flexible scaling and continuous availability, even when updates or patches are applied. |
| **CON-4** | User and institutional data must be encrypted during storage and transmission, and handled according to the university’s privacy and security standards. |
| **CON-5** | The AI assistant should respond to user queries with an average latency of around 2 seconds under normal load. |
| **CON-6** | Only authorized users can access data relevant to their roles (student, lecturer, or admin), and unauthorized attempts must be logged for auditing. |
| **CON-7** | The system must automatically perform backups of user interactions, model configurations, and system data to ensure recovery in case of a failure. |
| **CON-8** | Notifications, alerts, and announcements must reach the correct users based on their preferences and chosen language. |
| **CON-9** | System performance and availability metrics must be tracked in real time and displayed on a dashboard accessible only to maintainers and administrators. |

---

## Architectural Concerns

The following concerns have been selected as drivers:

| Concern ID | Details |
|-------------|---------|
| **CRN-1** | Maintaining multiple integrated external systems and other data resources , along with ensuring data consistency,synchronization and fault recovery when the systems experience downtime. |
| **CRN-2** | Protection of user’s data that is sensitive and ensure compliance with privacy regulations, since the AIDP system saves historical interactions of its users. |
| **CRN-3** | Managing AI model updates and configuration by supporting configuration of AI model versions and API keys,and ensuring that model updates do not interrupt service availability. |
| **CRN-4** | Having cloud-based architecture patterns(auto-scaling, message queues, etc.) to improve scalability and availability. |
| **CRN-5** | Maintaining intuitive, multi-language UI with fast responses (<2 s) during input and output, focusing on usability and accessibility across devices. |

---
## Step 3: Choose One or More Elements of the System to Refine

The elements refined in this iteration are the modules located in the client and server layers defined by the RIA architecture and the Service Application pattern from Iteration 1. This iteration focuses on refining the modules within them and identifying how they collaborate to support the system's primary functionality through the chosen Use Cases.

---

## Step 4: Choose One or More Design Concepts That Satisfy the Selected Drivers

| Design Decisions and Location | Rationale and Assumptions |
|------------------------------|----------------------------|
| **Create a Domain Model for the AIDAP application** | Before functional decomposition, we identify the main entities (User, Course, ConversationSession, etc.) and their relationships so that modules can be organized around them. Without an explicit domain model, the architecture would become ad hoc and harder to maintain. |
| **Identify Domain Objects that map to the functional requirements (UC-1, UC-2, UC-3, UC-5)** | Each distinct functional element (e.g., “Conversational Help”, “Dashboard Aggregation”, “Course Management”, “System Configuration”) is encapsulated into a **domain object** that owns core responsibilities. |
| **Decompose Domain Objects into components across layers** | Domain objects represent coherent sets of functionality; they must be realized by finer-grained modules in UI, business logic, and data layers. This decomposition allows clear responsibilities and testable implementation units. |
| **Implement dependency injection / IoC to connect components** | Enables unit-testing of services (supporting QA-9) and easier substitution of components (e.g., different AI models or external systems) without wide-ranging code changes, supporting QA-4. |

---

## Step 5: Instantiate Architectural Elements, Allocate Responsibilities, and Define Interfaces

| Design Decisions and Location | Rationale |
|------------------------------|-----------|
| **Create a Domain Model for AIDAP (Central Domain Layer)** | The domain model is required to identify and stabilize the main entities of the system (User, Course, Enrollment, ConversationSession, etc.). Without a domain model, the architecture becomes ad hoc and inconsistent. The FCAPS example mandates domain modeling before modular decomposition. |
| **Identify Domain Objects that map directly to UC-1, UC-2, UC-3, and UC-5** | Each primary use case involves distinct “conceptual building blocks” (e.g., ConversationSession for UC-1; Enrollment for UC-2; Course for UC-3; Policy & ModelConfig for UC-5). Identifying domain objects clarifies boundaries, responsibilities, and supports CRN-5 (usable UI) and QA-4 (modifiability). |
| **Decompose domain objects across layers (Presentation, Business Logic, Domain, Integration/Data)** | Following FCAPS, domain objects do not directly become modules; they must be realized by components across layers. This provides clear responsibilities, improves testability (QA-9), and aligns services with UC-1, UC-2, UC-3, UC-5. |
| **Introduce dedicated domain-level managers (AIOrchestrator, PolicyManager, IntegrationManager, ModelManager)** | These managers encapsulate complex logic unique to AIDAP (model switching, integration configuration, policy enforcement). Supports QA-4 (modifiability), CRN-3 (AI evolution), and CON-1 (integration requirements). |
| **Group backend modules by primary use case → ConversationService, DashboardService, CourseManagementService, AdminService** | Mirrors FCAPS modular design. Reduces coupling by ensuring each service owns a well-defined responsibility that directly corresponds to a primary UC. Helps CRN-1 (overall system structure) and creates team boundaries (if needed). |
| **Define initial interfaces using UC-driven sequence diagrams** | Per FCAPS: sequence diagrams → interface extraction. Methods such as generateReply(), postTurn(), findEnrollments(), updateLmsConfig() become explicit API and service interfaces. Supports QA-4 (modifiability) and QA-8 (interoperability). |
| **Separate Integration Layer modules (LMSAdapter, SISAdapter, CalendarAdapter, EmailAdapter)** | Required to satisfy CON-1. Provides clean isolation between AIDAP and external systems. Supports QA-8 (interoperability) and QA-4 (ease of replacing/changing backends). |

---
## Step 6:
**DOMAIN MODEL:**


| Domain Entity        | Responsibility / Role                                                         |
|----------------------|--------------------------------------------------------------------------------|
| User                 | Represents a system user (student, lecturer, admin).                           |
| StudentProfile       | Student-specific identifiers and metadata.                                     |
| LecturerProfile      | Lecturer-specific identifiers and permissions.                                 |
| AdminProfile         | Admin-level identifiers for privileged actions.                                |
| Course               | Represents an academic course.                                                 |
| CourseSection        | Represents a section of a course.                                              |
| Enrollment           | Links students to course sections.                                             |
| Assignment           | Represents coursework, deadlines, grading items.                               |
| Announcement         | Represents course announcements posted by lecturers.                            |
| ConversationSession  | Represents a persistent chat session with AIDAP.                               |
| ConversationTurn     | Represents one message/response pair in the chat flow.                         |
| NotificationPreference | User preferences for notification delivery.                                  |
| Policy               | A system or governance rule.                                                   |
| ModelConfig          | AI model settings/configurations.                                              |
| DataSourceConfig     | LMS/SIS/Calendar/Email connection settings.                                    |
| IntegrationState     | (Optional) Health + status of external integrations.                           |

**MODULE VIEW:**


**Elements and Responsibilities:**

| Element                     | Layer                     | Responsibility Description                                                                 |
|-----------------------------|---------------------------|---------------------------------------------------------------------------------------------|
| ChatUI                      | Client – Presentation     | Displays chat interface; captures user input; shows responses. Supports UC-1.              |
| DashboardUI                 | Client – Presentation     | Displays academic dashboard: courses, deadlines, announcements. Supports UC-2.             |
| CourseAdminUI               | Client – Presentation     | UI for lecturers to manage course content. Supports UC-3.                                   |
| AdminConsoleUI              | Client – Presentation     | UI for admins to manage integrations, policies, models. Supports UC-5.                     |
| ChatController              | Client – Business Logic   | Coordinates ChatUI → APIClient calls; updates UI state.                                    |
| DashboardController         | Client – Business Logic   | Coordinates dashboard data retrieval.                                                       |
| CourseAdminController       | Client – Business Logic   | Coordinates actions like posting announcements.                                             |
| AdminController             | Client – Business Logic   | Coordinates admin functions via backend APIs.                                               |
| APIClient                   | Client – Communication    | Common HTTP client wrapper for backend service calls.                                       |
| ConversationAPI             | Server – Services         | REST endpoints for conversation operations.                                                 |
| DashboardAPI                | Server – Services         | REST endpoints for dashboard data.                                                          |
| CourseAdminAPI              | Server – Services         | REST endpoints for course management.                                                       |
| AdminAPI                    | Server – Services         | REST endpoints for admin configuration.                                                     |
| ConversationService         | Server – Business Logic   | Implements UC-1: manages sessions, turns, calls AIOrchestrator.                             |
| DashboardService            | Server – Business Logic   | Implements UC-2: aggregates course, assignment, announcement data.                          |
| CourseManagementService     | Server – Business Logic   | Implements UC-3: verifies lecturer access, manages course entities.                         |
| AdminService                | Server – Business Logic   | Implements UC-5: config management, policy enforcement, model management.                   |
| AIOrchestrator              | Server – Business Logic   | Builds prompts, queries adapters, calls AI model.                                           |
| PolicyManager               | Domain / Business         | Manages governance and access policies.                                                     |
| IntegrationManager          | Domain / Business         | Validates, stores, tests external integration config.                                       |
| ModelManager                | Domain / Business         | Stores and updates AI model configuration.                                                  |
| Domain Entities             | Domain Layer              | User, Course, Enrollment, ConversationSession, Announcement, etc.                           |
| LMSAdapter                  | Integration Layer         | Integrates with LMS via secure API.                                                         |
| SISAdapter                  | Integration Layer         | Integrates with student information system.                                                 |
| CalendarAdapter             | Integration Layer         | Integrates with calendar API.                                                               |
| EmailAdapter                | Integration Layer         | Sends or triggers notifications.                                                            |
| Repositories (various)      | Data Access Layer         | CRUD operations for domain entities.                                                        |


**USE CASE 1:Student AI Assistant**


| Element               | Method Signature                                     | Description                                              |
|-----------------------|------------------------------------------------------|----------------------------------------------------------|
| ChatController        | sendMessage(text)                                    | Sends user text to backend via APIClient.               |
| APIClient             | postTurn(sessionId, text) : ConversationTurnDTO       | Sends POST request to ConversationAPI.                  |
| ConversationAPI       | handlePostTurn(requestDTO)                           | Validates request → delegates to ConversationService.   |
| ConversationService   | handleUserTurn(sessionId, text)                      | Loads session, calls AI, stores turn.                  |
| AIOrchestrator        | generateReply(context) : AIReply                     | Builds prompt, queries adapters, calls model.           |
| LMSAdapter            | getUpcomingAssessments(studentId)                    | Gets deadlines to support UC-1 contextual answers.      |
| ConversationRepository| saveTurn(sessionId, turn)                             | Persists conversation turn.                             |
| ChatUI                | displayReply(ConversationTurnDTO)                    | Renders the AI/system reply to user.                   |


**USE CASE 2:Academic Dashboard**

| Element                | Method Signature                                     | Description                                              |
|------------------------|------------------------------------------------------|----------------------------------------------------------|
| DashboardController    | loadDashboard()                                      | Requests dashboard via APIClient.                       |
| APIClient              | getDashboard() : DashboardDTO                        | GET /dashboard/me → DashboardAPI.                       |
| DashboardAPI           | getPersonalDashboard() : DashboardDTO                | Validates user, delegates to DashboardService.          |
| DashboardService       | getPersonalDashboard(userId) : DashboardDTO          | Aggregates course, assignment, announcement data.       |
| EnrollmentRepository   | findByUser(userId)                                   | Gets enrolments.                                        |
| CourseRepository       | findCourses(enrollments)                             | Retrieves corresponding courses.                        |
| AssignmentRepository   | findUpcomingAssignments(courses)                     | Gets deadlines.                                         |
| AnnouncementRepository | findRecentAnnouncements(courses)                     | Gets announcements.                                     |
| LMSAdapter             | fetchLiveData(courses)                               | Optional live LMS enrichment.                           |
| DashboardUI            | renderDashboard(DashboardDTO)                        | Renders dashboard info.                                 |

**USE CASE3:Course Management**

| Element                 | Method Signature                                       | Description                                           |
|-------------------------|--------------------------------------------------------|-------------------------------------------------------|
| CourseAdminController   | postAnnouncement(courseId, text)                       | Sends announcement text via APIClient.               |
| APIClient               | postAnnouncement(courseId, text) : AnnouncementDTO     | POST to CourseAdminAPI.                               |
| CourseAdminAPI          | createAnnouncement(courseId, requestDTO)               | Validates lecturer permissions.                       |
| CourseManagementService | postAnnouncement(courseId, userId, text)               | Creates announcement + verifies access.               |
| CourseRepository        | verifyLecturerAccess(courseId, userId)                 | Authorization check.                                  |
| AnnouncementRepository  | save(Announcement) : Announcement                      | Stores new announcement.                              |
| NotificationService     | notifyEnrolledStudents(courseId, announcement)         | Sends notifications.                                  |
| CourseAdminUI           | showAnnouncement(AnnouncementDTO)                      | Updates UI with new announcement.                     |


**USE CASE 5:System Administration**

| Element                 | Method Signature                                        | Description                                           |
|-------------------------|---------------------------------------------------------|-------------------------------------------------------|
| AdminController         | updateLmsConfig(newConfig)                              | Triggers update from UI.                              |
| APIClient               | putLmsConfig(newConfig) : UpdateResultDTO               | PUT request → AdminAPI.                               |
| AdminAPI                | updateLmsConfig(requestDTO) : UpdateResultDTO           | Validates SSO + RBAC; delegates to AdminService.      |
| AdminService            | updateLmsConfig(newConfig) : UpdateResultDTO            | Coordinates config update & validation.               |
| IntegrationManager      | updateConfig(systemType, newConfig) : Result            | Validates + persists DataSourceConfig.                |
| DataSourceConfigRepository | save(config)                                         | Persists integration configuration.                   |
| LMSAdapter              | testConnection(newConfig) : TestResult                  | Tests new LMS connection settings.                    |
| AdminConsoleUI          | showResult(UpdateResultDTO)                             | Displays outcome to admin.                            |

---
## Step 7:

| Not Addressed | Partially Addressed | Completely Addressed | Design Decisions Made During Iteration |
|---------------|----------------------|------------------------|-----------------------------------------|
|               |                      | UC-1 | Modules across layers (ChatUI, ChatController, APIClient, ConversationAPI, ConversationService, AIOrchestrator, LMSAdapter, ConversationRepository) and preliminary interfaces identified via sequence diagram. |
|               |                      |      | Dashboard modules (DashboardUI, DashboardController, DashboardService, repositories) identified; sequence diagram and interfaces can be refined similarly. |
|               |    UC-2    | UC-3 | CourseAdmin modules and repositories identified; responsibilities outlined. |
|               |           UC-5        || AdminConsole, AdminService, PolicyManager, IntegrationManager, ModelManager, and associated repositories identified. |
|               | QA-1                 |       | Elements participating in UC-1 have been identified; caching and concurrency tactics can now be applied to ConversationService, DashboardService, and adapters. |
| QA-2          |                      |       | No new redundancy decisions; will be a focus of a later iteration (similar to FCAPS Iteration 3). |
|               |      QA-3       |  | Admin functionality is clearly separated; all service calls flow through APIClient and ConversationAPI/AdminAPI, which is where RBAC and auditing will be enforced. |
|               |                      | QA-4 | Domain model and domain-based decomposition into modules provide a clear structure for adding new features and changing models/adapters. |
|               |      QA-6          |  | Stateless services and module boundaries identified; they can be independently scaled. |
|               |                      | QA-8 | The integration layer with distinct adapters for each external system is now explicit. |
|               |                      | CON-1 | Integration layer and specific adapters (LMSAdapter, SISAdapter, CalendarAdapter, EmailAdapter) are explicitly part of the architecture and appear in UC-1, UC-2, and UC-5 flows. The design clearly shows where and how integration happens. |
|               | CON-2                |       | API Gateway is defined as the enforcement point, and all flows go through it, but detailed SSO protocol, token formats, and login UI are not fully specified yet. Architecture shows where SSO happens, not how exactly. |
|               |                      | CON-3 | Three-tier cloud deployment view, stateless app servers, managed DB, cache, and object storage are defined in Iteration 1 and reused in Iteration 2. From an architectural standpoint, this constraint is fully addressed. |
|               | CON-4                |       | We assume HTTPS for all external calls and managed DBs that support encryption at rest, but there is no explicit design yet for key management, certificate management, or encryption policies. Locations are clear; detailed mechanisms are still pending. |
|               | CON-5                |       | Performance is considered in the decomposition (stateless services, clear services per use case, adapters, etc.), and we know where to apply caching and performance tactics, but those tactics are not fully designed or tuned yet. |
|               | CON-6                |       | Sequence diagrams and module view show API-level enforcement points (ConversationAPI, DashboardAPI, CourseAdminAPI, AdminAPI) and a distinct AdminConsole. Roles and checks are implied, but detailed role matrix + logging pipeline are not fully described yet. |
|    CON-7       |                |       | We know there is a managed DB and storage, but no explicit design of backup schedules, retention configurations, or recovery workflows yet. This is deferred to a later, ops-focused iteration. |
|               | CON-8                |       | Domain entities (NotificationPreference) and NotificationService appear in the decomposition for UC-3 and future notification flows, but full notification flows and preference-check logic are not yet detailed. |
|   CON-9     |                 |       | We have logical places where monitoring could be attached (API Gateway, services, adapters), but no explicit monitoring modules, dashboards, or metrics design have been defined yet in Iteration 2. |
|               |                | CRN-1   | Integration reliability is partially supported by the Integration Layer, but retry logic, circuit breakers, and recovery strategies are not yet defined. |
|               | CRN-2                |       | Enforcement points for authorization and admin controls identified. Domain model clarifies sensitive entities (User, Grades, Enrollment. Not yet refined?) |
|               |    CRN-3      |  | ModelManager and AI Orchestrator interactions defined; UC-5 sequence allows safe model switching and configuration. Final runtime tactics appear later. |
|               | CRN-4                |       | Stateless module boundaries are identified; integration points decoupled. Actual deployment tactics (autoscaling groups, circuit breakers) appear next iteration. |
|               | CRN-5                |       | UIs distinguished cleanly by use case (ChatUI, DashboardUI, CourseAdminUI, AdminConsoleUI). Full usability tactics applied during UI design phase (outside ADD). |
