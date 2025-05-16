````markdown
# 📚 Employee Management System

## 1. 🧑‍💻 User Flow

### a. Registration
- User visits registration page.
- Enters **name, email, password, phone number**.
- System:
  - Validates input.
  - Hashes password securely.
  - Stores user in the `Users` table with default role `customer`.

### b. Login
- User logs in with email and password.
- System:
  - Verifies credentials.
  - Starts session.
  - Redirects based on role (employee, manager, or admin).

### c. Profile Management
- Employee can:
  - View and update personal information (phone, address, emergency contact).
  - Upload documents (e.g., ID, certificates).
  - System stores changes in the Employees table and related document tables.

### d. Attendance
- Employees:
  - Clock in/out daily.
  - View attendance history.
  - System records time entries in Attendance table.

  ### e.  Leave Management
    - Employees:
    - Apply for leave (type, dates, reason).
    - View status of past and current leave requests.
    - Managers:
    - Approve or reject leave requests.
    - System:
    - Updates LeaveRequests and reflects changes in payroll and attendance.
---

## 2. 🛠️ Admin/Manager Flow

### a. Employee Management
- Admins/HR can:
  - View all employee records.
  -Add/edit employee roles, departments, and status (active/resigned).
  - Deactivate or terminate accounts.
  - Managers can:
  - View team members.
  - Track performance and leave.

### b. Performance Review
Managers/Admins:

Assign goals and KPIs.

Submit quarterly/annual reviews.

System stores reviews in PerformanceReviews table and notifies employees.

### c. Payroll Management
Admins can:

Generate monthly salary reports.

Include deductions (leave, tax) and bonuses.

System:

Pulls data from Attendance, LeaveRequests, and salary details.

Stores payslip in Payroll table and allows download.

### d. Exit Process
HR initiates offboarding for exiting employees.

Steps include:

Collecting feedback.

Clearance checklist (IT, Admin, Finance).

Final settlement processing.

System:

Archives employee record.

Issues experience letter and updates status to exited.

## 3. 📏 Rules & Validations
Email must be unique per employee.

Passwords must be securely hashed (e.g., bcrypt).

Leave balance is checked before approval.

Only logged-in users can clock in/out or apply for leave.

Only admins/HR can manage employee records or process payroll.

Attendance cannot be logged retroactively by employees.


---

## 4. 🗄️ Database Schema (SQL)
````


###  Employees (

```sql
id INT PRIMARY KEY,
name VARCHAR(100),
email VARCHAR(100) UNIQUE,
password_hash TEXT,
phone VARCHAR(20),
role ENUM('employee', 'manager', 'admin'),
department VARCHAR(50),
status ENUM('active', 'on_leave', 'exited'),
date_joined DATE
);
```

### Attendance

```sql
id INT PRIMARY KEY,
employee_id INT,
clock_in DATETIME,
clock_out DATETIME,
date DATE,
FOREIGN KEY (employee_id) REFERENCES Employees(id)
```

### Books

```sql
CREATE TABLE Books (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255),
    author VARCHAR(255),
    genre_id INT,
    price DECIMAL(10, 2),
    stock INT DEFAULT 0,
    isbn VARCHAR(20),
    description TEXT,
    cover_image_url TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (genre_id) REFERENCES Genres(id)
);
```

### Orders

```sql
CREATE TABLE Orders (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    total DECIMAL(10, 2),
    status ENUM('pending', 'completed', 'cancelled') DEFAULT 'pending',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES Users(id)
);
```

### OrderDetails

```sql
CREATE TABLE OrderDetails (
    id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT,
    book_id INT,
    quantity INT,
    price DECIMAL(10, 2),
    FOREIGN KEY (order_id) REFERENCES Orders(id),
    FOREIGN KEY (book_id) REFERENCES Books(id)
);
```

### Cart

```sql
CREATE TABLE Cart (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    book_id INT,
    quantity INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES Users(id),
    FOREIGN KEY (book_id) REFERENCES Books(id)
);
```
---