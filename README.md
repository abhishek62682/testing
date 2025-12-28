# 📚 DMS API Complete Testing Documentation

> **Document Management System - Comprehensive API Testing Guide**  
> Test all endpoints systematically to verify proper access control, MyDrive isolation, and department permissions.

---

## 📋 Table of Contents

1. [Prerequisites](#prerequisites)
2. [Step 1: Authentication](#step-1-authentication)
3. [Step 2: Create Departments](#step-2-create-departments)
4. [Step 3: Create Users](#step-3-create-users)
5. [Step 4: Login as Different Users](#step-4-login-as-different-users)
6. [Step 5: Test Department Access](#step-5-test-department-access)
7. [Step 6: Folders in MyDrive](#step-6-folders-in-mydrive)
8. [Step 7: Folders in Organization Departments](#step-7-folders-in-organization-departments)
9. [Step 8: Test Access Control](#step-8-test-access-control)
10. [Step 9: Folder Sharing](#step-9-folder-sharing)
11. [Step 10: Cross-Department Tests](#step-10-cross-department-tests)

---

## ⚙️ Prerequisites

- **Base URL:** `http://localhost:5000/api`
- **Super Admin Email:** `abhishek.sharma@digihost.in`
- **Super Admin Password:** Your configured password
- **Postman or any REST client**

### Variables to Track During Testing

Create these variables in Postman or keep them handy:

```
SUPER_ADMIN_TOKEN
USER1_TOKEN (Abhishek - Admin Engineering)
USER2_TOKEN (Ajay - Admin Marketing)
USER3_TOKEN (Firebase - Dept Owner Finance)
USER4_TOKEN (Arcade - User Engineering)
USER5_TOKEN (Shaxma - User No Dept)

ENGINEERING_DEPT_ID
MARKETING_DEPT_ID
FINANCE_DEPT_ID

USER1_ID, USER1_MYDRIVE_ID
USER2_ID, USER2_MYDRIVE_ID
USER3_ID, USER3_MYDRIVE_ID
USER4_ID, USER4_MYDRIVE_ID
USER5_ID, USER5_MYDRIVE_ID

FOLDER1_ID, FOLDER2_ID, etc.
```

---

## 🔐 STEP 1: Authentication

### 1.1 Login as Super Admin

**Endpoint:** `POST /api/auth/login`

**Request Body:**
```json
{
  "email": "abhishek.sharma@digihost.in",
  "password": "your_password_here"
}
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "_id": "675e1234567890abcdef1234",
      "username": "abhishek_sharma",
      "email": "abhishek.sharma@digihost.in",
      "role": "SUPER_ADMIN"
    }
  }
}
```

**✅ Action:** Copy the `token` and save as `SUPER_ADMIN_TOKEN`

**Use in all requests as:**
```
Authorization: Bearer YOUR_SUPER_ADMIN_TOKEN
```

---

## 📁 STEP 2: Create Departments

### 2.1 Create Engineering Department

**Endpoint:** `POST /api/departments`

**Headers:**
```
Authorization: Bearer SUPER_ADMIN_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "Engineering",
  "description": "Engineering Department - Software Development"
}
```

**Expected Response (201 Created):**
```json
{
  "success": true,
  "message": "Department created successfully",
  "data": {
    "_id": "675e1111111111111111111",
    "name": "Engineering",
    "description": "Engineering Department - Software Development",
    "ownerType": "ORG",
    "ownerId": null,
    "isActive": true,
    "path": "/Engineering",
    "createdAt": "2024-12-27T10:00:00.000Z"
  }
}
```

**✅ Action:** Save `_id` as `ENGINEERING_DEPT_ID`

---

### 2.2 Create Marketing Department

**Endpoint:** `POST /api/departments`

**Headers:**
```
Authorization: Bearer SUPER_ADMIN_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "Marketing",
  "description": "Marketing Department - Brand & Growth"
}
```

**✅ Action:** Save `_id` as `MARKETING_DEPT_ID`

---

### 2.3 Create Finance Department

**Endpoint:** `POST /api/departments`

**Headers:**
```
Authorization: Bearer SUPER_ADMIN_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "Finance",
  "description": "Finance Department - Accounting & Budgets"
}
```

**✅ Action:** Save `_id` as `FINANCE_DEPT_ID`

---

### 2.4 Verify - Get All Departments

**Endpoint:** `GET /api/departments`

**Headers:**
```
Authorization: Bearer SUPER_ADMIN_TOKEN
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "count": 3,
  "page": 1,
  "limit": 10,
  "totalPages": 1,
  "data": [
    {
      "_id": "675e1111111111111111111",
      "name": "Engineering",
      "ownerType": "ORG",
      "isActive": true
    },
    {
      "_id": "675e2222222222222222222",
      "name": "Marketing",
      "ownerType": "ORG",
      "isActive": true
    },
    {
      "_id": "675e3333333333333333333",
      "name": "Finance",
      "ownerType": "ORG",
      "isActive": true
    }
  ]
}
```

**✅ Test:** Should see ONLY ORG departments (no MyDrive departments)

---

## 👥 STEP 3: Create Users

> **Note:** Each user automatically gets a MyDrive department created with `ownerType: "USER"`

### 3.1 Create User 1 - Abhishek (Admin - Engineering)

**Endpoint:** `POST /api/admin/users`

**Headers:**
```
Authorization: Bearer SUPER_ADMIN_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "username": "abhishek_react",
  "email": "abhishekreact.dev@gmail.com",
  "role": "ADMIN",
  "departments": ["PASTE_ENGINEERING_DEPT_ID_HERE"]
}
```

**Example with actual ID:**
```json
{
  "username": "abhishek_react",
  "email": "abhishekreact.dev@gmail.com",
  "role": "ADMIN",
  "departments": ["675e1111111111111111111"]
}
```

**Expected Response (201 Created):**
```json
{
  "success": true,
  "message": "User created successfully with MyDrive. Welcome email sent with temporary password.",
  "data": {
    "userId": "675e4444444444444444444",
    "username": "abhishek_react",
    "email": "abhishekreact.dev@gmail.com",
    "role": "ADMIN",
    "myDriveDepartmentId": "675e5555555555555555555",
    "assignedDepartments": ["675e1111111111111111111"]
  }
}
```

**✅ Actions:**
- Save `userId` as `USER1_ID`
- Save `myDriveDepartmentId` as `USER1_MYDRIVE_ID`
- **Default Password:** `Welcome@123`

---

### 3.2 Create User 2 - Ajay (Admin - Marketing)

**Endpoint:** `POST /api/admin/users`

**Headers:**
```
Authorization: Bearer SUPER_ADMIN_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "username": "ajay_yadav",
  "email": "ajayyadav210586@gmail.com",
  "role": "ADMIN",
  "departments": ["PASTE_MARKETING_DEPT_ID_HERE"]
}
```

**✅ Actions:**
- Save `userId` as `USER2_ID`
- Save `myDriveDepartmentId` as `USER2_MYDRIVE_ID`
- **Password:** `Welcome@123`

---

### 3.3 Create User 3 - Firebase (Department Owner - Finance)

**Endpoint:** `POST /api/admin/users`

**Headers:**
```
Authorization: Bearer SUPER_ADMIN_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "username": "firebase_demo",
  "email": "firebasedemo96@gmail.com",
  "role": "DEPARTMENT_OWNER",
  "departments": ["PASTE_FINANCE_DEPT_ID_HERE"]
}
```

**✅ Actions:**
- Save `userId` as `USER3_ID`
- Save `myDriveDepartmentId` as `USER3_MYDRIVE_ID`
- **Password:** `Welcome@123`

---

### 3.4 Create User 4 - Arcade (Regular User - Engineering)

**Endpoint:** `POST /api/admin/users`

**Headers:**
```
Authorization: Bearer SUPER_ADMIN_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "username": "arcade_user",
  "email": "abhishek.arcade8928@gmail.com",
  "role": "USER",
  "departments": ["PASTE_ENGINEERING_DEPT_ID_HERE"]
}
```

**✅ Actions:**
- Save `userId` as `USER4_ID`
- Save `myDriveDepartmentId` as `USER4_MYDRIVE_ID`
- **Password:** `Welcome@123`

---

### 3.5 Create User 5 - Shaxma (Regular User - No Department)

**Endpoint:** `POST /api/admin/users`

**Headers:**
```
Authorization: Bearer SUPER_ADMIN_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "username": "shaxma_user",
  "email": "abhishekshaxma8356@gmail.com",
  "role": "USER",
  "departments": []
}
```

**✅ Actions:**
- Save `userId` as `USER5_ID`
- Save `myDriveDepartmentId` as `USER5_MYDRIVE_ID`
- **Password:** `Welcome@123`

---

## 🔑 STEP 4: Login as Different Users

### 4.1 Login as User 1 (Abhishek - Admin Engineering)

**Endpoint:** `POST /api/auth/login`

**Request Body:**
```json
{
  "email": "abhishekreact.dev@gmail.com",
  "password": "Welcome@123"
}
```

**✅ Action:** Save token as `USER1_TOKEN`

---

### 4.2 Login as User 2 (Ajay - Admin Marketing)

**Endpoint:** `POST /api/auth/login`

**Request Body:**
```json
{
  "email": "ajayyadav210586@gmail.com",
  "password": "Welcome@123"
}
```

**✅ Action:** Save token as `USER2_TOKEN`

---

### 4.3 Login as User 3 (Firebase - Dept Owner Finance)

**Endpoint:** `POST /api/auth/login`

**Request Body:**
```json
{
  "email": "firebasedemo96@gmail.com",
  "password": "Welcome@123"
}
```

**✅ Action:** Save token as `USER3_TOKEN`

---

### 4.4 Login as User 4 (Arcade - User Engineering)

**Endpoint:** `POST /api/auth/login`

**Request Body:**
```json
{
  "email": "abhishek.arcade8928@gmail.com",
  "password": "Welcome@123"
}
```

**✅ Action:** Save token as `USER4_TOKEN`

---

### 4.5 Login as User 5 (Shaxma - No Department)

**Endpoint:** `POST /api/auth/login`

**Request Body:**
```json
{
  "email": "abhishekshaxma8356@gmail.com",
  "password": "Welcome@123"
}
```

**✅ Action:** Save token as `USER5_TOKEN`

---

## 🧪 STEP 5: Test Department Access

### 5.1 Super Admin - Get All Departments

**Endpoint:** `GET /api/departments`

**Headers:**
```
Authorization: Bearer SUPER_ADMIN_TOKEN
```

**Expected:** Should see ALL 3 ORG departments (Engineering, Marketing, Finance)

**❌ Should NOT see:** Any MyDrive departments (ownerType: "USER")

---

### 5.2 User 1 (Admin Engineering) - Get All Departments

**Endpoint:** `GET /api/departments`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected Response:**
```json
{
  "success": true,
  "count": 1,
  "data": [
    {
      "_id": "675e1111111111111111111",
      "name": "Engineering",
      "ownerType": "ORG"
    }
  ]
}
```

**✅ Test:** Should see ONLY Engineering department  
**❌ Should NOT see:** Marketing, Finance, or any MyDrive

---

### 5.3 User 2 (Admin Marketing) - Get All Departments

**Endpoint:** `GET /api/departments`

**Headers:**
```
Authorization: Bearer USER2_TOKEN
```

**✅ Test:** Should see ONLY Marketing department  
**❌ Should NOT see:** Engineering, Finance, or any MyDrive

---

### 5.4 User 5 (No Department) - Get All Departments

**Endpoint:** `GET /api/departments`

**Headers:**
```
Authorization: Bearer USER5_TOKEN
```

**Expected Response:**
```json
{
  "success": true,
  "count": 0,
  "data": []
}
```

**✅ Test:** Should see EMPTY array (no ORG departments assigned)

---

### 5.5 User 1 - Access Engineering Department (Own Dept)

**Endpoint:** `GET /api/departments/{ENGINEERING_DEPT_ID}`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected:** ✅ **200 OK** - User has access to their assigned department

---

### 5.6 User 1 - Try Access Marketing Department (Not Assigned)

**Endpoint:** `GET /api/departments/{MARKETING_DEPT_ID}`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected Response (403 Forbidden):**
```json
{
  "success": false,
  "message": "Access denied. You do not have access to this department"
}
```

**✅ Test:** ❌ **403 Forbidden** - User cannot access non-assigned department

---

### 5.7 User 1 - Access Their Own MyDrive

**Endpoint:** `GET /api/departments/{USER1_MYDRIVE_ID}`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected:** ✅ **200 OK** - User can access their own MyDrive

---

### 5.8 User 1 - Try Access User 2's MyDrive

**Endpoint:** `GET /api/departments/{USER2_MYDRIVE_ID}`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected Response (403 Forbidden):**
```json
{
  "success": false,
  "message": "Access denied. You do not have access to this department"
}
```

**✅ Test:** ❌ **403 Forbidden** - Cannot access another user's MyDrive

---

### 5.9 Super Admin - Can Access Any MyDrive

**Endpoint:** `GET /api/departments/{USER1_MYDRIVE_ID}`

**Headers:**
```
Authorization: Bearer SUPER_ADMIN_TOKEN
```

**Expected:** ✅ **200 OK** - Super Admin can access any department including MyDrive

---

## 📂 STEP 6: Folders in MyDrive

### 6.1 User 1 - Create Folder in Their MyDrive

**Endpoint:** `POST /api/folders`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "Personal Documents",
  "parentId": "PASTE_USER1_MYDRIVE_ID_HERE",
  "description": "My personal files and documents",
  "color": "#3B82F6"
}
```

**Expected Response (201 Created):**
```json
{
  "success": true,
  "message": "Folder created successfully",
  "data": {
    "_id": "675e6666666666666666666",
    "name": "Personal Documents",
    "parentId": "675e5555555555555555555",
    "departmentId": "675e5555555555555555555",
    "path": "/MyDrive_675e4444444444444444444/Personal Documents",
    "createdBy": "675e4444444444444444444"
  }
}
```

**✅ Action:** Save `_id` as `USER1_FOLDER1_ID`

---

### 6.2 User 1 - Create Subfolder in Personal Documents

**Endpoint:** `POST /api/folders`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "Tax Documents",
  "parentId": "PASTE_USER1_FOLDER1_ID_HERE",
  "description": "Tax returns and documents"
}
```

**✅ Action:** Save `_id` as `USER1_FOLDER2_ID`

---

### 6.3 User 1 - Get Children of MyDrive

**Endpoint:** `GET /api/folders/{USER1_MYDRIVE_ID}/children`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected:** Should see "Personal Documents" folder

---

### 6.4 User 2 - Try to Create Folder in User 1's MyDrive

**Endpoint:** `POST /api/folders`

**Headers:**
```
Authorization: Bearer USER2_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "Hacker Folder",
  "parentId": "PASTE_USER1_MYDRIVE_ID_HERE",
  "description": "Trying to hack"
}
```

**Expected Response (403 Forbidden):**
```json
{
  "success": false,
  "message": "You do not have permission to create items here"
}
```

**✅ Test:** ❌ **403 Forbidden** - Cannot create folder in another user's MyDrive

---

### 6.5 User 2 - Try to View User 1's MyDrive Folder

**Endpoint:** `GET /api/folders/{USER1_FOLDER1_ID}`

**Headers:**
```
Authorization: Bearer USER2_TOKEN
```

**Expected Response (403 Forbidden):**
```json
{
  "success": false,
  "message": "You do not have permission to view this folder"
}
```

**✅ Test:** ❌ **403 Forbidden** - Cannot view folders in another user's MyDrive

---

## 🏢 STEP 7: Folders in Organization Departments

### 7.1 User 1 (Admin) - Create Folder in Engineering Dept

**Endpoint:** `POST /api/folders`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "Backend Projects",
  "parentId": "PASTE_ENGINEERING_DEPT_ID_HERE",
  "description": "All backend development projects"
}
```

**Expected:** ✅ **201 Created** - Admin can create folders in their department

**✅ Action:** Save `_id` as `ENG_FOLDER1_ID`

---

### 7.2 User 1 - Create Subfolder in Backend Projects

**Endpoint:** `POST /api/folders`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "DMS Project",
  "parentId": "PASTE_ENG_FOLDER1_ID_HERE",
  "description": "Document Management System"
}
```

**✅ Action:** Save `_id` as `ENG_FOLDER2_ID`

---

### 7.3 User 4 (Regular User) - Try Create Folder in Engineering

**Endpoint:** `POST /api/folders`

**Headers:**
```
Authorization: Bearer USER4_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "My Folder",
  "parentId": "PASTE_ENGINEERING_DEPT_ID_HERE",
  "description": "Trying to create"
}
```

**Expected Response (403 Forbidden):**
```json
{
  "success": false,
  "message": "You do not have permission to create items here"
}
```

**✅ Test:** ❌ **403 Forbidden** - Regular user cannot create folders at department root  
**Note:** They need explicit permission via ACL

---

### 7.4 User 1 - Try Create Folder in Marketing (Not Their Dept)

**Endpoint:** `POST /api/folders`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "Hacker Folder",
  "parentId": "PASTE_MARKETING_DEPT_ID_HERE",
  "description": "Should fail"
}
```

**Expected:** ❌ **403 Forbidden** - Cannot create in non-assigned department

---

## 🔒 STEP 8: Test Access Control

### 8.1 User 1 - View Engineering Folder

**Endpoint:** `GET /api/folders/{ENG_FOLDER1_ID}`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected:** ✅ **200 OK** - Admin can view folders in their department

---

### 8.2 User 2 - Try View Engineering Folder

**Endpoint:** `GET /api/folders/{ENG_FOLDER1_ID}`

**Headers:**
```
Authorization: Bearer USER2_TOKEN
```

**Expected:** ❌ **403 Forbidden** - Cannot view folders in non-assigned department

---

### 8.3 User 4 - Try View Engineering Folder (Same Dept, No ACL)

**Endpoint:** `GET /api/folders/{ENG_FOLDER1_ID}`

**Headers:**
```
Authorization: Bearer USER4_TOKEN
```

**Expected:** ❌ **403 Forbidden** - Regular user needs explicit ACL permission

**Note:** Being in the same department doesn't automatically grant folder access

---

## 🤝 STEP 9: Folder Sharing

### 9.1 User 1 - Share Folder with User 4 (View Only)

**Endpoint:** `POST /api/folders/{ENG_FOLDER1_ID}/share`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "users": [
    {
      "userId": "PASTE_USER4_ID_HERE",
      "permissions": ["view"]
    }
  ],
  "groups": []
}
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "message": "Folder shared successfully",
  "data": {
    "shared": {
      "users": 1,
      "groups": 0
    }
  }
}
```

---

### 9.2 User 4 - Now View Shared Folder

**Endpoint:** `GET /api/folders/{ENG_FOLDER1_ID}`

**Headers:**
```
Authorization: Bearer USER4_TOKEN
```

**Expected:** ✅ **200 OK** - Now can view because User 1 shared it

---

### 9.3 User 4 - Try Create Subfolder (No Upload Permission)

**Endpoint:** `POST /api/folders`

**Headers:**
```
Authorization: Bearer USER4_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "My Subfolder",
  "parentId": "PASTE_ENG_FOLDER1_ID_HERE"
}
```

**Expected:** ❌ **403 Forbidden** - Only has view permission, not upload

---

### 9.4 User 1 - Share Folder with User 4 (Upload Permission)

**Endpoint:** `POST /api/folders/{ENG_FOLDER1_ID}/share`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "users": [
    {
      "userId": "PASTE_USER4_ID_HERE",
      "permissions": ["view", "upload"]
    }
  ],
  "groups": []
}
```

---

### 9.5 User 4 - Now Create Subfolder (Has Upload)

**Endpoint:** `POST /api/folders`

**Headers:**
```
Authorization: Bearer USER4_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "My Subfolder",
  "parentId": "PASTE_ENG_FOLDER1_ID_HERE",
  "description": "Now I can create!"
}
```

**Expected:** ✅ **201 Created** - Now has upload permission

---

### 9.6 User 1 - Share with User 5 (Outside Department)

**Endpoint:** `POST /api/folders/{ENG_FOLDER1_ID}/share`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "users": [
    {
      "userId": "PASTE_USER5_ID_HERE",
      "permissions": ["view", "download"]
    }
  ],
  "groups": []
}
```

**Expected:** ✅ **200 OK** - Can share with users outside department via ACL

---

### 9.7 User 5 - View Shared Folder (Cross-Department)

**Endpoint:** `GET /api/folders/{ENG_FOLDER1_ID}`

**Headers:**
```
Authorization: Bearer USER5_TOKEN
```

**Expected:** ✅ **200 OK** - Can view because explicitly shared

---

### 9.8 User 4 - Try to Share Folder (No Share Permission)

**Endpoint:** `POST /api/folders/{ENG_FOLDER1_ID}/share`

**Headers:**
```
Authorization: Bearer USER4_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "users": [
    {
      "userId": "PASTE_USER5_ID_HERE",
      "permissions": ["view"]
    }
  ],
  "groups": []
}
```

**Expected:** ❌ **403 Forbidden** - User 4 doesn't have share permission

---

## 🔄 STEP 10: Cross-Department Tests

### 10.1 User 2 - Create Folder in Marketing

**Endpoint:** `POST /api/folders`

**Headers:**
```
Authorization: Bearer USER2_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "Marketing Campaigns",
  "parentId": "PASTE_MARKETING_DEPT_ID_HERE",
  "description": "All marketing campaign materials"
}
```

**✅ Action:** Save `_id` as `MARKETING_FOLDER1_ID`

---

### 10.2 User 1 - Try to View Marketing Folder

**Endpoint:** `GET /api/folders/{MARKETING_FOLDER1_ID}`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected:** ❌ **403 Forbidden** - Cannot access another department's folders

---

### 10.3 Super Admin - Can View Any Folder

**Endpoint:** `GET /api/folders/{MARKETING_FOLDER1_ID}`

**Headers:**
```
Authorization: Bearer SUPER_ADMIN_TOKEN
```

**Expected:** ✅ **200 OK** - Super Admin has access to all departments

---

### 10.4 User 2 - Share Marketing Folder with User 1

**Endpoint:** `POST /api/folders/{MARKETING_FOLDER1_ID}/share`

**Headers:**
```
Authorization: Bearer USER2_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "users": [
    {
      "userId": "PASTE_USER1_ID_HERE",
      "permissions": ["view", "download"]
    }
  ],
  "groups": []
}
```

**Expected:** ✅ **200 OK** - Cross-department sharing via ACL

---

### 10.5 User 1 - Now View Marketing Folder

**Endpoint:** `GET /api/folders/{MARKETING_FOLDER1_ID}`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected:** ✅ **200 OK** - Now can view because User 2 shared it

---



# ⭐ STEP 11: Test Starred Feature

> **Comprehensive testing for starred items with permission validation**

---

## 📋 Prerequisites for Starred Tests

Before starting, ensure you have:
- All users logged in with saved tokens
- Folders created in different departments and MyDrives
- User permissions already set up from previous steps

**Required Variables:**
```
USER1_TOKEN (Abhishek - Admin Engineering)
USER2_TOKEN (Ajay - Admin Marketing)
USER4_TOKEN (Arcade - User Engineering)
USER5_TOKEN (Shaxma - User No Dept)

USER1_FOLDER1_ID (Personal Documents in MyDrive)
ENG_FOLDER1_ID (Backend Projects in Engineering)
ENG_FOLDER2_ID (DMS Project - subfolder)
MARKETING_FOLDER1_ID (Marketing Campaigns)
```

---

## ⭐ 11.1 User 1 - Star Folder in Their MyDrive

**Endpoint:** `POST /api/starred/add`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "id": "PASTE_USER1_FOLDER1_ID_HERE",
  "type": "folder"
}
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "message": "folder added to starred successfully",
  "data": {
    "id": "675e6666666666666666666",
    "name": "Personal Documents",
    "type": "folder",
    "starred": true,
    "starredAt": "2024-12-28T10:30:00.000Z"
  }
}
```

**✅ Test:** User can star their own MyDrive folder

---

## ⭐ 11.2 User 1 - Star Folder in Engineering Department

**Endpoint:** `POST /api/starred/add`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "id": "PASTE_ENG_FOLDER1_ID_HERE",
  "type": "folder"
}
```

**Expected:** ✅ **200 OK** - Admin can star folders in their department

---

## ⭐ 11.3 User 2 - Try Star Engineering Folder (No Access)

**Endpoint:** `POST /api/starred/add`

**Headers:**
```
Authorization: Bearer USER2_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "id": "PASTE_ENG_FOLDER1_ID_HERE",
  "type": "folder"
}
```

**Expected Response (403 Forbidden):**
```json
{
  "success": false,
  "message": "You do not have permission to view this folder"
}
```

**✅ Test:** ❌ **403 Forbidden** - Cannot star folders without view permission  
**Note:** Middleware validates view access BEFORE starring

---

## ⭐ 11.4 User 4 - Star Shared Folder (Has View Access)

**Context:** User 1 previously shared `ENG_FOLDER1_ID` with User 4 (view permission)

**Endpoint:** `POST /api/starred/add`

**Headers:**
```
Authorization: Bearer USER4_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "id": "PASTE_ENG_FOLDER1_ID_HERE",
  "type": "folder"
}
```

**Expected:** ✅ **200 OK** - Can star because has view permission via ACL

**✅ Test:** Users can star folders they have been given access to

---

## ⭐ 11.5 User 5 - Try Star Engineering Folder (No Access)

**Context:** User 5 is not in Engineering dept and has no ACL permissions

**Endpoint:** `POST /api/starred/add`

**Headers:**
```
Authorization: Bearer USER5_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "id": "PASTE_ENG_FOLDER1_ID_HERE",
  "type": "folder"
}
```

**Expected:** ❌ **403 Forbidden** - No department access, no ACL permission

---

## ⭐ 11.6 User 1 - Try Star User 2's MyDrive Folder

**Endpoint:** `POST /api/starred/add`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "id": "PASTE_USER2_MYDRIVE_FOLDER_ID_HERE",
  "type": "folder"
}
```

**Expected:** ❌ **403 Forbidden** - Cannot access another user's MyDrive

**✅ Test:** MyDrive isolation is enforced for starring

---

## 📤 11.7 User 1 - Get All Starred Items

**Endpoint:** `GET /api/starred`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "count": 2,
  "children": [
    {
      "_id": "675e1111111111111111111",
      "name": "Backend Projects",
      "type": "folder",
      "color": "#3B82F6",
      "path": "/Engineering/Backend Projects",
      "starredAt": "2024-12-28T10:35:00.000Z",
      "createdAt": "2024-12-27T10:00:00.000Z",
      "updatedAt": "2024-12-27T10:00:00.000Z",
      "parentId": "675eENGINEERING_DEPT_ID",
      "createdBy": {
        "username": "abhishek_react",
        "email": "abhishekreact.dev@gmail.com"
      },
      "actions": {
        "canView": true,
        "canDownload": true,
        "canUpload": true,
        "canDelete": true,
        "canShare": true,
        "canCreateSubfolder": true,
        "canUploadFile": true
      }
    },
    {
      "_id": "675e6666666666666666666",
      "name": "Personal Documents",
      "type": "folder",
      "color": "#3B82F6",
      "path": "/MyDrive_675e4444444444444444444/Personal Documents",
      "starredAt": "2024-12-28T10:30:00.000Z",
      "createdAt": "2024-12-27T09:00:00.000Z",
      "actions": {
        "canView": true,
        "canDownload": true,
        "canUpload": true,
        "canDelete": true,
        "canShare": true,
        "canCreateSubfolder": true,
        "canUploadFile": true
      }
    }
  ]
}
```

**✅ Tests:**
- Returns only items user has access to
- Sorted by `starredAt` (most recent first)
- Includes `actions` object (like children API)
- Uses `children` key (same as getChildFolders)

---

## ⭐ 11.8 User 4 - Get Starred Items (Limited Access)

**Endpoint:** `GET /api/starred`

**Headers:**
```
Authorization: Bearer USER4_TOKEN
```

**Expected:** 
- Should see ONLY folders they starred
- Should NOT see folders they no longer have access to
- Actions reflect their limited permissions

**Example Response:**
```json
{
  "success": true,
  "count": 1,
  "children": [
    {
      "_id": "675e1111111111111111111",
      "name": "Backend Projects",
      "type": "folder",
      "actions": {
        "canView": true,
        "canDownload": false,
        "canUpload": true,
        "canDelete": false,
        "canShare": false,
        "canCreateSubfolder": true,
        "canUploadFile": true
      }
    }
  ]
}
```

**✅ Test:** Actions reflect actual user permissions (view + upload only)

---

## 🗑️ 11.9 User 1 - Unstar Folder

**Endpoint:** `POST /api/starred/remove`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "id": "PASTE_USER1_FOLDER1_ID_HERE",
  "type": "folder"
}
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "message": "folder removed from starred successfully",
  "data": {
    "id": "675e6666666666666666666",
    "name": "Personal Documents",
    "type": "folder",
    "starred": false
  }
}
```

**✅ Test:** Can unstar previously starred folder

---

## 🗑️ 11.10 User 2 - Try Unstar Engineering Folder (Never Had Access)

**Endpoint:** `POST /api/starred/remove`

**Headers:**
```
Authorization: Bearer USER2_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "id": "PASTE_ENG_FOLDER1_ID_HERE",
  "type": "folder"
}
```

**Expected:** ❌ **403 Forbidden** - Still needs view permission to unstar

**✅ Test:** Cannot unstar folders without view access

---

## 📦 11.11 User 1 - Bulk Star Multiple Items

**Endpoint:** `POST /api/starred/bulk`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "folderIds": [
    "PASTE_ENG_FOLDER1_ID_HERE",
    "PASTE_ENG_FOLDER2_ID_HERE"
  ],
  "fileIds": [],
  "starred": true
}
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "message": "Bulk starred operation completed",
  "data": {
    "totalRequested": 2,
    "totalUpdated": 2,
    "starred": true,
    "results": {
      "files": {
        "updated": 0,
        "notFound": [],
        "noPermission": [],
        "deleted": []
      },
      "folders": {
        "updated": 2,
        "notFound": [],
        "noPermission": [],
        "deleted": []
      }
    }
  }
}
```

