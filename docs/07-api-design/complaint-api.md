# CivicSnap — Complaint API

## 1. Introduction

The Complaint API is the core API module of CivicSnap. It manages the complete lifecycle of civic complaints from creation by a citizen through verification, department assignment, field work, resolution, verification, completion, and feedback.

The Complaint API is implemented using:

* Java
* Spring Boot
* Spring Security
* REST
* JSON
* PostgreSQL
* JWT

The API is consumed by:

* **Citizen App** — Flutter + Dart
* **Worker App** — Flutter + Dart
* **Admin Portal** — React + TypeScript

The backend is the authoritative source for complaint status, ownership, department routing, worker assignment, SLA state, and resolution state.

---

# 2. Complaint API Objectives

The Complaint API shall:

1. Create complaints.
2. Store complaint details.
3. Associate complaints with citizens.
4. Associate complaints with categories.
5. Determine the responsible department.
6. Store complaint locations.
7. Manage complaint images.
8. Submit complaints for processing.
9. Support complaint verification.
10. Support complaint rejection.
11. Support worker assignment.
12. Support worker reassignment.
13. Track complaint progress.
14. Manage resolution submissions.
15. Support resolution verification.
16. Track SLA information.
17. Maintain complaint history.
18. Notify relevant users about important changes.
19. Maintain an auditable complaint lifecycle.
20. Prevent unauthorized complaint access.

---

# 3. Complaint Lifecycle

The complaint lifecycle is:

```text
DRAFT
  │
  ▼
SUBMITTED
  │
  ▼
VERIFIED
  │
  ▼
ASSIGNED
  │
  ▼
IN_PROGRESS
  │
  ▼
RESOLUTION_SUBMITTED
  │
  ▼
RESOLUTION_APPROVED
  │
  ▼
COMPLETED
```

Alternative paths include:

```text
SUBMITTED ─────► REJECTED

RESOLUTION_SUBMITTED
        │
        ▼
RESOLUTION_REJECTED
        │
        ▼
IN_PROGRESS
```

---

# 4. Complaint API Architecture

```text
                    ┌──────────────────┐
                    │   Citizen App    │
                    │  Flutter + Dart  │
                    └────────┬─────────┘
                             │
                             │
┌──────────────────┐         │         ┌──────────────────┐
│    Worker App    │─────────┼─────────│   Admin Portal   │
│  Flutter + Dart  │         │         │ React + TypeScript│
└──────────────────┘         │         └────────┬─────────┘
                             │                  │
                             ▼                  │
                  ┌─────────────────────┐      │
                  │    Complaint API     │◄─────┘
                  │    Spring Boot      │
                  └──────────┬──────────┘
                             │
             ┌───────────────┼────────────────┐
             │               │                │
             ▼               ▼                ▼
        PostgreSQL       File Storage    External Services
                                              │
                                  ┌───────────┼───────────┐
                                  ▼           ▼           ▼
                                 AI          Maps        FCM
```

---

# 5. Base URL

All complaint endpoints use:

```text
/api/v1
```

Examples:

```text
/api/v1/complaints
/api/v1/complaints/{complaintId}
/api/v1/complaints/{complaintId}/submit
```

---

# 6. Authentication

Protected endpoints require:

```http
Authorization: Bearer <JWT_TOKEN>
```

The backend shall determine the authenticated user from the JWT.

The client shall not be allowed to override the authenticated user's identity.

---

# 7. Complaint API Roles

| Operation                   | Citizen | Worker | Department Admin | System Admin |
| --------------------------- | :-----: | :----: | :--------------: | :----------: |
| Create Complaint            |    ✓    |    ✗   |         ✗        |       ✗      |
| View Own Complaint          |    ✓    |    ✗   |         ✓        |       ✓      |
| Update Draft                |    ✓    |    ✗   |         ✗        |       ✗      |
| Submit Complaint            |    ✓    |    ✗   |         ✗        |       ✗      |
| Verify Complaint            |    ✗    |    ✗   |         ✓        |       ✓      |
| Reject Complaint            |    ✗    |    ✗   |         ✓        |       ✓      |
| Assign Worker               |    ✗    |    ✗   |         ✓        |       ✓      |
| Reassign Worker             |    ✗    |    ✗   |         ✓        |       ✓      |
| Start Work                  |    ✗    |    ✓   |         ✗        |       ✓      |
| Submit Resolution           |    ✗    |    ✓   |         ✗        |       ✓      |
| Approve Resolution          |    ✗    |    ✗   |         ✓        |       ✓      |
| Reject Resolution           |    ✗    |    ✗   |         ✓        |       ✓      |
| View System-wide Complaints |    ✗    |    ✗   |         ✗        |       ✓      |

Authorization shall always be enforced by the backend.

---

# 8. Create Complaint

## Endpoint

```http
POST /api/v1/complaints
```

## Role

```text
CITIZEN
```

## Purpose

Creates a new complaint associated with the authenticated citizen.

