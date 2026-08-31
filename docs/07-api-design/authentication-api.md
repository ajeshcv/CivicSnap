# CivicSnap — Authentication API

## 1. Introduction

The CivicSnap Authentication API manages user identity, authentication, authorization-related information, and secure access to the CivicSnap platform.

The authentication system supports four primary roles:

```text
CITIZEN
WORKER
DEPARTMENT_ADMIN
SYSTEM_ADMIN
```

Authentication is handled by the **Java Spring Boot backend** using **Spring Security** and **JWT-based authentication**.

The mobile applications are:

* Citizen App — Flutter + Dart
* Worker App — Flutter + Dart

The administrative interface is:

* Admin Portal — React + TypeScript

All authenticated clients communicate with the backend through HTTPS REST APIs.

---

# 2. Authentication Architecture

```text
                 CIVICSNAP CLIENTS
                        │
        ┌───────────────┼────────────────┐
        │               │                │
        ▼               ▼                ▼
   Citizen App      Worker App      Admin Portal
    Flutter          Flutter        React + TS
        │               │                │
        └───────────────┼────────────────┘
                        │
                     HTTPS
                        │
                        ▼
              Authentication API
                        │
                        ▼
               Spring Security
                        │
                ┌───────┴────────┐
                │                │
                ▼                ▼
          User Repository    JWT Service
                │                │
                ▼                ▼
            PostgreSQL       Access Token
```

---

# 3. Authentication Objectives

The authentication system shall:

1. Allow users to securely register.
2. Authenticate users using valid credentials.
3. Generate secure access tokens.
4. Identify the user's role.
5. Protect authenticated APIs.
6. Enforce role-based access.
7. Support token expiration.
8. Support logout/session invalidation where applicable.
9. Protect user credentials.
10. Prevent unauthorized access.
11. Support the Citizen App.
12. Support the Worker App.
13. Support the Admin Portal.

---

# 4. Authentication Technologies

| Component             | Technology                       |
| --------------------- | -------------------------------- |
| Backend               | Java                             |
| Framework             | Spring Boot                      |
| Security              | Spring Security                  |
| Authentication        | JWT                              |
| Password Hashing      | BCrypt / secure password encoder |
| Database              | PostgreSQL                       |
| API                   | REST                             |
| Transport             | HTTPS                            |
| Mobile Secure Storage | Flutter Secure Storage           |
| API Documentation     | OpenAPI / Swagger                |

---

# 5. Supported Roles

The authentication system recognizes four primary roles.

| Role             | Client       | Scope                      |
| ---------------- | ------------ | -------------------------- |
| CITIZEN          | Citizen App  | Own complaints             |
| WORKER           | Worker App   | Assigned departmental work |
| DEPARTMENT_ADMIN | Admin Portal | Own department             |
| SYSTEM_ADMIN     | Admin Portal | System-wide administration |

---

# 6. Authentication Flow

```text
User
 │
 ▼
Enter Email + Password
 │
 ▼
POST /api/v1/auth/login
 │
 ▼
Spring Security
 │
 ▼
Find User
 │
 ▼
Verify Password Hash
 │
 ▼
Check Account Status
 │
 ▼
Load Role + Department
 │
 ▼
Generate JWT
 │
 ▼
Return Token
 │
 ▼
Client Secure Storage
```

---

# 7. Registration API

## Endpoint

```http
POST /api/v1/auth/register
```

## Access

Public.

## Purpose

Creates a new CivicSnap citizen account.

Normal public registration shall create a user with:

```text
role = CITIZEN
```

Administrative and worker accounts should normally be created or managed by authorized administrators rather than through public registration.

---

# 8. Registration Request

```json
{
  "fullName": "John Doe",
  "email": "john@example.com",
  "phone": "9876543210",
  "password": "StrongPassword123"
}
```

---

# 9. Registration Request Fields

