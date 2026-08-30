# CivicSnap — Database Constraints

## 1. Introduction

This document defines the database-level constraints and data integrity rules for the CivicSnap system.

The constraints ensure that:

* Invalid data cannot be stored.
* Relationships between entities remain valid.
* Duplicate records are minimized.
* User access boundaries are preserved.
* Complaint lifecycle data remains consistent.
* Department and worker relationships remain valid.
* Historical and audit information remains traceable.

The primary database technology is **PostgreSQL**.

Database constraints shall work together with backend validation and authorization. Database constraints are not a replacement for application-level security.

---

# 2. Database Constraint Categories

CivicSnap shall use the following types of constraints:

```text
Database Constraints
│
├── Primary Key Constraints
├── Foreign Key Constraints
├── NOT NULL Constraints
├── UNIQUE Constraints
├── CHECK Constraints
├── DEFAULT Constraints
├── Referential Integrity
├── Domain Constraints
├── Business Data Constraints
├── Temporal Constraints
└── Data Security Constraints
```

---

# 3. Primary Key Constraints

Every major entity shall have a unique primary key.

Recommended primary key strategy:

* UUID for externally exposed major entities.
* BIGSERIAL/BIGINT where sequential internal identifiers are appropriate.

Major tables requiring primary keys:

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
notifications
feedback
audit_logs
```

Example:

```sql
PRIMARY KEY (id)
```

---

# 4. User Primary Key

Every user must have a unique identifier.

```text
users.id
```

Constraint:

```sql
PRIMARY KEY (id)
```

A user ID must never be reused after the account is removed or deactivated.

---

# 5. Role Primary Key

Every role must have a unique identifier.

```text
roles.id
```

Supported roles:

```text
CITIZEN
WORKER
DEPARTMENT_ADMIN
SYSTEM_ADMIN
```

---

# 6. Department Primary Key

Every department shall have a unique identifier.

```text
departments.id
```

A department ID shall uniquely identify one department throughout the system.

---

# 7. Category Primary Key

Every complaint category shall have a unique identifier.

```text
categories.id
```

---

# 8. Complaint Primary Key

Every complaint shall have a unique identifier.

```text
complaints.id
```

The complaint ID should preferably use UUID to reduce predictability when exposed through APIs.

---

# 9. Complaint Number Constraint

Each complaint shall have a human-readable complaint number.

Example:

```text
CS-100001
CS-100002
CS-100003
```

Constraint:

```sql
UNIQUE (complaint_number)
```

Two complaints shall never have the same complaint number.

---

# 10. Foreign Key Constraints

Foreign keys shall maintain relationships between related records.

Major relationships include:

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

# 11. User-Role Constraint

Every user must have a valid role.

```text
User
  │
  └── Role
```

Therefore:

```sql
FOREIGN KEY (role_id)
REFERENCES roles(id)
```

A user cannot reference a nonexistent role.

---

# 12. User-Department Constraint

Workers and Department Administrators shall be associated with a valid department where required.

```text
User
  │
  └── Department
```

The backend shall ensure that department association is consistent with the user's role.

For example:

```text
WORKER
    → Must belong to a department

DEPARTMENT_ADMIN
    → Must belong to a department

SYSTEM_ADMIN
    → Does not require a department
```

---

# 13. Department Name Constraint

Department names should be unique.

```sql
UNIQUE (name)
```

Example:

```text
Road/Public Works
Waste Management
Water Supply
```

Duplicate active department names should not be permitted.

---

# 14. Category Name Constraint

Category names should not be duplicated within the same department.

Recommended constraint:

```sql
UNIQUE (department_id, name)
```

This allows different departments to have appropriately scoped categories while preventing duplicates inside one department.

---

# 15. Category-Department Constraint

Every category must reference a valid department.

```text
Category
   │
   ▼
