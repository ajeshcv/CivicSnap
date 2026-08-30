# CivicSnap — User Stories

## 1. Introduction

This document defines the user stories for CivicSnap. Each user story describes a requirement from the perspective of a system user and follows the format:

> **As a [user], I want [function], so that [benefit].**

CivicSnap has four primary user roles:

* **Citizen**
* **Worker**
* **Department Administrator**
* **System Administrator**

---

# 2. Citizen User Stories

## US-01: Register Account

**As a citizen,**
I want to create an account,
**so that** I can submit and track civic complaints.

### Acceptance Criteria

* Citizen can enter required registration details.
* System validates the provided information.
* Duplicate accounts are prevented.
* Successfully registered users can log in.

---

## US-02: Login

**As a citizen,**
I want to log in securely,
**so that** I can access my account and complaints.

### Acceptance Criteria

* Valid credentials allow access.
* Invalid credentials display an error.
* User session is securely maintained.
* User can log out.

---

## US-03: Capture Civic Issue

**As a citizen,**
I want to capture a photograph of a civic issue using my phone camera,
**so that** I can report the problem visually.

### Acceptance Criteria

* Camera can be opened from the complaint screen.
* Citizen can capture an image.
* Citizen can preview the image.
* Citizen can retake the image.

---

## US-04: Upload Complaint Image

**As a citizen,**
I want to upload the captured image,
**so that** the system can process my complaint.

### Acceptance Criteria

* Image upload is supported.
* Invalid files are rejected.
* Upload progress or status is displayed.
* Uploaded image is associated with the complaint.

---

## US-05: Generate Complaint Description Using AI

**As a citizen,**
I want AI to analyze my uploaded image and generate a description,
**so that** I do not have to manually write the entire complaint.

### Acceptance Criteria

* AI receives the uploaded image.
* AI generates a suggested description.
* Generated description is displayed to the citizen.
* AI failure is handled gracefully.

---

## US-06: Edit AI Description

**As a citizen,**
I want to edit the AI-generated description,
**so that** I can correct inaccurate or incomplete information.

### Acceptance Criteria

* Citizen can edit the generated description.
* Citizen can add additional information.
* Citizen can confirm the final description.
* The confirmed description is stored with the complaint.

---

## US-07: Capture Complaint Location

**As a citizen,**
I want my complaint location to be captured using GPS,
**so that** the responsible department can identify where the issue exists.

### Acceptance Criteria

* Application requests location permission.
* GPS coordinates are captured.
* Location is associated with the complaint.
* Citizen can verify the captured location.

---

## US-08: Submit Complaint

**As a citizen,**
I want to submit my complaint after reviewing its details,
**so that** it can be processed by the appropriate department.

### Acceptance Criteria

* Image, description, and location are available before submission.
* Citizen can review the complaint.
* System generates a unique complaint ID.
* Complaint receives an initial status.

---

## US-09: View Complaint Status

**As a citizen,**
I want to view the status of my complaint,
**so that** I know how the issue is progressing.

### Acceptance Criteria

* Current status is clearly displayed.
* Status is updated when workflow events occur.
* Citizen can view important status changes.

---

## US-10: View Complaint History

**As a citizen,**
I want to view my previous complaints,
**so that** I can keep track of issues I have reported.

### Acceptance Criteria

* All complaints submitted by the citizen are listed.
* Complaints can be opened individually.
* Complaint details are displayed.
* Current status is visible.

---

## US-11: View Resolution

**As a citizen,**
I want to see how my complaint was resolved,
**so that** I can verify the outcome.

### Acceptance Criteria

* Completed complaints show resolution information.
* Resolution proof is displayed where permitted.
* Final complaint status is visible.

---

## US-12: Provide Feedback

**As a citizen,**
I want to provide feedback after my complaint is resolved,
**so that** I can evaluate the quality of the resolution.

### Acceptance Criteria

* Feedback is available after completion.
* Citizen can submit a rating/comment.
* Feedback is linked to the corresponding complaint.

---

# 3. Worker User Stories

## US-13: Worker Login

**As a worker,**
I want to securely log in to the worker application,
**so that** I can access my assigned complaints.

### Acceptance Criteria

* Worker can authenticate using valid credentials.
* Invalid credentials are rejected.
* Worker can securely log out.

---

## US-14: View Assigned Complaints

