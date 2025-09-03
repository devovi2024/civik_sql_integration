# Bangladesh Civic & Learning Platform — Database Management System

## 📖 Project Overview

The **Bangladesh Civic & Learning Platform** is a unified database system that brings together education, civic engagement, and community collaboration in Bangladesh. The DBMS is designed to support multiple stakeholders — students, citizens, government entities, and NGOs — with data structures for courses, civic projects, campaigns, events, and community interaction.

---

## 🎯 Objectives

- Design a normalized relational database schema for a multi-role platform (3NF)
- Implement efficient relationships and constraints for integrity and performance
- Provide useful SQL queries, views, stored procedures, and triggers for common operations
- Build a foundation for learning, civic projects, fundraising, and community discussions

---

## 🏗️ Database Architecture

### Modules
- **User & Role Management** — authentication, role-based access, profiles
- **Learning Management** — courses, enrollments, assignments, progress
- **Civic Projects & Campaigns** — project creation, funding, participants
- **Community & Interaction** — discussions, comments, events, reactions

### Key Entities
- `users` — platform users (Admin, Student, Citizen, Government, NGO)
- `roles` — defines permissions and role metadata
- `profiles` — extended user information (one-to-one with `users`)
- `courses`, `enrollments`, `assignments` — learning content & tracking
- `projects`, `project_participants`, `donations` — civic initiatives & funding
- `campaigns`, `events` — outreach and scheduled activities
- `discussions`, `comments`, `reactions` — community interaction

---

## 🗄️ Example Database Schema (MySQL)

> The following is a sample of key tables. For the full schema, see `database_schema.sql` in the repository.

```sql
-- Roles table
CREATE TABLE roles (
    role_id INT PRIMARY KEY AUTO_INCREMENT,
    role_name VARCHAR(50) NOT NULL UNIQUE,
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Users table
CREATE TABLE users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(255) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    role_id INT NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (role_id) REFERENCES roles(role_id)
);

-- Profiles table (one-to-one with users)
CREATE TABLE profiles (
    user_id INT PRIMARY KEY,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    phone VARCHAR(50),
    address VARCHAR(255),
    bio TEXT,
    avatar_url VARCHAR(255),
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

-- Courses table
CREATE TABLE courses (
    course_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    instructor_id INT NOT NULL,
    start_date DATE,
    end_date DATE,
    status ENUM('active','inactive') DEFAULT 'active',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (instructor_id) REFERENCES users(user_id)
);

-- Enrollments (many-to-many users <-> courses)
CREATE TABLE enrollments (
    enrollment_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    course_id INT NOT NULL,
    enrollment_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    progress DECIMAL(5,2) DEFAULT 0.00,
    UNIQUE KEY unique_enrollment (user_id, course_id),
    FOREIGN KEY (user_id) REFERENCES users(user_id),
    FOREIGN KEY (course_id) REFERENCES courses(course_id)
);

-- Projects table
CREATE TABLE projects (
    project_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    creator_id INT NOT NULL,
    goal_amount DECIMAL(14,2) DEFAULT 0.00,
    current_fund DECIMAL(14,2) DEFAULT 0.00,
    start_date DATE,
    end_date DATE,
    status ENUM('draft','active','completed','cancelled') DEFAULT 'draft',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (creator_id) REFERENCES users(user_id)
);

-- Project participants (many-to-many)
CREATE TABLE project_participants (
    id INT PRIMARY KEY AUTO_INCREMENT,
    project_id INT NOT NULL,
    user_id INT NOT NULL,
    role VARCHAR(100), -- e.g., volunteer, coordinator
    joined_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE KEY unique_participation (project_id, user_id),
    FOREIGN KEY (project_id) REFERENCES projects(project_id),
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

-- Donations
CREATE TABLE donations (
    donation_id INT PRIMARY KEY AUTO_INCREMENT,
    project_id INT NOT NULL,
    donor_id INT, -- optional for anonymous donations
    amount DECIMAL(14,2) NOT NULL,
    donated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (project_id) REFERENCES projects(project_id),
    FOREIGN KEY (donor_id) REFERENCES users(user_id)
);

-- Discussions and comments
CREATE TABLE discussions (
    discussion_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    body TEXT,
    creator_id INT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (creator_id) REFERENCES users(user_id)
);

CREATE TABLE comments (
    comment_id INT PRIMARY KEY AUTO_INCREMENT,
    discussion_id INT NOT NULL,
    user_id INT NOT NULL,
    body TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (discussion_id) REFERENCES discussions(discussion_id),
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

---

## 🔄 Relationships (Examples)
- **One-to-One:** `users` ↔ `profiles` (extended user data)
- **One-to-Many:** `users` (instructor) → `courses`
- **Many-to-Many:** `users` ↔ `courses` via `enrollments`
- **Many-to-Many:** `users` ↔ `projects` via `project_participants`

---

## 🛠️ Technical Implementation
- **DBMS:** MySQL 8+
- **Normalization:** Third Normal Form (3NF)
- **Constraints:** Primary keys, foreign keys, unique constraints
- **Indexes:** Add indexes on FK columns and frequently queried columns (e.g., `email`, `created_at`, `status`)
- **Advanced:** Use views for reporting, stored procedures for transactional operations (donations, enrollments), and triggers for audit logging or denormalized counters.

---

## ⚙️ Sample Queries

### Data Retrieval
```sql
-- Get all students (join users & profiles)
SELECT u.user_id, p.first_name, p.last_name, u.email
FROM users u
JOIN profiles p ON u.user_id = p.user_id
WHERE u.role_id = (SELECT role_id FROM roles WHERE role_name = 'Student');