Department
```

A category cannot exist without an associated department unless the future design explicitly introduces global categories.

---

# 16. Complaint-Citizen Constraint

Every complaint must belong to a valid citizen.

```sql
FOREIGN KEY (citizen_id)
REFERENCES users(id)
```

The backend shall additionally verify that the referenced user has the `CITIZEN` role.

---

# 17. Complaint-Category Constraint

Every submitted complaint must reference a valid category.

```sql
FOREIGN KEY (category_id)
REFERENCES categories(id)
```

Draft complaints may temporarily allow a null category if the UI supports category selection later.

Once submitted, the category must be present.

---

# 18. Complaint-Department Constraint

Every submitted complaint must have an associated responsible department.

The department may be determined from the selected complaint category.

```text
Complaint Category
       │
       ▼
Responsible Department
       │
       ▼
Complaint
```

The backend shall ensure that the complaint department matches the category's configured department.

---

# 19. Worker-Department Constraint

A worker must belong to a department.

```text
Worker
   │
   ▼
Department
```

A worker from Department A must not be assigned a complaint belonging to Department B.

This shall be enforced through backend business logic and assignment validation.

---

# 20. Assignment-Worker Constraint

Every assignment must reference a valid worker.

```sql
FOREIGN KEY (worker_id)
REFERENCES users(id)
```

The backend shall verify that the referenced user is a worker.

---

# 21. Assignment-Department Constraint

The assigned worker must belong to the department responsible for the complaint.

```text
Complaint
    │
    ▼
Department A
    │
    ▼
Worker
    │
    ▼
Department A
```

Invalid relationship:

```text
Complaint → Department A
Worker    → Department B
```

Such an assignment must be rejected.

---

# 22. Assignment-Administrator Constraint

The user assigning a complaint must be authorized.

The backend shall verify that:

* The user is a Department Administrator or authorized System Administrator.
* A Department Administrator belongs to the complaint's department.
* The assignment action is recorded.

---

# 23. Assignment History Constraint

Assignment history shall not be overwritten when reassignment occurs.

Example:

```text
Complaint
   │
   ├── Assignment 1 → Worker A
   │
   ├── Assignment 2 → Worker B
   │
   └── Assignment 3 → Worker C
```

This preserves historical accountability.

---

# 24. Active Assignment Constraint

A complaint should normally have no more than one active worker assignment at a time.

Conceptually:

```text
Complaint
    │
    └── One Active Assignment
```

Previous assignments must be marked as:

```text
REASSIGNED
COMPLETED
CANCELLED
```

before a new active assignment is created.

---

# 25. Complaint Status Constraint

Complaint status shall only contain approved values.

Recommended values:

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

A PostgreSQL ENUM or CHECK constraint may be used.

Example:

```sql
CHECK (
    status IN (
        'DRAFT',
        'SUBMITTED',
        'VERIFIED',
        'REJECTED',
        'ASSIGNED',
        'IN_PROGRESS',
        'RESOLUTION_SUBMITTED',
        'RESOLUTION_REJECTED',
        'COMPLETED'
    )
)
```

---

# 26. Complaint Status Transition Constraint

The database should prevent invalid status values, while the backend shall enforce valid transitions.

Valid example:

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

Invalid example:

```text
SUBMITTED
    ↓
COMPLETED
```

The backend must reject direct completion without resolution verification.

---

# 27. Rejection Constraint

A rejected complaint should contain a rejection reason.

Conceptually:

```text
status = REJECTED
        ↓
