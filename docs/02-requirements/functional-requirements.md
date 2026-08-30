# CivicFlow — Functional Requirements

## 1. Introduction

CivicFlow is a civic complaint management application designed to help citizens report public issues such as damaged street lights, waste accumulation, road damage, water leakage, and other civic problems.

The system allows citizens to capture an image using the application camera and upload it. AI analyzes the uploaded image and generates a suitable complaint description. The citizen can review and edit the generated description before submitting the complaint. The system also captures the complaint location using GPS and records relevant submission details.

Once submitted, the complaint is routed to the appropriate government department. Department administrators can verify complaints, assign verified complaints to available workers, and monitor their progress. Workers can view assigned complaints, visit the reported location, resolve the issue, and upload proof of completion. Department administrators can verify the resolution before the complaint is marked as completed.

---

# 2. Functional Requirements

## FR-01: User Registration

The system shall allow citizens to create an account.

The system shall collect required information such as:

* Name
* Email address
* Mobile number
* Password

The system shall validate the provided information before creating the account.

The system shall prevent registration using an already registered email or mobile number.

---

## FR-02: User Authentication

The system shall allow registered users to log in securely.

The system shall authenticate users using their registered credentials.

The system shall provide appropriate error messages for invalid credentials.

The system shall maintain the user's authenticated session.

The system shall provide a logout function.

---

## FR-03: User Profile Management

The system shall allow users to view their profile.

The system shall allow users to update permitted profile information.

The system shall allow users to change their password.

The system shall securely store user credentials.

---

## FR-04: Capture Complaint Image

The system shall allow citizens to capture an image of a civic issue using the application's camera.

The system shall allow the captured image to be reviewed before submission.

The system shall allow the user to retake the image if the image is unclear or incorrect.

The system shall store the complaint image associated with the complaint record.

---

## FR-05: Upload Complaint Image

The system shall allow the citizen to upload the captured image to the server.

The system shall validate the uploaded file type and size.

The system shall associate the uploaded image with the authenticated user.

The system shall securely store uploaded complaint images.

---

## FR-06: AI-Based Complaint Description Generation

The system shall analyze the uploaded complaint image using an AI service.

The AI system shall generate a suggested description of the civic issue visible in the image.

The generated description may identify issues such as:

* Broken street light
* Garbage accumulation
* Road damage
* Water leakage
* Damaged public infrastructure
* Other supported civic issues

The system shall display the AI-generated description to the citizen.

The AI-generated description shall be treated as a suggestion and not as the final complaint description.

---

## FR-07: User Verification of AI Description

The system shall allow the citizen to review the AI-generated description.

The citizen shall be able to edit or correct the generated description.

The citizen shall be able to confirm the description before submitting the complaint.

The system shall store the citizen-confirmed description rather than relying exclusively on the AI-generated result.

---

## FR-08: Location Capture

The system shall capture the geographical location of the complaint using the device's GPS/location services.

The system shall record latitude and longitude coordinates.

The system shall associate the location with the complaint.

The system shall display the captured location to the user before final submission where possible.

The user shall be able to confirm the captured location.

---

## FR-09: Complaint Submission

The system shall allow users to submit a complaint after confirming the image, description, and location.

The system shall generate a unique complaint ID for every submitted complaint.

The system shall record the complaint submission date and time.

The system shall assign an initial status such as `Submitted`.

The system shall prevent incomplete complaints from being submitted.

---

## FR-10: Department Identification and Routing

The system shall determine the appropriate department for a complaint based on the complaint information and configured civic issue categories.

Examples include:

* Electricity/Street Light Department
* Waste Management Department
* Water Supply Department
* Roads/Public Works Department
* Other relevant departments

The system shall route the complaint to the appropriate department dashboard.

The system shall allow authorized department administrators to review the assigned department when necessary.

---

## FR-11: Complaint Tracking

The system shall maintain the complete status history of each complaint.

Possible complaint statuses shall include:

1. Submitted
2. Under Verification
3. Verified
4. Assigned
5. In Progress
6. Resolved
7. Resolution Under Verification
8. Completed
9. Rejected

The system shall allow users to view the current status of their complaints.

The system shall record important status changes with date and time.

---

## FR-12: User Complaint History

The system shall provide users with a list of complaints submitted by them.

Users shall be able to view:

* Complaint ID
* Complaint image
* Description
* Location
* Department
* Submission date
* Current status
* Resolution details

Users shall be able to open an individual complaint to view its complete details.

---

# 3. Department Administrator Requirements

## FR-13: Department Administrator Login

The system shall provide a secure login facility for department administrators.

The system shall restrict department-specific information to authorized administrators.

A department administrator shall only be able to manage complaints belonging to their assigned department unless additional permissions are provided.

---

## FR-14: Department Dashboard

The system shall provide a dashboard for department administrators.

The dashboard shall display relevant information such as:

* Total complaints
* New complaints
* Complaints under verification
* Assigned complaints
* Complaints in progress
* Resolved complaints
* Pending complaints
* SLA-breached complaints

The dashboard shall provide suitable filtering and sorting options.

---

## FR-15: Complaint Verification

The department administrator shall be able to view submitted complaints.

The administrator shall verify whether the complaint contains sufficient and valid information.

The administrator shall be able to:

* Verify a complaint
* Reject a complaint
* View complaint image
* View complaint description
* View complaint location
* View submission time

The system shall record the verification decision and timestamp.

---

## FR-16: Worker Management

The department administrator shall be able to manage workers belonging to the department.

The administrator shall be able to:

* View workers
* Add workers
* Update worker information
* Activate/deactivate workers
* View worker availability
* View assigned complaints

---

## FR-17: Worker Availability

The system shall maintain the availability status of workers.

Possible worker availability states shall include:

* Available
* Assigned
* Busy
* Offline

The system shall use worker availability information when assigning complaints.

---

## FR-18: Complaint Assignment

The department administrator shall be able to assign verified complaints to workers.

The system shall display available workers for assignment.

The system may recommend suitable workers based on factors such as:

* Worker availability
* Current workload
* Complaint location
* Worker department
* Previous assignments

The administrator shall have the final authority to confirm or change the assignment.

The system shall record the assignment date and time.

---

## FR-19: SLA Monitoring

The system shall maintain a defined service-level time limit for complaints.

The system shall calculate the time elapsed since complaint submission or assignment based on the configured SLA.

The system shall identify complaints approaching or exceeding their SLA.

The department dashboard shall display SLA-breached complaints.

The system shall record SLA breach information for reporting and monitoring.

---

# 4. Worker Application Requirements

## FR-20: Worker Authentication

The worker shall be able to securely log in to the worker application.

The system shall authenticate the worker using valid credentials.

The worker shall be able to log out securely.

---

## FR-21: Worker Dashboard

The worker application shall provide a dashboard showing assigned complaints.

The dashboard shall display:

* Complaint ID
* Complaint type
* Description
* Location
* Priority
* Assignment status
* SLA information

The worker shall be able to view pending assignments.

---

## FR-22: View Complaint Details

The worker shall be able to view complete details of an assigned complaint.

The details shall include:

* Complaint image
* Description
* GPS coordinates
* Map/location
* Complaint type
* Submission time
* SLA deadline
* Relevant instructions

---

## FR-23: Update Work Status

The worker shall be able to update the status of an assigned complaint.

The worker shall be able to mark the complaint as:

* Accepted
* In Progress
* Unable to Resolve
* Resolved

The system shall record each status update with date and time.

---

## FR-24: Resolution Proof

After resolving an issue, the worker shall be able to upload proof of completion.

The proof may include:

* Before/after photographs
* Resolution description
* Additional notes
* Location information
* Completion timestamp

The system shall associate the resolution proof with the complaint.

---

## FR-25: Resolution Submission

The worker shall submit the completed work for department verification.

The complaint status shall change to `Resolution Under Verification`.

The worker shall not be able to directly mark the complaint as finally completed without department verification.

