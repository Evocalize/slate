# User Management API

## Get User

> Get User Response

```json
{
  "data": {
    "id": "test_user_id",
    "name": "Test User",
    "email": "example@evocalize.com",
    "groups": [
      {
        "id": "seattle_office",
        "name": "Seattle Office",
        "role": "group_admin"
      }
      // ...
    ]
  }
}
```

Returns a single user and associated groups.

### HTTP Request

`GET management/v1/user/{userId}`

### URL Params

| URL Param | type   | Required | Description                                    |
| --------- | ------ | -------- | ---------------------------------------------- |
| userId    | String | true     | The ID of the user you are wanting to retrieve |

**Response Codes**:

- `200 OK ` status code on sucess
- `404 NOT FOUND` when it can't locate the user.

## Get Users

> Get Users Response

```json
{
  "data": [
    {
      "id": "1234",
      "name": "Test User 1",
      "email": "test1@evocalize.com"
    },
    {
      "id": "5678",
      "name": "Test User 2",
      "email": "test2@evocalize.com"
    },
    {
      "id": "91011",
      "name": "Test User 2",
      "email": "test3@evocalize.com"
    },
    {
      "id": "1213",
      "name": "Test User 3",
      "email": "test3@evocalize.com"
    }
  ],
  "nextPageToken": "test_next_page_token", // only present when there is another page
  "previousPageToken": "test_previous_page_token" // only present when there is a previous page
}
```

Returns all users. This is a paginated response - returning 100 results per page with optional page tokens to go to the next page and previous page if they exist.

### HTTP Request

`GET management/v1/users`

### Request Params

| Param     | Required | Description                                                                                                                                       |
| --------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| pageToken | false    | The page token (taken from either `nextPageToken`, or `previousPageToken` provided in the response) for the page you of results you want to view. |

**Response Codes**:

- `200 OK` - NOTE: will return an empty array and this status code if there are no users present.

## Create Or Update User

> Create Request Example

```json
{
  "id": "1234",
  "email": "test1@evocalize.com",
  "name": "Test User 1",
  "groups": [
    {
      "groupId": "seattle_office",
      "role": "group_admin"
    }
    // ...
  ]
}
```

> Update Request Example

```json
{
  "id": "1234",
  "email": "test1@evocalize.com",
  "name": "Test User 1",
  "replaceGroups": false,
  "groups": [
    {
      "groupId": "seattle_office",
      "role": "group_admin"
    }
  ]
  // ...
}
```

> Create and Update Repsonse

```json
{
  "id": "1234",
  "email": "test1@evocalize.com",
  "name": "Test User 1",
  "groups": [
    {
      "id": "seattle_office",
      "name": "Seattle Office",
      "role": "group_admin"
    }
    // ...
  ]
}
```

This endpoint allows you to create or update a user and their group associations. You are able to call this endpoint and omit the groups property. In the Create case, the user would be created, but not be associated to any groups. In the case of update we would only update their name and/or email.

The group you want to associate with the user MUST exist prior to making this call.

### HTTP Request

`POST management/v1/user`

### Create User Request Properties

| Field  | Required | Type                                                                                                        | Description                                                             |
| ------ | -------- | ----------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| id     | true     | String                                                                                                      | ID of the user you are creating. This needs to be unique and immutable. |
| email  | true     | Email of the user you are creating.                                                                         |
| name   | true     | Full name of the user you are creating.                                                                     |
| groups | false    | Array of UserGroupAssociation objects. If this is not present on create or update, no groups will be added. |

### Update User Request Properties

| Field         | Required | Type       | Description                                                                                                                                                              |
| ------------- | -------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| id            | true     | String     | ID of the user you are updating.                                                                                                                                         |
| email         | false    | String     | Email of the user you are creating.                                                                                                                                      |
| name          | false    | String     | Full name of the user you are creating.                                                                                                                                  |
| replaceGroups | false    | boolean    | When `replaceGroups` is true, we will REMOVE all groups currently associated to the user, replacing them with the groups provided in the list. By default this is false. |
| groups        | false    | JSON Array | JSON Array of UserGroupAssociation objects. If this is not present on create or update the users group associations will remain unmodified.                              |

### User Group Association Properties

| Field   | Required | Type   | Description                                          |
| ------- | -------- | ------ | ---------------------------------------------------- |
| groupId | true     | String | The Id of the group you want the user associated to. |
| role    | true     | String | The role of the user within the group.               |

**Valid group roles**:

- group_user
- group_admin

**Response Codes**:

- `201 CREATED` - Returning the newly created or updated user.
- `400 BAD REQUEST` - When the request is malformed or you attempt to associate to a non existant group.

