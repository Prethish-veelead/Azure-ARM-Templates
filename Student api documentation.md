# 📘 Student Info API — Documentation

> **Project:** Local Azure Function (HTTP Trigger)
> **Runtime:** Python 3.x + Azure Functions V2
> **Base URL:** `http://localhost:7071/api`
> **Auth Level:** Anonymous (no API key required)
> **Generated as part of:** Day 9 Practice — Task 18

---

## 📌 Overview

The **Student Info API** is a lightweight REST API built with Azure Functions (V2 model). It serves student records as JSON responses based on query parameters. The function runs locally using Azure Functions Core Tools and requires no Azure account or deployment to use.

---

## 🔗 Endpoints

### 1. Get All Students

Returns a list of all students in the database.

**Request**
```
GET /api/students
```

**Parameters**
None required.

**Example Request**
```
http://localhost:7071/api/students
```

**Example Response** `200 OK`
```json
{
  "students": [
    { "id": "1", "name": "Arun Kumar",  "course": "Cloud Computing", "grade": "A", "status": "Active" },
    { "id": "2", "name": "Priya Sharma","course": "Data Science",    "grade": "B", "status": "Active" },
    { "id": "3", "name": "Rahul Verma", "course": "Cybersecurity",   "grade": "A", "status": "Inactive" }
  ],
  "total": 3
}
```

---

### 2. Get Student by ID

Returns a single student record matching the given ID.

**Request**
```
GET /api/students?id={student_id}
```

**Query Parameters**

| Parameter | Type   | Required | Description                        |
|-----------|--------|----------|------------------------------------|
| `id`      | string | Yes      | The unique ID of the student (1–3) |

**Example Request**
```
http://localhost:7071/api/students?id=1
```

**Example Response** `200 OK`
```json
{
  "id": "1",
  "name": "Arun Kumar",
  "course": "Cloud Computing",
  "grade": "A",
  "status": "Active"
}
```

---

### 3. Student Not Found

Returned when no student matches the given ID.

**Example Request**
```
http://localhost:7071/api/students?id=99
```

**Example Response** `404 Not Found`
```json
{
  "error": "Student with id '99' not found"
}
```

---

### 4. POST — Get Student by ID (Request Body)

You can also pass the student ID in the request body instead of the URL.

**Request**
```
POST /api/students
Content-Type: application/json
```

**Request Body**
```json
{
  "id": "2"
}
```

**Example Response** `200 OK`
```json
{
  "id": "2",
  "name": "Priya Sharma",
  "course": "Data Science",
  "grade": "B",
  "status": "Active"
}
```

---

## 📊 Response Fields

| Field    | Type   | Description                          |
|----------|--------|--------------------------------------|
| `id`     | string | Unique student identifier            |
| `name`   | string | Full name of the student             |
| `course` | string | Enrolled course name                 |
| `grade`  | string | Current grade (A, B, C, etc.)        |
| `status` | string | Enrollment status (Active/Inactive)  |

---

## ✅ HTTP Status Codes

| Code  | Meaning                          |
|-------|----------------------------------|
| `200` | Success — data returned          |
| `404` | Student ID not found             |
| `500` | Internal server error            |

---

## 🧪 How to Test

### Browser (GET requests)
Open any of these URLs directly in your browser:
```
http://localhost:7071/api/students
http://localhost:7071/api/students?id=1
http://localhost:7071/api/students?id=2
http://localhost:7071/api/students?id=3
http://localhost:7071/api/students?id=99
```

### PowerShell (POST request)
```powershell
Invoke-RestMethod -Uri "http://localhost:7071/api/students" `
  -Method POST `
  -ContentType "application/json" `
  -Body '{"id": "2"}'
```

### curl (GET + POST)
```bash
# GET all students
curl http://localhost:7071/api/students

# GET by ID
curl http://localhost:7071/api/students?id=1

# POST with body
curl -X POST http://localhost:7071/api/students \
  -H "Content-Type: application/json" \
  -d '{"id": "2"}'
```

---

## 🗂️ Project Structure

```
my-azure-function/
├── function_app.py          ← Main function code (V2 model)
├── host.json                ← Azure Functions host configuration
├── local.settings.json      ← Local environment settings
├── requirements.txt         ← Python dependencies
└── .vscode/                 ← VS Code settings
```

---

## ⚙️ How It Works (Flow)

```
User sends HTTP request
        │
        ▼
Azure Functions runtime receives request
        │
        ▼
Checks for 'id' in query params
        │
        ├── No ID → Return ALL students (200)
        │
        ├── ID found in database → Return that student (200)
        │
        └── ID not in database → Return error (404)
```

---

## 🚀 How to Run Locally

```powershell
# Step 1 — Navigate to project folder
cd Documents/my-azure-function

# Step 2 — Start the function
func start

# Step 3 — Open browser and test
# http://localhost:7071/api/students
```

---

## 📦 Dependencies

```txt
azure-functions
```

Install via:
```powershell
pip install -r requirements.txt
```

---

> 📝 **Note:** This API runs locally for development and learning purposes.
> To deploy to Azure, use `func azure functionapp publish <app-name>` (requires Azure account).

---
*Generated using Claude Desktop — Day 9 Practice, Task 18*
*Internship Project — Phase 2: Azure Functions*
