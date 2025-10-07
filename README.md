# Vulnerability Report: Stored Cross-Site Scripting (XSS) in Student Grades Management System
## Application Download link: 
```https://www.sourcecodester.com/download-code?nid=18408&title=Student+Grades+Management+System+Using+PHP+and+MySQL+with+Source+Code```
## 1. Executive Summary 📝

- **Product:** Student Grades Management System Using PHP and MySQL
- **Vulnerability Type:** Stored Cross-Site Scripting (XSS)
- **Vulnerability Location:** Admin Panel - "Add New User" functionality
- **Impact:** High. An authenticated administrator can inject malicious JavaScript code into the application. This code executes in the browser of any other administrator viewing the user list, potentially leading to session hijacking, data theft, or administrative actions being performed on behalf of the victim.
- **CWE-79:** Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting')

## 2. Vulnerability Description 🧐

The "Add New User" feature within the administrator panel is vulnerable to Stored XSS. The `first_name` and `last_name` parameters are not properly sanitized before being stored in the database.

When an administrator creates a new user, they can input a malicious JavaScript payload into these fields. This payload is then saved directly to the `users` table. The payload is later retrieved and rendered without proper output encoding on pages that display user information, such as the "Manage Users" table and the "Recent Users" list on the main dashboard. This causes the malicious script to execute in the context of the viewing administrator's browser.

## 3. Vulnerable Code Analysis 💻

The vulnerability exists in the `admin.php` file.

#### Data Injection (Input)
In the `add_user` logic (lines 10-26 of `admin.php`), the `first_name` and `last_name` are taken directly from the `$_POST` request and stored in the database without any sanitization or encoding.

```php
// admin.php: Lines 15-16
$first_name = $_POST['first_name'];
$last_name = $_POST['last_name'];

// admin.php: Line 23
$stmt = $pdo->prepare("INSERT INTO users (username, password, email, role, first_name, last_name) VALUES (?, ?, ?, ?, ?, ?)");
// admin.php: Line 24
$stmt->execute([$username, $password, $email, $role, $first_name, $last_name]);
```

# Steps to Reproduce (Proof of Concept) 🚀

  #### Login: 
  1. Log in to the application as an administrator (e.g., admin / admin123).

  #### Navigate: 
  2. Go to the Manage Users tab from the sidebar. 🧭

  #### Inject Payload: 
  3. In the "Add New User" form, enter the following payload into the First Name and Last Name fields. Fill out the other fields with any valid data.

  #### Payload: 
  
  ```<img src=x onerror=alert("XSS")>```

  #### ScreenShots
  
<img width="958" height="443" alt="student_xss1" src="https://github.com/user-attachments/assets/7fe5cda4-e6f7-4887-ba0e-535e5be4c34c" />


  #### Add User: 
  Click the "Add User" button. The user will be created successfully.

  #### Trigger Payload: 
  
  Navigate back to the Dashboard. The "Recent Users" table will render the malicious payload. The browser will execute the script, and an alert box with the text "XSS" will appear. ✅

  #### ScreenShots
  
<img width="955" height="400" alt="student_xss2" src="https://github.com/user-attachments/assets/80c63ff8-9f2d-420a-ac07-e3af29ac8b02" />
