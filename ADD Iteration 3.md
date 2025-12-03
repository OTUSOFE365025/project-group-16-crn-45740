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
