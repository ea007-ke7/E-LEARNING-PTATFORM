# 📖 Data Dictionary – E-learning Platform Database

This document explains the structure of the database, including each table and column.

---

## 🧑‍🎓 Students Table

**Table Name:** `students`  
Stores information about all students registered on the platform.

| Column       | Data Type      | Constraints            | Description                          |
|--------------|---------------|------------------------|--------------------------------------|
| `id`         | SERIAL        | Primary Key (PK)       | Unique identifier for each student.  |
| `name`       | VARCHAR(100)  | NOT NULL               | Full name of the student.            |
| `email`      | VARCHAR(100)  | UNIQUE, NOT NULL       | Student's email address (must be unique). |
| `created_at` | TIMESTAMP     | DEFAULT `NOW()`        | Timestamp when the student record was created. |

---

## 📚 Courses Table

**Table Name:** `courses`  
Stores information about available courses.

| Column       | Data Type      | Constraints            | Description                              |
|--------------|---------------|------------------------|------------------------------------------|
| `id`         | SERIAL        | Primary Key (PK)       | Unique identifier for each course.        |
| `title`      | VARCHAR(150)  | NOT NULL               | Course title.                            |
| `description`| TEXT          | NULL allowed           | Short description of the course content. |
| `created_at` | TIMESTAMP     | DEFAULT `NOW()`        | Timestamp when the course was created.   |

---

## 📝 Enrollments Table

**Table Name:** `enrollments`  
Stores relationships between students and courses (which student is enrolled in which course).

| Column        | Data Type      | Constraints                  | Description                                |
|---------------|---------------|------------------------------|--------------------------------------------|
| `id`          | SERIAL        | Primary Key (PK)             | Unique identifier for each enrollment.      |
| `student_id`  | INT           | Foreign Key → `students.id`  | The student who is enrolled.               |
| `course_id`   | INT           | Foreign Key → `courses.id`   | The course the student is enrolled in.     |
| `enrolled_at` | TIMESTAMP     | DEFAULT `NOW()`              | Timestamp when the student enrolled.       |

---

## 🔑 Relationships

- **One-to-Many**:  
  - A student can enroll in many courses → `students.id` → `enrollments.student_id`  
  - A course can have many students → `courses.id` → `enrollments.course_id`  

- **Many-to-Many (through enrollments)**:  
  - Students and Courses are linked through the `enrollments` table.

---

## 📊 ERD Reference
![Erd]()for a visual diagram of the relationships.
