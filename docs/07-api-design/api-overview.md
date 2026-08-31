# CivicSnap — API Overview

## 1. Introduction

The CivicSnap API provides the communication layer between the client applications and the backend system.

The system consists of three primary clients:

* Citizen App — Flutter + Dart
* Worker App — Flutter + Dart
* Admin Portal — React + TypeScript

All clients communicate with the Java Spring Boot backend through secure REST APIs.

```text
┌─────────────────────┐
│    Citizen App      │
│   Flutter + Dart    │
└──────────┬──────────┘
           │
           │
┌──────────▼──────────┐
│                     │
│  CivicSnap REST API │
│  Java + Spring Boot │
│                     │
└──────────┬──────────┘
           │
     ┌─────┼─────┐
     │     │     │
     ▼     ▼     ▼
 PostgreSQL  AI  Storage
     │
     ▼
 Business Logic
     ▲
     │
┌────┴──────────────┐
│                   │
│ Other Clients     │
│                   │
│ Worker App        │
│ Admin Portal      │
└───────────────────┘
```

---

# 2. API Objectives

The CivicSnap API shall:

1. Provide secure communication between clients and the backend.
2. Manage authentication and authorization.
3. Manage citizen complaints.
4. Manage departments and categories.
5. Manage workers and assignments.
6. Manage complaint status.
7. Manage resolution submissions.
8. Manage resolution verification.
9. Manage SLA tracking.
10. Manage notifications.
11. Manage citizen feedback.
12. Provide administrative reports.
13. Maintain audit information.
14. Integrate with external services such as AI, Maps, and Firebase.

---

# 3. API Technology Stack

| Component         | Technology                  |
| ----------------- | --------------------------- |
| API Architecture  | REST                        |
| Backend           | Java                        |
| Framework         | Spring Boot                 |
| API Format        | JSON                        |
| Transport         | HTTPS                       |
| Authentication    | JWT                         |
| Authorization     | Spring Security             |
| Database          | PostgreSQL                  |
| ORM               | Spring Data JPA / Hibernate |
| API Documentation | OpenAPI / Swagger           |
| API Testing       | Postman                     |

---

# 4. API Base URL

The API shall use an environment-specific base URL.

Example:

```text
Development:
http://localhost:8080/api

Production:
https://<production-domain>/api
```

The actual production domain shall be configured during deployment.

---

# 5. API Versioning

The API should support versioning to prevent future changes from breaking existing applications.

Recommended format:

```text
/api/v1
```

Example:

```text
/api/v1/auth/login
/api/v1/complaints
/api/v1/worker/complaints
/api/v1/admin/departments
```

Future versions may use:

```text
/api/v2
```

---

# 6. HTTP Methods

The API shall use standard HTTP methods.

| Method | Purpose                      |
| ------ | ---------------------------- |
| GET    | Retrieve data                |
| POST   | Create a resource            |
| PUT    | Replace a resource           |
| PATCH  | Partially update a resource  |
| DELETE | Delete/deactivate a resource |

Example:

```text
GET     /api/v1/complaints
POST    /api/v1/complaints
GET     /api/v1/complaints/{id}
PATCH   /api/v1/complaints/{id}
```

---

# 7. Authentication

Authentication shall use JWT-based authentication.

```text
Client
  │
  ▼
POST /auth/login
  │
  ▼
Spring Security
  │
  ▼
Validate Credentials
  │
  ▼
JWT Token
  │
  ▼
Client
```

Subsequent protected requests shall include:

```text
Authorization: Bearer <JWT_TOKEN>
```

---

# 8. User Roles

The API shall support four primary roles:

```text
CITIZEN
WORKER
DEPARTMENT_ADMIN
SYSTEM_ADMIN
```

Role-based authorization shall be enforced by Spring Security and backend business logic.

---

# 9. API Access Model

```text
Citizen
   │
   └── Own complaints

Worker
   │
   └── Assigned complaints

Department Admin
   │
   └── Own department data

System Admin
   │
   └── System-wide administrative data
```

The client applications shall not be trusted to enforce these restrictions independently.

---

# 10. Authentication API

## 10.1 Register

```http
POST /api/v1/auth/register
```

### Purpose

Creates a new citizen account.

### Request

```json
{
  "fullName": "John Doe",
  "email": "john@example.com",
  "phone": "9876543210",
  "password": "********"
}
```

