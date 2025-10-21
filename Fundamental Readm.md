# 📖 Data Fundamentals: Admin, Instructor & Learner Roles in E-Learning Platform
<div align="center">
  <img width="314" height="285" alt="Supabase Logo" src="https://github.com/user-attachments/assets/20661293-a214-4004-9042-657102fb0710" />
  <br/>
  <h3><b>Data Fundamentals Project</b></h3>
</div>

## 📗 Table of Contents
- [📖 About the Project](#about-project)
- [🛠 Built With](#built-with)
- [🚀 Live Demo](#live-demo)
- [💻 Getting Started](#getting-started)
- [💾 Sample SQL Queries & Policies](#sample-sql-queries)
- [🛡 Security Notes](#security-notes)
- [👥 Authors](#authors)
- [🔭 Future Features](#future-features)
- [🤝 Contributing](#contributing)
- [⭐️ Show your support](#support)
- [🙏 Acknowledgements](#acknowledgements)
- [❓ FAQ](#faq)
- [📝 License](#license)

## 📖 About the Project <a name="about-project"></a>
This project models an E-Learning Platform database using Supabase and PostgreSQL, with focus on Admin, and User roles enforced through Row Level Security (RLS).

It demonstrates:
- ✅ Role-based access control (Admin, Users )
- ✅ Secure CRUD operations via RLS
- ✅ Enrolled course management per user
- ✅ Policies for content creation and evaluation
- ✅ SQL validation and tested outputs from Supabase

## 🛠 Built With <a name="built-with"></a>

- **Supabase Dashboard** – SQL editor, authentication & policies 
- **PostgreSQL** – database and tables  
- **RLS Policies & Functions** – enforce admin/user restrictions  

---

## 🚀 Live Demo <a name="live-demo"></a>

- [Supabase Dashboard](https://app.supabase.com)  

---

## 💻 Getting Started <a name="getting-started"></a>
### Prerequisites
- Supabase account  
- Basic knowledge of SQL and PostgreSQL  
- Git installed  

### Setup

```bash
git clone https://github.com/ea007-ke7/E-LEARNING-PTATFORM-database.git
cd music-streaming-database
```

```bash

Usage

Open Supabase SQL editor

Run schema.sql to create tables & sample data

Apply RLS policies:

ALTER TABLE user_favorites ENABLE ROW LEVEL SECURITY;
ALTER TABLE songs ENABLE ROW LEVEL SECURITY;
ALTER TABLE artists ENABLE ROW LEVEL SECURITY;


Apply user vs admin policies.
```

---

## 💾 Sample SQL Queries & Policies <a name="sample-sql-queries"></a>

### 1️⃣ User Policies
```sql
-- Users can only read their own data
CREATE POLICY "Users can view own student record"
ON students
FOR SELECT
USING (auth.uid() = auth_id);
```

```sql
-- Users can update their own record
CREATE POLICY "Users can update own student record"
ON students
FOR UPDATE
USING (auth.uid() = auth_id)
WITH CHECK (auth.uid() = auth_id);
```

```sql
-- Everyone can view courses
CREATE POLICY "All users can view courses"
ON courses
FOR SELECT
USING (true);
```

---
### 2️⃣  Admin Policies

```sql
-- Admins have full access (read, insert, update, delete)
CREATE POLICY "Admins full access to students"
ON students
FOR ALL
USING (EXISTS (SELECT 1 FROM users WHERE id = auth.uid() AND role = 'admin'));
```

```sql
-- Admins can manage (insert, update, delete) all courses
CREATE POLICY "Admins manage all courses"
ON courses
FOR ALL
USING (EXISTS (SELECT 1 FROM users WHERE id = auth.uid() AND role = 'admin'));
```

```sql
-- Admins can manage all enrollments
CREATE POLICY "Admins full access to enrollments"
ON enrollments
FOR ALL
USING (EXISTS (SELECT 1 FROM users WHERE id = auth.uid() AND role = 'admin'));
```

---

### 3️⃣ Example CRUD Queries with their output.

**Student Queries and Output**
```sql
-- Students can view their coursemates
SELECT c.title, s.name AS student_name, e.enrolled_at
FROM enrollments e
JOIN students s ON e.student_id = s.id
JOIN courses c ON e.course_id = c.id
WHERE c.title = 'SQL Basics';
```
<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/d3296562-c981-48e6-9aec-f77aacee5782" />

```sql
--Students can browse what courses exist.
SELECT id, title, description
FROM courses
ORDER BY created_at;
```
<img width="1186" height="645" alt="Capture i" src="https://github.com/user-attachments/assets/c2cc1699-b98c-4b4e-9137-526940208058" />

### Admin Can View all Students
```sql
-- Admin can view all students
SELECT id, name 
FROM students;
```
<img width="1107" height="579" alt="Capture 2" src="https://github.com/user-attachments/assets/de45c463-868a-47b8-b77b-bd75c92176a8" />


### Admin Updating a Course
```sql
-- Admin can Insert New Courses
INSERT INTO courses (title, description)
VALUES ('Multimedia', 'learn HTML');

-- Output after Inserting a New Course
SELECT * FROM courses;

<img width="856" height="531" alt="Capture 3" src="https://github.com/user-attachments/assets/6800b64a-0e95-4a85-a3cd-a1b63f2f4bb5" />

-- Admin can Delete Courses
DELETE FROM courses
WHERE title = 'Multimedia';

-- Output after Deleting a Course


## 🛡 Security Notes <a name="security-notes"></a>
Full RLS, role enforcement, and function details available in 👉 **security_notes.md**

## 👥 Authors <a name="authors"></a>
**Edith Adikinyi**  
GitHub: [@](https://github.com/)  
LinkedIn: 

## 🔭 Future Features <a name="future-features"></a>
- Integrate with front-end E-Learning dashboard  
- Add analytics for course performance and engagement  
- Implement AI quiz generation and grading  

## 🤝 Contributing <a name="contributing"></a>
Pull requests and issue reports are welcome.

## ⭐️ Show your support <a name="support"></a>
Give a ⭐️ if you like this project and found it helpful!

## 🙏 Acknowledgements <a name="acknowledgements"></a>
- Supabase docs for RLS and Auth policies  
- PostgreSQL official documentation  
- Open Education API design guidelines  

## ❓ FAQ <a name="faq"></a>
**Q:** Can I connect this to a front-end app?  
**A:** Yes — use Supabase Auth + APIs with Next.js or React front-end.

**Q:** How do I test policies?  
**A:** Log in as Admin, or User and perform CRUD operations in Supabase SQL Editor.

## 📝 License <a name="license"></a>
This project is licensed under the **MIT License** — see the LICENSE file for details
