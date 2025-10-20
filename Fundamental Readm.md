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
This project models an E-Learning Platform database using Supabase and PostgreSQL, with focus on Admin, Instructor, and Learner roles enforced through Row Level Security (RLS).

It demonstrates:
- ✅ Role-based access control (Admin, Instructor, Learner)
- ✅ Secure CRUD operations via RLS
- ✅ Enrolled course management per user
- ✅ Policies for content creation and evaluation
- ✅ SQL validation and tested outputs from Supabase

## 🛠 Built With <a name="built-with"></a>
- Supabase Dashboard – SQL editor, authentication & policies  
- PostgreSQL – database, functions, and triggers  
- RLS Policies – secure access control at table level  

## 🚀 Live Demo <a name="live-demo"></a>
- Supabase Dashboard

## 💻 Getting Started <a name="getting-started"></a>
### Prerequisites
- Supabase account  
- Basic knowledge of SQL and PostgreSQL  
- Git installed  

### Setup
\`\`\`bash
git clone https://github.com/DENNIS-MURITHI/e-learning-database.git
cd e-learning-database
\`\`\`

### Usage
1. Open Supabase SQL Editor  
2. Run `schema.sql` to create tables and seed data  
3. Apply RLS policies:
\`\`\`sql
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
ALTER TABLE courses ENABLE ROW LEVEL SECURITY;
ALTER TABLE enrollments ENABLE ROW LEVEL SECURITY;
ALTER TABLE quizzes ENABLE ROW LEVEL SECURITY;
\`\`\`
4. Apply user vs instructor vs admin policies as below.

<img width="903" height="326" alt="image" src="https://github.com/user-attachments/assets/ac441b88-fe39-4c9d-8be6-b5406c1afa38" />

## 💾 Sample SQL Queries & Policies <a name="sample-sql-queries"></a>
### 1️⃣ Users Policies
\`\`\`sql
-- restricted access (users can only read and insert their own data)
CREATE POLICY "Users can view own student record"
ON students
FOR SELECT
USING (auth.uid() = auth_id);

-- Users can update their own record
CREATE POLICY "Users can update own student record"
ON students
FOR UPDATE
USING (auth.uid() = auth_id)
WITH CHECK (auth.uid() = auth_id);

-- Everyone can view courses
CREATE POLICY "All users can view courses"
ON courses
FOR SELECT
USING (true);
\`\`\`

### 2️⃣  Admin Policies

\`\`\`sql
-- Admins can manage (insert, update, delete) all courses
CREATE POLICY "Admins manage all courses"
ON courses
FOR ALL
USING (
  EXISTS (
    SELECT 1 FROM users WHERE id = auth.uid() AND role = 'admin'
  ));

-- Admins can manage all enrollments
CREATE POLICY "Admins full access to enrollments"
ON enrollments
FOR ALL
USING (
  EXISTS (
    SELECT 1 FROM users WHERE id = auth.uid() AND role = 'admin'
  ));

CREATE POLICY "Admins manage all courses"
ON courses
FOR ALL
USING (EXISTS (
  SELECT 1 FROM users u WHERE u.user_uuid = auth.uid() AND u.role = 'admin'
));


CREATE POLICY "Admins manage all enrollments"
ON enrollments
FOR ALL
USING (EXISTS (
  SELECT 1 FROM users u WHERE u.user_uuid = auth.uid() AND u.role = 'admin'
));
\`\`\`

## 🧠 Example Queries and Outputs

### Admin Can View all Courses
\`\`\`sql
-- Admin can view all students
SELECT id, name 
FROM students;
\`\`\`
<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/9c929974-ab62-495a-969c-51898e7f5132" />

### Admin Updating a Course
\`\`\`sql
UPDATE courses SET title = 'Data Science Fundamentals' WHERE course_id = 1;

-- Admin can Insert New Courses
INSERT INTO courses (title, description)
VALUES ('Multimedia', 'learn HTML');

-- Output after Inserting a New Course
SELECT * FROM courses;

-- Admin can Delete Courses
DELETE FROM courses
WHERE title = 'Multimedia';

-- Output after Deleting a Course
DELETE FROM courses
WHERE title = 'Multimedia'

**Student Queries and Output**
-- Students can view their coursemates
SELECT c.title, s.name AS student_name, e.enrolled_at
FROM enrollments e
JOIN students s ON e.student_id = s.id
JOIN courses c ON e.course_id = c.id
WHERE c.title = 'SQL Basics';

--Students can browse what courses exist.
SELECT id, title, description
FROM courses
ORDER BY created_at;
\`\`\`
<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/d3296562-c981-48e6-9aec-f77aacee5782" />

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/7e778970-6df8-40c8-8b0b-321685d0bed5" />

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
**A:** Log in as Admin, Instructor, or Learner and perform CRUD operations in Supabase SQL Editor.

## 📝 License <a name="license"></a>
This project is licensed under the **MIT License** — see the LICENSE file for detai