### Response

```json
{
  "message": "Registration successful"
}
```

---

# 11. Login

```http
POST /api/v1/auth/login
```

### Request

```json
{
  "email": "john@example.com",
  "password": "********"
}
```

### Response

```json
{
  "accessToken": "<jwt-token>",
  "tokenType": "Bearer",
  "user": {
    "id": "USER-101",
    "fullName": "John Doe",
    "role": "CITIZEN"
  }
}
```

---

# 12. Refresh Token

If refresh tokens are implemented:

```http
POST /api/v1/auth/refresh
```

The endpoint shall issue a new access token after validating the refresh token.

---

# 13. Logout

```http
POST /api/v1/auth/logout
```

The endpoint may invalidate refresh tokens or registered sessions where applicable.

---

# 14. User API

## Get Current User

```http
GET /api/v1/users/me
```

Returns the authenticated user's profile.

---

## Update Profile

```http
PATCH /api/v1/users/me
```

Allows a user to update permitted profile information.

---

# 15. Complaint API

The complaint API is the primary API module of CivicSnap.

Main operations include:

```text
Create Complaint
Get Complaint
List Complaints
Update Complaint
Submit Complaint
Track Complaint
View Complaint History
```

---

# 16. Create Complaint

```http
POST /api/v1/complaints
```

### Purpose

Creates a new complaint.

### Request

```json
{
  "categoryId": "CAT-001",
  "title": "Large pothole",
  "description": "There is a large pothole near the junction.",
  "latitude": 12.123456,
  "longitude": 75.123456,
  "locationAccuracy": 10.5
}
```

The complaint may initially be created as a draft if the application supports draft functionality.

---

# 17. Submit Complaint

```http
POST /api/v1/complaints/{complaintId}/submit
```

Changes a draft complaint into a submitted complaint.

The backend shall validate required information before submission.

---

# 18. Get Complaint

```http
GET /api/v1/complaints/{complaintId}
```

Returns complaint details according to the authenticated user's permissions.

---

# 19. Get Citizen Complaints

```http
GET /api/v1/complaints/my
```

Returns complaints belonging to the authenticated citizen.

Supported filters may include:

```text
status
category
priority
dateFrom
dateTo
page
size
```

Example:

```text
GET /api/v1/complaints/my?status=IN_PROGRESS&page=0&size=20
```

---

# 20. Complaint Images

## Upload Complaint Image

```http
POST /api/v1/complaints/{complaintId}/images
```

The endpoint shall accept multipart image uploads.

The backend shall validate:

* File type
* File size
* Image integrity
* User authorization

---

# 21. Get Complaint Images

```http
GET /api/v1/complaints/{complaintId}/images
```

Returns metadata or secure references for complaint images.

---

# 22. AI Complaint Assistance

## Generate AI Description

```http
POST /api/v1/complaints/{complaintId}/ai-description
```

The backend sends the relevant complaint image/data to the configured AI service.

### Response

```json
{
  "suggestedDescription": "A large pothole is visible on the road surface.",
  "suggestedCategory": "Road Damage"
}
```

The AI response is only a suggestion.

The final complaint description shall be confirmed by the citizen.

---

# 23. Category API

## Get Categories

```http
GET /api/v1/categories
```

Returns active complaint categories available to the authenticated user.

Optional filtering:

```text
departmentId
```

Example:

```text
GET /api/v1/categories?departmentId=DEP-001
```

---

# 24. Department API

## Get Departments

```http
GET /api/v1/departments
```

Returns active departments where permitted.

---

## Get Department

```http
GET /api/v1/departments/{departmentId}
```

Returns department information.

---

# 25. Worker API

The Worker API is specifically designed for the separate Flutter Worker App.

Worker operations include:

```text
Worker Dashboard
Assigned Complaints
Complaint Details
Accept Assignment
Start Work
Submit Resolution
View Work History
```

---

# 26. Worker Dashboard

```http
GET /api/v1/worker/dashboard
```

Returns summary information relevant to the authenticated worker.

Example:

```json
{
  "assigned": 8,
  "inProgress": 3,
  "pendingResolution": 2,
  "completed": 15
}
```

The dashboard shall only contain work relevant to that worker's authorized department and assignments.

---

# 27. Worker Assigned Complaints