rejection_reason IS NOT NULL
```

The database may enforce this through a CHECK constraint where practical.

---

# 28. Resolution Status Constraint

Resolution status shall use controlled values:

```text
SUBMITTED
UNDER_REVIEW
APPROVED
REJECTED
```

Invalid arbitrary values shall not be accepted.

---

# 29. Resolution Verification Constraint

A resolution may only be approved or rejected by an authorized Department Administrator.

The database shall maintain:

```text
verified_by
verified_at
verification_status
```

When a resolution is approved:

```text
verification_status = APPROVED
verified_by IS NOT NULL
verified_at IS NOT NULL
```

---

# 30. Resolution Rejection Constraint

If a resolution is rejected:

```text
verification_status = REJECTED
```

A rejection reason should be provided.

This ensures that the worker understands why the submitted resolution was rejected.

---

# 31. Resolution-Worker Constraint

The worker submitting a resolution must be associated with the relevant assignment.

```text
Complaint
   │
   ▼
Assignment
   │
   ▼
Worker
   │
   ▼
Resolution
```

A worker who was never assigned to the complaint must not submit its resolution.

This shall primarily be enforced by backend business logic.

---

# 32. Resolution-Complaint Constraint

Every resolution must belong to a valid complaint.

```sql
FOREIGN KEY (complaint_id)
REFERENCES complaints(id)
```

A resolution cannot exist independently of a complaint.

---

# 33. Resolution Completion Constraint

A complaint shall not be marked `COMPLETED` merely because a worker submitted a resolution.

Required workflow:

```text
Worker
   ↓
Submit Resolution
   ↓
Department Admin
   ↓
Verify Resolution
   ↓
Approve
   ↓
Complaint Completed
```

The backend shall enforce this rule.

---

# 34. Feedback Complaint Constraint

Feedback must reference a valid complaint.

```sql
FOREIGN KEY (complaint_id)
REFERENCES complaints(id)
```

---

# 35. Feedback Citizen Constraint

Feedback must reference the citizen associated with the complaint.

Conceptually:

```text
Feedback.citizen_id
        =
Complaint.citizen_id
```

A different citizen must not be able to submit feedback for another citizen's complaint.

This relationship shall be enforced by backend authorization and validation.

---

# 36. Feedback Completion Constraint

Feedback shall only be allowed after a complaint is completed.

```text
Complaint Status
      │
      ▼
COMPLETED
      │
      ▼
Feedback Allowed
```

Feedback for active or rejected complaints shall normally be rejected.

---

# 37. Feedback Rating Constraint

Ratings shall use a defined range.

Recommended range:

```text
1 – 5
```

Database constraint:

```sql
CHECK (rating >= 1 AND rating <= 5)
```

Values such as `0`, `6`, or negative values shall be rejected.

---

# 38. Image File Size Constraint

Uploaded images shall have a maximum permitted size.

The exact limit shall be configurable.

Example:

```text
Maximum Image Size = Configurable
```

The backend shall validate the file size before storage.

---

# 39. Image Content-Type Constraint

Only supported image types shall be accepted.

Example:

```text
image/jpeg
image/png
image/webp
```

The backend shall reject unsupported or dangerous file types.

---

# 40. Complaint Image Ownership Constraint

Every complaint image must reference:

* A valid complaint.
* A valid uploading user.
* A valid storage location.

```text
Complaint Image
   │
   ├── Complaint
   ├── Uploaded By
   └── Storage Reference
```

---

# 41. Resolution Image Constraint

Every resolution image must reference a valid resolution.

```text
Resolution
    │
    └──< Resolution Images
```

A resolution image cannot exist without its associated resolution.

---

# 42. GPS Latitude Constraint

Latitude values must fall within the valid geographical range:

```text
-90 ≤ latitude ≤ 90
```

Example:

```sql
CHECK (latitude >= -90 AND latitude <= 90)
```

---

# 43. GPS Longitude Constraint

Longitude values must fall within:

```text
-180 ≤ longitude ≤ 180
```

Example:

```sql
CHECK (longitude >= -180 AND longitude <= 180)
```

---

# 44. Location Accuracy Constraint

GPS accuracy should not contain negative values.

```sql
CHECK (location_accuracy >= 0)
```

A null value may be permitted if the device does not provide accuracy information.

---

# 45. Timestamp Constraints

Important timestamps shall maintain logical ordering.

Example:

```text
created_at
    ≤
