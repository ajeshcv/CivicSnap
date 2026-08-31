# CivicSnap — Software Requirements Specification (SRS)

**Project Name:** CivicSnap
**Document:** Software Requirements Specification
**Version:** 1.0
**Status:** Draft
**Backend:** Java Spring Boot
**Platforms:** Android Citizen App, Android Worker App, Web Administration Portal

---

# Table of Contents

1. Introduction
2. Overall Description
3. System Scope
4. Stakeholders
5. User Roles
6. Product Perspective
7. System Architecture
8. System Features
9. Functional Requirements
10. Non-Functional Requirements
11. User Stories
12. Business Rules
13. System Constraints
14. Data Requirements
15. Complaint Lifecycle
16. External System Interfaces
17. API Requirements
18. Security Requirements
19. Error Handling
20. Reporting and Analytics
21. Assumptions and Dependencies
22. Out of Scope
23. Future Enhancements
24. Requirement Traceability
25. Acceptance Criteria
26. Conclusion

---

# 1. Introduction

## 1.1 Purpose

This Software Requirements Specification defines the functional and non-functional requirements of **CivicSnap**, a civic complaint management system designed to improve the process of reporting, assigning, resolving, and monitoring public infrastructure and civic issues.

The document serves as a reference for the design, development, testing, deployment, and evaluation of the CivicSnap system.

CivicSnap provides separate interfaces for citizens, field workers, department administrators, and system administrators while using a centralized backend to manage the complete complaint lifecycle.

---

## 1.2 Problem Background

Citizens commonly encounter civic problems such as damaged roads, garbage accumulation, broken street lights, water leakage, drainage problems, and damaged public infrastructure.

Traditional complaint systems may require citizens to manually describe the issue, identify the responsible department, and repeatedly follow up to determine the status of their complaint.

There is also a lack of transparency between complaint submission, departmental verification, worker assignment, field resolution, and final verification.

CivicSnap aims to provide a structured digital workflow that connects citizens with the appropriate government departments and field workers.

---

## 1.3 Proposed Solution

CivicSnap allows citizens to report civic issues by capturing an image through the Citizen App.

The system uses AI-based image analysis to generate a suggested description of the issue. The citizen reviews and edits the description before submission.

The application also captures the geographical location of the issue using GPS.

After submission, the backend routes the complaint to the appropriate department. A department administrator verifies the complaint and assigns it to an available worker.

The worker uses the Worker App to view the complaint, navigate to the reported location, perform the required work, update the work status, and upload resolution proof.

The department administrator then verifies the resolution before the complaint is officially marked as completed.

---

# 2. Overall Description

## 2.1 Product Vision

CivicSnap aims to provide a transparent, efficient, and technology-driven platform for civic issue reporting and resolution.

The system shall reduce the effort required from citizens to report problems while improving departmental visibility and worker coordination.

---

## 2.2 Primary Objectives

The primary objectives of CivicSnap are:

* Simplify civic complaint submission.
* Use AI to assist citizens in describing reported issues.
* Capture accurate complaint locations using GPS.
* Automatically route complaints to relevant departments.
* Provide a dedicated Worker App for field operations.
* Allow administrators to verify complaints and resolutions.
* Track complaint progress throughout its lifecycle.
* Monitor SLA compliance.
* Improve transparency between citizens and government departments.
* Maintain an auditable record of complaint activities.

---

# 3. System Scope

## 3.1 In-Scope Features

The initial CivicSnap system shall include:

### Citizen App

* Registration and login
* Profile management
* Complaint image capture
* Image upload
* AI-generated complaint description
* Description editing
* GPS location capture
* Complaint submission
* Complaint tracking
* Complaint history
* Resolution viewing
* Feedback

### Worker App

* Worker login
* Assigned complaint dashboard
* Complaint details
* Complaint location
* Work status updates
* Resolution notes
* Resolution image upload
* Resolution submission
* Work history

### Department Administration

* Secure login
* Department dashboard
* Complaint verification
* Complaint rejection
* Worker management
* Worker availability
* Complaint assignment
* Complaint reassignment
* SLA monitoring
* Resolution verification
* Reports

### System Administration

* System administration
* Department management
* User management
* Worker management
* Department administrator management
* System-wide complaint monitoring
* Reports
* Configuration management

### Backend

* Authentication
* Authorization
* User management
* Complaint management
* Department management
* Worker management
* Assignment management
* SLA management
* Resolution management
* Notifications
* Reporting
* Audit logging
* External service integration

