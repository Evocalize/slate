# Request And Response

## API Endpoint

`https://partner-api-production.evocalize.com/`

## Requests

Evocalize has a majority of endpoints where the request and response is JSON formatted. At the same time, there are
Multipart-Form endpoints for uploading content for an entity. Specify `Content-Type` accordingly.

| Header       | Required | Description                                                                                  |
|--------------|----------|----------------------------------------------------------------------------------------------|
| Content-Type | true     | For JSON endpoints, `application/json`. For Multipart-Form endpoints, `multipart/form-data`. |

For security and identity purposes, we require partners to provide authentication headers on all API requests. The
required authentication request headers depend on your request's authentication type. Evocalize supports the following
authentication types:

1. Shared Secret
2. Signature

If both authentication types are present in the request, `Shared Secret` takes precedence.

### Shared Secret Authentication

> Request Headers Example (Shared Secret)

```text
X-Evocalize-Client-Key: 690a0ac5a5a219bb4a773f5bc116a325
X-Evocalize-Client-Key-Id: a5646c38-fc29-11e9-8f0b-362b9e155667
```

Pass in the client key and ID of the client key in your request. Evocalize should provide you with this information.

| Header                    | Required | Description                                 |
|---------------------------|----------|---------------------------------------------|
| X-Evocalize-Client-Key    | true     | Client Key provided by Evocalize.           |
| X-Evocalize-Client-Key-Id | true     | ID of the Client Key provided by Evocalize. |

### Signature Authentication:

> Request Headers Example (Signature)

```text
X-Evocalize-Client-Key-Id: a5646c38-fc29-11e9-8f0b-362b9e155667
X-Evocalize-Timestamp: 1667231735360
X-Evocalize-Signature: ba60ba1619762b3a06c88c4ed4e700c8a02aa1bf4a2eead8b41f8015c7902034
``` 

> Request Signing Example

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

Join the following values with a new line character between each item

- UrlPath (including path params if present)
- The body of the request (omit if your request does not contain a body, e.g. a GET request)
- Value of the timestamp used in the X-Evocalize-Timestamp Header
- Client Key Secret (this is a secret that will be provided by Evocalize)

| Header                    | Required | Description                                                                                                                                             |
|---------------------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| X-Evocalize-Client-Key-Id | true     | ID of the Client Key provided by Evocalize.                                                                                                             |
| X-Evocalize-Timestamp     | true     | Unix time since epoch in seconds, e.g. October 30, 2020 @ 9:44pm (UTC) => 1604094273. Requests with a timestamp over a minute old will not be accepted. |
| X-Evocalize-Signature     | true     | Signature of the payload. See Signing Requests below.                                                                                                   |

<aside class="notice">All code examples below assume that you are passing the required headers described in this section</aside>

## Responses

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
      "field": "String", // Omitted if null
      "details": {       // Omitted if null
        // JSON object
      }
    }
  ],
  "nextPageToken": "String",
  "previousPageToken": "String",
  "metadata": {
    // JSON object
  }
}
```

> Success Response Example:

```json
{
  "data": {
    // Request Specifc JSON Response
  }
}
```

> Error Response Example:

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

All requests are returned as JSON wrapped in a standard response format. The Schema section shows all the potential
fields that we could return. Do note that we do not include null values. The most common responses will look like the
examples provided.

| Schema Field      | Always Present | Description                                                                                                                  |
|-------------------|----------------|------------------------------------------------------------------------------------------------------------------------------|
| data              | false          | Returned for requests that result with 2xx responses.                                                                        |
| errors            | false          | Returned for requests that result in non 2xx responses.                                                                      |
| nextPageToken     | false          | Page token appears for result sets larger than 100 items. Not present on last page of results.                               |
| previousPageToken | false          | Same logic as nextPageToken. Not present on first page of results.                                                           |
| metadata          | false          | JSON object containing extra data around requests and responses. This data is subject to change and should not be relied on. |
