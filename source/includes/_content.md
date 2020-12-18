# Content API

## Get Table

> Get Table Response

```json
{
  "data": {
    "id": "table_id",
    "name": "Table Name",
    "columns": [
      {
        "id": "column_id",
        "primaryKey": true,
        "nullable": false,
        "indexed": true,
        "type": "TEXT",
        "defaultValue": null,
        "unique": true
      }
      // ...
    ]
  }
}
```

Returns a single table and associated columns.

### HTTP Request

`GET management/v1/table/{tableId}`

### Response Codes:

- `200 OK ` status code on sucess
- `404 NOT FOUND` when it can't locate the table.

## Get Tables

> Get Tables Response

```json
{
  "data": [
    {
      "id": "table_id",
      "name": "Table Name"
    }
    // ...
  ]
}
```

Returns a list of tables.

### HTTP Request

`GET management/v1/tables`

### Response Codes:

- `200 OK ` status code on sucess

## Get Item

> Get Item Response

```json
{
  "data": [
    {
      "id": "item_id",
      "name": "Item Name",
      "value": 42
    }
    // ...
  ]
}
```

Returns a content item. The JSON object in the `data` field of the response will contains fields defined by the table's columns.

### HTTP Request

`GET management/v1/table/{tableId}/item/{itemId}`

### Response Codes:

- `200 OK ` status code on sucess
- `404 NOT FOUND` when it can't locate the item

## Create Or Update Item

> Create Or Update Item Request

```json
{
  "id": "item_id",
  "name": "Item Name",
  "value": 42
  // ...
}
```

> Create Or Update Item Response

```json
{
  "data": {
    "id": "item_id",
    "name": "Item Name",
    "value": 42
    // ...
  }
}
```

Creates or updates a content item. The JSON request object should contain fields defined by the table's columns.

### HTTP Request

`POST management/v1/table/{tableId}/item`

### Response Codes:

- `200 OK ` status code on sucess

## Delete Item

> Delete Item Response

```json
{
  "data": {
    "id": "item_id",
    "name": "Item Name",
    "value": 42
    // ...
  }
}
```

Deletes a content item.

### HTTP Request

`DELETE management/v1/table/{tableId}/item/{itemId}`

### Response Codes:

- `200 OK ` status code on sucess
- `404 NOT FOUND` when it can't locate the item
