# CivicSnap — Citizen API

## 1. Introduction

The Citizen API provides all backend operations required by the **CivicSnap Citizen App**.

The Citizen App is developed using:

* Flutter
* Dart

The Citizen API is implemented using:

* Java
* Spring Boot
* Spring Security
* JWT
* PostgreSQL
* REST
* JSON

The API allows citizens to:

* Manage their account
* Create complaints
* Upload complaint images
* Capture complaint location
* Receive AI-assisted descriptions
* Submit complaints
* Track complaints
* View complaint history
* View resolutions
* Receive notifications
* Provide feedback

---

# 2. Citizen API Architecture

```text
┌──────────────────────────┐
│      Citizen App         │
│     Flutter + Dart       │
└────────────┬─────────────┘
             │
             │ HTTPS / REST
             ▼
┌──────────────────────────┐
│      Citizen API         │
│      Spring Boot         │
└────────────┬─────────────┘
             │
       ┌─────┼──────────┐
       │     │          │
       ▼     ▼          ▼
   Security  Service  Repository
                       │
                       ▼
                  PostgreSQL

External Services:
       │
       ├── AI Service
       ├── Maps
       ├── Object Storage
       └── Firebase FCM
```

---

# 3. Base URL

The Citizen API shall use the versioned API path:

```text
/api/v1
```

Example development URL:

```text
http://localhost:8080/api/v1
```

Production URL shall be environment-specific.

---

# 4. Authentication

All protected Citizen APIs require a valid JWT.

Request header:

```http
Authorization: Bearer <access-token>
```

The backend shall verify:

* Token validity
* Token expiration
* User identity
* User role
* Resource ownership

The required role for Citizen APIs is:

```text
CITIZEN
```

---

# 5. Citizen API Scope

The Citizen API covers:

```text
Citizen Account
       │
       ├── Profile
       │
       ├── Complaints
       │      ├── Create
       │      ├── Images
       │      ├── Location
       │      ├── AI Assistance
       │      ├── Submit
       │      ├── Track
       │      └── History
       │
       ├── Resolutions
       │
       ├── Notifications
       │
       └── Feedback
```

---

# 6. Citizen API Endpoint Summary

| Method | Endpoint                          | Purpose                     |
| ------ | --------------------------------- | --------------------------- |
| GET    | `/users/me`                       | Get profile                 |
| PATCH  | `/users/me`                       | Update profile              |
| POST   | `/complaints`                     | Create complaint            |
| GET    | `/complaints/my`                  | Get own complaints          |
| GET    | `/complaints/{id}`                | Get complaint details       |
| PATCH  | `/complaints/{id}`                | Update eligible complaint   |
| POST   | `/complaints/{id}/submit`         | Submit complaint            |
| POST   | `/complaints/{id}/images`         | Upload complaint image      |
| GET    | `/complaints/{id}/images`         | Get complaint images        |
| POST   | `/complaints/{id}/ai-description` | Generate AI suggestion      |
| GET    | `/complaints/{id}/timeline`       | Get complaint timeline      |
| GET    | `/complaints/{id}/resolution`     | Get resolution              |
| GET    | `/complaints/{id}/sla`            | Get SLA information         |
| GET    | `/categories`                     | Get complaint categories    |
| GET    | `/departments`                    | Get available departments   |
| GET    | `/notifications`                  | Get notifications           |
| PATCH  | `/notifications/{id}/read`        | Mark notification read      |
| PATCH  | `/notifications/read-all`         | Mark all notifications read |
| POST   | `/complaints/{id}/feedback`       | Submit feedback             |
| GET    | `/complaints/{id}/feedback`       | Get feedback                |

---

# 7. Get Citizen Profile

## Endpoint

```http
GET /api/v1/users/me
```

## Authentication

Required.

## Role

```text
CITIZEN
```

## Purpose

Returns the authenticated citizen's profile.

### Response

```json
{
  "success": true,
  "data": {
    "id": "USER-101",
    "fullName": "John Doe",
    "email": "john@example.com",
    "phone": "9876543210",
    "role": "CITIZEN",
    "isActive": true
  }
}
```

---

# 8. Update Citizen Profile

## Endpoint

```http
PATCH /api/v1/users/me
```

## Request

