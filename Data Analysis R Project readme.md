# E-Learning-Platform

<div align="center">
  <img width="200" height="200" alt="E-Learning Logo" src="https://github.com/user-attachments/assets/5c8df95f-6d6e-402a-8e3f-092d4376c48a" />
  <br/>
  <h2><b>E-Learning Management Platform</b></h2>
</div>

# 📗 Table of Contents

* [📖 About the Project](#about-project)
  * [🛠 Built With](#built-with)
  * [Key Features](#key-features)
  * [🚀 Live Demo](#live-demo)
* [💻 Getting Started](#getting-started)
  * [Prerequisites](#prerequisites)
  * [Setup](#setup)
  * [Usage](#usage)
  * [Connecting from Posit to Supabase](#posit-supabase-connection)
* [💾 Schema SQL](#schema-sql)
* [📊 R Data Analysis](#r-data-analysis)
* [📖 Data Dictionary](#data-dictionary)
* [👥 Authors](#authors)
* [🔭 Future Features](#future-features)
* [🤝 Contributing](#contributing)
* [⭐️ Show your support](#support)
* [🙏 Acknowledgements](#acknowledgements)
* [❓ FAQ](#faq)
* [📝 License](#license)

---

# 📖 About the Project <a name="about-project"></a>

> This project models an **E-Learning Platform** backend database. It includes **students**, **courses**, and **enrollments** tables. The project allows tracking which students enroll in which courses, analyzing performance trends, and enforcing security roles (Admin vs. User) using Supabase row-level security policies.  
> The project integrates **R/Posit** for querying, analyzing, and visualizing academic and enrollment data.

---

## 🛠 Built With <a name="built-with"></a>

### Tech Stack

<details>
  <summary>Database & Hosting</summary>
  <ul>
    <li><a href="https://supabase.com">Supabase (PostgreSQL)</a> – backend database for table creation, data storage, and query management.</li>
  </ul>
</details>

<details>
  <summary>SQL Queries</summary>
  <ul>
    <li>Database schema setup, data seeding, and row-level security (RLS) policies.</li>
  </ul>
</details>

<details>
  <summary>R Data Analysis</summary>
  <ul>
    <li><a href="https://posit.co/">Posit / RStudio</a> for connecting to Supabase and performing data visualization and exploration.</li>
    <li>Libraries: DBI, RPostgres, dplyr, ggplot2</li>
  </ul>
</details>

---

### Key Features <a name="key-features"></a>

* Students can enroll in multiple courses.
* Admins manage all data; users can view or insert only their own.
* SQL policies ensure secure role-based access.
* R-based analysis for enrollment trends and student activity visualization.

---

## 🚀 Live Demo <a name="live-demo"></a>

> Backend-only project. Interact via Supabase SQL editor.

* [Supabase Dashboard](https://supabase.com/dashboard)

---

# 💻 Getting Started <a name="getting-started"></a>

### Prerequisites

* Supabase account  
* Posit / RStudio Cloud  
* R packages: DBI, RPostgres, dplyr, ggplot2  

---

### Setup

Clone the repository:

```bash
git clone https://github.com/edithothieno/e-learning-platform.git
cd e-learning-platform

Usage

Open your Supabase project, navigate to SQL Editor, and run:

\i week1_schema.sql


Open Posit (RStudio Cloud), create a new R Script (analysis.R), and run the connection setup code.

Connecting from Posit to Supabase <a name="posit-supabase-connection"></a>

Install required R packages:

install.packages(c("DBI", "RPostgres", "dplyr", "ggplot2"))


Create a file connect_db.R:

library(DBI)
connect_db <- function() {
  dbConnect(
    RPostgres::Postgres(),
    dbname = "postgres",
    host = "yourproject.supabase.co",
    port = 5432,
    user = "postgres",
    password = "YOUR_PASSWORD",
    sslmode = "require"
  )
}


Test connection:

source("connect_db.R")
con <- connect_db()
dbListTables(con)


✅ Output:

[1] "students" "courses" "enrollments"

💾 Schema SQL <a name="schema-sql"></a>
<details> <summary>Click to expand the full schema</summary>
-- Students Table
CREATE TABLE students (
  student_id SERIAL PRIMARY KEY,
  full_name VARCHAR(100),
  email VARCHAR(100) UNIQUE,
  user_id UUID REFERENCES auth.users(id)
);

-- Courses Table
CREATE TABLE courses (
  course_id SERIAL PRIMARY KEY,
  course_name VARCHAR(100),
  instructor VARCHAR(100)
);

-- Enrollments Table
CREATE TABLE enrollments (
  enrollment_id SERIAL PRIMARY KEY,
  student_id INT REFERENCES students(student_id),
  course_id INT REFERENCES courses(course_id),
  enrollment_date DATE DEFAULT CURRENT_DATE
);

-- Sample Data
INSERT INTO students (full_name, email) VALUES
('Alice Oduor', 'alice@example.com'),
('Brian Mwangi', 'brian@example.com'),
('Cynthia Otieno', 'cynthia@example.com');

INSERT INTO courses (course_name, instructor) VALUES
('Data Fundamentals', 'Dr. Wanjiru'),
('Database Design', 'Prof. Ouma'),
('R Programming', 'Dr. Kimani');

INSERT INTO enrollments (student_id, course_id)
VALUES (1, 1), (2, 2), (3, 3);

</details>
📊 R Data Analysis <a name="r-data-analysis"></a>
<details> <summary>Click to expand full R analysis code</summary>
source("connect_db.R")
library(DBI)
library(dplyr)
library(ggplot2)

con <- connect_db()

# Fetch enrollments
data <- dbGetQuery(con, "
  SELECT s.full_name, c.course_name, e.enrollment_date
  FROM enrollments e
  JOIN students s ON e.student_id = s.student_id
  JOIN courses c ON e.course_id = c.course_id;
")

print(head(data))

# Visualize enrollments per course
enroll_summary <- data %>%
  group_by(course_name) %>%
  summarise(total_students = n())

ggplot(enroll_summary, aes(x=reorder(course_name, total_students), y=total_students, fill=course_name)) +
  geom_col(show.legend=FALSE) +
  coord_flip() +
  labs(title="Student Enrollment per Course", x="Course", y="Number of Students") +
  theme_minimal()

</details>
📖 Data Dictionary <a name="data-dictionary"></a>
Table	Column	Type	Description
students	student_id	SERIAL	Primary key
	full_name	VARCHAR	Student’s name
	email	VARCHAR	Unique student email
	user_id	UUID	Linked to Supabase Auth
courses	course_id	SERIAL	Primary key
	course_name	VARCHAR	Course title
	instructor	VARCHAR	Course instructor
enrollments	enrollment_id	SERIAL	Primary key
	student_id	INT	FK → students
	course_id	INT	FK → courses
	enrollment_date	DATE	Auto-filled date
👥 Authors <a name="authors"></a>

👤 Edith Adikinyi Othieno

GitHub: @edithothieno

LinkedIn: Edith Othieno

🔭 Future Features <a name="future-features"></a>

Add course grades and attendance tracking

Include admin dashboard analytics in R or React

Allow file uploads and progress visualization

Add email notifications via Supabase Edge Functions

🤝 Contributing <a name="contributing"></a>

Contributions, issues, and feature requests are welcome!
Open an issue or submit a pull request.

⭐️ Show your support <a name="support"></a>

If you like this project, give it a ⭐️ on GitHub!

🙏 Acknowledgements <a name="acknowledgements"></a>

Supabase
 for free PostgreSQL hosting

Posit
 for interactive RStudio workspace

❓ FAQ <a name="faq"></a>

Q1: Why can’t I see my tables in Posit?
👉 Ensure your Supabase connection credentials (host, password) are correct.

Q2: How do I apply policies?
👉 Run your week2_policies.sql file in Supabase’s SQL Editor.

Q3: What packages are required in R?

install.packages(c("DBI", "RPostgres", "dplyr", "ggplot2"))

📝 License <a name="license"></a>

This project is licensed under the MIT License — see LICENSE
 for details.


---
