# CivicSnap — Database Overview

## 1. Introduction

The CivicSnap database is responsible for storing and managing the structured information required for the civic complaint management system.

The database supports the complete complaint lifecycle:

```text
Citizen
   ↓
Complaint
   ↓
Department
   ↓
Worker Assignment
   ↓
Field Work
   ↓
Resolution
   ↓
Department Verification
   ↓
Completion
   ↓
Citizen Feedback
```

The database also maintains:

* User accounts
* Roles
* Departments
* Complaint categories
* Complaint locations
* Complaint images
* Worker assignments
* SLA information
* Resolution evidence
* Notifications
* Feedback
* Audit history

PostgreSQL is selected as the primary relational database.

---

# 2. Database Technology

| Component                  | Technology                  |
| -------------------------- | --------------------------- |
| Database Management System | PostgreSQL                  |
| Database Type              | Relational                  |
| ORM                        | Hibernate                   |
| Persistence Framework      | Spring Data JPA             |
| Backend                    | Java Spring Boot            |
| Query Language             | SQL                         |
| API Data Format            | JSON                        |
| Database Migration         | Flyway or Liquibase         |
| Development Tool           | pgAdmin / PostgreSQL Client |

---

# 3. Database Design Goals

The database shall be designed to provide:

1. Data consistency.
2. Referential integrity.
3. Secure access.
4. Efficient complaint retrieval.
5. Proper separation of user roles.
6. Department-level data isolation.
7. Complete complaint history.
8. Assignment traceability.
9. Resolution verification tracking.
10. SLA monitoring.
11. Auditability.
12. Future scalability.

---

# 4. High-Level Database Architecture

```text
                         CIVICSNAP DATABASE
                                │
       ┌────────────────────────┼────────────────────────┐
       │                        │                        │
       ▼                        ▼                        ▼
   USER DOMAIN            COMPLAINT DOMAIN        ADMIN DOMAIN
       │                        │                        │
       ├── User                ├── Complaint            ├── Department
       ├── Role                ├── Category             ├── Worker
       └── Profile             ├── Images               └── Admin
                               ├── Location
                               ├── Assignment
                               ├── Resolution
                               └── Feedback
                                        │
                    ┌───────────────────┼───────────────────┐
                    ▼                   ▼                   ▼
                   SLA             Notification          Audit
```

---

# 5. Major Database Domains

The CivicSnap database can be divided into six major domains.

| Domain         | Main Entities                                 |
| -------------- | --------------------------------------------- |
| Identity       | User, Role                                    |
| Organization   | Department, Worker                            |
| Complaint      | Complaint, Category, ComplaintImage, Location |
| Operations     | Assignment, SLA, Resolution, ResolutionImage  |
| Communication  | Notification, Feedback                        |
| Administration | AuditLog                                      |

---

# 6. Entity Overview

The primary entities are:

```text
User
Role
Department
Category
Complaint
ComplaintImage
Location
Assignment
SLA
Resolution
ResolutionImage
Notification
Feedback
AuditLog
```

---

# 7. Entity Relationship Overview

```text
                         ┌──────────────┐
                         │     Role     │
                         └──────┬───────┘
                                │
                                │
                         ┌──────▼───────┐
                         │     User     │
                         └──────┬───────┘
                                │
                ┌───────────────┼────────────────┐
                │               │                │
                ▼               ▼                ▼
             Citizen          Worker            Admin
                                │
                                ▼
                         ┌──────────────┐
                         │  Department  │
                         └──────┬───────┘
                                │
                                ▼
                         ┌──────────────┐
                         │   Category   │
                         └──────┬───────┘
                                │
                                ▼
                         ┌──────────────┐
                         │   Complaint  │
                         └──────┬───────┘
                                │
             ┌──────────────────┼───────────────────┐
             │                  │                   │
             ▼                  ▼                   ▼
       ComplaintImage        Location           Assignment
                                                   │
                                                   ▼
                                               Worker
                                                   │
                                                   ▼
                                             Resolution
                                                   │
                                                   ▼
                                         ResolutionImage

Complaint
   │
   ├── SLA
   ├── Feedback
   ├── Notification
   └── AuditLog
```

