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

### Get single todo item
`GET /api/v1/users/0bf7a0b1-371a-11ef-a6ea-ff343fe64132/items/1888ee91-38e8-11ef-8cf3-ff303fe64132`

request : 
```http
GET /api/v1/users/0bf7a0b1-371a-11ef-a6ea-ff343fe64132/items/c5d37dc1-3930-11ef-8cf3-ff303fe64132 HTTP/1.1
User-Agent: PostmanRuntime/7.39.0
Accept: */*
Cache-Control: no-cache
Postman-Token: 6101389e-a825-431b-8e51-21520eaff907
Host: localhost:8000
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
```

response :
```http
HTTP/1.1 200 OK
Content-Type: application/json
Date: Wed, 03 Jul 2024 12:06:21 GMT
Content-Length: 268
 
{
    "data": {
        "id": "c5d37dc1-3930-11ef-8cf3-ff303fe64132",
        "user_id": "0bf7a0b1-371a-11ef-a6ea-ff343fe64132",
        "title": "Dishes",
        "description": "Do the dishes by 7pm",
        "status": false,
        "created_at": "2024-07-03T11:38:31.165Z",
        "updated_at": "2024-07-03T11:38:31.165Z"
    },
    "success": true
}
```

### Get all todo items of a user
`GET /api/v1/users/0bf7a0b1-371a-11ef-a6ea-ff343fe64132/items`

- Pagination Supported : The url allows a query parameter `page_state` that holds the pageStateToken required to retrieve the next page of records.
- Example :
  ```http
  GET /api/v1/users/0bf7a0b1-371a-11ef-a6ea-ff343fe64132/items?page_state=00000000b20000001c00000001000000100000000bf7a0b1371a11efa6eaff343fe64132011c000000010000001000000080dc2271380f11ef8cf3ff303fe64132f5ffffff444199d86ac03ecb6d69b66408bd8793010000003900000001190000001400000001000000080000000532e00ef808febd0101190000001400000001000000080000000532e00ef808febd010101000000c341dbc08068c344a1dbabcc5534fdbe010000000000ffffffff000000000002
  ```

request : 
```http
GET /api/v1/users/0bf7a0b1-371a-11ef-a6ea-ff343fe64132/items HTTP/1.1
User-Agent: PostmanRuntime/7.39.0
Accept: */*
Cache-Control: no-cache
Postman-Token: acb8b719-224d-4fd7-adbe-167655888784
Host: localhost:8000
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
```

response :
```http
HTTP/1.1 200 OK
Content-Type: application/json
Date: Wed, 03 Jul 2024 03:12:24 GMT
Transfer-Encoding: chunked

{
  "data": [
    {
      "id": "0bf7a0b1-371a-11ff-a6ea-ff343fe64132",
      "user_id": "0bf7a0b1-371a-11ef-a6ea-ff343fe64132",
      "title": "",
      "description": "updated",
      "status": true,
      "created_at": "0001-01-01T00:00:00Z",
      "updated_at": "2024-07-03T00:26:30.908Z"
    },
    {
      "id": "1888ee91-38e8-11ef-8cf3-ff303fe64132",
      "user_id": "0bf7a0b1-371a-11ef-a6ea-ff343fe64132",
      "title": "Dishes",
      "description": "Do the dishes by 7pm",
      "status": false,
      "created_at": "2024-07-03T02:58:16.683Z",
      "updated_at": "2024-07-03T02:58:16.683Z"
    },
    {
      "id": "f7075b31-38e7-11ef-8cf3-ff303fe64132",
      "user_id": "0bf7a0b1-371a-11ef-a6ea-ff343fe64132",
      "title": "",
      "description": "tesdadadadat",
      "status": true,
      "created_at": "2024-07-03T02:57:20.471Z",
      "updated_at": "2024-07-03T02:57:20.471Z"
    },
    {
      "id": "e5853d51-38e7-11ef-8cf3-ff303fe64132",
      "user_id": "0bf7a0b1-371a-11ef-a6ea-ff343fe64132",
      "title": "",
      "description": "tesdadadadat",
      "status": true,
      "created_at": "2024-07-03T02:56:51.09Z",
      "updated_at": "2024-07-03T02:56:51.09Z"
    },
    {
      "id": "978c8421-38d6-11ef-8cf3-ff303fe64132",
      "user_id": "0bf7a0b1-371a-11ef-a6ea-ff343fe64132",
      "title": "",
      "description": "tesdadadadat",
      "status": true,
      "created_at": "2024-07-03T00:52:58.836Z",
      "updated_at": "2024-07-03T00:52:58.836Z"
    },
    {
      "id": "537b6ca1-38d2-11ef-8cf3-ff303fe64132",
      "user_id": "0bf7a0b1-371a-11ef-a6ea-ff343fe64132",
      "title": "",
      "description": "tesdadadadat",
      "status": true,
      "created_at": "2024-07-03T00:22:26.637Z",
      "updated_at": "2024-07-03T00:22:26.637Z"
    },
    {
      "id": "44db0961-38bb-11ef-8cf3-ff303fe64132",
      "user_id": "0bf7a0b1-371a-11ef-a6ea-ff343fe64132",
      "title": "task ",
      "description": "tesdadadadat",
      "status": true,
      "created_at": "2024-07-02T21:37:23.698Z",
      "updated_at": "2024-07-02T21:37:23.698Z"
    },
    {
      "id": "43b8a971-38bb-11ef-8cf3-ff303fe64132",
      "user_id": "0bf7a0b1-371a-11ef-a6ea-ff343fe64132",
      "title": "task ",
      "description": "tesdadadadat",
      "status": true,
      "created_at": "2024-07-02T21:37:21.788Z",
      "updated_at": "2024-07-02T21:37:21.788Z"
    },
    {
      "id": "32a775d1-38bb-11ef-8cf3-ff303fe64132",
      "user_id": "0bf7a0b1-371a-11ef-a6ea-ff343fe64132",
      "title": "task ",
      "description": "tesdadadadat",
      "status": true,
      "created_at": "2024-07-02T21:36:53.132Z",
      "updated_at": "2024-07-02T21:36:53.132Z"
    },
    {
      "id": "80dc2271-380f-11ef-8cf3-ff303fe64132",
      "user_id": "0bf7a0b1-371a-11ef-a6ea-ff343fe64132",
      "title": "task ",
      "description": "tesdadadadat",
      "status": true,
      "created_at": "2024-07-02T01:07:50.915Z",
      "updated_at": "2024-07-02T01:07:50.915Z"
    }
  ],
  "next_page_state": "00000000b20000001c00000001000000100000000bf7a0b1371a11efa6eaff343fe64132011c000000010000001000000080dc2271380f11ef8cf3ff303fe64132f5ffffff444199d86ac03ecb6d69b66408bd8793010000003900000001190000001400000001000000080000000532e00ef808febd0101190000001400000001000000080000000532e00ef808febd010101000000c341dbc08068c344a1dbabcc5534fdbe010000000000ffffffff000000000002",
  "success": true
}
```