**✅ Test:** Can bulk star multiple folders at once

---

## 📦 11.12 User 2 - Bulk Star (Mixed Permissions)

**Endpoint:** `POST /api/starred/bulk`

**Headers:**
```
Authorization: Bearer USER2_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "folderIds": [
    "PASTE_MARKETING_FOLDER1_ID_HERE",
    "PASTE_ENG_FOLDER1_ID_HERE"
  ],
  "fileIds": [],
  "starred": true
}
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "message": "Bulk starred operation completed",
  "data": {
    "totalRequested": 2,
    "totalUpdated": 1,
    "starred": true,
    "results": {
      "folders": {
        "updated": 1,
        "notFound": [],
        "noPermission": ["PASTE_ENG_FOLDER1_ID_HERE"],
        "deleted": []
      }
    }
  }
}
```

**✅ Tests:**
- Successfully stars `MARKETING_FOLDER1_ID` (has access)
- Fails to star `ENG_FOLDER1_ID` (no access) → added to `noPermission` array
- Operation continues for accessible items

---

## 📦 11.13 User 4 - Bulk Unstar Items

**Endpoint:** `POST /api/starred/bulk`

**Headers:**
```
Authorization: Bearer USER4_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "folderIds": ["PASTE_ENG_FOLDER1_ID_HERE"],
  "fileIds": [],
  "starred": false
}
```

