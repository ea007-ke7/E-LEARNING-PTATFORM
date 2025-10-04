# E-LEARNING-PTATFORM
A simple database schema for an E-learning platform using Supabase (students, courses, enrollments).
# 📗 Table of Contents

- [📖 About the Project](#about-project)
  - [🛠 Built With](#built-with)
    - [Tech Stack](#tech-stack)
    - [Key Features](#key-features)
  - [🚀 Live Demo](#live-demo)
- [💻 Getting Started](#getting-started)
  - [Setup](#setup)
  - [Prerequisites](#prerequisites)
  - [Install](#install)
  - [Usage](#usage)
  - [Run tests](#run-tests)
  - [Deployment](#triangular_flag_on_post-deployment)
- [👥 Authors](#authors)
- [🔭 Future Features](#future-features)
- [🤝 Contributing](#contributing)
- [⭐️ Show your support](#support)
- [🙏 Acknowledgements](#acknowledgements)
- [❓ FAQ (OPTIONAL)](#faq)
- [📝 License](#license)

<!-- PROJECT DESCRIPTION -->

# 📖 E-LEARNING-PTATFORM

> This project is a simple relational database for an **E-learning Platform**.  It is designed to store information about:
- **Students** registered on the platform
- **Courses** offered
- **Enrollments** that connect students to courses  

This project was created as part of a database course assignment. It demonstrates schema design, foreign keys, sample data insertion, and documentation.


## 🛠 Built With 
**PostgreSQL** (via [Supabase](https://supabase.com)).


### Tech Stack 

- **Database**: PostgreSQL (hosted on Supabase)
- **Tools**: Supabase SQL Editor, Supabase CLI
- **Diagram**: Entity Relationship Diagram (ERD)

### Key Features
-  Three relational tables: `students`, `courses`, `enrollments`
-  At least **5 sample rows per table**
-  Foreign key relationships (`students ↔ enrollments`, `courses ↔ enrollments`)
-  Schema exported as `schema.sql
  

<p align="right">(<a href="#readme-top">back to top</a>)</p>


<!-- GETTING STARTED -->

## 💻 Getting Started <a name="getting-started"></a>

1. Clone this repo:
   ```bash
   git clone https://github.com/your-username/elearning-db.git
   cd elearning-db
   
### Prerequisites

In order to run this project you need:
- A Supabase account (free)
- (Optional) PostgreSQL installed locally
- (Optional) Supabase CLI installed

Install

No installation required. Just run the SQL script.

### Setup

Clone this repository to your desired folder:

<!--
Example commands:

```sh
  cd my-folder
  git clone git@github.com:myaccount/my-project.git
```
--->

### Install

No installation required. Just run the SQL script on supabase by
1.Logging in to Supabase.
2.Open the SQL Editor.
3.Copy-paste the contents of schema.sql.

Run the script → tables and sample data will be created.


### Usage

To run the project, execute the following command:


```sql
 -- Students Table
CREATE TABLE students (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT NOW()
);
```

```sql

-- Insert sample data
INSERT INTO students (name, email, created_at) VALUES
('Jeanne Karanu', 'jkaranu@gmail.com', '2025-09-01 10:00:00'),
('Joseline Othieno', 'jothieno@gmail.com', '2025-09-02 11:15:00'),
('Charles Weko', 'charlesweko@egmail.com', '2025-09-03 09:45:00'),
('Diana Juma', 'dianaj@gmail.com', '2025-09-04 14:20:00'),
('Ethan Liko', 'ethanliko@gmail.com', '2025-09-05 16:10:00');
```
```sql
-- Courses Table
CREATE TABLE courses (
    id SERIAL PRIMARY KEY,
    title VARCHAR(150) NOT NULL,
    description TEXT,
    created_at TIMESTAMP DEFAULT NOW()
);
```
```sql
INSERT INTO courses (title, description, created_at) VALUES
('SQL Basics', 'Intro to SQL and Databases', '2025-09-01 08:00:00'),
('Python for Beginners', 'Learn Python fundamentals', '2025-09-01 09:30:00'),
('Web Development 101', 'HTML, CSS, and JavaScript basics', '2025-09-02 10:00:00'),
('Data Analysis with SQL', 'Analyze data using SQL queries', '2025-09-03 11:00:00'),
('Intro to Machine Learning', 'Basics of ML algorithms', '2025-09-04 12:00:00');
```
```sql
-- Enrollments Table
CREATE TABLE enrollments (
    id SERIAL PRIMARY KEY,
    student_id INT REFERENCES students(id),
    course_id INT REFERENCES courses(id),
    enrolled_at TIMESTAMP DEFAULT NOW()
);
```
```sql
INSERT INTO enrollments (student_id, course_id, enrolled_at) VALUES
(1, 1, '2025-09-06 09:00:00'),
(1, 2, '2025-09-06 09:15:00'),
(2, 3, '2025-09-07 10:30:00'),
(3, 1, '2025-09-08 11:00:00'),
(4, 5, '2025-09-09 13:45:00');

```
--->

### Run tests

To run tests, run the following command:

```sql
  SELECT * FROM students;
```

**output for all students**
<img width="1905" height="772" alt="image" src="https://github.com/user-attachments/assets/73973fa8-95b8-4e16-8254-c93eea4f3745" />


```sql
SELECT * FROM courses;
```
**output for all courses**
```sql
SELECT * FROM Courses;

```
<img width="1913" height="600" alt="image" src="https://github.com/user-attachments/assets/9ce261c5-1642-481d-9974-33456b62b8f6" />

**output for all courses**
```sql
SELECT * FROM Enrollments;

```
<img width="1913" height="600" alt="image" src="https://github.com/user-attachments/assets/559cfec8-fd26-4f37-af49-bab85d444dc3" />


---
### ERD DIAGRAM

### DATA DICTIONARY
---
[Data Dictionary](https://github.com/ea007-ke7/E-LEARNING-PTATFORM/blob/test_tool/data_dictionary.md)


### Deployment

This is a schema-only project.

Deployment = loading schema.sql into Supabase or any PostgreSQL instance.


## 👥 Authors 

> Mention all of the collaborators of this project.

👤 **Author1**

- GitHub: [@githubhandle](https://github.com/ea007-ke7)
- LinkedIn: [LinkedIn]()


## 🔭 Future Features

[] **Add instructors table**
[] **Add assignments and grades tables**
[] **Role-based access control (students vs instructors)]**


<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 🤝 Contributing 

Contributions, issues, and feature requests are welcome!

Feel free to open a PR in this repo.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- SUPPORT -->

## ⭐️ Show your support 

If you like this project, please ⭐️ the repo!

🙏 Acknowledgements

Thanks to [Supabase](https://www.google.com/search?q=supabase&oq=supa&gs_lcrp=EgZjaHJvbWUqBwgDEAAYjwIyBggAEEUYOTIHCAEQABiPAjIHCAIQABiPAjIHCAMQABiPAtIBCDMxODhqMGoxqAIAsAIA&sourceid=chrome&ie=UTF-8) for free PostgreSQL hosting.

Inspired by real-world E-learning platforms.

# ❓ FAQ (OPTIONAL)

Q: Can I run this without Supabase?
A: Yes, just use PostgreSQL locally and run schema.sql.



## 🙏 Acknowledgments <a name="acknowledgements"></a>

Thanks to Supabase for free PostgreSQL hosting.

Inspired by real-world E-learning platforms.

### 📝 **License**

This project is licensed under the MIT License

NOTE: we recommend using the MIT license - you can set it up quickly by using templates available on GitHub. You can also use any other license if you wish.

