# Security Notes -- Data Fundamentals Project

This document summarizes the security and access control configuration for the E-Learning Platform built using Supabase (PostgreSQL).
It enforces Role-Based Access Control (RBAC), Row Level Security (RLS), and function-level privileges to ensure data protection and least privilege access.

------------------------------------------------------------------------

## 🔑 Authentication Setup

Feature	Description
Auth Method	Supabase Auth (Email/Password)
Users Table	Stores id, name, email, role (admin / student)
Session Mapping	Each record in users corresponds to auth.uid() from Supabase Auth
Access Enforcement	All database queries use auth.uid() for identity verification

Example
```sql
SELECT id, name, role FROM users WHERE id = auth.uid();
```

➡ Ensures that every action is tied to a verified Supabase Auth session.



------------------------------------------------------------------------

👥 3. **Roles & Permissions**

Admin	have **Full access (SELECT, INSERT, UPDATE, DELETE)**	Can manage all students, courses, and enrollments
Student/User have	Limited access	Can only view courses and manage their own enrollment and profile


------------------------------------------------------------------------

## 👤 User Policies

Normal users have restricted access.

``` sql
-- View own student record
CREATE POLICY "Users can view own student record"
ON students
FOR SELECT
USING (auth.uid() = auth_id);

-- Update own student record
CREATE POLICY "Users can update own student record"
ON students
FOR UPDATE
USING (auth.uid() = auth_id)
WITH CHECK (auth.uid() = auth_id);

-- All users can view available courses
CREATE POLICY "All users can view courses"
ON courses
FOR SELECT
USING (true);

```

------------------------------------------------------------------------

## 👨‍💼 Admin Policies

Admins have **full access**.

``` sql
-- Full access to students
CREATE POLICY "Admins full access to students"
ON students
FOR ALL
USING (EXISTS (SELECT 1 FROM users WHERE id = auth.uid() AND role = 'admin'));

-- Manage all courses
CREATE POLICY "Admins manage all courses"
ON courses
FOR ALL
USING (EXISTS (SELECT 1 FROM users WHERE id = auth.uid() AND role = 'admin'));

-- Manage all enrollments
CREATE POLICY "Admins full access to enrollments"
ON enrollments
FOR ALL
USING (EXISTS (SELECT 1 FROM users WHERE id = auth.uid() AND role = 'admin'));

```

------------------------------------------------------------------------

## ⚡️ Testing Roles & Policies

### ✅ User Tests

1.  **SELECT all courses** (works)\
2.  **INSERT own enrollment** (works)\
3.  **UPDATE another student record** (denied)\
4.  **DELETE a course** (denied)

### ✅ Admin Tests

1.  **SELECT all students** (works)\
2.  **INSERT courses** (works)\
3.  **DELETE any course** (works)

------------------------------------------------------------------------

<img width="1363" height="654" alt="Capture iii" src="https://github.com/user-attachments/assets/5d1c4c68-5dad-44bd-9c4d-c41b172d9e03" />


## 🛠 Admin-only Function

Example:  Admin-only course deletion

``` sql
CREATE OR REPLACE FUNCTION delete_course(course_id uuid)
RETURNS void
LANGUAGE sql
SECURITY DEFINER
AS $$
  DELETE FROM courses WHERE id = course_id;
$$;

```
[] SECURITY DEFINER allows the function to execute with the privileges of its creator (admin).
[] Access is restricted via a policy to users with role = 'admin'.

------------------------------------------------------------------------

<img width="1343" height="581" alt="Capture vi" src="https://github.com/user-attachments/assets/4a9a6fed-ff85-4498-a0ad-ce1f15b55d8f" />

## 📎 Reference

-   Linked to [README.md](README.md)
-   [Supabase Policies:](https://supabase.com/docs/guides/database/postgres/row-level-security)
  https://supabase.com/dashboard/project/mhsnciubwnzincjbnpxj/sql
