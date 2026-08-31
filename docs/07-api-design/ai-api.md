# CivicSnap — AI API

## 1. Introduction

The AI API provides artificial-intelligence-assisted features for the CivicSnap platform.

The AI module primarily supports citizens during complaint creation by analyzing submitted complaint evidence and generating useful suggestions such as:

* Complaint description
* Complaint category
* Possible issue type
* Supporting observations

The AI system does **not** independently approve, reject, assign, complete, or modify complaints.

The final decision always remains with the CivicSnap backend, citizen, Department Administrator, or other authorized actor depending on the operation.

---

# 2. AI API Objectives

The AI API shall:

1. Analyze complaint images.
2. Generate suggested complaint descriptions.
3. Suggest complaint categories.
4. Assist citizens in describing civic issues.
5. Return structured AI results.
6. Handle AI service failures gracefully.
7. Protect uploaded complaint evidence.
8. Prevent unauthorized AI requests.
9. Avoid exposing sensitive information.
10. Keep AI output separate from authoritative complaint data.
11. Allow citizens to review and edit AI suggestions.
12. Support future AI-powered CivicSnap features.

---

# 3. AI Architecture

```text
┌──────────────────────────┐
│      Citizen App         │
│     Flutter + Dart       │
└────────────┬─────────────┘
             │
             │ HTTPS
             ▼
┌──────────────────────────┐
│       Spring Boot        │
│        AI API            │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│     AI Service Layer     │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│      External AI API     │
└────────────┬─────────────┘
             │
             ▼
       AI Result
             │
             ▼
┌──────────────────────────┐
│     Spring Boot Backend  │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│       Citizen App        │
│ Review / Edit / Accept   │
└──────────────────────────┘
```

---

# 4. AI API Design Principle

The AI API follows:

```text
AI = ASSISTANCE
NOT
AI = AUTHORITY
```

The AI service may recommend:

```text
Description
Category
Issue Type
```

But it must not independently perform:

```text
Complaint Verification
Complaint Rejection
Worker Assignment
Resolution Approval
Complaint Completion
```

---

# 5. AI Technology

The AI integration is implemented through the CivicSnap backend.

| Component       | Technology                               |
| --------------- | ---------------------------------------- |
| Backend         | Java                                     |
| Framework       | Spring Boot                              |
| API             | REST                                     |
| Authentication  | JWT                                      |
| AI Integration  | External AI API                          |
| Data Format     | JSON                                     |
| Image Transport | Secure backend-managed storage/reference |
| Database        | PostgreSQL                               |

The exact external AI provider may be configured independently of the application architecture.

---

# 6. Base URL

AI endpoints use:

```text
/api/v1
```

Primary endpoint:

```text
POST /api/v1/complaints/{complaintId}/ai-description
```

---

# 7. Authentication

AI APIs require authentication.

Request:

```http
Authorization: Bearer <JWT_TOKEN>
```

The backend shall verify:

* JWT validity
* User identity
* User role
* Complaint ownership
* Complaint state
* AI request eligibility

---

# 8. Authorized User

The primary AI consumer is:

```text
CITIZEN
```

A citizen can request AI assistance only for their own complaint.

Future AI features may be exposed to other roles under separate authorization rules.

---

# 9. AI Endpoint Summary

| Method | Endpoint                          | Role            | Purpose                        |
| ------ | --------------------------------- | --------------- | ------------------------------ |
| POST   | `/complaints/{id}/ai-description` | Citizen         | Generate complaint suggestions |
| POST   | `/ai/analyze-image`               | Authorized      | Analyze an image               |
| POST   | `/ai/classify-complaint`          | Authorized      | Suggest category               |
| GET    | `/complaints/{id}/ai-results`     | Complaint Owner | View generated suggestions     |

The first endpoint is the primary AI feature for the initial CivicSnap implementation.

---

# 10. Generate AI Complaint Description

## Endpoint

```http
POST /api/v1/complaints/{complaintId}/ai-description
```

## Purpose

Analyzes the complaint evidence and generates a suggested description.

---

# 11. Request

The complaint ID is supplied as a path parameter.