-- Course enrollments with progress
SELECT c.title, p.first_name, p.last_name, e.enrollment_date, e.progress
FROM enrollments e
JOIN users u ON e.user_id = u.user_id
JOIN profiles p ON u.user_id = p.user_id
JOIN courses c ON e.course_id = c.course_id;
```

### Data Modification
```sql
-- Update user profile
UPDATE profiles
SET phone = '+8801712345678', address = 'Dhaka, Bangladesh'
WHERE user_id = 1;

-- Record a donation to a project (transaction recommended)
START TRANSACTION;
UPDATE projects
SET current_fund = current_fund + 5000.00
WHERE project_id = 1;
INSERT INTO donations (project_id, donor_id, amount)
VALUES (1, 2, 5000.00);
COMMIT;
```

---

## 📊 Advanced Features (Suggested)
- **Views** for simplified queries (e.g., `view_course_progress`, `view_project_funding`)
- **Stored procedures** for operations like `sp_add_donation`, `sp_enroll_student`
- **Triggers** for audit logging and maintaining denormalized counters (e.g., `projects.current_fund` updates)
- **Full-text search** on discussion and course content (MySQL FULLTEXT or external search engine)

---

## 🚀 Getting Started

### Prerequisites
- MySQL Server 8.0+
- MySQL Workbench or preferred DB client

### Installation
1. Clone the repository.
2. Run SQL scripts (in this order):
   - `database_schema.sql`
   - `sample_data.sql`
   - `views_and_procedures.sql`
   - `triggers.sql`

### Usage
- Connect with your MySQL client and run the prepared queries or call stored procedures.
- Use transactions for multi-step updates (donations, enrollments).

---

## 📁 Project Structure

```
├── database_schema.sql     # Complete database schema
├── sample_data.sql         # Sample data for testing
├── queries.sql             # Useful SQL queries
├── stored_procedures.sql   # Stored procedures
└── triggers.sql            # Database triggers
```

---

## 🌟 Features
- Role-based access control
- Comprehensive learning management (courses, enrollments, progress)
- Civic project tracking, fundraising, and participant management
- Community discussions, comments, and reactions
- Advanced DB features (views, procedures, triggers)
- Normalized design (3NF) for data integrity

---

## 🔮 Future Enhancements
- Full-text and faceted search
- Rich multimedia content and storage (S3 / Cloud storage integration)
- Real-time notifications (WebSocket/Push)
- Geographic data & mapping (PostGIS or MySQL spatial types)
- Field-level encryption for sensitive data
- Advanced reporting and analytics dashboards

---

## 👥 Contributors
- **Arfan Ovi** — Developer
- Green University of Bangladesh — Academic Institution

---

## 📄 License
This project is part of academic coursework at Green University of Bangladesh. Include your institution's license or preferred open-source license here.

---

## 🙏 Acknowledgments
Department of Computer Science and Engineering, Green University of Bangladesh

Course Instructor: Fatema-Tuj- Johora


Database Systems concepts inspired by standard textbooks and best practices.

---