**Expected:** ✅ **200 OK** - Can unstar items they have access to

---

## 🔒 11.14 Permission Loss Test - User 1 Removes User 4's Access

**Step 1:** User 1 removes User 4's ACL permission on `ENG_FOLDER1_ID`

**Endpoint:** `POST /api/folders/{ENG_FOLDER1_ID}/share`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "users": [],
  "groups": []
}
```

**Step 2:** User 4 gets starred items

**Endpoint:** `GET /api/starred`

**Headers:**
```
Authorization: Bearer USER4_TOKEN
```

**Expected:** 
- `ENG_FOLDER1_ID` should NOT appear in results
- Even if starred, items without view access are filtered out

**✅ Test:** Starred items list automatically filters out items user lost access to

---

## 🗑️ 11.15 Deleted Item Test - User 1 Deletes Folder

**Step 1:** User 1 deletes `ENG_FOLDER2_ID`

**Endpoint:** `DELETE /api/folders/{ENG_FOLDER2_ID}`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Step 2:** User 1 gets starred items

**Endpoint:** `GET /api/starred`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected:** Deleted folder should NOT appear in starred items list

**✅ Test:** Deleted items are automatically filtered from starred results

---

## 📊 Summary of Starred Feature Tests

### ✅ What Should Work:

1. **Star Own Items**
   - Users can star folders in their MyDrive
   - Admins can star folders in their departments
   - Users can star folders they have view access to (via ACL)

2. **Permission Validation**
   - Middleware validates view permission BEFORE starring
   - Cannot star items without view access
   - MyDrive isolation is enforced

3. **Get Starred Items**
   - Returns only items user still has access to
   - Automatically filters deleted items
   - Automatically filters items user lost access to
   - Sorted by `starredAt` (most recent first)
   - Includes `actions` object with proper permissions

4. **Bulk Operations**
   - Can bulk star multiple items at once
   - Partial success: stars accessible items, skips inaccessible
   - Returns detailed results: `updated`, `noPermission`, `notFound`, `deleted`

5. **Unstarring**
   - Users can unstar any previously starred item
   - Still requires view permission to unstar

### ❌ What Should Fail:

1. **No Access Scenarios**
   - Cannot star folders in non-assigned departments
   - Cannot star another user's MyDrive folders
   - Cannot star folders without view permission
   - Cannot unstar folders without view permission

2. **Permission Loss**
   - Starred items disappear when user loses access
   - Deleted items don't appear in starred list

---

## 🎯 Critical Test Scenarios

### Scenario 1: Cross-Department Starring
1. User 1 (Engineering) creates folder
2. User 1 stars the folder → ✅ Success
3. User 2 (Marketing) tries to star same folder → ❌ 403 Forbidden
4. User 1 shares folder with User 2 (view permission)
5. User 2 stars the folder → ✅ Success
6. User 1 removes sharing
7. User 2 gets starred items → Folder no longer appears

### Scenario 2: Bulk Operations with Mixed Access
1. User has access to Folders A, B, C
2. User does NOT have access to Folders D, E
3. User bulk stars: `[A, B, C, D, E]`
4. Result: A, B, C starred successfully
5. Result: D, E in `noPermission` array
6. Operation completes without throwing error

### Scenario 3: MyDrive Privacy
1. User 1 stars folder in their MyDrive
2. User 2 cannot star User 1's MyDrive folder
3. User 2 cannot see User 1's starred MyDrive items
4. Each user sees only their own starred items

---

## 🔍 Additional Test Cases

### Test Case 1: Star Non-Existent Item
**Request:**
```json
{
  "id": "000000000000000000000000",
  "type": "folder"
}
```
**Expected:** ❌ **404 Not Found** - "Folder not found"

---

### Test Case 2: Star Already Starred Item
**Expected:** Should update `starredAt` timestamp (upsert behavior)

---

### Test Case 3: Get Starred Items - Empty Result
**Context:** User 5 (no starred items)  
**Expected:** 
```json
{
  "success": true,
  "count": 0,
  "children": []
}
```

---

### Test Case 4: Invalid Item Type
**Request:**
```json
{
  "id": "675e1111111111111111111",
  "type": "invalid"
}
```
**Expected:** ❌ **400 Bad Request** - "Invalid item type"

---

## 📝 Testing Checklist

- [ ] Star folder in own MyDrive
- [ ] Star folder in assigned department
- [ ] Try star folder in non-assigned department (should fail)
- [ ] Try star another user's MyDrive folder (should fail)
- [ ] Star folder with ACL view access
- [ ] Try star folder without view access (should fail)
- [ ] Get starred items (returns accessible items only)
- [ ] Unstar previously starred folder
- [ ] Try unstar folder without access (should fail)
- [ ] Bulk star multiple folders
- [ ] Bulk star with mixed permissions (partial success)
- [ ] Bulk unstar folders
- [ ] Verify deleted items don't appear in starred list
- [ ] Verify items user lost access to don't appear
- [ ] Verify actions reflect actual user permissions
- [ ] Verify response format matches getChildFolders API

---




## 🚀 Quick Test Script

```bash
# Variables (replace with actual IDs)
USER1_TOKEN="your_token_here"
USER1_FOLDER1_ID="folder_id_here"
ENG_FOLDER1_ID="folder_id_here"