```http
POST /api/v1/complaints/7d3c.../ai-description
Authorization: Bearer <JWT_TOKEN>
```

The backend obtains the associated complaint images from the authorized storage layer.

---

# 12. Request Validation

Before sending the request to the AI service, the backend shall verify:

```text
User authenticated
       ↓
User = Complaint Owner
       ↓
Complaint exists
       ↓
Complaint is AI-eligible
       ↓
Required evidence available
       ↓
AI request allowed
```

---

# 13. AI Processing Flow

```text
Citizen
   │
   ▼
Select "AI Assist"
   │
   ▼
POST /complaints/{id}/ai-description
   │
   ▼
Authenticate User
   │
   ▼
Validate Complaint Ownership
   │
   ▼
Retrieve Evidence
   │
   ▼
AI Service
   │
   ▼
External AI Provider
   │
   ▼
Analyze Evidence
   │
   ▼
Generate Structured Suggestions
   │
   ▼
Validate AI Response
   │
   ▼
Return Result
   │
   ▼
Citizen Reviews Suggestion
```

---

# 14. AI Response

Example:

```json
{
  "success": true,
  "data": {
    "suggestedDescription": "A large pothole is visible on the road surface near the junction.",
    "suggestedCategory": {
      "id": "CAT-001",
      "name": "Road Damage"
    },
    "confidence": 0.91
  }
}
```

---

# 15. AI Response Fields

| Field                | Type    | Description                  |
| -------------------- | ------- | ---------------------------- |
| suggestedDescription | String  | AI-generated description     |
| suggestedCategory    | Object  | Suggested complaint category |
| confidence           | Decimal | Optional confidence score    |

The confidence value should only be displayed or relied upon if the AI provider produces a meaningful calibrated score.

---

# 16. AI Description Rules

Generated descriptions should:

* Describe observable evidence.
* Avoid unsupported assumptions.
* Avoid identifying individuals unnecessarily.
* Avoid inventing locations.
* Avoid claiming certainty when the image is ambiguous.
* Remain editable by the citizen.

Example:

```text
Good:
"A large pothole is visible on the road surface."

Avoid:
"This pothole was caused by last week's heavy rain."
```

The second statement may contain information that cannot be determined from the image.

---

# 17. AI Category Suggestion

The AI may suggest a category:

```json
{
  "suggestedCategory": {
    "id": "CAT-001",
    "name": "Road Damage"
  }
}
```

The category must be matched against categories configured in CivicSnap.

The AI must not create a new authoritative category.

---

# 18. Category Validation

```text
AI Suggestion
      │
      ▼
Find Matching CivicSnap Category
      │
      ├── Match → Return Suggestion
      │
      └── No Match → Ignore / Return Null
```

This prevents arbitrary AI-generated categories from entering the database.

---

# 19. Citizen Confirmation

The Citizen App displays:

```text
AI Suggested Description

"A large pothole is visible on the road surface."

[ Edit ]    [ Accept ]
```

The citizen may:

```text
Accept
Edit
Reject
```

---

# 20. AI Output Is Not Final Data

The AI result must not automatically overwrite:

```text
complaint.description
complaint.category_id
complaint.department_id
```

Instead:

```text
AI Suggestion
     │
     ▼
Citizen Review
     │
     ▼
Accept / Edit
     │
     ▼
Final Complaint Data
```

---

# 21. AI Image Analysis

## Endpoint

```http
POST /api/v1/ai/analyze-image
```

This endpoint may be used as a generalized AI image-analysis service.

It should normally be called internally through controlled application flows rather than being exposed unnecessarily to public clients.

---

# 22. Image Analysis Request

Conceptually:

```json
{
  "imageReference": "IMG-001"
}
```

The backend resolves the image reference through secure storage.

Clients should not be able to use the endpoint to analyze arbitrary files that they do not own.

---

# 23. Image Analysis Response

```json
{
  "success": true,
  "data": {
    "observations": [
      "Damaged road surface",
      "Large depression in road"
    ],
    "suggestedIssueType": "Road Damage"
  }
}
```

---

# 24. AI Classification

## Endpoint

```http
POST /api/v1/ai/classify-complaint
```

Purpose:

