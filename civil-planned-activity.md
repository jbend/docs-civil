---
title: "Planned Activities"
description: "Planned Activity API"
---

## Overview

_Note: These services are still in alpha. Until they are formally released, they are only intended for use by internal applications and selected customers unless agreed by the Engineering Leads and Product Management. The target audience for these services are developers of internal applications that create or use Activities/Progress data._

The Planned Activity Service and the Planned Activity Progress Service support the Estimate-to-Field workflow. These services enable Activities defined in the estimating and scheduling phases to be used when tracking progress in the field. Additionally, they enable progress data to be fed back to the estimating and scheduling systems.

Lack of real-time visibility over the progress of an activity on a construction site may result in a lack of clarity about how much work has been carried out and when the Activity is likely to be completed. This may lead to underclaiming or overclaiming for work on the part of the contractor.

Using these services connects the machine data from the field to both the original estimate and the contractor's Enterprise Resource Planning (ERP) system. This process accelerates the Estimate-to-Field workflow by replacing the need for progress data to be exchanged through phone calls and spreadsheets.

### Estimate-to-Field Workflow

Users: Estimator, Project Manager, Site Supervisor, Operator

This is the process of creating a project bid and then using estimates from said bid to logically divide a project into small and easily trackable pieces known as Activities. Work is tracked against these Activities with the data being fed back in the form of Progress Measurements. This data can then be compared against the initial bid estimates and other costing information in order to determine bid accuracy, to track earned value, and to help generate accurate progress information. The progress information can then be shared with clients to aid in prompt and accurate payment for services rendered up to that point.

For example:

1. An Estimator creates an estimate in Trimble Quest.
2. This estimate can then be exported/published to a number of compatible applications for example Trimble Viewpoint, Microsoft Project. and also published to the CCS Planned Activities service. These published Activities can then be pulled into other applications such as WorksOS to be monitored by the Site Supervisor, assuming the project is a connected project.
3. The Project Manager can then pull the scheduling information into Quest from MS Project, and share projected costing information to the customer’s favored ERP.
4. The Site Supervisor configures an activity for monitoring in WorksOS. To configure the activity, the Site Supervisor specifies the starting surface (survey), the target surface (design), and (optionally) the boundary of the activity. As work is completed by the machines in the field, WorksOS updates the progress of the activity and writes the progress measurements to the Planned Activity Progress service. The Site Supervisor monitors the incoming Progress Measurements to determine whether Activities are on-track and make adjustments in the field if necessary. These Progress Measurements are automatically synced back to tools such as Quest.
5. The Project Manager can use the synced data to track earned value in Microsoft PowerBI. Quest then pushes the Progress data into costing solutions such as Viewpoint Vista.
6. Finally, this data can be used to accurately bill clients based on up-to-date information about the progress of the project.

### Use Cases (Estimate-to-Field)

The following use cases apply to the estimate-to-field workflow:

- An estimator breaks down an estimate into activities and uses a support ERP to push the Activities into the Planned Activity Service
- A site supervisor uses WorksOS to select an Earthworks activity from the Planned Activity Service and track the activity in WorksOS
- A site supervisor views the current progress of a tracked activity in WorksOS and the activity progress is pushed to the Planned Activity Service
- The operator of an automated compactor uses the Trimble Autonomous Solutions (TAS) app to create a task in the field. The operator adds the related activity to the task to link the task to the estimate
- A contractor uses a load-counting application, such as Optron, to select an activity from the Planned Activity Service and track the progress of the activity by means of load counts
- A project manager pulls progress data into Quest and pushes the progress data into a project management system, such as Viewpoint

## Getting Started

### Authorization

Once a user/app is authenticated and they make a call to our APIs, a check is made using Trimble Connect to ensure they have access to the project specified in the request.

### User Token

When using a user token, there are two roles available which are set via the project in Trimble Connect.

#### USER role

With this role, a user can use the following endpoints:

**Planned Activities**

`/activities` - GET

`/activities/{activityId}` - GET

`activities/external/{externalId}` - GET

**Planned Activities Progress**

`/activities/{activityId}/progress` - GET

`/activities/progress/{progressMeasurementId}` - GET

`/activities/external/{externalId}/progress` - GET

#### ADMIN role

With this role, a user can use all available endpoints with all available operations on said endpoints.

### App Token

When using an app token, the calling application can use the following endpoints:

**Planned Activities**

`/activities` - GET, POST

`/activities/{activityId}` - GET, PATCH

**Planned Activities Progress**

`/activities/{activityId}/progress` - GET, POST

If you wish for your application to authenticate using an app token rather than a user token, you must contact the Aurora team via email to request that your application be registered as a trusted application, citing a valid business reason for why you can’t provide a user token for requests. We aim to respond to these requests within **two working days**. Please use the following email template for these requests:

**_To: civil-sw-aurora-ug@trimble.com_**

**_Subject: Trusted Application Registration_**

_Hi team,_

_We’d like to register an application as a trusted application for the CCS Planned Activities and Planned Activities Progress APIs. Here are the details of our application:_

_Environment: &lt;pre-prod | prod>_

_Application Name: &lt;application_name>_

_Application ID: &lt;application_id>_

_Business purpose: &lt;business_purpose>_

_Contact email: &lt;contact_email>_

_Contact phone: &lt;contact_phone>_

_Estimated Usage (requests per month): &lt;estimated_usage>_

_Desired Start Date: &lt;desired_start_date | ASAP>_

## Error Messages

