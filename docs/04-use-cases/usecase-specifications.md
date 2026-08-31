# CivicSnap — Use Case Specifications

## 1. Introduction

This document defines the detailed use case specifications for the CivicSnap civic complaint management system.

The use cases describe how the four primary actors interact with the system

 Citizen
 Worker through the Worker App
 Department Administrator
 System Administrator

The use cases cover the complete complaint lifecycle, from reporting a civic issue through final resolution verification and feedback.

---

# 2. Actors

## 2.1 Citizen

The citizen uses the Citizen App to

 Register and log in
 Report civic issues
 Capture complaint images
 Review AI-generated descriptions
 Capture complaint location
 Submit complaints
 Track complaints
 View resolutions
 Provide feedback

---

## 2.2 Worker

The worker interacts with CivicSnap through the Worker App.

The worker uses the application to

 Log in
 View assigned complaints
 View complaint details
 View complaint location
 Accept assignments
 Update work status
 Resolve civic issues
 Upload resolution proof
 Submit resolutions for verification

---

## 2.3 Department Administrator

The Department Administrator manages complaints belonging to the assigned department.

The administrator can

 Review complaints
 Verify or reject complaints
 Manage workers
 Assignreassign complaints
 Monitor complaint progress
 Monitor SLA
 Verify resolutions
 Approvereject resolutions
 Generate department reports

---

## 2.4 System Administrator

The System Administrator manages the overall CivicSnap platform.

The administrator can

 Manage users
 Manage workers
 Manage departments
 Manage department administrators
 Manage complaint categories
 Configure category-department mappings
 Monitor system-wide complaints
 Generate system-wide reports

---

# 3. Use Case Diagram Overview

The overall use case structure is

```text
                         CIVICSNAP
                            │
        ┌───────────────────┼────────────────────┐
        │                   │                    │
        ▼                   ▼                    ▼
     Citizen             Worker            Administrators
        │             (Worker App)                │
        │                   │              ┌──────┴──────┐
        │                   │              │             │
        │                   │              ▼             ▼
        │                   │        Department       System
        │                   │           Admin          Admin
        │                   │
        ▼                   ▼
  Report Issue        Handle Assignment
        │                   │
        ▼                   ▼
  Track Complaint     Resolve Complaint
        │                   │
        └──────────┬────────┘
                   ▼
             Complaint Lifecycle
```

---

# 4. Use Case Identification

 ID     Use Case                          Primary Actor      
 -----  --------------------------------  ------------------ 
 UC-01  Register Account                  Citizen            
 UC-02  Login                             All Users          
 UC-03  Manage Profile                    Citizen            
 UC-04  Submit Complaint                  Citizen            
 UC-05  Capture Complaint Image           Citizen            
 UC-06  Generate AI Description           SystemAI          
 UC-07  ReviewEdit AI Description        Citizen            
 UC-08  Capture Complaint Location        Citizen            
 UC-09  Track Complaint                   Citizen            
 UC-10  View Complaint History            Citizen            
 UC-11  View Resolution                   Citizen            
 UC-12  Provide Feedback                  Citizen            
 UC-13  View Assigned Complaints          Worker             
 UC-14  View Complaint Details            Worker             
 UC-15  View Complaint Location           Worker             
 UC-16  Accept Assignment                 Worker             
 UC-17  Update Work Status                Worker             
 UC-18  Submit Resolution                 Worker             
 UC-19  Upload Resolution Proof           Worker             
 UC-20  View Worker History               Worker             
 UC-21  Review Complaint                  Department Admin   
 UC-22  Verify Complaint                  Department Admin   
 UC-23  Reject Complaint                  Department Admin   
 UC-24  Manage Workers                    Department Admin   
 UC-25  View Worker Availability          Department Admin   
 UC-26  Assign Complaint                  Department Admin   
 UC-27  Reassign Complaint                Department Admin   
 UC-28  Monitor Complaint Progress        Department Admin   
 UC-29  Monitor SLA                       Department Admin   
 UC-30  Verify Resolution                 Department Admin   
 UC-31  Approve Resolution                Department Admin   
 UC-32  Reject Resolution                 Department Admin   
 UC-33  Generate Department Report        Department Admin   
 UC-34  Manage Departments                System Admin       
 UC-35  Manage Complaint Categories       System Admin       
 UC-36  Manage Department Administrators  System Admin       
 UC-37  Manage System Users               System Admin       
 UC-38  Manage Workers System-Wide        System Admin       
 UC-39  Monitor System Complaints         System Admin       
 UC-40  Generate System Report            System Admin       
 UC-41  Receive Notification              All Relevant Users 
 UC-42  Maintain Audit Trail              System             

