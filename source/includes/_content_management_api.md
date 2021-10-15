# Content Management API

## Get Repository

> Get Repository Response

```json
{
  "data": {
    "id": "repository_id",
    "name": "Repository Name",
    "description": " Repository Description",
    "isWritable": true,
    "fields": [
      {
        "name": "field_name",
        "isNullable": false,
        "type": "string" // can be "string", "number", "boolean", or "date_time"
      }
      // ...
    ]
  }
}
```

Returns a single repository and associated fields.

### HTTP Request

`GET management/v1/repository/{repositoryId}`

### URL Params

| URL Param    | Required | type   | Description                                          |
| ------------ | -------- | ------ | ---------------------------------------------------- |
| repositoryId | true     | String | The ID of the repository you are wanting to retrieve |

### Response Codes:

- `200 OK ` status code on sucess
- `404 NOT FOUND` when it can't locate the repository.

## Get Repositories

> Get Repositories Response

```json
{
  "data": [
    {
      "id": "repository_id",
      "name": "Repository Name"
    }
    // ...
  ]
}
```

Returns a list of repositories.

### HTTP Request

`GET management/v1/repositories`

### Response Codes:

- `200 OK ` status code on sucess

## Get Item

> Get Item Response

```json
{
  "data": {
    "values": {
      "id": "item_id",
      "repository_field_name_1": "some value here",
      "repository_field_name_2": 42
      // ...
    }
  }
}
```

Returns a content item. The JSON object in the `values` field of the response will contains fields defined by the repository's fields.

### HTTP Request

`GET management/v1/repository/{repositoryId}/item/{itemId}`

### URL Params

| URL Param    | Required | type   | Description                                          |
| ------------ | -------- | ------ | ---------------------------------------------------- |
| repositoryId | true     | String | The ID of the repository you are wanting to retrieve |
| itemId       | true     | String | The ID of the item you are wanting to retrieve       |

### Response Codes:

- `200 OK ` status code on sucess
- `404 NOT FOUND` when it can't locate the item

## Create Or Update Item

> Create Or Update Item Request

```json
{
  "values": {
    "id": "item_id",
    "repository_field_name_1": "some value here",
    "repository_field_name_2": 42
    // ...
  }
}
```

> Create Or Update Item Response

```json
{
  "data": {
    "values": {
      "id": "item_id",
      "repository_field_name_1": "some value here",
      "repository_field_name_2": 42
      // ...
    }
  }
}
```

Creates or updates a content item. The JSON request object should contain fields defined by the repository's fields.

### HTTP Request

`POST management/v1/repository/{repositoryId}/item`

### URL Params

| URL Param    | Required | type   | Description                                                                   |
| ------------ | -------- | ------ | ----------------------------------------------------------------------------- |
| repositoryId | true     | String | The ID of the repository in which you are wanting to create or update an item |

### Response Codes:

- `200 OK ` status code on sucess

## Create Or Update Item Batch

> Batch Create Or Update Request

```json
[
  {
    "values": {
      "id": "item_id",
      "repository_field_name_1": "some value here",
      "repository_field_name_2": 42
      // ...
    }
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

This endpoint allows you to pass a JSON Array of Item Request objects for creation or updating. Batches are limited to 1000 items at a time. The creation option is asynchronous - rather than returning the newly created items, you will receive a `report_id` which can be used to track status of the requests as they are processed in our system. Reports are stored for 30 days.

The JSON objects passed in the array are the same as the ones listed in Create Or Update Item.

### HTTP Request

`POST management/v1/repository/{repositoryId}/items`

### URL Params

| URL Param    | Required | type   | Description                                                                 |
| ------------ | -------- | ------ | --------------------------------------------------------------------------- |
| repositoryId | true     | String | The ID of the repository in which you are wanting to create or update items |

**Response Codes**:

- `202 ACCEPTED` - List has been received and submitted for processing.
- `400 BAD REQUEST` - Malformed request.

## Delete Item

> Delete Item Response

```json
{
  "data": {
    "values": {
      "id": "item_id",
      "repository_field_name_1": "some value here",
      "repository_field_name_2": 42
      // ...
    }
  }
}
```

Deletes a content item.

### HTTP Request

`DELETE management/v1/repository/{repositoryId}/item/{itemId}`

### URL Params

| URL Param    | Required | type   | Description                                                         |
| ------------ | -------- | ------ | ------------------------------------------------------------------- |
| repositoryId | true     | String | The ID of the repository in which you are wanting to delete an item |
| itemId       | true     | String | The ID of the item you are wanting to delete                        |

### Response Codes:

- `200 OK ` status code on sucess
- `404 NOT FOUND` when it can't locate the item

## Delete Item Batch

> Batch Delete Request

```json
[
  "item_id_1",
  "item_id_2"
  // ...
]
```

> Delete Batch Repsonse

```json
{
  "data": {
    "reportId": "HG3SrEndQch8dAZbjwBV"
  }
}
```

This endpoint allows you to pass a JSON Array of Item IDs for deletion. Batches are limited to 1000 items at a time. The creation option is asynchronous - rather than returning the newly created items, you will receive a `report_id` which can be used to track status of the requests as they are processed in our system. Reports are stored for 30 days.

The JSON objects passed in the array are the same as the ones listed in Create Or Update Item.

### HTTP Request

`DELETE management/v1/repository/{repositoryId}/items`

### URL Params

| URL Param    | Required | type   | Description                                                       |
| ------------ | -------- | ------ | ----------------------------------------------------------------- |
| repositoryId | true     | String | The ID of the repository in which you are wanting to delete items |

**Response Codes**:

- `202 ACCEPTED` - List has been received and submitted for processing.
- `400 BAD REQUEST` - Malformed request.
