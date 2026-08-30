# CivicSnap — System Constraints

## 1. Introduction

This document defines the technical, architectural, operational, and project constraints that shall be considered during the design and development of the CivicSnap system.

These constraints ensure that the implementation remains consistent with the project's objectives, technology choices, security requirements, and operational workflow.

---

# 2. Technology Constraints

## SC-01: Backend Technology

The backend shall be developed using **Java with Spring Boot**.

The backend shall be responsible for:

* Authentication and authorization
* User management
* Complaint management
* Department management
* Worker management
* Complaint assignment
* SLA management
* Resolution verification
* Notifications
* Reporting
* API communication

---

## SC-02: API Architecture

The backend shall expose APIs for communication between the mobile applications, administration interfaces, database, and external services.

The API architecture shall follow standard RESTful principles where applicable.

---

## SC-03: Database

The system shall use a relational database for structured application data.

The database shall maintain relationships between:

* Users
* Roles
* Complaints
* Categories
* Departments
* Workers
* Assignments
* Resolution records
* Notifications
* Feedback
* Audit records

---

## SC-04: Mobile Applications

CivicSnap shall provide separate mobile experiences for:

1. **Citizen App**
2. **Worker App**

The Citizen App shall focus on complaint reporting and tracking.

The Worker App shall focus on assigned complaints, field work, status updates, and resolution submission.

---

## SC-05: Administration Interface

Administrative functionality shall be provided through a web-based interface.

The system shall support separate access levels for:

* Department Administrators
* System Administrators

---

# 3. Architecture Constraints

## SC-06: Layered Architecture

The Spring Boot backend shall follow a maintainable layered architecture.

The implementation should separate:

* Controller/API layer
* Service/business logic layer
* Repository/data-access layer
* Entity/model layer
* Security layer
* Integration layer

Business rules shall not be implemented exclusively in the client applications.

---

## SC-07: Backend as the Source of Truth

The backend shall be the authoritative source for:

* Complaint status
* Complaint ownership
* Worker assignments
* Department assignments
* SLA calculations
* Resolution verification
* User permissions

Client applications shall not be trusted to enforce business rules independently.

---

## SC-08: Role-Based Access

The system shall enforce role-based access control on the backend.

Client-side hiding of UI elements shall not be considered sufficient security.

---

## SC-09: Modular External Integrations

External services such as AI, maps, notifications, and file storage shall be integrated through modular service components.

Replacing an external service should not require major changes to the core complaint-management logic.

---

# 4. AI Constraints

## SC-10: External AI Dependency

AI-based image analysis may depend on an external AI service or model.

The system shall not assume that the AI service is always available.

---

## SC-11: AI Output Is Non-Authoritative

AI-generated complaint descriptions or classifications shall not be treated as authoritative.

The citizen or authorized administrator shall be able to review the generated information.

---

## SC-12: AI Failure Handling

If the AI service becomes unavailable:

* The application shall display an appropriate message.
* The uploaded image shall not be lost.
* The citizen should be able to enter the description manually.
* The complaint workflow should continue where possible.

---

## SC-13: AI API Credentials

AI service credentials and API keys shall not be stored directly in source code.

They shall be managed through secure environment configuration or an appropriate secrets-management mechanism.

---

# 5. Location Constraints

## SC-14: Device Location Dependency

Complaint location capture depends on the availability of GPS/location services on the citizen's device.

The system cannot guarantee GPS availability in all environments.

---

## SC-15: Location Permission

The Citizen App must obtain appropriate permission before accessing device location.

If permission is denied, the application shall clearly inform the citizen.

---

## SC-16: Location Accuracy

GPS accuracy may vary based on:

* Device hardware
* Indoor/outdoor environment
* Network availability
* Satellite visibility
* Operating-system location services

The system shall store the location accuracy information where available.

---

## SC-17: Location Service Dependency

Map and navigation functionality may depend on third-party map services.

Temporary failure of a map service shall not prevent the complaint itself from being stored.

---

# 6. Image and File Constraints

## SC-18: Image File Size

The system shall impose a maximum size for complaint and resolution images.

The limit shall be configurable according to deployment requirements.

---

## SC-19: Supported Image Formats

Only approved image formats shall be accepted.

The system shall reject unsupported or potentially unsafe file types.

---

## SC-20: Image Storage

Images shall not be stored directly inside relational database records unless explicitly required.

