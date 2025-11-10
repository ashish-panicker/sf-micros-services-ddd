# Problem Statement — **SkillHub: A Collaborative Learning and Training Platform**

## 1. Vision & Business Context

SkillHub is a **digital learning ecosystem** that connects **trainers**, **learners**, and **organizations** through a shared platform.
The system supports **live training sessions**, **course marketplaces**, **certification workflows**, and **performance analytics** — all built on **domain-driven, microservice-based architecture**.

Your role as the **Solution Architect** is to **define the domain, identify subdomains, design bounded contexts, and map them to microservices**.
Then, outline aggregates, events, and integration strategies for a production-scale implementation.

## 2. Core Business Goals

1. Allow trainers to create and manage **courses, sessions, and certifications**.
2. Enable learners to **enroll in courses, complete lessons, and earn certificates**.
3. Support organizations to **track employee learning paths and compliance training**.
4. Provide **analytics and recommendations** for learners and managers.
5. Ensure a **secure, auditable, and scalable** system supporting multi-tenant access.

## 3. Strategic Design

### 3.1 Domain

**Domain:** *Learning & Training Management*

### 3.2 Subdomains

**Core**
- Course Management
- Session Management
- Enrollment 
**Generic**
- User Management
**Support**
- Search and Reccomendations

## 4. Bounded Contexts and Their Responsibilities

Course Context
- Entities: Course, Module, Lesson
- Aggregate Root: Course
- Rules: 
    -- One course must have atleast on module
    -- Owner/Admin can only modify the course
    -- Cannot be removed if there are active subscribers
- Events: CourseCreated, CourseArchived
- Service: course-service

```java
class Course {
    Integer courseId;
    String description;
    Integer durationHours;
    List<Module> modules;
}

class Module {
    Integer moduleId;
    String description;
    Integer durationHours;
    List<Lesson> lessons;
}

class Lesson {
    Integer lesson id;
    Blob content;
}

```

Enrollment Context
- Entities: Enrollment, Attendance
- Aggregate Root: Enrollment
- Events: EnrollmentUpdated, ProgressUpdated

```java
class Enrollment {
    Integer enrollmentId;
    Integer courseId;
    Integer traineeId;
}
```

## 6. Ubiquitous Language

Course, Module, Lesson, Enrollment, Progress, Trainer, Trainee, Assessment, Subscription

## 7. Domain Events

Event                   Consumer                Producer
CoursePublished         Search, Notification    Course
EnrollmentCreated       Course, Notification    Enrollment
ProgressUpdated         Analytics               EnrollmentService

## 8. Service Integration 

Learner Enrollment:
- Enrollment Service    -> Payment Service -> Notifcation   [SAGA]
- Course Published      -> Search & Notification            [Event Driven]

## 9. MVP

- User Service          -> Register Trainee/Trainer
- Course Service        -> Create and Publish Courses
- Enrollment Service    -> Enroll Trainee   
- Payment Service       -> Subscribing
- Notification Service  -> Send notification

## 10. Sample Worlflow 

1. Trainer registers and then created and publishes a course -> emits CourseCreated and CoursePublished
2. Leaner entrolls and subscribes the course -> emits EnrollmentCreated
3. Payment Service confirms payment -> emits PaymentSucessfull
4. Enrollment Service listens -> Marks enrollment Active, emits EnrollmentActivated
5. Notifcation Service -> Send email notifying the enrollment