# Test 1: Star folder
curl -X POST http://localhost:5000/api/starred/add \
  -H "Authorization: Bearer $USER1_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"id":"'$USER1_FOLDER1_ID'","type":"folder"}'

# Test 2: Get starred items
curl -X GET http://localhost:5000/api/starred \
  -H "Authorization: Bearer $USER1_TOKEN"

# Test 3: Unstar folder
curl -X POST http://localhost:5000/api/starred/remove \
  -H "Authorization: Bearer $USER1_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"id":"'$USER1_FOLDER1_ID'","type":"folder"}'

# Test 4: Bulk star
curl -X POST http://localhost:5000/api/starred/bulk \
  -H "Authorization: Bearer $USER1_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"folderIds":["'$ENG_FOLDER1_ID'"],"fileIds":[],"starred":true}'
```

---

## 🎓 Key Learnings

1. **Starred is permission-aware**: Users can only star items they can view
2. **Dynamic filtering**: Starred list automatically adapts to current permissions
3. **Consistent UI**: Same response format as children API for easy frontend integration
4. **Bulk operations**: Handle partial success gracefully
5. **MyDrive isolation**: Enforced at every level including starring

## 📊 Summary of Test Results

### ✅ What Should Work:

1. **Super Admin**
   - Can access ALL departments (ORG and MyDrive)
   - Can create folders anywhere
   - Can view/manage all resources

2. **Department Admin/Owner**
   - Can access ONLY assigned departments
   - Can create folders in their departments
   - Can share folders with anyone
   - Can access their own MyDrive

3. **Regular Users**
   - Can access their own MyDrive ONLY
   - Cannot access ORG departments without ACL
   - Need explicit sharing to access folders
   - Can create folders where they have upload permission

4. **MyDrive Isolation**
   - Users can ONLY access their own MyDrive
   - Other users cannot see/access MyDrive folders
   - MyDrive departments do NOT appear in `/api/departments` list

5. **Department Isolation**
   - Users only see their assigned departments
   - Cannot access folders in non-assigned departments
   - Cross-department access requires explicit sharing (ACL)

### ❌ What Should Fail:

1. Regular user creating folder in department root (without ACL)
2. User accessing another user's MyDrive
3. User accessing non-assigned department folders
4. User with view permission trying to create folders
5. User without share permission trying to share folders
6. Admin from one department accessing another department

---

## 🎯 Critical Test Scenarios

### Scenario 1: MyDrive Privacy
- User 1 creates folder in MyDrive
- User 2 tries to access it → ❌ 403 Forbidden
- User 2 tries to create in User 1's MyDrive → ❌ 403 Forbidden

### Scenario 2: Department Isolation
- User 1 (Engineering) tries to view User 2 (Marketing) folders → ❌ 403 Forbidden
- User 1 tries to create in Marketing department → ❌ 403






# 📄 STEP 12: Test Document Upload & Management

> **Comprehensive testing for document upload, chunked upload, versioning, and document operations with proper access control validation**

---

## 📋 Prerequisites for Document Tests

Before starting, ensure you have:
- All users logged in with saved tokens
- Folders created in different departments and MyDrives
- User permissions already set up from previous steps
- Test files ready: small file (<100MB), large file (>100MB for chunked upload)

**Required Variables:**
```
USER1_TOKEN (Abhishek - Admin Engineering)
USER2_TOKEN (Ajay - Admin Marketing)
USER4_TOKEN (Arcade - User Engineering)
USER5_TOKEN (Shaxma - User No Dept)