---

# 9. Create Complaint Request

```json
{
  "categoryId": "CAT-001",
  "title": "Large pothole",
  "description": "A large pothole is present near the main junction.",
  "latitude": 12.123456,
  "longitude": 75.123456,
  "locationAccuracy": 8.5
}
```

---

# 10. Create Complaint Fields

| Field            | Type        | Required | Description                 |
| ---------------- | ----------- | -------: | --------------------------- |
| categoryId       | UUID/String |      Yes | Selected complaint category |
| title            | String      |      Yes | Complaint title             |
| description      | String      |      Yes | Complaint description       |
| latitude         | Decimal     |      Yes | Geographic latitude         |
| longitude        | Decimal     |      Yes | Geographic longitude        |
| locationAccuracy | Decimal     |       No | GPS accuracy in metres      |

---

# 11. Complaint Creation Rules

When creating a complaint, the backend shall:

1. Authenticate the citizen.
2. Validate request data.
3. Validate category.
4. Determine the responsible department.
5. Associate the complaint with the authenticated citizen.
6. Generate a complaint identifier.
7. Store the complaint.
8. Set the initial status.
9. Create necessary audit information.

The citizen shall not provide an authoritative:

```text
citizenId
departmentId
complaintNumber
status
createdAt
```

These values are controlled by the backend.

---

# 12. Initial Complaint Status

A newly created complaint may be stored as:

```text
DRAFT
```

This allows the Citizen App to complete evidence and review before final submission.

---

# 13. Create Complaint Response

```json
{
  "success": true,
  "data": {
    "id": "7d3c...",
    "complaintNumber": "CS-100001",
    "status": "DRAFT",
    "categoryId": "CAT-001",
    "departmentId": "DEP-001",
    "createdAt": "2026-08-30T08:30:00Z"
  },
  "message": "Complaint created successfully."
}
```

---

# 14. Complaint Number

Complaint numbers shall be generated by the backend.

Example:

```text
CS-100001
CS-100002
CS-100003
```

The Flutter application must not generate the authoritative complaint number.

---

# 15. Get Complaint

## Endpoint

```http
GET /api/v1/complaints/{complaintId}
```

The response depends on the authenticated user's role.

---

# 16. Citizen Access

A citizen may retrieve a complaint only when:

```text
JWT User ID
      =
Complaint Citizen ID
```

If not:

```http
403 Forbidden
```

---

# 17. Worker Access

A worker may retrieve a complaint only when the complaint is assigned to that worker.

```text
JWT Worker ID
      =
Assignment Worker ID
```

---

# 18. Department Admin Access

A Department Administrator may retrieve complaints only when:

```text
Admin Department ID
       =
Complaint Department ID
```

This ensures department isolation.

---

# 19. System Admin Access

A System Administrator may access complaints across departments according to system-level permissions.

---

# 20. Complaint Details Response

```json
{
  "success": true,
  "data": {
    "id": "7d3c...",
    "complaintNumber": "CS-100001",
    "title": "Large pothole",
    "description": "A large pothole is present near the main junction.",
    "category": {
      "id": "CAT-001",
      "name": "Road Damage"
    },
    "department": {
      "id": "DEP-001",
      "name": "Road/Public Works"
    },
    "status": "IN_PROGRESS",
    "priority": "HIGH",
    "location": {
      "latitude": 12.123456,
      "longitude": 75.123456,
      "accuracy": 8.5
    },
    "createdAt": "2026-08-30T08:30:00Z",
    "submittedAt": "2026-08-30T08:40:00Z"
  }
}
```

---

# 21. Update Complaint

## Endpoint

```http
PATCH /api/v1/complaints/{complaintId}
```

## Role

```text
CITIZEN
```

Only eligible complaints may be modified.

Normally:

```text
DRAFT
```

is editable.

Once departmental processing begins, unrestricted citizen modification should not be permitted.

---

# 22. Update Complaint Request

```json
{
  "title": "Large road pothole",
  "description": "The pothole is approximately two metres wide."
}
```

The backend determines which fields are editable.

---

# 23. Submit Complaint

## Endpoint

```http
POST /api/v1/complaints/{complaintId}/submit
```

## Role

```text
CITIZEN
```

Changes:

```text
DRAFT
  ↓
SUBMITTED
```

---

# 24. Submit Validation

Before submission, the API shall validate:

```text
Complaint exists
       ↓
Complaint belongs to citizen
       ↓
Status = DRAFT
       ↓
Category valid
       ↓
Description valid
       ↓
Location valid
       ↓
Required evidence available
```

If validation succeeds, the complaint is submitted.

---

# 25. Submit Response

```json
{
  "success": true,
  "data": {
    "complaintNumber": "CS-100001",
    "status": "SUBMITTED",
    "submittedAt": "2026-08-30T08:40:00Z"
  },
  "message": "Complaint submitted successfully."
}
```

---

# 26. Complaint Images

## Upload Image

