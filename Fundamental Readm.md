# 🎓 Data Fundamentals: Admin & User Roles in E-Learning Platform

<div align="center">
  <img width="314" height="285" alt="Supabase Logo" src="https://github.com/user-attachments/assets/20661293-a214-4004-9042-657102fb0710" />
  <br/>
  <h3><b>Data Fundamentals Project — E-Learning System</b></h3>
</div>

---

## 📗 Table of Contents

* [📖 About the Project](#about-project)  
* [🛠 Built With](#built-with)  
* [🚀 Live Demo](#live-demo)  
* [💻 Getting Started](#getting-started)  
* [💾 Sample SQL Queries & Policies](#sample-sql-queries)   
* [🛡 Security Notes](#security-notes)  
* [👥 Authors](#authors)  
* [🔭 Future Features](#future-features)  
* [🤝 Contributing](#contributing)  
* [⭐️ Show your support](#support)  
* [🙏 Acknowledgements](#acknowledgements)  
* [❓ FAQ](#faq)  
* [📝 License](#license)  

---

# 📖 About the Project <a name="about-project"></a>

This project models an **E-Learning Management Database** that enforces **admin and learner roles** using **Row Level Security (RLS)** in Supabase.

It demonstrates how students (learners) interact with content while admins and instructors manage courses, quizzes, and progress.

It showcases:  
- ✅ UUID-based learner authentication (linked with Supabase Auth)  
- ✅ Admin, Instructor, and Learner privilege enforcement  
- ✅ CRUD operations for course management and enrollments  
- ✅ Role-based RLS setup with Supabase SQL  

---

## 🛠 Built With <a name="built-with"></a>

- **Supabase Dashboard** – for database & authentication  
- **PostgreSQL** – relational data management  
- **RLS Policies & Functions** – fine-grained access control  

---

## 🚀 Live Demo <a name="live-demo"></a>

- [Supabase Dashboard](https://app.supabase.com)  

---

## 💻 Getting Started <a name="getting-started"></a>

### Prerequisites
- Supabase account  
- Basic SQL knowledge  
- Git installed  

### Setup

```bash
git clone https://github.com/DENNIS-MURITHI/e-learning-database.git
cd e-learning-database
```

```bash
# Open Supabase SQL Editor
# Run schema.sql to create tables & populate with sample data

ALTER TABLE enrollments ENABLE ROW LEVEL SECURITY;
ALTER TABLE courses ENABLE ROW LEVEL SECURITY;
ALTER TABLE quizzes ENABLE ROW LEVEL SECURITY;
```

Then apply user, instructor, and admin RLS policies.

---

## 💾 Sample SQL Queries & Policies <a name="sample-sql-queries"></a>

### 1️⃣ Learner Policies
```sql
-- Learners can only view and update their own enrollments
CREATE POLICY "Learners can view their enrollments"
ON enrollments
FOR SELECT
USING (auth.uid() = learner_uuid);
```

```sql
-- Learners can enroll in courses
CREATE POLICY "Learners can enroll in courses"
ON enrollments
FOR INSERT
WITH CHECK (auth.uid() = learner_uuid);
```

```sql
-- Learners can view published courses
CREATE POLICY "Learners can view published courses"
ON courses
FOR SELECT
USING (status = 'Published');
```

---

### 2️⃣ Instructor Policies
```sql
-- Instructors can manage courses they own
CREATE POLICY "Instructors manage their courses"
ON courses
FOR ALL
USING (auth.uid() = instructor_uuid);
```

```sql
-- Instructors can manage quizzes for their courses
CREATE POLICY "Instructors manage their quizzes"
ON quizzes
FOR ALL
USING (EXISTS (
  SELECT 1 FROM courses 
  WHERE courses.course_id = quizzes.course_id
  AND courses.instructor_uuid = auth.uid()
));
```

---

### 3️⃣ Admin Policies
```sql
-- Admins can manage all data
CREATE POLICY "Admins can manage all tables"
ON courses
FOR ALL
USING (EXISTS (SELECT 1 FROM users WHERE user_uuid = auth.uid() AND role = 'admin'));
```

```sql
CREATE POLICY "Admins can manage all enrollments"
ON enrollments
FOR ALL
USING (EXISTS (SELECT 1 FROM users WHERE user_uuid = auth.uid() AND role = 'admin'));
```

---

### Example CRUD Queries & Outputs

**Learner Enrolling in Course**
```sql
INSERT INTO enrollments (learner_uuid, course_id)
VALUES ('8e37bb4a-7cc4-4fae-8c13-5b8b3ff8e21f', 2);
```
✅ Output: Enrollment added successfully.

**Instructor Updating Quiz**
```