---

# 8. User Table

The `users` table stores the common account information for all CivicSnap users.

## Main Fields

| Field         | Type             | Description                            |
| ------------- | ---------------- | -------------------------------------- |
| id            | UUID / BIGSERIAL | Primary key                            |
| full_name     | VARCHAR          | User's name                            |
| email         | VARCHAR          | Unique email                           |
| phone         | VARCHAR          | Phone number                           |
| password_hash | VARCHAR          | Secure password hash                   |
| role_id       | FK               | User role                              |
| department_id | FK               | Associated department where applicable |
| is_active     | BOOLEAN          | Account status                         |
| created_at    | TIMESTAMP        | Account creation                       |
| updated_at    | TIMESTAMP        | Last update                            |

---

# 9. Role Table

The `roles` table defines the roles available in CivicSnap.

## Roles

```text
CITIZEN
WORKER
DEPARTMENT_ADMIN
SYSTEM_ADMIN
```

## Fields

| Field       | Type    | Description      |
| ----------- | ------- | ---------------- |
| id          | PK      | Role identifier  |
| name        | VARCHAR | Role name        |
| description | TEXT    | Role description |

---

# 10. User-Role Relationship

Each user shall have an assigned role.

```text
Role
  │
  └──< User
```

A role determines the broad permissions available to the user.

Detailed resource-level authorization shall still be enforced by the backend.

---

# 11. Department Table

The `departments` table stores government departments responsible for handling complaints.

Examples:

```text
Road/Public Works
Waste Management
Water Supply
Street Lighting
Drainage
```

## Fields

| Field         | Type      | Description            |
| ------------- | --------- | ---------------------- |
| id            | PK        | Department identifier  |
| name          | VARCHAR   | Department name        |
| description   | TEXT      | Department description |
| contact_email | VARCHAR   | Department email       |
| contact_phone | VARCHAR   | Department contact     |
| is_active     | BOOLEAN   | Department status      |
| created_at    | TIMESTAMP | Creation time          |
| updated_at    | TIMESTAMP | Last update            |

---

# 12. Department Relationships

A department can have:

* Multiple workers
* Multiple department administrators
* Multiple complaint categories
* Multiple complaints

```text
Department
    │
    ├──< Worker
    ├──< Department Admin
    ├──< Category
    └──< Complaint
```

---

# 13. Worker Relationship

A worker is a user with the `WORKER` role and belongs to a department.

```text
User
 │
 └── Worker
       │
       └── Department
```

Workers shall only receive complaints belonging to their authorized department.

---

# 14. Category Table

The `categories` table defines the types of civic complaints.

Examples:

```text
Road Damage
Garbage
Water Leakage
Street Light
Drainage
Public Infrastructure
```

## Fields

| Field             | Type      | Description            |
| ----------------- | --------- | ---------------------- |
| id                | PK        | Category identifier    |
| name              | VARCHAR   | Category name          |
| description       | TEXT      | Category description   |
| department_id     | FK        | Responsible department |
| default_sla_hours | INTEGER   | Default SLA            |
| is_active         | BOOLEAN   | Category status        |
| created_at        | TIMESTAMP | Creation time          |
| updated_at        | TIMESTAMP | Last update            |

---

# 15. Category-Department Relationship

Each complaint category shall be mapped to the responsible department.

```text
Category
    │
    ▼
Department
```

Example:

```text
Road Damage
      ↓
Road/Public Works Department
```

This mapping assists automatic department routing.

---

# 16. Complaint Table

The `complaints` table is the central table of CivicSnap.

It stores the main information about each civic complaint.

## Fields

