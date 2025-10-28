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
git clone https://github.com/edithothieno/e-learning-platform.git
cd e-learning-platform

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

# Run your query
query <- "
  SELECT u.username, s.title, a.name AS artist_name
  FROM user_favorites uf
  JOIN users u ON uf.user_id = u.user_id
  JOIN songs s ON uf.song_id = s.song_id
  JOIN artists a ON s.artist_id = a.artist_id
  WHERE u.username = 'alice';
"

# Execute query and store results
alice_favorites <- dbGetQuery(con, query)

# View results
print(alice_favorites)
#outome : 
# source("/cloud/project/alice_favorite.R")
#username              title artist_name
#1    alice Programmers choice   Sauti Sol

```
# Outcome upon running the code

<img width="1366" height="634" alt="image" src="https://github.com/user-attachments/assets/586fef26-f522-47cc-9531-11af15845984" />
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
<img width="1362" height="679" alt="Connect R" src="https://github.com/user-attachments/assets/1e385de7-d09c-4bb5-8d32-f4cdcb8c0d99" />



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
SELECT * courses;
```

```sql
-- List all songs liked by Alice
SELECT u.username, s.title, a.name AS artist_name
FROM user_favorites uf
JOIN users u ON uf.user_id = u.user_id
JOIN songs s ON uf.song_id = s.song_id
JOIN artists a ON s.artist_id = a.artist_id
WHERE u.username = 'alice';
```

```sql
-- Find all songs by 'Sauti Sol'
SELECT s.title, s.release_year
FROM songs s
JOIN artists a ON s.artist_id = a.artist_id
WHERE a.name = 'Sauti Sol';
```

```sql
-- Most popular artist (by total favorites across their songs)
-- Aggregates favorites to rank artists
-- Example: Who is the most liked artist overall?
SELECT a.name, COUNT(uf.user_id) AS total_favorites
FROM artists a
JOIN songs s ON a.artist_id = s.artist_id
LEFT JOIN user_favorites uf ON s.song_id = uf.song_id
GROUP BY a.artist_id
ORDER BY total_favorites DESC;
```

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

con <- connect_db()

# count students per course
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

# Active users
active_users <- dbGetQuery(con, "
  SELECT u.username, COUNT(uf.user_id) AS favorites_count
  FROM user_favorites uf
  JOIN users u ON uf.user_id = u.user_id
  GROUP BY u.username
  ORDER BY favorites_count DESC;
")
ggplot(active_users, aes(x = reorder(username, favorites_count), y = favorites_count, fill = username)) +
  geom_col(show.legend=FALSE) + coord_flip() + theme_minimal() +
  labs(title='Most Active Users', x='Username', y='Number of Favorites')

# Artist performance
agg_artist <- dbGetQuery(con, "
  SELECT a.name AS artist,
         COUNT(DISTINCT s.song_id) AS n_songs,
         COUNT(uf.song_id) AS total_favorites
  FROM songs s
  JOIN artists a ON s.artist_id = a.artist_id
  LEFT JOIN user_favorites uf ON uf.song_id = s.song_id
  GROUP BY a.name
")
agg_artist <- agg_artist %>% mutate(avg_fav_per_song = total_favorites / pmax(n_songs,1))
ggplot(agg_artist, aes(x=n_songs, y=avg_fav_per_song, size=total_favorites, label=artist)) +
  geom_point(alpha=0.7, color='steelblue') + geom_text(vjust=-1, size=3) + theme_minimal() +
  labs(title='Artists: Breadth vs. Popularity', subtitle='Comparing number of songs to avg favorites per song',
       x='Number of Songs', y='Average Favorites per Song', size='Total Favorites')
```

</details>

### Number of students per course
<img width="1356" height="677" alt="no  of students per course" src="https://github.com/user-attachments/assets/60f7a808-85ee-42a8-9da9-0db77f4e6fc4" />



### Most Active Users

<img width="1363" height="628" alt="most active user5" src="https://github.com/user-attachments/assets/12d08288-53ce-4880-8c05-ff0382909a74" />


### Artist Performance Bubble Chart
<img width="1366" height="686" alt="image" src="https://github.com/user-attachments/assets/e004292a-1f80-4ad9-97cf-b56b57af8339" />


<p align="right"><a href="#about-project">back to top</a></p>

---

# 📖 Data Dictionary <a name="data-dictionary"></a>

**📖 Full Data Dictionary:** [Check it here](https://github.com/DENNIS-MURITHI/Data-Tools/blob/test_branch/data_dictionary.md)

<p align="right"><a href="#about-project">back to top</a></p>

---

# 👥 Authors <a name="authors"></a>

👤 **Dennis Murithi**

* GitHub: [@dennismurithi](https://github.com/DENNIS-MURITHI)
* LinkedIn: [LinkedIn](https://www.linkedin.com/in/dennis-muthuri/)

<p align="right"><a href="#about-project">back to top</a></p>

---

# 🔭 Future Features <a name="future-features"></a>

* Front-end integration with music streaming app  
* Advanced analytics (top songs, popular artists, trends)  
* Playlists, ratings, and user-generated content
* 
<p align="right"><a href="#about-project">back to top</a></p>

---

# 🤝 Contributing <a name="contributing"></a>

Contributions, issues, and feature requests are welcome. Open an issue or submit a pull request.

<p align="right"><a href="#about-project">back to top</a></p>

---

# ⭐️ Show your support <a name="support"></a>

If you like this project, give it a ⭐️ on GitHub!

<p align="right"><a href="#about-project">back to top</a></p>

---

# 🙏 Acknowledgements <a name="acknowledgements"></a>

* [Supabase](https://supabase.com/) for PostgreSQL hosting and testing  
* [Posit](https://docs.posit.co/connect/) Connect Documentation   

<p align="right"><a href="#about-project">back to top</a></p>

---

# ❓ FAQ <a name="faq"></a>

**1. How do I run this project in Posit?**  
Open the repository in **Posit (RStudio)**, install dependencies, and run the R scripts step by step.  
Make sure your Supabase credentials are set correctly in `connect_db.R`.

**2. What dependencies are needed?**  
Install the following R packages:  
```r
install.packages(c("DBI", "RPostgres", "dplyr", "ggplot2"))
```
### 3. Can I use MySQL or other databases?  
❌ **No.** This project connects only to **Supabase (PostgreSQL)** for consistency and compatibility with R and Posit.

---

### 4. How do I connect Posit to Supabase?  
Use the `DBI` and `RPostgres` packages along with your Supabase credentials found in:  
**Supabase → Project Settings → Database → Connection Info**  

# 📝 License <a name="license"></a>

This project is licensed under MIT License - see [LICENSE](LICENSE) for details.

### Usage

Open your Supabase project, navigate to SQL Editor, and run:

\i week1_schema.sql


Open Posit (RStudio Cloud), create a new R Script (analysis.R), and run the connection setup code.

Connecting from Posit to Supabase <a name="posit-supabase-connection"></a>

Install required R packages:

install.packages(c("DBI", "RPostgres", "dplyr", "ggplot2"))


Create a file connect_db.R:

library(DBI)
library(RPostgres)

analysis <- function() {
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


Test connection:

source("connect_db.R")
con <- connect_db()
dbListTables(con)


# Outcome after establishing connection

<img width="1362" height="679" alt="Connect R" src="https://github.com/user-attachments/assets/aefb746d-764c-4c6d-b1a2-8359a21508b9" />


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
