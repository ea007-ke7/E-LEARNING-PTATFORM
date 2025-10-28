# 📖 E-LEARNING-PTATFORM Dataset Documentation (Posit + Supabase)

This data dictionary provides a structured description of all database tables, columns, and relationships used in the **E-Learning Management Platform**.  
It complements the `schema.sql` file and serves as a guide for developers, analysts, and contributors integrating with Supabase or R/Posit.

---

## students Table

| **Column Name** | **Data Type** | **Constraints** | **Description** |
|------------------|----------------|------------------|------------------|
| `id` | `SERIAL` | `PRIMARY KEY` | Unique identifier for each student. |
| `name` | `VARCHAR(100)` | `NOT NULL` | Full name of the student. |
| `email` | `VARCHAR(100)` | `UNIQUE, NOT NULL` | Student's unique email address. |
| `created_at` | `TIMESTAMP` | `DEFAULT NOW()` | Date and time when the student record was created. |

**Usage in R:**  
* Retrieving students data
* Joining with enrollments via s.id = e.student_id 
* Analytic purpose by Identifying which students enrolled in which courses
* Combine with enrollments for time trends or course popularity

---

## **courses Table**

 **Column Name** | **Data Type** | **Constraints** | **Description** |
|------------------|----------------|------------------|------------------|
| `id` | `SERIAL` | `PRIMARY KEY` | Unique identifier for each course. |
| `title` | `VARCHAR(150)` | `NOT NULL` | Name or title of the course. |
| `description` | `TEXT` | – | Detailed information about the course. |
| `created_at` | `TIMESTAMP` | `DEFAULT NOW()` | Date and time when the course was created. |

**Usage in R:**  
* Retrieving data
* Determine most popular courses (based on student count)
* Identify courses with low engagement
* Provide labels and axes for ggplot visualizations  

---

## **enrollments Table**

| **Column Name** | **Data Type** | **Constraints** | **Description** |
|------------------|----------------|------------------|------------------|
| `id` | `SERIAL` | `PRIMARY KEY` | Unique identifier for each enrollment record. |
| `student_id` | `INT` | `REFERENCES students(id)` | References the student enrolled in the course. |
| `course_id` | `INT` | `REFERENCES courses(id)` | References the course the student is enrolled in. |
| `enrolled_at` | `TIMESTAMP` | `DEFAULT NOW()` | Date and time when the student enrolled in the course. |

**Usage in R:**  
* Counting students per course
* Show enrollment trend over time
* visualize course popularity by showing enrollment per course
* Joining Queries

---

## **Relationships**

| **Entity** | **Relationship** | **Details** |
|-------------|------------------|--------------|
| `students` ↔ `enrollments` | One-to-Many | A student can enroll in many courses. |
| `courses` ↔ `enrollments` | One-to-Many | A course can have many enrolled students. |
| `students` ↔ `courses` | Many-to-Many | Relationship managed through `enrollments` table. |

---

## **Notes for Posit Analysis**

* Use `DBI` to connect to Supabase and retrieve tables.  
* Use `dplyr` for aggregation (`count`, `group_by`, `mutate`) and filtering.  
* Use `ggplot2` for visualization:
  - Popular course (based on student count) Bar Chart  
  - Enrollment trend over time Trend Line 
  
---

## **💡 Tip: Why Posit is Great**

Posit (RStudio) makes this workflow smooth because:

* **Seamless DB integration:** Connect directly to Supabase/PostgreSQL using `DBI`.  
* **Powerful data wrangling:** `dplyr` allows quick aggregations and transformations.  
* **Visualization-ready:** `ggplot2` enables clean, publication-quality charts with minimal code.  
* **Reproducible workflows:** R scripts can be run repeatedly with updated data, ideal for analytics projects.  

---

# 🔗 Connecting Posit (RStudio) to Supabase

This guide explains how to connect Posit (RStudio) to your Supabase PostgreSQL database for analysis.

---

## **1. Install Required R Packages**

```r
install.packages("DBI")
install.packages("RPostgres")
install.packages("dplyr")
install.packages("ggplot2")
```

---

## **2. Obtain Supabase Database Credentials**

From Supabase → **Settings → Database → Connection info**:

- Host URL
- Port (default: 5432)
- Database name
- Username
- Password
- SSL mode (`require`)

---
## 🔗 Here is a quick visual to learn how to connect supabase database to R Posit
[Supabase Connection Documentation](https://supabase.com/docs/guides/database/connecting-to-postgres) **Learn more from here..**

<img width="1366" height="670" alt="image" src="https://github.com/user-attachments/assets/88c68653-aae2-4e18-8c6c-4006c2ba4da3" />


## **3. Connect from Posit**

```r
library(DBI)
library(RPostgres)

connect_db <- function() {
  con <- dbConnect(
    RPostgres::Postgres(),
    dbname = "your_database_name",
    host = "your_host_url",
    port = 5432,
    user = "your_username",
    password = "your_password",
    sslmode = "require"
  )
  return(con)
}
```

---

## **4. Test the Connection**

```r
source("connect_db.R")
con <- connect_db()
dbListTables(con)
users <- dbGetQuery(con, "SELECT * FROM users LIMIT 5;")
print(users)
```

---

## **5. Use in Analysis**

* Aggregate with `dplyr` (`count`, `group_by`, `summarize`)  
* Visualize with `ggplot2` (popular songs, active users, artist metrics)  
* Query directly via `dbGetQuery()`

Example:

# Total students per course
query <- "
SELECT c.title AS course_title,
COUNT(e.student_id) AS total_students
FROM courses c
LEFT JOIN enrollments e ON c.id = e.course_id
GROUP BY c.title
ORDER BY total_students DESC;
"
course_data <- dbGetQuery(con, query)
```

```r
# Count enrollments by date
query_trend <- "
SELECT  DATE(enrolled_at) AS enrollment_date,
 COUNT(id) AS total_enrollments
FROM enrollments
GROUP BY enrollment_date
ORDER BY enrollment_date;
"
# Fetch the results
trend_data <- dbGetQuery(con, query_trend)

# View results in console
print(trend_data)

library(ggplot2)

ggplot(course_data, aes(x = reorder(course_title, -total_students), y = total_students, fill = course_title)) +
  geom_bar(stat = "identity") + theme_minimal() + labs(title = "Number of Students Enrolled per Course",
       x = "Course Title", y = "Total Students") + theme(axis.text.x = element_text(angle = 30, hjust = 1),
        legend.position = "none")""")
```

---

## Maintainer

**Author**: **Edith Adikinyi**
**Repository**: E-Learning Platform on GitHub
**Database**: [Supabase (PostgreSQL)](https://supabase.com/dashboard/project/mhsnciubwnzincjbnpxj/sql/84e4c0de-e88e-4856-b5f2-437275cec772)
**Visualization**: [ R / Posit Cloud (ggplot2, dplyr, DBI, RPostgres)](https://posit.cloud/content/11265505)
---
## **💡 Tip**

Connecting Posit to Supabase allows real-time queries, reproducible analysis, and clean integration with `ggplot2` for professional charts.
---