| Field             | Type         | Description                    |
| ----------------- | ------------ | ------------------------------ |
| id                | UUID         | Primary key                    |
| complaint_number  | VARCHAR      | Human-readable complaint ID    |
| citizen_id        | FK           | Citizen who submitted          |
| category_id       | FK           | Complaint category             |
| department_id     | FK           | Responsible department         |
| title             | VARCHAR      | Complaint title                |
| description       | TEXT         | Final complaint description    |
| ai_description    | TEXT         | AI-generated suggestion        |
| status            | VARCHAR/ENUM | Complaint status               |
| priority          | VARCHAR/ENUM | Complaint priority             |
| latitude          | DECIMAL      | Complaint latitude             |
| longitude         | DECIMAL      | Complaint longitude            |
| location_accuracy | DECIMAL      | GPS accuracy                   |
| submitted_at      | TIMESTAMP    | Submission time                |
| updated_at        | TIMESTAMP    | Last update                    |
| completed_at      | TIMESTAMP    | Completion time                |
| created_at        | TIMESTAMP    | Record creation                |
| deleted_at        | TIMESTAMP    | Optional soft-delete timestamp |

---

# 17. Complaint Status

The complaint status shall represent the lifecycle of a complaint.

Recommended statuses:

```text
DRAFT
SUBMITTED
VERIFIED
REJECTED
ASSIGNED
IN_PROGRESS
RESOLUTION_SUBMITTED
RESOLUTION_REJECTED
COMPLETED
```

The backend shall control valid transitions.

---

# 18. Complaint Status Flow

```text
DRAFT
  │
  ▼
SUBMITTED
  │
  ├──────────────► REJECTED
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
  ├──────────────► RESOLUTION_REJECTED
  │                       │
  │                       ▼
  │                  IN_PROGRESS
  │
  ▼
COMPLETED
```

---

# 19. Complaint Priority

Priority may be represented using:

```text
LOW
MEDIUM
HIGH
CRITICAL
```

Priority rules shall be defined by the application business rules.

---

# 20. Complaint Image Table

The `complaint_images` table stores metadata for images associated with complaints.

The actual image file should preferably be stored in object/file storage.

## Fields

| Field        | Type      | Description            |
| ------------ | --------- | ---------------------- |
| id           | PK        | Image identifier       |
| complaint_id | FK        | Related complaint      |
| storage_key  | VARCHAR   | File storage reference |
| file_name    | VARCHAR   | Original file name     |
| content_type | VARCHAR   | MIME type              |
| file_size    | BIGINT    | File size              |
| uploaded_by  | FK        | User who uploaded      |
| created_at   | TIMESTAMP | Upload time            |

---

# 21. Location Data

Location information may be stored directly in the complaint table for the initial implementation.

Required information:

```text
latitude
longitude
location_accuracy
```

For future expansion, location information may be normalized into a dedicated `locations` table.

---

# 22. Assignment Table

The `assignments` table records worker assignments.

## Fields

| Field           | Type      | Description                       |
| --------------- | --------- | --------------------------------- |
| id              | PK        | Assignment identifier             |
| complaint_id    | FK        | Complaint                         |
| worker_id       | FK        | Assigned worker                   |
| assigned_by     | FK        | Administrator                     |
| assigned_at     | TIMESTAMP | Assignment time                   |
| accepted_at     | TIMESTAMP | Worker acceptance                 |
| completed_at    | TIMESTAMP | Assignment completion             |
| status          | VARCHAR   | Assignment status                 |
| reassigned_from | FK        | Previous assignment if applicable |
| notes           | TEXT      | Assignment notes                  |

---

# 23. Assignment Status

Recommended values:

```text
ASSIGNED
ACCEPTED
IN_PROGRESS
COMPLETED
REASSIGNED
CANCELLED
```

---

# 24. Assignment History

Every reassignment should create a traceable assignment record.

Example:

```text
Complaint #CS-1001

Worker A
   ↓
Assigned
   ↓
Reassigned
   ↓
Worker B
   ↓
Accepted
   ↓
Completed
```

Previous assignments should not simply be overwritten.

---

# 25. SLA Table

The `sla` table stores service-level information associated with complaints.