```http
GET /api/v1/worker/complaints
```

Returns complaints assigned to the authenticated worker.

Possible filters:

```text
status
priority
date
page
size
```

The endpoint shall not return unrelated departmental complaints.

---

# 28. Accept Assignment

```http
POST /api/v1/worker/assignments/{assignmentId}/accept
```

The worker accepts an assignment.

Possible state:

```text
ASSIGNED
    ↓
ACCEPTED
```

---

# 29. Start Work

```http
POST /api/v1/worker/complaints/{complaintId}/start
```

Changes the complaint to:

```text
IN_PROGRESS
```

The backend shall verify that the worker has an active assignment.

---

# 30. Worker Complaint Details

```http
GET /api/v1/worker/complaints/{complaintId}
```

Returns the complaint information required for field work.

Information may include:

* Complaint title
* Description
* Category
* Priority
* Location
* Images
* SLA deadline
* Assignment details

---

# 31. Submit Resolution

```http
POST /api/v1/worker/complaints/{complaintId}/resolution
```

### Request

```json
{
  "description": "The damaged road section has been repaired."
}
```

The worker must have a valid assignment before submitting a resolution.

---

# 32. Upload Resolution Evidence

```http
POST /api/v1/resolutions/{resolutionId}/images
```

The Worker App may upload photographs showing completed work.

The backend shall validate the files before storing them.

---

# 33. Department Admin API

Department Administrators shall use the Admin Portal for department-level operations.

The API shall provide:

```text
Complaint Review
Worker Management
Assignment
Reassignment
SLA Monitoring
Resolution Verification
Department Reports
```

---

# 34. Department Complaint List

```http
GET /api/v1/admin/complaints
```

Returns complaints belonging to the authenticated Department Administrator's department.

Possible filters:

```text
status
category
priority
worker
dateFrom
dateTo
slaStatus
page
size
```

The backend shall automatically restrict results to the administrator's department.

---

# 35. Verify Complaint

```http
POST /api/v1/admin/complaints/{complaintId}/verify
```

Changes:

```text
SUBMITTED
    ↓
VERIFIED
```

The backend must verify that the administrator has access to the complaint's department.

---

# 36. Reject Complaint

```http
POST /api/v1/admin/complaints/{complaintId}/reject
```

### Request

```json
{
  "reason": "Insufficient evidence provided."
}
```

The complaint shall be marked as rejected.

---

# 37. Worker List

```http
GET /api/v1/admin/workers
```

Returns workers belonging to the authenticated administrator's department.

System Administrators may retrieve workers across departments.

---

# 38. Worker Details

```http
GET /api/v1/admin/workers/{workerId}
```

Returns worker information according to authorization.

---

# 39. Assign Complaint

```http
POST /api/v1/admin/complaints/{complaintId}/assign
```

### Request

```json
{
  "workerId": "WORKER-204"
}
```

The backend shall validate:

1. Complaint exists.
2. Complaint is assignable.
3. Worker exists.
4. Worker is active.
5. Worker belongs to the complaint's department.
6. Administrator is authorized.
7. No conflicting active assignment exists.

---

# 40. Reassign Complaint

```http
POST /api/v1/admin/complaints/{complaintId}/reassign
```

### Request

```json
{
  "workerId": "WORKER-205",
  "reason": "Previous worker unavailable."
}
```

The previous assignment shall remain in the assignment history.

---

# 41. Resolution Review

## Get Pending Resolutions

```http
GET /api/v1/admin/resolutions/pending
```

Returns resolutions requiring departmental verification.

---

# 42. Approve Resolution

```http
POST /api/v1/admin/resolutions/{resolutionId}/approve
```

Successful approval shall result in the complaint becoming eligible for completion.

---

# 43. Reject Resolution

```http
POST /api/v1/admin/resolutions/{resolutionId}/reject
```

### Request

```json
{
  "reason": "Repair evidence is insufficient."
}
```

The complaint shall return to the appropriate work state.

---

# 44. SLA API

The SLA API provides service-level monitoring.

## Get SLA Information

```http
GET /api/v1/complaints/{complaintId}/sla
```

Possible response:

```json
{
  "targetHours": 48,
  "deadlineAt": "2026-09-01T10:00:00Z",
  "status": "ACTIVE"
}
```

---

# 45. SLA Dashboard

