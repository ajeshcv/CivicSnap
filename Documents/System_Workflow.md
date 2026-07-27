# CivicFlow - System Workflow

Version: 1.0

---

# 1. Overview

This document describes the complete workflow of the CivicFlow system from complaint creation to complaint closure. It defines how Citizens, Artificial Intelligence, Department Administrators, Workers, and the System interact throughout the complaint lifecycle.

---

# 2. Actors

1. Citizen
2. Artificial Intelligence (AI)
3. Backend System
4. Department Administrator
5. Worker
6. System Administrator

---

# 3. Complaint Lifecycle

```
Citizen
    │
    ▼
Capture Image
    │
    ▼
Collect GPS + Date + Time
    │
    ▼
AI Analysis
    │
    ▼
Citizen Reviews AI Result
    │
    ▼
Submit Complaint
    │
    ▼
Department Verification
    │
    ▼
Worker Recommendation
    │
    ▼
Assign Worker
    │
    ▼
Worker Accepts Task
    │
    ▼
Worker Completes Work
    │
    ▼
Department Verification
    │
    ▼
Citizen Receives Report
    │
    ▼
Feedback / Reopen
```

---

# 4. Citizen Workflow

## Step 1

Citizen logs into the mobile application.

---

## Step 2

Citizen selects "Report Complaint".

---

## Step 3

The application opens only the in-app camera.

The citizen captures an image.

Gallery images are not allowed.

---

## Step 4

Immediately after capturing the image, the application automatically collects:

- GPS Coordinates
- Address
- Date
- Time

---

## Step 5

The captured image and metadata are sent to the AI module.

---

# 5. AI Workflow

The AI analyzes the uploaded image.

The AI generates:

- Complaint Title
- Complaint Description
- Complaint Category
- Suggested Department
- Confidence Score
- Priority Level

Example

Image

↓

Broken Street Light

↓

Category

Street Lighting

↓

Department

Electricity Department

↓

Confidence

96%

↓

Priority

High

---

# 6. Duplicate Complaint Detection

After AI analysis, the backend checks whether a similar complaint already exists.

Duplicate detection compares:

- Image similarity
- GPS location
- Complaint category
- Distance from existing complaints
- Complaint status

If a duplicate is found:

The citizen is informed.

Example:

"A similar complaint already exists nearby."

The citizen can

- Follow the existing complaint

or

- Submit a new complaint with a reason.

If no duplicate exists,

continue to submission.

---

# 7. Citizen Verification

The AI-generated complaint is displayed.

Citizen can:

- Edit Title
- Edit Description
- Change Category
- Confirm Department

Citizen presses

Submit Complaint.

---

# 8. Backend Processing

The backend stores:

- Complaint
- Location
- AI Result
- Citizen Details
- Images
- Status

Initial status:

Submitted

The complaint is automatically routed to the suggested department.

---

# 9. Department Administrator Workflow

The Department Administrator receives a notification.

The administrator reviews:

- Image
- Location
- AI Result
- Citizen Description

Administrator can:

Approve

Reject

Request Additional Information

---

# 10. Worker Recommendation

If approved,

the system recommends workers based on:

- Department
- Availability
- Number of active tasks
- Distance to complaint (future enhancement)

The recommendation list is shown to the Department Administrator.

The administrator selects one worker.

---

# 11. Worker Workflow

Worker receives a notification.

Worker opens the complaint.

Worker views:

- Complaint Image
- Complaint Description
- GPS Location
- Navigation Button

Worker accepts the task.

Status changes to

Assigned

---

Worker reaches the location.

Status becomes

In Progress

---

Worker completes the work.

Worker uploads:

- Completion Images
- Completion Notes

Worker presses

Complete Task

---

# 12. Completion Verification

Department Administrator reviews:

- Completion Images
- Completion Notes

If satisfied,

Status changes to

Completed

Otherwise,

Task is returned to the worker.

---

# 13. Citizen Notification

Citizen receives:

Complaint Completed

Citizen can view:

- Before Image
- After Image
- Completion Notes
- Completion Date

---

# 14. Feedback Workflow

Citizen rates:

1–5 Stars

Citizen writes feedback.

Feedback is stored.

Complaint becomes

Closed

---

# 15. Complaint Reopening

If the citizen is not satisfied,

they select

Reopen Complaint.

Reason must be provided.

Complaint status becomes

Reopened

The complaint returns to the same department for review.

---

# 16. Complaint Status Flow

Submitted

↓

Under Verification

↓

Approved

↓

Assigned

↓

Worker Accepted

↓

In Progress

↓

Completed

↓

Department Verified

↓

Citizen Feedback

↓

Closed

Alternative Flow

Submitted

↓

Rejected

Alternative Flow

Completed

↓

Reopened

↓

Under Verification

---

# 17. Notification Events

Citizen

- Complaint Submitted
- Duplicate Complaint Found
- Complaint Approved
- Worker Assigned
- Work Started
- Complaint Completed
- Complaint Reopened
- Feedback Reminder

Department Administrator

- New Complaint
- Worker Completed Task
- Reopened Complaint

Worker

- New Assignment
- Task Reminder
- Task Returned
- Completion Approved

---

# 18. AI Decision Outputs

The AI module returns:

- Complaint Title
- Complaint Description
- Complaint Category
- Suggested Department
- Confidence Score
- Priority Level
- Duplicate Detection Result

The citizen always has the final authority to edit the AI-generated content before submission.

---

# 19. System Rules

- Only in-app camera can be used for complaint images.
- GPS, date, and time are captured automatically.
- Every complaint is assigned a unique Complaint ID.
- Every status change is recorded.
- Every action is logged.
- Only Department Administrators can assign workers.
- Only assigned workers can complete tasks.
- Only the reporting citizen can reopen their complaint.
- AI suggestions are recommendations and require citizen confirmation before submission.

---

# 20. Workflow Summary

The CivicFlow workflow ensures that every complaint progresses through a structured process involving AI-assisted complaint generation, department verification, worker assignment, work completion, administrative validation, citizen feedback, and complaint closure while maintaining transparency and accountability throughout the complaint lifecycle.