```http
POST /api/v1/complaints/{complaintId}/images
```

## Content Type

```text
multipart/form-data
```

Only the complaint owner may upload evidence during the citizen submission stage.

---

# 27. Image Upload Validation

The API shall validate:

* Authentication
* Complaint ownership
* Complaint status
* MIME type
* File extension
* File size
* File integrity

Recommended supported formats:

```text
JPEG
PNG
WEBP
```

---

# 28. Image Storage

Images should be stored using object/file storage.

The PostgreSQL database should store metadata such as:

```text
imageId
complaintId
storageReference
fileName
contentType
fileSize
createdAt
```

The actual image binary should not normally be stored directly in the complaint table.

---

# 29. Get Complaint Images

## Endpoint

```http
GET /api/v1/complaints/{complaintId}/images
```

Returns authorized images associated with the complaint.

Secure storage references should be used rather than unrestricted public URLs.

---

# 30. Delete Complaint Image

## Endpoint

```http
DELETE /api/v1/complaints/{complaintId}/images/{imageId}
```

This operation may be allowed only while the complaint remains editable.

The backend must verify complaint ownership.

---

# 31. AI Complaint Assistance

## Endpoint

```http
POST /api/v1/complaints/{complaintId}/ai-description
```

## Role

```text
CITIZEN
```

The endpoint requests AI assistance for describing or categorizing the complaint.

---

# 32. AI Processing

```text
Citizen App
    │
    ▼
Complaint Image
    │
    ▼
Complaint API
    │
    ▼
AI Integration Service
    │
    ▼
AI Provider
    │
    ▼
Suggested Description
    │
    ▼
Citizen App
```

---

# 33. AI Response

```json
{
  "success": true,
  "data": {
    "suggestedDescription": "A large pothole is visible on the road surface.",
    "suggestedCategory": "Road Damage"
  }
}
```

The AI output is not authoritative.

---

# 34. AI Confirmation

The citizen may:

```text
Accept AI suggestion
Edit AI suggestion
Reject AI suggestion
Write manually
```

The final complaint data remains controlled by the citizen and backend validation.

---

# 35. Verify Complaint

## Endpoint

```http
POST /api/v1/admin/complaints/{complaintId}/verify
```

## Role

```text
DEPARTMENT_ADMIN
SYSTEM_ADMIN
```

A Department Administrator may verify only complaints belonging to their department.

---

# 36. Verification Flow

```text
SUBMITTED
    │
    ▼
Admin Reviews Complaint
    │
    ├──────────────┐
    │              │
    ▼              ▼
VERIFY           REJECT
    │              │
    ▼              ▼
VERIFIED        REJECTED
```

---

# 37. Verify Response

```json
{
  "success": true,
  "data": {
    "complaintNumber": "CS-100001",
    "status": "VERIFIED",
    "verifiedAt": "2026-08-30T09:10:00Z"
  },
  "message": "Complaint verified successfully."
}
```

---

# 38. Reject Complaint

## Endpoint

```http
POST /api/v1/admin/complaints/{complaintId}/reject
```

## Role

```text
DEPARTMENT_ADMIN
SYSTEM_ADMIN
```

---

# 39. Reject Request

```json
{
  "reason": "Insufficient evidence provided."
}
```

A rejection reason should be recorded.

---

# 40. Reject Response

```json
{
  "success": true,
  "data": {
    "complaintNumber": "CS-100001",
    "status": "REJECTED",
    "rejectedAt": "2026-08-30T09:15:00Z"
  },
  "message": "Complaint rejected."
}
```

---

# 41. Complaint Priority

Complaints may have priority levels such as:

```text
LOW
MEDIUM
HIGH
CRITICAL
```

Priority may be determined from:

* Category
* Severity
* Administrative rules
* SLA configuration
* Emergency criteria

The exact priority mechanism shall be defined in the business rules.

---

# 42. Department Assignment

After verification:

```text
VERIFIED
    ↓
Determine Department
    ↓
Create Assignment
    ↓
ASSIGNED
```

The responsible department is determined using the category-to-department configuration.

---

# 43. Worker Assignment

## Endpoint

```http
POST /api/v1/admin/complaints/{complaintId}/assign
```

## Role

```text
DEPARTMENT_ADMIN
SYSTEM_ADMIN
```

---

# 44. Assign Request

```json
{
  "workerId": "WORKER-204"
}
```

---

# 45. Assignment Validation

The backend shall verify:

1. Complaint exists.
2. Complaint belongs to the administrator's department.
3. Complaint is in an assignable state.
4. Worker exists.
5. Worker is active.
6. Worker belongs to the same department.
7. Worker is eligible for the assignment.
8. No conflicting assignment exists.

---

# 46. Assignment Response

```json
{
  "success": true,
  "data": {
    "complaintNumber": "CS-100001",
    "workerId": "WORKER-204",
    "status": "ASSIGNED"
  },
  "message": "Complaint assigned successfully."
}
```

---