**As a worker,**
I want to see complaints assigned to me,
**so that** I know which civic issues I need to resolve.

### Acceptance Criteria

* Assigned complaints are displayed.
* Complaint priority and status are visible.
* Worker can open individual complaints.

---

## US-15: View Complaint Location

**As a worker,**
I want to view the complaint location on a map,
**so that** I can travel to the reported location.

### Acceptance Criteria

* Complaint coordinates are displayed.
* Location can be viewed on a map.
* Worker can access relevant navigation functionality.

---

## US-16: View Complaint Details

**As a worker,**
I want to view the complaint image and description,
**so that** I understand the issue before visiting the location.

### Acceptance Criteria

* Original complaint image is displayed.
* Complaint description is displayed.
* Complaint category is displayed.
* Relevant SLA information is displayed.

---

## US-17: Accept Assignment

**As a worker,**
I want to accept an assigned complaint,
**so that** the department knows I have started handling it.

### Acceptance Criteria

* Worker can accept an assignment.
* Complaint status is updated.
* Acceptance time is recorded.

---

## US-18: Update Work Status

**As a worker,**
I want to update the status of my assigned complaint,
**so that** the department can track my progress.

### Acceptance Criteria

* Worker can mark work as in progress.
* Status changes are recorded.
* Department administrator can see the updated status.

---

## US-19: Upload Resolution Proof

**As a worker,**
I want to upload photographs showing the completed work,
**so that** the department can verify that the issue has been resolved.

### Acceptance Criteria

* Worker can capture or upload resolution images.
* Worker can add resolution notes.
* Proof is associated with the complaint.
* Upload status is displayed.

---

## US-20: Submit Resolution

**As a worker,**
I want to submit the completed work for verification,
**so that** the department can approve the resolution.

### Acceptance Criteria

* Worker can submit resolution details.
* Complaint moves to `Resolution Under Verification`.
* Worker cannot directly mark the complaint as finally completed.

---

## US-21: View Work History

**As a worker,**
I want to view my completed and previous assignments,
**so that** I can track my work history.

### Acceptance Criteria

* Previous assignments are displayed.
* Completed assignments can be viewed.
* Relevant complaint and resolution information is available.

---

# 4. Department Administrator User Stories

## US-22: Department Admin Login

**As a department administrator,**
I want to securely log in to the administration portal,
**so that** I can manage complaints belonging to my department.

### Acceptance Criteria

* Administrator authentication is required.
* Unauthorized users cannot access the dashboard.
* Administrator can securely log out.

---

## US-23: View Department Dashboard

**As a department administrator,**
I want to view a dashboard of complaints,
**so that** I can monitor the department's workload.

### Acceptance Criteria

* Dashboard displays complaint statistics.
* Complaints can be filtered by status.
* Pending and completed complaints are visible.
* SLA information is available.

---

## US-24: Review New Complaint

**As a department administrator,**
I want to review submitted complaints,
**so that** I can determine whether they are valid and actionable.

### Acceptance Criteria

* Administrator can view complaint image.
* Administrator can view description.
* Administrator can view location.
* Administrator can verify or reject the complaint.

---

## US-25: Verify Complaint

**As a department administrator,**
I want to verify a complaint,
**so that** only valid complaints proceed to field work.

### Acceptance Criteria

* Administrator can approve a complaint.
* Complaint status changes to `Verified`.
* Verification action is recorded.

---

## US-26: Reject Complaint

**As a department administrator,**
I want to reject invalid complaints,
**so that** incorrect or unusable complaints do not consume department resources.

### Acceptance Criteria

* Administrator can reject a complaint.
* Administrator can provide a rejection reason.
* Complaint status changes to `Rejected`.
* Citizen can view the rejection status.

---

## US-27: Manage Workers

**As a department administrator,**
I want to manage workers in my department,
**so that** I can maintain an up-to-date list of available field staff.

### Acceptance Criteria

* Administrator can view workers.
* Administrator can add workers where permitted.
* Administrator can update worker information.
* Administrator can activate/deactivate workers.

---

## US-28: View Worker Availability

**As a department administrator,**
I want to see worker availability and workload,
**so that** I can assign complaints effectively.

### Acceptance Criteria

* Available workers are identifiable.
* Current assignments can be viewed.
* Worker workload is visible.

---

## US-29: Assign Complaint to Worker