USER1_MYDRIVE_ID
USER1_FOLDER1_ID (Personal Documents in MyDrive)
ENG_FOLDER1_ID (Backend Projects in Engineering)
MARKETING_FOLDER1_ID (Marketing Campaigns)

DOCUMENT1_ID (to be saved)
DOCUMENT2_ID (to be saved)
```

---

## 📤 12.1 Generate Presigned URLs for Upload

### 12.1.1 User 1 - Generate Upload URL for Single File

**Endpoint:** `POST /api/documents/generate-upload-urls`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "files": [
    {
      "filename": "project-proposal.pdf",
      "mimeType": "application/pdf"
    }
  ]
}
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "data": [
    {
      "filename": "project-proposal.pdf",
      "key": "uploads/1735123456789-0-project-proposal.pdf",
      "url": "https://your-bucket.s3.amazonaws.com/...",
      "mimeType": "application/pdf",
      "fileGroup": "Document",
      "category": "document"
    }
  ],
  "message": "Generated 1 presigned upload URL(s)"
}
```

**✅ Actions:**
- Copy the `url` to upload file directly to S3
- Save the `key` for document creation
- **Test:** URL should be valid for 5 minutes (300 seconds)

---

### 12.1.2 User 1 - Generate Multiple Upload URLs

**Request Body:**
```json
{
  "files": [
    {
      "filename": "design.png",
      "mimeType": "image/png"
    },
    {
      "filename": "budget.xlsx",
      "mimeType": "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
    },
    {
      "filename": "presentation.pptx",
      "mimeType": "application/vnd.openxmlformats-officedocument.presentationml.presentation"
    }
  ]
}
```

**Expected:** ✅ **200 OK** - Returns 3 presigned URLs with validation info

**✅ Test:** All files validated against allowed formats before URL generation

---

### 12.1.3 User 1 - Try Upload Blocked File Type

**Request Body:**
```json
{
  "files": [
    {
      "filename": "malicious.exe",
      "mimeType": "application/x-msdownload"
    }
  ]
}
```

**Expected Response (400 Bad Request):**
```json
{
  "success": false,
  "message": "Invalid files detected: [{\"filename\":\"malicious.exe\",\"reason\":\"File extension '.exe' is blocked for security reasons\",\"extension\":\".exe\",\"mimeType\":\"application/x-msdownload\"}]"
}
```

**✅ Test:** ❌ **400 Bad Request** - Blocked file types rejected

---

### 12.1.4 User 1 - Upload File with Mismatched MIME Type

**Request Body:**
```json
{
  "files": [
    {
      "filename": "document.pdf",
      "mimeType": "image/jpeg"
    }
  ]
}
```

**Expected:** ❌ **400 Bad Request** - MIME type and extension mismatch detected

**✅ Test:** Validation ensures file extension matches MIME type

---

## 📁 12.2 Create Document Records

### 12.2.1 User 1 - Upload Document to Their MyDrive

**Context:** After uploading file to S3 using presigned URL

**Endpoint:** `POST /api/documents`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "Project Proposal",
  "originalName": "project-proposal.pdf",
  "parentId": "PASTE_USER1_FOLDER1_ID_HERE",
  "fileUrl": "uploads/1735123456789-0-project-proposal.pdf",
  "mimeType": "application/pdf",
  "extension": "pdf",
  "size": 2048576,
  "description": "Q4 Project Proposal Document",
  "tags": ["proposal", "q4", "important"]
}
```

**Expected Response (201 Created):**
```json
{
  "success": true,
  "message": "Document uploaded successfully",
  "data": {
    "_id": "675e7777777777777777777",
    "name": "Project Proposal",
    "originalName": "project-proposal.pdf",
    "type": "document",
    "fileType": "document",
    "parentId": "675e6666666666666666666",
    "departmentId": "675e5555555555555555555",
    "path": "/MyDrive_675e4444444444444444444/Personal Documents/Project Proposal.pdf",
    "fileUrl": "uploads/1735123456789-0-project-proposal.pdf",
    "mimeType": "application/pdf",
    "extension": "pdf",
    "size": 2048576,
    "version": 1,
    "description": "Q4 Project Proposal Document",
    "tags": ["proposal", "q4", "important"],
    "createdBy": "675e4444444444444444444",
    "currentVersion": {
      "versionNumber": 1,
      "isLatest": true,
      "changeDescription": "Initial upload"
    },
    "fileValidation": {
      "group": "Document",
      "category": "document",
      "fileType": "document"
    }
  }
}
```

**✅ Actions:**
- Save `_id` as `DOCUMENT1_ID`
- **Test:** Document created with initial version automatically
- **Test:** `fileType` auto-detected as "document"
- **Test:** Path includes file extension

---

### 12.2.2 User 1 - Upload Document to Engineering Department

**Request Body:**
```json
{
  "name": "API Documentation",
  "originalName": "api-docs.pdf",
  "parentId": "PASTE_ENG_FOLDER1_ID_HERE",
  "fileUrl": "uploads/1735123456789-1-api-docs.pdf",
  "mimeType": "application/pdf",
  "extension": "pdf",
  "size": 1024000,
  "description": "Backend API Documentation",
  "tags": ["api", "documentation", "backend"]
}
```

**Expected:** ✅ **201 Created** - Admin can upload to their department

**✅ Action:** Save `_id` as `DOCUMENT2_ID`

---

### 12.2.3 User 2 - Try Upload to Engineering Folder (No Access)

**Endpoint:** `POST /api/documents`

**Headers:**
```
Authorization: Bearer USER2_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "Hacker Document",
  "originalName": "hack.pdf",
  "parentId": "PASTE_ENG_FOLDER1_ID_HERE",
  "fileUrl": "uploads/1735123456789-2-hack.pdf",
  "mimeType": "application/pdf",
  "extension": "pdf",
  "size": 500000
}
```

**Expected:** ❌ **403 Forbidden** - Cannot upload to non-assigned department

**✅ Test:** Department isolation enforced for document upload

---

### 12.2.4 User 4 - Try Upload to Engineering Root (No Upload Permission)

**Context:** User 4 is in Engineering dept but has no ACL permission

**Request Body:**
```json
{
  "name": "My Report",
  "originalName": "report.pdf",
  "parentId": "PASTE_ENG_FOLDER1_ID_HERE",
  "fileUrl": "uploads/1735123456789-3-report.pdf",
  "mimeType": "application/pdf",
  "extension": "pdf",
  "size": 300000
}
```

**Expected:** ❌ **403 Forbidden** - Regular user needs explicit upload permission

---

### 12.2.5 User 1 - Share Folder with User 4 (Upload Permission)

**Endpoint:** `POST /api/folders/{ENG_FOLDER1_ID}/share`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "users": [
    {
      "userId": "PASTE_USER4_ID_HERE",
      "permissions": ["view", "upload"]
    }
  ],
  "groups": []
}
```

**Expected:** ✅ **200 OK** - Folder shared with upload permission

---

### 12.2.6 User 4 - Now Upload to Shared Folder (Has Upload)

**Endpoint:** `POST /api/documents`

**Headers:**
```
Authorization: Bearer USER4_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "My Report",
  "originalName": "report.pdf",
  "parentId": "PASTE_ENG_FOLDER1_ID_HERE",
  "fileUrl": "uploads/1735123456789-3-report.pdf",
  "mimeType": "application/pdf",
  "extension": "pdf",
  "size": 300000,
  "tags": ["report", "engineering"]
}
```

**Expected:** ✅ **201 Created** - Can upload because has upload permission via ACL

**✅ Test:** ACL upload permission works correctly

---

### 12.2.7 User 1 - Upload Image File

**Request Body:**
```json
{
  "name": "System Architecture",
  "originalName": "architecture.png",
  "parentId": "PASTE_ENG_FOLDER1_ID_HERE",
  "fileUrl": "uploads/1735123456789-4-architecture.png",
  "mimeType": "image/png",
  "extension": "png",
  "size": 1500000,
  "description": "System architecture diagram",
  "tags": ["diagram", "architecture"]
}
```

**Expected Response:**
```json
{
  "success": true,
  "data": {
    "fileType": "image",
    "mimeType": "image/png",
    "extension": "png",
    "fileValidation": {
      "group": "Image",
      "category": "image",
      "fileType": "image"
    }
  }
}
```

**✅ Test:** `fileType` auto-detected as "image"

---

## 🔍 12.3 Get & View Documents

### 12.3.1 User 1 - Get Document by ID