```text
Complaint Evidence
        ↓
AI Classification
        ↓
Suggested CivicSnap Category
```

---

# 25. Classification Request

```json
{
  "complaintId": "7d3c..."
}
```

The backend validates that the requesting user has access to the complaint.

---

# 26. Classification Response

```json
{
  "success": true,
  "data": {
    "suggestedCategory": {
      "id": "CAT-001",
      "name": "Road Damage"
    },
    "confidence": 0.94
  }
}
```

---

# 27. AI Result Retrieval

## Endpoint

```http
GET /api/v1/complaints/{complaintId}/ai-results
```

Returns AI suggestions associated with the complaint if AI result persistence is enabled.

---

# 28. AI Result Example

```json
{
  "success": true,
  "data": {
    "complaintId": "7d3c...",
    "results": [
      {
        "type": "DESCRIPTION",
        "value": "A large pothole is visible on the road surface.",
        "createdAt": "2026-08-30T08:36:00Z"
      },
      {
        "type": "CATEGORY",
        "value": "Road Damage",
        "createdAt": "2026-08-30T08:36:00Z"
      }
    ]
  }
}
```

---

# 29. AI Result Persistence

AI results may be:

### Option A — Temporary

```text
AI Request
   ↓
AI Result
   ↓
Citizen Review
   ↓
Discard
```

### Option B — Persisted

```text
AI Request
   ↓
AI Result
   ↓
Database
   ↓
Citizen Review
```

For CivicSnap, persisted AI metadata may be useful for auditability and future improvement, but raw AI output should not become authoritative complaint data automatically.

---

# 30. AI Database Structure

If AI results are persisted, a conceptual structure may be:

```text
AI_ANALYSIS
│
├── id
├── complaint_id
├── analysis_type
├── suggested_description
├── suggested_category_id
├── confidence
├── model_name
├── model_version
├── created_at
└── accepted_by_user
```

---

# 31. AI Auditability

AI requests may record:

```text
analysisId
complaintId
requestingUserId
analysisType
modelName
modelVersion
timestamp
resultStatus
```

Raw sensitive image data should not be duplicated unnecessarily into audit records.

---

# 32. AI Model Information

Where supported, the backend may record:

```text
Model Name
Model Version
Prompt Version
Timestamp
```

Example:

```json
{
  "model": "civic-vision-model",
  "version": "1.0"
}
```

This helps reproduce and investigate AI behavior.

---

# 33. Prompt Management

AI prompts should be managed by the backend rather than constructed directly by the Flutter application.

```text
Flutter App
    │
    ▼
Spring Boot
    │
    ▼
Prompt Template
    │
    ▼
AI Provider
```

This prevents clients from controlling system-level AI instructions.

---

# 34. AI Prompt Principles

Prompts should instruct the AI to:

1. Describe visible evidence.
2. Avoid unsupported claims.
3. Prefer concise civic descriptions.
4. Suggest only configured categories.
5. Return structured output.
6. Avoid unnecessary personal information.
7. Indicate uncertainty when appropriate.

---

# 35. Structured AI Output

The backend should prefer structured responses.

Example:

```json
{
  "description": "...",
  "category": "...",
  "observations": [],
  "confidence": 0.90
}
```

Structured output makes backend validation easier.

---

# 36. AI Response Validation

The Spring Boot backend shall validate AI responses before returning them to the client.

Validation includes:

```text
Valid JSON
       ↓
Required fields
       ↓
Length restrictions
       ↓
Valid category
       ↓
Safe content
       ↓
Return to Client
```

Malformed responses should not be passed directly to the Flutter application.

---

# 37. AI Hallucination Protection

The system should minimize unsupported AI statements.

For example:

```text
Image:
Road with pothole

Acceptable:
"Large pothole visible on the road."

Not acceptable:
"The pothole was created three days ago."
```

The AI must distinguish visible evidence from assumptions.

---

# 38. AI Safety Boundary

The AI service shall never independently:

```text
Approve Complaint
Reject Complaint
Assign Worker
Change Department
Change SLA
Approve Resolution
Complete Complaint
Deactivate User
```

These actions remain controlled by authorized backend workflows.

---

# 39. AI Failure Handling