---

# 5. Citizen Use Cases

## UC-01 Register Account

Primary Actor Citizen

Goal Create a CivicSnap account.

### Preconditions

 Citizen has access to the Citizen App.
 Citizen is not already registered with the same required unique identifier.

### Main Flow

1. Citizen opens the registration screen.
2. Citizen enters required information.
3. System validates the information.
4. System checks for an existing account.
5. System creates the account.
6. System confirms successful registration.

### Alternative Flows

A1 Invalid information

1. System detects invalid registration data.
2. System displays an appropriate error.
3. Citizen corrects the information.

A2 Existing account

1. System detects an existing account.
2. System informs the citizen.
3. Citizen is directed to login.

### Postconditions

A valid citizen account is created.

---

# 6. UC-02 Login

Primary Actor Citizen  Worker  Department Admin  System Admin

Goal Authenticate the user.

### Preconditions

 User has a registered and active account.

### Main Flow

1. User enters credentials.
2. System validates credentials.
3. System identifies the user's role.
4. System creates an authenticated sessiontoken.
5. System redirects the user to the appropriate interface.

### Alternative Flows

A1 Invalid credentials

1. System rejects authentication.
2. System displays an error.
3. User may retry.

### Postconditions

The authenticated user receives access according to their role.

---

# 7. UC-03 Manage Profile

Primary Actor Citizen

Goal View or update profile information.

### Preconditions

 Citizen is authenticated.

### Main Flow

1. Citizen opens the profile.
2. System displays profile information.
3. Citizen modifies permitted information.
4. Citizen submits changes.
5. System validates the changes.
6. System saves the updated information.

### Postconditions

Citizen profile information is updated.

---

# 8. UC-04 Submit Complaint

Primary Actor Citizen

Goal Submit a civic issue to CivicSnap.

### Preconditions

 Citizen is authenticated.
 Required complaint information is available.

### Main Flow

1. Citizen selects the complaintreport option.
2. Citizen captures or selects an image.
3. System uploads the image.
4. System requests AI analysis.
5. AI generates a suggested description.
6. Citizen reviews and edits the description.
7. System captures the complaint location.
8. Citizen reviews complaint information.
9. Citizen submits the complaint.
10. System validates the complaint.
11. System creates a unique complaint ID.
12. System assigns the initial status `Submitted`.
13. System determines the complaint category and responsible department.
14. System routes the complaint to the appropriate department.
15. System sends a submission confirmation.

### Alternative Flows

A1 AI service unavailable

1. System informs the citizen.
2. Citizen manually enters the description.
3. Complaint submission continues.

A2 GPS unavailable

1. System informs the citizen.
2. Citizen retries location capture.
3. If permitted by configured rules, an alternative location entry method may be used.

A3 Missing information

1. System identifies missing required information.
2. System prevents submission.
3. Citizen provides the missing information.

### Postconditions

A complaint is successfully created and routed to the appropriate department.

---

# 9. UC-05 Capture Complaint Image

Primary Actor Citizen

Goal Capture visual evidence of a civic issue.

### Preconditions

 Citizen is authenticated.
 Camera permission is available.

### Main Flow

1. Citizen opens the complaint camera.
2. Application activates the device camera.
3. Citizen captures an image.
4. Application displays a preview.
5. Citizen accepts the image.

### Alternative Flow

A1 Retake image

Citizen rejects the image and captures another image.

### Postconditions

The complaint image is available for upload.

---

# 10. UC-06 Generate AI Description

Primary Actor System

Supporting Actor AI Service

Goal Generate a suggested description from the complaint image.

### Preconditions

 Complaint image has been uploaded.
 AI service is available.

### Main Flow

1. Backend sends the image to the AI service.
2. AI service analyzes the image.
3. AI service returns a suggested description.
4. Backend validates the response.
5. Suggested description is returned to the Citizen App.

