# Facebook Audiences API

**[DEPRECATED] Replaced by the Audience Manager API.**

Facebook Audiences API will continue to work until Evocalize has determined a date to sunset these endpoints.
Facebook audiences added successfully via these endpoints will be available in the Audience Manager. View the Audience
Manager API for more information on creating and retrieving audiences including Facebook audiences.

## Get Facebook Audiences

> Get Facebook Audiences Response

```json
{
  "data": [
    {
      "id": 1462332663628108358,
      "facebookAudienceId": "someAudienceIdFromFacebook",
      "name": "Test Audience",
      "userId": "idOfSomeUserInEvocalize",
      "type": "CUSTOM_AUDIENCE"
    },
    {
      "id": 1462333796459287111,
      "facebookAudienceId": "someAudienceIdFromFacebook",
      "name": "Test Audience 2",
      "userId": null,
      "type": "CUSTOM_AUDIENCE"
    }
  ],
  "nextPageToken": "test_next_page_token (only present when there is another page)",
  "previousPageToken": "test_previous_page_token (only present when there is another page)"
}
```

Retrieves all facebook audiences you have stored in Evocalize.
This is a paginated response - returning 100 results per page
with optional page tokens to go to the next page and previous page if they exist.

### HTTP Request

`GET management/v1/facebookaudiences`

### Request Params

| Param     | Required | Description                                                                                                                                       |
| --------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| pageToken | false    | The page token (taken from either `nextPageToken`, or `previousPageToken` provided in the response) for the page you of results you want to view. |

**Response Codes**:

- `200 OK` - NOTE: will return an empty array and this status code if there are no audiences present.

## Add Existing Facebook Audience

> Add Request Example

```json
{
  "facebookAudienceId": "someAudienceIdFromFacebook",
  "name": "Test Audience",
  "userId": "idOfSomeUserInEvocalize"
}
```

> Add Response Example

```json
{
  "data": {
    "id": 1462333796459287111,
    "facebookAudienceId": "someAudienceIdFromFacebook",
    "name": "Test Audience",
    "userId": "idOfSomeUserInEvocalize",
    "type": "CUSTOM_AUDIENCE"
  }
}
```

Use this endpoint to add an existing facebook audience to Evocalize.

### HTTP Request

`POST management/v1/facebookaudience`

### Add Facebook Audience Request Properties

| Field              | Required | Type    | Description                                                             |
| -------------------| -------- | ------- | ----------------------------------------------------------------------- |
| facebookAudienceId | true     | String  | The ID of the audience in Facebook. |
| name               | true     | String  | The name of the audience. |                                                                     |
| userId             | false    | String  | The ID of the user in Evocalize who owns this audience. The audience will only display for this user. If null, then the audience will display for everyone. |                                                                |

## Bulk Add Existing Facebook Audiences

> Bulk Add Request Example

```json
{
  "audiences": [
    {
      "facebookAudienceId": "foo",
      "name": "Test Audience 1"
    },
    {
      "facebookAudienceId": "bar",
      "name": "Test Audience 2",
      "userId": "someUserId"
    }
  ]
}
```

Use this endpoint to add multiple existing facebook audiences to Evocalize
in a single call. You can add up to 500 audiences per call. This
endpoint has no response body.

### HTTP Request

`POST management/v1/facebookaudiences`

### Add Facebook Audience Request Properties

The request body is a json object with one property, `audiences`. `audiences` is an array of facebook
audience objects. Each object has the fields described below:

| Field              | Required | Type    | Description                                                             |
| -------------------| -------- | ------- | ----------------------------------------------------------------------- |
| facebookAudienceId | true     | String  | The ID of the audience in Facebook. |
| name               | true     | String  | The name of the audience. |                                                                     |
| userId             | false    | String  | The ID of the user in Evocalize who owns this audience. The audience will only display for this user. If null, then the audience will display for everyone. |                                                                |
