# CivicSnap — Business Rules

## 1. Introduction

This document defines the business rules governing the CivicSnap civic complaint management system.

Business rules determine how complaints are created, categorized, verified, assigned, processed, resolved, and completed. They also define the permissions and responsibilities of citizens, workers, department administrators, and system administrators.

These rules shall be enforced by the backend system regardless of which application or interface is used.

---

# 2. User and Role Rules

## BR-01: Supported User Roles

CivicSnap shall support the following primary roles:

* Citizen
* Worker
* Department Administrator
* System Administrator

Each role shall have different permissions and responsibilities.

---

## BR-02: Citizen Access

A citizen shall only be able to access and manage their own profile and complaints.

A citizen shall not be able to access complaints submitted by other citizens.

---

## BR-03: Worker Access

A worker shall only be able to view and manage complaints assigned to them, unless additional permissions are explicitly provided.

A worker shall not be able to modify department configuration or assign complaints to other workers.

---

## BR-04: Department Administrator Access

A department administrator shall be responsible for complaints belonging to their assigned department.

A department administrator shall not manage complaints belonging to another department unless granted system-level permission.

---

## BR-05: System Administrator Access

The system administrator shall have system-wide administrative privileges.

The system administrator shall be able to manage departments, users, workers, department administrators, and system-level configurations.

---

# 3. Account Rules

## BR-06: Unique User Accounts

Each citizen account shall use a unique registered email address and/or mobile number.

Duplicate accounts using the same required unique identifier shall not be permitted.

---

## BR-07: Authentication Required

Users must be authenticated before accessing protected system functions.

---

## BR-08: Account Deactivation

A deactivated account shall not be able to perform normal authenticated operations.

Existing complaint records associated with a deactivated user shall not be deleted automatically.

---

# 4. Complaint Creation Rules

## BR-09: Complaint Ownership

Every complaint shall belong to the citizen who submitted it.

The complaint shall be associated with the citizen's unique user ID.

---

## BR-10: Required Complaint Information

A complaint shall contain the required information before submission:

* Complaint image
* Complaint description
* Complaint category/type
* Complaint location
* Submission information

A complaint shall not be submitted if mandatory information is missing.

---

## BR-11: Complaint ID

Every successfully submitted complaint shall receive a unique complaint ID.

The complaint ID shall remain associated with the complaint throughout its lifecycle.

---

## BR-12: Submission Timestamp

The system shall automatically record the date and time when a complaint is submitted.

Users shall not be allowed to manually alter the official submission timestamp.

---

# 5. AI Description Rules

## BR-13: AI Description as a Suggestion

AI-generated descriptions shall be treated as suggestions and shall not automatically become the final complaint description.

---

## BR-14: Citizen Confirmation

The citizen shall review and confirm the AI-generated description before submitting the complaint.

The citizen shall be allowed to modify the generated description.

---

## BR-15: AI Failure

If the AI service fails to generate a description, the citizen shall be allowed to provide a description manually, provided the required complaint information is available.

AI failure shall not cause loss of the complaint image.

---

## BR-16: AI Output Validation

AI-generated content shall be validated before being stored or displayed as the final complaint description.

The system shall not blindly trust AI-generated classifications or descriptions.

---

# 6. Complaint Category Rules

## BR-17: Complaint Classification

Every complaint shall have a complaint category.

Examples include:

* Road damage
* Waste accumulation
* Street light issue
* Water leakage
* Drainage issue
* Public infrastructure damage
* Other civic issue

---

## BR-18: Department Mapping

Each supported complaint category shall be associated with an appropriate department.

For example:

```text
Street Light Issue → Electricity Department
Waste Issue        → Waste Management Department
Road Damage        → Public Works/Roads Department
Water Leakage      → Water Supply Department
```

The exact department mappings shall be configurable by the system administrator.

---

## BR-19: Unclassified Complaints

If the system cannot confidently determine the appropriate category or department, the complaint shall be routed for administrative review rather than automatically assigned to a worker.

