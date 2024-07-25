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
| `POST`    | `/api/v1/register/`                             |register new user                     |
| `POST`    | `/api/v1/login/`                             |get refresh and access token                      |
| `POST`    | `/api/v1/token/refresh/`                          | refresh the access token after it has expired                      |
| `GET`    | `/api/v1/contacts/<id>`                          | get contact with id                  |
| `GET`    | `/api/v1/contacts/`                          | get all contacts of logged in user            |
| `PUT`    | `/api/v1/contacts/<id>`                          | update a contact of the logged in user           |
| `POST`    | `/api/v1/contacts/`                          | create new contact                       |
| `DELETE`    | `/api/v1/contacts/`                          | delete contact                      |
| `POST`    | `/api/v1/report/`                          | spam report a number                     |
| `GET`    | `/api/v1/search/`                          | endpoint used for searching                    |

## User Auth

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

to logout we blacklist the refresh token. So that it cannot be reused in the future.

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

## Contacts

### Create new user
`POST /api/v1/contacts/`

request : 
```http
POST /api/v1/contacts/ HTTP/1.1
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNzIxOTIyODExLCJpYXQiOjE3MjE5MjI1MTEsImp0aSI6IjFjOTgxYTk1NjI2YzQ0OGZhMWQ2MmVmZGVmOGJhZWI1IiwidXNlcl9pZCI6Nn0.DesrC-mM6TWS0SRql5gMSwiF9ldWQQ3u-r--VNalbWE
User-Agent: PostmanRuntime/7.39.0
Accept: */*
Cache-Control: no-cache
Postman-Token: 81416079-9a09-4398-ad4b-e5e0d31fc18a
Host: localhost:8000
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Content-Length: 66
 
{
    "full_name" : "Aman Kumar",
    "phone_number" : 9645863214
}
```

response :

`Notice that the user field is automatically filled with 6?`
This user id is extracted from the token in Authorization Header.

This ensures that a user cannot create a contact for someone else. Ensuring Integrity. 

```http
HTTP/1.1 200 OK
Date: Thu, 25 Jul 2024 15:49:03 GMT
Server: WSGIServer/0.2 CPython/3.9.9
Content-Type: application/json
Vary: Accept
Allow: GET, POST, PUT, DELETE, HEAD, OPTIONS
X-Frame-Options: DENY
Content-Length: 84 
X-Content-Type-Options: nosniff
Referrer-Policy: same-origin
Cross-Origin-Opener-Policy: same-origin
 
{
    "id": 11,
    "full_name": "Aman Kumar",
    "phone_number": "9645863214",
    "email": null,
    "user": 6
}
```

### Delete User
`DELETE /api/v1/contacts/1`

request : 
```http
DELETE /api/v1/contacts/2 HTTP/1.1
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNzIxOTI1NTM5LCJpYXQiOjE3MjE5MjUyMzksImp0aSI6ImUyYmM1NDFlMzM1ZTQxZTFhMThjMDVkMjE5YzBmMWU3IiwidXNlcl9pZCI6Nn0.Den-RkDA82J1EOHKzkoA9eyXR76NGu16NccoJScEfJ8
User-Agent: PostmanRuntime/7.39.0
Accept: */*
Cache-Control: no-cache
Postman-Token: 1298bb77-9fa2-40c9-b5e7-3c9af3f8b13f
Host: localhost:8000
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Content-Length: 97
```

response :

`Trying to delete contact saved by some other user`
This user id is extracted from the token in Authorization Header.

This ensures that a user cannot delete a contact created by someone else. 

```http
HTTP/1.1 403 Forbidden
Date: Thu, 25 Jul 2024 16:36:26 GMT
Server: WSGIServer/0.2 CPython/3.9.9
Content-Type: application/json
Vary: Accept
Allow: GET, POST, PUT, DELETE, HEAD, OPTIONS
X-Frame-Options: DENY
Content-Length: 31
X-Content-Type-Options: nosniff
Referrer-Policy: same-origin
Cross-Origin-Opener-Policy: same-origin
 
{
    "detail": "Action not allowed"
}
```

### Invalid token in Authorization Header
`POST /api/v1/contacts/`

request : 
```http
POST /api/v1/contacts/ HTTP/1.1
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNzIxOTI1NTM5LCJpYXQiOjE3MjE5MjUyMzksImp0aSI6ImUyYmM1NDFlMzM1ZTQxZTFhMThjMDVkMjE5YzBmMWU3IiwidXNlcl9pZCI6Nn0.Den-RkDA82J1EOHKzkoA9eyXR76NGu16NccoJScEfJ8
User-Agent: PostmanRuntime/7.39.0
Accept: */*
Cache-Control: no-cache
Postman-Token: 648cc7cb-a250-4c57-86f2-c7192b7379c7
Host: localhost:8000
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Content-Length: 67
 
{
    "full_name" : "Tanmay Bhat",
    "phone_number" : 9825466548
}
```

response :


