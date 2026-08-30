# CivicSnap — Architecture Overview

## 1. Introduction

CivicSnap is a multi-role civic complaint management platform designed to connect citizens, government departments, and field workers through a centralized digital complaint-resolution workflow.

The system follows a **client-server architecture** with a centralized **Java Spring Boot backend** responsible for authentication, authorization, business logic, complaint management, worker assignment, SLA monitoring, resolution verification, notifications, and reporting.

CivicSnap consists of four primary user roles:

* **Citizen**
* **Worker through the Worker App**
* **Department Administrator**
* **System Administrator**

The architecture separates user interfaces from core business logic and external integrations to improve security, maintainability, scalability, and extensibility.

---

# 2. Architectural Goals

The architecture shall achieve the following goals:

1. Provide a simple interface for citizens to report civic issues.
2. Provide a dedicated Worker App for field operations.
3. Allow department administrators to manage and verify complaints.
4. Allow system administrators to manage the complete platform.
5. Centralize business rules in the backend.
6. Protect user and complaint data through role-based access control.
7. Integrate AI-assisted complaint analysis.
8. Capture and use geographical complaint locations.
9. Support SLA monitoring and reporting.
10. Maintain an auditable complaint history.
11. Allow external services to be replaced without major changes to the core system.
12. Support future scaling and feature expansion.

---

# 3. High-Level Architecture

The overall architecture is:

```text id="n1l9jz"
                         ┌──────────────────────┐
                         │      CITIZEN         │
                         │       APP            │
                         └──────────┬───────────┘
                                    │
                                    │ HTTPS / REST API
                                    │
                         ┌──────────▼───────────┐
                         │                      │
                         │   CIVICSNAP BACKEND  │
                         │     Spring Boot      │
                         │                      │
                         └──────────┬───────────┘
                                    │
            ┌───────────────────────┼────────────────────────┐
            │                       │                        │
            ▼                       ▼                        ▼
     ┌──────────────┐       ┌──────────────┐        ┌──────────────┐
     │  Relational  │       │ AI Service   │        │ External     │
     │   Database   │       │              │        │ Services     │
     └──────────────┘       └──────────────┘        └──────────────┘
                                   
                                   
                         ┌──────────────────────┐
                         │     WORKER APP       │
                         │                      │
                         └──────────┬───────────┘
                                    │
                                    │ HTTPS / REST API
                                    │
                         ┌──────────▼───────────┐
                         │   SPRING BOOT API    │
                         └──────────────────────┘


       ┌────────────────────────────────────────────────────┐
       │                  ADMIN PORTAL                       │
       │                                                     │
       │     Department Administrator   System Administrator │
       └──────────────────────────┬──────────────────────────┘
                                  │
                                  │ HTTPS / REST API
                                  ▼
                         Spring Boot Backend
```

The backend is the central authority and all clients communicate with it through secured APIs.

---

# 4. Architectural Style

CivicSnap shall follow a combination of:

* Client-server architecture
* Layered architecture
* RESTful API architecture
* Role-based access control
* Modular service architecture

The backend shall separate presentation/API handling from business logic and data access.

---

# 5. Major System Components

The major components are:

```text id="j6tr4x"
CivicSnap
│
├── Citizen App
│
├── Worker App
│
├── Admin Portal
│   ├── Department Administration
│   └── System Administration
│
├── Spring Boot Backend
│   ├── Authentication Module
│   ├── User Module
│   ├── Complaint Module
│   ├── Category Module
│   ├── Department Module
│   ├── Worker Module
│   ├── Assignment Module
│   ├── SLA Module
│   ├── Resolution Module
│   ├── Notification Module
│   ├── Feedback Module
│   ├── Reporting Module
│   └── Audit Module
│
├── Relational Database
│
└── External Services
    ├── AI Service
    ├── Map/Location Service
    ├── File/Object Storage
    └── Notification Service
```

---

# 6. Client Layer

## 6.1 Citizen App

The Citizen App is the primary interface for citizens.

It shall provide:

