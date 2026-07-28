# System Architecture

## Project Name

**CivicFlow: AI-Assisted Civic Complaint Management System**

---

# 1. Overview

CivicFlow follows a multi-tier architecture consisting of:

- Mobile Application
- Web Dashboard
- Backend API
- AI Module
- PostgreSQL Database
- Cloud Storage
- Notification Service

The architecture separates presentation, business logic, artificial intelligence, and data storage to improve scalability, maintainability, and security.

---

# 2. High-Level Architecture

```
                           +----------------------+
                           |    System Admin      |
                           |   React Dashboard    |
                           +----------+-----------+
                                      |
                                      |
                           +----------v-----------+
                           | Department Dashboard |
                           |      React.js        |
                           +----------+-----------+
                                      |
                                      |
                         HTTPS / REST API / JWT
                                      |
                                      |
               +----------------------v----------------------+
               |          Spring Boot Backend               |
               |                                            |
               | Authentication                             |
               | Complaint Management                       |
               | Department Management                      |
               | Worker Recommendation                      |
               | Notification Management                    |
               | AI Integration                             |
               +---------+----------------+-----------------+
                         |                |
                         |                |
              +----------v----+    +------v-------+
              | PostgreSQL    |    | AI Module    |
              | Database      |    | Vision Model |
              +---------------+    +--------------+
                         |
                         |
               +---------v----------+
               | Cloud Image Storage|
               | (Cloudinary)       |
               +--------------------+

                         ^
                         |
             +-----------+-----------+
             |                       |
     +-------+-------+       +-------+-------+
     | Citizen App   |       | Worker App    |
     | Flutter       |       | Flutter       |
     +---------------+       +---------------+
```

---

# 3. Architecture Components

## 3.1 Citizen Mobile Application

The Citizen Mobile Application allows users to:

- Register/Login
- Capture images using only the in-app camera
- Automatically capture GPS location
- Automatically capture date and time
- Review AI-generated complaint
- Submit complaints
- Track complaint status
- Receive notifications
- View completion reports
- Submit feedback
- Reopen complaints

---

## 3.2 Worker Mobile Application

The Worker Application allows workers to:

- Login
- Receive assignments
- View complaint details
- Navigate to the complaint location
- Upload progress updates
- Upload completion photos
- Complete assigned tasks

---

## 3.3 Department Dashboard

Department Administrators can:

- View incoming complaints
- Verify AI-generated complaints
- Approve or reject complaints
- View recommended workers
- Assign workers
- Monitor work progress
- Verify completed work
- Generate department reports

---

## 3.4 System Admin Dashboard

The System Administrator manages the entire platform.

Responsibilities include:

- Manage departments
- Create department administrators
- Manage complaint categories
- View reports
- Manage users
- Monitor the system
- View audit logs

---

# 4. Backend Layer

The backend is developed using Spring Boot.

Responsibilities:

- Authentication
- Authorization
- Business Logic
- Complaint Management
- AI Integration
- Notification Handling
- Database Communication

---

# 5. AI Module

The AI module receives:

- Complaint Image

The AI returns:

- Complaint Title
- Complaint Description
- Complaint Category
- Suggested Department
- Confidence Score
- Priority Level
- Duplicate Detection Result

The AI only provides recommendations.

The citizen must verify the generated information before submission.

---

# 6. Duplicate Detection Module

Before creating a complaint, the backend checks for existing complaints.

Duplicate detection compares:

- Image similarity
- Complaint category
- GPS location
- Complaint distance
- Existing complaint status

If a duplicate exists, the citizen can:

- Follow the existing complaint

or

- Continue submitting a new complaint.

---

# 7. Worker Recommendation Module

After complaint verification, the backend recommends workers based on:

- Department
- Availability
- Current workload

The Department Administrator makes the final assignment.

---

# 8. Database Layer

PostgreSQL stores:

- Users
- Departments
- Complaints
- AI Analysis
- Assignments
- Work Updates
- Feedback
- Notifications
- Audit Logs

---

# 9. Cloud Storage

Cloudinary stores:

- Complaint Images
- Progress Images
- Completion Images
- User Profile Images

Only image URLs are stored in PostgreSQL.

---

# 10. Notification Service

Notifications are sent when:

- Complaint Submitted
- Complaint Approved
- Complaint Rejected
- Worker Assigned
- Work Started
- Work Completed
- Complaint Reopened

Recipients include:

- Citizens
- Workers
- Department Administrators

---

# 11. Authentication Flow

1. User logs in.
2. Backend validates credentials.
3. JWT token is generated.
4. Token is returned to the application.
5. Every request includes the JWT token.
6. Backend verifies the token before processing requests.

---

# 12. Complaint Processing Flow

Citizen

↓

Capture Image

↓

Capture GPS, Date & Time

↓

Upload to Backend

↓

AI Analysis

↓

Citizen Reviews AI Output

↓

Submit Complaint

↓

Department Verification

↓

Worker Recommendation

↓

Worker Assignment

↓

Worker Completes Task

↓

Department Verification

↓

Citizen Receives Report

↓

Feedback / Reopen

---

# 13. Security Architecture

The system implements:

- JWT Authentication
- Role-Based Access Control (RBAC)
- Password Encryption
- Secure REST APIs
- HTTPS (Production)
- Input Validation
- File Type Validation

---

# 14. Design Principles

- Modular Architecture
- Separation of Concerns
- RESTful API Design
- Stateless Authentication
- Scalable Components
- Role-Based Security
- AI as an Assistance Layer

---

# 15. Future Architecture Enhancements

- Microservices Architecture
- Push Notification Server
- AI Model Hosting
- Analytics Service
- Real-Time Dashboard
- GIS Heatmap Service
- Smart City API Integration

---

# 16. Conclusion

The CivicFlow architecture is designed as a modular, scalable, and secure system that integrates mobile applications, a web dashboard, artificial intelligence, and cloud services. The separation of presentation, business logic, AI processing, and data storage ensures maintainability while supporting future enhancements and increased user adoption.