**As a department administrator,**
I want to assign verified complaints to workers,
**so that** civic issues can be handled in the field.

### Acceptance Criteria

* Only verified complaints can be assigned.
* Available workers can be selected.
* Assignment is recorded.
* Worker receives an assignment notification.

---

## US-30: Monitor Complaint Progress

**As a department administrator,**
I want to monitor complaints assigned to workers,
**so that** I can ensure issues are resolved on time.

### Acceptance Criteria

* Administrator can view current complaint status.
* Assigned worker is displayed.
* Work progress is visible.
* SLA information is available.

---

## US-31: Monitor SLA

**As a department administrator,**
I want to identify complaints approaching or exceeding their SLA,
**so that** I can take corrective action.

### Acceptance Criteria

* SLA deadline is calculated.
* Approaching deadlines are identifiable.
* Breached complaints are highlighted.
* SLA information is available on the dashboard.

---

## US-32: Verify Resolution

**As a department administrator,**
I want to review the worker's resolution proof,
**so that** I can confirm that the civic issue has actually been resolved.

### Acceptance Criteria

* Administrator can view resolution images.
* Administrator can view resolution notes.
* Administrator can approve or reject the resolution.

---

## US-33: Approve Resolution

**As a department administrator,**
I want to approve a valid resolution,
**so that** the complaint can be officially completed.

### Acceptance Criteria

* Administrator can approve the resolution.
* Complaint status changes to `Completed`.
* Completion time is recorded.
* Citizen can see the completed status.

---

## US-34: Reject Resolution

**As a department administrator,**
I want to reject insufficient resolution proof,
**so that** unresolved issues can be sent back for further work.

### Acceptance Criteria

* Administrator can reject the resolution.
* Administrator can provide a reason.
* Complaint returns to `In Progress`.
* Worker can see the rejection.

---

## US-35: View Department Reports

**As a department administrator,**
I want to view complaint and worker performance reports,
**so that** I can evaluate department performance.

### Acceptance Criteria

* Reports can be generated.
* Reports include complaint statistics.
* Resolution and SLA information is included.
* Worker performance information can be viewed where permitted.

---

# 5. System Administrator User Stories

## US-36: System Admin Login

**As a system administrator,**
I want to securely log in to the system administration portal,
**so that** I can manage the overall CivicSnap platform.

### Acceptance Criteria

* Admin authentication is required.
* Unauthorized users are denied access.
* Admin can securely log out.

---

## US-37: Manage Departments

**As a system administrator,**
I want to create and manage departments,
**so that** complaints can be routed to the appropriate government department.

### Acceptance Criteria

* Administrator can create departments.
* Administrator can update department information.
* Administrator can activate/deactivate departments.
* Complaint categories can be associated with departments.

---

## US-38: Manage Department Administrators

**As a system administrator,**
I want to manage department administrator accounts,
**so that** each department has authorized personnel to manage complaints.

### Acceptance Criteria

* Administrator can create administrator accounts.
* Administrators can be assigned to departments.
* Administrator accounts can be activated/deactivated.

---

## US-39: Manage Workers

**As a system administrator,**
I want to manage worker accounts across departments,
**so that** the system maintains accurate workforce information.

### Acceptance Criteria

* Worker accounts can be created.
* Workers can be assigned to departments.
* Worker information can be updated.
* Worker accounts can be activated/deactivated.

---

## US-40: Manage Users

**As a system administrator,**
I want to manage citizen accounts,
**so that** I can maintain the integrity and security of the platform.

### Acceptance Criteria

* Citizen accounts can be viewed.
* Authorized administrators can activate/deactivate accounts.
* User information is protected according to access permissions.

---

## US-41: Monitor All Complaints

**As a system administrator,**
I want to view complaints across all departments,
**so that** I can monitor the overall performance of the civic complaint system.

### Acceptance Criteria

* Complaints from all departments can be viewed.
* Complaints can be filtered by department.
* Complaints can be filtered by status.
* SLA information is available.

---

## US-42: Generate System Reports

**As a system administrator,**
I want to generate system-wide reports,
**so that** I can evaluate CivicSnap's overall performance.

### Acceptance Criteria

Reports may include:

* Complaints by department
* Complaints by category
* Resolution time
* SLA breaches
* Worker performance
* Department performance
* Citizen feedback

---

# 6. General System User Stories

## US-43: Receive Notifications