### Alternative Flow

A1 AI service failure

1. AI service fails or times out.
2. Backend records the failure.
3. Citizen is allowed to enter a description manually.

### Postconditions

A suggested description is available for citizen review.

---

# 11. UC-07 ReviewEdit AI Description

Primary Actor Citizen

Goal Confirm the accuracy of the AI-generated description.

### Main Flow

1. Citizen views the AI-generated description.
2. Citizen reviews the description.
3. Citizen edits the description if necessary.
4. Citizen confirms the final description.
5. System stores the citizen-confirmed description.

### Postconditions

The complaint contains a citizen-confirmed final description.

---

# 12. UC-08 Capture Complaint Location

Primary Actor Citizen

Goal Associate the complaint with its physical location.

### Main Flow

1. Application requests location permission if required.
2. Device provides location information.
3. Application obtains latitude and longitude.
4. System records available accuracy information.
5. Citizen reviews the location.
6. Citizen confirms the location.

### Alternative Flow

A1 Permission denied

The system informs the citizen that location access is required or provides an allowed alternative according to configured rules.

### Postconditions

Complaint location information is available.

---

# 13. UC-09 Track Complaint

Primary Actor Citizen

Goal View the current progress of a submitted complaint.

### Preconditions

 Citizen is authenticated.
 Citizen has submitted at least one complaint.

### Main Flow

1. Citizen opens the complaint list.
2. Citizen selects a complaint.
3. System retrieves the latest complaint status.
4. System displays the status and relevant progress information.

### Postconditions

Citizen understands the current state of the complaint.

---

# 14. UC-10 View Complaint History

Primary Actor Citizen

Goal View previously submitted complaints.

### Main Flow

1. Citizen opens complaint history.
2. System retrieves complaints belonging to the citizen.
3. System displays complaint summaries.
4. Citizen selects a complaint to view details.

### Postconditions

Citizen can review their complaint history.

---

# 15. UC-11 View Resolution

Primary Actor Citizen

Goal View the outcome of a completed complaint.

### Preconditions

 Complaint has status `Completed`.

### Main Flow

1. Citizen opens the completed complaint.
2. System displays resolution details.
3. System displays resolution evidence where permitted.
4. Citizen reviews the result.

### Postconditions

Citizen can see how the complaint was resolved.

---

# 16. UC-12 Provide Feedback

Primary Actor Citizen

Goal Provide feedback about a completed complaint.

### Preconditions

 Complaint is `Completed`.

### Main Flow

1. Citizen opens the completed complaint.
2. Citizen selects feedback.
3. Citizen provides a rating andor comment.
4. System validates the feedback.
5. System stores the feedback.

### Postconditions

Feedback is associated with the complaint and citizen.

---

# 17. Worker App Use Cases

# UC-13 View Assigned Complaints

Primary Actor Worker

Interface Worker App

Goal View complaints assigned to the worker.

### Preconditions

 Worker is authenticated.
 Worker has one or more assignments.

### Main Flow

1. Worker opens the Worker App.
2. System authenticates the worker.
3. System retrieves active assignments.
4. Worker views assigned complaints.
5. Worker selects a complaint.

### Postconditions

Worker can access assigned complaint information.

---

# 18. UC-14 View Complaint Details

Primary Actor Worker

Interface Worker App

### Main Flow

1. Worker selects an assigned complaint.
2. System retrieves complaint details.
3. Worker views

    Complaint image
    Description
    Category
    Location
    Submission time
    SLA information
4. Worker reviews the information.

### Postconditions

Worker understands the issue to be handled.

---

# 19. UC-15 View Complaint Location

Primary Actor Worker

Interface Worker App

### Main Flow

1. Worker opens complaint details.
2. Worker selects the location.
3. System displays complaint coordinatesmap.
4. Worker can use available navigation functionality.

### Postconditions

Worker can identify where the issue is located.

---

# 20. UC-16 Accept Assignment

Primary Actor Worker

Interface Worker App

### Preconditions

 Complaint is assigned to the worker.
 Worker account is active.

### Main Flow

1. Worker opens the assignment.
2. Worker reviews the complaint.
3. Worker accepts the assignment.
4. System records the acceptance.
5. System updates the assignmentwork status.

### Postconditions