* Registration
* Login
* Profile management
* Complaint reporting
* Camera access
* Image upload
* AI description review
* GPS location capture
* Complaint tracking
* Complaint history
* Resolution viewing
* Feedback

The Citizen App shall not contain authoritative business logic.

---

## 6.2 Worker App

The **Worker App is a dedicated first-class component of CivicSnap**.

It shall be used by field workers to perform operational tasks.

The Worker App shall provide:

* Worker authentication
* Assigned complaint dashboard
* Complaint details
* Complaint images
* Complaint location
* Assignment acceptance
* Work status updates
* Resolution notes
* Resolution image upload
* Resolution submission
* Work history
* Notifications

The Worker App shall communicate with the same Spring Boot backend used by the Citizen App and Admin Portal.

---

## 6.3 Admin Portal

The Admin Portal shall provide administrative functionality.

It shall support two administrative roles:

### Department Administrator

Responsible for:

* Complaint review
* Complaint verification
* Complaint rejection
* Worker management
* Worker availability
* Assignment
* Reassignment
* Progress monitoring
* SLA monitoring
* Resolution verification
* Reports

### System Administrator

Responsible for:

* User management
* Department management
* Worker management
* Department administrator management
* Complaint category management
* Category-department mapping
* System-wide monitoring
* System-wide reporting
* Platform configuration

---

# 7. API Gateway / REST API Layer

The backend API layer provides communication between clients and the application services.

The API layer shall:

* Receive HTTP requests.
* Authenticate users.
* Authorize requests.
* Validate request data.
* Invoke appropriate services.
* Return structured responses.
* Handle API errors consistently.

Example:

```text id="m0ob6f"
Citizen App
     │
     │ POST /api/complaints
     ▼
REST Controller
     │
     ▼
Complaint Service
     │
     ▼
Complaint Repository
     │
     ▼
Database
```

---

# 8. Backend Architecture

The Spring Boot backend shall use a layered architecture.

```text id="c9t4hs"
┌─────────────────────────────────────┐
│           REST Controllers           │
├─────────────────────────────────────┤
│             Services                │
├─────────────────────────────────────┤
│       Business Rules / Domain       │
├─────────────────────────────────────┤
│           Repositories              │
├─────────────────────────────────────┤
│             Database                │
└─────────────────────────────────────┘

Supporting:
Authentication | Authorization | Validation
Exception Handling | Logging | Integrations
```

---

# 9. Controller Layer

The Controller Layer handles external API requests.

Responsibilities include:

* Request routing
* Request validation
* Authentication context
* Authorization checks
* Response formatting
* HTTP status handling

Controllers shall not contain complex business logic.

Example controllers:

```text id="khw1e8"
AuthController
UserController
ComplaintController
WorkerController
AssignmentController
ResolutionController
DepartmentController
CategoryController
FeedbackController
ReportController
NotificationController
```

---

# 10. Service Layer

The Service Layer contains the core application logic.

Example services:

```text id="y2m5pn"
AuthService
UserService
ComplaintService
AIService
LocationService
AssignmentService
WorkerService
DepartmentService
SLAService
ResolutionService
NotificationService
FeedbackService
ReportService
AuditService
```

The Service Layer shall enforce business rules such as:

* Only verified complaints can be assigned.
* Workers can only manage their assignments.
* Workers cannot directly complete complaints.
* Department administrators verify resolutions.
* Citizens can only access their own complaints.
* Department administrators can only access their department's complaints.

---

# 11. Repository Layer

The Repository Layer provides database access.

Example repositories:

```text id="i7f1v8"
UserRepository
ComplaintRepository
CategoryRepository
DepartmentRepository
WorkerRepository
AssignmentRepository
ResolutionRepository
FeedbackRepository
NotificationRepository
AuditRepository
```

Repositories shall abstract database operations from the service layer.

---

# 12. Security Layer

The security architecture shall provide:

* Authentication
* Authorization
* Role-based access control
* Password hashing
* Token validation
* Protected API endpoints
* Resource-level access checks

Example roles:

```text id="6xk4kh"
ROLE_CITIZEN
ROLE_WORKER
ROLE_DEPARTMENT_ADMIN
ROLE_SYSTEM_ADMIN
```

