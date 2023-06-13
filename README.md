# PEECKO-API
Backend application to serve Peecko Android and iOS apps

## Software requirements
- Java JDK 17
- Postman

## Run the API from the command line
- set JAVA_HOME environment variable to the Java JDK 17 installation path then run the command:  
$ java -jar peecko-api.jar

**Note**  
The peecko-api.jar is a dummy API with the sole purpose of providing the interface for frontend development.  
The dummy API does not use a database, if you create an user it will exist only during execution time,  
The dummy API provides fake data in English and changing the User's language to another language will not have an effect.

## Authenticating
The Sign-In service authenticates the user and returns a token. You must provide valid credentials (username and password).   
This token has to be sent in all subsequent calls to the API as part of the HTTP headers:  
```
[ Authorization, Bearer + space + token ]
```
## Authenticating in Postman
Open the Peecko API Collection:    
1. Close all API Requests to avoid cache problem with the new token to be obtained
2. Execute the **Sign-Up** request to create the default user
3. Execute the **Sign-In** request and copy the new token from the response
4. In the left bar, select **Peecko API collection**
5. In the collection panel, select **Authorization** tab
   - Select **Bearer Token** from the **Type** list
   - Paste the copied value into the **Token** field
   - Click the **Save** button at the top-right corner of the collection panel

**From now on, you can execute any API request as they have setup to Inherit auth from parent [collection]**

## REST API


### Validate Email
-----

```
[POST] /api/auth/username/validate
```
[ ] **Body**
```json
{
    "username": "jose.pleitez@gmail.com"
}
```

[ ] **Responses**

- Successful Response
```json
{
    "code": "OK",
    "message": "Email is valid!"
}
```
- Error Response
```json
{
    "code": "ERROR",
    "message": "Email is invalid!"
}


### Validate Name
-----

```
[POST] /api/auth/name/validate
```
[ ] **Body**
```json
{
    "name": "Jose Pleitez"
}
```

[ ] **Responses**

- Successful Response
```json
{
    "code": "OK",
    "message": "Name is valid!"
}
```
- Error Response
```json
{
    "code": "ERROR",
    "message": "Name is invalid, it must contain 2 words minimum without symbols"
}



### Validate Password
-----

```
[POST] /api/auth/password/validate
```
[ ] **Body**
```json
{
    "password": "secret"
}
```

[ ] **Responses**

- Successful Response
```json
{
    "code": "OK",
    "message": "Password is valid!"
}
```
- Error Response
```json
{
    "code": "ERROR",
    "message": "Password is invalid, it must be 6 characters minimum of letters, digits and symbols &@?!#$%&"
}



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
"code": "OK",
"message": "User registered successfully!"
}
```
- Duplicate User Response
```json
{
  "code": "ERROR",
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
    "password": "12345678",
    "device-id": "aaa",
    "phone-model": "bbb",
    "os-version": "ccc"
}
```

[ ] **Responses**

- Successful SignIn Response
```json
{
    "token": "eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJqb3NlLnBsZWl0ZXpAZ21haWwuY29tIiwiaWF0IjoxNjg0MjYwOTM0LCJleHAiOjE2ODQzNDczMzR9",
    "name": "Jose Pleitez",
    "username": "jose.pleitez@gmail.com",
    "roles": [
        "USER"
    ],
    "installations": [
        {
            "device-id": "aaa",
            "phone-model": "bbb",
            "os-version": "ccc",
            "installed-on": "2023-05-16 18:15:34"
        }
    ],
    "max-allowed": 3
}
```
- Wrong Credentials Response
```
HTTP Status code 401 [UNAUTHORIZED]
```


### SignOut
-----

```
[POST] /api/auth/signout
```
[ ] **Body**
```json
{
    "username": "jose.pleitez@gmail.com",
    "device-id": "aaa"
}
```

[ ] **Response**
```json
{
    "code": "OK",
    "message": "User signed out successfully!"
}
```


### Get Installations
-----

```
[GET] /api/auth/installations
```

[ ] **Response**
```json
{
    "installations": [
        {
            "device-id": "aaa",
            "phone-model": "bbb",
            "os-version": "ccc",
            "installed-on": "2023-05-14 16:26:09"
        },
        {
            "device-id": "XXX",
            "phone-model": "ooo",
            "os-version": "ddd",
            "installed-on": "2023-05-23 11:12:43"
        }
    ],
    "max-allowed": 3
}
```


### Verify Active Email
-----

```
[GET] /api/auth/active/{username}
```

[ ] **Response**

- Verified Email
```json
{
    "code": "OK",
    "message": "Email verified successfully!"
}
```

- Not Verified Email
```json
{
    "code": "ERROR",
    "message": "Email not verified yet, please try again"
}
```

- Email not registered (does not exist in the database)
```json
{
    "code": "ERROR",
    "message": "Email is not registered"
}
```


### Activate Membership
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
  "code": "OK",
  "message": "License activated successfully!"
}
```
- Wrong Membership Response
```json
{
    "code": "ERROR",
    "message": "License is not valid, please ask your Employer to provide you a valid one"
}
```


### Get Profile
-----

```
[PUT] /api/auth/profile
```
[ ] **Body**
```json
{
  "username": "jose.pleitez@gmail.com"
}
```

[ ] **Response**

- Successful Response
```json
{
    "active-membership": true,
    "email-verified": true,
    "installations-exceeded": false,
    "installations": 1,
    "maxAllowed": 1
}
```
- Error Response
```json
{
    "code": "ERROR",
    "message": "User is not registered."
}
```


### Get Verification Code (first step to change password)
-----
This service does two things:
- returns a  request id  
- sends a verification code to the email specified in the request's body (<b>for now, the verification code is fixed 1234</b>)
These two pieces of information will be required to change the user's password in the last step
```
[POST] /api/auth/pincode
```
[ ] **Body**
```json
{
  "username": "jose.pleitez@gmail.com",
}
```

[ ] **Responses**

- Successul Response
```json
{
  "request-id": "53d05e76-159d-43c7-9cf2-f24f6078578b",
}
```
- Error Response
```
HTTP Status Code 400 (the username is invalid)
```


### Verify Activation Code (second step to change password)
-----

```
[PUT] /api/auth/pincode/{requestId}
```
[ ] **Body**
```json
{
  "pin-code": "1234",
}
```

[ ] **Responses**

- Successul Pin Code Verification Response
```json
{
    "code": "OK",
    "message": "Verification code is valid!"
}
```
- Wrong Pin Code Response
```json
{
    "code": "ERROR",
    "message": "Verification code is invalid"
}
```


### Change Password
-----

```
[POST] /api/account/change-password
```
[ ] **Body**
```json
{
    "request-id": "72de740d-c8ff-4611-aebd-718c866edd86",
    "pin-code": "1234",
    "password": "whatever"
}
```

[ ] **Responses**

- Successul Change Password Response
```json
{
    "code": "OK",
    "message": "Password changed successfully!"
}
```
- Error Change Password Response
```json
{
    "code": "ERROR",
    "message": "Cannot change password, token expired or invalid"
}
```


### Add Favorite Video
-----
```
[PUT] /api/videos/favorites/{video-code}
```

[ ] **Response**

```
HTTP Status Code 200 (OK)
```

### Remove Favorite Video
-----
```
[DELETE] /api/videos/favorites/{video-code}
```

[ ] **Response**

```
HTTP Status Code 200 (OK)
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
            "resume": "She is a LA-based yoga trainer who specialises in yoga therapeutics for scoliosis, streess and anxiety",
            "player": "youtube",
            "favorite": true
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


### Get Category Videos
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