```json
{
  "fullName": "John Updated",
  "phone": "9876543211"
}
```

Only permitted profile fields may be updated.

The citizen must not be allowed to modify:

```text
role
departmentId
accountStatus
userId
```

---

# 9. Get Complaint Categories

## Endpoint

```http
GET /api/v1/categories
```

## Purpose

Returns active categories that citizens can use when creating complaints.

### Response

```json
{
  "success": true,
  "data": [
    {
      "id": "CAT-001",
      "name": "Road Damage",
      "description": "Potholes and damaged road surfaces.",
      "departmentId": "DEP-001"
    },
    {
      "id": "CAT-002",
      "name": "Street Light",
      "description": "Non-functional or damaged street lights.",
      "departmentId": "DEP-002"
    }
  ]
}
```

---

# 10. Get Departments

## Endpoint

```http
GET /api/v1/departments
```

Returns active departments where public visibility is permitted.

The Citizen App may use this information for complaint categorization or display.

---

# 11. Create Complaint

## Endpoint

```http
POST /api/v1/complaints
```

## Authentication

Required.

## Role

```text
CITIZEN
```

## Purpose

Creates a new complaint.

---

# 12. Create Complaint Request

```json
{
  "categoryId": "CAT-001",
  "title": "Large pothole",
  "description": "There is a large pothole near the main junction.",
  "latitude": 12.123456,
  "longitude": 75.123456,
  "locationAccuracy": 10.5
}
```

---

# 13. Create Complaint Fields

| Field            | Type        | Required | Description           |
| ---------------- | ----------- | -------: | --------------------- |
| categoryId       | UUID/String |      Yes | Complaint category    |
| title            | String      |      Yes | Short complaint title |
| description      | String      |      Yes | Complaint description |
| latitude         | Decimal     |      Yes | GPS latitude          |
| longitude        | Decimal     |      Yes | GPS longitude         |
| locationAccuracy | Decimal     |       No | GPS accuracy          |

---

# 14. Complaint Creation Process

```text
Citizen
   │
   ▼
Select Category
   │
   ▼
Add Description
   │
   ▼
Capture GPS
   │
   ▼
Upload Image
   │
   ▼
POST /complaints
   │
   ▼
Backend Validation
   │
   ▼
Determine Department
   │
   ▼
Create Complaint
   │
   ▼
Create SLA
   │
   ▼
Return Complaint ID
```

---

# 15. Create Complaint Response

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

# 16. Complaint Ownership

Every complaint must be associated with the authenticated citizen.

```text
Authenticated Citizen ID
          =
Complaint Citizen ID
```

A citizen cannot create or modify complaints on behalf of another citizen.

---

# 17. Upload Complaint Image

## Endpoint

```http
POST /api/v1/complaints/{complaintId}/images
```

## Content Type

```text
multipart/form-data
```

The request shall contain an image file.

---

# 18. Image Upload Validation

The backend shall validate:

* Authentication
* Complaint ownership
* File type
* File size
* File integrity
* Upload authorization

Supported formats may include:

```text
JPEG
PNG
WEBP
```

---

# 19. Complaint Image Response

```json
{
  "success": true,
  "data": {
    "id": "IMG-001",
    "complaintId": "7d3c...",
    "fileName": "pothole.jpg",
    "contentType": "image/jpeg",
    "uploadedAt": "2026-08-30T08:35:00Z"
  }
}
```

Actual image files should be stored in object/file storage rather than PostgreSQL.

---

# 20. Get Complaint Images

## Endpoint

```http
GET /api/v1/complaints/{complaintId}/images
```

Returns images belonging to the authenticated citizen's complaint.

The API should return secure image references rather than exposing unrestricted storage access.

---

# 21. AI Description API

## Endpoint

```http
POST /api/v1/complaints/{complaintId}/ai-description
```

## Purpose

Requests an AI-generated description based on complaint evidence.

---

# 22. AI Description Flow

```text
Complaint Image
       │
       ▼
CivicSnap Backend
       │
       ▼
AI Integration Service
       │
       ▼
External AI API
       │
       ▼
Suggested Description
       │
       ▼
Citizen App
```

---

# 23. AI Description Response

```json
{
  "success": true,
  "data": {
    "suggestedDescription": "A large pothole is visible on the road surface.",
    "suggestedCategory": "Road Damage"
  }
}
```

