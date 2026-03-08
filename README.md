# Flutter + FastAPI Register System

A simple **fullstack register system** built using:

- **Flutter** (Frontend Web/Mobile)
- **FastAPI** (Backend REST API)
- **SQLite** (Database)

This project demonstrates how a Flutter application communicates with a Python backend and stores user data in a database.

---

# System Architecture

```text
Flutter App (Frontend)
        │
        │ HTTP POST /register
        ▼
FastAPI Backend (REST API)
        │
        │ INSERT
        ▼
SQLite Database (users.db)
```

---

# Project Structure

```text
register_system
│
├── backend
│   ├── main.py
│   ├── users.db
│   ├── cek.py
│   └── requirements.txt
│
└── flutter_app
    │
    ├── lib
    │   └── main.dart
    │
    ├── pubspec.yaml
    └── web
```

---

# Features

- User registration form
- REST API backend
- SQLite database storage
- Flutter web interface
- JSON API communication
- CORS enabled for browser requests

---

# Requirements

Make sure the following tools are installed:

- Python 3.10+
- Flutter SDK
- pip
- Chrome browser (for Flutter Web)

---

# Backend Setup (FastAPI)

## Navigate to backend folder

```bash
cd backend
```

---

## Install dependencies

```bash
pip install fastapi uvicorn
```

Optional:

```bash
pip install sqlalchemy
```

---

# Backend Code

Create a file named **main.py**

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel
import sqlite3

app = FastAPI()

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

conn = sqlite3.connect("users.db", check_same_thread=False)
cursor = conn.cursor()

cursor.execute("""
CREATE TABLE IF NOT EXISTS users(
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT,
    email TEXT,
    password TEXT
)
""")

class User(BaseModel):
    username: str
    email: str
    password: str

@app.post("/register")
def register(user: User):

    cursor.execute(
        "INSERT INTO users(username,email,password) VALUES(?,?,?)",
        (user.username, user.email, user.password)
    )

    conn.commit()

    return {"message": "User registered successfully"}
```

---

# Run Backend Server

```bash
uvicorn main:app --reload
```

Server will run at:

```
http://127.0.0.1:8000
```

---

# FastAPI API Documentation

Open:

```
http://127.0.0.1:8000/docs
```

---

# Flutter Setup

## Navigate to Flutter project

```bash
cd flutter_app
```

---

## Install dependencies

```bash
flutter pub get
```

Ensure `pubspec.yaml` contains:

```yaml
dependencies:
  flutter:
    sdk: flutter

  cupertino_icons: ^1.0.8
  http: ^1.2.0
```

---

# Flutter Frontend Code

Replace **lib/main.dart** with:

```dart
import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;
import 'dart:convert';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: RegisterPage(),
      debugShowCheckedModeBanner: false,
    );
  }
}

class RegisterPage extends StatefulWidget {
  @override
  _RegisterPageState createState() => _RegisterPageState();
}

class _RegisterPageState extends State<RegisterPage> {

  TextEditingController username = TextEditingController();
  TextEditingController email = TextEditingController();
  TextEditingController password = TextEditingController();

  Future registerUser() async {

    var url = Uri.parse("http://127.0.0.1:8000/register");

    var response = await http.post(
      url,
      headers: {"Content-Type": "application/json"},
      body: jsonEncode({
        "username": username.text,
        "email": email.text,
        "password": password.text
      }),
    );

    if(response.statusCode == 200){
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text("Register Success"))
      );
    }

  }

  @override
  Widget build(BuildContext context) {

    return Scaffold(

      appBar: AppBar(title: Text("Register")),

      body: Center(

        child: Container(

          width: 350,

          child: Padding(

            padding: EdgeInsets.all(20),

            child: Column(

              mainAxisSize: MainAxisSize.min,

              children: [

                Text(
                  "Register User",
                  style: TextStyle(
                    fontSize: 24,
                    fontWeight: FontWeight.bold
                  ),
                ),

                SizedBox(height: 20),

                TextField(
                  controller: username,
                  decoration: InputDecoration(
                    labelText: "Username",
                    border: OutlineInputBorder()
                  ),
                ),

                SizedBox(height: 15),

                TextField(
                  controller: email,
                  decoration: InputDecoration(
                    labelText: "Email",
                    border: OutlineInputBorder()
                  ),
                ),

                SizedBox(height: 15),

                TextField(
                  controller: password,
                  obscureText: true,
                  decoration: InputDecoration(
                    labelText: "Password",
                    border: OutlineInputBorder()
                  ),
                ),

                SizedBox(height: 20),

                SizedBox(
                  width: double.infinity,
                  child: ElevatedButton(
                    onPressed: registerUser,
                    child: Text("Register"),
                  ),
                )

              ],
            ),
          ),
        ),
      ),
    );
  }
}
```

---

# Run Flutter Web

```bash
flutter run -d chrome
```

---

# Testing the System

Fill the form:

```
username
email
password
```

Click **Register**

---

# Check Database

Run:

```bash
python cek.py
```

Example output:

```
[(1, 'username', 'email', 'password')]
```

This confirms the data has been stored in SQLite.

---

# API Example

## Request

```
POST /register
```

```json
{
 "username":"user1",
 "email":"user@email.com",
 "password":"123456"
}
```

## Response

```json
{
 "message": "User registered successfully"
}
```

---

# Technologies Used

| Technology | Purpose |
|------------|--------|
| Flutter | Frontend UI |
| FastAPI | Backend API |
| SQLite | Database |
| HTTP | Communication |
| JSON | Data format |

---

# Future Improvements

- Login API
- JWT authentication
- Password hashing
- PostgreSQL database
- Docker deployment
- Nginx reverse proxy
- User dashboard
- API authentication middleware

---

# License

This project is open-source and intended for learning purposes.