**Endpoint:** `GET /api/documents/{DOCUMENT1_ID}`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "_id": "675e7777777777777777777",
    "name": "Project Proposal",
    "originalName": "project-proposal.pdf",
    "type": "document",
    "fileType": "document",
    "extension": "pdf",
    "size": 2048576,
    "sizeFormatted": "2 MB",
    "path": "/MyDrive_675e4444444444444444444/Personal Documents/Project Proposal.pdf",
    "createdBy": {
      "name": "Abhishek React",
      "email": "abhishekreact.dev@gmail.com"
    },
    "parent": {
      "_id": "675e6666666666666666666",
      "name": "Personal Documents",
      "type": "Folder"
    },
    "currentVersionId": {
      "versionNumber": 1,
      "isLatest": true
    }
  }
}
```

**✅ Test:** Document details include parent folder info and current version

---

### 12.3.2 User 2 - Try Get User 1's MyDrive Document

**Endpoint:** `GET /api/documents/{DOCUMENT1_ID}`

**Headers:**
```
Authorization: Bearer USER2_TOKEN
```

**Expected:** ❌ **403 Forbidden** - Cannot access another user's MyDrive documents

**✅ Test:** MyDrive isolation enforced for documents

---

### 12.3.3 User 4 - View Document in Shared Folder

**Endpoint:** `GET /api/documents/{DOCUMENT2_ID}`

**Headers:**
```
Authorization: Bearer USER4_TOKEN
```

**Expected:** ✅ **200 OK** - Can view because folder is shared with view permission

---

### 12.3.4 User 1 - Get Folder Children (With Documents)

**Endpoint:** `GET /api/folders/{USER1_FOLDER1_ID}/children`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected Response:**
```json
{
  "success": true,
  "data": {
    "folders": [],
    "files": [
      {
        "_id": "675e7777777777777777777",
        "name": "Project Proposal",
        "type": "file",
        "extension": "pdf",
        "fileType": "document",
        "size": 2048576,
        "sizeFormatted": "2 MB",
        "createdAt": "2024-12-28T10:00:00.000Z"
      }
    ],
    "actions": {
      "canView": true,
      "canUpload": true,
      "canDelete": true
    }
  }
}
```

**✅ Test:** Folder children API includes documents with proper metadata

---

## 🔄 12.4 Update Document Operations

### 12.4.1 User 1 - Rename Document

**Endpoint:** `PUT /api/documents/{DOCUMENT1_ID}`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "Q4 Project Proposal - Final"
}
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "message": "Document updated successfully",
  "data": {
    "_id": "675e7777777777777777777",
    "name": "Q4 Project Proposal - Final",
    "path": "/MyDrive_675e4444444444444444444/Personal Documents/Q4 Project Proposal - Final.pdf"
  }
}
```

**✅ Tests:**
- Document renamed successfully
- Path updated automatically
- Activity log created for FILE_RENAMED

---

### 12.4.2 User 1 - Update Description & Tags

**Request Body:**
```json
{
  "description": "Final version of Q4 project proposal submitted to management",
  "tags": ["proposal", "q4", "final", "approved"]
}
```

**Expected:** ✅ **200 OK** - Description and tags updated

---

### 12.4.3 User 4 - Try Rename Document in Shared Folder (No Edit Permission)

**Endpoint:** `PUT /api/documents/{DOCUMENT2_ID}`

**Headers:**
```
Authorization: Bearer USER4_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "Renamed API Docs"
}
```

**Expected:** ❌ **403 Forbidden** - User 4 only has view + upload, not edit permission

---

### 12.4.4 User 1 - Add Tags to Document

**Endpoint:** `POST /api/documents/{DOCUMENT1_ID}/tags`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "tags": ["urgent", "priority"]
}
```

**Expected:** ✅ **200 OK** - Tags added (merged with existing tags)

---

### 12.4.5 User 1 - Remove Tags from Document

**Endpoint:** `DELETE /api/documents/{DOCUMENT1_ID}/tags`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "tags": ["urgent"]
}
```

**Expected:** ✅ **200 OK** - Specified tags removed

---

## 🔽 12.5 Download Documents

### 12.5.1 User 1 - Generate Download URL

**Endpoint:** `GET /api/documents/{DOCUMENT1_ID}/download`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "url": "https://your-bucket.s3.amazonaws.com/uploads/...?X-Amz-Algorithm=...",
    "expiresIn": 3600,
    "filename": "project-proposal.pdf"
  }
}
```

**✅ Tests:**
- Presigned download URL generated
- URL valid for 1 hour (3600 seconds)
- Filename matches original document name

---

### 12.5.2 User 4 - Download Shared Document

**Endpoint:** `GET /api/documents/{DOCUMENT2_ID}/download`

**Headers:**
```
Authorization: Bearer USER4_TOKEN
```

**Expected:** ✅ **200 OK** - Can download if has view permission on parent folder

---

### 12.5.3 User 2 - Try Download Engineering Document (No Access)

**Endpoint:** `GET /api/documents/{DOCUMENT2_ID}/download`

**Headers:**
```
Authorization: Bearer USER2_TOKEN
```

**Expected:** ❌ **403 Forbidden** - Cannot download without folder access

---

## 📦 12.6 Move Documents

### 12.6.1 User 1 - Move Document to Another Folder

**Endpoint:** `POST /api/documents/{DOCUMENT1_ID}/move`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "newParentId": "PASTE_USER1_FOLDER2_ID_HERE"
}
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "message": "Document moved successfully",
  "data": {
    "_id": "675e7777777777777777777",
    "parentId": "675eUSER1_FOLDER2_ID",
    "path": "/MyDrive_675e4444444444444444444/Personal Documents/Tax Documents/Q4 Project Proposal - Final.pdf"
  }
}
```

**✅ Tests:**
- Document moved to new folder
- Path updated automatically
- Activity log created for FILE_MOVED

---

### 12.6.2 User 1 - Try Move Document to Marketing Folder (No Access)

**Request Body:**
```json
{
  "newParentId": "PASTE_MARKETING_FOLDER1_ID_HERE"
}
```

**Expected:** ❌ **403 Forbidden** - Cannot move to non-accessible folder

---

### 12.6.3 User 4 - Try Move Document in Shared Folder (No Delete Permission)

**Endpoint:** `POST /api/documents/{DOCUMENT2_ID}/move`

**Headers:**
```
Authorization: Bearer USER4_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "newParentId": "PASTE_ENG_FOLDER2_ID_HERE"
}
```

**Expected:** ❌ **403 Forbidden** - Moving requires delete permission on source folder

---

## 🗑️ 12.7 Delete & Restore Documents

### 12.7.1 User 1 - Soft Delete Document

**Endpoint:** `DELETE /api/documents/{DOCUMENT1_ID}`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "message": "Document moved to bin"
}
```

**✅ Tests:**
- Document soft deleted (isDeleted = true)
- deletedAt timestamp set
- Activity log created for FILE_DELETED

---

### 12.7.2 User 1 - Verify Deleted Document Not in Folder

**Endpoint:** `GET /api/folders/{USER1_FOLDER2_ID}/children`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected:** Document should NOT appear in files list

---

### 12.7.3 User 1 - Restore Deleted Document

**Endpoint:** `POST /api/documents/{DOCUMENT1_ID}/restore`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "message": "Document restored successfully",
  "data": {
    "_id": "675e7777777777777777777",
    "isDeleted": false,
    "deletedAt": null
  }
}
```

**✅ Tests:**
- Document restored (isDeleted = false)
- Activity log created for FILE_RESTORED
- Document appears back in folder

---

### 12.7.4 User 4 - Try Delete Document in Shared Folder (No Delete Permission)

**Endpoint:** `DELETE /api/documents/{DOCUMENT2_ID}`

**Headers:**
```
Authorization: Bearer USER4_TOKEN
```

**Expected:** ❌ **403 Forbidden** - Only has view + upload, not delete permission

---

## 🔄 12.8 Document Versioning

### 12.8.1 User 1 - Create New Version (Re-upload)

**Context:** First upload new file to S3, then create version

**Endpoint:** `POST /api/documents/{DOCUMENT1_ID}/versions`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "Q4 Project Proposal - Final",
  "originalName": "project-proposal-v2.pdf",
  "fileUrl": "uploads/1735123456789-5-project-proposal-v2.pdf",
  "mimeType": "application/pdf",
  "extension": "pdf",
  "size": 2150000,
  "changeDescription": "Updated budget section and timeline"
}
```

**Expected Response (201 Created):**
```json
{
  "success": true,
  "message": "New version created successfully",
  "data": {
    "_id": "675e8888888888888888888",
    "documentId": "675e7777777777777777777",
    "versionNumber": 2,
    "isLatest": true,
    "changeDescription": "Updated budget section and timeline",
    "size": 2150000,
    "createdBy": "675e4444444444444444444",
    "createdAt": "2024-12-28T11:00:00.000Z"
  }
}
```

**✅ Tests:**
- New version created with versionNumber = 2
- Previous version's isLatest set to false
- Document's version field updated to 2
- Activity log created for FILE_VERSION_UPLOADED

---

### 12.8.2 User 1 - Try Re-upload with Different File Type

**Request Body:**
```json
{
  "name": "Q4 Project Proposal - Final",
  "originalName": "project-proposal.docx",
  "fileUrl": "uploads/1735123456789-6-project-proposal.docx",
  "mimeType": "application/vnd.openxmlformats-officedocument.wordprocessingml.document",
  "extension": "docx",
  "size": 1800000,
  "changeDescription": "Converted to Word format"
}
```

**Expected Response (400 Bad Request):**
```json
{
  "success": false,
  "message": "File format mismatch. Current: Document, New: Document"
}
```

**✅ Test:** ❌ **400 Bad Request** - Cannot change file type in version (pdf → docx not allowed even if same category)

**Note:** Format validation ensures version consistency using constant groups

---

### 12.8.3 User 1 - Re-upload with Compatible Format (jpg → png)

**Context:** Upload an image document first

**Request Body:**
```json
{
  "name": "System Architecture",
  "originalName": "architecture-updated.png",
  "fileUrl": "uploads/1735123456789-7-architecture-updated.png",
  "mimeType": "image/png",
  "extension": "png",
  "size": 1600000,
  "changeDescription": "Updated with new microservices"
}
```

**Expected:** ✅ **201 Created** - Compatible formats within same group allowed (image/jpeg → image/png)

**✅ Test:** Format validation uses constant groups (jpg ↔ jpeg ↔ png allowed)

---

### 12.8.4 User 1 - Get All Versions

**Endpoint:** `GET /api/documents/{DOCUMENT1_ID}/versions`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "count": 2,
  "data": [
    {
      "_id": "675e8888888888888888888",
      "versionNumber": 2,
      "isLatest": true,
      "size": 2150000,
      "sizeFormatted": "2.05 MB",
      "changeDescription": "Updated budget section and timeline",
      "createdBy": {
        "username": "abhishek_react",
        "email": "abhishekreact.dev@gmail.com"
      },
      "createdAt": "2024-12-28T11:00:00.000Z",
      "createdAgo": "2 hours ago"
    },
    {
      "_id": "675e7777777777777777777",
      "versionNumber": 1,
      "isLatest": false,
      "size": 2048576,
      "sizeFormatted": "2 MB",
      "changeDescription": "Initial upload",
      "createdBy": {
        "username": "abhishek_react",
        "email": "abhishekreact.dev@gmail.com"
      },
      "createdAt": "2024-12-28T10:00:00.000Z",
      "createdAgo": "3 hours ago"
    }
  ]
}
```