If the AI service fails:

```text
Citizen
   │
   ▼
AI Request
   │
   ▼
AI Service
   │
   └── Failure
        │
        ▼
AI Service Unavailable
        │
        ▼
Citizen Can Continue Manually
```

AI failure must not prevent ordinary complaint creation.

---

# 40. AI Timeout

The backend should define a maximum AI request timeout.

If the timeout is exceeded:

```http
503 Service Unavailable
```

or another appropriate service-unavailable response may be returned.

---

# 41. AI Rate Limiting

AI requests should be rate-limited to prevent:

* Abuse
* Excessive API costs
* Automated request flooding
* Resource exhaustion

Example policy:

```text
User
 ↓
AI Request Limit
 ↓
Accept / Reject
```

Exact limits should be configurable.

---

# 42. AI Retry Policy

Temporary AI failures may be retried by the backend.

Recommended:

```text
Request
  ↓
Attempt 1
  ↓
Temporary Failure
  ↓
Attempt 2
  ↓
Temporary Failure
  ↓
Final Failure
```

Retries should use a bounded strategy.

Client-side repeated retries should be avoided.

---

# 43. AI Provider Isolation

The Flutter and React applications must not communicate directly with the external AI provider.

Correct:

```text
Flutter
   ↓
Spring Boot
   ↓
AI Provider
```

Incorrect:

```text
Flutter
   ↓
AI Provider
```

This protects AI credentials and centralizes business rules.

---

# 44. AI API Keys

AI provider credentials must never be included in:

```text
Flutter source code
React source code
Mobile APK
Public configuration
Git repository
```

They must be managed securely on the backend.

---

# 45. AI Image Privacy

Complaint images may contain:

* People
* Vehicles
* Buildings
* Addresses
* License plates
* Other identifying information

The AI service should process only the minimum information required.

Where appropriate, privacy-preserving processing should be considered before external transmission.

---

# 46. Data Retention

AI data retention should follow CivicSnap privacy requirements.

The system should avoid storing duplicate copies of images.

Recommended:

```text
Original Image
      │
      ▼
Secure Storage
      │
      ├── AI Processing
      │
      └── Complaint Evidence
```

---

# 47. AI Authorization Matrix

| Operation                                | Citizen |  Worker  | Dept. Admin |   System Admin  |
| ---------------------------------------- | :-----: | :------: | :---------: | :-------------: |
| AI Description for Own Complaint         |    ✓    |     ✗    |      ✗      |        ✓        |
| AI Category Suggestion for Own Complaint |    ✓    |     ✗    |      ✗      |        ✓        |
| Analyze Own Complaint Image              |    ✓    |     ✗    |      ✗      |        ✓        |
| Analyze Assigned Complaint               |    ✗    | Optional |   Optional  |        ✓        |
| View Own AI Results                      |    ✓    |     ✗    |      ✗      |        ✓        |
| Modify AI Model                          |    ✗    |     ✗    |      ✗      | Authorized Only |

The exact administrative AI permissions may be restricted further.

---

# 48. AI Error Codes

Recommended error codes:

```text
AI_SERVICE_UNAVAILABLE
AI_REQUEST_TIMEOUT
AI_RATE_LIMIT_EXCEEDED
AI_INVALID_RESPONSE
AI_PROCESSING_FAILED
AI_RESULT_NOT_FOUND
AI_UNSUPPORTED_IMAGE
AI_CONTENT_REJECTED
AI_REQUEST_NOT_ALLOWED
AI_CATEGORY_NOT_FOUND
```

---

# 49. Standard AI Error Response

```json
{
  "success": false,
  "error": {
    "code": "AI_SERVICE_UNAVAILABLE",
    "message": "AI assistance is temporarily unavailable."
  }
}
```

The API should provide a useful message without exposing internal provider details.

---

# 50. HTTP Status Codes

| Status | Meaning                     |
| ------ | --------------------------- |
| 200    | AI request successful       |
| 400    | Invalid AI request          |
| 401    | Authentication required     |
| 403    | AI access denied            |
| 404    | Complaint/result not found  |
| 409    | Invalid complaint state     |
| 413    | Image/request too large     |
| 422    | AI input validation failure |
| 429    | AI rate limit exceeded      |
| 500    | Internal processing error   |
| 503    | AI provider unavailable     |