submitted_at
    ≤
assigned_at
    ≤
completed_at
```

Not every timestamp is required for every complaint state.

The backend shall validate lifecycle-specific timestamps.

---

# 46. SLA Deadline Constraint

An SLA deadline must occur after the SLA start time.

```text
deadline_at > started_at
```

Database constraint:

```sql
CHECK (deadline_at > started_at)
```

---

# 47. SLA Completion Constraint

If an SLA is completed:

```text
completed_at IS NOT NULL
```

If the complaint has not been completed:

```text
completed_at IS NULL
```

The backend shall synchronize SLA state with complaint state.

---

# 48. SLA Breach Constraint

If an SLA is marked as breached:

```text
status = BREACHED
```

then:

```text
breached_at IS NOT NULL
```

---

# 49. Notification Constraint

Every notification must have a valid recipient.

```sql
FOREIGN KEY (user_id)
REFERENCES users(id)
```

---

# 50. Notification Read Constraint

If a notification is marked as read:

```text
is_read = TRUE
```

then:

```text
read_at IS NOT NULL
```

If unread:

```text
is_read = FALSE
read_at IS NULL
```

---

# 51. Audit Log Constraint

Audit records must identify the action being recorded.

Required information should include:

```text
action
entity_type
entity_id
created_at
```

Where the action is associated with a user, `user_id` should reference a valid account.

---

# 52. Audit Immutability

Audit logs shall be treated as append-only records.

Normal users shall not be able to:

* Update audit records.
* Delete audit records.
* Modify historical audit information.

The application/database permissions shall restrict modification.

---

# 53. NOT NULL Constraints

The following fields should generally be NOT NULL.

### Users

```text
id
full_name
email
password_hash
role_id
is_active
created_at
```

### Roles

```text
id
name
```

### Departments

```text
id
name
is_active
created_at
```

### Categories

```text
id
name
department_id
is_active
```

### Complaints

For submitted complaints:

```text
id
complaint_number
citizen_id
category_id
department_id
description
status
created_at
```

---

# 54. Default Constraints

Default values should be provided where appropriate.

Examples:

```text
is_active = TRUE
is_read = FALSE
status = DRAFT
created_at = CURRENT_TIMESTAMP
updated_at = CURRENT_TIMESTAMP
```

---

# 55. Email Constraint

User email addresses shall be unique.

```sql
UNIQUE (email)
```

Email format validation should primarily be handled by the application layer.

---

# 56. Phone Constraint

Phone numbers should follow a consistent storage format.

The backend shall validate:

* Length
* Allowed characters
* Country code where required

The database should avoid overly restrictive formatting assumptions if the application may support multiple regions.

---

# 57. Password Constraint

Passwords shall never be stored as plain text.

The database shall only store a secure password hash.

```text
Password
   ↓
Hashing
   ↓
password_hash
```

The exact hashing algorithm shall be selected by the backend security configuration.

---

# 58. Department Administrator Constraint

A Department Administrator must belong to a department.

```text
DEPARTMENT_ADMIN
       │
       ▼
Department
```

The backend shall reject a Department Administrator configuration without an authorized department.

---

# 59. System Administrator Department Constraint

A System Administrator is system-wide and normally does not require a department association.

```text
SYSTEM_ADMIN
      │
      └── System-Wide
```

The database may allow:

```text
department_id = NULL
```

for System Administrators.

---

# 60. Worker Active Status Constraint

Only active workers should be eligible for new assignments.

```text
Worker
  │
  ├── Active → Eligible
  │
  └── Inactive → Not Eligible