---

# 4. Stakeholders

The major stakeholders are:

| Stakeholder                   | Interest                                        |
| ----------------------------- | ----------------------------------------------- |
| Citizens                      | Report and track civic issues                   |
| Field Workers                 | Receive and resolve assigned complaints         |
| Department Administrators     | Verify, assign, monitor, and approve complaints |
| System Administrators         | Manage the complete platform                    |
| Government Departments        | Improve civic issue management                  |
| Project Team                  | Design, develop, test, and maintain the system  |
| AI Service Provider           | Provide image analysis capabilities             |
| Map/Location Service Provider | Provide location and mapping functionality      |
| Notification Provider         | Deliver system notifications                    |

---

# 5. User Roles

CivicSnap shall support four primary roles.

## 5.1 Citizen

Citizens can:

* Create accounts
* Report civic issues
* Capture images
* Review AI-generated descriptions
* Provide locations
* Submit complaints
* Track complaints
* View resolutions
* Provide feedback

---

## 5.2 Worker

Workers are field personnel responsible for resolving assigned complaints.

Workers can:

* Log in to the Worker App
* View assigned complaints
* View complaint locations
* Accept assignments
* Update work status
* Upload resolution evidence
* Submit resolutions

Workers cannot approve their own resolutions.

---

## 5.3 Department Administrator

Department administrators manage complaints belonging to their department.

They can:

* Verify complaints
* Reject complaints
* Manage workers
* Assign complaints
* Reassign complaints
* Monitor SLA
* Track work progress
* Verify resolutions
* Approve or reject resolutions
* Generate department reports

---

## 5.4 System Administrator

The system administrator manages the complete CivicSnap platform.

They can:

* Manage departments
* Manage users
* Manage workers
* Manage department administrators
* Configure complaint categories
* Configure department mappings
* Monitor system-wide complaints
* Generate system-wide reports

---

# 6. Product Perspective

CivicSnap shall operate as a multi-client system consisting of:

```text
                 ┌───────────────────────┐
                 │      Citizen App      │
                 └───────────┬───────────┘
                             │
                             │
                 ┌───────────▼───────────┐
                 │                       │
                 │   Spring Boot API     │
                 │       Backend         │
                 │                       │
                 └───────────┬───────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
              ▼              ▼              ▼
        ┌──────────┐   ┌──────────┐   ┌─────────────┐
        │ Database │   │ AI       │   │ External    │
        │          │   │ Service  │   │ Services    │
        └──────────┘   └──────────┘   └─────────────┘
                             │
              ┌──────────────┴──────────────┐
              │                             │
              ▼                             ▼
      ┌──────────────┐             ┌──────────────────┐
      │ Worker App   │             │ Admin Portal     │
      └──────────────┘             └──────────────────┘
```

The backend shall act as the central authority for business logic and data.

---

# 7. System Architecture

## 7.1 Backend

The backend shall be developed using **Java Spring Boot**.

The backend shall expose RESTful APIs to the client applications.

---

## 7.2 Backend Layers

The backend should follow a layered architecture:

```text
Controller Layer
       ↓
Service Layer
       ↓
Repository Layer
       ↓
Database
```

Supporting components shall include:

* Security layer
* Validation layer
* Integration layer
* Exception handling
* Logging
* Notification services

---

## 7.3 Client Applications

The system shall provide:

1. Android Citizen App
2. Android Worker App
3. Web Administration Portal

---

## 7.4 Database

A relational database shall be used for structured system data.

The database shall maintain relationships between users, complaints, departments, workers, assignments, resolutions, notifications, feedback, and audit records.

---

# 8. System Features

## 8.1 Complaint Reporting

Citizens can report civic issues using images, descriptions, and location information.

---

## 8.2 AI-Assisted Reporting

The system uses AI to analyze complaint images and generate a suggested description.

The citizen remains responsible for confirming the final description.

---

## 8.3 Automatic Department Routing

The system determines the relevant complaint category and department based on configured mappings.

---

## 8.4 Complaint Verification

Department administrators verify submitted complaints before field assignment.

---

## 8.5 Worker Assignment

Verified complaints are assigned to appropriate workers.

---

## 8.6 Field Resolution

Workers use the Worker App to perform assigned work and provide resolution evidence.

---

## 8.7 Resolution Verification

Department administrators verify the worker's resolution before completing the complaint.

---

## 8.8 SLA Monitoring