**✅ Tests:**
- Versions sorted by versionNumber descending (latest first)
- isLatest flag shows current version
- Includes formatted size and relative time

---

### 12.8.5 User 1 - Download Specific Version

**Endpoint:** `GET /api/documents/{VERSION1_ID}/download`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected:** ✅ **200 OK** - Can download old versions

**✅ Test:** Download endpoint works for both documents and versions

---

### 12.8.6 User 1 - Revert to Previous Version

**Endpoint:** `POST /api/documents/{DOCUMENT1_ID}/versions/revert`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "versionNumber": 1
}
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "message": "Version 1 restored successfully",
  "data": {
    "_id": "675e9999999999999999999",
    "versionNumber": 3,
    "isLatest": true,
    "changeDescription": "Restored from version 1",
    "size": 2048576
  }
}
```

**✅ Tests:**
- Creates NEW version (versionNumber = 3) based on version 1
- Document updated to reflect reverted content
- Activity log created for FILE_VERSION_RESTORED

---

### 12.8.7 User 4 - Try Create Version in Shared Folder (No Edit Permission)

**Endpoint:** `POST /api/documents/{DOCUMENT2_ID}/versions`

**Headers:**
```
Authorization: Bearer USER4_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "fileUrl": "uploads/1735123456789-8-new-version.pdf",
  "mimeType": "application/pdf",
  "extension": "pdf",
  "size": 1000000,
  "changeDescription": "My update"
}
```

**Expected:** ❌ **403 Forbidden** - Requires edit permission to create versions

---

## 🔍 12.9 Search Documents

### 12.9.1 User 1 - Search Documents by Name

**Endpoint:** `GET /api/documents/search?q=proposal`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "count": 1,
  "data": [
    {
      "_id": "675e7777777777777777777",
      "name": "Q4 Project Proposal - Final",
      "extension": "pdf",
      "fileType": "document",
      "size": 2048576,
      "path": "/MyDrive_675e4444444444444444444/Personal Documents/Tax Documents/Q4 Project Proposal - Final.pdf",
      "createdBy": {
        "name": "Abhishek React",
        "email": "abhishekreact.dev@gmail.com"
      }
    }
  ]
}
```

**✅ Test:** Full-text search on name, description, and path

---

### 12.9.2 User 1 - Search with Department Filter

**Endpoint:** `GET /api/documents/search?q=api&departmentId={ENGINEERING_DEPT_ID}`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected:** Returns only documents from Engineering department

---

### 12.9.3 User 1 - Search with Limit

**Endpoint:** `GET /api/documents/search?q=document&limit=10`

**Expected:** Returns max 10 results

---

### 12.9.4 User 2 - Search Documents (No Cross-Department Results)

**Endpoint:** `GET /api/documents/search?q=api`

**Headers:**
```
Authorization: Bearer USER2_TOKEN
```

**Expected:** Should NOT see Engineering documents (no access)

**✅ Test:** Search respects department access control

---

## 🏷️ 12.10 Tag Operations

### 12.10.1 User 1 - Find Documents by Tags

**Endpoint:** `GET /api/documents/tags?tags=proposal,q4`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected:** Returns documents matching all specified tags

---

## 📊 12.11 Chunked Upload (Large Files >100MB)

### 12.11.1 User 1 - Check if File Should Use Chunked Upload

**Context:** For files larger than 100MB threshold

**File Size:** 150MB

**Decision:** Use chunked upload API

**✅ Test:** Files >100MB must use chunked upload flow

---

### 12.11.2 User 1 - Initiate Chunked Upload

**Endpoint:** `POST /api/documents/chunked/initiate`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "filename": "large-video.mp4",
  "mimeType": "video/mp4",
  "fileSize": 157286400,
  "parentId": "PASTE_USER1_FOLDER1_ID_HERE"
}
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "uploadId": "abc123xyz789multipartupload",
    "key": "documents/675e5555555555555555555/675e6666666666666666666/1735123456789-large-video.mp4",
    "chunkSize": 5242880,
    "totalParts": 30,
    "message": "Chunked upload initiated successfully"
  }
}
```

**✅ Actions:**
- Save `uploadId` as `UPLOAD_ID`
- Save `key` as `UPLOAD_KEY`
- Calculate chunk details: 5MB chunks, 30 total parts
- **Test:** Permission checked on parentId before initiating

---

### 12.11.3 User 1 - Generate Presigned URLs for Chunk Upload

**Endpoint:** `POST /api/documents/chunked/generate-upload-url`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "uploadId": "PASTE_UPLOAD_ID_HERE",
  "key": "PASTE_UPLOAD_KEY_HERE",
  "partNumber": 1
}
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "url": "https://your-bucket.s3.amazonaws.com/...?partNumber=1&uploadId=...",
    "partNumber": 1,
    "expiresIn": 3600,
    "message": "Presigned URL generated for chunk upload"
  }
}
```

**✅ Tests:**
- Presigned URL valid for 1 hour
- Client uploads chunk directly to S3 using this URL
- Repeat for each part (1 to 30)

---

### 12.11.4 User 1 - Complete Chunked Upload

**Context:** After all chunks uploaded to S3

**Endpoint:** `POST /api/documents/chunked/complete`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "uploadId": "PASTE_UPLOAD_ID_HERE",
  "key": "PASTE_UPLOAD_KEY_HERE",
  "parts": [
    {"ETag": "\"etag1\"", "PartNumber": 1},
    {"ETag": "\"etag2\"", "PartNumber": 2},
    {"ETag": "\"etag3\"", "PartNumber": 3}
  ],
  "name": "Training Video",
  "parentId": "PASTE_USER1_FOLDER1_ID_HERE",
  "mimeType": "video/mp4",
  "fileSize": 157286400,
  "description": "Product training video",
  "tags": ["training", "video", "product"]
}
```

**Expected Response (201 Created):**
```json
{
  "success": true,
  "data": {
    "document": {
      "_id": "675eAAAAAAAAAAAAAAAAAAA",
      "name": "Training Video",
      "originalName": "large-video.mp4",
      "type": "document",
      "fileType": "video",
      "extension": "mp4",
      "size": 157286400,
      "version": 1,
      "fileUrl": "documents/.../large-video.mp4"
    },
    "message": "Chunked upload completed and document created successfully"
  }
}
```

**✅ Tests:**
- All parts assembled by S3
- Document record created automatically
- `fileType` detected as "video"
- Initial version created

---

### 12.11.5 User 1 - Abort Chunked Upload (Cancel)

**Context:** User cancels upload midway

**Endpoint:** `POST /api/documents/chunked/abort`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "uploadId": "PASTE_UPLOAD_ID_HERE",
  "key": "PASTE_UPLOAD_KEY_HERE"
}
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "message": "Chunked upload aborted and cleaned up successfully"
}
```

**✅ Test:** Partial uploads cleaned up from S3

---

### 12.11.6 User 1 - List Uploaded Parts (Resume Functionality)

**Endpoint:** `GET /api/documents/chunked/parts?uploadId={UPLOAD_ID}&key={UPLOAD_KEY}`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "parts": [
      {
        "PartNumber": 1,
        "ETag": "\"etag1\"",
        "Size": 5242880,
        "LastModified": "2024-12-28T10:15:00.000Z"
      },
      {
        "PartNumber": 2,
        "ETag": "\"etag2\"",
        "Size": 5242880,
        "LastModified": "2024-12-28T10:16:00.000Z"
      }
    ],
    "totalParts": 2,
    "uploadId": "abc123xyz789multipartupload",
    "key": "documents/.../large-video.mp4"
  }
}
```

**✅ Test:** Can resume interrupted uploads by checking which parts already uploaded

---

### 12.11.7 User 2 - Try Initiate Chunked Upload to Engineering Folder (No Access)

**Endpoint:** `POST /api/documents/chunked/initiate`

**Headers:**
```
Authorization: Bearer USER2_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "filename": "big-file.mp4",
  "mimeType": "video/mp4",
  "fileSize": 157286400,
  "parentId": "PASTE_ENG_FOLDER1_ID_HERE"
}
```

**Expected:** ❌ **403 Forbidden** - Permission checked before initiating upload

---

### 12.11.8 User 1 - Try Chunked Upload with Small File (<100MB)

**Request Body:**
```json
{
  "filename": "small-video.mp4",
  "mimeType": "video/mp4",
  "fileSize": 50000000,
  "parentId": "PASTE_USER1_FOLDER1_ID_HERE"
}
```

**Expected Response (400 Bad Request):**
```json
{
  "success": false,
  "message": "File size (50000000 bytes) is too small for chunked upload. Use regular upload instead."
}
```

**✅ Test:** ❌ **400 Bad Request** - Chunked upload only for files >100MB

---

## 📈 12.12 Document Statistics & Analytics

### 12.12.1 User 1 - Get Recent Documents in Department

**Endpoint:** `GET /api/documents/recent/{ENGINEERING_DEPT_ID}?limit=10`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "count": 3,
  "data": [
    {
      "_id": "675eAAAAAAAAAAAAAAAAAAA",
      "name": "Training Video",
      "extension": "mp4",
      "fileType": "video",
      "size": 157286400,
      "createdAt": "2024-12-28T12:00:00.000Z",
      "createdBy": {
        "name": "Abhishek React",
        "email": "abhishekreact.dev@gmail.com"
      }
    }
  ]
}
```

**✅ Test:** Returns most recent documents (sorted by createdAt DESC)

---

### 12.12.2 Super Admin - Get Department Statistics

**Endpoint:** `GET /api/departments/{ENGINEERING_DEPT_ID}/stats`