```

This shall be enforced by the assignment service.

---

# 61. Complaint Deletion Constraint

Complaints should generally not be physically deleted after submission.

Instead, the system should retain complaint records for:

* Accountability
* Reporting
* Audit
* Historical tracking

If necessary, soft deletion may be used.

---

# 62. Department Deletion Constraint

Departments with historical complaints should not normally be physically deleted.

Instead:

```text
is_active = FALSE
```

should be used to deactivate the department.

---

# 63. Category Deletion Constraint

Categories referenced by historical complaints should not normally be physically deleted.

Instead:

```text
is_active = FALSE
```

should be used.

This prevents broken historical references.

---

# 64. User Deactivation Constraint

Users with historical activity should normally be deactivated instead of physically deleted.

Example:

```text
is_active = FALSE
```

This preserves:

* Complaints
* Assignments
* Resolutions
* Feedback
* Audit history

---

# 65. Referential Action Rules

Recommended foreign-key behavior:

### Historical Records

Use:

```text
ON DELETE RESTRICT
```

where deletion could destroy important history.

### Optional Relationships

Use:

```text
ON DELETE SET NULL
```

only where the relationship is genuinely optional.

### Dependent Media

For example, complaint images may use controlled cascading deletion only when the parent complaint is permanently removed under an authorized maintenance operation.

Cascading deletion shall not be used carelessly on audit or historical records.

---

# 66. Data Normalization

The database should generally follow normalization principles up to approximately **Third Normal Form (3NF)**.

The design should avoid:

* Repeated groups
* Unnecessary duplicated values
* Update anomalies
* Inconsistent department information
* Redundant user information

Example:

Instead of storing:

```text
Complaint
department_name = "Road Department"
```

the complaint should reference:

```text
department_id
```

which references the department table.

---

# 67. Controlled Redundancy

Some redundancy may be introduced intentionally for:

* Reporting performance
* Historical snapshots
* Audit records
* Search optimization

Any denormalized fields must have a clear synchronization strategy.

---

# 68. Department Consistency Constraint

The following relationship must remain consistent:

```text
Complaint
   │
   ├── Category
   │       │
   │       ▼
   │   Department
   │
   └── Department
```

The complaint's department must correspond to the category's responsible department.

This should be validated by the backend before submission.

---

# 69. Worker Assignment Consistency

The following must remain consistent:

```text
Complaint Department
       =
Worker Department
```

If not:

```text
Assignment → REJECTED
```

This prevents workers from receiving unrelated departmental work.

---

# 70. Department Administrator Scope Constraint

A Department Administrator may only operate on records belonging to their department.

Conceptually:

```text
Admin Department ID
       =
Complaint Department ID
```

If they differ:

```text
Access Denied
```

This is primarily a backend authorization constraint.

---

# 71. Citizen Ownership Constraint

A citizen may only access their own complaint records.

Conceptually:

```text
Authenticated User ID
       =
Complaint Citizen ID
```

If they differ:

```text
Access Denied
```

---

# 72. Worker Assignment Scope Constraint

A worker may only access operational information for complaints assigned to them.

Conceptually:

```text
Authenticated Worker ID
       =
Assignment Worker ID
```

The Worker App shall not expose unrelated departmental complaints.

---

# 73. System Administrator Scope

System Administrators may access system-wide information according to authorization policy.

This includes:

```text
Users
Departments
Categories
Workers
Complaints
Reports
Audit Logs
System Settings
```

---

# 74. AI Data Constraint

AI-generated descriptions shall be stored separately from the final citizen-confirmed description.

Recommended fields:

```text
ai_description
description
```

The system shall not overwrite the final complaint description automatically with AI output.

---

# 75. AI Output Integrity

AI-generated content shall be considered non-authoritative.

The workflow shall be:

```text
Image
  ↓
AI
  ↓
Suggested Description
  ↓
Citizen Review
  ↓
Final Description
  ↓
