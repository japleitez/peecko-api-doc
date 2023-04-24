# PEECKO-API
Backend application to serve Peecko Android and iOS apps

## Software requirements
- Java JDK 17 (for run the backend)

## Run the API from the command line
- set JAVA_HOME environment variable to the Java JDK 17 installation path then run the command:  
$ java -jar peecko-api.jar

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
"password": "12345678",
"language": "en"
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
- Wrong Input Response (e.g. empty name)
```
HTTP Status Code 422 (UNPROCESSABLE_ENTITY)
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
HTTP Status code 401 [UNAUTHORIZED]
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

- Successful Membership Activation Response
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
    "message": "License is not valid, please ask your Employer to provide you a valid one"
}
```


### Get Today Videos
-----
```
[GET] /api/videos/today
```

[ ] **Response**

- Today Videos Response
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
            "image": "https://i.ytimg.com/vi/hH_KVVgV_Vo/hqdefault.jpg",
            "url": "https://www.youtube.com/embed/hH_KVVgV_Vo",
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


### Get Favorite Videos
-----
```
[GET] /api/videos/favorites
```

[ ] **Response**

- Today Videos Response  
The response is similar to the payload of the **Get Today Videos** service


### Get Library Videos
-----
```
[GET] /api/videos/categories
```
This service provides the list of available categories along with a short list of videos for each category.

[ ] **Response**

- Library Videos Response
```json
{
    "greeting": "All our video content under one roof, organized into wellness & fitness categories",
    "categories": [
        {
            "code": "C1",
            "title": "Health Risks",
            "videos": [ <video-11>, <video-12>, <video-13>, <video-14> ]
        },
        {
            "code": "C2",
            "title": "Yoga",
            "videos": [ <video-21>, <video-22>, <video-23>, <video-24> ]
        },
        {
            "code": "C3",
            "title": "Pilates",
            "videos": [ <video-31>, <video-32>, <video-33>, <video-34> ]
        },
        {
            "code": "C4",
            "title": "Calisthenics",
            "videos": [ <video-41, <video-42> ]
        }
    ]
}
```

**Notes** 
- Every category contains at least 1 video and maximum 4 videos
- Videos use the same json format in all services, see example in the **Get Today Videos**'s response


### Get Library Videos
-----
```
[GET] /api/videos/categories/:id
```
The purpose of this service is to list all the videos of a specific category,   
The list of videos is sorted by published date in descendant order

[ ] **Response**

- Category Found Response

```json
{
   "code": "C1",
   "title": "Health Risks",
   "videos": [ <video-1>, <video-2>, <video-3>, <video-4>, <video-5>, <video-6>, ... <video-N> ]
}
```

- Category Not Found Response
```
HTTP Status Code 404 [NOT_FOUND]
```


### Get Notifications
-----
```
[GET] /api/account/notifications
```
This services provides the list of the latest notifications sorted by release date in descent order,  
Some notifications include a video

[ ] **Response**

- Notifications Response
```json
[
    {
        "title": "Weloner Marathon Luxembourg ",
        "message": "The Weloner Marathon is an annual marathon by night in Luxembourg which was held for the first time in 2006",
        "image": "http://path/filename.jpg",
        "video": "http://path/filename.jpg",
        "date": "07 Dec 2023"
    },
    {
        "title": "Meet Matheo, New Health Coach, We are boarding!",
        "message": "Matheo is a Wellness Coach Certified, Matheo is specialized in Healing from chronic health issues and Healing Nutrition",
        "image": "http://path/filename.jpg",
        "video": null,
        "date": "05 Dec 2023"
    }
]
```