---

# 7. Location Rules

## BR-20: Complaint Location

Each complaint shall have a location associated with it.

The location shall normally be captured using the citizen's device GPS.

---

## BR-21: Location Confirmation

The citizen shall be given an opportunity to verify the captured location before submitting the complaint.

---

## BR-22: Location Accuracy

If the captured location does not meet the required accuracy threshold, the system may request the citizen to retry location capture.

---

## BR-23: Location Privacy

Complaint location information shall only be accessible to users who require it for complaint processing.

---

# 8. Complaint Verification Rules

## BR-24: Initial Complaint Status

Every newly submitted complaint shall initially have the status:

**Submitted**

---

## BR-25: Department Verification

A complaint shall be reviewed by the appropriate department administrator before it can be assigned to a worker.

---

## BR-26: Valid Complaint

If a complaint contains sufficient and valid information, the department administrator may verify it.

The complaint status shall change:

```text
Submitted → Verified
```

---

## BR-27: Invalid Complaint

If a complaint is invalid, incomplete, inappropriate, or cannot be acted upon, the department administrator may reject it.

The complaint status shall change:

```text
Submitted → Rejected
```

A rejection reason should be recorded.

---

## BR-28: Rejected Complaint

A rejected complaint shall not be assigned to a worker.

The citizen shall be able to view the rejection status and reason where applicable.

---

# 9. Worker Assignment Rules

## BR-29: Assignment Eligibility

Only verified complaints may be assigned to workers.

A complaint shall not be assigned while it remains in the `Submitted` state.

---

## BR-30: Department Matching

A complaint shall only be assigned to a worker belonging to the department responsible for that complaint.

---

## BR-31: Worker Availability

The department administrator should consider worker availability and current workload before assigning a complaint.

Workers who are unavailable or inactive should not receive new assignments.

---

## BR-32: Assignment Record

Every complaint assignment shall record:

* Complaint ID
* Worker ID
* Assigning administrator
* Assignment date and time
* Assignment status

---

## BR-33: Assignment Notification

When a complaint is assigned, the assigned worker shall receive an appropriate notification.

---

## BR-34: Reassignment

A department administrator may reassign a complaint when necessary.

A reassignment shall be recorded in the complaint history.

The previously assigned worker and newly assigned worker shall be identifiable from the assignment history.

---

# 10. Worker App Rules

## BR-35: Worker Assignment Visibility

The Worker App shall display complaints assigned to the authenticated worker.

Workers shall not see unrelated complaints by default.

---

## BR-36: Accepting Work

A worker shall be able to accept an assigned complaint.

Once accepted, the assignment shall be recorded.

---

## BR-37: Work Status

A worker may update an accepted complaint to:

**In Progress**

This indicates that the worker has started handling the civic issue.

---

## BR-38: Worker Resolution

A worker shall not directly mark a complaint as finally completed.

The worker shall instead submit resolution information for department verification.

---

# 11. Resolution Rules

## BR-39: Resolution Evidence

A worker shall provide appropriate evidence when submitting a resolution.

Evidence may include:

* Resolution photographs
* Before/after photographs
* Resolution description
* Completion notes

---

## BR-40: Resolution Submission

After completing the work, the worker shall submit the resolution for verification.

The status shall change:

```text
In Progress → Resolution Under Verification
```

---

## BR-41: Resolution Verification

The department administrator shall review the submitted resolution evidence.

The administrator shall decide whether the issue has been adequately resolved.

---

## BR-42: Approved Resolution

If the resolution is satisfactory:

```text
Resolution Under Verification → Completed
```

The completion date and verification information shall be recorded.

---

## BR-43: Rejected Resolution

If the resolution is insufficient:

```text
Resolution Under Verification → In Progress
```

The administrator shall provide a reason for rejection where applicable.

The worker shall be able to continue working on the complaint.

---

# 12. Complaint Status Rules

## BR-44: Valid Status Flow

