# CivicSnap — Technology Stack

## 1. Introduction

This document defines the technology stack selected for the CivicSnap civic complaint management system.

CivicSnap is designed as a multi-client platform consisting of:

* Citizen App
* Worker App
* Admin Portal
* Spring Boot Backend
* Relational Database
* AI Integration
* Location and Mapping Services
* Notification Services
* File/Object Storage

The technology stack is selected to provide a maintainable, scalable, secure, and cross-platform system.

---

# 2. Technology Stack Overview

| Layer             | Technology                  |
| ----------------- | --------------------------- |
| Citizen App       | Flutter                     |
| Worker App        | Flutter                     |
| Mobile Language   | Dart                        |
| Admin Portal      | React + TypeScript          |
| Backend           | Java + Spring Boot          |
| API               | RESTful API                 |
| API Data Format   | JSON                        |
| Authentication    | Spring Security + JWT       |
| Database          | PostgreSQL                  |
| ORM               | Spring Data JPA + Hibernate |
| AI Integration    | External AI API             |
| Location          | GPS / Location Services     |
| Maps              | Maps API                    |
| Image Storage     | Object/File Storage         |
| Notifications     | Firebase Cloud Messaging    |
| API Documentation | OpenAPI / Swagger           |
| Backend Build     | Maven                       |
| Backend Testing   | JUnit + Mockito             |
| API Testing       | Postman                     |
| Version Control   | Git + GitHub                |
| Containerization  | Docker                      |
| CI/CD             | GitHub Actions              |
| Deployment        | Cloud/Server                |

---

# 3. High-Level Technology Architecture

```text
                         CIVICSNAP
                             │
            ┌────────────────┼────────────────┐
            │                │                │
            ▼                ▼                ▼
     ┌────────────┐   ┌────────────┐   ┌──────────────┐
     │  Citizen   │   │   Worker   │   │    Admin     │
     │    App     │   │    App     │   │    Portal    │
     │  Flutter   │   │  Flutter   │   │ React/TS     │
     │   + Dart   │   │   + Dart   │   │              │
     └─────┬──────┘   └─────┬──────┘   └──────┬───────┘
           │                │                  │
           └────────────────┼──────────────────┘
                            │
                       HTTPS / REST
                            │
                            ▼
              ┌─────────────────────────┐
              │      Spring Boot        │
              │        Backend          │
              │       Java              │
              └────────────┬────────────┘
                           │
          ┌────────────────┼─────────────────┐
          │                │                 │
          ▼                ▼                 ▼
    ┌───────────┐   ┌────────────┐   ┌──────────────┐
    │ PostgreSQL│   │ File/Object│   │ External     │
    │ Database  │   │  Storage   │   │ Services     │
    └───────────┘   └────────────┘   └──────┬───────┘
                                            │
                                  ┌─────────┼─────────┐
                                  ▼         ▼         ▼
                                 AI       Maps       FCM
```

---

# 4. Mobile Application Technology

CivicSnap shall use **Flutter** for both mobile applications.

The two applications shall remain logically separate:

```text
CivicSnap Mobile
│
├── Citizen App
│
└── Worker App
```

Both applications may share common UI components, models, networking utilities, and design principles where appropriate, but they shall have separate role-specific functionality.

---

# 5. Flutter

## 5.1 Purpose

Flutter shall be used to develop the:

* Citizen App
* Worker App

Flutter provides a cross-platform framework using a single Dart codebase.

---

## 5.2 Advantages for CivicSnap

Flutter is suitable because it provides:

* Cross-platform development
* Consistent UI
* Rapid development
* Native device integration
* Camera support
* GPS/location support
* Push notification support
* REST API integration
* Strong community ecosystem

---

# 6. Dart

Dart shall be the primary programming language for both Flutter applications.

Dart will be responsible for:

* UI implementation
* Application logic
* API communication
* State management
* Data models
* Local application state
* Device integration

---

# 7. Citizen App

The Citizen App shall be developed using Flutter and Dart.

## Main Features