```http
GET /api/v1/admin/sla
```

Returns authorized SLA information.

Possible filters:

```text
department
status
priority
category
dateFrom
dateTo
```

---

# 46. Notification API

## Get Notifications

```http
GET /api/v1/notifications
```

Returns notifications for the authenticated user.

---

## Mark Notification as Read

```http
PATCH /api/v1/notifications/{notificationId}/read
```

---

## Mark All as Read

```http
PATCH /api/v1/notifications/read-all
```

The backend shall ensure that users can only modify their own notifications.

---

# 47. Feedback API

## Submit Feedback

```http
POST /api/v1/complaints/{complaintId}/feedback
```

### Request

```json
{
  "rating": 5,
  "comment": "The issue was resolved quickly."
}
```

The backend shall verify:

* User is the complaint owner.
* Complaint is completed.
* Feedback has not already been submitted if only one submission is permitted.

---

# 48. Get Feedback

```http
GET /api/v1/complaints/{complaintId}/feedback
```

Access shall be controlled based on user role and complaint ownership.

---

# 49. System Admin API

System Administrators have platform-wide administrative APIs.

These include:

```text
Users
Departments
Categories
Workers
System Settings
Reports
Audit Logs
```

---

# 50. User Management

## List Users

```http
GET /api/v1/system/users
```

Supported filters may include:

```text
role
department
status
search
page
size
```

---

## Deactivate User

```http
PATCH /api/v1/system/users/{userId}/deactivate
```

The system should prefer deactivation over physical deletion when historical records exist.

---

# 51. Department Management

## Create Department

```http
POST /api/v1/system/departments
```

### Request

```json
{
  "name": "Road/Public Works",
  "description": "Responsible for road-related civic issues."
}
```

---

## Update Department

```http
PATCH /api/v1/system/departments/{departmentId}
```

---

## Deactivate Department

```http
PATCH /api/v1/system/departments/{departmentId}/deactivate
```

Historical records shall remain intact.

---

# 52. Category Management

## Create Category

```http
POST /api/v1/system/categories
```

### Request

```json
{
  "name": "Road Damage",
  "description": "Potholes and damaged road surfaces.",
  "departmentId": "DEP-001",
  "defaultSlaHours": 48
}
```

---

## Update Category

```http
PATCH /api/v1/system/categories/{categoryId}
```

---

## Deactivate Category

```http
PATCH /api/v1/system/categories/{categoryId}/deactivate
```

---

# 53. Reporting API

Reports shall provide authorized aggregated information.

## Department Report

```http
GET /api/v1/admin/reports/complaints
```

Possible filters:

```text
dateFrom
dateTo
category
status
priority
worker
```

---

# 54. System Report

```http
GET /api/v1/system/reports/overview
```

Possible information:

```text
Total Complaints
Completed Complaints
Pending Complaints
Rejected Complaints
SLA Breaches
Department Performance
Worker Performance
Average Resolution Time
Citizen Feedback
```

---

# 55. Audit API

## Get Audit Logs

```http
GET /api/v1/system/audit-logs
```

Access shall be restricted to authorized System Administrators.

Possible filters:

```text
user
action
entityType
entityId
dateFrom
dateTo
page
size
```

---

# 56. API Response Format

Successful responses should follow a consistent structure.

Example:

```json
{
  "success": true,
  "data": {
    "id": "CS-1001",
    "status": "VERIFIED"
  },
  "message": "Complaint verified successfully"
}
```

For list responses:

```json
{
  "success": true,
  "data": [],
  "pagination": {
    "page": 0,
    "size": 20,
    "totalElements": 100,
    "totalPages": 5
  }
}
```

---

# 57. Error Response Format

Errors should use a consistent structure.

```json
{
  "success": false,
  "error": {
    "code": "COMPLAINT_NOT_FOUND",
    "message": "The requested complaint was not found."
  }
}
```