# 47. Reassign Complaint

## Endpoint

```http
POST /api/v1/admin/complaints/{complaintId}/reassign
```

---

# 48. Reassignment Request

```json
{
  "workerId": "WORKER-205",
  "reason": "Previous worker unavailable."
}
```

---

# 49. Reassignment Rules

The backend shall:

```text
Validate Admin
      ↓
Validate Department
      ↓
Validate Worker
      ↓
Close Previous Assignment
      ↓
Create New Assignment
      ↓
Record History
      ↓
Notify Worker
```

The previous assignment must remain in historical records.

---

# 50. Start Work

## Endpoint

```http
POST /api/v1/worker/complaints/{complaintId}/start
```

## Role

```text
WORKER
```

Changes:

```text
ASSIGNED
    ↓
IN_PROGRESS
```

The worker must have a valid active assignment.

---

# 51. Start Work Response

```json
{
  "success": true,
  "data": {
    "complaintNumber": "CS-100001",
    "status": "IN_PROGRESS",
    "startedAt": "2026-08-30T10:30:00Z"
  },
  "message": "Work started successfully."
}
```

---

# 52. Submit Resolution

## Endpoint

```http
POST /api/v1/worker/complaints/{complaintId}/resolution
```

## Role

```text
WORKER
```

The worker submits a description of the completed work.

---

# 53. Resolution Request

```json
{
  "description": "The damaged road section has been repaired."
}
```

---

# 54. Resolution Validation

The backend shall verify:

```text
Worker authenticated
       ↓
Worker assigned to complaint
       ↓
Complaint = IN_PROGRESS
       ↓
Resolution description valid
       ↓
Store resolution
       ↓
Change status
```

---

# 55. Resolution Status

After submission:

```text
IN_PROGRESS
      ↓
RESOLUTION_SUBMITTED
```

The Department Administrator must then review the resolution.

---

# 56. Resolution Evidence

## Endpoint

```http
POST /api/v1/resolutions/{resolutionId}/images
```

## Role

```text
WORKER
```

Workers may upload photographs showing completed work.

---

# 57. Approve Resolution

## Endpoint

```http
POST /api/v1/admin/resolutions/{resolutionId}/approve
```

## Role

```text
DEPARTMENT_ADMIN
SYSTEM_ADMIN
```

---

# 58. Approval Flow

```text
RESOLUTION_SUBMITTED
        │
        ▼
Admin Reviews Evidence
        │
        ├─────────────┐
        │             │
        ▼             ▼
    APPROVE         REJECT
        │             │
        ▼             ▼
   COMPLETED     IN_PROGRESS
```

---

# 59. Approve Resolution Response

```json
{
  "success": true,
  "data": {
    "complaintNumber": "CS-100001",
    "status": "COMPLETED",
    "verifiedAt": "2026-08-31T11:30:00Z"
  },
  "message": "Resolution approved successfully."
}
```

---

# 60. Reject Resolution

## Endpoint

```http
POST /api/v1/admin/resolutions/{resolutionId}/reject
```

### Request

```json
{
  "reason": "Repair evidence is insufficient."
}
```

The complaint returns to an appropriate work state.

Example:

```text
RESOLUTION_SUBMITTED
        ↓
RESOLUTION_REJECTED
        ↓
IN_PROGRESS
```

---

# 61. Complaint Timeline

## Endpoint

```http
GET /api/v1/complaints/{complaintId}/timeline
```

Returns relevant complaint lifecycle events.

Example:

```json
{
  "success": true,
  "data": [
    {
      "event": "CREATED",
      "status": "DRAFT",
      "timestamp": "2026-08-30T08:30:00Z"
    },
    {
      "event": "SUBMITTED",
      "status": "SUBMITTED",
      "timestamp": "2026-08-30T08:40:00Z"
    },
    {
      "event": "VERIFIED",
      "status": "VERIFIED",
      "timestamp": "2026-08-30T09:10:00Z"
    },
    {
      "event": "ASSIGNED",
      "status": "ASSIGNED",
      "timestamp": "2026-08-30T09:20:00Z"
    },
    {
      "event": "WORK_STARTED",
      "status": "IN_PROGRESS",
      "timestamp": "2026-08-30T10:30:00Z"
    }
  ]
}
```

---

# 62. Complaint SLA

## Endpoint

```http
GET /api/v1/complaints/{complaintId}/sla
```

Returns SLA information appropriate to the authenticated role.

Example:

```json
{
  "success": true,
  "data": {
    "targetHours": 48,
    "deadlineAt": "2026-09-01T08:40:00Z",
    "status": "ACTIVE"
  }
}
```

---

# 63. SLA States

Possible states:

```text
ACTIVE
APPROACHING_DEADLINE
BREACHED
COMPLETED
```

---

# 64. Complaint List API

## Endpoint

```http
GET /api/v1/complaints
```

This endpoint should be role-aware.

The backend shall return only records the authenticated user is authorized to view.