---

# 51. AI Request State

An AI analysis may have:

```text
PENDING
PROCESSING
COMPLETED
FAILED
```

Example:

```text
PENDING
   ↓
PROCESSING
   ↓
COMPLETED
```

Failure:

```text
PROCESSING
   ↓
FAILED
```

---

# 52. Asynchronous AI Processing

For larger or more expensive AI operations, the system may use asynchronous processing.

```text
Citizen
   │
   ▼
POST /ai-description
   │
   ▼
Create AI Job
   │
   ▼
202 Accepted
   │
   ▼
Background AI Worker
   │
   ▼
External AI Provider
   │
   ▼
Store Result
   │
   ▼
Notify Client
```

For simple description generation, synchronous processing may be sufficient.

---

# 53. Synchronous AI Flow

Initial implementation may use:

```text
Flutter
   ↓
POST /ai-description
   ↓
Spring Boot
   ↓
AI Provider
   ↓
Spring Boot
   ↓
Flutter
```

This is simpler for the initial CivicSnap implementation.

---

# 54. Asynchronous AI Flow

If AI processing becomes slow:

```text
Flutter
   ↓
POST /ai-description
   ↓
Spring Boot
   ↓
AI Job Queue
   ↓
Background Worker
   ↓
AI Provider
   ↓
Database
   ↓
Notification
   ↓
Flutter
```

The architecture should allow this transition later.

---

# 55. AI Caching

AI results may be cached where appropriate.

For example, repeated requests for the same complaint evidence could potentially reuse an existing result.

However, caching must account for:

* Changed images
* Changed complaint content
* Model changes
* Prompt changes

---

# 56. Duplicate AI Request Protection

The system should prevent unnecessary repeated requests.

Example:

```text
Citizen taps AI Assist
       │
       ▼
Request sent
       │
       ▼
Button disabled
       │
       ▼
AI response
       │
       ▼
Button enabled
```

The backend should also protect against duplicate requests.

---

# 57. AI Cost Control

The backend should control:

* Maximum image size
* Maximum image count
* Request frequency
* Maximum prompt length
* AI model selection
* Retry count

This helps control external AI usage and cost.

---

# 58. AI Image Requirements

Before AI processing, images should be validated for:

```text
Supported format
Maximum file size
Valid image structure
Readable resolution
```

Unsupported images should return:

```text
AI_UNSUPPORTED_IMAGE
```

---

# 59. AI Content Filtering

AI responses should be checked for inappropriate or irrelevant content before being returned to the user.

The AI output should remain focused on the civic issue.

---

# 60. AI API Security

The AI API shall implement:

```text
JWT Authentication
Role-Based Authorization
Complaint Ownership Validation
Input Validation
Image Validation
Rate Limiting
Timeouts
Secure API Keys
HTTPS
Audit Logging
```

---

# 61. AI API Data Flow

```text
┌───────────────────────┐
│     Citizen App       │
│      Flutter          │
└───────────┬───────────┘
            │
            │ JWT + Request
            ▼
┌───────────────────────┐
│      Spring Boot      │
│       AI API          │
└───────────┬───────────┘
            │
            ▼
┌───────────────────────┐
│ Ownership Validation  │
└───────────┬───────────┘
            │
            ▼
┌───────────────────────┐
│    Image Retrieval    │
└───────────┬───────────┘
            │
            ▼
┌───────────────────────┐
│    AI Service Layer   │
└───────────┬───────────┘
            │
            ▼
┌───────────────────────┐
│    External AI API    │
└───────────┬───────────┘
            │
            ▼
┌───────────────────────┐
│ Response Validation   │
└───────────┬───────────┘
            │
            ▼
┌───────────────────────┐
│      Citizen App      │
│   Review / Edit / Use │
└───────────────────────┘
```

---

# 62. AI Integration Service

Recommended backend structure:

```text
service/
│
├── AIService
├── AIAnalysisService
├── AIResponseValidator
└── AIProviderClient
```

---

# 63. AI Provider Client

The provider-specific integration should be isolated:

