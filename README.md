# PEECKO-API
Backend application to serve Peecko Android and iOS apps

## About the Peecko REST API
This documentation describes how to use the Peecko REST API using Postman. 
When you make a request to the Peecko REST API, you will specify the HTTP method and a path. 
You might also specify request headers, query or body parameters in JSON format. 
The API will return the response status code, response headers, and a response body. 

## Authenticating
The Sign-In service authenticates the user and returns a token if the user provides valid credentials (username and password). 
This token has to be sent in all subsequent calls to the API as part of the HTTP headers:  

**{Authorization, Bearer + space + token}**

## Authenticating in Postman

Open Peecko API Collection
1. execute Sign-Up
2. execute Sign-In and copy the token from the response
3. In the left bar, select Peecko API collection
4. In the central panel, select Authorization tab
   - Select Bearer Token from the Type list
   - Paste the copied value into the Token field
   - Save the changes (click Save button at the top-right corner of the Peecko Collection Panel)

**From now on the calls to the API will inherit the authentication defined for the collection**


## REST API

### SignUp

-----

```
[POST] /api/auth/signup
```
[ ] **Body**
```json
{
"name": "Jose Pleitez",
"username": "jose.pleitez@gmail.com",
"password": "12345678"
}
```

[ ] **Responses**

- Successful Signup Response
```json
{
"type": "OK",
"message": "User registered successfully!"
}
```
- Duplicate User Response
```json
{
  "type": "ERROR",
  "message": "Error: Username is already registered!"
}
```

### SignIn

-----

```
[POST] /api/auth/signin
```
[ ] **Body**
```json
{
"username": "jose.pleitez@gmail.com",
"password": "12345678"
}
```

[ ] **Responses**

- Successful SignIn Response
```json
{
"token": "eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJqb3NlLnBsZWl0ZXpAZ21haWwuY29tIiwiaWF0IjoxNjgxODIyODEyLCJleHAiOjE2ODE5MDkyMTJ9.prusaGXL276v7ZEsrA5oP5mqOenusQ4vnZW-0qDNMJ_L3ZJ2Vhc5nV43NE_X13rkFfhEIP1ShVkgOjWKbY2Flg",
"name": "Jose Pleitez",
"username": "jose.pleitez@gmail.com",
"roles": ["USER"]
}
```
- Wrong Credentials Response
```
HTTP Status code 401
```

### Membership Activation

-----

```
[PUT] /api/membership/activate
```
[ ] **Body**
```json
{
  "license": "LU999999999999999999",
  "device": "334340280EA00B10"
}
```

[ ] **Responses**

- Successful Membership Response
```json
{
  "type": "OK",
  "message": "License activated successfully!"
}
```
- Wrong Membership Response
```json
{
    "type": "ERROR",
    "message": "License is not valid"
}
```

### Get Today Videos

-----
```
[GET] /api/today/videos
```

[ ] **Response**

```json
{
    "greeting": "Here is your weekly dose of Wellness support. Check back next week for more updates",
    "tags": [
        "all",
        "energy",
        "endurance",
        "relax",
        "learn"
    ],
    "videos": [
        {
            "code": "V0010",
            "category": "YOGA",
            "title": "Yoga Leg Stretching",
            "duration": "12",
            "coach": "Julia Hernandez",
            "image": "IMAGE",
            "url": "URL",
            "audience": "Women",
            "intensity": "Beginner, Intermediate",
            "tags": [
                "energy",
                "endurance"
            ],
            "description": "This is a quick routine that is focused mainly on hips and hamstrings",
            "resume": "She is a LA-based yoga trainer who specialises in yoga therapeutics for scoliosis, streess and anxiety"
        },
    ]
}
```