---

# 65. Citizen Complaint List

Citizens should use:

```http
GET /api/v1/complaints/my
```

This returns only their complaints.

---

# 66. Worker Complaint List

Workers should use:

```http
GET /api/v1/worker/complaints
```

This returns only complaints assigned to the authenticated worker.

---

# 67. Department Complaint List

Department Administrators should use:

```http
GET /api/v1/admin/complaints
```

The backend restricts results to their department.

---

# 68. Complaint Filtering

Supported filters may include:

```text
status
category
priority
worker
dateFrom
dateTo
slaStatus
```

Example:

```http
GET /api/v1/admin/complaints?status=IN_PROGRESS&priority=HIGH
```

---

# 69. Complaint Sorting

Example:

```http
GET /api/v1/admin/complaints?sort=createdAt,desc
```

Only approved sorting fields should be supported.

---

# 70. Pagination

Complaint lists shall support pagination.

Example:

```http
GET /api/v1/complaints/my?page=0&size=20
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

---

# 71. Complaint Search

Authorized users may search complaints using fields such as:

```text
complaintNumber
title
category
status
```

Example:

```http
GET /api/v1/admin/complaints?search=CS-100001
```

---

# 72. Complaint Ownership

Every complaint must contain a citizen association.

Conceptually:

```text
CITIZEN
   │
   └────< COMPLAINT
```

The backend automatically obtains the citizen identity from authentication.

---

# 73. Complaint Category Relationship

```text
CATEGORY
   │
   └────< COMPLAINT
```

Each complaint belongs to one category.

---

# 74. Complaint Department Relationship

```text
DEPARTMENT
   │
   └────< COMPLAINT
```

The department is determined through category configuration and backend business rules.

---

# 75. Complaint Assignment Relationship

```text
COMPLAINT
    │
    └────< ASSIGNMENT >──── WORKER
```

A complaint may have multiple historical assignments, but only the appropriate current assignment may be active.

---

# 76. Complaint Resolution Relationship

```text
COMPLAINT
    │
    └────< RESOLUTION
```

A complaint may have resolution records representing submitted work and review history.

---

# 77. Complaint Image Relationship

```text
COMPLAINT
    │
    └────< COMPLAINT_IMAGE
```

Multiple evidence images may be associated with a complaint.

---

# 78. Complaint Feedback Relationship

```text
COMPLAINT
    │
    └──── 0..1 FEEDBACK
```

Depending on the business rules, a completed complaint may allow one citizen feedback record.

---

# 79. Complaint Audit Relationship

```text
COMPLAINT
    │
    └────< AUDIT_LOG
```

Important state changes shall be auditable.

---

# 80. Complaint Data Model

Conceptual complaint structure:

```text
COMPLAINT
│
├── id
├── complaint_number
├── citizen_id
├── category_id
├── department_id
├── title
├── description
├── latitude
├── longitude
├── location_accuracy
├── priority
├── status
├── created_at
├── submitted_at
├── verified_at
├── completed_at
└── updated_at
```

---

# 81. Complaint Status Rules

The backend shall control status transitions.

Valid examples:

```text
DRAFT → SUBMITTED
SUBMITTED → VERIFIED
SUBMITTED → REJECTED
VERIFIED → ASSIGNED
ASSIGNED → IN_PROGRESS
IN_PROGRESS → RESOLUTION_SUBMITTED
RESOLUTION_SUBMITTED → COMPLETED
RESOLUTION_SUBMITTED → RESOLUTION_REJECTED
RESOLUTION_REJECTED → IN_PROGRESS
```

Invalid arbitrary transitions shall be rejected.

---

# 82. Status Transition Validation

Example:

```text
Citizen
   │
   └── Cannot:
          DRAFT → COMPLETED

Worker
   │
   └── Cannot:
          ASSIGNED → COMPLETED

Admin
   │
   └── Cannot:
          SUBMITTED → COMPLETED
```

Each transition requires the appropriate role and current status.

---

# 83. Complaint State Machine

```text
                 ┌───────────────┐
                 │     DRAFT     │
                 └───────┬───────┘
                         │ Submit
                         ▼
                 ┌───────────────┐
                 │   SUBMITTED   │
                 └───────┬───────┘
                     ┌───┴───┐
                     │       │
                  Verify   Reject
                     │       │
                     ▼       ▼
              ┌──────────┐  REJECTED
              │ VERIFIED │
              └────┬─────┘
                   │ Assign
                   ▼
              ┌──────────┐
              │ ASSIGNED │
              └────┬─────┘
                   │ Start
                   ▼
             ┌─────────────┐
             │ IN_PROGRESS │
             └──────┬──────┘
                    │ Submit Resolution
                    ▼
          ┌──────────────────────┐
          │ RESOLUTION_SUBMITTED │
          └──────────┬───────────┘
                 ┌───┴────┐
              Approve    Reject
                 │         │
                 ▼         ▼
            COMPLETED  IN_PROGRESS
