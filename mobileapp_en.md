---
title: Mobile app
subtitle: Descryption of mobile app
layout: page
show_sidebar: false
lang: en
permalink: /mobileapp
---

# Authorization

App uses Firebase Authentication by e-mail to authenticate users. By default login is required only on first install of app

# Communication with Firebase

App uses Firestore as its database. No data is saved explicity on device. Most of communication with Firestore is handled by Firestore library. \\
This means write into database and read from it happens with directly calls to Firestore collections and documents. \\
Only few events (like creating user answer) happens by API handled by cloud functions due to necessary to determine if this is update or creating new instance

# Architecture

App is written with Flutter. We decided to use **BLoC pattern** and some elements from *Clean Architecture* : repositories classes, model classes, "controllers" for views as Blocs, assets paths stored in separate class, translation keys stored in separate class, styles and colors also stored in separate class. \\
This approach tries to mimic known from Android development styles.xml, colors.xml etc. to ease working with app.\\

App uses localization feature storing all text literals in json files in *lang* folder

# Flavors

App uses flavors to create 3 different environments. We achieve it using gradle productFlavors and iOS schemas connected with Flutter flavor feature. \\
Every feature has its own **main_env.dart** file to save in config.dart selected flavor. Based on it app then selects valid URL for backend.\\
\\
This approach requires specif way of running and building app:
```
flutter build --flavor=development, --target lib/main_env.dart
```
For running app from Android Studio you can just define your Run Configuration. Open Run/Debug Configuration window and type following settings for Dev Environment:
```
Dart entrypoint: full_path\lib\main_dev.dart
Additional arguments: --flavor=development
Build flavor: development
```
It works for Android and iOS runs.

# Configuration files

As app is open-source all files containing our API keys and configurations are encrypted as \*.enc files. Those files are decrypted during Travis build for our private distributions. If you want build app on your own and give it a try - follow GetStarted to create your own Firebase environment

# Push notifications

App is using FCM with cloud functions to send users push notifications in response to certain events. We are using for this purpose 
<https://pub.dev/packages/firebase_messaging> library. For cases when app is in foreground we are using local notifications to handle incoming push payload.

## App handles following notifications:

- Action with alarm
Coordinator (user with admin role) creates action with option alarm enabled. Then all users will receive notification about new action. After selection of notification user is brought into declaration of availability screen. 
- Change of coordinator
When coordinator(creator) of action after its creation declares that he cannot participate in action and choose another coordinator. Chosen coordinator will receive notification that he was chosen to lead action. After selection of notification user is brought into declaration of availability screen. 
- Action started
Coordinator of action after its creation have decided to begin action with gathered users. After he chosen option "Begin organisation" from action details screen options and then assigned users to vehicles he tap "Action start" button document in *assignment* collection is created and notification to users declared as **Positive** for this action receives notification about it. \\
After selection of notification user is brought to summary screen with info about his assignments.
- Action cancelled
Coordinator of action after its creation decides to choose option "Cancel action". Everybody who declares **any** answer to this action will receive notification.

## Local notifications

This notifications are handled only locally without server
- Reminder for coordinator for action with field "deadline" filled (action with alarm enabled) will receive at this time notification that decision time passed and he should start assigning people and start action
- When user selects option "Ask later" in declarion screen he will receive reminder about decision required