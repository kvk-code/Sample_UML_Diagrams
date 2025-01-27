# System Description


## 1. Overview

This system manages **lab manuals** for different user roles:

- **Admin** – can manage users and lab manuals, access a dashboard.
- **Faculty** – can upload, rename, delete, and restore lab manuals.
- **Student** – can view and download lab manuals.

The application uses a **MySQL** database and **PHP** scripts for business logic and data operations. The **uploads/manuals** directory stores PDF files.

## 2. High-Level Features

1. **Authentication and Authorization**  
   - A login page (`login.php`) validates credentials.  
   - Session data tracks roles.  
   - Each role has specific permissions:  
     - Admin: full control over users and manuals.  
     - Faculty: can manage manuals but not users.  
     - Student: can only view/download manuals.

2. **Manual Management**  
   - Admin and Faculty upload PDFs, rename or delete existing manuals, and restore previous versions if supported.  
   - Students can view and download manuals.  
   - All actions are logged for auditing.

3. **User Management**  
   - Admin can create and deactivate user accounts, change roles, and do bulk imports.  
   - Password changes available to all users (secured by current password check).

4. **Logging/Audit Trails**  
   - The system records significant actions (manual uploads, user updates, downloads) in the database for tracking.

5. **Database Structure**  
   - `users` table for user info (username, password, role).  
   - `lab_manuals` for manual metadata (title, file path, uploading user).  
   - `faculty_actions` (or similar) logs administrative/faculty events.

6. **File Organization**  
   - **Root Directory**: entry scripts (`index.php`, `manual_operations.php`, etc.).  
   - **Views**: role-specific pages under `views/admin`, `views/faculty`, and `views/student`.  
   - **Includes**: shared code (`auth.php`, `functions.php`, `navbar.php`).  
   - **Uploads**: PDF storage in `uploads/manuals`.  
   - **Database Config**: `config/database.php` sets up MySQL connection, and `database.sql` defines tables.

## 3. UML Diagrams

Below is a summary of the UML diagrams to illustrate the system’s structure and workflows:

### 3.1 Use Case Diagram

Shows how **Admin**, **Faculty**, and **Student** interact with the system’s key actions:

- **Login**, **Logout** (common to all)  
- **Manage Manuals** (Admin/Faculty)  
- **Manage Users** (Admin)  
- **Download Manuals** (Student)  
- **Change Password** (all)

> **Diagram Purpose**: Provides a bird’s-eye view of system capabilities per role.

### 3.2 Activity Diagrams

1. **Upload Manual** (Admin/Faculty)  
   - Illustrates the steps from logging in, selecting a file, validating it, uploading to `/uploads/manuals`, and logging actions.

2. **Download Manual** (Student)  
   - Shows how a student requests a file, the system checks for validity, logs the action, and serves the PDF.

> **Diagram Purpose**: Captures the step-by-step control flow for major tasks.

### 3.3 Sequence Diagrams

1. **Upload Manual**  
   - Emphasizes the interactions among the user’s browser, `manual_operations.php`, `functions.php`, and the database.

2. **Download Manual**  
   - Depicts how the system validates the manual ID, retrieves file info, logs the download, and returns the PDF to the student’s browser.

> **Diagram Purpose**: Focuses on the **message flow** and **function calls** between components during specific actions.

### 3.4 Component Diagram

Demonstrates how files and directories are grouped into logical “components”:

- **Root Directory** (public entry scripts),  
- **Views** (admin/faculty/student),  
- **Includes** (interfaces, functions, auth, navbar),  
- **Config & SQL**,  
- **Uploads** (file storage),  
- **MySQL DB** (tables).

A **multi-interface** design (`IManualOps`, `IUserOps`, `ILogOps`) helps reduce diagram clutter. Each interface is ultimately implemented by the `functions.php` code, which in turn connects to the database.

> **Diagram Purpose**: High-level architectural map of system modules, showing how they communicate (arrows) and what interfaces or dependencies exist.

## 4. Key Processes

1. **Login**  
   - User credentials verified in `auth.php`. If valid, role is stored in session, directing the user to role-specific views.

2. **Managing Manuals**  
   - Admin/Faculty sees a manual listing page.  
   - They can upload a PDF, rename it, delete it, or restore a previous version.  
   - All changes are written to the `lab_manuals` table and logged in `faculty_actions`.

3. **Managing Users** (Admin Only)  
   - Creates new user accounts, assigns roles, deactivates accounts, etc.  
   - Activities are recorded for auditing.

4. **Downloading Manuals** (Student)  
   - After login, the student browses the manual list, clicks “Download,” and the system checks existence, logs the event, and streams the PDF.

## 5. Security Considerations

- **Role-Based Access Control**: Only authorized users reach certain pages (enforced via `auth.php`).  
- **Session Management**: `session_start()` in each page; `logout.php` destroys session data.  
- **File Validation**: `uploadManual()` checks file type to ensure only PDFs are uploaded.  
- **Password Hashing**: Storing hashed passwords in `users` table (e.g., via `password_hash()`).

## 6. Future Enhancements

- **HTTPS**: Enforce TLS for secure connections, preventing credential sniffing.  
- **Session Regeneration**: Regenerate session IDs on login to mitigate session fixation.  
- **Foreign Keys**: Strengthen referential integrity in `database.sql` (e.g., `lab_manuals.user_id` references `users.id`).  
- **Better Error Handling**: Centralize error messages for consistent user feedback.  
- **Logging and Auditing**: Expand logging details (timestamps, IP addresses) for more robust auditing.
