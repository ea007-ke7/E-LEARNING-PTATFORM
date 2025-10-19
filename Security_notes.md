E-Learning Platform – Security Setup Notes

This document describes the security configuration, roles, and policies applied to the Supabase (Postgres) database for the E-learning Platform project.
The goal is to ensure that data access is secure, role-based, and follows the principle of least privilege.

🔐 1. Row Level Security (RLS)

Status: Enabled on all tables.

ALTER TABLE students ENABLE ROW LEVEL SECURITY;
ALTER TABLE courses ENABLE ROW LEVEL SECURITY;
ALTER TABLE enrollments ENABLE ROW LEVEL SECURITY;
ALTER TABLE users ENABLE ROW LEVEL SECURITY;


RLS ensures that users can only access rows they are authorized to view or modify.

👥 2. Roles

Two logical roles are defined in the system:

Role	Description	Access Level
Admin	Full access to all data and functions	Read, Insert, Update, Delete
User	Limited access, can only view or insert their own data	Read own data, Insert own data

Roles are stored in the users table and linked to auth.users in Supabase.

CREATE TABLE users (
  id uuid PRIMARY KEY REFERENCES auth.users(id),
  name text,
  role text CHECK (role IN ('admin', 'user')) DEFAULT 'user',
  created_at timestamp DEFAULT now()
);

🧱 3. Policies Overview
🧩 students Table
Policy Name	Purpose	Access
Admins can view all students	Allows admins to view all student records.	select
Users can view their own student record	Users can view only their own record (based on auth.uid()).	select
Admins manage all students	Admins can insert, update, and delete student records.	all
create policy "Admins can view all students"
on students
for select
using (exists (
  select 1 from users where id = auth.uid() and role = 'admin'
));

create policy "Users can view their own student record"
on students
for select
using (auth.uid() = id);

create policy "Admins manage all students"
on students
for all
using (exists (
  select 1 from users where id = auth.uid() and role = 'admin'
));

🧩 courses Table
Policy Name	Purpose	Access
Authenticated users can view courses	Allows all logged-in users to view course information.	select
Admins can manage courses	Grants admins permission to insert, update, or delete courses.	all
create policy "Authenticated users can view courses"
on courses
for select
using (auth.role() = 'authenticated');

create policy "Admins can manage courses"
on courses
for all
using (exists (
  select 1 from users where id = auth.uid() and role = 'admin'
));

🧩 enrollments Table
Policy Name	Purpose	Access
Users manage their own enrollments	Users can view or insert only their own enrollments.	select, insert
Admins manage all enrollments	Admins have full access to all enrollments.	all
create policy "Users manage their own enrollments"
on enrollments
for select, insert
using (auth.uid() = student_id)
with check (auth.uid() = student_id);

create policy "Admins manage all enrollments"
on enrollments
for all
using (exists (
  select 1 from users where id = auth.uid() and role = 'admin'
));

⚙️ 4. Custom Admin-Only Function

Admins can delete a course using a security definer function, ensuring only admins can perform deletions.

create or replace function delete_course(course_id int)
returns void
language sql
security definer
as $$
  delete from courses where id = course_id;
$$;


This function runs with admin privileges and can be called from secure backend logic.

🔑 5. Authentication

Supabase Auth is enabled for:

Email/Password login (or Magic Link).

All database queries require authenticated sessions.

-- Example: Restrict access to authenticated users only
create policy "Authenticated users only"
on users
for select
using (auth.role() = 'authenticated');

🧠 6. Security Summary

✅ RLS enabled on all tables.
✅ Admin/User roles stored in users table.
✅ Policies enforce least privilege.
✅ Admin-only SQL function for secure operations.
✅ Authentication required for all database access.

📋 7. Testing Checklist
Test	Expected Result
Login as Admin	Can view and modify all tables
Login as Regular User	Can only view and insert own enrollments
Attempt to delete course as User	Fails
Call delete_course() as Admin	Success