**As a user,**
I want to receive notifications about important complaint events,
**so that** I remain informed about changes.

### Acceptance Criteria

Notifications may be generated for:

* Complaint submission
* Complaint verification
* Complaint rejection
* Worker assignment
* Work started
* Resolution submission
* Resolution approval
* Resolution rejection
* Complaint completion

---

## US-44: Search Complaints

**As an authorized user,**
I want to search for complaints,
**so that** I can quickly find a specific complaint.

### Acceptance Criteria

* Complaint ID can be searched.
* Authorized users can filter complaints.
* Search results display relevant complaint information.

---

## US-45: Maintain Audit Trail

**As a system administrator,**
I want important system actions to be logged,
**so that** activities can be traced when required.

### Acceptance Criteria

* Important actions are logged.
* User identity is recorded.
* Date and time are recorded.
* Complaint-related actions can be traced.

---

# 7. Complaint Lifecycle

The user stories support the following complete complaint lifecycle:

```text
Citizen
   │
   ├── Capture Image
   │
   ├── AI Generates Description
   │
   ├── Review/Edit Description
   │
   ├── Capture GPS Location
   │
   └── Submit Complaint
            │
            ▼
       Submitted
            │
            ▼
   Department Administrator
            │
       Verify Complaint
          /       \
       Reject     Verify
         │          │
         ▼          ▼
      Rejected    Verified
                     │
                     ▼
              Assign Worker
                     │
                     ▼
                  Worker
                     │
               Accept Work
                     │
                     ▼
                 In Progress
                     │
              Resolve Issue
                     │
              Upload Proof
                     │
                     ▼
       Resolution Under Verification
                /           \
             Reject        Approve
               │              │
               ▼              ▼
          In Progress      Completed
                              │
                              ▼
                           Citizen
                              │
                       View Resolution
                              │
                       Provide Feedback
```

---

# 8. User Story Summary

| ID    | User Role        | User Story                       |
| ----- | ---------------- | -------------------------------- |
| US-01 | Citizen          | Register account                 |
| US-02 | Citizen          | Login                            |
| US-03 | Citizen          | Capture civic issue              |
| US-04 | Citizen          | Upload complaint image           |
| US-05 | Citizen          | Generate AI description          |
| US-06 | Citizen          | Edit AI description              |
| US-07 | Citizen          | Capture GPS location             |
| US-08 | Citizen          | Submit complaint                 |
| US-09 | Citizen          | View complaint status            |
| US-10 | Citizen          | View complaint history           |
| US-11 | Citizen          | View resolution                  |
| US-12 | Citizen          | Provide feedback                 |
| US-13 | Worker           | Worker login                     |
| US-14 | Worker           | View assigned complaints         |
| US-15 | Worker           | View complaint location          |
| US-16 | Worker           | View complaint details           |
| US-17 | Worker           | Accept assignment                |
| US-18 | Worker           | Update work status               |
| US-19 | Worker           | Upload resolution proof          |
| US-20 | Worker           | Submit resolution                |
| US-21 | Worker           | View work history                |
| US-22 | Department Admin | Department admin login           |
| US-23 | Department Admin | View department dashboard        |
| US-24 | Department Admin | Review new complaint             |
| US-25 | Department Admin | Verify complaint                 |
| US-26 | Department Admin | Reject complaint                 |
| US-27 | Department Admin | Manage workers                   |
| US-28 | Department Admin | View worker availability         |
| US-29 | Department Admin | Assign complaint                 |
| US-30 | Department Admin | Monitor complaint progress       |
| US-31 | Department Admin | Monitor SLA                      |
| US-32 | Department Admin | Verify resolution                |
| US-33 | Department Admin | Approve resolution               |
| US-34 | Department Admin | Reject resolution                |
| US-35 | Department Admin | View department reports          |
| US-36 | System Admin     | System admin login               |
| US-37 | System Admin     | Manage departments               |
| US-38 | System Admin     | Manage department administrators |
| US-39 | System Admin     | Manage workers                   |
| US-40 | System Admin     | Manage users                     |
| US-41 | System Admin     | Monitor all complaints           |
| US-42 | System Admin     | Generate system reports          |
| US-43 | All Roles        | Receive notifications            |
| US-44 | Authorized Users | Search complaints                |
| US-45 | System Admin     | Maintain audit trail             |
