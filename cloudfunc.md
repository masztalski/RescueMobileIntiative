---
title: Cloud functions
subtitle: App is backed by Cloud functions by Firebase.
layout: page
show_sidebar: false
---

# Functions listening to firestore events

- On action doc created:\\
*actionWithAlarm* - sends push notification to all users when action with value alarmEnabled set to true
- On action doc upated:\\
*actionUpdate* - compares action document before and after change\\
	* if field coordinatorUID has changed sends push notification to new coordinator (value after change)
	* if field currentStatus after change turned into cancelled sends push notification to all users who have answered to action
- On assignment doc created (after action has been started with assignments to vehicles and roles assigment doc is created)\\
*actionStarted* - sends push notification to all users who have any assigment to this action with info when their vehicle have departure time

# API methods

All API methods requires authorization header with user ID token (getIdToken() on FirebaseUser) ). Base url is: ```https://<yourProject>.cloudfunctions.net/function_name```
 
- POST *createUserAnswer*\\
create or update user answer in actionAnswers collection
```
header: 
"authorization" : LoggedUserToken
{
 "userUID": "userUID",
 "actionUID": "actionUID",
 "declaredTime": "millis",
 "answer": "Agreed/Declined/NotDecided",
 "hasOwnTransport": "false/true/null",
 "nextAlarm": "millis"
}
```
- POST *createNewUser*\\
creates new user (Authentication, profile, roles). Used only by RescueWeb
```
header: 
"authorization" : LoggedUserToken
{
 "userName": "userName",
 "password": "userPassword",
 "email": "userE-mail",
 "phone": "userPhone",
 "profession": "nurse/doctor/firstAid/rescue",
 "roles": [
	"user",
	"admin"
 ],
 "skills": [
 "driver",
 "planner",
 "navigator",
 "keymaster"
 ],
}
```
- POST *updateUser*\\
updates existing user (Authentication, profile, roles). Used only by RescueWeb
```
header: 
"authorization" : LoggedUserToken
{
 "userName": "userName",
 "password": "userPassword",
 "email": "userE-mail",
 "phone": "userPhone",
 "profession": "nurse/doctor/firstAid/rescue",
 "roles": [
	"user",
	"admin"
 ],
 "skills": [
 "driver",
 "planner",
 "navigator",
 "keymaster"
 ],
 "uid": "userUID"
}
```
- POST *getUserMail*\\
obtain from Authentication desired user email based on its UID. Used only by RescueWeb
```
header: 
"authorization" : LoggedUserToken
{"uid": "userUID"}
```
- POST *saveUserLocale*\\
```
header: 
"authorization" : LoggedUserToken
{"locale": "EN"}
```

## Dangerous ones!!

- POST *cleanDatabase*
clean your database from all collections/documents created by mobile app: ```actions, assignment, actionAnswers, chat, reports```
```
header: 
"authorization" : LoggedUserToken
```
- POST *deleteAll*
```
header: 
"authorization" : LoggedUserToken
```

# OneTime use

- Initial setup of database:
*your_cloud_url/initDatabase*  - use it to create first superuser of app and initial data in Firestore. Doesn't require authorization header because no user exists yet. Payload:
```
{
    "email" : "mail@mail.com",
    "password": "password",
    "userName": "UserName"
}
```