The standard complaint lifecycle shall follow:

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

## BR-45: Rejection Flow

An invalid complaint may follow:

```text
Submitted → Rejected
```

---

## BR-46: Resolution Rejection Flow

An insufficient resolution may follow:

```text
Resolution Under Verification
            ↓
        Rejected
            ↓
       In Progress
```

---

## BR-47: Unauthorized Status Changes

Users shall not be allowed to arbitrarily change complaint statuses.

For example:

* Citizen cannot mark a complaint as Completed.
* Worker cannot directly mark a complaint as Completed.
* Citizen cannot assign complaints to workers.
* Worker cannot approve their own resolution.

---

# 13. SLA Rules

## BR-48: SLA Definition

Each applicable complaint category may have a defined Service Level Agreement (SLA).

The SLA shall define the expected time for resolving the complaint.

---

## BR-49: SLA Calculation

The system shall automatically calculate the complaint's SLA deadline based on the configured SLA rules.

---

## BR-50: SLA Monitoring

The system shall monitor active complaints against their SLA deadlines.

Complaints approaching their deadline should be highlighted to department administrators.

---

## BR-51: SLA Breach

A complaint shall be marked as SLA-breached when the defined deadline is exceeded without reaching the required resolution stage.

The SLA breach shall be recorded for reporting purposes.

---

## BR-52: SLA Does Not Automatically Complete Complaints

An SLA deadline shall not automatically mark a complaint as completed.

Completion shall still require actual resolution and department verification.

---

# 14. Duplicate Complaint Rules

## BR-53: Duplicate Detection

The system should attempt to identify potential duplicate complaints based on factors such as:

* Location
* Complaint category
* Similar description
* Submission time
* Similar images

---

## BR-54: Duplicate Handling

Potential duplicate complaints may be flagged for department administrator review.

The system shall not automatically delete a complaint solely because it appears similar to another complaint.

---

# 15. Citizen Notification Rules

## BR-55: Complaint Submission Notification

The citizen should receive confirmation after successfully submitting a complaint.

The notification should contain the complaint ID.

---

## BR-56: Status Change Notifications

The citizen should be notified about important status changes, including:

* Verification
* Rejection
* Assignment
* Resolution
* Completion

---

## BR-57: Notification Failure

Failure to send a notification shall not cancel or roll back the corresponding complaint operation.

---

# 16. Worker Notification Rules

## BR-58: Assignment Notification

A worker shall be notified when a complaint is assigned to them.

---

## BR-59: Reassignment Notification

A worker shall be notified when an assignment is changed or removed.

---

# 17. Department Administration Rules

## BR-60: Department Data Isolation

Department administrators shall only manage complaints, workers, and operational information belonging to their department.

---

## BR-61: Worker Management

Department administrators may manage workers associated with their department according to their permissions.

---

## BR-62: Assignment Authority

Only authorized department administrators shall be able to assign or reassign complaints.

Workers and citizens shall not have assignment authority.

---

## BR-63: Resolution Approval Authority

Only an authorized department administrator shall be able to approve or reject a worker's submitted resolution.

---

# 18. System Administration Rules

## BR-64: Department Creation

Only the system administrator shall be able to create or configure departments.

---

## BR-65: Department Category Mapping

The system administrator shall be able to configure which complaint categories are handled by each department.

---

## BR-66: Staff Assignment

The system administrator shall be able to associate workers and department administrators with appropriate departments.

---

## BR-67: System-Wide Monitoring

The system administrator shall be able to view system-wide complaint information across departments.

---

# 19. Feedback Rules

## BR-68: Feedback Eligibility

A citizen may provide feedback only after the corresponding complaint has been marked:

**Completed**

---

## BR-69: Feedback Association

Each feedback submission shall be associated with its corresponding complaint and citizen.

---

## BR-70: Feedback Modification

The system may restrict citizens from repeatedly modifying feedback after submission, depending on the configured policy.

---

# 20. Audit and History Rules