```text
AIService
    │
    ▼
AIProviderClient
    │
    ▼
External AI API
```

This allows the external provider to be changed without redesigning the entire CivicSnap application.

---

# 64. AI Controller

Recommended controller:

```text
controller/
└── AIController
```

Complaint-specific AI actions may alternatively remain within:

```text
ComplaintController
```

depending on the final Spring Boot package structure.

---

# 65. AI DTOs

Recommended DTOs:

```text
AIAnalysisRequest
AIAnalysisResponse

AIDescriptionResponse
AICategorySuggestion
AIObservation

AIErrorResponse
```

---

# 66. AI Service Example Flow

```text
AIController
     │
     ▼
AIService
     │
     ├── Validate User
     │
     ├── Validate Complaint
     │
     ├── Retrieve Evidence
     │
     ├── Build Prompt
     │
     ├── Call Provider
     │
     ├── Validate Response
     │
     └── Return Result
```

---

# 67. AI Database Relationship

If AI results are stored:

```text
COMPLAINT
    │
    └────< AI_ANALYSIS
```

This maintains a relationship between the AI operation and the complaint that triggered it.

---

# 68. AI Audit Relationship

```text
AI_ANALYSIS
     │
     └────< AUDIT_LOG
```

Important AI operations can therefore be traced.

---

# 69. AI Acceptance Flow

```text
AI Result
    │
    ▼
Citizen Review
    │
    ├────────────┐
    │            │
    ▼            ▼
 Accept         Edit
    │            │
    └─────┬──────┘
          ▼
    Final Complaint
          │
          ▼
       Submit
```

---

# 70. AI Rejection Flow

```text
AI Result
    │
    ▼
Citizen Rejects
    │
    ▼
Write Manually
    │
    ▼
Final Complaint
```

AI rejection must not prevent complaint submission.

---

# 71. AI and Department Routing

AI may suggest:

```text
Road Damage
```

But department routing remains backend-controlled:

```text
AI Suggested Category
        │
        ▼
Backend Category Validation
        │
        ▼
Configured Category
        │
        ▼
Department Mapping
        │
        ▼
Responsible Department
```

AI does not directly assign the department.

---

# 72. AI and Priority

The AI should not independently determine authoritative complaint priority unless a future requirement explicitly introduces an AI-assisted priority mechanism.

For the initial CivicSnap implementation:

```text
AI
 ↓
Description / Category Suggestion

Backend Business Rules
 ↓
Priority
```

---

# 73. AI and Complaint Verification

AI output does not verify a complaint.

Correct:

```text
AI Suggestion
     ↓
Citizen
     ↓
Submit
     ↓
Department Admin
     ↓
Verify
```

---

# 74. AI and Resolution

AI may potentially assist with future resolution analysis, but the initial AI API does not approve worker resolutions.

Correct:

```text
Worker
   ↓
Resolution
   ↓
Department Admin
   ↓
Approve / Reject
```

---

# 75. AI Monitoring

System administrators may monitor:

```text
AI Request Count
AI Success Rate
AI Failure Rate
Average Response Time
Provider Availability
Model Usage
```

These metrics are intended for system administration and operational monitoring.

---

# 76. AI Observability

The backend should record operational metrics such as:

```text
request count
latency
failure count
timeout count
provider errors
```

Sensitive complaint contents should not be unnecessarily written into application logs.

---

# 77. AI Logging Rules

Logs must not contain:

```text
AI API Key
JWT Token
Password
Raw sensitive image data
Unnecessary personal information
```

Safe logging may include:

```text
analysisId
complaintId
request timestamp
model version
processing time
result status
```

---

# 78. AI Privacy Principle

CivicSnap follows:

```text
Minimum Data
      +
Purpose Limitation
      +
Secure Processing
      =
Responsible AI Integration
```

Only data necessary for the requested AI function should be processed.

---

# 79. AI API Versioning

Current API version:

```text
/v1
```

Example:

```text
/api/v1/complaints/{id}/ai-description
```

Future breaking changes may use:

```text
/v2
```

rather than silently changing existing behavior.

---

# 80. Complete AI Workflow

