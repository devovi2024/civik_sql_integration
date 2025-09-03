Bangladesh Civic & Learning Platform: Database Management System
📖 Project Overview
The Bangladesh Civic & Learning Platform is a comprehensive database system designed to bridge the gap between education, civic engagement, and community collaboration in Bangladesh. This platform integrates multiple stakeholders - students, citizens, government entities, and NGOs - into a unified system that supports learning, civic projects, campaigns, and community interactions.

🎯 Objectives
Design a normalized database schema for a multi-role platform

Implement efficient data relationships and constraints

Ensure data integrity through proper normalization

Create SQL queries for various platform operations

Develop a robust foundation for educational and civic activities

🏗️ Database Architecture
Modules
User & Role Management

User authentication and authorization

Role-based access control

Profile management

Learning Management

Course creation and management

Student enrollment and progress tracking

Assignments and assessments

Civic Projects & Campaigns

Project creation and funding

Participant management

Campaign organization

Community & Interaction

Discussion forums

Comments and reactions

Event management

Key Entities
Users: Platform users with different roles

Roles: Defines user permissions (Admin, Student, Citizen, Government, NGO)

Courses: Educational content and materials

Projects: Civic initiatives and development projects

Campaigns: Awareness and participation campaigns

Discussions: Community interaction threads

🗄️ Database Schema
Sample Table Structure
sql
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
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (role_id) REFERENCES roles(role_id)
);

-- Courses table
CREATE TABLE courses (
    course_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    instructor_id INT NOT NULL,
    start_date DATE,
    end_date DATE,
    status ENUM('active', 'inactive') DEFAULT 'active',
    FOREIGN KEY (instructor_id) REFERENCES users(user_id)
);
🔄 Relationships
One-to-One: Users ↔ Profiles

One-to-Many: Users → Courses (as instructor), Users → Projects (as creator)

Many-to-Many: Users ↔ Courses (through enrollments), Users ↔ Projects (through participants)

🛠️ Technical Implementation
Database System
DBMS: MySQL

Normalization: 3NF (Third Normal Form)

Constraints: Primary keys, Foreign keys, Unique constraints

Advanced Features
Views for simplified data access

Stored procedures for complex operations

Triggers for automated actions

Indexes for performance optimization

📊 Sample Queries
Data Retrieval
sql
-- Get all students
SELECT u.user_id, p.first_name, p.last_name, p.email
FROM users u
JOIN profiles p ON u.user_id = p.user_id
WHERE u.role_id = (SELECT role_id FROM roles WHERE role_name = 'Student');

-- Get course enrollments with progress
SELECT c.title, p.first_name, p.last_name, e.enrollment_date, e.progress
FROM enrollments e
JOIN users u ON e.user_id = u.user_id
JOIN profiles p ON u.user_id = p.user_id
JOIN courses c ON e.course_id = c.course_id;
Data Modification
sql
-- Update user profile
UPDATE profiles 
SET phone = '+8801712345678', address = 'Dhaka, Bangladesh'
WHERE user_id = 1;

-- Record a donation to a project
UPDATE projects 
SET current_fund = current_fund + 5000.00
WHERE project_id = 1;
🚀 Getting Started
Prerequisites
MySQL Server 8.0+

MySQL Workbench or similar database management tool

Installation
Clone the repository

Execute the SQL scripts in the following order:

Database schema creation

Sample data insertion

Views and stored procedures

Usage
Connect to the database using your preferred MySQL client

Execute queries based on your requirements

Use the provided stored procedures for common operations

📁 Project Structure
text
├── database_schema.sql     # Complete database schema
├── sample_data.sql         # Sample data for testing
├── queries.sql            # Useful SQL queries
├── stored_procedures.sql  # Stored procedures
└── triggers.sql           # Database triggers
🌟 Features
Role-based access control system

Comprehensive learning management

Civic project tracking and funding

Community interaction platform

Advanced database features (views, procedures, triggers)

Normalized database design (3NF)

🔮 Future Enhancements
Full-text search capabilities

Advanced reporting and analytics

Multimedia content support

Real-time notifications

Geographic data integration

Data encryption for sensitive information

👥 Contributors
Arfan Ovi - Developer

Green University of Bangladesh - Academic Institution

📄 License
This project is part of academic coursework at Green University of Bangladesh.

🙏 Acknowledgments
Department of Computer Science and Engineering, Green University of Bangladesh

Course Instructor: Fatema-Tuj- Johora


Database Systems concepts inspired by classic textbooks

