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

## 🔒 Row Level Security (RLS)

RLS ensures **least privilege access**. It restricts what each role
(User/Admin) can read, insert, update, or delete.
It also Prevent unauthorized data exposure between us

RLS is enabled on all key tables:

``` sql
ALTER TABLE students ENABLE ROW LEVEL SECURITY;
ALTER TABLE courses ENABLE ROW LEVEL SECURITY;
ALTER TABLE enrollments ENABLE ROW LEVEL SECURITY;
```

------------------------------------------------------------------------

## 👤 User Policies

Normal users have restricted access.

``` sql
-- View only their own favorites
CREATE POLICY "Users can view their own favorites"
ON user_favorites
FOR SELECT
USING (auth.uid() = user_uuid);

-- Insert only their own favorites
CREATE POLICY "Users can insert their own favorites"
ON user_favorites
FOR INSERT
WITH CHECK (auth.uid() = user_uuid);

-- Read all songs & artists
CREATE POLICY "Users can read all songs"
ON songs FOR SELECT USING (true);

CREATE POLICY "Users can read all artists"
ON artists FOR SELECT USING (true);
```

------------------------------------------------------------------------

## 👨‍💼 Admin Policies

Admins have **full access**.

``` sql
CREATE POLICY "Admins can manage all favorites"
ON user_favorites
FOR ALL
USING (EXISTS (SELECT 1 FROM users WHERE user_uuid = auth.uid() AND role = 'admin'));

CREATE POLICY "Admins can manage all songs"
ON songs
FOR ALL
USING (EXISTS (SELECT 1 FROM users WHERE user_uuid = auth.uid() AND role = 'admin'));

CREATE POLICY "Admins can manage all artists"
ON artists
FOR ALL
USING (EXISTS (SELECT 1 FROM users WHERE user_uuid = auth.uid() AND role = 'admin'));
```

------------------------------------------------------------------------

## ⚡️ Testing Roles & Policies

### ✅ User Tests

1.  **SELECT own favorites** (works)\
2.  **INSERT a favorite** (works)\
3.  **UPDATE a favorite** (blocked)\
4.  **DELETE a favorite** (blocked)

### ✅ Admin Tests

1.  **SELECT all favorites** (works)\
2.  **UPDATE songs** (works)\
3.  **DELETE artists** (works)

------------------------------------------------------------------------

<img width="1355" height="682" alt="image" src="https://github.com/user-attachments/assets/da1574ed-80b1-4900-98c2-e803ab3d1d35"/>


## 🛠 Admin-only Function

Example: Admin deletes a project or song safely.

``` sql
CREATE OR REPLACE FUNCTION delete_song_safe(song_id INT)
RETURNS VOID
LANGUAGE SQL
SECURITY DEFINER
AS $$
  DELETE FROM songs WHERE song_id = $1;
$$;
```

------------------------------------------------------------------------

<img width="1331" height="326" alt="image" src="https://github.com/user-attachments/assets/ca9f8afe-9e25-4293-95fd-0d5a34941ad2"/>


## 📎 Reference

-   Linked to [README.md](README.md)
-   [Supabase Policies:](https://supabase.com/docs/guides/database/postgres/row-level-security)
  