| Field    | Type   | Required | Description          |
| -------- | ------ | -------: | -------------------- |
| fullName | String |      Yes | User's full name     |
| email    | String |      Yes | Unique email address |
| phone    | String |      Yes | Contact number       |
| password | String |      Yes | User password        |

---

# 10. Registration Validation

The backend shall validate:

* Full name is not empty.
* Email is valid.
* Email is unique.
* Phone number is valid.
* Password meets security requirements.
* User does not already exist.

---

# 11. Registration Process

```text
Registration Request
        │
        ▼
Validate Input
        │
        ▼
Check Existing Email
        │
        ├──── Exists ────► Reject
        │
        ▼
Hash Password
        │
        ▼
Create User
        │
        ▼
Assign CITIZEN Role
        │
        ▼
Store User
        │
        ▼
Return Success
```

---

# 12. Registration Response

```json
{
  "success": true,
  "message": "Registration successful"
}
```

The API should not return the user's password or password hash.

---

# 13. Duplicate Registration

If the email already exists:

```http
HTTP 409 Conflict
```

Example:

```json
{
  "success": false,
  "error": {
    "code": "EMAIL_ALREADY_EXISTS",
    "message": "An account with this email already exists."
  }
}
```

---

# 14. Login API

## Endpoint

```http
POST /api/v1/auth/login
```

## Access

Public.

## Purpose

Authenticates a registered user and issues authentication credentials.

---

# 15. Login Request

```json
{
  "email": "john@example.com",
  "password": "StrongPassword123"
}
```

---

# 16. Login Process

```text
Login Request
      │
      ▼
Validate Request
      │
      ▼
Find User
      │
      ├──── Not Found ───► Authentication Failed
      │
      ▼
Check Account Status
      │
      ├──── Inactive ───► Reject
      │
      ▼
Compare Password Hash
      │
      ├──── Invalid ────► Reject
      │
      ▼
Load User Role
      │
      ▼
Load Department
      │
      ▼
Generate JWT
      │
      ▼
Return Authentication Response
```

---

# 17. Login Response

```json
{
  "success": true,
  "data": {
    "accessToken": "<jwt-token>",
    "tokenType": "Bearer",
    "expiresIn": 3600,
    "user": {
      "id": "USER-101",
      "fullName": "John Doe",
      "email": "john@example.com",
      "role": "CITIZEN",
      "departmentId": null
    }
  },
  "message": "Login successful"
}
```

---

# 18. JWT Access Token

The JWT shall contain sufficient information to identify the authenticated user.

A conceptual payload may contain:

```json
{
  "sub": "USER-101",
  "role": "CITIZEN",
  "departmentId": null,
  "iat": 1788091200,
  "exp": 1788094800
}
```

The exact claims may vary during implementation.

---

# 19. JWT Claims

Recommended claims:

| Claim        | Purpose                           |
| ------------ | --------------------------------- |
| sub          | User identifier                   |
| role         | User role                         |
| departmentId | Department scope where applicable |
| iat          | Token issue time                  |
| exp          | Token expiration                  |

Sensitive information should not be placed inside JWT claims unnecessarily.

---

# 20. Access Token Usage

Authenticated API requests shall include:

```http
Authorization: Bearer <access-token>
```

Example:

```http
GET /api/v1/complaints/my
Authorization: Bearer eyJhbGciOi...
```

---

# 21. JWT Validation

For every protected API request:

```text
Request
  │
  ▼
Extract Authorization Header
  │
  ▼
Extract JWT
  │
  ▼
Validate Signature
  │
  ▼
Check Expiration
  │
  ▼
Extract User Identity
  │
  ▼
Load/Validate Security Context
  │
  ▼
Authorize Request
  │
  ▼
Controller
```

---

# 22. Token Expiration

Access tokens shall have a limited lifetime.

Example:

```text
Access Token Lifetime
       ↓
60 minutes
```

The actual duration shall be configurable.

Expired tokens must not be accepted.