* User registration
* Login
* Profile management
* Complaint creation
* Camera/image capture
* Image upload
* AI description
* Description editing
* GPS location
* Complaint submission
* Complaint tracking
* Complaint history
* Resolution viewing
* Feedback
* Notifications

---

# 8. Worker App

The **Worker App shall be a separate Flutter application**.

It shall be specifically designed for field workers.

## Main Features

* Worker login
* Assigned complaint dashboard
* Complaint details
* Complaint image viewing
* Complaint location
* Map/navigation support
* Assignment acceptance
* Work status updates
* Resolution description
* Resolution image capture
* Resolution evidence upload
* Resolution submission
* Work history
* Notifications

The Worker App shall communicate with the same Spring Boot backend used by the Citizen App.

---

# 9. Flutter Application Architecture

The Flutter applications should follow a maintainable architecture such as:

```text
Flutter Application
│
├── Presentation
│   ├── Screens
│   ├── Widgets
│   └── State Management
│
├── Domain
│   ├── Models
│   ├── Entities
│   └── Business Logic
│
├── Data
│   ├── API Services
│   ├── Repositories
│   └── Local Storage
│
└── Core
    ├── Constants
    ├── Utilities
    ├── Network
    └── Error Handling
```

The exact architecture may be refined during implementation.

---

# 10. Flutter Packages

The following categories of Flutter packages may be used.

| Requirement        | Technology/Package Category |
| ------------------ | --------------------------- |
| REST API           | HTTP client such as Dio     |
| State Management   | Provider / Riverpod / Bloc  |
| GPS                | Geolocation package         |
| Maps               | Google Maps or equivalent   |
| Camera             | Flutter camera plugin       |
| Image Selection    | Image picker                |
| Notifications      | Firebase Messaging          |
| Secure Storage     | Flutter Secure Storage      |
| Local Storage      | Shared Preferences / SQLite |
| JSON Serialization | JSON serialization tools    |

The final package selection shall be based on implementation requirements and project constraints.

---

# 11. State Management

A dedicated state-management approach shall be used to prevent application logic from becoming tightly coupled to UI components.

Suitable options include:

* Riverpod
* Provider
* Bloc/Cubit

One approach should be selected and consistently applied across the Citizen and Worker Apps.

---

# 12. REST API Communication

Both Flutter applications shall communicate with the Spring Boot backend through REST APIs.

```text
Citizen App ───────┐
                   │
Worker App ────────┼──── HTTPS / REST ────► Spring Boot
                   │
Admin Portal ──────┘
```

JSON shall be used as the primary data-exchange format.

---

# 13. Backend Technology

## 13.1 Java

Java shall be used for the backend.

Java provides:

* Strong type safety
* Mature ecosystem
* Enterprise support
* Platform independence
* Large library ecosystem

---

# 14. Spring Boot

Spring Boot shall be used as the main backend framework.

It shall provide:

* REST API development
* Dependency injection
* Database integration
* Security
* Validation
* Exception handling
* Configuration
* Testing support

---

# 15. Spring Web

Spring Web shall be used for REST API development.

Example endpoints:

```text
POST   /api/auth/register
POST   /api/auth/login

POST   /api/complaints
GET    /api/complaints/{id}
GET    /api/complaints/my

GET    /api/worker/complaints
PATCH  /api/worker/complaints/{id}/status
POST   /api/worker/complaints/{id}/resolution

GET    /api/admin/complaints
PATCH  /api/admin/complaints/{id}/verify
POST   /api/admin/assignments
```

---

# 16. Spring Security

Spring Security shall be used for:

* Authentication
* Authorization
* Role-based access control
* API security
* Password hashing
* Security filters

The system shall support:

```text
ROLE_CITIZEN
ROLE_WORKER
ROLE_DEPARTMENT_ADMIN
ROLE_SYSTEM_ADMIN
```

---

# 17. JWT Authentication

JWT may be used for stateless authentication between Flutter clients, the Admin Portal, and the backend.

Authentication flow:

```text
User
 │
 ▼
Login
 │
 ▼
Spring Security
 │
 ▼
Credential Validation
 │
 ▼
JWT Token
 │
 ▼
Flutter / React Client
 │
 ▼
Authenticated API Requests
```

