# Batch Report API

## Get Report

> Get Report Response

```json
{
  "data": {
    "totalItems": 750,
    "remainingItems": 500,
    "completedItems": 250,
    "successfulItems": 250,
    "errorItems": 0,
    "isCompleted": false
  }
}
```

For batch endpoints we return a `report_id` string instead of the created objects. This endpoint allows you to view the status of a given batch. We retain this data for 30 days after the report was generated.

### HTTP Request

`GET management/v1/items/report/{reportId}`

### URL Params

| URL Param | Required | type   | Description                                      |
| --------- | -------- | ------ | ------------------------------------------------ |
| reportId  | true     | String | The ID of the report you are wanting to retrieve |

**Response Codes**:

`200 OK`
`404 NOT FOUND` - When we are unable to locate a report with that ID.


## Get Errors Batch Rquests

> Get Report Response

```json
{
  "data": [
    {
      "input": {
        "id": "test_user_id",
        "name": "Test User",
        "email": "example@evocalize.com",
        "groups": [
          {
            "id": "seattle_office",
            "name": "Seattle Office",
            "role": "group_admin"
          }
        ]
      },
      "errorMessage":"Duplicate user create request detected: user with example@evocalize.com has already been associated to another user id.",
      "restErrorCode": "EV_BAD_REQUEST_DUPLICATE" //this is not always present. 
    }
  ],
  "nextPageToken": "test_next_page_token", // only present when there is another page
  "previousPageToken": "test_previous_page_token" // only present when there is a previous page
}
```

This endpoint displays error information for items in a given batch (retrieved by passing the `report_id` similar to the above call) that were not successful. The error messages and error codes generally are the ones you would see if using the non batch endpoints. For debugging purposes we return the input used so you can amend any mistakes. If there are no errors associated with a report this endpoint will return an empty array for the data field.

### HTTP Request

`GET management/v1/items/report/{reportId}/errors`

### URL Params

| URL Param | Required | type   | Description                                      |
| --------- | -------- | ------ | ------------------------------------------------ |
| reportId  | true     | String | The ID of the report you are wanting to retrieve |

**Response Codes**:

`200 OK`