The system tracks complaint resolution deadlines and identifies SLA breaches.

---

## 8.9 Notifications

Users receive notifications about important complaint events.

---

## 8.10 Reporting

Administrators can generate operational and performance reports.

---

# 9. Functional Requirements

## 9.1 Authentication and Account Management

### FR-01 User Registration

The system shall allow citizens to create accounts using required registration information.

### FR-02 Authentication

The system shall authenticate citizens, workers, department administrators, and system administrators.

### FR-03 Profile Management

Citizens shall be able to view and update permitted profile information.

### FR-04 Password Security

Passwords shall be securely hashed and shall never be stored in plain text.

### FR-05 Logout

Authenticated users shall be able to securely log out.

---

# 10. Citizen Functional Requirements

## FR-06 Capture Complaint Image

The Citizen App shall allow citizens to capture an image of a civic issue.

## FR-07 Image Review

Citizens shall be able to preview and retake captured images.

## FR-08 Image Upload

The system shall allow complaint images to be uploaded securely.

## FR-09 AI Image Analysis

The system shall send the complaint image to the configured AI service for analysis.

## FR-10 AI Description

The AI service shall generate a suggested complaint description.

## FR-11 Description Editing

Citizens shall be able to modify the AI-generated description.

## FR-12 Description Confirmation

Citizens shall confirm the final complaint description before submission.

## FR-13 GPS Location

The system shall capture complaint latitude and longitude using device location services.

## FR-14 Location Confirmation

Citizens shall be able to verify the captured location.

## FR-15 Complaint Submission

Citizens shall be able to submit complete complaints.

## FR-16 Complaint ID

The system shall generate a unique ID for every submitted complaint.

## FR-17 Complaint Tracking

Citizens shall be able to view the current status of their complaints.

## FR-18 Complaint History

Citizens shall be able to view previously submitted complaints.

## FR-19 Resolution Viewing

Citizens shall be able to view resolution information for completed complaints.

## FR-20 Feedback

Citizens may provide feedback after a complaint has been completed.

---

# 11. Worker App Functional Requirements

## FR-21 Worker Login

Workers shall be able to securely log in to the Worker App.

## FR-22 Assigned Complaint Dashboard

Workers shall be able to view complaints assigned to them.

## FR-23 Complaint Details

Workers shall be able to view:

* Complaint image
* Description
* Category
* Location
* Submission time
* SLA information

## FR-24 Location Access

Workers shall be able to view the complaint location.

## FR-25 Accept Assignment

Workers shall be able to accept assigned complaints.

## FR-26 Work Status

Workers shall be able to update complaint work status.

## FR-27 Resolution Evidence

Workers shall be able to upload resolution photographs and notes.

## FR-28 Resolution Submission

Workers shall be able to submit completed work for departmental verification.

## FR-29 Work History

Workers shall be able to view their previous assignments.

---

# 12. Department Administration Requirements

## FR-30 Department Admin Login

Department administrators shall authenticate before accessing the administration portal.

## FR-31 Department Dashboard

The dashboard shall display department complaint statistics.

## FR-32 Complaint Verification

Administrators shall verify submitted complaints.

## FR-33 Complaint Rejection

Administrators shall be able to reject invalid complaints and provide reasons.

## FR-34 Worker Management

Administrators shall be able to manage workers belonging to their department.

## FR-35 Worker Availability

Administrators shall be able to view worker availability and workload.

## FR-36 Complaint Assignment

Administrators shall assign verified complaints to appropriate workers.

## FR-37 Complaint Reassignment

Administrators shall be able to reassign complaints when required.

## FR-38 Complaint Monitoring

Administrators shall monitor complaint progress.

## FR-39 SLA Monitoring

Administrators shall monitor SLA deadlines and breaches.

## FR-40 Resolution Verification

Administrators shall review worker-submitted resolution evidence.

## FR-41 Resolution Approval

Administrators shall approve valid resolutions.

## FR-42 Resolution Rejection

Administrators shall reject insufficient resolutions and provide a reason.

## FR-43 Department Reports

Administrators shall be able to generate department-level reports.

---

# 13. System Administration Requirements

## FR-44 System Admin Login

System administrators shall securely authenticate.

## FR-45 Department Management

System administrators shall create, update, activate, and deactivate departments.

## FR-46 Category Management

System administrators shall manage complaint categories.

## FR-47 Department Mapping

System administrators shall configure mappings between complaint categories and departments.

## FR-48 User Management