```http
HTTP/1.1 401 Unauthorized
Date: Thu, 25 Jul 2024 16:42:47 GMT
Server: WSGIServer/0.2 CPython/3.9.9
Content-Type: application/json
WWW-Authenticate: Bearer realm="api"
Vary: Accept
Allow: GET, POST, PUT, DELETE, HEAD, OPTIONS
X-Frame-Options: DENY
Content-Length: 183
X-Content-Type-Options: nosniff
Referrer-Policy: same-origin
Cross-Origin-Opener-Policy: same-origin
 
{
    "detail": "Given token not valid for any token type",
    "code": "token_not_valid",
    "messages": [
        {
            "token_class": "AccessToken",
            "token_type": "access",
            "message": "Token is invalid or expired"
        }
    ]
}
```

## Spam Report

### Spam report a number
`POST /api/v1/report/`

request : 
```http
POST /api/v1/report/ HTTP/1.1
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNzIxOTI2NTQ1LCJpYXQiOjE3MjE5MjYyNDUsImp0aSI6Ijk1NDg5YzM5NjUzMTQwMjY4ZmYwODVkZWFlMzM1M2JkIiwidXNlcl9pZCI6Nn0.NnMLd2zzq-5DZaXS5qLIMU_CoNM2nLq0jFM2L9Yc-WU
User-Agent: PostmanRuntime/7.39.0
Accept: */*
Cache-Control: no-cache
Postman-Token: 5cfec79a-e437-42f0-88c9-7ee912b0c59e
Host: localhost:8000
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Content-Length: 35
 
{
    "phone_number" : 9934569871
}
```
response :
```http
HTTP/1.1 200 OK
Date: Thu, 25 Jul 2024 16:51:07 GMT
Server: WSGIServer/0.2 CPython/3.9.9
Content-Type: application/json
Vary: Accept
Allow: GET, POST, DELETE, HEAD, OPTIONS
X-Frame-Options: DENY
Content-Length: 45
X-Content-Type-Options: nosniff
Referrer-Policy: same-origin
Cross-Origin-Opener-Policy: same-origin
 
{
    "phone_number": "9934569871",
    "reported_by": 6
}
```

## Search

### Search by name
`GET /api/v1/search?full_name=chirag`

request : 
```http
GET /api/v1/search?full_name=chirag HTTP/1.1
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNzIxOTMzNzcyLCJpYXQiOjE3MjE5MzM0NzIsImp0aSI6IjQ0ZjkyM2ViZTM4NTQ0ZWQ4MWVhYzRkMDYyOWY3MmE4IiwidXNlcl9pZCI6Nn0.YxbaL_kc5dTC1xWmY3oHJHbLokf5q8TNryRBarbSy9E
User-Agent: PostmanRuntime/7.39.0
Accept: */*
Cache-Control: no-cache
Postman-Token: b149f07c-b1a1-454d-a17d-ed46a944d162
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Referer: http://localhost:8000/api/v1/search?full_name=chirag
Host: localhost:8000
```
response :
```http
HTTP/1.1 200 OK
Date: Thu, 25 Jul 2024 18:51:33 GMT
Server: WSGIServer/0.2 CPython/3.9.9
Content-Type: application/json
Vary: Accept
Allow: GET, HEAD, OPTIONS
X-Frame-Options: DENY
Content-Length: 183
X-Content-Type-Options: nosniff
Referrer-Policy: same-origin
Cross-Origin-Opener-Policy: same-origin
 
[
    {
        "full_name": "Chirag Bolakani",
        "phone_number": "816941962",
        "email": "cheragbolakani4@gmail.com"
    },
    {
        "full_name": "Chirag Bolakani",
        "phone_number": "3214589763",
        "email": "chirag@gmail.com"
    }
]
```

### Search by number
`GET /api/v1/search?phone_number=96`

request : 
```http
GET /api/v1/search?phone_number=96 HTTP/1.1
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNzIxOTM3MTE4LCJpYXQiOjE3MjE5MzY4MTgsImp0aSI6IjVhNGE4YWJkNGExMDQzZWJhZjZhMWY4NzlmYmVjNWM3IiwidXNlcl9pZCI6Nn0.9iSpouIJQIEE55bgIgueXjPzT8bQQqxQ0dVJwAQrAxQ
User-Agent: PostmanRuntime/7.39.0
Accept: */*
Cache-Control: no-cache
Postman-Token: 9413b181-43e8-4dc6-b5bb-6c703fe5d832
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Referer: http://localhost:8000/api/v1/search?phone_number=96
Host: localhost:8000
```
response :
```http
HTTP/1.1 200 OK
Date: Thu, 25 Jul 2024 19:48:55 GMT
Server: WSGIServer/0.2 CPython/3.9.9
Content-Type: application/json
Vary: Accept
Allow: GET, HEAD, OPTIONS
X-Frame-Options: DENY
Content-Length: 129
X-Content-Type-Options: nosniff
Referrer-Policy: same-origin
Cross-Origin-Opener-Policy: same-origin
 
{
    "Contacts": [
        {
            "full_name": "9645863214",
            "phone_number": null,
            "email": "Aman Kumar"
        }
    ],
    "spam reports": [
        {
            "phone_number": "9645866489"
        }
    ]
}
```