The token shall be stored securely on mobile devices.

---

# 18. Database Technology

## PostgreSQL

PostgreSQL shall be used as the primary relational database.

It is suitable for CivicSnap because the system contains strongly related entities.

Major data includes:

* Users
* Roles
* Departments
* Workers
* Categories
* Complaints
* Assignments
* Resolutions
* SLA records
* Notifications
* Feedback
* Audit logs

---

# 19. Spring Data JPA

Spring Data JPA shall be used to simplify database interaction.

Example:

```text
ComplaintService
       │
       ▼
ComplaintRepository
       │
       ▼
Spring Data JPA
       │
       ▼
Hibernate
       │
       ▼
PostgreSQL
```

---

# 20. Hibernate

Hibernate shall provide object-relational mapping between Java entities and PostgreSQL tables.

Example:

```text
Java Entity
    │
    ▼
Hibernate ORM
    │
    ▼
Database Table
```

---

# 21. Core Database Entities

The database is expected to contain entities such as:

```text
User
Role
Department
Category
Complaint
ComplaintImage
Assignment
Resolution
ResolutionImage
SLA
Notification
Feedback
AuditLog
```

The exact schema shall be finalized during database design.

---

# 22. Admin Portal

The Admin Portal shall be a web application.

## Technology

* React
* TypeScript
* HTML
* CSS
* REST API

The portal shall support:

### Department Administrator

* Complaint verification
* Complaint rejection
* Worker management
* Worker availability
* Complaint assignment
* Reassignment
* SLA monitoring
* Resolution verification
* Reports

### System Administrator

* User management
* Worker management
* Department management
* Category management
* Department mapping
* System monitoring
* System reports
* Configuration

---

# 23. Admin Portal Architecture

```text
React + TypeScript
        │
        ▼
Components
        │
        ▼
State Management
        │
        ▼
API Client
        │
        ▼
HTTPS / REST
        │
        ▼
Spring Boot Backend
```

---

# 24. AI Technology

CivicSnap shall integrate an external AI service.

The AI system may provide:

* Image understanding
* Suggested complaint description
* Complaint category suggestion
* Issue identification

Architecture:

```text
Complaint Image
       │
       ▼
Spring Boot AI Service
       │
       ▼
External AI API
       │
       ▼
AI Result
       │
       ▼
Backend Validation
       │
       ▼
Citizen Review
```

AI output shall remain a suggestion until confirmed by the citizen or authorized user.

---

# 25. GPS and Location Technology

The Flutter applications shall use device location services.

The Citizen App shall use GPS primarily for complaint location capture.

The Worker App may use location services for:

* Viewing location
* Navigation
* Field operations

Location information may include:

```text
Latitude
Longitude
Accuracy
Timestamp
```

---

# 26. Maps

A suitable map provider shall be integrated for displaying complaint locations.

Possible functionality includes:

* Complaint marker
* Map view
* Location visualization
* Navigation assistance

The final provider shall be selected based on:

* API availability
* Cost
* Licensing
* Required features
* Project requirements

---

# 27. Camera and Image Technology

Flutter camera/image plugins shall be used for:

### Citizen App

* Capturing complaint images

### Worker App

* Capturing resolution evidence

The system shall validate uploaded images before storage and AI processing.

---

# 28. Image Storage

Images should be stored separately from the relational database.

Recommended flow:

```text
Flutter App
    │
    ▼
Spring Boot
    │
    ▼
File/Object Storage
    │
    └────► AI Service when required
```

The database shall store metadata and storage references.

---

# 29. Firebase Cloud Messaging

Firebase Cloud Messaging (FCM) shall be used for mobile push notifications.

Notifications may include:

* Complaint submitted
* Complaint verified
* Complaint rejected
* Worker assigned
* Worker reassigned
* Resolution submitted
* Resolution rejected
* Resolution approved
* Complaint completed
* SLA warnings

---

# 30. Notification Architecture