## Fields

| Field        | Type      | Description             |
| ------------ | --------- | ----------------------- |
| id           | PK        | SLA identifier          |
| complaint_id | FK        | Related complaint       |
| target_hours | INTEGER   | Allowed resolution time |
| started_at   | TIMESTAMP | SLA start               |
| deadline_at  | TIMESTAMP | SLA deadline            |
| completed_at | TIMESTAMP | Completion time         |
| status       | VARCHAR   | SLA state               |
| breached_at  | TIMESTAMP | Breach timestamp        |

---

# 26. SLA Status

Recommended values:

```text
ACTIVE
COMPLETED
WARNING
BREACHED
PAUSED
```

The exact SLA rules shall be controlled by the backend.

---

# 27. Resolution Table

The `resolutions` table stores worker-submitted resolution information.

## Fields

| Field               | Type      | Description              |
| ------------------- | --------- | ------------------------ |
| id                  | PK        | Resolution identifier    |
| complaint_id        | FK        | Related complaint        |
| worker_id           | FK        | Submitting worker        |
| description         | TEXT      | Resolution description   |
| submitted_at        | TIMESTAMP | Submission time          |
| verified_by         | FK        | Department administrator |
| verified_at         | TIMESTAMP | Verification time        |
| verification_status | VARCHAR   | Verification state       |
| rejection_reason    | TEXT      | Reason if rejected       |

---

# 28. Resolution Status

Recommended values:

```text
SUBMITTED
UNDER_REVIEW
APPROVED
REJECTED
```

---

# 29. Resolution Image Table

Resolution evidence images shall be stored separately from complaint images.

## Fields

| Field         | Type      | Description            |
| ------------- | --------- | ---------------------- |
| id            | PK        | Image identifier       |
| resolution_id | FK        | Resolution             |
| storage_key   | VARCHAR   | File storage reference |
| file_name     | VARCHAR   | File name              |
| content_type  | VARCHAR   | MIME type              |
| file_size     | BIGINT    | File size              |
| uploaded_by   | FK        | Worker                 |
| created_at    | TIMESTAMP | Upload time            |

---

# 30. Feedback Table

The `feedback` table stores citizen feedback after complaint completion.

## Fields

| Field        | Type      | Description         |
| ------------ | --------- | ------------------- |
| id           | PK        | Feedback identifier |
| complaint_id | FK        | Related complaint   |
| citizen_id   | FK        | Citizen             |
| rating       | INTEGER   | Rating              |
| comment      | TEXT      | Feedback text       |
| created_at   | TIMESTAMP | Feedback time       |
| updated_at   | TIMESTAMP | Last update         |

---

# 31. Feedback Rules

Feedback shall:

1. Belong to a complaint.
2. Belong to the citizen who submitted that complaint.
3. Normally be allowed only after completion.
4. Not be transferable between citizens.
5. Be available for authorized reporting.

---

# 32. Notification Table

The `notifications` table stores application notifications.

## Fields

| Field        | Type      | Description             |
| ------------ | --------- | ----------------------- |
| id           | PK        | Notification identifier |
| user_id      | FK        | Recipient               |
| complaint_id | FK        | Related complaint       |
| title        | VARCHAR   | Notification title      |
| message      | TEXT      | Notification message    |
| type         | VARCHAR   | Notification type       |
| is_read      | BOOLEAN   | Read state              |
| created_at   | TIMESTAMP | Creation time           |
| read_at      | TIMESTAMP | Read time               |

---

# 33. Notification Types

Possible notification types:

```text
COMPLAINT_SUBMITTED
COMPLAINT_VERIFIED
COMPLAINT_REJECTED
WORKER_ASSIGNED
WORKER_REASSIGNED
WORK_STARTED
RESOLUTION_SUBMITTED
RESOLUTION_REJECTED
RESOLUTION_APPROVED
COMPLAINT_COMPLETED
SLA_WARNING
SLA_BREACH
```

---

# 34. Audit Log Table