The AI result shall be treated as a suggestion.

---

# 24. AI Confirmation

The Citizen App should allow the citizen to:

```text
Accept suggestion
Edit suggestion
Reject suggestion
Write own description
```

The AI-generated text must not automatically become the final complaint description.

---

# 25. Update Complaint

## Endpoint

```http
PATCH /api/v1/complaints/{complaintId}
```

Only complaints in an editable state may be updated.

Example:

```text
DRAFT
```

may be editable.

A complaint that has already entered departmental processing should normally not allow unrestricted citizen modification.

---

# 26. Update Complaint Request

```json
{
  "title": "Updated pothole report",
  "description": "A large pothole is located near the junction."
}
```

The backend shall determine which fields can be modified based on complaint status.

---

# 27. Submit Complaint

## Endpoint

```http
POST /api/v1/complaints/{complaintId}/submit
```

## Purpose

Submits a draft complaint for departmental processing.

---

# 28. Submit Validation

Before submission, the backend shall verify:

```text
Complaint exists
       ↓
Complaint belongs to citizen
       ↓
Category exists
       ↓
Department exists
       ↓
Description available
       ↓
Required location available
       ↓
Required evidence available
       ↓
Complaint status = DRAFT
```

Only then can the complaint be submitted.

---

# 29. Submit Complaint Response

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

# 30. Complaint Status

Citizens may see statuses such as:

```text
SUBMITTED
VERIFIED
REJECTED
ASSIGNED
IN_PROGRESS
RESOLUTION_SUBMITTED
RESOLUTION_REJECTED
COMPLETED
```

The Citizen App should display user-friendly labels rather than exposing internal implementation details where appropriate.

---

# 31. Get My Complaints

## Endpoint

```http
GET /api/v1/complaints/my
```

Returns complaints created by the authenticated citizen.

---

# 32. Complaint Filters

Optional filters:

```text
status
category
priority
dateFrom
dateTo
page
size
sort
```

Example:

```http
GET /api/v1/complaints/my?status=COMPLETED&page=0&size=20
```

---

# 33. My Complaints Response

```json
{
  "success": true,
  "data": [
    {
      "id": "7d3c...",
      "complaintNumber": "CS-100001",
      "title": "Large pothole",
      "category": "Road Damage",
      "status": "IN_PROGRESS",
      "priority": "HIGH",
      "submittedAt": "2026-08-30T08:40:00Z"
    }
  ],
  "pagination": {
    "page": 0,
    "size": 20,
    "totalElements": 1,
    "totalPages": 1
  }
}
```

---

# 34. Get Complaint Details

## Endpoint

```http
GET /api/v1/complaints/{complaintId}
```

Returns detailed information about a complaint.

---

# 35. Complaint Details Response

```json
{
  "success": true,
  "data": {
    "id": "7d3c...",
    "complaintNumber": "CS-100001",
    "title": "Large pothole",
    "description": "A large pothole is located near the junction.",
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
      "accuracy": 10.5
    },
    "submittedAt": "2026-08-30T08:40:00Z"
  }
}
```

---

# 36. Complaint Ownership Validation

When retrieving a complaint:

```text
Authenticated User
       │
       ▼
Find Complaint
       │
       ▼
Check complaint.citizen_id
       │
       ├── Match → Allow
       │
       └── Different → 403 Forbidden
```

The backend must perform this check for every protected complaint operation.

---

# 37. Complaint Timeline

## Endpoint

```http
GET /api/v1/complaints/{complaintId}/timeline
```

Returns the complaint's relevant lifecycle events.

Example:

```json
{
  "success": true,
  "data": [
    {
      "status": "SUBMITTED",
      "timestamp": "2026-08-30T08:40:00Z"
    },
    {
      "status": "VERIFIED",
      "timestamp": "2026-08-30T09:10:00Z"
    },
    {
      "status": "ASSIGNED",
      "timestamp": "2026-08-30T09:20:00Z"
    },
    {
      "status": "IN_PROGRESS",
      "timestamp": "2026-08-30T10:30:00Z"
    }
  ]
}
```

Sensitive internal information should not be exposed unnecessarily.

---

# 38. Complaint SLA

## Endpoint

