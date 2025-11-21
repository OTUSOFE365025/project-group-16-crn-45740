# Iteration 2:

## Step 2: Establish Iteration Goal

The goal of this iteration is to address the general architectural concern of identifying structures to support primary functionality.

---

## Primary Functional Requirements

The primary use cases were determined to be:

| Primary Functional Requirements | Details |
|--------------------------------|---------|
|  | • **UC-1: Student AI Assistant** | Because it is a core service for student use. Students ask academic questions using AI and receive personalized answers. <br> • **UC-2: Academic Dashboard** | Because it is a core functionality for the system view. Displays grades, deadlines, schedules, and analytics. <br> • **UC-3: Course Management** | Because it is a core functionality for lecturer & faculty operations. Lecturers upload/update course content and view analytics. <br> • **UC-5: System Administration** | Because it is important for institutional governance. Administrators define policies, manage integrations, and oversee announcements. |

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



---
## Constraints

The following constraints have been selected as drivers:

| Constraints | Details |
|-------------|---------|
|  | • **CON-1** | The AIDAP platform must connect securely with existing university systems such as registration, calendar, and LMS using standard REST or GraphQL APIs to exchange data in real time. <br> • **CON-2** | All users are required to authenticate through the institution’s Single Sign-On (SSO) before using any of the assistant’s features. <br> • **CON-3** | The system has to be deployed on a cloud environment to allow flexible scaling and continuous availability, even when updates or patches are applied. <br> • **CON-4** | User and institutional data must be encrypted during storage and transmission, and handled according to the university’s privacy and security standards. <br> • **CON-5** | The AI assistant should respond to user queries with an average latency of around 2 seconds under normal load. <br> • **CON-6** | Only authorized users can access data relevant to their roles (Student, Lecturer, or admin), and unauthorized attempts must be logged for auditing. <br> • **CON-7** | The system must automatically perform backups of user interactions, model configurations, and system data to ensure recovery in case of a failure. <br> • **CON-8** | Notifications, alerts, and announcements must reach the correct users based on their preferences and chosen language. <br> • **CON-9** | System performance and availability metrics must be tracked in real time and displayed on a dashboard accessible only to maintainers and administrators. |

---

## Architectural Concerns

The following concerns have been selected as drivers:

| Architectural Concerns | Details |
|-------------------------|---------|
|  | • **CRN-1** | Maintaining multiple integrated external systems and other data resources , along with ensuring data consistency,synchronization and fault recovery when the systems experience downtime. <br> • **CRN-2** | Protection of user’s data that is sensitive and ensure compliance with privacy regulations, since the AIDP system saves historical interactions of its users. <br> • **CRN-3** | Managing AI model updates and configuration by supporting configuration of AI model versions and API keys,and ensuring that model updates do not interrupt service availability. <br> • **CRN-4** | Having cloud-based architecture patterns(auto-scaling, message queues, etc.) to improve scalability and availability. <br> • **CRN-5** | Maintaining intuitive, multi-language UI with fast responses (<2 s) during input and output, focusing on usability and accessibility across devices. |

---


