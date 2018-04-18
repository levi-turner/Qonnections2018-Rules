# Extending the Power of Qlik Sense with Rules (Qonnections 2018)

This repository will include all the example rules used in the presentation from Qonnections 2018 titled Extending the Power of Qlik Sense with Rules. It will also include references to sources for more information regarding rules in Qlik Sense.

# Rules

These rules will be listed in the following format:
- Rule Name
  - `Filter(s)`
  - Action(s)
  - `Conditions()`
  - Context
  - Plain English explanation

At the outset, we have disabled the following default rules:

- `Stream`
- `CreateAppObjectsPublishedApp`

## Pre-prepared rules:
- Q-Stream-Development
  - `Stream_*`
  - Read+Publish
  - `((user.role="Developer") and (resource.name="Development"))`
  - Both
  - This rule will provide read and publish rights to streams with the name of Development to all users who have the role of Developer from the configured User Directory Connector

- Q-Stream-Executive
  - `Stream_*`
  - Read+Publish
  - `(((user.group="Executive" or user.role="Executive")) and (resource.name="Executive"))`
  - Both
  - This rule will provide read and publish rights to streams with the name of Executive to all users who have the role of Executive OR users who are members of the Executive group from the configured User Directory Connector

- Q-Stream-HR
  - `Stream_*`
  - Read+Publish
  - `((user.group="HR") and (resource.name="HR"))`
  - Both
  - This rule will provide read and publish rights to streams with the name of HR to all users who are members of the HR group from the configured User Directory Connector

- Q-Stream-IT
  - `Stream_*`
  - Read+Publish
  - `((user.group="IT") and (resource.name="IT"))`
  - Both
  - This rule will provide read and publish rights to streams with the name of IT to all users who are members of the IT group from the configured User Directory Connector

- Q-Stream-Sales
  - `Stream_*`
  - Read+Publish
  - `((user.group="Sales") and (resource.name="Sales"))`
  - Both
  - This rule will provide read and publish rights to streams with the name of Sales to all users who are members of the Sales group from the configured User Directory Connector

- Q-Stream-AppLevel
  - `App*`
  - Read
  - `(resource.resourcetype = "App" and resource.stream.HasPrivilege("read") and resource.@AppLevelManagement.Empty()) or ((resource.resourcetype = "App.Object" and resource.published ="true" and resource.objectType != "app_appscript" and resource.objectType != "loadmodel") and resource.app.stream.HasPrivilege("read"))`
  - Both
  - This rule will provide read rights to Apps if the user has read rights on the stream provided that the AppLevelManagement custom property has not been assigned. It will also provide read rights to App.Objects provided that the App.Object is published and is not a script or load model if the user has read rights on the stream. Functionally this rule allows for assigning a AppLevelManagement custom property and hiding the app from being visible in the stream unless another rule provides access. This is an example of a sub-stream level security rule.

- Q-CreateAppObjects
  - `App.Object_*`
  - Create
  - `((!resource.App.stream.Empty() and resource.App.HasPrivilege("read") and (resource.objectType = "userstate" or resource.objectType = "sheet" or resource.objectType = "story" or resource.objectType = "bookmark" or resource.objectType = "snapshot" or resource.objectType = "embeddedsnapshot" or resource.objectType = "hiddenbookmark") and !user.IsAnonymous()) and (user.role!="Consumer"))`
  - Both
  - This rule will provide the ability to create App.Objects (e.g. sheets, stories, bookmarks) where the App.Objects are userstates, sheets, stories, bookmarks, snapshots, embeddedsnapshots, hiddenbookmarks so long as the user is not anonymous and does not have the role of Consumer

- Q-Stream-AppLevel-Restricted
  - `App_*`
  - Read
  - `((resource.resourcetype = "App" and resource.stream.HasPrivilege("read") and resource.@AppLevelManagement="Restricted") and (user.group="Executive" or user.role="Executive"))`
  - Both
  - This rule will provide the read rights on Apps where the app has the Restricted value applied to the app of the AppLevelManagement custom property provided that the user is either in the Executive group or has an Executive role from the configured User Directory Connector.

