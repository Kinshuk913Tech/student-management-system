# ERP System Architecture & Logic Flow

This document explains how the **Student Management System** works, how its components separate, and how they connect to the database.

## 1. High-Level Overview
The system is built on **Django**, a powerful Python web framework. It follows the **MVT (Model-View-Template)** architecture:
-   **Model (Database)**: Defines the structure of your data (Students, Staff, Courses).
-   **View (Logic)**: Handles the logic (calculating results, saving students, checking login).
-   **Template (Interface)**: The HTML pages the user sees.

## 2. User Roles & Security
The system revolves around a single **CustomUser** model that splits into three distinct roles. This is defined in `app/models.py`.
-   **HOD (Admin)**: `user_type=1` - Has full access.
-   **Staff**: `user_type=2` - Limited to their subjects and students.
-   **Student**: `user_type=3` - View-only access for their own records.

> **Analogy**: Think of `CustomUser` as a generic ID card. The `user_type` stamp on it determines if you can enter the Principal's office (HOD), the Staff room (Staff), or just the Classrooms (Student).

## 3. The Database (The "Brain")
All data structure is defined in `app/models.py`.

### Key Relationships
1.  **Users**: `CustomUser` is the parent. `Student` and `Staff` models act as "extensions" (OneToOneField) to store extra details like address and gender.
2.  **Academics**:
    -   `Course`: (e.g., "Computer Science", "Mechanical Eng")
    -   `Session_Year`: (e.g., "2022-2025")
    -   `Subject`: Linked to a `Course` and assigned to a `Staff`.
3.  **Operations**:
    -   `Attendance`: Links `Subject`, `Student`, and `Session_Year`.
    -   `Staff_Leave` / `Student_Leave`: Requests waiting for approval.

## 4. How It Works Together (The Flow)

When you click "Add Student", here is the journey:

1.  **The Request (URL)**:
    -   You go to `/Hod/Student/Add`.
    -   `urls.py` sees this address and shouts: "Hey `Hod_Views.ADD_STUDENT`, this is for you!"

2.  **The Logic (View)**:
    -   The function `ADD_STUDENT` in `Hod_Views.py` wakes up.
    -   It checks: "Is this a POST request (data submission) or GET (viewing the page)?"
    -   **If GET**: It grabs the list of Courses and Sessions from the **Database** and sends them to the **Template**.
    -   **If POST**: It takes the data (Name, Email, Password), creates a generic `CustomUser`, then creates a specific `Student` profile linked to it, and saves both to the **Database**.

3.  **The Interface (Template)**:
    -   The HTML file (`add_student.html`) receives the data and renders the form.

## 5. File Structure Cheat Sheet
-   `student_management_system/settings.py`: The control center (Database config, Installed Apps).
-   `student_management_system/urls.py`: The traffic cop (Directs URLs to Views).
-   `app/models.py`: The blueprint for the database tables.
-   `app/Hod_Views.py`: Logic for the Admin (Principal).
-   `app/Staff_Views.py`: Logic for the Teachers.
-   `app/Student_Views.py`: Logic for the Students.
-   `app/templates/`: The visual HTML files.

## 6. Database Connection
-   The system uses **SQLite** by default (`db.sqlite3` file).
-   Django's **ORM (Object-Relational Mapper)** allows you to write Python code (`Student.objects.all()`) instead of raw SQL queries (`SELECT * FROM app_student`). This makes it secure and easy to manage.