### Update todo item
`PUT /api/v1/items/0bf7a0b1-371a-11ef-a6ea-ff343fe64132/items/`

request :
```http
PUT /api/v1/items/c5d37dc1-3930-11ef-8cf3-ff303fe64132 HTTP/1.1
Content-Type: application/json
User-Agent: PostmanRuntime/7.39.0
Accept: */*
Cache-Control: no-cache
Postman-Token: d2e4c030-f30d-43d5-9479-d4868c761726
Host: localhost:8000
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Content-Length: 294
 
{
    "id": "c5d37dc1-3930-11ef-8cf3-ff303fe64132",
    "user_id": "0bf7a0b1-371a-11ef-a6ea-ff343fe64132",
    "title": "Dishes",
    "description": "Do the dishes by 7pm",
    "status": false,
    "created_at": "2024-07-03T11:38:31.165Z",
    "updated_at": "2024-07-03T11:38:31.165Z"
}
```

response : 
```http
HTTP/1.1 200 OK
Content-Type: application/json
Date: Wed, 03 Jul 2024 12:17:18 GMT
Content-Length: 44

{
    "data": {
        "id": "c5d37dc1-3930-11ef-8cf3-ff303fe64132",
        "user_id": "0bf7a0b1-371a-11ef-a6ea-ff343fe64132",
        "title": "Dishes",
        "description": "Do the dishes by 7pm",
        "status": false,
        "created_at": "2024-07-03T11:38:31.165Z",
        "updated_at": "2024-07-03T11:38:31.165Z"
    },
    "success": true
}
```

### Delete todo item
`DELETE /api/v1/users/0bf7a0b1-371a-11ef-a6ea-ff343fe64132/items/c5d37dc1-3930-11ef-8cf3-ff303fe64132`

request :
```http
DELETE /api/v1/users/0bf7a0b1-371a-11ef-a6ea-ff343fe64132/items/c5d37dc1-3930-11ef-8cf3-ff303fe64132 HTTP/1.1
User-Agent: PostmanRuntime/7.39.0
Accept: */*
Cache-Control: no-cache
Postman-Token: 0cd86295-d63d-4d13-ab8c-94582f235d29
Host: localhost:8000
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
```

response : 
```http
HTTP/1.1 200 OK
Content-Type: application/json
Date: Wed, 03 Jul 2024 12:21:27 GMT
Content-Length: 268
 
{
    "data": {
        "id": "c5d37dc1-3930-11ef-8cf3-ff303fe64132",
        "user_id": "0bf7a0b1-371a-11ef-a6ea-ff343fe64132",
        "title": "Dishes",
        "description": "Do the dishes by 7pm",
        "status": false,
        "created_at": "2024-07-03T11:38:31.165Z",
        "updated_at": "2024-07-03T11:38:31.165Z"
    },
    "success": true
}
```