Please see below table containing the list of currently used error codes and their associated messages:

| Name                                      | Code  | Associated Message(s)                                                                                                                                                                                                                                                                                                                                           |
| ----------------------------------------- | ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `SERVER_ERROR_CONTACT_ADMINISTRATOR`      | 11001 | `An error has occurred. Please contact your administrator."<br/><br/>"An error has occurred while verifying that the activity exists. Please contact your administrator.` <br/><br/> `An error has occurred while verifying that activities with the specified external ID and project ID exist. Please contact your administrator.`                            |
| `MEDIA_TYPE_NOT_SUPPORTED`                | 11003 | `Check if Content-Type request header is present and the value is application/json`                                                                                                                                                                                                                                                                             |
| `ACTIVITY_NOT_FOUND`                      | 11005 | `No such Activity {activityId}`                                                                                                                                                                                                                                                                                                                                 |
| `UNABLE_TO_UPDATE_ACTIVITY`               | 11007 | `ERROR - Incorrect value specified: {0} for type: {1}`<br/><br/>`ERROR - Unrecognized property specified for '''add''' operation: {0}`<br/><br/>`ERROR - Unrecognized property specified for 'replace' operation`<br/><br/>`ERROR - A JSON processing error occurred"<br/><br/>"ERROR - 'path' missing leading slash`<br/><br/>`ERROR - 'path' cannot be empty` |
| `PLANNED_END_BEFORE_PLANNED_START`        | 11009 | `plannedEndDateTimeUtc ({plannedEndDateTimeUtc}) cannot be before plannedStartDateTimeUtc ({plannedStartDateTimeUtc}).`                                                                                                                                                                                                                                         |
| `NON_EDITABLE_FIELD`                      | 11010 | `{fieldName} field cannot be updated`                                                                                                                                                                                                                                                                                                                           |
| `MALFORMED_REQUEST`                       | 11011 | `Malformed request`                                                                                                                                                                                                                                                                                                                                             |
| `INVALID_ESTIMATED_DURATION`              | 11012 | `Estimated Duration ({duration}) is not valid.`                                                                                                                                                                                                                                                                                                                 |
| `NO_ACCESS_TO_PROJECT`                    | 11015 | `You do not have access to the project with projectId {projectId}`                                                                                                                                                                                                                                                                                              |
| `NOT_AUTHORISED_TO_PERFORM_ACTION`        | 11016 | `User is not authorized to complete this action.`                                                                                                                                                                                                                                                                                                               |
| `INVALID_REGION`                          | 11017 | `The provided tcRegion {tcRegion} is not a valid region.`                                                                                                                                                                                                                                                                                                       |
| `INVALID_VALUE_SPECIFIED`                 | 11018 | `ERROR - Invalid values for the following fields: {0}` <br/><br/>`ERROR - Incorrectly formatted value specified: {0} for type: {1}`<br/><br/>`ERROR - Unrecognized value given for type: {0}`                                                                                                                                                                   |
| `INVALID_REQUEST`                         | 11021 | `ERROR - Invalid Value : {}, specified for Query Param : {}`                                                                                                                                                                                                                                                                                                    |
| `UNRECOGNIZED_PROPERTY`                   | 11024 | `ERROR - Unrecognized property specified: {0}`                                                                                                                                                                                                                                                                                                                  |
| `INVALID_HTTP_METHOD`                     | 11022 | `ERROR : HTTP method POST is not supported for this request. Supported HTTP methods for this request are : GET, PATCH`                                                                                                                                                                                                                                          |
| `INVALID_TIMESTAMP_SPECIFIED`             | 11008 | `ERROR - Incorrect Timestamp Value: {} specified for: {}`                                                                                                                                                                                                                                                                                                       |
| `ACTIVITY_PROGRESS_MEASUREMENT_NOT_FOUND` | 21005 | `Activity Progress Measurement not found.`                                                                                                                                                                                                                                                                                                                      |
| `UNABLE_TO_CREATE_PROGRESS_MEASUREMENT`   | 21006 | `ERROR : {field} - {violationDetails}, {field2} - {violationDetails2}, ...`                                                                                                                                                                                                                                                                                     |
| `INVALID_PROGRESS_DURATION`               | 21010 | `Progress Duration ({duration}) is not valid.`                                                                                                                                                                                                                                                                                                                  |
| `INVALID_REQUEST`                         | 21012 | `ERROR - Invalid Value : {}, specified for Query Param : {}`                                                                                                                                                                                                                                                                                                    |
| `NO_ACTIVITIES_FOUND`                     | 21013 | `No activities found with external ID {externalId} and projectId ID {projectId}`                                                                                                                                                                                                                                                                                |
| `PROJECTS_DO_NOT_MATCH`                   | 21014 | `Project IDs {providedProjectId} and {retrievedProjectId} do not match.`                                                                                                                                                                                                                                                                                        |

## Limitations

The following functionality is not yet supported:

- Deletion of activities and progress data
- Posting of domain events to the Events API (for Progress measurements)
- Pagination, sorting, filtering, and advanced querying of activities and progress data
- The number of activities retrieved from the data store is a maximum of 500 of the most recently created activities
- Monitoring - traceability is limited due to missing infrastructure. This means it could take us longer to address consumer-reported issues if reproduction steps are not provided (said missing infrastructure is in the process of being set up)

_For further information and questions, please contact the Aurora team via email at [civil-sw-aurora-ug@trimble.com](mailto:civil-sw-aurora-ug@trimble.com)._