---

# 5. Department Resolution Verification

## FR-26: Verify Worker Resolution

The department administrator shall be able to review the worker's submitted resolution.

The administrator shall be able to view:

* Original complaint
* Original complaint image
* Worker resolution details
* Resolution proof images
* Resolution timestamp
* Worker information

The administrator shall verify whether the issue has actually been resolved.

---

## FR-27: Approve or Reject Resolution

The department administrator shall be able to approve the worker's resolution.

If approved:

`Resolution Under Verification → Completed`

If rejected:

`Resolution Under Verification → In Progress`

The administrator shall be able to provide a reason when rejecting a resolution.

The system shall record the verification decision.

---

# 6. Citizen Feedback

## FR-28: View Resolution

Once a complaint is completed, the citizen shall be able to view the resolution details.

The user shall be able to view the resolution proof uploaded by the worker where permitted.

The user shall be able to see the final complaint status.

---

## FR-29: Complaint Feedback

The system may allow citizens to provide feedback after complaint completion.

The citizen may provide:

* Rating
* Feedback/comment

The system shall associate feedback with the corresponding complaint.

Department administrators shall be able to view feedback for completed complaints.

---

# 7. Admin Requirements

## FR-30: System Administrator Login

The system shall provide a secure administrative login.

The system administrator shall have system-level privileges.

---

## FR-31: Department Management

The system administrator shall be able to:

* Create departments
* Update departments
* Activate/deactivate departments
* View department information

The administrator shall configure the categories of complaints handled by each department.

---

## FR-32: User Management

The system administrator shall be able to view registered users.

The administrator shall be able to activate or deactivate user accounts where required.

---

## FR-33: Worker and Department Administrator Management

The system administrator shall be able to manage department administrators and workers.

The administrator shall be able to assign workers and department administrators to departments.

---

## FR-34: Complaint Monitoring

The system administrator shall be able to monitor complaints across all departments.

The administrator shall be able to view:

* Total complaints
* Complaints by department
* Complaint status
* Pending complaints
* Completed complaints
* Rejected complaints
* SLA breaches

---

## FR-35: Reports

The system shall generate reports based on complaint data.

Reports may include:

* Complaints by department
* Complaints by category
* Complaints by location
* Resolution time
* SLA breaches
* Worker performance
* Department performance
* Citizen feedback

---

# 8. Notification Requirements

## FR-36: Complaint Notifications

The system shall notify users when important changes occur to their complaints.

Notifications may be generated when:

* Complaint is submitted
* Complaint is verified
* Complaint is rejected
* Complaint is assigned
* Work starts
* Complaint is marked resolved
* Resolution is approved
* Resolution is rejected

---

## FR-37: Worker Notifications

The system shall notify workers when a complaint is assigned to them.

The system shall notify workers when an assignment is changed or cancelled.

---

## FR-38: Department Notifications

The system shall notify department administrators about new complaints assigned to their department.

The system may notify administrators about approaching or breached SLA deadlines.

---

# 9. Search and Filtering Requirements

## FR-39: Complaint Search

Authorized users shall be able to search complaints using the complaint ID.

Department administrators shall be able to search complaints using relevant fields such as:

* Complaint ID
* Category
* Status
* Date
* Location
* Assigned worker

The system shall provide filtering and sorting functionality.

---

# 10. Role-Based Access Requirements

## FR-40: Role-Based Authorization

The system shall implement role-based access control.

The primary roles shall include:

* Citizen
* Worker
* Department Administrator
* System Administrator

Each role shall have access only to the functions permitted for that role.

A citizen shall not be able to access department or worker management functions.

A worker shall only be able to access assigned work and permitted worker functions.

A department administrator shall only manage their department's operations.

A system administrator shall have system-level management privileges.

---

# 11. Audit Requirements

## FR-41: Activity Logging

The system shall record important actions performed by authenticated users.

The system shall maintain information such as:

* User/employee ID
* Action performed
* Related complaint
* Date and time
* Previous status
* New status