```http
GET /api/v1/complaints/{complaintId}/sla
```

Returns the SLA information visible to the citizen.

Example:

```json
{
  "success": true,
  "data": {
    "status": "ACTIVE",
    "deadlineAt": "2026-09-01T08:40:00Z"
  }
}
```

---

# 39. SLA Visibility

Citizens may see appropriate SLA information such as:

```text
Within SLA
Approaching Deadline
Delayed
Completed
```

Internal administrative SLA information may remain restricted.

---

# 40. Resolution API

## Endpoint

```http
GET /api/v1/complaints/{complaintId}/resolution
```

Returns the resolution associated with the citizen's completed or processed complaint.

---

# 41. Resolution Response

```json
{
  "success": true,
  "data": {
    "id": "RES-001",
    "description": "The damaged road section has been repaired.",
    "status": "APPROVED",
    "submittedAt": "2026-08-31T10:00:00Z",
    "verifiedAt": "2026-08-31T11:30:00Z"
  }
}
```

---

# 42. Resolution Evidence

If resolution images are available, the response may include secure references:

```json
{
  "images": [
    {
      "id": "RESIMG-001",
      "url": "<secure-resource-reference>"
    }
  ]
}
```

The storage system must not expose unrestricted public access to private evidence.

---

# 43. Resolution Rejection

If a resolution is rejected, the citizen may see a suitable status such as:

```text
Resolution requires additional work
```

Internal administrative notes should only be exposed when appropriate.

---

# 44. Notification API

## Get Notifications

```http
GET /api/v1/notifications
```

Returns notifications belonging to the authenticated citizen.

---

# 45. Notification Response

```json
{
  "success": true,
  "data": [
    {
      "id": "NOTIF-001",
      "title": "Complaint Verified",
      "message": "Your complaint CS-100001 has been verified.",
      "type": "COMPLAINT_VERIFIED",
      "isRead": false,
      "createdAt": "2026-08-30T09:10:00Z"
    }
  ]
}
```

---

# 46. Mark Notification as Read

## Endpoint

```http
PATCH /api/v1/notifications/{notificationId}/read
```

Only the notification owner may perform this operation.

---

# 47. Mark All Notifications as Read

## Endpoint

```http
PATCH /api/v1/notifications/read-all
```

Marks all eligible notifications belonging to the authenticated citizen as read.

---

# 48. Notification Types

Citizens may receive:

```text
COMPLAINT_SUBMITTED
COMPLAINT_VERIFIED
COMPLAINT_REJECTED
WORKER_ASSIGNED
WORK_STARTED
RESOLUTION_SUBMITTED
RESOLUTION_REJECTED
RESOLUTION_APPROVED
COMPLAINT_COMPLETED
SLA_WARNING
SLA_BREACH
```

---

# 49. Push Notifications

Push notifications may be delivered through Firebase Cloud Messaging.

```text
Spring Boot
     │
     ▼
Notification Service
     │
     ▼
Firebase FCM
     │
     ▼
Flutter Citizen App
```

The notification record should still be stored in the database.

---

# 50. Submit Feedback

## Endpoint

```http
POST /api/v1/complaints/{complaintId}/feedback
```

Feedback is submitted after complaint completion.

---

# 51. Feedback Request

```json
{
  "rating": 5,
  "comment": "The issue was resolved quickly and properly."
}
```

---

# 52. Feedback Validation

The backend shall verify:

```text
Citizen is authenticated
       ↓
Complaint exists
       ↓
Complaint belongs to citizen
       ↓
Complaint is COMPLETED
       ↓
No existing feedback
       ↓
Rating is 1–5
```

---

# 53. Feedback Response

```json
{
  "success": true,
  "data": {
    "id": "FB-001",
    "complaintId": "7d3c...",
    "rating": 5,
    "comment": "The issue was resolved quickly and properly.",
    "createdAt": "2026-09-01T12:00:00Z"
  },
  "message": "Feedback submitted successfully."
}
```

---

# 54. Get Feedback

## Endpoint

```http
GET /api/v1/complaints/{complaintId}/feedback
```

Returns the citizen's feedback for the complaint if it exists.

---

# 55. Citizen Complaint Lifecycle