---

# 23. Refresh Token

If refresh-token support is implemented:

```http
POST /api/v1/auth/refresh
```

The refresh token shall be used to obtain a new access token without requiring the user to enter credentials again.

---

# 24. Refresh Token Flow

```text
Access Token Expired
        │
        ▼
Client Sends Refresh Token
        │
        ▼
Backend Validates Refresh Token
        │
        ▼
Generate New Access Token
        │
        ▼
Return New Token
```

Refresh tokens should be stored securely.

For mobile applications, secure device storage should be used.

---

# 25. Refresh Token Request

```json
{
  "refreshToken": "<refresh-token>"
}
```

---

# 26. Refresh Token Response

```json
{
  "success": true,
  "data": {
    "accessToken": "<new-access-token>",
    "tokenType": "Bearer",
    "expiresIn": 3600
  }
}
```

---

# 27. Logout API

## Endpoint

```http
POST /api/v1/auth/logout
```

## Access

Authenticated users.

Logout behavior depends on the token strategy.

If refresh tokens are used, the backend should invalidate the corresponding refresh token/session.

---

# 28. Logout Request

The authenticated access token identifies the current user.

If refresh-token revocation is supported:

```json
{
  "refreshToken": "<refresh-token>"
}
```

---

# 29. Logout Response

```json
{
  "success": true,
  "message": "Logout successful"
}
```

---

# 30. Current User API

## Endpoint

```http
GET /api/v1/users/me
```

## Access

Authenticated users.

## Purpose

Returns the profile and authorization information of the current user.

---

# 31. Current User Response

```json
{
  "success": true,
  "data": {
    "id": "USER-101",
    "fullName": "John Doe",
    "email": "john@example.com",
    "phone": "9876543210",
    "role": "CITIZEN",
    "departmentId": null,
    "isActive": true
  }
}
```

---

# 32. Update Profile API

## Endpoint

```http
PATCH /api/v1/users/me
```

## Access

Authenticated users.

Users may update permitted profile information.

Example:

```json
{
  "fullName": "John Updated",
  "phone": "9876543211"
}
```

Role and department assignments shall not be freely modified by users.

---

# 33. Password Security

Passwords shall never be stored as plain text.

The process shall be:

```text
Plain Password
      │
      ▼
Password Encoder
      │
      ▼
Secure Password Hash
      │
      ▼
PostgreSQL
```

Spring Security's configured password encoder should be used.

---

# 34. Password Verification

During login:

```text
Entered Password
       │
       ▼
Password Encoder
       │
       ▼
Compare Against Stored Hash
       │
       ├── Match ──► Authentication Successful
       │
       └── No Match ► Authentication Failed
```

The original password shall never be recovered from the stored hash.

---

# 35. Account Status

Each user should have an account status.

Minimum implementation:

```text
ACTIVE
INACTIVE
```

Only active accounts may authenticate.

---

# 36. Inactive Account Login

If an inactive account attempts to log in:

```http
HTTP 401 Unauthorized
```

or an appropriate authentication failure response shall be returned.

The API should avoid revealing unnecessary account-status information to unauthenticated attackers.

---

# 37. Role-Based Authorization

Authentication establishes identity.

Authorization determines what the user can access.

```text
Authentication
       ↓
Who are you?
       ↓
Authorization
       ↓
What are you allowed to do?
```

---

# 38. Citizen Authorization

Citizen APIs include:

```text
GET    /complaints/my
POST   /complaints
POST   /complaints/{id}/submit
POST   /complaints/{id}/images
POST   /complaints/{id}/feedback
GET    /notifications
```

A citizen must not access another citizen's complaint.

---

# 39. Worker Authorization

Worker APIs include:

```text
GET    /worker/dashboard
GET    /worker/complaints
GET    /worker/complaints/{id}
POST   /worker/assignments/{id}/accept
POST   /worker/complaints/{id}/start
POST   /worker/complaints/{id}/resolution
POST   /resolutions/{id}/images
```

