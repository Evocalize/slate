# Content Access API

## Access Basics

For each content item your organization uploads you will need coresponding access records. Access records define who can use the item for their ad campaigns and whether or not they have the ability to edit the item after it is in the system. Content Items have a one to many relationship to access records, although it is completely valid to have a content item only accessible by a single user.

Access to a given content item can be defined at the following levels:
- User based
- Group Based
- Group and Role based

The ability to edit the content item can be granted by passing true for the `canEdit` flag. 

## Get Access Records associated to a repository

> Access Records Response

```json
{
    "data": [
        {
            "contentItemId": "exampleId1",
            "userId": "seattle_user_1",
            "groupId": "seattle_office",
            "canEdit": false,
            "role": "group_user",
            "deleted": false
        }, // more records if applicable
    ],
    "nextPageToken": "next_page_token", // only present when there is another page
    "previousPageToken": "previous_page_token" // only present when there is a previous page
}   
```

If you decline to pass query params - this call will return all access records associated with a given repository. Otherwise it will return return a subset based on the provied query parameters.

**Response Codes**:

- `200 OK`

### HTTP Request

`GET management/v1/repository/{repositorySlug}/access`

### URL Params

| URL Param | Type   | Required  | Description                                    |
| --------- | ------ | --------- | ---------------------------------------------- |
| repositorySlug | String | true | The slug of the content repository whose access records you wish to view. |

### Request Query Params

| URL Param | Type   | Required | Description                                    |
| --------- | ------ | -------- | ---------------------------------------------- |
| userId | string | false | Query for all records that match the provided userId. | 
| groupId | string | false | Query for all records that match the provided groupId, note that this will return records that may or may not have a userId associated with them. |
| role | string | false | Query for all records that match the provided role. If this is omitted, all records will be return according to the other criteria provided. |
| deletedOnly | boolean | false | When this is passed we will only return items that have been deleted. |


## Get Access Records associated to a repository and content item

> Access Records Response

```json
{
    "data": [
        {
            "contentItemId": "exampleId1",
            "userId": "seattle_user_1",
            "groupId": "seattle_office",
            "canEdit": false,
            "role": "group_user",
            "deleted": false
        }, // more records if applicable
    ],
    "nextPageToken": "next_page_token", // only present when there is another page
    "previousPageToken": "previous_page_token" // only present when there is a previous page
}   
```

### HTTP Request

`GET management/v1/repository/{repositorySlug}/access/item/{contentItemId}`

**Response Codes**:

- `200 OK`

### URL Params

| URL Param | Type   | Required | Description                                    |
| --------- | ------ | -------- | ---------------------------------------------- |
| repositorySlug | string | true | The slug of the content repository whose access records you wish to view |
| contentItemId | string | true | The Id of the content item whose access records you wish to view |

### Request Query Params

| URL Param | Type   | Required | Description                                    |
| --------- | ------ | -------- | ---------------------------------------------- |
| userId | string | false | Query for all records that match the provided userId. | 
| groupId | string | false | Query for all records that match the provided groupId, note that this will return records that may or may not have a userId associated with them. |
| role | string | false | Query for all records that match the provided role. If this is omitted, all records will be return according to the other criteria provided. |
| deletedOnly | boolean | false | When this is passed we will only return items that have been deleted. |



## Create/Update a single access record

> Access Record Request Payload: Every `group_user` in the group `seattle_office` can use this content item. Can not edit.

```json
{
    "contentItemId": "exampleId1",
    "userId": null,
    "groupId": "seattle_office",
    "canEdit": false,
    "role": "group_user"
}
```

> Access Record Request Payload: User `seattle_user_1`, in group `seattle_office` has is able to use this content item. Can not edit.

```json
{
    "contentItemId": "exampleId1",
    "userId": "seattle_user_1",
    "groupId": "seattle_office",
    "canEdit": false,
    "role": "group_user"
}
```

> Access Record Response Payload

```json
{
    "data": {
        "contentItemId": "exampleId1",
        "userId": "seattle_user_1",
        "groupId": "seattle_office",
        "canEdit": false,
        "role": "group_user",
        "deleted": false
    }
}
```

### HTTP Request

`POST management/v1/repository/{repositorySlug}/access/item`

**Response Codes**:

- `201 CREATED`

### Create / Update Access Record Request fields