Authorization shall be enforced by the backend.

---

# 13. Complaint Management Module

The Complaint Module is the central business component.

It shall manage:

* Complaint creation
* Complaint identification
* Complaint descriptions
* Images
* Categories
* Locations
* Status
* Ownership
* Department association
* SLA information
* Complaint history

The module shall enforce valid status transitions.

---

# 14. AI Integration Module

The AI Integration Module communicates with the configured AI service.

### Input

* Complaint image

### Output

* Suggested description
* Optional category suggestion

The AI service shall not directly modify the authoritative complaint record.

The backend shall validate the AI response before returning it to the Citizen App.

---

# 15. Location Module

The Location Module manages location-related information.

It shall support:

* Latitude
* Longitude
* Location accuracy
* Map references
* Complaint location display

GPS data originates from the user's device.

The backend stores the location associated with the complaint.

---

# 16. Department and Category Module

The system shall maintain:

```text id="6v0zqh"
Complaint Category
       │
       ▼
Responsible Department
       │
       ▼
Eligible Workers
```

Example:

```text id="b6j6h0"
Road Damage
     ↓
Road/Public Works Department
     ↓
Road Maintenance Workers
```

Category-to-department mappings shall be configurable by the System Administrator.

---

# 17. Assignment Module

The Assignment Module handles worker allocation.

Responsibilities:

* Identify eligible workers.
* Check department membership.
* Consider worker availability.
* Create assignments.
* Reassign complaints.
* Maintain assignment history.
* Trigger assignment notifications.

Assignment rules shall be enforced by the backend.

---

# 18. Worker Operations Module

The Worker Operations Module supports the Worker App.

The workflow is:

```text id="3f3qk5"
Assigned
   ↓
Accept Assignment
   ↓
In Progress
   ↓
Resolve Issue
   ↓
Upload Evidence
   ↓
Submit Resolution
   ↓
Resolution Under Verification
```

Workers shall not be allowed to bypass departmental verification.

---

# 19. Resolution Module

The Resolution Module manages worker-submitted resolution information.

It shall store:

* Resolution description
* Resolution images
* Worker ID
* Submission timestamp
* Verification status
* Administrator decision
* Rejection reason

The final state shall only become `Completed` after authorized verification.

---

# 20. SLA Module

The SLA Module manages service-level deadlines.

It shall:

* Retrieve applicable SLA rules.
* Calculate deadlines.
* Track active complaints.
* Identify approaching deadlines.
* Identify SLA breaches.
* Provide SLA information to administrators.

SLA calculation shall be performed by the backend.

---

# 21. Notification Module

The Notification Module handles system-generated notifications.

Possible events include:

```text id="w3n6xx"
Complaint Submitted
Complaint Verified
Complaint Rejected
Worker Assigned
Worker Reassigned
Work Started
Resolution Submitted
Resolution Rejected
Resolution Approved
Complaint Completed
SLA Warning
SLA Breach
```

The notification system shall be loosely coupled with core complaint processing.

---

# 22. Feedback Module

The Feedback Module manages citizen feedback.

Feedback shall:

* Belong to a complaint.
* Belong to the citizen who submitted it.
* Only be allowed after completion.
* Be available for reporting.

---

# 23. Reporting Module

The Reporting Module shall aggregate operational data.

It shall support:

### Department Reports

* Complaint volume
* Complaint status
* Resolution rate
* SLA performance
* Worker workload
* Average resolution time

### System Reports

* Cross-department complaint statistics
* Department comparison
* Category distribution
* SLA performance
* System-wide resolution statistics
* Feedback analysis

---

# 24. Audit Module

The Audit Module records important system actions.

Examples:

```text id="v9j3e0"
Complaint Verified
Complaint Rejected
Worker Assigned
Worker Reassigned
Status Changed
Resolution Submitted
Resolution Approved
Resolution Rejected
Department Modified
User Modified
```

Each audit event should contain:

* Actor
* Action
* Entity
* Previous state
* New state
* Timestamp

Audit information shall be protected from unauthorized modification.

---

# 25. Data Layer

The data layer shall use a relational database.