- Q-QMC-Administrators
  - `*`
  - Create + Read + Update + Delete + Export + Publish + Change owner + Change role + Export data + Access offline
  - `(user.role="Administrator")`
  - QMC
  - This rule will provide all rights to all things in the QMC for users who have the Administrator role from the configured User Directory Connector. This rule re-creates the RootAdmin role but leverages existing user attributes to provide that access rather than needing to assign a formal role inside of Qlik Sense.

- Q-QMC-Developers
  - `QmcSection_App,QmcSection_Task`
  - Read
  - `(user.role="Developer")`
  - QMC
  - This rule will provide read rights to the Apps and Tasks section of the QMC provided that the user has the Developer role from the configured User Directory Connector.

-  Q-QMC-Developers-Tasks
  - `ReloadTask*`
  - Read + Update + Delete
  - `((user.role="Developer") and resource.App.HasPrivilege("read"))`
  - QMC
  - This rule will provide the rights to read, modify, or delete tasks in the QMC so long as the task is associated with apps which the user has read rights on for users who have the Developer role from the configured User Directory Connector.

-  Q-QMC-Developers-Tasks-Create
  - `ReloadTask*`
  - Create
  - `(user.role="Developer")`
  - QMC
  - This rule will provide the rights to create tasks in the QMC for users who have the Developer role from the configured User Directory Connector. When the users create the task, they will only be able to create a reload task for apps which they have read rights on.

-  Q-QMC-Developers-Triggers
  - `SchemaEvent*,CompositeEvent*, ExecutionResult*`
  - Create + Read + Update + Delete
  - `(user.role="Developer")`
  - QMC
  - This rule will provide the rights to create, see, modify, remove triggers for tasks which the user already has read rights on for users who have the Developer role from the configured User Directory Connector.

-  Q-QMC-Developers-Apps-WithRestrictions
  - `App*`
  - Create + Update + Delete
  - `((user.role="Developer"))`
  - QMC
  - This rule will allow users who have the Developer role to be able to create, modify, and delete apps or app objects which they already have read rights to. Functionally this will allow for publish and replace operations.

-  Q-QMC-Developers-Apps-WithoutRestrictions-Objects
  - `App.Object_*`
  - __TODO__
  - `((user.role="Developer") and resource.app.HasPrivilege("read"))`
  - QMC
  - This rule will allow users with the Developer role to be able to __TODO__ to the objects belonging to apps which they have read rights on. Functionally this likewise is integral to the publish and replace operation.

-  Q-QMC-Developers-Apps-WithoutRestrictions-Apps
  - `App_*`
  - __TODO__
  - `((user.role="Developer") and resource.stream.HasPrivilege("read"))`
  - QMC
  - This rule will allow users with the Developer role to be able to __TODO__ to the apps belonging to the streams which they have read rights to. Functionally this rule over-rides the AppLevelManagement custom property sub-stream level access that has been setup on the front end.


## Relevant documentation on Rules
- [Resource Filters](https://help.qlik.com/en-US/sense/February2018/Subsystems/ManagementConsole/Content/available-resource-filters.htm)
- [Operators and Functions](https://help.qlik.com/en-US/sense/February2018/Subsystems/ManagementConsole/Content/operators-and-functions-for-conditions.htm)
- [Conditions](https://help.qlik.com/en-US/sense/February2018/Subsystems/ManagementConsole/Content/available-resource-conditions.htm)
- [Examples](https://help.qlik.com/en-US/sense/February2018/Subsystems/ManagementConsole/Content/security-rules-examples.htm)
- [GSS Example Security Rules](https://github.com/eapowertools/iPortal/blob/master/docs/gss_setup_guide.md)
- [Handling Exceptions](https://community.qlik.com/blogs/qlikviewdesignblog/2015/12/29/managing-qlik-sense-streams-security-rules-and-exception-management)