A suitable file/object storage mechanism should be used, with database records maintaining the relevant references.

---

## SC-21: Image Validation

Uploaded files shall be validated before being stored or sent to external AI services.

---

# 7. Security Constraints

## SC-22: Secure Communication

All production communication between clients and backend services shall use HTTPS/TLS.

---

## SC-23: Password Security

Passwords shall never be stored in plain text.

The backend shall use an industry-standard password hashing mechanism.

---

## SC-24: Authentication Tokens

Authentication tokens shall be securely generated, transmitted, stored, and validated.

Expired or invalid tokens shall not provide access to protected resources.

---

## SC-25: Sensitive Configuration

The following shall not be committed to the source-code repository:

* Database passwords
* API keys
* AI credentials
* JWT secrets
* Notification credentials
* Other sensitive configuration values

---

## SC-26: API Authorization

Every protected API endpoint shall verify the authenticated user's permissions.

---

## SC-27: File Access Security

Complaint and resolution images shall not be exposed through unrestricted public URLs unless explicitly intended.

Access shall be controlled through appropriate authorization mechanisms.

---

# 8. Data Constraints

## SC-28: Unique Identifiers

The system shall use unique identifiers for major entities such as:

* Users
* Complaints
* Workers
* Departments
* Assignments
* Resolution records

---

## SC-29: Referential Integrity

Relationships between database entities shall maintain referential integrity.

For example, a complaint assignment shall reference an existing complaint and worker.

---

## SC-30: Historical Data

Important complaint history shall not be permanently overwritten when a status, assignment, or resolution changes.

Historical information shall be retained where required for auditing.

---

## SC-31: Data Validation

Data received from mobile applications, web interfaces, and external services shall be validated by the backend.

---

# 9. Workflow Constraints

## SC-32: Complaint Lifecycle

Complaints shall follow the defined workflow:

```text
Submitted
    ↓
Verified
    ↓
Assigned
    ↓
In Progress
    ↓
Resolution Under Verification
    ↓
Completed
```

---

## SC-33: Complaint Verification Constraint

A complaint shall not be assigned to a worker until it has been verified by an authorized department administrator.

---

## SC-34: Worker Completion Constraint

A worker shall not directly mark a complaint as `Completed`.

The worker can only submit a resolution for verification.

---

## SC-35: Resolution Verification Constraint

Only an authorized department administrator can approve or reject a worker's resolution.

---

## SC-36: Department Assignment Constraint

A complaint shall only be assigned to a worker belonging to the department responsible for that complaint.

---

## SC-37: Unauthorized Workflow Changes

Users shall not be able to skip required workflow stages through direct API requests.

For example:

```text
Citizen → Completed
Worker → Completed
Submitted → Completed
```

shall not be permitted.

---

# 10. Worker App Constraints

## SC-38: Assigned Complaint Access

The Worker App shall primarily expose complaints assigned to the authenticated worker.

---

## SC-39: Worker Permissions

Workers shall not be able to:

* Assign complaints
* Reassign complaints
* Approve resolutions
* Manage departments
* Manage other workers
* Modify citizen accounts

unless explicitly granted additional privileges.

---

## SC-40: Field Work Dependency

Worker functionality may depend on:

* Mobile network availability
* GPS availability
* Device camera
* Device storage

The application shall handle unavailable services gracefully.

---

## SC-41: Resolution Proof

Resolution submission shall require the worker to provide sufficient resolution information according to configured requirements.

---

# 11. Citizen App Constraints

## SC-42: Citizen Complaint Ownership

The Citizen App shall only allow a citizen to view their own complaints.

---

## SC-43: Complaint Submission

The application shall not submit a complaint until the required information has been collected and validated.

---

## SC-44: AI Confirmation

AI-generated descriptions shall require citizen review before becoming the final complaint description.

---

## SC-45: Location Permission

The Citizen App shall comply with Android location permission requirements.

---

# 12. Administration Constraints

## SC-46: Department Isolation

Department administrators shall only have access to information belonging to their assigned department.

---

## SC-47: System Administrator Privileges

System administrators shall have broader privileges than department administrators.

System administrator functions shall be protected by stronger authorization controls.

---

## SC-48: Administrative Actions

Important administrative actions shall be recorded in the audit trail.

---

# 13. SLA Constraints

## SC-49: Configurable SLA

