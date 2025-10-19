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

## 💾 Sample SQL Queries & Policies <a name="sample-sql-queries"></a>
### 1️⃣ Learner Policies
\`\`\`sql
-- Learners can view and enroll in courses
CREATE POLICY "Learners can view all courses"
ON courses
FOR SELECT
USING (true);

-- Learners can only manage their own enrollments
CREATE POLICY "Learners manage their own enrollments"
ON enrollments
FOR ALL
USING (auth.uid() = learner_uuid);

-- Learners can attempt quizzes they are enrolled in
CREATE POLICY "Learners can view assigned quizzes"
ON quizzes
FOR SELECT
USING (EXISTS (
  SELECT 1 FROM enrollments e 
  WHERE e.course_id = quizzes.course_id 
  AND e.learner_uuid = auth.uid()
));
\`\`\`

### 2️⃣ Instructor Policies
\`\`\`sql
-- Instructors can manage their own created courses
CREATE POLICY "Instructors manage their courses"
ON courses
FOR ALL
USING (auth.uid() = instructor_uuid);

-- Instructors can view learners enrolled in their courses
CREATE POLICY "Instructors view enrolled learners"
ON enrollments
FOR SELECT
USING (EXISTS (
  SELECT 1 FROM courses c 
  WHERE c.course_id = enrollments.course_id 
  AND c.instructor_uuid = auth.uid()
));

-- Instructors can create and update quizzes in their own courses
CREATE POLICY "Instructors manage quizzes"
ON quizzes
FOR ALL
USING (EXISTS (
  SELECT 1 FROM courses c 
  WHERE c.course_id = quizzes.course_id 
  AND c.instructor_uuid = auth.uid()
));
\`\`\`

### 3️⃣ Admin Policies
\`\`\`sql
-- Admins can manage all users, courses, and enrollments
CREATE POLICY "Admins manage all users"
ON users
FOR ALL
USING (EXISTS (
  SELECT 1 FROM users u WHERE u.user_uuid = auth.uid() AND u.role = 'admin'
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
### Learner View
\`\`\`sql
SELECT c.title AS course, i.full_name AS instructor
FROM enrollments e
JOIN courses c ON e.course_id = c.course_id
JOIN users i ON c.instructor_uuid = i.user_uuid
WHERE e.learner_uuid = '2d953804-5827-4f73-bfd5-41d83d53762f';
\`\`\`

### Instructor Creating a Course
\`\`\`sql
INSERT INTO courses (course_id, title, description, instructor_uuid)
VALUES (10, 'Data Ethics 101', 'Introduction to ethical data use.', auth.uid());
\`\`\`

### Admin Updating a Course
\`\`\`sql
UPDATE courses SET title = 'Data Science Fundamentals' WHERE course_id = 1;
\`\`\`

## 🛡 Security Notes <a name="security-notes"></a>
Full RLS, role enforcement, and function details available in 👉 **security_notes.md**

## 👥 Authors <a name="authors"></a>
**Dennis Murithi**  
GitHub: [@dennismurithi](https://github.com/dennismurithi)  
LinkedIn: Dennis Murithi

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
