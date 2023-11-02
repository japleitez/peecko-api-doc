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
```


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
```


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
```


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
    "token": "eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJqb3NlLnBsZWl0ZXpAZ21haWwuY29tIiwiaWF0IjoxNjkwNjQ1NTI3LCJleHAiOjE2OTA3MzE5Mjd9.P_7VJltDZpS0e5cciTGFdTJNXSigyuB3Qf-cseRj7HU885-cEfW3j2SzGqs5E1IJP0nONAdRs0WjHUqGNY_HSA",
    "name": "Jose Pleitez",
    "username": "jose.pleitez@gmail.com",
    "email-verified": true,
    "devices-exceeded": false,
    "devices-count": 1,
    "devices-max": 3,
    "membership": "11111111111111111111",
    "membership-activated": true,
    "membership-expiration": "20-10-2023",
    "membership-sponsor": "BIL Wealth Management",
    "membership-sponsor-logo": ""
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
    "message": "Successful sign out"
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
For now, valid license must:   
- has 20 chars
- start with '1111'
- not be in the list of deactivated licenses
```
[PUT] /api/membership/activate
```
[ ] **Body**
```json
{
  "license": "11119999999999999999",
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

### De-Activate Membership
-----
You can deactivate a license using this backdoor service (no need of token and it will be available only during development time)   
Minimum validation: license must be 20 chars 
```
[PUT] /api/auth/deactivate/{license}

[ ] **Responses**

- Successful Membership De-Activation Response
```json
{
  "code": "OK",
  "message": "License inactivated successfully!"
}
```
- Wrong Membership Response
```json
{
    "code": "ERROR",
    "message": "License must be 20 char length"
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
    "name": "Jose Pleitez",
    "username": "jose.pleitez@gmail.com",
    "email-verified": true,
    "devices-exceeded": false,
    "devices-count": 1,
    "devices-max": 3,
    "membership": "11111111111111111111",
    "membership-activated": true,
    "membership-expiration": "09-10-2023",
    "membership-sponsor": "BIL Wealth Management",
    "membership-sponsor-logo": "http://peecko-api/logo/0190.png"
}
```
Where:   
membership-expiration: do not use for validation, it is merely informative and should be handled as plain text   
membership-sponsor-logo: if the url is empty then display the default logo used in the design   

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


### Reset Password [UPDATED] [endpoint renamed]
-----

```
[POST] /api/auth/reset-password
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

- Successul Reset Password Response
```json
{
    "code": "OK",
    "message": "Password changed successfully!"
}
```
- Error Reset Password Response
```json
{
    "code": "ERROR",
    "message": "Cannot change password, token expired or invalid"
}
```



### Change Password [NEW]
-----

```
[POST] /api/auth/change-password
```
[ ] **Body**
```json
{
    "username": "jose.pleitez@gmail.com",
    "old-password": "old-value",
    "new-password": "new value"
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
- Error Email validation Response
```json
{
    "code": "ERROR",
    "message": "Email is required"
}
```
- Error Email Not Found Response
```json
{
    "code": "ERROR",
    "message": "Email is not registered"
}
```
- Error Old Password Wrong Response
```json
{
    "code": "ERROR",
    "message": "Current password does not match"
}
```
- Error New Password Invalid Response
```json
{
    "code": "ERROR",
    "message": "Password is invalid, it must contain only letters, digits and symbols &@?!#$%&"
}
```



### Change Personal Info [NEW]
-----
This service changes only the user's name, the user's email is provided as a referenced only
```
[POST] /api/auth/change-personal-info
```
[ ] **Body**
```json
{
    "username": "jose.pleitez@gmail.com",
    "name": "Jose A. Pleitez"
}
```

[ ] **Responses**

- Successul Change Personal Info Response
```json
{
    "code": "OK",
    "message": "Personal information updated"
}
```
- Error Email validation Response
```json
{
    "code": "ERROR",
    "message": "Email is required"
}
```
- Error Email Not Found Response
```json
{
    "code": "ERROR",
    "message": "Email is not registered"
}
```
- Error Name Invalid Response
```json
{
    "code": "ERROR",
    "message": "Name is invalid, it must contain 2 words minimum without symbols"
}
```


### Add Favorite 
-----
```
[PUT] /api/s/favorites/{-code}
```

[ ] **Response**

```
HTTP Status Code 200 (OK)
```

### Remove Favorite 
-----
```
[DELETE] /api/s/favorites/{-code}
```

[ ] **Response**

```
HTTP Status Code 200 (OK)
```

### Remove All Favorite s
-----
```
[DELETE] /api/s/favorites
```

[ ] **Response**

```
HTTP Status Code 200 (OK)
```


### Get Today s [UPDATED][coach fields ed]
-----
```
[GET] /api/s/today
```

[ ] **Response**

- Today s Response
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
    "s": [
        {
            "code": "YG002",
            "category": "YOGA",
            "title": "Yoga Leg Stretching 2",
            "duration": "12:30",
            "coach": "Julia Hernandez",
            "image": "https://i.ytimg.com/vi/hH_KVVgV_Vo/hqdefault.jpg",
            "url": "https://www.youtube.com/embed/hH_KVVgV_Vo",
            "audience": "Women",
            "intensity": "Beginner, Intermediate",
            "tags": [
                "energy",
                "endurance"
            ],
            "description": "This is a quick routine that is focused mainly on hips and hamstrings. Perfect for recovery/sore muscles and working on your flexibility",
            "resume": "She is a LA-based yoga trainer who specialises in yoga therapeutics for scoliosis, stress, anxiety, depression, and many other modern day imbalances and health issues",
            "player": "youtube",
            "favorite": false,
            "coach-website": "https://peecko.com/",
            "coach-email": "julia.hernandez@gmail.com",
            "coach-instagram": "julialux"
        },
    ]
}
```


### Get Favorite s
-----
```
[GET] /api/s/favorites
```

[ ] **Response**

- Today s Response  
The response is similar to the payload of the **Get Today s** service


### Get Library s
-----
```
[GET] /api/s/categories
```
This service provides the list of available categories along with a short list of s for each category.

[ ] **Response**

- Library s Response
```json
{
    "greeting": "All our  content under one roof, organized into wellness & fitness categories",
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
This service returns the list of notifications sorted by release date in descent order,  
**Note:** The notification's viewed attribute indicates whether the message has been read or not

[ ] **Response**

- Notifications Response
```json
[
    {
        "id": 1,
        "title": "Weloner Marathon Luxembourg ",
        "message": "The Weloner Marathon is an annual marathon by night in Luxembourg which was held for the first time in 2006",
        "image": "http://path/filename.jpg",
        "video": "http://path/filename.jpg",
        "date": "07 Dec 2023",
        "viewed": true
    },
    {
        "id": 2,
        "title": "Meet Matheo, New Health Coach, We are boarding!",
        "message": "Matheo is a Wellness Coach Certified, Matheo is specialized in Healing from chronic health issues and Healing Nutrition",
        "image": "http://path/filename.jpg",
        "video": "http://path/filename.jpg",
        "date": "05 Dec 2023",
        "viewed": false
    }
]
```

### Set Notification as Viewed
-----
```
[PUT] /api/account/notifications/:id
```
This service sets the notification's viewed to true and returns the HTTP status 200.   
**Note:** if the notification does not exist, the service does not raise any error and still returns the HTTP status 200.   

[ ] **Response**
HTTP Status 200 OK

### Create Playlist
-----
```
[POST] /api/videos/playlists/
```
[ ] **Body**
```json
{
  "name": "Core Exercises",
}
```
This service creates a new playlist 

[ ] **Response**

- Response
```json

{
    "username": "jose.pleitez@gmail.com",
    "id": 1,
    "name": "short workouts",
    "videoItems": []
}

```
- Error Response
```json
{
    "code": "ERROR",
    "message": "Playlist already exists"
}

```
 

### Remove Playlist
-----
```
[DELETE] /api/videos/playlists/:id
```
This service deletes a playlist by id an returns the updated playlists.     
**Note:** requesting to delete a non-existing playlist does not raise an error and the service returns the existing playlists.   

[ ] **Response**   
For example, the request /api/videos/playlists/2 will remove the playlist number 2 and the updated list looks like this:   
- Response
```json

[
    {
        "id": 1,
        "name": "aaa"
    },
    {
        "id": 3,
        "name": "XXX"
    },
]
```


### Add Video to Playlist
-----
```
[PUT] /api/videos/playlists/:listId/:videoCode
```
Example: /api/videos/playlists/1/YG001   

[ ] **Response**

- Successful Response   

The service adds the specified video (by code) to the playlist (by id) and returns the updated playlist.   
The new video appears at the end of the list.   

The playlist is represented by a linked list of video nodes where:   
- the node's code makes reference to the video's code
- the node's previous points to the previous node in the linked list
- the node's next points to the next node in the linked list
- the node's video
**Note:** this means that the top node.previous=null and the bottom node.next=null   

```json

{
    "username": "jose.pleitez@gmail.com",
    "id": 1,
    "name": "short workouts",
    "videoItems": [
        {
            "previous": null,
            "code": "YG001",
            "next": "null",
            "video": {
                "code": "YG001",
                "category": "YOGA",
                "title": "Yoga Leg Stretching 1",
                "duration": "12:30",
                "coach": "Julia Hernandez",
                "image": "https://i.ytimg.com/vi/-btuQsCiyfc/hqdefault.jpg",
                "url": "https://www.youtube.com/embed/-btuQsCiyfc",
                "audience": "Women",
                "intensity": "Beginner, Intermediate",
                "tags": [
                    "energy",
                    "endurance"
                ],
                "description": "This is a quick routine that is focused mainly on hips and hamstrings. Perfect for recovery/sore muscles and working on your flexibility",
                "resume": "She is a LA-based yoga trainer who specialises in yoga therapeutics for scoliosis, stress, anxiety, depression, and many other modern day imbalances and health issues",
                "player": "youtube",
                "favorite": false,
                "coach-website": "https://peecko.com/",
                "coach-email": "julia.hernandez@gmail.com",
                "coach-instagram": "julialux"
            }
        },
    ]
}
```

- Error Response (playlist does not exist)
```json
{
    "code": "ERROR",
    "message": "Playlist is invalid"
}
```

- Error Response (video does not exist)
```json
{
    "code": "ERROR",
    "message": "Video is invalid"
}
```

-- Error Response (video already added)
```json
{
    "code": "ERROR",
    "message": "Video already exists in the playlist"
}
```


### Get Playlists (ids and names)
-----
```
[GET] /api/videos/playlists/
```
[ ] **Response**

- Response
```json
[
    {
        "id": 1,
        "name": "short workouts"
    },
    {
        "id": 2,
        "name": "intensive pilates"
    },
    {
        "id": 2,
        "name": "short meditations"
    },
]
```


### Get Playlist by Identifier
-----
```
[GET] /api/videos/playlists/:id
```
[ ] **Response**
- Successful Response
```json
{
    "username": "jose.pleitez@gmail.com",
    "id": 1,
    "name": "aaa",
    "videoItems": [
        {
            "previous": null,
            "code": "YG001",
            "next": "YG002",
            "video": { video_YG001 }
        },
        {
            "previous": "YG001",
            "code": "YG002",
            "next": "YG003",
            "video": { video_YG002 }
        },
        {
            "previous": "YG002",
            "code": "YG003",
            "next": "YG004",
            "video": { video_YG003 }
        },
        {
            "previous": "YG003",
            "code": "YG004",
            "next": null,
            "video": { video_YG004 }
        }
    ]
}
```
- Error Response
```json
{
    "code": "ERROR",
    "message": "Playlist is invalid"
}
```


### Move video up or down in a Playlist
-----
```
[PUT] /api/videos/playlists/:listId/:videoCode/:direction

Where direction is 'up' or 'down'   

Example
/api/videos/playlists/1/YG001/down   

[ ] **Response**

This service returns the playlist with the videos re-ordered as requested.





