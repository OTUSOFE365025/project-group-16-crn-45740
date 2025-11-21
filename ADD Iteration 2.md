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