System administrators shall manage citizen accounts.

## FR-49 Worker Management

System administrators shall manage worker accounts and department associations.

## FR-50 Department Administrator Management

System administrators shall manage department administrator accounts.

## FR-51 System Monitoring

System administrators shall monitor complaints across all departments.

## FR-52 System Reports

System administrators shall generate system-wide reports.

---

# 14. Notification Requirements

## FR-53 Citizen Notifications

Citizens should receive notifications for important complaint events.

## FR-54 Worker Notifications

Workers shall receive notifications when complaints are assigned or reassigned.

## FR-55 Administrator Notifications

Department administrators should receive notifications about new complaints and important SLA events.

---

# 15. Search and Filtering

## FR-56 Complaint Search

Authorized users shall be able to search complaints using complaint ID and other relevant fields.

## FR-57 Complaint Filtering

Administrators shall be able to filter complaints by:

* Status
* Category
* Date
* Department
* Worker
* SLA condition

---

# 16. Reporting Requirements

## FR-58 Complaint Statistics

The system shall provide complaint statistics.

## FR-59 Department Performance

The system shall provide department-level performance information.

## FR-60 Worker Performance

The system may provide worker workload and resolution information.

## FR-61 SLA Reports

The system shall provide SLA breach information.

## FR-62 Feedback Reports

The system may provide citizen feedback statistics.

---

# 17. Non-Functional Requirements

## 17.1 Performance

### NFR-01

Normal API requests should respond within approximately 3 seconds under normal operating conditions.

### NFR-02

AI processing should preferably complete within approximately 10 seconds under normal service conditions.

### NFR-03

The system shall support multiple concurrent users.

---

## 17.2 Availability

### NFR-04

The system should be available 24/7 except during planned maintenance.

### NFR-05

External service failures shall not cause loss of complaint data.

---

## 17.3 Reliability

### NFR-06

The system shall prevent duplicate complaint submissions caused by repeated requests.

### NFR-07

Regular backups shall be maintained for important system data.

### NFR-08

The system shall support data recovery following system failures.

---

## 17.4 Security

### NFR-09

All production client-server communication shall use HTTPS/TLS.

### NFR-10

Role-based access control shall be enforced by the backend.

### NFR-11

Passwords shall use secure hashing.

### NFR-12

Authentication tokens shall be securely managed.

### NFR-13

Sensitive credentials shall not be stored in source code.

### NFR-14

The system shall validate and sanitize external input.

### NFR-15

The system shall provide protection against common web and API security threats.

---

## 17.5 Privacy

### NFR-16

Personal information shall only be accessible to authorized users.

### NFR-17

Complaint location data shall be protected.

### NFR-18

Complaint and resolution images shall be securely stored.

---

## 17.6 Usability

### NFR-19

The Citizen App shall provide a simple complaint-submission process.

### NFR-20

The Worker App shall provide quick access to assigned work.

### NFR-21

Complaint statuses shall be presented clearly.

### NFR-22

Error messages shall be understandable.

### NFR-23

The mobile applications shall follow basic accessibility practices.

---

## 17.7 Scalability

### NFR-24

The backend shall support an increasing number of users.

### NFR-25

The database shall support increasing complaint volume.

### NFR-26

The architecture should support horizontal scaling.

---

## 17.8 Maintainability

### NFR-27

The backend shall use a modular architecture.

### NFR-28

The source code shall follow consistent coding standards.

### NFR-29

Critical APIs and components shall be documented.

### NFR-30

Application errors and important system events shall be logged.

---

## 17.9 Compatibility

### NFR-31

The mobile applications shall support the targeted Android versions.

### NFR-32

The administration portal shall support modern web browsers.

---

# 18. User Stories

## Citizen Stories

### US-01

**As a citizen, I want to create an account so that I can submit and track complaints.**

### US-02

**As a citizen, I want to capture a photo of a civic issue so that I can report it visually.**

### US-03

**As a citizen, I want AI to generate a complaint description so that I do not have to write everything manually.**

### US-04

**As a citizen, I want to edit the AI-generated description so that I can correct inaccurate information.**

### US-05

**As a citizen, I want my location to be captured using GPS so that the responsible department knows where the problem exists.**

### US-06

**As a citizen, I want to track my complaint so that I know its current status.**

### US-07

**As a citizen, I want to view the resolution so that I can see whether the issue was actually addressed.**

### US-08

**As a citizen, I want to provide feedback so that I can evaluate the service.**

---