Audit information shall be accessible only to authorized administrators.

---

# 12. Data Management Requirements

## FR-42: Complaint Data Storage

The system shall securely store complaint information including:

* Complaint ID
* User ID
* Image
* AI-generated description
* User-confirmed description
* Complaint category
* Department
* Location coordinates
* Submission date/time
* Status
* Assigned worker
* Resolution information
* Verification information

---

## FR-43: Data Integrity

The system shall maintain consistency between complaints, users, departments, workers, and resolution records.

The system shall prevent unauthorized modification of complaint records.

The system shall maintain complaint history when important information or status changes occur.

---

# 13. Core Complaint Workflow

The complete functional workflow shall be:

**Citizen**

Capture Image
↓
Upload Image
↓
AI Generates Description
↓
Citizen Reviews/Edits Description
↓
Capture/Confirm GPS Location
↓
Submit Complaint
↓
Generate Complaint ID
↓

**System**

Identify Complaint Category
↓
Route to Appropriate Department
↓

**Department Administrator**

Verify Complaint
↓
Approve / Reject
↓
Assign Available Worker
↓

**Worker**

View Assignment
↓
Visit Location
↓
Resolve Issue
↓
Upload Resolution Proof
↓
Submit Resolution

**Department Administrator**

Verify Resolution
↓
Approve / Reject
↓

**Citizen**

View Final Status
↓
View Resolution
↓
Provide Feedback

---

# 14. Functional Requirements Summary

| ID    | Requirement                   | Primary Actor           |
| ----- | ----------------------------- | ----------------------- |
| FR-01 | User Registration             | Citizen                 |
| FR-02 | Authentication                | All Users               |
| FR-03 | Profile Management            | Citizen                 |
| FR-04 | Capture Complaint Image       | Citizen                 |
| FR-05 | Upload Image                  | Citizen                 |
| FR-06 | AI Description Generation     | System/AI               |
| FR-07 | Verify AI Description         | Citizen                 |
| FR-08 | GPS Location Capture          | Citizen/System          |
| FR-09 | Complaint Submission          | Citizen                 |
| FR-10 | Department Routing            | System                  |
| FR-11 | Complaint Tracking            | All Authorized Users    |
| FR-12 | Complaint History             | Citizen                 |
| FR-13 | Department Login              | Department Admin        |
| FR-14 | Department Dashboard          | Department Admin        |
| FR-15 | Complaint Verification        | Department Admin        |
| FR-16 | Worker Management             | Department Admin        |
| FR-17 | Worker Availability           | Department Admin/Worker |
| FR-18 | Complaint Assignment          | Department Admin        |
| FR-19 | SLA Monitoring                | System/Department Admin |
| FR-20 | Worker Authentication         | Worker                  |
| FR-21 | Worker Dashboard              | Worker                  |
| FR-22 | Complaint Details             | Worker                  |
| FR-23 | Work Status Update            | Worker                  |
| FR-24 | Resolution Proof              | Worker                  |
| FR-25 | Resolution Submission         | Worker                  |
| FR-26 | Resolution Verification       | Department Admin        |
| FR-27 | Resolution Approval/Rejection | Department Admin        |
| FR-28 | View Resolution               | Citizen                 |
| FR-29 | Feedback                      | Citizen                 |
| FR-30 | System Admin Login            | System Admin            |
| FR-31 | Department Management         | System Admin            |
| FR-32 | User Management               | System Admin            |
| FR-33 | Staff Management              | System Admin            |
| FR-34 | Complaint Monitoring          | System Admin            |
| FR-35 | Reports                       | System Admin            |
| FR-36 | Complaint Notifications       | System                  |
| FR-37 | Worker Notifications          | System                  |
| FR-38 | Department Notifications      | System                  |
| FR-39 | Complaint Search              | Authorized Users        |
| FR-40 | Role-Based Access             | System                  |
| FR-41 | Activity Logging              | System                  |
| FR-42 | Complaint Data Storage        | System                  |
| FR-43 | Data Integrity                | System                  |
