# 🔐 UserVault — User Management System
### ApexPlanet Internship | Task 3 — Backend Development & Database Integration

---

## 📌 Project Overview
UserVault is a full-stack **PHP & MySQL** User Management System built as part of the **ApexPlanet 60-Day Full Stack Web Development Internship (Task 3)**. It demonstrates CRUD operations, user authentication, role-based access, security best practices, and profile picture upload.

---

## ✅ Features Implemented

### 1. 🗄️ Database Design
- ER Diagram with `users` table and `roles` field
- Normalized schema (1NF, 2NF, 3NF)
- Primary keys, indexed email field for performance

### 2. 📋 CRUD Operations
- **Create** — Add new users via modal form with validation
- **Read** — Fetch all users in searchable, filterable HTML table
- **Update** — Edit user details inline with confirmation
- **Delete** — Delete users with confirmation popup (cannot self-delete)

### 3. 🔐 Authentication System
- User Registration with **hashed passwords** (`password_hash()` / `PASSWORD_BCRYPT`)
- Login & Logout using **PHP Sessions** (`$_SESSION`)
- **Role-Based Login**: `admin` gets Dashboard access, `user` gets Profile only
- Password match validation on registration

### 4. 🛡️ Security
- **Prepared Statements** (`mysqli_prepare`) to prevent SQL Injection
- Server-side input validation (email format, required fields, password length)
- Passwords stored in **encrypted/hashed format** — never plain text
- Session-based auth guard on all protected pages

### 5. 👤 Profile Management
- Edit Profile: update name and email with real-time validation
- **Profile Picture Upload** with:
  - File type validation (`image/jpeg`, `image/png`, `image/gif`, `image/webp`)
  - File size validation (max **2MB**)
  - Stored in `/uploads/` folder
- Change Password with current-password verification and strength meter
- Dynamic profile info display

---

## 🛠️ Tech Stack

| Layer      | Technology                        |
|------------|-----------------------------------|
| Frontend   | HTML5, CSS3, JavaScript (ES6+)    |
| Backend    | PHP 8.x                           |
| Database   | MySQL 8.x via phpMyAdmin          |
| Server     | Apache (XAMPP / WAMP / LAMP)      |
| Version Control | Git & GitHub                 |

---

## 🗃️ Database Schema

```sql
CREATE DATABASE uservault;
USE uservault;

CREATE TABLE users (
  id         INT AUTO_INCREMENT PRIMARY KEY,
  fname      VARCHAR(50)  NOT NULL,
  lname      VARCHAR(50)  NOT NULL,
  email      VARCHAR(100) NOT NULL UNIQUE,
  password   VARCHAR(255) NOT NULL,       -- bcrypt hash
  role       ENUM('admin','user') DEFAULT 'user',
  status     ENUM('active','inactive') DEFAULT 'active',
  avatar     VARCHAR(255) DEFAULT NULL,   -- file path
  joined_at  DATETIME DEFAULT CURRENT_TIMESTAMP,
  INDEX idx_email (email)
);

-- Seed admin user
INSERT INTO users (fname, lname, email, password, role)
VALUES ('Admin', 'User', 'admin@test.com',
        '$2y$10$...bcrypt_hash_here...', 'admin');
```

---

## 📁 Project Structure

```
task3/
├── index.php           # Login & Registration page
├── dashboard.php       # Admin dashboard (CRUD)
├── profile.php         # User profile & edit
├── logout.php          # Session destroy
├── config/
│   └── db.php          # Database connection (mysqli)
├── includes/
│   ├── auth.php        # Session auth guard
│   ├── header.php      # Shared nav/sidebar
│   └── functions.php   # Helper functions
├── uploads/            # Profile pictures (gitignored)
│   └── .gitkeep
└── README.md
```

---

## 🚀 How to Run Locally

1. **Install XAMPP** from [apachefriends.org](https://www.apachefriends.org)
2. Clone this repo into `C:/xampp/htdocs/task3/`
3. Start **Apache** and **MySQL** from XAMPP Control Panel
4. Open `http://localhost/phpmyadmin` → create database `uservault`
5. Import `database/uservault.sql`
6. Open `http://localhost/task3/` in your browser

---

## 🔑 Demo Credentials

| Role  | Email            | Password  |
|-------|------------------|-----------|
| Admin | admin@test.com   | admin123  |
| User  | user@test.com    | user123   |

---

## 🖼️ Screenshots

| Page       | Description                        |
|------------|------------------------------------|
| Login      | Auth with role-based redirect      |
| Register   | Form with validation               |
| Dashboard  | Admin CRUD table + stats           |
| Profile    | Edit info + avatar upload          |

---

## 📚 Key PHP Code Snippets

### Secure DB Connection
```php
<?php
$conn = mysqli_connect('localhost', 'root', '', 'uservault');
if (!$conn) die('Connection failed: ' . mysqli_connect_error());
```

### Prepared Statement (CRUD Read)
```php
$stmt = $conn->prepare("SELECT * FROM users WHERE role = ?");
$stmt->bind_param('s', $role);
$stmt->execute();
$result = $stmt->get_result();
while ($row = $result->fetch_assoc()) { /* render */ }
```

### Password Hashing
```php
// Register
$hash = password_hash($_POST['password'], PASSWORD_BCRYPT);

// Login verify
if (password_verify($_POST['password'], $user['password'])) {
    $_SESSION['user_id'] = $user['id'];
    $_SESSION['role']    = $user['role'];
}
```

### Profile Picture Upload
```php
$allowed  = ['image/jpeg', 'image/png', 'image/gif', 'image/webp'];
$maxBytes = 2 * 1024 * 1024; // 2 MB

if (in_array($_FILES['avatar']['type'], $allowed)
    && $_FILES['avatar']['size'] <= $maxBytes) {
    $ext  = pathinfo($_FILES['avatar']['name'], PATHINFO_EXTENSION);
    $name = uniqid() . '.' . $ext;
    move_uploaded_file($_FILES['avatar']['tmp_name'], 'uploads/' . $name);
    $stmt = $conn->prepare("UPDATE users SET avatar=? WHERE id=?");
    $stmt->bind_param('si', $name, $_SESSION['user_id']);
    $stmt->execute();
}
```

---

## 👨‍💻 Author
**[Your Name]**  
ApexPlanet Internship — Full Stack Web Development (PHP & MySQL)  
Task 3: Backend Development & Database Integration  
Timeline: Days 25–36

---

## 📞 Contact — ApexPlanet
- 📧 info@apexplanet.in
- 📱 +91 9905879870