## Worker Stories

### US-09

**As a worker, I want to view complaints assigned to me so that I know which issues I need to resolve.**

### US-10

**As a worker, I want to view the complaint location so that I can travel to the reported location.**

### US-11

**As a worker, I want to update my work status so that the department can track progress.**

### US-12

**As a worker, I want to upload resolution proof so that the department can verify my work.**

### US-13

**As a worker, I want to submit a resolution for verification so that the complaint can be reviewed by the department administrator.**

---

## Department Administrator Stories

### US-14

**As a department administrator, I want to verify complaints so that only valid complaints are sent for field work.**

### US-15

**As a department administrator, I want to assign complaints to workers so that civic issues can be resolved.**

### US-16

**As a department administrator, I want to monitor worker progress so that I can ensure complaints are handled on time.**

### US-17

**As a department administrator, I want to monitor SLA deadlines so that delayed complaints can be identified.**

### US-18

**As a department administrator, I want to verify worker resolutions so that complaints are only completed after the work is confirmed.**

---

## System Administrator Stories

### US-19

**As a system administrator, I want to manage departments so that complaints can be routed correctly.**

### US-20

**As a system administrator, I want to manage users and workers so that the platform remains organized and secure.**

### US-21

**As a system administrator, I want to monitor complaints across departments so that I can evaluate overall system performance.**

---

# 19. Business Rules

## BR-01: Complaint Ownership

Every complaint shall belong to the citizen who submitted it.

## BR-02: Required Information

A complaint shall contain the required image, description, category, and location before submission.

## BR-03: AI Confirmation

AI-generated descriptions shall require citizen review before becoming the final description.

## BR-04: Department Mapping

Complaint categories shall be mapped to responsible departments.

## BR-05: Complaint Verification

Only authorized department administrators can verify complaints.

## BR-06: Assignment

Only verified complaints can be assigned to workers.

## BR-07: Department Matching

A complaint shall only be assigned to a worker belonging to the responsible department.

## BR-08: Worker Completion Restriction

Workers cannot directly mark complaints as completed.

## BR-09: Resolution Verification

Worker resolutions must be verified by a department administrator.

## BR-10: SLA

SLA deadlines shall be calculated by the backend.

## BR-11: SLA Breach

An SLA breach shall not automatically mark a complaint as completed.

## BR-12: Status Authorization

Users cannot arbitrarily modify complaint statuses.

## BR-13: Feedback

Citizen feedback shall only be available after complaint completion.

## BR-14: Audit Trail

Important complaint actions shall be recorded.

---

# 20. System Constraints

## SC-01 Technology

The backend shall use Java Spring Boot.

## SC-02 API

The backend shall expose RESTful APIs.

## SC-03 Database

The system shall use a relational database.

## SC-04 Mobile Applications

The system shall provide separate Citizen and Worker mobile applications.

## SC-05 Administration

Administrative functions shall be provided through a web interface.

## SC-06 Backend Authority

The backend shall be the authoritative source for business rules and system state.

## SC-07 External Services

AI, maps, notifications, and other integrations shall be modular.

## SC-08 AI Dependency

The AI service may become unavailable and the system shall provide appropriate fallback behavior.

## SC-09 GPS Dependency

Location accuracy and availability depend on the user's device and environment.

## SC-10 Image Constraints

Uploaded images shall be validated and subject to configurable size limits.

## SC-11 Security

Production communication shall use HTTPS/TLS.

## SC-12 Secrets

API keys, passwords, tokens, and other sensitive credentials shall not be committed to source control.

## SC-13 Workflow

The system shall enforce the defined complaint lifecycle.

## SC-14 Worker Permissions

Workers cannot assign complaints or approve resolutions.

## SC-15 Department Isolation

Department administrators shall only access authorized department information.

## SC-16 Network

Core operations require network connectivity.

## SC-17 Deployment

The Spring Boot backend shall be deployable on a suitable server or cloud environment.

## SC-18 Version Control

Source code shall be maintained using Git or an equivalent version-control system.

---

# 21. Data Requirements

## 21.1 User Data

The system shall maintain:

* User ID
* Name
* Email
* Mobile number
* Password credential information
* Role
* Account status
* Creation date

---

## 21.2 Complaint Data

Each complaint shall maintain:

* Complaint ID
* Citizen ID
* Image reference
* AI-generated description
* Final description
* Category
* Department
* Latitude
* Longitude
* Location accuracy where available
* Submission timestamp
* Current status
* SLA information

