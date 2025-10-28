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

> This project models an **E-Learning Platform** database. It includes **students**, **courses**, and **enrollments** tables. The project allows tracking which students enroll in which courses, and enforcing security roles (Admin vs. User) using Supabase row-level security policies.  
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

<p align="right"><a href="#about-project">back to top</a></p>
---

## 🚀 Live Demo <a name="live-demo"></a>

> Backend-only project. Interact via Supabase SQL editor.

* [Supabase Project Link](https://supabase.com/dashboard/project/mhsnciubwnzincjbnpxj/sql/84e4c0de-e88e-4856-b5f2-437275cec772)
  
<p align="right"><a href="#about-project">back to top</a></p>

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
git clone https://github.com/ea007-ke7/E-LEARNING-PTATFORM.git

### Usage

1. Open Supabase and create a new project.
2. Access the SQL editor and execute `schema.sql` to create tables and insert sample data:

```sql
\i schema.sql
```

3. A quick taste of how R posit code would look like:

```r
# Load connection
source("connect_db.R")
con <- connect_db()

# Total students per course
query <- "
  SELECT c.title AS course_title,
  COUNT(e.student_id) AS total_students
  FROM courses c
  LEFT JOIN enrollments e ON c.id = e.course_id
  GROUP BY c.title
  ORDER BY total_students DESC;
"

# Execute query and store results
course_enrollments <- dbGetQuery(con, query)

# View results
print(course_enrollments)

# Example output:
#   course_title               total_students
# 1 SQL Basics                           2
# 2 Python for Beginners                 1
# 3 Web Development 101                  1
# 4 Intro to Machine Learning            1
# 5 Data Analysis with SQL               0

```
# Outcome upon running the code

<img width="1299" height="436" alt="enrollments per courses" src="https://github.com/user-attachments/assets/579d0d78-3621-4394-a1d4-9136e12f05cd" />


---

### Connecting from Posit to Supabase <a name="posit-supabase-connection"></a>

1. Install required R packages:

```r
install.packages(c("DBI", "RPostgres", "dplyr", "ggplot2"))
```
# Successful Package Installation
<img width="1357" height="670" alt="Successful installation" src="https://github.com/user-attachments/assets/4ff847a5-546d-4ce4-9967-426042bbbe65" />


2. Create a `connect_db.R` file:

```r
library(DBI)
library(RPostgres)

connect_db <- function() {
  con <- dbConnect(
    RPostgres::Postgres(),
    host = "aws-1-eu-north-1.pooler.supabase.com",
    port = 6543,
    dbname = "postgres",
    user = "postgres.mhsnciubwnzincjbnpxj",
    password = "cccccc",
    sslmode = "require"
  )
  return(con)
}

```

3. Use this connection in R scripts:

```r
source("connect_db.R")
con <- connect_db()
dbListTables(con)
```

---
# Outcome after establishing connection

<img width="1361" height="685" alt="connect_db R" src="https://github.com/user-attachments/assets/3d8d6e66-ec0d-481f-b077-af5ecf893623" />



# 💾 Must Have Schema SQL <a name="schema-sql"></a>


<details>
  <summary>Click to expand the full schema.sql that you must run in supabase before you create a conection to posit studi</summary>

```sql
-- Students Table
CREATE TABLE students (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT NOW()
);

-- Courses Table
CREATE TABLE courses (
    id SERIAL PRIMARY KEY,
    title VARCHAR(150) NOT NULL,
    description TEXT,
    created_at TIMESTAMP DEFAULT NOW()
);

-- Enrollments Table
CREATE TABLE enrollments (
    id SERIAL PRIMARY KEY,
    student_id INT REFERENCES students(id),
    course_id INT REFERENCES courses(id),
    enrolled_at TIMESTAMP DEFAULT NOW()
);

-- Students
INSERT INTO students (name, email, created_at) VALUES
('Jeanne Karanu', 'jkaranu@gmail.com', '2025-09-01 10:00:00'),
('Joseline Othieno', 'jothieno@gmail.com', '2025-09-02 11:15:00'),
('Charles Weko', 'charlesweko@egmail.com', '2025-09-03 09:45:00'),
('Diana Juma', 'dianaj@gmail.com', '2025-09-04 14:20:00'),
('Ethan Liko', 'ethanliko@gmail.com', '2025-09-05 16:10:00');

-- Courses
INSERT INTO courses (title, description, created_at) VALUES
('SQL Basics', 'Intro to SQL and Databases', '2025-09-01 08:00:00'),
('Python for Beginners', 'Learn Python fundamentals', '2025-09-01 09:30:00'),
('Web Development 101', 'HTML, CSS, and JavaScript basics', '2025-09-02 10:00:00'),
('Data Analysis with SQL', 'Analyze data using SQL queries', '2025-09-03 11:00:00'),
('Intro to Machine Learning', 'Basics of ML algorithms', '2025-09-04 12:00:00');

-- Enrollments
INSERT INTO enrollments (student_id, course_id, enrolled_at) VALUES
(1, 1, '2025-09-06 09:00:00'),
(1, 2, '2025-09-06 09:15:00'),
(2, 3, '2025-09-07 10:30:00'),
(3, 1, '2025-09-08 11:00:00'),
(4, 5, '2025-09-09 13:45:00');

-- Example query
```sql
SELECT * courses;
```

```sql
-- View all students and their enrolled courses
SELECT s.name AS student_name, s.email,
   c.title AS course_title, e.enrolled_at
FROM enrollments e
JOIN students s ON e.student_id = s.id
JOIN courses c ON e.course_id = c.id
ORDER BY s.name;

```

```sql
-- Show all courses and the students enrolled in each
SELECT  c.title AS course_title,
COUNT(e.student_id) AS total_students
FROM courses c
LEFT JOIN enrollments e ON c.id = e.course_id
GROUP BY c.title
ORDER BY total_students DESC;

```

```sql
-- Find all courses for a specific student (e.g., “Jeanne Karanu”)
SELECT s.name AS student_name, c.title AS course_title, e.enrolled_at
FROM enrollments e
JOIN students s ON e.student_id = s.id
JOIN courses c ON e.course_id = c.id
WHERE s.name = 'Jeanne Karanu';

```

```sql
-- List students enrolled in a specific course
SELECT c.title AS course_title, s.name AS student_name, e.enrolled_at
FROM enrollments e
JOIN students s ON e.student_id = s.id
JOIN courses c ON e.course_id = c.id
WHERE c.title = 'Web Development 101';

</details>

<p align="right"><a href="#about-project">back to top</a></p>

---

# 📊 R Data Analysis <a name="r-data-analysis"></a>

<details>
<summary>Click to expand full R analysis code</summary>

```r
source("connect_db.R")
library(DBI)
library(dplyr)
library(ggplot2)

# count students per course(most popular course)
query <- "
SELECT c.title AS course_title,
COUNT(e.student_id) AS total_students
FROM courses c
LEFT JOIN enrollments e ON c.id = e.course_id
GROUP BY c.title
ORDER BY total_students DESC;
"
course_data <- dbGetQuery(con, query)

# View data
print(course_data)

# Create a bar chart using ggplot2
ggplot(course_data, aes(x = reorder(course_title, -total_students), y = total_students, fill = course_title)) + geom_bar(stat = "identity") + theme_minimal() +
  labs(title = "Number of Students Enrolled per Course",  x = "Course Title", y = "Total Enrolled Students") +
  theme(axis.text.x = element_text(angle = 30, hjust = 1, size = 10), legend.position = "none")
```
```r
# Load required libraries
library(DBI)
library(RPostgres)
library(ggplot2)
library(dplyr)

# Count enrollments by date
query_trend <- "
SELECT 
  DATE(enrolled_at) AS enrollment_date,
  COUNT(id) AS total_enrollments
FROM enrollments
GROUP BY enrollment_date
ORDER BY enrollment_date;
"

# Fetch the results
trend_data <- dbGetQuery(con, query_trend)

# View results in console
print(trend_data)

# Enrollment Trend Over Time
ggplot(trend_data, aes(x = enrollment_date, y = total_enrollments)) +
  geom_line(color = "#0077B6", size = 1.2) +
  geom_point(color = "#00B4D8", size = 3) +
  geom_text(aes(label = total_enrollments), vjust = -0.5, color = "#333333", size = 4, 
            fontface = "bold") + labs( title = "📈 Student Enrollment Trend Over Time",
    subtitle = "Tracking total course enrollments per day (Supabase Data)", x = "Enrollment Date",
    y = "Number of Enrollments",
    caption = "Data Source: Supabase • Visualization: ggplot2 (RStudio Cloud)") +
  theme_minimal(base_size = 12) + theme(
    plot.title = element_text(face = "bold", size = 15, color = "#1A1A1A"),
    plot.subtitle = element_text(size = 11, color = "#555555"),
    axis.title = element_text(face = "bold"),
    panel.grid.minor = element_blank(),
    panel.grid.major.x = element_blank(),
    plot.caption = element_text(size = 9, color = "#888888", face = "italic"))
```

</details>

### Number of students per course(most popular course)

<img width="1356" height="677" alt="no  of students per course" src="https://github.com/user-attachments/assets/60f7a808-85ee-42a8-9da9-0db77f4e6fc4" />

### Enrollment Trend Over Time
<img width="1366" height="681" alt="Students enrollment trend over time" src="https://github.com/user-attachments/assets/8deb58e9-87d0-4fd1-bc6e-16835de70a4f" />

<p align="right"><a href="#about-project">back to top</a></p>

---

# 📖 Data Dictionary <a name="data-dictionary"></a>

**📖 Full Data Dictionary:** [Check it here](https://github.com/ea007-ke7/E-LEARNING-PTATFORM/blob/Test_tool/Data%20Analysis%20Data%20Dictionary.md)

<p align="right"><a href="#about-project">back to top</a></p>

---

# 👥 Authors <a name="authors"></a>

👤 **Edith Adikinyi**

* GitHub: [@ea007-ke7](https://github.com/ea007-ke7/E-LEARNING-PTATFORM/blob/Test_tool/Data%20Analysis%20R%20Project%20readme.md)
* LinkedIn: [LinkedIn](https:www.linkedin.com/in/edith-othieno7)

<p align="right"><a href="#about-project">back to top</a></p>

---

# 🔭 Future Features <a name="future-features"></a>

* Add courses grades and attendance tracking
* Add instructors table
* Include admin dashboard analytics in R or React
* Allow file uploads and progress visualization
* Add email notifications via Supabase Edge Functions

<p align="right"><a href="#about-project">back to top</a></p>

---

# 🤝 Contributing <a name="contributing"></a>

Contributions, issues, and feature requests are welcome!
Open an issue or submit a pull request.

<p align="right"><a href="#about-project">back to top</a></p>

---

# ⭐️ Show your support <a name="support"></a>

If you like this project, give it a ⭐️ on GitHub!

<p align="right"><a href="#about-project">back to top</a></p>

---

# 🙏 Acknowledgements <a name="acknowledgements"></a>

* [Supabase](https://supabase.com/) for PostgreSQL hosting and testing  
* [Posit](https://docs.posit.co/connect/) for interactive RStudio workspace & Connect Documentation   

<p align="right"><a href="#about-project">back to top</a></p>

---

# ❓ FAQ <a name="faq"></a>

**1. How do I run this project in Posit?**  
Open the repository in **Posit (RStudio)**, install dependencies, and run the R scripts step by step.  
Make sure your Supabase credentials are correct(host, password) in `connect_db.R` for your connection to be successful.

**2. What packages are needed?**  
Install the following R packages:  
```r
install.packages(c("DBI", "RPostgres", "dplyr", "ggplot2"))
```

### 3. Why am I getting an error upon running my Rscript for connect_db.R  
Ensure your Supabase connection credentials (host, password) are correct.

### 4. How do I connect Posit to Supabase?  
Use the `DBI` and `RPostgres` packages along with your Supabase credentials found in:  
**Supabase → Project Settings → Database → Connection Info**  

---

# 📝 License <a name="license"></a>

This project is licensed under MIT License - see [LICENSE](LICENSE) for details.

---