Complaint
```

---

# 76. Complaint Image and AI Constraint

An image submitted for AI analysis must:

1. Exist in the storage system.
2. Be associated with the complaint/draft.
3. Pass file validation.
4. Be accessible to the AI integration service where permitted.

---

# 77. Unique Active Feedback Constraint

A complaint should normally have at most one active citizen feedback record.

Conceptually:

```text
Complaint
   │
   └── 0 or 1 Feedback
```

A unique constraint may be applied:

```sql
UNIQUE (complaint_id)
```

if the business requirement allows only one feedback submission.

---

# 78. Unique Active Resolution Constraint

The system should normally maintain one current resolution under review for a complaint.

Historical rejected resolutions may remain stored.

Example:

```text
Complaint
   │
   ├── Resolution 1 → REJECTED
   │
   └── Resolution 2 → APPROVED
```

Only one resolution should be the final approved resolution.

---

# 79. Resolution Approval Constraint

A complaint cannot have multiple simultaneously approved final resolutions.

Conceptually:

```text
Complaint
    │
    └── Maximum One APPROVED Resolution
```

A partial unique index may be used if appropriate.

---

# 80. Audit Relationship Constraint

Audit records should preserve the identity of the actor whenever the actor is known.

Example:

```text
User
  │
  ▼
AuditLog
```

Historical audit information must remain understandable even if the user's account is later deactivated.

---

# 81. Database Transaction Constraints

Critical multi-record operations should execute atomically.

### Assignment

```text
Validate Worker
      ↓
Create Assignment
      ↓
Update Complaint
      ↓
Create Audit
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
Update SLA
      ↓
Create Audit
      ↓
Create Notification
```

If a critical database operation fails, the transaction should roll back where appropriate.

---

# 82. Concurrency Constraints

The system shall protect against conflicting simultaneous operations.

Example:

```text
Admin A ── Assign Worker A
Admin B ── Assign Worker B
              │
              ▼
        Same Complaint
```

The backend/database must prevent inconsistent assignment states.

Appropriate transaction isolation, optimistic locking, or pessimistic locking may be used.

---

# 83. Optimistic Locking

Entities that are frequently modified may use optimistic locking.

Example field:

```text
version
```

This can help prevent accidental overwriting of newer data.

Potential entities include:

* Complaint
* Assignment
* Resolution

---

# 84. Database Validation vs Backend Validation

Not every business rule should be implemented purely at database level.

### Database should enforce:

* Primary keys
* Foreign keys
* NOT NULL
* UNIQUE
* Basic CHECK constraints
* Referential integrity
* Basic data ranges

### Backend should enforce:

* Role permissions
* Department isolation
* Worker assignment rules
* Complaint status transitions
* Resolution approval
* SLA business logic
* AI workflow
* Notification behavior

Both layers should work together.

---

# 85. Constraint Priority

| Constraint              | Database | Backend |
| ----------------------- | :------: | :-----: |
| Primary Keys            |     ✓    |    —    |
| Foreign Keys            |     ✓    |    ✓    |
| Required Fields         |     ✓    |    ✓    |
| Unique Email            |     ✓    |    ✓    |
| Unique Complaint Number |     ✓    |    ✓    |
| Latitude Range          |     ✓    |    ✓    |
| Longitude Range         |     ✓    |    ✓    |
| Rating Range            |     ✓    |    ✓    |
| Role Authorization      |     —    |    ✓    |
| Department Isolation    |     —    |    ✓    |
| Worker Assignment       |     —    |    ✓    |
| Status Transition       |  Limited |    ✓    |
| Resolution Approval     |     —    |    ✓    |
| SLA Rules               |  Limited |    ✓    |
| Audit Creation          |     —    |    ✓    |
| Password Hashing        |     —    |    ✓    |

---

# 86. Security Constraints

The database shall enforce or support:

1. Restricted database users.
2. No direct client access.
3. Least-privilege database permissions.
4. Encrypted database connections where required.
5. Secure credential storage.
6. Controlled access to audit data.
7. Secure image storage references.
8. Protection against unauthorized modification.

---

# 87. Client Database Access Constraint

The following applications must never connect directly to PostgreSQL:

```text
Flutter Citizen App     ✗
Flutter Worker App      ✗
React Admin Portal      ✗
```

All database operations must pass through:

```text
Client
   ↓