```text
                    CITIZEN
                       │
                       ▼
                Create Complaint
                       │
                       ▼
                    DRAFT
                       │
                       ▼
                  Add Evidence
                       │
                       ▼
                  Add Location
                       │
                       ▼
                 AI Assistance
                       │
                       ▼
                 Review / Edit
                       │
                       ▼
                   SUBMIT
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
                       │
                       ▼
                   FEEDBACK
```

---

# 56. Citizen API Authorization Matrix

| Operation                        | Citizen |
| -------------------------------- | :-----: |
| Get Own Profile                  |    ✓    |
| Update Own Profile               |    ✓    |
| Get Categories                   |    ✓    |
| Get Departments                  |    ✓    |
| Create Complaint                 |    ✓    |
| Upload Complaint Image           |    ✓    |
| Generate AI Description          |    ✓    |
| Update Draft Complaint           |    ✓    |
| Submit Complaint                 |    ✓    |
| View Own Complaints              |    ✓    |
| View Own Complaint Details       |    ✓    |
| View Own Timeline                |    ✓    |
| View Own SLA                     |    ✓    |
| View Resolution                  |    ✓    |
| View Notifications               |    ✓    |
| Mark Own Notification Read       |    ✓    |
| Submit Feedback                  |    ✓    |
| View Own Feedback                |    ✓    |
| Access Other Citizen's Complaint |    ✗    |
| Assign Worker                    |    ✗    |
| Verify Complaint                 |    ✗    |
| Approve Resolution               |    ✗    |
| Manage Department                |    ✗    |
| View System Reports              |    ✗    |
| View Audit Logs                  |    ✗    |

---

# 57. Citizen API Error Codes

Recommended errors include:

```text
COMPLAINT_NOT_FOUND
COMPLAINT_NOT_OWNED
COMPLAINT_NOT_EDITABLE
INVALID_COMPLAINT_STATUS
CATEGORY_NOT_FOUND
DEPARTMENT_NOT_FOUND
IMAGE_NOT_FOUND
INVALID_IMAGE
IMAGE_TOO_LARGE
AI_SERVICE_UNAVAILABLE
FEEDBACK_NOT_ALLOWED
FEEDBACK_ALREADY_EXISTS
INVALID_RATING
NOTIFICATION_NOT_FOUND
NOTIFICATION_NOT_OWNED
```

---

# 58. HTTP Error Responses

### Unauthorized

```http
401 Unauthorized
```

### Forbidden

```http
403 Forbidden
```

### Not Found

```http
404 Not Found
```

### Conflict

```http
409 Conflict
```

### Validation Error

```http
422 Unprocessable Entity
```

---

# 59. Standard Error Response

```json
{
  "success": false,
  "error": {
    "code": "COMPLAINT_NOT_OWNED",
    "message": "You do not have permission to access this complaint."
  }
}
```

---

# 60. Validation Rules

The Citizen API shall validate:

### Complaint

```text
Title
Description
Category
Latitude
Longitude
```

### Images

```text
File Type
File Size
File Integrity
```

### Feedback

```text
Rating: 1–5
Comment Length
```

---

# 61. Pagination

The following endpoints should support pagination:

```text
GET /complaints/my
GET /notifications
```

Example:

```http
GET /api/v1/complaints/my?page=0&size=20
```

---

# 62. Sorting

Complaint history may support sorting.

Example:

```http
GET /api/v1/complaints/my?sort=submittedAt,desc
```

The API should expose only supported sorting fields.

---

# 63. Data Privacy

Citizen APIs shall not expose unnecessary information about:

* Other citizens
* Workers
* Internal administrators
* Internal department operations
* Private audit information

For example, a citizen may see that a complaint was assigned, but does not necessarily need access to a worker's private information.

---

# 64. Citizen API Security

The API shall use:

```text
HTTPS
JWT
Spring Security
Role-Based Authorization
Ownership Validation
Input Validation
File Validation
Rate Limiting
Secure Storage
```

---

# 65. Client-Side Security

The Flutter Citizen App should:

* Store tokens securely.
* Never hard-code API secrets.
* Never connect directly to PostgreSQL.
* Handle token expiration.
* Validate API responses.
* Avoid storing unnecessary sensitive information locally.

---

# 66. Backend Ownership Enforcement

The backend must never rely on a citizen-provided `citizenId`.