```text
Spring Boot Backend
        │
        ▼
Notification Service
        │
        ▼
Firebase Cloud Messaging
        │
      ┌─┴───────────┐
      ▼             ▼
Citizen App     Worker App
```

Notification delivery failure shall not roll back the underlying complaint operation.

---

# 31. API Documentation

## OpenAPI / Swagger

OpenAPI shall be used to document backend APIs.

Documentation shall include:

* Endpoint
* HTTP method
* Parameters
* Request body
* Authentication requirements
* Response
* Error responses

---

# 32. Build Tool

## Maven

Maven shall be used for Spring Boot project management.

It shall manage:

* Dependencies
* Compilation
* Testing
* Packaging
* Build lifecycle

---

# 33. Testing Stack

## Backend

* JUnit
* Mockito
* Spring Boot Test

## API

* Postman

## Flutter

Flutter's testing framework shall be used for:

* Unit tests
* Widget tests
* Integration tests

## Web

Appropriate React/TypeScript testing tools may be used for the Admin Portal.

---

# 34. API Testing

Postman shall be used to test:

```text
Authentication
Complaint Creation
Complaint Retrieval
Complaint Verification
Complaint Assignment
Worker Status Updates
Resolution Submission
Resolution Approval
Resolution Rejection
Authorization
SLA Operations
```

---

# 35. Version Control

## Git

Git shall be used for source-code version control.

The project repository may be structured as:

```text
CivicSnap/
│
├── citizen-app/
├── worker-app/
├── admin-portal/
├── backend/
├── documentation/
└── infrastructure/
```

---

# 36. GitHub

GitHub may be used for:

* Repository hosting
* Collaboration
* Pull requests
* Issue tracking
* Documentation
* CI/CD

Sensitive information shall never be committed to the repository.

---

# 37. Docker

Docker may be used for backend infrastructure.

A development environment may contain:

```text
Docker Compose
│
├── Spring Boot
│
├── PostgreSQL
│
└── Object Storage
```

Docker is optional for the initial prototype but recommended for consistent development and deployment.

---

# 38. CI/CD

GitHub Actions may be used for automated workflows.

Example:

```text
Git Push
   │
   ▼
GitHub Actions
   │
   ├── Build Backend
   ├── Run Tests
   ├── Build Flutter Apps
   ├── Validate Admin Portal
   └── Build Docker Image
          │
          ▼
       Deployment
```

CI/CD may be introduced gradually during development.

---

# 39. Deployment Technology

The system can be deployed using a cloud or server environment.

Possible deployment architecture:

```text
                  Internet
                     │
          ┌──────────┼──────────┐
          │          │          │
          ▼          ▼          ▼
      Citizen      Worker     Admin
        App          App      Portal
          │          │          │
          └──────────┼──────────┘
                     │
                  HTTPS
                     │
                     ▼
              Spring Boot API
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
    PostgreSQL   File Storage   External APIs
                                  │
                          ┌───────┼───────┐
                          ▼       ▼       ▼
                         AI      Maps     FCM
```

---

# 40. Configuration Management

Environment-specific configuration shall be maintained.

Example:

```text
Development
    │
    ├── Database Configuration
    ├── AI Configuration
    ├── JWT Configuration
    └── Storage Configuration

Testing
    │
    ├── Test Database
    ├── Test Credentials
    └── Test Services

Production
    │
    ├── Production Database
    ├── Production Secrets
    ├── Production AI
    └── Production Storage
```

Secrets shall be stored through environment variables or secure secret management.

---

# 41. Security Technology Stack

| Security Requirement | Technology                             |
| -------------------- | -------------------------------------- |
| Authentication       | Spring Security                        |
| Authorization        | RBAC                                   |
| API Authentication   | JWT                                    |
| Password Hashing     | BCrypt/secure password encoder         |
| Transport Security   | HTTPS/TLS                              |
| API Validation       | Spring Validation                      |
| File Validation      | Backend validation                     |
| Mobile Token Storage | Secure Storage                         |
| Audit                | Spring Boot Audit Module               |
| Secrets              | Environment Variables / Secret Manager |

---