```

---

# 84. Complaint Notifications

Important complaint events may generate notifications.

Examples:

```text
Complaint Submitted
Complaint Verified
Complaint Rejected
Worker Assigned
Work Started
Resolution Submitted
Resolution Rejected
Complaint Completed
SLA Warning
SLA Breach
```

---

# 85. Notification Flow

```text
Complaint Event
      │
      ▼
Complaint Service
      │
      ▼
Notification Service
      │
      ├── Database Notification
      │
      └── Push Notification
              │
              ▼
         Firebase FCM
```

Notification delivery should not compromise the primary complaint transaction.

---

# 86. Audit Logging

The following operations should create audit records:

```text
Complaint Created
Complaint Submitted
Complaint Verified
Complaint Rejected
Worker Assigned
Worker Reassigned
Work Started
Resolution Submitted
Resolution Approved
Resolution Rejected
Complaint Completed
```

---

# 87. Audit Information

An audit record may contain:

```text
userId
action
entityType
entityId
oldStatus
newStatus
timestamp
reason
```

Sensitive information must not be unnecessarily stored in audit logs.

---

# 88. Complaint Security

The Complaint API shall implement:

* JWT authentication
* Role-based authorization
* Ownership validation
* Department isolation
* Worker assignment validation
* Input validation
* File validation
* HTTPS
* Rate limiting where required
* Audit logging

---

# 89. Citizen Security Rules

Citizens can only access:

```text
Their own complaints
Their own complaint images
Their own complaint timeline
Their own resolution information
Their own feedback
```

---

# 90. Worker Security Rules

Workers can only access:

```text
Complaints assigned to them
Their authorized work information
Their resolution submissions
```

A worker must not access unrelated departmental complaints.

---

# 91. Department Admin Security Rules

Department Administrators can only access:

```text
Complaints in their department
Workers in their department
Resolutions belonging to their department
Department reports
```

The department ID must be determined from the authenticated administrator's authorization context.

---

# 92. System Admin Security Rules

System Administrators may have system-wide complaint access for administrative and reporting purposes.

---

# 93. Complaint API Error Codes

Recommended error codes:

```text
COMPLAINT_NOT_FOUND
COMPLAINT_NOT_OWNED
COMPLAINT_ACCESS_DENIED
INVALID_COMPLAINT_STATUS
INVALID_STATUS_TRANSITION
CATEGORY_NOT_FOUND
DEPARTMENT_NOT_FOUND
WORKER_NOT_FOUND
WORKER_NOT_ELIGIBLE
ASSIGNMENT_NOT_FOUND
ACTIVE_ASSIGNMENT_EXISTS
RESOLUTION_NOT_FOUND
RESOLUTION_NOT_ALLOWED
IMAGE_NOT_FOUND
INVALID_IMAGE
IMAGE_TOO_LARGE
AI_SERVICE_UNAVAILABLE
SLA_NOT_FOUND
VALIDATION_ERROR
```

---

# 94. Standard Error Response

```json
{
  "success": false,
  "error": {
    "code": "INVALID_STATUS_TRANSITION",
    "message": "The complaint cannot be moved to the requested status."
  }
}
```

---

# 95. HTTP Status Codes

| Status | Meaning                           |
| ------ | --------------------------------- |
| 200    | Successful request                |
| 201    | Complaint/resource created        |
| 204    | Successful operation without body |
| 400    | Invalid request                   |
| 401    | Authentication required           |
| 403    | Access denied                     |
| 404    | Complaint/resource not found      |
| 409    | State/conflict error              |
| 422    | Validation/business rule failure  |
| 429    | Rate limit exceeded               |
| 500    | Internal server error             |
| 503    | External service unavailable      |

---

# 96. Complaint API DTOs

Recommended DTOs:

```text
ComplaintCreateRequest
ComplaintUpdateRequest
ComplaintResponse
ComplaintSummaryResponse
ComplaintTimelineResponse

ComplaintVerificationRequest
ComplaintRejectionRequest

ComplaintAssignmentRequest
ComplaintReassignmentRequest

ResolutionRequest
ResolutionResponse

SlaResponse
ComplaintImageResponse
```

---

# 97. Controller Structure

Recommended Spring Boot controllers:

```text
controller/
│
├── ComplaintController
├── ComplaintImageController
├── ComplaintAdminController
├── ComplaintWorkerController
├── ResolutionController
└── ComplaintTimelineController
```

---

# 98. Service Structure

```text
service/
│
├── ComplaintService
├── ComplaintAssignmentService
├── ComplaintVerificationService
├── ComplaintImageService
├── ComplaintTimelineService
├── ResolutionService
├── SLAService
├── NotificationService
└── AuditService
```

---

# 99. Repository Structure

```text
repository/
│
├── ComplaintRepository
├── ComplaintImageRepository
├── AssignmentRepository
├── ResolutionRepository
├── SLARepository
├── NotificationRepository
└── AuditLogRepository
```

---

# 100. Complaint Creation Flow

```text
             CITIZEN APP
                  │
                  ▼
          Create Complaint
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
       ┌──────────┼───────────┐
       │          │           │
       ▼          ▼           ▼
   Validate    Category    Ownership
                  │
                  ▼
           Determine Department
                  │
                  ▼
             PostgreSQL
                  │
                  ▼
             Complaint ID
                  │
                  ▼
             Citizen App