Validation errors may contain field-level information:

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request.",
    "fields": {
      "description": "Description is required."
    }
  }
}
```

---

# 58. HTTP Status Codes

| Status | Meaning                                  |
| ------ | ---------------------------------------- |
| 200    | Successful request                       |
| 201    | Resource created                         |
| 204    | Successful request with no response body |
| 400    | Bad request                              |
| 401    | Authentication required/invalid          |
| 403    | Access denied                            |
| 404    | Resource not found                       |
| 409    | Conflict                                 |
| 422    | Validation/business rule failure         |
| 429    | Too many requests                        |
| 500    | Internal server error                    |
| 503    | External service unavailable             |

---

# 59. API Validation

Spring Boot validation shall be used for request validation.

Examples:

```text
Required fields
Email format
String length
Numeric ranges
GPS coordinates
Rating range
File size
File type
```

Example:

```text
Latitude:
-90 to +90

Longitude:
-180 to +180

Rating:
1 to 5
```

---

# 60. Authorization Rules

## Citizen

Can:

```text
Register
Login
Create complaints
View own complaints
Update eligible complaints
Submit feedback
View own notifications
```

Cannot:

```text
Access another citizen's complaints
Assign workers
Approve resolutions
Manage departments
Access audit logs
```

---

# 61. Worker

Can:

```text
Login
View assigned complaints
Accept assignments
Start work
Update work status
Submit resolutions
Upload resolution evidence
View own work history
```

Cannot:

```text
Access unrelated complaints
Assign other workers
Approve resolutions
Manage departments
Access system-wide reports
```

---

# 62. Department Administrator

Can:

```text
Review department complaints
Verify/reject complaints
Assign workers
Reassign workers
Monitor SLA
Review resolutions
Approve/reject resolutions
View department reports
```

Cannot:

```text
Manage unrelated departments
Access unrestricted system administration
```

---

# 63. System Administrator

Can:

```text
Manage users
Manage departments
Manage categories
Manage workers
View system reports
View audit logs
Manage system configuration
```

System-wide access shall remain subject to authorization policies.

---

# 64. Department Isolation

Department isolation is a critical API rule.

For a Department Administrator:

```text
Authenticated Admin Department
          =
Requested Resource Department
```

If the values do not match:

```text
HTTP 403 Forbidden
```

The same principle applies to worker assignments.

---

# 65. Worker Isolation

The Worker App must only receive assignments belonging to the authenticated worker.

```text
Authenticated Worker
       =
Assignment Worker
```

The backend shall reject unauthorized requests even if the client manually changes the complaint ID.

---

# 66. Citizen Isolation

A citizen may only retrieve complaints where:

```text
Authenticated User
       =
Complaint Citizen
```

This rule must be enforced server-side.

---

# 67. API Security

The API shall use:

* HTTPS/TLS
* JWT authentication
* Spring Security
* Password hashing
* Input validation
* Authorization checks
* Secure file validation
* Rate limiting where appropriate
* Secure CORS configuration
* Security headers

---

# 68. File Upload Security

Image upload endpoints shall validate:

1. File size.
2. MIME type.
3. File extension.
4. File contents where practical.
5. Authentication.
6. Authorization.
7. Storage destination.

Uploaded files shall not be executed as application code.

---

# 69. API Rate Limiting

Rate limiting may be implemented for sensitive endpoints such as:

```text
Login
Registration
AI Requests
File Upload
Password/Authentication Operations
```

This reduces abuse and excessive external service consumption.

---

# 70. CORS

Cross-Origin Resource Sharing shall be configured to allow only trusted frontend origins.

The production environment shall not use unrestricted wildcard origins unless explicitly required.

---

# 71. Transactional API Operations

Operations affecting multiple records shall use database transactions where required.

Example:

```text
Assign Complaint
      │
      ├── Create Assignment
      ├── Update Complaint
      ├── Update SLA
      ├── Create Audit
      └── Create Notification
```

The backend should maintain consistency if one operation fails.

---

# 72. External Service Integration

The backend may communicate with:

```text
AI Service
Maps/Geocoding Service
Firebase Cloud Messaging
Object Storage
```

Clients should not directly expose service credentials.

---

# 73. AI API Architecture

```text
Flutter Citizen App
        │
        ▼
CivicSnap API
        │
        ▼
AI Integration Service
        │
        ▼
External AI Provider
        │
        ▼
Suggested Result
        │
        ▼
CivicSnap API
        │
        ▼
Citizen App
```

The AI provider shall remain replaceable.

---

# 74. Notification API Architecture

```text
Backend Event
      │
      ▼
Notification Service
      │
      ▼
Firebase Cloud Messaging
      │
      ├────────► Citizen App
      │
      └────────► Worker App