The assignment is accepted by the worker.

---

# 21. UC-17 Update Work Status

Primary Actor Worker

Interface Worker App

### Main Flow

1. Worker opens an accepted assignment.
2. Worker begins field work.
3. Worker changes the status to `In Progress`.
4. System validates the transition.
5. System records the status change.

### Postconditions

Department administrators can see the updated progress.

---

# 22. UC-18 Submit Resolution

Primary Actor Worker

Interface Worker App

### Preconditions

 Worker has handled the assigned complaint.
 Required resolution information is available.

### Main Flow

1. Worker opens the complaint.
2. Worker enters resolution details.
3. Worker uploads resolution evidence.
4. Worker submits the resolution.
5. System validates the submission.
6. System changes the complaint to `Resolution Under Verification`.
7. Department administrator is notified.

### Postconditions

Resolution is awaiting department verification.

---

# 23. UC-19 Upload Resolution Proof

Primary Actor Worker

Interface Worker App

### Main Flow

1. Worker selects resolution proof.
2. Worker captures or uploads photographs.
3. System validates the files.
4. System uploads the evidence.
5. System associates the evidence with the resolution.

### Postconditions

Resolution evidence is stored securely.

---

# 24. UC-20 View Worker History

Primary Actor Worker

Interface Worker App

### Main Flow

1. Worker opens work history.
2. System retrieves previous assignments.
3. Worker views completed and previous work.

### Postconditions

Worker can review historical assignments.

---

# 25. Department Administrator Use Cases

# UC-21 Review Complaint

Primary Actor Department Administrator

### Preconditions

 Administrator is authenticated.
 Complaint belongs to the administrator's department.

### Main Flow

1. Administrator opens the department dashboard.
2. Administrator views new complaints.
3. Administrator selects a complaint.
4. System displays

    Image
    Description
    Category
    Location
    Submission information
5. Administrator reviews the complaint.

### Postconditions

Administrator can make a verification decision.

---

# 26. UC-22 Verify Complaint

Primary Actor Department Administrator

### Preconditions

 Complaint is in `Submitted` status.

### Main Flow

1. Administrator reviews the complaint.
2. Administrator confirms that the complaint is valid.
3. Administrator selects verify.
4. System changes status to `Verified`.
5. System records administrator, timestamp, and action.
6. Complaint becomes eligible for assignment.

### Postconditions

Complaint is verified.

---

# 27. UC-23 Reject Complaint

Primary Actor Department Administrator

### Preconditions

 Complaint is awaiting verification.

### Main Flow

1. Administrator reviews the complaint.
2. Administrator determines that the complaint is invalid or cannot be acted upon.
3. Administrator selects reject.
4. Administrator provides a reason where required.
5. System changes status to `Rejected`.
6. System records the decision.
7. Citizen is notified.

### Postconditions

Complaint is rejected and cannot be assigned.

---

# 28. UC-24 Manage Workers

Primary Actor Department Administrator

### Main Flow

1. Administrator opens worker management.
2. System displays department workers.
3. Administrator views or updates permitted worker information.
4. Administrator activatesdeactivates workers where authorized.
5. System records changes.

### Postconditions

Department worker information is updated.

---

# 29. UC-25 View Worker Availability

Primary Actor Department Administrator

### Main Flow

1. Administrator opens worker availability.
2. System retrieves worker status.
3. System displays

    Available
    Assigned
    Busy
    Offline
4. Administrator reviews worker workload.

### Postconditions

Administrator can make an informed assignment decision.

---

# 30. UC-26 Assign Complaint

Primary Actor Department Administrator

### Preconditions

 Complaint is `Verified`.
 Worker belongs to the responsible department.
 Worker is eligible for assignment.

### Main Flow

1. Administrator selects a verified complaint.
2. System displays eligible workers.
3. Administrator selects a worker.
4. Administrator confirms the assignment.
5. System creates an assignment record.
6. Complaint status changes to `Assigned`.
7. Worker receives a notification.

### Alternative Flow

A1 No suitable worker

1. System indicates that no eligible worker is available.
2. Complaint remains unassigned.
3. Administrator can retry later.

### Postconditions

Complaint is assigned to a worker.

---

# 31. UC-27 Reassign Complaint

Primary Actor Department Administrator

