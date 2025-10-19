# Data Fundamentals Project: Admin Roles & Security in Supabase

<a name="readme-top"></a>

<!-- TABLE OF CONTENTS -->

# 📗 Table of Contents

- [Data Fundamentals Project: Admin Roles & Security in Supabase](#about-project)
- [📗 Table of Contents](#-table-of-contents)
- [📖 About the Project](#about-project)
  - [🛠 Built With ](#-built-with-)
    - [Tech Stack ](#tech-stack-)
    - [Key Features ](#key-features-)
  - [💻 Getting Started ](#-getting-started-)
    - [Prerequisites](#prerequisites)
    - [Setup](#setup)
    - [Usage](#usage)
  - [👥 Authors ](#-authors-)
  - [🔭 Future Features ](#-future-features-)
  - [🤝 Contributing ](#-contributing-)

<!-- PROJECT DESCRIPTION -->

# 📖 About the Project <a name="about-project"></a>

**Data Fundamentals Project: Admin Roles & Security in Supabase** is a 4-week project designed to build, secure, and document a database using **PostgreSQL via Supabase**.  
The project focuses on access control using **Row Level Security (RLS)**, **user roles (Admin & User)**, and **SQL policies** to enforce data privacy and security.

The system models an **E-Learning Platform** with 3 main entities:
- Students  
- Courses  
- Enrollments  

## 🛠 Built With <a name="built-with"></a>

### Tech Stack <a name="tech-stack"></a>
- **PostgreSQL (Supabase)**
- **SQL**
- **Supabase Auth**
- **GitHub**

<!-- Features -->

### Key Features <a name="key-features"></a>

- [x] **Database schema with Students, Courses, and Enrollments**
- [x] **Row Level Security (RLS) enabled**
- [x] **Admin and User role management**
- [x] **Policies controlling data visibility and updates**
- [x] **Admin-only functions for secure data operations**

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

## 💻 Getting Started <a name="getting-started"></a>

Follow the steps below to set up and test this project on Supabase.

### Prerequisites

Before you begin, make sure you have:
- A [Supabase](https://supabase.com/) account
- Basic understanding of **SQL** and **database security**
- [Git](https://git-scm.com/) installed for version control

---

### Setup

Clone this repository to your local machine:

```bash
git clone https://github.com/<your-username>/data-fundamentals-project.git
cd data-fundamentals-project
Create a new project in Supabase
Then open the SQL Editor and execute each of the following scripts in order:

Week 1 – Database Setup
File: week1_schema.sql

This script:

Creates students, courses, and enrollments tables

Inserts sample data (5 rows per table)

Enables Row Level Security (RLS)

sql
Copy code
ALTER TABLE students ENABLE ROW LEVEL SECURITY;
ALTER TABLE courses ENABLE ROW LEVEL SECURITY;
ALTER TABLE enrollments ENABLE ROW LEVEL SECURITY;
✅ Screenshot example:

“Success. No rows returned” (after enabling RLS in Supabase)

Week 2 – Roles & Policies
File: week2_policies.sql

This script:

Defines Admin and User roles

Creates Row Level Security policies controlling read/write access

Example:

sql
Copy code
CREATE POLICY "Users can view own student record"
ON students
FOR SELECT
USING (auth.uid() = auth_id);

CREATE POLICY "Admins full access to students"
ON students
FOR ALL
USING (EXISTS (SELECT 1 FROM users WHERE id = auth.uid() AND role = 'admin'));
✅ Screenshot example:

Supabase Policy tab showing your created policies.

Week 3 – Admin-only Functions
File: week3_functions.sql

This script:

Defines custom functions restricted to Admins

Implements delete_course() and enroll_student()

Example:

sql
Copy code
CREATE OR REPLACE FUNCTION delete_course(course_id INT)
RETURNS VOID
LANGUAGE SQL
SECURITY DEFINER
AS $$
  DELETE FROM courses WHERE id = course_id;
$$;
✅ Screenshot example:

Function executed successfully in Supabase SQL Editor.

Week 4 – Submission & Documentation
Deliverables:

GitHub repo with all .sql files

security_notes.md explaining RLS and policies

ERD Diagram in /docs/erd.png

Presentation demo (optional)

Push your work:

bash
Copy code
git add .
git commit -m "Add Supabase schema, policies, and admin functions"
git push origin main
Then create a Pull Request (PR) to your class repository.

<p align="right">(<a href="#readme-top">back to top</a>)</p>
👥 Authors <a name="authors"></a>
👤 Edith Othieno

GitHub: @edithothieno

LinkedIn: Edith Othieno

Email: edithothieno@example.com

<p align="right">(<a href="#readme-top">back to top</a>)</p>
🔭 Future Features <a name="future-features"></a>
 Add detailed analytics dashboard using SQL views

 Implement Supabase Auth UI for login management

 Integrate with a frontend (React/Next.js)

<p align="right">(<a href="#readme-top">back to top</a>)</p>
🤝 Contributing <a name="contributing"></a>
Contributions, issues, and feature requests are welcome!
Feel free to check the issues page.

<p align="right">(<a href="#readme-top">back to top</a>)</p> ```
