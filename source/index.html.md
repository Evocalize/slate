---
title: Management API Reference

includes:
  - _jwt_token
  - _user_and_group_management_api
  - _content_management_api
  - _content_access_management
  - _batch_report_api
  - _webhook_integration
  - _facebook_audiences
  - _audiences
  - _media 
  - _errors
  - _architecture_deep_linking
---

# Introduction

Welcome to the Evocalize management API.

# Request And Response Info

## API Endpoint

`https://office-api.evocalize.com/`

## Required Headers

> Example Headers

```
X-Evocalize-Client-Key-Id: a5646c38-fc29-11e9-8f0b-362b9e155667
X-Evocalize-Client-Key: 690a0ac5a5a219bb4a773f5bc116a325 
Content-Type: application/json
```

| Header                    | Required | Description                                                                                                                                             |
| ------------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| X-Evocalize-Client-Key-Id | true     | ID of the Client Key provided by Evocalize.                                                                                                             |
| X-Evocalize-Client-Key    | true     | The Secret Key Provided to you by Evocalize                                                                                                             |  
| Content-Type              | true     | `application/json`                                                                                                                                      |

## Response Format

> JSON Response Schema

```json
{
  "data": {
    // Request Specifc JSON object
  },
  "errors": [
    {
      "message": "String",
      "code": "String",
      "field" : "String", // Omitted if null
      "details": {        // Omitted if null
        // JSON object
      }
    }
  ],
  "nextPageToken": "String",
  "previousPageToken": "String",
  "metadata": {
    // JSON object
  },
```

> Example Success Response:

```json
{
  "data": {
    // Request Specifc JSON Response
  }
}
```

> Example Error Response:

```json
{
  "errors": [
    {
      "message": "Unauthorized Request",
      "code": "EV_UNAUTHORIZED_MISSING_HEADERS"
    }
  ]
}
```

All requests are returned as JSON wrapped in a standard response format. The Schema section shows all the potential fields that we could return. Do note that we do not include null values. The most common responses will look like the examples provided.

| Schema Field      | Always Present | Description                                                                                                                  |
| ----------------- | -------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| data              | false          | Returned for requests that result with 2xx responses.                                                                        |
| errors            | false          | Returned for requests that result in non 2xx responses.                                                                      |
| nextPageToken     | false          | Page token appears for result sets larger than 100 items. Not present on last page of results.                               |
| previousPageToken | false          | Same logic as nextPageToken. Not present on first page of results.                                                           |
| metadata          | false          | JSON object containing extra data around requests and responses. This data is subject to change and should not be relied on. |