```

Notification failures should not corrupt the primary complaint transaction.

---

# 75. Pagination

List APIs should support pagination.

Example:

```text
GET /api/v1/complaints?page=0&size=20
```

Response:

```json
{
  "page": 0,
  "size": 20,
  "totalElements": 87,
  "totalPages": 5
}
```

Pagination is required for potentially large collections such as:

* Complaints
* Workers
* Notifications
* Audit logs
* Reports

---

# 76. Filtering and Sorting

List endpoints may support filtering and sorting.

Example:

```text
GET /api/v1/admin/complaints
    ?status=IN_PROGRESS
    &priority=HIGH
    &sort=submittedAt,desc
    &page=0
    &size=20
```

The backend shall validate supported filter parameters.

---

# 77. API Logging

The backend should log important API events.

Logging should include:

```text
Request method
Endpoint
Timestamp
Authenticated user ID
Response status
Execution time
Error information
```

Sensitive information such as passwords and authentication secrets must not be logged.

---

# 78. Audit Logging

Business-critical API operations shall create audit records.

Examples:

```text
Complaint Verification
Complaint Rejection
Worker Assignment
Worker Reassignment
Resolution Approval
Resolution Rejection
Department Creation
Category Modification
User Deactivation
```

---

# 79. API Documentation

OpenAPI/Swagger shall document:

* Endpoints
* Request schemas
* Response schemas
* Authentication
* Parameters
* Error responses
* Role requirements

The API documentation should be generated from the Spring Boot application where practical.

---

# 80. API Testing

Postman shall be used during development to test:

```text
Authentication
Authorization
Complaint Management
Worker Operations
Assignments
Resolution
SLA
Notifications
Feedback
Administration
Reports
```

Automated backend tests shall use:

```text
JUnit
Mockito
Spring Boot Test
```

---

# 81. API Module Structure

The Spring Boot backend may organize API controllers as:

```text
controller/
│
├── AuthController
├── UserController
├── ComplaintController
├── CategoryController
├── DepartmentController
├── WorkerController
├── AssignmentController
├── ResolutionController
├── SLAController
├── NotificationController
├── FeedbackController
├── ReportController
└── AuditController
```

---

# 82. Service Layer

Controllers shall not contain all business logic.

Recommended structure:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
PostgreSQL
```

Example:

```text
ComplaintController
       ↓
ComplaintService
       ↓
ComplaintRepository
       ↓
PostgreSQL
```

---

# 83. Repository Layer

Spring Data JPA repositories shall provide database access.

Example:

```text
ComplaintRepository
AssignmentRepository
ResolutionRepository
UserRepository
DepartmentRepository
CategoryRepository
NotificationRepository
FeedbackRepository
AuditLogRepository
```

---

# 84. DTO Layer

Data Transfer Objects should be used to separate API contracts from internal database entities.

Example:

```text
ComplaintCreateRequest
ComplaintResponse
AssignmentRequest
ResolutionRequest
FeedbackRequest
LoginRequest
LoginResponse
```

This reduces unnecessary exposure of database fields.

---

# 85. API Data Flow

## Citizen Complaint

```text
Citizen App
     │
     ▼
POST /complaints
     │
     ▼
Complaint Controller
     │
     ▼
Complaint Service
     │
     ├── Validate
     ├── Determine Department
     ├── Apply Business Rules
     ├── Store Complaint
     ├── Create SLA
     └── Create Audit
     │
     ▼
PostgreSQL
```

---

# 86. Worker Resolution Flow

```text
Worker App
     │
     ▼
POST /worker/complaints/{id}/resolution
     │
     ▼
Resolution Controller
     │
     ▼
Resolution Service
     │
     ├── Verify Worker Assignment
     ├── Validate Resolution
     ├── Store Resolution
     ├── Update Complaint
     └── Create Notification
     │
     ▼
PostgreSQL
```

---

# 87. Resolution Approval Flow

```text
Admin Portal
     │
     ▼
POST /admin/resolutions/{id}/approve
     │
     ▼
Resolution Service
     │
     ├── Verify Admin Department
     ├── Approve Resolution
     ├── Complete Complaint
     ├── Update SLA
     ├── Create Audit
     └── Notify Citizen
     │
     ▼
PostgreSQL
```

---

# 88. API Naming Convention

Endpoints shall use lowercase plural resource names where appropriate.

