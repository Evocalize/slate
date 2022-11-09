# Content Access API V2
You can use our standard access control system to control access
to content items. See the [Access Control](#access-control) section for more
details.

## Get grant

Coming soon

## Get grants

Coming soon

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

### Request/Response
See [Access Control](#access-control) for full details. `READ` is the only
valid permission for content access.

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

This endpoint allows you to pass a JSON object holding multiple grants for upserting.
Batches are limited to 1000 grants at a time. The creation option is asynchronous - rather
than returning the newly created grants, you will receive a `report_id` which can be used
to track status of the requests as they are processed in our system. Reports are stored for 30 days.

**Note: Empty permissions lists are currently not supported by
this endpoint. Use the DELETE endpoint instead.**

### HTTP Request

`POST management/v1/repository/{repositoryId}/grants`

### URL Params

| URL Param    | Type   | Required | Description                                                             |
|--------------|--------|----------|-------------------------------------------------------------------------|
| repositoryId | String | true     | The ID of the content repository whose grants you would like to update. |

## Delete grants

Coming soon