SLA durations shall be configurable according to complaint category or department requirements.

---

## SC-50: SLA Calculation

SLA deadlines shall be calculated by the backend rather than by the client application.

---

## SC-51: SLA Breach

The system shall identify complaints that exceed their defined SLA.

An SLA breach shall not automatically mark a complaint as resolved or completed.

---

# 14. Notification Constraints

## SC-52: External Notification Dependency

Push notifications, email, SMS, or other notification mechanisms may depend on external services.

---

## SC-53: Notification Independence

Notification failure shall not cause the underlying complaint operation to fail.

For example:

```text
Complaint Assignment
        ↓
Assignment Stored Successfully
        ↓
Notification Attempt
        ↓
Notification Failure
```

The assignment shall remain valid even if notification delivery fails.

---

# 15. Network Constraints

## SC-54: Internet Dependency

Core CivicSnap operations require network connectivity to communicate with the backend.

---

## SC-55: Unstable Connections

The applications shall handle:

* Request timeouts
* Connection failures
* Slow networks
* Temporary server unavailability

without corrupting complaint data.

---

## SC-56: Duplicate Requests

The backend shall use appropriate mechanisms to prevent duplicate complaint submissions caused by repeated requests.

---

# 16. Deployment Constraints

## SC-57: Backend Deployment

The Spring Boot backend shall be deployable on a suitable server or cloud environment capable of running Java applications.

---

## SC-58: Database Deployment

The relational database shall be deployed separately or alongside the backend depending on the selected deployment architecture.

---

## SC-59: Environment Configuration

Development, testing, and production environments shall use separate configuration values.

Sensitive production credentials shall not be reused in development environments.

---

# 17. Development Constraints

## SC-60: Version Control

The complete source code shall be maintained using a version-control system such as Git.

---

## SC-61: API Documentation

Backend APIs shall be documented sufficiently for integration with the Citizen App, Worker App, and administration interfaces.

---

## SC-62: Testing

The system shall include appropriate testing for critical functionality, including:

* Authentication
* Authorization
* Complaint submission
* Complaint assignment
* Status transitions
* SLA calculation
* Resolution verification
* API validation

---

## SC-63: Error Handling

The backend shall return consistent and meaningful error responses.

Internal implementation details and sensitive information shall not be exposed to clients.

---

# 18. Scalability Constraints

## SC-64: Horizontal Scalability

The backend architecture should allow multiple application instances to operate when required.

---

## SC-65: Database Scalability

Database design shall avoid unnecessary duplication and support indexing for frequently queried fields.

---

## SC-66: Image Storage Scalability

Image storage shall be designed so that increasing complaint volume does not unnecessarily increase the load on the relational database.

---

# 19. Project Scope Constraints

## SC-67: Civic Issue Scope

The initial version shall focus on common civic issues supported by the project.

Examples include:

* Road damage
* Waste accumulation
* Street light problems
* Water leakage
* Drainage issues
* Public infrastructure damage

Additional categories may be added later.

---

## SC-68: Initial Platform Scope

The initial implementation shall prioritize:

* Android Citizen App
* Android Worker App
* Web-based Department Admin interface
* Web-based System Admin interface
* Spring Boot backend
* Relational database
* AI image-analysis integration
* GPS/location integration

---

## SC-69: External Government Integration

Direct integration with existing government information systems is outside the initial implementation scope unless specifically required.

---

## SC-70: AI Model Training

Training a custom AI model from scratch is outside the initial project scope unless explicitly included as a separate project objective.

The system may integrate an existing suitable AI service or model.

---

# 20. Constraint Priority

When conflicts occur between different constraints, the following priority shall be followed:

1. **Security**
2. **Data integrity**
3. **Business rules**
4. **User authorization**
5. **System reliability**
6. **Performance**
7. **Usability**
8. **Optional/future features**

Security and data-integrity constraints shall not be sacrificed to improve convenience or performance.

---

# 21. System Constraint Summary