```

---

# 101. Verification Flow

```text
              ADMIN PORTAL
                   │
                   ▼
           Review Complaint
                   │
                   ▼
        POST /admin/complaints/id/verify
                   │
                   ▼
          Authorization Check
                   │
                   ▼
          Department Validation
                   │
                   ▼
           Complaint Validation
                   │
                   ▼
             Update Status
                   │
                   ▼
                VERIFIED
                   │
          ┌────────┴────────┐
          ▼                 ▼
        Audit          Notification
```

---

# 102. Worker Assignment Flow

```text
Department Admin
       │
       ▼
Select Complaint
       │
       ▼
Select Worker
       │
       ▼
POST /complaints/{id}/assign
       │
       ▼
Validate Department
       │
       ▼
Validate Worker
       │
       ▼
Create Assignment
       │
       ▼
ASSIGNED
       │
       ├── Audit
       │
       └── Notification
```

---

# 103. Resolution Flow

```text
Worker App
    │
    ▼
Start Work
    │
    ▼
IN_PROGRESS
    │
    ▼
Complete Field Work
    │
    ▼
Upload Evidence
    │
    ▼
Submit Resolution
    │
    ▼
RESOLUTION_SUBMITTED
    │
    ▼
Department Admin
    │
    ├─────────────┐
    ▼             ▼
 Approve        Reject
    │             │
    ▼             ▼
COMPLETED     IN_PROGRESS
```

---

# 104. Complete Complaint API Flow

```text
┌────────────────────────────────────────────────────────┐
│                    CITIZEN APP                         │
└──────────────────────────┬─────────────────────────────┘
                           │
                    Create Complaint
                           │
                           ▼
                         DRAFT
                           │
                    Submit Complaint
                           │
                           ▼
                      SUBMITTED
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│                    ADMIN PORTAL                        │
└──────────────────────────┬─────────────────────────────┘
                           │
                    Review Complaint
                      ┌────┴────┐
                      ▼         ▼
                   VERIFY     REJECT
                      │
                      ▼
                   VERIFIED
                      │
                  Assign Worker
                      │
                      ▼
                   ASSIGNED
                      │
                      ▼
┌────────────────────────────────────────────────────────┐
│                     WORKER APP                         │
└──────────────────────────┬─────────────────────────────┘
                           │
                       Start Work
                           │
                           ▼
                     IN_PROGRESS
                           │
                    Submit Resolution
                           │
                           ▼
               RESOLUTION_SUBMITTED
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│                    ADMIN PORTAL                        │
└──────────────────────────┬─────────────────────────────┘
                           │
                     Review Resolution
                      ┌────┴────┐
                      ▼         ▼
                   APPROVE    REJECT
                      │         │
                      ▼         ▼
                  COMPLETED  IN_PROGRESS
                      │
                      ▼
┌────────────────────────────────────────────────────────┐
│                    CITIZEN APP                         │
│                  View Result + Feedback                │
└────────────────────────────────────────────────────────┘
```

---

# 105. Complete Endpoint Summary

| Method | Endpoint                             | Role        | Purpose                    |
| ------ | ------------------------------------ | ----------- | -------------------------- |
| POST   | `/complaints`                        | Citizen     | Create complaint           |
| GET    | `/complaints/my`                     | Citizen     | List own complaints        |
| GET    | `/complaints/{id}`                   | Authorized  | Get complaint              |
| PATCH  | `/complaints/{id}`                   | Citizen     | Update eligible complaint  |
| POST   | `/complaints/{id}/submit`            | Citizen     | Submit complaint           |
| POST   | `/complaints/{id}/images`            | Citizen     | Upload evidence            |
| GET    | `/complaints/{id}/images`            | Authorized  | View evidence              |
| DELETE | `/complaints/{id}/images/{imageId}`  | Citizen     | Delete eligible image      |
| POST   | `/complaints/{id}/ai-description`    | Citizen     | AI assistance              |
| GET    | `/complaints/{id}/timeline`          | Authorized  | View timeline              |
| GET    | `/complaints/{id}/sla`               | Authorized  | View SLA                   |
| GET    | `/complaints/{id}/resolution`        | Authorized  | View resolution            |
| GET    | `/admin/complaints`                  | Dept. Admin | Department complaints      |
| POST   | `/admin/complaints/{id}/verify`      | Dept. Admin | Verify complaint           |
| POST   | `/admin/complaints/{id}/reject`      | Dept. Admin | Reject complaint           |
| POST   | `/admin/complaints/{id}/assign`      | Dept. Admin | Assign worker              |
| POST   | `/admin/complaints/{id}/reassign`    | Dept. Admin | Reassign worker            |
| GET    | `/worker/complaints`                 | Worker      | Assigned complaints        |
| GET    | `/worker/complaints/{id}`            | Worker      | Assigned complaint         |
| POST   | `/worker/complaints/{id}/start`      | Worker      | Start work                 |
| POST   | `/worker/complaints/{id}/resolution` | Worker      | Submit resolution          |
| POST   | `/resolutions/{id}/images`           | Worker      | Upload resolution evidence |
| POST   | `/admin/resolutions/{id}/approve`    | Dept. Admin | Approve resolution         |
| POST   | `/admin/resolutions/{id}/reject`     | Dept. Admin | Reject resolution          |

---

# 106. API Transaction Principles

Critical complaint operations should use database transactions.

For example, worker assignment may involve:

```text
Create Assignment
      +