Workers must only access complaints assigned to them.

---

# 40. Department Admin Authorization

Department Administrators may access:

```text
/admin/complaints
/admin/workers
/admin/complaints/{id}/verify
/admin/complaints/{id}/reject
/admin/complaints/{id}/assign
/admin/complaints/{id}/reassign
/admin/resolutions/pending
/admin/resolutions/{id}/approve
/admin/resolutions/{id}/reject
/admin/reports/*
```

Department scope must be enforced.

---

# 41. System Admin Authorization

System Administrators may access:

```text
/system/users
/system/departments
/system/categories
/system/reports
/system/audit-logs
```

They may have system-wide administrative access.

---

# 42. Department Scope

For Department Administrators:

```text
Authenticated Admin
        │
        ▼
Admin Department ID
        │
        =
        │
Requested Resource Department ID
```

If the department does not match:

```http
403 Forbidden
```

---

# 43. Worker Scope

For workers:

```text
Authenticated Worker ID
        │
        =
        │
Assignment Worker ID
```

If the worker is not assigned to the complaint:

```http
403 Forbidden
```

---

# 44. Citizen Scope

For citizens:

```text
Authenticated User ID
        │
        =
        │
Complaint Citizen ID
```

If the complaint belongs to another citizen:

```http
403 Forbidden
```

---

# 45. Spring Security Architecture

The backend may follow:

```text
HTTP Request
     │
     ▼
Security Filter Chain
     │
     ▼
JWT Authentication Filter
     │
     ▼
Validate Token
     │
     ▼
SecurityContext
     │
     ▼
Authorization
     │
     ▼
Controller
```

---

# 46. JWT Authentication Filter

The JWT authentication filter shall:

1. Read the Authorization header.
2. Extract the bearer token.
3. Validate the token.
4. Extract user identity.
5. Build the authentication object.
6. Set the Spring Security context.

Invalid tokens shall not be accepted.

---

# 47. Public Endpoints

The following endpoints may be public:

```text
POST /api/v1/auth/register
POST /api/v1/auth/login
POST /api/v1/auth/refresh
```

Additional public endpoints should be explicitly reviewed before being permitted.

---

# 48. Protected Endpoints

All application-management APIs shall require authentication.

Examples:

```text
/api/v1/complaints/**
/api/v1/worker/**
/api/v1/admin/**
/api/v1/system/**
/api/v1/notifications/**
/api/v1/feedback/**
```

---

# 49. Unauthorized Request

If no valid authentication is provided:

```http
401 Unauthorized
```

Example:

```json
{
  "success": false,
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Authentication is required."
  }
}
```

---

# 50. Forbidden Request

If the user is authenticated but lacks permission:

```http
403 Forbidden
```

Example:

```json
{
  "success": false,
  "error": {
    "code": "FORBIDDEN",
    "message": "You do not have permission to access this resource."
  }
}
```

---

# 51. Invalid Token

If the JWT is invalid:

```http
401 Unauthorized
```

Example:

```json
{
  "success": false,
  "error": {
    "code": "INVALID_TOKEN",
    "message": "Authentication token is invalid."
  }
}
```

---

# 52. Expired Token

If the access token has expired:

```http
401 Unauthorized
```

The client may use the refresh-token flow if implemented.

---

# 53. Invalid Credentials

Invalid login credentials shall result in authentication failure.

Example:

```json
{
  "success": false,
  "error": {
    "code": "INVALID_CREDENTIALS",
    "message": "Invalid email or password."
  }
}
```

The response should avoid indicating whether the email or password specifically was incorrect.

---

# 54. Authentication Rate Limiting

The following endpoints should be protected against excessive requests:

```text
/auth/login
/auth/register
/auth/refresh
```

Possible strategy:

```text
Multiple Failed Attempts
        ↓
Rate Limit
        ↓
Temporary Delay / Rejection
```