# 42. Technology-to-Requirement Mapping

| Requirement             | Technology                  |
| ----------------------- | --------------------------- |
| Citizen reporting       | Flutter + Dart              |
| Worker field operations | Flutter + Dart              |
| Separate Worker App     | Flutter                     |
| Administration          | React + TypeScript          |
| Backend                 | Java + Spring Boot          |
| API communication       | REST                        |
| API data                | JSON                        |
| Authentication          | Spring Security + JWT       |
| Database                | PostgreSQL                  |
| ORM                     | Spring Data JPA + Hibernate |
| AI description          | External AI API             |
| GPS capture             | Flutter location services   |
| Maps                    | Maps API                    |
| Complaint images        | Camera + Object Storage     |
| Resolution evidence     | Camera + Object Storage     |
| Push notifications      | Firebase Cloud Messaging    |
| API documentation       | OpenAPI/Swagger             |
| Backend testing         | JUnit + Mockito             |
| Flutter testing         | Flutter Test                |
| API testing             | Postman                     |
| Build                   | Maven                       |
| Version control         | Git/GitHub                  |
| Containerization        | Docker                      |
| CI/CD                   | GitHub Actions              |

---

# 43. Technology-to-Actor Mapping

| Component       | Citizen | Worker | Department Admin | System Admin |
| --------------- | :-----: | :----: | :--------------: | :----------: |
| Citizen App     |    ✓    |    —   |         —        |       —      |
| Worker App      |    —    |    ✓   |         —        |       —      |
| Admin Portal    |    —    |    —   |         ✓        |       ✓      |
| Spring Boot API |    ✓    |    ✓   |         ✓        |       ✓      |
| PostgreSQL      |    —    |    —   |         —        |       —      |
| AI Service      |    ✓    |    —   |         —        |       —      |
| GPS             |    ✓    |    ✓   |         —        |       —      |
| Maps            |    ✓*   |    ✓   |        ✓*        |      ✓*      |
| Notifications   |    ✓    |    ✓   |         ✓        |      ✓*      |
| Reports         |    —    |    —   |         ✓        |       ✓      |

`✓*` indicates optional or authorization-dependent functionality.

---

# 44. Technology Selection Rationale

## Flutter + Dart

Flutter is selected for the Citizen and Worker Apps because it provides cross-platform mobile development, consistent UI, rapid development, and access to device capabilities such as camera, GPS, and notifications.

## Java + Spring Boot

Spring Boot provides a mature framework for developing secure REST APIs and implementing the business logic required by CivicSnap.

## React + TypeScript

React provides a flexible framework for building the web-based administrative interface, while TypeScript improves type safety and maintainability.

## PostgreSQL

PostgreSQL is suitable for the relational nature of CivicSnap's data.

## Spring Security + JWT

This combination provides secure authentication and role-based authorization for the different CivicSnap actors.

## External AI Service

An external AI service allows CivicSnap to provide AI-assisted image analysis without requiring the project to develop and train a custom AI model.

## Firebase Cloud Messaging

FCM provides a practical mechanism for push notifications to the Flutter mobile applications.

## Object Storage

Separating image storage from the relational database improves scalability and keeps structured data independent from large media files.

---

# 45. Recommended Repository Structure

```text
CivicSnap/
│
├── backend/
│   ├── src/
│   ├── pom.xml
│   └── README.md
│
├── citizen-app/
│   ├── lib/
│   ├── test/
│   ├── pubspec.yaml
│   └── README.md
│
├── worker-app/
│   ├── lib/
│   ├── test/
│   ├── pubspec.yaml
│   └── README.md
│
├── admin-portal/
│   ├── src/
│   ├── package.json
│   └── README.md
│
├── documentation/
│   ├── srs.md
│   ├── functional-requirements.md
│   ├── non-functional-requirements.md
│   ├── user-stories.md
│   ├── business-rules.md
│   ├── system-constraints.md
│   ├── use-case-specifications.md
│   ├── use-case-matrix.md
│   ├── architecture-overview.md
│   └── technology-stack.md
│
├── infrastructure/
│   ├── docker/
│   └── deployment/
│
└── README.md
```

