# Spam Detector API
Backend for an application enabling users to register, add contacts and spam report numbers.

Some features of the app:
- Leverages class-based views
- Endpoints secured using Json Web Tokens (JWT)
- searching using Name and Mobile numbers
- Modular code. 
- Utilised Serializers
- Extends AbstractUser Model to implememt our own custom model.

## Design Explanation

The structure of the project encourages modularity and leverages packages in Go.
The idea is to ensure Idempotency.

```bash
│   instructions.txt                #instructions to install the project
│   manage.py                
│   requirements.txt                #packages required to run the project
│
├───api
│   │   admin.py
│   │   apps.py
│   │   models.py                   # models for our app
│   │   Serializers.py              # serializers used in our app
│   │   tests.py
│   │   urls.py                     # url endpoints
│   │   __init__.py
│   │
│   ├───fixtures    
│   │       data.json               # load data from this file
│   │       dummy_data.json
│   │
│   └───views                       # directory containing all the views
│           auth.py
│           contacts.py
│           search.py
│           spam_report.py
│
├───contacts
│       admin.py
│       apps.py
│       models.py
│       tests.py
│       views.py
│       __init__.py
│
└───spamDetector
        asgi.py
        settings.py                # settings for our django project
        urls.py
        wsgi.py
        __init__.py
```

## Getting Started

To get a local copy up and running follow these simple example steps.

### Installation

The instructions are also mentioned in instructions.txt file

1. Create Virtual Env and activate it
   ```sh
   python -m venv spamVenv
   cd spamVenv
   cd spamVenv/Scripts
   activate
   ```
2. Move the SPAMDETECTOR Project inside VENV
3. Clone this repository
   ```sh
   git clone https://github.com/ChiragBolakani/GoTodo.git
   ```
4. install packages from requirements.txt
   ```sh
   pip install -r requirements.txt
   ```
   NOTE : Make sure to enable the venv before installing.
   
5. Migrations
   
   use the below command to make migrations:
   ```sh
    python manage.py makemigrations api
   ```

   use the below command to show migrations:
   ```sh
    python manage.py showmigrations
   ```

   use the below command to migrate:
   ```sh
    python manage.py migrate
   ```
6. Load Data into db
   ```sh
   python manage.py loaddata ./api/fixtures/data.json
   ```
7. Run app
   ```sh
   python manage.py runserver <port>
   ```

## API Documentation
The API provides the following routes:
| Method   | URL                                      | Description                              |
| -------- | ---------------------------------------- | ---------------------------------------- |
| `POST`    | `/api/v1/register`                             |register new user                     |
| `POST`    | `/api/v1/login`                             |get refresh and access token                      |
| `POST`    | `/api/v1/token/refresh/`                          | refresh the access token after it has expired                      |
| `GET`    | `/api/v1/contacts/<id>`                          | get contact with id                  |
| `GET`    | `/api/v1/contacts/`                          | get all contacts of logged in user            |
| `PUT`    | `/api/v1/contacts/`                          | update a contact of the logged in user           |
| `POST`    | `/api/v1/contacts/`                          | create new contact                       |
| `DELETE`    | `/api/v1/contacts/`                          | delete contact                      |
| `GET`    | `/api/v1/report/`                          | get spam report using id                      |
| `POST`    | `/api/v1/report/`                          | spam report a number                     |
| `DELETE`    | `/api/v1/report/`                          | delete spam report                    |
| `GET`    | `/api/v1/search/`                          | endpoint used for searching                    |

## Usage
### Register user
`POST /api/v1/register`

request : 
```http
POST /api/v1/register/ HTTP/1.1
Content-Type: application/json
User-Agent: PostmanRuntime/7.39.0
Accept: */*
Cache-Control: no-cache
Postman-Token: 7cfa4c97-0b86-4bd3-862e-fa1d113d9d17
Host: localhost:8000
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Content-Length: 168
 
{
    "email": "chirag@gmail.com",
    "full_name": "Chirag Bolakani",
    "phone_number": 3214589763,
    "password": "chirag786",
    "password2": "chirag786"
}
```
response :
```http
HTTP/1.1 200 OK
Date: Thu, 25 Jul 2024 14:57:38 GMT
Server: WSGIServer/0.2 CPython/3.9.9
Content-Type: application/json
Vary: Accept
Allow: OPTIONS, POST
X-Frame-Options: DENY
Content-Length: 86
X-Content-Type-Options: nosniff
Referrer-Policy: same-origin
Cross-Origin-Opener-Policy: same-origin
 
{
    "full_name": "Chirag Bolakani",
    "email": "chirag@gmail.com",
    "phone_number": "3214589763"
}
```

### Login User
`POST /api/v1/login/`

request : 
```http
POST /api/v1/login/ HTTP/1.1
Content-Type: application/json
User-Agent: PostmanRuntime/7.39.0
Accept: */*
Cache-Control: no-cache
Postman-Token: 0e370c79-fc57-4682-8d1c-3e6bede70d97
Host: localhost:8000
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Content-Length: 66
 
{
    "phone_number": 3214589763,
    "password": "chirag786"
}
```