Spring Boot REST API
   ↓
Service Layer
   ↓
Repository Layer
   ↓
PostgreSQL
```

---

# 88. Sensitive Data Constraints

Sensitive information shall be minimized.

The database shall not store:

* Plain-text passwords
* Unnecessary authentication secrets
* API keys
* AI provider secrets
* Database passwords

Secrets shall be managed through secure configuration mechanisms.

---

# 89. Audit Retention Constraint

Audit records should be retained according to the project's data-retention policy.

Audit data should not be automatically removed merely because the associated user has been deactivated.

---

# 90. Historical Data Constraint

Historical records should remain internally consistent even after:

* Worker deactivation
* Department deactivation
* Category deactivation
* User deactivation

Historical reports must continue to identify the relevant entities.

---

# 91. Database Naming Conventions

The following naming convention is recommended:

### Tables

Use lowercase snake_case:

```text
users
departments
complaint_images
resolution_images
audit_logs
```

### Columns

Use lowercase snake_case:

```text
created_at
updated_at
department_id
complaint_id
```

### Primary Keys

```text
id
```

### Foreign Keys

```text
<entity>_id
```

---

# 92. Index Constraints

Indexes should be created for frequently searched fields.

Recommended indexes:

```text
users.email
users.role_id
users.department_id

complaints.complaint_number
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

audit_logs.entity_type
audit_logs.entity_id
audit_logs.user_id
```

Indexes should be evaluated based on actual query performance.

---

# 93. Pagination Constraint

Large result sets shall not be returned without pagination.

This is particularly important for:

* Complaint lists
* Worker assignments
* Reports
* Notifications
* Audit logs

Example:

```text
Page 1 → 20 records
Page 2 → 20 records
Page 3 → 20 records
```

Pagination shall primarily be implemented at the API/service level.

---

# 94. Search Constraint

Search queries should use indexed fields where possible.

Common search fields include:

```text
complaint_number
status
category
department
priority
date
worker
```

Full-text search may be introduced later if required.

---

# 95. Soft Delete Constraints

Where soft deletion is used, the system shall ensure that normal queries do not accidentally return deleted records.

Example:

```text
deleted_at IS NULL
```

Historical records shall remain available to authorized administrators where required.

---

# 96. Migration Constraints

All structural database changes should be version-controlled.

Examples:

```text
V1__initial_schema
V2__add_sla
V3__add_notifications
V4__add_audit_logs
```

Production schema changes shall not be performed manually without proper migration tracking.

---

# 97. Backup Constraints

The production database shall have a backup strategy.

Backups should support:

* Scheduled backups
* Backup verification
* Recovery testing
* Secure storage
* Appropriate retention

---

# 98. Disaster Recovery Constraints

The database recovery process should ensure that critical CivicSnap data can be restored.

Priority data includes:

```text
Users
Departments
Complaints
Assignments
Resolutions
SLA
Feedback
Audit Logs
```

---

# 99. Final Constraint Matrix

| Entity           | Primary Key | Foreign Keys                  | Unique            | NOT NULL         | CHECK / Validation     |
| ---------------- | ----------- | ----------------------------- | ----------------- | ---------------- | ---------------------- |
| User             | id          | role, department              | email             | Core fields      | Role/dept consistency  |
| Role             | id          | —                             | name              | name             | Valid role             |
| Department       | id          | —                             | name              | name             | Active status          |
| Category         | id          | department                    | name + department | Core fields      | SLA values             |
| Complaint        | id          | citizen, category, department | complaint number  | Submitted fields | Status, priority, GPS  |
| Complaint Image  | id          | complaint, user               | —                 | Core fields      | File type/size         |
| Assignment       | id          | complaint, worker, admin      | —                 | Core fields      | Valid status           |
| SLA              | id          | complaint                     | complaint         | Core fields      | Deadline ordering      |
| Resolution       | id          | complaint, worker, admin      | —                 | Core fields      | Verification status    |
| Resolution Image | id          | resolution, user              | —                 | Core fields      | File validation        |
| Feedback         | id          | complaint, citizen            | complaint*        | Core fields      | Rating 1–5             |
| Notification     | id          | user, complaint               | —                 | Core fields      | Read-state consistency |
| Audit Log        | id          | user                          | —                 | Core fields      | Append-only            |

`*` applies if CivicSnap permits only one feedback submission per complaint.

---

# 100. Final Database Constraint Model

The overall integrity model is:

```text
                         ROLE
                          │
                          ▼
                         USER
                          │
              ┌───────────┼────────────┐
              │           │            │
              ▼           ▼            ▼
          CITIZEN      WORKER        ADMIN
              │           │            │
              │           │            │
              ▼           │            ▼
          COMPLAINT       │       DEPARTMENT
              │           │            │
        ┌─────┼─────┐     │            │
        │     │     │     │            │
        ▼     ▼     ▼     │            ▼
    CATEGORY IMAGE  SLA   │        CATEGORY
        │                │
        ▼                │
    DEPARTMENT ◄─────────┘
        │
        ▼
    ASSIGNMENT
        │
        ▼
      WORKER
        │
        ▼
    RESOLUTION
        │
        ▼
  RESOLUTION IMAGE
        │
        ▼
  DEPARTMENT VERIFY
        │
        ▼
    COMPLETED
        │
        ▼
     FEEDBACK