For example, this request should not be trusted:

```json
{
  "citizenId": "USER-999",
  "description": "..."
}
```

Instead:

```text
JWT
 ↓
Authenticated User ID
 ↓
Backend assigns citizen_id
```

This prevents users from creating records under another user's identity.

---

# 67. Department Routing

When a citizen selects a category:

```text
Category
   │
   ▼
Department Mapping
   │
   ▼
Complaint Department
```

The department shall be determined by the backend.

The client should not be trusted to assign an arbitrary department.

---

# 68. Example Department Routing

```text
Road Damage
     ↓
Road/Public Works Department

Garbage
     ↓
Waste Management Department

Water Leakage
     ↓
Water Supply Department

Street Light
     ↓
Electrical / Street Lighting Department
```

The actual department-category mappings shall be configured by authorized administrators.

---

# 69. Complaint Number Generation

Complaint numbers shall be generated by the backend.

Example:

```text
CS-100001
CS-100002
CS-100003
```

The Citizen App must not generate authoritative complaint numbers.

---

# 70. Timestamp Generation

Important timestamps shall be generated by the server.

Examples:

```text
createdAt
submittedAt
updatedAt
```

The backend should not trust client-supplied timestamps for authoritative lifecycle events.

---

# 71. Idempotency

Operations such as complaint submission should be protected against accidental duplicate execution.

Example:

```text
Citizen taps Submit twice
        ↓
Backend
        ↓
Only one valid submission
```

Idempotency keys may be introduced if required.

---

# 72. Offline Considerations

The Flutter Citizen App may temporarily operate without network connectivity.

The application may store an unfinished draft locally.

However:

```text
Offline Draft
      ↓
Network Available
      ↓
Backend Submission
```

The backend remains the authoritative source of submitted complaints.

---

# 73. API Transaction Flow

Complaint submission may involve:

```text
Validate Complaint
       ↓
Determine Department
       ↓
Update Complaint Status
       ↓
Create SLA
       ↓
Create Audit Event
       ↓
Create Notification
```

Critical database operations shall be handled transactionally where appropriate.

---

# 74. AI Failure Handling

AI assistance is optional.

If AI is unavailable:

```text
AI Request
    ↓
AI Service Failure
    ↓
Return AI Unavailable
    ↓
Citizen Can Continue Manually
```

AI failure must not prevent normal complaint submission unless AI is explicitly made mandatory by future requirements.

---

# 75. Notification Failure Handling

Notification delivery failure should not undo a successful complaint operation.

Example:

```text
Complaint Completed
       │
       ├── Database Update ✓
       │
       ├── Audit ✓
       │
       └── Push Notification ✗
```

The complaint should remain completed.

The notification service can retry delivery independently.

---

# 76. API Performance

Citizen APIs should support:

* Pagination
* Efficient queries
* Database indexes
* Response compression where appropriate
* Secure image references
* Asynchronous notifications
* Caching of stable category information

Large image files should not be unnecessarily included in ordinary complaint-list responses.

---

# 77. Response Optimization

The complaint list should return summary information.

```text
Complaint List
     │
     ├── Complaint Number
     ├── Title
     ├── Category
     ├── Status
     ├── Priority
     └── Submitted Date
```

Detailed images, timeline, resolution, and SLA information should be loaded separately when required.

---

# 78. API Module Structure

The backend may organize citizen-related APIs as:

```text
controller/
│
├── AuthController
├── UserController
├── ComplaintController
├── ComplaintImageController
├── CategoryController
├── DepartmentController
├── NotificationController
└── FeedbackController
```

Supporting services:

```text
service/
├── ComplaintService
├── ComplaintImageService
├── AIService
├── NotificationService
├── FeedbackService
└── UserService
```

---

# 79. DTO Structure

Recommended DTOs include:

```text
RegisterRequest
LoginRequest
UserResponse

ComplaintCreateRequest
ComplaintUpdateRequest
ComplaintResponse
ComplaintSummaryResponse
ComplaintTimelineResponse

AIComplaintResponse

FeedbackRequest
FeedbackResponse

NotificationResponse
```

---

# 80. Citizen API Data Flow