## Get User Associated Facebook Pages

> Get User Associated Facebook Pages Response

```json
{
  "data": [
    {
      "id": "12309817412312",
      "facebookPageName": "Big Time Facebook Page",
      "facebookPageId": "1231412312",
      "instagramId": "1231243154123"
    }
  ]
}
```

Returns a list of Facebook pages associated to the user.

### HTTP Request

`GET management/v1/user/{userId}/facebook/pages`

### URL Params

| URL Param | type   | Required | Description                                    |
| --------- | ------ | -------- | ---------------------------------------------- |
| userId    | String | true     | The ID of the user whose pages you want to view |

**Response Codes**:

- `200 OK ` status code on sucess
- `404 NOT FOUND` when it can't locate the user.

## Create Or Update User Batch

> Batch Create Request

```json
[
  {
    "id": "1234",
    "email": "test1@evocalize.com",
    "name": "Test User 1",
    "groups": [
      {
        "groupId": "seattle_office",
        "role": "group_admin"
      }
      // ...
    ]
  }
  // ...
]
```

> Batch Update Request

```json
[
  {
    "id": "1234",
    "email": "test1@evocalize.com",
    "name": "Test User 1",
    "replaceGroups": false,
    "groups": [
      {
        "groupId": "8675309",
        "role": "group_admin"
      }
      // ...
    ]
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

This endpoint allows you to pass a JSON Array of User Request objects for creation or updating. Batches are limited to 1000 items at a time. The creation option is asynchronous - rather than returning the newly created items, you will receive a `report_id` which can be used to track status of the requests as they are processed in our system. Reports are stored for 30 days.

The JSON objects passed in the array are the same as the ones listed in Create Or Update User.

### HTTP Request

`POST management/v1/users`

**Response Codes**:

- `202 ACCEPTED` - List has been received and submitted for processing.
- `400 BAD REQUEST` - Malformed request.

## Deactivate User

> Deactivate User Response

```
No Body returned
```

Deactivates all active programs for a given user placing them in an inactive state.

### HTTP Request

`DELETE management/v1/user/{userId}`

**Response Codes**:

- `202 ACCEPTED`

# Group Management API

## Get Groups

> Get Groups Response

```json
{
  "data": [
    {
      "id": "1234",
      "name": "Test Group 1"
    },
    {
      "id": "5678",
      "name": "Test Group 2"
    },
    {
      "id": "91011",
      "name": "Test Group 3"
    },
    {
      "id": "1213",
      "name": "Test Group 4"
    }
  ],
  "nextPageToken": "test_next_page_token", // only present when there is another page
  "previousPageToken": "test_previous_page_token" // only present when there is a previous page
}
```

Returns all groups. This is a paginated response - returning 100 results per page with optional page tokens to go to the next page and previous page if they exist.

### HTTP Request

`GET management/v1/groups`

### Request Params

| Param     | Required | Description                                                                                                                            |
| --------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| pageToken | false    | The page token (taken from either `nextPageToken`, or `previousPageToken` provided in the response) for the page you want to retrieve. |

**Response Codes**:

- `200 OK` NOTE: will return an empty array and this status code if there are no groups present.

## Create Or Update Group

> Group Create/Update Request

```json
{
  "id": "123",
  "name": "Test Group 1"
}
```

> Group Create/Update Response

```json
{
  "data": {
    "id": "123",
    "name": "Test Group 1"
  }
}
```

Endpoint for creating new groups or updating existing ones.

### HTTP Request

`POST management/v1/group`

### Create / Update Group Properties

| Field | Required | Type   | Description                                         |
| ----- | -------- | ------ | --------------------------------------------------- |
| id    | true     | String | ID of the group you wish to create or update.       |
| name  | true     | String | The human-readable name of the group you are creating or updating. |

**Response Codes**:

- `201 CREATED`

## Create Or Update Group Batch

> Group Create Request

```json
[
  {
    "id": "123",
    "name": "Test Group 1"
  }
  // ...
]
```

> Group Create Response

```json
{
  "data": {
    "reportId": "HG3SrEndQch8dAZbjwBV"
  }
}
```

Endpoint that allows you to pass a JSON Array of Group Request objects for creation. Batches are limited to 1000 items at a time. The creation option is asynchronous - rather than returning the newly created items, you will receive a `report_id` which can be used to track status of the requests as they are processed in our system. Reports are stored for 30 days.

### HTTP Request

`POST management/v1/groups`

**Response Codes**:

- `202 ACCEPTED` - List has been received and submitted for processing.
- `400 BAD REQUEST` - Malformed request.
