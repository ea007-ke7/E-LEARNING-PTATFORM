# 🛡 Security Notes: E-Learning Data Fundamentals Platform

<div align="center">
  <img width="380" height="240" alt="Security ERD" src="https://github.com/user-attachments/assets/placeholder-security-erd.png" />
  <br/>
  <b>Entity-Relationship Diagram of E-Learning Database Schema</b>
</div>

---

## 📖 Overview

This document outlines **Row Level Security (RLS)** and **Access Control Policies** for the **E-Learning Data Fundamentals Platform**.  
The setup ensures secure data operations between roles:
- **Admin**
- **Instructor**
- **Learner**

All policies follow the **principle of least privilege** — each user can access or modify only the data they own or manage.

---

## 🧱 Core Tables

| Table | Description |
|-------|--------------|
| `users` | Stores registered users, roles, and UUIDs |
| `courses` | Contains course information and ownership |
| `enrollments` | Links learners to courses |

---

## 🔐 Role Descriptions

| Role | Privileges |
|------|-------------|
| **Admin** | Full CRUD on all tables |
| **Instructor** | CRUD on courses they own; read-only on others |
| **Learner** | Read course content; update their progress and view grades |

## ⚙️ Enabling RLS

```sql
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
ALTER TABLE courses ENABLE ROW LEVEL SECURITY;
ALTER TABLE enrollments ENABLE ROW LEVEL SECURITY;


---

## 🧩 Security Policies by Role

### 🧑‍🎓 Learner Policies
```sql
-- Learners can view all public courses
CREATE POLICY "Learners can view public courses"
ON courses
FOR SELECT
USING (visibility = 'public');

-- Learners can view only their enrollments
CREATE POLICY "Learners view their own enrollments"
ON enrollments
FOR SELECT
USING (auth.uid() = learner_uuid);

-- Learners can update their progress
CREATE POLICY "Learners update their own progress"
ON progress
FOR UPDATE
USING (auth.uid() = learner_uuid);
```

---

### 🧑‍🏫 Instructor Policies
```sql
-- Instructors can manage their own courses
CREATE POLICY "Instructors manage their own courses"
ON courses
FOR ALL
USING (auth.uid() = instructor_uuid);

-- Instructors can view learners in their courses
CREATE POLICY "Instructors view enrollments in owned courses"
ON enrollments
FOR SELECT
USING (EXISTS (
  SELECT 1 FROM courses c
  WHERE c.course_id = enrollments.course_id
  AND c.instructor_uuid = auth.uid()
));

-- Instructors assign and update grades
CREATE POLICY "Instructors manage grades for their courses"
ON grades
FOR ALL
USING (EXISTS (
  SELECT 1 FROM courses c
  WHERE c.course_id = grades.course_id
  AND c.instructor_uuid = auth.uid()
));
```

---

### 🧑‍💼 Admin Policies
```sql
-- Admins can access everything
CREATE POLICY "Admins full access"
ON users, courses, enrollments, progress, grades, content
FOR ALL
USING (EXISTS (
  SELECT 1 FROM users
  WHERE user_uuid = auth.uid() AND role = 'admin'
));
```

---

## 🔍 Example Role Validation Queries

```sql
-- Check current user's role
SELECT role FROM users WHERE user_uuid = auth.uid();

-- Verify RLS enforcement for current user
SELECT * FROM enrollments;
```

✅ *If RLS is active, only authorized rows will appear.*

---

## 🔬 Testing RLS in Supabase

1. Log in as different users (`admin@edu.com`, `instructor@edu.com`, `learner@edu.com`).
2. Use the Supabase SQL editor to run CRUD operations.
3. Confirm that access restrictions match the defined policies.

---

## 📊 Analytics Section

<div align="center">
  <img width="520" height="300" alt="Power BI Dashboard Preview" src="https://github.com/user-attachments/assets/placeholder-powerbi-preview.png" />
  <br/>
  <b>Power BI Dashboard Preview – Course Engagement & Completion Analytics</b>
</div>

**Dashboard Insights:**
- Enrollment trends by course and cohort  
- Completion rate vs. dropout  
- Instructor performance metrics  
- Learner progress heatmaps  

---

## ⚠️ Security Best Practices

- Always enable RLS before deploying production tables  
- Avoid exposing API keys in front-end code  
- Use **Supabase Auth** for session-based access  
- Audit policy changes via version control  
- Validate UUID references before inserts or updates  

---

## 🔐 Policy Verification

You can verify that your RLS setup works as expected by querying Supabase’s built-in catalog:

```sql
SELECT tablename, policyname, permissive
FROM pg_policies
WHERE schemaname = 'public';
```

---

## 🧭 Notes

This setup is adaptable for **scalable multi-tenant LMS platforms**, ensuring:
- Data privacy per learner
- Safe instructor access
- Centralized admin control

---

## 📜 License

This project is licensed under the **MIT License**.

---

> **Author:** [Dennis Murithi](https://github.com/dennismurithi)  
> LinkedIn: [Dennis Murithi](https://www.linkedin.com/in/dennis-murithi)