```text
┌─────────────────────┐
│    Flutter App      │
│     Citizen         │
└──────────┬──────────┘
           │
           ▼
     HTTPS REST API
           │
           ▼
┌─────────────────────┐
│ Complaint Controller│
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Complaint Service   │
└──────────┬──────────┘
           │
     ┌─────┼──────┐
     │     │      │
     ▼     ▼      ▼
 Category  SLA   AI
     │
     ▼
 Repository
     │
     ▼
 PostgreSQL
```

---

# 81. Complete Citizen Complaint API Flow

```text
                    CITIZEN APP
                         │
                         ▼
                  Select Category
                         │
                         ▼
                   Add Description
                         │
                         ▼
                    Add Location
                         │
                         ▼
                  Upload Evidence
                         │
                         ▼
                  AI Assistance
                         │
                         ▼
                    Review/Edit
                         │
                         ▼
               POST /complaints/{id}/submit
                         │
                         ▼
                 SPRING BOOT API
                         │
                         ▼
                   Authenticate
                         │
                         ▼
                     Authorize
                         │
                         ▼
                    Validate
                         │
                         ▼
               Determine Department
                         │
                         ▼
                   Update Status
                         │
                         ▼
                     Create SLA
                         │
                         ▼
                  Create Audit
                         │
                         ▼
                Create Notification
                         │
                         ▼
                    PostgreSQL
                         │
                         ▼
                  Citizen Tracking
```

---

# 82. Final Citizen API Architecture

```text
┌───────────────────────────────────────────────────────┐
│                    CITIZEN APP                       │
│                  Flutter + Dart                      │
│                                                       │
│ Login │ Dashboard │ Complaints │ Tracking │ Feedback │
└──────────────────────────┬────────────────────────────┘
                           │
                         HTTPS
                           │
                           ▼
┌───────────────────────────────────────────────────────┐
│                  CITIZEN API                         │
│                Spring Boot + REST                    │
│                                                       │
│ User │ Complaint │ Image │ AI │ SLA │ Notification   │
│ Feedback │ Category │ Department                      │
└──────────────────────────┬────────────────────────────┘
                           │
               ┌───────────┼────────────┐
               │           │            │
               ▼           ▼            ▼
          PostgreSQL    Object Storage  External
                                      Services
                                         │
                              ┌──────────┼──────────┐
                              ▼          ▼          ▼
                             AI        Maps       FCM
```

---

# 83. Final Citizen API Principles

The Citizen API shall follow these principles:

1. **Citizen ownership** — Citizens can only access their own complaints.
2. **Secure authentication** — All protected endpoints require JWT authentication.
3. **Backend authorization** — Security rules are enforced server-side.
4. **Department routing** — Complaint departments are determined from trusted backend configuration.
5. **Controlled lifecycle** — Citizens cannot arbitrarily change complaint status.
6. **AI as assistance** — AI-generated descriptions remain suggestions until confirmed.
7. **Secure media** — Images are validated and stored outside the relational database.
8. **Server timestamps** — Important lifecycle timestamps are generated by the backend.
9. **Consistent responses** — APIs use standardized success and error formats.
10. **Pagination** — Large complaint and notification collections are paginated.
11. **Privacy** — Other citizens' and internal users' private information is protected.
12. **Auditability** — Important complaint actions are traceable.
13. **Transaction safety** — Critical database operations are performed consistently.
14. **External service isolation** — AI, Maps, storage, and FCM remain backend-managed integrations.
15. **No direct database access** — The Flutter Citizen App never connects directly to PostgreSQL.

---

# 84. Final Summary

The Citizen API provides the complete backend interface for the **CivicSnap Flutter Citizen App**.

Its primary responsibilities are:

```text
Authentication
      ↓
Citizen Profile
      ↓
Complaint Creation
      ↓
Image Upload
      ↓
GPS Location
      ↓
AI Assistance
      ↓
Complaint Submission
      ↓
Complaint Tracking
      ↓
Resolution Viewing
      ↓
Notifications
      ↓
Citizen Feedback
```

The Citizen API communicates with the Spring Boot backend through secure REST endpoints and uses PostgreSQL as the authoritative data store.

The backend ensures that each citizen can access **only their own complaint information**, while departmental processing, worker assignment, resolution verification, SLA management, and system administration remain controlled by the appropriate backend APIs and roles.