---

## 21.3 Assignment Data

Assignment records shall contain:

* Assignment ID
* Complaint ID
* Worker ID
* Department ID
* Assigning administrator
* Assignment timestamp
* Assignment status

---

## 21.4 Resolution Data

Resolution records shall contain:

* Resolution ID
* Complaint ID
* Worker ID
* Resolution description
* Resolution images
* Submission timestamp
* Verification status
* Verification administrator
* Verification timestamp
* Rejection reason where applicable

---

## 21.5 Feedback Data

Feedback may contain:

* Feedback ID
* Complaint ID
* Citizen ID
* Rating
* Comment
* Submission timestamp

---

## 21.6 Audit Data

Audit records shall contain:

* Audit ID
* User ID
* Action
* Entity/complaint ID
* Previous value/status
* New value/status
* Timestamp

---

# 22. Complaint Lifecycle

The complete complaint lifecycle shall be:

```text
                 Citizen
                    │
                    ▼
              Capture Image
                    │
                    ▼
              AI Description
                    │
                    ▼
             Citizen Reviews
                    │
                    ▼
              Capture GPS
                    │
                    ▼
            Submit Complaint
                    │
                    ▼
                Submitted
                    │
                    ▼
          Department Verification
               /          \
          Reject           Verify
            │                │
            ▼                ▼
        Rejected          Verified
                              │
                              ▼
                       Assign Worker
                              │
                              ▼
                          Assigned
                              │
                              ▼
                         Worker App
                              │
                              ▼
                         In Progress
                              │
                              ▼
                       Resolve Issue
                              │
                              ▼
                    Upload Resolution Proof
                              │
                              ▼
                 Resolution Under Verification
                         /           \
                    Reject            Approve
                      │                  │
                      ▼                  ▼
                 In Progress          Completed
                                         │
                                         ▼
                                      Citizen
                                         │
                                         ▼
                                      Feedback
```

---

# 23. Complaint Status Definitions

| Status                        | Description                                          |
| ----------------------------- | ---------------------------------------------------- |
| Submitted                     | Complaint has been submitted by the citizen          |
| Verified                      | Department administrator has approved the complaint  |
| Assigned                      | Complaint has been assigned to a worker              |
| In Progress                   | Worker is handling the issue                         |
| Resolution Under Verification | Worker has submitted resolution proof                |
| Completed                     | Department administrator has approved the resolution |
| Rejected                      | Complaint has been rejected                          |
| SLA Breached                  | Complaint has exceeded its configured SLA            |

`SLA Breached` shall be treated as a monitoring condition rather than a replacement for the actual workflow status.

---

# 24. External System Interfaces

## 24.1 AI Service

The backend may communicate with an external AI service to analyze complaint images.

The integration shall support:

* Image submission
* AI response retrieval
* Generated description
* Category suggestion where supported
* Error handling
* Timeout handling

---

## 24.2 GPS/Location Services

The Citizen App shall use Android location services to capture complaint coordinates.

The Worker App may also use location services where required for field operations.

---

## 24.3 Map Service

A map provider may be integrated to display complaint locations and assist workers with navigation.

---

## 24.4 Notification Service

The system may use push notifications, email, SMS, or another suitable notification service.

Notification failure shall not invalidate the underlying business transaction.

---

# 25. API Requirements

The Spring Boot backend shall expose secure APIs for the client applications.

Major API groups should include:

```text
/auth
/users
/complaints
/categories
/departments
/workers
/assignments
/resolutions
/notifications
/feedback
/reports
/audit
```

---

## 25.1 Authentication APIs

Examples:

```text
POST /api/auth/register
POST /api/auth/login
POST /api/auth/logout
```

---

## 25.2 Complaint APIs

Examples:

```text
POST   /api/complaints
GET    /api/complaints/{id}
GET    /api/complaints/my
PATCH  /api/complaints/{id}
```

---

## 25.3 Worker APIs

Examples:

```text
GET   /api/worker/complaints
GET   /api/worker/complaints/{id}
PATCH /api/worker/complaints/{id}/status
POST  /api/worker/complaints/{id}/resolution
```

---

## 25.4 Administration APIs

Examples:

```text
GET  /api/admin/complaints
PATCH /api/admin/complaints/{id}/verify
PATCH /api/admin/complaints/{id}/reject
POST /api/admin/assignments
PATCH /api/admin/assignments/{id}
```

---

# 26. Security Requirements

## 26.1 Authentication