Update Complaint Status
      +
Create Audit Record
      +
Create Notification Record
```

Core database changes should remain consistent if an operation fails.

External push notification delivery should be handled separately where appropriate.

---

# 107. API Performance

The Complaint API should support:

* Pagination
* Database indexing
* Efficient filtering
* Efficient status queries
* Lazy loading of large evidence collections
* Secure image references
* Asynchronous notifications
* Caching for stable category information

Complaint list APIs should not unnecessarily return full image data.

---

# 108. API Idempotency

Critical operations should prevent accidental duplicate execution.

For example:

```text
Citizen taps Submit twice
       │
       ▼
Backend
       │
       ▼
Only one valid submission
```

Similarly, repeated resolution approval requests must not repeatedly alter the complaint.

---

# 109. Server-Side Authority

The backend is authoritative for:

```text
Complaint ID
Complaint Number
Citizen ID
Department ID
Worker Assignment
Status
Priority
SLA
Lifecycle timestamps
Verification
Resolution approval
```

The client applications may request actions but cannot directly set these authoritative values.

---

# 110. Final Complaint API Architecture

```text
                    CIVICSNAP
                        │
        ┌───────────────┼────────────────┐
        │               │                │
        ▼               ▼                ▼
     Citizen          Worker           Admin
       App              App             Portal
    Flutter           Flutter           React
        │               │                │
        └───────────────┼────────────────┘
                        │
                       HTTPS
                        │
                        ▼
             ┌──────────────────────┐
             │    Complaint API     │
             │    Spring Boot      │
             └──────────┬───────────┘
                        │
       ┌────────────────┼─────────────────┐
       │                │                 │
       ▼                ▼                 ▼
  Authentication    Business Logic    Validation
       │                │                 │
       └────────────────┼─────────────────┘
                        │
                        ▼
                   PostgreSQL
                        │
        ┌───────────────┼───────────────┐
        ▼               ▼               ▼
    Complaint       Assignment       Resolution
        │               │               │
        └───────────────┼───────────────┘
                        │
                        ▼
                   Notifications
```

---

# 111. Final Principles

The CivicSnap Complaint API shall follow these principles:

1. **Backend authority** — Complaint lifecycle decisions are controlled by Spring Boot.
2. **Secure ownership** — Citizens can only access their own complaints.
3. **Department isolation** — Department Administrators can only manage complaints within their department.
4. **Worker isolation** — Workers can only access complaints assigned to them.
5. **Controlled status transitions** — Complaint statuses cannot be changed arbitrarily.
6. **Server-side identity** — Citizen, worker, and administrator identities come from authentication.
7. **Category-based routing** — Departments are determined using trusted backend configuration.
8. **Evidence validation** — Uploaded images must be validated.
9. **AI assistance only** — AI-generated content remains a suggestion.
10. **Resolution verification** — Worker resolutions require appropriate administrative verification.
11. **SLA tracking** — Complaint processing is associated with SLA information.
12. **Auditability** — Important lifecycle operations are recorded.
13. **Notification support** — Relevant users receive status-change notifications.
14. **Transaction safety** — Related database changes remain consistent.
15. **Scalability** — Pagination and efficient queries are used for large datasets.
16. **Privacy** — Internal and personal information is exposed only according to authorization.
17. **No direct database access** — All client applications communicate through the REST API.

---

# 112. Final Summary

The Complaint API is the central business API of CivicSnap.

It connects:

```text
Citizen App
    ↓
Complaint Creation
    ↓
Submission
    ↓
Department Verification
    ↓
Worker Assignment
    ↓
Worker Field Work
    ↓
Resolution Submission
    ↓
Department Verification
    ↓
Complaint Completion
    ↓
Citizen Feedback
```

The API ensures that the **Citizen App, Worker App, and Admin Portal** can operate on the same complaint lifecycle while maintaining strict role, department, worker, and ownership boundaries.

The **Spring Boot backend remains the single authoritative layer** for complaint state, assignment, verification, SLA, resolution, security, and database operations.