Recommended:

```text
/complaints
/users
/departments
/categories
/notifications
```

Role-specific operations may use:

```text
/worker/...
/admin/...
/system/...
```

Actions may use explicit subresources where they represent state transitions:

```text
/complaints/{id}/submit
/complaints/{id}/assign
/resolutions/{id}/approve
```

---

# 89. API Idempotency

Operations that may be retried should be designed carefully.

For example:

```text
POST /complaints/{id}/submit
```

should not create multiple submissions if the request is accidentally repeated.

Where necessary, idempotency keys may be introduced for critical operations.

---

# 90. API Timeout and External Failure Handling

External services may become unavailable.

Examples:

```text
AI unavailable
Maps unavailable
FCM unavailable
Storage unavailable
```

The API shall return meaningful errors and avoid corrupting complaint data.

For example:

```text
AI Failure
   ↓
Complaint Creation Continues
   ↓
AI Suggestion Unavailable
```

where AI assistance is optional.

---

# 91. API Performance

The API should support efficient operations through:

* Pagination
* Database indexing
* Efficient JPA queries
* Connection pooling
* Caching where necessary
* Asynchronous notification delivery
* Appropriate timeout handling

Large files should not be unnecessarily loaded into memory.

---

# 92. API Availability

The API should be designed so that temporary failures of non-core services do not bring down the entire complaint management system.

Core complaint operations should remain the highest priority.

---

# 93. API Compatibility

The API shall maintain backward compatibility within a major API version where practical.

Breaking changes should result in a new API version.

Example:

```text
/api/v1
/api/v2
```

---

# 94. API Environment Configuration

Different API configurations shall be used for:

```text
Development
Testing
Production
```

Environment-specific configuration may include:

```text
Database URL
JWT Secret
AI API Configuration
Storage Configuration
FCM Configuration
CORS Origins
API Base URL
```

Secrets shall never be committed to source control.

---

# 95. Complete API Endpoint Summary

| Module       | Method | Endpoint                             | Primary Role  |
| ------------ | ------ | ------------------------------------ | ------------- |
| Auth         | POST   | `/auth/register`                     | Public        |
| Auth         | POST   | `/auth/login`                        | Public        |
| Auth         | POST   | `/auth/refresh`                      | Authenticated |
| Auth         | POST   | `/auth/logout`                       | Authenticated |
| User         | GET    | `/users/me`                          | All           |
| User         | PATCH  | `/users/me`                          | All           |
| Complaint    | POST   | `/complaints`                        | Citizen       |
| Complaint    | GET    | `/complaints/{id}`                   | Authorized    |
| Complaint    | GET    | `/complaints/my`                     | Citizen       |
| Complaint    | POST   | `/complaints/{id}/submit`            | Citizen       |
| Complaint    | POST   | `/complaints/{id}/images`            | Citizen       |
| AI           | POST   | `/complaints/{id}/ai-description`    | Citizen       |
| Category     | GET    | `/categories`                        | Authorized    |
| Department   | GET    | `/departments`                       | Authorized    |
| Worker       | GET    | `/worker/dashboard`                  | Worker        |
| Worker       | GET    | `/worker/complaints`                 | Worker        |
| Worker       | POST   | `/worker/assignments/{id}/accept`    | Worker        |
| Worker       | POST   | `/worker/complaints/{id}/start`      | Worker        |
| Worker       | POST   | `/worker/complaints/{id}/resolution` | Worker        |
| Resolution   | POST   | `/resolutions/{id}/images`           | Worker        |
| Admin        | GET    | `/admin/complaints`                  | Dept. Admin   |
| Admin        | POST   | `/admin/complaints/{id}/verify`      | Dept. Admin   |
| Admin        | POST   | `/admin/complaints/{id}/reject`      | Dept. Admin   |
| Admin        | GET    | `/admin/workers`                     | Dept. Admin   |
| Admin        | POST   | `/admin/complaints/{id}/assign`      | Dept. Admin   |
| Admin        | POST   | `/admin/complaints/{id}/reassign`    | Dept. Admin   |
| Admin        | GET    | `/admin/resolutions/pending`         | Dept. Admin   |
| Admin        | POST   | `/admin/resolutions/{id}/approve`    | Dept. Admin   |
| Admin        | POST   | `/admin/resolutions/{id}/reject`     | Dept. Admin   |
| SLA          | GET    | `/complaints/{id}/sla`               | Authorized    |
| SLA          | GET    | `/admin/sla`                         | Dept. Admin   |
| Notification | GET    | `/notifications`                     | All           |
| Notification | PATCH  | `/notifications/{id}/read`           | All           |
| Feedback     | POST   | `/complaints/{id}/feedback`          | Citizen       |
| Feedback     | GET    | `/complaints/{id}/feedback`          | Authorized    |
| System       | GET    | `/system/users`                      | System Admin  |
| System       | PATCH  | `/system/users/{id}/deactivate`      | System Admin  |
| System       | POST   | `/system/departments`                | System Admin  |
| System       | PATCH  | `/system/departments/{id}`           | System Admin  |
| System       | POST   | `/system/categories`                 | System Admin  |
| System       | PATCH  | `/system/categories/{id}`            | System Admin  |
| Reports      | GET    | `/admin/reports/complaints`          | Dept. Admin   |
| Reports      | GET    | `/system/reports/overview`           | System Admin  |
| Audit        | GET    | `/system/audit-logs`                 | System Admin  |

