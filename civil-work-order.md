---
title: "Civil Work Orders"
description: "Civil Work Order API"
---

## Overview

_Note: These services are still in alpha. Until they are formally released, they are only intended for use by internal applications and selected customers unless agreed by the Engineering Leads and Product Management._

The target audience for these services are developers of applications that create or use WorkOrders and Tasks. Actions on the construction site have traditionally been the result of verbal conversations between team members. To support the running of a digital connected site, the WorkOrders API provide the means to define the actions undertaken on a particular site.

These actions are undertaken within the context of a Common Project (see the "Concepts" section below). These tasks are assigned by the Site Supervisor to an available Resource that is on-site (for example, a Compactor) and are related to a planned Activity, defined as part of the overall Project plan (for more details, see the Planned Activity and Planned Progress APIs). Such an action or Task can also be related to a Design that the Resource will follow.

### Use Cases

The WorkOrder API allows you to:

- Create, Edit, and retrieve the WorkOrders with attached Tasks from XXX, for a given Project - This helps to manage the volume of Tasks and associated data when monitoring a large scale project. A work order is just a container of one or more Tasks.
- Create, Edit, search, and retrieve Tasks - The tasks reduce the work orders further to manageable workloads for each machine or resource
- Relate a Task to a WorkOrder - As projects change, the tasks may need to be moved to reflect the updates
- Associate a Resource (for example, a machine) to a Task - This is for resource management so it is clear which task is being worked on by which resource
- Associate a Design to the Task for the Resource/machine to follow during execution.
- Associate a Task to a planned Activity to account for progress against plans. This is for clear and accurate tracking of the day-to-day site progression
- Follow the progress of a Task through a number of states, from _NotStarted_ to _Completed_.

Included in these APIs is an interim set of **Machine** related APIs.

A Machine is typically defined first and then associated with a given Project. These additional APIs support the definition of “Machines” working on a Project, with their parameters and their associations to Devices attached to them, for example, EC520 Machine Control device). One may define a specific machine with its parameters and associate it with an EC520XXX control device, defined in WorksManager. Multiple Devices can be associated with the Machines.

Note that for historical reasons, these interim APIs use the term ‘Asset’ to represent a ‘Machine’ - for example, using \*\*<code>GET /projects/{projectId}/assets returns all Assets/Machines associated with a given Project. </code></strong>

To allow a caller to assign a Task to a Resource, this Resource needs to have been defined as a Machine/Asset of the selected Project.

Having assigned a collection of Tasks to a Machine/Asset, you can define the order of this list of Tasks. This collection then becomes the ordered schedule of Tasks to be executed by the selected Machine/Asset. The ordering is achieved by placing the Ids of the Task in the desired list position, by calling the following service:

```
PUT /projects/{projectId}/schedule/assets/{assetId}
```

Note that if no schedule is explicitly defined, for a given Machine/Asset, the system will create a default one where Tasks will be ordered by their creation time, ascending.

Using the below, a caller can retrieve the Resource/Asset schedule:

```
GET /projects/{projectId}/schedule/assets/{assetId}
```

**Note :** These interim APIs will only be available as a stop-gap solution to define Machine/Assets, to support WorkOrders/Tasks. As part of the Cloud Construction development roadmap, these will eventually be replaced by an alternative platform solution that will fulfill the same requirements, and more.

### Concepts

When looking at the WorkOrder API, it is good to have an understanding of some of the basic terminology:

