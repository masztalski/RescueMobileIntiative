---
title: Database architecture
subtitle: Explanation of tables
layout: page
show_sidebar: false
---

# Collections explanation

- **actionAnswers/{actionUID}/values/{autoID}** - containing answers/declaration of users
- **actionTypesDictionary/{autoID}** - dictionary for spinner/comboBox to choose type of action. One or more types can have value isRescue = true . Those are action types with highest prio and default alarm setted, Can be extended by client
- **actions/{autoID}** - all created actions
- **assignment/{actionUID}/values/{autoID}** - each doc is one vehicle assignment. Departure time, passangers and roles.
- **profiles/{userUID}** - basic information about user
- **roles/{userUID}** - roles assigned to user
- **tokens/{userUID}** - FCM token for each user for sending FCM message
- **vehicles/{autoID}** - dictionary of vehicles.
- **locale/{userUID}** - assigned langauge of each user to determine notifications payload from cloud functions
- **reports/{actionUID}** - reports from action created by user
- **chat/{actionUID}/messages/{timestamp}** - collection for chat messages per each action.

# Minimum database content requirments

If you followed QuickStart guide then described below documents should already be in your database created by *initDatabase* function.
***Without those requirments app won't work***

**Required collections:**
- **actionTypesDictionary**
- **vehicles** should always contain at least 1 common car ```capacity != null, name != null, ownerID = null``` and 1 dummy car ```capacity = null, isDummy = true, name != null (np. Unassigned), owenrID = null```. Cars with owenrID != null can be deleted - they are created during declaration of user with ownVehicle checkbox marked.
- **roles/{userUID}** should contain array named roles with two elements: user, admin. *userUID* is ID user from Authentication tab. This user is required for Admin panel to create new users with roles and profile tables. 

***User without roles and profiles document cannot log into mobile!!***