The `audit_logs` table records important system operations.

## Fields

| Field       | Type      | Description        |
| ----------- | --------- | ------------------ |
| id          | PK        | Audit identifier   |
| user_id     | FK        | Actor              |
| action      | VARCHAR   | Action performed   |
| entity_type | VARCHAR   | Entity affected    |
| entity_id   | VARCHAR   | Affected entity ID |
| old_value   | JSONB     | Previous state     |
| new_value   | JSONB     | New state          |
| ip_address  | VARCHAR   | Optional source IP |
| created_at  | TIMESTAMP | Action time        |

---

# 35. Audit Examples

The audit system may record:

```text
Complaint Verified
Complaint Rejected
Worker Assigned
Worker Reassigned
Status Changed
Resolution Submitted
Resolution Approved
Resolution Rejected
Department Created
Department Updated
Category Updated
User Deactivated
```

---

# 36. Complete Relationship Model

```text
ROLE
 │
 └────────< USER
               │
       ┌───────┼────────┐
       │       │        │
       ▼       ▼        ▼
   CITIZEN   WORKER    ADMIN
               │        │
               │        ├──── Department Admin
               │        └──── System Admin
               │
               ▼
          DEPARTMENT
               │
               ▼
           CATEGORY
               │
               ▼
          COMPLAINT
          │   │   │
          │   │   ├────< COMPLAINT_IMAGE
          │   │
          │   ├──────── SLA
          │
          ├────────────< ASSIGNMENT >──── WORKER
          │
          ├────────────< RESOLUTION
          │                  │
          │                  └────< RESOLUTION_IMAGE
          │
          ├────────────< FEEDBACK
          │
          ├────────────< NOTIFICATION
          │
          └────────────< AUDIT_LOG
```

---

# 37. Cardinality Matrix

| Relationship                 | Cardinality |
| ---------------------------- | ----------- |
| Role → User                  | 1:N         |
| Department → Worker          | 1:N         |
| Department → Admin           | 1:N         |
| Department → Category        | 1:N         |
| Department → Complaint       | 1:N         |
| Citizen → Complaint          | 1:N         |
| Category → Complaint         | 1:N         |
| Complaint → ComplaintImage   | 1:N         |
| Complaint → Assignment       | 1:N         |
| Worker → Assignment          | 1:N         |
| Complaint → SLA              | 1:1         |
| Complaint → Resolution       | 1:N         |
| Resolution → ResolutionImage | 1:N         |
| Complaint → Feedback         | 1:0..1      |
| Citizen → Feedback           | 1:N         |
| User → Notification          | 1:N         |
| Complaint → Notification     | 1:N         |
| User → AuditLog              | 1:N         |

---

# 38. Foreign Key Relationships

Major foreign keys include:

```text
users.role_id
users.department_id

categories.department_id

complaints.citizen_id
complaints.category_id
complaints.department_id

complaint_images.complaint_id
complaint_images.uploaded_by

assignments.complaint_id
assignments.worker_id
assignments.assigned_by

sla.complaint_id

resolutions.complaint_id
resolutions.worker_id
resolutions.verified_by

resolution_images.resolution_id
resolution_images.uploaded_by

feedback.complaint_id
feedback.citizen_id

notifications.user_id
notifications.complaint_id

audit_logs.user_id
```

---

# 39. Database Integrity Rules

The database shall enforce appropriate integrity constraints.

Examples:

* User email should be unique.
* Role name should be unique.
* Department name should be unique.
* Category names should be controlled.
* Foreign keys shall reference valid records.
* Complaint must reference a valid citizen.
* Complaint must reference a valid category.
* Assignment must reference a valid worker.
* Worker must belong to the relevant department.
* Feedback must reference a valid complaint.
* Resolution must reference a valid complaint.

---

# 40. Unique Constraints

Recommended unique fields include:

```text
users.email
roles.name
departments.name
categories.name + department_id
complaints.complaint_number
```

Additional unique constraints may be introduced as required.