| ID    | Constraint                                       | Area           |
| ----- | ------------------------------------------------ | -------------- |
| SC-01 | Java Spring Boot backend                         | Technology     |
| SC-02 | RESTful API architecture                         | Technology     |
| SC-03 | Relational database                              | Technology     |
| SC-04 | Citizen and Worker mobile applications           | Technology     |
| SC-05 | Web administration interface                     | Technology     |
| SC-06 | Layered backend architecture                     | Architecture   |
| SC-07 | Backend is the source of truth                   | Architecture   |
| SC-08 | Backend-enforced RBAC                            | Architecture   |
| SC-09 | Modular external integrations                    | Architecture   |
| SC-10 | External AI dependency                           | AI             |
| SC-11 | AI output is non-authoritative                   | AI             |
| SC-12 | AI failure handling                              | AI             |
| SC-13 | Secure AI credentials                            | AI             |
| SC-14 | GPS dependency                                   | Location       |
| SC-15 | Location permission required                     | Location       |
| SC-16 | GPS accuracy limitations                         | Location       |
| SC-17 | Map service dependency                           | Location       |
| SC-18 | Maximum image size                               | File           |
| SC-19 | Supported image formats                          | File           |
| SC-20 | Separate image storage                           | File           |
| SC-21 | File validation                                  | File           |
| SC-22 | HTTPS/TLS                                        | Security       |
| SC-23 | Password hashing                                 | Security       |
| SC-24 | Secure authentication tokens                     | Security       |
| SC-25 | Secrets excluded from source code                | Security       |
| SC-26 | API authorization                                | Security       |
| SC-27 | Protected image access                           | Security       |
| SC-28 | Unique entity identifiers                        | Data           |
| SC-29 | Referential integrity                            | Data           |
| SC-30 | Historical data preservation                     | Data           |
| SC-31 | Backend data validation                          | Data           |
| SC-32 | Defined complaint lifecycle                      | Workflow       |
| SC-33 | Verification before assignment                   | Workflow       |
| SC-34 | Worker cannot directly complete                  | Workflow       |
| SC-35 | Admin verifies resolution                        | Workflow       |
| SC-36 | Department-worker matching                       | Workflow       |
| SC-37 | No unauthorized workflow skipping                | Workflow       |
| SC-38 | Worker sees assigned complaints                  | Worker App     |
| SC-39 | Restricted worker permissions                    | Worker App     |
| SC-40 | Field-service dependencies                       | Worker App     |
| SC-41 | Resolution proof required                        | Worker App     |
| SC-42 | Citizen sees own complaints                      | Citizen App    |
| SC-43 | Required information before submission           | Citizen App    |
| SC-44 | AI description confirmation                      | Citizen App    |
| SC-45 | Android location permissions                     | Citizen App    |
| SC-46 | Department data isolation                        | Administration |
| SC-47 | System admin privileges                          | Administration |
| SC-48 | Administrative audit logging                     | Administration |
| SC-49 | Configurable SLA                                 | SLA            |
| SC-50 | Backend SLA calculation                          | SLA            |
| SC-51 | SLA breach does not resolve complaint            | SLA            |
| SC-52 | External notification dependency                 | Notification   |
| SC-53 | Notification failure isolation                   | Notification   |
| SC-54 | Network required for core operations             | Network        |
| SC-55 | Unstable network handling                        | Network        |
| SC-56 | Duplicate request prevention                     | Network        |
| SC-57 | Deployable Spring Boot backend                   | Deployment     |
| SC-58 | Relational database deployment                   | Deployment     |
| SC-59 | Environment-specific configuration               | Deployment     |
| SC-60 | Git version control                              | Development    |
| SC-61 | API documentation                                | Development    |
| SC-62 | Critical functionality testing                   | Development    |
| SC-63 | Consistent API error handling                    | Development    |
| SC-64 | Backend horizontal scalability                   | Scalability    |
| SC-65 | Scalable database design                         | Scalability    |
| SC-66 | Scalable image storage                           | Scalability    |
| SC-67 | Initial civic issue scope                        | Project Scope  |
| SC-68 | Initial platform scope                           | Project Scope  |
| SC-69 | Government-system integration excluded initially | Project Scope  |
| SC-70 | Custom AI training excluded initially            | Project Scope  |

---

# 22. Overall System Constraint

CivicSnap shall maintain a clear separation between the **Citizen App**, **Worker App**, **Department Administration**, and **System Administration**, while using a centralized Spring Boot backend as the authoritative source for authentication, authorization, complaint processing, assignment, SLA management, resolution verification, and system data.

The system shall prioritize **security, data integrity, controlled complaint workflow, maintainability, and scalability** while allowing AI, GPS, maps, notifications, and other external services to be replaced or upgraded independently.