### Preconditions

 Complaint has an existing assignment.

### Main Flow

1. Administrator opens the assigned complaint.
2. Administrator selects reassign.
3. System displays eligible workers.
4. Administrator selects a new worker.
5. System records the reassignment.
6. Previous assignment is retained in history.
7. New worker is notified.

### Postconditions

Complaint is assigned to the new worker.

---

# 32. UC-28 Monitor Complaint Progress

Primary Actor Department Administrator

### Main Flow

1. Administrator opens the department dashboard.
2. System displays active complaints.
3. Administrator filters or searches complaints.
4. Administrator views

    Current status
    Assigned worker
    SLA
    Submission time
    Progress
5. Administrator takes action when required.

### Postconditions

Department administrator has visibility into active work.

---

# 33. UC-29 Monitor SLA

Primary Actor Department Administrator

### Main Flow

1. System calculates complaint SLA deadlines.
2. System compares active complaints with deadlines.
3. Administrator views approaching deadlines.
4. Administrator views breached complaints.
5. Administrator takes corrective action where necessary.

### Postconditions

SLA-related information is available for management.

---

# 34. UC-30 Verify Resolution

Primary Actor Department Administrator

### Preconditions

 Worker has submitted a resolution.
 Complaint is `Resolution Under Verification`.

### Main Flow

1. Administrator opens the resolution.
2. System displays original complaint information.
3. System displays worker resolution information.
4. System displays resolution proof.
5. Administrator reviews the evidence.
6. Administrator decides whether the issue has been adequately resolved.

### Postconditions

Administrator can approve or reject the resolution.

---

# 35. UC-31 Approve Resolution

Primary Actor Department Administrator

### Preconditions

 Resolution has been submitted.
 Administrator has reviewed the evidence.

### Main Flow

1. Administrator selects approve.
2. System validates authorization.
3. System changes complaint status to `Completed`.
4. System records verification information.
5. Citizen is notified.
6. Worker is notified where applicable.

### Postconditions

Complaint is officially completed.

---

# 36. UC-32 Reject Resolution

Primary Actor Department Administrator

### Preconditions

 Resolution is awaiting verification.

### Main Flow

1. Administrator reviews the resolution.
2. Administrator determines that the work is insufficient.
3. Administrator selects reject.
4. Administrator provides a reason.
5. System changes status back to `In Progress`.
6. Worker is notified.
7. Worker continues the work.

### Postconditions

Complaint requires additional work.

---

# 37. UC-33 Generate Department Report

Primary Actor Department Administrator

### Main Flow

1. Administrator selects reports.
2. Administrator selects a reporting period or filter.
3. System retrieves department data.
4. System calculates relevant metrics.
5. System displays the report.

### Possible Metrics

 Total complaints
 Pending complaints
 Completed complaints
 Rejected complaints
 Average resolution time
 SLA breaches
 Worker workload
 Category distribution
 Feedback

---

# 38. System Administrator Use Cases

# UC-34 Manage Departments

Primary Actor System Administrator

### Main Flow

1. Administrator opens department management.
2. System displays existing departments.
3. Administrator creates, updates, activates, or deactivates departments.
4. System validates the operation.
5. System saves the changes.
6. Action is recorded in the audit trail.

### Postconditions

Department configuration is updated.

---

# 39. UC-35 Manage Complaint Categories

Primary Actor System Administrator

### Main Flow

1. Administrator opens category management.
2. System displays configured categories.
3. Administrator creates or updates a category.
4. Administrator configures the responsible department.
5. System validates the configuration.
6. System saves the category.

### Postconditions

Complaint category configuration is updated.

---

# 40. UC-36 Manage Department Administrators

Primary Actor System Administrator

### Main Flow

1. Administrator opens administrator management.
2. System displays department administrator accounts.
3. Administrator creates or updates an account.
4. Administrator associates the administrator with a department.
5. System validates the information.
6. System saves the changes.

### Postconditions

Department administrator access is updated.

---

# 41. UC-37 Manage System Users

Primary Actor System Administrator

### Main Flow

1. Administrator opens user management.
2. System displays registered users.
3. Administrator searches or filters users.
4. Administrator views user information.
5. Administrator activatesdeactivates accounts where authorized.
6. System records the action.

### Postconditions

User account status is updated.