response :
```http
HTTP/1.1 200 OK
Date: Thu, 25 Jul 2024 15:06:47 GMT
Server: WSGIServer/0.2 CPython/3.9.9
Content-Type: application/json
Vary: Accept
Allow: POST, OPTIONS
X-Frame-Options: DENY
Content-Length: 576
X-Content-Type-Options: nosniff
Referrer-Policy: same-origin
Cross-Origin-Opener-Policy: same-origin
 
{
    "refresh": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ0b2tlbl90eXBlIjoicmVmcmVzaCIsImV4cCI6MTcyMjAwNjQwNywiaWF0IjoxNzIxOTIwMDA3LCJqdGkiOiI0NTA4NGI1Yzc5NTY0NmQwOGYzNzU5MmM2ZGYwMmUzMiIsInVzZXJfaWQiOjZ9.vHrJb3jXlJmMzHOT5g_IEy4mqf_m72VhHl7F_JtJ9GY",
    "access": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNzIxOTIwMzA3LCJpYXQiOjE3MjE5MjAwMDcsImp0aSI6ImVjMWQ2ODAyNTg3ZTRhMjJiZjUxOTc2YzY1NmU4Nzc4IiwidXNlcl9pZCI6Nn0.U9DVUaeuy6t8AO-IlMUJOHaYQH92jiUlc1p_zSHI0O0",
    "refresh_expires": 1722006407,
    "access_expires": 1721920307,
    "username": "3214589763",
    "user_id": 6
}
```

### get new access token using after expiry using refresh token
`POST /api/v1/token/refresh/`

request : 
```http
POST /api/v1/token/refresh/ HTTP/1.1
Content-Type: application/json
User-Agent: PostmanRuntime/7.39.0
Accept: */*
Cache-Control: no-cache
Postman-Token: 05a1a025-2fc6-4242-8baa-43f8162699ea
Host: localhost:8000
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Content-Length: 251
 
{
   "refresh": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ0b2tlbl90eXBlIjoicmVmcmVzaCIsImV4cCI6MTcyMjAwNjQwNywiaWF0IjoxNzIxOTIwMDA3LCJqdGkiOiI0NTA4NGI1Yzc5NTY0NmQwOGYzNzU5MmM2ZGYwMmUzMiIsInVzZXJfaWQiOjZ9.vHrJb3jXlJmMzHOT5g_IEy4mqf_m72VhHl7F_JtJ9GY"
}
```

response :
```http
HTTP/1.1 200 OK
Date: Thu, 25 Jul 2024 15:10:53 GMT
Server: WSGIServer/0.2 CPython/3.9.9
Content-Type: application/json
Vary: Accept
Allow: POST, OPTIONS
X-Frame-Options: DENY
Content-Length: 316
X-Content-Type-Options: nosniff
Referrer-Policy: same-origin
Cross-Origin-Opener-Policy: same-origin
 
{
    "access": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNzIxOTIwNTUzLCJpYXQiOjE3MjE5MjAwMDcsImp0aSI6IjdjZTYxMTU4ZTkwOTQ4OTRhNjIxMmM3ZGI3MWI5OGQ3IiwidXNlcl9pZCI6Nn0.Nc3cML6KfWlpxlxMauS0ZetXXmN-1zCnDvnnGBDLdEc",
    "access_expires": "2024-07-25T20:45:53",
    "username": "3214589763",
    "user_id": 6
}
```

### Login failure example
`POST /api/v1/login/`

request : 
```http
POST /api/v1/login/ HTTP/1.1
Content-Type: application/json
User-Agent: PostmanRuntime/7.39.0
Accept: */*
Cache-Control: no-cache
Postman-Token: cab57a2a-d019-4aef-8491-11a31f3165b3
Host: localhost:8000
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Content-Length: 66
 
{
    "phone_number": 3214589763,
    "password": "chirag706"
}
```

response :
```http
HTTP/1.1 401 Unauthorized
Date: Thu, 25 Jul 2024 15:16:18 GMT
Server: WSGIServer/0.2 CPython/3.9.9
Content-Type: application/json
WWW-Authenticate: Bearer realm="api"
Vary: Accept
Allow: POST, OPTIONS
X-Frame-Options: DENY
Content-Length: 63
X-Content-Type-Options: nosniff
Referrer-Policy: same-origin
Cross-Origin-Opener-Policy: same-origin
 
{
    "detail": "No active account found with the given credentials"
}
```

### Logout (blacklist refresh token)
`POST /api/v1/logout/`

request : 
```http
POST /api/v1/logout/ HTTP/1.1
Content-Type: application/json
User-Agent: PostmanRuntime/7.39.0
Accept: */*
Cache-Control: no-cache
Postman-Token: 90212874-1b91-4642-8ac5-412be46db246
Host: localhost:8000
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Content-Length: 252
 
{
    "refresh": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ0b2tlbl90eXBlIjoicmVmcmVzaCIsImV4cCI6MTcyMjAwNzk3NSwiaWF0IjoxNzIxOTIxNTc1LCJqdGkiOiI0YTg2ODlhZDM4MDE0NDc4YWI0ZGQ1NTE3ZWYwYzg4MyIsInVzZXJfaWQiOjZ9.QszPWCpJ-sXir1vMA8nUsZfwaRTTTVEmnUn9IQOL0UY"
}
```

response :
```http
HTTP/1.1 200 OK
Date: Thu, 25 Jul 2024 15:33:38 GMT
Server: WSGIServer/0.2 CPython/3.9.9
Content-Type: application/json
Vary: Accept
Allow: POST, OPTIONS
X-Frame-Options: DENY
Content-Length: 2
X-Content-Type-Options: nosniff
Referrer-Policy: same-origin
Cross-Origin-Opener-Policy: same-origin
 
{}
```