---

# 41. Indexing Strategy

Indexes shall be created on frequently queried columns.

Recommended indexes:

```text
users.email
users.role_id
users.department_id

complaints.citizen_id
complaints.department_id
complaints.category_id
complaints.status
complaints.priority
complaints.submitted_at

assignments.worker_id
assignments.complaint_id
assignments.status

sla.deadline_at
sla.status

notifications.user_id
notifications.is_read

audit_logs.user_id
audit_logs.entity_type
audit_logs.entity_id
```

Indexes shall be added based on actual query patterns during implementation.

---

# 42. Department Data Isolation

Department-level access is a critical requirement.

The database model supports:

```text
Department 1
   │
   ├── Workers
   ├── Categories
   └── Complaints

Department 2
   │
   ├── Workers
   ├── Categories
   └── Complaints

Department 3
   │
   ├── Workers
   ├── Categories
   └── Complaints
```

A Department Administrator shall only access complaints, workers, and operational information belonging to their authorized department.

This restriction shall be enforced primarily at the backend authorization layer.

---

# 43. Worker Data Isolation

Workers shall only access complaints assigned to them.

Example:

```text
Worker A
   ↓
Assigned Complaints
   ↓
Only those complaints are accessible
```

The Worker App shall not expose complaints from unrelated departments or workers.

---

# 44. Citizen Data Isolation

Citizens shall only access their own complaint records.

```text
Citizen A
   ↓
Complaint A1
Complaint A2

Citizen B
   ↓
Complaint B1
Complaint B2
```

Citizen A must not be able to retrieve Citizen B's complaints.

---

# 45. System Administrator Access

The System Administrator shall have system-wide administrative visibility where authorized.

This includes:

* Users
* Departments
* Categories
* Workers
* Complaints
* Reports
* System configuration
* Audit information

System Administrator access shall still be controlled through authentication and authorization.

---

# 46. Data Lifecycle

The database lifecycle is:

```text
Account Creation
      ↓
Complaint Creation
      ↓
Complaint Submission
      ↓
Verification
      ↓
Assignment
      ↓
Field Work
      ↓
Resolution
      ↓
Verification
      ↓
Completion
      ↓
Feedback
      ↓
Reporting / Audit
```

---

# 47. Complaint Data Lifecycle

```text
┌───────────────┐
│   Complaint   │
│    Created    │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│   Submitted   │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│   Verified    │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│    Assigned   │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│  In Progress  │
└───────┬───────┘
        │
        ▼
┌─────────────────────┐
│ Resolution Submitted│
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Resolution Verified │
└──────────┬──────────┘
           │
           ▼
┌───────────────┐
│   Completed   │
└───────────────┘
```

---

# 48. Soft Delete

Where required, records may use soft deletion instead of physical deletion.

Example:

```text
deleted_at
```

Soft deletion may be used for:

* Users
* Departments
* Categories

Complaint and audit records should generally remain available for historical and accountability purposes.

---

# 49. Timestamp Strategy

Important records should contain timestamps such as:

```text
created_at
updated_at
submitted_at
assigned_at
accepted_at
started_at
submitted_at
verified_at
completed_at
```

All server-generated timestamps should preferably use a consistent timezone strategy, such as UTC.

The client may convert timestamps to the user's local timezone for display.

---

# 50. Database Security

Database security shall include:

* Strong database credentials
* Restricted database access
* Encrypted connections where applicable
* Principle of least privilege
* No hard-coded credentials
* Regular backups
* Secure environment configuration
* Controlled database migrations

The database should not be directly exposed to client applications.

---

# 51. Database Access Architecture

```text
Flutter Citizen App
        │
Flutter Worker App
        │
React Admin Portal
        │
        ▼
   Spring Boot API
        │
        ▼
   Service Layer
        │
        ▼
 Repository Layer
        │
        ▼
 Spring Data JPA
        │
        ▼
    PostgreSQL
```

Clients shall never connect directly to PostgreSQL.

---