## BR-71: Status History

Every significant complaint status change shall be recorded.

The history shall contain:

* Previous status
* New status
* User responsible
* Date and time
* Optional reason/comment

---

## BR-72: Assignment History

All complaint assignments and reassignments shall be recorded.

---

## BR-73: Resolution History

Resolution submissions, approvals, and rejections shall be recorded.

---

## BR-74: Audit Protection

Audit records shall only be accessible to authorized personnel and shall not be freely editable by ordinary users.

---

# 21. Image and File Rules

## BR-75: Supported Images

The system shall only accept supported image formats and file sizes.

---

## BR-76: Image Validation

Uploaded images shall be validated before being stored or processed by the AI service.

---

## BR-77: Resolution Images

Resolution images shall be associated with the worker's resolution submission and the relevant complaint.

---

## BR-78: Unauthorized Image Access

Complaint and resolution images shall not be publicly accessible without authorization.

---

# 22. Data Retention Rules

## BR-79: Complaint Records

Complaint records shall be retained according to the system's configured data-retention policy.

---

## BR-80: Historical Records

Completed and rejected complaints shall remain available for authorized reporting and auditing unless removed according to an approved retention policy.

---

## BR-81: Account Deletion

Deleting or deactivating a user account shall not automatically delete historical complaint records required for auditing or reporting.

---

# 23. Reporting Rules

## BR-82: Department Reports

Department administrators shall be able to generate reports for complaints handled by their department.

---

## BR-83: System Reports

System administrators shall be able to generate system-wide reports.

---

## BR-84: Performance Metrics

Reports may include:

* Total complaints
* Complaints by category
* Complaints by department
* Completed complaints
* Pending complaints
* Average resolution time
* SLA breaches
* Worker workload
* Department performance
* Citizen feedback

---

# 24. Business Rule Priority

When multiple rules apply, the following principles shall take priority:

1. **Security and authorization**
2. **Data integrity**
3. **Complaint workflow rules**
4. **Department and worker assignment rules**
5. **SLA rules**
6. **Notifications and user convenience**

No convenience feature shall override security, authorization, or data-integrity rules.

---

# 25. Core Business Rule Summary