---

# 42. UC-38 Manage Workers System-Wide

Primary Actor System Administrator

### Main Flow

1. Administrator opens worker management.
2. System displays workers across departments.
3. Administrator creates or updates worker information.
4. Administrator associates workers with departments.
5. System validates the changes.
6. System saves the information.

### Postconditions

Worker information is updated.

---

# 43. UC-39 Monitor System Complaints

Primary Actor System Administrator

### Main Flow

1. Administrator opens the system dashboard.
2. System retrieves complaint information across departments.
3. Administrator filters complaints.
4. Administrator views system-wide statistics.
5. Administrator identifies operational issues.

### Possible Filters

 Department
 Category
 Status
 Date
 SLA condition
 Worker

### Postconditions

System administrator has system-wide complaint visibility.

---

# 44. UC-40 Generate System Report

Primary Actor System Administrator

### Main Flow

1. Administrator opens system reports.
2. Administrator selects report type.
3. Administrator selects date range and filters.
4. System retrieves relevant data.
5. System calculates metrics.
6. System displays the report.

### Possible Reports

 Department performance
 Category statistics
 Complaint volume
 Resolution time
 SLA performance
 Worker workload
 Citizen feedback

---

# 45. UC-41 Receive Notification

Primary Actor Citizen  Worker  Department Administrator

### Trigger

A relevant complaint event occurs.

### Main Flow

1. System detects an important event.
2. System creates a notification.
3. Notification service attempts delivery.
4. User receives the notification.
5. User can open the related complaint.

### Possible Events

 Complaint submitted
 Complaint verified
 Complaint rejected
 Worker assignment
 Worker reassignment
 Work started
 Resolution submitted
 Resolution approved
 Resolution rejected
 Complaint completed

### Alternative Flow

A1 Notification service unavailable

The complaint transaction remains successful and the notification failure is logged.

---

# 46. UC-42 Maintain Audit Trail

Primary Actor System

Supporting Actor System Administrator

### Main Flow

1. User performs an important system action.
2. System identifies the action.
3. System records

    User ID
    Action
    Related entity
    Previous state
    New state
    Datetime
4. Audit record is securely stored.

### Postconditions

The action can be traced for auditing.

---

# 47. Complaint Workflow Use Case Relationship

The major use cases are connected through the following workflow

```text
UC-05 Capture Image
        │
        ▼
UC-06 Generate AI Description
        │
        ▼
UC-07 ReviewEdit Description
        │
        ▼
UC-08 Capture Location
        │
        ▼
UC-04 Submit Complaint
        │
        ▼
     Submitted
        │
        ▼
UC-21 Review Complaint
        │
     ┌──┴──┐
     ▼     ▼
UC-23    UC-22
Reject   Verify
     │     │
     ▼     ▼
Rejected Verified
             │
             ▼
          UC-26
       Assign Worker
             │
             ▼
          Assigned
             │
             ▼
          Worker App
             │
             ▼
          UC-16
      Accept Assignment
             │
             ▼
          UC-17
     Update Work Status
             │
             ▼
        In Progress
             │
             ▼
          UC-19
    Upload Resolution Proof
             │
             ▼
          UC-18
     Submit Resolution
             │
             ▼
 Resolution Under Verification
             │
             ▼
          UC-30
     Verify Resolution
             │
          ┌──┴──┐
          ▼     ▼
       UC-32   UC-31
       Reject  Approve
          │      │
          ▼      ▼
    In Progress Completed
                   │
                   ▼
                UC-11
          View Resolution
                   │
                   ▼
                UC-12
             Feedback
```

---

# 48. Use Case Relationships

## 48.1 Include Relationships

The following use cases may be modeled using `include` relationships

```text
Submit Complaint
    include Capture Image
    include Generate AI Description
    include ReviewEdit Description
    include Capture Location
```

```text
Assign Complaint
    include View Worker Availability
```

```text
Submit Resolution
    include Upload Resolution Proof
```

```text
Verify Resolution
    include View Resolution Proof
```

---

## 48.2 Extend Relationships

The following may be represented using `extend` relationships

```text
Generate AI Description
        ▲
        │ extend
        │
Manual Description Entry
```

when AI processing fails.

Similarly