# 52. Transaction Management

Critical operations shall be executed within appropriate database transactions.

Examples:

### Complaint Assignment

```text
Verify Worker
     ↓
Create Assignment
     ↓
Update Complaint Status
     ↓
Create Audit Record
     ↓
Create Notification
```

### Resolution Approval

```text
Verify Resolution
     ↓
Update Resolution
     ↓
Update Complaint
     ↓
Record Audit
     ↓
Create Notification
```

Transactions shall help maintain consistency between related records.

---

# 53. Database Migration

Database schema changes should be version-controlled.

A migration tool such as Flyway or Liquibase may be used.

Example:

```text
V1__create_users.sql
V2__create_departments.sql
V3__create_categories.sql
V4__create_complaints.sql
V5__create_assignments.sql
V6__create_resolutions.sql
V7__create_notifications.sql
V8__create_audit_logs.sql
```

This allows database changes to be reproduced across development, testing, and production environments.

---

# 54. Backup and Recovery

The production database should support:

* Regular backups
* Backup verification
* Recovery procedures
* Point-in-time recovery where supported
* Disaster recovery planning

The academic prototype may use simpler scheduled backups.

---

# 55. Database Performance Considerations

Performance shall be improved through:

* Proper indexing
* Efficient SQL queries
* Pagination
* Connection pooling
* Avoiding unnecessary joins
* Query optimization
* Appropriate database constraints
* Caching where required

Large complaint histories and reports should use pagination rather than loading all records at once.

---

# 56. Reporting Data

Reports shall primarily be generated from transactional data.

Common reporting dimensions include:

```text
Department
Category
Status
Priority
Date
SLA
Worker
Resolution Time
Feedback
```

For the initial implementation, reports can be generated using SQL queries and backend aggregation.

A separate analytics database is not required initially.

---

# 57. Example Complaint Record

Conceptually, a complaint may contain:

```json
{
  "complaintNumber": "CS-1001",
  "citizenId": "USER-101",
  "category": "Road Damage",
  "department": "Public Works",
  "description": "Large pothole near the junction",
  "status": "ASSIGNED",
  "priority": "HIGH",
  "latitude": 12.123456,
  "longitude": 75.123456,
  "submittedAt": "2026-08-30T08:30:00Z"
}
```

The actual database representation shall use normalized relational records.

---

# 58. Example Assignment Record

```json
{
  "complaintId": "CS-1001",
  "workerId": "WORKER-204",
  "assignedBy": "ADMIN-101",
  "status": "ACCEPTED",
  "assignedAt": "2026-08-30T09:00:00Z"
}
```

---

# 59. Example Resolution Record

```json
{
  "complaintId": "CS-1001",
  "workerId": "WORKER-204",
  "description": "Pothole repaired and surrounding surface restored",
  "verificationStatus": "UNDER_REVIEW"
}
```

---

# 60. Recommended PostgreSQL Schema

The logical schema can be represented as:

```text
users
roles
departments
categories
complaints
complaint_images
assignments
sla
resolutions
resolution_images
feedback
notifications
audit_logs
```

Optional future tables may include:

```text
refresh_tokens
user_devices
complaint_status_history
worker_availability
department_settings
system_settings
```

---

# 61. Optional Complaint Status History

For stronger auditability, a dedicated `complaint_status_history` table may be introduced.

## Fields

| Field        | Type      | Description        |
| ------------ | --------- | ------------------ |
| id           | PK        | History identifier |
| complaint_id | FK        | Complaint          |
| old_status   | VARCHAR   | Previous status    |
| new_status   | VARCHAR   | New status         |
| changed_by   | FK        | Actor              |
| reason       | TEXT      | Optional reason    |
| created_at   | TIMESTAMP | Change time        |

This provides a complete timeline:

```text
SUBMITTED
   ↓
VERIFIED
   ↓
ASSIGNED
   ↓
IN_PROGRESS
   ↓
RESOLUTION_SUBMITTED
   ↓
COMPLETED
```

---