| ID    | Business Rule                                                  | Primary Responsibility |
| ----- | -------------------------------------------------------------- | ---------------------- |
| BR-01 | Four primary user roles                                        | System                 |
| BR-02 | Citizens access their own complaints                           | System                 |
| BR-03 | Workers access assigned complaints                             | System                 |
| BR-04 | Department admins manage department complaints                 | System                 |
| BR-05 | System admin has system-wide authority                         | System                 |
| BR-06 | User accounts must be unique                                   | System                 |
| BR-07 | Protected functions require authentication                     | System                 |
| BR-08 | Deactivated accounts cannot perform normal operations          | System                 |
| BR-09 | Complaint belongs to submitting citizen                        | System                 |
| BR-10 | Required complaint information must be present                 | System                 |
| BR-11 | Every complaint receives a unique ID                           | System                 |
| BR-12 | Submission timestamp is system-generated                       | System                 |
| BR-13 | AI output is a suggestion                                      | AI/System              |
| BR-14 | Citizen confirms final description                             | Citizen                |
| BR-15 | AI failure allows manual description                           | System                 |
| BR-16 | AI output requires validation                                  | System                 |
| BR-17 | Every complaint has a category                                 | System                 |
| BR-18 | Categories map to departments                                  | System Admin           |
| BR-19 | Unclassified complaints require review                         | Department Admin       |
| BR-20 | Complaint requires location                                    | Citizen/System         |
| BR-21 | Citizen confirms location                                      | Citizen                |
| BR-22 | Poor GPS accuracy may require retry                            | System                 |
| BR-23 | Location access is restricted                                  | System                 |
| BR-24 | New complaints start as Submitted                              | System                 |
| BR-25 | Department must verify complaints                              | Department Admin       |
| BR-26 | Valid complaints become Verified                               | Department Admin       |
| BR-27 | Invalid complaints can be Rejected                             | Department Admin       |
| BR-28 | Rejected complaints cannot be assigned                         | System                 |
| BR-29 | Only verified complaints can be assigned                       | System                 |
| BR-30 | Worker must belong to responsible department                   | System                 |
| BR-31 | Worker availability should be considered                       | Department Admin       |
| BR-32 | Assignments are recorded                                       | System                 |
| BR-33 | Workers receive assignment notifications                       | System                 |
| BR-34 | Complaints can be reassigned                                   | Department Admin       |
| BR-35 | Worker App shows assigned complaints                           | Worker                 |
| BR-36 | Worker can accept assignments                                  | Worker                 |
| BR-37 | Worker can update work status                                  | Worker                 |
| BR-38 | Worker cannot directly complete complaints                     | System                 |
| BR-39 | Resolution requires evidence                                   | Worker                 |
| BR-40 | Resolution requires department verification                    | System                 |
| BR-41 | Admin reviews resolution                                       | Department Admin       |
| BR-42 | Approved resolution completes complaint                        | Department Admin       |
| BR-43 | Rejected resolution returns to In Progress                     | Department Admin       |
| BR-44 | Complaint follows defined lifecycle                            | System                 |
| BR-45 | Invalid complaints can be rejected                             | Department Admin       |
| BR-46 | Rejected resolutions return to work                            | Department Admin       |
| BR-47 | Unauthorized status changes are prohibited                     | System                 |
| BR-48 | SLA can be defined per complaint category                      | System Admin           |
| BR-49 | SLA deadline is system-calculated                              | System                 |
| BR-50 | SLA is monitored                                               | System                 |
| BR-51 | Exceeded SLA is recorded as breached                           | System                 |
| BR-52 | SLA breach does not complete a complaint                       | System                 |
| BR-53 | Potential duplicates may be detected                           | System                 |
| BR-54 | Duplicate flags require review                                 | Department Admin       |
| BR-55 | Complaint submission generates confirmation                    | System                 |
| BR-56 | Important status changes generate notifications                | System                 |
| BR-57 | Notification failure does not cancel operations                | System                 |
| BR-58 | Workers receive assignment notifications                       | System                 |
| BR-59 | Workers receive reassignment notifications                     | System                 |
| BR-60 | Department data is isolated                                    | System                 |
| BR-61 | Department admins manage their workers                         | Department Admin       |
| BR-62 | Assignment authority belongs to admins                         | Department Admin       |
| BR-63 | Resolution approval belongs to admins                          | Department Admin       |
| BR-64 | Departments are managed by system admin                        | System Admin           |
| BR-65 | Category-department mapping is configurable                    | System Admin           |
| BR-66 | Staff can be associated with departments                       | System Admin           |
| BR-67 | System admin can monitor all departments                       | System Admin           |
| BR-68 | Feedback requires completed complaint                          | Citizen                |
| BR-69 | Feedback belongs to complaint and citizen                      | System                 |
| BR-70 | Feedback modification follows configured policy                | System                 |
| BR-71 | Status changes are audited                                     | System                 |
| BR-72 | Assignments are audited                                        | System                 |
| BR-73 | Resolutions are audited                                        | System                 |
| BR-74 | Audit records are protected                                    | System                 |
| BR-75 | Only supported image files are accepted                        | System                 |
| BR-76 | Uploaded files are validated                                   | System                 |
| BR-77 | Resolution images belong to resolution record                  | System                 |
| BR-78 | Images require authorized access                               | System                 |
| BR-79 | Complaint data follows retention policy                        | System                 |
| BR-80 | Historical complaints remain available                         | System                 |
| BR-81 | Account deletion does not automatically erase required history | System                 |
| BR-82 | Department admins access department reports                    | Department Admin       |
| BR-83 | System admins access system-wide reports                       | System Admin           |
| BR-84 | Reports include operational performance metrics                | System                 |
