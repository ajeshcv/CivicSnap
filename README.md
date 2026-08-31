# CivicSnap

## AI-Assisted Civic Complaint Management System

CivicSnap is an AI-powered civic complaint management platform designed to simplify how citizens report and track public infrastructure issues.

Using a mobile application, citizens can capture an image of an issue such as potholes, damaged streetlights, overflowing garbage, road damage, or other civic problems. CivicSnap analyzes the submitted image using a Vision-Language AI model and automatically generates relevant complaint information, recommends the responsible department, estimates the priority, and checks for possible duplicate complaints.

Once submitted, complaints are intelligently routed to the appropriate department for verification and resolution. Officials can assign complaints to available workers, monitor progress, and verify completion. Citizens can track their complaints in real time, provide feedback, and reopen complaints if the issue has not been properly resolved.

---

## Key Features

### 📸 AI-Powered Complaint Creation

* Capture civic issues directly through the mobile application's camera.
* AI-generated complaint title.
* AI-generated complaint description.
* Automatic analysis of the uploaded image.
* Confidence score for AI predictions.

### 🏢 Smart Department Recommendation

* Automatically identifies the most relevant department for a complaint.
* Helps reduce manual routing and processing time.
* Ensures complaints reach the appropriate authority.

### 🚨 Priority Estimation

* AI-assisted complaint priority prediction.
* Helps authorities identify issues requiring immediate attention.
* Supports efficient allocation of workers and resources.

### 🔍 Duplicate Complaint Detection

* Detects potentially duplicate complaints.
* Helps prevent multiple complaints for the same civic issue.
* Reduces unnecessary workload for government departments.

### 📍 Location & Metadata

* GPS location capture.
* Automatic date and time recording.
* Google Maps integration for location visualization.

### 👷 Worker Assignment

* Recommends suitable workers for complaints.
* Supports assignment based on availability and complaint requirements.
* Enables departments to track assigned work.

### 📊 Complaint Tracking

Citizens can track the complete lifecycle of their complaints:

**Submitted → Under Review → Assigned → In Progress → Resolved → Verified**

### ✅ Completion Verification

* Workers can submit completion reports.
* Departments can verify whether the issue has been resolved.
* Citizens can review the completed complaint.

### 💬 Citizen Feedback & Reopening

* Citizens can provide feedback after resolution.
* Complaints can be reopened when the issue remains unresolved.
* Improves accountability and service quality.

---

## System Workflow

```text
Citizen
   │
   ▼
Capture Image + GPS Location
   │
   ▼
AI Analysis
   ├── Generate Title
   ├── Generate Description
   ├── Recommend Department
   ├── Estimate Priority
   ├── Detect Duplicate
   └── Calculate Confidence
   │
   ▼
Complaint Submission
   │
   ▼
Department Verification
   │
   ▼
Worker Recommendation & Assignment
   │
   ▼
Issue Resolution
   │
   ▼
Completion Report
   │
   ▼
Department Verification
   │
   ▼
Citizen Feedback
   │
   ├── Satisfied → Complaint Closed
   │
   └── Not Resolved → Complaint Reopened
```

---

## User Roles

### 👤 Citizen

* Report civic issues.
* Capture images using the mobile application.
* View AI-generated complaint details.
* Track complaint status.
* View complaint history.
* Receive updates on complaint progress.
* Verify completed complaints.
* Provide feedback.
* Reopen unresolved complaints.

### 🏛️ Department / Authority

* View complaints assigned to the department.
* Verify submitted complaints.
* Review AI recommendations.
* Assign complaints to workers.
* Monitor complaint progress.
* Verify completion reports.
* Manage reopened complaints.

### 👷 Worker

* View assigned complaints.
* Access complaint location and details.
* Update work progress.
* Submit completion reports.
* Mark assigned tasks as completed.

---

## Tech Stack

| Component          | Technology               |
| ------------------ | ------------------------ |
| Mobile Application | Flutter                  |
| Web Dashboard      | React.js                 |
| Backend            | Spring Boot (Java)       |
| Database           | PostgreSQL               |
| AI                 | Vision-Language AI Model |
| Authentication     | JWT                      |
| Maps & Location    | Google Maps API          |