The exact limits shall be configured based on deployment requirements.

---

# 55. Brute-Force Protection

The system should implement protections against repeated login attempts.

Possible mechanisms include:

* Rate limiting
* Progressive delays
* Account security monitoring
* IP-based throttling
* Device/session monitoring

---

# 56. Password Requirements

Passwords should meet minimum security requirements.

Recommended requirements:

```text
Minimum length
Uppercase character
Lowercase character
Number
Special character
```

The exact password policy shall be configurable.

---

# 57. Credential Storage

The following must never be stored directly:

```text
Plain Password
JWT Secret in Database
AI API Key
Database Password
FCM Credentials
```

Secrets should be managed through secure environment configuration or a secret-management system.

---

# 58. Mobile Token Storage

The Flutter applications should not store JWT tokens in ordinary unsecured preferences.

Recommended:

```text
Flutter App
    │
    ▼
Secure Storage
    │
    ▼
Access / Refresh Token
```

A secure storage package such as Flutter Secure Storage may be used.

---

# 59. Admin Authentication

Department Administrators and System Administrators shall authenticate through the Admin Portal.

```text
Admin Portal
     │
     ▼
/auth/login
     │
     ▼
Spring Security
     │
     ▼
JWT
     │
     ▼
Admin Portal
```

The backend determines the user's actual role.

The frontend must not be trusted to assign administrative privileges.

---

# 60. Worker Authentication

Workers authenticate through the separate Flutter Worker App.

```text
Worker App
     │
     ▼
/auth/login
     │
     ▼
Backend
     │
     ▼
WORKER Role
     │
     ▼
Worker Dashboard
```

After authentication, the Worker App should load only the worker's authorized departmental assignments.

---

# 61. Citizen Authentication

Citizens authenticate through the Flutter Citizen App.

```text
Citizen App
     │
     ▼
/auth/login
     │
     ▼
Backend
     │
     ▼
CITIZEN Role
     │
     ▼
Citizen Dashboard
```

---

# 62. Authentication State in Flutter

The Flutter applications should maintain an authentication state such as:

```text
INITIALIZING
    ↓
CHECKING_SESSION
    ↓
┌──────────────┬───────────────┐
│              │               │
▼              ▼               ▼
AUTHENTICATED  UNAUTHENTICATED EXPIRED
```

The application should redirect users appropriately.

---

# 63. Token Expiration Handling in Flutter

When a protected API returns:

```http
401 Unauthorized
```

the mobile client may:

```text
Check Refresh Token
       │
       ▼
Refresh Access Token
       │
       ├── Success → Retry Request
       │
       └── Failure → Logout
```

The retry mechanism should avoid infinite refresh loops.

---

# 64. Token Revocation

If refresh tokens are implemented, they should be revocable.

Possible reasons:

```text
Logout
Password Change
Account Deactivation
Security Incident
Refresh Token Rotation
```

---

# 65. Password Change API

## Endpoint

```http
PATCH /api/v1/auth/password
```

## Access

Authenticated users.

### Request

```json
{
  "currentPassword": "OldPassword123",
  "newPassword": "NewPassword456!"
}
```

The backend shall:

1. Validate current password.
2. Validate new password.
3. Hash the new password.
4. Update the password.
5. Invalidate relevant sessions/tokens where appropriate.

---

# 66. Password Reset

A future password-reset flow may use:

```text
POST /api/v1/auth/forgot-password
POST /api/v1/auth/reset-password
```

The reset process should use a secure, short-lived reset token.

The reset token must not expose the user's password.

---

# 67. Email Verification

Email verification may be introduced if required.

Possible endpoints:

```text
POST /api/v1/auth/verify-email
POST /api/v1/auth/resend-verification
```

This is optional for the initial implementation unless required by the project.

---

# 68. Session Management

If refresh-token sessions are implemented, the system may maintain:

```text
User
 │
 ├── Device / Session 1
 ├── Device / Session 2
 └── Device / Session 3
```

This allows individual sessions to be revoked.

---

# 69. Authentication Audit Events

Important authentication events should be logged.

Examples:

```text
LOGIN_SUCCESS
LOGIN_FAILURE
LOGOUT
PASSWORD_CHANGED
PASSWORD_RESET
ACCOUNT_DEACTIVATED
TOKEN_REFRESHED
```

Authentication logs should not contain passwords or raw tokens.

---

# 70. Security Logging

Logs may contain:

```text
Timestamp
User ID
Event
IP Address
Device information where appropriate
Result
```

Sensitive credentials and tokens must be excluded.

---

# 71. Authentication Database Structure

Authentication primarily uses:

```text
users
roles
departments
```

Optional session-related tables:

```text
refresh_tokens
user_devices
sessions
```

---

# 72. User Authentication Data

Conceptual user record:

```text
users
│
├── id
├── full_name
├── email
├── phone
├── password_hash
├── role_id
├── department_id
├── is_active
├── created_at
└── updated_at
```

---

# 73. Role Relationship

```text
ROLE
 │
 └────< USER
```

A user references one primary role.

Example:

```text
USER-101
   │
   └── CITIZEN
```

---

# 74. Department Relationship

Workers and Department Administrators may reference a department.

```text
USER
 │
 └── department_id
          │
          ▼
     DEPARTMENT
```

System Administrators may have no department association.

---

# 75. Authentication API Error Codes

Recommended error codes:

```text
INVALID_CREDENTIALS
EMAIL_ALREADY_EXISTS
ACCOUNT_INACTIVE
UNAUTHORIZED
FORBIDDEN
INVALID_TOKEN
EXPIRED_TOKEN
INVALID_REFRESH_TOKEN
VALIDATION_ERROR
PASSWORD_POLICY_VIOLATION
RATE_LIMIT_EXCEEDED
```

---

# 76. Authentication API Endpoint Summary

| Method | Endpoint                | Access                      | Purpose              |
| ------ | ----------------------- | --------------------------- | -------------------- |
| POST   | `/auth/register`        | Public                      | Register citizen     |
| POST   | `/auth/login`           | Public                      | Login                |
| POST   | `/auth/refresh`         | Authenticated/Refresh Token | Refresh access token |
| POST   | `/auth/logout`          | Authenticated               | Logout               |
| GET    | `/users/me`             | Authenticated               | Current user         |
| PATCH  | `/users/me`             | Authenticated               | Update profile       |
| PATCH  | `/auth/password`        | Authenticated               | Change password      |
| POST   | `/auth/forgot-password` | Public                      | Request reset        |
| POST   | `/auth/reset-password`  | Public + Reset Token        | Reset password       |

---

# 77. Authentication Sequence — Citizen

```text
Citizen
   │
   ▼
Open Citizen App
   │
   ▼
Enter Credentials
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
Generate JWT
   │
   ▼
Return Token
   │
   ▼
Secure Storage
   │
   ▼
Citizen Dashboard
```

---

# 78. Authentication Sequence — Worker

```text
Worker
   │
   ▼
Open Worker App
   │
   ▼
Login
   │
   ▼
POST /auth/login
   │
   ▼
Validate Credentials
   │
   ▼
Check WORKER Role
   │
   ▼
Generate JWT
   │
   ▼
Worker App
   │
   ▼
Worker Dashboard
```

The dashboard shall show only the worker's authorized departmental work.

---

# 79. Authentication Sequence — Department Admin

```text
Department Admin
       │
       ▼
Admin Portal
       │
       ▼
POST /auth/login
       │
       ▼
Spring Security
       │
       ▼
Validate Role
       │
       ▼
DEPARTMENT_ADMIN
       │
       ▼
Load Department Scope
       │
       ▼
JWT
       │
       ▼
Department Dashboard
```