All protected resources shall require authentication.

## 26.2 Authorization

Authorization shall be enforced based on user roles.

## 26.3 Password Protection

Passwords shall be securely hashed.

## 26.4 Token Security

Authentication tokens shall have appropriate expiration and validation.

## 26.5 Input Validation

All client input shall be validated by the backend.

## 26.6 File Security

Uploaded files shall be validated before storage or processing.

## 26.7 Access Control

Users shall not be able to access data outside their permissions.

## 26.8 Audit Logging

Important administrative and complaint-related actions shall be logged.

---

# 27. Error Handling

The system shall handle common errors gracefully.

Examples include:

* Invalid login credentials
* Network failure
* GPS unavailable
* Location permission denied
* Invalid image
* Image upload failure
* AI service unavailable
* Database failure
* Unauthorized API access
* Invalid complaint status transition
* Worker unavailable
* Notification failure

The backend shall return consistent error responses.

Sensitive implementation details shall not be exposed to users.

---

# 28. Reporting and Analytics

The system shall support reports including:

## Complaint Reports

* Total complaints
* Complaints by category
* Complaints by status
* Complaints by department
* Complaints by date

## Resolution Reports

* Completed complaints
* Average resolution time
* Pending resolutions
* Rejected resolutions

## SLA Reports

* SLA-compliant complaints
* SLA-breached complaints
* Average resolution time
* Category-wise SLA performance

## Worker Reports

* Assigned complaints
* Completed complaints
* Pending assignments
* Workload

## Department Reports

* Total department complaints
* Resolution rate
* Average resolution time
* SLA performance

---

# 29. Assumptions and Dependencies

## 29.1 Assumptions

The system assumes that:

* Citizens have Android smartphones.
* Citizens can provide required permissions.
* Workers have smartphones capable of running the Worker App.
* Workers have network connectivity during normal operations.
* Government departments have authorized administrators.
* Departments maintain worker information.
* AI services are available through an API or compatible integration.
* GPS services are available on supported devices.

---

## 29.2 Dependencies

CivicSnap may depend on:

* Android operating system
* Spring Boot
* Relational database
* AI service
* Map/location services
* Notification service
* Internet connectivity
* File/object storage

Failure of an external dependency shall be isolated wherever possible.

---

# 30. Out of Scope

The following features are outside the initial project scope unless specifically added later:

* Direct integration with existing government enterprise systems
* Custom AI model training from scratch
* Automated physical repair
* Payment processing
* Government employee payroll
* Procurement management
* Full municipal financial management
* Emergency response management
* IoT-based automatic civic issue detection

---

# 31. Future Enhancements

Potential future features include:

* Multilingual citizen interface
* Voice-based complaint submission
* Advanced AI complaint classification
* AI-based duplicate complaint detection
* Predictive maintenance
* Advanced GIS analytics
* IoT sensor integration
* Government-system integration
* Citizen web portal
* Advanced notification channels
* Real-time worker location tracking
* Advanced analytics dashboards
* Automatic priority calculation
* Smart worker assignment
* Public civic issue heatmaps

---

# 32. Requirement Traceability

The major project artifacts shall maintain the following relationship:

```text
Project Objectives
       │
       ▼
Problem Statement
       │
       ▼
User Stories
       │
       ▼
Functional Requirements
       │
       ▼
Business Rules
       │
       ▼
System Design
       │
       ▼
Implementation
       │
       ▼
Testing
```

---

## 32.1 Traceability Examples

| User Story                   | Functional Requirement | Business Rule       |
| ---------------------------- | ---------------------- | ------------------- |
| Citizen captures issue       | FR-06                  | BR-02               |
| Citizen uses AI description  | FR-09, FR-10           | BR-03               |
| Citizen confirms description | FR-12                  | BR-03               |
| Citizen captures location    | FR-13                  | BR-02               |
| Citizen submits complaint    | FR-15                  | BR-01, BR-02        |
| Admin verifies complaint     | FR-32                  | BR-05               |
| Admin assigns worker         | FR-36                  | BR-06, BR-07        |
| Worker views assignment      | FR-22                  | Worker access rules |
| Worker resolves issue        | FR-27, FR-28           | BR-08               |
| Admin verifies resolution    | FR-40, FR-41           | BR-09               |
| SLA monitoring               | FR-39                  | BR-10, BR-11        |
| Citizen gives feedback       | FR-20                  | BR-13               |

---

# 33. Acceptance Criteria