---

## Architecture

```text
┌───────────────────────┐
│    Flutter Mobile     │
│      Application      │
└───────────┬───────────┘
            │
            │ REST API
            ▼
┌───────────────────────┐
│     Spring Boot       │
│       Backend         │
│                       │
│  Authentication       │
│  Complaint Management  │
│  Department Routing   │
│  Worker Assignment    │
└───────┬───────┬───────┘
        │       │
        │       │ AI Analysis
        │       ▼
        │  ┌─────────────────┐
        │  │ Vision-Language │
        │  │    AI Model     │
        │  └─────────────────┘
        │
        ▼
┌───────────────────────┐
│      PostgreSQL       │
│       Database        │
└───────────────────────┘

            ▲
            │
            │ REST API
            │
┌───────────┴───────────┐
│     React.js Web      │
│       Dashboard       │
└───────────────────────┘

        │
        ▼
┌───────────────────────┐
│   Google Maps API     │
│  Location & Mapping   │
└───────────────────────┘
```

---

## AI Capabilities

CivicSnap uses a Vision-Language AI model to analyze images submitted by citizens.

The AI pipeline supports:

1. **Image Understanding**
   Identifies the civic issue represented in the image.

2. **Complaint Generation**
   Generates a meaningful complaint title and description.

3. **Department Classification**
   Recommends the department responsible for addressing the issue.

4. **Priority Prediction**
   Estimates the urgency of the complaint.

5. **Duplicate Detection**
   Identifies complaints that may refer to the same or a nearby existing issue.

6. **Confidence Estimation**
   Provides a confidence score to help users and authorities evaluate AI-generated recommendations.

---

## Complaint Lifecycle

| Stage        | Description                                       |
| ------------ | ------------------------------------------------- |
| Submitted    | Citizen submits a new complaint                   |
| Under Review | Department reviews and verifies the complaint     |
| Assigned     | Complaint is assigned to a suitable worker        |
| In Progress  | Worker begins resolving the issue                 |
| Resolved     | Worker submits a completion report                |
| Verified     | Department verifies the resolution                |
| Closed       | Complaint is successfully completed               |
| Reopened     | Citizen reports that the issue remains unresolved |

---

## Security

CivicSnap uses JWT-based authentication to secure communication between users and the application.

Key security mechanisms include:

* JWT authentication
* Role-based access control
* Secure REST APIs
* Protected user and complaint data
* Authenticated department and worker operations

---

## Google Maps Integration

Google Maps API is used to support location-based civic complaint management.

The platform can use location information to:

* Capture the location where an issue was reported.
* Display complaint locations on maps.
* Help workers locate reported issues.
* Support location-based complaint analysis.
* Assist with worker assignment.

---

## Benefits

### For Citizens

* Faster complaint registration.
* No need to manually write detailed descriptions.
* Easy image-based reporting.
* Real-time complaint tracking.
* Greater transparency and accountability.

### For Government Departments

* Automated complaint categorization.
* Reduced manual processing.
* Better complaint prioritization.
* Duplicate complaint reduction.
* Efficient worker allocation.
* Centralized complaint monitoring.

### For Workers

* Clear issue descriptions.
* Accurate complaint locations.
* Organized task assignment.
* Easy progress reporting.

---

## Project Goals

CivicSnap aims to:

* Make civic issue reporting simple and accessible.
* Reduce the manual effort involved in complaint registration.
* Improve complaint routing and prioritization.
* Reduce duplicate complaints.
* Improve coordination between citizens, departments, and workers.
* Increase transparency throughout the complaint resolution process.
* Use AI to make civic administration more efficient.


## Project Vision

> **Capture an issue. Let AI understand it. Connect it to the right people. Track it until it's fixed.**

CivicSnap aims to bridge the gap between citizens and civic authorities by combining **mobile technology, artificial intelligence, location services, and centralized complaint management** into a single platform.

---

## Technologies

**Frontend**

* Flutter
* React.js

**Backend**

* Spring Boot
* Java
* REST APIs

**Database**

* PostgreSQL

**Artificial Intelligence**

* Vision-Language AI Model

**Security**

* JWT Authentication

**Maps**

* Google Maps API