![](https://coreconsole-assets.console.trimblecloud.com/api/a8c1e3fb-9a88-4836-9a52-72314462ff20/ccsdiagram.png)

- A **WorkOrder** is the container for Tasks.
  - A WorkOrder has a _name_, a _description,_ and a _status_.
  - On creation, a WorkOrder is in the _NotStarted_ status.
  - As soon as a Task the WorkOrder contains goes into _InProgress_, the WorkOrder transitions automatically to _InProgress_.
  - Eventually, a WorkOrder is _Completed_ through a manual action.
- A **Task** represents an action taken to transition an item of work towards its completion.
- A Task belongs to a **Common Project** which is a Trimble Connect Project that has been associated with a corresponding WorksManager Project.
- A Task has a number of attributes
  - _name_ and a _type_ indicating the kind of work being undertaken (for example, Compaction)
  - _Start_ and _End_ date and times. Which are optional.
  - A Task can contain _notes_ indicating more details about what needs to be done and possibly how it needs to be done.
  - _Notes_ - this is an optional free-from field that can be used to add additional comments about a task
  - _parameters_ that set the context within which a task can be performed - for example, _MinimumPassCount_, for a Compaction Task.
- A Task _Status_ indicates what state the Task is in.
  - A Task transitions from:
    - _NotStarted_
    - to _InProgress_
    - to _Complete_
    - and eventually _Archived_
  - An _InProgress_ Task can be paused, to reflect “interruptions” in the field. When an _InProgress_ Task is paused, the Task’s Status becomes ‘_paused_’ as well.
- A Task is assigned to a **Resource**.
  - At this stage, a Resource is a machine (for example, Compactor)
  - _A Resource will eventually be able to also represent a Person, or a Team, in other words, a group of machines or people. Please note that this is not yet supported._
- When a Task is performed, its execution can be attributed to a more granular portion of time in the overall Project plan, which is termed a Planned Activity. This can be achieved so long as the Task is associated with a **Planned Activity**. The Task update service supports this association.
- A task can also be associated with a WorksManager **Design** (A WorksManager entity helping to guide the selected machine). This association is optional.
- A Task can be part of a collection of Tasks, which is termed a **WorkOrder**. This inclusion in a collection is optional.

### Prerequisites

- A [license for Trimble Connect](https://connect.trimble.com/storefront) is required before you can utilize these APIs.
- You must also have access to a Trimble Connect Project.
- Additionally, any application wishing to make use of the APIs needs to be registered in Trimble Cloud Console .
  - Once registered, the Application will be identified by a **ClientId**, which will be needed later on, when users of the APIs authenticate themselves.
  - This registered application needs to also be subscribed to the CCS Construction Cloud Field Factory APIs. (See subscriptions of our sample app)
- A user wishing to make use of the WorkOrder APIs will need to have permission to a given “Common Project” (see the Concepts section), that has been synchronized between TrimbleConnect and WorksManager.
  - For the creation or editing of WorksOrder or Tasks in the selected Project, the user will need to have the ‘Admin’ role in this Project, which translates to ‘Project Manager’ in WorksManager.
- An app token can be used as an alternative to a user token, for applications invoking the APIs without a specific user identity. See the Trimble Cloud Console guide on authentication for more details.

## Getting Started

- To get started, make sure that all dependencies stated above are satisfied.
- Download our <a href="https://github.com/trimble-oss/Workorder-demo-app" target="_blank">sample app</a> and test it against your chosen project.

## Authentication

Authentication is handled via Trimble ID. Both user and app tokens are supported. See the Trimble Cloud Console guide on authentication for further details on how to obtain these tokens.

## Authorization

Once a user is authenticated, they make a call to the APIs. Users with a “ProjectManager” role (in WorksManager) can perform all API calls including creating/editing WorkOrder and Tasks. Users with a simple “User” role can only view the data but not modify it. See the Reference tab for accessibility of each service.

When an app token is provided, the calling application can invoke all the APIs available, for reading and writing Tasks, WorkOrders and Resources/Assets.

## Code Samples

- Various sample code extracts are present in the OpenAPI Specification that can be found in the Reference tab.
- A [sample app](https://github.com/trimble-oss/Workorder-demo-app) is also available.

![](https://coreconsole-assets.console.trimblecloud.com/api/a8c1e3fb-9a88-4836-9a52-72314462ff20/ccssample.png)

- This application has had to be subscribed to a number of required APIs Products, to make use of the WorkOrder/Task functionality:-
  - CCS Construction Cloud Field Factory APIs.
  - Profile-Legacy
  - CWS-Internal
  - WorksOS UI
  - CCS Construction Cloud Process APIs.
  - CWS
  - profiles

## Error messages

Error messages are a vital part of any API. Any failures related to the invocation of a given service will be documented in the Reference tab against that service.

For example, the <code>GET /workorders</code> service documentation covers the following potential errors:

<p><code>
400 Bad Request - 20029 : BAD_FILTER
401 Unauthorized - 20001 : USER_HAS_NO_ACCESS
</code></p>

When, an error occurs, further details regarding the error are provided in the body of the response, as in this example:

```
{
 "status": 400,
 "code": 20029,
 "message": "Filter (parameter 'q') must have a 'projectId' or 'projectid and 'workOrderId",
 "moreInfo": "Please provide this id to support, while contacting, TraceId 631b39278479c916c",
 "timestamp": "2022-09-09T13:03:06.987+00:00",
 "errorCode": "BAD_FILTER",
 "traceId": "631b398a8c271cc52278479c916c3681"
}
```

The caller can use this error information to address the error condition in the API calls or can be used for further diagnostics with Trimble support by providing the given _TraceId_ information.

## Additional Links

There are a number of related APIs to this one, see these for more information:

- Planned Activities
- Planned Activities Progress
- Zone
- Geofence