---

# 46. Minimum Development Stack

For the initial CivicSnap academic implementation, the minimum stack shall be:

### Mobile

```text
Flutter
Dart
Android Studio / VS Code
```

### Backend

```text
Java
Spring Boot
Spring Security
Spring Data JPA
Hibernate
Maven
```

### Database

```text
PostgreSQL
```

### Admin

```text
React
TypeScript
HTML
CSS
```

### Integration

```text
REST
JSON
AI API
GPS
Maps API
Firebase Cloud Messaging
```

### Development

```text
Git
GitHub
Postman
JUnit
Mockito
Flutter Test
```

---

# 47. Technology Constraints

The following constraints apply to the selected technology stack:

1. Backend development shall use Java Spring Boot.
2. Citizen and Worker Apps shall use Flutter and Dart.
3. Citizen and Worker Apps shall remain separate applications.
4. The Admin Portal shall be a web application.
5. REST APIs shall be used for client-backend communication.
6. PostgreSQL shall be used for structured relational data.
7. Backend business rules shall not depend solely on client-side implementation.
8. AI shall remain an external and replaceable integration.
9. GPS functionality shall depend on device capabilities and permissions.
10. Image files shall be securely validated and stored.
11. Production API communication shall use HTTPS.
12. Sensitive credentials shall not be stored in source control.
13. Backend authorization shall enforce all role permissions.
14. External service failures shall not corrupt core complaint data.

---

# 48. Future Technology Options

The architecture shall allow future adoption of:

* Redis
* WebSockets
* Message queues
* Kubernetes
* Cloud object storage
* Advanced monitoring
* GIS platforms
* Dedicated AI infrastructure
* Advanced analytics
* Real-time worker tracking
* Automated deployment infrastructure

These technologies are not required for the initial CivicSnap implementation.

---

# 49. Final Technology Stack

The final recommended CivicSnap stack is:

```text
┌────────────────────────────────────────────────────┐
│                    CLIENT LAYER                    │
│                                                    │
│  Citizen App              Worker App              │
│  Flutter + Dart            Flutter + Dart          │
│                                                    │
│                 Admin Portal                       │
│                 React + TypeScript                 │
└─────────────────────────┬──────────────────────────┘
                          │
                       HTTPS
                          │
                       REST API
                          │
┌─────────────────────────▼──────────────────────────┐
│                  BACKEND LAYER                     │
│                                                    │
│              Java + Spring Boot                    │
│                                                    │
│  Spring Web │ Security │ JPA │ Hibernate          │
│  JWT │ Validation │ Business Logic │ Reporting    │
└─────────────────────────┬──────────────────────────┘
                          │
             ┌────────────┼─────────────┐
             │            │             │
             ▼            ▼             ▼
       PostgreSQL    File Storage   External APIs
                                      │
                           ┌──────────┼──────────┐
                           ▼          ▼          ▼
                          AI        Maps        FCM
```

---

# 50. Final Technology Decision

CivicSnap shall use the following technologies for the initial implementation:

**Mobile Applications**

* Flutter
* Dart
* Citizen App
* Worker App

**Web Administration**

* React
* TypeScript

**Backend**

* Java
* Spring Boot
* Spring Security
* Spring Data JPA
* Hibernate

**Database**

* PostgreSQL

**API**

* REST
* JSON
* HTTPS

**Authentication**

* JWT
* Spring Security

**AI**

* External AI API

**Location**

* GPS/Location Services
* Maps API

**Media**

* Camera
* Object/File Storage

**Notifications**

* Firebase Cloud Messaging

**Testing**

* JUnit
* Mockito
* Spring Boot Test
* Flutter Test
* Postman

**Development**

* Maven
* Git
* GitHub
* Android Studio / VS Code

**Deployment**

* Docker
* Cloud/Server
* GitHub Actions

This stack provides a clear separation between the **Citizen App**, **Worker App**, **Admin Portal**, and **Spring Boot backend**, while allowing CivicSnap to scale from an academic prototype into a more complete civic service platform.
