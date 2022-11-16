# Content Access API V2
You can use our standard access control system to control access
to content items. See the [Access Control](#access-control) section for more
details.

## Get grants

> Example grantee filter object

```json
{
  "grantees": [
    {
      "type": "USER_IN_GROUP",
      "userId": "exampleUserId",
      "groupId": "exampleGroupId"
    },
    {
      "type": "USER_IN_GROUP",
      "userId": "exampleUserId",
      "groupId": "exampleGroupId"
    },
    {
      "type": "USER_IN_GROUP",
      "userId": "exampleUserId2",
      "groupId": "exampleGroupId"
    },
    {
      "type": "ORGANIZATION"
    },
    {
      "type": "USER",
      "userId": "exampleUserId3"
    },
    {
      "type": "GROUP",
      "groupId": "exampleGroupId3"
    },
    {
      "type": "GROUP_ROLE",
      "groupId": "exampleGroupId",
      "groupRole": "group_user"
    }
  ]
}
```

> Get all grants for the content repository

```
GET management/v1/repository/{repositoryId}/grants
```

> Get grants for the content repository, filtered by the example grantee filter object

```
// Note: If you percent decode the value for the filterByGrantee query parameter,
// you'll see that it decodes to the example grantee filter object.
GET management/v1/repository/{repositoryId}/grants?filterByGrantee=%7B%0D%0A++%22grantees%22%3A+%5B%0D%0A++++%7B%0D%0A++++++%22type%22%3A+%22USER_IN_GROUP%22%2C%0D%0A++++++%22userId%22%3A+%22exampleUserId%22%2C%0D%0A++++++%22groupId%22%3A+%22exampleGroupId%22%0D%0A++++%7D%2C%0D%0A++++%7B%0D%0A++++++%22type%22%3A+%22USER_IN_GROUP%22%2C%0D%0A++++++%22userId%22%3A+%22exampleUserId%22%2C%0D%0A++++++%22groupId%22%3A+%22exampleGroupId%22%0D%0A++++%7D%2C%0D%0A++++%7B%0D%0A++++++%22type%22%3A+%22USER_IN_GROUP%22%2C%0D%0A++++++%22userId%22%3A+%22exampleUserId2%22%2C%0D%0A++++++%22groupId%22%3A+%22exampleGroupId%22%0D%0A++++%7D%2C%0D%0A++++%7B%0D%0A++++++%22type%22%3A+%22ORGANIZATION%22%0D%0A++++%7D%2C%0D%0A++++%7B%0D%0A++++++%22type%22%3A+%22USER%22%2C%0D%0A++++++%22userId%22%3A+%22exampleUserId3%22%0D%0A++++%7D%2C%0D%0A++++%7B%0D%0A++++++%22type%22%3A+%22GROUP%22%2C%0D%0A++++++%22groupId%22%3A+%22exampleGroupId3%22%0D%0A++++%7D%2C%0D%0A++++%7B%0D%0A++++++%22type%22%3A+%22GROUP_ROLE%22%2C%0D%0A++++++%22groupId%22%3A+%22exampleGroupId%22%2C%0D%0A++++++%22groupRole%22%3A+%22group_user%22%0D%0A++++%7D%0D%0A++%5D%0D%0A%7D
```

> Get grants for a specific content item

```
GET management/v1/repository/{repositoryId}/items/{itemId}/grants
```

> Get grants for a specific content item, filtered by the example grantee filter object

```
// Note: If you percent decode the value for the filterByGrantee query parameter,
// you'll see that it decodes to the example grantee filter object.
GET management/v1/repository/{repositoryId}/items/{itemId}/grants?filterByGrantee=%7B%0D%0A++%22grantees%22%3A+%5B%0D%0A++++%7B%0D%0A++++++%22type%22%3A+%22USER_IN_GROUP%22%2C%0D%0A++++++%22userId%22%3A+%22exampleUserId%22%2C%0D%0A++++++%22groupId%22%3A+%22exampleGroupId%22%0D%0A++++%7D%2C%0D%0A++++%7B%0D%0A++++++%22type%22%3A+%22USER_IN_GROUP%22%2C%0D%0A++++++%22userId%22%3A+%22exampleUserId%22%2C%0D%0A++++++%22groupId%22%3A+%22exampleGroupId%22%0D%0A++++%7D%2C%0D%0A++++%7B%0D%0A++++++%22type%22%3A+%22USER_IN_GROUP%22%2C%0D%0A++++++%22userId%22%3A+%22exampleUserId2%22%2C%0D%0A++++++%22groupId%22%3A+%22exampleGroupId%22%0D%0A++++%7D%2C%0D%0A++++%7B%0D%0A++++++%22type%22%3A+%22ORGANIZATION%22%0D%0A++++%7D%2C%0D%0A++++%7B%0D%0A++++++%22type%22%3A+%22USER%22%2C%0D%0A++++++%22userId%22%3A+%22exampleUserId3%22%0D%0A++++%7D%2C%0D%0A++++%7B%0D%0A++++++%22type%22%3A+%22GROUP%22%2C%0D%0A++++++%22groupId%22%3A+%22exampleGroupId3%22%0D%0A++++%7D%2C%0D%0A++++%7B%0D%0A++++++%22type%22%3A+%22GROUP_ROLE%22%2C%0D%0A++++++%22groupId%22%3A+%22exampleGroupId%22%2C%0D%0A++++++%22groupRole%22%3A+%22group_user%22%0D%0A++++%7D%0D%0A++%5D%0D%0A%7D
```

> Get grants response

```json
{
  "data": {
    "grants": [
      {
        "grantee": {
          "type": "GROUP",
          "groupId": "exampleGroupId"
        },
        "permissions": [
          "READ"
        ],
        "objectId": "exampleItemId"
      },
      {
        "grantee": {
          "type": "USER",
          "userId": "exampleUserId"
        },
        "permissions": [
          "READ"
        ],
        "objectId": "exampleItemId2"
      }
    ]
  },
  "nextPageToken": "next_page_token", // only present when there is another page
  "previousPageToken": "previous_page_token" // only present when there is a previous page
}   
```

You can retrieve existing grants for a specific content item,
or across the entire content repository. You can also filter
those grants by grantee.

### HTTP Request

Across the entire repository: `GET management/v1/repository/{repositoryId}/grants`

For a specific item: `GET management/v1/repository/{repositoryId}/items/{itemId}/grants`

### URL Params

| URL Param    | Type   | Required                              | Description                                                            |
|--------------|--------|---------------------------------------|------------------------------------------------------------------------|
| repositoryId | String | true                                  | The ID of the content repository whose grants you wish to view.        |
| itemId       | String | true (if using the item specific url) | The ID of the content item that you would like to retrieve grants for. |

### Request Query Params

| URL Param       | Type                  | Required | Description                                                                                                                                                                                                        |
|-----------------|-----------------------|----------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| filterByGrantee | [Grantees](#grantees) | false    | A percent-encoded (as all query parameters should be) json object that specifies the grantees you would like to filter on. This object has a single `grantees` field that is a list of [Grantee](#grantee) objects | 

### Response properties
An example is shown to the right. The `data` property contains a json object holding all the grants you retrieved.
See [Access Control](#access-control) for full details. `READ` is the only valid permission for content access.

| Field  | Nullable | Type                            | Description                     |
|--------|----------|---------------------------------|---------------------------------|
| grants | false    | List of [Grant](#grant) objects | A list of the grants retrieved. |

## Grant Upsert

> Grant upsert request

```json
{
  "grantee": {
    "type": "USER",
    "userId": "exampleUserId"
  },
  "permissions": ["READ"]
}
```

> Grant upsert response

```json
{
  "data": {
    "grantee": {
      "type": "USER",
      "userId": "exampleUserId"
    },
    "permissions": ["READ"],
    "objectId": "exampleContentItemId"
  }
}
```

You can upsert a grant on a content item. The upsert is performed
based off of the grantee and content item ID. So only the grant for
the given grantee/content-item-ID combo will be impacted by
your request.

**Note: Empty permissions lists are currently not supported by
this endpoint. Use the DELETE endpoint instead.**

### HTTP Request

`POST management/v1/repository/{repositoryId}/items/{itemId}/grant`

### URL Params

| URL Param    | Type   | Required | Description                                                     |
|--------------|--------|----------|-----------------------------------------------------------------|
| repositoryId | String | true     | The ID of the content repository that the content item live in. |
| itemId       | String | true     | The ID of the content item that you'd like to grant access to.  |

### Request
The request body is a single Grant object. An example is shown to the right. See [Grant](#grant) for full
details. `READ` is the only valid permission for content access.

### Response
The `data` property contains the Grant object that you upserted.
See [Grant](#grant) for full details. `READ` is the only valid permission for content access.

## Batch Grant Upsert

> Batch grant upsert request

```json
{
  "grants": [
    {
      "grantee": {
        "type": "USER",
        "userId": "exampleUserId"
      },
      "permissions": ["READ"],
      "objectId": "exampleContentItemId1"
    },
    {
      "grantee": {
        "type": "GROUP",
        "groupId": "exampleGroupId"
      },
      "permissions": ["READ"],
      "objectId": "exampleContentItemId2"
    }
  ]
}
```

> Batch grant upsert response

```json
{
  "data": {
    "reportId": "HG3SrEndQch8dAZbjwBV"
  }
}
```

This endpoint allows you to pass a [Grants](#grants) object holding multiple grants for upserting.
Batches are limited to 1000 grants at a time. The creation option is asynchronous - rather
than returning the newly created grants, you will receive a `report_id` which can be used
to track status of the requests as they are processed in our system. Reports are stored for 30 days.
See the [Batch Report API](#batch-report-api) docs for more details.

**Note: Empty permissions lists are currently not supported by
this endpoint. Use the DELETE endpoint instead.**

### HTTP Request

`POST management/v1/repository/{repositoryId}/grants`

### URL Params

| URL Param    | Type   | Required | Description                                                             |
|--------------|--------|----------|-------------------------------------------------------------------------|
| repositoryId | String | true     | The ID of the content repository whose grants you would like to update. |

### Request

The request body is our standard Grants object. See [Access Control](#grants) for more details.

## Delete grants

> Optional request body to delete grants for specific grantees

```json
// This request body is the same whether you are deleting
// grants across the entire repository or for a specific
// item.
{
  "grantees": [
    {
      "type": "USER_IN_GROUP",
      "userId": "exampleUserId",
      "groupId": "exampleGroupId"
    },
    {
      "type": "USER_IN_GROUP",
      "userId": "exampleUserId",
      "groupId": "exampleGroupId"
    },
    {
      "type": "USER_IN_GROUP",
      "userId": "exampleUserId2",
      "groupId": "exampleGroupId"
    },
    {
      "type": "ORGANIZATION"
    },
    {
      "type": "USER",
      "userId": "exampleUserId3"
    },
    {
      "type": "GROUP",
      "groupId": "exampleGroupId3"
    },
    {
      "type": "GROUP_ROLE",
      "groupId": "exampleGroupId",
      "groupRole": "group_user"
    }
  ]
}
```

> Delete grants response

```json
{
  "data": {
    "numberOfGrantsDeleted": 42
  }
}
```

You can delete grants for a specific content item,
or across the entire content repository. You can also
delete grants for specific grantees by providing a
[Grantees object](#grantees) as the request body.

### HTTP Request

Across the entire repository: `DELETE management/v1/repository/{repositoryId}/grants`

For a specific item: `DELETE management/v1/repository/{repositoryId}/items/{itemId}/grants`

### URL Params

| URL Param    | Type   | Required                              | Description                                                          |
|--------------|--------|---------------------------------------|----------------------------------------------------------------------|
| repositoryId | String | true                                  | The ID of the content repository whose grants you want to delete.    |
| itemId       | String | true (if using the item specific url) | The ID of the content item that you would like to delete grants for. |

### Request Body
The request body is optional and is only needed if you want
to limit the delete operation to specific grantees. The body
is our standard [Grantees](#grantees) object.

### Response properties
An example is shown to the right. The `data` property contains a json object with a single property:

| Field                 | Nullable | Type | Description                             |
|-----------------------|----------|------|-----------------------------------------|
| numberOfGrantsDeleted | false    | Int  | The number of grants that were deleted. |