# 62. Optional Worker Availability

A future `worker_availability` table may store:

```text
worker_id
status
available_from
available_until
updated_at
```

Possible states:

```text
AVAILABLE
BUSY
OFFLINE
ON_LEAVE
```

This can improve automated worker assignment in future versions.

---

# 63. Database-to-Application Mapping

| Database Domain | Backend Module      | Client                  |
| --------------- | ------------------- | ----------------------- |
| User            | User/Auth Module    | All                     |
| Role            | Security Module     | All                     |
| Department      | Department Module   | Admin                   |
| Category        | Category Module     | Citizen/Admin           |
| Complaint       | Complaint Module    | Citizen/Worker/Admin    |
| Images          | Media Module        | Citizen/Worker          |
| Assignment      | Assignment Module   | Worker/Department Admin |
| SLA             | SLA Module          | Department Admin        |
| Resolution      | Resolution Module   | Worker/Department Admin |
| Feedback        | Feedback Module     | Citizen                 |
| Notification    | Notification Module | Citizen/Worker/Admin    |
| Audit           | Audit Module        | System Admin            |

---

# 64. Data Ownership Matrix

| Data                | Citizen               | Worker          | Department Admin | System Admin |
| ------------------- | --------------------- | --------------- | ---------------- | ------------ |
| Own Profile         | Own                   | Own             | Own              | Own          |
| Own Complaints      | Full                  | —               | —                | System       |
| Assigned Complaints | —                     | Assigned        | Department       | System       |
| Worker Data         | —                     | Own             | Department       | System       |
| Department Data     | —                     | Own Department  | Own Department   | System       |
| Categories          | Read                  | Read            | Read             | Full         |
| Assignments         | —                     | Own             | Department       | System       |
| Resolutions         | Read after completion | Own submissions | Department       | System       |
| Feedback            | Own                   | —               | Department       | System       |
| Notifications       | Own                   | Own             | Own              | System       |
| Audit Logs          | —                     | —               | Limited          | Full         |

---

# 65. Final Database Architecture

The final CivicSnap database architecture is:

```text
                         PostgreSQL
                             │
        ┌────────────────────┼─────────────────────┐
        │                    │                     │
        ▼                    ▼                     ▼
     Identity           Organization            Complaints
        │                    │                     │
   ┌────┴────┐         ┌─────┴─────┐       ┌──────┼─────────┐
   │         │         │           │       │      │         │
 Users     Roles   Departments  Categories │    Images    Location
                                             │
                           ┌─────────────────┼────────────────┐
                           │                 │                │
                           ▼                 ▼                ▼
                      Assignments           SLA          Resolution
                           │                                  │
                           │                                  ▼
                           │                         Resolution Images
                           │
                           └──────────────────────────────────┐
                                                              │
                                      ┌───────────────────────┼────────────┐
                                      ▼                       ▼            ▼
                                  Feedback               Notifications   Audit Logs
```

---

# 66. Final Database Summary

CivicSnap shall use **PostgreSQL** as its primary relational database.

The core database shall consist of:

```text
Identity
├── Users
└── Roles

Organization
├── Departments
└── Categories

Complaint Management
├── Complaints
├── Complaint Images
└── Locations

Field Operations
├── Assignments
├── SLA
├── Resolutions
└── Resolution Images

Communication
├── Notifications
└── Feedback

Administration
└── Audit Logs
```

The database shall act as the **central source of truth** for CivicSnap, while all access shall occur through the **Java Spring Boot backend**.

The **Flutter Citizen App**, **Flutter Worker App**, and **React Admin Portal** shall never access PostgreSQL directly.

The backend shall enforce:

* Role-based access
* Citizen ownership
* Worker assignment boundaries
* Department-level isolation
* Complaint status transitions
* Resolution verification
* SLA rules
* Audit requirements

This database architecture supports the complete CivicSnap workflow from **citizen complaint submission → department verification → worker assignment → field resolution → department approval → complaint completion → citizen feedback**.