Major entities include:

```text id="7e4s2a"
User
  │
  ├── Citizen
  ├── Worker
  ├── Department Admin
  └── System Admin

Department
  │
  ├── Categories
  ├── Workers
  └── Complaints

Complaint
  │
  ├── Image
  ├── Location
  ├── Assignment
  ├── SLA
  ├── Resolution
  ├── Feedback
  └── Audit History
```

---

# 26. Storage Architecture

Complaint and resolution images should be stored using a dedicated file/object storage mechanism rather than directly storing large binary files in the relational database.

The database should store references such as:

```text id="p6a2lq"
image_id
storage_key
file_name
content_type
file_size
created_at
```

Access to stored files shall be authorized.

---

# 27. External Service Architecture

CivicSnap may integrate with the following external services:

```text id="q7h7s1"
              CivicSnap Backend
                     │
       ┌─────────────┼─────────────┐
       │             │             │
       ▼             ▼             ▼
   AI Service    Map Service   Notification
                                  Service
       │             │             │
       ▼             ▼             ▼
    Analysis       Maps       Push/Email/SMS
```

External services shall be accessed through dedicated integration components.

---

# 28. Complaint Processing Architecture

The complaint processing flow is:

```text id="d6c8g4"
Citizen App
     │
     ▼
Capture Image
     │
     ▼
Upload Image
     │
     ▼
AI Service
     │
     ▼
Suggested Description
     │
     ▼
Citizen Confirmation
     │
     ▼
GPS Location
     │
     ▼
Spring Boot Backend
     │
     ▼
Complaint Created
     │
     ▼
Category / Department Mapping
     │
     ▼
Department Admin
     │
     ▼
Verify Complaint
     │
     ▼
Assign Worker
     │
     ▼
Worker App
     │
     ▼
In Progress
     │
     ▼
Resolution Submission
     │
     ▼
Department Admin
     │
     ▼
Resolution Verification
     │
     ├───────────────┐
     ▼               ▼
  Rejected         Approved
     │               │
     ▼               ▼
In Progress       Completed
                     │
                     ▼
                  Citizen
                     │
                     ▼
                  Feedback
```

---

# 29. Authentication Architecture

Authentication shall follow:

```text id="e6y5i7"
Client
  │
  │ Credentials
  ▼
Auth Controller
  │
  ▼
Authentication Service
  │
  ▼
User Repository
  │
  ▼
Credential Validation
  │
  ▼
Authentication Token
  │
  ▼
Client
```

Subsequent API requests shall include the required authentication credentials/token.

---

# 30. Authorization Architecture

Authorization shall be implemented at multiple levels.

### Role Level

```text id="6xbyz8"
Citizen
Worker
Department Admin
System Admin
```

### Resource Level

For example:

```text id="7f9j4m"
Citizen
   ↓
Own Complaints Only

Worker
   ↓
Assigned Complaints Only

Department Admin
   ↓
Department Complaints Only

System Admin
   ↓
System-Wide Data
```

This prevents unauthorized access even when a valid user attempts to manipulate another resource.

---

# 31. API Communication

All clients shall communicate with the backend through secure HTTP APIs.

```text id="d9g6j2"
Citizen App ───────┐
                   │
Worker App ────────┼── HTTPS/REST ──► Spring Boot
                   │
Admin Portal ──────┘
```

The backend shall return structured responses and appropriate HTTP status codes.

---

# 32. Error Handling Architecture

Errors shall be handled centrally through the backend.

```text id="x3w9ha"
Request
   │
   ▼
Controller
   │
   ▼
Service
   │
   ├── Success ───────► Response
   │
   └── Exception
          │
          ▼
   Global Exception Handler
          │
          ▼
   Standard Error Response
```

The system shall not expose internal stack traces or sensitive implementation details to clients.

---

# 33. Logging Architecture

The backend shall maintain appropriate logs for:

* Authentication events
* API errors
* Business operations
* External service failures
* AI failures
* Notification failures
* Database errors
* Security events

Logs shall not contain unnecessary sensitive information.

---

# 34. Security Architecture

The overall security architecture is:

```text id="z1q6sh"
          Client Applications
                  │
                  ▼
             HTTPS / TLS
                  │
                  ▼
          Authentication
                  │
                  ▼
          Authorization / RBAC
                  │
                  ▼
          Input Validation
                  │
                  ▼
        Business Rule Validation
                  │
                  ▼
          Service Layer
                  │
                  ▼
        Authorized Data Access
                  │
                  ▼
              Database
```

Security controls shall be applied consistently across all client applications.

---

# 35. Data Flow — Complaint Submission

```text id="f7q4t8"
Citizen
  │
  │ Image
  ▼
Citizen App
  │
  ├──────────────► AI Service
  │                     │
  │                     ▼
  │              Suggested Description
  │                     │
  ◄─────────────────────┘
  │
  │ Confirmed Description
  │ + GPS
  ▼
Spring Boot Backend
  │
  ▼
Validation
  │
  ▼
Category / Department Mapping
  │
  ▼
Database
  │
  ▼
Department Admin
```

---

# 36. Data Flow — Worker Resolution

```text id="c5w3sj"
Department Admin
       │
       ▼
Assign Complaint
       │
       ▼
Spring Boot Backend
       │
       ▼
Worker App
       │
       ▼
Worker Accepts
       │
       ▼
In Progress
       │
       ▼
Worker Performs Work
       │
       ▼
Resolution Evidence
       │
       ▼
Spring Boot Backend
       │
       ▼
Department Admin
       │
       ▼
Verify Resolution
       │
   ┌───┴────┐
   ▼        ▼
Reject    Approve
   │        │
   ▼        ▼
In Progress Completed
```

---

# 37. Deployment Architecture

A possible production deployment is:

```text id="w8z2ap"
                         Internet
                            │
             ┌──────────────┼──────────────┐
             │              │              │
             ▼              ▼              ▼
       Citizen App     Worker App      Admin Portal
             │              │              │
             └──────────────┼──────────────┘
                            │
                         HTTPS
                            │
                            ▼
                    ┌───────────────┐
                    │ Load Balancer │
                    └───────┬───────┘
                            │
                 ┌──────────┴──────────┐
                 ▼                     ▼
          Spring Boot API 1     Spring Boot API 2
                 │                     │
                 └──────────┬──────────┘
                            │
                ┌───────────┼───────────┐
                ▼           ▼           ▼
             Database    File Store   External APIs
```

The initial academic deployment may use a simpler architecture with a single Spring Boot instance.

---

# 38. Scalability Strategy

The architecture shall allow future scaling.

Potential strategies include:

* Multiple Spring Boot instances
* Load balancing
* Database indexing
* Connection pooling
* Dedicated file storage
* Caching
* Asynchronous notifications
* Background job processing
* External AI service scaling

The system shall avoid tightly coupling external services to the complaint lifecycle.

---

# 39. Availability and Fault Tolerance

External failures should be isolated.

### AI Failure

```text id="7f0s7m"
AI Failure
    ↓
Manual Description
    ↓
Complaint Continues
```

### Notification Failure

```text id="j4n8ax"
Notification Failure
       ↓
Log Failure
       ↓
Complaint Operation Remains Successful
```

### Map Failure

```text id="u3v9v2"
Map Service Failure
       ↓
Complaint Data Remains Available
```

---

# 40. Architectural Constraints

The architecture shall comply with the following constraints:

1. Backend must use Java Spring Boot.
2. REST APIs shall be used for client-backend communication.
3. Relational database shall be used for structured data.
4. Citizen and Worker applications shall be separate client applications.
5. Worker functionality shall be provided through the dedicated Worker App.
6. Department and System administration shall be separated by authorization.
7. Business rules shall be enforced by the backend.
8. AI shall be treated as an external/non-authoritative service.
9. GPS accuracy shall depend on device and environmental conditions.
10. Sensitive credentials shall not be stored in source code.
11. Complaint status transitions shall be controlled by the backend.
12. Resolution completion shall require department verification.
13. External service failures shall not corrupt complaint data.

---

# 41. Architectural Security Boundaries

The system shall maintain the following security boundaries:

```text id="k8h4e3"
┌───────────────────────────────────────┐
│             PUBLIC CLIENTS            │
│                                       │
│ Citizen App / Worker App / Admin UI  │
└───────────────────┬───────────────────┘
                    │
                 HTTPS
                    │
┌───────────────────▼───────────────────┐
│          APPLICATION BOUNDARY         │
│                                       │
│ Authentication / Authorization / API  │
└───────────────────┬───────────────────┘
                    │
┌───────────────────▼───────────────────┐
│             BUSINESS LAYER            │
│                                       │
│ Complaint / Assignment / Resolution   │
└───────────────────┬───────────────────┘
                    │
┌───────────────────▼───────────────────┐
│              DATA LAYER               │
│                                       │
│ Database / File Storage / Audit Data │
└───────────────────────────────────────┘
```

---

# 42. Architecture Principles

CivicSnap shall follow these principles:

## 42.1 Single Source of Truth

The backend shall be the authoritative source of application state.

## 42.2 Least Privilege

Users shall receive only the permissions required for their role.

## 42.3 Separation of Concerns

UI, API, business logic, data access, and external integrations shall remain separated.

## 42.4 Secure by Default

Security controls shall be applied at the backend rather than relying on client-side restrictions.

## 42.5 External Service Independence

AI, maps, notifications, and storage providers shall be replaceable.

## 42.6 Auditability

Important actions shall be traceable.

## 42.7 Scalability

The architecture should allow increasing users, complaints, and departments without major redesign.

---

# 43. Architecture-to-Actor Mapping

| Component             | Citizen | Worker | Department Admin | System Admin |
| --------------------- | :-----: | :----: | :--------------: | :----------: |
| Citizen App           |    ✓    |    —   |         —        |       —      |
| Worker App            |    —    |    ✓   |         —        |       —      |
| Admin Portal          |    —    |    —   |         ✓        |       ✓      |
| Authentication        |    ✓    |    ✓   |         ✓        |       ✓      |
| Complaint Module      |    ✓    |   ✓*   |         ✓        |      ✓*      |
| Assignment Module     |    —    |    —   |         ✓        |      ✓*      |
| Resolution Module     |    —    |    ✓   |         ✓        |      ✓*      |
| SLA Module            |    —    |   ✓*   |         ✓        |      ✓*      |
| Reporting Module      |    —    |    —   |         ✓        |       ✓      |
| User Management       |   Self  |  Self  |      Limited     |       ✓      |
| Department Management |    —    |    —   |         —        |       ✓      |
| Category Management   |    —    |    —   |         —        |       ✓      |
| Audit Module          |    —    |    —   |         —        |      ✓*      |

`✓*` indicates access subject to authorization and implementation policy.

---

# 44. Architecture-to-Technology Mapping

| Layer           | Technology / Approach                   |
| --------------- | --------------------------------------- |
| Citizen Client  | Android                                 |
| Worker Client   | Android                                 |
| Admin Client    | Web                                     |
| API             | REST                                    |
| Backend         | Java Spring Boot                        |
| Security        | Spring Security / secure authentication |
| Database        | Relational Database                     |
| File Storage    | Object/File Storage                     |
| AI              | External AI API/Model                   |
| Maps            | External Map/Location Service           |
| Notifications   | Push/Email/SMS Service                  |
| Version Control | Git                                     |
| Deployment      | Server/Cloud                            |

---

# 45. Recommended Package Structure

A suitable Spring Boot package structure is:

```text id="j5j1k8"
com.civicsnap
│
├── config
│
├── security
│
├── auth
│   ├── controller
│   ├── service
│   ├── dto
│   └── ...
│
├── user
│   ├── controller
│   ├── service
│   ├── repository
│   ├── entity
│   └── dto
│
├── complaint
│   ├── controller
│   ├── service
│   ├── repository
│   ├── entity
│   └── dto
│
├── worker
│
├── department
│
├── category
│
├── assignment
│
├── resolution
│
├── sla
│
├── notification
│
├── feedback
│
├── report
│
├── audit
│
├── integration
│   ├── ai
│   ├── maps
│   └── notifications
│
└── exception
```

