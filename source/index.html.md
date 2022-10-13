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
X-Evocalize-Timestamp: 1604094273
X-Evocalize-Signature: 690a0ac5a5a219bb4a773f5bc116a32553be4e8380845d854f07b5e8471bc954
Content-Type: application/json
```

| Header                    | Required | Description                                                                                                                                             |
| ------------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| X-Evocalize-Client-Key-Id | true     | ID of the Client Key provided by Evocalize.                                                                                                             |
| X-Evocalize-Timestamp     | true     | Unix time since epoch in seconds, e.g. October 30, 2020 @ 9:44pm (UTC) => 1604094273. Requests with a timestamp over a minute old will not be accepted. |
| X-Evocalize-Signature     | true     | Signature of the payload. See Signing Requests below.                                                                                                   |
| Content-Type              | true     | `application/json`                                                                                                                                      |

## Signing Requests:

> Request signing example

```kotlin
// Concatenate all the parts
val signatureParts = StringBuilder()
  .append(request.servletPath)
  .append("\n")
  // Omit this block if the request does not have a body
  .append(request.body())
  .append("\n")
  .append(request.headers["X-Evocalize-Timestamp"])
  .append("\n")
  // append the client key secret
  .append(myClientKeySecret)
  .toString()

// Take this value and pass it as the value for the header key "X-Evocalize-Signature"
val signature = Hashing.sha256().hashString(signatureParts, Charsets.UTF_8)
```

For security and identity purposes, we require partners to sign all API requests. The methodology is straight forward. Join the following values with a new line character between each item

- UrlPath (including path params if present)
- The body of the request (omit if your request does not contain a body, e.g. a GET request)
- Value of the timestamp used in the X-Evocalize-Timestamp Header
- Client Key Secret (this is a secret that will be provided by Evocalize)

<aside class="notice">All code examples below assume that you are passing the required headers described in this section</aside>

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