| Field                                          | Nullable | Type   | Description                                                                                             |
| ---------------------------------------------- | -------- | ------ | ------------------------------------------------------------------------------------------------------- |
| contentItemId                                  | false    | string | The content item id for which this access record is responsible for.                                    | 
| userId                                         | true     | string | If you would like access to be constrained to a specifc user, set this field. Otherwise pass null.      |
| groupId                                        | false    | string | The group associated to this content item. If userId is null, then all members of the group (with the given role) have access to the content item |
| role                                           | false    | string | The role this access row applies too. Valid roles are `group_user`, `group_admin`. If you do not want to provide access based on role - pass `any`|
| canEdit                                        | false    | boolean| Sets whether or not the users this access record applies too are allowed to edit the content. |

## Create / Update Access Record Batch

> Batch Create Or Update Request

```json
[
    {
        "contentItemId": "exampleId1",
        "userId": "seattle_user_1",
        "groupId": "seattle_office",
        "canEdit": false,
        "role": "group_user"
    },
    {
        "contentItemId": "exampleId2",
        "userId": "seattle_user_1",
        "groupId": "seattle_office",
        "canEdit": false,
        "role": "group_admin"
    }
  // ...
]
```

> Create and Update Batch Repsonse

```json
{
  "data": {
    "reportId": "HG3SrEndQch8dAZbjwBV"
  }
}
```

This endpoint allows you to pass a JSON Array of Access Record Request objects for creation or updating. Batches are limited to 1000 items at a time. The creation option is asynchronous - rather than returning the newly created items, you will receive a `report_id` which can be used to track status of the requests as they are processed in our system. Reports are stored for 30 days.

The JSON objects passed in the array are the same as the ones listed in Create Or Update Access Record.

### HTTP Request

`POST management/v1/repository/{repositoryId}/access/items`

### URL Params

| URL Param    | Required | type   | Description                                                                 |
| ------------ | -------- | ------ | --------------------------------------------------------------------------- |
| repositoryId | true     | String | The ID of the repository in which you are wanting to create or update items |

**Response Codes**:

- `202 ACCEPTED` - List has been received and submitted for processing.
- `400 BAD REQUEST` - Malformed request.

## Delete Access Record

> Remove Single Access for a given Content Item Record Request Payload (Request Includes `/{contentItemId}`).

```json
{
    "userId": "seattle_user_2",
    "groupId": "seattle_office",
    "role": "group_admin"
}
```

> Remove All Access Records Associated For A Given Content Item and `groupId` Request Payload (Request Includes `/{contentItemId}`).

```json
{
    "userId": null,
    "groupId": "seattle_office",
    "role": null
}
```

> Remove All Access Records Associated For A Given Content Item Request Payload (Request Includes `/{contentItemId}`).

```json
{
    "userId": null,
    "groupId": null,
    "role": null
}
```

> Remove All Access Records For A Given Repository Matching a Specific `userId` (Request Includes `/{contentItemId}`).

```json
{
    "userId": "example_user_id",
    "groupId": null,
    "role": null
}
```

> Remove All Access Records For A Given Repository Matching a Specific `groupId` (Request Does Not Include `/{contentItemId}`).

```json
{
    "userId": null,
    "groupId": "seattle_office",
    "role": null
}
```

> Remove All Access Records For A Given Repository Matching a Specific `groupId` and `userId` (Request Does Not Include `/{contentItemId}`).

```json
{
    "userId": "example_user_id",
    "groupId": "seattle_office",
    "role": null
}
```

> Remove All Access Records In a Respository (Request Does Not Include `/{contentItemId}`)

```json
{
    "userId": null,
    "groupId": null,
    "role": null
}
```

> Response

```json
{
    "data": {
        "numberOfAccessRecordsDeleted": 5
    }
}   
```

Removing access follows a very similar pattern to querying for them. One important difference is that you _must_ always pass each field, with `null` being a valid value. Example: If you wanted to remove all the access records for a given `contentItemId` then you would need to set `userId`, `groupId` and `role` as null. If you omit `contentItemId` the operation is applied against the entire `repository`. If your query does not match any access records the response will indicate `0`.

### HTTP Request

`DELETE management/v1/content/{repositorySlug}/{contentItemId}`,
`DELETE management/v1/content/{repositorySlug}`

**Response Codes**:

- `200 OK`

### URL Params

| URL Param | Type   | Required | Description                                    |
| --------- | ------ | -------- | ---------------------------------------------- |
| contentItemId | string | false | The Id of the content item whose access records you are removing. |

### Delete Access Record Fields

| Field                                          | Nullable | Type   | Description                                                                                             |
| ---------------------------------------------- | -------- | ------ | ------------------------------------------------------------------------------------------------------- |
| userId                                         | true     | string | The user id for the associated access record.                                                           |
| groupId                                        | true     | string | The group id for the associated access record.                                                          |
| role                                           | true     | string | The role for the associated access record.                                                              |