```text
Verify Complaint
       ▲
       │ extend
       │
Reject Complaint
```

and

```text
Verify Resolution
       ▲
       │ extend
       │
Reject Resolution
```

---

# 49. Authorization Matrix

 Function                  Citizen  Worker  Department Admin  System Admin 
 ------------------------  -----  ----  --------------  ---------- 
 Register                     ✓        —            —               —      
 Login                        ✓        ✓            ✓               ✓      
 Submit Complaint             ✓        —            —               —      
 View Own Complaints          ✓        —            —               —      
 View Assigned Complaints     —        ✓            ✓               ✓      
 Verify Complaint             —        —            ✓              ✓      
 Reject Complaint             —        —            ✓              ✓      
 Assign Worker                —        —            ✓              ✓      
 Reassign Worker              —        —            ✓              ✓      
 Update Work Status           —        ✓            —               —      
 Upload Resolution            —        ✓            —               —      
 Verify Resolution            —        —            ✓              ✓      
 Approve Resolution           —        —            ✓              ✓      
 Manage Department            —        —            —               ✓      
 Manage Workers               —        —            ✓               ✓      
 Manage Users                 —        —         Limited            ✓      
 Manage Categories            —        —            —               ✓      
 View Department Reports      —        —            ✓               ✓      
 View System Reports          —        —            —               ✓      
 Provide Feedback             ✓        —            —               —      

`✓` indicates system-level administrative access where explicitly permitted by the implementation.

---

# 50. Global Preconditions

The following conditions apply to protected use cases

1. User must be authenticated.
2. User must have an active account.
3. User must have the required role.
4. User must have permission to access the requested resource.
5. Backend services must be available unless an appropriate offlinefallback mechanism exists.

---

# 51. Global Postconditions

Important system operations shall

 Persist valid changes.
 Maintain data integrity.
 Update relevant status information.
 Record required timestamps.
 Create audit records where applicable.
 Trigger required notifications.
 Prevent unauthorized state changes.

---

# 52. Exception Handling

The system shall handle the following exceptions

 Exception                    Expected System Behaviour                      
 ---------------------------  ---------------------------------------------- 
 Invalid login                Reject authentication                          
 Unauthorized access          Return access-denied response                  
 AI unavailable               Allow manual description                       
 GPS unavailable              Inform user and retryfallback                 
 Invalid image                Reject upload                                  
 Network failure              Preserve unsaved state where possible          
 Worker unavailable           Prevent invalid assignment                     
 Duplicate complaint request  Prevent duplicate creation                     
 Invalid status transition    Reject operation                               
 Resolution proof missing     Prevent resolution submission                  
 Notification failure         Log failure without rolling back operation     
 Database failure             Return controlled error and preserve integrity 

---

# 53. Security Rules for Use Cases

Every use case involving protected information shall enforce

 Authentication
 Role-based authorization
 Resource ownership checks
 Input validation
 File validation
 Secure API communication
 Audit logging for important actions

The backend shall enforce these rules even if the client application attempts to bypass them.

---

# 54. Core Use Case Scenario

The most important CivicSnap scenario is

 A citizen reports a civic issue, the system assists with AI-generated description and GPS location, the department verifies and assigns the complaint, the worker resolves it through the Worker App, and the department administrator verifies the resolution before completion.

This scenario represents the central business workflow of CivicSnap and connects the four primary actors.

---

# 55. Summary

CivicSnap's use cases cover the complete lifecycle of a civic complaint

```text
Citizen
   │
   ├── RegisterLogin
   │
   ├── Capture Issue
   │
   ├── AI Description
   │
   ├── Confirm Description
   │
   ├── Capture Location
   │
   └── Submit Complaint
             │
             ▼
      Department Admin
             │
       Verify Complaint
                 
      Reject      Verify
        │            │
        ▼            ▼
     Rejected     Assign Worker
                       │
                       ▼
                 Worker App
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
                           
                Reject      Approve
                  │            │
                  ▼            ▼
             In Progress    Completed
                                │
                                ▼
                             Citizen
                                │
                           View Result
                                │
                           Give Feedback
```

The Worker App is an independent and essential system component, positioned between departmental assignment and departmental resolution verification. The worker performs the actual field operation, while the Department Administrator retains authority over complaint verification, assignment, and final resolution approval.