All important operations
        │
        ▼
    AUDIT LOG
```

---

# 101. Final Database Integrity Principles

CivicSnap shall follow these core database principles:

1. **Every important entity must have a unique identifier.**
2. **Foreign keys must preserve referential integrity.**
3. **Duplicate critical records must be prevented.**
4. **Required data must not be NULL.**
5. **Controlled fields must use valid values.**
6. **GPS coordinates must remain within valid ranges.**
7. **Complaint status values must be controlled.**
8. **Workers must remain associated with their departments.**
9. **Workers must only be assigned to complaints belonging to their department.**
10. **Department Administrators must remain restricted to their department.**
11. **Citizens must only access their own complaints.**
12. **Workers must only access their assigned complaints.**
13. **Resolution approval must remain an administrative operation.**
14. **Completed complaints must have an approved resolution.**
15. **Feedback must be associated with the correct citizen and completed complaint.**
16. **Audit records must be protected from modification.**
17. **Historical records should not be unnecessarily deleted.**
18. **Critical multi-record operations must be transactional.**
19. **Clients must never directly access PostgreSQL.**
20. **The Spring Boot backend remains the authoritative enforcement layer for business rules and authorization.**

---

# 102. Final Summary

The CivicSnap PostgreSQL database shall provide a strong integrity foundation for the entire complaint-management workflow.

The most important constraints protect the following relationships:

```text
Citizen
   ↓
Own Complaint

Complaint
   ↓
Correct Category
   ↓
Correct Department
   ↓
Correct Worker

Worker
   ↓
Resolution

Department Admin
   ↓
Resolution Verification
   ↓
Completion

Citizen
   ↓
Feedback

Every Important Action
   ↓
Audit Log
```

The database will enforce structural integrity through **primary keys, foreign keys, unique constraints, NOT NULL constraints, CHECK constraints, defaults, and referential integrity**.

The Spring Boot backend will enforce higher-level business rules including **role authorization, department isolation, worker assignment rules, complaint status transitions, SLA logic, and resolution verification**.

Together, the PostgreSQL database and Spring Boot backend provide the integrity and security foundation required for the CivicSnap system.
