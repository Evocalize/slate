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