---

# 96. Final API Architecture

```text
                     CIVICSNAP CLIENTS
                            │
          ┌─────────────────┼─────────────────┐
          │                 │                 │
          ▼                 ▼                 ▼
    Citizen App        Worker App       Admin Portal
 Flutter + Dart      Flutter + Dart    React + TypeScript
          │                 │                 │
          └─────────────────┼─────────────────┘
                            │
                       HTTPS / REST
                            │
                            ▼
                ┌────────────────────────┐
                │   Spring Boot API      │
                │                        │
                │ Controllers            │
                │ Services               │
                │ Security               │
                │ Validation             │
                │ DTOs                   │
                └───────────┬────────────┘
                            │
             ┌──────────────┼──────────────┐
             │              │              │
             ▼              ▼              ▼
        PostgreSQL      File Storage   External APIs
                                           │
                                  ┌────────┼────────┐
                                  ▼        ▼        ▼
                                 AI      Maps       FCM
```

---

# 97. Final API Principles

The CivicSnap API shall follow these principles:

1. **RESTful Design** — APIs shall use standard HTTP methods and resource-oriented endpoints.
2. **Secure by Default** — Protected APIs require authentication and authorization.
3. **Role-Based Access** — API access depends on the authenticated user's role.
4. **Department Isolation** — Department Administrators can only access their own department's operational data.
5. **Worker Isolation** — Workers can only access their assigned complaints.
6. **Citizen Ownership** — Citizens can only access their own complaints.
7. **Backend Enforcement** — Security rules must be enforced by Spring Boot, not only by client applications.
8. **Consistent Responses** — APIs should use standardized success and error structures.
9. **Validation** — Requests must be validated before processing.
10. **Auditability** — Important administrative operations must be recorded.
11. **Transaction Safety** — Related database operations must remain consistent.
12. **External Service Isolation** — AI, Maps, FCM, and storage integrations should remain modular.
13. **Pagination** — Large collections must support pagination.
14. **Versioning** — APIs should use versioned endpoints such as `/api/v1`.
15. **Documentation** — APIs should be documented through OpenAPI/Swagger.

---

# 98. Final Summary

The CivicSnap API serves as the central communication layer between:

```text
Flutter Citizen App
        │
Flutter Worker App
        │
React Admin Portal
        │
        ▼
Java Spring Boot REST API
        │
        ▼
PostgreSQL Database
```

The API manages the complete CivicSnap lifecycle:

```text
Authentication
     ↓
Complaint Creation
     ↓
AI Assistance
     ↓
Complaint Verification
     ↓
Department Routing
     ↓
Worker Assignment
     ↓
Field Work
     ↓
Resolution Submission
     ↓
Resolution Verification
     ↓
Complaint Completion
     ↓
Citizen Feedback
     ↓
Reporting & Audit
```

The API architecture ensures that the **Citizen App**, **Worker App**, and **Admin Portal** remain separate clients while sharing a secure and centralized backend.

The **Java Spring Boot API is the authoritative application layer** responsible for authentication, authorization, business rules, department isolation, worker assignment validation, complaint lifecycle management, SLA processing, resolution verification, notifications, and database interaction.