**Headers:**
```
Authorization: Bearer SUPER_ADMIN_TOKEN
```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "totalFolders": 5,
    "totalDocuments": 12,
    "totalSize": 524288000,
    "documentsByType": {
      "document": 8,
      "image": 3,
      "video": 1
    },
    "storageUsed": "500 MB",
    "lastActivity": "2024-12-28T12:00:00.000Z"
  }
}
```

**✅ Test:** Department statistics calculated correctly

---

## 🔐 12.13 Access Control Edge Cases

### 12.13.1 User 1 - Upload to Deleted Folder (Should Fail)

**Context:** Folder was soft deleted but ID still exists

**Endpoint:** `POST /api/documents`

**Request Body:**
```json
{
  "name": "Test Document",
  "parentId": "PASTE_DELETED_FOLDER_ID_HERE",
  "fileUrl": "uploads/test.pdf",
  "mimeType": "application/pdf",
  "extension": "pdf",
  "size": 100000
}
```

**Expected:** ❌ **404 Not Found** - Cannot upload to deleted folder

---

### 12.13.2 User 4 - Access Document After Permission Revoked

**Context:** User 1 removes User 4's folder access

**Step 1:** User 1 removes sharing

**Endpoint:** `POST /api/folders/{ENG_FOLDER1_ID}/share`

**Request Body:**
```json
{
  "users": [],
  "groups": []
}
```

**Step 2:** User 4 tries to view document

**Endpoint:** `GET /api/documents/{DOCUMENT2_ID}`

**Headers:**
```
Authorization: Bearer USER4_TOKEN
```

**Expected:** ❌ **403 Forbidden** - Permission revoked, access denied

**✅ Test:** Permission changes take effect immediately

---

### 12.13.3 User 1 - Share MyDrive Document with User 5

**Context:** Can share individual documents from MyDrive

**Endpoint:** `POST /api/folders/{USER1_FOLDER1_ID}/share`

**Headers:**
```
Authorization: Bearer USER1_TOKEN
Content-Type: application/json
```

**Request Body:**
```json
{
  "users": [
    {
      "userId": "PASTE_USER5_ID_HERE",
      "permissions": ["view", "download"]
    }
  ],
  "groups": []
}
```

**Step 2:** User 5 accesses document

**Endpoint:** `GET /api/documents/{DOCUMENT1_ID}`

**Headers:**
```
Authorization: Bearer USER5_TOKEN
```

**Expected:** ✅ **200 OK** - Cross-user sharing works via ACL

---

## 📋 12.14 Testing Checklist

### Upload & Creation
- [ ] Generate presigned URLs for single file
- [ ] Generate presigned URLs for multiple files
- [ ] Upload blocked file type (should fail)
- [ ] Upload with mismatched MIME type (should fail)
- [ ] Create document in MyDrive
- [ ] Create document in department folder
- [ ] Try create in non-assigned department (should fail)
- [ ] Regular user upload without permission (should fail)
- [ ] Regular user upload with ACL permission (should work)
- [ ] Auto-detect fileType (document, image, video, etc.)

### View & Access
- [ ] Get document by ID
- [ ] Try access another user's MyDrive document (should fail)
- [ ] View document in shared folder
- [ ] Get folder children with documents
- [ ] Download document with presigned URL
- [ ] Download shared document
- [ ] Try download without access (should fail)

### Update Operations
- [ ] Rename document
- [ ] Update description and tags
- [ ] Try rename without edit permission (should fail)
- [ ] Add tags to document
- [ ] Remove tags from document
- [ ] Move document to another folder
- [ ] Try move to inaccessible folder (should fail)

### Delete & Restore
- [ ] Soft delete document
- [ ] Verify deleted document not in folder
- [ ] Restore deleted document
- [ ] Try delete without permission (should fail)

### Versioning
- [ ] Create new version (re-upload)
- [ ] Try re-upload with incompatible format (should fail)
- [ ] Re-upload with compatible format (jpg→png)
- [ ] Get all versions sorted correctly
- [ ] Download specific version
- [ ] Revert to previous version
- [ ] Try create version without edit permission (should fail)

### Chunked Upload (>100MB)
- [ ] Initiate chunked upload
- [ ] Generate presigned URLs for chunks
- [ ] Complete chunked upload
- [ ] Abort chunked upload
- [ ] List uploaded parts (resume)
- [ ] Try chunked upload without permission (should fail)
- [ ] Try chunked upload with small file (should fail)

### Search & Tags
- [ ] Search documents by name
- [ ] Search with department filter
- [ ] Search with limit
- [ ] Search respects access control
- [ ] Find documents by tags

### Statistics
- [ ] Get recent documents in department
- [ ] Get department statistics

### Access Control Edge Cases
- [ ] Upload to deleted folder (should fail)
- [ ] Access after permission revoked (should fail)
- [ ] Share MyDrive document with other user
- [ ] Permission changes immediate effect

---

## 🎯 Critical Test Scenarios

### Scenario 1: Complete Document Lifecycle
1. User 1 generates presigned URL
2. Upload file to S3
3. Create document record → Version 1
4. Update document name and tags
5. Upload new version → Version 2
6. Revert to version 1 → Version 3
7. Move to different folder
8. Soft delete
9. Restore
10. Download latest version

### Scenario 2: Large File Chunked Upload
1. User 1 initiates chunked upload (150MB video)
2. Calculate chunks: 30 parts × 5MB
3. Generate presigned URL for part 1
4. Upload part 1 to S3
5. Repeat for all 30 parts
6. Complete multipart upload
7. Document created automatically with proper metadata

### Scenario 3: Permission-Based Access
1. User 4 (regular user) cannot access Engineering folder
2. User 1 shares folder with view + upload permissions
3. User 4 can now view and upload documents
4. User 4 cannot edit/delete (no permission)
5. User 1 removes sharing
6. User 4 loses access immediately

### Scenario 4: Cross-Department Document Sharing
1. User 1 (Engineering) creates document
2. User 2 (Marketing) cannot access it
3. User 1 shares parent folder with User 2
4. User 2 can now view and download
5. User 2 cannot edit (cross-department restriction)

### Scenario 5: MyDrive Privacy
1. User 1 uploads personal document to MyDrive
2. User 2, User 3, User 4 cannot access it
3. Super Admin can access (system-wide access)
4. User 1 can share with specific users via ACL
5. Shared users can access only with granted permissions

---

## 🚀 Quick Test Script

```bash
# Variables (replace with actual values)
USER1_TOKEN="your_token_here"
USER1_FOLDER1_ID="folder_id_here"
DOCUMENT1_ID="document_id_here"

# Test 1: Generate presigned URL
curl -X POST http://localhost:5000/api/documents/generate-upload-urls \
  -H "Authorization: Bearer $USER1_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"files":[{"filename":"test.pdf","mimeType":"application/pdf"}]}'

# Test 2: Create document
curl -X POST http://localhost:5000/api/documents \
  -H "Authorization: Bearer $USER1_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name":"Test Document",
    "originalName":"test.pdf",
    "parentId":"'$USER1_FOLDER1_ID'",
    "fileUrl":"uploads/test.pdf",
    "mimeType":"application/pdf",
    "extension":"pdf",
    "size":100000
  }'

# Test 3: Get document
curl -X GET http://localhost:5000/api/documents/$DOCUMENT1_ID \
  -H "Authorization: Bearer $USER1_TOKEN"

# Test 4: Rename document
curl -X PUT http://localhost:5000/api/documents/$DOCUMENT1_ID \
  -H "Authorization: Bearer $USER1_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"name":"Renamed Document"}'

# Test 5: Download document
curl -X GET http://localhost:5000/api/documents/$DOCUMENT1_ID/download \
  -H "Authorization: Bearer $USER1_TOKEN"

# Test 6: Delete document
curl -X DELETE http://localhost:5000/api/documents/$DOCUMENT1_ID \
  -H "Authorization: Bearer $USER1_TOKEN"

# Test 7: Restore document
curl -X POST http://localhost:5000/api/documents/$DOCUMENT1_ID/restore \
  -H "Authorization: Bearer $USER1_TOKEN"

# Test 8: Search documents
curl -X GET "http://localhost:5000/api/documents/search?q=test" \
  -H "Authorization: Bearer $USER1_TOKEN"
```

---

## 🎓 Key Learnings

1. **File Validation is Multi-Layered**
   - Extension validation
   - MIME type validation
   - Extension-MIME compatibility check
   - Blocked file type filtering

2. **Chunked Upload for Large Files**
   - Files >100MB must use multipart upload
   - Resume capability via part listing
   - Clean abort functionality

3. **Versioning Preserves History**
   - All versions retained
   - Revert creates new version (non-destructive)
   - Format consistency enforced across versions

4. **Permission Inheritance**
   - Documents inherit folder permissions
   - Can override with document-level ACL
   - Permission changes immediate

5. **Activity Logging**
   - Every document operation logged
   - Version operations tracked
   - Audit trail maintained

---

## 📊 Summary of Expected Results

### ✅ What Should Work:

1. **File Upload**
   - Presigned URL generation
   - Document creation in accessible folders
   - File type auto-detection
   - MyDrive and department uploads

2. **Chunked Upload**
   - Large file uploads (>100MB)
   - Resume interrupted uploads
   - Clean abort/cleanup

3. **Document Operations**
   - View, download, rename, update
   - Move between accessible folders
   - Soft delete and restore
   - Tag management

4. **Versioning**
   - Create new versions
   - View version history
   - Revert to old versions
   - Download specific versions

5. **Access Control**
   - MyDrive isolation
   - Department isolation
   - ACL-based sharing
   - Permission validation

### ❌ What Should Fail:

1. **No Access Scenarios**
   - Upload to non-assigned department
   - Access another user's MyDrive documents
   - Operations without required permissions
   - Blocked file types

2. **Invalid Operations**
   - Chunked upload for small files
   - Mismatched file formats in versions
   - Upload to deleted folders
   - Extension-MIME mismatch

---

## 🏁 Final Notes

- **Always test with multiple users** to verify access control
- **Test both small and large files** to cover regular and chunked uploads
- **Verify activity logs** after each operation
- **Check permission inheritance** from folders to documents
- **Validate file type detection** for different formats
- **Test edge cases** like deleted folders, revoked permissions

This completes the comprehensive document upload and management testing guide! 🎉