The exact package structure may evolve during implementation, but responsibilities should remain clearly separated.

---

# 46. Architecture Decision Summary

| Decision       | Choice                           | Reason                                                |
| -------------- | -------------------------------- | ----------------------------------------------------- |
| Backend        | Java Spring Boot                 | Mature enterprise framework and suitable REST support |
| API            | REST                             | Simple integration with mobile and web clients        |
| Mobile         | Separate Citizen and Worker Apps | Clear role separation                                 |
| Admin          | Web Portal                       | Efficient administrative operations                   |
| Database       | Relational                       | Suitable for structured relationships                 |
| Business Logic | Backend                          | Security and consistency                              |
| Authentication | Token-based                      | Suitable for mobile/API architecture                  |
| Authorization  | RBAC                             | Supports four distinct roles                          |
| AI             | External integration             | Avoids coupling AI to core system                     |
| Location       | GPS + Map Service                | Supports location-based complaint handling            |
| Images         | File/Object Storage              | Better scalability than database blobs                |
| Notifications  | External service                 | Reliable delivery and modularity                      |
| Auditing       | Backend                          | Centralized traceability                              |

---

# 47. Final Architecture

The final CivicSnap architecture can be summarized as:

```text id="e3y6h4"
                         ┌───────────────────────┐
                         │      CITIZEN APP      │
                         │                       │
                         │ Report / Track /      │
                         │ Feedback              │
                         └───────────┬───────────┘
                                     │
                                     │
                                     ▼
┌────────────────────────────────────────────────────────────┐
│                    CIVICSNAP BACKEND                       │
│                    JAVA SPRING BOOT                        │
│                                                            │
│  ┌────────────┐ ┌──────────────┐ ┌─────────────────────┐ │
│  │ Auth/RBAC  │ │ Complaint    │ │ Department/Worker   │ │
│  │            │ │ Management   │ │ Management          │ │
│  └────────────┘ └──────────────┘ └─────────────────────┘ │
│                                                            │
│  ┌────────────┐ ┌──────────────┐ ┌─────────────────────┐ │
│  │ Assignment │ │ Resolution   │ │ SLA / Notification │ │
│  │            │ │ Verification │ │ / Feedback         │ │
│  └────────────┘ └──────────────┘ └─────────────────────┘ │
│                                                            │
│  ┌──────────────────┐ ┌────────────────────────────────┐ │
│  │ Reporting / Audit│ │ External Integration Services │ │
│  └──────────────────┘ └────────────────────────────────┘ │
└───────────────┬──────────────────────┬─────────────────────┘
                │                      │
       ┌────────┴────────┐    ┌────────┴───────────┐
       ▼                 ▼    ▼                    ▼
┌──────────────┐  ┌──────────────┐  ┌────────────────────┐
│  Relational  │  │ File/Object  │  │ External Services │
│  Database    │  │ Storage      │  │ AI / Maps / Push  │
└──────────────┘  └──────────────┘  └────────────────────┘
                ▲
                │
┌───────────────┴────────────────────────────────────────────┐
│                      CLIENT LAYER                          │
│                                                            │
│  ┌────────────────┐       ┌─────────────────────────────┐ │
│  │   WORKER APP   │       │       ADMIN PORTAL          │ │
│  │                │       │                             │ │
│  │ Assignments    │       │ Department Admin             │ │
│  │ Field Work     │       │ System Admin                 │ │
│  │ Status         │       │                             │ │
│  │ Resolution     │       │                             │ │
│  └────────────────┘       └─────────────────────────────┘ │
└────────────────────────────────────────────────────────────┘
```

The architecture establishes a clear separation of responsibilities:

**Citizen App → Report**

**Spring Boot Backend → Process & Control**

**Department Admin → Verify & Assign**

**Worker App → Execute & Submit Resolution**

**Department Admin → Verify Resolution**

**System Admin → Manage Platform**

This separation ensures that CivicSnap remains secure, maintainable, auditable, and extensible while preserving the complete civic complaint lifecycle from **citizen reporting to verified resolution**.