---

# 80. Authentication Sequence — System Admin

```text
System Admin
       │
       ▼
Admin Portal
       │
       ▼
POST /auth/login
       │
       ▼
Spring Security
       │
       ▼
Validate Role
       │
       ▼
SYSTEM_ADMIN
       │
       ▼
JWT
       │
       ▼
System Dashboard
```

---

# 81. Complete Authentication Flow

```text
                    ┌──────────────┐
                    │     User     │
                    └──────┬───────┘
                           │
                           ▼
                    Enter Credentials
                           │
                           ▼
                 POST /auth/login
                           │
                           ▼
                ┌────────────────────┐
                │   Spring Security  │
                └─────────┬──────────┘
                          │
                    Validate User
                          │
             ┌────────────┴────────────┐
             │                         │
          Invalid                    Valid
             │                         │
             ▼                         ▼
          401 Error             Load Role/Dept
                                       │
                                       ▼
                                  Generate JWT
                                       │
                                       ▼
                                  Return Token
                                       │
                                       ▼
                              Client Secure Storage
                                       │
                                       ▼
                              Authenticated Requests
                                       │
                                       ▼
                              JWT Validation Filter
                                       │
                                       ▼
                                  Authorization
                                       │
                                       ▼
                                  API Resource
```

---

# 82. Security Principles

The authentication system shall follow these principles:

1. Passwords are never stored in plain text.
2. JWT secrets must remain private.
3. Access tokens must expire.
4. Protected APIs require authentication.
5. Roles must be determined by the backend.
6. Department scope must be enforced server-side.
7. Worker assignment scope must be enforced server-side.
8. Citizen ownership must be enforced server-side.
9. Authentication failures must not reveal sensitive information.
10. Authentication events should be auditable.
11. Tokens must be stored securely on mobile devices.
12. HTTPS must be used in production.
13. Rate limiting should protect authentication endpoints.
14. Deactivated users must not authenticate.
15. External clients must never directly access the database.

---

# 83. Final Authentication Architecture

```text
┌────────────────────────────────────────────────────────┐
│                    CIVICSNAP CLIENTS                   │
│                                                        │
│ Citizen App       Worker App       Admin Portal        │
│ Flutter/Dart      Flutter/Dart     React/TypeScript    │
└─────────────────────────┬──────────────────────────────┘
                          │
                         HTTPS
                          │
                          ▼
              ┌──────────────────────────┐
              │     Authentication API   │
              │      Spring Boot         │
              └────────────┬─────────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │ Spring Security │
                  └────────┬────────┘
                           │
                  ┌────────┴────────┐
                  │                 │
                  ▼                 ▼
             Password Hash       JWT Service
                  │                 │
                  ▼                 ▼
             PostgreSQL        Access Token
                  │                 │
                  └────────┬────────┘
                           ▼
                     Authorization
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
          Citizen        Worker         Admin
           Scope          Scope          Scope
```

---

# 84. Final Summary

The CivicSnap Authentication API provides a centralized and secure authentication mechanism for all three client applications:

```text
Flutter Citizen App
        │
Flutter Worker App
        │
React Admin Portal
        │
        ▼
Spring Boot Authentication API
        │
        ▼
Spring Security + JWT
        │
        ▼
PostgreSQL
```

The system supports:

* Citizen registration and login
* Worker authentication
* Department Administrator authentication
* System Administrator authentication
* JWT access tokens
* Optional refresh tokens
* Secure password hashing
* Account activation/deactivation
* Role-based authorization
* Department-level authorization
* Worker-level assignment authorization
* Citizen-level ownership authorization
* Secure mobile token storage
* Authentication auditing
* Password management
* Rate limiting and brute-force protection

The **backend remains the authoritative source for authentication and authorization**. The Flutter and React clients may hide or display features based on roles, but all security decisions must ultimately be enforced by the **Spring Boot backend**.
