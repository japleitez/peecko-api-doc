# PEECKO-API
Backend application to serve Peecko Android and iOS apps

## About the Peecko REST API
This documentation describes how to use the Peecko REST API using Postman.
When you make a request to the Peecko REST API, you will specify the HTTP method and a path
You might also specify request headers, query or body data parameters in JSON format
The API will return the response status code, response headers, and a response body

## REST API

### Create new user

```
/api/auth/signup
```
Signup Body
```json
{
"name": "Jose Pleitez",
"username": "jose.pleitez@gmail.com",
"password": "12345678"
}
```
Syntax
- `name` - The full name of the user
- `username` - The email address of the user
- `password` - The password of the user

Signup Responses
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

### Login

-----

```
/api/auth/signin
```
Login Body
```json
{
"username": "jose.pleitez@gmail.com",
"password": "12345678"
}
```
Login Responses
- Successful Login Response
```json
{
"token": "eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJqb3NlLnBsZWl0ZXpAZ21haWwuY29tIiwiaWF0IjoxNjgxODIyODEyLCJleHAiOjE2ODE5MDkyMTJ9.prusaGXL276v7ZEsrA5oP5mqOenusQ4vnZW-0qDNMJ_L3ZJ2Vhc5nV43NE_X13rkFfhEIP1ShVkgOjWKbY2Flg",
"name": "Jose Pleitez",
"username": "jose.pleitez@gmail.com",
"roles": [ "USER"]
}
```
- Invalid Login Response
```
HTTP Status code 401
```
