
| **ID** | **Quality Attribute** | **Scenario Description** | **Associated Use Cases** |
|:----------------|--------------|---------------------------| ---------------------------|
| **QA-1:** |Performance| When ≤ 5 000 users query simultaneously, AI responses are returned within 2 seconds. | UC-1, UC-2 |
| **QA-2** |Availability| If a service node fails, the system recovers within 30 seconds with no data loss (99.5 % uptime monthly). | All |
| **QA-3:** |Security| Every login, update, or AI interaction is logged with timestamp and user ID; unauthorized access is denied and audited |UC-1 – UC-6|
| **QA-4:** |Modifiability| Integrate a new data source (e.g., library API) or AI model with minimal changes to existing modules (< 2 days effort). | UC-1, UC-2, UC-3, UC-5|
| **QA-5:** |Usability| 95 % of users successfully complete their task without assistance during UX testing. Interface meets WCAG 2.1 AA standards. | UC-1, UC-2, UC-4 |
| **QA-6:** |Scalability| During peak week (e.g., registration), system auto-scales to 10 000 sessions without performance degradation.|UC-1, UC-2, UC-3|
| **QA-7:** |Reliability| When the LMS API is unavailable, dashboard returns cached results and logs the incident for sync on recovery.  | UC-2, UC-3 |
| **QA-8:** |Interoperability| APIs conform to REST/GraphQL standards and support OAuth2 for secure integration with university systems.  |UC-3, UC-5 |
| **QA-9:** |Maintainability| Each service layer has automated unit and integration tests ≥ 80 % coverage. Continuous integration verifies builds. |UC-5, UC-6|