The CivicSnap system shall be considered functionally acceptable when the following core workflow can be completed successfully:

## Citizen

1. Citizen registers/logs in.
2. Citizen captures a civic issue image.
3. Image is uploaded.
4. AI generates a suggested description.
5. Citizen reviews/edits the description.
6. GPS location is captured.
7. Citizen submits the complaint.
8. Unique complaint ID is generated.
9. Citizen can track the complaint.

## Department Administrator

10. Department administrator receives the complaint.
11. Administrator reviews the complaint.
12. Administrator verifies the complaint.
13. Administrator assigns it to an eligible worker.
14. Administrator can monitor its progress.
15. Administrator can monitor SLA status.

## Worker

16. Worker receives the assignment.
17. Worker views complaint details and location.
18. Worker accepts the assignment.
19. Worker updates the work status.
20. Worker resolves the civic issue.
21. Worker uploads resolution proof.
22. Worker submits the resolution.

## Department Administrator

23. Administrator reviews resolution evidence.
24. Administrator approves or rejects the resolution.
25. If approved, the complaint becomes `Completed`.
26. If rejected, the complaint returns to `In Progress`.

## Citizen

27. Citizen sees the final status.
28. Citizen can view resolution details.
29. Citizen can provide feedback.

---

# 34. Core Security Acceptance Criteria

The system shall also satisfy the following:

* Unauthorized users cannot access protected APIs.
* Citizens cannot access other citizens' complaints.
* Workers cannot access unrelated assignments.
* Department administrators cannot access unauthorized department data.
* Workers cannot approve their own resolutions.
* Workers cannot directly complete complaints.
* Citizens cannot change official complaint statuses.
* Sensitive credentials are not stored in source code.
* Passwords are never stored as plain text.
* Uploaded images are protected from unauthorized access.

---

# 35. Core System Acceptance Criteria

The system shall:

* Maintain complaint ownership.
* Generate unique complaint IDs.
* Maintain complaint status history.
* Maintain assignment history.
* Maintain resolution history.
* Maintain audit records.
* Preserve data when external services fail.
* Prevent invalid status transitions.
* Prevent duplicate complaint submissions.
* Calculate SLA deadlines through the backend.
* Support separate Citizen and Worker applications.
* Support Department and System Administration.
* Use Java Spring Boot as the backend technology.
* Use a relational database for structured data.

---

# 36. Final System Workflow

The complete CivicSnap platform can be summarized as:

```text
                         CIVICSNAP
                             │
             ┌───────────────┼────────────────┐
             │               │                │
             ▼               ▼                ▼
        CITIZEN APP      WORKER APP      ADMIN PORTAL
             │               │                │
             │               │        ┌───────┴───────┐
             │               │        │               │
             │               │        ▼               ▼
             │               │   Department       System
             │               │   Admin            Admin
             │               │
             └───────────────┼────────────────┐
                             ▼                │
                    SPRING BOOT BACKEND      │
                             │                │
             ┌───────────────┼────────────────┤
             │               │                │
             ▼               ▼                ▼
         Database       AI Service       External Services
                             │
                             ▼
                      Image Analysis
                             │
                             ▼
                    Complaint Workflow
                             │
                             ▼
       Submitted → Verified → Assigned → In Progress
                             │
                             ▼
                Resolution Under Verification
                             │
                       ┌─────┴─────┐
                       │           │
                    Reject       Approve
                       │           │
                       ▼           ▼
                  In Progress   Completed
                                   │
                                   ▼
                                Feedback
```

---

# 37. Conclusion

CivicSnap is a multi-role civic complaint management platform that connects citizens, government departments, and field workers through a centralized digital workflow.

The system simplifies complaint reporting through image capture, AI-assisted description generation, and GPS location capture. It provides departments with tools for verification, worker assignment, SLA monitoring, and resolution validation.

The dedicated **Worker App** forms an essential part of the system by allowing field workers to receive assignments, access complaint information, update work status, and submit resolution proof.

The **Spring Boot backend** acts as the authoritative system layer, enforcing authentication, authorization, business rules, complaint status transitions, assignments, SLA calculations, and resolution verification.

The complete workflow ensures that a complaint is not considered completed merely because a worker reports it as resolved. Instead, the worker submits evidence and the **Department Administrator verifies the resolution**, providing greater accountability and transparency.

The requirements defined in this SRS provide the foundation for the subsequent system design, database design, API design, implementation, testing, and deployment of CivicSnap.