```text
                     CITIZEN
                        │
                        ▼
                Create Complaint
                        │
                        ▼
                 Upload Evidence
                        │
                        ▼
                   AI Assist
                        │
                        ▼
            POST /ai-description
                        │
                        ▼
               Authenticate User
                        │
                        ▼
             Validate Ownership
                        │
                        ▼
               Retrieve Evidence
                        │
                        ▼
                 AI Service Layer
                        │
                        ▼
               External AI Provider
                        │
                        ▼
                 Analyze Image
                        │
              ┌─────────┴─────────┐
              ▼                   ▼
       Description             Category
       Suggestion              Suggestion
              │                   │
              └─────────┬─────────┘
                        ▼
                Validate AI Result
                        │
                        ▼
                  Citizen Review
                   ┌────┼────┐
                   ▼    ▼    ▼
                Accept Edit Reject
                   │    │    │
                   └────┼────┘
                        ▼
                 Final Complaint
                        │
                        ▼
                    Submit
                        │
                        ▼
                Department Review
```

---

# 81. Complete AI API Endpoint Summary

| Method | Endpoint                          | Purpose                                       |
| ------ | --------------------------------- | --------------------------------------------- |
| POST   | `/complaints/{id}/ai-description` | Generate description and category suggestions |
| POST   | `/ai/analyze-image`               | Analyze authorized complaint image            |
| POST   | `/ai/classify-complaint`          | Suggest complaint category                    |
| GET    | `/complaints/{id}/ai-results`     | Retrieve AI analysis results                  |

---

# 82. Final AI Architecture

```text
┌───────────────────────────────────────────────────────┐
│                    CIVICSNAP                         │
│                                                       │
│                 Citizen App                          │
│                Flutter + Dart                        │
└─────────────────────────┬─────────────────────────────┘
                          │
                          │ HTTPS + JWT
                          ▼
┌───────────────────────────────────────────────────────┐
│                 SPRING BOOT BACKEND                  │
│                                                       │
│                     AI API                           │
│                                                       │
│ Authentication → Authorization → Validation          │
└─────────────────────────┬─────────────────────────────┘
                          │
                          ▼
                  ┌───────────────┐
                  │   AI Service  │
                  └───────┬───────┘
                          │
                          ▼
                  ┌───────────────┐
                  │ AI Provider   │
                  │ External API  │
                  └───────┬───────┘
                          │
                          ▼
                  Structured Result
                          │
                          ▼
                  Response Validation
                          │
                          ▼
                    Citizen App
                          │
                  ┌───────┴────────┐
                  ▼                ▼
               Accept            Edit
                  │                │
                  └───────┬────────┘
                          ▼
                    Final Complaint
```

---

# 83. Final AI Principles

The CivicSnap AI API shall follow these principles:

1. **AI is an assistant, not an authority.**
2. **All AI requests pass through the Spring Boot backend.**
3. **External AI credentials remain server-side.**
4. **Citizens can review and edit AI suggestions.**
5. **AI cannot independently verify complaints.**
6. **AI cannot independently assign workers.**
7. **AI cannot independently approve resolutions.**
8. **AI categories must map to configured CivicSnap categories.**
9. **Complaint ownership must be validated before processing.**
10. **Images must be securely retrieved and validated.**
11. **AI failures must not block normal complaint submission.**
12. **AI responses must be validated before reaching clients.**
13. **AI requests should be rate-limited.**
14. **Sensitive credentials and raw private data must not appear in logs.**
15. **AI operations should be auditable where required.**
16. **The external AI provider must remain replaceable through an abstraction layer.**
17. **AI processing should follow data-minimization and privacy principles.**

---

# 84. Final Summary

The CivicSnap AI API provides intelligent assistance during complaint creation:

```text
Complaint Evidence
       ↓
      AI
       ↓
Description Suggestion
       +
Category Suggestion
       ↓
Citizen Review
       ↓
Accept / Edit / Reject
       ↓
Final Complaint
       ↓
Normal CivicSnap Workflow
```

The AI layer enhances the citizen experience without replacing CivicSnap's core governance workflow.

The **Spring Boot backend remains the authoritative layer**, while the external AI service functions only as a controlled supporting component.
